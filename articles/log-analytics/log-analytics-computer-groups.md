---
title: Számítógép-csoportok az Azure Log Analytics naplóbeli kereséseivel |} A Microsoft Docs
description: A Log Analytics számítógépcsoportjaival naplókeresések hatókör számára egy adott számítógép engedélyezése.  Ez a cikk ismerteti a különböző módszerek használatával hozzon létre számítógép- és a egy naplóbeli keresés használatával.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 81eacf464c90d56c4ebeae1d1cefbd6f2f0fdab8
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408540"
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Számítógépcsoportok a Log Analytics naplóbeli kereséseivel

A Log Analytics számítógépcsoportjaival lehetővé teszi a hatókör [naplókereséseket](log-analytics-log-search-new.md) a számítógépek egy adott készletét.  Minden csoport fel van töltve, vagy az Ön által meghatározott lekérdezés segítségével számítógépek vagy csoportok különböző forrásokból származó importálásával.  A csoport szerepel egy Naplókeresés, amikor az eredmények korlátozódnak, amelyek megfelelnek a számítógépek a csoportban lévő rekordok.

## <a name="creating-a-computer-group"></a>Számítógépcsoport létrehozása
Létrehozhat egy számítógépcsoportot a Log Analytics az alábbi táblázatban a módszerek bármelyikével.  Részletek az egyes módszerek az alábbi szakaszokban találhatók. 

| Módszer | Leírás |
|:--- |:--- |
| Naplókeresés |Hozzon létre egy naplókeresést, amely a számítógépek listáját adja vissza. |
| Log Search API |A Log Search API használatával programozott módon létrehozhat egy Naplókeresés eredménye alapján számítógép csoport. |
| Active Directory |Automatikus vizsgálatokat végez a csoport tagságát, minden olyan ügynököt futtató számítógépek, amelyek az Active Directory-tartomány tagja, és hozzon létre egy csoportot a Log Analytics az egyes biztonsági csoportokhoz. |
| Configuration Manager | Gyűjtemények importálása a System Center Configuration Managerben, és hozzon létre egy csoportot a Log Analytics az egyes. |
| A Windows Server Update Services |Automatikus csoportok célzó WSUS-kiszolgálók vagy az ügyfelek keresése, és hozzon létre egy csoportot a Log Analytics az egyes. |

### <a name="log-search"></a>Naplókeresés
Naplókeresés létre számítógépcsoportokat tartalmaz minden Ön által meghatározott lekérdezés által visszaadott számítógépek.  Ez a lekérdezés futtatása minden alkalommal, amikor a számítógép (csoport) használja úgy, hogy a csoport létrehozása óta módosítások is megjelenik.  

Minden lekérdezés egy számítógép (csoport) is használhat, de egy különálló számítógépek csoportja használatával kell visszaadnia `distinct Computer`.  Következő sikerült használhat olyan számítógépcsoportként jellemző példa keresés.

    Heartbeat | where Computer contains "srv" | distinct Computer

A következő táblázat ismerteti, amelyek meghatározzák egy számítógép (csoport) tulajdonságait.

| Tulajdonság | Leírás |
|:---|:---|
| Megjelenítendő név   | A Keresés a portálon megjelenítendő neve. |
| Kategória       | A keresések a portálon rendszerezéséhez kategória. |
| Lekérdezés          | A lekérdezés a számítógép (csoport). |
| Függvényalias | A számítógépcsoport-lekérdezés azonosítására használt egyedi nevet. |

Az alábbi eljárás segítségével számítógépcsoport létrehozása egy Naplókeresés, az Azure Portalon.

2. Nyissa meg **naplóbeli keresés** majd **mentett keresések** a képernyő tetején.
3. Kattintson a **Hozzáadás** , és adja meg a számítógép csoport minden egyes tulajdonság értékét.
4. Válassza ki **lekérdezés mentése számítógépcsoportként** kattintson **OK**.



### <a name="active-directory"></a>Active Directory
Konfigurálja a Log Analyticsben, hogy az Active Directory-csoporttagságok importálása, elemzi a Log Analytics-ügynökkel rendelkező bármely tartományhoz csatlakoztatott számítógépek csoport tagsága.  Számítógép csoport jön létre a Log Analytics minden egyes biztonsági csoport az Active Directoryban, és mindegyik számítógépet adnak a számítógépcsoportok, a biztonsági csoportok tagjai megfelelő.  A csoporttagság 4 óránként folyamatosan frissítjük.  

A Log Analytics az Active Directory biztonsági csoportok importálása a Log Analytics konfigurálása **speciális beállítások** az Azure Portalon.  Válassza ki **számítógépcsoportok**, **Active Directory**, majd **importálás az Active Directory-csoporttagságok számítógépekről**.  Nincs szükség további konfigurációra.

![A számítógépcsoportok Active Directoryból](media/log-analytics-computer-groups/configure-activedirectory.png)

