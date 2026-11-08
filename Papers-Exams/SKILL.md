---
name: "pdf-to-paper-quiz"
description: "Turn an uploaded exam or quiz PDF into a simple exam-paper-style interactive HTML quiz with Submit, Reset and Shuffle buttons."
---

# PDF to paper-style interactive quiz

Use when the user sends a PDF (or pasted text) of multiple-choice questions and wants an interactive HTML/CSS quiz, "like the paper", or says "make this also".

## Steps
1. Read the PDF fully (extract text; rasterize pages if the text is garbled). Capture every question, its options (a)-(e), and any code or CLIPS snippets exactly.
2. If the PDF has several separate quizzes/sections, make one entry per quiz in QUIZZES (they become tabs). Otherwise one entry.
3. Work out the correct answer for each question yourself (the PDF usually has no key). For code questions, trace the code carefully, or run it with Bash when possible. Never guess: for any uncertain or debatable answer, note it in the final message.
4. Fill the template below: replace `/*DATA*/` with the QUIZZES object. Each quiz: `{title, heading, qs:[{q, code?, o:[...], a:<index of correct option 0-based>, e:<one-line explanation>}]}`. Keep option text as in the PDF (including "All answers are true"). Put code in `code`, not in `q`.
5. Save as a single self-contained .html file (CSS and JS inline, no external libraries) and deliver it with SendUserFile. Name it after the quiz, e.g. `ai-quiz-paper.html`.
6. Validate JS syntax before sending (e.g. node check) and make sure the number of questions matches the PDF.
7. Final message: short. Say what the page does (paper look, tap to choose, Submit shows answers and score, Reset, Shuffle randomizes question order) and list any answers worth double-checking. Reply in the language the user writes in.

## Design rules (do not change unless asked)
- Plain exam paper: white sheet, black border, Times New Roman, Name/ID lines at top, questions numbered `[1]`, options as (a) (b) (c) (d) in two columns.
- Feedback only after Submit: correct option green, wrong pick red with strikethrough, explanation line, score.
- Footer buttons: Shuffle (random question order, clears answers, renumbers), Reset (original order, clears answers), Submit.
- Shuffle changes question order only, never option order (options like "All answers are true" depend on position).
- Prints cleanly (print CSS hides buttons).

