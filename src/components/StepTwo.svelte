<script lang="ts">
  export let durationType = -1;

  function handleRadioToggle(radio, status) {
    if (status) durationType = radio;
  }
</script>

<view style="padding-horizontal: 50px;margin-top: 8px; ">
  <radioButton on:toggled={(x) => handleRadioToggle(0, x)}
    >Infinite Duration</radioButton
  >
  <radioButton on:toggled={(x) => handleRadioToggle(1, x)}
    >Negative Duration</radioButton
  >
</view>
