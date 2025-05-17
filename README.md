#Requires AutoHotkey v2.0
#UseHook

global macroActive := false
global macroToggleKey := "v"
global toggleHotkey
global guiStatus, guiText, guiVKey, waitingForKey := false

; === GUI ===
guiStatus := Gui("+AlwaysOnTop -Caption +ToolWindow +LastFound +E0x80020")
guiStatus.BackColor := "050A16"  ; koyu gece mavisi bir tema beyler 
guiStatus.MarginX := 12
guiStatus.MarginY := 7

; Simge ● ve durum metni
guiStatus.Add("Text", "vStatusIcon x5 y8 w15 Center cRed", "●")
guiText := guiStatus.Add("Text", "vStatusText x30 y6 w180 h30 Center cRed", "")

; 'V' Butonu
guiVKey := guiStatus.Add("Text", "x40 y8 w30 h30 cRed", "V")  ; guiVKey'e metin ekliyoz 

; GUI’yi Göster
UpdateGUI()
FadeIn(guiStatus.Hwnd)
guiStatus.Show("x10 y10 NoActivate")
WinSetTransparent(230, guiStatus.Hwnd)

; Köşeleri yuvarlamak 
RoundCorners(guiStatus.Hwnd, 16)

; Hotkey Tanımlamak 
SetHotkey()

; === Sol Tık Makrosu ===
~*LButton:: {
    global macroActive

    ; Yalnızca ProSoccerOnline penceresi aktifse makro çalışsın
    If WinActive("ProSoccerOnline") {
        if macroActive {
            while GetKeyState("LButton", "P") && macroActive {
                Click("Down")
                Sleep(50)
                Click("Up")
                Sleep(50)
            }
        }
    }
    return
}

; === Sürekli Kontrol Eden Timer ===
SetTimer(CheckKeyStates, 10)
CheckKeyStates() {
    ; Gelişmiş kontrol için yer
}

; === GUI Güncelleme ===
UpdateGUI() {
    global macroActive, guiText, guiStatus, guiVKey
    icon := guiStatus["StatusIcon"]

    if macroActive {
        guiText.Text := "L Dribbling: On"
        guiText.SetFont("cLime")
        icon.Text := "●"
        icon.SetFont("cLime")
        guiVKey.SetFont("cLime")  ; guiVKey'in rengi yeşil
    } else {
        guiText.Text := "L Dribbling: Off"
        guiText.SetFont("cRed")
        icon.Text := "●"
        icon.SetFont("cRed")
        guiVKey.SetFont("cRed")  ; guiVKey'in rengi kırmızı
    }
}

FadeIn(hwnd) {
    Loop 10 {
        WinSetTransparent(20 * A_Index, hwnd)
        Sleep(20)
    }
}

; === Hotkey Ayarı ===
SetHotkey() {
    global macroToggleKey, toggleHotkey
    try {
        if toggleHotkey
            Hotkey(toggleHotkey, "Off")
    }
    toggleHotkey := "*" . macroToggleKey
    Hotkey(toggleHotkey, ToggleMacro, "On")
}

; === Makroyu Aç/Kapat ===
ToggleMacro(*) {
    global macroActive
    macroActive := !macroActive
    UpdateGUI()
}

; === Köşe Yuvarlamak === 
RoundCorners(hwnd, radius := 16) {
    width := 260
    height := 45
    hRgn := DllCall("CreateRoundRectRgn", "int", 0, "int", 0, "int", width, "int", height, "int", radius, "int", radius, "ptr")
    DllCall("SetWindowRgn", "ptr", hwnd, "ptr", hRgn, "int", true)
}
