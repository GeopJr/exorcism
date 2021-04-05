<script lang="ts">
  import { QFileDialog, FileMode } from "@nodegui/nodegui";
  import FS from "fs";

  export let fileInfo = {};

  let text = "Pick a mp4 File";
  let textColor = "#000000";

  function changeColor(error = false) {
    textColor = error ? "#ff0000" : "#000000";
  }

  const fileDialog = new QFileDialog();
  fileDialog.setFileMode(FileMode.ExistingFile);
  fileDialog.setNameFilter("Video files (*.mp4 *.MP4);;All files(*)");

  function onClicked(): void {
    fileDialog.exec();
    const selectedFiles = fileDialog.selectedFiles();
    FS.lstat(selectedFiles[0], (err, stats) => {
      if (err) return console.log(err);

      let shouldContinue = false;

      if (stats.isDirectory()) {
        text = "Please pick a File instead of a Folder";
      } else if (stats.isSymbolicLink()) {
        text = "Please pick a File instead of a SymLink";
      } else if (stats.isFile()) {
        if (!selectedFiles[0].toLowerCase().endsWith(".mp4")) {
          text = "Please pick a mp4 File";
        } else {
          shouldContinue = true;
          text = selectedFiles[0].replace(/^.*[\\\/]/, "");

          fileInfo["path"] = selectedFiles[0];
          fileInfo["filename"] = text;
        }
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
