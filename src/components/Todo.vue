<template>
  <v-container>
    <v-card class="mx-auto" max-width="600">
      <v-form ref="form">
        <v-row align="center" justify="center">
          <v-col cols="9">
            <v-text-field v-model="newTodo" label="New Todo"></v-text-field>
            <!-- 새로운 Todo를 입력하는 텍스트 필드 -->
          </v-col>
          <v-col cols="2">
            <v-btn rounded type="submit" @click.prevent="addTodo()">ADD</v-btn>
            <!-- Todo 추가 버튼 -->
          </v-col>
        </v-row>
      </v-form>

      <v-divider></v-divider>

      <v-list flat>
        <v-list-item-group multiple active-class>
          <!-- 
            <v-list-item>
            <template>
              <v-list-item-action>
                <v-checkbox input-value="true"></v-checkbox>
              </v-list-item-action>

              <v-list-item-content>
                <v-list-item-title>Todo item</v-list-item-title>
              </v-list-item-content>
            </template>
          </v-list-item> 
          -->
          <!-- 6-1에서 작성했던 부분을 아래와 같이 수정 -->

          <v-list-item v-for="todo in todos" :key="todo.id">
            <!-- todos로 읽어온 데이터를 반복해서 출력 -->
            <template>
              <v-list-item-action>
                <v-checkbox
                  :input-value="todo.completed"
                  v-model="todo.completed"
                  @change="updateTodo(todo)"
                ></v-checkbox>
                <!-- completed에 저장된 완료 여부를 체크박스에 반영 -->
                <!-- 체크박스 체크가 변경되면 update 실행 -->
              </v-list-item-action>

              <v-list-item-content>
                <v-list-item-title>{{todo.text}}</v-list-item-title>
                <!-- todo.text 출력 -->
              </v-list-item-content>

              <!-- delete 버튼 -->
              <v-btn color="red" fab text small @click="deleteTodo(todo)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </template>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-card>
  </v-container>
</template>

<script>
import { todosCollection } from "../firebase";
// firebase.js 파일에서 설정한 todosCollection을 불러옴

export default {
  name: "Todo",

  data: () => ({
    newTodo: "", // 새로 작성하는 Todo 저장
    todos: [] // DB에서 가져온 Todos 저장
  }),

  firestore() {
    return {
      todos: todosCollection.orderBy("createdDate", "desc")
      // createdDate를 내림차순으로 정렬해서 todos로 가져옴
    };
  },

  methods: {
    addTodo() {
      todosCollection
        .add({
          completed: false,
          createdDate: new Date(),
          text: this.newTodo
          // completed, createdDate, text 값을 설정해서 추가
        })
        .then(function(docRef) {
          console.log("Add Document ID: ", docRef.id);
          // 성공시 콘솔에 문서 ID 출력
        })
        .catch(function(error) {
          console.error("Add Error: ", error);
          // 실패시 콘솔에 에러 출력
        });
      this.newTodo = "";
    },
    // 문서를 수정하는 메소드
    updateTodo(todo) {
      todosCollection
        .doc(todo.id)
        .update({ ...todo })
        .then(function() {
          console.log("Update Document ID: ", todo.id);
          // 성공시 콘솔에 문서 ID 출력
        })
        .catch(function(error) {
          console.error("Update Error: ", error);
          // 실패시 콘솔에 에러 출력
        });
    },

    // 문서를 삭제하는 메소드
    deleteTodo(todo) {
      todosCollection.doc(todo.id).delete();
    }
  }
};
</script>
