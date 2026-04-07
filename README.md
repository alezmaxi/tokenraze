# TokenRaze

### *Let them think, not talk.*

See it: https://www.tokenraze.com/

Tokenraze reduces general expensive token consumption up to 60% for general tasks using cheapers model for prompt compression and output decompression with minimal/zero quality loss.

This, combined with recent RAG techniques provides a really efficient interface for many token intensive tasks.

---
 
## The Problem

Would you happily pay a $500/hour elite engineer to explain bit by bit the schematics while he's designing it for you?
Or would you prefer that engineer just to focus on the heavy lifting (making the schematics) and 
use competent technicians to do the explaining?

You're paying premium prices for models like GPT-4, Opus 4.6, and Gemini Pro. 
But most of what you're paying for isn't *thinking* — it's **verbosity.** 
Filler words, redundant phrasing, over-explanation. 
Intelligence can come cheaper. 
But the talking is expensive.
 
## The Insight
 
What if a cheap model compressed your prompt before it hits the expensive model, and another cheap model decompressed the output after?
 
```
Your prompt → Cheap model compresses → Expensive model reasons → Cheap model decompresses → Final output
```
 
The expensive model processes fewer tokens in AND out. The cheap models handle the cosmetics. Same quality. Fraction of the cost.
 
## How It Works
 
Three system prompts. That's the whole framework.
 
| Stage | Model | Role |
|-------|-------|------|
| **Compress** | Haiku / Gemini Flash Lite | Strips your prompt to minimum viable expression while preserving every idea, instruction, and detail |
| **Think** | Opus / GPT / Gemini Pro | Receives compressed input, reasons on it, outputs compressed response |
| **Decompress** | Haiku  / Gemini Flash Lite | Expands compressed output into natural, readable prose matching original tone |
 
The key innovation: **metadata tags** `[tone][expertise][voice][emotion]` travel with the compressed text, so the decompressor rebuilds not just the content but the *feel* of the response.
 
## Real Example
 
### Raw prompt (147 tokens):
```
Infer the amount of people at the top of the AI game right now in the world. 
Knows AI models, knows agents capabilities, knows how to deploy advanced AI 
workflows, senior devs (preferably). How many worldwide?
```
 
### After compression (29 tokens):
```
Globe Elite AI pros count?must dominate LLMs,agents,adv workflow deploys.
Senior dev bg is ideal.
```
 
### Expensive model output (compressed, 78 tokens):
```
Est.global count:2k-5k.Reqs cross-domain mastering(LLMs,agents,adv.deployment,
sr dev exp),tel talent rarissime.Pays↓:US~38%>CN~16%>UK~7%>CA~5%>DE~4%>IN~4%>
FR~3%>IL~3%>KR~2%>JP~2%|AI-biz mondial:Pure AI-native:~500-1k|AI=moteur 
rev/ops:~5k-10k.Adopters enterprise:~50k-100k|Secteurs clés:tech,fintech,
santé,véhicules auton.,défense,pharma,e-comm
```
 
### After decompression (full readable output):
 
> The estimated global population of professionals possessing elite AI R&D skills is quite limited, falling within the low-to-mid thousands, approximately 2,000 to 5,000 individuals worldwide.
>
> This particular niche demands deep, cross-domain expertise. Practitioners must be proficient in Large Language Models, autonomous agents, advanced deployment strategies, and possess significant senior-level development experience. The intersection of these specialized fields makes finding talent with this comprehensive background exceptionally rare.
>
> **Country Rankings:** The United States leads at approximately 38%, followed by China at around 16%. The United Kingdom comes in at roughly 7%, with Canada at 5%. Germany and India each account for about 4%, while France and Israel sit at approximately 3% each. South Korea and Japan round out the top ten at around 2% each.
>
> **AI-Integrated Businesses Worldwide:** Pure AI-native companies number approximately 500 to 1,000. Businesses where AI serves as a major revenue driver are estimated at 5,000 to 10,000. Broader enterprise-level AI adopters range from 50,000 to 100,000.
>
> **Key Sectors:** technology, fintech, healthcare, autonomous vehicles, defense, pharmaceuticals, and e-commerce.
 
**Same depth. Same accuracy. ~80% fewer tokens on the expensive model.**
 
---
 
## Token Savings Breakdown
 
