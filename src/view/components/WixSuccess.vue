<template>
  <div class="rootContainer"></div>
</template>

<script>
import { mapGetters } from "vuex";
import ApiService from "@/core/services/api.service";
import Swal from "sweetalert2";
export default {
  data() {
    return {
      auth_code: this.$route.query.code,
      instance_id: this.$route.query.instanceId,
    };
  },
  computed: {
    ...mapGetters(["currentUser", "isAuthenticated"]),
  },
  created() {
    let query = this.$route.query;
    if (!this.isAuthenticated) {
      this.$router.push({ path: "/login", query: query });
      return;
    }

    let postData = {
      tokenData: {
        auth_code: this.auth_code,
        instance_id: this.instance_id,
        customer_id: this.currentUser.id,
      },
    };
    ApiService.post(`/wix/finishInitialize`, postData)
      .then(({ data }) => {
        // console.log(data)
        // alert(data)
        // window.close();
        this.$router.push("/integrations/wix/dashboard");
        //    window.location.href=`https://www.wix.com/installer/close-window?access_token=${data.global.token}`
      })
      .catch((resp) => {
        console.error(resp);
        Swal.fire({
          title: "Some error occurred while authorizing",
          icon: "error",
        }).then(() => {
          window.location.href = "editor.wix.com";
        });
      });
  },
};
</script>

<style scoped>
.rootContainer {
  height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
