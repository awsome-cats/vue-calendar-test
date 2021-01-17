<template>
  <div class="content">
      Calendar {{ displayDate }}
      <div class="button-area">
          <button @click="prevMonth">前の月</button>
          <button @click="nextMonth">次の月</button>
      </div>
      <!-- 曜日 -->
      <div class="youbi">
          <div class="youbi-item" v-for="n in 7" :key="n" >
            {{ youbi(n-1)}}
         </div>
      </div>
      
    <!-- カレンダーエリア -->
    <div class="calendar">
        <div class="calendar-weekly" v-for="(week, index) in calendars" :key="index" style="display:flex;">
            <div class="calendar-daily" v-for="(day, index) in week" :key="index" :class="{outsideCurrentMonth: currentMonth !==day.month}">
                <!-- week配列 -->
                <div class="calendar-day">
                    {{ day.day}}
                </div>
                <!-- event配列 -->
                <!-- draggable属性 -->
                <div class="calendar-event" 
                :style="`width:${dayEvent.width}%; background-color:${dayEvent.color}`"
                v-for="dayEvent in day.dayEvents" :key="dayEvent.id">
                    <div class="calendar-event" draggable="true" :style="`background-color:${dayEvent.color}`">
                        {{dayEvent.name}}
                    </div>
                </div>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