| Metric | Without TokenRaze | With TokenRaze | Savings |
|--------|-------------------|----------------|---------|
| Expensive model input | 147 tokens | 29 tokens | **80%** |
| Expensive model output | ~320 tokens | ~78 tokens | **75%** |
| Total expensive tokens | ~467 tokens | ~107 tokens | **77%** |
| Cheap model overhead | 0 | ~600 tokens | pennies |
 
At scale (thousands of API calls/day), this translates directly to 50-75% reduction on your most expensive line item.
 
---
 
## The Prompts
 
Everything is in the [`/prompts`](./prompts) folder:
 
### Base Pipeline
- **[compress.txt](compress.txt)** — Compressor (cheap model)
- **[think.txt](think.txt)** — Thinker (expensive model)  
- **[decompress.txt](decompress.txt)** — Decompressor (cheap model)
 
### Extensions (Plug-ins)
- **[code_lean.ext](./extensions/code_lean.ext)** — Append to `think.txt` for code projects
- **[code_dress.ext](./extensions/code_dress.ext)** — Append to `decompress.txt` for code projects
 
Extensions are modular. If your project involves code, append the relevant `.ext` to your base prompt. If it's text-only, use the base pipeline as-is. No wasted tokens on unused instructions.
 
```
Text project:  compress.txt → think.txt → decompress.txt
Code project:  compress.txt → think.txt + code_lean.ext → decompress.txt + code_dress.ext
```
 
Future extensions (sql_lean.ext, data_lean.ext, etc.) follow the same pattern — bolt on what you need, ignore what you don't.
 
### Quick Start — Text Mode
 
1. Copy the system prompts
2. Set up your preferred cheap model (Haiku, Flash, etc.) with `compress.txt` as system prompt
3. Send user input → get compressed output
4. Send compressed output to expensive model with `think.txt` as system prompt
5. Send expensive model's response to cheap model with `decompress.txt` as system prompt
6. Return decompressed response to user
 
### Quick Start — Code Mode
 
1. Append `code_lean.ext` content to the end of `think.txt`
2. Append `code_dress.ext` content to the end of `decompress.txt`
3. Send your coding request to the expensive model → get lean output
4. Send lean output to cheap model → receive clean, readable, production-ready code
 
That's it. Works with any provider: Anthropic, OpenAI, Google, local models.
 
### Try It Now — Free

Option 1: 

Try it at: https://www.tokenraze.com/ (with limits, only a token limited Gemini Pro available)

Option 2: 

You can test TokenRaze flow in 5 minutes using AI providers projects/gems (free tier works). 
Create 2 projects/gems, paste one prompt into each as project instructions, then chain them.
 
**Step 1 — Create the Cheap Model Compressor/Decompressor project**
 
- Go to claude.ai → Projects → Create Project
- Name it `Compress-Decompress`
- In the project instructions, paste the contents of `compress.txt`
- Add 'If you receive a compressed text then use these rules:", then paste the contents of `decompress.txt`
- For code projects: also paste the contents of `extensions/code_dress.ext` at the end
 
**Step 2 — Create the Thinker project**
 
- Create another project → name it `Thinker`
- In the project instructions, paste the contents of `think.txt`
- For code projects: also paste the contents of `extensions/code_lean.ext` at the end
- Select the most capable model (Opus)

**Step 3 — Run the pipeline**
 
1. Open `Compress-Decompress` → select Haiku -> start a conversation → type your prompt → copy the compressed output
2. Open `Thinker` → start a conversation → paste the compressed prompt → copy the compressed response
3. Revisit `Compress-Decompress` → paste the compressed response → get your final readable output
4. Revisit (1) to continue the conversation
 
Compare the Thinker's compressed response length against what you'd normally get with a regular prompt. That's your savings.
 
> **Tip:** You can skip the Compressor if input prompts are short.
 
---
 
## Code Mode
 
Text mode compresses both ends. Code mode takes a different approach: **the expensive model writes lean code, the cheap model dresses it.**
 
The insight: output tokens are the most expensive. A 200-line function with verbose variable names, comments, and formatting costs significantly more than the same logic written lean. The expensive model loses zero reasoning quality — it *knows* what `v1` is because it just decided what `v1` holds. The cheap model infers meaning from context and restores readable names.
 
```
Your coding request → Expensive model (lean output) → Cheap model (dresses it) → Production code
```
 
### Code Mode: PHP Example
 
**Task:** "PHP script to handle bookmark AJAX calls — add, remove, list."
 
