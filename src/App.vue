<script setup>
import {ref, reactive, provide, onUpdated, watch, toRefs} from 'vue';
import UserForm from './components/UserForm.vue';
import FilterList from './components/FilterList.vue';
import UserList from './components/UserList.vue';
import Pagination from './components/Pagination.vue';
import fireBaseConnect from './composables/firebase.connect';

const { database, getDatabase, fbRef, set, onValue, child, push, update} = fireBaseConnect();

const selectedFilterList = ref(0);
const currPage = ref(1);
const totalPages = ref(0);
const lastUserId = ref(0);
const userData = reactive([]);
const perPage = 4;

const sortUserList = (sortOption)=>{
  const sortObj = {
        '0': (a,b) => { return a.userId - b.userId },
        '1': (a,b) => { return new Date(a.userBirthday).getTime() - new Date(b.userBirthday).getTime() },
        '2': (a,b) => { return new Date(b.userBirthday).getTime() - new Date(a.userBirthday).getTime() },
        '3': (a,b) => { 
                    if (a.userLastName > b.userLastName){
                    return -1;
                    } else if (a.userLastName == b.userLastName) {
                    return 0;
                    }
                    return 1;
            },
        '4': (a,b) => { 
                    if (a.userLastName > b.userLastName){
                    return 1;
                    } else if (a.userLastName == b.userLastName) {
                    return 0;
                    }
                    return -1;
            }
    };
    
    let i = 0;
    const sortedUserData = userData.value.sort(sortObj[sortOption]);
    for ( const [key, value] of Object.entries(sortedUserData) ){
      value.id = i;
      i++;
    }

    userData.value = sortedUserData;
    responseMessage.value = 'List Sorted!';
    showResponse.value = true;
    
}

const showPage = (pageNum)=>{
  currPage.value = pageNum;
}

const userDatabase = fbRef(database, 'users/');

onValue(userDatabase, (snapshot) => {
    const dbData = snapshot.val();
    const userListArr = [];
    let i = 0;
    for ( const [key, value] of Object.entries(dbData) ){
      value.id = i;
      value.userId = key;
      userListArr.push(value);
      i++;
    }

    userData.value = userListArr;
    lastUserId.value = Number(userListArr[userListArr.length - 1].userId);
    totalPages.value = Math.ceil(userListArr.length / perPage);
    console.log('last id',lastUserId.value);

});

const updateUser = (options)=>{
  const db = getDatabase();

  const {
    userId,
    userAvatar,
    userBirthday,
    userEmail,
    userLastName,
    userName,
    userTelephone
    } = options;
  
    update(fbRef(db, 'users/' + userId), {
        userName,
        userLastName,
        userEmail,
        userTelephone,
        userBirthday,
        userAvatar,
    }).then(()=>{
        showNewUserModal.value = false;
        showResponse.value = true;
        responseMessage.value = 'User successfully updated!';
    }).catch((error) => {
        responseMessage.value = 'Error: ' + error;
    });
}

const tellPattern = (e) => {
  let field = e.currentTarget,
      currentPosition = doGetCaretPosition(field),
      patternRules = {
          7: ')',
          11: '-',
          14: '-',
      };
  if (patternRules[`${currentPosition}`]){
      field.value += patternRules[`${currentPosition}`];
  }
}

const doGetCaretPosition = (oField) => {

    // Initialize
    let iCaretPos = 0;
  
    // IE Support
    if (document.selection) {
  
      // Set focus on the element
      oField.focus();
  
      // To get cursor position, get empty selection range
      var oSel = document.selection.createRange();
  
      // Move selection start to 0 position
      oSel.moveStart('character', -oField.value.length);
  
      // The caret position is selection length
      iCaretPos = oSel.text.length;
    } else if (oField.selectionStart || oField.selectionStart == '0')
      iCaretPos = oField.selectionDirection=='backward' ? oField.selectionStart : oField.selectionEnd;
  
    // Return results
    return iCaretPos;
  }

const responseMessage = ref('');
const showResponse = ref(false);
const showNewUserModal = ref(false);
const newUser = reactive({
  userAvatar: '/src/images/noavatar.jpg',
  userName: '',
  userLastName: '',
  userEmail: '',
  userTelephone: '+38(',
  userBirthday: '',
});


