<template>
  <div class="dagre-graph-container">
    <Dagre-Graph :nodes="nodes" :edges="edges"></Dagre-Graph>
  </div>
</template>
<script>
/**
   * 流程图
   */
import DagreGraph from './DagreGraph';
export default {
  name: 'FlowDiagram',
  components: {
    DagreGraph
  },
  data() {
    return {
      nodes: [],
      edges: []
    };
  },
  created() {
    this.init();
  },
  methods: {
    init() {
      this.nodes = [
        {
          id: 'e1',
          state: '',
          value: {
            label: '开始',
            operator: 'BashOperator'
          }
        },
        {
          id: 'e2',
          state: 'success',
          value: {
            label: '分支一',
            operator: 'BranchPythonMapOperator'
          }
        },
        {
          id: 'e3',
          state: 'success',
          value: {
            label: '分支二',
            operator: 'BranchPythonMapOperator'
          }
        },
        {
          id: 'e4',
          state: 'queued',
          value: {
            label: '节点3',
            operator: 'BashOperator'
          }
        },
        {
          id: 'e5',
          state: 'failed',
          value: {
            label: '节点4',
            operator: 'BashOperator'
          }
        },
        {
          id: 'e6',
          state: 'upstream_failed',
          value: {
            label: '结束',
            operator: 'JoinOperator'
          }
        }
      ];
      this.edges = [
        {
          from: 'e1',
          to: 'e2'
        },
        {
          from: 'e1',
          to: 'e3'
        },
        {
          from: 'e2',
          to: 'e4',
          label: '条件1'
        },
        {
          from: 'e3',
          to: 'e5',
          label: '条件2'
        },
        {
          from: 'e4',
          to: 'e6'
        },
        {
          from: 'e5',
          to: 'e6'
        }
      ];
    }
  }
};
</script>
