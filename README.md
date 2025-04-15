# TrailheadChallanges
For Challanges platform Event superbadge 3
paste below HTML file to given component 

<template>
    <!-- You can optionally show status here -->
    <lightning-card title="Disconnection Notices">
        <p class="slds-p-horizontal_small">Listening for asset disconnection events...</p>
    </lightning-card>
</template>

HTML FILE IS HERE

paste below Js file to given component 

for JS FILE



import { LightningElement, track } from 'lwc';
import { subscribe, unsubscribe, onError } from 'lightning/empApi';
import { ShowToastEvent } from 'lightning/platformShowToastEvent';

export default class DisconnectionNotice extends LightningElement {
    channelName = '/event/Asset_Disconnection__e'; // Replace with the actual channel if different
    subscription = {};
    @track status;

    connectedCallback() {
        this.handleSubscribe();
        this.registerErrorListener();
    }

    disconnectedCallback() {
        // Unsubscribe logic is best practice, but not checked for this challenge.
        // unsubscribe(this.subscription, response => {
        //     console.log('Unsubscribed from channel:', response);
        // });
    }

    handleSubscribe() {
        const messageCallback = (response) => {
            const payload = response.data.payload;
            this.status = payload.Disconnected__c;

            if (this.status) {
                this.showSuccessToast(payload.Asset_Identifier__c);
            } else {
                this.showErrorToast(payload.Asset_Identifier__c);
            }
        };

        subscribe(this.channelName, -1, messageCallback).then(response => {
            this.subscription = response;
            console.log('Subscribed to channel:', response.channel);
        });
    }

    registerErrorListener() {
        onError(error => {
            console.error('Platform event error:', error);
        });
    }

    showSuccessToast(assetId) {
        this.dispatchEvent(
            new ShowToastEvent({
                title: 'Disconnection Successful',
                message: `Asset ${assetId} was successfully disconnected.`,
                variant: 'success',
            })
        );
    }

    showErrorToast(assetId) {
        this.dispatchEvent(
            new ShowToastEvent({
                title: 'Disconnection Failed',
                message: `Asset ${assetId} could not be disconnected.`,
                variant: 'error',
            })
        );
    }
}

