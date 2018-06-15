---
title: A StorSimple 8000 Series Update 2.2 kibocsátási megjegyzései |} Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 78be340b4a47fed88f5e8c3f5741ae7024124bd5
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057842"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>A StorSimple 8000 Series Update 2.2 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések új szolgáltatásait ismerteti, és a StorSimple 8000 Series Update 2.2 a kritikus megnyitott problémák azonosításához. Ebben a kiadásban szereplő StorSimple szoftverfrissítések listáját is tartalmaznak.

2.2-es frissítés bármely kiadásban (GA) vagy frissítés 0,1 futtató keresztül frissítés 2.1 StorSimple eszközre alkalmazhatók. Az eszköz frissítés 2.2 társított verziója 6.3.9600.17708.

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * 2.2-es frissítést csak szoftverfrissítések rendelkezik. Ez a frissítés körülbelül 1,5-2 órát vesz igénybe. 
> * Ha frissítés 2.1-es verzióját futtatja, ajánlott frissítés 2.2 minél hamarabb alkalmazni.
> * Új kiadásokban nem láthatók frissítések azonnal, mert a frissítések fázisokra bontva történő bevezetéséhez végezzük. Néhány napot várni, és a frissítések újra ezeket a majd vizsgálatát hamarosan elérhető lesz.
> 
> 

## <a name="whats-new-in-update-22"></a>2.2-es frissítés újdonságai
A következő fontos fejlesztést tartalmaz végzett frissítés 2.2.

* **Automatikus terület-visszanyerést optimalizálási** – Ha az adatok törlése a dinamikusan kiosztott kötetek, a nem használt tárolási blokkok kell kiválasztása. Ebben a kiadásban javult a terület-visszanyerést folyamat a felhőből, ami azt eredményezi, a nem használt terület ismertté váló gyorsabb a korábbi verziókhoz képest.
* **Pillanatkép készítése a teljesítményt javító** – frissítés 2.2 javult a idő pillanatkép bizonyos forgatókönyvekben, ahol nagy kötetek használatban vannak, és nincs minimális nem adatforgalommal a felhő feldolgozásához. Olyan forgatókönyvekben, amelyek ez a fejlesztés előnyös az archív kötetek lenne.
* **Támogatási csomag összegyűjtéséhez korlátozására** – a támogatási csomag összegyűjtött és ebben a kiadásban feltöltött módon fejlesztések történtek. 
* **Megbízhatóság fejlesztései frissítése** – ebben a kiadásban rendelkezik hibajavításokat tartalmaz, amelyek egy továbbfejlesztett frissítés megbízhatóságát.

## <a name="issues-fixed-in-update-22"></a>A frissítés 2.2 megoldott problémák
Az alábbi táblázatok a frissítések 2.2 és 2.1 javított problémák összegzését tartalmazza.    

| Nem | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagép-teljesítmény |A régebbi verziót, a gazdagép-oldali teljesítményproblémák megfigyelt egy helyileg rögzített kötet létrehozásakor, és egy helyileg rögzített kötet rétegzett kötet alakítása során. Ebben a kiadásban, így a kötet létrehozása és -konvertálási eljárások során az állomás teljesítmény javulása eredményezve fix ezeket a problémákat. |Igen |Nem |
| 2 |Helyileg rögzített kötetek |Ritka esetekben a rendszer akkor összeomlás egy helyileg rögzített kötet létrehozásakor. Ez a hiba kijavítása ebben a kiadásban. |Igen |Nem |
| 3 |rétegezéséhez |Nem volt, szórványos összeomlik, ha a StorSimple felhő készülékek (a 8010-es és a 8020-as modell) metaadatainak rétegzett a felhőbe. Ez a probléma fennáll ebben a kiadásban. |Nem |Igen |
| 4 |Pillanatkép létrehozása |Problémák merültek kapcsolódó növekményes pillanatképek forgatókönyvekben nagy kötetek létrehozását és minimális nem adatforgalommal számára. Ebben a kiadásban ezek a problémák kerültek. |Igen |Igen |
| 5 |Openstack hitelesítés |Használatával Openstack, a felhőbeli szolgáltatás szolgáltatója, amikor a felhasználó fog futni egy alkalomszerű hiba a hitelesítéshez kapcsolódó történő ahol összehasonlításának eredménye a JSON-elemző crash. A kijavítanak ebben a kiadásban. |Igen |Nem |
| 6 |Gazdagép-oldali másolása |Szoftver korábbi verzióiban egy alkalomszerű ODX időzítése kapcsolatos hiba fordult elő, amikor az adatok másolását egy kötet egy másik kötetre. Eredményezne. a vezérlő feladatátvétel és potenciálisan nyissa meg a rendszer sikerült helyreállítási módba. A kijavítanak ebben a kiadásban. |Igen |Nem |
| 7 |A Windows Management Instrumentation (WMI) |A szoftver korábbi verzióiban volt webes proxy hiba a következő kivétellel több példányát "<ManagementException> szolgáltató betöltési hibához". A hiba történt a WMI-memóriavesztés tulajdonítják, és most rögzített. |Igen |Nem |
| 8 |Frissítés |Bizonyos ritka esetekben a szoftver, a korábbi verzióiban a felhasználó egy "CisPowershellHcsscripterror" fogadott, ellenőrzési és a frissítések telepítése közben. Ez a probléma fennáll ebben a kiadásban. |Igen |Igen |
| 9 |Támogatási csomag |Ebben a kiadásban ahhoz, ahogy a támogatási csomag összegyűjtött és feltöltött fejlesztések történtek. |Igen |Igen |

