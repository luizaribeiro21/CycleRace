# CycleRace
**A idea era criar um jogo onde um andador de bicicleta tenta passear sem esbarrar nos outros andadores de bicicleta**  

- Primeiro coloquei todas as variáveis necessárias: 
~~~javascript
var caminho,imgCaminho;
var jogador1,jogador2,jogador3;
var imgCaminho,img1CiclistaPrincipal,img2CiclistaPrincipal;

var opRosaimg1,opRosaimg2;
var opAmareloimg1,opAmareloimg2;
var opVermelhoimg1,opVermelhoimg2;
var imgFimJogo,sinoBicicleta;

~~~
- Despois fiz o upload de todas as imagens ou animações necessárias no function preload 
~~~javascript
function preload(){
  imgCaminho = loadImage("images/Road.png");
  img1CiclistaPrincipal = loadAnimation("images/mainPlayer1.png","images/mainPlayer2.png");
  img2CiclistaPrincipal= loadAnimation("images/mainPlayer3.png");
  
  opRosaimg1 = loadAnimation("images/opponent1.png","images/opponent2.png");
  opRosaimg2 = loadAnimation("images/opponent3.png");
  
  opAmareloimg1 = loadAnimation("images/opponent4.png","images/opponent5.png");
  opAmareloimg2 = loadAnimation("images/opponent6.png");
  
  opVermelhoimg1 = loadAnimation("images/opponent7.png","images/opponent8.png");
  opVermelhoimg2 = loadAnimation("images/opponent9.png");
  
  sinoBicicleta = loadSound("sound/bell.mp3");
  imgFimJogo = loadImage("images/gameOver.png");
}
~~~
- Depois eu criei uma sprite, adicionei as animações/imagens, adicionei velocidade e o set collider no ciclista (para ele não bater muito antes) 
~~~javascript 

  createCanvas(1200,300);
  // movendo o plano de fundo
  caminho=createSprite(100,150);
  caminho.addImage(imgCaminho);
  caminho.velocityX = -5;

  //criando o ciclista correndo de bicicleta
  ciclistaPrincipal  = createSprite(70,150);
  ciclistaPrincipal.addAnimation("SahilRunning",img1CiclistaPrincipal);
  ciclistaPrincipal.scale=0.07;
  ciclistaPrincipal.setCollider("rectangle",0,0,ciclistaPrincipal.width, ciclistaPrincipal.height);   
  
  fimdeJogo = createSprite(600,150);
  fimdeJogo.addImage(imgFimJogo);
  fimdeJogo.scale = 0.8;
  fimdeJogo.visible = false; 
  ~~~
  **Depois dessa etapa, as coisas começaram a ficar mais dificíl!!!** 

<img align="right"  height="230" width="230" src="https://media4.giphy.com/media/XGJqYmrDrmonHAIx0b/200.gif">

- Quando eu morrese no jogo eu queria que ele recomeçasse, então eu tinha que criar um estado do jogo para 'Jogar' e 'Encerrar'
~~~javascript 
var ENCERRAMENTO =0;
var JOGAR =1;
var estadoJogo = JOGAR;
~~~
- Depois eu pensei em todas as coisas que eu jogo teria que fazer enquanto estivesse rodando, o que inclui: 
  - O ciclista move com o mouse
  ~~~
  ciclistaPrincipal.y = World.mouseY;
  ~~~
  - O ciclista colide com os edges 
  ~~~
   edges= createEdgeSprites();
   ciclistaPrincipal .collide(edges);
  ~~~
  - O caminho adicionava metade dele quando acabava 
 ~~~
   if(caminho.x < 0 ){
    caminho.x = width/2;
  }
  ~~~
  -E o som de sino quando apertasse espaço
  if(keyDown("space")) {
    sinoBicicleta.play();
  }
  ~~~
  -Agora eu tinha que criar os obstaculos então eu criei uma function para cada um 
  ~~~javascript
  function ciclistaRosa(){
        jogador1 =createSprite(1100,Math.round(random(50, 250)));
        jogador1.scale =0.06;
        jogador1.velocityX = -(6 + 2*distancia/150);
        jogador1.addAnimation("opponentPlayer1",opRosaimg1);
        jogador1.setLifetime=170;
}

function ciclistaAmarelo(){
        jogador2 =createSprite(1100,Math.round(random(50, 250)));
        jogador2.scale =0.06;
        jogador2.velocityX = -(6 + 2*distancia/150);
        jogador2.addAnimation("opponentPlayer2",opAmareloimg1);
        jogador2.setLifetime=170    
}

