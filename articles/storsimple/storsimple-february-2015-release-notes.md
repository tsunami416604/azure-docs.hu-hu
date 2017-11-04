---
title: "A StorSimple 8000 frissítés 0,3 kibocsátási megjegyzései |} Microsoft Docs"
description: "Az új szolgáltatásokat és javításokat, nyissa meg problémák és rendelkezésre megkerülő megoldások ismerteti a 2015. február a Microsoft Azure StorSimple release (0,3. frissítés)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: b01bfd04-f9f8-45f4-ade8-95ac2b979e6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: c059fd74854813615754e67547497b7ededbe4dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>A StorSimple 8000 Series Update 0,3 kibocsátási megjegyzések – 2015. február
## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések a StorSimple 8000 Series Update 0,3, amely 2015. február a kritikus megnyitott problémák azonosításához. Az ebben a kiadásban szereplő StorSimple szoftver és a belső vezérlőprogram frissítés listáját is tartalmaznak. Ez az a harmadik kiadás után a StorSimple 8000 Series kiadási verziójával történt a 2014. július általánosan elérhető.

A frissítés nem módosul az eszköz szoftverének verziójával. januári frissítés. Verzió 6.3.9600.17312 továbbra is. Ellenőrizheti, hogy a frissítés a ellenőrzésével telepítve van-e a **utolsó frissített** dátum. Ha a dátumot 2/10/2015 vagy újabb verzióját, majd a frissítés telepítése sikeresen befejeződött.  

Azt javasoljuk, hogy keressen, és elérhető frissítések alkalmazása a StorSimple eszköz telepítése után azonnal. Engedélyezheti az automatikus frissítések letöltése és telepítése a Microsoft fontos frissítések, amint azok kiadásakor. További információkért lásd: [a StorSimple eszköz frissítése](storsimple-update-device.md).  

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat.  

> [!IMPORTANT]
> * Használja a StorSimple Manager szolgáltatás és a nem Windows PowerShell-lel. februári frissítés telepítéséhez.   
> * A frissítés telepítéséhez körülbelül egy óra vesz igénybe. Azonban az összegző frissítések telepítésekor, a folyamat eltarthat körülbelül 3 órát befejezéséhez.  
> * A StorSimple. február kiadása nem tartalmaz a StorSimple virtuális eszköz módosításait. Elérhető Windows-frissítések továbbra is alkalmazhat a virtuális eszköz, többek között a legutóbbi biztonsági javítások, de nem jelenik meg a virtuális eszköz verziója megváltozik.  
> 
> 

Győződjön meg arról, hogy a StorSimple eszköz frissítése előtt az alábbi előfeltételek meglétét.  

