<template>
    <div id="Toolbar">
        <h1>JS Readable</h1>

        <div id="info" class="">
            Parser:
            <a
                href="https://github.com/babel/babel"
                target="_blank"
                rel="noopener noreferrer"
                >babel</a
            ><br />
        </div>
    </div>
</template>

<script>
export default {
    name: "Toolbar"
}
</script>

<style>
#Toolbar {
    flex: 0 0 auto;
    font-family: monospace;
    line-height: 32px;
    border-bottom: 1px solid #ddd;
    color: #454545;
    padding-left: 10px;
    padding-right: 10px;
    z-index: 200;
    display: flex;
    flex-wrap: wrap;
}

#Toolbar > * {
    flex: 0 0 auto;
    border-right: 1px solid #ddd;
}

#Toolbar,
#Toolbar .menuButton ul {
    background-color: #efefef;
}

#Toolbar > *,
#Toolbar > .menuButton > span,
#Toolbar button {
    background-color: transparent;
    box-sizing: border-box;
    color: inherit;
    font-family: inherit;
    font-size: 16px;
    min-width: 90px;
    outline: none;
}

#Toolbar > .menuButton > span {
    cursor: default;
    padding: 0px 6px;
}

#Toolbar > * button {
    height: 100%;
    border: none;
    cursor: pointer;
}

#Toolbar > h1 {
    padding: 0;
    padding-right: 10px;
    margin: 0;
    font-size: 18px;
}

#Toolbar > a {
    color: inherit;
    text-decoration: none;
}

#Toolbar .menuButton:hover > ul {
    display: block;
}

#Toolbar .menuButton ul {
    position: fixed;
    padding: 0;
    margin: 0;
    list-style: none;
    display: none;
    border: 1px solid #ddd;
    max-height: calc(100vh - 65px);
    overflow-y: auto;
    z-index: 500;
}

#Toolbar .menuButton ul li {
    white-space: nowrap;
}

#Toolbar .menuButton ul button {
    line-height: 28px;
    padding-top: 2px;
    padding-bottom: 2px;
    /* for scrollbar */
    padding-right: 15px;
    text-align: left;
    width: 100%;
}

#Toolbar > *.disabled,
#Toolbar button:disabled,
#Toolbar button:disabled:hover,
#Toolbar button:disabled:active {
    background-color: transparent;
    color: #888;
    cursor: default;
}

#Toolbar .menuButton li.selected {
    background-color: rgba(0, 0, 0, 0.05);
}

#Toolbar > a:hover,
#Toolbar > .button:hover,
#Toolbar ul button:hover {
    background-color: rgba(0, 0, 0, 0.1);
}

#Toolbar > a:active,
#Toolbar button:active {
    background-color: rgba(0, 0, 0, 0.3);
}

#info {
    color: #898989;
    cursor: default;
    border: none;
    margin-left: auto;
}

#info.small {
    font-size: 12px;
    line-height: 1.3em;
}

.output .toolbar > button {
    margin: 0;
    height: 100%;
    min-width: 90px;
    border: 1px solid transparent;
    border-left: 1px solid #ddd;
    border-right: 1px solid #ddd;
    font-size: 14px;
    background-color: transparent;
    display: inline-block;
    vertical-align: top;
    outline: none;
    cursor: pointer;
}

.output .toolbar > button.active {
    border-color: #999;
    background-color: #999;
    color: #f5f5f5;
}

.fa-fw {
    width: 1.28571429em;
    text-align: center;
}
</style>
