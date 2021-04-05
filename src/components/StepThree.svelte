<script lang="ts">
  import { QFileDialog, FileMode } from "@nodegui/nodegui";

  import FS from "fs";

  export let dirInfo = "";

  let text = "Select a Save Location";
  let textColor = "#000000";

  function changeColor(error = false) {
    textColor = error ? "#ff0000" : "#000000";
  }

  const fileDialog = new QFileDialog();
  fileDialog.setFileMode(FileMode.Directory);

  function onClicked(): void {
    fileDialog.exec();
    const selectedFiles = fileDialog.selectedFiles();
    FS.lstat(selectedFiles[0], (err, stats) => {
      if (err) return console.log(err);

      let shouldContinue = false;

      if (stats.isFile()) {
        text = "Please pick a Folder instead of a Folder";
      } else if (stats.isSymbolicLink()) {
        text = "Please pick a Folder instead of a SymLink";
      } else if (stats.isDirectory()) {
        shouldContinue = true;
        text = selectedFiles[0].replace(/^.*[\\\/]/, "");

        dirInfo = selectedFiles[0];
      }
      changeColor(!shouldContinue);
    });
  }
</script>

<view
  style="margin-top: 8px; margin-horizontal: 16px; padding-horizontal: 8px;"
  id="FileChooserDialog"
>
  <text id="FileChooserLabel" style="color: {textColor}">
    {text}
  </text>
  <button on:clicked={onClicked} id="FileChooserButton"> Browse </button>
</view>

<style>
  #FileChooserDialog {
    flex-direction: "row";
    padding-horizontal: 50px;
    align-items: "center";
  }
  #FileChooserLabel {
    flex: 1;
    height: 40px;
  }
  #FileChooserButton {
    margin-left: 5px;
    width: 150px;
    height: 40px;
  }
</style>