## <a name="known-issues-in-update-22"></a>A frissítés 2.2 ismert problémák
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések / megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet kerül offline. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 4 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. Több eszköz egyetlen elhalt eszközről feladatátvétel működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. | |Igen |Nem |
| 5 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-szolgáltatások közötti kommunikáció néven érinti, és kerül, hogy az eszköz offline állapotban. Támogatási csomag is hozható létre a folyamat során fel jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe HTTP megadott protokollt. További információ: [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha konfigurált és engedélyezett a webalkalmazás-proxy regisztrált egy eszközt, majd szüksége lesz az eszköz aktív vezérlőjén újraindítására. | |Igen |Nem |
| 8 |Magas felhő késéssel és nagy i/o-munkaterhelés |Amikor a StorSimple eszköz nagyon magas felhő késések (másodperc sorrendben) és a magas i/o-munkaterhelés észlel, az eszköz kötetek csökkent állapotba, és az i/o "az eszköz nem áll készen" hiba miatt sikertelen lehet. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy egy eszköz feladatátvétel ebben a helyzetben helyreállítás végrehajtása. |Igen |Nem |
| 9 |Azure PowerShell |A StorSimple parancsmag használatakor **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - először 1 - várakozási** jelölje ki az első objektumot, így hozhat létre egy új **VolumeContainer** objektum, a parancsmag az összes található objektumokat adja vissza. |A parancsmag burkolása zárójelek között az alábbiak szerint: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - First 1 - várakozási** |Igen |Igen |
| 10 |Migrálás |Több kötet tároló áttelepítési lett átadva, az Európai legfrissebb biztonsági mentés esetén csak az első kötettároló pontos. Emellett a párhuzamos áttelepítés után az első kötettároló az első 4 biztonsági másolatok települnek indul. |Azt javasoljuk, hogy egyszerre több kötet tároló át. |Igen |Nem |
| 11 |Migrálás |A visszaállítás után kötetek nem adódnak hozzá a biztonsági mentési házirend vagy a virtuális lemez. |Szüksége lesz a biztonsági mentési házirend ahhoz, hogy készítsen biztonsági másolatot, ezek a kötetek hozzá. |Igen |Igen |
| 12 |Migrálás |Az áttelepítés befejezése után az adatsorozat 5000/7000-es eszköz nem hozzáférést kell az áttelepített adatok tárolókat. |Azt javasoljuk, hogy törölje az áttelepített adatok tárolókat, az áttelepítés befejezése és véglegesítése után. |Igen |Nem |
| 13 |Klónozás és vész-Helyreállítási |1. frissítést futtató a StorSimple eszköz nem klónozza vagy katasztrófa utáni helyreállítás 1 frissítés előtti szoftvert futtató eszközre. |Szüksége lesz a cél-eszköz frissítése 1 frissítése lehetővé ezen műveletek |Igen |Igen |
| 14 |Migrálás |Áttelepítés konfigurációs biztonsági másolat nem tud az adatsorozat 5000-7000-es eszközön, kötet csoportok nem társított kötetekkel vannak. |Nincs társított kötetek üres kötet csoportok törlése, és ismételje meg a konfigurációs biztonsági másolat. |Igen |Nem |
| 15 |Az Azure PowerShell-parancsmagok és a helyileg rögzített kötetek |Nem hozható létre egy helyileg rögzített kötet Azure PowerShell-parancsmagok használatával. (Azure PowerShell használatával hoz létre kötet lesz helyezhető el.) |Mindig a StorSimple Manager szolgáltatás segítségével konfigurálhatja a helyileg rögzített kötetekhez. |Igen |Nem |
| 16 |A helyileg rögzített kötetekhez szabad terület |Ha töröl egy helyileg rögzített kötetet, a szabad lemezterület az új kötetekre esetleg nem azonnal frissíti. A StorSimple Manager szolgáltatás körülbelül óránként frissíti a rendelkezésre álló helyi területet. |Várjon, amíg egy óraba az új kötet létrehozásakor. |Igen |Nem |
| 17 |Helyileg rögzített kötetek |A visszaállítási feladat mutatja meg az ideiglenes pillanatkép biztonsági másolatából a biztonságimásolat-katalógus, de csak a visszaállítási feladat időtartama. Emellett elérhetővé teszi a virtuális lemez csoport előtaggal rendelkező **tmpCollection** a a **biztonsági mentési házirendek** oldalon, de csak a visszaállítási feladat időtartamára. |Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik. Meg kell adni a felhasználói beavatkozás nélkül. |Igen |Nem |
| 18 |Helyileg rögzített kötetek |Ha a helyreállítási feladat megszakítása, a tartományvezérlő feladatátvétel közvetlenül ezek után jelennek meg a helyreállítási feladat **sikertelen** helyett **visszavonva**. Ha a helyreállítási feladat sikertelen lesz, a tartományvezérlő feladatátvétel közvetlenül ezek után jelennek meg a helyreállítási feladat **visszavonva** helyett **sikertelen**. |Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik. Meg kell adni a felhasználói beavatkozás nélkül. |Igen |Nem |
| 19 |Helyileg rögzített kötetek |Ha a helyreállítási feladat megszakítása vagy a visszaállítás sikertelen lesz, majd a tartományvezérlő feladatátvétel, ha egy további visszaállítási feladat jelenik meg a **feladatok** lap. |Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik. Meg kell adni a felhasználói beavatkozás nélkül. |Igen |Nem |
| 20 |Helyileg rögzített kötetek |Ha a rétegzett kötetek (létrehozott és 1.2-es frissítés a klónozott vagy korábbi) átalakítása egy helyileg rögzített kötetet próbál és az eszköz nincs elegendő szabad terület, vagy egy felhőalapú leállás van, a clone(s) is megsérült. |A probléma csak a köteteket, amelyek a létrehozott és a klónozott frissítés előtti 2.1 szoftvert. Ez egy ritka eset legyen. | | |
| 21 |Kötet átalakítás |Nem frissíthető a kötet csatlakozik, amíg folyamatban van egy kötet átalakítás ACRs (helyileg rögzített rétegzett vagy fordítva). A ACRs frissítése adatsérülést eredményezhet. |Szükség esetén frissítse a mennyiségi átalakítás előtti ACRs, és ne végezzen további ACR frissítések az átalakítás ideje alatt megszakadnak. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>A tartományvezérlő és a belső vezérlőprogram frissítési 2.2 frissítések
Ez a kiadás csak szoftveres frissítést tartalmaz. Azonban ha frissít, 2. frissítés előtt verziójából származó, szüksége lesz illesztőprogram, Storport, Spaceport, telepítéséhez és (bizonyos esetekben) az eszköz belső vezérlőprogram-frissítések lemez.

Az illesztőprogram, a Storport, a Spaceport és a belső vezérlőprogram-frissítésekre telepítéséről további információkért lásd: [2.2-es frissítés telepítése](storsimple-install-update-21.md) a StorSimple eszköz.

## <a name="virtual-device-updates-in-update-22"></a>A frissítés 2.2 virtuális eszköz frissítése
A frissítés nem alkalmazható a virtuális eszköz. Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Megtudhatja, hogyan [2.2-es frissítés telepítése](storsimple-install-update-21.md) a StorSimple eszköz.

