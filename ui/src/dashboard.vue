<template>
<div>
    <pageheader/>
    <sidemenu active="/dashboard"></sidemenu>
    <div class="ui pusher">
        <div class="page-content">
		<div class="margin20">
            <b-alert show>todo</b-alert>
        </div><!--magin20-->
        </div><!--page-content-->
    </div><!--off-sidemenu-->
</div>
</template>

<script>
import Vue from 'vue'
import sidemenu from '@/components/sidemenu'
import pageheader from '@/components/pageheader'

export default {
    components: { sidemenu, pageheader },
    data () {
        return {
            msg: 'Welcome to Your Vue.js App',
            config: Vue.config,
        }
    },
}
</script>

<style scoped>
</style>
