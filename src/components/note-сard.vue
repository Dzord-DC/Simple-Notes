<template>
        <span class="projects-title">{{ text }}</span>
        <div class="note-container">
            <div v-for="note in project" :key="note.index" class="note-main">
                <div class="note" :style="{'background-color': note.color}">
                    <input
                        class="delete-btn" 
                        type="button" 
                        value="×" 
                        @click="deleteHendler(note.index)"
                    />
                    
                    <div 
                        v-if="note.title && note.index !== isEditingIndex"
                        class="note-text"
                        @dblclick="startEditing(note)"
                    >
                        {{ note.title }}
                    </div>
                    <textarea
                        v-else-if="isEditingIndex == note.index"
                        type="text"
                        v-model="note.title"
                        @blur="stopEditing(note)"
                        @keyup.enter.prevent="stopEditing(note)"
                        @keyup.escape="cancelEditing(note)"
                        v-focus
                        class="cnt"
                        maxlength='250'
                        placeholder="Enter note description here"
                    />
                    <span 
                        v-else
                        @click="startEditing(note)"
                        >
                        {{ note.title }}
                        <span class="edit-icon">✏️</span>
                    </span>
                    
                </div>
            </div>
        </div>
</template>
<script>
export default {
  name: "note-сard",
  props: {
    project: Array,
    text: String,
  },
  emits: ['deleteNote', 'updateNote'],
  data() {
        return {
            isEditingIndex: "",
            originalTitle: "",
        };
    },
    methods: {
        deleteHendler(id) {
            this.$emit("deleteNote", id);
        },
        startEditing(note) {
            this.originalTitle = note.title;
            this.isEditingIndex = note.index;
        },
        stopEditing(note) {
            this.isEditingIndex = "";
            note.title= note.title.trim();
            this.$emit("updateNote", {
                id: note.index,
                newTitle: note.title
            });
        },
        cancelEditing(note) {
            note.title = this.originalTitle;
            this.isEditingIndex = "";
        }
    }
}
</script>
<style lang="scss" scoped>
.note-container {
    display: flex;
    justify-content: space-evenly;
    flex-wrap: wrap;
    margin: 16px;
}
.note-main {
    position: relative;
}
.note {
    font-family: "Zhuan", sans-serif;
    font-weight: normal;
    font-size: 18px;
    display: flex;
    width: 180px;
    min-height: 200px;
    justify-content: center;
    align-items: center;
    text-align: center;
    margin: 8px;
    box-shadow: 4px 4px 0.4em lightslategray;
    overflow: auto;
    height: 200px;
}
.note:hover { 
    .delete-btn {
        display: flex;
    }
}
.note-text {
    word-break: break-word;
}
.delete-btn {
    display: none;
    color: red;
    right: 24px;
    position: absolute;
    top: 12px;
    border-radius: 15px;
    width: 25px;
    height: 25px;
    border: none;
    font-size: 20px;
    cursor: pointer;
    transition: all 0.4s;
}
.delete-btn:hover {
    background-color: rgb(194, 191, 189);
}
.edit-icon {
    cursor: pointer;
}
textarea {
    background-color: transparent;
    border: none;
    resize: vertical;
    font-family: "Gloria Hallelujah", cursive;
    width: 100%;
    padding: 5px;
    &:focus {
      outline: none;
      border: none;
      box-shadow: 0 0 5px 1px rgba(0,0,0,.2) inset;
    }
    &.cnt {
      min-height: 190px;
      font-size: 16px;
    }
  }

</style>