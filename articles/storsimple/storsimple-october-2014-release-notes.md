---
title: "A StorSimple 8000 frissítés 0,1 kibocsátási megjegyzései |} Microsoft Docs"
description: "Az új szolgáltatásokat és javításokat, nyissa meg problémák és rendelkezésre megkerülő megoldások ismerteti a 2014. októberi a Microsoft Azure StorSimple release (frissítés 0,1)."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fd35e3c3-4770-460c-999d-f72ab7053a20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 4dfd3973593a94adfc15a6e15d69c697e13998af
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>A StorSimple 8000 Series Update 0,1 kibocsátási megjegyzések – 2014. október
## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések a StorSimple 8000 Series frissítés, amely a 2014. októberi 0,1 a kritikus megnyitott problémák azonosításához. Az ebben a kiadásban szereplő StorSimple szoftver és a belső vezérlőprogram frissítés listáját is tartalmaznak. Ez az első kiadása után a StorSimple 8000 Series kiadási verzió általánosan elérhető a 2014. július történt, és szoftververzió 6.3.9600.17312 felel meg.  

Javasoljuk, hogy keressen, és alkalmazza az elérhető frissítések, az eszköz telepítését követően azonnal. Engedélyezheti az automatikus frissítések letöltése és telepítése a Microsoft fontos frissítések, amint azok kiadásakor. További információkért lásd: hogyan [a StorSimple eszköz frissítése](storsimple-update-device.md).  

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítések telepítése előtt található információkat.  

> [!IMPORTANT]
> * A StorSimple Manager szolgáltatás és a nem Windows PowerShell-lel segítségével október frissítéseinek telepítéséhez.  
> * A frissítések végrehajtásához általában körülbelül 3 órát vesz igénybe.  
> * A StorSimple. októberi kiadása nem tartalmaz a StorSimple virtuális eszköz módosításait. Továbbra is alkalmazhat, bármely elérhető Windows-frissítések, a legutóbbi biztonsági tartalmaz, de nem jelenik meg a virtuális eszköz verziója megváltozik.  
> 
> 

Győződjön meg arról, hogy a StorSimple eszköz frissítése előtt az alábbi előfeltételek teljesülését.  

