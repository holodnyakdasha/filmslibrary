<template lang="pug">
  .content-wrapper
    section
      .container
        .task-list__header
          h1.ui-title-1 Tasks
          .buttons-list
            .button.button--round.button-default(
              @click="filter = 'active'"
            ) Active
            .button.button--round.button-default(
              @click="filter = 'completed'"
              ) Completed
            .button.button--round.button-default(
            @click="filter = 'all'"
            ) All
        .task-list
          .task-item(
            v-for="task in tasksFilter"
            :key="task.id"
            :class="{ completed: task.completed}"
            )
            .ui-card.ui-card--shadow
              .task-item__info
                .task-item__main-info
                  span.ui-label.ui-label--light {{ task.whatWatch }}
                  span Total Time: {{ task.time}}
                span.button-close(
                  @click="deleteTask(task.id)"
                )

              .task-item__content
                .task-item__header
                  .ui-checkbox-wrapper
                    input.ui-checkbox(
                      type="checkbox"
                      v-model="task.completed")
                  span.ui-title-3 {{ task.title }}
                .task-item__body
                  p.ui-text-regular {{ task.description }}

                .task-item__footer
                  .tag-list
                    .ui-tag__wrapper(
                      v-for="tag in task.tags"
                      :key="tag.title"
                      )
                      .ui-tag
                        span.tag-title {{tag.title}}
                  .buttons-list
                    .button.button--round.button-default(
                    @click="taskEdit(task.id, task.title, task.description)"
                    ) Edit
                    .button.button--round.button-primary Done

                  // Edit popup
                  .ui-messageBox__wrapper(
                    v-if="editing"
                    :class="{active: editing}"
                  )
                    .ui-messageBox.fadeInDown
                      .ui-messageBox__header
                        span.messageBox-title {{ titleEditing }}
                        span.button-close(@click="cancelTaskEdit")
                      .ui-messageBox__content
                        p Title
                        input(
                          type="text"
                          v-model='titleEditing'
                        )
                        p Description
                        textarea(
                          type="text"
                          v-model='desrEditing'
                        )
                      .ui-messageBox__footer
                        .button.button-light(@click="cancelTaskEdit") Cancel
                        .button.button-primary(@click="finishTaskEdit") OK



</template>

<script>
  export default{
      data(){
          return{
            filter: 'active',
            editing: false,
            titleEditing: '',
            desrEditing: '',
            taskId: null

          }
      },
    methods:{
      taskEdit (id, title, description) {
        this.editing = !this.editing
        this.taskId = id
        this.titleEditing = title
        this.desrEditing = description
      },
      // Cancel button (POPUP)
      cancelTaskEdit () {
        this.editing = !this.editing
        // Reset
        this.taskId = null
        this.titleEditing = ''
        this.desrEditing = ''
      },
      // Done button
      finishTaskEdit () {
        // console.log(this.titleEditing)
        this.$store.dispatch('editTask', {
          id: this.taskId,
          title: this.titleEditing,
          description: this.desrEditing
        })
        this.editing = !this.editing
      },
      // Delete button
      deleteTask (id) {
        this.$store.dispatch('deleteTask', id)
          .then(() => {
            console.log('task deleted')
            this.$store.dispatch('loadTasks')
          })
      }
      },

   computed:{
     tasksFilter(){

       if (this.filter === 'active') {
         return this.$store.getters.tasksNotCompleted

       } else if (this.filter === 'completed') {
         return this.$store.getters.tasksCompleted

       } else if (this.filter === 'all') {
         return this.$store.getters.tasks
       }
       return this.filter === 'active'
     },
     loading(){
       return this.$store.getters.loading

     }
   }
  }

</script>

<style lang="stylus" scoped>
  .task-list__header
    display flex
    justify-content space-between
    align-items center
    margin-bottom 30px
    .button
      margin-right 8px
    .ui-title-1
      margin-bottom 0
  .task-item
    margin-bottom 20px
  .ui-checkbox:checked:before
    border-color #909399
    border-color #909399
  &.completed
    .ui-title-2,
    .ui-text-regular,
    .ui-tag
      text-decoration line-through
      color #909399
    &:last-child
      margin-bottom 0
  .ui-label
    margin-right 8px
  .task-item__info
    display flex
    align-items center
    justify-content space-between
    margin-bottom 20px
    .button-close
      width 20px
      height @width
  .task-item__header
    display flex
    align-items center
    margin-bottom 18px
    .ui-checkbox-wrapper
      margin-right 8px
    .ui-title-3
      margin-bottom 0
  .ui-tag__wrapper
    margin-right 10px
    margin-bottom 10px
    &:last-child
      margin-right 0

  .tag-list
    margin-bottom 20px
  .task-item__foter
    .buttons-list
      text-align right
  // ALL buttons
  .buttons-list
    .button
      margin-right 12px
      &:last-child
        margin-right 0
  // POPUP
  .ui-messageBox__wrapper
    &.active
      display flex
    .button-light
      margin-right 8px
</style>
