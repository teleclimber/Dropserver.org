<style scoped>
h5 {
	margin: 0 0 5px -35px;
	text-transform: uppercase;
	font-size: 1rem;
	display: inline-block;
	background-color: rgb(57, 78, 172);
	color: white;
	font-weight:bold;
	padding: 2px 35px;
}
.valid-version {
	background: linear-gradient(0deg, rgb(57, 78, 172) 0%, rgb(235, 54, 54) 50%, rgba(252,176,69,1) 100%);	/*rgba(131,58,180,1)*/
	margin: 1em 0;
}
.valid-version > div {
	margin-left: 10px;
	padding: 10px;
	background-color: white;
}
p {
	margin: 0;
}
span.ver {
    font-weight: bold;
}
</style>

<script setup lang="ts">
const props = defineProps<{
  from?: string
  to?: string
}>();

if( !props.from && !props.to ) throw new Error("no versions passed to ValidVersion");
</script>

<template>
    <div class="valid-version">
        <div>
            <h5>Applicable Versions:</h5>
            <p>
                This page is valid for 
                <template v-if="!!from && !!to">
                    versions <span class="ver">{{ from }}</span> to <span class="ver">{{ to }}</span>.
                </template>
                <template v-else-if="!!from">
                    version <span class="ver">{{ from }}</span> and above.
                </template>
                <template v-else-if="!!to">
                    versions up to <span class="ver">{{ to }}</span>.
                </template>
                <template v-else></template>
            </p>
        </div>
    </div>
</template>
