
<template>
    <!-- <div class="clock" :class="{'is-small':size==='small'}" :style="clockStyle"> -->
    <div class="clock" :style="clockStyle">
        <div class="clock-circle"></div>
        <div class="clock-hour" :style="{transform:hourRotate}"></div>
        <div class="clock-minute" :style="{transform:minuteRotate}"></div>
        <div class="clock-second" :style="{transform:secondRotate}"></div>
        <b class="hour" v-for="h in timeList" :key="h">
            <span>
                <i :style="{transform:transform}">{{h}}</i>
            </span>
        </b>
        <b-table class="table" :items="items" :striped="striped" :bordered="bordered"></b-table>
        <input class="enter-time" v-model="newTime" name="newTime" id="myInput" placeholder="00 : 00 : 00">
        <button class="form-submit-button" v-on:click="checkInput">submit</button>
        //<h2>"{{ greeting }}"</h2>
        
    </div>
</template>

<script>
import Vue from "vue";
import axios from "axios";

import { BTable } from 'bootstrap-vue' 
Vue.component('b-table', BTable)

export default {
    name: "clock",
    
    data() {
        return {
            timeList: [12, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11],
            transform: "scale(1)",
            hourRotate: "rotatez(0deg)",
            minuteRotate: "rotatez(0deg)",
            secondRotate: "rotatez(0deg)",
            newTime: this.newTime,
            hours: '',
            minutes: '',
            seconds: '',
            items: [
          { age: 40, first_name: 'Dickerson', last_name: 'Macdonald' },
          { age: 21, first_name: 'Larsen', last_name: 'Shaw' },
          { age: 89, first_name: 'Geneva', last_name: 'Wilson' },
          { age: 38, first_name: 'Jami', last_name: 'Carney' }
        ],
        striped: false,
        bordered: false,
        greeting: this.greeting

        };
    },
    components: {
    BTable
    },
    props: ["time", "color", "border", "bg", "size"],
    computed: {
        clockStyle() {
            return {
                height: this.size,
                width: this.size,
                color: this.color,
                border: this.border,
                background: this.bg
            };
        }
    },
    watch: {
        time() {
            this.show();
        }
    },
    methods: {
        show() { //general
            this.showTime();
            if (this._timer) clearInterval(this._timer);
            if (!this.time) {
                this._timer = setInterval(() => {
                    this.showTime();
                }, 1000);
            }
        },
        showTime() {
            let times;
            if (this.time) {
                times = this.time.split(":");
            } else {
                const now = new Date();
                times = [now.getHours(), now.getMinutes(), now.getSeconds()];
                
            }
            let hour = +times[0];
            this.hours=hour;
            hour = hour > 11 ? hour - 12 : hour;
            let minute = +times[1];
            let second = +times[2] || 0;
            let hourAngle = hour * 30 + minute * 6 / 360 * 30;
            let minuteAngle = minute * 6;
            let secondAngle = second * 6;
            this.hourRotate = `rotatez(${hourAngle}deg)`;
            this.minuteRotate = `rotatez(${minuteAngle}deg)`;
            this.secondRotate = `rotatez(${secondAngle}deg)`;
            this.minutes=minute;
            this.seconds=second;
            //console.log(times);

        },
        checkInput() { // checking if the value is bigger and at the right format.
            console.log(this.newTime);
            const regex = /(1[0-2]|0?[1-9]):(?:[0-5]\d):(?:[0-5]\d)/ //clock regular expression.
            const time_new = this.newTime;
            const matches = regex.exec(time_new);
            if(matches!==null){
            console.log(matches[0]+"ok");
            let time_arr;
            time_arr = time_new.split(":");
            let h = parseInt(time_arr[0],10);
            let m = parseInt(time_arr[1],10);
            let s = parseInt(time_arr[2],10);
            console.log("new: "+h +" "+ m + " " + s);
            console.log("old: "+this.hours +" "+ this.minutes + " " + this.seconds);
            let flag=false;
                if(h > this.hours){ //check if the time is bigger.
                flag=true;
                }
                else if(h == this.hours){
                    if(m > this.minutes){
                    flag=true;
                    }
                    else if(m == this.minutes && s > this.seconds){
                    flag=true;
                    }
                }

                if(flag){
                    console.log("yay!");
                    this.callAPI();
                    if(this.greeting != "")
                    console.log(this.greeting);

                }
                
                
            }
             //this.$emit('add-todo', this.newTime);

        },
        callAPI() {
      axios
        .get("http://localhost:8888/execute")
        .then(resp => {
          this.greeting = resp.data;
        })
        .catch(err => {
          this.greeting = err;
        });
        
    }
    },
    mounted() {
        let scale = this.$el.clientWidth / 120;
        scale = scale > 3 ? 3 : scale;
        this.transform = `scale(${scale})`;
        this.show();
    },
    destroyed() {
        if (this._timer) clearInterval(this._timer);
    }
};
</script>

