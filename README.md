#MainPlan_autoGPT

The 90-day development plan for building an AI-powered full-stack web application targeting the automobile industry. This includes a user-friendly chatbot with RAG capabilities, AI agents for part diagram generation, process planning, and digital twin simulation.

The plan will cover:

* Core features (signup/login, vector DB setup, cloud deployment)
* Backend with Python + FastAPI, frontend with React
* DevOps setup using GitLab, Docker, and AWS Free Tier
* AI agent integration planning
* A landing page for lead collection and beta access

I’ll structure the plan by day and week, and highlight major development milestones and dependencies. I’ll let you know when it’s ready for your review.


# 90-Day Roadmap for Building an AI-Powered Automotive Chatbot

**Goal:** Develop a full-stack AI chatbot MVP for the automobile industry within 90 days, using Retrieval-Augmented Generation (RAG) on \~300 automotive PDF books. The chatbot will answer user queries using this data and demonstrate advanced AI agent features (basic CAD diagram generation, process planning assistance, and rudimentary digital twin simulations). The stack will include a Python FastAPI backend, a React frontend, Docker for containerization, PostgreSQL (with vector search capabilities for embeddings), and deployment on AWS Free Tier. GitLab will be used for version control and CI/CD. We assume a **\$100 budget**, so we’ll leverage free-tier resources and open-source tools wherever possible.

Below is a **day-by-day plan** organized into three phases (one per month). Each phase outlines the focus, milestone features, and daily tasks across backend, frontend, model/AI development, DevOps, and launch prep. The plan is designed for a solo founder with strong technical skills, aiming to build an impressive MVP (suitable for a future YC application) in 90 days.

## Phase 1: Month 1 (Days 1–30) – Setup & Core Chatbot Development

*Focus:* Laying the groundwork – project planning, environment setup, and building the core RAG-based Q\&A chatbot using the PDF knowledge base. By the end of Day 30, the chatbot should answer automotive questions from the PDFs via a basic interface (no advanced agent features yet). **Milestone (Day 30):** A working backend that can ingest PDFs into a vector database and answer user queries with relevant info from those documents.

* **Day 1:** *Project Setup & Planning* – Initialize a GitLab repository and set up project structure for a **FastAPI** backend and **React** frontend. Plan out key components and how they interact. For example, decide on using RAG for Q\&A: the chatbot will extract knowledge from PDFs by chunking text, embedding those chunks, and using a vector store to retrieve relevant pieces for the LLM. Research existing implementations (e.g. a PDF chatbot using LangChain, FAISS, etc.) for guidance. Also, sketch a high-level architecture: how the frontend will communicate with the backend (REST API endpoints for chat and for email sign-up), and how the backend will perform retrieval and call the LLM.

* **Day 2:** *Development Environment Setup* – Set up the development environments. Create a Python virtual environment and install FastAPI and essential libraries (like Uvicorn for ASGI server). Initialize a React project (using Create React App or Vite) for the frontend. Install Docker on the local machine and start writing a **Dockerfile** for the backend (this will help later with deployment). Ensure GitLab version control is in place: commit the initial project structure and set up branch protections if needed. Also, set up a basic **PostgreSQL** database locally (could be via Docker or a local install) – this will later store user emails and possibly be used for vector embeddings via the pgvector extension.

* **Day 3:** *AWS Free Tier Planning* – Research AWS Free Tier resources and make an AWS account if not already. Note that AWS Free Tier provides **750 hours/month of a t2.micro EC2 instance and 750 hours of a t2.micro RDS instance free for 12 months**, which covers a small server and database for the MVP. Plan to use a single EC2 instance to host the FastAPI app (and maybe Postgres if not using RDS). Keep cloud costs minimal – likely under the free tier, aside from perhaps \~\$10 for a domain name and some usage of external APIs (like OpenAI) within the \$100 budget.

* **Day 4:** *FastAPI Scaffold* – Implement a simple FastAPI app with a health-check endpoint (e.g., `/ping`) to verify the server runs. Configure CORS so that the React frontend can call the API. Write a dummy endpoint for chat (e.g., POST `/chat/query`) that just echoes back the question for now, to test the request/response flow. This will later be replaced with actual logic. Test running the FastAPI app locally (Uvicorn on port 8000).

* **Day 5:** *React Frontend Scaffold* – Set up the React app structure. Implement a basic UI with a homepage that will act as the landing page. Also create a placeholder for the chatbot interface (could be a `/chat` route or a component on the homepage for demo purposes). At minimum, add an input box and a submit button that sends a test query to the backend’s dummy `/chat/query` endpoint. For design speed, consider using a UI library like **Chakra UI or Material-UI** to get pre-styled components. Test the end-to-end connection: from the browser, hit the FastAPI health endpoint and dummy chat endpoint.

* **Day 6:** *PDF Data Ingestion Research* – Research how to ingest and process PDF books in Python. Likely use libraries like **PyMuPDF (fitz)** or **PDFMiner** to extract text from PDFs. Plan to break documents into chunks (e.g., \~500 tokens each) to fit the context window of the LLM. Each chunk will be embedded into a vector. Evaluate tools like **LangChain** or **LlamaIndex** for managing this process versus writing from scratch. (LangChain can simplify document loading, splitting, embedding, and querying, though it’s possible to do manually.) Given the budget, leaning on open-source libraries is wise – for example, LangChain is free and can work with local models, and **FAISS or Chroma** can be used as the vector store in-memory or on disk.

* **Day 7:** *Embedding & Vector DB Setup* – Decide on the embedding model and vector database. Two approaches: use OpenAI’s API for embeddings (e.g., `text-embedding-ada-002`) or use a local model from HuggingFace (like `sentence-transformers/all-MiniLM-L6-v2`). To conserve budget, using a local embedding model is preferred (the MiniLM-L6-v2 model is small and can run on CPU). Install the chosen embedding model or set up OpenAI API keys if using their service. For the vector store, set up **ChromaDB** (an open-source vector DB) or integrate **FAISS** in Python for similarity search – these allow storing text embeddings and querying by similarity. Alternatively, consider using Postgres with the pgvector extension to store embeddings, which would simplify infrastructure (embedding vectors can be stored and queried in PostgreSQL itself). Since we have Postgres in our stack, using pgvector is a practical option to avoid running a separate vector DB service.

* **Day 8:** *PDF Text Extraction* – Write a script (or Jupyter notebook) to extract text from a sample PDF (choose one automotive PDF as a test). Use PyMuPDF or a similar library to parse the PDF into raw text. Implement a simple text **chunking** strategy: for example, split text by chapters/sections or fixed window sizes (e.g., 300-500 words per chunk) to ensure each chunk can later fit in the LLM’s prompt context. Clean the text (remove headers, footers, page numbers if possible) to avoid junk in the knowledge base. Verify the extraction by printing some chunks to ensure text integrity.

* **Day 9:** *Embedding and Indexing Test* – Take the chunks from Day 8 and generate embeddings for them using the chosen model. If using a local model (MiniLM), use the SentenceTransformers library to encode texts into vectors. If using OpenAI API, call the embedding endpoint (be mindful of token counts). Store these vectors in the vector database (Chroma or FAISS index in memory, or in Postgres with pgvector). Implement a simple similarity search function: given a sample query, encode it to a vector and find the top-N most similar chunks. Test this retrieval by asking a question that you know is answered in the test PDF (e.g., if the PDF is an automotive manual, a question like “What is the engine oil capacity?”). Ensure the relevant chunk is returned. This verifies that the **RAG pipeline** backbone (embed → store → retrieve by similarity) works.

* **Day 10:** *LLM Q\&A Integration (Prototype)* – With retrieval working, integrate a language model to form answers. For now, possibly use a quick solution like OpenAI’s GPT-3.5 via API (since it can be accessed easily and handle instructions to use retrieved context) – include the retrieved text chunks in the prompt, asking the model to answer using that information. Alternatively, test with a smaller open-source model (like GPT4All or local LLaMA 2 7B) to avoid API costs, though those might be less accurate. Implement this in a script or within the FastAPI backend: a function that takes a user query, performs the steps (embed query, similarity search, compose prompt with top chunks, get LLM answer). Test end-to-end in the backend with a hardcoded query to see if a reasonable answer comes out using the context. This is the heart of the chatbot’s brain: **Retrieval-Augmented Generation** where the LLM can use external data (the PDFs) to answer questions.

