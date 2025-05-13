<template>
  
    <div id="app">
        <div class="header">Simple Notes</div>
        <div v-if="!currentUser" >
            <LoginForm 
              :users="users"
              @identify-user="setCurrentUserData"
            />
        </div>
        <div v-else class="container">
            <div class="saidbar">
                <div>
                    <div class="user-container">
                        <span class="user-name">{{ currentUser }}</span> 
                        <button @click="logOut()"> Log Out </button>
                    </div>
                    <div class="add-project-form project-border">
                        <div class="projects-container">
                            <span class="projects-title">Projects</span> 
                            <div class="project-add" @click="openAddProject">+</div>
                        </div>
                        <form 
                          v-show="showAddProject"
                          class="add-project-form"
                          @submit.prevent="!newProject || addProject"
                        >
                            <input 
                              v-model="newProject"
                              type="text"
                              placeholder="Project name"
                              class="note-input projects-input-text" 
                              @input="projectDuplicated = false"
                              />
                            <button :disabled="!newProject" @click="addProject"> + Add New Project </button>
                            <div v-if="projectDuplicated">The project is duplicated</div>
                        </form>

                    </div>
                        <div v-if="projects.length">
                            <div 
                                v-for="proj in projects"
                                :key="`name_${proj}`"
                                
                                class="project-row"
                                :class="proj == project && 'project-row-activ'"
                                @click="project = proj">
                                    <div class="project-row-action">
                                        <span>{{ proj }}</span> 
                                        <div class="project-add-hote" @click="openAddNote(proj)">+</div>
                                    </div>
                                <div v-if="proj == nameNewProject" class="add-note-form">
                                    <form @submit.prevent="!disabledAddNote || addNote" class="add-project-container">
                                        <div class="note-actions">
                                            <span>Color: </span>
                                            <select v-model="color" class="note-input">
                                                <option value="#ffecb8">Yellow</option>
                                                <option value="#b8e0ff">Blue</option>
                                                <option value="#b8ffc5">Green</option>
                                                <option value="#ffb8b8">Red</option>
                                                <option value="#e8b8ff">Purple</option>
                                            </select>
                                        </div>
                                        <button :disabled="disabledAddNote" @click="addNote"> + Add New Note </button>
                                        <span v-if="errorMessage" class="error">{{ errorMessage }}</span>
                                      </form>
                                </div>
                            </div>
                            
                        </div>
                </div>
            </div>
                    <div v-for="proj in projects" :key="`name_${proj}`" class="project-container">
                        <div v-if="proj == project" class="project">
                          <span class="projects-title">{{ proj }}</span>
                          <div class="note-container">
                          <div v-for="note in getFilteredProject(proj)" :key="note.index" class="note-main">
                            <NoteCard 
                              v-model="note.title"
                              :index="note.index"
                              :color="note.color"
                              @delete-note="deleteNote"
                              @updateNote="handleUpdate"
                            />
                          </div>
                        </div>
                        </div>
                    </div>
                </div>
            </div>
</template>

<script>
import NoteCard from './note-сard.vue'
import LoginForm from './forms/login-form.vue'

export default {
  name: 'mainComponentNotes',
  components: {
    NoteCard,
    LoginForm,
  },
  data() {
    return {
        currentUser: "",
        text: "",
        project: "",
        color: "",
        users: [],
        loginError: "",
        userData: {
          notes: [],
        },
        projects: [],
        newProject: "",
        maxIndex: "",
        selectedProject: "",
        nameNewProject: "",
        showAddProject: false,
        errorMessage: "",
        projectDuplicated: false,
      };
},
  mounted() {
    this.users = [
      { name: "Met", password: "1111" },
      { name: "Ger", password: "2222" },
    ];
    this.setCurrentUserData(this.currentUser);
  },
  watch: {
    project() {
      this.nameNewProject = "";
    }
  },
  computed: {
    disabledAddNote() {
      return  !this.color || !this.project || !!this.errorMessage
    },
  },
  methods: {
    setCurrentUserData(user) {
      this.currentUser = user;
      this.userData = this.loadUserData(user) || {notes: []};
      if (this.userData.notes?.length) {
        this.projects = this.uniq(this.userData.notes.map(item => item.project))
      }
    },
    getFilteredProject(name) {
      return this.userData?.notes?.filter( item => item.project == name);
    },
    addProject() {
      this.projectDuplicated = false;
      if(this.newProject.trim()) {
        const foundProject = this.projects.some( item => item == this.newProject);
        if (!foundProject) {
          this.projects.push(this.newProject.trim());
          this.newProject = "";
        } else {
          this.projectDuplicated = true;
        }
      }
    },
    addNote() {
      const index = (new Date()).getTime().toString(36);
      this.userData.notes.push({
        index: index,
        title: this.text,
        color: this.color,
        project: this.project,
      });
      this.saveUserData(this.currentUser, this.userData);
      this.clearInputs();
    },
    deleteNote(id) {
      this.userData.notes = this.userData.notes.filter(item => item.index !== id)
      this.saveUserData(this.currentUser, this.userData);
    },
    saveUserData(username, data) {
      this.errorMessage = "";
      try {
        localStorage.setItem(`data_${username}`, JSON.stringify(data));
      } 
      catch(error) {
        this.errorMessage = 'Ошибка записи в localStorage:' + error;
      }
    },
    loadUserData(username) {
      const data = localStorage.getItem(`data_${username}`);
      return data ? JSON.parse(data) : null;
    },
    clearInputs() {
      this.text = "";
      this.color= "";
    },
    logOut() {
      this.currentUser = "";
      this.userData.notes = {};
      this.projects = [];
      this.newProject = "";
      this.clearInputs();
    },
    openAddNote(name) {
      if(this.nameNewProject !== name) {
        this.nameNewProject = name;
      } else {
        this.nameNewProject = "";
      }
    },
    openAddProject() {
      this.showAddProject = !this.showAddProject;
    },
    uniq(arr) { 
      return [...new Set(arr)] 
    },
    handleUpdate() {
      this.saveUserData(this.currentUser, this.userData);
    },
  }
}
</script>
