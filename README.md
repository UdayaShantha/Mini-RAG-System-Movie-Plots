# Mini RAG System - Movie Plots

A lightweight Retrieval-Augmented Generation (RAG) system that answers questions about movie plots using the Wikipedia Movie Plots dataset.

## Overview

This system demonstrates a complete RAG pipeline:
1. **Data Loading**: Loads and preprocesses movie plot data
2. **Chunking**: Splits long plots into manageable chunks (~300 words)
3. **Embedding**: Converts text chunks into vector embeddings using Sentence Transformers
4. **Vector Store**: Stores embeddings in FAISS for efficient similarity search
5. **Retrieval**: Finds most relevant plot chunks for a given query
6. **Generation**: Uses an LLM to generate natural language answers with retrieved context

## Features

- ✅ Processes 200-500 movies from Wikipedia Movie Plots dataset
- ✅ In-memory FAISS vector store for fast retrieval
- ✅ Sentence-BERT embeddings (all-MiniLM-L6-v2)
- ✅ OpenAI GPT integration for answer generation
- ✅ Structured JSON output with answer, contexts, and reasoning
- ✅ Interactive query mode
- ✅ Fallback method if no OpenAI API key provided

## Installation

### Prerequisites
- Python 3.8+
- pip

### Setup

1. Clone this repository:
```bash
git clone <your-repo-url>
cd movie-plot-rag
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Download the dataset:
   - Visit [Wikipedia Movie Plots on Kaggle](https://www.kaggle.com/datasets/jrobischon/wikipedia-movie-plots)
   - Download `wiki_movie_plots_deduped.csv`
   - Place it in the project root directory

4. (Optional) Set OpenAI API key:
```bash
export OPENAI_API_KEY='your-api-key-here'
```

## Usage

### Basic Usage

Run the main script:
```bash
python rag_system.py
```

This will:
- Load 300 movies from the dataset
- Build the vector store
- Run example queries
- Save results to `rag_results.json`
- Enter interactive mode for custom queries

### Using Without OpenAI API

If you don't have an OpenAI API key, the system will use a fallback method that returns the most relevant plot excerpt. This still demonstrates the RAG pipeline (retrieval works perfectly, just simplified generation).

### Example Output

```json
{
  "query": "What movie features an artificial intelligence system?",
  "result": {
    "answer": "The movie '2001: A Space Odyssey' features an artificial intelligence system called HAL 9000, which becomes antagonistic during a space mission.",
    "contexts": [
      "2001: A Space Odyssey: The HAL 9000 computer becomes antagonistic toward the astronauts during their journey to Jupiter...",
      "Ex Machina: A programmer is invited to test an advanced AI with human-level consciousness..."
    ],
    "reasoning": "Searched through 847 plot chunks. Retrieved top 3 most relevant contexts from movies: 2001: A Space Odyssey, Ex Machina, Her. Used these contexts to form an answer."
  }
}
```

## Architecture

```
Query → Embedding → Vector Search (FAISS) → Top-K Chunks → LLM → Structured Answer
                                                ↓
                                          Vector Store
                                          (Movie Plots)
```

## Key Components

### 1. MoviePlotRAG Class
Main class that orchestrates the RAG pipeline.

### 2. Embedding Model
Uses `all-MiniLM-L6-v2` from Sentence Transformers:
- Fast and efficient
- 384-dimensional embeddings
- Good for semantic similarity

### 3. Vector Store
FAISS (Facebook AI Similarity Search):
- In-memory index
- L2 distance metric
- Efficient for datasets of this size

### 4. LLM Integration
OpenAI GPT-3.5-turbo:
- Generates natural language answers
- Uses retrieved contexts
- Fallback to simple extraction if API unavailable

## Project Structure

```
movie-plot-rag/
├── rag_system.py              # Main RAG implementation
├── requirements.txt           # Python dependencies
├── README.md                  # This file
├── wiki_movie_plots_deduped.csv  # Dataset (download separately)
└── rag_results.json          # Output file (generated)
```

## Configuration

You can modify these parameters in the `main()` function:

- `num_movies`: Number of movies to load (default: 300)
- `chunk_size`: Words per chunk (default: 300)
- `top_k`: Number of contexts to retrieve (default: 3)
- `embedding_model`: Change to different Sentence Transformer model
- `llm_model`: Change OpenAI model (default: gpt-3.5-turbo)

## Example Queries

Try these questions:
- "What movie features an artificial intelligence system?"
- "Tell me about a movie with time travel"
- "Which movie has a character named HAL 9000?"
- "What movies involve space exploration?"
- "Tell me about a thriller with a twist ending"

## How It Works

1. **Loading**: Reads CSV, extracts Title and Plot columns, takes first 300 movies
2. **Chunking**: Splits each plot into ~300 word chunks to manage context size
3. **Embedding**: Each chunk is converted to a 384-dim vector using Sentence-BERT
4. **Indexing**: All vectors stored in FAISS for efficient similarity search
5. **Query**: User question is embedded using same model
6. **Retrieval**: FAISS finds top-3 most similar chunks (lowest L2 distance)
7. **Generation**: Retrieved chunks + query sent to LLM to generate answer
8. **Output**: Structured JSON with answer, contexts used, and reasoning

## Limitations

- In-memory only (no persistence)
- Limited to 300-500 movies for demo purposes
- Simple chunking strategy (fixed word count)
- No re-ranking or advanced retrieval techniques
- Requires OpenAI API for best results (has fallback)

## Future Improvements

- Add persistent storage (save FAISS index)
- Implement hybrid search (keyword + semantic)
- Add re-ranking layer
- Support other LLM providers (Anthropic, HuggingFace)
- Add evaluation metrics (answer quality, retrieval accuracy)
- Implement streaming responses

## Troubleshooting

**Issue**: ModuleNotFoundError
- **Solution**: Run `pip install -r requirements.txt`

**Issue**: Dataset not found
- **Solution**: Download `wiki_movie_plots_deduped.csv` from Kaggle and place in project root

**Issue**: OpenAI API errors
- **Solution**: Check API key is set correctly or run without it (uses fallback)

**Issue**: Out of memory
- **Solution**: Reduce `num_movies` parameter in main()

## Contact

For questions about this implementation, please open an issue on GitHub.

---

**Note**: This is a minimal demonstration RAG system designed for educational purposes and technical interviews. Production systems would require additional features like error handling, logging, monitoring, and scalability considerations.