<style lang="scss" scoped>
$angle: 30deg;
.clock {
    position: relative;
    display: inline-block;
    vertical-align: middle;
    width: 200px;
    height: 200px;
    border: 4px solid;
    border-radius: 100%;
    text-align: center;
    font-size: 18px;
    background-color: #E6E6FA; 
    .hour {
        position: absolute;
        top: 0px;
        left: 50%;
        display: block;
        width: 20px;
        height: 50%;
        margin-left: -10px;
        padding-top: 4%;
        font-weight: 400;
        transform-origin: bottom;
        user-select: none;
        box-sizing: border-box;
        > span {
            display: block;
            > i {
                display: block;
                font-style: normal;
            }
        }
    }
    @for $i from 2 through 12 {
        .hour:nth-of-type(#{$i}) {
            transform: rotatez(#{$angle * ($i - 1)});
            > span {
                transform: rotatez(#{-$angle * ($i - 1)});
            }
        }
    }
    .clock-circle {
        position: absolute;
        top: 50%;
        left: 50%;
        width: 16px;
        height: 16px;
        transform: translate(-50%, -50%);
        border: 2px solid #666666;
        border-radius: 100%;
        background-color: #ffffff;
        z-index: 1;
        box-sizing: border-box;
        &:before {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: block;
            content: "";
            width: 4px;
            height: 4px;
            border-radius: 100%;
            background-color: #666666;
        }
    }
    .clock-hour,
    .clock-minute,
    .clock-second {
        position: absolute;
        top: 15%;
        left: 50%;
        display: block;
        width: 3px;
        height: 35%;
        margin-left: -1px;
        border-radius: 5px;
        transform-origin: bottom;
        background-color: #666666;
    }
    .clock-hour {
        top: 30%;
        width: 4px;
        height: 20%;
        margin-left: -2px;
    }
    .clock-second {
        width: 1px;
    }
}
.clock.is-small {
    width: 80px;
    height: 80px;
    border-width: 1px;
    font-size: 12px;
    .clock-circle {
        width: 10px;
        height: 10px;
        border-width: 1px;
        &:before {
            width: 2px;
            height: 2px;
        }
    }
}
  .enter-time {
    flex: 0;
    padding: 10px;
    border: 2px solid grey;
    width: 100px; 
    height: 20px;
    //margin-top: 220px;
    margin-right: 0px;

  }

  .form-submit-button {
background: #008CBA;
color: white;
//border-style: outset;
border-color: #008CBA;
height: 30px;
width: 70px;
font: bold 15px arial;
//text-shadow:none;
margin-top: 10px;
margin-bottom: 0px;
margin-left: 20px;
margin-right: 20px;

}

.table {
   max-width: 500px;
   margin-top: 30px;
   margin-left: 260px;
   margin-bottom: 50px;
   font-size: 1.2em;
}

</style>