---
title: StorSimple 8000 sorozatú hardverösszetevők és állapot | Microsoft dokumentumok
description: Ismerje meg, hogyan figyelheti a StorSimple-eszköz hardverösszetevőit a StorSimple Eszközkezelő szolgáltatáson keresztül.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254741"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>A StorSimple Eszközkezelő szolgáltatás használata a hardverösszetevők és az állapot figyelésére

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a különböző fizikai és logikai összetevők a helyszíni StorSimple 8000 sorozatú eszköz. Azt is ismerteti, hogyan figyelheti az eszköz összetevő állapotát a StorSimple Device Manager szolgáltatás **Állapot és hardver állapot** paneljével.

Az **Állapot és a hardver állapotpanel** a StorSimple összes eszközösszetevő hardverállapotát jeleníti meg.

A 8100 összetevőinek listája alatt három szakasz található, amelyek a következőket írják le:

* **Megosztott összetevők** – Ezek nem részei a vezérlőknek, például lemezmeghajtók, ház, áram- és hűtőmodul (PCM) alkatrészek és PCM-hőmérséklet, vonalfeszültség és vonaláram-érzékelők.
* **Vezérlő 0 Alkatrészek** - Az alkatrészek, amelyek a Controller 0, mint például a vezérlő, SAS expander és csatlakozó, vezérlő hőmérséklet-érzékelők, és a különböző hálózati interfészek.
* **Controller 1 Alkatrészek** – Az alkatrészek alkotó Controller 1, hasonló részletes controller 0.

Egy 8600-as eszköz további összetevőket tartalmaz, amelyek megfelelnek a kiterjesztett fürtlemezek (EBOD) ház. Az összetevők listája alatt öt rész van. Ezek közül három szakasz található, amelyek az elsődleges ház összetevőit tartalmazzák, és megegyeznek a 8100-hoz leírtakkal. Az EBOD-házhoz két további szakasz található, amelyek a következőket írják le:

* **EBOD-vezérlő 0 komponensek** – Az alkatrészek, amelyek az EBOD ház 0, mint például az EBOD vezérlő, SAS expander és csatlakozó, és a vezérlő hőmérséklet-érzékelők.
* **EBOD Controller 1 alkatrészek** – Az alkatrészek alkotó EBOD ház 1, hasonló a részletes EBOD ház 0.
* **EBOD ház megosztott összetevők** – Az EBOD ház és a PCM, amelyek nem részei az EBOD vezérlő.

> [!NOTE]
> **A hardver állapota nem érhető el a StorSimple cloud appliance (8010/8020) esetén.**


## <a name="monitor-the-hardware-status"></a>A hardver állapotának figyelése
Az eszközösszetevő hardverállapotának megtekintéséhez hajtsa végre az alábbi lépéseket:

