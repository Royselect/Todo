<template>
    <Page backgroundImage="~/assets/amw_prime.jpg">
        <ActionBar>
            <Label text="Трекер задач"/>
        </ActionBar>
        <GridLayout columns="*" rows="*, 6*, *">
            <SegmentedBar col="0" row="0" v-model="selectedFilter" class="filter-list" selectedBackgroundColor="white">
                <SegmentedBarItem title="Все"/>
                <SegmentedBarItem title="В процессе"/>
                <SegmentedBarItem title="Выполнено"/>
            </SegmentedBar>
            <FlexboxLayout flexDirection="column" col="0" row="1" class="list-tasks">
                <template v-if="filteredListTasks.length == 0 && (selectedFilter == 0 || isNaN(selectedFilter))">
                    <FlexboxLayout flexDirection="column" class="item-hollow">
                        <label class="title-task" text="Список задач пуст!"/>
                        <label class="description-task" text="Используйте кнопку ниже чтобы добавить" />
                    </FlexboxLayout>
                </template>
                <template v-else>
                    <template v-for="(task, key) in filteredListTasks">
                    <template v-if="task.done">
                        <FlexboxLayout flexDirection="row"  :key="key" class="done-item" @tap="insideTask(task.id)">
                            <FlexboxLayout flexDirection="column" @tap="insideTask(task.id)">
                                <label :text="task.title" class="title-task" />
                                <label :text="task.description" class="description-task" />
                            </FlexboxLayout>
                            <FlexboxLayout flexDirection="column" class="date-front">
                                <label text="Выполнено!" class="title-access"/>
                                <label :text="task.date" class="date-task"/>
                            </FlexboxLayout>
                        </FlexboxLayout>    
                    </template>
                    <template v-else>
                        <FlexboxLayout flexDirection="row" :key="key" class="not-done-item">
                            <FlexboxLayout flexDirection="column" @tap="insideTask(task.id)">
                                <label :text="task.title" class="title-task" />
                                <label :text="task.description" class="description-task" />
                            </FlexboxLayout>
                            <FlexboxLayout flexDirection="column" class="date-front">
                                <button class="button-access" text="✔" @tap="done(task.id)"/>
                                <label :text="task.date" class="date-task"/>
                            </FlexboxLayout>
                        </FlexboxLayout>
                    </template>
                </template>
                </template>
            </FlexboxLayout>
            <button @tap="add" col="0" row="2" class="button-task" text="+"/>
        </GridLayout>
       
    </Page>
</template>

<script>
import Add from "./Add.vue";
import Change from "./Change.vue"
import * as ApplicationSettings from '@nativescript/core/application-settings';
  export default {
    data() {
        return {
            listTasks: [],
            selectedFilter: "",
            filteredListTasks: [],
        };
    },
    watch: {
        selectedFilter: function () {
            this.filter();
        },
    },
    mounted() {
        if (ApplicationSettings.getString("tasks")) { 
            this.listTasks = Object.values(JSON.parse(ApplicationSettings.getString("tasks")));
        }
        this.listTasks = this.listTasks.reverse();
        this.filter();
    },
    methods: {
        add() {
            this.$navigateTo(Add);
        },
        save() {
            let listSave = Object.assign({}, this.listTasks);
            ApplicationSettings.setString("tasks", JSON.stringify(listSave));
            //this.backHomeWithSave();
        },
        done(id) {
            this.listTasks.forEach(item => {
                if (item.id == id) {
                    item.done = true;
                }
            });
            this.save();
        },
        filter() {
            if (isNaN(this.selectedFilter)) {
                this.filteredListTasks = [];
                this.filteredListTasks = this.listTasks;
            }
            switch (this.selectedFilter) {
                case 0: //Выбраны все
                    this.filteredListTasks = [];
                    this.filteredListTasks = this.listTasks;
                    break;
                case 1: //Выбраны те, что в процессе
                    this.filteredListTasks = [];
                    this.listTasks.forEach(item => {
                        if (!item.done) {
                            this.filteredListTasks.push({
                                id: item.id,
                                title: item.title,
                                description: item.description,
                                date: item.date,
                                done: item.done,
                            });
                        }
                    });
                    break;
                case 2: // Выполненные
                    this.filteredListTasks = [];
                    this.listTasks.forEach(item => {
                        if (item.done) {
                            this.filteredListTasks.push({
                                id: item.id,
                                title: item.title,
                                description: item.description,
                                date: item.date,
                                done: item.done,
                            });
                        }
                    });
                    break;
            }
        },
        insideTask(id) {
            this.$navigateTo(Change, { props: { id: id } });
        }
    },
};
</script>

<style scoped lang="scss">
    @import '@nativescript/theme/scss/variables/blue';

    // Custom styles
    .fas {
        @include colorize($color: accent);
    }

    .info {
        font-size: 20;
        horizontal-align: center;
        vertical-align: center;
    }
</style>