Ha csoportok lettek importálva, a menü mutatja az a csoport tagjai észlelt számítógépek számát és a csoport importálva számát.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Konfigurálja a Log Analyticsben, hogy a WSUS-csoporttagságok importálása, megvizsgálja a célcsoport-kezelési csoport tagságát, minden olyan, Log Analytics-ügynökkel rendelkező számítógépek.  Ügyféloldali használatakor célozza, minden olyan számítógép, amely a Log Analyticshez csatlakoztatva van, és minden olyan WSUS része csoportokat célzó rendelkezik csoporttagságát. a Log Analytics szolgáltatásba importált. Kiszolgálóoldali használatakor céloz meg, a Log Analytics ügynököt kell telepíteni ahhoz, hogy a csoporttagsági információkat lehet importálni a Log Analytics WSUS-kiszolgálón.  A csoporttagság 4 óránként folyamatosan frissítjük. 

Konfigurálja a WSUS-csoportok importálása a Log Analytics a Log Analytics **speciális beállítások** az Azure Portalon.  Válassza ki **számítógépcsoportok**, **WSUS**, majd **importálási WSUS-csoporttagságok**.  Nincs szükség további konfigurációra.

![Számítógépcsoportokat a WSUS-nak](media/log-analytics-computer-groups/configure-wsus.png)

Ha csoportok lettek importálva, a menü mutatja az a csoport tagjai észlelt számítógépek számát és a csoport importálva számát.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Amikor konfigurálja a Configuration Manager-gyűjteménytagságok importálása a Log Analytics, az egyes gyűjtemények számítógépcsoport hoz létre.  A gyűjtemény tagsági információ 3 óránként rendszer olvassa be a számítógépcsoportok naprakészen tartása. 

A Configuration Manager-gyűjtemények importálása előtt kell [a Configuration Manager csatlakoztatása a Log Analytics](log-analytics-sccm.md).  Ezt követően konfigurálhatja a Log Analytics az importálás **speciális beállítások** az Azure Portalon.  Válassza ki **számítógépcsoportok**, **SCCM**, majd **importálása a Configuration Manager-gyűjteménytagságok**.  Nincs szükség további konfigurációra.

![Számítógépcsoportok az SCCM-ből](media/log-analytics-computer-groups/configure-sccm.png)

Gyűjtemények lettek importálva, ha a menüben a csoport tagjai észlelt számítógépek számát és a csoport importálva számát sorolja fel.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

## <a name="managing-computer-groups"></a>Számítógépcsoportok kezelése
Megtekintheti a Naplókeresés vagy a Log Search API a Log Analytics alapján létrehozott számítógépcsoportok **speciális beállítások** az Azure Portalon.  Válassza ki **számítógépcsoportok** , majd **mentett csoportok**.  

Kattintson a **x** a a **eltávolítása** törölni a számítógép (csoport) oszlopban.  Kattintson a **tagok megtekintése** ikon egy csoportra, amely visszaadja a tagok a csoport Naplókeresés futtatásához.  Egy számítógép (csoport) nem módosítható, de ehelyett kell törölje és hozza létre újra a módosított beállításokkal.

![Mentett számítógépcsoportok](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Számítógépcsoport használata a Naplókeresés
A lekérdezésben Naplókeresés való kezelésével svému aliasu függvényében, általában a következő szintaxist a létrehozott számítógépcsoport használata:

  `Table | where Computer in (ComputerGroup)`

Ha például a következő segítségével mycomputergroup nevű számítógép csoport lévő updateSummary típusú rekordok csak számítógépek visszaadása.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Az importált számítógép- és az érintett számítógépekre vannak tárolva a **ComputerGroup** tábla.  Például a következő lekérdezés termékazonosítóhoz azon számítógépek listáját a tartományi számítógépek csoport az Active Directoryból. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A következő lekérdezést a tartományi számítógépek adna vissza updateSummary típusú rekordok csak a számítógépek számára.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Számítógép biztonságicsoport-rekordok
Egy rekord jön létre minden egyes létrehozott Active Directory vagy a WSUS számítógépcsoport-tagság a Log Analytics-munkaterületen.  Ezeket a rekordokat rendelkezik olyan típusú **ComputerGroup** , és a tulajdonságait az alábbi táblázatban.  Rekordok nem jön létre számítógépcsoportokat a naplókeresések alapján.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computer |A számítógép neve. |
| Csoport |A csoport nevét. |
| GroupFullName |Teljes elérési útja a csoporthoz, többek között a forrás- és adatforrás neve. |
| GroupSource |Forrás-csoporthoz való gyűjtése történt. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |A forrás, az összegyűjtött a csoport nevére.  Az Active Directory Ez a tartománynév. |
| ManagementGroupName |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetén ez AOI -\<munkaterület azonosítója\> |
| TimeGenerated |Dátum és időpont a számítógép (csoport) létrehozott vagy frissített. |

## <a name="next-steps"></a>További lépések
* Ismerje meg [naplókereséseket](log-analytics-log-searches.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  

