<template>
    <div class="content-inside">
            <div class="splitpane" style="display: flex; flex-direction: row;">

                <codemirror ref="myCm"
                        :value="code" 
                        :options="cmOption"
                        @ready="onCmReady"
                        @input="onCmCodeChange">
            </codemirror>
            </div>
    
            <div class="splitpane" style="display: flex; flex-direction: row;">
                <codemirror v-model="result" :options="cmOption" />
                
            </div>
    
    
    </div>
    
    
    
</template>

<script>
// 参考：https://github.com/surmon-china/surmon-china.github.io/blob/source/projects/vue-codemirror/examples/02-text-javascript.vue
import dedent from "dedent"
import { codemirror } from "vue-codemirror"
import "codemirror/lib/codemirror.css"
// language
import "codemirror/mode/javascript/javascript.js"
// theme css
import "codemirror/theme/monokai.css"
// require active-line.js
import "codemirror/addon/selection/active-line.js"
// styleSelectedText
import "codemirror/addon/selection/mark-selection.js"
import "codemirror/addon/search/searchcursor.js"
// hint
import "codemirror/addon/hint/show-hint.js"
import "codemirror/addon/hint/show-hint.css"
import "codemirror/addon/hint/javascript-hint.js"
import "codemirror/addon/selection/active-line.js"
// highlightSelectionMatches
import "codemirror/addon/scroll/annotatescrollbar.js"
import "codemirror/addon/search/matchesonscrollbar.js"
import "codemirror/addon/search/searchcursor.js"
import "codemirror/addon/search/match-highlighter.js"
// keyMap
import "codemirror/mode/clike/clike.js"
import "codemirror/addon/edit/matchbrackets.js"
import "codemirror/addon/comment/comment.js"
import "codemirror/addon/dialog/dialog.js"
import "codemirror/addon/dialog/dialog.css"
import "codemirror/addon/search/searchcursor.js"
import "codemirror/addon/search/search.js"
import "codemirror/keymap/sublime.js"
// foldGutter
// import "codemirror/addon/fold/foldgutter.css"
// import "codemirror/addon/fold/brace-fold.js"
// import "codemirror/addon/fold/comment-fold.js"
// import "codemirror/addon/fold/foldcode.js"
// import "codemirror/addon/fold/foldgutter.js"
// import "codemirror/addon/fold/indent-fold.js"
// import "codemirror/addon/fold/markdown-fold.js"
// import "codemirror/addon/fold/xml-fold.js"

const fs = require('fs');
const {parse} = require("@babel/parser");
const  traverse = require("@babel/traverse").default;
const types = require("@babel/types");
import generator from "@babel/generator";