**Opus 4.6 lean output (~250 tokens):**
```php
<?php
require_once '../ath_main.php';
$v1=$_POST['action']??'';$v2=intval($_POST['user_id']??0);$v3=intval($_POST['item_id']??0);
if(!$v2){J(R('e','ERR101'));exit;}
if($v1=='add'){Q("INSERT INTO bookmarks (user_id,item_id,created_at) VALUES (?,?,NOW())",[$v2,$v3]);
J(R('ok',['id'=>$conn->lastInsertId()]));}
elseif($v1=='remove'){Q("DELETE FROM bookmarks WHERE user_id=? AND item_id=?",[$v2,$v3]);J(R('ok'));}
elseif($v1=='list'){$v4=Q("SELECT item_id,created_at FROM bookmarks WHERE user_id=? ORDER BY created_at DESC",[$v2]);
J(R('ok',['data'=>$v4->fetchAll(PDO::FETCH_ASSOC)]));}
else{J(R('e','ERR102'));}
```
 
**Haiku dressed output:**
```php
<?php
require_once '../ath_main.php';
 
// Input sanitization
$action = $_POST['action'] ?? '';
$user_id = intval($_POST['user_id'] ?? 0);
$item_id = intval($_POST['item_id'] ?? 0);
 
if (!$user_id) {
    echo json_encode(['status' => 'error', 'msg' => 'ERR101']);
    exit;
}
 
// Add bookmark
if ($action == 'add') {
    $stmt = $conn->prepare("INSERT INTO bookmarks (user_id, item_id, created_at) VALUES (?, ?, NOW())");
    $stmt->execute([$user_id, $item_id]);
    echo json_encode(['status' => 'ok', 'id' => $conn->lastInsertId()]);
 
// Remove bookmark
} elseif ($action == 'remove') {
    $stmt = $conn->prepare("DELETE FROM bookmarks WHERE user_id = ? AND item_id = ?");
    $stmt->execute([$user_id, $item_id]);
    echo json_encode(['status' => 'ok']);
 
// List bookmarks
} elseif ($action == 'list') {
    $stmt = $conn->prepare("SELECT item_id, created_at FROM bookmarks WHERE user_id = ? ORDER BY created_at DESC");
    $stmt->execute([$user_id]);
    echo json_encode(['status' => 'ok', 'data' => $stmt->fetchAll(PDO::FETCH_ASSOC)]);
 
} else {
    echo json_encode(['status' => 'error', 'msg' => 'ERR102']);
}
```
 
### Code Mode: JS Example
 
**Task:** "JS function to manage todos — add, delete, toggle, load via fetch."
 
**Opus lean output (~370 tokens):**
```js
function f1(p1,p2={}){
var v1=D('todo_input'),v2=D('todo_list'),v3=new FormData();
FA(v3,'action',p1);
if(p1=='add'){var v4=v1.value.trim();if(!v4)return;
FA(v3,'text',v4);F('script/todo_functions.php',{method:'POST',body:v3}).then(v5=>{
if(v5.status=='ok'){v1.value='';f2(v5.data);}});}
else if(p1=='delete'){FA(v3,'id',p2.id);
F('script/todo_functions.php',{method:'POST',body:v3}).then(v5=>{
if(v5.status=='ok'){D('todo_'+p2.id).remove();}});}
else if(p1=='toggle'){FA(v3,'id',p2.id);
F('script/todo_functions.php',{method:'POST',body:v3}).then(v5=>{
if(v5.status=='ok'){CL(D('todo_'+p2.id),'toggle','completed');}});}
else if(p1=='load'){
F('script/todo_functions.php',{method:'POST',body:v3}).then(v5=>{
if(v5.status=='ok'){v2.innerHTML='';v5.data.forEach(v6=>f2(v6));}});}
}
function f2(p1){var v1=D('todo_list'),v2=CE('div');
v2.id='todo_'+p1.id;v2.className='todo_item'+(p1.done?' completed':'');
v2.innerHTML='<span onclick="f1(\'toggle\',{id:'+p1.id+'})">'+p1.text+'</span><button onclick="f1(\'delete\',{id:'+p1.id+'})">X</button>';
v1.appendChild(v2);}
EL(document,'DOMContentLoaded',function(){f1('load');EL(D('todo_btn'),'click',function(){f1('add');});});
```
 
