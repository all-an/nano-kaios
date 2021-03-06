<script lang="ts">
    import LabelledInput from "../../components/LabelledInput.svelte";
    import Button from "../../components/Button.svelte";
    import type {NanoAddress, NANO, NanoAccount, RAW} from "../../machinery/models";
    import type {SendAction} from "../../machinery/NavigationState";
    import {tools} from "nanocurrency-web";
    import {sendNano} from "../../machinery/nano-ops";
    import {nanoToRaw, rawToNano} from "../../machinery/nanocurrency-web-wrapper";
    import CameraCapture from "../../components/CameraCapture.svelte";
    import LabelledLoader from "../../components/LabelledLoader.svelte";
    import {popState} from "../../machinery/eventListener";

    export let sendType: SendAction;
    export let account: NanoAccount;
    export let balance: RAW;

    let sending: boolean = false;

    let toAddress: NanoAddress | undefined
    let showCamera: boolean = sendType === 'qr'

    let sendValue: number | undefined = undefined

    const setAddress = (event) => {
        const address = event.target.value;
        if (tools.validateAddress(address)) {
            toAddress = address
        }
    }

    const setAmount = (event) => {
        let strAmount: string = event.target.value;
        const amount: number = Number.parseFloat(strAmount);
        if (!isNaN(amount)) {
            sendValue = amount;
        }
    }

    const setMax = () => {
        sendValue = Number.parseFloat(rawToNano(balance, 10).amount)
    }

    const send = async () => {
        if (toAddress && sendValue > 0) {
            sending = true;
            try {
                await sendNano(account, toAddress, nanoToRaw({amount: sendValue.toString()}), balance)
                popState();
            } catch (e) {
                console.log(e)
            }
            sending = false;
        }
    }

    const scannedAddress = (address: NanoAddress) => {
        toAddress = address;
        showCamera = false;
        sendType = 'address'
    }

</script>

{#if sending}
    <LabelledLoader languageId="sending-funds"/>
{:else}
    {#if sendType === 'qr' && showCamera}
        <CameraCapture scannedAddress={scannedAddress}/>
    {:else}
        <LabelledInput languageId="send-address" type="text" on:change={setAddress} value={toAddress}/>
        <LabelledInput languageId="send-amount" type="text" on:change={setAmount} value={sendValue}/>
        <Button languageId="send-max-button" on:click={setMax}/>
        <Button languageId="send-button" on:click={send}/>
    {/if}
{/if}