export default {
    data () {
        return {
            currentDate: moment(),
            events:[
            { id: 1, name: "ミーティング", start: "2021-01-01", end:"2021-01-01", color:"blue"},
            { id: 2, name: "イベント", start: "2021-01-02", end:"2021-01-03", color:"limegreen"},
            { id: 3, name: "会議", start: "2021-01-06", end:"2021-01-06", color:"deepskyblue"},
            { id: 4, name: "有給", start: "2021-01-08", end:"2021-01-08", color:"dimgray"},
            { id: 5, name: "海外旅行", start: "2021-01-08", end:"2021-01-11", color:"navy"},
            { id: 6, name: "誕生日", start: "2021-01-16", end:"2021-01-16", color:"orange"},
            { id: 7, name: "イベント", start: "2021-01-12", end:"2021-01-15", color:"limegreen"},
            { id: 8, name: "出張", start: "2021-01-12", end:"2021-01-13", color:"teal"},
            { id: 9, name: "客先訪問", start: "2021-01-14", end:"2021-01-14", color:"red"},
            { id: 10, name: "パーティ", start: "2021-01-15", end:"2021-01-15", color:"royalblue"},
            { id: 12, name: "ミーティング", start: "2021-01-18", end:"2021-01-19", color:"blue"},
            { id: 13, name: "イベント", start: "2021-01-21", end:"2021-01-21", color:"limegreen"},
            { id: 14, name: "有給", start: "2021-01-20", end:"2021-01-20", color:"dimgray"},
            { id: 15, name: "イベント", start: "2021-01-25", end:"2021-01-28", color:"limegreen"},
            { id: 16, name: "会議", start: "2021-01-21", end:"2021-01-21", color:"deepskyblue"},
            { id: 17, name: "旅行", start: "2021-01-23", end:"2021-01-24", color:"navy"},
            { id: 18, name: "ミーティング", start: "2021-01-28", end:"2021-01-28", color:"blue"},
            { id: 19, name: "会議", start: "2021-01-12", end:"2021-01-12", color:"deepskyblue"},
            { id: 20, name: "誕生日", start: "2021-01-30", end:"2021-01-30", color:"orange"},
            { id: 1, name: "ミーティング", start: "2021-02-01", end:"2021-02-01", color:"blue"},
            { id: 2, name: "イベント", start: "2021-02-02", end:"2021-02-03", color:"limegreen"},
            { id: 3, name: "会議", start: "2021-02-06", end:"2021-02-06", color:"deepskyblue"},
            ]
        }
    },
    mounted () {
        const startDate = this.getStartDate()
        const endDate = this.getEndDate()
        const weekNumber = Math.ceil(endDate.diff(startDate, 'days') / 7)
        console.log('weekNumberでカレンダーの高さを求める', weekNumber) // 6
        this.getCalendar()
        console.log('getCalendar', this.getCalendar())
        console.log('daysInMonth', moment('2020-02-01').daysInMonth())
        console.log('getDayEvents', this.getDayEvents())
    // 現在時間
    // let date = moment(this.currentDate)
    // console.log(date) // 現在時間(1/16)Sat Jan 16 2021 23:33:42 GMT+0900 (日本標準時)
    // 現在時間からみる最初の日時
    // let startOf = date.startOf('month')
    // console.log('startOf', startOf) // 現在時間からみる最初の日時(1/1)Fri Jan 01 2021 00:00:00 GMT+0900 (日本標準時)
    // 現在時間から見る最後の日時
    // let endOf = date.endOf('month')
    // console.log('endOf', endOf) // 現在時間から見る最後の日時(1/31)Sun Jan 31 2021 23:59:59 GMT+0900 (日本標準時)
    // 現在時間からみる最後の年の日時
    // let endYear = date.endOf('year')
    // console.log('endYear', endYear)// 現在時間からみる最後の年の日時(12/31)Fri Dec 31 2021 23:59:59 GMT+0900 (日本標準時)
    // 現在時刻から見る曜日に割り当てられた番号
    // let dayOfTodayNumber = date.day()
    // console.log('dayOfTodayNumber', dayOfTodayNumber) // dayOfTodayNumber 5
    // console.log(date.day())

    },

    methods: {
        // widthを返す
        getEventWidth(end, start, day) {
            let betweenDays = moment(end).diff(moment(start), 'days')
            if (betweenDays > 6 - day) {
                return (6 - day) * 100 + 95;
            } else {
                return betweenDays * 100 + 95
            }
        },
        // イベントを取得するメソッド
        // filteringされるデータの特定を行う
        getDayEvents (date, day) {
            let dayEvents = []
            this.events.forEach(event => {
            // events配列のstartプロパティにformat関数で変換
            let startDate = moment(event.start).format('YYYY-MM-DD')
            // console.log('startDate', startDate) // events配列のstartのあたいのみにfilteringする
            // events配列のendプロパティにformat関数で変換
            let endDate = moment(event.end).format('YYYY-MM-DD')

            // console.log('endDate', endDate)
            // dateに渡ってきているのはcurrentDate
            let Date = date.format('YYYY-MM-DD')

            if (startDate <= Date && endDate >= Date) {
                let width = this.getEventWidth(startDate, endDate, day)
                dayEvents.push({...event, width})
                console.log('dayEvents', dayEvents) // width: 95が追加された
            } else if(day === 0) {
                let width = this.getEventWidth(startDate, endDate, day)
                dayEvents.push({...event, width})
            }
          })
          return dayEvents;
        },
        // currentDateが変更したときに動的に表示を変更するメソッド
        nextMonth () {
            this.currentDate = moment(this.currentDate).add(1, 'month')
        },
        // currentDateが変更したときに動的に表示を変更するメソッド
        prevMonth () {
            this.currentDate = moment(this.currentDate).subtract(1, 'month')
        },
        // 月を表示する列のなかで繰り返しループする
        // comptedで実行する
        getCalendar () {
            let startDate = this.getStartDate()
            let endDate = this.getEndDate()
            const weekNumber = Math.ceil(endDate.diff(startDate, 'days') / 7)
            console.log('weekNumber', weekNumber) // 6列
            
            // カレンダーの配列を作成
            let calendars = []
            let calendarDate = this.getStartDate()
            for (let week = 0; week < weekNumber; week++) {
                let weekRow = [];
                for (let day = 0; day < 7; day++) {
                    let dayEvents = this.getDayEvents(calendarDate)
                    // weekRow配列の作成
                    // - date
                    // - month
                    // - events
                    weekRow.push({
                        day: calendarDate.get('date'),
                        month: calendarDate.format('YYYY-MM'),
                        dayEvents
                    })
                    // これを実行しないと列の最初の日のみがレンダリングされる
                    calendarDate.add(1, 'days')
                }
                calendars.push(weekRow)
                console.log('calendars配列', calendars) // [Array(7), Array(7), Array(7), Array(7), Array(7)]
                console.log('weedRow配列', weekRow) // [{…}, {…}, {…}, {…}, {…}, {…}, {…}]
            }
            return calendars;
        },
        getStartDate () {
            // 現在の日時
            let date = moment(this.currentDate)
            console.log('getStartDate-date', date) // Sat Jan 16 2021 23:57:25 GMT+0900 (日本標準時)
            // 現在の日時の月初め
            let startOfDate = date.startOf('month')
            console.log('startOfDate', startOfDate) // Fri Jan 01 2021 00:00:00 GMT+0900 (日本標準時)
            // その月の初日の曜日を数字で表示
            const  dayNumber = startOfDate.day()
            console.log('dayNumber', dayNumber) // 5
            // その初日の数字から曜日の0番の日時を返す
            let subtractDay = date.subtract(dayNumber, 'days')
            console.log('subtractDay', subtractDay) // Sun Dec 27 2020 00:00:00 GMT+0900 (日本標準時)
            return subtractDay
        },
        getEndDate () {
            // 現在の日時
            let date = moment(this.currentDate)
            console.log('getEndDate', date) //  Sun Jan 17 2021 00:04:38 GMT+0900 (日本標準時)
            // 現在の日時から見る月最後の日時
            let endOfDate = date.endOf('month')
            console.log('endOfDate', endOfDate) // un Jan 31 2021 23:59:59 GMT+0900 (日本標準時)
            // その最後の日の曜日を数字で表示
            const endDayNumber = date.day()
            console.log('endDayNumber', endDayNumber) // 0
            // その月の最後の日の曜日番号は0なので残りのマスがある
            // マスの数は6個なので0をひいた数を足すことになる
            // そのことでカレンダーの最後の日時がわかる
            let addDay = date.add(6 - endDayNumber, 'days')
            console.log('addDay', addDay) // Sat Feb 06 2021 23:59:59
            return addDay
        },
        youbi (dayIndex) {
            const week = ['日', '月', '火', '水', '木', '金', '土']
            return week[dayIndex]
        }
    },
    computed: {
        // カレンダー配列を返すgetCalendarをロードする
        calendars () {
            return this.getCalendar()
        },
        // カレンダーのタイトル横の現在の日付を表示する為
        displayDate () {
            return this.currentDate.format('YYYY[年]M[月]')
        },
        // 表示される月以外の日付と区別する為に現在の日付を取得
        currentMonth() {
            return this.currentDate.format('YYYY-MM')
        }
    }
}
</script>

<style>
.content{
  margin:2em auto;
  width:900px;
}
.button-area{
  margin:0.5em 0;
}
.button{
  padding:4px 8px;
  margin-right:8px;
}
.calendar{
  max-width:900px;
  border-top:1px solid #E0E0E0;
  font-size:0.8em;
}
.calendar-weekly{
  display:flex;
  border-left:1px solid #E0E0E0;
  /* background-color: black; */
}
.calendar-daily{
  flex:1;
  min-height:125px;
  border-right:1px solid #E0E0E0;
  border-bottom:1px solid #E0E0E0;
  margin-right:-1px;
}
.calendar-day{
  text-align: center;
}
.youbi {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-template-rows: repeat(1, 7fr);
    background-color:azure;
    border-right:1px solid #E0E0E0;
    border-left:1px solid #E0E0E0;
    border-top:1px solid #E0E0E0;

}
.youbi-item {
    box-sizing: border-box;
}
.outsideCurrentMonth {
    background-color: rgb(236, 235, 235);
    border:1px solid #E0E0E0;
}
.calendar-event{
  color:white;
  margin-bottom:1px;
  height:25px;
  line-height:25px;
}
</style>