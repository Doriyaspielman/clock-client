<template>
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
    <input class="enter-time" v-model="newTime" name="newTime" id="myInput" placeholder="00 : 00 : 00">
    <button class="form-submit-button" v-on:click="checkInput">submit</button>
    <!-- <b-table striped hover class="table" :data="data"></b-table> -->
   <table class="table" :data="data">
  <thead>
    <tr>
      <th>Name</th>
      <th>Role</th>
      <th>Workplace</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="(comment, idx) in data" v-bind:key="idx">
       <td>{{ comment.name }}</td>  
       <td>{{ comment.role }}</td> 
       <td>{{ comment.workplace }}</td>  
    </tr>
   </tbody>
</table>
        
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
            cur_h : 19,
            cur_m : 20,
            cur_s : 15,
            transform: "scale(1)",
            hourRotate: "rotatez(0deg)",
            minuteRotate: "rotatez(0deg)",
            secondRotate: "rotatez(0deg)",
            newTime: this.newTime,
            hours: '',
            minutes: '',
            seconds: '',
            data: [] //table info
        };
    },
    components: {
        // BTable
    },
    props: ["time", "color", "border", "bg", "size"],
    computed: {
        clockStyle() {
            return {
                height: this.size,
                width: this.size,
                color: this.color,
                border: this.border,
                background: this.bg,

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
            let times=null;
            times = [this.cur_h, this.cur_m, this.cur_s];
            this.cur_s-=1;
            if(this.cur_s==0){
                this.cur_m-=1;
            }
            if(this.cur_m==0){
                this.cur_h-=1;
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

        },
        checkInput() { // checking if the value is bigger and at the right format.
            const regex = /([0-2]\d):([0-5]\d):([0-5]\d)/ //clock regular expression.
            const time_new = this.newTime;
            const matches = regex.exec(time_new);
            if(matches!==null && time_new.length==8){//check if input is currect
            // console.log(matches[0]+"ok");
            let time_arr;
            time_arr = time_new.split(":");
            let h = parseInt(time_arr[0],10);
            let m = parseInt(time_arr[1],10);
            let s = parseInt(time_arr[2],10);
            let flag=this.ifHourIsBigger(h,m,s);
                if(flag){ //if the time is valid
                     console.log("flag");
                    this.callAPI() //connect to server
                }            
            }
        },
        ifHourIsBigger(h, m, s){
            if(h > this.hours){ //check if the time is bigger.
                return true;
            }
            else if(h == this.hours){
                if(m > this.minutes){
                    return true;
                }
                else if(m == this.minutes && s > this.seconds){
                    return true;
                }
            }
            return false
        },
        callAPI() { //server connection
            axios
                .get("http://localhost:8888/execute")
                .then(resp => {
console.log("server")
                this.data = resp.data;
                })
                .catch(err => {
                this.data = err;
                });
                
            },
            
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
    margin-top: 220px;
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
   margin-right: 100px;
   margin-bottom: 50px;
   font-size: 1em;
}

  tr, td {
    border: 1px solid #ddd;
    padding: 1.314em;
  }

</style>