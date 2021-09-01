<template>
  <div>
    <div class="nav-tabs">
        <div class="tablink" @click="openPage($event, 'query-params')" id="defaultOpen">Query params</div>
        <div class="tablink" @click="openPage($event, 'headers')">Headers</div>
        <div class="tablink" @click="openPage($event, 'json')">JSON</div>
        <div>&nbsp;</div>
    </div>
    <div id="query-params" class="tabcontent">
        Query params
    </div>

    <div id="headers" class="tabcontent">
        Headers
    </div>

    <div id="json" class="tabcontent">
        JSON
    </div>
  </div>
</template>

<script>
export default {
    name: 'tab-section',
    data() {
        return {
            rowCount: 1,
        }
    },
    mounted() {
        // Get the element with id="defaultOpen" and click on it
        document.getElementById("defaultOpen").click();
    },
    methods: {
        openPage(elmnt, pageName) {
            // Hide all elements with class="tabcontent" by default */
            var i, tabcontent, tablinks;
            tabcontent = document.getElementsByClassName("tabcontent");
            for (i = 0; i < tabcontent.length; i++) {
                tabcontent[i].style.display = "none";
            }

            // Remove the background color of all tablinks/buttons
            tablinks = document.getElementsByClassName("tablink");
            for (i = 0; i < tablinks.length; i++) {
                   tablinks[i].style.border = "none";
                   tablinks[i].style["border-bottom"] = "2px solid #dee2e6";
                   tablinks[i].style["box-shadow"] = "none";
            }

            // Show the specific tab content
            document.getElementById(pageName).style = {
                display : "block",
                backgroundColor: "#f8f9fa"
            }

            // Add the specific color to the button used to open the tab content
            elmnt.target.style.border= "2px solid #dee2e6"
            elmnt.target.style['border-top-radius']= '.25rem';
            elmnt.target.style['border-bottom']="none"
            elmnt.target.style['box-shadow'] = "rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px";
        }
    }
}
</script>

<style lang="scss" scoped>
.nav-tabs {
    display: flex;
    width: 100%;
    margin-top: 10px;
    /* Style tab links */
    .tablink {
    color: #444;
    float: left;
    outline: none;
    cursor: pointer;
    padding: 14px 16px;
    font-size: 17px;
    width: 15%;
        &:hover {
            background-color: #eee; 
            border-color: #e9ecef #e9ecef #dee2e6;
        }
    }
    div {
        width: 55%;
        border-bottom: 2px solid #dee2e6;
    }
}

/* Style the tab content (and add height:100% for full page content) */
.tabcontent {
  color: white;
  display: none;
  padding: 100px 20px;
  height: 100%;
}

// #query-params, #header #json {background-color: blue;}
</style>