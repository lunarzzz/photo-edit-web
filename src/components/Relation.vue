<template>
    <div>
        <input v-model="msg" placeholder="Put Your Name">
        <button v-on:click="getRelation(msg)">click</button>
    </div>
</template>


<script>
import axios from "axios";
export default {
    name: "relation",
    data: () => {
        return {
            msg: "init msg"
        }
    },
    components: {

    },
    methods: {
        getRelation: function(msg) {
            axios.get("http://localhost:8081/relation", {params : {
                    name : msg
                }
            }).then( (res) => {
                alert(res.data)
            });
        }
    }
}
</script>