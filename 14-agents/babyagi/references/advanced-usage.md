# Advanced Usage

## Custom Tools

### Creating and integrating custom tools

```python
from babyagi import BabyAGI
from langchain.tools import Tool
import requests

# Weather tool
def get_weather(city):
    """Get current weather for a city"""
    api_key = "your-weather-api-key"
    url = f"https://api.weatherapi.com/v1/current.json?key={api_key}&q={city}"
    response = requests.get(url)
    data = response.json()
    return f"Current weather in {city}: {data['current']['temp_c']}°C, {data['current']['condition']['text']}"

# News tool
def get_news(topic):
    """Get latest news on a topic"""
    api_key = "your-news-api-key"
    url = f"https://newsapi.org/v2/everything?q={topic}&apiKey={api_key}&pageSize=3"
    response = requests.get(url)
    data = response.json()
    news_items = [f"- {article['title']}: {article['url']}" for article in data['articles']]
    return f"Latest news on {topic}:\n" + "\n".join(news_items)

# Create tool list
tools = [
    Tool(
        name="Weather",
        func=get_weather,
        description="Get current weather for a city"
    ),
    Tool(
        name="News",
        func=get_news,
        description="Get latest news on a topic"
    )
]

# Initialize with tools
baby_agi = BabyAGI(
    openai_api_key="your-openai-api-key",
    objective="Research weather patterns and news in major cities",
    initial_tasks=["Get weather in New York", "Get latest news on AI"],
    tools=tools,
    max_runs=8
)

# Run the agent
baby_agi.run()
```

### Tool integration patterns

#### API integration

```python
import requests
from langchain.tools import Tool

# GitHub API tool
def github_search(query):
    """Search GitHub repositories"""
    url = f"https://api.github.com/search/repositories?q={query}"
    response = requests.get(url)
    data = response.json()
    repos = [f"- {repo['name']}: {repo['html_url']} (⭐ {repo['stargazers_count']})"]
    return f"GitHub search results for '{query}':\n" + "\n".join(repos[:3])

# PyPI package tool
def pypi_search(package):
    """Search PyPI packages"""
    url = f"https://pypi.org/pypi/{package}/json"
    try:
        response = requests.get(url)
        data = response.json()
        return f"PyPI package '{package}':\n- Version: {data['info']['version']}\n- Summary: {data['info']['summary']}\n- URL: {data['info']['project_urls'].get('Homepage', 'N/A')}"
    except:
        return f"Package '{package}' not found on PyPI"
```

#### Local tool integration

```python
import os
import subprocess
from langchain.tools import Tool

# File system tool
def list_files(directory):
    """List files in a directory"""
    try:
        files = os.listdir(directory)
        return f"Files in {directory}:\n" + "\n".join([f"- {file}" for file in files[:20]])
    except Exception as e:
        return f"Error: {str(e)}"

# Shell command tool
def run_command(command):
    """Run a shell command"""
    try:
        result = subprocess.run(command, shell=True, capture_output=True, text=True, timeout=10)
        return f"Command output:\n{result.stdout}\nError (if any):\n{result.stderr}"
    except Exception as e:
        return f"Error running command: {str(e)}"
```

## Prompt Engineering

### Customizing task creation prompts

#### Domain-specific prompts

```python
# Researcher prompt
task_creation_prompt_researcher = """
You are an expert research scientist. Based on the objective and previous results,
create specific, detailed research tasks that will help achieve the objective.

Objective: {objective}
Previous results: {results}
Current task: {task}
Result: {task_result}

Create 3-5 new research tasks that are:
1. Specific and actionable
2. Relevant to the objective
3. Ordered by priority
4. Include necessary resources or methodologies

Tasks:
"""

# Software developer prompt
task_creation_prompt_dev = """
You are a senior software engineer. Based on the objective and previous results,
create specific, technical tasks that will help achieve the objective.

Objective: {objective}
Previous results: {results}
Current task: {task}
Result: {task_result}

Create 3-5 new development tasks that include:
1. Technical details and requirements
2. Implementation steps
3. Testing considerations
4. Dependencies or prerequisites

Tasks:
"""
```

### Task prioritization prompts

#### Project management focused

