<template>
  <div>
    <b-card
      no-body
      class="mb-0"
    >

      <div class="m-2">

        <!-- Table Top -->
        <b-row>

          <!-- Per Page -->
          <b-col
            cols="12"
            md="6"
            class="d-flex align-items-center justify-content-start mb-1 mb-md-0"
          >
            <label>Show</label>
            <v-select
              v-model="maxPerPage"
              :options="perPageOptions"
              :clearable="false"
              class="per-page-selector d-inline-block mx-50"
            />
            <label>entries</label>
          </b-col>

          <!-- Search -->
          <b-col
            cols="12"
            md="6"
          >
            <div class="d-flex align-items-center justify-content-end">
              <b-form-input
                v-model="searchQuery"
                class="d-inline-block mr-1"
                placeholder="Search..."
              />
              <b-button
                variant="primary"
                @click="isAddNewUserSidebarActive = true"
              >
                <span class="text-nowrap">Add User</span>
              </b-button>
            </div>
          </b-col>
        </b-row>

      </div>
        <b-table
          ref="refUserListTable"
          class="position-relative"
          primary-key="id"
          :fields="['utilisateur', 'email', 'role', 'status', 'actions' ]"
          :items="users"
        >
          <!-- Column: User -->
          <template #cell(utilisateur)="data">
            <b-media vertical-align="center">
              <template #aside>
                <b-avatar
                  size="32"
                  :src="data.item.avatar"
                  :text="avatarText(data.item.fullName)"
                  :to="{ name: 'apps-users-view', params: { id: data.item.id } }"
                />
              </template>
              <b-link
                :to="{ name: 'apps-users-view', params: { id: data.item.id } }"
                class="font-weight-bold d-block text-nowrap"
              >
                {{ data.item.fullName }}
              </b-link>
              <small class="text-muted">@{{ data.item.username }}</small>
            </b-media>
          </template>

          <!-- Column: Status -->
          <template #cell(status)="data">
            <b-badge
              pill
              :variant="`light-${resolveUserStatusVariant(data.item.status)}`"
              class="text-capitalize"
            >
              {{ data.item.status }}
            </b-badge>
          </template>

          <!-- Column: Actions -->
          <template #cell(actions)="data">
            <b-dropdown
              variant="link"
              no-caret
            >
              <template #button-content>
                <unicon name="ellipsis-v" width="16px" heigth="16px" class="align-middle text-body" />
              </template>
              <b-dropdown-item :to="{ name: 'apps-users-view', params: { id: data.item.id } }">
                <span class="align-middle ml-50">Details</span>
              </b-dropdown-item>

              <b-dropdown-item :to="{ name: 'apps-users-edit', params: { id: data.item.id } }">
                <span class="align-middle ml-50">Edit</span>
              </b-dropdown-item>

              <b-dropdown-item>
                <span class="align-middle ml-50">Delete</span>
              </b-dropdown-item>
            </b-dropdown>
          </template>

        </b-table>
      <div class="mx-4 mb-4">
        <b-row>

          <b-col
            cols="12"
            sm="6"
            class="d-flex align-items-center justify-content-center justify-content-sm-start"
          >
            <span class="text-muted">Showing {{ metaData.from }} to {{ metaData.to }} of {{ metaData.of }} entries</span>
          </b-col>

        </b-row>
      </div>
    </b-card>
  </div>
</template>

<script>

export default {
  data() {
    return {
      searchQuery: '',
      currentPage: 1,
      maxPerPage: 10,
      users: []
    }
  },
  computed: {
    metaData: function() {
      return { 
        from : this.currentPage,
        to: this.maxPerPage,
        of: this.users.length,
      }
    },
  },
  created: function() {
    this.fetchUsers()
  },
  methods: {
    fetchUsers() {
      this.$user.getUsersList({
        q: this.searchQuery,
      }).then(res => {
        this.users = res
      })
    },
    avatarText(value) {
      if (!value) return ''
      const nameArray = value.split(' ')
      return nameArray.map(word => word.charAt(0).toUpperCase()).join('')
    },
    resolveUserStatusVariant(status) {
      if (status === 'pending') return 'warning'
      if (status === 'active') return 'success'
      if (status === 'inactive') return 'danger'
      return 'primary'
    }
  },
  watch: {
    searchQuery: function() {
      this.fetchUsers()
    }
  }
}
</script>

<style lang="scss">
.table {
  color: #6e6b7b;
  th, td {
    vertical-align: middle;
    padding: 0.72rem 2rem;
  }
  th {
    vertical-align: top;
    text-transform: uppercase;
    font-size: 0.857rem;
    letter-spacing: 0.5px;
    background-color: #f3f2f7;
    border-bottom: 2px solid #ebe9f1 !important;
    text-transform: uppercase;
  }
  .media {
    display: flex;
    align-items: flex-start;
  }
}

</style>