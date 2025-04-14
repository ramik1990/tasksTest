<template>
    <section class="tasks"> 
        <h2>Tasks from JSON</h2>
        <ul>
            <li v-for="(item, index) in tasks" :key="index">
                <input class="form-check-input" type="checkbox" :id="item.id" v-model="item.done" :aria-label="item.title"> {{ item.title }}
            </li>
        </ul>
    </section>
</template>

<script>
import axios from 'axios';

export default {
    name: "Tasks",
    data() {
        return {
            tasks: []
        }
    },
    methods: {
        //Получаю данные из файла
        async getJson() {       
            try {
                const response = await axios.get('/tasks.json');
                localStorage.setItem('tasks', JSON.stringify(response.data));
            } catch (error) {
                console.error("Ошибка загрузки JSON:", error);
            }
        },
        //Получить задачи с локального хранилища
        loadTasks() {
            const storageTasks = localStorage.getItem('tasks');
            if(storageTasks) {
                this.tasks = JSON.parse(storageTasks);
            } else {
                this.getJson();
            }
        },
        //сохранить изменения в хранилище
        saveTasks() {
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        }
    },
    mounted() {
        //При запуске получеам данные
        this.loadTasks();
    },
    watch: {
        //отслеживаю изменение чекбокса. при изменении состояния выполняю метод сохранения данных
        tasks: {
            handler: "saveTasks",
            deep: true
        }
    },
}
</script>

<style scoped>
.tasks {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}
.tasks ul li{
    list-style-type: none;
}
.tasks ul li input {
    cursor: pointer;
}
</style>