* **Day 11:** *Backend Q\&A API* – Now that the core logic is tested, create a proper API endpoint for the chatbot Q\&A. For example, implement POST `/chat/query` in FastAPI that accepts a user question and returns the answer. Inside this endpoint:

  1. Vectorize the question using the same embedding model.
  2. Query the vector store for similar content (e.g., top 3-5 chunks).
  3. Construct a prompt for the LLM that includes the user question and the retrieved text (perhaps as a context or quoted source material).
  4. Call the LLM (OpenAI API or local model) to generate an answer.
  5. Return the answer (and possibly the sources, if we plan to show them).

  Also handle basic error cases (e.g., if no relevant chunk found, respond with “I’m not sure, I couldn’t find that information”). At this stage, keep it simple – the main goal is to return an answer string.

* **Day 12:** *Testing Q\&A Pipeline* – Rigorously test the Q\&A endpoint with various queries related to the PDF content. Use both exact questions (where answers are definitely in the PDFs) and slightly open-ended ones to see how the system performs. Check for accuracy and any hallucinations. If the LLM is hallucinating (making up answers not in docs), consider strategies: e.g., ensure prompt clearly says “use the provided context only” or experiment with a smaller temperature. If using an open-source model with limited knowledge, consider temporarily using the OpenAI API for better quality answers in the MVP (within budget). The retrieval should mitigate major misses by grounding the answers in the PDFs. Note any slow performance issues (embedding + LLM call latency) – these might be fine for MVP but keep an eye on them.

* **Day 13:** *Scale up PDF Ingestion (Begin)* – With one PDF working, scale up to all \~300 automotive PDFs. This is a significant data ingestion task. Write a script to iterate through all PDF files, extract text, chunk it, and create embeddings. This could run for a long time if done sequentially, so plan accordingly:

  * Option 1: Use OpenAI embeddings for all text (fast, but cost must be calculated – embedding models like Ada cost \~\$0.0001–\$0.0004 per 1K tokens, so 300 books \* perhaps 1M tokens each could be \~\$30-120, which may be too high for the \$100 budget).
  * Option 2: Use the local MiniLM model to embed on your own machine or a beefier instance temporarily. This costs time, not money. Possibly run it overnight or in parallel batches.

  If using local embedding, consider using multiple processes or threads to speed it up (but be mindful of memory). If using OpenAI for embedding, implement in batches and monitor cost usage to not exceed budget (maybe start with a subset of books first).

  Begin the ingestion process today, perhaps targeting a subset (say 50 PDFs) to ensure the pipeline works at scale.

* **Day 14:** *Continue PDF Processing* – This day will likely continue the embedding job for the remaining PDFs. As chunks and embeddings are generated, continually add them to the vector database. If using Postgres with pgvector, make sure to commit regularly to avoid data loss and ensure the index is maintained for similarity queries. If using Chroma or FAISS, they may handle incremental adds in memory – ensure you persist the index to disk after building (Chroma does this by default to a local DB file, FAISS index can be saved to a file). Check that storage size is within reason (embedding 300 books might be a few hundred MB of vectors, which should be fine). Keep an eye on any out-of-memory issues; if using a local machine with limited RAM, you might embed in smaller chunks or one book at a time.

* **Day 15:** *Basic Frontend Chat Integration* – While the data ingestion runs or after it’s done, switch to the frontend to integrate the chat interface with the backend Q\&A API. Create a chat widget on the React frontend: a text input for the question and an area to display the conversation (user question and AI answer). On submit, call the FastAPI `/chat/query` endpoint with the user’s question. Show a loading indicator while waiting for a response. Upon receiving the answer, display it in the chat history. At this point, the UI can be rudimentary (plain text bubbles). Ensure CORS is configured correctly and handle any errors (e.g., show an error message if the API call fails). Test the live chat end-to-end: ask something that should be answered from the PDFs and see it appear on the webpage.

* **Day 16:** *Iterate on Answer Formatting* – Improve how answers are presented. If the backend can return source info (like document name or section) along with the answer, consider displaying that (e.g., a “Sources: PDF name” note) to increase credibility. Also, consider limiting answer length or formatting lists if the response is procedural. Since the automotive data might contain technical instructions, ensure the chatbot preserves any structured information (like lists of steps). If answers seem too verbose or too terse, adjust the prompt given to the LLM (e.g., “answer in 3-4 sentences” or “if relevant, list steps as bullet points”). Verify that these adjustments don’t degrade accuracy.

* **Day 17:** *User Feedback Mechanism (Basic)* – Implement a simple way for a user to give feedback on the answer quality (this can be useful later for improvements and looks good for YC showing you care about feedback). Perhaps add thumbs-up/thumbs-down buttons next to answers in the UI. Hook this to an API endpoint (e.g., POST `/feedback`) that logs the feedback (store the question, answer, rating in a small Postgres table). This can be as simple as writing to the database for later analysis. (Analytics and user feedback are part of launch prep; starting them early is good.) This also showcases an “analytics/feedback collection” aspect of the product.

* **Day 18:** *Refinement & Debugging* – By now, the core Q\&A functionality is in place. Use this day to refine and fix bugs:

  * Debug any extraction issues (if some PDFs failed to parse or some text got garbled, attempt a fix or note it as a limitation for now).
  * Address any crashes or edge cases in the chat workflow (e.g., what if a user asks something completely unrelated to cars? The bot should handle gracefully – possibly respond that it can only answer automotive questions, to avoid nonsense).
  * Ensure the system is not overly slow: measure response time for a query. If it’s above, say, 5-10 seconds, consider optimizations: reducing the number of chunks retrieved, or if using OpenAI, enable streaming responses to start showing partial answer (this is an advanced nice-to-have, maybe postpone unless time permits).
  * Confirm that the entire pipeline (embedding model + vector search + LLM) is working on the full dataset now. If any performance bottlenecks (like vector search on many chunks), you might add an index or adjust vector DB parameters (FAISS flat vs HNSW index for faster search, etc.).

* **Day 19:** *Initial DevOps – CI Pipeline Setup* – Set up **GitLab CI/CD** for the repository. Write a `.gitlab-ci.yml` that at least lints/tests the code on each commit. Later we’ll add deployment steps, but start with basics: for backend, run formatting (e.g., black) and perhaps any unit tests you write for utility functions; for frontend, run a build to ensure no compile errors. Configure GitLab runners (use the shared runners or set up a custom runner on your machine if needed). The goal is to catch issues early and ensure reproducible builds. Also add the environment variables (like API keys, etc.) to GitLab CI settings (masked) so that tests/builds can use them if needed. With CI in place, every push will verify that the app can at least build properly.

* **Day 20:** *Review and Milestone Check* – Day 20 is a good point to review progress and ensure Phase 1 is on track. By now:

  * The PDF ingestion should be mostly done (or a plan in place to finish it soon).
  * The core chatbot (Q\&A via RAG) is working locally.
  * Basic UI is connected.

  This is a major milestone: **Core MVP backend functionality achieved**. If any of these are notably behind, allocate time to catch up or simplify (e.g., if embeddings for all 300 PDFs are not done, consider trimming the dataset to the top 100 most important PDFs for now to stay on schedule). Document the milestone: write a short summary of features completed (this can help with a YC application or progress report). Also, verify the budget spend so far – ideally almost \$0 aside from your own time, since we used local resources and free tiers (maybe some OpenAI cost if you tested API, but keep it minimal now).

* **Day 21:** *Advanced Feature Planning* – Now that the base Q\&A is functioning, plan the **AI agent advanced features** to implement in Phase 2. These include:

  * **CAD diagram generation:** The idea is an AI feature where the chatbot can provide a diagram (e.g., of a part or process). True CAD generation from text is complex, but for MVP we can simulate it. Options: have a small library to generate simple SVG diagrams or use an external API. For instance, you might use a text-to-image model (like Stable Diffusion with a specific model for technical drawings) or have a pre-set collection of diagram images to retrieve based on query keywords. Decide on an approach that is feasible in a short time. A promising MVP approach is to use a known tool or library: e.g., use **Blender or OpenSCAD via Python** to programmatically create a basic 3D model from parameters (maybe too advanced), or use a placeholder image and focus on the integration (so the user sees some response for a diagram query).
  * **Process planning copilot:** This is essentially instructing the LLM to output step-by-step plans for tasks (like a procedure or workflow). This can likely be done with prompt engineering – e.g., detect if a query asks “How do I plan…” or contains certain keywords, then respond in a structured, stepwise format. Perhaps have the LLM answer normally but then post-process or re-prompt for a step-by-step solution.
  * **Digital twin simulations:** A digital twin simulates the behavior of a system. For MVP, we can hard-code a simple simulation. For example, simulate a basic car component’s behavior: if user asks “simulate the engine temperature over 10 minutes of runtime,” we could produce a made-up but plausible temperature curve or simple data (even text output like “Temperature starts at 90°C and rises to 110°C in 10 minutes”). This could be done by writing a small Python function that models a trivial linear increase or uses a formula from physics (if any known formulas from the PDFs). The AI agent would call this function and return the result to the user. Essentially, treat it as a **tool** the chatbot can use. Indeed, frameworks like LangChain allow defining tools (Python functions, search, etc.) that an LLM agent can invoke. We might not implement a full agent with dynamic planning in MVP, but we can manually route certain queries to certain functions.

  Today, flesh out the design for these features: define what triggers them and what the output format will be. This plan ensures Phase 2 coding is well-directed.