* Győződjön meg arról, hogy mindkét eszközvezérlők futnak, a frissítések keresése előtt. Vagy a tartományvezérlő nem fut, ha az ellenőrzés sikertelen lesz. Győződjön meg arról, hogy a tartományvezérlők kifogástalan állapotban vannak-e, lépjen **hardverállapot** alatt a **karbantartási** lap. Ha az összetevő, amely **kezeléséről**, forduljon a Microsoft Support adatbázist.
* Győződjön meg a vezérlő 0 és 1. vezérlő rögzített IP-címek irányíthatók, és képes csatlakozni az internethez, mivel ezek az eszköz frissítéseinek karbantartásához használatosak. Használhatja a [Test-Connection parancsmag](https://technet.microsoft.com/library/hh849808.aspx) Pingelje meg egy ismert cím kívül a hálózathoz, például az Outlook.com-os, ellenőrizheti, hogy a tartományvezérlő rendelkezik-e a külső hálózathoz csatlakoznak.
* Győződjön meg arról, hogy elérhetők-e 80-as és 443-as port kimenő kommunikációra a StorSimple eszköz. További információkért lásd: a [a StorSimple eszköz hálózatkezelési követelményei](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* Ha az eszköz szoftver verziója régebbi, mint 6.3.9600.17312 (2014. októberi frissítés), tiltsa le a Data 2 és a Data 3 portokat, ha engedélyezve van, a frissítés elkezdése előtt. A Data 2 vagy a Data 3 portok engedélyezett, ha a frissítés alkalmazásához hagyja, előfordulhat, hogy a helyreállítási módba az eszköz vezérlő. Vegye figyelembe, hogy a hálózati illesztők letiltása esetén a társított kötetek a rendszer offline állapotra, és az i/o műveletek fog működni a frissítés ideje alatt.  

## <a name="whats-new-in-the-february-release"></a>What's new in a február kiadás
A frissítés javítást tartalmaz, a gyári alaphelyzetbe probléma történt az eszközöket, amelyek kellett frissített a GA kiadás a 2014. októberi kiadásához. További információkért lásd: [ebben a kiadásban javított](#issues-fixed-in-the-february-release).   

A frissítés nem tartalmaz új szolgáltatások vagy funkciók.  

## <a name="issues-fixed-in-the-february-release"></a>A február kiadásban megoldott problémák
A következő táblázat ismerteti a problémát, a frissítés rögzített.  

| Nem. | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gyári beállítások visszaállítása |Hajtsa végre a gyári beállításokat egy eszközön, amelyet eredetileg a GA kiadás (verzió: 6.3.9600.17215) volt, de a október megújult próbál kiadása (6.3.9600.17312 verzió). A gyári meghiúsul, és az eszköz instabillá válik. |Igen |Nem |

## <a name="known-issues-in-the-february-release"></a>Ismert problémák a február kiadásban
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Gyári beállítások visszaállítása |Bizonyos esetekben a gyári beállítások visszaállítása, végrehajtásakor a StorSimple eszköz előfordulhat, hogy, és megjeleníti ezt az üzenetet: **a gyári alaphelyzetbe állítása folyamatban van a (fázis 8)**. Ez akkor fordul elő, ha a CTRL + C gombra, amíg folyamatban van a parancsmagot. |CTRL + C nem nyomja meg a gyári beállítások visszaállítása kezdeményezése után. Ha már ebben az állapotban, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Lemez kvórum |Bizonyos ritkán előforduló esetekben ha a legtöbb, a EBOD ház egy 8600device a lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet lesz kapcsolva a hálózatról. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 3 |Felhőalapú pillanatfelvétel hibák |Ritka esetekben a egy felhő-pillanatfelvételt sikertelen lehet a hibával **elérte a maximális biztonsági korlátot**. Ez akkor fordul elő, ha ugyanazon az eszközön, az azonos eredeti kötet már törölt 255 online klónok túllépi. | |Igen |Igen |
| 4 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 5 |Eszközfigyelési diagramok |A StorSimple Manager szolgáltatásban az eszköz figyelési diagramok nem egyszerű vagy az NTLM-hitelesítés engedélyezve van a proxykiszolgáló beállításait az eszközön. |Módosítsa a webproxy-konfigurációja az eszközt a StorSimple Manager szolgáltatás úgy, hogy a hitelesítés beállítása nincs regisztrálva. Ehhez futtassa a StorSimple Set-HcsWebProxy parancsmag a Windows PowerShell. |Igen |Igen |
| 6 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 7 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott.    Több eszköz egyetlen elhalt eszközről feladatátvétel működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. | |Igen |Nem |
| 8 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 9 |Webproxy |Ha a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-szolgáltatások közötti kommunikáció néven érinti, és kerül, hogy az eszköz offline állapotban. Támogatási csomag is hozható létre a folyamat során fel jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe HTTP megadott protokollt. További tájékoztatást [az eszköz webalkalmazás-proxy konfigurálása](storsimple-configure-web-proxy.md). |Igen |Nem |
| 10 |Webproxy |Ha konfigurált és engedélyezett a webalkalmazás-proxy regisztrált egy eszközt, majd szüksége lesz az eszköz aktív vezérlőjén újraindítására. | |Igen |Nem |
| 11 |Magas felhő késéssel és nagy i/o-munkaterhelés |Amikor a StorSimple eszköz nagyon magas felhő késések (másodperc sorrendben) és a magas i/o-munkaterhelés észlel, az eszköz kötetek csökkent állapotba, és az i/o "az eszköz nem áll készen" hiba miatt sikertelen lehet. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy egy eszköz feladatátvétel ebben a helyzetben helyreállítás végrehajtása. |Igen |Nem |

## <a name="physical-device-updates-in-the-february-release"></a>Fizikai eszköz frissítése a februári kiadás
A frissítés megoldja a gyári alaphelyzetbe állítása probléma történt az eszközöket, amelyek kellett frissített a GA kiadás a 2014. októberi kiadásához. Bármely egyéb frissítésekről a StorSimple-eszköz nem tartalmaz.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Soros csatlakozású SCSI (SAS) vezérlő és a belső vezérlőprogram-frissítésekre a februári kiadás
Ez a kiadás nem tartalmaz frissítéseket soros csatlakozású SCSI (SAS) vagy a belső vezérlőprogram. Az illesztőprogram frissítéséhez a októberben 2014 kiadásban volt.  

## <a name="virtual-device-updates-in-the-february-release"></a>Virtuális eszköz frissítése a februári kiadás
Ez a kiadás nem tartalmaz frissítéseket a virtuális eszközhöz. A frissítés telepítése nem módosítja a virtuális eszköz szoftverének verziójával.

