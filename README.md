# Manav.html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cosmic Mathematical Music Engine</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.29/Tone.js"></script>

<style>
body{
  margin:0;
  text-align:center;
  font-family:Arial;
  background:black;
  color:white;
  overflow:hidden;
  transition:background 0.5s ease;
}

input{
  padding:10px;
  font-size:18px;
  border-radius:6px;
  border:none;
  width:220px;
  text-align:center;
}

button{
  padding:10px 20px;
  font-size:16px;
  margin:5px;
  border:none;
  border-radius:6px;
  cursor:pointer;
  background:#00c6ff;
  color:black;
  font-weight:bold;
}

#analysis{
  margin-top:10px;
  font-size:14px;
  opacity:0.9;
}
canvas{
  display:block;
  margin:auto;
}
</style>
</head>

<body>

<h1>🌌 Cosmic Mathematical Music Engine</h1>
<p>Enter a Number. Let the Universe Compute.</p>

<input type="text" id="numberInput" placeholder="Digits only">
<br>
<button onclick="start()">Generate</button>
<button onclick="stopMusic()">Stop</button>

<div id="analysis"></div>

<script>

let synth, bass, drum, reverb;
let numberString="";
let index=0;
let tempo=300;
let playing=false;
let particles=[];

let notes=["C4","D4","E4","F4","G4","A4","B4","C5","D5","E5"];

function setup(){
  let canvas=createCanvas(window.innerWidth,window.innerHeight*0.6);
  canvas.parent(document.body);
}

function draw(){
  background(0,20);

  for(let p of particles){
    p.update();
    p.show();
  }

  stroke(0,255,200);
  noFill();
  beginShape();
  for(let x=0;x<width;x++){
    let angle=(x*0.02)+frameCount*0.05;
    let y=height/2+sin(angle)*120;
    vertex(x,y);
  }
  endShape();
}

class Particle{
  constructor(){
    this.x=random(width);
    this.y=random(height);
    this.size=random(2,6);
    this.speed=random(1,3);
  }
  update(){
    this.y+=this.speed;
    if(this.y>height){
      this.y=0;
      this.x=random(width);
    }
  }
  show(){
    noStroke();
    fill(0,255,200);
    circle(this.x,this.y,this.size);
  }
}

for(let i=0;i<100;i++){
  particles.push(new Particle());
}

async function start(){
  numberString=document.getElementById("numberInput").value;
  if(!/^[0-9]+$/.test(numberString)){
    alert("Digits only!");
    return;
  }

  await Tone.start();

  reverb=new Tone.Reverb(4).toDestination();

  synth=new Tone.PolySynth(Tone.Synth).connect(reverb);
  bass=new Tone.MonoSynth({oscillator:{type:"square"}}).connect(reverb);
  drum=new Tone.MembraneSynth().toDestination();

  let num=parseInt(numberString);

  let isPrime=checkPrime(num);
  let isFibo=checkFibo(num);
  let isPalindrome=numberString===numberString.split('').reverse().join('');

  tempo=Math.max(100,600-numberString.length*40);

  if(isPrime) document.body.style.background="#001f3f";
  else if(isFibo) document.body.style.background="#003d33";
  else if(isPalindrome) document.body.style.background="#2d0033";
  else document.body.style.background="#000";

  document.getElementById("analysis").innerHTML=
  "Prime: "+isPrime+
  " | Fibonacci: "+isFibo+
  " | Palindrome: "+isPalindrome+
  " | Binary: "+num.toString(2);

  index=0;
  playing=true;
  play();
}

function play(){
  if(!playing || index>=numberString.length) return;

  let digit=parseInt(numberString[index]);
  synth.triggerAttackRelease(notes[digit],"8n");

  // Harmony
  if(digit%3===0){
    synth.triggerAttackRelease(["C4","E4","G4"],"8n");
  }

  // Drum on even
  if(digit%2===0){
    drum.triggerAttackRelease("C2","8n");
  }

  // Bass from binary
  let binary=parseInt(numberString).toString(2);
  if(binary[index]==="1"){
    bass.triggerAttackRelease("C2","8n");
  }

  index++;
  setTimeout(play,tempo);
}

function stopMusic(){
  playing=false;
}

function checkPrime(n){
  if(n<=1) return false;
  for(let i=2;i<=Math.sqrt(n);i++){
    if(n%i===0) return false;
  }
  return true;
}

function checkFibo(n){
  function isPerfectSquare(x){
    let s=Math.sqrt(x);
    return s===Math.floor(s);
  }
  return isPerfectSquare(5*n*n+4)||isPerfectSquare(5*n*n-4);
}

</script>
</body>
</html>