const submitNewUser = () => {
  const db = getDatabase();
  const userId = lastUserId.value + 1;
  const {
    userName,
    userLastName,
    userEmail,
    userTelephone,
    userBirthday,
    userAvatar,
  } = toRefs(newUser);
 
  set(fbRef(db, 'users/' + userId), {
        userName: userName.value,
        userLastName: userLastName.value,
        userEmail: userEmail.value,
        userTelephone: userTelephone.value,
        userBirthday: userBirthday.value,
        userAvatar: userAvatar.value,
    }).then(()=>{
        showNewUserModal.value = false;
        showResponse.value = true;
        responseMessage.value = 'New user successfully added!';
    }).catch((error) => {
        responseMessage.value = 'Error: ' + error;
    });
}
</script>

<template>
  <div>
    <div class="container">
            <div class="row">
                <div class="col-12">
                    <h1>JS Junior Task VUE</h1>
                    <hr/>
                    <h2>User list</h2>
                    <UserForm>
                      <template v-slot:filterList>
                        <FilterList :selectedFilterList="selectedFilterList" @changeVal="sortUserList"  />
                      </template>
                      <template v-slot:userList>
                        <UserList :userData="userData" @updateUser="updateUser" :currPage="currPage" :perPage="perPage" @inputUserTel="inputUserTel" />
                      </template>
                        <template v-slot:pagination>
                          <Pagination :currPage="currPage" :totalPages="totalPages" @showPage="showPage" />
                      </template>
                    </UserForm>
                    <div class="add_user">
                      <button type="button" class="btn btn-primary mt-3" @click="showNewUserModal = true">Add new user</button>
                  </div>
                </div>
            </div>
        </div>
        <div class="modal_block" v-show="showResponse">
          <div class="modal_window">
            <button class="modal_close" @click="showResponse = false">X</button>
            <p>{{responseMessage}}</p>
          </div>
        </div>
        <div class="modal_block" v-show="showNewUserModal">
          <div class="modal_window">
            <button class="modal_close" @click="showNewUserModal = false">X</button>
            <h2>Add new user</h2>
            <p>{{newUser}}</p>
            <div class="new_user_row d-flex flex-column flex-wrap align-items-start justify-content-start">
                <div class="mb-3">
                    <img src="/src/images/noavatar.jpg" class="img-thumbnail" alt="avatar">
                    <input type="file" class="choose_avatar_js" name="userAvatar">
                </div>
                <div class="mb-3">
                    <label for="userName" class="form-label">Name</label>
                    <input type="text" class="form-control" name="userName" placeholder="Name" v-model="newUser.userName">
                </div>
                <div class="mb-3">
                    <label for="userLastName" class="form-label">Last Name</label>
                    <input type="text" class="form-control" name="userLastName" placeholder="Last name" v-model="newUser.userLastName">
                </div>
                <div class="mb-3">
                    <label for="userEmail" class="form-label">Email address</label>
                    <input type="email" class="form-control" name="userEmail" placeholder="name@example.com" v-model="newUser.userEmail">
                </div>
                <div class="mb-3">
                    <label for="userTelephone" class="form-label">Telephone</label>
                    <input type="text" class="form-control" maxlength="17" name="userTelephone" placeholder="+380 (XX) XXX-XX-XX" @input="tellPattern"  v-model="newUser.userTelephone">
                </div>
                <div class="mb-3">
                    <label for="userBirthday" class="form-label">Birthday Date</label>
                    <input type="text" class="form-control" name="userBirthday" placeholder="YYYY-MM-DD" v-model="newUser.userBirthday">
                </div>
                <div class="mb-3">
                    <button type="button" @click="submitNewUser" class="btn btn-primary">Add</button>
                </div>
            </div>
          </div>
        </div>
  </div>
</template>

<style scoped>
.modal_block{
  position:absolute;
  display: flex;
  flex-direction:row;
  align-items: center;
  justify-content: center;
  background-color: rgba(0,0,0,.15);
  width:100%;
  height:100%;
  top:0;
  left:0;
}

.modal_window{
  position: relative;
  display: block;
  width:50%;
  padding: 15px;
  background-color:#fff;
  box-shadow: 2px 2px 4px rgba(0,0,0,.15);
}

.modal_window .modal_close{
  position:absolute;
  display: block;
  width:30px;
  height: 30px;
  padding: 4px;
  color:#fff;
  background-color:blue;
  border-radius: 2px;
  border:none;
  top:-15px;
  right:-15px;
}
</style>
