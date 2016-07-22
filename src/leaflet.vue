<template>
	<div>{{message}}</div>
	<h2>{{example}}</h2>
</template>

<script type="text/javascript">
	export default{
		data(){
			return{
				name:'leaflet'
			}
		},
		computed:{
			example:function(){
				return Date.now() + this.name;
			}
		},
		props:['message']
	}
</script>