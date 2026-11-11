<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>家計簿</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Zen+Kaku+Gothic+New:wght@400;500;700&display=swap" rel="stylesheet">
<style>
:root{
  box-sizing:border-box;
  padding-top:env(safe-area-inset-top,0px);
  padding-bottom:env(safe-area-inset-bottom,0px);
  --bg:#F5F6F7; --surface:#FFFFFF; --ink:#1C1F24; --sub:#7A808A; --line:#E3E5E8;
  --key:#EEF0F2; --key-press:#E1E4E8; --accent:#2F3A8F; --accent-ink:#FFFFFF; --warn:#C2410C;
  --c1:#2F3A8F; --c2:#6C8EBF; --c3:#9AB89B; --c4:#D9A86C; --c5:#C27C7C; --c6:#8E7CB3; --c7:#5E8C8C; --c8:#B5B0A3;
  color-scheme:light;
}
@media (prefers-color-scheme:dark){
  :root:not([data-theme="light"]){
    --bg:#121417; --surface:#1B1E23; --ink:#ECEEF1; --sub:#8A909A; --line:#2A2E35;
    --key:#252930; --key-press:#2F343C; --accent:#8F9CFF; --accent-ink:#10131A; --warn:#FB923C;
    --c1:#8F9CFF; --c2:#7FA6D6; --c3:#8DBA90; --c4:#E0B27A; --c5:#D49595; --c6:#A998CF; --c7:#78AFAF; --c8:#A7A296;
    color-scheme:dark;
  }
}
:root[data-theme="dark"]{
  --bg:#121417; --surface:#1B1E23; --ink:#ECEEF1; --sub:#8A909A; --line:#2A2E35;
  --key:#252930; --key-press:#2F343C; --accent:#8F9CFF; --accent-ink:#10131A; --warn:#FB923C;
  --c1:#8F9CFF; --c2:#7FA6D6; --c3:#8DBA90; --c4:#E0B27A; --c5:#D49595; --c6:#A998CF; --c7:#78AFAF; --c8:#A7A296;
  color-scheme:dark;
}
*,*::before,*::after{box-sizing:inherit}
html{height:100%;scroll-padding-top:env(safe-area-inset-top,0px)}
body{height:100%;margin:0;background:var(--bg);color:var(--ink);font-family:"Zen Kaku Gothic New","Hiragino Sans","Yu Gothic",system-ui,sans-serif;-webkit-font-smoothing:antialiased;font-feature-settings:"palt"}
button{font:inherit;color:inherit;background:none;border:0;cursor:pointer;-webkit-tap-highlight-color:transparent}
input{font:inherit;color:inherit}
button:focus-visible,input:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
.app{max-width:460px;margin:0 auto;height:100%;display:flex;flex-direction:column;position:relative;overflow:hidden}
.view{flex:1;min-height:0;display:none;flex-direction:column}
.view.on{display:flex}
.num{font-variant-numeric:tabular-nums;letter-spacing:.01em}

.tabs{display:flex;border-top:1px solid var(--line);background:var(--bg)}
.tab{flex:1;padding:14px 0 12px;font-size:13px;color:var(--sub);position:relative;font-weight:500}
.tab[aria-selected="true"]{color:var(--ink);font-weight:700}
.tab[aria-selected="true"]::before{content:"";position:absolute;top:-1px;left:35%;right:35%;height:2px;background:var(--ink)}

/* input */
#v-input{overflow-y:auto}
.cats{padding:16px 20px 4px}
.cats-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px}
.cats-head span{font-size:12px;color:var(--sub)}
.link{font-size:12px;color:var(--sub);padding:4px 0}
.link.on{color:var(--accent);font-weight:700}
.chips{display:flex;flex-wrap:wrap;gap:8px}
.chip{padding:8px 14px 8px 11px;border-radius:999px;border:1px solid var(--line);background:var(--surface);font-size:14px;transition:background .15s,color .15s,border-color .15s;display:inline-flex;align-items:center;gap:6px}
.chip[aria-pressed="true"]{background:var(--ink);color:var(--bg);border-color:var(--ink)}
.chip.add{border-style:dashed;color:var(--sub);background:transparent;padding-left:14px}
.chips.editing .chip:not(.add){border-style:dashed;border-color:var(--sub)}
.empty{font-size:13px;color:var(--sub);line-height:1.8;padding:6px 2px 2px}
.display{padding:8px 24px 4px;text-align:right;margin-top:auto}
.display .for{font-size:12px;color:var(--sub);min-height:18px;display:flex;justify-content:space-between;align-items:center}
.display .amt{font-size:48px;font-weight:500;line-height:1.1;word-break:break-all}
.display .amt small{font-size:20px;color:var(--sub);margin-right:6px;font-weight:400}
.display .expr{font-size:12.5px;color:var(--sub);min-height:16px}
.meta{display:flex;align-items:center;gap:10px;padding:2px 20px 6px}
.meta .memo{flex:1;min-width:0;border:0;border-bottom:1px solid var(--line);background:transparent;padding:7px 2px;font-size:14px}
.meta .memo:focus{outline:none;border-color:var(--ink)}
.meta2{display:flex;align-items:center;justify-content:space-between;padding:0 20px 2px;gap:10px}
.meta2 input[type=date]{border:0;background:transparent;color:var(--sub);font-size:12.5px}
.seg{display:flex;background:var(--key);border-radius:999px;padding:3px}
.seg button{padding:6px 14px;border-radius:999px;font-size:12.5px;color:var(--sub);font-weight:500}
.seg button[aria-pressed="true"]{background:var(--surface);color:var(--ink);font-weight:700;box-shadow:0 1px 2px rgba(0,0,0,.08)}
.pad{display:grid;grid-template-columns:repeat(4,1fr);gap:9px;padding:6px 20px 14px}
.key{height:clamp(42px,6.2vh,54px);border-radius:15px;background:var(--key);font-size:21px;font-weight:500;transition:background .1s,transform .1s}
.key:active{background:var(--key-press);transform:scale(.97)}
.key.op{color:var(--accent)}
.key.rec{grid-column:span 2;background:var(--accent);color:var(--accent-ink);font-size:15.5px;font-weight:700}
.key.rec.plan{background:transparent;color:var(--accent);border:1.5px dashed var(--accent)}
.key.rec:disabled{opacity:.35}
.toast{position:absolute;left:50%;top:14px;transform:translate(-50%,-16px);background:var(--ink);color:var(--bg);font-size:13px;padding:9px 16px;border-radius:999px;opacity:0;pointer-events:none;transition:all .25s;z-index:20;white-space:nowrap}
.toast.on{opacity:1;transform:translate(-50%,0)}

/* shared top */
.top{display:flex;align-items:center;justify-content:space-between;padding:16px 20px 6px}
.month{display:flex;align-items:center;gap:4px}
.month button{width:32px;height:32px;border-radius:50%;color:var(--sub);font-size:18px}
.month b{font-size:17px;font-weight:700;min-width:96px;text-align:center}
.scroll{flex:1;overflow-y:auto;padding:0 20px 28px}
.total{padding:14px 0 4px;display:flex;align-items:flex-end;gap:18px;flex-wrap:wrap}
.total .l{font-size:12px;color:var(--sub)}
.total .v{font-size:32px;font-weight:500}
.total .v small{font-size:16px;color:var(--sub);margin-right:4px}
.total .pl .v{font-size:20px;color:var(--sub)}
.total .pl .box{display:inline-block;border:1.5px dashed var(--sub);border-radius:10px;padding:2px 10px}
.chart{margin:8px 0 6px}
.bars-wrap{overflow-x:auto;margin:0 -20px;padding:0 20px}
svg text{font-family:inherit}
.legend{list-style:none;margin:6px 0 0;padding:0}
.legend li{display:flex;align-items:center;gap:10px;padding:11px 0;border-bottom:1px solid var(--line);font-size:14px}
.legend .dot{width:10px;height:10px;border-radius:50%;flex:none}
.legend .n{flex:1}
.legend .p{color:var(--sub);font-size:12px;width:40px;text-align:right}
.legend .a{width:86px;text-align:right}
.key-note{display:flex;gap:16px;font-size:11.5px;color:var(--sub);margin:4px 0 0;flex-wrap:wrap}
.key-note i{display:inline-block;vertical-align:middle;margin-right:6px}
.goals{margin-top:10px}
.goal{display:block;width:100%;text-align:left;padding:12px 0;border-bottom:1px solid var(--line)}
.goal .g1{display:flex;justify-content:space-between;font-size:14px;gap:8px}
.goal .g2{font-size:11.5px;color:var(--sub);margin-top:3px}
.goal .bar{height:4px;border-radius:2px;background:var(--key);margin-top:8px;overflow:hidden}
.goal .bar i{display:block;height:100%;border-radius:2px}

.coach{margin-top:22px;padding:18px;border-radius:18px;background:var(--surface);border:1px solid var(--line)}
.coach .who{font-size:12px;color:var(--sub);display:flex;align-items:center;gap:8px;margin-bottom:10px}
.coach .who::before{content:"";width:6px;height:6px;border-radius:50%;background:var(--accent)}
.coach p{margin:0 0 8px;font-size:14.5px;line-height:1.85}
.coach p:last-of-type{margin-bottom:0}
.wish{margin-top:14px;padding-top:14px;border-top:1px solid var(--line);display:flex;gap:10px;align-items:center}
.wish label{font-size:12px;color:var(--sub);white-space:nowrap}
.wish input{flex:1;min-width:0;border:0;border-bottom:1px solid var(--line);background:transparent;padding:6px 2px;font-size:14px}

