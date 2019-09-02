<template>
  <div id="app">
    <div class="logo">
      <img src="./assets/logo.png" />
      <h2>Gondr Calendar</h2>
    </div>
    <div class="button-row">
      <button @click="prev">이전</button>
      <div class="month-label">{{current.getMonth() + 1}} 월</div>
      <button @click="next">다음</button>
    </div>
    <div class="calendar">
      <div v-for="week in dayList" class="week">
        <day
          v-for="day in week"
          :day="day"
          :key="day.idx"
          @edit="openEditPopup"
          @open="openPopupWindow(day.idx)"
        >{{day}}</day>
      </div>
    </div>

    <div id="popup" v-if="popupOpen">
      <div class="inner">
        <div class="form-group">
          <input v-model="todoTitle" type="text" placeholder="할 일을 입력하세요" />
          <textarea v-model="todoContent" placeholder="상세한 내용을 입력하세요"></textarea>
          <button type="button" @click="saveTodo">입력</button>
          <button type="button" @click="popupOpen = false">닫기</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  created() {
    let now = new Date();
    this.drawCalendar(now);
  },
  methods: {
    drawCalendar(now) {
      this.current = now;
      let first = now.getFirstDate();
      first.addDay(-first.getDay()); //달력의 최초 시작일이 나온다.
      this.dayList = [];
      while (true) {
        let arr = [];
        for (let i = 0; i < 7; i++) {
          arr.push({ idx: first.getTime(), date: first.clone(), list: [] });
          first.addDay(1);
        }
        this.dayList.push(arr);
        if (first.getMonth() != now.getMonth()) {
          break;
        }
      }
    },
    prev() {
      let pre = this.current.clone();
      pre.setMonth(pre.getMonth() - 1);
      this.drawCalendar(pre);
    },
    next() {
      let nex = this.current.clone();
      nex.setMonth(nex.getMonth() + 1);
      this.drawCalendar(nex);
    },
    openPopupWindow(day) {
      console.log(day);
      this.currentPopupData = day;
      this.popupOpen = true;
    },
    saveTodo() {
      if (this.currentEditItem != null) {
        this.currentEditItem.name = this.todoTitle;
        this.currentEditItem.content = this.todoContent;
        this.currentEditItem = null;
      } else {
        this.dayList.forEach(week => {
          week.forEach(day => {
            if (day.idx == this.currentPopupData) {
              day.list.push({
                name: this.todoTitle,
                content: this.todoContent
              });
            }
          });
        });
      }

      this.popupOpen = false;
      this.todoTitle = "";
      this.todoContent = "";
    },
    openEditPopup(e, idx, dIdx) {
      this.dayList.forEach(week => {
        week.forEach(day => {
          if (day.idx == dIdx) {
            this.todoTitle = day.list[idx].name;
            this.todoContent = day.list[idx].content;
            this.currentEditItem = day.list[idx];
            this.popupOpen = true;
          }
        });
      });
    }
  },
  data() {
    return {
      current: null,
      dayList: [],
      popupOpen: false,
      currentPopupData: null,
      todoTitle: "",
      todoContent: "",
      currentEditItem: null
    };
  }
};
</script>

<style>
.logo {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.button-row {
  width: 80%;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
}

.month-label {
  font-size: 25px;
}
.calendar {
  width: 80%;
  margin: 0 auto;
  margin-top: 20px;
}
.week {
  display: grid;
  grid-gap: 10px;
  margin-bottom: 10px;
  grid-template-columns: repeat(7, 1fr);
  grid-template-rows: minmax(120px, auto);
}

#popup {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

#popup > .inner {
  width: 400px;
  height: 300px;
  background-color: #fff;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

#popup > .inner > .form-group {
  display: grid;
  grid-template-columns: 1fr;
  width: 80%;
  height: 80%;
  grid-gap: 10px;
}

.form-group > textarea {
  height: 150px;
}
</style>
