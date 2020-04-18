---
title: Hibaelhárítás
description: Az Azure távoli rendereléssel kapcsolatos információk hibáinak elhárítása
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: ac7e721a863414cf0617177885e0ff1c9e9a35d4
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617868"
---
# <a name="troubleshoot"></a>Hibaelhárítás

Ez a lap felsorolja az Azure távoli renderelést zavaró gyakori problémákat, és azok megoldásának módjait.

## <a name="client-cant-connect-to-server"></a>Az ügyfél nem tud csatlakozni a kiszolgálóhoz

Győződjön meg arról, hogy a tűzfalak (eszközön, útválasztókon belül stb.) nem blokkolják a következő portokat:

* **50051 (TCP)** – a kezdeti kapcsolathoz szükséges (HTTP kézfogás)
* **8266 (TCP+UDP)** – adatátvitelhez szükséges
* **5000 (TCP),** **5433 (TCP),** **8443 (TCP)** - szükséges [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Hiba: "Leválasztva: VideoFormatNotAvailable"

Ellenőrizze, hogy a GPU támogatja-e a hardveres videodekódolást. Lásd: [Fejlesztő ipc](../overview/system-requirements.md#development-pc).

Ha két GPU-val rendelkező laptopon dolgozik, lehetséges, hogy az alapértelmezés szerint futtatott GPU nem biztosít hardveres videodekódolási funkciót. Ha igen, próbálja meg kényszeríteni az alkalmazást a másik GPU használatára. Ez gyakran lehetséges a GPU-illesztőprogram beállításaiban.

## <a name="h265-codec-not-available"></a>A H265 kodek nem érhető el

Két oka lehet annak, hogy a kiszolgáló nem hajlandó csatlakozni egy **olyan kodekhez,** amely nem érhető el.

**A H265 kodeket nincs telepítve:**

Először győződjön meg róla, hogy telepítse a **HEVC videobővítményeket,** ahogy azt a rendszerkövetelmények [Szoftver](../overview/system-requirements.md#software) részében említettük.

Ha továbbra is problémákat tapasztal, győződjön meg arról, hogy a grafikus kártya támogatja a H265-öt, és a legújabb grafikus illesztőprogram van telepítve. A gyártóspecifikus információkat a rendszerkövetelmények [Fejlesztési PC](../overview/system-requirements.md#development-pc) részében talál.

**A kodek telepítve van, de nem használható:**

A probléma oka a DL-ek helytelen biztonsági beállítása. Ez a probléma nem jelentkezik, amikor a H265-tel kódolt videókat próbál meg nézni. A kodek újratelepítése sem oldja meg a problémát. Ehelyett hajtsa végre a következő lépéseket:

1. Rendszergazdai **jogosultságokkal rendelkező PowerShell** megnyitása és futtatás

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Ez a `InstallLocation` parancs kell kimeneti a codec, valami ilyesmi:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. A mappa megnyitása a Windows Intézőben
1. Legyen egy **x86** és egy **x64** almappa. Kattintson a jobb gombbal az egyik mappára, és válassza a **Tulajdonságok parancsot**
    1. Válassza a **Biztonság** lapot, és kattintson a **Speciális** beállítások gombra
    1. Kattintson a **Tulajdonos módosítása** **gombra.**
    1. Írja be **a Rendszergazdák szövegmezőbe** a szövegmezőbe
    1. Kattintson **a Nevek ellenőrzése** és az OK **gombra**
1. A fenti lépések megismétlése a másik mappához
1. Ismételje meg a fenti lépéseket is minden DLL fájlon mindkét mappában. Összesen négy DL-nek kell lennie.

Annak ellenőrzéséhez, hogy a beállítások most helyesek-e, tegye ezt a négy DL-re vonatkozóan:

1. **Tulajdonságok > biztonsági > szerkesztés e**
1. Nézze át az összes **csoport / felhasználó listáját,** és győződjön meg róla, hogy mindegyikrendelkezik az **Olvasás & A végrehajtás** jobb oldali beállítással rendelkezik (az **allow** oszlop bejelölését be kell jelölni)

## <a name="low-video-quality"></a>Gyenge videominőség

A videó minősége a hálózat minősége vagy a hiányzó H265 video kodeket veszélyezteti.

* A hálózati [problémák azonosításának lépései.](#unstable-holograms)
* Tekintse meg a legújabb grafikus illesztőprogram telepítésének [rendszerkövetelményeit.](../overview/system-requirements.md#development-pc)

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Az MRC-vel rögzített videó nem tükrözi az élő élmény minőségét

A hololensen a [Mixed Reality Capture (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)segítségével lehet videót rögzíteni. Az eredményül kapott videó azonban két okból is rosszabb minőségű, mint az élő élmény:
* A videoframerate 30 Hz-en van lezárva, szemben a 60 Hz-es szinttel.
* A videó képek nem megy keresztül a [késői szakaszban reprojection](../overview/features/late-stage-reprojection.md) feldolgozási lépés, így a videó úgy tűnik, hogy choppier.

Mindkettő a rögzítési technika eredendő korlátai.

## <a name="black-screen-after-successful-model-loading"></a>Fekete képernyő a sikeres modellbetöltés után

Ha csatlakozik a renderelési futásidőhöz, és sikeresen betöltött egy modellt, de csak egy fekete képernyőt lát utána, akkor ennek néhány különböző oka lehet.

Javasoljuk, hogy alaposabb elemzés előtt tesztelje a következő dolgokat:

* Telepítve van a H265 kodeket? Bár a H264 kodeknek vissza kell lépnie, láttunk olyan eseteket, amikor ez a tartalék nem működött megfelelően. Tekintse meg a legújabb grafikus illesztőprogram telepítésének [rendszerkövetelményeit.](../overview/system-requirements.md#development-pc)
* Unity-projekt használataesetén zárja be a Unity programot, törölje az ideiglenes *könyvtárat* és az *obj* mappákat a projekt könyvtárában, és töltse be/építse fel újra a projektet. Egyes esetekben a gyorsítótárazott adatok miatt a minta nem működik megfelelően minden nyilvánvaló ok nélkül.

Ha ez a két lépés nem segített, meg kell tudni, hogy az ügyfél megkapja-e a videoképkockákat. Ez programozott módon kérdezhető le a [kiszolgálóoldali teljesítménylekérdezésekről](../overview/features/performance-queries.md) szóló fejezetben leírtak szerint. A `FrameStatistics struct` tag azt jelzi, hogy hány videoképkocka érkezett. Ha ez a szám nagyobb, mint 0, és idővel növekszik, az ügyfél tényleges videoképkockákat kap a kiszolgálótól. Következésképpen ez a probléma az ügyféloldalon.

### <a name="common-client-side-issues"></a>Gyakori ügyféloldali problémák

**A modell nem a nézeten belül frustum:**

Sok esetben a modell helyesen jelenik meg, de kívül található a kamera frustum. Ennek gyakori oka, hogy a modellt egy távoli középre fordítható sarkalokkal exportálták, így a kamera messze vágósíkja vágja le. Segít a modell határolókeretének programozott lekérdezésében, és a Unity mező vonalmezőként való megjelenítésében, vagy az értékek kinyomtatásában a hibakeresési naplóba.

**A Unity renderelési folyamat nem tartalmazza a renderelési horgokat:**

Az Azure remote rendering hooks a Unity renderelési folyamat a keret összetételét a videóval, és az újravetítés. A horgok létezésének ellenőrzéséhez nyissa meg az *Ablak > Elemzés > kerethibakereső menüt.* Engedélyezze, és győződjön meg `HolographicRemotingCallbackPass` arról, hogy két bejegyzés van a folyamatban:

![Unity kerethiba-kereső](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>A távoli renderelési API-t használó Egységkód nem fordít

Váltson az Unity megoldás *buildtípusára* a **Debug**. Az ARR-t az Unity `UNITY_EDITOR` szerkesztőben tesztelve a meghatározás csak a "Debug" buildekben érhető el. Vegye figyelembe, hogy ez nem kapcsolódik az [üzembe helyezett alkalmazásokhoz](../quickstarts/deploy-to-hololens.md)használt buildtípushoz, ahol a "Release" buildeket kell előnyben részesíteni.

## <a name="unstable-holograms"></a>Instabil hologramok

Abban az esetben, ha úgy tűnik, hogy a renderelt objektumok a fejmozgásokkal együtt mozognak, problémákmerülhetnek fel a *késői szakaszújravetítéssel* (LSR). Az ilyen helyzet megközelítésével kapcsolatos útmutatásért tekintse meg a [késői szakasz átvetítéséről](../overview/features/late-stage-reprojection.md) szóló szakaszt.

Az instabil hologramok (ingadozó, hajlító, jittering vagy ugró hologramok) másik oka lehet a rossz hálózati kapcsolat, különösen az elégtelen hálózati sávszélesség vagy a túl nagy késleltetés. A hálózati kapcsolat minőségének jó mutatója a [teljesítménystatisztika](../overview/features/performance-queries.md) értéke. `ARRServiceStats.VideoFramesReused` Az újrafelhasznált keretek olyan helyzeteket jelölnek, amikor egy régi videokeretet újra fel kellett használni az ügyféloldalon, mert nem állt rendelkezésre új videoképkocka – például a csomagvesztés vagy a hálózati késés változásai miatt. Ha `ARRServiceStats.VideoFramesReused` gyakran nagyobb, mint nulla, ez hálózati problémára utal.

Egy másik érték, `ARRServiceStats.LatencyPoseToReceiveAvg`hogy nézd meg is . Következetesen 100 ms alatt kell lennie. Ha magasabb értékeket lát, az azt jelzi, hogy túl távoli adatközponthoz csatlakozik.

A lehetséges megoldások listáját a [hálózati kapcsolatra vonatkozó irányelvekben tetszési útmutatóban tetszései között található.](../reference/network-requirements.md#guidelines-for-network-connectivity)

## <a name="next-steps"></a>További lépések

* [Rendszerkövetelmények](../overview/system-requirements.md)
* [A hálózatra vonatkozó követelmények](../reference/network-requirements.md)
