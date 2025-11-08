
chatGPT generated this btw:

### [[Day 1 - Get Oriented]]

**Goal:** Understand what web scraping _is_ and what legal/ethical constraints apply.  
**Focus topics:**

- What’s the difference between scraping, APIs, and crawling?
    
- How to inspect a web page’s structure (using Chrome DevTools → Elements tab).
    
- Read CTFTime’s robots.txt to confirm what’s allowed.
    

📚 **Resources**

- Real Python: “What Is Web Scraping?”
    
- Scrapy docs → Intro to spiders (just read the concept part)
    
- CTFTime API docs — this might actually save you coding complexity.
    

---

### **Day 2 – Learn the Basic Tools**

**Goal:** Learn to use `requests` and `BeautifulSoup` (or `pandas` for parsing JSON if using API).  
**Focus topics:**

- Sending a request to a URL.
    
- Viewing returned HTML or JSON data.
    
- Selecting elements by tag/class.
    
- Exporting data to CSV.
    

📚 **Resources**

- Beautiful Soup 4 Docs: Quick Start
    
- requests library docs
    
- pandas: DataFrame to CSV
    

🧠 _Don’t copy code yet._ Instead, read through and describe what each part does in your own words.

---

### **Day 3 – Explore CTFTime API or HTML**

**Goal:** Find where CTF info actually lives.  
**Steps:**

- Go to https://ctftime.org/api/v1/events/ — it returns raw JSON.
    
- Inspect what fields exist (title, start, finish, onsite/online, format, etc.).
    
- Plan which columns your spreadsheet will include.
    

🧩 **Your Spreadsheet Schema:**

|Event Name|Start Date|End Date|Format|Online/In-Person|Fee|Requirements|Target Age|Link|
|---|---|---|---|---|---|---|---|---|

---

### **Day 4 – Prototype Extraction**

**Goal:** Extract only 3–5 events into variables and print them out.

- Practice parsing JSON (e.g., `.get("title")` etc.).
    
- Don’t loop over everything yet.
    
- Add basic validation (e.g., handle missing fields gracefully).
    

📚 **Resource:**  
Python JSON Module Docs

---

### **Day 5 – Expand to Full List**

**Goal:** Iterate through all 2024 events and store them in a Python data structure (list of dicts).

- Filter results by `start` or `finish` date (keep only 2024).
    
- Skip incomplete entries (some CTFTime events lack fields).
    

📚 **Reference:**  
CTFTime API: `/events/`

---

### **Day 6 – Clean & Export**

**Goal:** Convert your data into a table (using pandas) and save it.

- Create a DataFrame.
    
- Use `to_csv()` to save results to a `.csv` or `.xlsx` file.
    
- Open it in Excel or Google Sheets to confirm the layout.
    

---

### **Day 7 – Document & Test**

**Goal:** Make it GitHub-ready.

- Add a `README.md` explaining:
    
    - your goal (build a school CTF database),
        
    - how the scraper works (no need to include full code here),
        
    - what tools you used,
        
    - example output screenshot.
        
- Write comments in your code describing _why_ each section exists.
    
- Optional: make a `requirements.txt` file listing dependencies.
    

📚 **GitHub Guides**

- Creating a repository
    
- README best practices
    

---

### **Day 8–10 – (Optional Polishing Phase)**

If you finish early:

- Add error handling for failed requests.
    
- Pull from other sources (like NCAE Cyber Games or US CyberPatriot).
    
- Create a flag to limit output (e.g., “show only free events”).
    
- Try scheduling it monthly to auto-update your CSV.
    

---

## 🧠 **Concepts You’ll Learn**

- HTTP requests and responses
    
- JSON vs HTML data
    
- Parsing and data cleaning
    
- Exporting to spreadsheets
    
- Reading and respecting site policies
    
- Structuring a project repo
    

---

## ⚙️ **When You Get Stuck**

If you want to troubleshoot step-by-step instead of getting code:

- Tell me _what you tried_ and _what confused you_ (e.g., “I don’t understand why this variable is empty”).
    
- I’ll walk you through the logic, not hand you a fix.  
    That way, you actually _learn to think like a developer._


stuff installed in the environment:


- https://beautiful-soup-4.readthedocs.io/en/latest/
- https://pypi.org/project/requests-html/
- https://pypi.org/project/lxml/
- https://pypi.org/project/pandas/


Refer to "[[Understanding Installed Libraries]]" to get an idea of what Libraries are associated to this project

Refer to "[[Dumb Stuff I learned While Troubleshooting This]]" to see what issues were encountered and how they were resolved

site recommendation I just received:
https://cyber-atlas.caecommunity.org/competitions

see the notes on the process in "[[Building a spider with Scrapy]]"