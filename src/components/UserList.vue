<template>
  <div class="container">
    <table id="tblUsers">
      <thead>
        <tr>
          <th>&nbsp;</th>
          <th>First Name</th>
          <th>Last Name</th>
          <th>Username</th>
          <th>Email Address</th>
          <th>Status</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>
            <input type="checkbox" id="selectAll" v-on:click="selectAll" />
          </td>
          <td>
            <input
              type="text"
              id="firstNameFilter"
              v-model="filter.firstName"
            />
          </td>
          <td>
            <input type="text" id="lastNameFilter" v-model="filter.lastName" />
          </td>
          <td>
            <input type="text" id="usernameFilter" v-model="filter.username" />
          </td>
          <td>
            <input type="text" id="emailFilter" v-model="filter.emailAddress" />
          </td>
          <td>
            <select id="statusFilter" v-model="filter.status">
              <option value>Show All</option>
              <option value="Active">Active</option>
              <option value="Disabled">Disabled</option>
            </select>
          </td>
          <td>&nbsp;</td>
        </tr>
        <tr
          v-for="user in filteredList"
          v-bind:key="user.id"
          v-bind:class="{ disabled: user.status === 'Disabled' }"
        >
          <td>
            <input
              type="checkbox"
              v-bind:id="user.id"
              v-bind:value="user.id"
              v-on:click="dealWithId(user.id)"
            />
          </td>
          <td>{{ user.firstName }}</td>
          <td>{{ user.lastName }}</td>
          <td>{{ user.username }}</td>
          <td>{{ user.emailAddress }}</td>
          <td>{{ user.status }}</td>
          <td>
            <button
              class="btnEnableDisable"
              v-if="user.status === 'Active'"
              v-on:click="flipStatus(user.id)"
            >
              Disable
            </button>
            <button
              class="btnEnableDisable"
              v-else
              v-on:click="flipStatus(user.id)"
            >
              Enable
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="all-actions">
      <button v-if="actionButtonDisabled" disabled>Enable Users</button>
      <button v-else v-on:click="enableSelectedUsers">Enable Users</button>

      <button v-if="actionButtonDisabled" disabled>Disable Users</button>
      <button v-else v-on:click="disableSelectedUsers">Disable Users</button>

      <button v-if="actionButtonDisabled" disabled>Delete Users</button>
      <button v-else v-on:click="deleteSelectedUsers">Delete Users</button>
    </div>

    <button v-on:click="toggleForm">Add New User</button>

    <form id="frmAddNewUser" @submit.prevent="saveUser" v-show="showForm">
      <div class="field">
        <label for="firstName">First Name:</label>
        <input type="text" name="firstName" v-model="newUser.firstName" />
      </div>
      <div class="field">
        <label for="lastName">Last Name:</label>
        <input type="text" name="lastName" v-model="newUser.lastName" />
      </div>
      <div class="field">
        <label for="username">Username:</label>
        <input type="text" name="username" v-model="newUser.username" />
      </div>
      <div class="field">
        <label for="emailAddress">Email Address:</label>
        <input type="text" name="emailAddress" v-model="newUser.emailAddress" />
      </div>
      <button type="submit" class="btn save">Save User</button>
    </form>
  </div>
</template>

