<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <p style="paddingbottom: 20px">Excelファイルをアップロードする</p>
    <input
      type="file"
      ref="file"
      multiple
      @change="handler2"
    />
    <div id="sample"></div>
    <html>
      <head>
        <meta charset="utf-8" />
        <title>SheetJS Table Export</title>
      </head>
      <body>
        <table
          border="1"
          style="border-collapse: collapse; border-color: lightgrey"
        >
          <tr>
            <td data-t="n" data-v="20211020" id="sjs-A1">20211020</td>
            <td data-t="n" data-v="1" id="sjs-B1">1</td>
            <td data-t="s" data-v="Train" id="sjs-C1">Train</td>
            <td data-t="s" data-v="Akihabara" id="sjs-D1">
              Aki<span style="background-color: yellow">haba</span>
            </td>
            <td data-t="s" data-v="Tokyo" id="sjs-E1">Tokyo</td>
            <td data-t="n" data-v="200" id="sjs-F1">200</td>
          </tr>
          <tr>
            <td data-t="n" data-v="20211020" id="sjs-A2">20211020</td>
            <td data-t="n" data-v="1" id="sjs-B2">1</td>
            <td data-t="s" data-v="Train" id="sjs-C2">Train</td>
            <td data-t="s" data-v="Akihabara" id="sjs-D2">Akihabara</td>
            <td data-t="s" data-v="Tokyo" id="sjs-E2">Tokyo</td>
            <td data-t="n" data-v="200" id="sjs-F2">200</td>
          </tr>
        </table>
      </body>
    </html>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import XLSX from "xlsx";

export default {
  name: "App",
  components: {
    // HelloWorld
  },
  methods: {
    handleReadFile() {
      const files = this.$refs.file;
      console.log(files.files);

      const userDocs = [];
      const fileObjs = files.files;
      for (const key in fileObjs) {
        if (
          fileObjs[key].constructor !== File ||
          fileObjs[key].name === undefined ||
          fileObjs[key].name.split("")[0] === "."
        ) {
          continue;
        }
        fileObjs[key].arrayBuffer().then((buffer) => {
          const workbook = XLSX.read(buffer, {
            type: "buffer",
            bookVBA: true,
          });
          const sheets = workbook.Sheets;
          for (const sheetName in sheets) {
            const doc = {};
            doc["user_no"] = fileObjs[key].webkitRelativePath
              .split("/")
              .slice(-2)[0]
              .split("_")[0];
            doc["file_name"] = fileObjs[key].name;
            doc["file_extension"] = fileObjs[key].name.split(".").slice(-1)[0];
            doc["sheet_name"] = sheetName;
            doc["text_content"] = XLSX.utils.sheet_to_txt(sheets[sheetName]);
            doc["html_content"] = XLSX.utils.sheet_to_html(sheets[sheetName], {
              header: "",
              footer: "",
            });
            console.log(doc);
            userDocs.push(doc);
          }
        });
      }

      // const fileObj = files.files[1];
      // fileObj.arrayBuffer().then((buffer) => {
      //   const workbook = XLSX.read(buffer, { type: 'buffer', bookVBA: true })
      //   const firstSheetName = workbook.SheetNames[0]
      //   const worksheet = workbook.Sheets[firstSheetName]
      //   // const data = XLSX.utils.sheet_to_json(worksheet)
      //   // const datat = XLSX.utils.sheet_to_txt(worksheet)
      //   let datah = XLSX.utils.sheet_to_html(worksheet, {header:"", footer:""})
      //   const datah2 = datah.replace('<table>', '<table border="1" style="border-collapse: collapse; border-color: lightgrey">')
      //   const datah3 = datah2.replace(/株価/g, '<span style="background-color:yellow;">株価</span>')
      // console.log(JSON.stringify(data))
      // console.log(datat)
      // console.log(datah3)
      // const sample = document.getElementById("sample")
      // sample.innerHTML = datah3
      // console.log(sample)
      // XLSX.utils.sheet_add_dom(worksheet, document.getElementById('sample'), {origin: -1});
      // })
    },

    handler2() {
      const input = this.$refs.file;
      console.log("input.files", input.files);
      // var input = document.getElementById("input_sheet");
      

      // フォームで入力されたExcelのsheetNameシートをオブジェクトにする。
      var parseSheet = function (file, sheetName, callback) {
        var reader = new FileReader();
        reader.onload = function (e) {
          var unit8 = new Uint8Array(e.target.result);
          var workbook = XLSX.read(unit8, { type: "array" });
          // 通常は1行目がヘッダ行となる。{header: 1} を指定で配列形式となる。
          var sheet = XLSX.utils.sheet_to_txt(workbook.Sheets[sheetName], {
            header: 1,
          });
          console.log("sheet", sheet);
          // 基本的な使い方であれば、sheet_to_jsonの結果だけでOK
          // 下記は変換を行う例
          var result = [];
          // 0行目から末尾まで走査
          // for (var i = 0; i < sheet.length; i++) {
          //   var row = sheet[i];
          //   // 何か変換処理があれば行う
          //   console.log(i, row);
          //   result.push(row);
          // }
          callback(result);
        };
        reader.readAsArrayBuffer(file);
      };

      parseSheet(input.files[0], "シート1", function (result) {
        // 変換後の処理
        console.log("result", result);
      });
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
