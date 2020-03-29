<template>
  <div class="grid-container">
    <div class="grid-item-left">
      <textarea id="code"/>
    </div>
    <div class="grid-item-right">
      <div id="preview">
      </div>
    </div>
  </div>
</template>

<style lang="less">
  @import './docs.css';
  @import './../node_modules/codemirror/theme/dracula.css';
  @import './../node_modules/codemirror/addon/hint/show-hint.css';
  @import './../node_modules/codemirror/lib/codemirror.css';

  .grid-container {
    display: flex;
    width: 100%;
    height: 100%;
    flex-direction: row;
  }
  .grid-item-left{
    width: 50%;
    flex-grow: 1;
    overflow: auto;
    resize: horizontal;
  }
  .grid-item-right{
    flex-grow: 3;
  }

</style>

<script>
  import Handlebars from 'handlebars';
  import '../node_modules/codemirror/addon/mode/simple.js'
  import '../node_modules/codemirror/addon/mode/multiplex.js'
  import '../node_modules/codemirror/addon/hint/show-hint.js'
  import '../node_modules/codemirror/addon/edit/matchbrackets.js'
  import '../node_modules/codemirror/addon/hint/xml-hint.js'
  import '../node_modules/codemirror/addon/hint/html-hint.js'
  import '../node_modules/codemirror/addon/hint/css-hint.js'
  import '../node_modules/codemirror/addon/edit/closetag.js'
  import '../node_modules/codemirror/mode/javascript/javascript.js'
  import '../node_modules/codemirror/mode/css/css.js'
  import '../node_modules/codemirror/mode/xml/xml.js'
  import CodeMirror  from 'codemirror'
  import enumVariables from './enumVariables';

export default {
  data() {
    return {
      editor: null,
      timeout: null,
      templateCompile: null,
      compiledTemplate: null,
      template: `
<body style="margin: 0; padding: 0;">
  <table border="0" cellpadding="0" cellspacing="0" width="100%">
    <tr>
      <td>
        <table align="center" border="0" cellpadding="0" cellspacing="0" width="720">
          <tr>
            <td style="padding:20px 0;">
              <table>
                <tr>
                  <td width="240">
                    <img src="https://ih0.redbubble.net/image.1013128788.9596/flat,750x,075,f-pad,750x1000,f8f8f8.u1.jpg" 
                      width="100px"/>
                  </td>
                  <td 
                    align="right"
                    valign="bottom"
                    width="360"
                    style="color:#666666; font-family:Arial, sans-serif;font-size:20px;line-height:20px;">
                      <b>Anime:</b>
                      <b>{{anime_name}}</b>
                  </td>
                </tr>
              </table>                               
            </td>
          </tr>
          <tr>
            <td style="padding-top:20px;border-top:1px solid #e1e1e1">
            </td>                          
          </tr>
          <tr>
            <td 
              align="center"
              style="padding:10px 0 20px 0;border-bottom:1px solid #e1e1e1">
                <img src="https://1.bp.blogspot.com/-F6WzlY8fHco/Xa7zDS-8TaI/AAAAAAAAAsQ/9ASBPKy1HFUJxg1hZJLehvL4-xE3XUiKgCLcBGAsYHQ/s1600/demon%2Bslayer%2Bthe%2Bhashira.jpg"/>
            </td>
          </tr>
          <tr>
            <td style="padding:30px 0;">
              <table cellpadding="0" cellspacing="0" width="100%">
                <tr>
                  <td 
                    width="200"
                    style="color:#666666; font-family:Arial, sans-serif;font-size:20px;line-height:20px;">
                      <b>Personagens</b>
                  </td>
                  <td 
                    width="200"
                    style="color:#666666; font-family:Arial, sans-serif;font-size:20px;line-height:20px;">
                      <b>Origem</b>
                  </td>
                  <td 
                    width="200"
                    style="color:#666666; font-family:Arial, sans-serif;font-size:20px;line-height:20px;">
                      <b>Criador</b>
                  </td>
                </tr>
                <tr>
                  <td valign="top" width="200" style="color:#666666;font-family:Arial, sans-serif;font-size:14px;line-height:20px;padding-top:20px;">
                    {{personagem0}}<br/>
                    {{personagem1}}<br/>
                    {{personagem2}}<br/>
                    {{personagem3}}<br/>
                    {{#if personagem4}}
                      {{personagem4}}<br/>
                    {{/if}}
                    {{personagem5}}
                    <br/>
                    {{personagem6}}</br>
                    {{personagem7}}
                  </td>
                  <td 
                    valign="top"
                    width="200"
                    style="color:#666666;font-family:Arial, sans-serif;font-size:14px;line-height:20px;padding-top:20px;">
                    {{origem}}<br/>
                  </td>
                  <td
                    valign="top"
                    width="200"
                    style="color:#666666;font-family:Arial, sans-serif;font-size:14px;line-height:20px;padding-top:20px;">
                    {{escritor}} {{end_customer_last_name}}<br>
                  </td>
                </tr>
              </table>
            </td>
          </tr>
  </table>
</body>`,
    };
  },
  methods: {
    settingInitialTemplate() {
      this.templateCompile =  Handlebars.compile(this.template);
      
      document.getElementById('code').innerHTML = this.template;
      
      document.getElementById('preview').innerHTML = this.compiledTemplate;

      this.editor = CodeMirror.fromTextArea(document.getElementById("code"), {
        lineNumbers: true,
        theme: "dracula",
        extraKeys: {"Ctrl-Space": "autocomplete"},
        autoCloseTags: true,
      });

      this.editor.setOption({
        mode: { name: "handlebars", base: "text/html" },
      })

      this.editor.setSize("100%", "100%");
      this.editor.on("change",() => {
        let delay;
        clearTimeout(delay);
        this.updatePreview();
      });
    },

    updatePreview() {
      let editorNewValue = this.editor.doc.getValue();
      this.templateCompile =  Handlebars.compile(editorNewValue);
      this.compiledTemplate = this.templateCompile(enumVariables);

      document.getElementById('preview').innerHTML = this.compiledTemplate;
    },
  },
  mounted() {
    this.timeout = setTimeout(this.updatePreview, 300);
    this.settingInitialTemplate();
  },
  created() {
    this.templateCompile =  Handlebars.compile(this.template);
    this.compiledTemplate = this.templateCompile(enumVariables);
  }
}
</script>
