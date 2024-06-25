<template>
  <div class="container">
    <ejs-button class="button" @click="onSave">Save</ejs-button>
    <ejs-button class="button" @click="onLoad">Load</ejs-button>
    <ejs-button class="button" @click="onExport">Export</ejs-button>
    <ejs-button class="button" @click="onPrint">Print</ejs-button>
  </div>
  <div style="display:none;">
    <ejs-uploader :asyncSettings="fileUploaderAsyncSettings"
    :success="fileUploadSuccess"
    ></ejs-uploader>
  </div>
  <ejs-diagram ref="diagramObject" :width="width" :height="height" :nodes="nodes" :connectors="connectors"
    :getNodeDefaults="getNodeDefaults"></ejs-diagram>
</template>

<script>
let diagramInstance;
let diagramData;

// #region nodes, and connectors definitions
const nodes = [
  {
    id: "startNode",
    offsetX: 440,
    offsetY: 60,
    shape: { type: "Flow", shape: "Terminator" },
    annotations: [{ content: 'Start', style: { color: 'white' } }]
  },
  {
    id: "inputNode",
    offsetX: 440,
    offsetY: 180,
    shape: { type: "Flow", shape: "Data" },
    annotations: [{ content: 'Enter a number', style: { color: 'white' } }]
  },
  {
    id: "decisionNode",
    offsetX: 440,
    offsetY: 300,
    shape: { type: "Flow", shape: "Decision" },
    annotations: [{ content: 'N divisible by 2 ?', style: { color: 'white' } }]
  },
  {
    id: "processEvenNode",
    offsetX: 700,
    offsetY: 300,
    shape: { type: "Flow", shape: "Process" },
    annotations: [{ content: 'N is Even', style: { color: 'white' } }]
  },
  {
    id: "processOddNode",
    offsetX: 440,
    offsetY: 420,
    shape: { type: "Flow", shape: "Process" },
    annotations: [{ content: 'N is Odd', style: { color: 'white' } }]
  },
  {
    id: "endNode",
    offsetX: 440,
    offsetY: 540,
    shape: { type: "Flow", shape: "Terminator" },
    annotations: [{ content: 'End', style: { color: 'white' } }]
  }
];

const connectors = [
  {
    id: 'startToInputConnector',
    sourceID: 'startNode',
    targetID: 'inputNode'
  },
  {
    id: 'inputToDecisionConnector',
    sourceID: 'inputNode',
    targetID: 'decisionNode'
  },
  {
    id: 'decisionToProcessEvenConnector',
    sourceID: 'decisionNode',
    targetID: 'processEvenNode',
    annotations: [{ content: 'true', alignment: 'Before', displacement: { x: 5, y: 5 } }]
  },
  {
    id: 'decisionToProcessOddConnector',
    sourceID: 'decisionNode',
    targetID: 'processOddNode',
    annotations: [{ content: 'false', alignment: 'After', displacement: { x: 5, y: 5 } }]
  },
  {
    id: 'processOddToEndConnector',
    sourceID: 'processOddNode',
    targetID: 'endNode'
  },
  {
    id: 'processEvenToEndConnector',
    sourceID: 'processEvenNode',
    targetID: 'endNode',
    type: "Orthogonal",
    segments: [{ type: "Orthogonal", direction: "Bottom", length: 200 }]
  }
];
// #endregion

import {
  DiagramComponent, PrintAndExport
} from '@syncfusion/ej2-vue-diagrams';
import { ButtonComponent } from '@syncfusion/ej2-vue-buttons';
import { UploaderComponent } from '@syncfusion/ej2-vue-inputs';

export default {
  components: {
    'ejs-diagram': DiagramComponent,
    'ejs-button': ButtonComponent,
    'ejs-uploader': UploaderComponent
  },
  data: function () {
    return {
      width: '1102px',
      height: '602px',
      nodes: nodes,
      connectors: connectors,
      fileUploaderAsyncSettings: {
        saveUrl: "https://services.syncfusion.com/vue/production/api/FileUploader/Save"
      }
    };
  },
  methods: {
    getNodeDefaults(node) {
      node.height = 60;
      node.width = 150;
      node.style = { fill: '#6495ED' };
    },
    onSave(){
      //diagramData = diagramInstance.saveDiagram();
      download(diagramInstance.saveDiagram());
    },
    onLoad(){
      //diagramInstance.loadDiagram(diagramData);
      document.querySelector('.e-file-select-wrap').querySelector('button').click();
    },
    fileUploadSuccess: onFileUploadSuccess,
    onExport(){
      diagramInstance.exportDiagram({ mode: 'Download', format: 'PNG', region: 'PageSettings',
        pageHeight: 800, pageWidth: 800
      });
    },
    onPrint(){
      diagramInstance.print({mode:'Data', region: 'PageSettings',
        pageHeight: 800, pageWidth: 800});
    }
  },
  provide: { diagram: [PrintAndExport]},
  mounted: function(){
    diagramInstance = this.$refs.diagramObject.ej2Instances;
  }
};

function download(JSONdata){
  const a = document.createElement('a');
  a.href = "data:text/json;charset=utf-8," + encodeURIComponent(JSONdata);
  a.download = 'Diagram.json';
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
}

function onFileUploadSuccess(args){
  const reader = new FileReader();
  reader.onloadend = () => diagramInstance.loadDiagram(reader.result);
  reader.readAsText(args.file.rawFile);
}
</script>

<style>
@import "../node_modules/@syncfusion/ej2-vue-diagrams/styles/material.css";
@import "../node_modules/@syncfusion/ej2-vue-buttons/styles/material.css";

.container{
  text-align: left;
  margin-bottom: 10px;
}

.button{
  margin-right: 10px;
}
</style>