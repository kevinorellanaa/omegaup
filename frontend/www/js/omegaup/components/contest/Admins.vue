<template>
  <div class="panel panel-primary">
    <div class="panel-body">
      <form class="form" v-on:submit.prevent="onSubmit">
        <div class="form-group">
          <label>{{ T.wordsAdmin }}</label>
          <omegaup-autocomplete
            v-bind:init="el =&gt; UI.userTypeahead(el)"
            v-model="user"
          ></omegaup-autocomplete>
        </div>
        <div class="form-group">
          <div class="col-xs-5 col-sm-3 col-md-3 action-container">
            <button class="btn btn-primary" type="submit">
              {{ T.wordsAddAdmin }}
            </button>
          </div>
          <div class="col-xs-7 col-sm-9 col-md-9 toggle-container">
            <input type="checkbox" v-model="showSiteAdmin" />
            <label>{{ T.wordsShowSiteAdmins }}</label>
          </div>
        </div>
      </form>
    </div>
    <table class="table table-striped">
      <thead>
        <tr>
          <th>{{ T.contestEditRegisteredAdminUsername }}</th>
          <th>{{ T.contestEditRegisteredAdminRole }}</th>
          <th>{{ T.contestEditRegisteredAdminDelete }}</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="admin in admins"
          v-if="admin.role != 'site-admin' || showSiteAdmin"
        >
          <td>
            <omegaup-user-username
              v-bind:linkify="true"
              v-bind:username="admin.username"
            ></omegaup-user-username>
          </td>
          <td>{{ admin.role }}</td>
          <td>
            <button class="close" type="button" v-on:click="onRemove(admin)">
              ×
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';
import { omegaup } from '../../omegaup';
import T from '../../lang';
import * as UI from '../../ui';
import Autocomplete from '../Autocomplete.vue';
import user_Username from '../user/Username.vue';

@Component({
  components: {
    'omegaup-autocomplete': Autocomplete,
    'omegaup-user-username': user_Username,
  },
})
export default class Admins extends Vue {
  @Prop() data!: omegaup.UserRole[];

  T = T;
  UI = UI;
  user = '';
  showSiteAdmin = false;
  admins = this.data;
  selected = {};

  onSubmit(): void {
    this.$emit('emit-add-admin', this);
  }

  onRemove(admin: omegaup.UserRole): void {
    this.selected = admin;
    this.$emit('emit-remove-admin', this);
  }
}
</script>
