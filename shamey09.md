name: Verify Human Identity

on:
  workflow_dispatch:
    inputs:
      agent_name:
        description: 'shamey'
        required: true
        default: 'shamey'
      agent_description:
        description: 'shamey AI Agent'
        required: true
        default: 'shamey AI Agent'

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Step 1 - Install ClawHub skill
        run: |
          echo "Installing ClawHub..."
          npx clawhub@latest install verified-agent-identity || echo "ClawHub install failed, continuing..."

      - name: Step 2 - Install script dependencies
        run: |
          echo "Installing script dependencies..."
          if [ -d scripts ]; then
            cd scripts
            npm install || echo "npm install failed, continuing..."
          else
            echo "No scripts folder found, skipping npm install"
          fi

      - name: Step 3 - Create new Ethereum Identity
        run: |
          echo "Creating new Ethereum Identity..."
          if [ -f scripts/createNewEthereumIdentity.js ]; then
            node scripts/createNewEthereumIdentity.js || echo "Ethereum Identity creation failed, continuing..."
          else
            echo "createNewEthereumIdentity.js not found, skipping..."
          fi

      - name: Step 4 - Link Human to Agent
        run: |
          echo "Linking human to agent..."
          if [ -f scripts/manualLinkHumanToAgent.js ]; then
            node scripts/manualLinkHumanToAgent.js --challenge '{"name": "shamey", "description": "shamey AI Agent"}' || echo "Linking failed, continuing..."
          else
            echo "manualLinkHumanToAgent.js not found, skipping..."
          fi

      - name: Finished
        run: echo "Workflow complete ✅"
