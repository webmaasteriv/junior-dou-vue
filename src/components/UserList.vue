<template>
    <div>
        <div v-for="user in userData.value" :key="user.id" :data-key="user.id" :class="currPage * perPage - 1 >= user.id && user.id > (currPage*perPage) - perPage - 1?'d-flex':'d-none'" class="user_row flex-row flex-wrap align-items-center justify-content-around">
            <div class="mb-3">
                <img :src="user.userAvatar" class="img-thumbnail" alt="avatar">
            </div>
            <div class="mb-3">
                <label for="userName" class="form-label">Name</label>
                <input type="text" class="form-control" name="userName" placeholder="Name" v-model.lazy="user.userName">
            </div>
            <div class="mb-3">
                <label for="userLastName" class="form-label">Last Name</label>
                <input type="text" class="form-control" name="userLastName" placeholder="Last name" v-model.lazy="user.userLastName">
            </div>
            <div class="mb-3">
                <label for="userEmail" class="form-label">Email address</label>
                <input type="email" class="form-control" name="userEmail" placeholder="name@example.com" v-model.lazy="user.userEmail">
            </div>
            <div class="mb-3">
                <label for="userTelephone" class="form-label">Telephone</label>
                <input type="text" class="form-control" name="userTelephone" maxlength="17" placeholder="+380(XX)XXX-XX-XX" v-model="user.userTelephone">
            </div>
            <div class="mb-3">
                <label for="userBirthday" class="form-label">Birthday Date</label>
                <input type="text" class="form-control" name="userBirthday" placeholder="YYYY-MM-DD" v-model.lazy="user.userBirthday">
            </div>
            <div class="mb-3">
                <button type="button" class="update_row_js btn btn-secondary mt-3" @click="$emit('updateUser', {...user})">Update</button>
            </div>
        </div>
    </div>
</template>

<script setup>
import {onUpdated, ref, computed} from 'vue';

const props = defineProps({
    userData:{
        type: Array,
        default:[],
    },
    perPage:{
        type: Number,
        default:4,
    },
    currPage:{
        type: Number,
        default:1,
    }
});

defineEmits(['updateUser', 'inputUserTel']);

const itter = ref(1);

const showClass = ()=>{
    console.log('showClass init');
    if ((currPage * perPage - 1) >= (itter.value = itter.value + 1) && itter.value > (currPage*perPage) - perPage - 1 ){
        return true;
    }
    return false;
}

onUpdated(()=>{
  itter.value = 0;
  console.log('UserList Updated', itter.value);
});
</script>

<style scoped>
.form-control {
    font-size:.75em;
}

form .img-thumbnail{
    width:75px;
    height:75px;
}

form .user_row{
    border-bottom: 1px solid rgba(0,0,0,.15);
    padding: 15px 0;
}

form .user_output{
    display: block;
    width: 100%;
}
</style>