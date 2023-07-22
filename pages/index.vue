
<template>
    <div class="container">
      <aside class="sidebar">
        <nav class="navigation">
            <img src="~/assets/Logo.png" alt="Logo" class="navigation__logo" />
            <button @click="currentTab = 'inbox'" :class="{ active: currentTab === 'inbox' }" class="navigation__button">
                <font-awesome-icon icon="fa-solid fa-inbox" style="color: #121829;" />
                 Inbox  
                 <label class="navigation__label">{{ paginatedInbox.length }}</label>
            </button>

            <button @click="currentTab = 'archive'" :class="{ active: currentTab === 'archive' }" class="navigation__button"> 
                <font-awesome-icon icon="fa-solid fa-box-archive" style="color: #121829;" /> Archive
                <label class="navigation__label">{{ paginatedArchive.length }}</label>
            </button>
        </nav>
        <button class="sidebar__logout" @click="logout">
            <font-awesome-icon icon="fa-solid fa-arrow-right-from-bracket" style="color: #4B5563;" />
            Logout</button>
      </aside>
      <div class="content">
        <div v-if="currentTab === 'inbox'" class="content__inbox">
          <h2 style="padding: 20px;">Inbox </h2>
          <div class="content__topNav">
            <label class="content__selectedEmail">
            <input type="checkbox" v-model="selectAll" @change="handleSelectAll" />
            Email Selected  ({{ selectedEmails.length }})
          </label>
          <div class="content__buttons">
            <button v-if="selectedEmails.length > 0" @click="markAsRead"  class="content__button"> 
                <font-awesome-icon icon="fa-regular fa-envelope-open" style="color: #4B5563;" />
                Mark as read (r)</button>
          <button v-if="selectedEmails.length > 0" @click="archiveSelectedEmails"  class="content__button">
            <font-awesome-icon icon="fa-regular fa-trash-can" style="color: #4B5563;" />
            Archive (a)</button>
          </div>

          </div>

          <ul>
            <li v-for="(email, index) in paginatedInbox" :key="index" @click="openModal(email)">
                <div class="content__emails" :style="{ opacity: email.isRead ? '0.5' : '1' }">
             <label class="content__selectedEmail">
                <input type="checkbox" v-model="selectedEmails" :value="email" 
                :checked="isSelected(email)"
                @click.stop="handleCheckboxClick($event, email)"/>
                {{ email.subject }}
              </label>
                </div>
     
            </li>
          </ul>

        </div>
        <div v-else class="archive">
          <h2 style="padding: 20px;">Archive </h2>
          <ul>
            <li v-for="(email, index) in paginatedArchive" :key="index" @click="openModal(email)" >
                <div class="content__emails">
                <label class="content__selectedEmail">
                <input type="checkbox"  />
                {{ email.subject }}
                </label>
                </div>
            </li>
          </ul>
        </div>
      </div>
   <!-- Modal -->
   <div v-if="selectedEmail" class="modal">

      <div class="modal__content">
        <div class="modal__header">
          <span class="modal__close" @click="closeModal">
            <font-awesome-icon icon="fa-solid fa-xmark" style="color: #4B5563;"/> Close (Esc)
        </span>
          <div class="modal__buttons">
     
           
          <button @click="markAsRead" :disabled="selectedEmailIds.length === 0" class="modal__button">
            <font-awesome-icon icon="fa-regular fa-envelope-open" style="color: #4B5563;" />
            Mark as read (r)
          </button>
          <button v-if="currentTab === 'inbox'" @click="archiveSelectedEmails" :disabled="selectedEmails.length === 0" class="modal__button">
            <font-awesome-icon icon="fa-regular fa-trash-can" style="color: #4B5563;" />
            Archive (a)
          </button>
   
          </div>
        
        </div>
        <div class="modal__body">
            <h3>{{ selectedEmail.subject }}</h3>
            <p>{{ selectedEmail.description }}</p>
        </div>

      </div>
    </div>
    </div>
  </template>

<script setup>
import { ref, computed } from 'vue';

