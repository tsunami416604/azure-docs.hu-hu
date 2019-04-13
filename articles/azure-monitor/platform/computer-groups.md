---
title: Az Azure monitorban számítógépcsoportok lekérdezések naplózását |} A Microsoft Docs
description: Az Azure monitorban számítógépcsoportok lehetővé teszik naplólekérdezések hatókör számára egy adott számítógépeken.  Ez a cikk ismerteti a különböző módszerek használatával hozzon létre számítógépcsoportokat és azok használatát a napló lekérdezésben.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: c2babb5a86d69881b6a76c6dceae80a24a891f6c
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549303"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Az Azure monitorban számítógépcsoportok lekérdezések naplózását.
Az Azure monitorban számítógépcsoportok lehetővé teszi a hatókör [lekérdezések naplózását](../log-query/log-query-overview.md) a számítógépek egy adott készletét.  Minden csoport fel van töltve, vagy az Ön által meghatározott lekérdezés segítségével számítógépek vagy csoportok különböző forrásokból származó importálásával.  A csoport szerepel egy naplólekérdezés, amikor az eredmények korlátozódnak, amelyek megfelelnek a számítógépek a csoportban lévő rekordok.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Számítógépcsoport létrehozása
Létrehozhat egy számítógép (csoport) az Azure monitorban az alábbi táblázatban a módszerek bármelyikével.  Részletek az egyes módszerek az alábbi szakaszokban találhatók. 

| Módszer | Leírás |
|:--- |:--- |
| A lekérdezési napló |Hozzon létre egy log lekérdezést, amely a számítógépek listáját adja vissza. |
| Log Search API |A Log Search API használatával programozott módon létrehozhat egy számítógépcsoport log lekérdezés eredményei alapján. |
| Active Directory |Automatikus vizsgálatokat végez a csoport tagságát, minden olyan ügynököt futtató számítógépek, amelyek az Active Directory-tartomány tagja, és hozzon létre egy csoportot az Azure monitorral az egyes biztonsági csoportokhoz. (Csak Windows-gépek esetében)|
| Configuration Manager | Gyűjtemények importálása a System Center Configuration Managerben, és hozzon létre egy csoportot az Azure monitorral az egyes. |
| A Windows Server Update Services |Automatikus csoportok célzó WSUS-kiszolgálók vagy az ügyfelek keresése, és hozzon létre egy csoportot az Azure monitorral az egyes. |

### <a name="log-query"></a>A lekérdezési napló
Napló lekérdezésből létrehozott számítógépcsoportok tartalmaz minden Ön által meghatározott lekérdezés által visszaadott számítógépek.  Ez a lekérdezés futtatása minden alkalommal, amikor a számítógép (csoport) használja úgy, hogy a csoport létrehozása óta módosítások is megjelenik.  

Minden lekérdezés egy számítógép (csoport) is használhat, de egy különálló számítógépek csoportja használatával kell visszaadnia `distinct Computer`.  Következő egy jellemző példa lekérdezés számítógépcsoportként az használt sikerült.

    Heartbeat | where Computer contains "srv" | distinct Computer

Az alábbi eljárás segítségével számítógépcsoport létrehozása egy Naplókeresés, az Azure Portalon.

1. Kattintson a **naplók** a a **Azure Monitor** menü az Azure Portalon.
1. Hozzon létre és futtathat olyan lekérdezést, a számítógépeket, amelyeket a csoporthoz adja vissza.
1. Kattintson a **mentése** a képernyő tetején.
1. Változás **Mentés másként** való **függvény** válassza **lekérdezés mentése számítógépcsoportként**.
1. Adja meg minden egyes tulajdonság értékét a számítógép (csoport) lásd a táblázatot, és kattintson a **mentése**.

A következő táblázat ismerteti, amelyek meghatározzák egy számítógép (csoport) tulajdonságait.

| Tulajdonság | Leírás |
|:---|:---|
| Name (Név)   | A lekérdezés a portálon megjelenítendő neve. |
| Függvényalias | A számítógépcsoport-lekérdezés azonosítására használt egyedi nevet. |
| Kategória       | A lekérdezések a portálon rendezése kategória. |


### <a name="active-directory"></a>Active Directory
Konfigurálja az Active Directory-csoporttagságok importálása az Azure Monitor, elemzi a bármely Windows-tartományhoz csatlakoztatott számítógépeket a Log Analytics-ügynököket a csoport tagsága.  Számítógép csoport jön létre az Azure monitorban minden egyes biztonsági csoport az Active Directoryban, és minden egyes Windows-számítógép hozzáadása a számítógépcsoportokat, a biztonsági csoportok tagjai megfelelő.  A csoporttagság 4 óránként folyamatosan frissítjük.  

> [!NOTE]
> Importált Active Directory-csoportok csak a Windows-gépeket tartalmazhat.

