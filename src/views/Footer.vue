<template>
    <div id="contribution">
            Built with
            <a href="https://cn.vuejs.org/">Vue</a>,
            <a href="https://babeljs.io/">Babel</a>,
            <a href="https://codemirror.net/">CodeMirror</a>
            |
            <a href="https://github.com/fkling/astexplorer">GitHub</a>
            |
            <!-- <span>Build: <a href="https://github.com/fkling/astexplorer/commits/1d50a8f">1d50a8f</a></span> -->
        </div>
</template>

<script>

export default {
    name: "Footer",
};
</script>

<style>
#contribution {
    font-size: 0.9em;
    color: #555;
    height: 25px;
    text-align: center;
    line-height: 25px;
    background-color: #efefef;
    border-top: 1px solid #ddd;
    margin-top: -50px;
  }
</style>