const currentTab = ref('inbox');
const emailsPerPage = 15;
const inbox = ref([
  {id: 1 ,subject: 'Wave hello with video, reactions and more now in huddles',description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  {id: 2 ,subject: '[JIRA] (LXQ-2604) Learning path- file - Existing File -two buttons for viewing the file' ,description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  {id: 3 ,subject: 'Wave hello with video, reactions and more now in huddles' ,description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  {id: 4 ,subject: 'Wave hello with video, reactions and more now in huddles' ,description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  {id: 5 ,subject: 'Wave hello with video, reactions and more now in huddles' ,description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  {id: 6 ,subject: 'Wave hello with video, reactions and more now in huddles' ,description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },


  
]);
const archive = ref([
  { id: 7 ,subject: 'Email 1 in Archive' , description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  { id: 8 ,subject: 'Email 2 in Archive', description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },
  { id: 9 ,subject: 'Email 3 in Archive', description : 'Wave hello with video, reactions and more now in huddles In the early days of the pandemic, audio-only huddles helped recreate the informal discussions that traditionally took place in office cafés and hallways. Whether your team is remote, in the office, or a mix of both, working alongside your colleagues is as important as ever.' },

  
]);
const selectedEmails = ref([]);
const selectAll = ref(false);


//created in order if we want to make pagination it will be easy
const paginatedInbox = computed(() => {
  const startIndex = 0;
  const endIndex = startIndex + emailsPerPage;
  return inbox.value.slice(startIndex, endIndex);
});

const paginatedArchive = computed(() => {
  const startIndex = 0;
  const endIndex = startIndex + emailsPerPage;
  return archive.value.slice(startIndex, endIndex);
});



const visibleEmails = computed(() => {
  return currentTab.value === 'inbox' ? paginatedInbox.value : paginatedArchive.value;
});

function handleSelectAll() {
  if (selectAll.value) {
    selectedEmails.value = [...visibleEmails.value];
  } else {
    selectedEmails.value = [];
  }
}



function archiveSelectedEmails() {
  const emailsToArchive = selectedEmails.value.splice(0, selectedEmails.value.length);
  archive.value.push(...emailsToArchive);
  emailsToArchive.forEach((email) => {
    const index = inbox.value.findIndex((e) => e === email);
    if (index !== -1) {
      inbox.value.splice(index, 1);
    }
  });
}

const selectedEmail = ref(null);
const selectedEmailIds = ref([]);

function openModal(email) {
  selectedEmail.value = email;
}

function closeModal() {
  selectedEmail.value = null;
}

function isSelected(email) {
  return selectedEmailIds.value.includes(email.id);
}

function selectAllEmails() {
  selectedEmailIds.value = visibleEmails.value.map((email) => email.id);
}


function handleCheckboxClick(event, email) {
  event.stopPropagation(); // Prevent click event from propagating to parent elements
  const emailId = email.id;

  if (!selectedEmailIds.value.includes(emailId)) {
    // If email is not in selectedEmailIds, add it 
    selectedEmailIds.value.push(emailId);
  } else {
    // If email is already in selectedEmailIds, remove it 
    selectedEmailIds.value = selectedEmailIds.value.filter((id) => id !== emailId);
  }
}

function markAsRead() {
  selectedEmailIds.value.forEach((id) => {
    const email = inbox.value.find((e) => e.id === id);
    if (email) {
      email.isRead = true; 
    }
  });
}




</script>




<style>
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@200;400&display=swap');
/* Full-page layout */
html, body {
  height: 100%;
  margin: 0;
}

body {
  display: flex;
  font-family: Arial, sans-serif;
}

.container {
  flex: 1;
  display: flex;
  min-height: 100vh; /* Ensure the container takes up the full height of the viewport */
  width: 100vw;
  font-family: 'Plus Jakarta Sans';
}

/* Sidebar styles */
.sidebar {
    width: fit-content;
  background-color: white;
  color: #333;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  border-right: 1px solid #ddd;
}

.navigation {
  display: block;
  display: grid;
  margin: 10px;
  justify-items: center;
  
}

.navigation__logo {
    width: 40px;
    height: 20px;
    display: flex;
    margin: 0 150px 40px 10px;
}

.navigation__button {
  background-color: transparent;
  color: #333;
  border: none;
  font-size: 16px;
  margin-bottom: 10px;
  cursor: pointer;
  border-radius: 5px;
  padding: 10px 20px;
  display: flex;
  gap: 10px;
  position: relative;
  width: 200px;
}

.navigation__label{
    position: absolute;
    right: 20px;
}

.navigation__button.active {
    font-weight: bold;
    background-color: #d6e2fb;
    border-radius: 56px;
    color: black;
}

.sidebar__logout {
    background-color: transparent;
    color: #333;
    border: none;
    font-size: 16px;
    padding: 10px 15px;
    margin-top: auto;
    cursor: pointer;
    width: 100%;
    display: flex;
    gap: 10px;
}


.sidebar__logout:hover {
    font-weight: bold;
    background-color: #d6e2fb;
    border-radius: 56px;
    color: black;
}

.content {
  flex: 1;
  border-left: 1px solid #ddd;
}

.selection-info {
  padding: 20px;
}

.content__selectedEmail{
    display: flex;
    align-items: center;
    gap: 20px;
    padding-left: 20px;
}

.content__emails{
    height: 60px;
    align-items: center;
    display: flex;
    border-top: solid 1px #E5E7EB;
    border-bottom: solid 1px #E5E7EB;
    cursor: pointer;
}

.content__emails:hover{
    background-color: #D1E2FF;
}

ul{
    list-style-type: none;
    padding-left: 0px;
}


input[type="checkbox" i] {
    width: 20px;
    height: 20px;
}

.content__topNav{
    display: flex;
    position: relative;
}

.content__buttons{
    display: flex;
    gap: 10px;
    position: absolute;
    right: 10px;
    top: 5px;
}

.content__button{
    border: none;
    background-color: transparent;
    color: #4B5563;
    font-weight: bold;
    display: flex;
    gap: 10px;
    font-size: 15px;
    align-items: end;
}

.content__buttons  svg{
    width: 19px;
    height: 19px;
}

/* Modal styles */
.modal {
  display: flex;
  justify-content: right;
    align-items: center;
  position: fixed;
  z-index: 1;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.6);
}

.modal__content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  height: 100vh;
  width: 60%;
}

.modal__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  position: relative;
  padding: 20px;
}

.modal__buttons {
    display: flex;
    gap: 20px;
    position: absolute;
    right: 10px;
}

.modal__button{
    border: none;
    background-color: transparent;
    color: #4B5563;
    font-weight: bold;   display: flex;
    gap: 10px;
    font-size: 15px;
    align-items: end;
}

.modal__button svg{
    width: 19px;
    height: 19px;
}

.modal__close {
    font-weight: bold;
    cursor: pointer;
    color: #4B5563;
    display: flex;
    align-items: center;
    gap: 10px;
}

.modal__close svg{
    width: 16px;
    height: 26px;
}

.modal__body {
  margin-top: 10px;
}

</style>