Konfigurálja az Active Directory biztonsági csoportok importálása az Azure Monitor **speciális beállítások** a Log Analytics-munkaterület az Azure Portalon.  Válassza ki **számítógépcsoportok**, **Active Directory**, majd **importálás az Active Directory-csoporttagságok számítógépekről**.  Nincs szükség további konfigurációra.

![A számítógépcsoportok Active Directoryból](media/computer-groups/configure-activedirectory.png)

Ha csoportok lettek importálva, a menü mutatja az a csoport tagjai észlelt számítógépek számát és a csoport importálva számát.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Konfigurálja a WSUS-csoporttagságok importálása az Azure Monitor, megvizsgálja a célcsoport-kezelési csoport tagságát, minden olyan, Log Analytics-ügynökkel rendelkező számítógépek.  Ügyféloldali használatakor célozza, minden olyan számítógép, amely az Azure Monitor csatlakozik, és minden olyan WSUS része csoportok célzó rendelkezik importálta az Azure Monitor csoporttagságát. Kiszolgálóoldali használatakor céloz meg, a Log Analytics ügynököt kell telepíteni ahhoz, hogy a csoporttagsági információkat az Azure monitornak importálni a WSUS-kiszolgálón.  A csoporttagság 4 óránként folyamatosan frissítjük. 

Konfigurálja a WSUS-csoportok importálása az Azure Monitor **speciális beállítások** a Log Analytics-munkaterület az Azure Portalon.  Válassza ki **számítógépcsoportok**, **WSUS**, majd **importálási WSUS-csoporttagságok**.  Nincs szükség további konfigurációra.

![Számítógépcsoportokat a WSUS-nak](media/computer-groups/configure-wsus.png)

Ha csoportok lettek importálva, a menü mutatja az a csoport tagjai észlelt számítógépek számát és a csoport importálva számát.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Amikor konfigurál a Configuration Manager-gyűjteménytagságok importálása az Azure Monitor, az egyes gyűjtemények számítógépcsoport hoz létre.  A gyűjtemény tagsági információ 3 óránként rendszer olvassa be a számítógépcsoportok naprakészen tartása. 

A Configuration Manager-gyűjtemények importálása előtt kell [a Configuration Manager csatlakoztatása az Azure monitornak](collect-sccm.md).  Ezt követően konfigurálhatja az importálás **speciális beállítások** a Log Analytics-munkaterület az Azure Portalon.  Válassza ki **számítógépcsoportok**, **SCCM**, majd **importálása a Configuration Manager-gyűjteménytagságok**.  Nincs szükség további konfigurációra.

![Számítógépcsoportok az SCCM-ből](media/computer-groups/configure-sccm.png)

Gyűjtemények lettek importálva, ha a menüben a csoport tagjai észlelt számítógépek számát és a csoport importálva számát sorolja fel.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

## <a name="managing-computer-groups"></a>Számítógépcsoportok kezelése
Megtekintheti a napló lekérdezés vagy a Log Search API-jával létrehozott számítógépcsoportok **speciális beállítások** a Log Analytics-munkaterület az Azure Portalon.  Válassza ki **számítógépcsoportok** , majd **mentett csoportok**.  

Kattintson a **x** a a **eltávolítása** törölni a számítógép (csoport) oszlopban.  Kattintson a **tagok megtekintése** ikon egy csoportra, amely visszaadja a tagok a csoport Naplókeresés futtatásához.  Egy számítógép (csoport) nem módosítható, de ehelyett kell törölje és hozza létre újra a módosított beállításokkal.

![Mentett számítógépcsoportok](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Számítógépcsoport használata a log lekérdezésben
Lekérdezés log lekérdezésből való kezelésével svému aliasu függvényében, általában a következő szintaxist a létrehozott számítógépcsoport használata:

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
Egy rekord jön létre minden egyes létrehozott Active Directory vagy a WSUS számítógépcsoport-tagság a Log Analytics-munkaterületen.  Ezeket a rekordokat rendelkezik olyan típusú **ComputerGroup** , és a tulajdonságait az alábbi táblázatban.  Rekordok nem jön létre számítógépcsoportokat log lekérdezések alapján.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |A számítógép neve. |
| `Group` |A csoport nevét. |
| `GroupFullName` |Teljes elérési útja a csoporthoz, többek között a forrás- és adatforrás neve. |
| `GroupSource` |Forrás-csoporthoz való gyűjtése történt. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |A forrás, az összegyűjtött a csoport nevére.  Az Active Directory Ez a tartománynév. |
| `ManagementGroupName` |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetén ez AOI -\<munkaterület azonosítója\> |
| `TimeGenerated` |Dátum és időpont a számítógép (csoport) létrehozott vagy frissített. |

## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  

