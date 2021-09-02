<template>
    <div class="response-main">
        <h3>Response</h3>
        <div class="details">
          <div class="status">Status: <span>200</span></div>
          <div class="time">Time: <span>10</span>ms</div>
          <div class="size">Size: <span>180</span></div>
        </div>
        <div class="response-nav-tabs">
                <div class="tablink" :class="{'active-tab': activeTab.body, 'inactive-tab': !activeTab.body}" @click="openPage('body')">Body</div>
                <div class="tablink" :class="{'active-tab': activeTab.headers, 'inactive-tab': !activeTab.headers}" @click="openPage('headers')">Headers</div>
                <div>&nbsp;</div>
        </div>
        <div v-if="activeTab.body" id="response-body" class="tabcontent"></div>
        <div v-if="activeTab.headers" id="response-headers" class="tabcontent">
           <div v-for="(value, Key) in getHeaders" :key="Key" class="header-row">
               <div>{{Key}}</div>
               <div>{{value}}</div>
           </div>
        </div>

  </div>
</template>

<script>
export default {
    name: 'response-section',
    data() {
        return {
            activeTab: {
                body: true,
                headers: false,
            },
        }
    },
    props: {
        responseDetails: {
            type: Object,
            default: () => {}
        },
        headersContent: {
            type: Object,
            default: () => {}
        }
    },
    computed: {
        getHeaders() {
            return this.headersContent;
        }
    },
    methods: {
    openPage(pageName) {
            Object.keys(this.activeTab).forEach(key => this.activeTab[key] = false)
            this.activeTab[pageName] = true;
    },
    }
}
</script>

<style lang="scss" scoped>
.response-main {
    margin-top: 5%;
    .response-nav-tabs {
        display: flex;
        width: 100%;
        margin-top: 20px;
        /* Style tab links */
        .tablink {
        color: #444;
        float: left;
        outline: none;
        cursor: pointer;
        padding: 14px 16px;
        font-size: 17px;
        box-sizing: border-box;
        width: 100px;
        text-align: center;
        border-radius: 5px 5px 0 0;
            &:hover {
                background-color: #f5f5f5; 
            }
        }
        .active-tab {
           border: 2px solid #dee2e6;
           border-bottom: none;
        //    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
        }
        .inactive-tab {
            border: none;
            border-bottom: 2px solid #dee2e6;
            box-shadow: none;
        }
        div {
            width: calc(100% - 200px);
            border-bottom: 2px solid #dee2e6;
        }
    }
    .tabcontent {
        padding: 10px;
        border: 2px solid #dee2e6;
        border-top: none;
        .header-row {
            padding: 10px;
            display: flex;
            box-sizing: border-box;
            div:first-child {
                width: 120px;
            }
            div:last-child {
                margin-left: 20px;
                text-align: left;
            }
        }
    }
    .details {
        display: flex;
        div {
            margin-right: 2em;
        }
    }
}
</style>