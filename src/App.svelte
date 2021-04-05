<script lang="ts">
  import { onMount } from "svelte";
  import { QIcon } from "@nodegui/nodegui";
  import StepOne from "./components/StepOne.svelte";
  import StepTwo from "./components/StepTwo.svelte";
  import StepThree from "./components/StepThree.svelte";
  import { promises as FS } from "fs";
  import open from "open";
  import nodeguiIcon from "../assets/exorcism.png";
  import type { NSVElement, RNWindow } from "@nodegui/svelte-nodegui";

  const winIcon = new QIcon(nodeguiIcon);

  let fileInfo = {};
  let durationType = -1;
  let dirInfo = "";
  let canExport = false;
  let successText = "";

  let win;

  $: if (
    fileInfo.hasOwnProperty("path") &&
    fileInfo.path.length > 0 &&
    durationType >= 0 &&
    dirInfo.length > 0
  ) {
    canExport = true;
    successText = "";
  } else {
    canExport = false;
  }

  function onClicked(): void {
    open("https://geopjr.dev/").catch(console.error);
  }

  async function onExport(): Promise<void> {
    const file = await FS.readFile(fileInfo.path);

    const modification =
      durationType === 0
        ? new Buffer.from([0x00, 0x01, 0x7f, 0xff, 0xff, 0xff])
        : new Buffer.from([0x00, 0x01, 0xff, 0xff, 0xff, 0xf0]);

    const start = file.indexOf(Buffer.from("mvhd")) + 17;
    modification.copy(file, start + 1, 0, 4);
    await FS.writeFile(
      fileInfo.path.split(".").slice(0, -1).join(".") + "_CURSED.mp4",
      file
    );
    successText =
      "Done! Your file should be under\n" +
      fileInfo.path.split(".").slice(0, -1).join(".") +
      "_CURSED.mp4";
  }

  onMount(() => {
    (window as any).win = win;
    (win as NSVElement<RNWindow>).nativeView.show();

    return () => {
      delete (window as any).win;
    };
  });
</script>

<window
  bind:this={win}
  windowIcon={winIcon}
  minSize={{ width: 500, height: 450 }}
  maxSize={{ width: 500, height: 450 }}
  windowTitle="Exorcism"
>
  <view style="flex: 1;">
    <text id="welcome-text">Who doesn't need an Exorcism?</text>
    <text id="step-1">Step 1</text>
    <StepOne bind:fileInfo />
    <text id="step-2">Step 2</text>
    <StepTwo bind:durationType />
    <text id="step-2">Step 3</text>
    <StepThree bind:dirInfo />
    <view id="btnGroup">
      <button id="saveBtn" on:clicked={onExport} enabled={canExport}
        >Export</button
      >
      <button id="promoBtn" on:clicked={onClicked}>Made by GeopJr</button>
    </view>
    <text id="statusText">
      {successText}
    </text>
  </view>
</window>

<style>
  #welcome-text {
    font-size: 24px;
    padding-top: 20px;
    qproperty-alignment: "AlignHCenter";
    font-family: "sans-serif";
  }
  #step-1,
  #step-2 {
    font-size: 18px;
    padding-top: 10px;
    padding-horizontal: 20px;
  }
  #btnGroup {
    flex-direction: "row";
    padding-horizontal: 50px;
    align-items: "center";
    flex-grow: 1;
  }
  #saveBtn {
    flex: 1;
    height: 40px;
  }
  #promoBtn {
    margin-left: 5px;
    width: 150px;
    height: 40px;
  }
  #statusText {
    qproperty-alignment: "AlignHCenter";
    font-family: "sans-serif";
  }
</style>
