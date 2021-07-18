<script>
	import ONSHeader from './ONSHeader.svelte';
  	import ONSFooter from './ONSFooter.svelte';
	import PrototypeWarning from './PrototypeWarning.svelte';
	import ENVQUIZ from './ENVQUIZ.svelte';
	import "./sixteens_main.css";
	import "./roboto_slab.css";
</script>

<style>
	:global(body) {
	  margin: 0;
	  font-family:
			system-ui,
			-apple-system, /* Firefox supports this but not yet `system-ui` */
			'Segoe UI',
			Roboto,
			Helvetica,
			Arial,
			sans-serif,
			'Apple Color Emoji',
			'Segoe UI Emoji';
	}
</style>


<PrototypeWarning></PrototypeWarning>
<ONSHeader></ONSHeader>
<ENVQUIZ></ENVQUIZ>     
<ONSFooter></ONSFooter>     
    
