<template>
    <div class="flex gap-2 py-6 px-20 bg-[#AFEEEE]">
        <div style="width: 30%" class="bg-white p-4">
            <div>
                <FullCalendar ref="calendarRef2" :options="calendarOptionsLeft"/>
            </div>
            <div class="flex justify-between mt-5">
                <b class="text-[#0F4C81] text-[20px]">Upcoming Events</b>
                <button class="bg-[#0F4C81] rounded-xl text-white px-3 py-1 text-[12px]">View All</button>
            </div>
            <div>
                <b class="text-[#A9A9A9]">Today, {{ new Date().getDate() }} {{ monthNames[new Date().getMonth()] }}</b>
            </div>
            <div class="block-events mt-3">
                <div v-for="event of events" :class="checkBorderLeftAndBg(event)"
                     class="flex gap-2 justify-between mb-2 rounded-md p-2 pr-4 border-l-8 border-[#0F4C81]">
                    <div class="col-8">
                        <p :class="checkColorTextTitle(event)" class="text-[14px] text-[#0F4C81] font-bold">
                            {{ event.title }}</p>
                        <p :class="checkColorTextTime(event)" class="text-[12px] mt-1">{{ event.start_time }} -
                            {{ event.end_time }} {{ event.timezone }}</p>
                        <div v-if="event.type == 'appointment'" class="flex mt-1">
                            <img class="w-[20px] h-[20px] rounded-full mr-1" :src="event.client.avatar">
                            <div :class="checkColorTextTime(event)" class="text-[12px]">
                                <button @click="handleClientProfileClick(event)" class="underline">View Client Profile
                                </button>
                            </div>
                        </div>
                    </div>
                    <div v-if="event.type == 'appointment'" class="col-4">
                        <div :class="event.status == 'important' ? 'bg-[#5684AE]' : 'bg-[#FFFACD]'"
                             class="px-2 py-1 rounded-full ">
                            <i class="fa-solid fa-video"></i>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div style="width: 70%" class=" position-relative bg-white w-full p-4">
            <FullCalendar ref="calendarRef" :options="calendarOptionsRight"/>
            <div class="month-picker">
                <select class="bg-[#5684AE] pl-3 py-1 rounded-xl text-white cursor-pointer custom-select"
                        id="monthSelect" @change="handleMonthChange">
                    <option value="" disabled selected>Month</option>
                    <option value="01">January</option>
                    <option value="02">February</option>
                    <option value="03">March</option>
                    <option value="04">April</option>
                    <option value="05">May</option>
                    <option value="06">June</option>
                    <option value="07">July</option>
                    <option value="08">August</option>
                    <option value="09">September</option>
                    <option value="10">October</option>
                    <option value="11">November</option>
                    <option value="12">December</option>
                </select>
            </div>
        </div>
    </div>
    <div>
        <a-modal v-if="eventInfo" v-model:visible="openModalEventInfo" :title="eventInfo.title" :footer="null"
                 width="450px">
            <p><b>Date: </b>{{ moment(eventInfo.start).format('DD/MM/YYYY') }}</p>
            <p><b>Time: </b>{{ eventInfo.extendedProps.start_time }} - {{ eventInfo.extendedProps.end_time }}
                {{ eventInfo.extendedProps.timezone }}</p>
            <p><b>Description: </b>{{ eventInfo.extendedProps.description }}</p>
            <p>
                <b>Client: </b><br>
                <b>- Name: </b>{{ eventInfo.extendedProps.client.name }}<br>
                <b>- Gender: </b>{{ eventInfo.extendedProps.client.gender }}<br>
                <b>- Age: </b>{{ eventInfo.extendedProps.client.age }}
            </p>
        </a-modal>
    </div>
    <div>
        <a-modal v-if="eventInfoClientProfile" v-model:visible="openModalClientProfile"
                 :title="eventInfoClientProfile.title" :footer="null" width="350px">
            <p>
                <b class="underline">Client Profile: </b><br><br>
                <b class="font-medium">- Avatar: <img class="w-[75px] h-[80px] inline-block ml-2"
                                                      :src="eventInfoClientProfile.client.avatar"></b><br><br>
                <b class="font-medium">- Name: </b>{{ eventInfoClientProfile.client.name }}<br>
                <b class="font-medium">- Gender: </b>{{ eventInfoClientProfile.client.gender }}<br>
                <b class="font-medium">- Age: </b>{{ eventInfoClientProfile.client.age }}
            </p>
        </a-modal>
    </div>
</template>
<script setup>
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import interactionPlugin from '@fullcalendar/interaction'
import moment from 'moment';
import {ref} from "vue";

const calendarRef = ref(null);
const calendarRef2 = ref(null);
const openModalEventInfo = ref(false);
const openModalClientProfile = ref(false);
const eventInfo = ref();
const eventInfoClientProfile = ref();
const monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];

