<template>
  <section class="content">
    <div class="box">
      <div class="box-body">
        <!--Query Start-->
        <form class="form-horizontal">
          <div class="row row-margin-bottom">
            <div class="col-md-4">
              <FormGroup label="字典名称">
                <select class="form-control" v-model="param.type">
                  <option value="">全部</option>
                  <option v-for="dictType of dictTypeList" :value="dictType">{{ dictType }}</option>
                </select>
              </FormGroup>
            </div>
            <div class="col-md-4">
              <FormGroup label="字典描述">
                <input class="form-control" type="text" v-model="param.description">
              </FormGroup>
            </div>
            <div class="col-md-4">
              <div class="pull-right">
                <router-link to="/system/dict/add" class="btn btn-primary">添加</router-link>
              </div>
            </div>
          </div>
        </form>
        <!--Query End-->

        <div class="row">
          <div class="col-md-12">
            <table class="table table-bordered table-hover">
              <thead>
              <tr>
                <th>字典名称</th>
                <th>字典描述</th>
                <th>字典值描述</th>
                <th>字典值</th>
                <th>排序号</th>
                <th>操作</th>
              </tr>
              </thead>
              <tbody>
              <tr v-for="obj of page.list">
                <td><a @click="param.type = obj.type">{{ obj.type }}</a></td>
                <td>{{ obj.description }}</td>
                <td>{{ obj.label }}</td>
                <td>{{ obj.value }}</td>
                <td>{{ obj.sort }}</td>
                <td>
                  <router-link :to='"/system/dict/" + obj.id + "/form"'>修改</router-link>
                  <a @click="deleteData(obj.id)">删除</a>
                </td>
              </tr>
              </tbody>
            </table>

            <Pagination :page="page" @page="handlerPage(arguments)"></Pagination>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
  import PageMixin from 'mixins/PageMixin.js'

  export default {
    mixins: [PageMixin],
    data: () => {
      return {
        actions: {
          list: {method: 'get', url: '/api/sys/dict'},
          listType: {method: 'get', url: '/api/sys/dict/type'},
          deleteData: {method: 'delete', url: '/api/sys/dict{/id}'}
        },
        param: {
          type: '',
          description: ''
        },
        dictTypeList: []
      }
    },
    mounted () {
      this.loadDictType()
    },
    methods: {
      loadDictType () {
        this.resource.listType().then((response) => {
          let result = response.body
          if (result.ok) {
            this.dictTypeList = result.data
          }
        })
      },
      deleteData (id) {
        this.$confirm('确认删除吗？', () => {
          this.resource.deleteData({id: id}).then((response) => {
            let result = response.body
            if (result.ok) {
              this.query()
              this.$notify.success('删除成功！')
            }
          })
        })
      }
    }
  }
</script>
