<template>
  <div class="home">
    
          <v-text-field
            outlined
            class="pa-2"
            v-model="newTaskTitle"
            label="Add Task"
            append-icon="mdi-plus-thick"
            hide-details
            @keyup.enter="addNewTask()"
            @click:append="addNewTask()"
            clearable
          ></v-text-field>
          
    <v-list two-line flat>
      <v-subheader>Today Todos</v-subheader>
    </v-list>

    <v-divider></v-divider>

    <v-list flat subheader two-line>
      <div class="tasks" v-for="task in tasks" :key="task.id">
        <v-list-item 
        :class="{ 'blue lighten-5' : task.done}">
          <template v-slot:default="{ active, }">
            <v-list-item-action>
              <v-checkbox @click="doneTask(task.id)" :input-value="task.done"></v-checkbox>
            </v-list-item-action>

            <v-list-item-content>
              
              <v-row>
                <v-col
                cols="12"
                  sm="6"
                  md="9">
                <v-list-item-title
                  :class="{ 'text-decoration-line-through' : task.done }"
                >
                {{ task.title }}
                </v-list-item-title>
                <v-list-item-subtitle>{{
                  task.description
                }}</v-list-item-subtitle>
                </v-col>
                <v-col
                  cols="12"
                  sm="6"
                  md="3"
                >
                  <v-menu
                    ref="menu"
                  
                    :close-on-content-click="false"
                    :return-value.sync="date"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                        v-model="date"
                        label="Picker in menu"
                        prepend-icon="mdi-calendar"
                        readonly
                        v-show="task.datePickerShow"
                        v-bind="attrs"
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker
                      v-model="date"
                      no-title
                      scrollable
                    >
                      <v-spacer></v-spacer>
                      <v-btn
                        text
                        color="primary"
                        @click="menu = false"
                      >
                        Cancel
                      </v-btn>
                      <v-btn
                        text
                        color="primary"
                        @click="dateChanged(task.id)"
                      >
                        OK
                      </v-btn>
                    </v-date-picker>
                  </v-menu>
                </v-col>
              </v-row>
              
            </v-list-item-content>
            <v-list-item-action>
              <v-menu offset-y>
                <template v-slot:activator="{ on, attrs }">
                  <v-icon
                  color="primary"
                    dark
                    v-bind="attrs"
                    v-on="on"
                  >
                  mdi-dots-vertical
                  </v-icon>
                </template>
                <v-list>
                  <v-list-item>
                    <v-list-item-action>
                      <v-list-item-title
                        style="cursor:pointer;"
                        @click.stop="removeTask(task.id)"
                      >Remove</v-list-item-title>
                      <v-list-item-title
                        style="cursor:pointer;"
                        @click.stop="showDatePicker(task.id)"
                      >Add Date</v-list-item-title>
                      <v-list-item-title
                        style="cursor:pointer;"
                        @click.stop="showDatePicker(task.id)"
                      >Edit</v-list-item-title>
                    </v-list-item-action>
                  </v-list-item>
                </v-list>
              </v-menu>
            </v-list-item-action>
          </template>
        </v-list-item>
        <v-divider></v-divider>
      </div>
    </v-list>
  </div>
</template>

<script>
export default {
  name: "Todo",

  data() {
    return {
      doneTasks : [],
      newTaskTitle:'',
      menu: false,
      modal: false,
      menu2: false,
      date: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      items: [
        { title: 'Remove', event: 'removeTask', param: 'id' },
        { title: 'Add Date' },
      ],
      tasks: [
        {
          id: 1,
          title: "Wake Up",
          description: "On 7 am",
          datePickerShow: false,
          date: Date.now(),
          done: false,
        },
        {
          id: 2,
          title: "Go Shopping",
          datePickerShow: false,
          description: "On 12 am",
          date: Date.now(),
          done: false,
        },
        {
          id: 3,
          title: "Visit Mom",
          datePickerShow: false,
          description: "On 13 am",
          date: Date.now(),
          done: false,
        },
      ],
    };
  },
methods:{
   addNewTask(){
     if(this.newTaskTitle != ''){
       let task = {
        id: Date.now(),
        title: this.newTaskTitle,
        description: 'none',
        done:false
      };

      this.tasks.push(task)

      this.newTaskTitle = ''
     }

  },
  doneTask(id){
    let task = this.tasks.filter(task => task.id === id)[0]
    task.done = !task.done
  },
  removeTask(id){
    this.tasks = this.tasks.filter(task => task.id !== id)
  },
  dateChanged(id){
    let task = this.tasks.filter(task => task.id === id)
    task.date = this.date
  },
  showDatePicker(id){
    let otherTasks = this.tasks.filter(task => task.id !== id)
    otherTasks.forEach(element => {
      element.datePickerShow = false
    });

    let task = this.tasks.filter(task => task.id === id)[0]
    task.datePickerShow = !task.datePickerShow
  },
}
};
</script>