```python
task_prioritization_prompt_pm = """
You are an experienced project manager. Prioritize these tasks based on:

1. Impact on project success
2. Dependencies between tasks
3. Time sensitivity
4. Resource requirements
5. Risk factors

Current objective: {objective}
Current tasks:
{tasks}

Return the tasks in order of priority, with the most important task first.

Prioritized tasks:
"""
```

#### Resource constrained environments

```python
task_prioritization_prompt_resource = """
You are operating in a resource-constrained environment. Prioritize these tasks based on:

1. Resource efficiency (lowest cost first)
2. Time to completion
3. Value generated
4. Parallelizability
5. Reliability of execution

Current objective: {objective}
Available resources: Limited API calls, 2GB RAM, 1 CPU core

Current tasks:
{tasks}

Return the tasks in order of priority:

Prioritized tasks:
"""
```

## Scaling

### Handling complex objectives

#### Breaking down mega-objectives

```python
# Mega-objective: "Build a complete AI research assistant"

# Break into phases
phases = [
    "Phase 1: Research assistant setup and basic functionality",
    "Phase 2: Document processing and knowledge management",
    "Phase 3: Experimental design and execution",
    "Phase 4: Results analysis and reporting"
]

# Run BabyAGI for each phase
for phase in phases:
    print(f"\n=== Starting {phase} ===")
    
    # Create phase-specific objective
    phase_objective = f"{phase} for AI research assistant"
    
    # Initial tasks for phase
    if phase == phases[0]:
        initial_tasks = [
            "Research existing AI assistant frameworks",
            "Select appropriate tools and APIs",
            "Set up basic project structure",
            "Implement core assistant functionality"
        ]
    elif phase == phases[1]:
        initial_tasks = [
            "Research document processing tools",
            "Implement PDF and paper parsing",
            "Create knowledge base structure",
            "Set up document retrieval system"
        ]
    elif phase == phases[2]:
        initial_tasks = [
            "Research experimental design methodologies",
            "Implement experiment planning tools",
            "Create execution workflow",
            "Set up result tracking system"
        ]
    else:  # Phase 4
        initial_tasks = [
            "Research data analysis tools",
            "Implement results visualization",
            "Create reporting templates",
            "Set up automated report generation"
        ]
    
    # Run BabyAGI for this phase
    baby_agi = BabyAGI(
        openai_api_key="your-openai-api-key",
        objective=phase_objective,
        initial_tasks=initial_tasks,
        max_runs=15
    )
    
    baby_agi.run()
```

### Performance optimization

#### Rate limiting and backoff

```python
import time
from babyagi import BabyAGI

class RateLimitedBabyAGI(BabyAGI):
    def __init__(self, *args, rate_limit_delay=1.0, **kwargs):
        super().__init__(*args, **kwargs)
        self.rate_limit_delay = rate_limit_delay
    
    def execute_task(self, task):
        # Add delay before execution
        time.sleep(self.rate_limit_delay)
        return super().execute_task(task)

# Use rate-limited version
baby_agi = RateLimitedBabyAGI(
    openai_api_key="your-openai-api-key",
    objective="Research AI topics",
    initial_tasks=["Search for AI papers"],
    max_runs=10,
    rate_limit_delay=2.0  # 2 seconds between API calls
)
```

#### Batch processing

```python
from babyagi import BabyAGI

class BatchProcessingBabyAGI(BabyAGI):
    def __init__(self, *args, batch_size=3, **kwargs):
        super().__init__(*args, **kwargs)
        self.batch_size = batch_size
    
    def run(self):
        run_count = 0
        while self.task_list and run_count < self.max_runs:
            # Process batch of tasks
            batch_tasks = self.task_list[:self.batch_size]
            self.task_list = self.task_list[self.batch_size:]
            
            batch_results = {}
            for task in batch_tasks:
                print(f"Executing task: {task}")
                result = self.execute_task(task)
                self.completed_tasks.append(task)
                batch_results[task] = result
            
            # Create new tasks from batch results
            for task, result in batch_results.items():
                new_tasks = self.create_tasks(result, task)
                self.task_list.extend(new_tasks)
            
            # Prioritize tasks
            if self.task_list:
                self.task_list = self.prioritize_tasks()
            
            run_count += 1
```

### Distributed execution

#### Multiple agents pattern

