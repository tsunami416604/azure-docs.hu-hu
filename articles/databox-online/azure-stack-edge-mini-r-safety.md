---
title: Azure Stack Edge mini R biztonsági útmutatója | Microsoft Docs
description: Ismerteti a biztonsági konvenciókat, az irányelveket, a szempontokat és ismerteti a Azure Stack Edge mini R-eszköz biztonságos telepítését és üzemeltetését.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466687"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge mini R biztonsági utasítások

![1. figyelmeztetési ikon – ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ a biztonsági értesítés ikonjának olvasása ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **– biztonsági és egészségvédelmi információk**

Olvassa el az ebben a cikkben található összes biztonsági információt, mielőtt a Azure Stack Edge mini R-eszközt használja, amely egy teleptöltöttség-csomag, egy AC/DC-beli áramforrás, egy modul-adapter és egy kiszolgálói modul. Az utasítások követésének elmulasztása tüzet, áramütést, sérüléseket vagy a tulajdonságok sérülését eredményezheti. Az Azure Stack Edge mini R használata előtt olvassa el az összes biztonsági információt alább.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciói

A következő jelzési szavak a veszélyforrások riasztási jelei:

| Ikon | Description |
|:--- |:--- |
| ![Kockázati szimbólum](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Veszély:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. <br> **Figyelmeztetés:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. <br> **Vigyázat:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – kisebb vagy mérsékelt sérülést eredményezhet.|
|

Az Azure Stack Edge mini R-eszköz beállításakor és futtatásakor a következő veszélyforrási ikonokat kell figyelembe venni:

| Ikon | Description |
|:--- |:--- |
| ![Először olvassa el az összes utasítást](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Először olvassa el az összes utasítást |
| ![Kockázati szimbólum](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Kockázati szimbólum |
| ![Áramütés ikon](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Áramütés veszélye |
| ![Csak beltéri használatra](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Csak beltéri használatra |
| ![Nincs felhasználó által szervizelhető rész ikon](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Nincsenek felhasználó által szervizelhető részek. A megfelelő képzés hiányában ne legyen hozzáférés. |
|

## <a name="handling-precautions-and-site-selection"></a>Az óvintézkedések és a hely kijelölésének feldolgozása

Az Azure Stack Edge mini R-eszköz a következő kezelési óvintézkedésekkel és hely-kiválasztási feltételekkel rendelkezik:

![Figyelmeztetés ikon 2 az áramütés ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ikonja nem rendelkezik a ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ felhasználó által szervizelhető részek ikonjának figyelmeztetésével ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* A *kapott* eszköz bevizsgálása károkért. Ha az eszköz bekerítése sérült, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-placeholder.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz üzemeltetését.
* Ha azt gyanítja, hogy az eszköz meghibásodik, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-placeholder.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz kiszolgálását.
* Az eszköz nem tartalmaz felhasználó által szervizelhető részt. A veszélyes feszültség, a jelenlegi és az energia szintje belül van. Ne nyissa meg. Az eszköz visszaküldése a Microsoftnak a karbantartáshoz.

![Figyelmeztető ikon 3 figyelmeztetés ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

A rendszer üzemeltetése ajánlott:

* A hő forrása, beleértve a közvetlen napfényt és a fűtőtesteket.
* A nem nedvességnek vagy csapadéknak kitett helyen.
* Egy olyan térben található, amely a vibrációt és a fizikai sokkot is lekicsinyíti.  A rendszer a MIL-STD-810G szerint a sokk és a vibráció számára lett kialakítva.
* Elszigetelten a villamossági eszközök által előállított erős elektromágneses mezőkből.
* Ne engedélyezzen semmilyen folyadékot vagy idegen objektumot a rendszeren való belépéshez. Ne helyezzen italokat vagy más folyadék-tárolókat a rendszeren vagy annak közelében.

![Figyelmeztetés ikon 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ nincs felhasználó által szervizelhető rész ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Figyelmeztetés:**

* Ez a berendezés lítium-akkumulátort tartalmaz. Ne kísérelje meg az akkumulátor-csomag karbantartását. A berendezésben lévő elemek nem a felhasználó által szervizelhető. A robbanás kockázata, ha az akkumulátort helytelen típus váltja fel.

![5 figyelmeztető ikon figyelmeztetése ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

Csak akkor számítson fel díjat az akkumulátornak, ha az Azure Stack Edge mini R-eszköz részét képezi, nem számít külön eszköznek.

![6. figyelmeztetési ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **– Figyelmeztetés:**

* Az akkumulátor be-és kikapcsolási kapcsolója az akkumulátor csak a kiszolgálói modulnak való kitöltésére szolgál. Ha a Power adapter csatlakoztatva van az akkumulátor-csomaghoz, a rendszer akkor is továbbítja a teljesítményt a kiszolgálói modulnak, ha a kapcsoló ki van kapcsolva.

![Figyelmeztető ikon 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **. figyelmeztetés:**

* Ne égesse el vagy rövidzárlat nélkül az akkumulátoros csomagot. Ennek megfelelően újra kell telepíteni vagy ártalmatlanítani.

![Figyelmeztetési ikon 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **– Vigyázat:**

* Ha a megadott AC/DC tápegységet használja, akkor a rendszer az 2590-as típusú mezőt is használhatja. Ebben az esetben a végfelhasználónak ellenőriznie kell, hogy az megfelel-e az összes vonatkozó biztonsági, szállítási, környezeti és egyéb nemzeti/helyi előírásnak.
* Ha a rendszer 2590-as típusú akkumulátorral működik, az akkumulátor gyártója által megadott használati feltételeken belül hajtsa végre az akkumulátort.

![Figyelmeztető ikon 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **. figyelmeztetés:**

Ez az eszköz két SFP + portot tartalmaz, amelyek optikai adó-vevővel is használhatók. A veszélyes lézeres sugárzás elkerülése érdekében csak az 1. osztályba tartozó adóvevőket használja.

## <a name="electrical-precautions"></a>Elektromos óvintézkedések

Az Azure Stack Edge mini R-eszköz a következő elektromos óvintézkedésekkel rendelkezik:

![Figyelmeztetés ikon 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ elektromos áramütés ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Figyelmeztetés:**

Tápegység-adapterrel való használat esetén:

* Biztonságos elektromos földi csatlakozást biztosít a tápkábelhez. A váltakozó áramú (AC) kábel három vezetékes (egy földelt PIN-kódot tartalmazó) csatlakozóval rendelkezik. Ez a beépülő modul csak egy megalapozott hálózati konnektorhoz illeszkedik. Ne győzje le a földelő PIN-kód célját.
* Mivel a tápkábel csatlakoztatása a fő leválasztási eszköz, győződjön meg arról, hogy a szoftvercsatorna-kimenetek az eszköz közelében találhatók, és könnyen elérhetők.
* Bontsa ki a tápkábel (eke) t (a dugasz húzásával, ne a kábelt), és válassza le az összes kábelt, ha az alábbi feltételek bármelyike fennáll:

  * A tápkábel vagy a beépülő modul kikopott vagy más módon sérült lesz.
  * Az eszköz az eső, a felesleges nedvesség vagy más folyadékok számára van kitéve.
  * Az eszköz el lett dobva, és az eszköz burkolata sérült.
  * Azt gyanítja, hogy az eszköznek szolgáltatásra vagy javításra van szüksége.
* Véglegesen kihúzza az egységet, mielőtt áthelyezi, vagy ha úgy gondolja, hogy bármilyen módon megsérült.

* Adjon meg egy megfelelő áramforrást elektromos túlterhelés elleni védelemmel a következő energiaellátási követelmények teljesítéséhez:

* Feszültség: 100 – 240 v AC
* Jelenlegi: 1,7 amper
* Gyakoriság: 50 – 60 Hz

![Figyelmeztetés ikon 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektromos áramütés ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Figyelmeztetés:**

* Ne kísérelje meg a nem a berendezéshez biztosított hálózati adapterek (ok) módosítását vagy használatát.

![Figyelmeztető ikon 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ – az elektromos áramütés ikonja ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ csak beltéri használatra ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **figyelmeztető:**

* A szimbólummal jelölt tápegység csak beltéri használatra van minősítve.

## <a name="regulatory-information"></a>Szabályozási információk

Az alábbi szabályok a Azure Stack Edge mini R-eszköz, a szabályozási modell száma: TMA01 című témakörben találhatók.

Az Azure Stack Edge mini R-eszköz úgy van kialakítva, hogy a felsorolt NRTL (UL, CSA, ETL stb.), valamint az IEC/EN 60950-1 vagy az IEC/EN 62368-1 kompatibilis (CE jelölésű) információtechnológiai berendezésekkel használható legyen.

Az Egyesült Államokban és Kanadában kívüli országokban a hálózati kábelek (a berendezéshez nem biztosított) nem telepíthetők ezzel a berendezéssel, ha a hossza nagyobb, mint 3 méter.

A berendezés a következő környezetekben való működésre lett tervezve:

| Környezet | Specifikációk |
|:---  |:--- |
| Rendszerhőmérséklet-specifikációk | <ul><li>Tárolási hőmérséklet: – 20 &deg; c – 50 &deg; c (– 4 &deg; F-122 &deg; f)</li><li>Folyamatos művelet: 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; f)</li></ul> |
| Relatív páratartalom (RH) előírásai | <ul><li>Tárolás: 5% – 95% relatív páratartalom</li><li>Üzemeltetés: 10% – 90% relatív páratartalom</li></ul>|
| Maximális magassági specifikációk | <ul><li>Üzemeltetés: 15 000 méter (4 572 méter)</li><li>Nem működő: 40 000 méter (12 192 méter)</li></ul>|

> ![Értesítési ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Megjegyzés:** &nbsp; a Microsoft által kifejezetten nem jóváhagyott berendezések módosításai vagy módosításai érvénytelenítik a felhasználó hatóságát a berendezés üzemeltetéséhez.

Kanada és USA:

FIGYELJE meg, hogy a rendszer tesztelte a készüléket, és úgy találta, hogy megfelel az "a" osztályú digitális eszközök korlátainak, az FCC-szabályok 15. része alapján. Ezek a korlátok úgy vannak kialakítva, hogy ésszerű védelmet biztosítanak a káros interferenciák ellen, ha a berendezés kereskedelmi környezetben üzemel. Ez a berendezés a rádiófrekvenciás energia előállítására, felhasználására és kisugárzására szolgál, és ha nincs telepítve és használatban a használati utasítással összhangban, a rádiófrekvenciás kommunikáció káros hatással lehet. A berendezésnek a lakossági környezetben való működése valószínűleg káros interferencia okoz, amely esetben a felhasználónak a saját költségén kell kijavítania a beavatkozást.

Ez az eszköz megfelel az FCC-szabályok és az iparági kanadai licencek alól mentesített RSS-szabványoknak. A művelet a következő két feltétel hatálya alá esik: (1) Ez az eszköz nem okoz káros interferenciát, és (2) az eszköznek el kell fogadnia a kapott interferenciát, beleértve az interferenciát is, ami az eszköz nemkívánatos működéséhez vezethet.

![Szabályozási információ – 1. figyelmeztetés](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

A következő lehet: ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.
Egyesült Államok: (800) 426-9400 Kanada: (800) 933-4750

Európai Unió: az EU megfelelőségi nyilatkozatának másolatának kérése.

> ![Figyelmeztető ikon 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) . Ez a termék egy osztálya. Belföldi környezetben Ez a termék a rádiófrekvenciás interferencia okozhatja, amely esetben a felhasználónak szükség lehet a megfelelő intézkedések meghozatalára.

A hulladék akkuinak és elektromos és elektronikus berendezéseinek ártalmatlanítása:

![Figyelmeztetési ikon 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

A termékre vagy annak elemeire vagy csomagolására vonatkozó szimbólum azt jelenti, hogy a terméket és a benne található elemeket nem szabad a háztartási hulladékból ártalmatlanítani. Ehelyett az Ön felelőssége, hogy ezt átadja a megfelelő gyűjtési pontnak az elemek és az elektromos és elektronikus berendezések újrahasznosításához. Ez a különálló gyűjtemény és újrahasznosítás segíti a természeti erőforrások megőrzését, és megelőzheti az emberi egészségre és a környezetre vonatkozó lehetséges negatív következményeket a veszélyes anyagok lehetséges jelenléte miatt az akkumulátorok és az elektromos és elektronikus berendezések esetében, amelyeket a nem megfelelő ártalmatlanítás okozhat. Ha többet szeretne megtudni az akkumulátorok és az elektromos és elektronikus hulladékok eltávolításának helyéről, forduljon a helyi városi/helyhatósági irodához, a háztartási hulladék-ártalmatlanítási szolgáltatáshoz, illetve a terméket megvásárló áruházhoz. erecycle@microsoft.comTovábbi információért forduljon az WEEE-ről.

Ez a termék érme cella akkumulátort (IES) tartalmaz.
Microsoft Ireland Sandyford ind est Dublin D18 KX32 IRL telefon száma: + 353 1 295 3826 fax száma: + 353 1 706 4110

## <a name="next-steps"></a>További lépések

- [Felkészülés a Azure Stack Edge mini R üzembe helyezésére](azure-stack-edge-mini-r-deploy-prep.md)