## Template
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Quiz</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#e9e9e9;font-family:"Times New Roman",Times,serif;color:#000;padding:20px 10px}
.switch{max-width:820px;margin:0 auto 12px;display:flex;gap:8px;font-family:Arial,sans-serif;flex-wrap:wrap}
.switch button{padding:8px 14px;border:1px solid #000;background:#fff;cursor:pointer;font-size:14px}
.switch button.on{background:#000;color:#fff}
.paper{max-width:820px;margin:0 auto;background:#fff;border:1px solid #000;padding:36px 44px;box-shadow:0 2px 8px rgba(0,0,0,.25)}
h1{font-size:20px;text-align:center;margin:0 0 14px;letter-spacing:.5px}
.info{display:flex;gap:20px;margin-bottom:22px;font-weight:bold;font-size:16px}
.info label{display:flex;align-items:baseline;gap:6px;flex:1}
.info label:last-child{flex:.7}
.info input{flex:1;border:none;border-bottom:1px solid #000;font:inherit;font-weight:normal;outline:none;min-width:0}
.q{margin-bottom:18px;font-size:16px;line-height:1.35;break-inside:avoid}
.qt{white-space:pre-wrap}
.qt b{margin-right:4px}
pre{margin:6px 0 6px 18px;font-family:"Courier New",monospace;font-size:14px;background:#f4f4f4;border-left:3px solid #999;padding:6px 10px;overflow:auto}
.opts{display:grid;grid-template-columns:1fr 1fr;gap:2px 20px;margin:4px 0 0 18px}
.opt{display:flex;gap:6px;padding:3px 6px;cursor:pointer;border:1px solid transparent;border-radius:3px}
.opt:hover{background:#f0f0f0}
.opt.sel{background:#ddd;border-color:#000;font-weight:bold}
.opt.ok{background:#d6f5d6;border-color:#2a8a2a;font-weight:bold}
.opt.no{background:#fbd5d5;border-color:#b22;text-decoration:line-through}
.exp{margin:4px 0 0 18px;font-size:14px;font-style:italic;color:#333;display:none}
.exp.show{display:block}
.foot{display:flex;justify-content:space-between;align-items:center;margin-top:26px;border-top:1px solid #000;padding-top:14px;font-family:Arial,sans-serif;flex-wrap:wrap;gap:10px}
.foot button{padding:9px 18px;border:1px solid #000;background:#000;color:#fff;cursor:pointer;font-size:14px}
.foot button.alt{background:#fff;color:#000}
.score{font-size:18px;font-weight:bold}
@media(max-width:600px){.paper{padding:20px 16px}.opts{grid-template-columns:1fr}.info{flex-direction:column;gap:8px}}
@media print{body{background:#fff;padding:0}.switch,.foot button{display:none}.paper{border:none;box-shadow:none}}
</style>
</head>
<body>
<div class="switch" id="switch"></div>
<div class="paper">
  <h1 id="title"></h1>
  <div class="info"><label>Name: <input type="text"></label><label>ID: <input type="text"></label></div>
  <div id="qs"></div>
  <div class="foot"><span class="score" id="score"></span><span><button class="alt" id="shuffle">🔀 Shuffle</button> <button class="alt" id="reset">Reset</button> <button id="submit">Submit</button></span></div>
</div>
<script>
// a = index of correct option (0=a, 1=b, ...). code = optional code block. e = short explanation.
const QUIZZES = /*DATA*/;

const st = {};
const ident = n => Array.from({length:n},(_,i)=>i);
function fresh(k){ const n = QUIZZES[k].qs.length; return {order:ident(n), ans:Array(n).fill(null), done:false}; }
for (const k in QUIZZES) st[k] = fresh(k);
let key = Object.keys(QUIZZES)[0];
const $ = id => document.getElementById(id);
const esc = s => s.replace(/[&<>]/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;'}[c]));

function render(){
  const S = st[key], Q = QUIZZES[key].qs;
  $('switch').innerHTML = Object.keys(QUIZZES).length>1 ? Object.keys(QUIZZES).map(k=>`<button class="${k===key?'on':''}" data-k="${k}">${QUIZZES[k].title}</button>`).join('') : '';
  $('switch').querySelectorAll('button').forEach(b=>b.onclick=()=>{key=b.dataset.k;render();window.scrollTo(0,0);});
  $('title').textContent = QUIZZES[key].heading || QUIZZES[key].title.toUpperCase();
  $('qs').innerHTML = S.order.map((qi,pos)=>{
    const q = Q[qi];
    const opts = q.o.map((t,j)=>{
      let c='opt';
      if (S.done){ if(j===q.a) c+=' ok'; else if(S.ans[qi]===j) c+=' no'; }
      else if (S.ans[qi]===j) c+=' sel';
      return `<div class="${c}" data-q="${qi}" data-j="${j}"><span>(${'abcde'[j]})</span><span>${esc(t)}</span></div>`;
    }).join('');
    return `<div class="q"><div class="qt"><b>[${pos+1}]</b>${esc(q.q)}</div>${q.code?`<pre>${esc(q.code)}</pre>`:''}<div class="opts">${opts}</div><div class="exp ${S.done?'show':''}">Answer: (${'abcde'[q.a]}) — ${esc(q.e||'')}</div></div>`;
  }).join('');
  document.querySelectorAll('.opt').forEach(o=>o.onclick=()=>{
    if (S.done) return;
    S.ans[+o.dataset.q] = +o.dataset.j;
    const y = window.scrollY; render(); window.scrollTo(0,y);
  });
  const score = S.ans.filter((a,i)=>a===Q[i].a).length;
  $('score').textContent = S.done ? `Score: ${score} / ${Q.length}` : `Answered: ${S.ans.filter(a=>a!==null).length} / ${Q.length}`;
  $('submit').style.display = S.done ? 'none' : '';
}
$('submit').onclick = () => { st[key].done = true; render(); $('score').scrollIntoView({behavior:'smooth'}); };
$('reset').onclick = () => { st[key] = fresh(key); render(); window.scrollTo(0,0); };
$('shuffle').onclick = () => {
  const s = fresh(key);
  for (let i=s.order.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [s.order[i],s.order[j]]=[s.order[j],s.order[i]]; }
  st[key] = s; render(); window.scrollTo(0,0);
};
render();
</script>
</body>
</html>
```