<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Карта раскроя ткани плиссе</title>
<style>
  :root{
    --bg:#f3f4f6;--card:#fff;--dark:#111827;--muted:#6b7280;--line:#e5e7eb;--accent:#111827;--soft:#f9fafb;
  }
  *{box-sizing:border-box}
  body{margin:0;font-family:Arial, sans-serif;background:var(--bg);color:#111827;padding:18px}
  .wrap{max-width:1180px;margin:0 auto}
  .header{background:var(--dark);color:#fff;border-radius:18px;padding:18px 20px;margin-bottom:16px;box-shadow:0 8px 24px rgba(0,0,0,.12)}
  .header h1{margin:0;font-size:24px}.header p{margin:6px 0 0;color:#d1d5db;font-size:14px}
  .card{background:var(--card);border-radius:18px;padding:16px;margin-bottom:16px;box-shadow:0 6px 20px rgba(0,0,0,.08)}
  .controls{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}
  label{display:block;font-size:12px;color:var(--muted);margin-bottom:5px}
  input,select{width:100%;height:40px;border:1px solid var(--line);border-radius:10px;padding:0 10px;font-size:14px;background:#fff}
  button{border:0;border-radius:12px;padding:11px 14px;background:var(--accent);color:#fff;font-weight:700;cursor:pointer}
  button.secondary{background:#e5e7eb;color:#111827} button.danger{background:#7f1d1d;color:#fff}
  .actions{display:flex;gap:10px;flex-wrap:wrap;margin-top:12px}
  table{width:100%;border-collapse:separate;border-spacing:0;overflow:hidden;border-radius:14px}
  th{background:#111827;color:#fff;font-size:13px;text-align:left;padding:10px}td{background:#fff;border-bottom:1px solid var(--line);padding:8px}td input{height:36px}
  tr:last-child td{border-bottom:0}.table-wrap{overflow:auto}
  .summary{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}.sum-box{background:#111827;color:#fff;border-radius:14px;padding:14px}.sum-box span{display:block;font-size:12px;color:#d1d5db}.sum-box strong{font-size:22px}
  .layout-head{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:12px}.note{font-size:13px;color:var(--muted)}
  .roll{width:100%;background:var(--soft);border:2px solid #111827;border-radius:14px;padding:10px;overflow:auto}
  .row{display:flex;align-items:stretch;border-bottom:1px dashed #9ca3af;min-width:760px;position:relative}.row:last-child{border-bottom:0}
  .piece{border:1px solid #111827;background:#dbeafe;margin:4px 2px;border-radius:8px;display:flex;align-items:center;justify-content:center;text-align:center;font-size:12px;font-weight:700;min-width:42px;min-height:44px;padding:4px;overflow:hidden}
  .waste{background:#f3f4f6;border:1px dashed #9ca3af;color:#6b7280;font-weight:400}.row-label{width:72px;flex:0 0 72px;font-size:12px;color:#374151;padding:8px 6px;background:#fff;border-right:1px solid #e5e7eb}.row-pieces{display:flex;flex:1}
  @media(max-width:760px){body{padding:10px}.controls,.summary{grid-template-columns:1fr}.header h1{font-size:20px}.card{padding:12px}th,td{font-size:12px;padding:6px}.actions button{flex:1}.layout-head{display:block}.roll{padding:6px}.row{min-width:680px}}
  @media print{body{background:#fff;padding:0}.actions,.no-print{display:none}.card,.header{box-shadow:none;border:1px solid #ddd}.header{color:#fff;-webkit-print-color-adjust:exact;print-color-adjust:exact}.piece,.sum-box,th{print-color-adjust:exact;-webkit-print-color-adjust:exact}}
</style>
</head>
<body>
<div class="wrap">
  <div class="header">
    <h1>Карта раскроя ткани плиссе</h1>
    <p>Раскладка деталей по ширине рулона с расчетом общей длины ткани</p>
  </div>

  <div class="card">
    <div class="controls">
      <div><label>Артикул ткани</label><input id="fabricName" value="ROMA 04"></div>
      <div><label>Ширина рулона, см</label><input id="rollWidth" type="number" value="300" min="1"></div>
      <div><label>Припуск к каждой детали, см</label><input id="allowance" type="number" value="1" min="0" step="0.5"></div>
      <div><label>Сортировка</label><select id="sortMode"><option value="height">Сначала высокие</option><option value="width">Сначала широкие</option><option value="none">Как в таблице</option></select></div>
    </div>
    <div class="actions no-print">
      <button onclick="addRow()">Добавить строку</button>
      <button class="secondary" onclick="calculate()">Рассчитать карту</button>
      <button class="secondary" onclick="window.print()">Печать / PDF</button>
      <button class="danger" onclick="clearRows()">Очистить</button>
    </div>
  </div>

  <div class="card">
    <div class="table-wrap">
      <table>
        <thead><tr><th>№</th><th>Ширина, см</th><th>Высота, см</th><th>Кол-во</th><th>Комментарий</th><th class="no-print">Удалить</th></tr></thead>
        <tbody id="tbody"></tbody>
      </table>
    </div>
  </div>

  <div class="card">
    <div class="summary">
      <div class="sum-box"><span>Количество изделий</span><strong id="sumQty">0</strong></div>
      <div class="sum-box"><span>Фактическая площадь</span><strong id="sumArea">0 м²</strong></div>
      <div class="sum-box"><span>Длина ткани</span><strong id="sumLength">0 м</strong></div>
      <div class="sum-box"><span>Использование ширины</span><strong id="sumUse">0%</strong></div>
    </div>
  </div>

  <div class="card">
    <div class="layout-head">
      <div>
        <h2 style="margin:0;font-size:20px">Карта раскроя</h2>
        <div class="note" id="layoutNote">После расчета здесь появится схема раскладки.</div>
      </div>
    </div>
    <div class="roll" id="layout"></div>
  </div>
</div>

<script>
const tbody = document.getElementById('tbody');

function addRow(w='', h='', q=1, c=''){
  const tr = document.createElement('tr');
  tr.innerHTML = `
    <td class="num"></td>
    <td><input type="number" min="1" value="${w}" oninput="calculate()"></td>
    <td><input type="number" min="1" value="${h}" oninput="calculate()"></td>
    <td><input type="number" min="1" value="${q}" oninput="calculate()"></td>
    <td><input type="text" value="${c}" oninput="calculate()"></td>
    <td class="no-print"><button class="danger" onclick="this.closest('tr').remove();renumber();calculate()">×</button></td>`;
  tbody.appendChild(tr); renumber(); calculate();
}
function renumber(){[...tbody.querySelectorAll('tr')].forEach((tr,i)=>tr.querySelector('.num').textContent=i+1)}
function clearRows(){tbody.innerHTML=''; addRow();}
function getPieces(){
  const allowance = parseFloat(document.getElementById('allowance').value)||0;
  const pieces=[]; let line=0;
  [...tbody.querySelectorAll('tr')].forEach(tr=>{
    line++;
    const inputs=tr.querySelectorAll('input');
    const w=parseFloat(inputs[0].value)||0, h=parseFloat(inputs[1].value)||0, q=parseInt(inputs[2].value)||0, c=inputs[3].value.trim();
    if(w>0&&h>0&&q>0){
      for(let i=0;i<q;i++) pieces.push({w:w+allowance*2,h:h+allowance*2,realW:w,realH:h,comment:c,line});
    }
  });
  const sortMode=document.getElementById('sortMode').value;
  if(sortMode==='height') pieces.sort((a,b)=>b.h-a.h||b.w-a.w);
  if(sortMode==='width') pieces.sort((a,b)=>b.w-a.w||b.h-a.h);
  return pieces;
}
function packRows(pieces, rollWidth){
  const rows=[];
  pieces.forEach(p=>{
    let placed=false;
    for(const row of rows){
      if(row.used+p.w<=rollWidth){row.items.push(p);row.used+=p.w;row.height=Math.max(row.height,p.h);placed=true;break;}
    }
    if(!placed) rows.push({items:[p],used:p.w,height:p.h});
  });
  return rows;
}
function calculate(){
  const rollWidth=parseFloat(document.getElementById('rollWidth').value)||300;
  const pieces=getPieces();
  const rows=packRows(pieces,rollWidth);
  const totalLength=rows.reduce((s,r)=>s+r.height,0);
  const area=pieces.reduce((s,p)=>s+(p.realW*p.realH/10000),0);
  const usedArea=pieces.reduce((s,p)=>s+(p.w*p.h),0);
  const totalArea=rollWidth*totalLength;
  const usePct=totalArea?Math.round(usedArea/totalArea*100):0;
  document.getElementById('sumQty').textContent=pieces.length;
  document.getElementById('sumArea').textContent=area.toFixed(2)+' м²';
  document.getElementById('sumLength').textContent=(totalLength/100).toFixed(2)+' м';
  document.getElementById('sumUse').textContent=usePct+'%';
  renderLayout(rows,rollWidth,totalLength);
}
function renderLayout(rows, rollWidth, totalLength){
  const layout=document.getElementById('layout'); layout.innerHTML='';
  const note=document.getElementById('layoutNote');
  const fabric=document.getElementById('fabricName').value||'Ткань';
  if(!rows.length){note.textContent='После расчета здесь появится схема раскладки.';return;}
  note.textContent=`${fabric}. Ширина рулона: ${rollWidth} см. Использовано по длине: ${(totalLength/100).toFixed(2)} м.`;
  rows.forEach((row,idx)=>{
    const rowDiv=document.createElement('div'); rowDiv.className='row';
    const label=document.createElement('div'); label.className='row-label'; label.innerHTML=`Ряд ${idx+1}<br>${row.height.toFixed(1)} см`;
    const piecesDiv=document.createElement('div'); piecesDiv.className='row-pieces';
    row.items.forEach(p=>{
      const d=document.createElement('div'); d.className='piece'; d.style.flexBasis=(p.w/rollWidth*100)+'%';
      d.innerHTML=`${p.realW}×${p.realH}<br><small>${p.comment||'стр. '+p.line}</small>`;
      piecesDiv.appendChild(d);
    });
    const wasteWidth=rollWidth-row.used;
    if(wasteWidth>0){const w=document.createElement('div'); w.className='piece waste'; w.style.flexBasis=(wasteWidth/rollWidth*100)+'%'; w.innerHTML=`остаток<br>${wasteWidth.toFixed(1)} см`; piecesDiv.appendChild(w);}
    rowDiv.appendChild(label); rowDiv.appendChild(piecesDiv); layout.appendChild(rowDiv);
  });
}
addRow(80,150,2,'заказ 1');
addRow(60,120,1,'заказ 2');
addRow(100,160,2,'заказ 3');
</script>
</body>
</html>
