<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>照片編號 PDF 淺藍框選工具｜最終版</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf-lib/1.17.1/pdf-lib.min.js"></script>
  <style>
    body{font-family:system-ui,-apple-system,"Segoe UI",sans-serif;background:#f5f7fb;margin:0;color:#102033}
    .wrap{max-width:1100px;margin:28px auto;padding:0 18px}
    .card{background:white;border-radius:18px;padding:24px;box-shadow:0 8px 28px rgba(16,32,51,.08)}
    h1{font-size:26px;margin:0 0 12px}.muted{color:#60708a;font-size:14px;line-height:1.7}
    label{font-weight:700;display:block;margin-top:18px;margin-bottom:8px}
    input[type=file],textarea{width:100%;box-sizing:border-box;border:1px solid #c9d4e5;border-radius:10px;background:#fff;padding:12px;font-size:16px}
    textarea{height:150px;resize:vertical;font-family:ui-monospace,SFMono-Regular,Consolas,monospace}
    button{border:0;border-radius:12px;padding:13px 20px;font-size:16px;font-weight:800;cursor:pointer;margin:14px 10px 0 0}
    .primary{background:#2f6df6;color:#fff}.secondary{background:#e7edf6;color:#102033}.danger{background:#6b7b92;color:#fff}
    #status{margin-top:16px;white-space:pre-wrap;font-weight:650;line-height:1.7}.ok{color:#126a2a}.warn{color:#a35a00}.err{color:#a20d0d}
    .preview{margin-top:22px;display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:16px}
    .pagebox{background:#fff;border:1px solid #dbe5f2;border-radius:14px;padding:12px;box-shadow:0 4px 14px rgba(16,32,51,.06)}
    canvas{width:100%;height:auto;border:1px solid #d4deec;border-radius:8px;background:white}
    .pageTitle{font-weight:800;margin-bottom:8px}.tag{font-size:13px;background:#eaf3ff;color:#075baf;border-radius:999px;padding:4px 9px;margin-left:6px}
  </style>
</head>
<body>
<div class="wrap">
  <div class="card">
    <h1>照片編號 PDF 淺藍框選工具｜最終版</h1>
    <div class="muted">
      功能：上傳完整 PDF，輸入「要標的照片編號清單」，工具會在 PDF 文字層尋找「照片 N」標籤，並只框選該標籤文字位置。<br>
      適用不同案場版型；不切單張照片、不重新排版，直接輸出完整 PDF。框線為淺藍色透明。<br>
      可輸入：<b>1, 2, 4-6</b> 或一行一個編號。若 PDF 是純掃描圖片且沒有文字層，需先 OCR 後再使用。
    </div>

    <label>1. 選擇照片 PDF</label>
    <input id="pdfFile" type="file" accept="application/pdf" />

    <label>2. 要標的照片編號清單</label>
    <textarea id="numbers" placeholder="例如：&#10;1&#10;11&#10;33, 55&#10;100-105"></textarea>

    <button class="primary" id="runBtn">產生淺藍框選 PDF</button>
    <button class="secondary" id="previewBtn">只預覽定位</button>
    <button class="danger" id="clearBtn">清除</button>

    <div id="status"></div>
    <div id="preview" class="preview"></div>
  </div>
</div>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc = "https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js";

const { PDFDocument, rgb } = PDFLib;
let lastPdfBytes = null;
let lastMatches = [];

function parseNumbers(text){
  const out = new Set();
  const tokens = text.replace(/[，、;；\n\r\t]+/g, ',').split(',').map(s=>s.trim()).filter(Boolean);
  for(const t of tokens){
    const m = t.match(/^(\d+)\s*-\s*(\d+)$/);
    if(m){
      let a = parseInt(m[1],10), b = parseInt(m[2],10);
      if(a>b) [a,b]=[b,a];
      for(let i=a;i<=b;i++) out.add(i);
    }else{
      const n = parseInt(t.replace(/\D/g,''),10);
      if(Number.isFinite(n) && n>0) out.add(n);
    }
  }
  return [...out].sort((a,b)=>a-b);
}

function normalizeChar(ch){
  if(!ch) return '';
  const c = ch.normalize('NFKC');
  if(/\s/.test(c)) return '';
  return c;
}

function buildLines(textContent, viewport){
  const items = [];
  for(const it of textContent.items){
    if(!it.str || !it.str.trim()) continue;
    const tx = pdfjsLib.Util.transform(viewport.transform, it.transform);
    const x = tx[4];
    const y = tx[5];
    const fontH = Math.max(5, Math.abs(tx[3]) || Math.abs(it.height || 8));
    const w = Math.max(1, it.width || (it.str.length * fontH * .55));
    items.push({str:it.str, x, y, w, h:fontH});
  }
  items.sort((a,b)=> a.y===b.y ? a.x-b.x : a.y-b.y);

  const lines=[];
  const tol=4;
  for(const it of items){
    let line = lines.find(l => Math.abs(l.y - it.y) <= tol);
    if(!line){ line={y:it.y, items:[]}; lines.push(line); }
    line.items.push(it);
    line.y = (line.y*(line.items.length-1)+it.y)/line.items.length;
  }
  for(const line of lines) line.items.sort((a,b)=>a.x-b.x);
  return lines;
}

function lineToSearchable(line){
  const chars=[];
  let normalized='';
  const normMap=[];

  for(const it of line.items){
    const raw=[...it.str];
    const len=Math.max(raw.length,1);
    for(let i=0;i<raw.length;i++){
      const ch=raw[i];
      const x0=it.x + it.w * (i/len);
      const x1=it.x + it.w * ((i+1)/len);
      const y0=it.y - it.h * 1.05;
      const y1=it.y + it.h * .25;
      const rec={ch,x0,x1,y0,y1};
      chars.push(rec);
      const nc=normalizeChar(ch);
      if(nc){
        normalized += nc;
        normMap.push(rec);
      }
    }
  }
  return {normalized, normMap, chars};
}

function rectFromNormRange(normMap, start, end, viewport){
  const parts = normMap.slice(start,end);
  if(!parts.length) return null;
  let x0=Math.min(...parts.map(p=>p.x0));
  let x1=Math.max(...parts.map(p=>p.x1));
  let y0=Math.min(...parts.map(p=>p.y0));
  let y1=Math.max(...parts.map(p=>p.y1));

  // 只框「照片 N」文字，稍微加一點邊距，避免蓋不到冒號或字邊。
  const padX=3, padY=2;
  x0-=padX; x1+=padX; y0-=padY; y1+=padY;

  const pA = viewport.convertToPdfPoint(x0, y0);
  const pB = viewport.convertToPdfPoint(x1, y1);
  const left=Math.min(pA[0],pB[0]);
  const right=Math.max(pA[0],pB[0]);
  const bottom=Math.min(pA[1],pB[1]);
  const top=Math.max(pA[1],pB[1]);
  return {x:left,y:bottom,width:right-left,height:top-bottom, vx:x0, vy:y0, vw:x1-x0, vh:y1-y0};
}

async function findPhotoLabels(pdfBytes, targets){
  const pdf = await pdfjsLib.getDocument({data: pdfBytes.slice(0)}).promise;
  const wanted = new Set(targets.map(String));
  const found = new Map(targets.map(n => [String(n), []]));

  for(let pageNo=1; pageNo<=pdf.numPages; pageNo++){
    const page = await pdf.getPage(pageNo);
    const viewport = page.getViewport({scale:1});
    const tc = await page.getTextContent({normalizeWhitespace:false, disableCombineTextItems:false});
    const lines = buildLines(tc, viewport);

    for(const line of lines){
      const {normalized, normMap} = lineToSearchable(line);
      if(!normalized.includes('照片')) continue;

      for(const n of wanted){
        // 嚴格匹配「照片N」，並避免 1 誤中 11：下一個字若仍是數字就不算。
        const pat = '照片' + n;
        let idx = -1;
        while((idx = normalized.indexOf(pat, idx+1)) !== -1){
          const before = normalized[idx-1] || '';
          const after = normalized[idx + pat.length] || '';
          if(/\d/.test(before) || /\d/.test(after)) continue;
          const rect = rectFromNormRange(normMap, idx, idx+pat.length, viewport);
          if(rect){
            found.get(n).push({pageNo, rect});
          }
        }
      }
    }
  }

  const matches=[];
  for(const n of targets.map(String)){
    const arr = found.get(n) || [];
    for(const m of arr) matches.push({number:n, ...m});
  }
  return {numPages: pdf.numPages, matches, found};
}

async function drawPdf(pdfBytes, matches){
  const doc = await PDFDocument.load(pdfBytes);
  const pages = doc.getPages();
  for(const m of matches){
    const p = pages[m.pageNo-1];
    const r = m.rect;
    p.drawRectangle({
      x:r.x, y:r.y, width:r.width, height:r.height,
      color: rgb(0.55,0.85,1), opacity:0.30,
      borderColor: rgb(0.10,0.55,0.95), borderOpacity:0.85,
      borderWidth: 1.5
    });
  }
  return await doc.save({useObjectStreams:false});
}

function downloadBytes(bytes, filename){
  const blob = new Blob([bytes], {type:'application/pdf'});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = filename;
  document.body.appendChild(a); a.click(); a.remove();
  setTimeout(()=>URL.revokeObjectURL(a.href), 5000);
}

async function renderPreview(pdfBytes, matches){
  const box=document.getElementById('preview'); box.innerHTML='';
  const grouped = new Map();
  for(const m of matches){
    if(!grouped.has(m.pageNo)) grouped.set(m.pageNo, []);
    grouped.get(m.pageNo).push(m);
  }
  const pdf = await pdfjsLib.getDocument({data: pdfBytes.slice(0)}).promise;
  for(const [pageNo, arr] of grouped){
    const page = await pdf.getPage(pageNo);
    const viewport = page.getViewport({scale:1.4});
    const canvas=document.createElement('canvas');
    canvas.width=viewport.width; canvas.height=viewport.height;
    const ctx=canvas.getContext('2d');
    await page.render({canvasContext:ctx, viewport}).promise;

    const vp1 = page.getViewport({scale:1});
    ctx.save();
    ctx.fillStyle='rgba(85, 200, 255, .30)';
    ctx.strokeStyle='rgba(15, 130, 220, .90)';
    ctx.lineWidth=2;
    for(const m of arr){
      const r=m.rect;
      const p1=vp1.convertToViewportPoint(r.x, r.y);
      const p2=vp1.convertToViewportPoint(r.x+r.width, r.y+r.height);
      const scale=1.4;
      const x=Math.min(p1[0],p2[0])*scale, y=Math.min(p1[1],p2[1])*scale;
      const w=Math.abs(p2[0]-p1[0])*scale, h=Math.abs(p2[1]-p1[1])*scale;
      ctx.fillRect(x,y,w,h); ctx.strokeRect(x,y,w,h);
    }
    ctx.restore();

    const div=document.createElement('div'); div.className='pagebox';
    const nums=arr.map(x=>x.number).join(', ');
    div.innerHTML=`<div class="pageTitle">第 ${pageNo} 頁 <span class="tag">照片 ${nums}</span></div>`;
    div.appendChild(canvas); box.appendChild(div);
  }
}

function setStatus(msg, cls=''){
  const s=document.getElementById('status'); s.className=cls; s.textContent=msg;
}

async function run(makePdf){
  try{
    const file=document.getElementById('pdfFile').files[0];
    const targets=parseNumbers(document.getElementById('numbers').value);
    document.getElementById('preview').innerHTML='';
    if(!file) return setStatus('請先選擇 PDF。','err');
    if(!targets.length) return setStatus('請輸入至少一個照片編號。','err');
    setStatus('讀取 PDF 並搜尋「照片 N」文字中，請稍候…');
    const pdfBytes = new Uint8Array(await file.arrayBuffer());
    lastPdfBytes=pdfBytes;
    const {matches, found} = await findPhotoLabels(pdfBytes, targets);
    lastMatches=matches;

    const missing = targets.filter(n => !(found.get(String(n))||[]).length);
    const repeated = targets.filter(n => (found.get(String(n))||[]).length > 1);
    let msg = `輸入 ${targets.length} 個編號；找到 ${matches.length} 處；框選 ${matches.length} 處。`;
    if(missing.length) msg += `\n找不到：${missing.join(', ')}`;
    if(repeated.length) msg += `\n注意：以下編號在 PDF 中出現超過一次，已全部框選：${repeated.join(', ')}`;

    if(!matches.length){
      msg += '\n\n若確定 PDF 裡看得到「照片 N」，通常代表該 PDF 是掃描圖片或文字層被做成圖片，請先用 Acrobat / OCR 軟體辨識文字後再使用。';
      return setStatus(msg, 'err');
    }

    await renderPreview(pdfBytes, matches);
    if(makePdf){
      setStatus(msg + '\n正在輸出完整 PDF…', missing.length?'warn':'ok');
      const out = await drawPdf(pdfBytes, matches);
      const base = file.name.replace(/\.pdf$/i,'');
      downloadBytes(out, `${base}_淺藍框選.pdf`);
      setStatus(msg + '\n已輸出完整 PDF。', missing.length?'warn':'ok');
    }else{
      setStatus(msg + '\n已完成預覽，確認正確後可按「產生淺藍框選 PDF」。', missing.length?'warn':'ok');
    }
  }catch(e){
    console.error(e);
    setStatus('發生錯誤：' + (e && e.message ? e.message : e), 'err');
  }
}

document.getElementById('runBtn').addEventListener('click',()=>run(true));
document.getElementById('previewBtn').addEventListener('click',()=>run(false));
document.getElementById('clearBtn').addEventListener('click',()=>{
  document.getElementById('numbers').value='';
  document.getElementById('preview').innerHTML='';
  setStatus('');
});
</script>
</body>
</html>
