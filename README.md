-- Mystery Menu - Admin RP (LEGÍTIMO)
-- Key de acesso: Fischborn

local MENU_KEY = "Fischborn"
local menuOpen = false

-- Permissões simples (exemplo)
local isAdmin = false

-- Comando para abrir menu
RegisterCommand("mystery", function()
    local input = KeyboardInput("Digite a Key:", "", 20)
    if input == MENU_KEY then
        isAdmin = true
        menuOpen = not menuOpen
        TriggerEvent("chat:addMessage", {
            args = {"Mystery Menu", "Acesso autorizado."}
        })
    else
        TriggerEvent("chat:addMessage", {
            args = {"Mystery Menu", "Key incorreta."}
        })
    end
end)

-- Menu simples (exemplo básico)
Citizen.CreateThread(function()
    while true do
        Citizen.Wait(0)
        if menuOpen and isAdmin then
            DrawTxt("~b~Mystery Menu~w~\n1 - Teleport\n2 - Spawn Veículo\n3 - Invisível\n4 - God Mode\n5 - Dar Arma", 0.01, 0.3)

            if IsControlJustPressed(0, 157) then -- 1
                SetEntityCoords(PlayerPedId(), 200.0, -900.0, 30.0)
            end

            if IsControlJustPressed(0, 158) then -- 2
                SpawnVehicle("adder")
            end

            if IsControlJustPressed(0, 160) then -- 3
                SetEntityVisible(PlayerPedId(), false, false)
            end

            if IsControlJustPressed(0, 164) then -- 4
                SetEntityInvincible(PlayerPedId(), true)
            end

            if IsControlJustPressed(0, 165) then -- 5
                GiveWeaponToPed(PlayerPedId(), `WEAPON_PISTOL`, 250, false, true)
            end
        end
    end
end)

-- Funções auxiliares
function SpawnVehicle(model)
    local m = GetHashKey(model)
    RequestModel(m)
    while not HasModelLoaded(m) do Wait(0) end
    local ped = PlayerPedId()
    local coords = GetEntityCoords(ped)
    CreateVehicle(m, coords.x, coords.y, coords.z, GetEntityHeading(ped), true, false)
end

function KeyboardInput(text, example, max)
    AddTextEntry("FMMC_KEY_TIP1", text)
    DisplayOnscreenKeyboard(1, "FMMC_KEY_TIP1", "", example, "", "", "", max)
    while UpdateOnscreenKeyboard() == 0 do
        DisableAllControlActions(0)
        Wait(0)
    end
    if GetOnscreenKeyboardResult() then
        return GetOnscreenKeyboardResult()
    end
    return nil
end

function DrawTxt(text, x, y)
    SetTextFont(4)
    SetTextScale(0.35, 0.35)
    SetTextColour(255,255,255,215)
    SetTextOutline()
    BeginTextCommandDisplayText("STRING")
    AddTextComponentSubstringPlayerName(text)
    EndTextCommandDisplayText(x, y)
end
