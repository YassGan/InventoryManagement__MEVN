<template>
  <div style="height: 100vh; background: #f5f7fa">




    <button v-if="this.showHamburger" class="hamburger-button" @click="toggleSidebar">
      <span></span>
      <span></span>
      <span></span>
    </button>

    <div class="navbarContainer" v-if="windowWidth >= mobileBreakpoint">
      <!-- Your existing navigation bar code here -->
      <AppNavbar> </AppNavbar>

    </div>



    <div   class="mobile-sidebar"
  :class="{open: isOpen, closed: !isOpen}" v-else>
      <!-- Hamburger button -->

      <!-- Mobile sidebar -->
      <MobileSidebar v-show="isSidebarOpen" />
    </div>














    <div  class="PageContainer">
            <div style="margin-top: 60px">
      <h3>
        Liste de vos agents de comptage
      </h3>
    </div>

      <!-- Button to navigate to create new user page -->
      <div style="margin-bottom:40px;margin-left:20px" class="CreateButtonContainer">
        <router-link to="/PageCreationNvUtilisateur" class="CreateButton">Créer un nouveau agent</router-link>
      </div>

<div style=" width:100%;padding-left:50px;padding-right:50px">
<div class="TableContainer">
      <!-- Display the list of users in a table -->
<table class="UserTable">
        <thead>
          <tr>
            <th>Nom et Prénom</th>
            <th>Email</th>
            <th style="width:10%"><span style="">Actions</span></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(user, index) in users" :key="index" class="UserRow">
            <td>{{ user.nom }}</td>
            <td>{{ user.email }}</td>

            <td style="display:flex;justify-content:center">
              <button class="CustomButton yellowButton" @click="editUser(user.email)">Modifier</button>
              <button class="CustomButton redButton" @click="deleteUser(user.email)">Supprimer</button>
            </td>
          </tr>
        </tbody>
      </table>
</div>

</div>
    </div>
  </div>
</template>

<script>
import { HTTP } from "/axios";
import AppNavbar from "../components/AppNavbar.vue";
import MobileSidebar from "../components/SideBarMobile.vue";

import router from '@/router'

export default {
  components: {
    AppNavbar,
                    MobileSidebar,

  },
    computed: {
          sidebarState() {
    return this.isSidebarOpen ? 'open' : 'closed';
  },
  },
  data() {
    return {
      users: [], 
              mobileBreakpoint: 768,
      windowWidth: window.innerWidth,
      isSidebarOpen: false,
    };
  },
  mounted() {
    this.getUsers(); 
                            window.addEventListener('resize', this.handleResize);
    this.handleResize(); 
  },
  
  methods: {

    handleResize() {
      this.windowWidth = window.innerWidth;
      if (this.windowWidth >= this.mobileBreakpoint) {
        this.isSidebarOpen = false; 
        this.showHamburger=false;
      }
      else{
                this.showHamburger=true;

      }
    },

    toggleSidebar() {
        this.isOpen = !this.isOpen;

  if (this.isSidebarOpen) {
    this.isSidebarOpen = false;

  } else {
    this.isSidebarOpen = true; 
  }

},
  watch: {
    windowWidth(newValue) {
      if (newValue >= this.mobileBreakpoint) {
        this.isSidebarOpen = false;
      }
    },
  },



    async getUsers() {
      try {
        const User_loggedin_info_String =
          localStorage.getItem("User_loggedin_info");
        const User_loggedin_info_Object = JSON.parse(User_loggedin_info_String);
        const idSociete = User_loggedin_info_Object.idSociete;

        const response = await HTTP.get(`UtilisateursAPI/getAllUsersBySocieteId/${idSociete}`);

            this.users = response.data.filter(user => !user.email.includes('test_User_'));

        console.log(this.users)
      } catch (error) {
        console.error(error);
      }
    },
    editUser(email) {
      console.log("Editing user:", email);
      router.push("PageUpdateUser/"+email);
    },
   
    async deleteUser(email) {
      const shouldDelete = window.confirm("Etes-vous sûr de vouloir effacer l' agent "+email+" ?");
      if (shouldDelete) {
        try {
          const response = await HTTP.delete(`UtilisateursAPI/deleteUser/${email}`);
          console.log(response.data.message);
          const deletedIndex = this.users.findIndex((item) => item.email === email);

          if (deletedIndex !== -1) {
            this.users.splice(deletedIndex, 1);
          }
        } catch (error) {
          console.error("Error deleting user:", error);
        }
      }
    },





  },
};
</script>