export default {
    components: {
        codemirror
    },
    data() {
        return {
            code: dedent`
                if(a==2) b=1,c=2; else b=3,c=4; 

                if(a=1,b=2,c==3) d=b;

                {{a=1;}}

                function a1() {
                    if(x) return;
                }

                // ConditionalExpression
                a = m?11:2;
                1 - 1 ? aa = b + 4 : c + 5 ? cc = a + 1 : b + 2 ? ee = c + 3 : ff = 4;

                // LogicalExpression + ConditionalExpression
                a==1 && (b?c=2:d=4);
            `,
            result: dedent`
                
            `,
            cmOption: {
                tabSize: 4,
                styleActiveLine: true,
                lineNumbers: true,
                styleSelectedText: true,
                scrollbarStyle: null,
                line: true,
                foldGutter: true,
                gutters: ["CodeMirror-linenumbers", "CodeMirror-foldgutter"],
                highlightSelectionMatches: {
                    showToken: /\w/,
                    annotateScrollbar: true
                },
                mode: "text/javascript",
                // hint.js options
                hintOptions: {
                    // 当匹配只有一项的时候是否自动补全
                    completeSingle: false
                },
                //快捷键 可提供三种模式 sublime、emacs、vim
                keyMap: "sublime",
                matchBrackets: true,
                showCursorWhenSelecting: true,
                // theme: "monokai",
                theme: "default",
                extraKeys: { Ctrl: "autocomplete" },
                viewportMargin: Infinity
            }
        }
    },
    methods: {
        onCmReady(cm) {
            let newcode = this.convert(this.code);
            this.result = newcode;
        },
        onCmFocus(cm) {
            console.log('the editor is focused!', cm)
        },
        onCmCodeChange(jscode) {
            let newcode = this.convert(jscode);
            this.result = newcode;
        },
        convert(jscode) {
            // console.log('convert code', jscode)

            let ast = parse(jscode);

            const visitor1 =    {
                // 连续表达式，拆分为多行
                SequenceExpression: {
                    exit(path) {
                        // if(x) a=1, c=2; 转为 if(x){ a=1; c=2;}
                        if (path.parentPath.isExpressionStatement() ) {

                            let nodes = path.node.expressions;
                            var str = "";
                            for(var i=0; i<nodes.length; i++) {
                                let tmpNpde = nodes[i];
                                let aa = generator(tmpNpde).code
                                str+=  aa;
                                str+=  ";";
                            }

                            let js_code2 = parse(
                                `{${str}}`
                            )
                            path.parentPath.replaceWith(js_code2.program.body[0])
                        }

                        // if(a=1,b=2,c==3)  转为 a=1;b=2; if(c==3)
                        if (path.parentPath.isIfStatement() ) {
                            
                            // 语句提取出来，放到if前面
                            let nodes = path.node.expressions;

                            for(var i=0; i<nodes.length-1; i++) {
                                let tmpNpde = nodes[i];
                                let str =  generator(tmpNpde).code + ";";

                                let js_code2 = parse(
                                    `${str}`
                                )

                                path.parentPath.insertBefore(js_code2.program.body[0]);
                            }

                            // 删除if 里面多余的表达式
                            let nodePaths = path.get("expressions");
                            for(var i=0; i<nodePaths.length-1; i++) {
                                let tmpPath = nodePaths[i];
                                tmpPath.remove();
                            }

                        }

                    }
                },

                // if(x) return; 转为 if(x){ return;}
                ReturnStatement: {
                    exit(path) {
                        if ( path.parentPath.isIfStatement()) {
                            let nodes = [path.node];
                            path.replaceWith(types.blockStatement(nodes))
                        }
                    }
                },

                // a==1 && (b?c=2:d=4)  => if(a==1) {b?c=2:d=4}
                LogicalExpression: {
                    exit(path) {

                        let {left, operator, right} = path.node;

                        let rightPath = path.get("right");

                        // 找到最外层的逻辑表达式
                        if(rightPath.isConditionalExpression()) {
                            
                            if(operator === "&&") {
                                
                                let if_node = types.IfStatement(left, types.blockStatement([types.expressionStatement(right)]) , null);
                                path.replaceWithMultiple(if_node);
                            }
                        }
                    },
                }
        
            }

            // 第二层处理，需要依赖之前的处理结果
            const visitor2 =
            {
                // if 后面要么是 ExpressionStatement 要么是 BlockStatement
                // if(x) a=1, c=2; 转为 if(x){ a=1, c=2;}
                ExpressionStatement: {
                    exit(path) {
                        if ( path.parentPath.isIfStatement()) {
                            // console.log(path);
                            let js_code1 = parse(
                                `
                            {${path.toString()}}
                            `
                            )
                            path.replaceWith(js_code1.program.body[0])
                        }
                    }
                },

                // {{a=1;}} 转为 {a=1;}
                BlockStatement: {
                    exit(path) {
                        // 连续两个 block，且 内层block 没有兄弟节点
                        if ( path.parentPath.isBlockStatement() && 1 == path.parentPath.get("body").length) {

                            path.parentPath.replaceWith(path.node)
                        }
                    }
                },

            }

            const visitorConditional =
            {
                // 三元表达式
                ConditionalExpression: {
                    exit(path) {

                        if(path.parentPath.isAssignmentExpression()) {
                            return;
                        }

                        let {test, consequent, alternate} = path.node;
                        let new_consequent = types.BlockStatement([types.ExpressionStatement(consequent)]);
                        let new_alternate = types.BlockStatement([types.ExpressionStatement(alternate)]);
                        let if_node = types.IfStatement(test, new_consequent, new_alternate);

                        path.replaceWithMultiple(if_node);

                        path.stop();
                    },
                }

            }

            traverse(ast, visitor1);
            traverse(ast, visitor2);

            // 递归处理三元表达式
            this.recursionConditional(ast, visitorConditional);


            let { code } = generator(ast);
            return code;
        },
        recursionConditional(ast, visitorConditional) {

            // 递归处理，直到两次结果一致才会停下来
            traverse(ast, visitorConditional)
            let first  = generator(ast).code;

            traverse(ast, visitorConditional)
            let  second  = generator(ast).code;

            while(first !== second) {
                first = second;
                traverse(ast, visitorConditional)
                second  = generator(ast).code;
            }

        },
    },
    computed: {
        codemirror() {
            return this.$refs.myCm.codemirror
        }
    },
    mounted() {
        // console.log('the current CodeMirror instance object:', this.codemirror)
    }
}
</script>

<style>
/* Autoresize Demo: https://codemirror.net/demo/resize.html */

.content-inside {
  /* padding: 20px; */
  /* padding-bottom: 50px; */
  /* height: 100%; */
  flex:1;
  min-height: 0;

  display: flex;
}


.vue-codemirror {
    width: 100%;
}

.CodeMirror {
    /* height: auto; */
    min-height: 90%;
    flex: 1;
    position: relative;
  overflow: hidden;
  background: white;
  }
  
.CodeMirror-scroll {
    overflow: auto;
}
  
  .editor .CodeMirror-gutters {
    background-color: white;
    border: none;
  }
  
  .CodeMirror .ErrorGutter {
    width: .7em;
  }
  
  .CodeMirror pre.errorMarker {
    background-color: #EB9494;
  }
  .CodeMirror-linenumber {
    /* padding: 0 3px 0 5px;
    min-width: 20px;
    text-align: right; */
    color: #999;
    /* white-space: nowrap; */
}



.splitpane-content {
    flex: 1;
    /* for Firefox, otherwise it overflows the parent*/
    min-height: 0;
    min-width: 0;
  }
  
  .splitpane {
    flex: 1;
    /* for Firefox, otherwise it overflows the parent*/
    min-height: 0;
    min-width: 0;
  }
  
  .splitpane-divider {
    background-color: #ddd;
  }
  
  .splitpane-divider.horizontal {
    width: 5px;
  }
  
  .splitpane-divider.vertical {
    height: 5px;
  }
  
  .splitpane-divider:hover {
    background-color: #999;
    cursor: col-resize;
  }
  
  .splitpane-divider.vertical:hover {
    cursor: row-resize;
  }
</style>
