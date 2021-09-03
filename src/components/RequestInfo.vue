<template>
      <div class="request-form">
        <form class="form-section" @submit.prevent="handleSubmit">
            <div class="input-section">
            <select class="select-type" v-model="request_type">
                <option value="GET" selected>GET</option>
                <option value="POST">POST</option>
                <option value="PUT">PUT</option>
                <option value="PATCH">PATCH</option>
                <option value="DELETE">DELETE</option>
            </select>
            <input class="standard-input url-input" required type="URL" v-model="request_url" placeholder="https://www.example.com"/>
            <button type="submit" class="btn btn-primary submit-btn">SUBMIT</button>
            </div>
            <tab-section :params="params_array" :headers="headers_array" @attrs-change="handleAttrsChange" @delete-row="handleRowDeletion" @insert-row="handleRowInsertion" />
        </form>
        <response-section :headersContent="responseHeaders" :responseDetails="responseDetails" :responseBody="responseData ? colorize(JSON.stringify(responseData, null, 2)) : ''"/>
      </div>
</template>
<script>
import TabSection from "./TabSection";
import ResponseSection from "./ResponseSection";
import axios from "axios";
import prettyBytes from 'pretty-bytes';
const _ = require("lodash");

export default {
    name: 'request-info',
    data() {
        return {
            request_type: 'GET',
            request_url: "",
            params_array: [],
            headers_array: [],
            paramsObject: {},
            headersObject: {},
            responseHeaders: {},
            responseDetails: {},
            responseData: null
        };
    },
    computed: {
        getJSON() {
            return this.responseData ? JSON.stringify(this.responseData, null, 2) : ''
        }
    },
    components: {
        TabSection,
        ResponseSection
    },
    methods: {
        handleSubmit() {
            const startTime = new Date().getTime();
            axios({
                url: this.request_url, // https://get.geojs.io/v1/ip/country.json?ip=8.8.8.8
                method: this.request_type,
                params: this.paramsObject,
                headers: this.headersObject
            }).then(response => {
                this.updateResponseHeaders(response.headers);
                this.updateResponseDetails(response, startTime);
                this.responseData = response.data;
            }).catch(e => e.response);
        },
        updateResponseHeaders(headers) {
            this.responseHeaders = headers;
        },
        updateResponseDetails(response, hitTime) {
            this.responseDetails.status = response.status;
            this.responseDetails.time = new Date().getTime() - hitTime;
            this.responseDetails.size = prettyBytes(
                JSON.stringify(response.data).length +
                JSON.stringify(response.headers).length
            )
        },
        handleRowDeletion(args) {
            this[args.page].splice(args.index, 1);
            if(args.page === 'params_array') this.handleAttrsChange({newVal : this[args.page], page: 'params_array'});
            else this.handleAttrsChange({newVal : this[args.page], page: 'headers_array'});
        },
        handleRowInsertion(args) {
           this[args].push({key: '', value: ''});
        },
        handleAttrsChange(args) {
            this[args.page] = _.cloneDeep(args.newVal);
            if(args.page === 'params_array') {
                this.paramsObject = {};
                this[args.page].forEach(param => {
                    if(param.key !== '') this.paramsObject[param.key] = param.value;            
                });
            } else {
                this.headersObject = {};
                this[args.page].forEach(header => {
                    if(header.key !== '') this.headersObject[header.key] = header.value;            
                });
            }
        }
    }
}
</script>

<style lang="scss">
 .request-form {
     padding: 20px;
     .form-section {
         .input-section {
             display: flex;
             width: 100%;
             select {
                 width: 100px;
             }
             .url-input {
                margin-left: 5px;
                width: 85%;
             }
            .submit-btn {
                margin-left: 5px;
                width: 100px;
            }
         }
     }
 }
</style>