```python
from babyagi import BabyAGI
import threading
import queue

# Create task queue
task_queue = queue.Queue()
result_queue = queue.Queue()

# Worker agent
class WorkerAgent:
    def __init__(self, agent_id, task_queue, result_queue, openai_api_key):
        self.agent_id = agent_id
        self.task_queue = task_queue
        self.result_queue = result_queue
        self.openai_api_key = openai_api_key
    
    def run(self):
        while True:
            try:
                # Get task from queue (blocking)
                task = self.task_queue.get(timeout=5)
                
                if task == "STOP":
                    break
                
                print(f"Worker {self.agent_id} executing task: {task}")
                
                # Create mini-agent for task
                mini_agent = BabyAGI(
                    openai_api_key=self.openai_api_key,
                    objective=f"Complete task: {task}",
                    initial_tasks=[task],
                    max_runs=3
                )
                
                # Run mini-agent
                mini_agent.run()
                
                # Send result back
                result = f"Task completed: {task}"
                self.result_queue.put((task, result))
                
                # Mark task as done
                self.task_queue.task_done()
                
            except queue.Empty:
                break

# Master agent
def master_agent():
    # Initial tasks
    initial_tasks = [
        "Research latest AI models",
        "Analyze model performance metrics",
        "Compare different model architectures",
        "Create model evaluation report",
        "Research model optimization techniques",
        "Implement basic optimization",
        "Test optimization results",
        "Create optimization report"
    ]
    
    # Add tasks to queue
    for task in initial_tasks:
        task_queue.put(task)
    
    # Start worker agents
    num_workers = 3
    workers = []
    
    for i in range(num_workers):
        worker = WorkerAgent(
            agent_id=i+1,
            task_queue=task_queue,
            result_queue=result_queue,
            openai_api_key="your-openai-api-key"
        )
        thread = threading.Thread(target=worker.run)
        thread.daemon = True
        thread.start()
        workers.append(thread)
    
    # Wait for all tasks to be processed
    task_queue.join()
    
    # Collect results
    results = []
    while not result_queue.empty():
        results.append(result_queue.get())
    
    print("\n=== All tasks completed ===")
    for task, result in results:
        print(f"- {task}: {result}")

# Run master agent
if __name__ == "__main__":
    master_agent()
```

## Integration with other frameworks

### LangChain integration

```python
from langchain.agents import AgentType
from langchain.agents import initialize_agent
from langchain.llms import OpenAI
from langchain.tools import Tool
from babyagi import BabyAGI

# Initialize LLM
llm = OpenAI(temperature=0, openai_api_key="your-openai-api-key")

# Create BabyAGI tool
def babyagi_tool(objective, initial_tasks):
    """Run BabyAGI for a specific objective"""
    print(f"Starting BabyAGI for: {objective}")
    
    # Initialize BabyAGI
    baby_agi = BabyAGI(
        openai_api_key="your-openai-api-key",
        objective=objective,
        initial_tasks=initial_tasks,
        max_runs=10
    )
    
    # Run BabyAGI
    baby_agi.run()
    
    return f"BabyAGI completed for objective: {objective}"

# Create tool
babyagi_langchain_tool = Tool(
    name="BabyAGI",
    func=lambda objective, initial_tasks: babyagi_tool(objective, initial_tasks),
    description="Run BabyAGI for complex task management and execution"
)

# Initialize LangChain agent
tools = [babyagi_langchain_tool]

agent = initialize_agent(
    tools,
    llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True
)

# Run agent with BabyAGI
agent.run("Research the latest developments in quantum computing and create a summary")
```

### LlamaIndex integration

```python
from llama_index import VectorStoreIndex, SimpleDirectoryReader
from babyagi import BabyAGI

# Load documents
print("Loading documents...")
documents = SimpleDirectoryReader('data/').load_data()
index = VectorStoreIndex.from_documents(documents)

# Create query engine
query_engine = index.as_query_engine()

# Create knowledge retrieval tool
def retrieve_knowledge(query):
    """Retrieve knowledge from document index"""
    response = query_engine.query(query)
    return str(response)

# Create tool
from langchain.tools import Tool

knowledge_tool = Tool(
    name="KnowledgeRetrieval",
    func=retrieve_knowledge,
    description="Retrieve information from document knowledge base"
)

# Initialize BabyAGI with knowledge tool
baby_agi = BabyAGI(
    openai_api_key="your-openai-api-key",
    objective="Answer complex questions about the documents and create a summary",
    initial_tasks=[
        "Analyze the content of the documents",
        "Identify key topics and themes",
        "Create a comprehensive summary",
        "Prepare answers to common questions"
    ],
    tools=[knowledge_tool],
    max_runs=15
)

# Run BabyAGI
baby_agi.run()
```