**Haiku dressed output:**
```js
// Todo Manager
function manageTodos(action, data = {}) {
    var todoInput = document.getElementById('todo_input');
    var todoList = document.getElementById('todo_list');
    var formData = new FormData();
    formData.append('action', action);
 
    // Add todo
    if (action == 'add') {
        var text = todoInput.value.trim();
        if (!text) return;
        formData.append('text', text);
        fetch('script/todo_functions.php', {
            method: 'POST',
            body: formData
        }).then(r => r.json()).then(res => {
            if (res.status == 'ok') {
                todoInput.value = '';
                renderTodo(res.data);
            }
        });
 
    // Delete todo
    } else if (action == 'delete') {
        formData.append('id', data.id);
        fetch('script/todo_functions.php', {
            method: 'POST',
            body: formData
        }).then(r => r.json()).then(res => {
            if (res.status == 'ok') {
                document.getElementById('todo_' + data.id).remove();
            }
        });
 
    // Toggle complete
    } else if (action == 'toggle') {
        formData.append('id', data.id);
        fetch('script/todo_functions.php', {
            method: 'POST',
            body: formData
        }).then(r => r.json()).then(res => {
            if (res.status == 'ok') {
                document.getElementById('todo_' + data.id).classList.toggle('completed');
            }
        });
 
    // Load all todos
    } else if (action == 'load') {
        fetch('script/todo_functions.php', {
            method: 'POST',
            body: formData
        }).then(r => r.json()).then(res => {
            if (res.status == 'ok') {
                todoList.innerHTML = '';
                res.data.forEach(item => renderTodo(item));
            }
        });
    }
}
 
// Render single todo item
function renderTodo(item) {
    var todoList = document.getElementById('todo_list');
    var div = document.createElement('div');
    div.id = 'todo_' + item.id;
    div.className = 'todo_item' + (item.done ? ' completed' : '');
    div.innerHTML = '<span onclick="manageTodos(\'toggle\',{id:' + item.id + '})">' + item.text + '</span>' +
        '<button onclick="manageTodos(\'delete\',{id:' + item.id + '})">X</button>';
    todoList.appendChild(div);
}
 
// Init
document.addEventListener('DOMContentLoaded', function() {
    manageTodos('load');
    document.getElementById('todo_btn').addEventListener('click', function() {
        manageTodos('add');
    });
});
```
 
### Code Mode Savings
 
| | Normal Opus Output | Lean Opus Output | Savings |
|---|---|---|---|
| PHP example | ~530 tokens | ~250 tokens | **53%** |
| JS example | ~780 tokens | ~370 tokens | **52%** |
| Haiku dressing cost | — | ~1000 tokens (in+out) | pennies |
 
The expensive model does the thinking. The cheap model does the talking. Same logic. Same quality. Half the cost.
 
---
 
## Why This Works
 
Most AI cost-reduction approaches sacrifice quality (use a smaller model router), reduce context/details (RAG limitations), or require expensive fine-tuning.
 
TokenRaze doesn't touch the reasoning nor the actual response meat. The expensive model still does all the thinking. 

We just compress the I/O wrapper around that thinking.
 
**Think of it this way:** Following with the engineer example, you wouldn't pay a surgeon's rate for the nurse to read a you post-op instructions. Let the surgeon operate. Let the nurse communicate.
 
---
 
## Compatibility

Model-agnostic. Provider-agnostic. Language-agnostic.
 
---
 
## What's Next
 
V1 ships with a **modular plug-in architecture**: base pipeline + bolt-on extensions. Generic Code mode is the first extension, which try to cover JS syntax as a start. 
 
**Planned extensions:**
- **data_lean.ext / data_dress.ext** — Structured data compression
- **sql_lean.ext / sql_dress.ext** — Database query optimization
More could be coming (Python,C,Java,etc)
 
The base pipeline stays stable. Extensions grow the ecosystem.
 
Follow this repo for updates.
 
---
 
## Who Benefits
 
- **Developers** burning through API budgets on premium models
- **Startups** that need GPT-4/Opus quality but can only afford Haiku/Flash pricing
- **Enterprises** processing thousands of AI calls daily
- **AI SaaS products** where every token is margin erosion
- **Subscription AI providers** absorbing token costs on flat-fee plans
 
---
 
## License
 
MIT — Use it, fork it, build on it.
 
---
 
## Author
 
Built by [Alex Maxi] (https://www.linkedin.com/in/alex-maxi-53b373203/)
