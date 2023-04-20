<template>
    <Page backgroundImage="~/assets/amw_prime.jpg">
        <ActionBar>
            <NavigationButton text="Go back" android.systemIcon="ic_menu_back" @tap="backHome"/>
            <Label text="Изменение задачи"/>
        </ActionBar>
        <FlexboxLayout flexDirection="column" class="task-change">
            <label text="Название задачи: " class="title-label"/>
            <TextView v-model="title" editable="true" class="title"/>
            <label text="Описание задачи: " class="description-label" />
            <TextView v-model="description" editable="true" class="description"/>
            <button text="Удалить" class="button-save" @tap="del"/>
            <button text="Изменить" class="button-save" @tap="change"/>
        </FlexboxLayout>
    </Page>
</template>

<script> 
import * as ApplicationSettings from '@nativescript/core/application-settings';
import Home from "./Home.vue"
export default {
    data(){
        return{
            listTasks: [],
            title: "",
            description: "",
        }
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
        del(){ //Удаление, по сути делаем перезапись
            let newListTasks = [];
            this.listTasks.forEach(item =>{
                if(item.id != this.id){
                    newListTasks.push({
                        id: item.id,
                        title: item.title,
                        description: item.description,
                        date: item.date,
                        done: item.done,
                    });
                }
            });
            this.listTasks = newListTasks;
            this.save();
        },
        change(){
            this.listTasks.forEach(item =>{
                if(item.id != this.id){
                    if(this.title != "" || this.title.length !=0){
                        item.title = this.title;
                    }
                    if(this.description != "" || this.description.length !=0){
                        item.description = this.description;
                    }
                }
            });
            this.save();
        },
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
