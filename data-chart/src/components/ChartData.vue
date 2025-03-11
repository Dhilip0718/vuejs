<template>
  <div class="container">
    <p>CHECK RENDER {{ selectedNode }}</p>
    <div id="graph"></div>
    <div v-if="dataLoaded">
      <div class="node-details">
        <h3>{{ selectedNode.name }}</h3>
        <p>123</p>
        <p>{{ selectedNode.description }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3';
import Vue from 'vue'; // Import Vue

export default {
  name: 'ChartData',
  data() {
    return {
      selectedNode: null,
      dataLoaded: false,
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      try {
        const response = await fetch('http://localhost:3000/api/data');
        const data = await response.json();
        this.renderGraph(data);
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    renderGraph(data) {
      const width = 800;
      const height = 600;
      const nodeRadius = 20;

      const svg = d3
        .select('#graph')
        .append('svg')
        .attr('width', width)
        .attr('height', height);

      const treeLayout = d3
        .tree()
        .size([height, width - 150])
        .separation((a, b) => (a.parent == b.parent ? 1 : 2));

      const root = d3.hierarchy(data[0]);
      treeLayout(root);

      // eslint-disable-next-line no-unused-vars
      const links = svg
        .selectAll('.link')
        .data(root.links())
        .enter()
        .append('path')
        .attr('class', 'link')
        .attr(
          'd',
          d3
            .linkHorizontal()
            .x(d => d.y)
            .y(d => d.x)
        );

      const nodes = svg
        .selectAll('.node')
        .data(root.descendants())
        .enter()
        .append('g')
        .attr('class', 'node')
        .attr('transform', d => `translate(${d.y},${d.x})`)
        .on('click', this.handleNodeClick);

      nodes
        .append('circle')
        .attr('r', nodeRadius)
        .attr('fill', d => (d.data.parent ? 'lightblue' : 'orange'));

      nodes
        .append('text')
        .attr('dy', '0.31em')
        .attr('x', d => (d.children ? -6 : 6))
        .attr('text-anchor', d => (d.children ? 'end' : 'start'))
        .text(d => d.data.name);
    },
    handleNodeClick(event, data) {
      if (this.selectedNode && this.selectedNode.name === data.data.name) {
        this.selectedNode = null;
        this.dataLoaded = false;
      } else {
        this.selectedNode = data.data;
        this.dataLoaded = true;
        // Example: Update the children (replace with your actual logic)
        if (this.selectedNode.children) {
          const updatedChildren = this.selectedNode.children.map(child => ({
            ...child,
            // Modify child properties as needed
          }));
          Vue.set(this.selectedNode, 'children', updatedChildren); // Force reactivity
        }
      }
    },
  },
};
</script>

<style>
.node circle {
  stroke: #999;
  stroke-width: 1px;
}

.link {
  fill: none;
  stroke: #999;
  stroke-opacity: 0.6;
  stroke-width: 1.5px;
}

.node-details {
  position: absolute;
  top: 20px;
  right: 20px;
  background-color: white;
  border: 1px solid #ccc;
  padding: 10px;
  z-index: 100;
}
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  /* flex-direction: column; */
}
</style>