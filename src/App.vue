<template>
  <div>

  </div>
</template>

<script  setup>
// pixi导入
import * as PIXI from 'pixi.js'

const app = new PIXI.Application({
  width:window.innerWidth,
  height:window.innerHeight,
  backgroundColor: 0xffffff,
  resolution: window.devicePixelRatio || 1,
  antialias:true,
})
document.body.appendChild(app.view)
const container = new PIXI.Container()
//变量
let isPlaying =false//判断是否开始
let moveSpeed = 10//障碍移动速度
let dead = false
let score =0//分数
let jumpSpeed = 20//跳跃速度
let gravity = 1//重力
let speedUp=1//难度增加
app.stage.addChild(container)
//函数
function playGame(){
  isPlaying=true
  console.log(isPlaying)
  //显示移动动画与分数
  RunAnimation.visible = true
  ScoreText.visible =true
}
app.ticker.add(function(){//游戏运行时每帧执行
  RunAnimation.play()
  if(isPlaying==true)//游戏开始
  {
    //地面移动
    GroundSprite.tilePosition.x-=moveSpeed
    //障碍移动
    CactusSprite.x-=moveSpeed
    score+=moveSpeed/10
    score= Math.floor(score)
    ScoreText.text = score.toString()//分数增加
    if(CactusSprite.x<-300)//重置障碍
    {
      CactusSprite.x = window.innerWidth
    }
  }
  if(score>1000&&score<10000&&speedUp>0)
  {
    moveSpeed+=5
    // console.log(moveSpeed)
    speedUp--;
  }
  if(DinoSprite.visible == true)//跳跃时执行
  {
    jumpSpeed-=gravity
    DinoSprite.y  -=jumpSpeed
    if(DinoSprite.y>=window.innerHeight/2-25)//触碰地面复原
    {
      DinoSprite.y =window.innerHeight/2-25
      jumpSpeed = 20
      DinoSprite.visible = false
      RunAnimation.visible = true

    }
  }
  //死亡
  if(CactusSprite.x<DinoSprite.x+30&&CactusSprite.x>DinoSprite.x-30&&CactusSprite.y == DinoSprite.y)
  {
    moveSpeed=0
    isPlaying = false
    dead= true
    GameOverSprite.x = DinoSprite.x
    GameOverSprite.y = DinoSprite.y-100
    GameOverSprite.visible = true
    StartGame.visible =true
    RunAnimation.stop()
  }

  
})
//事件监听
window.addEventListener("keyup",function(){//键盘开始游戏
  if(isPlaying == false)
  {
    playGame()
    StartGame.visible = false
  }
})
window.addEventListener("click",function(){//鼠标开始游戏
  if(isPlaying == false)
  {
    playGame()
    StartGame.visible = false
  }
})
window.addEventListener("keydown",function(e){//跳跃
  if(isPlaying==true)
  {
    if(isPlaying&&e.code =='Space')
    {
      console.log("jump")
      RunAnimation.visible = false
      DinoSprite.visible =true
    }
  }
})
window.addEventListener('keyup',function(){
  if(dead==true)
  {
    window.location.reload()
  }
})
window.addEventListener('click',function(){
  if(dead==true)
  {
    window.location.reload()
  }
})
//基础资源添加
const BaseTexture = PIXI.BaseTexture.from("./resource/dino.png")
const DinoTexture = new PIXI.Texture(BaseTexture,new PIXI.Rectangle(40,4,44,45))
const DinoSprite = new PIXI.Sprite(DinoTexture)
DinoSprite.visible = false
DinoSprite.anchor.set(0.5)
DinoSprite.x = window.innerWidth/4
DinoSprite.y = window.innerHeight/2-25
//移动动画
const DinoRunArrayTexture = []
for(let i=0;i<4;i++)
{
  if(i==1)
  {
    continue
  }
  DinoRunArrayTexture.push(new PIXI.Texture(BaseTexture , new PIXI.Rectangle(677+i*44,2,44,47)))
}
const RunAnimation = new PIXI.AnimatedSprite(DinoRunArrayTexture)
RunAnimation.animationSpeed = 0.2
RunAnimation.anchor.set(0.5)
RunAnimation.x = window.innerWidth/4
RunAnimation.y = window.innerHeight/2-25
RunAnimation.visible = false
//游戏结束
const DinoDeadTexture = new PIXI.Texture(BaseTexture,new PIXI.Rectangle(897,2,44,47))
const DinoDeadSprite = new PIXI.Sprite(DinoDeadTexture)
DinoDeadSprite.visible = false
//地面
const GroundTexture =new PIXI.Texture(BaseTexture,new PIXI.Rectangle(2,54,1200,12))
const GroundSprite = new PIXI.TilingSprite(GroundTexture)
GroundSprite.anchor.set(0.5)
GroundSprite.width = window.innerWidth
GroundSprite.height = 12
GroundSprite.position.set(window.innerWidth/2,window.innerHeight/2-12)
//障碍物
const CactusTexture = new PIXI.Texture(BaseTexture,new PIXI.Rectangle(332,2,26,50))
const CactusSprite = new PIXI.Sprite(CactusTexture)
CactusSprite.anchor.set(0.5)
CactusSprite.x = window.innerWidth/2
CactusSprite.y = window.innerHeight/2-25
//文字
//开始游戏文字
const StartGame = new PIXI.Text("Press Any Key To Continue",{
  fontFamily:"Arial",
  fontSize:36,
  fill:0x333333,
  align:'center'
})
StartGame.anchor.set(0.5)
StartGame.x = window.innerWidth/2
StartGame.y = window.innerHeight/2-200
StartGame.interactive = true
//分数显示文字
const ScoreText = new PIXI.Text(score.toString(),{
  fontFamily:"Arial",
  fontSize:36,
  fill:0x333333,
  align:'center'
})
ScoreText.anchor.set(0.5)
ScoreText.x = window.innerWidth/2+400
ScoreText.y = window.innerHeight/2-150
ScoreText.visible = false
//死亡时
const GameOverTexture =PIXI.Texture.from("./resource/death.png")
const GameOverSprite = new PIXI.Sprite(GameOverTexture)
GameOverSprite.scale.set(0.1)
GameOverSprite.anchor.set(0.5)
GameOverSprite.x = window.innerWidth/2
GameOverSprite.y =window.innerHeight/2-200
GameOverSprite.visible = false
container.addChild(DinoSprite,RunAnimation,GroundSprite,StartGame,ScoreText,DinoDeadSprite,CactusSprite,GameOverSprite)




</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: 0;
}
canvas{
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  right: 0;
}
</style>