1. Keresse meg **az Eszközök**, válasszon ki egy adott StorSimple eszközt. Nyissa meg **a > hardver állapotának figyelése**lehetőséget.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Keresse meg a **Hardverösszetevők szakaszt,** és válasszon a rendelkezésre álló összetevők közül. Egyszerűen kattintson az összetevő címkéjére a lista kibontásához és a különböző eszközösszetevők állapotának megtekintéséhez. Tekintse meg [az elsődleges ház részletes összetevőlistáját](#component-list-for-primary-enclosure-of-storsimple-device) és az [EBOD ház részletes összetevőlistáját.](#component-list-for-ebod-enclosure-of-storsimple-device)

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Az összetevő állapotának értelmezéséhez használja a következő színkódolási sémát:
   
   * **Zöld ellenőrzés** – Egy **késésállapotú** kifogástalan összetevőt jelöl.
   * **Sárga** – Figyelmeztetés állapotú degradált összetevőt **jelöl.**
   * **Vörös felkiáltójel** – Egy sikertelen összetevőt jelöl, **amelynek hiba** állapota van.
   * **Fehér, fekete szöveggel** – Olyan összetevőt jelöl, amely nincs jelen.
   
   A következő képernyőképen egy olyan eszköz látható, amelynek összetevői **OK**, **Figyelmeztetés**és **Hiba** állapotban vannak.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   A **megosztott összetevők listájának**kibontásával láthatjuk, hogy az NVRAM és a fürt leromlott.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   A **Controller 1 összetevőinek listájának** kibontásával látható, hogy a fürtcsomópont meghibásodott.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Ha olyan összetevővel találkozik, amely nem **kifogástalan** állapotú, forduljon a Microsoft támogatási szolgálatához. Ha az eszközön engedélyezve vannak a riasztások, e-mailben értesítést kap. Ha egy meghibásodott hardverösszetevőt kell cserélnie, olvassa el a [StorSimple hardverösszetevő cseréje](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>A StorSimple eszköz elsődleges burkolatának összetevőlistája
Az alábbi táblázat ismerteti a helyszíni StorSimple eszköz elsődleges házában található fizikai és logikai összetevőket (a 8100-as és a 8600-as készletben).

| Összetevő | Modul | Típus | Hely | Mező cserélhető egység (FRU)? | Leírás |
| --- | --- | --- | --- | --- | --- |
| Meghajtó a foglalatban [0-11] |Lemezmeghajtók |Fizikai |Megosztott |Igen |Az elsődleges ház minden SSD- vagy HDD-meghajtóhoz egy sor jelenik meg. |
| Környezeti hőmérséklet-érzékelő |Kamra |Fizikai |Megosztott |Nem |Méri a hőmérsékletet az alvázon belül. |
| Síkközi hőmérséklet-érzékelő |Kamra |Fizikai |Megosztott |Nem |Méri a középsík hőmérsékletét. |
| Hallható riasztás |Kamra |Fizikai |Megosztott |Nem |Azt jelzi, hogy a hangjelző alrendszer az alházban működik-e. |
| Kamra |Kamra |Fizikai |Megosztott |Igen |Az alváz jelenlétét jelzi. |
| Ház beállításai |Kamra |Fizikai |Megosztott |Nem |Az alváz előlapjára utal. |
| Vezetékfeszültség-érzékelők |PCM |Fizikai |Megosztott |Nem |Számos vonalfeszültség-érzékelő állapota jelenik meg, ami azt jelzi, hogy a mért feszültség a tűréshatáron belül van-e. |
| Vonaláram-érzékelők |PCM |Fizikai |Megosztott |Nem |Számos vonaláram-érzékelő állapota jelenik meg, ami azt jelzi, hogy a mért áram a tűréshatáron belül van-e. |
| Hőmérséklet-érzékelők PCM-ben |PCM |Fizikai |Megosztott |Nem |Számos hőmérséklet-érzékelő, például a beömlési és a hotspot-érzékelők állapota jelenik meg, jelezve, hogy a mért hőmérséklet a tűréshatáron belül van-e. |
| Tápegység [0-1] |PCM |Fizikai |Megosztott |Igen |A készülék hátulján található két PCM mindegyikéhez egy sor jelenik meg. |
| Hűtés [0-1] |PCM |Fizikai |Megosztott |Igen |A két PCM-ben található négy hűtőventilátor mindegyikéhez egy sor kerül bemutatásra. |
| Akkumulátor [0-1] |PCM |Fizikai |Megosztott |Igen |A PCM-ben ülő tartalék akkumulátormodulok mindegyikéhez egy sor jelenik meg. |
| Metis között |N/A |Logikai |Megosztott |N/A |Megjeleníti az akkumulátorok állapotát: hogy szükségük van-e töltésre, és közelednek-e az életciklus végéhez. |
| Fürt |N/A |Logikai |Megosztott |N/A |A két integrált vezérlőmodul között létrehozott fürt állapotát jeleníti meg. |
| Fürtcsomópont |N/A |Logikai |Megosztott |N/A |A vezérlő állapotát jelzi a fürt részeként. |
| Fürt kvóruma |N/A |Logikai | |N/A |A merevlemez-tárolókészlet többségi lemeztagságának jelenlétét jelzi. |
| HDD adattér |N/A |Logikai |Megosztott |N/A |A merevlemez-meghajtó (HDD) tárolókészletében lévő adatok tárolására használt tárhely. |
| HDD felügyeleti terület |N/A |Logikai |Megosztott |N/A |A HDD-tárolókészletben a felügyeleti feladatok számára fenntartott hely. |
| HDD kvórumterület |N/A |Logikai |Megosztott |N/A |A fürt kvóruma számára fenntartott hdd-tárolókészletben fenntartott terület. |
| HDD cserehely |N/A |Logikai |Megosztott |N/A |A HDD tárolókészletben a vezérlő cseréjére fenntartott hely. |
| SSD adattér |N/A |Logikai |Megosztott |N/A |A ssd-tárolókészletben lévő adatok hoz használt tárhely. |
| SSD NVRAM tér |N/A |Logikai |Megosztott |N/A |Az SSD-tárolókészlet, amely az NVRAM-logika számára dedikált tárolóhely. |
| HDD tárolókészlet |N/A |Logikai |Megosztott |N/A |Az eszköz HDD-iből létrehozott logikai tárolókészlet állapotát jeleníti meg. |
| SSD-tárolókészlet |N/A |Logikai |Megosztott |N/A |Az eszköz SSD-kből létrehozott logikai tárolókészlet állapotát jeleníti meg. |
| Vezérlő [0-1] [állapot] |I/o |Fizikai |Vezérlő |Igen |Megjeleníti a vezérlő állapotát, valamint azt, hogy aktív vagy készenléti üzemmódban van-e a házon belül. |
| Hőmérséklet-érzékelők a vezérlőben |I/o |Fizikai |Vezérlő |Nem |Számos hőmérséklet-érzékelő, például az I/O modul, a CPU hőmérséklete, a DIMM és a PCIe érzékelők állapota jelenik meg, ami azt jelzi, hogy a tapasztalt hőmérséklet a tűréshatáron belül van-e. |
| SAS bővítő |I/o |Fizikai |Vezérlő |Nem |A soros csatlakoztatott SCSI (SAS) bővítő állapotát jelzi, amely az integrált tárolót a vezérlőhöz csatlakoztatja. |
| SAS-csatlakozó [0-1] |I/o |Fizikai |Vezérlő |Nem |Az egyes SAS-összekötők állapotát jelzi, amelyek az integrált tároló tágítóhoz való csatlakoztatására szolgálnak. |
| SBB középsíkú összeköttetés |I/o |Fizikai |Vezérlő |Nem |A középsík-összekötő állapotát jelzi, amely az egyes vezérlőket a középsíkhoz csatlakoztatja. |
| Processzormag |I/o |Fizikai |Vezérlő |Nem |Az egyes vezérlőn belüli processzormagok állapotát jelzi. |
| A burkolat elektronikai teljesítménye |I/o |Fizikai |Vezérlő |Nem |A ház által használt energiarendszer állapotát jelzi. |
| Burkolat elektronikai diagnosztikája |I/o |Fizikai |Vezérlő |Nem |A vezérlő által biztosított diagnosztikai alrendszerek állapotát jelzi. |
| Alaplapi felügyeleti vezérlő (BMC) |I/o |Fizikai |Vezérlő |Nem |Az alaplapi felügyeleti vezérlő (BMC) állapotát jelzi, amely egy speciális szervizprocesszor, amely érzékelőkön keresztül figyeli a hardvereszközt, és független kapcsolaton keresztül kommunikál a rendszergazdával. |
| Ethernet |I/o |Fizikai |Vezérlő |Nem |Az egyes hálózati adapterek állapotát jelzi, azaz a vezérlőn biztosított felügyeleti és adatportokat. |
| Nvram |I/o |Fizikai |Vezérlő |Nem |Az NVRAM állapotát jelzi, amely egy nem felejtő véletlen hozzáférésű memória, amelyet az akkumulátor biztonsági másolatot ad, és amely áramkimaradás esetén az alkalmazás szempontjából kritikus információk megőrzésére szolgál. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>A StorSimple-eszköz EBOD-házának összetevőlistája
Az alábbi táblázat ismerteti a fizikai és logikai összetevők a helyszíni StorSimple eszköz EBOD-ház (csak 8600 modell) található.

| Összetevő | Modul | Típus | Hely | Fru? | Leírás |
| --- | --- | --- | --- | --- | --- |
| Meghajtó a foglalatban [0-11] |Lemezmeghajtók |Fizikai |Megosztott |Igen |Az EBOD-ház elején található HDD-meghajtók mindegyikéhez egy sor jelenik meg. |
| Környezeti hőmérséklet-érzékelő |Kamra |Fizikai |Megosztott |Nem |Méri a hőmérsékletet az alvázon belül. |
| Síkközi hőmérséklet-érzékelő |Kamra |Fizikai |Megosztott |Nem |Méri a középsík hőmérsékletét. |
| Hallható riasztás |Kamra |Fizikai |Megosztott |Nem |Azt jelzi, hogy a hangjelző alrendszer az alházban működik-e. |
| Kamra |Kamra |Fizikai |Megosztott |Igen |Az alváz jelenlétét jelzi. |
| Ház beállításai |Kamra |Fizikai |Megosztott |Nem |Az OPS-ra vagy az alváz előlapjára vonatkozik. |
| Vezetékfeszültség-érzékelők |PCM |Fizikai |Megosztott |Nem |Számos vonalfeszültség-érzékelő állapota jelenik meg, ami azt jelzi, hogy a mért feszültség a tűréshatáron belül van-e. |
| Vonaláram-érzékelők |PCM |Fizikai |Megosztott |Nem |Számos vonaláram-érzékelő állapota jelenik meg, ami azt jelzi, hogy a mért áram a tűréshatáron belül van-e. |
| Hőmérséklet-érzékelők PCM-ben |PCM |Fizikai |Megosztott |Nem |Számos hőmérséklet-érzékelő, például a beömlési és a hotspot-érzékelők állapota jelenik meg, ami azt jelzi, hogy a mért hőmérséklet a tűréshatáron belül van-e. |
| Tápegység [0-1] |PCM |Fizikai |Megosztott |Igen |A készülék hátulján található két PCM mindegyikéhez egy sor jelenik meg. |
| Hűtés [0-1] |PCM |Fizikai |Megosztott |Igen |A két PCM-ben található négy hűtőventilátor mindegyikéhez egy sor kerül bemutatásra. |
| Helyi tároló [HDD] |N/A |Logikai |Megosztott |N/A |Az eszköz HDD-iből létrehozott logikai tárolókészlet állapotát jeleníti meg. |
| Vezérlő [0-1] [állapot] |I/o |Fizikai |Vezérlő |Igen |Megjeleníti a vezérlők állapotát az EBOD modulban. |
| Hőmérséklet-érzékelők az EBOD-ban |I/o |Fizikai |Vezérlő |Nem |Az egyes vezérlőktől származó számos hőmérséklet-érzékelő állapota megjelenik, ami azt jelzi, hogy a tapasztalt hőmérséklet a tűréshatáron belül van-e. |
| SAS bővítő |I/o |Fizikai |Vezérlő |Nem |A SAS-bővítő állapotát jelzi, amely az integrált tárolóvezérlőhöz való csatlakoztatására szolgál. |
| SAS-csatlakozó [0-2] |I/o |Fizikai |Vezérlő |Nem |Az egyes SAS-összekötők állapotát jelzi, amelyek az integrált tároló tágítóhoz való csatlakoztatására szolgálnak. |
| SBB középsíkú összeköttetés |I/o |Fizikai |Vezérlő |Nem |A középsík-összekötő állapotát jelzi, amely az egyes vezérlőket a középsíkhoz csatlakoztatja. |
| A burkolat elektronikai teljesítménye |I/o |Fizikai |Vezérlő |Nem |A ház által használt energiarendszer állapotát jelzi. |
| Burkolat elektronikai diagnosztikája |I/o |Fizikai |Vezérlő |Nem |A vezérlő által biztosított diagnosztikai alrendszerek állapotát jelzi. |
| Csatlakozás az eszközvezérlőhöz |I/o |Fizikai |Vezérlő |Nem |Az EBOD I/O modul és az eszközvezérlő közötti kapcsolat állapotát jelzi. |

## <a name="next-steps"></a>További lépések
* Ha a StorSimple Eszközkezelő szolgáltatást szeretné használni az eszköz felügyeletéhez, a [StorSimple Eszközkezelő szolgáltatással felügyelheti a StorSimple-eszközt.](storsimple-8000-manager-service-administration.md)
* Ha egy csökkentett vagy hibás állapotú eszközösszetevőt kell elhárítania, olvassa el a [StorSimple figyelési jelzőit.](storsimple-monitoring-indicators.md)
* Egy meghibásodott hardverösszetevő cseréjéről a [StorSimple hardverösszetevő cseréje](storsimple-hardware-component-replacement.md).
* Ha továbbra is eszközproblémákat tapasztal, [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)

