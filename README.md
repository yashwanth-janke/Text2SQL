# 🔄 Text2SQL: Natural Language to SQL Query Generator

An intelligent system that converts natural language questions into SQL queries using Large Language Models (LLMs) and retrieval-augmented generation (RAG). This project demonstrates how to build a production-ready text-to-SQL system with conversation memory, few-shot learning, and dynamic table selection.

## 🌟 Features

- **🤖 Natural Language Processing**: Convert plain English questions to SQL queries using OpenAI GPT-3.5-turbo
- **🎯 Smart Table Selection**: Automatically identifies relevant database tables based on user questions
- **📚 Few-Shot Learning**: Uses semantic similarity to select the most relevant examples for better query generation
- **💭 Conversation Memory**: Maintains context across multiple questions for follow-up queries
- **🛡️ Secure Configuration**: Environment-based credential management with proper secret handling
- **🔍 Vector-Based Example Selection**: ChromaDB integration for intelligent example retrieval
- **📊 Database Integration**: MySQL database connectivity with comprehensive table metadata

## 🏗️ Architecture

```
User Question → Table Selection → Few-Shot Examples → LLM → SQL Query → Execution → Natural Language Answer
```

### Key Components:
1. **Dynamic Table Selection**: Uses Pydantic models to extract relevant tables
2. **Semantic Example Selection**: ChromaDB + OpenAI embeddings for finding similar examples
3. **Prompt Engineering**: Structured prompts with system instructions and examples
4. **Query Execution**: Safe SQL execution with result formatting
5. **Answer Generation**: Natural language responses from query results

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- MySQL database
- OpenAI API key

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yashwanth-janke/Text2SQL.git
   cd Text2SQL
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Set up environment variables**
   Create a `.env` file in the project root:
   ```env
   # Database Configuration
   DB_USER=your_db_user
   DB_PASSWORD=your_db_password
   DB_HOST=localhost
   DB_NAME=classicmodels
   
   # OpenAI API Configuration
   OPENAI_API_KEY=your_openai_api_key
   
   # Optional: LangChain Tracing
   LANGCHAIN_TRACING_V2=true
   LANGCHAIN_API_KEY=your_langchain_api_key
   ```

4. **Prepare your database**
   - Ensure your MySQL database is running
   - Update `database_table_descriptions.csv` with your table information

## 💻 Usage

### Basic Query Generation
```python
from langchain_openai import ChatOpenAI
from langchain.chains import create_sql_query_chain

# Initialize the system
llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0)
chain = create_sql_query_chain(llm, db)

# Generate SQL query
query = chain.invoke({"question": "How many customers are there?"})
print(query)
```

### Complete Pipeline with Answer Generation
```python
# Full chain: Question → SQL → Execution → Natural Language Answer
response = chain.invoke({
    "question": "How many customers have an order count greater than 5?",
    "messages": history.messages
})
print(response)
```

### Interactive Session with Memory
```python
# Maintain conversation context
history = ChatMessageHistory()

# First question
response1 = chain.invoke({"question": "How many customers with order count more than 5"})
history.add_user_message("How many customers with order count more than 5")
history.add_ai_message(response1)

# Follow-up question (uses previous context)
response2 = chain.invoke({
    "question": "Can you list their names?", 
    "messages": history.messages
})
```

## 📋 Database Schema

The system works with the ClassicModels database containing:

| Table | Description |
|-------|-------------|
| `customers` | Customer information, contact details, credit limits |
| `orders` | Order information with dates, status, and customer references |
| `orderdetails` | Detailed product information for each order |
| `products` | Product catalog with names, descriptions, prices |
| `productlines` | Product categories and classifications |
| `employees` | Employee data and organizational hierarchy |
| `offices` | Office locations and territory assignments |
| `payments` | Customer payment transaction records |

## 🧠 How It Works

### 1. Table Selection
The system analyzes your question and automatically selects relevant tables:
```python
# Extract relevant tables based on question context
tables = table_chain.invoke({"input": "give me details of customer and their order count"})
# Returns: ['customers', 'orders']
```

### 2. Few-Shot Learning
Uses semantic similarity to find the most relevant examples:
```python
example_selector = SemanticSimilarityExampleSelector.from_examples(
    examples,
    OpenAIEmbeddings(),
    vectorstore,
    k=2,  # Select top 2 most similar examples
    input_keys=["input"],
)
```

### 3. Query Generation
Combines table schema, examples, and user question to generate SQL:
```python
final_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a MySQL expert..."),
    few_shot_prompt,
    ("human", "{input}"),
])
```

## 🛠️ Configuration

### Example Configuration in Notebook
The system supports various configuration options:
- **Model Selection**: Switch between different OpenAI models
- **Temperature Settings**: Control randomness in query generation  
- **Few-Shot Examples**: Customize training examples for your domain
- **Table Filtering**: Include/exclude specific tables

### Adding Custom Examples
```python
examples = [
    {
        "input": "List all customers in France with a credit limit over 20,000.",
        "query": "SELECT * FROM customers WHERE country = 'France' AND creditLimit > 20000;"
    },
    # Add your domain-specific examples
]
```

## 🔒 Security

- ✅ **Environment Variables**: All sensitive data stored in `.env` files
- ✅ **Git Ignore**: Credentials excluded from version control  
- ✅ **API Key Protection**: OpenAI keys secured with environment variables
- ✅ **Input Validation**: Structured prompts prevent SQL injection

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **LangChain**: For the powerful LLM orchestration framework
- **OpenAI**: For the GPT-3.5-turbo model and embeddings
- **ChromaDB**: For vector storage and semantic search capabilities
- **MySQL**: For the robust database backend

## 📞 Contact

**Yashwanth Janke**
- GitHub: [@yashwanth-janke](https://github.com/yashwanth-janke)
- Email: yashwanthjanke@gmail.com

---

⭐ **Star this repository if you found it helpful!**
