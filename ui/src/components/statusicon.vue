<template>
<!--adding spin causes cpu usage to go way up https://github.com/Justineo/vue-awesome/issues/90-->
<icon :scale="scale" :name="name" :spin="name == 'cog'"/>
</template>

<script>
export default {
    props: [ 'status', 'scale' ],
    computed: {
        name: function() {
            switch(this.status) {
            case "ok":
            case "finished":
                return "check";
            case "waiting":
                return "hourglass-start";
            case "running":
            case "running_sync":
                return "cog";
            case "requested":
                return "hourglass-half";
            case "failed":
                return "exclamation-circle";
            case "removed":
                return "trash";
            case "stop_requested":
            case "stopped":
                return "hand-paper";
            default:
                return "question";
            }
        }
    }
}
</script>
