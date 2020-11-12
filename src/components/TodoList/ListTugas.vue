<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">
            To Do List
        </h3>

    <div class="d-flex align-start">
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                    >
                </v-text-field>

                <v-spacer></v-spacer>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>
            
            <v-data-table
             :headers="headers" 
             :items="todos" 
             :search="search">


                <template v-slot:[`item.priority`]="{item}">
                    <td>
                        <v-chip v-if="item.priority == 'Penting'" color="red" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else-if="item.priority == 'Biasa'" color="primary" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else color="success" outlined>
                            {{ item.priority }}
                        </v-chip>
                    </td>
                </template>
                <template v-slot:[`item.actions`]="{item}">
                    <!-- <v-icon
                        small
                        class="mr-2"
                        @click="editItem(item)"
                        color="blue"
                    >
                        mdi-pencil
                    </v-icon>
                    <v-icon
                        small
                        @click="deleteitem(item)"
                        color="red"
                    >
                        mdi-delete
                    </v-icon> -->
                    <v-icon
                        small
                        @click="actions(item)"
                        color="blue"
                    >
                        mdi-file
                    </v-icon>
                </template> 
                <template v-slot:[`item.checks`]="{item}">
                    <v-checkbox multiple :key="item" @click.capture.stop="checkItem(item)"/>              
                </template>        
            </v-data-table>
        </v-card>

        <v-card v-model="tempTombol" v-if="tempTombol==true" class="px-5 py-5 ml-3">
                <b>{{formEditData.task}}</b>
                 <v-chip v-if="formEditData.priority == 'Penting'" color="red" text-color="white">
                    {{formEditData.priority}}
                </v-chip>
                 <v-chip v-else-if="formEditData.priority == 'Biasa'" color="primary" text-color="white">
                     {{formEditData.priority}}
                 </v-chip>
                 <v-chip v-else color="success" text-color="white">
                    {{formEditData.priority}}
                </v-chip>
                <br>
                {{formEditData.note}}<br>
                <v-icon
                    small
                    class="mr-2"
                    @click="editItem(item)"
                    color="blue"
                >
                    mdi-pencil
                </v-icon>
                <v-icon
                    small
                    @click="deleteitem()"
                    color="red"
                >
                    mdi-delete
                </v-icon>
        </v-card>
    </div>
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">
                        Form Todo
                    </span>
                </v-card-title>

                <v-card-text>
                    <v-container>
                        <v-text-field
                        v-model="formTodo.task"
                        label="Task"
                        required
                        >
                        </v-text-field>

                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting','Biasa','Tidak penting']"
                            label="Priority"
                            required
                        >
                        </v-select>

                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        >
                        </v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer>

                    </v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-card v-if="check.length" style="overflow-y:scroll; height:200px;" class="pa-5 my-2">
            Delete Multiple
                <br>
                <v-list-item v-for="(item, i) in check" :key="i">
                    <v-list-item-content>
                        <v-list-item-title>•  {{item.task}}</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
                <v-btn color="error" dark @click="deleteAll">
                    Hapus Semua
            </v-btn>
        </v-card>

        <v-dialog v-model="dialogEdit" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">
                        Form Todo Edit
                    </span>
                </v-card-title>

                <v-card-text>
                    <v-container>
                        <v-text-field
                        v-model="formEdit.task"
                        label="Task"
                        required
                        >
                        </v-text-field>

                        <v-select
                            v-model="formEdit.priority"
                            :items="['Penting','Biasa','Tidak penting']"
                            label="Priority"
                            required
                        >
                        </v-select>

                        <v-textarea
                            v-model="formEdit.note"
                            label="Note"
                            required
                        >
                        </v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer>

                    </v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="saveEdit">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>


        <v-dialog v-model="dialogWarning" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">
                        Apakah anda yakin ingin menghapus?
                    </span>
                </v-card-title>

                <v-card-actions>
                    <v-spacer>

                    </v-spacer>
                    <v-btn color="green darken-1" text @click="dialogWarning = false">
                        Tidak
                    </v-btn>
                    <v-btn color="red darken-1" text @click="ya">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>

<script>
export default {
    name:"List",
    data()
    {
        return{
            tempEdit: null,
            tempTombol: false,
            search: null,
            dialog: false,
            tempDelete: null,
            dialogWarning: false,
            dialogEdit: false,
            headers:[
                {
                    text : "Task",
                    align: "start",
                    sortable : true,
                    value: "task",
                },
                {
                    text : "Priority",
                    value : "priority"
                },
                // {
                //     text : "Note",
                //     value : "note"
                // },
                {
                    text : "Actions",
                    value : "actions"
                },
                {
                    text :" ",
                    value : "checks"
                }
            ],
            todos: [
                {
                    task: "bernafas",
                    priority : "Penting",
                    note: "huffttt",
                },
                {
                    task : "nongkrong",
                    priority : "Tidak penting",
                    note : "bersama teman2",
                },
                {
                    task : "masak",
                    priority: "Biasa",
                    note : "masak air 500 ml",
                },
            ],
            formTodo:{
                task : null,
                priority : null,
                note : null,
            },
            formEdit:{
                task : null,
                priority : null,
                note : null,
            },
            formEditData:{
                task : null,
                priority : null,
                note : null,
            },
            check:[],
        };
    },
    methods:{
        save(){
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancel(){
            this.resetForm();
            this.dialog = false;
            this.dialogEdit = false;
        },
        resetForm(){
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        deleteitem(){
            // this.tempDelete = this.todos.indexOf(item);
            this.dialogWarning = true;
        },
        ya(){
            this.todos.splice(this.tempEdit,1);
            this.dialogWarning = false;
            this.tempTombol = false;
        },
        editItem(item){
            // this.formEditData = Object.assign({},item);
            // this.tempDelete = this.todos.indexOf(item);
            this.dialogEdit = true;
        },
        saveEdit(){
            Object.assign(this.todos[this.tempEdit],this.formEdit);
            this.resetForm();
            this.dialogEdit = false;
            this.tempTombol = false;
        },
        actions(item){
            if(this.tempTombol == true)
            {
                this.tempTombol = false;
            }
            this.tempEdit = this.todos.indexOf(item);
            this.tempTombol=true;
            Object.assign(this.formEdit,item);
            this.formEditData = Object.assign({},item);
        },
        checkItem(item){
            if(this.check.includes(item)) {
                this.check.splice(this.check.indexOf(item), 1);
            } else {
                this.check.push(item);                
            }
        },
        deleteAll(){             
            for(var i = 0; i < this.check.length; i++){    
                if(this.check[i].task == this.formEditData.task && this.check[i].priority == this.formEditData.priority && this.check[i].note == this.formEditData.note){
                    this.formEditData = false;
                    this.tempTombol=false;
                }        
                this.todos.splice( this.todos.indexOf(this.check[i]), 1);
            }
            this.check=[];                            
        },
    },
};
</script>