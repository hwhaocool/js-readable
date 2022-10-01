<template>
    <div class="home">
        <Toolbar  />
        <JavaScriptDemo  />
        <Footer  />
    </div>
</template>

<script>
// @ is an alias to /src
import JavaScriptDemo from "./JavaScriptDemo.vue";
import Toolbar from "./Toolbar.vue";
import Footer from "./Footer.vue";

export default {
    name: "Home",
    components: {
        Toolbar,
        JavaScriptDemo,
        Footer,
    }
};
</script>

<style>
.home {
    display: flex;
    flex-direction: column;
    height: 100vh;
    /* prevents the page to growing larger than the viewport*/
    max-height: 100vh;
}
</style>