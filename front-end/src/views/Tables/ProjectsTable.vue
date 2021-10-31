<template>
  <div class="card shadow" :class="type === 'dark' ? 'bg-default' : ''">
    <div
      class="card-header border-0"
      :class="type === 'dark' ? 'bg-transparent' : ''"
    >
      <div class="row align-items-center">
        <div class="col">
          <h3 class="mb-0" :class="type === 'dark' ? 'text-white' : ''">
            {{ title }}
          </h3>
        </div>
        <div class="col text-right">
          <base-button type="primary" size="sm">See all</base-button>
        </div>
      </div>
    </div>

    <div class="table-responsive">
      <base-table
        class="table align-items-center table-flush"
        :class="type === 'dark' ? 'table-dark' : ''"
        :thead-classes="type === 'dark' ? 'thead-dark' : 'thead-light'"
        tbody-classes="list"
        :data="tableData"
      >
        <template v-slot:columns>
          <th>Id</th>
          <th>Name</th>
          <th>Email</th>
          <th>Created_at</th>
          <th>Updated_at</th>
          <th></th>
        </template>

        <template v-slot:default="row">
          <th scope="row">
            {{ row.item.id }}
          </th>
          <td class="budget">
            {{ row.item.name }}
          </td>
          <td class="budget">
            {{ row.item.email }}
          </td>
          <td>
            {{ row.item.created_at }}
          </td>
          <td>
            {{ row.item.updated_at }}
          </td>
        </template>
      </base-table>
    </div>

    <div
      class="card-footer d-flex justify-content-end"
      :class="type === 'dark' ? 'bg-transparent' : ''"
    >
      <base-pagination total="10"></base-pagination>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "projects-table",
  props: {
    type: {
      type: String,
    },
    title: String,
  },
  data() {
    return {
      tableData: [],
    };
  },
  mounted() {
    axios
      .post(
        "http://127.0.0.1:8000/graphql",
        {
          query: `query{
                    users{
                      data{
                        id
                        name
                        email
                        created_at
                        updated_at
                      }
                    }
                  }`,
        },
        {
          headers: {
            "Content-Type": "application/json",
          },
        }
      )
      .then((res) => {
        this.tableData = res.data.data.users.data;
      })
      .catch((error) => {
        console.log(error);
      });
  },
};
</script>
<style></style>
