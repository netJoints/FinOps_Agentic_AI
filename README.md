# Multi-Agent Finance Operations System Deployment Guide

## 🏗️ Architecture Overview

### Hierarchical Supervisor Architecture

```
                    ┌─────────────────────┐
                    │  Supervisor Agent   │
                    │  (Orchestrator)     │
                    └──────────┬──────────┘
                               │
                ┌──────────────┼──────────────┐
                │              │              │
        ┌───────▼──────┐ ┌────▼─────┐ ┌─────▼──────┐
        │    Fraud     │ │Compliance│ │    Risk    │
        │  Detection   │ │  Agent   │ │  Analysis  │
        └──────────────┘ └──────────┘ └────────────┘
```

### Supervisor Agent Responsibilities

1. **Task Decomposition**: Breaks complex queries into subtasks
2. **Agent Orchestration**: Determines which specialists to invoke
3. **Execution Coordination**: Manages sequence and flow
4. **Result Aggregation**: Synthesizes outputs into actionable insights
5. **Conflict Resolution**: Identifies and resolves discrepancies
6. **Quality Assurance**: Ensures comprehensive analysis

## 📋 Prerequisites

1. AWS Account with Bedrock access
2. Britive credentials configured
3. Python 3.12+ with virtual environment
4. Required packages installed:
   ```bash
   pip install flask flask-cors boto3 pybritive yfinance bedrock-agentcore bedrock-agentcore-starter-toolkit strands-agents
   ```

## 🚀 Step-by-Step Deployment

### Step 1: Verify Configuration

```bash
# Check if config file exists
cat .bedrock_agentcore.yaml

# List configured agents
agentcore configure list
```

You should see 4 agents:
- supervisor_agent (Orchestrator)
- fraud_agent (Fraud Detection)
- compliance_agent (Compliance Monitoring)
- risk_agent (Risk Analysis)

### Step 2: Deploy Agents

Deploy in this order (Supervisor first, then specialists):

```bash
# 1. Deploy Supervisor Agent
agentcore launch --agent supervisor_agent
# Wait for completion, note the agent_id and alias_id

# 2. Deploy Fraud Detection Agent
agentcore launch --agent fraud_agent
# Wait for completion

# 3. Deploy Compliance Agent
agentcore launch --agent compliance_agent
# Wait for completion

# 4. Deploy Risk Analysis Agent
agentcore launch --agent risk_agent
# Wait for completion
```

### Step 3: Verify Deployment

```bash
# Check status of all agents
agentcore status

# You should see output with agent_id and agent_arn for each
```

### Step 4: Update Web Application

Copy the agent IDs and alias IDs from the status command and update `finance_webapp.py`:

```python
AGENTS = {
    "supervisor": {
        "agent_id": "supervisor_agent-XXXXXXXXXX",  # From agentcore status
        "alias_id": "TSTALIASID"  # Or your actual alias ID
    },
    "fraud_detection": {
        "agent_id": "fraud_agent-XXXXXXXXXX",
        "alias_id": "TSTALIASID"
    },
    "compliance": {
        "agent_id": "compliance_agent-XXXXXXXXXX",
        "alias_id": "TSTALIASID"
    },
    "risk_analysis": {
        "agent_id": "risk_agent-XXXXXXXXXX",
        "alias_id": "TSTALIASID"
    }
}
```

### Step 5: Install yfinance (for real financial data)

```bash
pip install yfinance
```

### Step 6: Run the Web Application

```bash
python finance_webapp.py
```

Access at: **http://localhost:5001**

## 🎯 How It Works

### Request Flow

1. **User Query** → Web Interface
2. **Supervisor Agent** receives query
   - Analyzes intent
   - Decomposes into subtasks
   - Creates execution plan
3. **Specialist Agents** invoked based on plan
   - Fraud Agent: Transaction analysis
   - Compliance Agent: Regulatory checks
   - Risk Agent: Portfolio risk calculations
4. **Supervisor Agent** aggregates results
   - Synthesizes findings
   - Identifies cross-functional insights
   - Provides actionable recommendations
5. **Final Report** → User

### Example Query Flow

**Query**: "Analyze recent suspicious transactions and check compliance status"

1. Supervisor decomposes:
   - Subtask 1: Fraud detection on transactions
   - Subtask 2: Compliance status review

