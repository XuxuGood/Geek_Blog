<template>
  <div class="wrapper">
    <div class="ui message success">
      <div v-if="!token" class="header">恭喜！站点部署成功！</div>
      <div v-if="token" class="header">恭喜！博客初始化成功！</div>

      <div style="margin-top: 20px">
        <div v-if="!token">博客部署成功！首次进入博客需要先用inis登录。</div>
        <div v-if="token">
          博客初始化成功！你可以在设置页面配置你的个人信息！
        </div>
      </div>
    </div>
    <nuxt-link v-if="token" to="/Setting">
      <div style="width: 150px" class="ui fluid large teal submit button">
        前往设置
      </div>
    </nuxt-link>
    <div v-if="!token" class="login">
      <form class="ui large form">
        <div class="ui stacked segment">
          <div class="field">
            <div class="ui left icon input">
              <i class="user icon"></i>
              <input type="text" v-model="form.account" placeholder="账号" />
            </div>
          </div>
          <div class="field">
            <div class="ui left icon input">
              <i class="lock icon"></i>
              <input
                type="password"
                v-model="form.password"
                placeholder="密码"
              />
            </div>
          </div>
          <div @click="login" class="ui fluid large teal submit button">
            下一步
          </div>
        </div>
        <div class="ui error message"></div>
      </form>
    </div>
  </div>
</template>

<script>
// 这里是留言墙的id 注意要是文章的id 文章标题必须是：留言墙
import SettingDetail from "@/components/detail/SettingDetail";
import first_geek_config from "@/util/geek.config.js";

export default {
  components: { SettingDetail },
  head() {
    return {
      title: "起步",
    };
  },
  props: {},
  data() {
    return {
      token: "",
      geek_config: {},
      form: {
        account: "",
        password: "",
      },
    };
  },
  watch: {},
  computed: {},
  methods: {
    async getConfig() {
      const res_geek_config = await this.$axios.get(
        "/api/options?key=geek_config&cache=false"
      );
      if (res_geek_config.code == 200 && res_geek_config.data.opt) {
        this.geek_config = res_geek_config.data.opt;
      } else {
        var data = {
          "login-token": this.$cookies.get("token"),
          keys: "geek_config",
          opt: first_geek_config.geek_config,
        };
        this.$axios.post("/api/options", data);
        this.geek_config = first_geek_config.geek_config;
      }
    },
    login() {
      var data = this.form;
      data.mode = "login";
      this.loading = true;
      this.$axios.post("/api/users", data).then((res) => {
        this.loading = false;
        if (res.code == "200") {
          this.$cookies.set("token", res.data["login-token"]);
          location.reload();
        }
      });
    },
  },
  created: function () {
    this.token = this.$cookies.get("token");
    if (this.token) {
      this.getConfig();
    }
  },
};
</script>
<style lang="scss" scoped>
.wrapper {
  width: 1000px;
  margin: 0px auto;
  padding: 50px;
}
</style>