* Győződjön meg arról, hogy mindkét eszközvezérlők futnak, a frissítések keresése előtt. Vagy a tartományvezérlő nem fut, ha az ellenőrzés sikertelen lesz. Győződjön meg arról, hogy a tartományvezérlők kifogástalan állapotban vannak-e, lépjen **hardverállapot** alatt a **karbantartási** lap. Ha az összetevő, amely **kezeléséről**, forduljon a Microsoft Support adatbázist.  
* Győződjön meg arról, hogy mindkét 0 vezérlő rögzített IP-címek és a vezérlő 1 k tud csatlakozni az internethez, mivel ezek az eszköz frissítéseinek karbantartásához használatosak. Használhatja a [Test-Connection parancsmag](https://technet.microsoft.com/library/hh849808.aspx) Pingelje meg egy ismert címet, például az Outlook.com-os, ellenőrizheti, hogy a tartományvezérlő rendelkezik-e a külső hálózati kapcsolattal a hálózaton kívülről.  
* Győződjön meg arról, hogy elérhetők-e a szükséges kimenő portok a StorSimple eszköz kimenő kommunikációra. További információkért lásd: a [a StorSimple eszköz hálózatkezelési követelményei](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
* Ha az eszköz szoftver verziója régebbi, mint 6.3.9600.17312 (2014. októberi frissítés), tiltsa le a Data 2 és a Data 3 portokat, ha engedélyezve van, a frissítés elkezdése előtt. Ha nem adja meg a Data 2 vagy a Data 3 portok engedélyezett, ha a frissítés telepítését, a helyreállítási módba az eszköz vezérlő okozhat. Vegye figyelembe, hogy a hálózati illesztők letiltása esetén a társított kötetek a rendszer offline állapotra, és az i/o fog működni a frissítés ideje alatt.  

## <a name="whats-new-in-the-october-release"></a>What's new in. októberi kiadása
A frissítés a következő fejlesztéseket tartalmazza:

* A StorSimple Manager szolgáltatás felhasználói felület segítségével most már a eszközvezérlők kezelheti. A felügyeleti műveletek közé tartoznak indítsa újra, Leállítás, vagy kapcsolja be a tartományvezérlő. További információkért látogasson el [kezelése StorSimple eszközvezérlők](storsimple-manage-device-controller.md).  
* A WAN sávszélesség-foglalási nap-hét a és a nap idő kombinációk szerint is ütemezheti. Ez lehetővé teszi, hogy végezze alacsony forgalmú időszakban a WAN sávszélesség hatékonyabb használatát. Különböző sávszélesség sablonok engedélyezve van a különböző kötettárolók. További információkért látogasson el [StorSimple sávszélesség sablonok kezelésére](storsimple-manage-bandwidth-templates.md).  
* E-mail értesítések proaktív értesíteni a rendszergazdájával vagy rendszergazdáival, és a meglévő vagy valószínűleg jövőbeli problémák másokat is konfigurálhat. További információkért látogasson el [riasztási beállítások megadását](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Javított. októberi kiadása
A következő táblázat összefoglalja a problémákat, amelyek a frissítés javítva lett.  

| Nem. | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Hálózati illesztők |A korábbi változatban a hálózati adapterek DATA 2 és a DATA 3 a szoftver lettek cserélve. Ez a frissítés megszüntetése. Törölje a beállításokat, és a hálózati illesztők letiltása, a frissítés telepítése előtt. A frissítés telepítése után fog konfigurációkezelővel újrakonfigurálása. |Igen |Nem |
| 2 |Támogatási csomag |A korábbi változatban, ha a Windows PowerShell próbálkozott **Export-HcsSupportPackage** parancsmag használatával kérhet le az alaplapi felügyeleti vezérlő (BMC) naplózza a művelet nem sikerült, a következő figyelmeztetéssel: "a művelet sikeres volt Ebben a vezérlőben, de a partner tartományvezérlő a következő hiba miatt sikertelen volt. Győződjön meg arról működik megfelelően-e a társ, és hogy az aktuális csomópont csatlakozni tud-e a társ." Ez a probléma fennáll most. |Igen |Nem |
| 3 |Eszköz feladatátvétel |A korábbi változatban történt egy esélye adatinkonzisztenciát, ha egy **felderítése a biztonsági mentés** feladat sikertelen volt, egy eszköz a feladatátvétel során. Ez a probléma fennáll most. |Igen |Nem |
| 4 |Eszköz feladatátvétel |Egy eszköz feladatátvétel után a korábbi változatban biztonsági mentése volt látható, de a társított kötettároló nem voltak a céleszközön. Ez a probléma fennáll most. |Igen |Nem |
| 5 |Eszköz feladatátvétel |A korábbi változatban történt hiba az enumerálás a felhő biztonsági mentések során a beállításjegyzék-visszaállítási művelet, amely adatinkonzisztenciát okozhat, ha a felhő kapcsolódási problémák merültek. |Igen |Nem |
| 6 |Belső vezérlőprogram frissítése |A korábbi változatban feladat eszköz belső vezérlőprogram frissítése sikertelen volt, és jelenik meg, hogy a parancsmagok nem volt felismerhető, és azzal, hogy a frissítés eredményeképpen sikertelen-e hibát. A vezérlő majd helyreállítási módba merült fel. Ez a probléma fennáll most. |Igen |Nem |
| 7 |Telepítés |Most már rögzített nem kívánt lemezképet készít róla megfelelően telepítése során az eszköz által okozott hibákat. |Igen |Nem |
| 8 |Gyári beállítások visszaállítása |Most már továbbléphet opcionálisan a belső vezérlőprogram ellenőrzése a gyári beállítások visszaállítása. Ez a változás a korábbi kiadásáról. |Igen |Nem |
| 9 |Gyári beállítások visszaállítása |A korábbi változatban gyári alaphelyzetbe állítása a parancsmag futtatásakor a rendszer belső vezérlőprogram verziója ellenőrzések végzett csak az egyes hardverösszetevők listáját. További belső vezérlőprogram ellenőrzi a folyamat során, így a visszaállítás sikertelen az első újraindítás után történtek. Ez a javítás biztosítja, hogy a belső vezérlőprogram ellenőrzéseket, amikor a gyári parancsmag az összes fut, és mielőtt az első rendszer újraindul. |Igen |Nem |
| 10 |Tárolási fiók kulcs Elforgatás |A **Invoke-HcsmServiceDataEncryptionKeyChange** parancsmag használható, a tárfiókok kulcsait most kéri a felhasználót a szolgáltatásadat-titkosítási kulcs. Ez az a korábbi kiadásáról, amelyben a szolgáltatásadat-titkosítási kulcs egy beágyazott paraméter lett átadva. |Igen |Nem |
| 11 |Feladat-visszavétel 24 órában |A vészhelyreállítás során a tisztítás, a forrás eszközön nem következtek be áramtalanították, ezért a feladat-visszavétel sikertelen. Ez kijavítása ebben a kiadásban. |Igen |Nem |

## <a name="known-issues-in-the-october-release"></a>Ismert problémák. októberi kiadása
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Gyári beállítások visszaállítása |Bizonyos esetekben a gyári beállítások visszaállítása, végrehajtásakor a StorSimple eszköz előfordulhat, hogy, és megjeleníti ezt az üzenetet: **a gyári alaphelyzetbe állítása folyamatban van a (fázis 8)**. Ez akkor fordul elő, ha a CTRL + C gombra, amíg folyamatban van a parancsmagot. |CTRL + C nem nyomja meg a gyári beállítások visszaállítása kezdeményezése után. Ha már ebben az állapotban, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Gyári beállítások visszaállítása |Hajtsa végre a nem a gyári beállítások visszaállítása a StorSimple eszköz, amely a 2014. októberi frissül a GA kiadás. |Ez a művelet csak akkor fog működni, a javítás telepítve van. Forduljon a Microsoft Support a szükséges javítás. |Igen |Nem |
| 3 |Lemez kvórum |Bizonyos ritkán előforduló esetekben ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet lesz kapcsolva a hálózatról. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 4 |Felhőalapú pillanatfelvétel hibák |Ritka esetekben a egy felhő-pillanatfelvételt sikertelen lehet a hibával **elérte a maximális biztonsági korlátot**. Ez akkor fordul elő, ha ugyanazon az eszközön, az azonos eredeti kötet már törölt 255 online klónok túllépi. | |Igen |Igen |
| 5 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 6 |Eszközfigyelési diagramok |A StorSimple Manager szolgáltatásban az eszköz figyelési diagramok nem egyszerű vagy az NTLM-hitelesítés engedélyezve van a proxykiszolgáló beállításait az eszközön. |Módosítsa a webproxy-konfigurációja az eszközt a StorSimple Manager szolgáltatás úgy, hogy a hitelesítés beállítása nincs regisztrálva. Ehhez futtassa a StorSimple Set-HcsWebProxy parancsmag a Windows PowerShell. |Igen |Igen |
| 7 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 8 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. |Több eszköz egyetlen elhalt eszközről feladatátvétel működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. |Igen |Nem |
| 9 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 10 |Webproxy |Ha a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-szolgáltatások közötti kommunikáció néven érinti, és kerül, hogy az eszköz offline állapotban. Támogatási csomag is hozható létre a folyamat során fel jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe HTTP megadott protokollt. További tájékoztatást [az eszköz webalkalmazás-proxy konfigurálása](storsimple-configure-web-proxy.md). |Igen |Nem |
| 11 |Webproxy |Ha konfigurált és engedélyezett a webalkalmazás-proxy regisztrált egy eszközt, majd szüksége lesz az eszköz aktív vezérlőjén újraindítására. | |Igen |Nem |
| 12 |Magas felhő késéssel és nagy i/o-munkaterhelés |Amikor a StorSimple eszköz nagyon magas felhő késések (másodperc sorrendben) és a magas i/o-munkaterhelés észlel, az eszköz kötetek csökkent állapotba, és az i/o "az eszköz nem áll készen" hiba miatt sikertelen lehet. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy egy eszköz feladatátvétel ebben a helyzetben helyreállítás végrehajtása. |Igen |Nem |

## <a name="physical-device-updates-in-the-october-release"></a>Fizikai eszköz frissítése a októberi kiadás
Ha ezeket a frissítéseket egy fizikai eszközre alkalmazzák, a szoftververzió 6.3.9600.17312 változik. Hacsak másként nincs megadva, a kibocsátási megjegyzéseket összes modellt a StorSimple eszköz vonatkozik. Ezekről a frissítésekről további információkért lásd: [2014. októberi fizikai készülék szoftverfrissítés Microsoft Azure StorSimple alapplatformjaként](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Soros csatlakozású SCSI (SAS) vezérlő és a belső vezérlőprogram-frissítésekre a októberi kiadás
Ebben a kiadásban frissíti az illesztőprogramot, és a fizikai eszköz vezérlő belső vezérlőprogramját. A SAS-vezérlő frissítéssel kapcsolatos további információkért lásd: [2014. októberi frissítés a Microsoft Azure StorSimple készülék LSI SAS-vezérlők](http://support.microsoft.com/kb/2987020).   

Ebben a kiadásban, amely az eszköz hardverösszetevők kapcsolatos problémák összegző vezérlőprogram-frissítés is vonatkozik. A belső vezérlőprogram frissítéssel kapcsolatos további információkért lásd: [2014. októberi vezérlőprogram-frissítés a Microsoft Azure StorSimple készülék](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Virtuális eszköz frissítése a októberi kiadás
Ez a kiadás nem tartalmaz frissítéseket a virtuális eszközhöz. A frissítés telepítése nem módosítja a virtuális eszköz szoftverének verziójával.

