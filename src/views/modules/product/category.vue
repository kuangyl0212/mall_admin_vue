<template>
  <el-tree
    :data="tree"
    :props="treeProps"
    :expand-on-click-node="false"
    node-key="catId"
    :default-expanded-keys="expandedKeys"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button
          v-if="node.isLeaf === false"
          type="text"
          size="mini"
          @click="() => append(data)"
        >
          Append
        </el-button>
        <el-button
          v-if="node.childNodes.length <= 0"
          type="text"
          size="mini"
          @click="() => remove(node, data)"
        >
          Delete
        </el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
export default {
  data () {
    return {
      tree: [],
      expandedKeys: [],
      treeProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  methods: {
    getTreeData () {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        console.log(data)
        this.tree = data.tree
      })
    },
    remove (node, data) {
      let ids = []
      if (!data.catId) {
        // TODO maybe some fancy user-friendly tips
        return
      }
      ids.push(data.catId)
      console.log(node, data)
      this.$confirm(`确认删除${data.name}?`)
        .then(() => {
          this.$http({
            url: this.$http.adornUrl('/product/category/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            console.log(data)
            this.$message({
              message: '删除成功',
              type: 'success'
            })
            this.getTreeData()
            this.expandedKeys = [node.parent.data.catId]
          })
        })
    },
    append (data) {
      console.log(data)
    }
  },
  created () {
    this.getTreeData()
  }
}
</script>

<style></style>
