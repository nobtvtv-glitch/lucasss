local MENU_KEY = "Fischborn"
local hasAccess = false
local menuOpen = false

RegisterCommand("mystery", function()
    AddTextEntry('MM_KEY', 'Digite a Key')
    DisplayOnscreenKeyboard(1, 'MM_KEY', '', '', '', '', '', 20)
    while UpdateOnscreenKeyboard() == 0 do Wait(0) end
    local result = GetOnscreenKeyboardResult()
    if result == MENU_KEY then
        hasAccess = true
        SendNUIMessage({ action = "showLoading" })
        SetNuiFocus(true, true)
    else
        TriggerEvent('chat:addMessage', { args = { 'Mystery Menu', 'Key incorreta.' } })
    end
end)

RegisterNUICallback("loadingDone", function(_, cb)
    SendNUIMessage({ action = "openMenu" })
    cb('ok')
end)

RegisterNUICallback("close", function(_, cb)
    SetNuiFocus(false, false)
    cb('ok')
end)

-- ===== FUNÇÕES ADMIN PERMITIDAS =====
RegisterNUICallback("teleportWaypoint", function(_, cb)
    local wp = GetFirstBlipInfoId(8)
    if DoesBlipExist(wp) then
        local c = GetBlipInfoIdCoord(wp)
        SetEntityCoords(PlayerPedId(), c.x, c.y, c.z)
    end
    cb('ok')
end)

RegisterNUICallback("toggleFly", function(_, cb)
    -- fly simples admin (sem abuso)
    local ped = PlayerPedId()
    SetEntityInvincible(ped, true)
    SetEntityCollision(ped, false, false)
    cb('ok')
end)

RegisterNUICallback("spawnVehicle", function(data, cb)
    local model = data.model or "adder"
    local m = GetHashKey(model)
    RequestModel(m)
    while not HasModelLoaded(m) do Wait(0) end
    local p = PlayerPedId()
    local c = GetEntityCoords(p)
    CreateVehicle(m, c.x, c.y, c.z, GetEntityHeading(p), true, false)
    cb('ok')
end)

RegisterNUICallback("toggleInvisible", function(_, cb)
    local p = PlayerPedId()
    SetEntityVisible(p, not IsEntityVisible(p), false)
    cb('ok')
end)
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8"/>
  <link rel="stylesheet" href="style.css"/>
</head>
<body>
  <div id="loading" class="screen red hidden">
    <h1>Mystery Menu</h1>
    <p>Carregando <span id="pct">0</span>%</p>
  </div>

  <div id="menu" class="screen red hidden">
    <div class="tabs">
      <button data-tab="pvp">PVP</button>
      <button data-tab="esp">ESP</button>
      <button data-tab="tp">Teleporte</button>
      <button data-tab="arsenal">Arsenal</button>
      <button data-tab="flycar">Fly Car</button>
      <button data-tab="loja">Loja</button>
      <button class="close">X</button>
    </div>

    <div class="content" id="tp">
      <button id="tpWp">Teleportar p/ Waypoint</button>
      <button id="fly">Fly (Admin)</button>
    </div>

    <div class="content hidden" id="arsenal">
      <p>Placeholders (funções permitidas apenas)</p>
      <button id="spawnAdder">Spawn Adder</button>
    </div>

    <div class="content hidden" id="pvp"><p>UI pronta (placeholder)</p></div>
    <div class="content hidden" id="esp"><p>UI pronta (placeholder)</p></div>
    <div class="content hidden" id="flycar"><p>UI pronta (placeholder)</p></div>
    <div class="content hidden" id="loja"><p>UI pronta (placeholder)</p></div>
  </div>

  <script src="app.js"></script>
</body>
</html>
* { font-family: Arial, sans-serif; }
.hidden { display: none; }
.screen {
  position: fixed; inset: 0;
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
}
.red { background: #b00000; color: #fff; }
.tabs { display: flex; gap: 6px; margin-bottom: 10px; }
.tabs button { padding: 8px 10px; }
.content { background: rgba(0,0,0,.3); padding: 12px; }
.close { margin-left: auto; }
const loading = document.getElementById('loading');
const menu = document.getElementById('menu');
const pct = document.getElementById('pct');

window.addEventListener('message', (e) => {
  if (e.data.action === 'showLoading') {
    loading.classList.remove('hidden');
    let p = 0;
    const i = setInterval(() => {
      p++; pct.innerText = p;
      if (p >= 100) {
        clearInterval(i);
        fetch(https://${GetParentResourceName()}/loadingDone, {method:'POST', body:'{}'});
      }
    }, 20);
  }
  if (e.data.action === 'openMenu') {
    loading.classList.add('hidden');
    menu.classList.remove('hidden');
  }
});

document.querySelectorAll('.tabs button[data-tab]').forEach(b=>{
  b.onclick=()=>{
    document.querySelectorAll('.content').forEach(c=>c.classList.add('hidden'));
    document.getElementById(b.dataset.tab).classList.remove('hidden');
  };
});
document.querySelector('.close').onclick=()=>fetch(https://${GetParentResourceName()}/close,{method:'POST',body:'{}'});

document.getElementById('tpWp').onclick=()=>fetch(https://${GetParentResourceName()}/teleportWaypoint,{method:'POST',body:'{}'});
document.getElementById('fly').onclick=()=>fetch(https://${GetParentResourceName()}/toggleFly,{method:'POST',body:'{}'});
document.getElementById('spawnAdder').onclick=()=>fetch(https://${GetParentResourceName()}/spawnVehicle,{
  method:'POST', body: JSON.stringify({model:'adder'})
});