const events = [
    {
        title: 'First session with Alex Stan',
        date: '2024-10-26',
        start_time: '09:00 AM',
        end_time: '09:30 AM',
        timezone: 'GTM+8',
        type: 'appointment',
        description: 'Description appointment',
        client: {
            avatar: '/avatar.jpg',
            name: 'Alex Stan',
            gender: 'Male',
            age: 25
        },
        status: 'important'
    },
    {
        title: 'Webinar: How to cope with trauma in professional file',
        date: '2024-10-27',
        start_time: '09:00 AM',
        end_time: '09:30 AM',
        timezone: 'GTM+8',
        type: 'event',
        description: 'Description appointment',
        url: 'https://www.eventbrite.sg/'
    },
    {
        title: 'First session with Alex Stan',
        date: '2024-10-31',
        start_time: '09:00 AM',
        end_time: '09:30 AM',
        timezone: 'GTM+8',
        type: 'appointment',
        description: 'Description appointment',
        client: {
            avatar: '/avatar-1.jpg',
            name: 'Alex Stan',
            gender: 'Female',
            age: 30
        },
        status: 'normal'
    },
]

const calendarOptionsLeft = {
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    headerToolbar: {
        start: '',
        center: 'prev,title,next',
        end: ''
    },
    height: 520
}

const handleEventClick = (event) => {
    eventInfo.value = event.event;
    openModalEventInfo.value = true;
}

const handleClientProfileClick = (event) => {
    eventInfoClientProfile.value = event;
    openModalClientProfile.value = true;
}

const calendarOptionsRight = {
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    headerToolbar: {
        start: 'today,prev,next,title',
        center: '',
        end: ''
    },
    events: events,
    eventClassNames: function (arg) {
        switch (arg.event.extendedProps.type) {
            case 'appointment':
                if (arg.event.extendedProps.status === 'important') {
                    return 'event-appointment-important';
                }
                return 'event-appointment-normal';
            case 'event':
                return 'event-event';
        }
    },
    buttonText: {
        today: 'Today'
    },
    eventClick: handleEventClick,
}

const handleMonthChange = (event) => {
    const selectedMonth = event.target.value;
    const currentYear = new Date().getFullYear();
    calendarRef.value.getApi().gotoDate(`${currentYear}-${selectedMonth}-01`);
};

const checkBorderLeftAndBg = (event) => {
    switch (event.type) {
        case 'appointment':
            if (event.status === 'important') {
                return 'border-[#0F4C81] bg-[#FFE4C8] ';
            }
            return 'border-[#FF8C00] bg-[#5684AE]';
        case 'event':
            return 'border-[#5684AE] bg-[#F9BE81]';
    }
}

const checkColorTextTitle = (event) => {
    switch (event.type) {
        case 'appointment':
            if (event.status === 'important') {
                return 'text-[#0F4C81]';
            }
            return 'text-white';
        case 'event':
            return 'text-[#0F4C81;]';
    }
}

const checkColorTextTime = (event) => {
    switch (event.type) {
        case 'appointment':
            if (event.status === 'important') {
                return 'text-[#5684AE]';
            }
            return 'text-[#FFFACD]';
        case 'event':
            return 'text-[#5684AE]';
    }
}

</script>

<style>
.fc-toolbar-chunk > div {
    display: flex !important;
    align-items: center;
}

.month-picker {
    position: absolute;
    top: 44px;
    right: 94px;
    height: 30px;
    margin-left: 10px;
}

.custom-select {
    appearance: none;
    background-repeat: no-repeat;
    background-position: right 0.7rem top 50%;
    background-size: 0.65rem auto;
    background-image: url("/icon-down.svg")
}

.event-appointment-important {
    background-color: #FFE4C8;
    border: 0;
    border-left: 4px solid #0F4C81;
    border-radius: 4px;
    padding-left: 3px;
    cursor: pointer;
}

.event-appointment-important .fc-event-title {
    color: #5684AE !important;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.event-appointment-normal {
    background-color: #5684AE;
    border: 0;
    border-left: 4px solid #FF8C00;
    border-radius: 4px;
    padding-left: 3px;
    cursor: pointer;
}

.event-appointment-normal .fc-event-title {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.event-event {
    background-color: #F9BE81;
    border: 0;
    border-left: 4px solid #5684AE;
    border-radius: 4px;
    padding-left: 3px;
}

.event-event .fc-event-title {
    color: #5684AE !important;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.fc-daygrid-day-top {
    justify-content: center;
    margin-top: 4px;
}

.fc .fc-button-primary {
    background-color: unset !important;
    color: #5684AE !important;
    border-color: white !important;
}

.fc .fc-button-primary:hover {
    color: unset;
}

.fc .fc-today-button {
    border: 1px solid #5684AE !important;;
    color: #5684AE !important;
    border-radius: 12px !important;
    padding: 4px 10px;
}

.fc .fc-toolbar-title {
    color: #0F4C81;
    font-weight: bold;
}

.fc-day-today .fc-daygrid-day-top a {
    width: 33px;
    text-align: center;
    background: #0F4C81;
    border-radius: 50%;
    color: white;
}

.fc .fc-daygrid-day.fc-day-today {
    background: none;
}

</style>
