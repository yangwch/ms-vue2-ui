<!--
Created by Bane.Shi.
Copyright MoenSun
User: Bane.Shi
Blog: http://blog.fengxiaotx.com
Date: 2017/2/4
Time: 17:05-->
<template>
    <div class="ms-datepicker">
        <ms-date-panel v-show="(view=='date')"
                       :ms-datepicker-id="msDatepickerId"
                       :current-year="currentYear"
                       :current-month="currentMonth"
                       :current-date="currentDate"
                       :change-view="changeView">
        </ms-date-panel>
        <ms-month-panel v-show="(view=='month')"
                        :ms-datepicker-id="msDatepickerId"
                        :current-year="currentYear"
                        :current-month="currentMonth"
                        :change-view="changeView">
        </ms-month-panel>
        <ms-year-panel v-show="(view=='year')"
                       :ms-datepicker-id="msDatepickerId"
                       :current-year="currentYear" >
        </ms-year-panel>
    </div>
</template>
<script>
    import moment from "moment";
    import MsDatePanel from "./date-panel.vue";
    import MsMonthPanel from "./month-panel.vue";
    import MsYearPanel from "./year-panel.vue";

    import datepickerMixmin from "./mixins/datepickerMixin";
    import bus from "./DatepickerEvents";
    export default {
        name:'ms-datepicker',
        mixins:[datepickerMixmin],
        props: {
            "value":{
                type:[String,Date],
                default(){
                    return new Date();
                }
            },
            "change":{
                type:Function
            }
        },
        data(){
            return {
                msDatepickerId:_.uniqueId("ms_datepicker_"),
                "view":"date",
                currentYear:(new Date()).getFullYear(),
                currentMonth:(new Date()).getMonth(),
            }
        },
        computed: {
            "currentDate":function(){
                let me = this;
                if(me.value){
                    if(me.value instanceof Date){
                        return me.value;
                    }else {
                        return new Date(me.dateAdapter(me.value));
                    }
                }
                return new Date();
            }
        },
        watch:{
            "value":{
                handler:function(newVal,oldVal){
                    let me = this;
                    if(newVal){
                        if(!(newVal instanceof Date)){
                            newVal = new Date(me.dateAdapter(newVal));
                        }
                        me.currentYear = newVal.getFullYear();
                        me.currentMonth = newVal.getMonth();
                    }
                },
                immediate:true
            }
        },
        created(){
            let me = this;
            bus.$on("ms-datepicker-year-change",function(id,year){
                if(me.msDatepickerId == id){
                    me.currentYear = year;
                    me.view = "date";
                }
            });
            bus.$on("ms-datepicker-month-change",function (id,month) {
                if(me.msDatepickerId == id){
                    me.currentMonth = month;
                     me.view = "date";
                }
            });
            bus.$on("ms-datepicker-date-change",function(id,date){
                 if(me.msDatepickerId == id){
                    if(me.change){
                        me.change(date);
                    }
                }
            });
        },
        updated(){
            let me = this;
        },
        methods:{
            changeView:function(view){
                let me = this;
                me.view = view;
            }
        },
        components: {
            MsDatePanel,
            MsMonthPanel,
            MsYearPanel
        }
    }
</script>
<style>

</style>