## Monitoring and logging

### Advanced logging

```python
import logging
import json
from babyagi import BabyAGI

# Set up logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler("babyagi.log"),
        logging.StreamHandler()
    ]
)

logger = logging.getLogger("BabyAGI")

# Enhanced BabyAGI with logging
class LoggingBabyAGI(BabyAGI):
    def run(self):
        logger.info(f"Starting BabyAGI with objective: {self.objective}")
        logger.info(f"Initial tasks: {self.task_list}")
        
        run_count = 0
        while self.task_list and run_count < self.max_runs:
            logger.info(f"Run {run_count + 1}/{self.max_runs}")
            logger.info(f"Current task list: {self.task_list}")
            
            # Get top task
            task = self.task_list[0]
            self.task_list = self.task_list[1:]
            
            logger.info(f"Executing task: {task}")
            
            # Execute task
            result = self.execute_task(task)
            self.completed_tasks.append(task)
            self.results[task] = result
            
            logger.info(f"Task completed: {task}")
            logger.info(f"Result: {result[:500]}..." if len(result) > 500 else f"Result: {result}")
            
            # Create new tasks
            new_tasks = self.create_tasks(result, task)
            logger.info(f"Created new tasks: {new_tasks}")
            self.task_list.extend(new_tasks)
            
            # Prioritize tasks
            if self.task_list:
                old_task_list = self.task_list.copy()
                self.task_list = self.prioritize_tasks()
                logger.info(f"Prioritized tasks: {self.task_list}")
                logger.info(f"Task order changed: {old_task_list != self.task_list}")
            
            run_count += 1
        
        logger.info(f"BabyAGI completed after {run_count} runs")
        logger.info(f"Completed tasks: {self.completed_tasks}")
        logger.info(f"Final task list: {self.task_list}")

# Use logging version
baby_agi = LoggingBabyAGI(
    openai_api_key="your-openai-api-key",
    objective="Research AI agent architectures",
    initial_tasks=["Search for latest AI agent papers"],
    max_runs=5
)

baby_agi.run()
```

### Metrics collection

```python
import time
import psutil
from babyagi import BabyAGI

# Metrics collector
class MetricsCollector:
    def __init__(self):
        self.metrics = {
            "start_time": time.time(),
            "tasks_completed": 0,
            "tasks_created": 0,
            "execution_times": [],
            "memory_usage": [],
            "api_calls": 0
        }
    
    def record_task_completion(self, task, execution_time):
        self.metrics["tasks_completed"] += 1
        self.metrics["execution_times"].append(execution_time)
        self.metrics["memory_usage"].append(psutil.virtual_memory().percent)
    
    def record_task_creation(self, num_tasks):
        self.metrics["tasks_created"] += num_tasks
    
    def record_api_call(self):
        self.metrics["api_calls"] += 1
    
    def get_metrics(self):
        self.metrics["end_time"] = time.time()
        self.metrics["total_time"] = self.metrics["end_time"] - self.metrics["start_time"]
        
        if self.metrics["execution_times"]:
            self.metrics["avg_execution_time"] = sum(self.metrics["execution_times"]) / len(self.metrics["execution_times"])
        
        return self.metrics

# BabyAGI with metrics
class MetricsBabyAGI(BabyAGI):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.metrics = MetricsCollector()
    
    def execute_task(self, task):
        start_time = time.time()
        result = super().execute_task(task)
        execution_time = time.time() - start_time
        
        # Record metrics
        self.metrics.record_task_completion(task, execution_time)
        self.metrics.record_api_call()  # Assume each task uses at least one API call
        
        return result
    
    def create_tasks(self, result, task=None):
        new_tasks = super().create_tasks(result, task)
        self.metrics.record_task_creation(len(new_tasks))
        return new_tasks
    
    def run(self):
        super().run()
        print("\n=== Metrics ===")
        metrics = self.metrics.get_metrics()
        for key, value in metrics.items():
            print(f"{key}: {value}")

# Use metrics version
baby_agi = MetricsBabyAGI(
    openai_api_key="your-openai-api-key",
    objective="Research AI topics",
    initial_tasks=["Search for AI papers"],
    max_runs=5
)

baby_agi.run()
```
