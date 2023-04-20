<template>
    <Page backgroundImage="~/assets/amw_prime.jpg">
        <ActionBar>
            <NavigationButton text="Go back" android.systemIcon="ic_menu_back" @tap="backHome"/>
            <Label text="Добавление задачи"/>
        </ActionBar>
        <FlexboxLayout flexDirection="column" class="task-change">
            <label text="Название задачи: " class="title-label"/>
            <TextView v-model="title" editable="true" class="title"/>
            <label text="Описание задачи: " class="description-label" />
            <TextView v-model="description" editable="true" class="description"/>
            <button text="Добавить" class="button-save" @tap="add"/>
        </FlexboxLayout>
    </Page>
</template>

<script>
import * as ApplicationSettings from '@nativescript/core/application-settings';
import Home from "./Home.vue"
export default {
    data() {
        return {
            listTasks: [],
            title: "",
            description: "",
        };
    },
    methods: {
        backHomeWithSave() {
            this.$navigateTo(Home);
        },
        backHome(){
            this.$navigateBack();
        },
        save(){ //Сохранение задачи и переход на домашнюю стр.
            let listSave = Object.assign({}, this.listTasks);
            ApplicationSettings.setString("tasks", JSON.stringify(listSave));
            this.backHomeWithSave();
        },
        add(){
            let currentDate = new Date();
            let month = currentDate.getMonth();
            month = month.toString();
            let day = currentDate.getDate().toString()
            let year = currentDate.getFullYear();
            //Условия для нормальной красивой записи даты
            if(month.length == 1){
                month = "0"+month;
            }
            if(day.length == 1){
                day = "0"+day;
            }
            let date = day+"."+month+"."+year;
            //Собираем задачу уже учитывая и дату, и сохраняем
            this.listTasks.push({
                id: Math.random(),
                title: this.title,
                description: this.description,
                date: date,
                done: false,
            });
            this.save();

        },
    },
    mounted() {
        if (ApplicationSettings.getString("tasks")) { // получаем из памяти всё, что хранится в переменной tasks
            this.listTasks = Object.values(JSON.parse(ApplicationSettings.getString("tasks"))); // там хранится json. Эти методы грубо говоря парсят его в обычный список
        }
    },
}
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
