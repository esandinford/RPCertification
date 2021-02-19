<template>
	<view>
		<button @tap="dullClick($event)" class="eButton" ref="eButton">
			<slot></slot>
			<span class="ripple" ref="ripple"></span>
		</button>
	</view>
</template>
<script>
	export default {
		data(){
			return{
				ripplesSave:"",
			}
		},
		methods:{
			dullClick(e){
				this.$emit("buttonClick");
				let X=e.target.x-e.target.offsetLeft;
				let Y=e.target.y-e.target.offsetTop;
				let cloneripple=this.ripplesSave.cloneNode(true);
				this.$refs.eButton.$el.appendChild(cloneripple);
				let initWidth=10;
				let initHeight=10;
				let times=1;
				let opacity=1;
				let intervalID=setInterval(x=>{
					if(times>=100){
						clearInterval(intervalID);
						cloneripple.remove();
					}
					cloneripple.style.width=initWidth+"px";
					cloneripple.style.height=initHeight+"px";
					cloneripple.style.left=(X-initWidth/2)+"px";
					cloneripple.style.top=(Y-initHeight/2)+"px";
					cloneripple.style.opacity=opacity;
					initWidth+=initWidth*0.2;
					initHeight+=initHeight*0.2;
					opacity*=0.9;
					times++;
				},20);
			},
		},
		mounted(){
			this.ripplesSave=this.$refs.ripple;
			console.log(this.ripplesSave);
		}
	}
</script>

<style>
	.eButton{
		
	}
	.ripple{
		display: inline-block;
		position: absolute;
		top: -10px;
		left: -10px;
		width: 10px;
		height: 10px;
		background-color: #C8C7CC;
		border-radius: 50%;
		opacity: 1;
	}
</style>
