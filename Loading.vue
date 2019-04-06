<template>
  <div>
    <canvas id="canvas"></canvas>
  </div>
</template>

<script>
  export default {
    name: 'MainPage',
    props: {
      circleColor: {
        type: Object,
        default: () =>{
          return {
            start: `rgba(137, 190, 178, .25)`,
            end: `rgba(69, 137, 148, 0)`
          }
        }
      },
      tailBorderColor: {
        type: Object,
        default: () => {
          return {
            start: `rgba(69, 137, 148, 0)`,
            middle: 'rgba(137, 190, 178, .7)',
            end: `rgba(137, 190, 178, 0)`
          }
        }
      },
      circleFlareColor: {
        type: Object,
        default: () => {
          return {
            start: `rgba(254, 67, 101, .35)`,
            end: `rgba(252, 157, 154, 0)`
          }
        }
      },
      circleFlareInColor: {
        type: Object,
        default: () => {
          return {
            start: `rgba(130, 57, 53, .6)`,
            end: `rgba(252, 157, 154, 0)`
          }
        }
      }
    },
    data() {
      return {
          show: false,
          canvas: {},
          ctx: {},
          cw: 1000,
          ch: 600,
          particles: [], //保存星星的数组
          particleMax: 100, //设置星星的最大数量
          circle: { //初始圆弧信息
            x: (1000 / 2) + 5, //初始圆弧x坐标
            y: (600 / 2) + 22, //初始圆弧的y坐标
            radius: 90, //初始半径
            speed: 5, //初始旋转速度
            rotation: 0, //初始角度
            angleStart: 270, //初始圆弧开始角度
            angleEnd: 90, //初始圆弧结束角度
            hue: 220, //初始颜色
            thickness: 18, //初始宽度
            blur: 25 //初始阴影宽度
          },
      }
    },
    mounted() {
      this.canvas = document.getElementById('canvas');
      this.canvas.width = 1000;
      this.canvas.height = 600;
      this.ctx = canvas.getContext('2d');
      this.loop()
    },
    methods: {
      rand(a, b) {
        return ((Math.random()*(b-a+1))+a);
      },
      dToR(degrees) {
        return degrees * (Math.PI / 180);
      },
      updateCircle() { ////更新圆弧的角度
        let circle = this.circle;
        if(circle.rotation < 360){ //小于360度时
          circle.rotation += circle.speed; //顺时针旋转
        } else { //否则
          circle.rotation = 0; //设置为0
        }
      },
      renderCircle() {  //画蓝色圆弧
        let ctx = this.ctx,
            circle = this.circle,
            gradient1 = ctx.createLinearGradient(0, -circle.radius, 0, circle.radius);
        gradient1.addColorStop(0, `${this.circleColor.start}`);
        gradient1.addColorStop(0.5, `${this.circleColor.end}`);
        ctx.save();               //保存canvas的状态
        ctx.translate(circle.x, circle.y); //移动canvas位置和loading位置一样
        ctx.rotate(this.dToR(circle.rotation)); //设置角度
        ctx.beginPath(); //开始绘制
        ctx.arc(0, 0, circle.radius, this.dToR(circle.angleStart), this.dToR(circle.angleEnd), true); //设置圆弧
        ctx.lineWidth = circle.thickness;  //设置圆弧宽度
        ctx.strokeStyle = gradient1;  //设置渐变颜色
        ctx.stroke();  //画出圆弧
        ctx.restore(); //恢复canvas之前保存的状态
      },
      renderCircleBorder() {  //画圆弧的border
        let ctx = this.ctx,
            circle = this.circle,
            gradient2 = ctx.createLinearGradient(0, -circle.radius, 0, circle.radius);
        gradient2.addColorStop(0, `${this.tailBorderColor.start}`);
        gradient2.addColorStop(.1, `${this.tailBorderColor.middle}`);
        gradient2.addColorStop(1, `${this.tailBorderColor.end}`);
        ctx.save();                     //保存canvas的状态
        ctx.translate(circle.x, circle.y); //移动canvas位置和loading位置一样
        ctx.rotate(this.dToR(circle.rotation)); //设置角度
        ctx.beginPath(); //开始绘制
        ctx.arc(0, 0, circle.radius + (circle.thickness/2), this.dToR(circle.angleStart), this.dToR(circle.angleEnd), true); //设置圆弧
        ctx.lineWidth = 2; //设置圆弧宽度
        ctx.strokeStyle = gradient2;  //设置渐变颜色
        ctx.stroke();  //画出圆弧
        ctx.restore(); //恢复canvas之前保存的状态
      },
      renderCircleFlare() {  //画头部外面的光圆
        let ctx = this.ctx,
            circle = this.circle,
            gradient3 = ctx.createRadialGradient(0, circle.radius, 0, 0, circle.radius, 30); //创建渐变
        gradient3.addColorStop(0, `${this.circleFlareColor.start}`); //设置初始渐变颜色
        gradient3.addColorStop(1, `${this.circleFlareColor.end}`); //设置结束渐变颜色
        ctx.save();                    //保存canvas的状态
        ctx.translate(circle.x, circle.y); //移动canvas位置和loading位置一样
        ctx.rotate(this.dToR(circle.rotation+185)); //设置角度
        ctx.scale(1,1); //设置缩放比例
        ctx.beginPath(); //开始绘制
        ctx.arc(0, circle.radius, 30, 0, Math.PI *2, false); //设置光圆
        ctx.closePath(); //结束绘制
        ctx.fillStyle = gradient3; //添加渐变
        ctx.fill(); //填充
        ctx.restore(); //恢复canvas之前保存的状态
      },
      renderCircleFlareIn() { //画头部里面的光圆
        let ctx = this.ctx,
            circle = this.circle,
            gradient4 = ctx.createRadialGradient(0, circle.radius, 0, 0, circle.radius, 25); //创建渐变
        gradient4.addColorStop(0, `${this.circleFlareInColor.start}`);
        gradient4.addColorStop(1, `${this.circleFlareInColor.end}`);
        ctx.save();                    //保存canvas的状态
        ctx.translate(circle.x, circle.y); //移动canvas位置和loading位置一样
        ctx.rotate(this.dToR(circle.rotation+165)); //设置角度
        ctx.scale(1.5,1); //设置缩放比例
        ctx.beginPath(); //开始绘制
        ctx.arc(0, circle.radius, 25, 0, Math.PI *2, false); //设置光圆
        ctx.closePath(); //结束绘制
        ctx.fillStyle = gradient4; //添加渐变
        ctx.fill(); //填充
        ctx.restore(); //恢复canvas之前保存的状态
      },
      createParticles() { //创建小星
        let circle = this.circle,
            particles = this.particles,
            particleMax = this.particleMax;
        if(particles.length < particleMax){ //星星的数量没超过时才执行
          particles.push({ //往星星的数组里添加星星
            x: (circle.x + circle.radius * Math.cos(this.dToR(circle.rotation-85))) + (this.rand(0, circle.thickness*2) - circle.thickness), //设置星星的x坐标
            y: (circle.y + circle.radius * Math.sin(this.dToR(circle.rotation-85))) + (this.rand(0, circle.thickness*2) - circle.thickness), //设置星星的y坐标
            vx: (this.rand(0, 100)-50)/1000, //设置星星x轴移动距离
            vy: (this.rand(0, 100)-50)/1000, //设置星星y轴移动距离
            radius: this.rand(1, 6)/2, //设置星星的宽高
            alpha: this.rand(10, 20)/100 //设置星星的透明度
          });
        }
      },
      updateParticles() { //更新星星的位置
        let particles = this.particles,
            i = particles.length; //获取星星的总个数
        while(i--){ //循环遍历每一个星星
          let p = particles[i]; //保存当前的星星
          p.vx += (this.rand(0, 100)-50)/750; //更新星星x轴移动距离
          p.vy += (this.rand(0, 100)-50)/750; //更新星星y轴移动距离
          p.x += p.vx; //更新星星x坐标
          p.y += p.vy; //更新星星y坐标
          p.alpha -= .01; //更新星星的透明度，渐渐透明
          if(p.alpha < .02){ //当透明小于0.2
            particles.splice(i, 1) //删除当前星星
          }
        }
      },
      renderParticles() { //绘制星星
        let particles = this.particles,
            i = particles.length, //获取星星的总个数
            ctx = this.ctx,
            p = {};
        while(i--){ //循环遍历每一个星星
          p = particles[i]; //保存当前星星
          ctx.beginPath(); //开始绘制
          ctx.fillRect(p.x, p.y, p.radius*2, p.radius*2); //绘制星星形状
          ctx.closePath(); //结束绘制
          ctx.fillStyle = 'hsla(0, 0%, 100%, '+p.alpha+')'; //绘制星星的颜色和透明度
        }
      },
      renderText() {
        let ctx = this.ctx;
        ctx.save();
        ctx.font="20px Georgia";
        ctx.fillText("Loading", 472, 327);
        ctx.restore();
      },
      clear() { //清除重绘
        let ctx = this.ctx;
        ctx.globalCompositeOperation = 'destination-out'; //在源图像外显示目标图像。只有源图像外的目标图像部分会被显示，源图像是透明的。
        ctx.fillStyle = 'rgba(0, 0, 0, .1)'; //设置源图像的颜色
        ctx.fillRect(0, 0, this.cw, this.ch); //绘制源图像
        ctx.globalCompositeOperation = 'lighter';	//显示源图像 + 目标图像。
      },
      loop() {
        this.clear();
        this.updateCircle();
        this.renderCircle();
        this.renderCircleBorder();
        this.renderCircleFlare();
        this.renderCircleFlareIn();
        this.renderText();
        this.createParticles();
        this.updateParticles();
        this.renderParticles();
        requestAnimationFrame(this.loop)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
img {
  display: block;
  float: left;
  margin: 0 1px 0 0;
}

canvas {
  display: block;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translateX(-50%) translateY(-50%);
}
</style>