function ciclistaVermelho(){
        jogador3 =createSprite(1100,Math.round(random(50, 250)));
        jogador3.scale =0.06;
        jogador3.velocityX = -(6 + 2*distancia/150);
        jogador3.addAnimation("opponentPlayer3",opVermelhoimg1);
        jogador3.setLifetime=170;    
}
~~~
- Depois eu coloquei o nome de todas essas functios no draw. 
**Porém quando eu tentei fazer uma condição para que se o ciclista batesse em um desses obstaculos o jogo terminasse, falava que estava 'out of scope'**
<img align="right"  height="230" width="230" src="https://media2.giphy.com/media/11daZqJWhUZucE/giphy.gif">

-Mas eu resolvi isso criando new Groups no setup e criando variáis para esses grupos
  ~~~javascript
var CGRosa, CGAmarelo,CGVermelho; 
~~~
~~~javascript 
function setup (){

  CGRosa = new Group();
  CGAmarelo = new Group();
  CGVermelho = new Group();
}
~~~
- Então nos functions dos obsstaculos era so adicionar eles dentro desss groups
~~~javascript
 CGRosa.add(jogador1);
 CAmarelo.add(jogado2);
 CGVermelho.add(jogador3);
~~~
-Agora sim eu pude fazer o ciclista bater em obstaculos e fazer esses obstaculos aparecerem aleátoriamente
~~~javascript
var selecionar_jogadorOP = Math.round(random(1,3));
  
  if (World.frameCount % 150 == 0) {
    if (selecionar_jogadorOP == 1) {
      ciclistaRosa();
    } else if (selecionar_jogadorOP == 2) {
      ciclistaAmarelo();
    } else {
      ciclistaVermelho();
    }
  }
  ~~~
  ~~~javascript 
  if(CGRosa.isTouching(ciclistaPrincipal)){
     estadoJogo = ENCERRAMENTO;
     jogador1.velocityY = 0;
     jogador1.addAnimation("opponentPlayer1",opRosaimg2);
    }
    
    if(CGAmarelo.isTouching(ciclistaPrincipal)){
      estadoJogo = ENCERRAMENTO;
      jogador2.velocityY = 0;
      jogador2.addAnimation("opponentPlayer2",opAmareloimg2);
    }
    
    if(CGVermelho.isTouching(ciclistaPrincipal)){
      estadoJogo = ENCERRAMENTO;
      jogador3.velocityY = 0;
      jogador3.addAnimation("opponentPlayer3",opVermelhoimg2);
    }
 ~~~
    
 - Terminei a parte do estado 'Jogar'! Então agora eu tive que fazer o mesmo para o estado 'Encerrar'. Então coloquei a imagem do fim de jogo visivel, o caminho e os obstaculos para velocidade 0, e mudei a animação do ciclista para que ele estivesse caido
    
 ~~~javascript 
    }else if (estadoJogo === ENCERRAMENTO) {
        fimdeJogo.visible = true
  
    caminho.velocityX = 0;
    imgCaminho.velocityY = 0;                                       
    ciclistaPrincipal.addAnimation("SahilRunning",img2CiclistaPrincipal);
  
    CGRosa.setVelocityXEach(0);
  
    CGAmarelo.setVelocityXEach(0);
  
    CGVermelho.setVelocityXEach(0);
   ~~~
    
    -Também coloquei o lifetime dos obstaculos para -1 para que eles não fossem destruídos de verdade
 ~~~javascript
    CGRosa.setLifetimeEach(-1);
    CGAmarelo.setLifetimeEach(-1);
    CGVermelho.setLifetimeEach(-1);
~~~
- Também queria que você pudesse reiniciar o jogo, então fic com que quando perdesse um texto iria aparecer pedindo para você clicar na tela para recomeçar
~~~javascript 
    text ("Aperte a tecla direcional para cima para reiniciar o jogo!",350,200); 
    if (mousePressedOver (caminho)){
      reset (); 
    }
~~~
- Agora bastava eu criar uma funcão para o reset 
~~~javascript 
function reset (){
  estadoJogo = JOGAR; 
  fimdeJogo.visible = false; 
  ciclistaPrincipal.addAnimation("SahilRunning",img1CiclistaPrincipal);   CGRosa.destroyEach (); 
  CGAmarelo.destroyEach (); 
  CGVermelho.destroyEach (); 
}
~~~
**Porém queria deixar o jogo mais desafiante então eu coloquei que cada vez que a distancia passase 50, a velocidade do iria aumentar** 
- Para fazer isso tive que usar um pouco de matemática 
<img align="right"  height="230" width="230" src="https://c.tenor.com/dlJSiLUJNmsAAAAC/math-calculate.gif">

~~~javascript
   distancia = distancia + Math.round(getFrameRate()/50);
   caminho.velocityX = -(6 + 2*distancia/150);
~~~

- E finalmente escrever a 'distancia' na tela 

~~~javascript 
var distancia=0;


function draw() {
  textSize(20);
  fill(255);
  text("Distancia: "+ distancia,600,30);
}