.hist{margin-top:26px}
.hist h3{font-size:13px;font-weight:700;margin:0 0 4px}
.row{display:flex;align-items:center;gap:10px;padding:11px 0;border-bottom:1px solid var(--line);font-size:14px}
.row .d{color:var(--sub);font-size:12px;width:34px;flex:none}
.row .c{flex:1;min-width:0}
.row .c small{display:block;color:var(--sub);font-size:11.5px;margin-top:2px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.row .x{color:var(--sub);width:28px;height:28px;border-radius:50%;font-size:16px;flex:none}
.row.plan{border-bottom:1.5px dashed var(--line)}
.row.plan .c,.row.plan .num{color:var(--sub)}
.tag{display:inline-block;font-size:10.5px;border:1px dashed var(--sub);color:var(--sub);border-radius:6px;padding:0 5px;margin-left:6px;vertical-align:1px}
.ok{font-size:11.5px;color:var(--accent);font-weight:700;border:1px solid var(--accent);border-radius:999px;padding:3px 10px;flex:none}

/* calendar */
.csum{display:flex;gap:18px;padding:6px 20px 8px;align-items:flex-end}
.csum .l{font-size:11.5px;color:var(--sub)}
.csum .v{font-size:20px;font-weight:500}
.csum .p .box{display:inline-block;border:1.5px dashed var(--sub);border-radius:9px;padding:0 9px;color:var(--sub)}
.wk,.grid{display:grid;grid-template-columns:repeat(7,1fr);padding:0 12px}
.wk span{text-align:center;font-size:11px;color:var(--sub);padding:6px 0}
.cell{height:60px;border-radius:12px;display:flex;flex-direction:column;align-items:center;padding:5px 0 0;gap:2px;position:relative}
.cell .dn{font-size:12.5px;width:22px;height:22px;line-height:22px;text-align:center;border-radius:50%}
.cell.today .dn{background:var(--ink);color:var(--bg);font-weight:700}
.cell.sel{background:var(--key)}
.cell .ca{font-size:10.5px;font-weight:700}
.cell .cp{font-size:10px;color:var(--sub);border:1px dashed var(--sub);border-radius:6px;padding:0 4px;line-height:15px}
.cell.blank{pointer-events:none}
.dayhead{display:flex;justify-content:space-between;align-items:center;margin:18px 0 2px}
.dayhead b{font-size:15px}
.addday{font-size:12.5px;color:var(--accent);font-weight:700;border:1px solid var(--accent);border-radius:999px;padding:6px 12px}
.addday.plan{border-style:dashed}

/* sheet */
.scrim{position:absolute;inset:0;background:rgba(10,12,16,.45);opacity:0;pointer-events:none;transition:opacity .2s;z-index:30}
.scrim.on{opacity:1;pointer-events:auto}
.sheet{position:absolute;left:0;right:0;bottom:0;max-height:92%;overflow-y:auto;background:var(--surface);border-radius:24px 24px 0 0;padding:24px 22px calc(22px + env(safe-area-inset-bottom,0px));transform:translateY(105%);transition:transform .28s cubic-bezier(.2,.8,.2,1);z-index:40}
.sheet.on{transform:none}
.sheet h2{margin:0 0 18px;font-size:16px}
.namerow{display:flex;align-items:flex-end;gap:12px}
.emo{width:54px;height:54px;border-radius:16px;background:var(--key);font-size:28px;flex:none;position:relative}
.emo::after{content:"▾";position:absolute;right:-4px;bottom:-4px;width:18px;height:18px;border-radius:50%;background:var(--surface);border:1px solid var(--line);font-size:11px;line-height:16px;color:var(--sub)}
.field{margin-bottom:16px;flex:1}
.field label{display:block;font-size:12px;color:var(--sub);margin-bottom:6px}
.field input{width:100%;border:0;border-bottom:1.5px solid var(--line);background:transparent;padding:8px 2px;font-size:17px}
.field input:focus{outline:none;border-color:var(--ink)}
.field .hint{font-size:11.5px;color:var(--sub);margin-top:6px;line-height:1.6}
.two{display:flex;gap:14px;align-items:flex-end}
.times{padding-bottom:10px;color:var(--sub)}
.preview{background:var(--key);border-radius:12px;padding:12px 14px;font-size:13.5px;margin-bottom:16px;line-height:1.7}
.adjs{display:flex;flex-wrap:wrap;gap:8px;margin:2px 0 10px}
.adj{display:inline-flex;align-items:center;gap:4px;border:1px solid var(--line);border-radius:999px;padding:5px 6px 5px 12px;font-size:13px}
.adj button{width:20px;height:20px;border-radius:50%;color:var(--sub);font-size:14px;line-height:1}
.adjadd{display:flex;gap:8px;align-items:flex-end}
.adjadd input{flex:1}
.adjadd .btn{flex:none;height:38px;padding:0 16px;font-size:13px}
.sheet-actions{display:flex;gap:10px;margin-top:20px}
.btn{flex:1;height:48px;border-radius:14px;background:var(--key);font-size:15px;font-weight:500}
.btn.pri{background:var(--accent);color:var(--accent-ink);font-weight:700}
.btn.del{flex:none;padding:0 18px;color:var(--warn)}
.adjrow{display:flex;align-items:center;gap:8px;padding:0 20px 8px}
.adjrow>span{font-size:12px;color:var(--sub);margin-right:2px}
.adjchip{padding:5px 11px;border-radius:999px;border:1px solid var(--line);background:var(--surface);font-size:12.5px;font-variant-numeric:tabular-nums}
.adjchip:active{background:var(--key)}
.adjtag{font-size:12px;color:var(--accent);border:1px solid var(--accent);border-radius:999px;padding:1px 9px;margin-left:6px}
.row{-webkit-user-select:none;user-select:none;-webkit-touch-callout:none;cursor:pointer}
.row.lifting{opacity:.3}
.ghost{position:fixed;z-index:200;pointer-events:none;width:210px;overflow:hidden;background:var(--surface);border-radius:14px;padding:6px 14px;box-shadow:0 10px 30px rgba(0,0,0,.25);border:0!important}
.ghost button,.ghost .d{display:none}
.cell.drop{outline:2px dashed var(--accent);outline-offset:-2px;background:var(--key)}
.banner{margin:12px 20px 0;padding:12px 14px;border-radius:14px;background:var(--surface);border:1px solid var(--line);display:flex;align-items:center;gap:12px;font-size:13px;line-height:1.6}
.banner span{flex:1}
.banner button{flex:none;font-size:12.5px;font-weight:700;color:var(--accent-ink);background:var(--accent);border-radius:999px;padding:7px 13px}
.btn.a{display:flex;align-items:center;justify-content:center;text-decoration:none;color:var(--ink)}
.mtext{width:100%;border:1px solid var(--line);border-radius:12px;background:var(--bg);padding:12px;font-size:14px;line-height:1.8;resize:vertical;margin-top:16px;color:var(--ink)}
.mtext{font-family:inherit}
.opts{display:flex;gap:8px;flex-wrap:wrap;margin:2px 0 4px}
.opt{padding:6px 12px;border-radius:999px;border:1px dashed var(--line);font-size:12.5px;color:var(--sub)}
.opt[aria-pressed="true"]{border:1px solid var(--ink);color:var(--ink);background:var(--surface)}
.opt[aria-pressed="true"]::before{content:"✓ "}
.adjchip.pm{width:38px;height:38px;padding:0;font-size:19px;border-radius:50%;line-height:1}
.adjnum{width:120px;border:0;border-bottom:1.5px solid var(--line);background:transparent;padding:7px 2px;font-size:16px;text-align:center}
.adjnum:focus{outline:none;border-color:var(--ink)}
.wish-card{display:block;width:calc(100% - 40px);margin:14px 20px 0;padding:16px 18px 15px;border-radius:20px;text-align:left;position:relative;overflow:hidden;color:var(--accent-ink);background:var(--accent);background-image:linear-gradient(135deg,var(--accent),color-mix(in srgb,var(--accent) 62%,#B77CFF))}
.wish-card .wc-l{font-size:12px;opacity:.85;margin-bottom:4px}
.wish-card .wc-t{font-size:21px;font-weight:700;line-height:1.4;padding-right:28px;word-break:break-all}
.wish-card .wc-s{font-size:12.5px;margin-top:8px;opacity:.95}
.wish-card .wc-s.dim{opacity:.75;margin-top:6px}
.wish-card .wc-bar{height:4px;border-radius:2px;background:color-mix(in srgb,currentColor 25%,transparent);margin-top:6px;overflow:hidden}
.wish-card .wc-bar i{display:block;height:100%;background:currentColor;border-radius:2px}
.wish-card .wc-in{display:flex;gap:10px;align-items:flex-end;margin-top:6px}
.wish-card input{flex:1;min-width:0;border:0;border-bottom:1.5px solid color-mix(in srgb,currentColor 55%,transparent);background:transparent;color:inherit;font-size:18px;font-weight:700;padding:6px 2px}
.wish-card input::placeholder{color:inherit;opacity:.55;font-weight:400}
.wish-card input:focus{outline:none;border-color:currentColor}
.wish-card .wc-in button{flex:none;background:var(--accent-ink);color:var(--accent);font-size:13px;font-weight:700;border-radius:999px;padding:8px 16px}
.spk{position:absolute;font-size:14px;opacity:.5;animation:tw 2.8s ease-in-out infinite;pointer-events:none}
.spk.s1{right:18px;top:12px}
.spk.s2{right:46px;top:34px;font-size:11px;animation-delay:1.2s}
.spk.s3{right:22px;bottom:12px;font-size:12px;animation-delay:.6s}
@keyframes tw{0%,100%{opacity:.2;transform:scale(.8)}50%{opacity:.95;transform:scale(1.2)}}
@media (prefers-reduced-motion:reduce){.spk{animation:none;opacity:.6}}
.chip .pr{font-size:11px;opacity:.6;font-variant-numeric:tabular-nums;margin-left:-2px}
.gm{display:flex;align-items:center;justify-content:space-between;margin:2px 0 4px}
.gm>label{font-size:12px;color:var(--sub)}
.gmnav{display:flex;align-items:center;gap:2px}
.gmnav button{width:30px;height:30px;border-radius:50%;color:var(--sub);font-size:18px;background:var(--key)}
.gmnav b{font-size:15px;min-width:96px;text-align:center}
.gmhint{font-size:11.5px;color:var(--sub);margin:0 0 12px;line-height:1.6}
.autorow{display:flex;align-items:center;gap:12px;width:100%;text-align:left;padding:12px 0;margin-bottom:12px;border-top:1px solid var(--line)}
.autorow .t{flex:1;font-size:14px}
.autorow .t small{display:block;font-size:11.5px;color:var(--sub);margin-top:3px;line-height:1.55}
.sw{flex:none;width:44px;height:26px;border-radius:999px;background:var(--key-press);position:relative;transition:background .2s}
.sw::after{content:"";position:absolute;left:3px;top:3px;width:20px;height:20px;border-radius:50%;background:var(--surface);box-shadow:0 1px 2px rgba(0,0,0,.25);transition:transform .2s}
.autorow[aria-pressed="true"] .sw{background:var(--accent)}
.autorow[aria-pressed="true"] .sw::after{transform:translateX(18px)}
.stylebar{display:flex;align-items:center;gap:10px;margin:10px 0 0}
.restyle{padding:8px 14px;border-radius:999px;border:1px solid var(--accent);color:var(--accent);font-size:13px;font-weight:700}
.restyle:active{background:var(--key)}
.stylebar span{font-size:12px;color:var(--sub)}
.wish-card{min-height:112px}
.wish-card .ill{position:absolute;right:12px;top:12px;width:96px;height:auto;opacity:.4;pointer-events:none}
.wish-card.ed .ill{width:60px;top:10px}
.wish-card .wc-t{padding-right:96px}
.wish-card .wc-l{padding-right:70px}
.rcbtn{flex:none;width:38px;height:38px;border-radius:12px;color:var(--sub);background:var(--key);display:grid;place-items:center;position:relative}
.rcbtn svg{width:20px;height:20px}
.rcbtn.on{color:var(--accent);background:var(--surface);box-shadow:inset 0 0 0 1.5px var(--accent)}
.rcbtn.on::after{content:"";position:absolute;right:-3px;top:-3px;width:9px;height:9px;border-radius:50%;background:var(--accent)}
.remind{margin:12px 20px 0;padding:12px 10px 12px 14px;border-radius:14px;background:var(--surface);box-shadow:inset 0 0 0 1.5px var(--accent);display:flex;align-items:center;gap:12px}
.remind .ri{flex:none;width:22px;color:var(--accent);display:grid}
.remind .ri svg{width:22px;height:22px}
.remind .rt{flex:1;min-width:0;font-size:13px;line-height:1.6}
.remind .rt small{display:block;color:var(--sub);font-size:11.5px;margin-top:2px}
.rdone{flex:none;font-size:12px;font-weight:700;color:var(--accent);border:1px solid var(--accent);border-radius:999px;padding:4px 11px}
.rx{flex:none;width:26px;height:26px;color:var(--sub);font-size:16px}
.mwrap{position:relative;margin-top:12px}
.mwrap .mtext{margin-top:0;padding-bottom:38px;display:block}
.mcorner{position:absolute;right:8px;bottom:8px;display:flex;align-items:center;gap:6px}
.mcorner span{font-size:10.5px;color:var(--sub);opacity:.8}
.restyle2{width:28px;height:28px;border-radius:50%;color:var(--sub);opacity:.6;display:grid;place-items:center}
.restyle2:hover,.restyle2:active{opacity:1;background:var(--key)}
.restyle2 svg{width:16px;height:16px}
.evrow{display:flex;align-items:center;gap:10px;padding:8px 0;border-bottom:1px solid var(--line);font-size:13.5px}
.evrow .n{flex:1;min-width:0}
.evrow .d{color:var(--sub);font-size:12px}
.evrow button{width:26px;height:26px;color:var(--sub)}
.evadd{display:grid;grid-template-columns:1fr 110px;gap:8px 10px;align-items:end;margin:8px 0 16px}
.evadd input{width:100%;border:0;border-bottom:1.5px solid var(--line);background:transparent;padding:7px 2px;font-size:14.5px}
.evadd input:focus{outline:none;border-color:var(--ink)}
.evadd .btn{height:38px;font-size:13px}
.linkbtn{display:inline-block;font-size:12.5px;color:var(--accent);font-weight:700;padding:6px 0;margin:0 14px 8px 0;text-decoration:none}
.erow{margin-top:14px}
.rimg{border:1.5px dashed var(--line);border-radius:14px;min-height:120px;display:grid;place-items:center;overflow:hidden;margin-bottom:10px;color:var(--sub);font-size:13px}
.rimg img{width:100%;max-height:260px;object-fit:contain;display:block}
.rbtns{display:flex;gap:10px;margin-bottom:16px}
.rc{display:inline-block;width:14px;height:14px;vertical-align:-2px;margin-left:6px;color:var(--accent)}
.rc svg{width:14px;height:14px}
.rc.done{color:var(--sub);opacity:.6}
.rcm{position:absolute;right:5px;bottom:4px;width:11px;height:11px;color:var(--accent)}
.rcm svg{width:11px;height:11px}
.row.due{cursor:default}
.evbox{border:1.5px solid var(--accent);border-radius:16px;padding:14px;margin-bottom:16px;background:color-mix(in srgb,var(--accent) 5%,var(--surface))}
.evhead{display:flex;flex-direction:column;gap:2px;margin-bottom:8px}
.evhead b{font-size:14px}
.evhead span{font-size:11.5px;color:var(--sub)}
.evform{margin-top:12px;padding:12px;border-radius:12px;background:var(--surface);border:1px dashed var(--accent)}
.evtitle{font-size:13px;font-weight:700;color:var(--accent);margin-bottom:10px}
.evf{margin-bottom:10px}
.evf label{display:block;font-size:11.5px;color:var(--sub);margin-bottom:4px}
.evf input{width:100%;border:1px solid var(--line);border-radius:10px;background:var(--bg);padding:10px 12px;font-size:15px}
.evf input:focus{outline:none;border-color:var(--accent)}
.evpicks{max-height:190px;overflow-y:auto;border:1px solid var(--line);border-radius:10px;margin:10px 0 6px;background:var(--bg)}
.evp{display:flex;align-items:center;gap:10px;width:100%;text-align:left;padding:9px 12px;border-bottom:1px solid var(--line);font-size:13.5px}
.evp:last-child{border-bottom:0}
.evp .ck{flex:none;width:18px;height:18px;border-radius:50%;border:1.5px solid var(--sub)}
.evp[aria-pressed="true"] .ck{background:var(--accent);border-color:var(--accent);box-shadow:inset 0 0 0 3px var(--bg)}
.evp .n{flex:1;min-width:0}
.evp .d{color:var(--sub);font-size:11.5px}
.evsum{font-size:13px;min-height:20px;margin-bottom:10px;line-height:1.6}
.evrow .n small{display:block;color:var(--sub);font-size:11px;margin-top:1px}
.addwish{flex:none;width:38px;height:38px;border-radius:50%;background:var(--accent);color:var(--accent-ink);font-size:22px;line-height:1}
.mason{column-count:2;column-gap:10px;padding-top:10px}
.wcard{display:block;width:100%;text-align:left;break-inside:avoid;margin-bottom:14px}
.wimg{border-radius:16px;overflow:hidden;background:var(--key);position:relative}
.wimg img{width:100%;height:100%;object-fit:cover;display:block}
.wimg.noimg{display:grid;place-items:center;color:var(--sub);opacity:.7}
.wimg.noimg svg{width:34px;height:34px}
.wcap{padding:6px 2px 0}
.wcap b{display:block;font-size:13px;font-weight:500;line-height:1.4;word-break:break-all}
.wcap span.num{font-size:12px;color:var(--sub)}
.wcap .m{font-size:11.5px;color:var(--sub);margin-top:2px}
.phbtn svg{opacity:1}
.phbtn.on{background-size:cover;background-position:center;box-shadow:none}
.phbtn.on svg{opacity:0}
.wish-card .wc-l,.wish-card .wc-t,.wish-card .wc-s,.wish-card .wc-bar,.wish-card .wc-in{position:relative}
.wish-card .fx{position:absolute;inset:0;pointer-events:none;overflow:hidden;display:block}
.wish-card .cloud{position:absolute;left:-60px;width:46px;opacity:.2;animation:drift 40s linear infinite}
.wish-card .cloud.c1{top:14px;animation-delay:-14s}
.wish-card .cloud.c2{top:62%;width:32px;opacity:.14;animation-duration:58s;animation-delay:-36s}
.wish-card .tw{position:absolute;width:12px;height:12px;animation:tw 2.6s ease-in-out infinite}
.wish-card .tw.t1{right:114px;top:16px}
.wish-card .tw.t2{right:14px;top:74px;width:9px;height:9px;animation-delay:.9s}
.wish-card .tw.t3{right:58px;top:80px;width:7px;height:7px;animation-delay:1.7s}
@keyframes drift{from{left:-60px}to{left:100%}}
@media (prefers-reduced-motion:reduce){.wish-card .fx *{animation:none!important}.wish-card .tw{opacity:.5}}
#v-input{overflow:hidden}
.in-top{flex:1;min-height:0;overflow-y:auto;overscroll-behavior:contain;padding-bottom:6px}
.in-bottom{flex:none;display:flex;flex-direction:column;background:var(--bg)}
.undo{position:absolute;z-index:60;left:0;top:0;background:var(--ink);color:var(--bg);font-size:13px;padding:6px 6px 6px 14px;border-radius:14px;display:flex;align-items:center;gap:10px;opacity:0;pointer-events:none;transition:opacity .2s,transform .2s;transform:translateY(6px);white-space:nowrap;box-shadow:0 6px 20px rgba(0,0,0,.25)}
.undo.on{opacity:1;pointer-events:auto;transform:none}
.undo button{font-weight:700;color:var(--bg);background:color-mix(in srgb,var(--bg) 22%,transparent);border-radius:999px;padding:6px 14px;font-size:13px}
.undo::after{content:"";position:absolute;left:var(--tail,40px);bottom:-5px;width:12px;height:12px;background:var(--ink);transform:rotate(45deg);border-radius:2px}
.undo.below::after{bottom:auto;top:-5px}
.evrow{cursor:pointer}
.evrow.editing{background:color-mix(in srgb,var(--accent) 10%,transparent);box-shadow:0 0 0 6px color-mix(in srgb,var(--accent) 10%,transparent);border-radius:6px}
.evhint{font-size:11px;color:var(--sub);margin:8px 0 0}
.wimg.noimg{opacity:1}
.wimg.noimg svg.wil{width:64%;max-width:130px;height:auto}
.pktabs{display:flex;gap:4px;overflow-x:auto;margin-bottom:6px;padding-bottom:2px}
.pktab{flex:none;width:40px;height:40px;border-radius:10px;font-size:20px;line-height:1}
.pktab[aria-pressed="true"]{background:var(--key)}
.pklabel{grid-column:1/-1;font-size:11.5px;color:var(--sub);padding:6px 2px 2px}
.pkhint{grid-column:1/-1;font-size:12px;color:var(--sub);padding:6px 2px 10px;line-height:1.6}
.pkgrid{display:grid;grid-template-columns:repeat(7,1fr);gap:2px;max-height:300px;overflow-y:auto;padding:2px 0 6px}
.pk{font-size:26px;height:44px;border-radius:10px;line-height:1}
.pk:active{background:var(--key)}
.pk.sel{background:var(--key);box-shadow:inset 0 0 0 1.5px var(--accent)}
.pk.used{opacity:.4}
.wdate{position:absolute;left:8px;top:8px;font-size:11px;font-weight:700;color:#fff;background:rgba(0,0,0,.5);border-radius:999px;padding:2px 9px}
.wimg.noimg .wdate{background:color-mix(in srgb,currentColor 80%,#000)}
body{background-color:var(--bg);background-image:var(--tex,none);background-size:var(--texsize,auto)}
.in-bottom,.tabs{background:transparent}
.tabinfo{flex:none;width:52px;color:var(--sub);display:grid;place-items:center;padding:12px 0 10px}
.tabinfo svg{width:22px;height:22px}
.skins{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
.skin{height:66px;border-radius:14px;position:relative;overflow:hidden;box-shadow:inset 0 0 0 1px rgba(0,0,0,.14);text-align:left}
.skin span{position:absolute;left:6px;bottom:6px;font-size:11.5px;font-weight:700;background:rgba(255,255,255,.86);color:#222;border-radius:6px;padding:1px 6px}
.skin[aria-pressed="true"]{box-shadow:0 0 0 2.5px var(--accent)}
.skin[aria-pressed="true"]::after{content:"✓";position:absolute;right:6px;top:4px;width:18px;height:18px;border-radius:50%;background:var(--accent);color:var(--accent-ink);font-size:11px;line-height:18px;text-align:center}
.isec{margin-bottom:24px}
.isec h3{font-size:13px;margin:0 0 10px}
.irow2{display:flex;gap:10px;align-items:center;padding:9px 0;border-bottom:1px solid var(--line);font-size:13.5px}
.irow2 span:first-child{flex:1;min-width:0}
.irow2 i{font-style:normal;color:var(--sub);font-size:11.5px}
.irowbtn{display:flex;gap:10px;margin-top:14px}
.btn.pri.a{color:var(--accent-ink)}
.ihint{font-size:11.5px;color:var(--sub);line-height:1.7;margin-top:8px;min-height:20px}
.istats{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.istat{background:var(--key);border-radius:12px;padding:10px 12px}
.istat small{display:block;font-size:11px;color:var(--sub);margin-bottom:2px}
.istat b{font-size:15px;font-weight:500}
.ihelp{margin:14px 0 0;padding-left:18px;font-size:12.5px;line-height:1.85}
@media (prefers-reduced-motion:reduce){*{transition:none!important}}
</style>
</head>
<body>
<div class="app">
  <div class="toast" id="toast" role="status"></div>
  <div class="undo" id="undo" role="status"><span id="undoMsg"></span><button id="undoBtn">戻す</button></div>

  <!-- 記録 -->
  <section class="view on" id="v-input" aria-label="記録">
    <div class="in-top">
    <div id="wishCard"></div>
    <div id="remind"></div>
    <div id="banner1"></div>
    <div class="cats">
      <div class="cats-head">
        <span>項目を選ぶ</span>
        <button class="link" id="editToggle">編集</button>
      </div>
      <div class="chips" id="chips"></div>
      <div id="emptyHost"></div>
    </div>
    </div>
    <div class="in-bottom">
    <div class="display">
      <div class="for"><span id="forLabel"></span><button class="link" id="clr">クリア</button></div>
      <div class="amt num" id="amt"><small>¥</small>0</div>
      <div class="expr num" id="expr"></div>
    </div>
    <div class="meta"><input class="memo" id="memo" maxlength="30" placeholder="メモ（任意）例：ファンデ、アイライナー"><button class="rcbtn" id="rcptBtn" aria-label="領収書・引き換えを追加"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M6 3h12v18l-3-2-3 2-3-2-3 2z"/><path d="M9 8h6M9 12h6M9 16h3"/></svg></button><button class="rcbtn phbtn" id="photoBtn" aria-label="写真を入れる"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><rect x="3" y="5" width="18" height="14" rx="2.5"/><circle cx="9" cy="10" r="1.8"/><path d="M4 18l5-5 4 4 3-3 4 4"/></svg></button><input type="file" id="phFile" accept="image/*" hidden></div>
    
    <div class="meta2">
      <div class="seg" role="group" aria-label="種類">
        <button id="k-act" aria-pressed="true">使った</button>
        <button id="k-plan" aria-pressed="false">使う予定</button>
      </div>
      <input type="date" id="date" aria-label="日付">
    </div>
    <div class="pad" id="pad"></div>
    </div>
  </section>

  <!-- 集計 -->
  <section class="view" id="v-stats" aria-label="集計">
    <div class="top">
      <div class="month">
        <button id="prev" aria-label="前の月">‹</button>
        <b id="monthLabel"></b>
        <button id="next" aria-label="次の月">›</button>
      </div>
      <div class="seg" role="group" aria-label="グラフの切り替え">
        <button id="m-pie" aria-pressed="true">円</button>
        <button id="m-bar" aria-pressed="false">棒</button>
      </div>
    </div>
    <div class="scroll">
      <div id="banner2" style="margin:0 -20px"></div>
      <div class="total" id="total"></div>
      <div class="chart" id="chart"></div>
      <div id="below"></div>
      <div class="coach" id="coach"></div>
      <button class="btn" id="mkMsg" style="width:100%;margin-top:12px">報告のメッセージをつくる</button>
      <div class="hist" id="hist"></div>
    </div>
  </section>

  <!-- カレンダー -->
  <section class="view" id="v-cal" aria-label="カレンダー">
    <div class="top">
      <div class="month">
        <button id="cprev" aria-label="前の月">‹</button>
        <b id="cMonthLabel"></b>
        <button id="cnext" aria-label="次の月">›</button>
      </div>
    </div>
    <div class="csum" id="csum"></div>
    <div class="scroll" style="padding:0 0 28px">
      <div class="wk"><span>日</span><span>月</span><span>火</span><span>水</span><span>木</span><span>金</span><span>土</span></div>
      <div class="grid" id="grid"></div>
      <div style="padding:0 20px">
        <div class="dayhead"><b id="dayTitle"></b><button class="addday" id="addDay"></button></div>
        <div id="dayList"></div>
        <div class="key-note" style="margin-top:14px"><span><i style="width:10px;height:2px;background:var(--ink)"></i>実績</span><span><i style="width:14px;border-top:1.5px dashed var(--sub)"></i>予定</span></div>
        <div style="font-size:11.5px;color:var(--sub);margin-top:10px;line-height:1.7">タップで編集。長押ししてカレンダーの別の日までドラッグすると、日付を移せます。</div>
      </div>
    </div>
  </section>

  <!-- ウィッシュ -->
  <section class="view" id="v-wish" aria-label="ウィッシュ">
    <div class="top"><div><div style="font-size:17px;font-weight:700">ほしいもの</div><div id="wTotal" class="num" style="font-size:12px;color:var(--sub);margin-top:2px"></div></div><button class="addwish" id="wAdd" aria-label="使う予定を追加">＋</button></div>
    <div class="scroll"><div id="wGrid" class="mason"></div></div>
  </section>

  <nav class="tabs" role="tablist">
    <button class="tab" role="tab" id="t-input" aria-selected="true">記録</button>
    <button class="tab" role="tab" id="t-stats" aria-selected="false">集計</button>
    <button class="tab" role="tab" id="t-cal" aria-selected="false">カレンダー</button>
    <button class="tab" role="tab" id="t-wish" aria-selected="false">ウィッシュ</button>
    <button class="tabinfo" id="infoBtn" aria-label="情報（テーマ・スプレッドシート）"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><circle cx="12" cy="12" r="9"/><path d="M12 11v5.5"/><circle cx="12" cy="7.8" r=".7" fill="currentColor"/></svg></button>
  </nav>

  <div class="scrim" id="scrim"></div>
  <div class="sheet" id="sheet" role="dialog" aria-modal="true" aria-labelledby="sheetTitle">
    <h2 id="sheetTitle">項目をつくる</h2>
    <div class="namerow">
      <button class="emo" id="emoBtn" aria-label="絵文字を変える">🌿</button>
      <div class="field">
        <label for="catName">項目の名前</label>
        <input id="catName" maxlength="12" placeholder="例：特急券、カフェ、お洋服">
      </div>
    </div>
    <div class="gm"><label>月の目標（任意）</label><div class="gmnav"><button id="gmPrev" aria-label="前の月">‹</button><b id="gmLabel"></b><button id="gmNext" aria-label="次の月">›</button></div></div>
    <div class="gmhint" id="gmHint"></div>
    <div class="two">
      <div class="field"><label for="gUnit">1回の金額（円）</label><input id="gUnit" inputmode="numeric" placeholder="1050"></div>
      <span class="times">×</span>
      <div class="field"><label for="gCount">回数</label><input id="gCount" inputmode="numeric" placeholder="11"></div>
    </div>
    <div class="field" style="margin-bottom:8px">
      <label id="adjLabel">この月の調整</label>
      <div class="adjs" id="adjs"></div>
      <div class="adjadd">
        <div class="field" style="margin:0"><input id="adjIn" inputmode="text" placeholder="+200 や -100" style="font-size:15px"></div>
        <button class="btn" id="adjAdd">追加</button>
      </div>
      <div class="hint">値段が変わった分を、この月だけ足し引きできます。</div>
    </div>
    <div class="preview num" id="preview"></div>
    <button class="autorow" id="autoBtn" aria-pressed="false" type="button"><span class="t">選ぶだけで金額を自動入力<small>この項目をタップすると、その月の「1回の金額」が電卓に入ります。特急券のように、いつも同じ金額の項目向けです。</small></span><span class="sw"></span></button>
    <div class="sheet-actions">
      <button class="btn del" id="catDel" hidden>削除</button>
      <button class="btn" id="catCancel">キャンセル</button>
      <button class="btn pri" id="catSave">保存</button>
    </div>
  </div>

  <div class="sheet" id="esheet" role="dialog" aria-modal="true" aria-label="記録を編集">
    <h2>記録を編集</h2>
    <div class="chips" id="eChips" style="margin-bottom:18px"></div>
    <div class="field"><label for="eAmt">金額（円）</label><input id="eAmt" inputmode="numeric"></div>
    <div class="field"><label for="eMemo">メモ</label><input id="eMemo" maxlength="30" style="font-size:15px"></div>
    <div class="field"><label for="eDate">日付</label><input id="eDate" type="date" style="font-size:15px"></div>
    <div class="seg" role="group" aria-label="種類" style="width:max-content"><button id="eAct" aria-pressed="true">使った</button><button id="ePlan" aria-pressed="false">使う予定</button></div>
    <div class="erow"><button class="linkbtn" id="eRcpt" type="button">領収書・引き換えを追加</button><button class="linkbtn" id="ePhoto" type="button">写真を追加</button><a class="linkbtn" id="eCal" target="_blank" rel="noopener" hidden>前日9時のお知らせをカレンダーに入れる</a></div>
    <div class="sheet-actions">
      <button class="btn del" id="eDel">削除</button>
      <button class="btn" id="eCancel">キャンセル</button>
      <button class="btn pri" id="eSave">保存</button>
    </div>
  </div>

  <div class="sheet" id="msheet" role="dialog" aria-modal="true" aria-label="親へのメッセージ">
    <h2 id="mTitle">メッセージ</h2>
    <div class="gm" style="margin-bottom:12px">
      <div class="seg" role="group" aria-label="種類"><button id="mModeRep" aria-pressed="true">報告</button><button id="mModeReq" aria-pressed="false">お願い</button></div>
      <div class="gmnav"><button id="mPrev" aria-label="前の月">‹</button><b id="mMonth"></b><button id="mNext" aria-label="次の月">›</button></div>
    </div>
    <div id="mReq" hidden>
      <div class="evbox">
        <div class="evhead"><b>特大イベント</b><span>この月のイベントを入れておく欄です</span></div>
        <div id="evList"></div>
        <div class="evform" id="evForm">
          <div class="evtitle" id="evTitle">＋ イベントを追加する</div>
          <div class="evf"><label for="evName">イベントの名前</label><input id="evName" maxlength="20" placeholder="例：クリスマスディズニー"></div>
          <div class="evf"><label for="evDate">いつ</label><input id="evDate" type="date"></div>
          <div class="seg" id="evMode" role="group" aria-label="金額の入れ方" style="width:max-content"><button data-m="pick" aria-pressed="true" type="button">カレンダーから選ぶ</button><button data-m="amt" aria-pressed="false" type="button">金額を入れる</button></div>
          <div id="evPickWrap">
            <div class="evpicks" id="evPicks"></div>
            <div class="evsum num" id="evSum"></div>
          </div>
          <div class="evf" id="evAmtWrap" hidden style="margin-top:10px"><label for="evAmt">金額（円）</label><input id="evAmt" inputmode="numeric" placeholder="8000"></div>
          <button class="btn pri" id="evAdd" style="width:100%" type="button">イベントに追加</button>
          <button class="linkbtn" id="evCancel" type="button" hidden style="margin:10px 0 0">編集をやめる</button>
        </div>
      </div>
    </div>
    <div class="field"><label id="mNoteL" for="mNote"></label><textarea class="mtext" id="mNote" rows="3" style="margin-top:0"></textarea></div>
    <div class="opts" id="mOpts"></div>
    <div class="mwrap">
      <textarea class="mtext" id="mText" rows="14" aria-label="送る文章"></textarea>
      <div class="mcorner"><span id="mStyleName"></span><button class="restyle2" id="mStyle" aria-label="文章のスタイルを変える" title="綺麗にする"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M4 11a8 8 0 0 1 14-4.5L20 9"/><path d="M20 4v5h-5"/><path d="M20 13a8 8 0 0 1-14 4.5L4 15"/><path d="M4 20v-5h5"/></svg></button></div>
    </div>
    <div style="font-size:11.5px;color:var(--sub);margin-top:8px;line-height:1.7">報告は記録から、お願いは各項目のその月の目標から自動でつくります。ここも直接直せます（上の入力を変えると作り直します）。</div>
    <div class="sheet-actions">
      <button class="btn" id="mClose">閉じる</button>
      <a class="btn a" id="mLine" target="_blank" rel="noopener">LINEで開く</a>
      <button class="btn pri" id="mCopy">コピー</button>
    </div>
  </div>

  <div class="sheet" id="rsheet" role="dialog" aria-modal="true" aria-label="領収書・引き換え">
    <h2>領収書・引き換え</h2>
    <div class="rimg"><img id="rImg" alt="領収書の写真" hidden><span id="rEmpty">写真はまだありません</span></div>
    <input type="file" id="rFile" accept="image/*" hidden>
    <div class="rbtns"><button class="btn" id="rPick">領収書の写真を選ぶ</button><button class="btn del" id="rImgDel" hidden>外す</button></div>
    <div class="field"><label for="rWho">誰のための引き換え</label><input id="rWho" maxlength="24" placeholder="例：バイト先の精算、サークルの備品" style="font-size:16px"></div>
    <div class="field"><label for="rDate">引き換えする日</label><input id="rDate" type="date" style="font-size:16px"><div class="hint">前日に、アプリを開くとお知らせが出ます。</div></div>
    <a class="linkbtn" id="rCal" target="_blank" rel="noopener" hidden>前日9時のお知らせをカレンダーに入れる</a>
    <button class="autorow" id="rDone" aria-pressed="false" type="button"><span class="t">引き換え済み<small>済みにすると、お知らせが出なくなります。</small></span><span class="sw"></span></button>
    <div class="sheet-actions">
      <button class="btn del" id="rClear" hidden>取り消す</button>
      <button class="btn" id="rCancel">閉じる</button>
      <button class="btn pri" id="rSave">保存</button>
    </div>
  </div>

  <div class="sheet" id="psheet" role="dialog" aria-modal="true" aria-label="写真">
    <h2>写真</h2>
    <div class="rimg"><img id="pImg" alt="写真" hidden><span id="pEmpty">写真はまだありません</span></div>
    <div class="rbtns"><button class="btn" id="pPick">写真を選ぶ</button><button class="btn del" id="pDel" hidden>外す</button></div>
    <div style="font-size:11.5px;color:var(--sub);line-height:1.7;margin-bottom:12px">「使う予定」の記録に写真を付けると、ウィッシュのページに並びます。</div>
    <div class="sheet-actions"><button class="btn pri" id="pClose">閉じる</button></div>
  </div>

  <div class="sheet" id="pksheet" role="dialog" aria-modal="true" aria-label="絵文字を選ぶ">
    <h2>絵文字を選ぶ</h2>
    <div class="pktabs" id="pkTabs"></div>
    <div class="pkgrid" id="pkGrid"></div>
    <div class="sheet-actions"><button class="btn" id="pkClose">閉じる</button></div>
  </div>

  <div class="sheet" id="isheet" role="dialog" aria-modal="true" aria-label="情報">
    <h2>情報</h2>
    <div class="isec">
      <h3>見た目（色とテクスチャー）</h3>
      <div class="skins" id="skins"></div>
    </div>
    <div class="isec">
      <h3>Googleスプレッドシート</h3>
      <div id="isum"></div>
      <div class="irowbtn">
        <a class="btn pri a" id="gsOpen" href="https://docs.google.com/spreadsheets/create" target="_blank" rel="noopener">コピーして開く</a>
        <button class="btn" id="csvSave" type="button">CSVを保存</button>
      </div>
      <div class="ihint" id="gsMsg"></div>
    </div>
    <div class="isec">
      <h3>アプリの中身のまとめ</h3>
      <div class="istats" id="istats"></div>
      <ul class="ihelp">
        <li>記録：電卓で入力。項目は自分でつくり、＋／－で金額を調整。メモ・領収書・写真も付けられます。</li>
        <li>集計：円グラフと棒グラフ（点線が月ごとの目標）。「使った」は5秒間、戻せます。</li>
        <li>カレンダー：日ごとの合計。記録を長押しして別の日へドラッグ。予定は点線。</li>
        <li>ウィッシュ：まだ来ていない「使う予定」が、写真かイラストで並びます。</li>
        <li>報告・お願い：親へのLINE用の文章を5つのスタイルで作成。特大イベントも入れられます。</li>
        <li>お知らせ：予定や引き換えの前日に、アプリを開くと表示されます。</li>
      </ul>
    </div>
    <div class="sheet-actions"><button class="btn" id="iClose">閉じる</button></div>
  </div>
</div>

<script>
(function(){
  var KEY = "kakeibo.v1";
  var memory = null;
  function load(){
    try{ var raw = localStorage.getItem(KEY); if(raw){ return JSON.parse(raw); } }catch(e){}
    return memory;
  }
  function save(){
    memory = state;
    try{ localStorage.setItem(KEY, JSON.stringify(state)); }catch(e){}
  }
  var state = load() || { cats:[], entries:[], wishes:{} };
  if(!state.cats) state.cats = [];
  if(!state.entries) state.entries = [];
  if(!state.wishes) state.wishes = {};
  if(!state.sent) state.sent = {};
  if(!state.notes) state.notes = {};
  if(!state.events) state.events = [];
  if(!state.wl) state.wl = [];
  if(!state.dismissed) state.dismissed = {};
  if(!state.settings) state.settings = { call:"", tone:"やわらかい" };

  function $(id){ return document.getElementById(id); }
  function pad2(n){ return (n<10?"0":"")+n; }
  function ym(d){ return d.getFullYear()+"-"+pad2(d.getMonth()+1); }
  function ymd(d){ return ym(d)+"-"+pad2(d.getDate()); }
  function yen(n){ return "¥"+Math.round(n).toLocaleString("ja-JP"); }
  function uid(){ return Date.now().toString(36)+Math.random().toString(36).slice(2,6); }
  function esc(s){ return String(s).replace(/[&<>"]/g,function(c){return {"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;"}[c];}); }
  function color(i){ return "var(--c"+((i%8)+1)+")"; }
  function catById(id){ for(var i=0;i<state.cats.length;i++){ if(state.cats[i].id===id) return state.cats[i]; } return null; }
  function today(){ return ymd(new Date()); }
  function fmtC(n){ return n>=10000 ? (n/10000).toFixed(1).replace(".0","")+"万" : n.toLocaleString("ja-JP"); }

  /* ---------- 絵文字 ---------- */
  var DICT = [
    [["住居費","住居","家賃","ローン","賃貸"],["🛖","🏠","🔑"]],
    [["水道光熱費","光熱費","光熱","電気代","ガス代","電気","ガス"],["💡","🔥"]],
    [["水道代","水道"],["🚰","💧"]],
    [["通信費","通信","スマホ代","携帯代","ネット代","Wi-Fi","wifi","ネット"],["📱","📶"]],
    [["保険料","保険"],["🛡️"]],
    [["食費","食料","食材"],["🍚","🍜","🥦"]],
    [["日用品費","日用品","洗剤","シャンプー","ティッシュ","トイレットペーパー","雑貨"],["🧴","🧻","🪥"]],
    [["交通費","交通","電車代","バス代","定期","切符","ICカード","Suica","特急"],["🚃","🚌","🚄"]],
    [["娯楽費","娯楽","遊び","レジャー","趣味"],["🎡","🎮","🎨"]],
    [["交際費","交際","飲み会","会食","付き合い","香典","ご祝儀"],["🥂","🍻","🤝"]],
    [["医療費","医療","病院","薬","歯医者","眼科","クリニック"],["💊","🩺","🦷"]],
    [["美容費","美容","化粧品","エステ","美容院"],["💇","💄","💅"]],
    [["被服費","被服","衣類","衣服","服飾"],["👗","👕","👟"]],
    [["特別費","特別","臨時","イベント","お祝い","記念日","冠婚葬祭"],["🎁","🎉","🎊"]],
    [["教育費","教育","学費","授業料","塾","習い事","教材","参考書","資格","本代"],["🎓","📚","✏️"]],
    [["その他","雑費","予備費","予備"],["📦","🧩","🔖"]],
    [["サブスク","月額","会員","Netflix","Spotify","YouTube","アプリ"],["🔁","🎬","🎧"]],
    [["貯金","貯蓄","投資","積立","へそくり","お小遣い"],["💰","🌱","🐷"]],
    [["クレジット","カード","返済","奨学金","借金"],["💳","🧾"]],
    [["駐輪","自転車"],["🚲"]],
    [["駐車","ガソリン","車検","高速"],["🚗","⛽"]],
    [["食費","ごはん","ご飯","外食","ランチ","ディナー","夕食","昼食","食事","グルメ"],["🍚","🍜","🍽️","🍱"]],
    [["自炊","スーパー","食材"],["🍳","🥦","🛒"]],
    [["カフェ","コーヒー","喫茶","スタバ"],["☕","🥐"]],
    [["スイーツ","お菓子","ケーキ","おやつ","デザート"],["🍰","🍪","🍩"]],
    [["お酒","酒","飲み","居酒屋","ビール","ワイン"],["🍻","🍷","🍶"]],
    [["日用","日用品","雑貨","消耗品"],["🧴","🧻","🪥"]],
    [["洋服","お洋服","服","ファッション","アパレル","靴","バッグ"],["👗","👚","👟","👜"]],
    [["コスメ","化粧","メイク","スキンケア","ファンデ","香水"],["💄","🪞","🧖"]],
    [["美容","美容院","ヘア","髪","ネイル","エステ"],["💇","💅","✂️"]],
    [["特急","電車","交通","通勤","バス","新幹線","定期","切符","ICカード","Suica"],["🚃","🚄","🚌"]],
    [["タクシー","車","ガソリン","駐車"],["🚕","🚗","⛽"]],
    [["家賃","住居","住まい","ローン"],["🏠","🔑"]],
    [["光熱","電気","ガス"],["💡","🔥"]],
    [["水道"],["🚰","💧"]],
    [["通信","スマホ","携帯","ネット","Wi-Fi","wifi"],["📱","📶"]],
    [["医療","病院","薬","歯医者","クリニック","健康"],["💊","🩺","🦷"]],
    [["推し","ライブ","推し活","コンサート","グッズ","チケット"],["🎤","🎫","🪩"]],
    [["映画","動画","配信"],["🎬","🍿","📺"]],
    [["本","書籍","漫画","マンガ","読書"],["📚","📖"]],
    [["ゲーム","アプリ課金"],["🎮","🕹️"]],
    [["音楽","楽器"],["🎧","🎸"]],
    [["旅行","旅","ホテル","宿泊","遠出"],["✈️","🧳","🏨"]],
    [["交際","デート","友達","付き合い","会食"],["🥂","💬","🤝"]],
    [["プレゼント","ギフト","お祝い","お土産"],["🎁","🎀"]],
    [["ペット","犬","猫","えさ"],["🐾","🐶","🐱"]],
    [["習い事","教育","勉強","学習","スクール","資格"],["🎓","✏️","📝"]],
    [["運動","ジム","ヨガ","スポーツ","ランニング"],["🏃","🧘","🏋️"]],
    [["サブスク","会員","月額"],["🔁","🔖"]],
    [["保険"],["🛡️"]],
    [["税","税金","年金"],["🧾","🏛️"]],
    [["貯金","貯蓄","投資","積立"],["💰","🌱"]],
    [["子供","子ども","育児","ベビー"],["🧸","🍼"]],
    [["趣味","娯楽","遊び","レジャー"],["🎨","🎡","🧩"]],
    [["家電","パソコン","ガジェット"],["🔌","💻","📷"]],
    [["インテリア","家具","部屋"],["🪴","🛋️","🕯️"]],
    [["洗濯","クリーニング"],["🧺","👔"]],
    [["花","ガーデン"],["🌷","🌻"]],
    [["寄付","募金"],["🤲","🎗️"]]
  ];
  var POOL = ["🔖","📌","🌿","🫧","🧺","🪴","🎀","⭐","🍀","🌙","🔖","🧩","🍋","🌸","🪁","🧸","🫐","🍑","🪄","🕊️","🧿","🌈","🍃","🔔","📌","🎈"];
  function matchedFor(name){
    var out = [], n = (name||"").toLowerCase();
    if(n){
      DICT.forEach(function(d){
        var hit = d[0].some(function(k){
          var kk = k.toLowerCase();
          return n.indexOf(kk)>=0 || (n.length>=2 && kk.indexOf(n)>=0);
        });
        if(hit) d[1].forEach(function(e){ if(out.indexOf(e)<0) out.push(e); });
      });
    }
    return out;
  }
  function candidates(name){
    var out = matchedFor(name);
    POOL.forEach(function(e){ if(out.indexOf(e)<0) out.push(e); });
    DICT.forEach(function(d){ d[1].forEach(function(e){ if(out.indexOf(e)<0) out.push(e); }); });
    return out;
  }
  function usedBy(excludeId){
    return state.cats.filter(function(c){ return c.id!==excludeId; }).map(function(c){ return c.emoji; });
  }
  function pickEmoji(name, excludeId){
    var used = usedBy(excludeId), list = candidates(name);
    for(var i=0;i<list.length;i++){ if(used.indexOf(list[i])<0) return list[i]; }
    return list[0];
  }
  function nextEmoji(name, current, excludeId){
    var used = usedBy(excludeId), list = candidates(name);
    var start = list.indexOf(current);
    for(var k=1;k<=list.length;k++){
      var e = list[(start+k+list.length)%list.length];
      if(used.indexOf(e)<0 && e!==current) return e;
    }
    return current;
  }

  /* ---------- 目標 ---------- */
  function adjSum(c, month){ return ((c.adj && c.adj[month]) || []).reduce(function(s,x){return s+x;},0); }
  function baseOf(c, month){ var g = goalCfg(c, month); return g.unit*g.count; }
  function goalOf(c, month){ var b = baseOf(c, month), a = adjSum(c, month); return (b>0||a!==0) ? Math.max(0, b+a) : 0; }
  function goalText(c, month){
    var s = "", g = goalCfg(c, month);
    if(g.unit>0 && g.count>0) s = g.unit.toLocaleString("ja-JP")+"円 × "+g.count+"回";
    ((c.adj && c.adj[month])||[]).forEach(function(x){ s += (x>=0?" ＋":" －")+Math.abs(x).toLocaleString("ja-JP"); });
    return s;
  }
  /* 目標は月ごと。設定がない月は、いちばん近い前の月の設定を引き継ぐ */
  function goalCfg(c, month){
    var g = c.goals || {}, best = null;
    Object.keys(g).forEach(function(k){ if(k<=month && (best===null || k>best)) best = k; });
    return best===null ? { unit:0, count:0, from:null } : { unit:g[best].unit||0, count:g[best].count||0, from:best };
  }
  function shiftYm(m, d){ var p = m.split("-"); return ym(new Date(+p[0], +p[1]-1+d, 1)); }
  function mLabel(m){ var p = m.split("-"); return p[0]+"年"+(+p[1])+"月"; }

  /* migrate */
  state.cats.forEach(function(c){
    if(c.goal && !c.unit){ c.unit = c.goal; c.count = 1; }
    delete c.goal;
    if(c.unit!==undefined || c.count!==undefined){
      c.goals = c.goals || {};
      if(c.unit || c.count) c.goals["0000-00"] = { unit:c.unit||0, count:c.count||0 };
      delete c.unit; delete c.count;
    }
    if(!c.goals) c.goals = {};
    if(!c.emoji) c.emoji = pickEmoji(c.name, c.id);
  });
  save();

  var ui = { tab:"input", mode:"pie", sel:null, editing:false, editId:null, expr:"", adj:0, month:ym(new Date()), planned:false, day:today() };

  /* ---------- tabs ---------- */
  function setTab(t){
    ui.tab = t;
    ["input","stats","cal","wish"].forEach(function(n){
      $("v-"+n).classList.toggle("on", n===t);
      $("t-"+n).setAttribute("aria-selected", n===t);
    });
    if(t==="stats") renderStats();
    if(t==="cal") renderCal();
    if(t==="input"){ renderWish(); renderRemind(); }
    if(t==="wish") renderWishTab();
  }
  $("t-input").onclick = function(){ setTab("input"); };
  $("t-stats").onclick = function(){ setTab("stats"); };
  $("t-cal").onclick = function(){ setTab("cal"); };

  /* ---------- 記録画面 ---------- */
  function renderChips(){
    var h = "";
    state.cats.forEach(function(c){
      var pr = c.auto ? goalCfg(c, ($("date").value||today()).slice(0,7)).unit : 0;
      h += '<button class="chip" data-id="'+c.id+'" aria-pressed="'+(ui.sel===c.id && !ui.editing)+'"><span>'+c.emoji+'</span>'+esc(c.name)+(pr>0?'<small class="pr">¥'+pr.toLocaleString("ja-JP")+'</small>':'')+'</button>';
    });
    h += '<button class="chip add" id="addCat">＋ 項目をつくる</button>';
    var box = $("chips");
    box.innerHTML = h;
    box.classList.toggle("editing", ui.editing);
    $("editToggle").textContent = ui.editing ? "完了" : "編集";
    $("editToggle").classList.toggle("on", ui.editing);
    $("editToggle").hidden = state.cats.length===0;
    Array.prototype.forEach.call(box.querySelectorAll(".chip[data-id]"), function(b){
      b.onclick = function(){
        if(ui.editing){ openSheet(b.getAttribute("data-id")); }
        else {
          var nid = b.getAttribute("data-id");
          if(ui.fresh && nid!==ui.sel){ ui.expr = ""; ui.fresh = false; }
          ui.sel = nid; autofill(); renderChips(); renderDisplay();
        }
      };
    });
    $("addCat").onclick = function(){ openSheet(null); };
    $("emptyHost").innerHTML = state.cats.length===0 ? '<div class="empty">最初は空っぽです。よく使う項目を自分の言葉でつくってみましょう。</div>' : "";
  }
  function autofill(){
    var c = ui.sel ? catById(ui.sel) : null;
    if(!c || !c.auto) return false;
    var g = goalCfg(c, ($("date").value||today()).slice(0,7));
    if(g.unit>0 && (ui.expr==="" || ui.fresh)){ ui.expr = String(g.unit); ui.fresh = true; return true; }
    return false;
  }
  function evalExpr(){
    var m = ui.expr.match(/[+-]?\d+/g) || [];
    return m.reduce(function(s,x){ return s + (parseInt(x,10)||0); }, 0);
  }
  function fmtExpr(s){ return s.replace(/\d+/g,function(n){return Number(n).toLocaleString("ja-JP");}).replace(/\+/g," ＋ ").replace(/-/g," － "); }
  function renderDisplay(){
    var total = evalExpr()+ui.adj;
    $("amt").innerHTML = '<small>¥</small>'+total.toLocaleString("ja-JP");
    var hasOp = /[+-]/.test(ui.expr);
    var eh = (hasOp || ui.adj) ? esc(fmtExpr(ui.expr)) : "";
    if(ui.adj) eh += ' <button class="adjtag" id="adjClear" aria-label="調整を消す">調整 '+(ui.adj>0?"＋":"－")+Math.abs(ui.adj).toLocaleString("ja-JP")+' ×</button>';
    $("expr").innerHTML = eh;
    if($("adjClear")) $("adjClear").onclick = function(){ ui.adj = 0; renderDisplay(); };
    var c = ui.sel ? catById(ui.sel) : null;
    $("forLabel").textContent = c ? (c.emoji+" "+c.name+(ui.planned?"（予定）":"")) : (state.cats.length ? "項目を選んでください" : "");
    $("rec").disabled = !(c && total>0);
    $("rec").textContent = ui.planned ? "予定に追加" : "記録";
    $("rec").classList.toggle("plan", ui.planned);
    $("k-act").setAttribute("aria-pressed", !ui.planned);
    $("k-plan").setAttribute("aria-pressed", ui.planned);
  }
  var keys = [["7","7"],["8","8"],["9","9"],["⌫","bs","op"],["4","4"],["5","5"],["6","6"],["＋","+","op"],["1","1"],["2","2"],["3","3"],["－","-","op"],["0","0"],["00","00"],["記録","rec","rec"]];
  var padHtml = "";
  keys.forEach(function(k){
    padHtml += '<button class="key '+(k[2]||"")+'" data-k="'+k[1]+'"'+(k[1]==="rec"?' id="rec"':'')+'>'+k[0]+'</button>';
  });
  $("pad").innerHTML = padHtml;
  Array.prototype.forEach.call($("pad").querySelectorAll(".key"), function(b){
    b.onclick = function(){ press(b.getAttribute("data-k")); };
  });
  (function(){
    var bs = $("pad").querySelector('[data-k="bs"]'), bsT, bsLong = false;
    bs.onpointerdown = function(){ bsLong = false; bsT = setTimeout(function(){ bsLong = true; ui.expr = ""; ui.adj = 0; ui.fresh = false; renderDisplay(); }, 500); };
    bs.onpointerup = bs.onpointerleave = function(){ clearTimeout(bsT); };
    bs.onclick = function(){ if(bsLong){ bsLong = false; return; } press("bs"); };
    $("clr").onclick = function(){ ui.expr = ""; ui.adj = 0; ui.fresh = false; renderDisplay(); };

  })();
  function press(k){
    if(ui.fresh){ if(/^\d+$/.test(k)) ui.expr = ""; if(k!=="rec") ui.fresh = false; }
    if(k==="c"){ ui.expr=""; ui.adj=0; }
    else if(k==="bs"){ ui.expr = ui.expr.slice(0,-1); }
    else if(k==="+"||k==="-"){ if(!ui.expr) return; var lc = ui.expr.slice(-1); if(lc==="+"||lc==="-") ui.expr = ui.expr.slice(0,-1)+k; else ui.expr += k; }
    else if(k==="rec"){ record(); return; }
    else {
      var last = ui.expr.split(/[+-]/).pop();
      if(last==="" && (k==="0"||k==="00")) return;
      if((last+k).length>8) return;
      ui.expr += k;
    }
    renderDisplay();
  }
  function calcAdj(){
    var t = ui.expr.match(/[+-]?\d+/g) || [];
    return t.slice(1).reduce(function(s,x){ return s + (parseInt(x,10)||0); }, 0);
  }
  function record(){
    var c = ui.sel ? catById(ui.sel) : null;
    var amt = evalExpr()+ui.adj;
    if(!c || amt<=0) return;
    var date = $("date").value || today();
    var planned = ui.planned || date > today();
    var ent = { id:uid(), cat:c.id, amt:amt, date:date, memo:$("memo").value.trim(), planned:planned, adj:calcAdj() };
    if(ui.rcpt){
      ent.receipt = { who:ui.rcpt.who, date:ui.rcpt.date, done:ui.rcpt.done, img:!!ui.rcpt.img };
      if(ui.rcpt.img) imgPut(ent.id, ui.rcpt.img);
    }
    if(ui.photo){ ent.photo = true; ent.par = ui.photoAr; imgPut("ph:"+ent.id, ui.photo); }
    state.entries.push(ent);
    save();
    ui.expr = ""; ui.adj = 0; ui.fresh = false; $("memo").value = ""; ui.rcpt = null; renderRcptBtn(); ui.photo = null; ui.photoAr = null; renderPhBtn(); renderWish(); renderRemind();
    if(planned && !ui.planned){ ui.planned = true; }
    renderDisplay();
    toast(c.emoji+" "+c.name+" "+yen(amt)+(planned?" を予定に追加しました":" を記録しました"));
  }
  var undoT = null, undoFn = null;
  function hideUndo(){ $("undo").classList.remove("on"); clearTimeout(undoT); undoFn = null; }
  function showUndo(msg, fn, rect){
    var u = $("undo"), ar = document.querySelector(".app").getBoundingClientRect();
    undoFn = fn; $("undoMsg").textContent = msg;
    u.classList.add("on");
    var w = u.offsetWidth || 220, h = u.offsetHeight || 40, left, top, below = false;
    if(rect && rect.width){
      var cx = rect.left + rect.width/2 - ar.left;
      left = Math.max(8, Math.min(ar.width - w - 8, cx - w + 28));
      top = rect.top - ar.top - h - 12;
      if(top < 8){ top = rect.bottom - ar.top + 12; below = true; }
      u.style.setProperty("--tail", Math.max(10, Math.min(w-22, cx-left-6))+"px");
    } else {
      left = (ar.width-w)/2; top = ar.height - h - 80;
      u.style.setProperty("--tail", (w/2-6)+"px");
    }
    u.classList.toggle("below", below);
    u.style.left = left+"px"; u.style.top = top+"px";
    clearTimeout(undoT); undoT = setTimeout(hideUndo, 5500);
  }
  $("undoBtn").onclick = function(){ var f = undoFn; hideUndo(); if(f) f(); };
  var tt;
  function toast(msg){
    var t = $("toast"); t.textContent = msg; t.classList.add("on");
    clearTimeout(tt); tt = setTimeout(function(){ t.classList.remove("on"); }, 1900);
  }
  function setKind(planned){
    ui.planned = planned;
    var d = $("date").value || today();
    if(planned && d<=today()){
      var t = new Date(); t.setDate(t.getDate()+1); $("date").value = ymd(t);
    }
    if(!planned && d>today()) $("date").value = today();
    renderDisplay();
  }
  $("k-act").onclick = function(){ setKind(false); };
  $("k-plan").onclick = function(){ setKind(true); };
  $("date").onchange = function(){ if(this.value > today() && !ui.planned){ ui.planned = true; } if(ui.fresh) autofill(); renderChips(); renderDisplay(); };
  $("editToggle").onclick = function(){ ui.editing = !ui.editing; renderChips(); };
  $("date").value = today();

  /* ---------- 項目シート ---------- */
  var sh = { emoji:"🌿", manual:false, adj:[], month:ym(new Date()), cache:{}, dG:{}, dA:{}, auto:false };
  function numOf(id){ return parseInt($(id).value.replace(/[^\d]/g,""),10)||0; }
  function renderSheetGoal(){
    var h = "";
    sh.adj.forEach(function(x,i){
      h += '<span class="adj num">'+(x>=0?"＋":"－")+Math.abs(x).toLocaleString("ja-JP")+'<button data-i="'+i+'" aria-label="削除">×</button></span>';
    });
    $("adjs").innerHTML = h;
    Array.prototype.forEach.call($("adjs").querySelectorAll("button"), function(b){
      b.onclick = function(){ sh.adj.splice(+b.getAttribute("data-i"),1); sh.dA[sh.month] = true; renderSheetGoal(); };
    });
    var u = numOf("gUnit"), n = numOf("gCount");
    var a = sh.adj.reduce(function(s,x){return s+x;},0);
    var tot = Math.max(0, u*n + a);
    var s = "";
    if(u>0 && n>0) s = u.toLocaleString("ja-JP")+"円 × "+n+"回";
    sh.adj.forEach(function(x){ s += (s?" ":"")+(x>=0?"＋":"－")+Math.abs(x).toLocaleString("ja-JP"); });
    $("preview").innerHTML = tot>0 ? (+sh.month.slice(5,7))+'月の目標　'+s+' ＝ <b>'+tot.toLocaleString("ja-JP")+'円</b>' : '<span style="color:var(--sub)">'+(+sh.month.slice(5,7))+'月の目標は未設定です</span>';
  }
  function captureMonth(){ sh.cache[sh.month] = { unit:numOf("gUnit"), count:numOf("gCount"), adj:sh.adj.slice() }; }
  function loadMonth(m){
    var c = ui.editId ? catById(ui.editId) : null, cf = sh.cache[m], unit = 0, count = 0, adj = [], hint = "";
    sh.month = m;
    if(cf){ unit = cf.unit; count = cf.count; adj = cf.adj.slice(); }
    else if(c){
      var g = goalCfg(c, m);
      unit = g.unit; count = g.count;
      adj = (c.adj && c.adj[m]) ? c.adj[m].slice() : [];
      if(g.from && g.from!==m) hint = g.from==="0000-00" ? "これまでの共通の設定を引き継いでいます。" : mLabel(g.from)+"の設定を引き継いでいます。";
    }
    $("gUnit").value = unit || ""; $("gCount").value = count || "";
    sh.adj = adj;
    $("gmLabel").textContent = mLabel(m);
    $("gmHint").textContent = hint || "目標は月ごとに決められます。変えた月から先の月に引き継がれます。";
    $("adjLabel").textContent = (+m.slice(5,7))+"月の調整（値上がり・値下がり）";
    renderSheetGoal();
  }
  function openSheet(id){
    ui.editId = id;
    var c = id ? catById(id) : null;
    $("sheetTitle").textContent = c ? "項目を編集" : "項目をつくる";
    $("catName").value = c ? c.name : "";
    sh.cache = {}; sh.dG = {}; sh.dA = {};
    sh.auto = !!(c && c.auto);
    $("autoBtn").setAttribute("aria-pressed", sh.auto);
    sh.emoji = c ? c.emoji : pickEmoji("", null);
    sh.manual = c ? (c.emoji !== pickEmoji(c.name, c.id)) : false;
    $("emoBtn").textContent = sh.emoji;
    $("catDel").hidden = !c;
    loadMonth(ui.tab==="input" ? ym(new Date()) : ui.month);
    $("scrim").classList.add("on"); $("sheet").classList.add("on");
    setTimeout(function(){ $("catName").focus(); }, 280);
  }
  function closeSheet(){ $("scrim").classList.remove("on"); Array.prototype.forEach.call(document.querySelectorAll(".sheet"), function(s){ s.classList.remove("on"); }); }
  $("scrim").onclick = closeSheet;
  $("catCancel").onclick = closeSheet;
  $("gmPrev").onclick = function(){ captureMonth(); loadMonth(shiftYm(sh.month,-1)); };
  $("gmNext").onclick = function(){ captureMonth(); loadMonth(shiftYm(sh.month,1)); };
  $("autoBtn").onclick = function(){ sh.auto = !sh.auto; this.setAttribute("aria-pressed", sh.auto); };
  $("catName").oninput = function(){
    if(!sh.manual){ sh.emoji = pickEmoji(this.value.trim(), ui.editId); $("emoBtn").textContent = sh.emoji; }
  };
  var EMOJI_CATS = [
    ["😀","顔","😀 😃 😄 😁 😆 😅 😂 🤣 😊 😇 🙂 🙃 😉 😌 😍 🥰 😘 😗 😙 😚 😋 😛 😜 🤪 😝 🤑 🤗 🤭 🤫 🤔 🤐 🤨 😐 😑 😶 😏 😒 🙄 😬 😔 😪 🤤 😴 😷 🤒 🤕 🤢 🤮 🤧 🥵 🥶 🥴 😵 🤯 🤠 🥳 😎 🤓 🧐 😕 😟 🙁 😮 😲 😳 🥺 😨 😰 😥 😢 😭 😱 😖 😣 😞 😓 😩 😫 🥱 😤 😡 😠 🤬 😈 💀 💩 🤡 👻 👽 🤖"],
    ["👋","人・手","👋 🤚 ✋ 🖖 👌 🤏 ✌️ 🤞 🤟 🤘 🤙 👈 👉 👆 👇 👍 👎 ✊ 👊 👏 🙌 👐 🤲 🙏 💪 👀 👂 👃 🧠 👶 👧 🧒 👦 👩 🧑 👨 🧓 👵 👴 💃 🕺 👪 💅 👑 🎅 🧙 🧚 🦸"],
    ["🐶","動物・自然","🐶 🐱 🐭 🐹 🐰 🦊 🐻 🐼 🐨 🐯 🦁 🐮 🐷 🐸 🐵 🐔 🐧 🐦 🐤 🦆 🦉 🐺 🐴 🦄 🐝 🐛 🦋 🐌 🐞 🐜 🐢 🐍 🐙 🦀 🐠 🐟 🐬 🐳 🦈 🐊 🐘 🦒 🐑 🐐 🐕 🐈 🌵 🎄 🌲 🌳 🌴 🌱 🌿 ☘️ 🍀 🎍 🍃 🍂 🍁 🌾 🌺 🌻 🌹 🌷 🌸 💐 🌼 🌙 ⭐ 🌟 ✨ ⚡ 🔥 🌈 ☀️ ⛅ ☁️ ❄️ ⛄ 💧 🌊 🪴 🫧"],
    ["🍔","食べ物","🍎 🍐 🍊 🍋 🍌 🍉 🍇 🍓 🍒 🍑 🥭 🍍 🥥 🥝 🍅 🥑 🍆 🥔 🥕 🌽 🥦 🍄 🍞 🥐 🥖 🧀 🥚 🍳 🥞 🥓 🥩 🍗 🍖 🌭 🍔 🍟 🍕 🥪 🌮 🌯 🍝 🍜 🍲 🍛 🍣 🍱 🥟 🍤 🍙 🍚 🍘 🍥 🍢 🍡 🍧 🍨 🍦 🥧 🍰 🎂 🍮 🍭 🍬 🍫 🍿 🍩 🍪 🥛 ☕ 🍵 🍶 🍺 🍻 🥂 🍷 🥃 🍸 🍹 🧃 🥤"],
    ["⚽","アクティビティ","⚽ 🏀 🏈 ⚾ 🎾 🏐 🏉 🎱 🏓 🏸 🥊 🎯 ⛳ 🎣 🎿 🛷 🏂 🏋️ 🤸 🚴 🏊 🧘 🎮 🕹️ 🎲 🧩 🎨 🎭 🎬 🎤 🎧 🎼 🎹 🥁 🎷 🎺 🎸 🎻 🎳 🎪 🎫 🎟️ 🏆 🥇"],
    ["🚗","乗り物・場所","🚗 🚕 🚙 🚌 🚎 🏎️ 🚓 🚑 🚒 🚚 🚜 🛵 🏍️ 🚲 🛴 🚃 🚄 🚅 🚆 🚇 🚉 ✈️ 🛫 🛬 🚀 🛸 🚁 ⛵ 🚤 🛳️ ⚓ ⛽ 🚦 🗺️ 🗼 🗽 🏰 🏯 ⛩️ 🏠 🏡 🏢 🏬 🏥 🏦 🏫 🏪 ⛪ 🛖 ⛺ 🌋 🗻 🏖️ 🏝️ 🎡 🎢 🎠"],
    ["💡","モノ","⌚ 📱 💻 ⌨️ 🖥️ 🖨️ 📷 📸 📹 🎥 📺 📻 ⏰ 💡 🔦 🔋 🔌 💰 💴 💵 💳 💎 🧾 💸 📦 🛍️ 🛒 🎁 🎀 🎈 🧸 🔑 🔨 🔧 🧰 🧲 🔬 💊 💉 🩺 🧴 🧼 🧻 🪥 🧽 🚿 🛁 🛏️ 🛋️ 🪑 🚪 🧺 👓 🕶️ 👔 👕 👖 🧣 🧤 🧥 🧦 👗 👘 👙 👚 👛 👜 👝 🎒 👞 👟 👠 👡 👢 👒 🎩 🎓 💄 💍 🪞 🌂 ☂️ 📚 📖 📝 ✏️ 🖊️ 📌 📎 📅 📋 🗂️ 📈 📉 ✉️ 📮 🔔 🔖 🏷️ 🕯️"],
    ["❤️","記号","❤️ 🧡 💛 💚 💙 💜 🖤 🤍 🤎 💔 ❣️ 💕 💞 💓 💗 💖 💘 💝 ✅ ❌ ❓ ❗ ⚠️ ♻️ 🔁 🔄 ➕ ➖ ➗ ✖️ ♾️ 💯 🔴 🟠 🟡 🟢 🔵 🟣 ⚫ ⚪ 🔶 🔷 ▶️ ⏸️ 🔔 🔕 🆗 🆕 🆓 🛡️ ⭕ ✔️ 🎌"]
  ];
  var pkCat = 0, pkSug = [];
  function renderPicker(){
    var used = usedBy(ui.editId);
    var tabs = '<button class="pktab" data-i="0" aria-pressed="'+(pkCat===0)+'" aria-label="おすすめ">✨</button>';
    EMOJI_CATS.forEach(function(c,i){ tabs += '<button class="pktab" data-i="'+(i+1)+'" aria-pressed="'+(pkCat===i+1)+'" aria-label="'+c[1]+'">'+c[0]+'</button>'; });
    $("pkTabs").innerHTML = tabs;
    var list, label;
    if(pkCat===0){ list = pkSug; label = "名前に近い絵文字"; }
    else { list = EMOJI_CATS[pkCat-1][2].split(" "); label = EMOJI_CATS[pkCat-1][1]; }
    var h = '<div class="pklabel">'+label+'</div>';
    if(!list.length) h += '<div class="pkhint">名前に近い絵文字は見つかりませんでした。上のタブから選んでください。</div>';
    list.forEach(function(e){
      h += '<button class="pk'+(e===sh.emoji?' sel':'')+(used.indexOf(e)>=0?' used':'')+'" data-e="'+e+'" type="button">'+e+'</button>';
    });
    $("pkGrid").innerHTML = h;
    Array.prototype.forEach.call($("pkTabs").querySelectorAll(".pktab"), function(b){
      b.onclick = function(){ pkCat = +b.getAttribute("data-i"); renderPicker(); };
    });
    Array.prototype.forEach.call($("pkGrid").querySelectorAll(".pk"), function(b){
      b.onclick = function(){
        sh.emoji = b.getAttribute("data-e"); sh.manual = true;
        $("emoBtn").textContent = sh.emoji;
        $("pksheet").classList.remove("on");
      };
    });
  }
  function openPicker(){
    if(document.activeElement && document.activeElement.blur) document.activeElement.blur();
    pkSug = matchedFor($("catName").value.trim());
    pkCat = pkSug.length ? 0 : 1;
    renderPicker();
    $("pksheet").classList.add("on");
  }
  $("emoBtn").onclick = openPicker;
  $("pkClose").onclick = function(){ $("pksheet").classList.remove("on"); };

  $("gUnit").oninput = function(){ sh.dG[sh.month] = true; renderSheetGoal(); };
  $("gCount").oninput = function(){ sh.dG[sh.month] = true; renderSheetGoal(); };
  function addAdj(){
    var raw = $("adjIn").value.replace(/＋/g,"+").replace(/[－ー−]/g,"-").replace(/[,\s円]/g,"");
    var v = parseInt(raw,10);
    if(!v) return;
    sh.adj.push(v); sh.dA[sh.month] = true; $("adjIn").value = ""; renderSheetGoal();
  }
  $("adjAdd").onclick = addAdj;
  $("adjIn").onkeydown = function(e){ if(e.key==="Enter"){ e.preventDefault(); addAdj(); } };
  $("catSave").onclick = function(){
    var name = $("catName").value.trim();
    if(!name){ $("catName").focus(); return; }
    captureMonth();
    var emoji = sh.emoji;
    if(!sh.manual && usedBy(ui.editId).indexOf(emoji)>=0) emoji = pickEmoji(name, ui.editId);
    var c = ui.editId ? catById(ui.editId) : null;
    if(!c){ c = { id:uid(), goals:{}, adj:{} }; state.cats.push(c); ui.sel = c.id; }
    if(!c.goals) c.goals = {};
    if(!c.adj) c.adj = {};
    c.name = name; c.emoji = emoji; c.auto = sh.auto;
    Object.keys(sh.dG).forEach(function(m){ var cf = sh.cache[m]; if(cf) c.goals[m] = { unit:cf.unit, count:cf.count }; });
    Object.keys(sh.dA).forEach(function(m){
      var cf = sh.cache[m]; if(!cf) return;
      if(cf.adj.length) c.adj[m] = cf.adj.slice(); else delete c.adj[m];
    });
    save(); closeSheet(); renderChips(); renderDisplay();
    if(ui.tab==="stats") renderStats();
    if(ui.tab==="cal") renderCal();
    if(ui.tab==="input") renderWish();
  };
  $("catDel").onclick = function(){
    var c = catById(ui.editId); if(!c) return;
    var used = state.entries.some(function(e){ return e.cat===c.id; });
    var msg = used ? "「"+c.name+"」とその記録をすべて削除します。よろしいですか？" : "「"+c.name+"」を削除します。よろしいですか？";
    if(!window.confirm(msg)) return;
    state.cats = state.cats.filter(function(x){ return x.id!==c.id; });
    state.entries = state.entries.filter(function(e){ return e.cat!==c.id; });
    if(ui.sel===c.id) ui.sel = null;
    save(); closeSheet(); renderChips(); renderDisplay();
    if(ui.tab==="stats") renderStats();
    if(ui.tab==="cal") renderCal();
  };

  /* ---------- 共通 ---------- */
  function rowHtml(e, showDate){
    var c = catById(e.cat);
    var d = '<span class="d num">'+(+e.date.slice(5,7))+'/'+(+e.date.slice(8,10))+'</span>';
    var bits = [];
    if(e.memo) bits.push(esc(e.memo));
    if(e.adj) bits.push("調整 "+(e.adj>0?"＋":"－")+Math.abs(e.adj).toLocaleString("ja-JP"));
    if(e.receipt){
      if(e.receipt.date) bits.push("引換 "+md(e.receipt.date)+(e.receipt.who?" "+esc(e.receipt.who):"")+(e.receipt.done?"（済）":""));
      else if(e.receipt.who) bits.push(esc(e.receipt.who));
    }
    var h = '<div class="row'+(e.planned?' plan':'')+'" data-eid="'+e.id+'">'+(showDate?d:'');
    h += '<span class="c">'+(c?c.emoji+' '+esc(c.name):'')+(e.receipt?'<span class="rc'+(e.receipt.done?' done':'')+'">'+RC_SVG+'</span>':'')+(e.photo?'<span class="rc">'+PH_SVG+'</span>':'')+(e.planned?'<span class="tag">予定</span>':'')+(bits.length?'<small>'+bits.join(" ・ ")+'</small>':'')+'</span>';
    h += '<span class="num">'+yen(e.amt)+'</span>';
    if(e.planned) h += '<button class="ok" data-ok="'+e.id+'">使った</button>';
    h += '<button class="x" data-del="'+e.id+'" aria-label="削除">×</button></div>';
    return h;
  }
  function bindRows(box, after, drag){
    bindEdit(box, after, drag);
    Array.prototype.forEach.call(box.querySelectorAll("[data-del]"), function(b){
      b.onclick = function(){
        var id = b.getAttribute("data-del");
        state.entries = state.entries.filter(function(e){ return e.id!==id; });
        imgDel(id); imgDel("ph:"+id); save(); after(); renderRemind();
      };
    });
    Array.prototype.forEach.call(box.querySelectorAll("[data-ok]"), function(b){
      b.onclick = function(){
        var id = b.getAttribute("data-ok"), r = b.getBoundingClientRect(), pd = null;
        state.entries.forEach(function(e){
          if(e.id===id){ pd = e.date; e.planned = false; if(e.date>today()) e.date = today(); }
        });
        save(); after(); renderRemind();
        showUndo("記録に変えました", function(){
          var e = entryById(id);
          if(e){ e.planned = true; e.date = pd; save(); after(); renderRemind(); }
        }, r);
      };
    });
  }
  /* ---------- 記録の編集・ドラッグ移動 ---------- */
  function entryById(id){ for(var i=0;i<state.entries.length;i++){ if(state.entries[i].id===id) return state.entries[i]; } return null; }
  var es = { id:null, cat:null, planned:false, after:null };
  function renderEChips(){
    var h = "";
    state.cats.forEach(function(c){ h += '<button class="chip" data-id="'+c.id+'" aria-pressed="'+(es.cat===c.id)+'"><span>'+c.emoji+'</span>'+esc(c.name)+'</button>'; });
    $("eChips").innerHTML = h;
    Array.prototype.forEach.call($("eChips").querySelectorAll(".chip"), function(b){
      b.onclick = function(){ es.cat = b.getAttribute("data-id"); renderEChips(); updateECal(); };
    });
  }
  function renderEKind(){ $("eAct").setAttribute("aria-pressed", !es.planned); $("ePlan").setAttribute("aria-pressed", es.planned); }
  function openEntry(id, after){
    var e = entryById(id); if(!e) return;
    es.id = id; es.cat = e.cat; es.planned = !!e.planned; es.after = after;
    $("eAmt").value = e.amt; $("eMemo").value = e.memo || ""; $("eDate").value = e.date;
    renderEChips(); renderEKind(); updateECal();
    $("scrim").classList.add("on"); $("esheet").classList.add("on");
  }
  $("eAct").onclick = function(){ es.planned = false; renderEKind(); updateECal(); };
  $("ePlan").onclick = function(){ es.planned = true; renderEKind(); updateECal(); };
  $("eCancel").onclick = closeSheet;
  $("eSave").onclick = function(){
    var e = entryById(es.id); if(!e){ closeSheet(); return; }
    var amt = parseInt($("eAmt").value.replace(/[^\d]/g,""),10)||0;
    if(amt<=0){ $("eAmt").focus(); return; }
    var date = $("eDate").value || e.date;
    e.cat = es.cat; e.amt = amt; e.memo = $("eMemo").value.trim(); e.date = date;
    e.planned = es.planned || date>today();
    save(); closeSheet();
    if(date.slice(0,7)===ui.month) ui.day = date;
    if(es.after) es.after();
  };
  $("eDel").onclick = function(){
    if(!window.confirm("この記録を削除します。よろしいですか？")) return;
    state.entries = state.entries.filter(function(x){ return x.id!==es.id; });
    imgDel(es.id); imgDel("ph:"+es.id); save(); closeSheet(); if(es.after) es.after();
  };
  var suppressClick = false;
  function bindEdit(box, after, drag){
    Array.prototype.forEach.call(box.querySelectorAll(".row[data-eid]"), function(row){
      var id = row.getAttribute("data-eid");
      row.addEventListener("click", function(ev){
        if(ev.target.closest("button")) return;
        if(suppressClick){ suppressClick = false; return; }
        openEntry(id, after);
      });
      if(drag){
        row.addEventListener("contextmenu", function(ev){ ev.preventDefault(); });
        row.addEventListener("pointerdown", function(ev){
          if(ev.target.closest("button")) return;
          if(ev.pointerType==="mouse" && ev.button!==0) return;
          startPress(row, id, ev, after);
        });
      }
    });
  }
  function moveEntry(id, date, after){
    var e = entryById(id); if(!e || e.date===date) return;
    var toPlan = date>today() && !e.planned;
    e.date = date; if(toPlan) e.planned = true;
    save(); ui.day = date;
    toast((+date.slice(5,7))+"/"+(+date.slice(8,10))+" に移しました"+(toPlan?"（予定になりました）":""));
    after();
  }
  function startPress(row, id, ev, after){
    var sx = ev.clientX, sy = ev.clientY, px = sx, py = sy, dragging = false, ghost = null, target = null, auto = null;
    var scroller = document.querySelector("#v-cal .scroll");
    var timer = setTimeout(begin, 380);
    function block(e){ e.preventDefault(); }
    function place(){
      if(!ghost) return;
      ghost.style.left = (px-105)+"px"; ghost.style.top = (py-26)+"px";
      var el = document.elementFromPoint(px, py);
      var cell = el && el.closest ? el.closest(".cell[data-d]") : null;
      Array.prototype.forEach.call(document.querySelectorAll(".cell.drop"), function(c){ c.classList.remove("drop"); });
      target = null;
      if(cell){ cell.classList.add("drop"); target = cell.getAttribute("data-d"); }
    }
    function tick(){
      var r = scroller.getBoundingClientRect();
      if(py < r.top+60) scroller.scrollTop -= 12; else if(py > r.bottom-60) scroller.scrollTop += 12;
      place();
    }
    function begin(){
      dragging = true;
      ghost = row.cloneNode(true);
      ghost.classList.add("ghost");
      document.body.appendChild(ghost);
      row.classList.add("lifting");
      document.addEventListener("touchmove", block, { passive:false });
      try{ if(navigator.vibrate) navigator.vibrate(12); }catch(err){}
      auto = setInterval(tick, 30);
      place();
    }
    function onMove(e){
      px = e.clientX; py = e.clientY;
      if(!dragging){
        if(Math.abs(px-sx)>8 || Math.abs(py-sy)>8) finish(false);
        return;
      }
      place();
    }
    function onUp(){ finish(true); }
    function onCancel(){ finish(false); }
    function finish(drop){
      clearTimeout(timer); clearInterval(auto);
      document.removeEventListener("pointermove", onMove);
      document.removeEventListener("pointerup", onUp);
      document.removeEventListener("pointercancel", onCancel);
      document.removeEventListener("touchmove", block, { passive:false });
      if(ghost && ghost.parentNode) ghost.parentNode.removeChild(ghost);
      row.classList.remove("lifting");
      Array.prototype.forEach.call(document.querySelectorAll(".cell.drop"), function(c){ c.classList.remove("drop"); });
      if(dragging){
        suppressClick = true; setTimeout(function(){ suppressClick = false; }, 400);
        if(drop && target) moveEntry(id, target, after);
      }
      dragging = false;
    }
    document.addEventListener("pointermove", onMove);
    document.addEventListener("pointerup", onUp);
    document.addEventListener("pointercancel", onCancel);
  }

  function shiftMonth(d){
    var p = ui.month.split("-"); var dt = new Date(+p[0], +p[1]-1+d, 1);
    ui.month = ym(dt);
    ui.day = ui.month===ym(new Date()) ? today() : ui.month+"-01";
    if(ui.tab==="stats") renderStats(); else renderCal();
  }
  function monthLabel(){ var p = ui.month.split("-"); return p[0]+"年"+(+p[1])+"月"; }
  $("prev").onclick = function(){ shiftMonth(-1); };
  $("next").onclick = function(){ shiftMonth(1); };
  $("cprev").onclick = function(){ shiftMonth(-1); };
  $("cnext").onclick = function(){ shiftMonth(1); };

  /* ---------- 集計 ---------- */
  function monthEntries(){ return state.entries.filter(function(e){ return e.date.slice(0,7)===ui.month; }); }
  function sums(){
    var m = {}, pm = {};
    monthEntries().forEach(function(e){
      if(e.planned) pm[e.cat] = (pm[e.cat]||0)+e.amt; else m[e.cat] = (m[e.cat]||0)+e.amt;
    });
    return state.cats.map(function(c,i){ return { cat:c, i:i, v:m[c.id]||0, p:pm[c.id]||0, goal:goalOf(c, ui.month) }; });
  }
  $("m-pie").onclick = function(){ ui.mode="pie"; renderStats(); };
  $("m-bar").onclick = function(){ ui.mode="bar"; renderStats(); };

  function arc(cx,cy,r,a0,a1){
    var x0=cx+r*Math.cos(a0), y0=cy+r*Math.sin(a0), x1=cx+r*Math.cos(a1), y1=cy+r*Math.sin(a1);
    return "M"+x0+" "+y0+" A"+r+" "+r+" 0 "+((a1-a0)>Math.PI?1:0)+" 1 "+x1+" "+y1;
  }
  function pieSvg(data,total){
    var S=240, cx=120, cy=120, r=90, sw=30;
    var svg = '<svg viewBox="0 0 '+S+' '+S+'" width="100%" style="max-width:260px;display:block;margin:0 auto" role="img" aria-label="項目別の支出の円グラフ">';
    svg += '<circle cx="'+cx+'" cy="'+cy+'" r="'+r+'" fill="none" stroke="var(--key)" stroke-width="'+sw+'"/>';
    if(total>0){
      var nz = data.filter(function(d){return d.v>0;});
      if(nz.length===1){
        svg += '<circle cx="'+cx+'" cy="'+cy+'" r="'+r+'" fill="none" stroke="'+color(nz[0].i)+'" stroke-width="'+sw+'"/>';
      } else {
        var a = -Math.PI/2, gap = 0.025;
        nz.forEach(function(d){
          var sweep = d.v/total*Math.PI*2;
          svg += '<path d="'+arc(cx,cy,r,a+gap/2,a+sweep-gap/2)+'" fill="none" stroke="'+color(d.i)+'" stroke-width="'+sw+'"/>';
          a += sweep;
        });
      }
    }
    svg += '<text x="'+cx+'" y="'+(cy-4)+'" text-anchor="middle" font-size="12" fill="var(--sub)">合計</text>';
    svg += '<text x="'+cx+'" y="'+(cy+22)+'" text-anchor="middle" font-size="22" font-weight="500" fill="var(--ink)">'+yen(total)+'</text>';
    return svg + '</svg>';
  }
  function barSvg(data){
    var n = data.length, colW = 78, W = Math.max(320, n*colW), H = 290, top = 40, bot = 58, ch = H-top-bot;
    var max = 0; data.forEach(function(d){ max = Math.max(max, d.v+d.p, d.goal); });
    if(max<=0) max = 1;
    var svg = '<svg viewBox="0 0 '+W+' '+H+'" width="'+W+'" height="'+H+'" role="img" aria-label="項目別の支出と目標の棒グラフ">';
    svg += '<line x1="0" x2="'+W+'" y1="'+(top+ch)+'" y2="'+(top+ch)+'" stroke="var(--line)"/>';
    function rbar(x,y,w,h,fill,dashed){
      if(h<=0) return "";
      var r = Math.min(6,h);
      var d = 'M'+x+' '+(y+h)+' V'+(y+r)+' Q'+x+' '+y+' '+(x+r)+' '+y+' H'+(x+w-r)+' Q'+(x+w)+' '+y+' '+(x+w)+' '+(y+r)+' V'+(y+h)+' Z';
      return dashed ? '<path d="'+d+'" fill="'+fill+'" fill-opacity=".14" stroke="'+fill+'" stroke-width="1.5" stroke-dasharray="3 3"/>'
                    : '<path d="'+d+'" fill="'+fill+'"/>';
    }
    data.forEach(function(d,k){
      var x0 = k*(W/n), cw = W/n, bw = Math.min(34, cw*0.46), bx = x0+(cw-bw)/2;
      var h = d.v/max*ch, ph = d.p/max*ch, y = top+ch-h;
      var over = d.goal>0 && d.v>d.goal;
      var col = over ? 'var(--warn)' : color(d.i);
      if(d.p>0) svg += rbar(bx, y-ph, bw, ph+ (h>0?6:0), color(d.i), true);
      if(h>0) svg += rbar(bx, y, bw, h, col, false);
      var topY = top+ch-h-ph;
      svg += '<text x="'+(x0+cw/2)+'" y="'+(topY-7)+'" text-anchor="middle" font-size="11" fill="'+(over?'var(--warn)':'var(--ink)')+'" font-weight="500">'+d.v.toLocaleString("ja-JP")+'</text>';
      if(d.p>0) svg += '<text x="'+(x0+cw/2)+'" y="'+(topY-20)+'" text-anchor="middle" font-size="10" fill="var(--sub)">+'+d.p.toLocaleString("ja-JP")+'</text>';
      if(d.goal>0){
        var gy = top+ch-d.goal/max*ch;
        svg += '<line x1="'+(bx-10)+'" x2="'+(bx+bw+10)+'" y1="'+gy+'" y2="'+gy+'" stroke="var(--ink)" stroke-width="1.5" stroke-dasharray="3 4" stroke-linecap="round"/>';
      }
      var nm = d.cat.name.length>5 ? d.cat.name.slice(0,5)+"…" : d.cat.name;
      svg += '<text x="'+(x0+cw/2)+'" y="'+(top+ch+22)+'" text-anchor="middle" font-size="16">'+d.cat.emoji+'</text>';
      svg += '<text x="'+(x0+cw/2)+'" y="'+(top+ch+40)+'" text-anchor="middle" font-size="11" fill="var(--sub)">'+esc(nm)+'</text>';
    });
    return svg + '</svg>';
  }
  function goalList(data){
    var h = '<div class="goals">';
    data.forEach(function(d){
      var pct = d.goal>0 ? Math.min(100, d.v/d.goal*100) : 0;
      var over = d.goal>0 && d.v>d.goal;
      h += '<button class="goal" data-g="'+d.cat.id+'"><div class="g1"><span>'+d.cat.emoji+' '+esc(d.cat.name)+'</span><span class="num">'+(d.goal>0 ? d.v.toLocaleString("ja-JP")+' / '+d.goal.toLocaleString("ja-JP") : '目標を決める')+'</span></div>';
      if(d.goal>0){
        h += '<div class="g2 num">'+goalText(d.cat, ui.month)+'</div><div class="bar"><i style="width:'+pct+'%;background:'+(over?'var(--warn)':color(d.i))+'"></i></div>';
      }
      h += '</button>';
    });
    return h + '</div>';
  }

  function message(data,total,ptotal){
    var p = ui.month.split("-"), y=+p[0], m=+p[1];
    var now = new Date(), isCur = ym(now)===ui.month;
    var left = isCur ? new Date(y,m,0).getDate()-now.getDate() : 0;
    var wish = (state.wishes[ui.month]||"").trim();
    var out = [];
    if(wish){
      out.push("今月の楽しみは「"+esc(wish)+"」だったね！");
      var hit = null;
      data.forEach(function(d){
        var nm = d.cat.name;
        if(nm && (wish.indexOf(nm)>=0 || nm.indexOf(wish)>=0)) hit = d;
      });
      if(hit){
        if(hit.goal>0){
          if(hit.v<=hit.goal) out.push(hit.cat.emoji+" 「"+esc(hit.cat.name)+"」は"+yen(hit.v)+"で、目標の"+yen(hit.goal)+"まであと"+yen(hit.goal-hit.v)+"。思いきり楽しんでね。");
          else out.push(hit.cat.emoji+" 「"+esc(hit.cat.name)+"」は目標を"+yen(hit.v-hit.goal)+"超えたけれど、それだけ大切な楽しみだったんだね。他の項目で少し整えよう。");
        } else if(hit.v>0){
          out.push(hit.cat.emoji+" 「"+esc(hit.cat.name)+"」には"+yen(hit.v)+"使ったよ。楽しみのための支出、いいね。");
        } else if(hit.p>0){
          out.push(hit.cat.emoji+" 「"+esc(hit.cat.name)+"」は"+yen(hit.p)+"の予定が入っているね。楽しみにしていよう。");
        }
      }
    } else {
      out.push("今月の楽しみを下に書いておくと、ここで一緒に振り返れるよ。");
    }
    if(total===0 && ptotal===0){ out.push("まだ記録がないよ。ゆっくり始めよう。"); return out; }
    var withGoal = data.filter(function(d){ return d.goal>0; });
    var overs = withGoal.filter(function(d){ return d.v>d.goal; });
    if(withGoal.length){
      if(overs.length===0){
        out.push("目標を決めた"+withGoal.length+"項目は、どれも目標内。この調子で"+(left>0?"あと"+left+"日、":"")+"目標に向かって頑張ろう！");
      } else {
        var o = overs.sort(function(a,b){return (b.v-b.goal)-(a.v-a.goal);})[0];
        out.push(o.cat.emoji+" 「"+esc(o.cat.name)+"」が目標を"+yen(o.v-o.goal)+"超えているよ。"+(left>0?"残り"+left+"日で、無理のない範囲で調整してみよう。":"来月は少し意識してみよう。"));
      }
      var risky = withGoal.filter(function(d){ return d.p>0 && d.v<=d.goal && d.v+d.p>d.goal; });
      if(risky.length) out.push("予定を足すと、"+risky.map(function(d){return d.cat.emoji+"「"+esc(d.cat.name)+"」";}).join("・")+"は目標を超えそう。予定を見直すなら今のうちだよ。");
      else if(ptotal>0) out.push("使う予定の"+yen(ptotal)+"を足しても、目標の範囲に収まりそう。");
    } else {
      out.push("項目に月の目標を入れると、棒グラフに点線が出るよ。");
    }
    var top1 = data.slice().sort(function(a,b){return b.v-a.v;})[0];
    if(top1 && top1.v>0 && total>0) out.push("いちばん使ったのは"+top1.cat.emoji+"「"+esc(top1.cat.name)+"」（"+Math.round(top1.v/total*100)+"%）。");
    return out;
  }

  function renderStats(){
    $("monthLabel").textContent = monthLabel();
    $("m-pie").setAttribute("aria-pressed", ui.mode==="pie");
    $("m-bar").setAttribute("aria-pressed", ui.mode==="bar");
    var data = sums();
    var total = data.reduce(function(s,d){return s+d.v;},0);
    var ptotal = data.reduce(function(s,d){return s+d.p;},0);
    var th = '<div><div class="l">支出</div><div class="v num"><small>¥</small>'+Math.round(total).toLocaleString("ja-JP")+'</div></div>';
    if(ptotal>0) th += '<div class="pl"><div class="l">使う予定</div><div class="v num"><span class="box">'+yen(ptotal)+'</span></div></div>';
    $("total").innerHTML = th;

    var chart = $("chart"), below = $("below");
    if(state.cats.length===0){
      chart.innerHTML = '<div class="empty" style="padding:40px 0;text-align:center">「記録」で項目をつくると、ここにグラフが出ます。</div>';
      below.innerHTML = "";
    } else if(ui.mode==="pie"){
      chart.innerHTML = pieSvg(data,total);
      var rows = data.slice().sort(function(a,b){return b.v-a.v;});
      var l = '<ul class="legend">';
      rows.forEach(function(d){
        l += '<li><span class="dot" style="background:'+color(d.i)+'"></span><span class="n">'+d.cat.emoji+' '+esc(d.cat.name)+'</span><span class="p num">'+(total>0?Math.round(d.v/total*100):0)+'%</span><span class="a num">'+yen(d.v)+'</span></li>';
      });
      below.innerHTML = l+'</ul>';
    } else {
      chart.innerHTML = '<div class="bars-wrap">'+barSvg(data)+'</div>';
      below.innerHTML = '<div class="key-note"><span><i style="width:16px;border-top:1.5px dashed var(--ink)"></i>目標</span><span><i style="width:10px;height:10px;border-radius:2px;border:1.5px dashed var(--sub)"></i>使う予定</span><span><i style="width:8px;height:8px;border-radius:2px;background:var(--warn)"></i>目標超え</span></div>'+goalList(data);
      Array.prototype.forEach.call(below.querySelectorAll("[data-g]"), function(b){
        b.onclick = function(){ openSheet(b.getAttribute("data-g")); };
      });
    }

    var msgs = message(data,total,ptotal);
    var c = '<div class="who">ひとこと</div>';
    msgs.forEach(function(m){ c += '<p>'+m+'</p>'; });
    c += '<div class="wish"><label for="wishIn">今月の楽しみ</label><input id="wishIn" maxlength="30" placeholder="例：お洋服を買うこと" value="'+esc(state.wishes[ui.month]||"")+'"></div>';
    $("coach").innerHTML = c;
    $("wishIn").onchange = function(){ state.wishes[ui.month] = this.value; save(); renderStats(); renderWish(); };

    var es = monthEntries().sort(function(a,b){ return a.date<b.date?1:(a.date>b.date?-1:0); });
    var h = "";
    if(es.length){
      h = '<h3>記録と予定</h3>';
      es.forEach(function(e){ h += rowHtml(e, true); });
    }
    $("hist").innerHTML = h;
    bindRows($("hist"), renderStats);
  }

  /* ---------- カレンダー ---------- */
  var WD = ["日","月","火","水","木","金","土"];
  function renderCal(){
    $("cMonthLabel").textContent = monthLabel();
    var p = ui.month.split("-"), y=+p[0], m=+p[1];
    var first = new Date(y,m-1,1).getDay(), days = new Date(y,m,0).getDate();
    var act = {}, pln = {}, ta = 0, tp = 0;
    state.entries.forEach(function(e){
      if(e.date.slice(0,7)!==ui.month) return;
      var d = +e.date.slice(8,10);
      if(e.planned){ pln[d] = (pln[d]||0)+e.amt; tp += e.amt; } else { act[d] = (act[d]||0)+e.amt; ta += e.amt; }
    });
    $("csum").innerHTML = '<div><div class="l">実績</div><div class="v num">'+yen(ta)+'</div></div>'+(tp>0?'<div class="p"><div class="l">使う予定</div><div class="v num"><span class="box">'+yen(tp)+'</span></div></div>':'');
    var rcd = {};
    state.entries.forEach(function(e){ if(e.receipt && !e.receipt.done && e.receipt.date && e.receipt.date.slice(0,7)===ui.month) rcd[+e.receipt.date.slice(8,10)] = true; });
    var g = "";
    for(var i=0;i<first;i++) g += '<div class="cell blank"></div>';
    for(var d=1; d<=days; d++){
      var ds = ui.month+"-"+pad2(d);
      g += '<button class="cell'+(ds===today()?' today':'')+(ds===ui.day?' sel':'')+'" data-d="'+ds+'"><span class="dn num">'+d+'</span>'
        + (act[d]?'<span class="ca num">'+fmtC(act[d])+'</span>':'')
        + (pln[d]?'<span class="cp num">'+fmtC(pln[d])+'</span>':'')+(rcd[d]?'<span class="rcm">'+RC_SVG+'</span>':'')+'</button>';
    }
    $("grid").innerHTML = g;
    Array.prototype.forEach.call($("grid").querySelectorAll("[data-d]"), function(b){
      b.onclick = function(){ ui.day = b.getAttribute("data-d"); renderCal(); };
    });
    if(ui.day.slice(0,7)!==ui.month) ui.day = ui.month+"-01";
    var dd = new Date(+ui.day.slice(0,4), +ui.day.slice(5,7)-1, +ui.day.slice(8,10));
    $("dayTitle").textContent = (dd.getMonth()+1)+"月"+dd.getDate()+"日（"+WD[dd.getDay()]+"）";
    var future = ui.day>today();
    $("addDay").textContent = future ? "＋ この日の予定" : "＋ この日の支出";
    $("addDay").classList.toggle("plan", future);
    $("addDay").onclick = function(){
      $("date").value = ui.day;
      ui.planned = future;
      setTab("input"); renderDisplay();
    };
    var es = state.entries.filter(function(e){ return e.date===ui.day; });
    var h = "";
    es.forEach(function(e){ h += rowHtml(e, false); });
    if(!es.length) h = '<div class="empty" style="padding:14px 0">'+(future?"この日の予定はまだありません。":"この日の記録はまだありません。")+'</div>';
    state.entries.forEach(function(e){
      if(e.receipt && !e.receipt.done && e.receipt.date===ui.day){
        var cc = catById(e.cat);
        h += '<div class="row due"><span class="c"><span class="rc" style="margin-left:0">'+RC_SVG+'</span> 引き換えの日<small>'+(e.receipt.who?esc(e.receipt.who)+' ・ ':'')+(cc?cc.emoji+' '+esc(cc.name)+' ':'')+yen(e.amt)+'</small></span><button class="ok" data-rdone="'+e.id+'">済み</button></div>';
      }
    });
    $("dayList").innerHTML = h;
    Array.prototype.forEach.call($("dayList").querySelectorAll("[data-rdone]"), function(b){
      b.onclick = function(){
        var id = b.getAttribute("data-rdone"), e = entryById(id);
        if(e && e.receipt){
          var r = b.getBoundingClientRect();
          e.receipt.done = true; save(); renderCal(); renderRemind();
          showUndo("引き換え済みにしました", function(){ e.receipt.done = false; save(); renderCal(); renderRemind(); }, r);
        }
      };
    });
    bindRows($("dayList"), renderCal, true);
  }

  /* ---------- 今月の楽しみ ---------- */
  var ILL = {
    shanghai: '<path d="M60 6 V20"/><circle cx="60" cy="28" r="7"/><path d="M56 35 L51 90 M64 35 L69 90 M60 35 V90"/><circle cx="60" cy="56" r="12"/><path d="M6 90 V64 H18 V90 M18 90 V74 H29 V90"/><path d="M92 90 V52 H98 V42 H102 V52 H108 V90 M108 90 V72 H116"/><path d="M2 90 H118"/>',
    tokyotower: '<path d="M60 4 V18"/><path d="M60 18 L38 92 M60 18 L82 92"/><path d="M52 36 H68 M48 50 H72 M43 68 H77 M39 80 H81"/><path d="M50 50 L72 68 M70 50 L48 68"/><path d="M28 92 H92"/>',
    eiffel: '<path d="M60 6 V20"/><path d="M60 20 C58 42 50 62 34 92 M60 20 C62 42 70 62 86 92"/><path d="M54 40 H66 M47 60 H73 M40 80 H80"/><path d="M34 92 H86 M50 80 C50 66 70 66 70 80"/>',
    torii: '<path d="M8 26 Q60 36 112 26"/><path d="M16 38 H104"/><path d="M30 36 V92 M90 36 V92"/><path d="M60 31 V38"/>',
    fuji: '<path d="M8 90 L44 28 H76 L112 90 Z"/><path d="M44 28 L51 38 L58 30 L65 40 L72 31 L76 28"/>',
    castle: '<path d="M12 92 V56 H34 V92 M34 62 H46 V92 M46 92 V38 L60 10 L74 38 V92 M74 62 H86 V92 M86 56 H108 V92 M12 92 H108"/><path d="M60 10 V2"/><path d="M53 92 V78 A7 7 0 0 1 67 78 V92"/>',
    ferris: '<circle cx="60" cy="42" r="32"/><circle cx="60" cy="42" r="4"/><path d="M60 10 V74 M28 42 H92 M37 19 L83 65 M83 19 L37 65"/><path d="M60 42 L40 92 M60 42 L80 92 M30 92 H90"/>',
    fireworks: '<circle cx="60" cy="40" r="3"/><path d="M60 10 V24 M60 56 V70 M30 40 H44 M76 40 H90 M39 19 L49 29 M71 51 L81 61 M81 19 L71 29 M49 51 L39 61"/><path d="M18 88 L28 78 M102 88 L92 78 M60 96 V84"/>',
    plane: '<path d="M60 6 C64 6 66 12 66 20 V40 L110 64 V72 L66 60 V80 L78 90 V96 L60 92 L42 96 V90 L54 80 V60 L10 72 V64 L54 40 V20 C54 12 56 6 60 6 Z"/>',
    suitcase: '<rect x="14" y="32" width="92" height="56" rx="8"/><path d="M44 32 V22 A4 4 0 0 1 48 18 H72 A4 4 0 0 1 76 22 V32"/><path d="M40 32 V88 M80 32 V88"/><path d="M28 88 V94 M92 88 V94"/>',
    globe: '<circle cx="60" cy="50" r="38"/><ellipse cx="60" cy="50" rx="16" ry="38"/><path d="M22 50 H98 M28 28 H92 M28 72 H92"/>',
    train: '<rect x="26" y="12" width="68" height="64" rx="12"/><path d="M26 50 H94"/><rect x="36" y="22" width="48" height="18" rx="3"/><circle cx="42" cy="62" r="4"/><circle cx="78" cy="62" r="4"/><path d="M38 76 L30 92 M82 76 L90 92 M24 92 H96"/>',
    onsen: '<path d="M16 62 C16 78 30 90 44 90 H76 C90 90 104 78 104 62 Z"/><path d="M38 20 Q30 30 38 40 Q46 50 38 58 M60 14 Q52 24 60 34 Q68 44 60 54 M82 20 Q74 30 82 40 Q90 50 82 58"/>',
    hotel: '<path d="M10 86 V30 M10 62 H110 V86 M10 74 H110"/><rect x="18" y="42" width="28" height="16" rx="6"/><path d="M52 62 V54 A8 8 0 0 1 60 46 H98 A12 12 0 0 1 110 58 V62"/>',
    coat: '<path d="M40 14 L60 28 L80 14 L100 24 L106 80 L90 82 L88 50 V94 H32 V50 L30 82 L14 80 L20 24 Z"/><path d="M40 14 L50 44 L60 28 M80 14 L70 44 L60 28"/><path d="M60 28 V94"/><circle cx="67" cy="58" r="1.8"/><circle cx="67" cy="72" r="1.8"/>',
    dress: '<path d="M48 8 L56 28 H64 L72 8"/><path d="M56 28 L44 52 L20 92 H100 L76 52 L64 28"/><path d="M44 52 H76"/>',
    shirt: '<path d="M42 14 L16 28 L27 48 L38 42 V90 H82 V42 L93 48 L104 28 L78 14 C74 24 46 24 42 14 Z"/>',
    shoe: '<path d="M10 72 V44 L30 38 C34 48 46 52 58 50 L88 58 C100 62 110 66 110 74 V80 H10 Z"/><path d="M10 72 H110"/><path d="M36 50 L42 57 M46 53 L52 60"/>',
    bag: '<rect x="20" y="38" width="80" height="52" rx="6"/><path d="M42 38 V30 A18 18 0 0 1 78 30 V38"/><path d="M20 54 H100"/>',
    hat: '<path d="M24 62 C24 36 40 22 60 22 C80 22 96 36 96 62"/><path d="M10 68 C30 78 90 78 110 68 C110 63 96 62 96 62 H24 C24 62 10 63 10 68 Z"/><path d="M24 54 H96"/>',
    glasses: '<circle cx="34" cy="58" r="20"/><circle cx="86" cy="58" r="20"/><path d="M54 56 Q60 50 66 56 M14 54 L6 38 M106 54 L114 38"/>',
    lipstick: '<rect x="40" y="54" width="40" height="38" rx="3"/><rect x="46" y="38" width="28" height="16"/><path d="M50 38 V22 L66 8 L70 38"/>',
    perfume: '<rect x="30" y="46" width="60" height="46" rx="8"/><rect x="50" y="30" width="20" height="16"/><path d="M52 30 V20 H68 V30"/><circle cx="60" cy="68" r="9"/><path d="M72 22 H88 M92 16 L100 12"/>',
    nail: '<rect x="38" y="48" width="44" height="44" rx="9"/><rect x="52" y="30" width="16" height="18" rx="2"/><path d="M56 30 V12 H64 V30"/>',
    scissors: '<circle cx="24" cy="32" r="13"/><circle cx="24" cy="68" r="13"/><path d="M35 39 L108 64 M35 61 L108 36"/><circle cx="70" cy="50" r="2.4"/>',
    laptop: '<rect x="24" y="20" width="72" height="48" rx="4"/><path d="M10 78 H110 L102 68 H18 Z"/>',
    phone: '<rect x="34" y="6" width="52" height="88" rx="9"/><path d="M52 15 H68 M55 83 H65"/>',
    headphones: '<path d="M22 66 V52 A38 38 0 0 1 98 52 V66"/><rect x="16" y="60" width="16" height="28" rx="7"/><rect x="88" y="60" width="16" height="28" rx="7"/>',
    watch: '<rect x="36" y="30" width="48" height="40" rx="11"/><path d="M45 30 L49 8 H71 L75 30 M45 70 L49 92 H71 L75 70"/><path d="M60 42 V51 L68 55"/>',
    camera: '<rect x="12" y="34" width="96" height="56" rx="8"/><circle cx="60" cy="62" r="18"/><path d="M40 34 L46 20 H74 L80 34"/>',
    game: '<path d="M34 32 H86 A24 24 0 0 1 110 56 V62 A12 12 0 0 1 88 68 L82 58 H38 L32 68 A12 12 0 0 1 10 62 V56 A24 24 0 0 1 34 32 Z"/><path d="M34 44 V58 M27 51 H41"/><circle cx="84" cy="45" r="3"/><circle cx="94" cy="53" r="3"/>',
    guitar: '<path d="M70 52 L104 14 M96 8 L110 22"/><path d="M62 40 C48 26 28 36 34 52 C18 54 12 74 28 84 C42 94 60 88 64 74 C78 72 84 58 72 50 Z"/><circle cx="46" cy="66" r="6"/>',
    piano: '<rect x="12" y="24" width="96" height="58" rx="4"/><path d="M36 24 V82 M60 24 V82 M84 24 V82"/><rect x="28" y="24" width="14" height="32" rx="1"/><rect x="52" y="24" width="14" height="32" rx="1"/><rect x="76" y="24" width="14" height="32" rx="1"/>',
    note: '<path d="M44 78 V22 L92 12 V66"/><circle cx="34" cy="78" r="10"/><circle cx="82" cy="66" r="10"/><path d="M44 36 L92 26"/>',
    live: '<rect x="46" y="10" width="28" height="48" rx="14"/><path d="M34 46 A26 26 0 0 0 86 46"/><path d="M60 72 V90 M44 90 H76"/><path d="M20 30 Q14 40 20 50 M100 30 Q106 40 100 50"/>',
    movie: '<rect x="12" y="42" width="96" height="48" rx="4"/><path d="M12 42 L22 18 H112 L102 42 M36 42 L46 18 M62 42 L72 18 M88 42 L98 18"/>',
    book: '<path d="M60 26 C46 16 28 16 14 22 V84 C28 78 46 78 60 88 C74 78 92 78 106 84 V22 C92 16 74 16 60 26 Z"/><path d="M60 26 V88"/>',
    cake: '<path d="M18 92 V58 H102 V92 Z"/><path d="M18 72 Q30 82 42 72 T66 72 T90 72 T102 72"/><path d="M60 58 V44"/><path d="M60 44 C55 38 60 30 60 30 C60 30 65 38 60 44 Z"/><path d="M30 58 V50 M90 58 V50"/>',
    gift: '<rect x="20" y="46" width="80" height="44" rx="3"/><rect x="14" y="32" width="92" height="16" rx="3"/><path d="M60 32 V90"/><path d="M60 32 C48 8 28 18 40 32 M60 32 C72 8 92 18 80 32"/>',
    tree: '<path d="M60 8 L82 40 H70 L90 66 H74 L98 90 H22 L46 66 H30 L50 40 H38 Z"/><path d="M60 90 V98"/>',
    bowl: '<path d="M12 54 H108 C108 78 90 92 60 92 C30 92 12 78 12 54 Z"/><path d="M40 92 H80"/><path d="M72 10 L52 50 M82 14 L64 50"/><path d="M28 54 Q38 44 48 54 T68 54"/>',
    cup: '<path d="M20 42 H82 V62 A24 24 0 0 1 58 86 H44 A24 24 0 0 1 20 62 Z"/><path d="M82 50 H90 A10 10 0 0 1 90 70 H80"/><path d="M40 12 Q34 20 40 28 M56 12 Q50 20 56 28"/>',
    beer: '<path d="M28 28 H80 V84 A6 6 0 0 1 74 90 H34 A6 6 0 0 1 28 84 Z"/><path d="M80 40 H92 A8 8 0 0 1 100 48 V66 A8 8 0 0 1 92 74 H80"/><path d="M28 28 C28 16 44 16 48 22 C52 14 68 14 70 22 C76 18 80 22 80 28"/><path d="M42 42 V76 M54 42 V76 M66 42 V76"/>',
    wine: '<path d="M38 10 H82 C82 40 76 56 60 56 C44 56 38 40 38 10 Z"/><path d="M60 56 V86 M42 92 H78"/><path d="M40 26 H80"/>',
    icecream: '<path d="M44 54 L60 96 L76 54"/><path d="M38 54 C38 28 82 28 82 54 Z"/><path d="M50 64 L64 80 M58 56 L68 68"/>',
    bread: '<path d="M18 52 C18 28 102 28 102 52 V88 H18 Z"/><path d="M40 40 L36 58 M60 38 L56 58 M80 40 L76 58"/>',
    beach: '<circle cx="86" cy="28" r="13"/><path d="M6 66 q10 -10 20 0 t20 0 t20 0 t20 0 t20 0 M6 84 q10 -10 20 0 t20 0 t20 0 t20 0 t20 0"/>',
    fish: '<path d="M14 50 C32 22 70 22 90 50 C70 78 32 78 14 50 Z"/><path d="M90 50 L108 32 V68 Z"/><circle cx="34" cy="46" r="3"/><path d="M52 32 C58 44 58 56 52 68"/>',
    mountain: '<path d="M4 90 L42 30 L64 64 L80 42 L116 90 Z"/><path d="M32 46 L42 54 L52 46"/>',
    tent: '<path d="M8 90 L60 16 L112 90 Z"/><path d="M60 16 V90 M60 50 L42 90 M60 50 L78 90"/>',
    snow: '<path d="M60 8 V92 M24 28 L96 72 M96 28 L24 72"/><path d="M52 14 L60 22 L68 14 M52 86 L60 78 L68 86 M18 38 L30 36 L28 24 M102 62 L90 64 L92 76 M102 38 L90 36 L92 24 M18 62 L30 64 L28 76"/>',
    flower: '<circle cx="60" cy="44" r="8"/><g><ellipse cx="60" cy="22" rx="9" ry="14"/><ellipse cx="60" cy="22" rx="9" ry="14" transform="rotate(72 60 44)"/><ellipse cx="60" cy="22" rx="9" ry="14" transform="rotate(144 60 44)"/><ellipse cx="60" cy="22" rx="9" ry="14" transform="rotate(216 60 44)"/><ellipse cx="60" cy="22" rx="9" ry="14" transform="rotate(288 60 44)"/></g><path d="M60 68 V96"/>',
    pumpkin: '<path d="M60 26 C20 20 6 50 14 70 C22 90 44 92 60 84 C76 92 98 90 106 70 C114 50 100 20 60 26 Z"/><path d="M60 26 C58 18 64 12 70 10"/><path d="M60 30 C50 50 50 70 60 84 M60 30 C70 50 70 70 60 84"/>',
    umbrella: '<path d="M10 56 C10 30 34 14 60 14 C86 14 110 30 110 56 Z"/><path d="M60 56 V82 A8 8 0 0 1 44 82"/><path d="M36 56 C36 44 48 22 60 14 M84 56 C84 44 72 22 60 14"/>',
    leaf: '<path d="M60 8 C80 30 100 40 96 64 C92 84 70 86 60 78 C50 86 28 84 24 64 C20 40 40 30 60 8 Z"/><path d="M60 78 V96 M60 78 V30 M60 56 L78 46 M60 56 L42 46"/>',
    cat: '<path d="M26 40 L30 12 L48 28 C56 26 64 26 72 28 L90 12 L94 40 C104 56 98 84 60 86 C22 84 16 56 26 40 Z"/><circle cx="46" cy="52" r="3"/><circle cx="74" cy="52" r="3"/><path d="M56 62 L60 66 L64 62 M60 66 V72 M44 66 L30 64 M44 72 L30 74 M76 66 L90 64 M76 72 L90 74"/>',
    dog: '<path d="M30 30 C14 30 12 56 20 66 C26 54 28 44 34 40 M90 30 C106 30 108 56 100 66 C94 54 92 44 86 40"/><path d="M34 40 C34 26 86 26 86 40 C90 70 78 88 60 88 C42 88 30 70 34 40 Z"/><circle cx="48" cy="52" r="3"/><circle cx="72" cy="52" r="3"/><path d="M54 64 H66 L60 72 Z M60 72 V78"/>',
    pet: '<ellipse cx="60" cy="70" rx="20" ry="16"/><ellipse cx="28" cy="48" rx="8" ry="11"/><ellipse cx="48" cy="30" rx="8" ry="11"/><ellipse cx="72" cy="30" rx="8" ry="11"/><ellipse cx="92" cy="48" rx="8" ry="11"/>',
    bicycle: '<circle cx="28" cy="68" r="20"/><circle cx="92" cy="68" r="20"/><path d="M28 68 L50 36 H78 L92 68 M50 36 L62 68 H28 M62 68 L78 36 M44 28 H56 M74 28 H86 L78 36"/>',
    car: '<path d="M10 72 V54 L24 52 L36 30 H84 L96 52 L110 54 V72 Z"/><path d="M36 30 L28 52 H92 L84 30 Z"/><circle cx="34" cy="74" r="9"/><circle cx="86" cy="74" r="9"/>',
    dumbbell: '<path d="M22 40 V60 M32 30 V70 M98 40 V60 M88 30 V70 M32 50 H88"/><path d="M12 46 V54 M108 46 V54"/>',
    palette: '<path d="M60 12 C28 12 10 36 14 58 C18 80 40 88 52 84 C62 80 56 70 64 66 C72 62 84 70 96 64 C108 58 108 12 60 12 Z"/><circle cx="38" cy="40" r="5"/><circle cx="58" cy="30" r="5"/><circle cx="80" cy="36" r="5"/><circle cx="34" cy="62" r="5"/>',
    pencil: '<path d="M18 82 L24 64 L76 12 L94 30 L42 82 Z"/><path d="M64 24 L82 42 M18 82 L36 76"/>',
    gradcap: '<path d="M60 16 L110 40 L60 64 L10 40 Z"/><path d="M30 52 V72 C30 84 90 84 90 72 V52"/><path d="M110 40 V66"/>',
    house: '<path d="M12 52 L60 12 L108 52"/><path d="M24 44 V92 H96 V44"/><rect x="49" y="62" width="22" height="30"/>',
    ring: '<circle cx="60" cy="66" r="24"/><path d="M46 38 L52 20 H68 L74 38 L60 48 Z"/>',
    gem: '<path d="M28 28 H92 L110 52 L60 94 L10 52 Z"/><path d="M10 52 H110 M42 28 L34 52 L60 94 M78 28 L86 52 L60 94 M42 28 L60 52 L78 28"/>',
    coin: '<circle cx="60" cy="50" r="38"/><path d="M60 26 V74 M46 38 H68 C76 38 76 52 68 52 H50 C42 52 42 66 50 66 H74"/>',
    heart: '<path d="M60 92 C18 62 12 40 26 26 C40 14 56 24 60 34 C64 24 80 14 94 26 C108 40 102 62 60 92 Z"/>',
    sparkle: '<path d="M58 12 L67 44 L99 53 L67 62 L58 94 L49 62 L17 53 L49 44 Z"/><path d="M98 12 l3 9 9 3 -9 3 -3 9 -3 -9 -9 -3 9 -3 Z"/><path d="M20 76 l2 6 6 2 -6 2 -2 6 -2 -6 -6 -2 6 -2 Z"/>',
    moon: '<path d="M78 12 A42 42 0 1 0 108 74 A34 34 0 0 1 78 12 Z"/><path d="M22 22 l3 8 8 3 -8 3 -3 8 -3 -8 -8 -3 8 -3 Z"/>',
    stars: '<circle cx="22" cy="68" r="3.5"/><circle cx="50" cy="40" r="3.5"/><circle cx="76" cy="58" r="3.5"/><circle cx="100" cy="24" r="3.5"/><circle cx="64" cy="86" r="3.5"/><path d="M22 68 L50 40 L76 58 L100 24 M76 58 L64 86"/>',
    balloon: '<path d="M60 8 C28 8 22 50 60 70 C98 50 92 8 60 8 Z"/><path d="M52 70 H68 L66 78 H54 Z M54 78 L50 92 H70 L66 78"/>'
  };
  /* [使うモチーフ（月ごとに入れ替わる）, キーワード]。上にあるほど優先 */
  var ILL_KEYS = [
    [["shanghai"], ["上海","中国","外灘","東方明珠"]],
    [["tokyotower"], ["東京タワー","東京"]],
    [["eiffel"], ["パリ","フランス","エッフェル"]],
    [["torii"], ["京都","神社","お寺","初詣","奈良"]],
    [["fuji"], ["富士山","富士"]],
    [["castle"], ["ディズニー","お城"]],
    [["ferris"], ["遊園地","USJ","ユニバ","テーマパーク","観覧車"]],
    [["plane"], ["飛行機","フライト","空港"]],
    [["suitcase"], ["スーツケース","キャリーケース"]],
    [["train"], ["新幹線","電車","特急","鉄道","列車"]],
    [["onsen"], ["温泉","銭湯","サウナ"]],
    [["hotel"], ["ホテル","旅館","宿泊"]],
    [["plane","suitcase","globe"], ["旅","海外","パスポート","trip","遠出"]],
    [["glasses"], ["メガネ","眼鏡","めがね"]],
    [["coat"], ["コート","アウター","ジャケット","ダウン"]],
    [["dress"], ["ワンピ","ドレス","スカート"]],
    [["shirt"], ["シャツ","トップス","ブラウス","ニット","セーター"]],
    [["shoe"], ["靴","スニーカー","ブーツ","シューズ"]],
    [["bag"], ["バッグ","鞄"]],
    [["hat"], ["帽子","キャップ"]],
    [["coat","dress","shirt","shoe","bag"], ["服","ファッション","コーデ","アパレル","買い物","ショッピング"]],
    [["lipstick"], ["コスメ","口紅","リップ","メイク","化粧","チーク","ファンデ"]],
    [["perfume"], ["香水","フレグランス"]],
    [["nail"], ["ネイル"]],
    [["scissors"], ["美容院","髪","ヘア","カット","パーマ"]],
    [["laptop"], ["パソコン","PC","タブレット","iPad"]],
    [["phone"], ["スマホ","iPhone","携帯"]],
    [["headphones"], ["イヤホン","ヘッドホン","AirPods"]],
    [["watch"], ["時計"]],
    [["camera"], ["写真","カメラ","撮影"]],
    [["game"], ["ゲーム","switch","Switch","PS5"]],
    [["guitar"], ["ギター","ベース","バンド"]],
    [["piano"], ["ピアノ","キーボード"]],
    [["live"], ["ライブ","推し","コンサート","フェス","カラオケ"]],
    [["note"], ["音楽","歌","CD","レコード"]],
    [["movie"], ["映画","ドラマ","アニメ","舞台"]],
    [["tree"], ["クリスマス"]],
    [["cake"], ["誕生日","ケーキ","スイーツ"]],
    [["gift"], ["プレゼント","ギフト","お祝い","記念日"]],
    [["fireworks"], ["花火","夏祭り","祭り"]],
    [["pumpkin"], ["ハロウィン"]],
    [["flower"], ["花","桜","お花見","ガーデン","庭"]],
    [["bowl"], ["ラーメン","うどん","そば","麺","ご飯","ごはん","ランチ","ディナー","定食","食べ","グルメ","焼肉","寿司"]],
    [["cup"], ["カフェ","コーヒー","お茶","紅茶"]],
    [["beer","wine"], ["飲み","お酒","ビール","ワイン","居酒屋","乾杯"]],
    [["icecream"], ["アイス","かき氷"]],
    [["bread"], ["ベーカリー","パン屋","食パン"]],
    [["fish"], ["水族館","魚","釣り"]],
    [["beach"], ["海","ビーチ","プール","サーフ"]],
    [["mountain"], ["山","登山","ハイキング","トレッキング"]],
    [["tent"], ["キャンプ","アウトドア","BBQ","バーベキュー"]],
    [["snow"], ["スキー","スノボ","雪"]],
    [["umbrella"], ["傘","梅雨"]],
    [["cat"], ["猫","ねこ"]],
    [["dog"], ["犬","いぬ","散歩"]],
    [["pet"], ["ペット","動物"]],
    [["bicycle"], ["自転車","サイクリング"]],
    [["car"], ["車","ドライブ","免許"]],
    [["dumbbell"], ["ジム","筋トレ","運動","トレーニング","ヨガ"]],
    [["palette"], ["絵","イラスト","創作","画材","デザイン","名刺"]],
    [["pencil"], ["勉強","資格","文房具","ペン","ノート"]],
    [["gradcap"], ["卒業","進学","学校","入学","留学"]],
    [["house"], ["引っ越し","引越","新居","インテリア","家具","部屋"]],
    [["ring"], ["結婚","指輪","婚約"]],
    [["gem"], ["ジュエリー","ネックレス","ピアス","アクセ","宝石"]],
    [["coin"], ["貯金","貯蓄","投資","お金"]],
    [["heart"], ["デート","彼氏","彼女","恋"]],
    [["book"], ["読書","漫画","マンガ","小説","本屋","本を","本が"]]
  ];
  /* 当てはまる言葉がないときは、月ごとの季節のモチーフ */
  var ILL_FALL = ["fuji","heart","gradcap","flower","balloon","umbrella","fireworks","beach","moon","pumpkin","leaf","tree"];
  var WISH_FALL = ["sparkle","heart","stars","balloon","flower","gem","moon","leaf","umbrella","gift","note","cup"];
  function hashStr(s){ var h = 0; for(var i=0;i<s.length;i++) h = (h*31+s.charCodeAt(i))>>>0; return h; }
  function illKeyFor(text, idx, fallback){
    if(text){
      for(var i=0;i<ILL_KEYS.length;i++){
        var kw = ILL_KEYS[i][1];
        for(var j=0;j<kw.length;j++){
          if(text.indexOf(kw[j])>=0){ var ms = ILL_KEYS[i][0]; return ms[idx % ms.length]; }
        }
      }
    }
    return fallback[idx % fallback.length];
  }
  function illSvgByKey(key, cls){
    return '<svg class="'+(cls||"ill")+'" viewBox="0 0 120 100" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">'+ILL[key]+'</svg>';
  }
  function illFor(wish, month){
    var mi = +month.slice(5,7)-1;
    return illSvgByKey(illKeyFor(wish, mi, ILL_FALL), "ill");
  }
var FX = '<span class="fx" aria-hidden="true"><svg class="cloud c1" viewBox="0 0 48 26" fill="currentColor"><path d="M12 24 A9 9 0 0 1 13 6 A12 12 0 0 1 36 8 A9 9 0 0 1 38 24 Z"/></svg><svg class="cloud c2" viewBox="0 0 48 26" fill="currentColor"><path d="M12 24 A9 9 0 0 1 13 6 A12 12 0 0 1 36 8 A9 9 0 0 1 38 24 Z"/></svg><svg class="tw t1" viewBox="0 0 12 12" fill="currentColor"><path d="M6 0 L7.4 4.6 L12 6 L7.4 7.4 L6 12 L4.6 7.4 L0 6 L4.6 4.6 Z"/></svg><svg class="tw t2" viewBox="0 0 12 12" fill="currentColor"><path d="M6 0 L7.4 4.6 L12 6 L7.4 7.4 L6 12 L4.6 7.4 L0 6 L4.6 4.6 Z"/></svg><svg class="tw t3" viewBox="0 0 12 12" fill="currentColor"><path d="M6 0 L7.4 4.6 L12 6 L7.4 7.4 L6 12 L4.6 7.4 L0 6 L4.6 4.6 Z"/></svg></span>';
  function wishCat(wish){
    var hit = null;
    state.cats.forEach(function(c){ if(c.name && wish && (wish.indexOf(c.name)>=0 || c.name.indexOf(wish)>=0)) hit = c; });
    return hit;
  }
  function renderWish(){
    var now = new Date(), m = ym(now), w = (state.wishes[m]||"").trim(), box = $("wishCard");
    var left = new Date(now.getFullYear(), now.getMonth()+1, 0).getDate()-now.getDate();
    var h;
    if(ui.wishEdit || !w){
      h = '<div class="wish-card ed">'+illFor(w, m)+FX+'<div class="wc-l">'+(w?'今月の楽しみを変える':'今月のワクワクを決めよう')+'</div><div class="wc-in"><input id="wishTop" maxlength="30" placeholder="例：上海への旅行" value="'+esc(w)+'" aria-label="今月の楽しみ"><button id="wishSave">'+(w?'保存':'決める')+'</button></div></div>';
    } else {
      var hit = wishCat(w), sub = "";
      if(hit){
        var spent = 0;
        state.entries.forEach(function(e){ if(e.cat===hit.id && e.date.slice(0,7)===m && !e.planned) spent += e.amt; });
        var goal = goalOf(hit, m);
        if(goal>0){
          sub += '<div class="wc-s num">'+hit.emoji+' '+esc(hit.name)+'　'+yen(spent)+' / '+yen(goal)+'</div><div class="wc-bar"><i style="width:'+Math.min(100, spent/goal*100)+'%"></i></div>';
        } else {
          sub += '<div class="wc-s num">'+hit.emoji+' '+esc(hit.name)+'　'+yen(spent)+'</div>';
        }
      }
      sub += '<div class="wc-s dim">'+(left>0 ? '今月はあと'+left+'日' : '今日で月末！')+'</div>';
      h = '<button class="wish-card" id="wishShow" aria-label="今月の楽しみを変える">'+illFor(w, m)+FX+'<div class="wc-l">今月の楽しみ</div><div class="wc-t">'+esc(w)+'</div>'+sub+'</button>';
    }
    box.innerHTML = h;
    var inp = $("wishTop");
    if(inp){
      var saveW = function(){ state.wishes[m] = inp.value.trim(); ui.wishEdit = false; save(); renderWish(); };
      $("wishSave").onclick = saveW;
      inp.onkeydown = function(e){ if(e.key==="Enter"){ e.preventDefault(); saveW(); } };
      if(ui.wishEdit) inp.focus();
    }
    var show = $("wishShow");
    if(show) show.onclick = function(){ ui.wishEdit = true; renderWish(); };
  }

  /* ---------- 親への報告メッセージ ---------- */
  function msgTarget(){
    var n = new Date(), last = new Date(n.getFullYear(), n.getMonth()+1, 0).getDate();
    if(n.getDate() >= last-2) return ym(n);
    if(n.getDate() <= 3) return ym(new Date(n.getFullYear(), n.getMonth()-1, 1));
    return null;
  }
  function renderBanners(){
    var t = msgTarget(), html = "";
    if(t && !state.sent[t]){
      var has = state.entries.some(function(e){ return e.date.slice(0,7)===t && !e.planned; });
      if(has) html = '<div class="banner"><span>そろそろ集計できました。メッセージを送りましょう。</span><button data-m="'+t+'">文章をつくる</button></div>';
    }
    ["banner1","banner2"].forEach(function(id){
      $(id).innerHTML = html;
      var b = $(id).querySelector("button");
      if(b) b.onclick = function(){ openMsg(b.getAttribute("data-m")); };
    });
  }
  function prevMonthOf(month){ var p = month.split("-"); return ym(new Date(+p[0], +p[1]-2, 1)); }
  function summary(month){
    var cats = state.cats.map(function(c){
      var spent = 0, planned = 0, sums = {}, order = [], memoTotal = 0;
      state.entries.forEach(function(e){
        if(e.cat!==c.id || e.date.slice(0,7)!==month) return;
        if(e.planned){ planned += e.amt; return; }
        spent += e.amt;
        if(e.memo){
          var k = "m"+e.memo;
          if(sums[k]===undefined){ sums[k] = 0; order.push(e.memo); }
          sums[k] += e.amt; memoTotal += e.amt;
        }
      });
      return { id:c.id, name:c.name, emoji:c.emoji, spent:spent, planned:planned, goal:goalOf(c, month),
               memos:order.map(function(t){ return { t:t, v:sums["m"+t] }; }), rest:spent-memoTotal };
    });
    return {
      label: (+month.slice(5,7))+"月", cats: cats,
      total: cats.reduce(function(s,c){ return s+c.spent; },0),
      planned: cats.reduce(function(s,c){ return s+c.planned; },0)
    };
  }
  function sgn(n){ return (n>=0?"＋":"－")+yen(Math.abs(n)); }
  var STYLES = ["箇条書き","1行ずつ","罫線つき","やさしい文章","ポップ"];
  function goalPart(c){
    if(!(c.goal>0)) return "";
    var d = c.goal-c.spent;
    return d>=0 ? "（目標 "+yen(c.goal)+"・あと"+yen(d)+"）" : "（目標 "+yen(c.goal)+"・"+yen(-d)+"オーバー）";
  }
  function reportData(month, o){
    var sm = summary(month);
    var d = { label:sm.label, total:sm.total, planned:sm.planned };
    d.used = sm.cats.filter(function(c){ return c.spent>0; }).sort(function(a,b){ return b.spent-a.spent; });
    var wg = sm.cats.filter(function(c){ return c.goal>0; });
    d.hasGoal = wg.length>0;
    d.G = wg.reduce(function(s,c){ return s+c.goal; },0);
    d.dd = d.G - wg.reduce(function(s,c){ return s+c.spent; },0);
    d.hasPrev = false; d.changes = [];
    if(o.prev){
      var ps = summary(prevMonthOf(month));
      if(ps.total>0){
        d.hasPrev = true; d.prevLabel = ps.label; d.prevTotal = ps.total; d.diff = sm.total-ps.total;
        d.changes = sm.cats.map(function(c){
          var p = null; ps.cats.forEach(function(x){ if(x.id===c.id) p = x; });
          return { c:c, d:c.spent-(p?p.spent:0) };
        }).filter(function(x){ return x.d!==0; }).sort(function(a,b){ return Math.abs(b.d)-Math.abs(a.d); }).slice(0,5);
      }
    }
    d.wish = o.wish ? (state.wishes[month]||"").trim() : "";
    d.note = (state.notes[month]||"").trim();
    return d;
  }
  function reportText(month, o){
    var d = reportData(month, o), L = [], st = o.style||0, lab = d.label;
    var memoLines = function(c, pre){
      if(!o.memo || !c.memos.length) return;
      c.memos.forEach(function(m){ L.push(pre+m.t+"："+yen(m.v)); });
      if(c.rest>0) L.push(pre+"その他："+yen(c.rest));
    };
    var diffWord = d.hasPrev ? (d.diff===0 ? "変わらず" : sgn(d.diff)) : "";
    if(st===0){
      L.push("【"+lab+"分の支出まとめ💰】","");
      if(!d.used.length) L.push("（まだ記録がありません）");
      d.used.forEach(function(c){
        L.push(c.emoji+" "+c.name);
        L.push("　┗"+yen(c.spent)+goalPart(c));
        memoLines(c, "　　・");
      });
      L.push("","▶ 合計："+yen(d.total));
      if(d.hasGoal) L.push("▶ 目標との差："+(d.dd>=0 ? yen(d.dd)+" 少なく済みました" : yen(-d.dd)+" オーバーしました")+"（目標 "+yen(d.G)+"）");
      if(d.hasPrev){
        L.push("","▶ 先月（"+d.prevLabel+"）との比較","　"+yen(d.prevTotal)+" → "+yen(d.total)+"（"+diffWord+"）");
        d.changes.forEach(function(x){ L.push("　┗ "+x.c.emoji+" "+x.c.name+" "+sgn(x.d)); });
      }
      if(d.planned>0) L.push("","※"+lab+"に使う予定："+yen(d.planned));
      if(d.wish) L.push("","✨今月の楽しみ：「"+d.wish+"」");
      if(d.note) L.push("","▶ 今月の感想",d.note);
    } else if(st===1){
      L.push("【"+lab+"の支出】");
      if(!d.used.length) L.push("（まだ記録がありません）");
      d.used.forEach(function(c){
        var s = c.emoji+" "+c.name+" "+yen(c.spent)+goalPart(c);
        if(o.memo && c.memos.length) s += " ← "+c.memos.map(function(m){ return m.t+yen(m.v); }).join("・")+(c.rest>0?"・その他"+yen(c.rest):"");
        L.push(s);
      });
      L.push("合計 "+yen(d.total)+(d.hasGoal ? " ／ 目標より "+(d.dd>=0 ? yen(d.dd)+" 少ない" : yen(-d.dd)+" 多い") : ""));
      if(d.hasPrev){
        L.push("先月比 "+yen(d.prevTotal)+"→"+yen(d.total)+"（"+diffWord+"）");
        if(d.changes.length) L.push("　"+d.changes.map(function(x){ return x.c.emoji+x.c.name+" "+sgn(x.d); }).join(" ／ "));
      }
      if(d.planned>0) L.push(lab+"の予定 "+yen(d.planned));
      if(d.wish) L.push("楽しみ：「"+d.wish+"」");
      if(d.note) L.push("感想："+d.note);
    } else if(st===2){
      var bar = "━━━━━━━━━━━━";
      L.push(bar,"💰 "+lab+"分の支出まとめ",bar,"");
      if(!d.used.length) L.push("（まだ記録がありません）");
      d.used.forEach(function(c){
        L.push(c.emoji+" "+c.name+"　"+yen(c.spent));
        if(c.goal>0){ var x = c.goal-c.spent; L.push("　　目標 "+yen(c.goal)+" ／ "+(x>=0 ? "あと"+yen(x) : yen(-x)+"オーバー")); }
        memoLines(c, "　　・");
      });
      L.push("","────────────","合計　"+yen(d.total));
      if(d.hasGoal) L.push("目標との差　"+(d.dd>=0 ? yen(d.dd)+" 少なく済んだ" : yen(-d.dd)+" オーバー")+"（目標 "+yen(d.G)+"）");
      if(d.hasPrev){
        L.push("","先月（"+d.prevLabel+"）との比較","　"+yen(d.prevTotal)+" → "+yen(d.total)+"（"+diffWord+"）");
        d.changes.forEach(function(x){ L.push("　"+x.c.emoji+" "+x.c.name+" "+sgn(x.d)); });
      }
      if(d.planned>0) L.push("","※"+lab+"に使う予定　"+yen(d.planned));
      if(d.wish) L.push("","✨ 今月の楽しみ「"+d.wish+"」");
      if(d.note) L.push("","────────────","💬 今月の感想",d.note);
    } else if(st===3){
      L.push(lab+"分の支出をご報告します。","");
      var s1 = "今月は合計"+yen(d.total)+"使いました。";
      if(d.hasGoal) s1 += "目標を決めていた項目では、目標より"+yen(Math.abs(d.dd))+(d.dd>=0 ? "少なく済みました。" : "オーバーしました。");
      L.push(s1);
      if(d.used.length) L.push("使ったのは、"+d.used.slice(0,5).map(function(c){ return c.emoji+c.name+"（"+yen(c.spent)+"）"; }).join("、")+"です。");
      var overs = d.used.filter(function(c){ return c.goal>0 && c.spent>c.goal; });
      if(overs.length) L.push("目標をオーバーしたのは、"+overs.map(function(c){ return c.emoji+c.name+"（"+yen(c.spent-c.goal)+"）"; }).join("、")+"でした。");
      if(o.memo){
        var ms2 = []; d.used.forEach(function(c){ c.memos.forEach(function(m){ ms2.push(m.t+"（"+yen(m.v)+"）"); }); });
        if(ms2.length) L.push("細かい内訳は、"+ms2.slice(0,8).join("、")+"などです。");
      }
      if(d.hasPrev) L.push("先月（"+d.prevLabel+"）は"+yen(d.prevTotal)+"だったので、"+(d.diff>0 ? yen(d.diff)+"増えました。" : d.diff<0 ? yen(-d.diff)+"減りました。" : "同じでした。"));
      if(d.planned>0) L.push(lab+"に使う予定が"+yen(d.planned)+"あります。");
      if(d.wish) L.push("今月の楽しみは「"+d.wish+"」でした。");
      if(d.note) L.push("","【感想】",d.note);
    } else {
      L.push("✨💸 "+lab+"の家計レポ 💸✨","");
      if(!d.used.length) L.push("（まだ記録がありません）");
      d.used.forEach(function(c){
        L.push(c.emoji+" "+c.name+" → "+yen(c.spent)+" "+(c.goal>0 ? (c.spent>c.goal ? "⚠️" : "✅") : "🌟"));
        if(c.goal>0){ var x = c.goal-c.spent; L.push("　　目標 "+yen(c.goal)+" ／ "+(x>=0 ? "あと"+yen(x)+"🙌" : yen(-x)+"オーバー😢")); }
        memoLines(c, "　　🔸");
      });
      L.push("","🧮 合計 "+yen(d.total));
      if(d.hasGoal) L.push("🎯 目標との差："+(d.dd>=0 ? yen(d.dd)+" 少なく済んだ！" : yen(-d.dd)+" オーバー…"));
      if(d.hasPrev){
        L.push("",(d.diff>0 ? "📈" : "📉")+" 先月（"+d.prevLabel+"）："+yen(d.prevTotal)+" → "+yen(d.total)+"（"+diffWord+"）");
        d.changes.forEach(function(x){ L.push("　　"+x.c.emoji+x.c.name+" "+sgn(x.d)); });
      }
      if(d.planned>0) L.push("","🗓 "+lab+"に使う予定 "+yen(d.planned));
      if(d.wish) L.push("","🌟 今月の楽しみ「"+d.wish+"」");
      if(d.note) L.push("","📝 今月の感想",d.note);
    }
    return L.join("\n");
  }
  function md(ds){ return (+ds.slice(5,7))+"/"+(+ds.slice(8,10)); }
  function requestData(month, o){
    var cats = state.cats.map(function(c){ return { emoji:c.emoji, name:c.name, goal:goalOf(c, month), text:goalText(c, month) }; }).filter(function(x){ return x.goal>0; });
    var evs = o.events ? state.events.filter(function(e){ return e.date.slice(0,7)===month; }).sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); }) : [];
    var sum = cats.reduce(function(s,x){ return s+x.goal; },0), evSum = evs.reduce(function(s,e){ return s+evAmt(e); },0);
    return { label:(+month.slice(5,7))+"月", cats:cats, evs:evs, sum:sum, evSum:evSum, total:sum+evSum, note:(state.notes["req:"+month]||"").trim() };
  }
  function requestText(month, o){
    var d = requestData(month, o), L = [], st = o.style||0, lab = d.label;
    if(!d.cats.length && !d.evs.length) return "（"+lab+"の目標がまだ設定されていません。項目の編集で、"+lab+"の目標を入れてください）";
    var calc = function(x){ return (o.calc && x.text) ? "（"+x.text+"）" : ""; };
    var evl = function(e){ return e.name+"（"+md(e.date)+"）"; };
    if(st===0){
      L.push("【"+lab+"分 いただきたい金額💰】","");
      d.cats.forEach(function(x){ L.push(x.emoji+" "+x.name); L.push("　┗"+yen(x.goal)+calc(x)); });
      if(d.cats.length) L.push("","▶ 項目の合計："+yen(d.sum));
      if(d.evs.length){
        L.push("","【特大イベント】");
        d.evs.forEach(function(e){ L.push("・"+evl(e)+"："+evFormula(e)); });
        L.push("▶ イベントの合計："+yen(d.evSum));
      }
      L.push("","▶ 合計："+yen(d.total));
      if(d.note) L.push("","▶ ひとこと",d.note);
    } else if(st===1){
      L.push("【"+lab+"分 いただきたい金額】");
      d.cats.forEach(function(x){ L.push(x.emoji+" "+x.name+" "+yen(x.goal)+calc(x)); });
      if(d.cats.length) L.push("項目の合計 "+yen(d.sum));
      d.evs.forEach(function(e){ L.push("◆特大 "+evl(e)+" "+evFormula(e)); });
      L.push("合計 "+yen(d.total));
      if(d.note) L.push("ひとこと："+d.note);
    } else if(st===2){
      var bar = "━━━━━━━━━━━━";
      L.push(bar,"💰 "+lab+"分 いただきたい金額",bar,"");
      d.cats.forEach(function(x){ L.push(x.emoji+" "+x.name+"　"+yen(x.goal)); if(o.calc && x.text) L.push("　　"+x.text); });
      if(d.cats.length) L.push("","────────────","項目の合計　"+yen(d.sum));
      if(d.evs.length){
        L.push("","特大イベント");
        d.evs.forEach(function(e){ L.push("　"+evl(e)+"　"+evFormula(e)); });
        L.push("イベントの合計　"+yen(d.evSum));
      }
      L.push("","────────────","合計　"+yen(d.total));
      if(d.note) L.push("","💬 ひとこと",d.note);
    } else if(st===3){
      L.push(lab+"分に必要な金額をお願いしたく、ご連絡しました。","");
      if(d.cats.length) L.push("項目ごとの金額は、"+d.cats.map(function(x){ return x.emoji+x.name+"（"+yen(x.goal)+"）"; }).join("、")+"です。項目の合計は"+yen(d.sum)+"です。");
      if(d.evs.length) L.push("特大イベントとして、"+d.evs.map(function(e){ return e.name+"（"+md(e.date)+"・"+evFormula(e)+"）"; }).join("、")+"があります。");
      L.push("合計で"+yen(d.total)+"をお願いしたいです。よろしくお願いします。");
      if(d.note) L.push("","【ひとこと】",d.note);
    } else {
      L.push("🙏✨ "+lab+"分 お願いリスト ✨🙏","");
      d.cats.forEach(function(x){ L.push(x.emoji+" "+x.name+" → "+yen(x.goal)); if(o.calc && x.text) L.push("　　🔸"+x.text); });
      if(d.cats.length) L.push("","🧮 項目の合計 "+yen(d.sum));
      if(d.evs.length){
        L.push("","🎉 特大イベント");
        d.evs.forEach(function(e){ L.push("　　🔸"+evl(e)+" "+evFormula(e)); });
      }
      L.push("","💰 合計 "+yen(d.total));
      if(d.note) L.push("","📝 ひとこと",d.note);
    }
    return L.join("\n");
  }
  var ms = { month:null, mode:"report", prev:true, memo:true, wish:true, calc:true, events:true, style:0, edited:false };
  var OPTS = { report:[["prev","先月との比較"],["memo","メモの内訳"],["wish","今月の楽しみ"]], req:[["calc","計算の内訳"],["events","特大イベント"]] };
  function noteKey(){ return (ms.mode==="req" ? "req:" : "")+ms.month; }
  function syncLine(){ $("mLine").href = "https://line.me/R/share?text="+encodeURIComponent($("mText").value); }
  function styleLabel(){ $("mStyleName").textContent = (ms.style+1)+"/"+STYLES.length+" "+STYLES[ms.style]; }
  function refreshReport(){
    ms.edited = false;
    $("mText").value = ms.mode==="req" ? requestText(ms.month, ms) : reportText(ms.month, ms);
    syncLine(); styleLabel();
  }
  function renderOpts(){
    var h = "";
    OPTS[ms.mode].forEach(function(o){ h += '<button class="opt" data-k="'+o[0]+'" aria-pressed="'+ms[o[0]]+'">'+o[1]+'</button>'; });
    $("mOpts").innerHTML = h;
    Array.prototype.forEach.call($("mOpts").querySelectorAll(".opt"), function(b){
      b.onclick = function(){ var k = b.getAttribute("data-k"); ms[k] = !ms[k]; b.setAttribute("aria-pressed", ms[k]); refreshReport(); };
    });
  }
  function evAmt(e){
    if(e.items && e.items.length) return e.items.reduce(function(s,id){ var a = entryById(id); return s+(a?a.amt:0); },0);
    return e.amt||0;
  }
  function evFormula(e){
    if(e.items && e.items.length){
      var amts = [];
      e.items.forEach(function(id){ var a = entryById(id); if(a) amts.push(a.amt); });
      if(amts.length>1) return amts.map(function(n){ return n.toLocaleString("ja-JP"); }).join("＋")+"＝"+yen(amts.reduce(function(s,n){ return s+n; },0));
      return yen(evAmt(e));
    }
    return yen(e.amt||0);
  }
  function entryLabel(a){ var c = catById(a.cat); return a.memo || (c ? c.name : ""); }
  var evSel = {}, evMode = "pick", evDateTouched = false, evEditId = null;
  function monthEntryList(){
    return state.entries.filter(function(e){ return e.date.slice(0,7)===ms.month; }).sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); });
  }
  function renderEvents(){
    var list = state.events.filter(function(e){ return e.date.slice(0,7)===ms.month; }).sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); });
    var h = "";
    list.forEach(function(e){
      var sub = "";
      if(e.items && e.items.length){
        var labs = [];
        e.items.forEach(function(id){ var a = entryById(id); if(a) labs.push(esc(entryLabel(a))); });
        sub = '<small>'+labs.join("・")+'</small>';
      }
      h += '<div class="evrow'+(evEditId===e.id?' editing':'')+'" data-id="'+e.id+'"><span class="n">'+esc(e.name)+sub+'</span><span class="d num">'+md(e.date)+'</span><span class="num">'+yen(evAmt(e))+'</span><button data-ev="'+e.id+'" aria-label="削除">×</button></div>';
    });
    if(!list.length) h = '<div class="empty" style="padding:2px 0 6px">この月の特大イベントはまだありません。</div>';
    else h += '<div class="evhint">イベントをタップすると、内容を編集できます。</div>';
    $("evList").innerHTML = h;
    Array.prototype.forEach.call($("evList").querySelectorAll("[data-ev]"), function(b){
      b.onclick = function(){
        var id = b.getAttribute("data-ev");
        state.events = state.events.filter(function(e){ return e.id!==id; });
        if(evEditId===id) resetEvForm();
        save(); renderEvents(); refreshReport();
      };
    });
    Array.prototype.forEach.call($("evList").querySelectorAll(".evrow"), function(r){
      r.onclick = function(ev){ if(ev.target.closest("button")) return; startEditEv(r.getAttribute("data-id")); };
    });
  }
  function evFormLabels(){
    var ed = !!evEditId;
    $("evTitle").textContent = ed ? "イベントを編集する" : "＋ イベントを追加する";
    $("evAdd").textContent = ed ? "変更を保存" : "イベントに追加";
    $("evCancel").hidden = !ed;
  }
  function startEditEv(id){
    var e = null;
    state.events.forEach(function(x){ if(x.id===id) e = x; });
    if(!e) return;
    evEditId = id; evSel = {}; evDateTouched = true;
    $("evName").value = e.name; $("evDate").value = e.date; $("evAmt").value = "";
    if(e.items && e.items.length){ e.items.forEach(function(k){ evSel[k] = true; }); setEvMode("pick"); }
    else { $("evAmt").value = e.amt || ""; setEvMode("amt"); }
    evFormLabels(); renderEvents(); renderPicks();
    $("evForm").scrollIntoView({ block:"nearest" });
  }
  function selIds(){ return Object.keys(evSel).filter(function(k){ return evSel[k]; }); }
  function updateEvSum(){
    var picked = selIds().map(entryById).filter(Boolean).sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); });
    if(!picked.length){ $("evSum").textContent = "含める項目をタップして選んでください（いくつでも）"; return; }
    var tot = picked.reduce(function(s,a){ return s+a.amt; },0);
    $("evSum").textContent = picked.length>1 ? picked.map(function(a){ return a.amt.toLocaleString("ja-JP"); }).join("＋")+"＝"+yen(tot) : yen(tot);
    if(!evDateTouched) $("evDate").value = picked[0].date;
  }
  function renderPicks(){
    var list = monthEntryList(), h = "";
    list.forEach(function(a){
      var c = catById(a.cat);
      h += '<button class="evp" type="button" data-id="'+a.id+'" aria-pressed="'+(!!evSel[a.id])+'"><span class="ck"></span><span class="n">'+(c?c.emoji+" ":"")+esc(entryLabel(a))+(a.planned?'<span class="tag">予定</span>':'')+'</span><span class="d num">'+md(a.date)+'</span><span class="num">'+yen(a.amt)+'</span></button>';
    });
    if(!list.length) h = '<div class="empty" style="padding:12px">この月のカレンダーには、まだ記録や予定がありません。</div>';
    $("evPicks").innerHTML = h;
    Array.prototype.forEach.call($("evPicks").querySelectorAll(".evp"), function(b){
      b.onclick = function(){ var id = b.getAttribute("data-id"); evSel[id] = !evSel[id]; renderPicks(); };
    });
    updateEvSum();
  }
  function setEvMode(m){
    evMode = m;
    Array.prototype.forEach.call($("evMode").querySelectorAll("button"), function(b){ b.setAttribute("aria-pressed", b.getAttribute("data-m")===m); });
    $("evPickWrap").hidden = m!=="pick"; $("evAmtWrap").hidden = m!=="amt";
  }
  function resetEvForm(){
    evSel = {}; evDateTouched = false; evEditId = null;
    $("evName").value = ""; $("evAmt").value = ""; $("evDate").value = ms.month+"-01";
    setEvMode(monthEntryList().length ? "pick" : "amt");
    evFormLabels();
    renderPicks();
  }
  Array.prototype.forEach.call($("evMode").querySelectorAll("button"), function(b){ b.onclick = function(){ setEvMode(b.getAttribute("data-m")); }; });
  $("evDate").onchange = function(){ evDateTouched = true; };
  function renderMsgHead(){
    var req = ms.mode==="req";
    $("mTitle").textContent = (+ms.month.slice(5,7))+"月の"+(req ? "お願い" : "報告");
    $("mMonth").textContent = mLabel(ms.month);
    $("mModeRep").setAttribute("aria-pressed", !req);
    $("mModeReq").setAttribute("aria-pressed", req);
    $("mReq").hidden = !req;
    $("mNoteL").textContent = req ? "ひとこと（お願いの理由など）" : "今月の感想（自分の言葉で）";
    $("mNote").placeholder = req ? "例：12月は特急券の回数が増えそうです。よろしくお願いします。" : "例：お金を使い過ぎてしまいました。原因はお菓子の買い過ぎです。気をつけます。";
    $("mNote").value = state.notes[noteKey()] || "";
    renderOpts();
    if(req){ renderEvents(); resetEvForm(); }
    refreshReport();
  }
  function openMsg(month, mode){
    ms.month = month;
    ms.mode = mode || (month > ym(new Date()) ? "req" : "report");
    ms.style = state.settings.style || 0;
    renderMsgHead();
    $("scrim").classList.add("on"); $("msheet").classList.add("on");
  }
  function setMode(m){
    var cur = ym(new Date());
    ms.mode = m;
    if(m==="req" && ms.month<=cur) ms.month = shiftYm(cur, 1);
    if(m==="report" && ms.month>cur) ms.month = cur;
    renderMsgHead();
  }
  $("mModeRep").onclick = function(){ setMode("report"); };
  $("mModeReq").onclick = function(){ setMode("req"); };
  $("mPrev").onclick = function(){ ms.month = shiftYm(ms.month,-1); renderMsgHead(); };
  $("mNext").onclick = function(){ ms.month = shiftYm(ms.month,1); renderMsgHead(); };
  $("mNote").oninput = function(){ state.notes[noteKey()] = this.value; save(); refreshReport(); };
  $("mText").oninput = function(){ ms.edited = true; syncLine(); };
  $("mStyle").onclick = function(){
    if(ms.edited && !window.confirm("手で直した内容は消えて、スタイルが変わります。よろしいですか？")) return;
    ms.style = (ms.style+1)%STYLES.length; state.settings.style = ms.style; save(); refreshReport();
  };
  $("evAdd").onclick = function(){
    var name = $("evName").value.trim(), date = $("evDate").value, ids = null, amt = 0;
    if(!name){ $("evName").focus(); return; }
    if(!date){ $("evDate").focus(); return; }
    if(evMode==="pick"){
      ids = selIds();
      if(!ids.length){ $("evSum").textContent = "先に、含める項目を選んでください"; return; }
    } else {
      amt = numOf("evAmt");
      if(amt<=0){ $("evAmt").focus(); return; }
    }
    var ev = null;
    if(evEditId) state.events.forEach(function(x){ if(x.id===evEditId) ev = x; });
    var isNew = !ev;
    if(isNew) ev = { id:uid() };
    ev.name = name; ev.date = date;
    if(ids){ ev.items = ids; delete ev.amt; } else { ev.amt = amt; delete ev.items; }
    if(isNew) state.events.push(ev);
    save(); ms.month = date.slice(0,7);
    renderMsgHead();
  };
  $("evCancel").onclick = function(){ resetEvForm(); renderEvents(); };
  $("mClose").onclick = closeSheet;
  $("mCopy").onclick = function(){
    var ta = $("mText"), text = ta.value;
    function done(){
      if(ms.mode==="report"){ state.sent[ms.month] = true; save(); renderBanners(); }
      toast("コピーしました。LINEに貼り付けてね");
    }
    function fallback(){
      ta.focus(); ta.select();
      try{ if(document.execCommand("copy")) done(); else toast("長押しでコピーしてください"); }catch(e){ toast("長押しでコピーしてください"); }
    }
    if(navigator.clipboard && navigator.clipboard.writeText){
      navigator.clipboard.writeText(text).then(done, fallback);
    } else { fallback(); }
  };
  $("mkMsg").onclick = function(){ openMsg(ui.month); };



  /* ---------- 領収書・引き換え / お知らせ ---------- */
  var RC_SVG = '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M6 3h12v18l-3-2-3 2-3-2-3 2z"/><path d="M9 8h6M9 12h6M9 16h3"/></svg>';
  var CAL_SVG = '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M4 6h16v14H4z"/><path d="M4 10h16M8 3v4M16 3v4"/></svg>';
  function dayAdd(ds, n){ return ymd(new Date(+ds.slice(0,4), +ds.slice(5,7)-1, +ds.slice(8,10)+n)); }
  function gcalUrl(date, text, details){
    var d = dayAdd(date,-1).replace(/-/g,"");
    return "https://calendar.google.com/calendar/render?action=TEMPLATE&text="+encodeURIComponent(text)+"&details="+encodeURIComponent(details)+"&dates="+d+"T090000/"+d+"T093000";
  }
  /* 写真はIndexedDBに保存（容量の大きいlocalStorageを避ける） */
  var imgMem = {}, idbP = null;
  function idb(){
    if(idbP) return idbP;
    idbP = new Promise(function(res){
      try{
        var r = indexedDB.open("kakeibo-img", 1);
        r.onupgradeneeded = function(){ r.result.createObjectStore("img"); };
        r.onsuccess = function(){ res(r.result); };
        r.onerror = function(){ res(null); };
      }catch(e){ res(null); }
    });
    return idbP;
  }
  function imgPut(k, v){
    return idb().then(function(db){
      if(!db){ imgMem[k] = v; return; }
      return new Promise(function(res){
        try{
          var tx = db.transaction("img","readwrite"); tx.objectStore("img").put(v, k);
          tx.oncomplete = function(){ res(); };
          tx.onerror = function(){ imgMem[k] = v; res(); };
        }catch(e){ imgMem[k] = v; res(); }
      });
    });
  }
  function imgGet(k){
    return idb().then(function(db){
      if(!db) return imgMem[k] || null;
      return new Promise(function(res){
        try{
          var r = db.transaction("img").objectStore("img").get(k);
          r.onsuccess = function(){ res(r.result || imgMem[k] || null); };
          r.onerror = function(){ res(imgMem[k] || null); };
        }catch(e){ res(imgMem[k] || null); }
      });
    });
  }
  function imgDel(k){
    delete imgMem[k];
    return idb().then(function(db){ if(!db) return; try{ db.transaction("img","readwrite").objectStore("img").delete(k); }catch(e){} });
  }
  function compress(file, cb){
    var fr = new FileReader();
    fr.onload = function(){
      var im = new Image();
      im.onload = function(){
        var sc = Math.min(1, 1000/Math.max(im.width, im.height));
        var cv = document.createElement("canvas");
        cv.width = Math.round(im.width*sc); cv.height = Math.round(im.height*sc);
        cv.getContext("2d").drawImage(im, 0, 0, cv.width, cv.height);
        cb(cv.toDataURL("image/jpeg", 0.72), cv.width, cv.height);
      };
      im.onerror = function(){ cb(null); };
      im.src = fr.result;
    };
    fr.onerror = function(){ cb(null); };
    fr.readAsDataURL(file);
  }

  var rc = { mode:"new", id:null, img:null, imgChanged:false, done:false };
  function renderRcptBtn(){
    $("rcptBtn").classList.toggle("on", !!ui.rcpt);
    $("rcptBtn").setAttribute("aria-label", ui.rcpt ? "領収書・引き換えを見る" : "領収書・引き換えを追加");
  }
  function renderRcImg(){
    var has = !!rc.img;
    $("rImg").hidden = !has; if(has) $("rImg").src = rc.img;
    $("rEmpty").hidden = has; $("rImgDel").hidden = !has;
    $("rPick").textContent = has ? "写真を変える" : "領収書の写真を選ぶ";
  }
  function updateRCal(){
    var d = $("rDate").value, a = $("rCal"), who = $("rWho").value.trim();
    if(d && d>today()){
      a.hidden = false;
      a.href = gcalUrl(d, "明日は引き換えの日", "明日は引き換えの日です。領収書を忘れないようにしましょう。"+(who ? "（"+who+"）" : ""));
    } else a.hidden = true;
  }
  function openReceipt(mode, id){
    rc.mode = mode; rc.id = id; rc.imgChanged = false;
    var r = {}, has = false;
    if(mode==="new"){ r = ui.rcpt || {}; has = !!ui.rcpt; rc.img = r.img || null; }
    else {
      var e = entryById(id); if(!e) return;
      r = e.receipt || {}; has = !!e.receipt; rc.img = null;
      if(r.img) imgGet(id).then(function(v){ if(rc.mode==="entry" && rc.id===id && !rc.imgChanged){ rc.img = v; renderRcImg(); } });
    }
    $("rWho").value = r.who || ""; $("rDate").value = r.date || "";
    rc.done = !!r.done; $("rDone").setAttribute("aria-pressed", rc.done);
    $("rClear").hidden = !has;
    renderRcImg(); updateRCal();
    $("scrim").classList.add("on"); $("rsheet").classList.add("on");
  }
  function closeR(){
    $("rsheet").classList.remove("on");
    if(rc.mode==="new" || !$("esheet").classList.contains("on")) $("scrim").classList.remove("on");
  }
  $("rcptBtn").onclick = function(){ openReceipt("new", null); };
  $("eRcpt").onclick = function(){ openReceipt("entry", es.id); };
  $("rPick").onclick = function(){ $("rFile").click(); };
  $("rFile").onchange = function(){
    var f = this.files && this.files[0]; this.value = "";
    if(!f) return;
    compress(f, function(url){
      if(url){ rc.img = url; rc.imgChanged = true; renderRcImg(); }
      else toast("画像を読み込めませんでした");
    });
  };
  $("rImgDel").onclick = function(){ rc.img = null; rc.imgChanged = true; renderRcImg(); };
  $("rDone").onclick = function(){ rc.done = !rc.done; this.setAttribute("aria-pressed", rc.done); };
  $("rDate").onchange = updateRCal;
  $("rWho").oninput = updateRCal;
  $("rCancel").onclick = closeR;
  $("rSave").onclick = function(){
    var who = $("rWho").value.trim(), date = $("rDate").value;
    if(rc.mode==="new"){
      ui.rcpt = { who:who, date:date, done:rc.done, img:rc.img };
      closeR(); renderRcptBtn(); toast("領収書をつけました。記録すると保存されます");
    } else {
      var e = entryById(rc.id); if(!e){ closeR(); return; }
      var had = !!(e.receipt && e.receipt.img);
      e.receipt = { who:who, date:date, done:rc.done, img: rc.imgChanged ? !!rc.img : had };
      if(rc.imgChanged){ if(rc.img) imgPut(e.id, rc.img); else imgDel(e.id); }
      save(); closeR(); updateECal(); renderRemind();
      if(es.after) es.after();
    }
  };
  $("rClear").onclick = function(){
    if(rc.mode==="new"){ ui.rcpt = null; closeR(); renderRcptBtn(); return; }
    if(!window.confirm("この記録の領収書・引き換えの情報を消します。よろしいですか？")) return;
    var e = entryById(rc.id); if(!e){ closeR(); return; }
    delete e.receipt; imgDel(e.id); save(); closeR(); updateECal(); renderRemind();
    if(es.after) es.after();
  };
  function updateECal(){
    var e = entryById(es.id), a = $("eCal");
    if(!e){ a.hidden = true; return; }
    var date = $("eDate").value || e.date, n = numOf("eAmt"), c = catById(es.cat);
    if(es.planned && date>today()){
      a.hidden = false;
      var lab = $("eMemo").value.trim() || (c?c.name:"");
      a.href = gcalUrl(date, "明日は「"+lab+"」に"+n.toLocaleString("ja-JP")+"円を使う予定", "明日は「"+lab+"」に"+n.toLocaleString("ja-JP")+"円を使う予定です。お財布やカードの準備をしておきましょう。");
    } else a.hidden = true;
    $("ePhoto").textContent = e.photo ? "写真を見る・変える" : "写真を追加";
    $("eRcpt").textContent = e.receipt ? "領収書・引き換え（"+(e.receipt.date ? md(e.receipt.date) : "日付なし")+(e.receipt.done ? "・済み" : "")+"）" : "領収書・引き換えを追加";
  }
  $("eDate").oninput = updateECal;
  $("eAmt").oninput = updateECal;

  function reminders(){
    var t = today(), tm = dayAdd(t,1), out = [];
    state.entries.forEach(function(e){
      if(e.receipt && !e.receipt.done && e.receipt.date){
        var d = e.receipt.date;
        if(d===tm) out.push({ key:"r"+e.id+d, kind:"rcpt", e:e, msg:"明日は引き換えの日です。領収書を忘れないようにしましょう。" });
        else if(d===t) out.push({ key:"r"+e.id+d, kind:"rcpt", e:e, msg:"今日は引き換えの日です。領収書を持ちましたか？" });
        else if(d<t) out.push({ key:"r"+e.id+d, kind:"rcpt", e:e, msg:"引き換え日（"+md(d)+"）を過ぎています。まだなら早めに行きましょう。" });
      }
      if(e.planned && e.date===tm) out.push({ key:"p"+e.id+tm, kind:"plan", e:e, msg:"明日は「"+entryLabel(e)+"」に"+e.amt.toLocaleString("ja-JP")+"円を使う予定です。" });
    });
    return out.filter(function(x){ return !state.dismissed[x.key]; });
  }
  function renderRemind(){
    var items = reminders(), h = "";
    items.forEach(function(r,i){
      var e = r.e, c = catById(e.cat), sub;
      if(r.kind==="rcpt") sub = (e.receipt.who ? esc(e.receipt.who)+"　" : "")+(c ? c.emoji+" "+esc(c.name)+" " : "")+yen(e.amt);
      else sub = (c ? c.emoji+" "+esc(c.name)+"　" : "")+"お財布やカードの準備をしておきましょう。";
      h += '<div class="remind"><span class="ri">'+(r.kind==="rcpt" ? RC_SVG : CAL_SVG)+'</span><div class="rt"><b>'+esc(r.msg)+'</b><small>'+sub+'</small></div>'
        + (r.kind==="rcpt" ? '<button class="rdone" data-k="'+i+'">済み</button>' : '')
        + '<button class="rx" data-x="'+i+'" aria-label="閉じる">×</button></div>';
    });
    $("remind").innerHTML = h;
    Array.prototype.forEach.call($("remind").querySelectorAll("[data-x]"), function(b){
      b.onclick = function(){ state.dismissed[items[+b.getAttribute("data-x")].key] = true; save(); renderRemind(); };
    });
    Array.prototype.forEach.call($("remind").querySelectorAll("[data-k]"), function(b){
      b.onclick = function(){
        var e = items[+b.getAttribute("data-k")].e, r = b.getBoundingClientRect();
        e.receipt.done = true; save(); renderRemind();
        showUndo("引き換え済みにしました", function(){ e.receipt.done = false; save(); renderRemind(); }, r);
      };
    });
  }

  /* ---------- ウィッシュリスト・写真 ---------- */
  var PH_SVG = '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><rect x="3" y="5" width="18" height="14" rx="2.5"/><circle cx="9" cy="10" r="1.8"/><path d="M4 18l5-5 4 4 3-3 4 4"/></svg>';
  function wishItems(){
    var t = today(), items = [];
    state.entries.filter(function(e){ return e.planned && e.date>t; }).sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); }).forEach(function(e){
      items.push({ id:e.id, name:entryLabel(e), cat:catById(e.cat), price:e.amt, date:e.date, ar:e.par, key:e.photo ? "ph:"+e.id : null });
    });
    return items;
  }
  function illTile(x, hs){
    var ci = hs%8+1, txt = x.name+" "+(x.cat ? x.cat.name : "");
    return { style:'color:var(--c'+ci+');background:color-mix(in srgb,var(--c'+ci+') 15%,var(--surface))', svg:illSvgByKey(illKeyFor(txt, hs, WISH_FALL), "wil") };
  }
  function renderWishTab(){
    var items = wishItems(), sum = items.reduce(function(s,x){ return s+(x.price||0); },0);
    $("wTotal").textContent = items.length ? items.length+"件　合計 "+yen(sum) : "";
    if(!items.length){
      $("wGrid").innerHTML = '<div class="empty" style="column-span:all;padding:48px 8px;text-align:center;line-height:2">まだ日付が来ていない「使う予定」が、ここに並びます。<br>記録で「使う予定」を選んで追加してね。<br>写真を付けると、写真が大きく並びます。</div>';
      return;
    }
    var h = "";
    items.forEach(function(x,i){
      var has = !!x.key, hs = hashStr(x.name+x.id), last = hs%5, st, inner, cls = "wimg";
      if(has){ st = 'aspect-ratio:'+(x.ar||1); inner = '<img data-k="'+x.key+'" data-i="'+i+'" alt="">'; }
      else {
        var t = illTile(x, hs);
        st = 'aspect-ratio:1 / '+(0.78+last*0.14)+';'+t.style; inner = t.svg; cls += " noimg";
      }
      h += '<button class="wcard" data-i="'+i+'"><div class="'+cls+'" style="'+st+'">'+inner+'<span class="wdate num">'+md(x.date)+'</span></div><div class="wcap"><b>'+(x.cat ? x.cat.emoji+" " : "")+esc(x.name)+'</b><span class="num">'+yen(x.price)+'</span></div></button>';
    });
    $("wGrid").innerHTML = h;
    Array.prototype.forEach.call($("wGrid").querySelectorAll("img[data-k]"), function(im){
      imgGet(im.getAttribute("data-k")).then(function(v){
        if(v){ im.src = v; return; }
        var x = items[+im.getAttribute("data-i")], box = im.parentNode;
        if(!x || !box) return;
        var t = illTile(x, hashStr(x.name+x.id));
        box.className = "wimg noimg"; box.style.cssText = 'aspect-ratio:1 / 1;'+t.style;
        box.innerHTML = t.svg+'<span class="wdate num">'+md(x.date)+'</span>';
      });
    });
    Array.prototype.forEach.call($("wGrid").querySelectorAll(".wcard"), function(b){
      b.onclick = function(){ openEntry(items[+b.getAttribute("data-i")].id, renderWishTab); };
    });
  }
  $("wAdd").onclick = function(){
    ui.planned = true;
    var t = new Date(); t.setDate(t.getDate()+1); $("date").value = ymd(t);
    setTab("input"); renderChips(); renderDisplay();
  };

  /* 記録画面の写真 */
  var ph = { mode:"new", id:null, img:null };
  function renderPhBtn(){
    var b = $("photoBtn");
    b.classList.toggle("on", !!ui.photo);
    b.style.backgroundImage = ui.photo ? 'url("'+ui.photo+'")' : "";
    b.setAttribute("aria-label", ui.photo ? "写真を見る・変える" : "写真を入れる");
  }
  function renderPh(){
    var has = !!ph.img;
    $("pImg").hidden = !has; if(has) $("pImg").src = ph.img;
    $("pEmpty").hidden = has; $("pDel").hidden = !has;
    $("pPick").textContent = has ? "写真を変える" : "写真を選ぶ";
  }
  function openPhoto(mode, id){
    ph.mode = mode; ph.id = id; ph.img = null;
    if(mode==="new") ph.img = ui.photo || null;
    else {
      var e = entryById(id); if(!e) return;
      if(e.photo) imgGet("ph:"+id).then(function(v){ if(ph.mode==="entry" && ph.id===id){ ph.img = v; renderPh(); } });
    }
    renderPh();
    $("scrim").classList.add("on"); $("psheet").classList.add("on");
  }
  function closePh(){
    $("psheet").classList.remove("on");
    if(ph.mode==="new" || !$("esheet").classList.contains("on")) $("scrim").classList.remove("on");
  }
  function applyPhoto(url, ar){
    if(ph.mode==="new"){ ui.photo = url; ui.photoAr = ar; ph.img = url; renderPhBtn(); }
    else {
      var e = entryById(ph.id); if(!e) return;
      e.photo = true; e.par = ar; imgPut("ph:"+e.id, url); save(); ph.img = url;
      updateECal(); if(es.after) es.after();
    }
    renderPh();
  }
  function pickPhoto(){ $("phFile").click(); }
  $("photoBtn").onclick = function(){
    ph.mode = "new"; ph.id = null;
    if(ui.photo) openPhoto("new", null); else pickPhoto();
  };
  $("ePhoto").onclick = function(){ openPhoto("entry", es.id); };
  $("pPick").onclick = pickPhoto;
  $("phFile").onchange = function(){
    var f = this.files && this.files[0]; this.value = "";
    if(!f) return;
    compress(f, function(url, w, h){
      if(url) applyPhoto(url, w/h); else toast("画像を読み込めませんでした");
    });
  };
  $("pDel").onclick = function(){
    if(ph.mode==="new"){ ui.photo = null; ui.photoAr = null; ph.img = null; renderPhBtn(); }
    else {
      var e = entryById(ph.id); if(!e) return;
      e.photo = false; delete e.par; imgDel("ph:"+e.id); save(); ph.img = null;
      updateECal(); if(es.after) es.after();
    }
    renderPh();
  };
  $("pClose").onclick = closePh;
  $("t-wish").onclick = function(){ setTab("wish"); };

  /* ---------- 情報・テーマ・スプレッドシート ---------- */
  function svgUrl(svg){ return 'url("data:image/svg+xml,'+encodeURIComponent(svg)+'")'; }
  function noiseTex(freq, rgb, c, d, size, oct){
    var svg = "<svg xmlns='http://www.w3.org/2000/svg' width='"+size+"' height='"+size+"'><filter id='n' x='0' y='0' width='100%' height='100%'><feTurbulence type='fractalNoise' baseFrequency='"+freq+"' numOctaves='"+(oct||3)+"' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 "+(rgb[0]/255).toFixed(3)+" 0 0 0 0 "+(rgb[1]/255).toFixed(3)+" 0 0 0 0 "+(rgb[2]/255).toFixed(3)+" "+c+" 0 0 0 "+d+"'/></filter><rect width='100%' height='100%' filter='url(#n)'/></svg>";
    return svgUrl(svg);
  }
  var LC = { "--c2":"#6C8EBF", "--c3":"#9AB89B", "--c4":"#D9A86C", "--c5":"#C27C7C", "--c6":"#8E7CB3", "--c7":"#5E8C8C", "--c8":"#B5B0A3" };
  function mkSkin(id, name, v, layers){
    var o = {};
    Object.keys(LC).forEach(function(k){ o[k] = LC[k]; });
    Object.keys(v).forEach(function(k){ o[k] = v[k]; });
    o["--c1"] = v["--accent"];
    return { id:id, name:name, v:o, tex:layers.map(function(l){ return l[0]; }).join(", "), size:layers.map(function(l){ return l[1]; }).join(", ") };
  }
  var WAVE_WATER = svgUrl("<svg xmlns='http://www.w3.org/2000/svg' width='120' height='48'><path d='M0 16 Q15 4 30 16 T60 16 T90 16 T120 16' fill='none' stroke='white' stroke-opacity='.55' stroke-width='2'/><path d='M0 38 Q15 26 30 38 T60 38 T90 38 T120 38' fill='none' stroke='white' stroke-opacity='.3' stroke-width='2'/></svg>");
  var WAVE_LINE = svgUrl("<svg xmlns='http://www.w3.org/2000/svg' width='56' height='28'><path d='M0 14 Q14 0 28 14 T56 14' fill='none' stroke='rgb(91,75,196)' stroke-opacity='.22' stroke-width='2'/></svg>");
  var SKINS = [
    { id:"default", name:"標準" },
    mkSkin("pink","ピンクの布",{"--bg":"#F6D5DF","--surface":"#FFF6F8","--ink":"#47222E","--sub":"#8E5666","--line":"#E7B9C7","--key":"#F2C4D2","--key-press":"#EBB0C2","--accent":"#BE3F68","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["repeating-linear-gradient(0deg,rgba(255,255,255,.28) 0 1px,rgba(255,255,255,0) 1px 3px)","auto"],
      ["repeating-linear-gradient(90deg,rgba(176,56,98,.09) 0 1px,rgba(255,255,255,0) 1px 3px)","auto"],
      [noiseTex("0.7",[170,60,100],1.1,-0.38,200),"200px 200px"]]),
    mkSkin("yellow","黄色の画用紙",{"--bg":"#F6DE6A","--surface":"#FFFBE3","--ink":"#352C00","--sub":"#6F5F12","--line":"#E2C84F","--key":"#F0D250","--key-press":"#E7C73C","--accent":"#B35A00","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      [noiseTex("0.9",[130,95,0],1.0,-0.36,180),"180px 180px"],
      [noiseTex("0.02",[255,255,255],1.6,-0.55,400,2),"400px 400px"],
      ["linear-gradient(180deg,rgba(255,255,255,.18),rgba(255,255,255,0))","auto"]]),
    mkSkin("sand","砂",{"--bg":"#E6D3AB","--surface":"#FBF4E4","--ink":"#3A2F1E","--sub":"#7F6A49","--line":"#D2BC90","--key":"#DCC698","--key-press":"#D2BA88","--accent":"#9A5A24","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      [noiseTex("1.1",[110,80,40],1.8,-0.8,160),"160px 160px"],
      [noiseTex("0.8",[255,255,255],1.4,-0.55,160),"160px 160px"],
      ["repeating-radial-gradient(ellipse at 30% 130%,rgba(140,100,50,.08) 0 2px,rgba(0,0,0,0) 2px 20px)","auto"]]),
    mkSkin("metal","メタル",{"--bg":"#C3C9D2","--surface":"#F1F3F6","--ink":"#1B2230","--sub":"#505B6D","--line":"#A6AEBB","--key":"#D3D8DF","--key-press":"#C2C8D1","--accent":"#34476A","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["repeating-linear-gradient(90deg,rgba(255,255,255,.30) 0 1px,rgba(0,0,0,.05) 1px 2px,rgba(255,255,255,0) 2px 4px)","auto"],
      ["linear-gradient(115deg,rgba(255,255,255,.6),rgba(255,255,255,0) 35%,rgba(255,255,255,.35) 62%,rgba(0,0,0,.07))","auto"],
      [noiseTex("0.9",[60,70,90],0.9,-0.3,160),"160px 160px"]]),
    mkSkin("water","水（ビーチ）",{"--bg":"#B7E2EC","--surface":"#F3FBFD","--ink":"#0C3140","--sub":"#3F7081","--line":"#9ACBD8","--key":"#C5E7EF","--key-press":"#B3DDE8","--accent":"#0A809A","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      [WAVE_WATER,"120px 48px"],
      ["linear-gradient(180deg,rgba(255,255,255,.35),rgba(255,255,255,0) 30%)","auto"],
      ["linear-gradient(180deg,rgba(255,255,255,0) 80%,rgba(238,220,170,.85) 100%)","auto"]]),
    mkSkin("paint","絵の具",{"--bg":"#FBF2E4","--surface":"#FFFFFF","--ink":"#2E2220","--sub":"#7C6A63","--line":"#EBDCC6","--key":"#F3E6D3","--key-press":"#EAD9C0","--accent":"#D9482B","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["radial-gradient(circle at 10% 14%,rgba(228,87,46,.30),rgba(228,87,46,0) 34%)","auto"],
      ["radial-gradient(circle at 92% 10%,rgba(255,196,40,.38),rgba(255,196,40,0) 32%)","auto"],
      ["radial-gradient(circle at 86% 78%,rgba(40,130,225,.30),rgba(40,130,225,0) 38%)","auto"],
      ["radial-gradient(circle at 12% 86%,rgba(70,175,90,.28),rgba(70,175,90,0) 34%)","auto"],
      ["radial-gradient(circle at 50% 46%,rgba(180,90,200,.14),rgba(180,90,200,0) 30%)","auto"],
      [noiseTex("0.04 0.5",[120,90,60],1.0,-0.4,300),"300px 300px"]]),
    mkSkin("wave","波線",{"--bg":"#E4E0F6","--surface":"#FBFAFF","--ink":"#241F4A","--sub":"#625B96","--line":"#CBC4EA","--key":"#D9D4F1","--key-press":"#CCC5EA","--accent":"#5846C2","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      [WAVE_LINE,"56px 28px"]]),
    mkSkin("memo","メモ用紙",{"--bg":"#FFFDF0","--surface":"#FFFFFF","--ink":"#2A2A2A","--sub":"#6E6E6E","--line":"#E5E1CC","--key":"#F4F1DE","--key-press":"#EAE6CC","--accent":"#2F6FB5","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["repeating-linear-gradient(180deg,rgba(0,0,0,0) 0 27px,rgba(70,130,200,.30) 27px 28px)","auto"],
      [noiseTex("0.8",[120,100,60],0.7,-0.25,180),"180px 180px"]]),
    mkSkin("mesh","網目",{"--bg":"#D3E2D8","--surface":"#F5FAF6","--ink":"#17301F","--sub":"#4F6F5A","--line":"#B6CDBE","--key":"#C4D8CB","--key-press":"#B3CBBC","--accent":"#25704D","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["repeating-linear-gradient(45deg,rgba(20,60,40,.16) 0 1px,rgba(0,0,0,0) 1px 11px)","auto"],
      ["repeating-linear-gradient(-45deg,rgba(20,60,40,.16) 0 1px,rgba(0,0,0,0) 1px 11px)","auto"]]),
    mkSkin("wood","木目",{"--bg":"#D6B488","--surface":"#FBF2E3","--ink":"#35220F","--sub":"#76552F","--line":"#C09A6B","--key":"#E2C69C","--key-press":"#D8B982","--accent":"#7A3C18","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["repeating-linear-gradient(90deg,rgba(60,30,10,.20) 0 1px,rgba(0,0,0,0) 1px 140px)","auto"],
      [noiseTex("0.012 0.5",[84,46,16],1.5,-0.5,360,4),"360px 360px"],
      [noiseTex("0.03 0.9",[255,230,190],0.8,-0.3,240,2),"240px 240px"]]),
    mkSkin("tatami","畳",{"--bg":"#C5D095","--surface":"#F5F8E8","--ink":"#2A3718","--sub":"#5F6E37","--line":"#AAB878","--key":"#D2DCA4","--key-press":"#C5D192","--accent":"#4F7424","--accent-ink":"#FFFFFF","--warn":"#B3321C"},[
      ["linear-gradient(90deg,rgba(40,58,22,.45) 0 3px,rgba(0,0,0,0) 3px)","220px 110px"],
      ["linear-gradient(0deg,rgba(40,58,22,.45) 0 3px,rgba(0,0,0,0) 3px)","220px 110px"],
      ["repeating-linear-gradient(0deg,rgba(70,92,30,.16) 0 1px,rgba(0,0,0,0) 1px 4px)","auto"],
      [noiseTex("0.9 0.3",[255,255,200],0.8,-0.3,200),"200px 200px"]])
  ];
  var SKIN_KEYS = ["--bg","--surface","--ink","--sub","--line","--key","--key-press","--accent","--accent-ink","--warn","--c1","--c2","--c3","--c4","--c5","--c6","--c7","--c8","--tex","--texsize","color-scheme"];
  function findSkin(id){ for(var i=0;i<SKINS.length;i++){ if(SKINS[i].id===id) return SKINS[i]; } return SKINS[0]; }
  function applySkin(id){
    var root = document.documentElement;
    if(!root || !root.style) return;
    SKIN_KEYS.forEach(function(k){ root.style.removeProperty(k); });
    var sk = findSkin(id);
    if(sk.v){
      Object.keys(sk.v).forEach(function(k){ root.style.setProperty(k, sk.v[k]); });
      root.style.setProperty("--tex", sk.tex);
      root.style.setProperty("--texsize", sk.size);
      root.style.setProperty("color-scheme", "light");
    }
    state.settings.skin = sk.id; save();
  }
  function renderSkins(){
    var cur = state.settings.skin || "default", h = "";
    SKINS.forEach(function(sk){
      var st = sk.v ? 'background-color:'+sk.v["--bg"]+';background-image:'+sk.tex.replace(/"/g,"&quot;")+';background-size:'+sk.size : 'background:#F5F6F7';
      h += '<button class="skin" type="button" data-id="'+sk.id+'" aria-pressed="'+(cur===sk.id)+'" style="'+st+'"><span>'+sk.name+'</span></button>';
    });
    $("skins").innerHTML = h;
    Array.prototype.forEach.call($("skins").querySelectorAll(".skin"), function(b){
      b.onclick = function(){ applySkin(b.getAttribute("data-id")); renderSkins(); };
    });
  }

  function cleanCell(v){ return String(v==null ? "" : v).replace(/[\t\r\n]+/g," "); }
  function buildSheetRows(){
    var R = [], months = {}, cm = ym(new Date());
    state.entries.forEach(function(e){ months[e.date.slice(0,7)] = 1; });
    months[cm] = 1;
    var ml = Object.keys(months).sort().slice(-12);
    R.push(["家計簿まとめ", today()]); R.push([]);
    R.push(["■ 月ごとの合計（円）"]);
    var head = ["項目"];
    ml.forEach(function(m){ var n = (+m.slice(5,7))+"月"; head.push(n+" 実績", n+" 予定", n+" 目標"); });
    head.push("実績の合計");
    R.push(head);
    var tot = ml.map(function(){ return [0,0,0]; }), allTot = 0;
    state.cats.forEach(function(c){
      var row = [c.emoji+" "+c.name], sumAll = 0;
      ml.forEach(function(m,i){
        var a = 0, p = 0;
        state.entries.forEach(function(e){ if(e.cat!==c.id || e.date.slice(0,7)!==m) return; if(e.planned) p += e.amt; else a += e.amt; });
        var g = goalOf(c, m);
        row.push(a, p, g||""); tot[i][0] += a; tot[i][1] += p; tot[i][2] += g; sumAll += a;
      });
      row.push(sumAll); allTot += sumAll; R.push(row);
    });
    var tr = ["合計"];
    tot.forEach(function(t){ tr.push(t[0], t[1], t[2]||""); });
    tr.push(allTot); R.push(tr); R.push([]);
    R.push(["■ 項目ごとの記録"]);
    state.cats.forEach(function(c){
      var es2 = state.entries.filter(function(e){ return e.cat===c.id; }).sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); });
      R.push([c.emoji+" "+c.name]);
      R.push(["日付","金額","メモ","種類","調整","引き換え日","誰のため","引き換え"]);
      var sum = 0;
      es2.forEach(function(e){
        if(!e.planned) sum += e.amt;
        R.push([e.date, e.amt, e.memo||"", e.planned?"予定":"実績", e.adj||"", (e.receipt && e.receipt.date)||"", (e.receipt && e.receipt.who)||"", e.receipt ? (e.receipt.done?"済み":"未") : ""]);
      });
      R.push(["実績の小計", sum]); R.push([]);
    });
    if(state.events.length){
      R.push(["■ 特大イベント"]); R.push(["名前","日付","金額","式"]);
      state.events.slice().sort(function(a,b){ return a.date<b.date?-1:(a.date>b.date?1:0); }).forEach(function(e){ R.push([e.name, e.date, evAmt(e), evFormula(e)]); });
      R.push([]);
    }
    return R;
  }
  function toTSV(rows){ return rows.map(function(r){ return r.map(cleanCell).join("\t"); }).join("\n"); }
  function toCSV(rows){ return "\uFEFF"+rows.map(function(r){ return r.map(function(v){ return '"'+cleanCell(v).replace(/"/g,'""')+'"'; }).join(","); }).join("\r\n"); }
  function fallbackCopy(text){
    var ok = false;
    try{
      var ta = document.createElement("textarea");
      ta.value = text; ta.style.position = "fixed"; ta.style.opacity = "0";
      document.body.appendChild(ta); ta.select();
      ok = document.execCommand("copy");
      document.body.removeChild(ta);
    }catch(e){}
    return ok;
  }
  function copyText(text){
    if(navigator.clipboard && navigator.clipboard.writeText) return navigator.clipboard.writeText(text).then(function(){ return true; }, function(){ return fallbackCopy(text); });
    return Promise.resolve(fallbackCopy(text));
  }
  function renderInfo(){
    var h = "";
    state.cats.forEach(function(c){
      var n = 0, a = 0, p = 0;
      state.entries.forEach(function(e){ if(e.cat!==c.id) return; n++; if(e.planned) p += e.amt; else a += e.amt; });
      h += '<div class="irow2"><span>'+c.emoji+' '+esc(c.name)+'</span><span class="num">'+n+'件</span><span class="num">'+yen(a)+(p>0 ? ' <i>＋予定 '+yen(p)+'</i>' : '')+'</span></div>';
    });
    if(!state.cats.length) h = '<div class="empty">項目をつくると、ここに記入内容がまとまります。</div>';
    $("isum").innerHTML = h;
    var act = state.entries.filter(function(e){ return !e.planned; }).length, pl = state.entries.filter(function(e){ return e.planned; }).length;
    var rc = state.entries.filter(function(e){ return e.receipt; }), rcOpen = rc.filter(function(e){ return !e.receipt.done; }).length;
    var photos = state.entries.filter(function(e){ return e.photo; }).length;
    var goals = state.cats.filter(function(c){ return goalOf(c, ym(new Date()))>0; }).length;
    var cells = [["項目", state.cats.length+"個"],["記録（実績）", act+"件"],["使う予定", pl+"件"],["ウィッシュ（これから）", wishItems().length+"件"],["領収書", rc.length+"件（未引換 "+rcOpen+"）"],["写真", photos+"枚"],["特大イベント", state.events.length+"件"],["今月の目標がある項目", goals+"個"]];
    var g = "";
    cells.forEach(function(x){ g += '<div class="istat"><small>'+x[0]+'</small><b class="num">'+x[1]+'</b></div>'; });
    $("istats").innerHTML = g;
  }
  function openInfo(){
    renderSkins(); renderInfo();
    $("gsMsg").textContent = "「コピーして開く」を押すと、まとめをコピーして新しいスプレッドシートが開きます。A1に貼り付けると、項目ごとの表になります。";
    $("scrim").classList.add("on"); $("isheet").classList.add("on");
  }
  $("infoBtn").onclick = openInfo;
  $("iClose").onclick = closeSheet;
  $("gsOpen").onclick = function(){
    copyText(toTSV(buildSheetRows())).then(function(ok){
      $("gsMsg").textContent = ok ? "コピーしました。開いたスプレッドシートのA1をタップして、貼り付けてください。" : "コピーできませんでした。「CSVを保存」を使ってください。";
    });
  };
  $("csvSave").onclick = async function(){
    var csv = toCSV(buildSheetRows());
    try{
      var dl = (window.claude && window.claude.use) ? await window.claude.use("downloads") : null;
      if(!dl) throw new Error("none");
      await dl.save({ filename:"kakeibo-"+today()+".csv", data:csv });
      $("gsMsg").textContent = "保存しました。Googleドライブに入れて、スプレッドシートで開けます。";
    }catch(e){
      $("gsMsg").textContent = (e && e.code==="declined") ? "保存をやめました。" : "この画面では保存できませんでした。「コピーして開く」を使ってください。";
    }
  };
  applySkin(state.settings.skin || "default");

  if(state.wl && state.wl.length){ state.wl.forEach(function(w){ imgDel("w:"+w.id); }); state.wl = []; save(); }
  renderChips(); renderDisplay(); renderBanners(); renderWish(); renderRemind(); renderRcptBtn(); renderPhBtn();
})();
</script>
</body>
</html>