2. Supervisor routes:
   - fraud_agent → Analyzes transaction patterns
   - compliance_agent → Reviews regulatory status

3. Supervisor aggregates:
   - Synthesizes both reports
   - Identifies if fraud has compliance implications
   - Provides unified recommendations

## 🔧 Troubleshooting

### Agent Not Deploying

```bash
# Check AWS credentials
aws sts get-caller-identity

# Or use Britive
pybritive checkout "AWS SE Demo/Britive Agentic AI Solution/Admin" -t demo
```

### Configuration Errors

```bash
# Validate YAML syntax
python -c "import yaml; yaml.safe_load(open('.bedrock_agentcore.yaml'))"
```

### Agent Invocation Fails

```bash
# Check agent status
agentcore status

# Test individual agent
agentcore invoke --agent supervisor_agent --input "Test query"
```

## 📊 Architecture Benefits

### Hierarchical Supervision Advantages

1. **Intelligent Task Decomposition**
   - Complex queries automatically broken down
   - Optimal agent selection
   - Parallel or sequential execution based on dependencies

2. **Centralized Coordination**
   - Single point of orchestration
   - Consistent workflow management
   - Better error handling

3. **Cross-Functional Insights**
   - Supervisor identifies patterns across specialist reports
   - Resolves conflicts between agents
   - Provides holistic recommendations

4. **Scalability**
   - Easy to add new specialist agents
   - Supervisor adapts routing logic
   - No changes needed to specialist agents

5. **Quality Assurance**
   - Supervisor validates completeness
   - Ensures all relevant analyses performed
   - Aggregates with context awareness

## 🎓 Testing Queries

Try these to see the supervisor in action:

1. **Multi-Domain Query**:
   ```
   Analyze suspicious transactions, check SOX compliance, and calculate portfolio risk for high-risk accounts
   ```
   → Supervisor invokes all three specialists

2. **Fraud + Compliance**:
   ```
   Identify fraudulent activities and assess regulatory implications
   ```
   → Supervisor coordinates fraud and compliance agents, then synthesizes findings

3. **Comprehensive Analysis**:
   ```
   Provide a complete financial health assessment including fraud, compliance, and risk
   ```
   → Supervisor orchestrates full analysis pipeline

## 📈 Monitoring

### View Agent Logs

```bash
# CloudWatch logs
aws logs tail /aws/bedrock-agentcore/supervisor_agent --follow

# Check specific agent
agentcore status --agent fraud_agent
```

### Performance Metrics

The supervisor tracks:
- Task decomposition time
- Individual agent execution time
- Aggregation and synthesis time
- Total end-to-end latency

## 🔐 Security

- Britive provides just-in-time credential management
- Agents have least-privilege IAM roles
- Network isolation via VPC configuration
- Encryption at rest and in transit

## 🎉 Success!

Your hierarchical multi-agent system is now deployed!

**Next Steps**:
1. Test with various financial queries
2. Monitor agent performance
3. Adjust supervisor routing logic as needed
4. Add more specialist agents if required




# Software Installation Instructions and Structure

<img width="733" height="278" alt="image" src="https://github.com/user-attachments/assets/4fda9e39-7733-47eb-8831-fc65568d05dd" />

## Summary of Modular Structure:
### Benefits of this structure:

1. Easy Debugging 🐛

** Problem with financial data? → Check services/financial_data.py
** Britive issues? → Check services/britive_client.py
** Agent not responding? → Check services/agentcore_client.py
** API errors? → Check routes/api.py

2. Easy Updates 🔄

** Want to change styling? → Edit static/css/styles.css
** Need to add API endpoint? → Edit routes/api.py
New agent logic? → Edit services/agentcore_client.py

3. Testable ✅

** Each service can be unit tested independently
** Mock external dependencies easily


4. Scalable 📈

** Add new agents by extending agentcore_client.py
** Add new data sources in financial_data.py
** Add new routes in routes/



### How to Set It Up:
'''
1. Run the setup script (creates all directories and files):

bash   
  bash setup.sh

2. Copy code from the 3 artifacts I created:

Artifact 1: "Modular FinOps App Structure" → Copy to Python files
Artifact 2: "Frontend Files" → Copy to HTML/CSS/JS files
Artifact 3: "Setup Guide" → Reference for commands


3. Install and run:

bash   
   cd finops_app
   pip install -r requirements.txt
   python app.py
'''
