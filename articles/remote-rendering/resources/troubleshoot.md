---
title: Hibaelhárítás
description: Az Azure távoli rendereléssel kapcsolatos hibaelhárítási információk
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 59dc64c952aab6b37e6a779ab1e7e85b9a8ab4b7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018820"
---
# <a name="troubleshoot"></a>Hibaelhárítás

Ezen a lapon az Azure távoli renderelést akadályozó gyakori problémák és a megoldási módszerek találhatók.

## <a name="cant-link-storage-account-to-arr-account"></a>A Storage-fiók nem csatolható ARR-fiókhoz

Néha a [Storage-fiók összekapcsolása](../how-tos/create-an-account.md#link-storage-accounts) során a távoli renderelési fiók nem szerepel a listáján. A probléma megoldásához nyissa meg a Azure Portal ARR-fiókját, és válassza az **identitás** elemet a bal oldali **Beállítások** csoportban. Győződjön **meg**arról, hogy az **állapot** beállítása be értékre van állítva.
![Unity frame Debugger](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Az ügyfél nem tud csatlakozni a kiszolgálóhoz

Győződjön meg arról, hogy a tűzfalak (az eszközökön belül, az útválasztók stb.) nem blokkolja a következő portokat:

* **50051 (TCP)** – a kezdeti kapcsolathoz szükséges (http-kézfogás)
* **8266 (TCP + UDP)** – adatátvitelhez szükséges
* **5000 (TCP**), **5433 (tcp)**, **8443 (TCP)** – szükséges a [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Hiba: " `Disconnected: VideoFormatNotAvailable` "

Győződjön meg arról, hogy a GPU támogatja a hardveres videó-dekódolást. Lásd: [fejlesztői számítógép](../overview/system-requirements.md#development-pc).

Ha két GPU-val rendelkező laptopon dolgozik, akkor előfordulhat, hogy az alapértelmezés szerint futó GPU nem biztosít hardveres videó-dekódolási funkciót. Ha igen, próbálja meg kényszeríteni az alkalmazást, hogy használja a többi GPU-t. Ez gyakran lehetséges a GPU illesztőprogram-beállításaiban.

## <a name="h265-codec-not-available"></a>A H265-kodek nem érhető el

Két oka lehet annak, hogy a kiszolgáló elutasítja a hibával való kapcsolódást `codec not available` .

**Nincs telepítve a H265-kodek:**

Először győződjön meg arról, hogy a rendszerkövetelmények között a [szoftver](../overview/system-requirements.md#software) szakaszban említettek szerint telepíti a **HEVC video Extensions** szolgáltatást.

Ha továbbra is problémákba ütközik, győződjön meg arról, hogy a videokártya támogatja a H265, és hogy telepítve van a legújabb grafikus illesztőprogram. Tekintse meg a gyártó által megadott információk rendszerkövetelményeit a [fejlesztői számítógépekről](../overview/system-requirements.md#development-pc) szóló szakaszban.

**A kodek telepítve van, de nem használható:**

A probléma oka a DLL-ek helytelen biztonsági beállítása. Ez a probléma nem jelenik meg a H265-mel kódolt videók megtekintésére tett kísérlet során. A kodek újratelepítése nem oldja meg a problémát. Ehelyett hajtsa végre a következő lépéseket:

1. Nyisson meg egy **rendszergazdai jogosultságokkal rendelkező PowerShellt** , és futtassa a parancsot

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    A parancsnak a következőhöz hasonló módon kell kiadnia a `InstallLocation` kodeket:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. A mappa megnyitása a Windows Intézőben
1. Egy **x86** -os és egy **x64** -es almappanek kell lennie. Kattintson a jobb gombbal az egyik mappára, és válassza a **Tulajdonságok** lehetőséget.
    1. Válassza a **Biztonság** fület, és kattintson a **speciális** beállítások gombra.
    1. Kattintson a **tulajdonos** **módosítása** elemre.
    1. Írja be a **rendszergazdákat** a szövegmezőbe
    1. Kattintson **a Névellenőrzés** elemre, majd **az OK gombra** .
1. Ismételje meg a fenti lépéseket a másik mappára vonatkozóan
1. Ismételje meg a fenti lépéseket is az egyes DLL-fájlokon mindkét mappában. Összesen négy dll-fájlnak kell lennie.

Annak ellenőrzéséhez, hogy a beállítások helyesek-e, tegye a következőket a négy dll-fájlhoz:

1. Válassza a **tulajdonságok > biztonsági > szerkesztés** lehetőséget
1. Lépjen végig az összes **csoport/felhasználó** listáján, és ellenőrizze, hogy mindegyik rendelkezik-e az **olvasási & végrehajtásának** megfelelő készlettel (az **Engedélyezés** oszlopban jelölje be a jelölőnégyzetet)

## <a name="low-video-quality"></a>Alacsony minőségű videó

A videó minősége a hálózati minőség vagy a hiányzó H265-videó kodekkel is feltörhető.

* Tekintse meg a [hálózati problémák azonosításához](#unstable-holograms)szükséges lépéseket.
* Tekintse meg a legújabb grafikus illesztőprogram telepítéséhez [szükséges rendszerkövetelményeket](../overview/system-requirements.md#development-pc) .

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>A MRC-mel rögzített videó nem tükrözi az élő élmény minőségét

A Hololens [vegyes valóság-rögzítéssel (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)rögzíthet videót. Az eredményül kapott videó azonban rosszabb minőségben működik, mint az élő élmény, két okból:
* A videó frameráta 30 Hz-re van korlátozva, szemben a 60 Hz-es értékkel.
* A videó-lemezképek nem haladnak át a [késői szakasz újravetítésének](../overview/features/late-stage-reprojection.md) feldolgozásának lépésén, így a videó choppier válik.

Mindkettő a rögzítési technikában rejlő korlátozásokat is magában foglalhat.

## <a name="black-screen-after-successful-model-loading"></a>A sikeres modell betöltését követő fekete képernyő

Ha a renderelési futtatókörnyezethez csatlakozik, és sikeresen betöltötte a modellt, de ezt követően csak fekete képernyő jelenik meg, akkor ez néhány különböző okot is tartalmazhat.

Javasoljuk, hogy a részletes elemzés előtt tesztelje a következő dolgokat:

* Telepítve van a H265-kodek? Bár a H264 kodeknek tartaléknak kell lennie, láttuk azokat az eseteket, amelyekben ez a tartalék nem működött megfelelően. Tekintse meg a legújabb grafikus illesztőprogram telepítéséhez [szükséges rendszerkövetelményeket](../overview/system-requirements.md#development-pc) .
* Ha Unity-projektet használ, zárjuk be az egységet, törölje az ideiglenes *könyvtárat* és az *obj* -mappákat a projekt könyvtárában, majd töltse be/hozza létre újra a projektet. Bizonyos esetekben a gyorsítótárazott adat miatt a minta nem működik megfelelően a nyilvánvaló ok nélkül.

Ha ez a két lépés nem segített, meg kell állapítani, hogy az ügyfél fogad-e képkockákat. Ez programozott módon kérdezhető le a [kiszolgálóoldali teljesítmény-lekérdezések](../overview/features/performance-queries.md) című fejezetben leírtak szerint. A `FrameStatistics struct` tartalmaz egy tagot, amely azt jelzi, hogy hány képkockát fogadtak. Ha ez a szám nagyobb, mint 0, és az idő múlásával növekszik, az ügyfél megkapja a tényleges képkockákat a kiszolgálóról. Ennek következtében az ügyfél oldalán problémának kell lennie.

### <a name="common-client-side-issues"></a>Gyakori ügyféloldali problémák

**A modell meghaladja a kiválasztott virtuális gép korlátait, pontosabban a sokszögek maximális számát:**

Lásd a virtuálisgép- [méretek bizonyos korlátozásait](../reference/limits.md#overall-number-of-polygons).

**A modell nem a kamera csonkakúpot belül található:**

Sok esetben a modell helyesen jelenik meg, de a kamera csonkakúpot kívül található. Ennek gyakori oka az, hogy a modellt egy távoli középpontú kimutatással exportálták, így azt a kamera távoli nyírási síkja vágja le. Segít lekérdezni a modell határoló mezőjét programozott módon, és megjelenítheti a négyzetet az egységgel, vagy kinyomtathatja az értékeket a hibakeresési naplóba.

Továbbá a konverziós folyamat létrehoz egy [kimeneti JSON-fájlt](../how-tos/conversion/get-information.md) a konvertált modellel együtt. A modell pozicionálási problémáinak hibakereséséhez érdemes megtekinteni a `boundingBox` [outputStatistics szakaszban](../how-tos/conversion/get-information.md#the-outputstatistics-section)található bejegyzést:

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

A határolókeret a következőként van leírva: a `min` és a `max` pozíciója 3D térben, méterben. Így a 1000,0-es koordináta azt jelenti, hogy a forrás 1 kilométer távolságra van.

Ennek a határolókeretnak két problémája lehet, amely láthatatlan geometriát eredményez:
* **A mező távolról is elvégezhető**, így az objektum teljes kivágása a sík kivágása miatt történik. Ebben az esetben a következőhöz `boundingBox` hasonló értékek jelennek meg: az `min = [-2000, -5,-5], max = [-1990, 5,5]` x tengely nagy eltolásának használata példaként. Az ilyen típusú probléma megoldásához engedélyezze a `recenterToOrigin` [modell átalakítási konfigurációjában](../how-tos/conversion/configure-model-conversion.md)a beállítást.
* **A Box lehet középre igazított, de a nagyságrenddel túl nagy**. Ez azt jelenti, hogy bár a kamera a modell közepén indul el, a geometriája minden irányban kikerül. Ebben az esetben a tipikus `boundingBox` értékek a következőképpen néznek ki: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Az ilyen típusú probléma oka általában az egység méretezési eltérése. A kiegyenlítéshez az átalakítás során meg kell adnia egy [skálázási értéket](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) , vagy a megfelelő egységekkel kell megadnia a forrás modellt. A skálázás a legfelső szintű csomópontra is alkalmazható a modell futásidőben történő betöltésekor.

**Az Unity Render folyamat nem tartalmazza a renderelési horgokat:**

Az Azure Remote rendering összekapcsolja az egység renderelési folyamatát, hogy elvégezze a képkockák összeállítását a videóval, és hogy elvégezze az újravetítést. Ha szeretné ellenőrizni, hogy ezek a hookok léteznek-e, nyissa meg a menüt *:::no-loc text="Window > Analysis > Frame debugger":::* . Engedélyezze, és győződjön meg arról, hogy a folyamat két bejegyzéssel rendelkezik `HolographicRemotingCallbackPass` :

![Unity frame Debugger](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>A távoli renderelési API-t használó Unity kód nem fordítható le

### <a name="use-debug-when-compiling-for-unity-editor"></a>Az Unity Editor fordításakor használja a hibakeresést

Állítsa át az Unity megoldás *Build típusát* a **hibakereséshez**. A Unity Editorban az ARR tesztelésekor a define parancs `UNITY_EDITOR` csak a "debug" buildek esetében érhető el. Vegye figyelembe, hogy ez nem kapcsolódik a [központilag telepített alkalmazásokhoz](../quickstarts/deploy-to-hololens.md)használt Build típushoz, ahol a "Release" buildeket érdemes előnyben részesíteni.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Hibák fordítása a 2. HoloLens Unity-minták fordításakor

Hamis hibák történtek, amikor az egységbeli mintákat (gyors üzembe helyezési útmutató, ShowCaseApp,..) próbálta lefordítani a 2. HoloLens. A Visual Studio arra panaszkodik, hogy nem tud fájlokat másolni, noha ott vannak. Ha a probléma a következő:
* Távolítsa el az összes ideiglenes Unity-fájlt a projektből, és próbálkozzon újra. Az egység bezárásához törölje az ideiglenes *könyvtárat* és az *obj* -mappákat a projekt könyvtárában, majd töltse be vagy hozza létre újra a projektet.
* Győződjön meg arról, hogy a projektek a lemez egy olyan könyvtárában találhatók, amely ésszerűen rövid elérési úttal rendelkezik, mert a másolási lépés néha úgy tűnik, hogy hosszú fájlnevekkel problémákba ütköznek.
* Ha ez nem segít, előfordulhat, hogy az MS Sense ütközik a másolási lépéssel. Kivétel beállításához futtassa ezt a beállításjegyzék-parancsot a parancssorból (rendszergazdai jogosultságok szükségesek):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Instabil Hologramok

Ha úgy tűnik, hogy a megjelenített objektumok a fej mozgásával együtt mozognak, előfordulhat, hogy a *késői fázis-újravetítéssel* (LSR) kapcsolatos problémák merülhetnek fel. Az ilyen helyzetek megközelítésével kapcsolatos útmutatásért tekintse meg a [késői fázisok újravetítésének](../overview/features/late-stage-reprojection.md) szakaszát.

Az instabil Hologramok (ingadozó, hajlítási, vibrálás vagy jumping Hologramok) egy másik oka lehet gyenge hálózati kapcsolat, különösen a nem megfelelő hálózati sávszélesség vagy túl nagy késés. A hálózati kapcsolatok minőségének jó mutatója a [teljesítmény statisztika](../overview/features/performance-queries.md) értéke `ARRServiceStats.VideoFramesReused` . Az újrafelhasznált keretek olyan helyzeteket jeleznek, amikor egy régi videó-keretet újra fel kell használni az ügyféloldali oldalon, mert nem volt elérhető új videó keret – például a csomagok elvesztése miatt vagy a hálózati késésben lévő változatok miatt. Ha a `ARRServiceStats.VideoFramesReused` értéke gyakran nagyobb nullánál, ez hálózati problémát jelez.

Egy másik érték a következő: `ARRServiceStats.LatencyPoseToReceiveAvg` . Következetesen 100 MS alá kell esnie. Ha magasabb értékeket lát, ez azt jelzi, hogy egy túl távol lévő adatközponthoz csatlakozik.

A lehetséges enyhítések listáját a [hálózati kapcsolatra vonatkozó irányelvek](../reference/network-requirements.md#guidelines-for-network-connectivity)című részben tekintheti meg.

## <a name="next-steps"></a>További lépések

* [Rendszerkövetelmények](../overview/system-requirements.md)
* [A hálózatra vonatkozó követelmények](../reference/network-requirements.md)