* **Day 22:** *Tool Integration – CAD Generation (Stub)* – Start implementing the CAD diagram generation feature in a basic form. For instance, create an endpoint or function `generate_cad_diagram(query)` that the chatbot can use. Initially, this function can be a **stub** that returns a placeholder result (like a static image or a simple SVG of a generic part) or a message: “Diagram generation is in progress.” If possible, improve it: maybe parse the query for a part name (like “camshaft diagram”) and if you have an image of a camshaft from the PDFs or internet (ensure license), return that image. Alternatively, use a quick hack: the OpenAI DALL-E or Bing Image Creator (free) could generate an image given a prompt – you might manually try this for a few typical queries and store the images. But due to budget and complexity, a static approach is acceptable for MVP. The key is to show the architecture can handle image outputs. In the React frontend, add logic to display an image if the response includes one (maybe the API returns a URL or base64 image). Test this integration with a dummy query like “Show me a diagram of an engine” and ensure the frontend can display the returned image (even if it’s just a pre-saved diagram). Document that this is an experimental feature – it’s okay if it’s limited, since it’s just MVP-level demonstration.

* **Day 23:** *Tool Integration – Process Planning Copilot* – Implement the process planning assistance. This likely involves prompt engineering rather than external tools. For example, detect queries that imply process planning (“plan”, “steps to”, “how to organize”). You can maintain a list of keywords or a simple classifier. If detected, you can alter the prompt to the LLM: e.g., “The user is asking for a process plan. Provide a step-by-step plan with bullet points.” Alternatively, create a separate function or small chain that always formats the answer as a list of steps. Another idea: use the LLM’s ability to follow system instructions – supply a system prompt that if query type is "process", the assistant should act as a **process planning copilot**, giving structured guidance. Implement this in the backend. Test with an example such as “I need to plan the maintenance process for a fleet of cars” – the output should come as a series of steps (maybe enumerated). Adjust formatting in the frontend for this (maybe preserve newline and bullet formatting from the response). This feature shows the chatbot’s capability as a *copilot* in planning tasks.

* **Day 24:** *Tool Integration – Digital Twin Simulation* – Implement a minimal digital twin simulation tool. Create a function `simulate(parameter)` in Python for a simple scenario. For example, maybe simulate tire wear or battery drain given certain inputs. This could be as simple as a formula or even random generation that yields plausible results. The point is to show that the chatbot can “run a simulation.” For instance, if query contains “simulate” or “predict” with numeric parameters, intercept it: parse out the parameters (this could be done via a regex or by asking the LLM to extract parameters). Then have the function produce a result. Example: user asks “Simulate the battery charge over 1 hour of engine idling.” The function might assume a certain discharge rate and output “After 1 hour, the battery level will drop from 100% to 95%.” The result can be given as text or even a simple chart image (maybe too much for MVP, so text is fine). Integrate this so that the chatbot’s response uses the simulation output. Essentially, the chatbot agent decides to use this tool for such queries. (If using LangChain agent, you’d register this function as a tool and let the LLM decide when to call it. But given time, a simpler rule-based trigger is fine.) Test this with a sample query. This demonstrates a rudimentary **digital twin** capability – albeit very simplistic.

* **Day 25:** *Connect Agent Features to Chat Flow* – Now that we have stubs or basic implementations for the three advanced features (CAD, planning, simulation), integrate them into the main chat workflow. This could be via if/else logic before calling the usual QA flow:

  * e.g., If query asks for a diagram -> call `generate_cad_diagram` and return its result (skip the normal text answer).
  * If query looks like planning -> still use RAG to fetch any relevant info from PDFs that might help, but then instruct the LLM to output a plan.
  * If query asks to simulate -> call the `simulate` function for results and then format that into the answer (the LLM might not even be needed if we trust our simulation output, or we could have the LLM explain the simulation result).

  Ensure that the integration doesn’t break the normal Q\&A flow for other queries. We want the bot to normally answer from PDFs, except in these special cases where extra capabilities are triggered. Do a refactor if needed: it might be good to structure code so that “tools” can be invoked cleanly. (If time permits, using a library like LangChain’s agent with tools could handle this elegantly, as LangChain agents let an LLM choose tools like search, calculations, etc. But implementing a full agent with prompting might be tricky to get right quickly, so manual routing is acceptable for MVP.)