<script>
export default {
  name: "user-list",
  data() {
    return {
      filter: {
        firstName: "",
        lastName: "",
        username: "",
        emailAddress: "",
        status: "",
      },
      newUser: {
        id: null,
        firstName: "",
        lastName: "",
        username: "",
        emailAddress: "",
        status: "Active",
      },
      users: [
        {
          id: 1,
          firstName: "John",
          lastName: "Smith",
          username: "jsmith",
          emailAddress: "jsmith@gmail.com",
          status: "Active",
        },
        {
          id: 2,
          firstName: "Anna",
          lastName: "Bell",
          username: "abell",
          emailAddress: "abell@yahoo.com",
          status: "Active",
        },
        {
          id: 3,
          firstName: "George",
          lastName: "Best",
          username: "gbest",
          emailAddress: "gbest@gmail.com",
          status: "Disabled",
        },
        {
          id: 4,
          firstName: "Ben",
          lastName: "Carter",
          username: "bcarter",
          emailAddress: "bcarter@gmail.com",
          status: "Active",
        },
        {
          id: 5,
          firstName: "Katie",
          lastName: "Jackson",
          username: "kjackson",
          emailAddress: "kjackson@yahoo.com",
          status: "Active",
        },
        {
          id: 6,
          firstName: "Mark",
          lastName: "Smith",
          username: "msmith",
          emailAddress: "msmith@foo.com",
          status: "Disabled",
        },
      ],
      showForm: false,
      selectedUserIDs: [],
    };
  },
  methods: {
    saveUser() {
      this.users.push({
        id: this.users.length + 1,
        firstName: this.newUser.firstName,
        lastName: this.newUser.lastName,
        username: this.newUser.username,
        emailAddress: this.newUser.emailAddress,
        status: "Active",
      });
      this.newUser.id = null;
      this.newUser.firstName = "";
      this.newUser.lastName = "";
      this.newUser.username = "";
      this.newUser.emailAddress = "";
      this.showForm = !this.showForm;
    },
    toggleForm() {
      this.showForm = !this.showForm;
    },
    flipStatus(id) {
      for (const user of this.users) {
        if (user.id === id) {
          console.log(user);
          if (user.status === "Active") user.status = "Disabled";
          else if (user.status === "Disabled") user.status = "Active";
        }
      }
    },
    dealWithId(id) {
      const index = this.selectedUserIDs.indexOf(id);
      if (index > -1) {
        this.selectedUserIDs.splice(index, 1);
      } else {
        this.selectedUserIDs.push(id);
      }
      console.log(this.selectedUserIDs.length);
    },
    enableSelectedUsers() {
      this.selectedUserIDs.map((id) => {
        for (const user of this.users) {
          if (user.id === id) {
            console.log(user);
            if (user.status === "Disabled") user.status = "Active";
          }
        }
      });

      const markedCheckbox = document.querySelectorAll(
        'input[type="checkbox"]:checked'
      );
      for (const checkbox of markedCheckbox) {
        checkbox.checked = false;
      }
      this.selectedUserIDs = [];
    },
    disableSelectedUsers() {
      this.selectedUserIDs.map((id) => {
        for (const user of this.users) {
          if (user.id === id) {
            console.log(user);
            if (user.status === "Active") user.status = "Disabled";
          }
        }
      });

      const markedCheckbox = document.querySelectorAll(
        'input[type="checkbox"]:checked'
      );
      for (const checkbox of markedCheckbox) {
        checkbox.checked = false;
      }
      this.selectedUserIDs = [];
    },
    deleteSelectedUsers() {
      this.selectedUserIDs.forEach((id) => {
        console.log("selectedId", id);
        for (const user of this.users) {
          console.log("userId", id);
          if (user.id === id) {
            let index = this.users.findIndex((u) => u.id === user.id);
            console.log("index", index);
            this.users.splice(index, 1);
          }
        }
      });
      this.selectedUserIDs = [];
    },
    selectAll() {
      if (this.users.length === this.selectedUserIDs.length) {
        this.selectedUserIDs = [];
        const markedCheckbox = document.querySelectorAll(
          'input[type="checkbox"]'
        );
        for (const checkbox of markedCheckbox) {
          checkbox.checked = false;
        }
      } else {
        this.users.map((user) => {
          if (!this.selectedUserIDs.includes(user.id)) {
            this.selectedUserIDs.push(user.id);
          }
        });
        const markedCheckbox = document.querySelectorAll(
          'input[type="checkbox"]'
        );
        for (const checkbox of markedCheckbox) {
          checkbox.checked = true;
        }
      }
    },
  },
  computed: {
    filteredList() {
      let filteredUsers = this.users;
      if (this.filter.firstName != "") {
        filteredUsers = filteredUsers.filter((user) =>
          user.firstName
            .toLowerCase()
            .includes(this.filter.firstName.toLowerCase())
        );
      }
      if (this.filter.lastName != "") {
        filteredUsers = filteredUsers.filter((user) =>
          user.lastName
            .toLowerCase()
            .includes(this.filter.lastName.toLowerCase())
        );
      }
      if (this.filter.username != "") {
        filteredUsers = filteredUsers.filter((user) =>
          user.username
            .toLowerCase()
            .includes(this.filter.username.toLowerCase())
        );
      }
      if (this.filter.emailAddress != "") {
        filteredUsers = filteredUsers.filter((user) =>
          user.emailAddress
            .toLowerCase()
            .includes(this.filter.emailAddress.toLowerCase())
        );
      }
      if (this.filter.status != "") {
        filteredUsers = filteredUsers.filter(
          (user) => user.status === this.filter.status
        );
      }
      return filteredUsers;
    },
    actionButtonDisabled() {
      let disablebtns;
      if (this.selectedUserIDs.length === 0) {
        disablebtns = true;
      }
      if (this.selectedUserIDs.length > 0) {
        disablebtns = false;
      }
      return disablebtns;
    },
  },
};
</script>

<style scoped>
table {
  margin-top: 20px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  margin-bottom: 20px;
}
th {
  text-transform: uppercase;
}
td {
  padding: 10px;
}
tr.disabled {
  color: red;
}
input,
select {
  font-size: 16px;
}

form {
  margin: 20px;
  width: 350px;
}
.field {
  padding: 10px 0px;
}
label {
  width: 140px;
  display: inline-block;
}
button {
  margin-right: 5px;
}
.all-actions {
  margin-bottom: 40px;
}
.btn.save {
  margin: 20px;
  float: right;
}
</style>
