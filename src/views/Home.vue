<template>
    <div class="home">
        <Toolbar  />
        <CodeConverter  />
        <Footer  />
    </div>
</template>

<script>
// @ is an alias to /src
import Toolbar from "./Toolbar.vue";
import Footer from "./Footer.vue";
import CodeConverter from "./CodeConverter.vue";

export default {
    name: "Home",
    components: {
        Toolbar,
        Footer,
        CodeConverter
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