* **Day 26:** *Testing Advanced Features* – Spend this day extensively testing the new features:

  * Ask for a CAD diagram of various things (some that you have an answer for, some that you don’t). See what the response is. If using placeholders, ensure the user understands it’s a placeholder (e.g., watermark the image with “Demo” or include a caption “(Prototype diagram)”).
  * Test process planning queries of different types (maintenance processes, manufacturing steps, etc.). Make sure the responses are coherent and formatted as steps. If the LLM output is not great, tweak the prompt or consider giving it an example format.
  * Test simulation queries with different numbers or conditions. Ensure the parse and function work for various phrasing. The results should be plausible enough not to confuse the user. (It’s okay if it’s not highly accurate – just state it's an estimate in the answer.)

  Also, test that normal questions still work (the new logic doesn’t interfere with regular Q\&A). Fix any bugs (e.g., maybe a query that looks like one of these triggers but shouldn’t, etc.).

* **Day 27:** *Frontend Updates for New Features* – Update the React frontend to handle the outputs from the advanced features gracefully:

  * If the response contains an image (for CAD diagrams), ensure the chat UI can display an image message. You might create a separate message component for images vs text.
  * If the response is a list of steps (process plan), ensure the newlines or markdown bullets are preserved. You might need to render the text as Markdown or at least handle line breaks.
  * If the response indicates a simulation result (possibly numerical data), consider highlighting it or formatting it differently (maybe use a monospaced font or a simple table if multiple values).

  Add small UI indicators if needed, e.g., an icon or label “Simulated Result” or “Diagram” to make it clear the bot did something extra. Keep the design clean and not too cluttered – the core is still a chat interface. Use CSS to differentiate user vs bot messages (different background colors, etc. for readability).

* **Day 28:** *PostgreSQL Integration* – Up to now, we may have been using an in-memory vector store and possibly not persisting chat data. Now integrate PostgreSQL properly:

  * If using Postgres with pgvector for embeddings, ensure all embeddings from PDF ingestion are saved in a table (with columns for vector, document id, text chunk, etc.). If those were in memory, this is the time to batch insert them into Postgres (if not already done).
  * Create tables for user data: e.g., a table for `emails` collected from signups, a table for `feedback` (from Day 17), and maybe a table for `chat_logs` if we want to record user queries and responses (useful for debugging and improving, but be mindful of PII if any – here it’s just support questions, likely fine).
  * Write the SQLAlchemy models or use raw SQL in FastAPI for these. Test inserting a sample email and feedback to confirm the DB setup. This will be important when we launch (to have persistence).

  Additionally, set up a development Postgres instance on AWS or locally that mirrors production (maybe use Docker Postgres image for local dev to mimic the RDS that will be used in production).

* **Day 29:** *Security & Configs* – Review security basics before going live:

  * Remove any hard-coded secrets from code (use environment variables for API keys, database URLs, etc., and load them via FastAPI settings or similar).
  * Implement basic rate limiting or abuse prevention on the API, if possible, given the time. For example, you could use FastAPI dependencies or middleware to limit each IP to X requests per minute (there are simple packages for this, or use an API gateway throttle once on AWS). This is to ensure that even if the app is public, someone can’t spam and rack up huge token costs.
  * If relevant, sanitize user inputs – although for Q\&A it’s mostly text (LLM is robust with text), but if any part of the query is used in functions (like our simulate function), ensure type safety (e.g., if parsing numbers, handle errors).
  * Consider CORS and allowed origins – for now, maybe allow all origins (since you haven’t decided the domain yet), but plan to restrict in production to your domain.
  * Plan out API keys for external services: e.g., OpenAI key should be stored in an environment var on the server and not exposed to frontend. Same for any email service or others.

  This day ensures the foundation is solid for moving to deployment and that you won’t accidentally expose something or break when multiple users come.

* **Day 30:** **Milestone 1 – Core MVP Complete** – By end of Day 30, the **MVP core features should be functional**:

  * Users can ask automotive questions and get answers based on the PDF knowledge base via the chatbot interface.
  * The system can (at a prototype level) handle requests for diagrams, process plans, and simulations (even if the outputs are simplistic).
  * The backend, frontend, and database are working together locally.

  Use today to do a full walkthrough as if you’re an end-user and as if you’re demonstrating to an investor:

  1. Go to the landing page (which we’ll flesh out in Phase 2), use the chat to ask a few sample questions (one factual, one asking for a diagram, one for a process plan, etc.).
  2. Ensure responses make sense.
  3. Test the email capture (if built) or plan to build it soon.

  Fix any last critical bugs. Now you have a solid base to build the remaining polish and deployment in the next phases. This concludes Phase 1 with a working prototype that showcases the technical viability of your idea.

## Phase 2: Month 2 (Days 31–60) – Advanced Features, Frontend & User Experience

*Focus:* Building out the user-facing parts (landing page, UI/UX improvements), refining the advanced features, and making the application more robust and user-friendly. By the end of Day 60, all intended MVP features should be implemented and the app should be usable by early beta users. **Milestone (Day 60):** Fully functional application with front-end polished, all features integrated (core QA + CAD/Planning/Simulation capabilities), ready for deployment testing.

* **Day 31:** *Design & UX for Landing Page* – Create a polished **landing page** to impress visitors and collect sign-ups. This page should explain the product (e.g., “AI-Powered Automotive Support Chatbot for B2B and Consumers”), list key features, and have a clear call-to-action (like an email signup form for beta access). Use React with a CSS framework (like Tailwind or Chakra UI) to speed up styling. Include a hero section with a catchy headline, a brief description of the chatbot’s benefits (maybe “Instant answers from 300+ automotive manuals” etc.), and possibly some illustrative graphic (if you have a relevant image or icon, e.g., a car or a robot assistant – ensure you have usage rights or use a free icon library). Plan space on this page to embed the chatbot demo as well (some startups embed a limited version of their chatbot on the landing page so users can try it instantly). If embedding the full chat is heavy, at least include some example Q\&A interactions as text or a short video/GIF to showcase it.

* **Day 32:** *Implement Email Signup Form* – On the landing page, implement the email collection form. Use a simple form that sends a request to the backend (e.g., POST `/signup` with email). In the FastAPI backend, add an endpoint to handle this: validate the email format and store it in the `emails` table (from earlier setup). Also consider integrating a mailing list provider – for MVP, storing in Postgres is fine, but you might also set up a free tier Mailchimp or SendGrid list to later send newsletters to these signups. (With a \$100 budget, you’ll likely stick to free tiers of such services too; Mailchimp allows up to 500 contacts on free plan, for example). For now, just saving emails and maybe sending a simple confirmation email would be great. AWS SES could send emails cheaply (though sandbox requires verification). If time permits, configure AWS SES or a transactional email API (some have free credits) to send a “Thank you for signing up for the beta!” confirmation when a user registers. This touches on **onboarding flows** by welcoming new users.

* **Day 33:** *Integrate Chatbot into Landing Page* – Decide how users will access the chatbot. Two approaches:

  1. **On-page demo:** Embed the chat interface on the landing page itself (beneath the marketing material). This allows visitors to try basic queries immediately without login. You can label it “Try the chatbot now” with a small disclaimer about limited scope.
  2. **Separate page:** Have a “Try the Demo” button that navigates to a dedicated `/chat` page or opens a modal with the chat.

  For MVP and ease, embedding on the main page is fine. Implement this by bringing over the chat component built earlier into the landing page. Style it nicely – perhaps a chat bubble interface contained in a div with a fixed max height and overflow scroll. Ensure it’s mobile-friendly (test the page on a small screen; use responsive design so that the layout is still readable on phones).

  Once integrated, test that a new visitor can open the page, read about the product, enter a question in the chat, and get a response – all without any complex setup. This is important for showcasing the MVP: instant value to end users.

* **Day 34:** *Visual Branding & UI Polish* – Add basic branding elements: choose a project name (if not already) and create a simple logo or wordmark (you can use a free logo maker or just a stylized text). Apply a consistent color scheme across the app (possibly something automotive-themed like blues or greys reminiscent of engineering diagrams). Ensure buttons, inputs, and fonts are consistent. Polishing the UI is important for first impressions:

  * Use a clean typography (e.g., a Google Font that looks professional).
  * Add some spacing and dividers in the chat interface for clarity.
  * Maybe include small avatar icons for “User” and “AI” in the chat to differentiate (there are free icon sets or use letters).
  * Make the email signup form prominent and visually appealing (perhaps as a modal or a separate section with a nice background).

  The goal is a landing page and chat interface that look **professional and demo-ready**, not just a bare-bones form.

* **Day 35:** *Frontend Testing & Debugging* – Test the entire frontend for usability:

  * Does the page load fast enough (on first visit, with images/scripts)? Optimize by removing any large unused libraries. Maybe lazy-load the LLM-related heavy scripts if any.
  * Test on multiple browsers (Chrome, Firefox, Safari) and devices. Fix any compatibility issues (CSS quirks, etc.).
  * Verify the email form doesn’t accept invalid emails (add a regex check on frontend too for UX).
  * Check the chat for edge cases: very long query input, or special characters. Ensure it scrolls properly as messages overflow, etc.
  * Confirm that after an interaction or two, the app remains stable (no memory leaks or huge CPU hog in browser).

  Pay attention to error handling: simulate the backend being down or returning error (maybe by turning off backend for a test) – does the UI handle it gracefully (e.g., showing “Server error, please try later”)? Add such messages if not present. These details improve user trust during beta testing.

* **Day 36:** *Complete Advanced Feature Implementation* – Revisit the advanced features (CAD, process, simulation) now with the perspective of an end user. At this point, decide if any upgrades are feasible within MVP:

  * For **CAD diagrams**: If the current solution is too placeholder (say it always returns one static image), maybe implement a small improvement. For example, use a dictionary of a few component names to images. If user asks “diagram of \[X]”, and X is in our dictionary (like engine, transmission, battery), return that specific image. This way it’s a bit smarter. If X not recognized, you can either say “Diagram feature is limited in beta” or try a quick on-the-fly generation via an API (only if time allows). Perhaps you discovered an open API or service for generating mechanical diagrams – if one exists on free trial, you could integrate it now.
  * For **process planning**: Possibly fine-tune the output format. If the LLM often gives 5 steps but user asked for a detailed plan, consider prompting it to elaborate each step. Or if it’s too verbose, shorten it. Essentially calibrate the helpfulness. Maybe add a feature where after listing steps, the bot asks “Do you want me to expand on any step?” – that could be a nice interactive touch (though optional).
  * For **simulation**: If there’s time, make the simulation more visible. Perhaps output not just text but also a simple chart. You could use a Python plotting library (matplotlib) to generate a chart image from the data and return it. For example, simulate a curve and produce a line graph. This would wow demo viewers (seeing a chart generated). Keep it simple (e.g., one series, short time span). If implementing, ensure to integrate image display in frontend (similar to CAD image). If time is short, a text output is acceptable.

  The aim is to ensure each advanced feature is at least demo-able and doesn’t feel completely static, while staying within feasibility for one person.

* **Day 37:** *Writing Tests (Basic)* – Write some basic automated tests for critical components. This might include:

  * Backend: test the PDF ingestion on a small sample (e.g., does `extract_text` function return non-empty string for a known PDF? Does `search_vectors` return expected chunk for a sample query?).
  * If possible, test the `/chat/query` with a mocked vector store and LLM (you might inject a dummy that returns a known answer) to see that the endpoint returns a 200 and well-formed JSON. Also test the `/signup` endpoint with a dummy email.
  * Frontend: not necessary to write automated tests (unless skilled with Jest/React Testing Library), but at least test manually that builds work and the app functions. The reason for tests is to catch regressions as you continue to refine and also it’s a good look to have some tests (YC might not ask, but it’s good engineering).

  Integrate these tests into the CI pipeline (update `.gitlab-ci.yml` to run tests on push). Ensure they all pass. This also gives confidence when deploying that things are less likely to break unexpectedly.

* **Day 38:** *Analytics Integration* – Add basic analytics to understand user behavior once the beta is live:

  * Integrate **Google Analytics 4** (free) or a privacy-friendly alternative like Plausible (if you prefer) to the landing page to track page visits, button clicks (signups), etc. This can simply be including the GA script with your tracking ID in the React app. Test that it’s capturing visits (you can check real-time analytics when you browse the site).
  * Additionally, in the backend, log important events: e.g., each question asked could be logged (anonymously) with a timestamp. This helps in analyzing what users ask most and how well the answers might be. Make sure not to log any sensitive user info without consent – but questions about car support likely fine to log for improvement purposes.
  * If possible, capture usage metrics like how many queries per user session, or how long they interact. This might be as simple as counting queries and associating with a session ID (you can use a random session token cookie on the front-end to identify sessions without login).

  These analytics will be useful for iteration and also for demonstrating traction (e.g., “we had X users ask Y questions in the first week” is a strong point). Keep the analytics lightweight to not blow budget (GA is free, and logging to your own DB is fine).

* **Day 39:** *Performance Optimization* – With all features in, ensure the app runs efficiently:

  * Check the memory usage of the backend. If using an in-memory vector index for 300 books, it might be heavy; ensure you’re either using Postgres or an on-disk store so that memory is under control. If memory is an issue, consider reducing vector dimension (if using a smaller embedding) or offloading the vector search to disk.
  * Speed: Each query currently does (embedding + DB search + LLM call). Perhaps cache embeddings of frequent queries or simple FAQ pairs. Or implement a basic caching mechanism: e.g., in Python, use an LRU cache to store the last N questions and answers. So if a user repeats or if multiple users ask same common question, you serve it instantly without recomputation. This can be done with an in-memory dict for now.
  * If using OpenAI API for answers, consider enabling streaming (which allows the frontend to get partial answer tokens). This improves perceived speed. FastAPI and React can handle streaming via server-sent events or websockets. This might be advanced, so only do it if time and it integrates well; otherwise, ensure the single-turn answer is fast enough (under a few seconds).
  * Consider concurrency: with FastAPI (async) and Uvicorn, you can handle multiple requests, but on a single free-tier instance it’s limited. Still, ensure no part of code is blocking unnecessarily (e.g., if using local model, that’s slow and blocks event loop – maybe run it in a threadpool or consider using an async OpenAI call instead).

  These optimizations will help the app handle initial users smoothly on minimal infrastructure.

* **Day 40:** *Prepare Deployment Scripts* – As we approach deploying to AWS, prepare the necessary scripts/config:

  * Finish the Docker setup: perhaps create a `docker-compose.yml` that defines two services: app (FastAPI + Uvicorn) and db (Postgres), if you plan to run Postgres in container on EC2. Alternatively, if using RDS, just a service for app. Ensure the Dockerfile for the app is optimized (use a slim Python base image, multi-stage build to reduce image size).
  * Add configuration for environment variables in Docker (like taking from host or a `.env` file).
  * Test running the whole stack via Docker on your local machine: `docker-compose up` should start the API and DB and the API should be reachable. Fix any issues with container networking or volumes (e.g., persist the DB data in a volume).
  * Write a small deployment script (could be a simple bash script) that you’ll use on the server to pull the latest image and run it. Or plan to use GitLab CI for deployment (e.g., a CI job that SSHes into the EC2 and updates the app).

  This groundwork will make actual deployment faster and less error-prone.

* **Day 41:** *AWS Environment Setup* – Set up the AWS infrastructure:

  * Launch an **EC2 t2.micro** instance (Amazon Linux or Ubuntu). This is free-tier eligible. Configure security group to allow HTTP (port 80 or 443) and SSH.
  * If using RDS for Postgres: launch an RDS instance (also free-tier micro). Otherwise, plan to use a Postgres container or install Postgres on EC2 directly. RDS is convenient but one more moving part; given free tier covers it and ease of setup, it’s a good option. If using RDS, set it up now (Postgres 15 with pgvector extension if possible – you might need to use a custom parameter group to enable pgvector extension).
  * Set up an S3 bucket (free tier) if you plan to serve static files or store logs (not strictly necessary, but could be used for storing user-uploaded files or images if any in future).
  * Configure IAM roles/credentials for the instance if needed (for example, to allow it to pull from ECR if you use that for Docker images).
  * Ensure you have key pairs and access setup to SSH into the instance. Test SSH into EC2.

  Essentially, have the cloud environment ready for the code deployment.

* **Day 42:** *Domain & DNS Configuration* – If you have a domain name for the product, set it up now (you can purchase one for \~\$10-15/year). Update the DNS A record to point to the EC2’s IP. If using AWS Route 53, it can manage DNS easily (though Route 53 costs a little per month). Alternatively, use your domain registrar’s DNS to add the record.

  * Once DNS propagates, try hitting your domain – it should (after deployment) serve the app. But now, you can also set up an **SSL certificate**. The best way is to use **Let’s Encrypt**. You can do this via a tool like Certbot on the EC2 instance. Since you plan to use Docker, one approach is to use a reverse proxy like **nginx** with a Let’s Encrypt companion container to automate SSL. If that’s too heavy, you can temporarily run Certbot on the host to get a certificate and then configure Uvicorn or an Nginx to use it.
  * For MVP, you might even run the site on HTTP for the very initial demo and add SSL a bit later (not ideal, but if time-pressed, you could launch without SSL and add it in a few days – however, since this is public-facing and YC judges on polish, it’s worth trying to include HTTPS).

  Document these steps carefully (you’ll use them soon on launch day).

* **Day 43:** *Deploy Backend to AWS (Initial)* – Time to deploy the backend application to AWS EC2. There are a few strategies:

  * Simplest: SSH to EC2, install Docker, then manually pull the Docker image (from Docker Hub or GitLab Registry if you pushed it) and run it. Or SCP the code and run it directly (though using Docker ensures consistency). Let’s say we use Docker: push the image built from Day 40 to Docker Hub (private repo) or to AWS ECR (which has a free tier for a limited storage).
  * On EC2, run `docker run` (or `docker-compose up` if including Postgres).
  * If using RDS, ensure the app’s DB connection string points to the RDS endpoint. If using local Postgres container, make sure data directory is persisted and ports are open only to the app (not public).
  * Start the FastAPI container, ensuring it’s listening on port 80 or mapped to 80. Check the EC2’s firewall/security group allows that port.
  * Once running, test via curl or in browser hitting the EC2 public DNS or your domain: you should see either the landing page or at least the health endpoint.

  At first, deploy in **development mode** (ease of debugging). Then you can switch to a production server like Uvicorn with multiple workers or even Gunicorn if needed. But a single Uvicorn worker might suffice for testing.

* **Day 44:** *Deploy Frontend (Static)* – The React frontend can be deployed in two ways:

  1. Build and serve it via the FastAPI backend (e.g., build the React app to static files and have FastAPI serve the `build/` directory as static files). This is straightforward and keeps everything in one service.
  2. Host it separately (like on S3+CloudFront or GitHub Pages). Given the backend is on EC2, it’s simplest to serve it from the same EC2.

  Implement option 1: Update FastAPI to mount a StaticFiles app serving the React build output. In CI or deployment script, ensure `npm run build` is executed and the files are copied into the backend image (the Dockerfile can do this if it’s multi-stage). Alternatively, copy the build folder to the server and configure Nginx to serve it and reverse-proxy API calls to FastAPI.

  After setting this up, navigate to your domain in a browser – you should see the actual landing page served from the EC2, and the chat should function (it will call the API on the same domain or via relative path if configured). This is a big moment: the app is essentially live on the internet (maybe in a staging sense). Test all functionalities on the deployed version, since sometimes things that worked locally break in prod (like CORS, or static paths, etc.). Fix any deployment-specific bugs.

* **Day 45:** *CI/CD Automation* – Now that manual deployment works, streamline it:

  * In GitLab CI, add a deployment stage. One approach is to use a GitLab Runner on the EC2 instance (install GitLab runner on it and register to your project), so it can run jobs directly on the server. Then you can configure the pipeline to upon merge to main, build the Docker image and deploy it. However, building on EC2 might be slow.
  * Another approach: Use GitLab’s shared runners to build and push the Docker image to a registry, then have a small SSH deploy script. For example, after image push, use `ssh user@ec2 'docker pull ... && docker-compose up -d'`. You can secure this via SSH keys.
  * If those are too time-consuming to set up, at least document the manual steps so that you can quickly redeploy when needed. But CI/CD is a good look for modern development, so try to get a basic automated pipeline.

  Test the pipeline by making a small change (like edit the README) and pushing – see if it triggers a deployment successfully. Debug any CI issues (like permission denied, etc.). Overcoming these shows solid DevOps capabilities.

* **Day 46:** *Rigorously Test in Production Environment* – Now that the app is deployed, do another round of thorough testing, but on the live environment:

  * Use a real web browser to sign up an email, ask questions, use all features. Ensure data is actually saved in the cloud Postgres (connect to DB and verify email row exists, etc.).
  * Test concurrency if possible: open the site in two browsers and send queries around the same time – see if the server handles it.
  * Check logs on the server (if using Docker, `docker logs`) for any errors or warnings. Fix them if minor (e.g., maybe a missing environment var causing a fallback).
  * Ensure the response times are acceptable on the micro instance. If it’s too slow, consider this: for demo purposes, it might be okay, but if you anticipate YC or others testing it, you want a decent speed. If needed, consider upgrading the instance to a t3.small (which might incur cost but still low) for the demo period, or just ensure the queries are cached as earlier to compensate.

  This testing is to simulate real usage and catch anything you missed in dev. It’s essentially a soft launch to yourself.

* **Day 47:** *Monitoring & Logging Setup* – Set up basic monitoring so you can keep the service running:

  * Use a tool like **Prometheus/Grafana** (heavy for MVP) or simpler – use CloudWatch (AWS) to monitor EC2 CPU/memory. CloudWatch can have alarms (free basic alarms) – for example, alarm if CPU > 80% for 5 minutes (maybe means something stuck) or if memory high, etc. Configure a couple of these if comfortable, or at least plan to watch metrics in AWS console periodically.
  * Implement application-level logging: ensure FastAPI logs requests and errors to a file or stdout (Docker will capture stdout). You might mount a volume so logs persist or use a logging service. Logging is crucial if something goes wrong – you want to see error traces. Already, you log analytics and perhaps each query; just ensure those logs won’t fill the disk (rotate or truncate if needed).
  * If feasible, integrate a service like **Sentry** for error tracking (they have a free dev tier). Sentry can capture exceptions in the backend and frontend and alert you. It’s a nice-to-have for quality, but not mandatory.

  These steps show you’re thinking about reliability – a good sign for any startup.

* **Day 48:** *Collect Beta Feedback Plan* – As you prepare to allow users in, plan how to collect feedback beyond analytics:

  * Perhaps create a **Google Form** or Typeform and link it on the site (“Send Feedback”). Or simpler, provide an email contact for feedback. However, a form can guide users to answer specific questions (e.g., “Did the bot answer your question effectively? Any issues you encountered?”).
  * Consider adding an in-app feedback prompt: after X queries or on session end, the UI could pop “How satisfied are you? \[Good/Bad]” and an optional comment. This could tie into your earlier feedback endpoint.
  * Set up a way to regularly review this feedback. Maybe create a Slack channel and use a Slack webhook to post feedback submissions to Slack for quick visibility (if you use Slack).
  * The goal is to iterate quickly based on user input, and also to have qualitative data to mention (like “Beta users mentioned they love the quick answers on maintenance questions but want more images – which aligns with our next steps…”).

  Implement at least one feedback channel now (like the link to a form or an email in the footer).

* **Day 49:** *Business Model and B2B Considerations (Planning)* – Since this is aimed at B2B clients eventually, take a day to brainstorm and incorporate tiny elements that hint at the future direction:

  * Perhaps include a section on the landing page “Solutions for Businesses” explaining how a car dealership or a repair shop could integrate this chatbot (e.g., on their website for customer support, or for training mechanics). This is not a functional feature, but content that sets context for B2B.
  * Plan what a premium version would entail (like private data integration, etc.) – not to implement now, but to be ready to discuss it.
  * On the sign-up form, maybe ask if the person is interested in a business partnership (could be a checkbox or separate form).
  * This day is more for aligning the MVP with the ultimate business pitch – useful for YC application where they ask about the business model. Document these thoughts.

* **Day 50:** *Documentation & Cleanup* – Write documentation for the project:

  * A **README** in the repo that clearly explains how to run the app, the architecture, and the tech stack. Mention usage of FastAPI, React, vector DB, etc., and how the RAG works. Also note any limitations (like data sources used, etc.).
  * Inline code comments for any complex logic, so that if you revisit later or if someone else looks, it’s understandable.
  * Clean up leftover debug code, print statements, or TODOs that are done. Make the code production-clean.
  * If open-sourcing parts of the code (maybe not now, but consider which parts could be open – some startups open source their demo), ensure keys are not in the repo and it’s safe to show.

  Good documentation will also help you in writing the YC application, as you can pull from it to describe what you’ve built.

* **Day 51:** *Milestone 2 – Feature-Complete & Beta Ready* – At roughly the 60-day mark (end of Month 2), your MVP should be **feature-complete**:

  * All core and advanced features implemented.
  * Frontend and UI polished for a good user experience.
  * The app deployed on a server with monitoring, ready for external users.

  That means you can now start a **beta testing period**. Summarize the achievements: e.g., “In 60 days, built a full-stack AI chatbot that answers automotive questions from 300 manuals with additional AI tools for diagrams, planning, and simulation, containerized and deployed on AWS within \$100 budget.” This is powerful to mention in YC applications or to potential advisors. Take a short breather – Phase 3 will focus on launch, user feedback, and further tweaks rather than adding major new features.

## Phase 3: Month 3 (Days 61–90) – Testing, Launch & Preparations for YC

*Focus:* Final testing, iterative improvements based on feedback, and launch preparations. This includes scaling considerations, final touches, and compiling the progress/metrics for a YC application. By Day 90, the product should be launched (at least a beta launch) with initial users and data, and you should be ready to pitch it. **Milestone (Day 90):** Public beta launched, initial user engagement recorded, and a strong narrative for the project’s future is prepared.

* **Day 61:** *Invite Beta Users & Soft Launch* – Begin inviting users to try out the chatbot. These could be:

  * Friends or colleagues with interest in cars to test the system.
  * Niche communities (for example, an automotive forum or a subreddit) – carefully share the link to get some early adopters, making it clear it’s a beta.
  * Post on social media or relevant Discord groups inviting people to ask the chatbot car questions.

  Even a small number (dozens) of testers is valuable. The goal is to get real usage. Keep an eye on the system when you do this (monitor the server metrics and logs). Also be prepared to temporarily disable features if something goes wrong under load. This is essentially a **soft launch**.

* **Day 62:** *Collect and Analyze Usage Data* – As beta users start using it, collect data:

  * Look at analytics: How many unique users? How many questions asked per day?
  * Look at the types of questions asked. Are they mostly maintenance questions, car specifications, troubleshooting? This will tell you what aspects are most valuable.
  * See if any queries failed or produced wrong answers. Identify a few examples to later improve.
  * Gather feedback responses from the form or email. Categorize them: e.g., some might say “answers are good but I’d like an app for mobile” or “didn’t find info on my specific car model” etc.

  Summarize this initial data. It’s impressive to say “In our first week, X users asked Y questions, and we learned Z from their feedback.” It shows traction and learning.

* **Day 63:** *Iterate on Content and Knowledge Base* – Based on the user queries, you might find gaps in the knowledge base:

  * For example, if many ask about a specific car model not well covered in the PDFs, consider adding data for it. Maybe find an official manual PDF for that model and add to the corpus (assuming it’s legally okay).
  * If users ask very generic questions (e.g., “what car should I buy?”) which the system isn’t meant for, think about handling that gracefully (maybe a witty response like “I can’t help with that – I’m more of a repair and technical support bot”).
  * If some answers were wrong or unsatisfactory, debug why: was the retrieval missing relevant info? If so, maybe the chunking missed a key detail (you could adjust chunk size or overlap). Or the LLM hallucinated – if so, consider adding a prompt caution “If you are unsure or the information is not in context, say you don’t know.” This can reduce hallucinations.

  Make incremental improvements to the knowledge base and QA logic as needed. This is part of polishing the MVP quality.

* **Day 64:** *Scaling Considerations* – Consider how you would scale if usage increases:

  * The free-tier EC2 might not handle too many concurrent users or large LLM loads. Think about short-term upgrades: e.g., moving to a larger instance or using AWS Fargate to run containers (though might exceed free tier). Given budget, perhaps not now, but outline a plan.
  * Think about the vector DB scaling: if you had to handle more data or faster queries, you might use a dedicated vector DB service (like Pinecone or Weaviate cloud) in the future. For now, ensure the current setup is indexed well (if pgvector, ensure an index on the vector column for ANN search).
  * If using OpenAI, watch the usage and costs as more queries come. You might implement a daily quota in code (like after X queries, temporarily stop, or show a “rate limit” message) to avoid runaway costs from heavy users.

  These considerations may not all be implemented now, but being aware is good. If any quick wins (like increasing the batch size for vector search or enabling caching more aggressively), do them.

* **Day 65:** *Marketing & Community Engagement* – Use this time to build awareness (which also helps show YC that people want your product):

  * Write a Medium blog post or a LinkedIn article about the making of this chatbot and the problem it solves. (e.g., “Introducing AutoBot – An AI assistant trained on 300 car manuals”). Highlight the tech stack and the use cases. This can draw attention and also serve as something to reference in your YC application (shows you as an expert and thought leader).
  * Engage on relevant forums or Q\&A sites. If someone asks a car question on Reddit, you can gently mention “We built a chatbot that can answer these from manuals – you can try it here.” But be genuine and non-spammy.
  * Update the landing page or site with any press or community feedback (even a testimonial from a user if you have one like “I got my repair question answered in seconds – user X”).

  These actions help get a few more users and also show that you are actively trying to get traction.

* **Day 66:** *Onboarding Flow Enhancements* – Now that some users are trying the product, refine the onboarding:

  * Maybe add a **welcome message** from the chatbot itself when a user first opens the chat, e.g., “Hi, I’m AutoBot! I can answer questions about car maintenance and more. Ask me anything about your vehicle.” This guides the user on how to start.
  * On the landing page, include a brief “How to use” section or a few example questions as buttons that users can click to quickly see a result. (For instance, a button saying “How often should I change oil?” that triggers that query in the chat). This engages users who might not know what to ask.
  * If you collected the user’s email at signup, consider if you want to send a follow-up (maybe not yet for MVP, but maybe a one-time “Welcome to the beta, here’s what to try” email could be nice if done manually).

  Smooth onboarding will make users more likely to actually use the bot and find value quickly.

* **Day 67:** *Final QA & Bugfixes* – Sweep through the application to fix any lingering bugs or UX issues:

  * Check console logs in the browser for any errors or warnings and resolve them.
  * Check server logs for any exceptions that haven’t been addressed.
  * Make sure all major paths have been tried: signup form submission (try an invalid email to see the error message), multiple questions in one session, etc.
  * Ensure that if an advanced feature is not available (like user asks for a diagram that we don’t have), the bot responds gracefully (“I’m sorry, I can’t generate that diagram yet” rather than crashing or giving a wrong answer).

  At this stage, the product should be stable enough for real use without you constantly monitoring.

* **Day 68:** *Plan the YC Application Narrative* – With the MVP essentially done and some user data, start preparing for the YC application (assuming next batch deadline is near, or just to have it ready):

  * Define the **problem and solution** clearly: e.g., “Problem: Automotive businesses spend a lot on customer support and technicians waste time searching manuals. Solution: Our AI chatbot instantly answers technical questions from a vast library of automotive manuals, saving time and improving customer satisfaction.”
  * Highlight the progress: “In 3 months, I built an MVP that… (list features). We have \[X] early users, \[Y] sign-ups, and answered \[Z] queries with positive feedback.”
  * Think about the future: how will you monetize? Possibly a subscription for businesses to get custom versions of the bot on their data, or charging for premium usage by consumers beyond basic queries.
  * Think about moat/defensibility: your unique data (300 manuals curated), the domain expertise, or maybe a proprietary process for CAD/simulation. You might cite that big companies are exploring similar (e.g., Car-GPT uses curated data with GPT to answer car queries, showing market interest).

  Jot down these points to use when writing the application.

* **Day 69:** *Load Testing (Optional)* – If time permits and you want to ensure reliability, do a simple load test. For example, use a tool like JMeter or k6 to simulate, say, 10 concurrent users asking a question every few seconds. See if the system holds up on the micro instance. It likely will hit the CPU hard (especially if using LLM API it might queue requests). This test can reveal if you need to add a queue or limit concurrency. If it fails badly, consider adding a note in your site “Limit: one query at a time in beta” or something, or temporarily upgrading hardware. But for a small beta, it might be okay. Still, it’s good to know the breaking point.

* **Day 70:** *Budget Review & Cost Management* – Check your spend to date:

  * AWS should still be free tier mostly, but verify no unexpected charges (some services like data transfer or certain usage can incur small costs).
  * If using OpenAI API, calculate how many tokens have been used. Ensure it’s within your \$100 allowance. If, say, \$20 has been used so far, that’s fine. If more, you might want to curb usage or find optimizations (like using GPT-3.5 instead of GPT-4 if you were using 4, or limiting answer length).
  * Domain and other one-time costs should be minimal (domain \$12, maybe an icon asset \$5 or so).
  * If a particular feature is costly (e.g., image generation via some API), you should limit its use or disable after demo.

  Implement any cost-saving measures: for example, if using OpenAI and you want to ensure not to overspend, put a daily cap (like after 100 questions in a day, the bot says “We’ve reached the daily limit, please come back later”). This is not ideal for user experience but safe for budget. Or require signup after certain usage to throttle usage somewhat.

* **Day 71:** *Showcase Preparation* – Prepare to showcase the MVP in its best light:

  * Create a short demo video (2-3 minutes) where you screencast using the chatbot. Show various capabilities: asking a question, getting an answer, asking for a diagram (and showing it), asking for a process plan, etc. Highlight also the landing page and how easy it is to sign up. This video is useful for applications or pitching (YC often likes a video demo).
  * Prepare a pitch deck (brief) if you plan to talk to investors or mentors. Slides could cover: Problem, Solution (with screenshot of your chatbot), Market (automotive industry support stats), Why Now (LLMs enabling this new approach), Roadmap (how you’ll go from MVP to full product, e.g., add more data, more integrations), Team (solo founder background).
  * Though not directly coding, this task solidifies your story and ensures you can communicate what you built effectively. It’s part of launch prep.

* **Day 72:** *Public Launch (if ready)* – Consider doing a wider launch:

  * Post on **Product Hunt** or **Hacker News** if you feel the product is polished enough. This can attract attention and more beta users. Ensure you have monitoring in place as this can spike traffic. Also be ready to respond to comments.
  * Alternatively, a more targeted launch like reaching out to a tech blogger or posting in a car enthusiast community might be valuable.
  * Public launches can be unpredictable; even if you only gather a few users, it’s experience and possibly some feedback or even a potential partner may notice.

  If you decide to launch on a platform like Product Hunt, coordinate things like time of day, wording of the post, etc., on this day.

* **Day 73:** *Post-Launch Triage* – After a public launch, gather results:

  * How many upvotes or comments? Any critical feedback or bugs reported?
  * Did the site handle the load? Check server logs and performance metrics again.
  * Respond to any user inquiries or emails promptly – show that you are actively supporting the project.

  Even if launch didn’t blow up, treat whoever engaged as valuable – their input can refine the product. Fix any urgent issues uncovered (if a lot of people got an error doing X, solve that ASAP).

* **Day 74:** *Plan Next Features (Beyond MVP)* – Now that MVP is done and launched, take some time to envision the next steps (this might be asked by YC: what’s your plan moving forward?):

  * Perhaps adding a **knowledge base management interface** (for you or future clients to add their own docs).
  * Or a **chat history and login system** for users to save conversations (especially for B2B clients who might have accounts).
  * Consider developing a mobile-friendly app or integration with WhatsApp for easier access (just ideas).
  * Advanced AI improvements: training a custom smaller model on the PDF data to reduce dependency on external APIs, or integrating the system with actual CAD software for real diagram generation, etc.
  * List these ideas and prioritize which make sense to do first if you had more time or post-YC funding. It shows a vision beyond the MVP.

  No coding required today, just strategic thinking which is equally important.

* **Day 75:** *Team & Advisory (if applicable)* – As a solo founder, consider if you need any advisor or if you have someone who can validate the tech/business. YC likes to see that you understand your gaps. For instance, maybe reach out to an automotive industry expert or a former service manager to try the bot and give feedback – that could later be mentioned as “We consulted with \[role] and they see potential in reducing support time by 50%” etc.

  * Also reflect on if you might need a co-founder (YC often asks if you’re looking for one). If you are considering it, maybe identify what skills (business side or AI research side) would complement you.
  * Not something to solve now, but be ready to address it.

* **Day 76:** *Polish YC Application Answers* – Draft the YC application answers (if you’re applying):

  * Describe your idea in one sentence.
  * How does it make money (eventually)? Perhaps “Subscription for automotive businesses to integrate this chatbot into their customer support, and upsell premium features or API access.”
  * How much progress? – you can now confidently say you built an MVP that is live and used by X people.
  * What’s your unique insight? – perhaps that “The trove of automotive knowledge in manuals is underutilized; by unlocking it with AI, we can revolutionize automotive support.”
  * Etc.

  Use data and specifics from your 90-day journey to make these answers strong. Have a friend review if possible.

* **Day 77:** *Pause and Code Freeze* – By this time, avoid making major changes to the code unless critical. You want the demo to remain stable. Use this day to run a full backup:

  * Backup the database (dump the Postgres data) so you don’t lose the signups and feedback collected.
  * Backup the vector index or be sure you can re-run ingestion if needed.
  * Save the current frontend and backend builds. Tag the repository with a version (v0.1).

  This freeze ensures that leading up to Day 90, you’re not breaking anything inadvertently; you’ll focus on wrap-up and any last-minute tweaks or presentation stuff.

* **Day 78:** *Final Performance Tweaks* – If there were any lingering performance issues noticed from beta, address them now:

  * e.g., if memory usage was high, perhaps increase swap on the EC2 (as a temporary measure) to avoid OOM.
  * If certain queries are slow, see if you can add an index or adjust code logic for speed.
  * If the site took long to load resources, maybe enable gzip compression on responses (Uvicorn/Starlette can do that, or use Nginx).

  These small tweaks can make the service snappier for the next wave of users.

* **Day 79:** *Legal & Compliance Check* – Just to be safe, think about the content you’re serving:

  * Are those 300 PDFs copyrighted? If they are official manuals, it’s likely fine for personal use but serving them via an AI could be a gray area. For MVP, it’s usually under the radar, but long-term, you’d want to license data or use public data. Make a note of which data sources might need permission if scaling.
  * Add a **Terms of Service/Privacy Policy** page on the site (even a basic one) stating this is a beta service, info provided “as-is” without liability, etc., and that you may collect usage data but won’t misuse it. There are free templates for beta app terms. This is good hygiene and shows you’re thinking ahead.

  Implement links in the footer for Terms and Privacy (even if they’re brief or stored as static HTML). This can protect you and also is expected if real users are using it.

* **Day 80:** *Prepare for Investor Meetings* – If you’re applying to YC, you might also talk to some other investors or advisors:

  * Prepare a **live demo** script: If on a call, what will you show? You might walk through using the product for a real scenario (like “Watch how a mechanic could use this: *\[shares screen]* – ‘How do I replace the air filter on a 2018 Honda Accord?’ – and see the bot give step-by-step from the manual”). Practice this to be smooth.
  * Be ready to talk numbers: even if small, any usage metric or growth (“we went from 5 to 50 users in a week without marketing” or “users have asked 500 questions with a 90% success rate”).
  * Think of answers to common questions: “Why will this be a big business?”, “What stops Google from doing this?”, “How will you get car companies on board?”, etc.

  This day is about stepping out of coding and into CEO mode, which is crucial as you move beyond MVP.

* **Day 81:** *Continuous Improvement Setup* – Set up a system for continuous improvement after Day 90:

  * Create a backlog (if not already) of all the features and fixes you’ve thought of but couldn’t do. Use a Trello or GitHub Issues or even a simple spreadsheet. Prioritize them. This backlog will guide you post-MVP.
  * Set up a cadence to review feedback and data weekly and plan changes.
  * If any community or user base exists (even a mailing list of signups), plan to keep them engaged, e.g., send an update email when you add a new feature or data source.
  * Essentially, ensure the momentum can continue past the 90 days, as real product development is ongoing. This plan itself is a great record to show progress, but progress must continue.

* **Day 82:** *Final Demo Day Simulation* – Simulate a scenario as if it’s demo day or YC interview:

  * Prepare a 1-minute elevator pitch. Practice saying it succinctly.
  * Prepare a few slides or talking points and limit yourself to 10 minutes of demo+talk.
  * Possibly present to a friend or just record yourself and watch it – see if the value prop comes across clearly.
  * Make sure to emphasize what’s impressive: “We ingested 300 books of domain knowledge and built a chatbot that can do X, Y, Z (with mini AI tools for diagrams and simulations) – all in 3 months with <\$100. Imagine what we can do with proper resources.”

  This exercise helps catch any last presentation issues and builds confidence.

* **Day 83:** *Buffer & Last-Minute Fixes* – Despite best planning, there may be a few last-minute issues or ideas. Use this buffer day (and any remaining days up to 90 as buffer) to address any of:

  * A pesky bug that was low priority but nice to fix.
  * UI refinement like a better error message or a spinner icon when waiting for answer.
  * Infrastructure tweak like resizing the EC2 if you anticipate more traffic for a demo (maybe upgrade to t3.small for a day costs just cents).
  * Or simply take a rest today to recharge, because burnout can ruin a final presentation.

* **Day 84:** *Achievements Summary & KPIs* – Write down a summary of all key achievements over the 90 days:

  * e.g., “Implemented 10+ major features (from RAG-based Q\&A to AI-driven CAD generation)”.
  * “Built full stack (React/FastAPI) and devops pipeline (CI/CD, Docker, AWS deploy)”.
  * “Indexed \~300 manuals (\~X million tokens) into a custom knowledge base and served answers with an average response time of Y seconds.”
  * “Collected Z beta signups and answered W questions with Q% accuracy as per feedback.”
  * “All achieved with only \$N spent (within a \$100 budget) using mostly free-tier resources and open-source tools (LangChain, FAISS/Chroma, HuggingFace models).”

  These nuggets are great for applications and also for your own sense of accomplishment. Include relevant citations or data points if needed in your notes (for personal use or any reports).

* **Day 85:** *Final Touch: Personalization & Misc* – If there are any minor features that could delight users and take minimal time, you could slip one in now:

  * For example, allow the user to provide their name and then greet them by name in the chat, making the interaction feel personalized.
  * Or a dark mode for the interface (some users love that).
  * Or multi-language support (maybe too heavy, but if you know your user base might ask in Spanish, consider allowing that by translating the query, etc. – only if trivial with some API).

  These are purely optional nice-to-haves. Only do them if everything else is truly done and stable. They can set you apart slightly by showing attention to detail.

* **Day 86:** *Graceful Degradation & Fail-safes* – Ensure that if any external component fails, the app degrades gracefully:

  * What if OpenAI API is down or over capacity? Maybe have the bot respond: “Sorry, I’m having trouble accessing my brain right now. Please try again later.” instead of just hanging.
  * If the vector search returns nothing, maybe respond: “I don’t have information on that. You might try rephrasing or check back later.”
  * If the user asks something beyond scope (like “What’s the weather?”), have a witty or helpful response rather than a confused one.

  This kind of polish can improve user perception especially if something goes wrong in a live demo or usage.

* **Day 87:** *Future Roadmap Outline* – Articulate a 6-12 month roadmap to complement the MVP (often asked by investors):

  * Month 4-6: “Add support for dynamic data (e.g., integration with a database of car parts for inventory queries)” or “Pilot with a local auto shop – incorporate their specific process docs into the chatbot.”
  * Month 6-9: “Launch premium tier with user accounts, saving favorite questions, possibly a mobile app version.”
  * Month 9-12: “Expand to related domains (maybe heavy machinery manuals or aerospace, etc.) or add voice interface for hands-free use in the garage.”

  This shows long-term vision. Write this down and perhaps even include a slimmed version on the website (like a “Coming Next: Mobile app, more data...” to keep users intrigued).

* **Day 88:** *Solicit Mentors or Expert Feedback* – With the product stable, you might want a seasoned perspective:

  * If you have access to any YC alumni or mentors, now is a good time to ask them to try it out and give feedback on both the product and how you’re positioning it. Incorporate their advice.
  * If none, try to get an industry expert (like an automotive engineer or service manager as mentioned) to use it and ask if this would actually help them. Their insights can validate your direction or suggest a pivot.

  The responses here might not result in immediate changes, but they will enrich your understanding and can be referenced (e.g., “We spoke to a service center manager and he said he answers 100 common questions weekly – our bot could handle those, saving hours.” That’s compelling.)

* **Day 89:** *Mentally Prepare & Rest* – The final stretch: ensure you get some rest before any important presentations or submission deadlines. It’s been an intense 90 days. Review everything one last time calmly:

  * Run through the app as a user with fresh eyes.
  * Ensure your demo video, documents, and application answers are all consistent and telling the same story.
  * Double-check that the production site is up and domain hasn’t issues, etc.
  * Then, give yourself a break – a fresh mind will help you catch any subtle issues on Day 90.

* **Day 90:** **Final Launch & Presentation** – This is the notional finish line:

  * Do a final live test of the system on the day of any demo or submission. Because of Murphy’s law, ensure all services (DB, API, etc.) are up and running. Restart containers if needed for a fresh state.
  * If applying to YC, submit the application with confidence, including all the details of what you’ve accomplished. Perhaps link the demo video and the live site for them to try.
  * If pitching to others, deliver your presentation and demo, highlighting the roadmap and vision now that the MVP is in hand.

  Celebrate the progress – in 90 days, you went from scratch to a sophisticated AI-powered chatbot platform. This roadmap demonstrates not only the features delivered at each milestone but also the methodical approach to building a startup product from the ground up within tight resource constraints.

**References:**

* Automotive AI assistant example (Car-GPT) combining LLMs with a domain-specific database
* RAG approach for PDF Q\&A: chunking documents, embedding with models like MiniLM or OpenAI, and using a vector DB (Chroma, FAISS, etc.) for retrieval
* Vector database use in LLM apps: stores text as numeric vectors for similarity search, enabling relevant document retrieval for answering questions
* Tech stack inspiration: PDF chatbot built with Python, LangChain, FAISS, and local LLM (Mistral) – demonstrating feasible open-source tools for our use case
* LangChain agent concept: allows an LLM to use external tools (e.g., functions for simulation or search) dynamically, which informed our approach to the advanced features
* AWS Free Tier resources: free 12-month usage of small EC2 and RDS instances, enabling us to deploy the full stack with minimal cost.