<style scoped>

.hamburger-button {
  position: relative;
  z-index: 100;
    display: block;
  padding: 10px;
  background-color: transparent;
  border: none;
  cursor: pointer;
}
.mobile-sidebar.open {
  transform: translateX(0);  
}

.mobile-sidebar.closed {
  transform: translateX(-100%);
}
.mobile-sidebar {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  width: 300px;
  z-index: 99;
  background-color: #d1d1d1;
  box-shadow: 2px 0px 5px rgba(0, 0, 0, 0.2);
  overflow-y: auto;
  transition: transform 0.3s ease; 
}
.hamburger-button span {
  display: block;
  width: 25px;
  height: 3px;
  background-color: black;
  margin: 4px auto;
}
.navbarContainer{
  margin: 0;
  padding: 0;
}

.InputSearch {
  padding: 10px;
  border: 1px solid #ccc;
  margin-bottom: 30px;
  border-radius: 4px;
  font-size: 16px;
  outline: none;
  width: 200px !important;
}


@media (max-width: 768px) {


.InventoryTableContainer{
  width: 95%  !important;
}


  /* Adjustments for smaller screens */
  h3{
  font-size: 16px;
}

  .accueil {
    padding: 20px;
  }

  .fonctionnalites {
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  }

  h1 {
    font-size: 20px;
    margin-bottom: 15px;
  }

  h2 {
    font-size: 16px;
    margin-bottom: 10px;
  }

  p {
    font-size: 12px;
  }

  .fonctionnalite {
    padding: 15px;
  }

  /* Make buttons more touch-friendly */
  .fonctionnalite button {
    padding: 5px 10px;
    font-size: 12px;
    }



.PageContainer {
padding-left: 10px;
padding-right: 10px;
width: 85% !important;

}
.UserTable {
  width: 100px !important;
  
}


.TableContainer {
  overflow-x: auto ;
  width: 100%;
  margin-left: 20px;
}

.CustomButton {
  color: white;
  border: none;
  padding: 5px 10px !important;
  font-size: 14px;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.3s ease-in-out;
}

}




.PageContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-left:4%;
}

.PageTitle {
  font-size: 24px;
  margin-bottom: 20px;
  color: #333;
  padding-top: 70px;
}

.UserTable {
  width: 90%;
  border-collapse: collapse;
  margin-top: 20px;
}

.UserTable th,
.UserTable td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}

.UserTable th {
  background-color: #2c3e50;
  color:white;
  
}

.UserRow:hover {
  background-color: #f0f0f0;
}

button {
  padding: 4px 8px;
  margin-right: 4px;
  cursor: pointer;
}

.CustomButton {
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 14px;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.1s ease-in-out;
}

.CustomButton:hover {
  background-color: #c2c0bd;
}

.CreateButtonContainer {
  margin-top: 20px;
}

.CreateButton {
  background-color: #e65124;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 14px;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.1s ease-in-out;
  text-decoration: none;
}

.CreateButton:hover {
  background-color: #d5613e;
}

.redButton {
  background: rgb(214, 38, 38);
}
.redButton:hover {
  background: rgb(230, 63, 63);
}
.yellowButton {
  background: rgb(20, 111, 160);
}
.yellowButton:hover {
  background: rgb(30, 147, 210);
}
</style>