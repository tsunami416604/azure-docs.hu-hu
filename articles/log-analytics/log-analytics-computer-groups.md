---
title: Az Azure Naplóelemzés számítógépcsoportok jelentkezzen keresések |} Microsoft Docs
description: A Naplóelemzési számítógépcsoportok lehetővé teszik hatókör napló kereséseket számítógépek egy adott készletét.  Ez a cikk ismerteti a különböző módszerek segítségével hozzon létre számítógépcsoportokat és a naplófájl-keresési használatával.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: a6f0aa58762966f8da76387f3da7a7895801fcb9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>A Naplóelemzési számítógépcsoportok jelentkezzen keresések

A Naplóelemzési számítógépcsoportok lehetővé teszi a hatókör [keresések jelentkezzen](log-analytics-log-search-new.md) a számítógépek egy adott készletét.  Minden egyes csoport fel van töltve, vagy az Ön által meghatározott lekérdezés segítségével számítógépek vagy csoportok különböző forrásokból származó importálásával.  A csoport szerepel egy naplófájl-keresési, amikor a program csak a számítógép a megfelelő rekordok.

## <a name="creating-a-computer-group"></a>Olyan számítógép-csoport létrehozása
Log Analytics használata a módszereket az alábbi táblázatban is létrehozhat egy számítógép (csoport).  Az egyes módszerek részletei is szerepelnek, az alábbi szakaszokban. 

| Módszer | Leírás |
|:--- |:--- |
| Naplókeresés |Hozzon létre egy naplófájl-keresés, amely számítógépek listáját adja vissza. |
| Log Search API |A naplófájl-keresési API segítségével szoftveresen hozzon létre egy számítógépcsoportot, a naplófájl-keresési eredmények alapján. |
| Active Directory |Active Directory-tartomány tagja, és hozzon létre egy csoportot a Naplóelemzési minden egyes biztonsági csoport ügynök számítógépek csoport tagsága automatikusan vizsgálata. |
| Configuration Manager | Gyűjteményeket importálhat a System Center Configuration Manager és az egyes hozzon létre egy csoportot Naplóelemzési. |
| A Windows Server Update Services |Automatikusan keresése a WSUS-kiszolgálók és ügyfelek csoportok megcélzása, és hozzon létre egy csoportot a Naplóelemzési minden. |

### <a name="log-search"></a>Naplókeresés
Naplófájl-keresési létre számítógépcsoportok tartalmaz minden Ön által meghatározott egy lekérdezés által visszaadott számítógép.  Ez a lekérdezés futtatása minden alkalommal, amikor a számítógép (csoport) szolgál, hogy a csoport létrehozása óta módosításokat is megjelenik.  

Minden számítógépcsoport használható, de egy meghatározott készletét számítógépek használatával kell vissza `distinct Computer`.  Az alábbiakban az számítógépcsoportként használhat például keresést.

    Heartbeat | where Computer contains "srv" | distinct Computer

A következő táblázat ismerteti a számítógépcsoport meghatározó tulajdonságok biztosítása.

| Tulajdonság | Leírás |
|:---|:---|
| Megjelenítendő név   | A Keresés a portálon megjelenítendő neve. |
| Kategória       | A portál a keres rendszerezésére kategóriát. |
| Lekérdezés          | A lekérdezés a számítógép (csoport). |
| Függvényalias | Egy egyedi alias alapján határozza meg a számítógépcsoport a lekérdezésben. |

A következő eljárással hozzon létre egy számítógépcsoportot napló keresési az Azure portálon.

2. Nyissa meg **naplófájl-keresési** majd **mentett keresések** a képernyő tetején.
3. Kattintson a **Hozzáadás** és adjon meg értékeket a számítógép csoport minden egyes tulajdonsága.
4. Válassza ki **lekérdezés mentése számítógépcsoportként** kattintson **OK**.


A következő eljárással hozzon létre egy számítógépcsoportot napló keresési az OMS-portálon.

1. Nyissa meg **naplófájl-keresési** , és hozzon létre a naplófájl keressen rá a számítógép (csoport).  
2. Kattintson a **mentése** gomb a képernyő tetején.
3. Válassza ki **Igen** való **lekérdezés mentése számítógépcsoportként**.
5. Adja meg a számítógépcsoport mindegyik tulajdonság értékét. 



### <a name="active-directory"></a>Active Directory
Konfigurálja az Active Directory-csoporttagságok importálása Naplóelemzési, megvizsgálja a csoporttagság bármely tartományhoz csatlakoztatott számítógép az OMS-ügynököt.  Egy számítógép (csoport) minden egyes biztonsági csoport az Active Directoryban létrejön a Naplóelemzési, és minden számítógép nincs felvéve a biztonsági csoportok tagjai megfelelő számítógépcsoportokra.  A csoporttagság folyamatosan frissítjük 4 óránként.  

Konfigurálja az Active Directory biztonsági csoportok importálása Naplóelemzési Naplóelemzési **speciális beállítások** az Azure portálon.  Válassza ki **számítógépcsoportok**, **Active Directory**, majd **csoporttagságok importálása az Active Directory számítógépek**.  Nincs szükség további konfigurációra.

![A számítógépcsoportok Active Directoryból](media/log-analytics-computer-groups/configure-activedirectory.png)

Ha csoportok importálta, a menü észlelt csoporttagsággal rendelkező számítógépek számát és a csoportok importálása számát sorolja fel.  Akár a hivatkozások való visszatéréshez kattintson a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="windows-server-update-service"></a>A Windows Server Update Service
Konfigurálja a WSUS-csoporttagságok importálása Naplóelemzési, megvizsgálja a célcsoport-kezelési csoportba tartozik az OMS-ügynökkel rendelkező számítógépek.  Használata ügyféloldali célcsoport-kezelési, Log Analyticshez csatlakozik, és a WSUS részét számítógépek csoportok megcélzása rendelkezik Naplóelemzési importált csoport tagságát. Használata Kiszolgálóoldali célcsoport-kezelési, a OMS ügynököt kell telepíteni ahhoz, hogy a csoporttagsági információkat Naplóelemzési importálásra WSUS-kiszolgálón.  A csoporttagság folyamatosan frissítjük 4 óránként. 

Konfigurálhatja a WSUS-csoportok importálása Naplóelemzési Naplóelemzési **speciális beállítások** az Azure portálon.  Válassza ki **számítógépcsoportok**, **WSUS**, majd **importálási WSUS-csoporttagságok**.  Nincs szükség további konfigurációra.

![A WSUS számítógépcsoportok](media/log-analytics-computer-groups/configure-wsus.png)

Ha csoportok importálta, a menü észlelt csoporttagsággal rendelkező számítógépek számát és a csoportok importálása számát sorolja fel.  Akár a hivatkozások való visszatéréshez kattintson a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="system-center-configuration-manager"></a>System Center Configuration Managerben
Naplóelemzési importálása a Configuration Manager gyűjteménytagságok konfigurálásakor gyűjtemény számítógépcsoport hoz létre.  A gyűjtemény tagsági információk naprakészen tartása a számítógépcsoportok nélkül 3 óránként. 

A Configuration Manager a gyűjtemények importálásához előtt [csatlakoztatása a Configuration Manager szolgáltatáshoz](log-analytics-sccm.md).  Ezután úgy konfigurálhatja az importálás Naplóelemzési **speciális beállítások** az Azure portálon.  Válassza ki **számítógépcsoportok**, **SCCM**, majd **importálása a Configuration Manager gyűjteménytagságok**.  Nincs szükség további konfigurációra.

![Számítógépcsoportok az SCCM-ből](media/log-analytics-computer-groups/configure-sccm.png)

Amikor gyűjtemények importálta, a menü észlelt csoporttagsággal rendelkező számítógépek számát és a csoportok importálása számát sorolja fel.  Akár a hivatkozások való visszatéréshez kattintson a **ComputerGroup** ezeket az adatokat rögzíti.

## <a name="managing-computer-groups"></a>Számítógépcsoportok kezelése
Megtekintheti a napló keresési vagy a napló keresési API-t a Naplóelemzési alapján létrehozott számítógépcsoportok **speciális beállítások** az Azure portálon.  Válassza ki **számítógépcsoportok** , majd **csoportok mentett**.  

Kattintson a **x** a a **eltávolítása** oszlop a számítógép-csoport törléséhez.  Kattintson a **tagok megtekintéséhez** egy csoport, amely a tagot ad vissza a csoporthoz tartozó napló keresés futtatásához ikonjára.  Olyan számítógép-csoport nem módosítható, de ehelyett kell törölje és hozza létre újra a módosított beállításokkal.

![Mentett számítógépcsoportokat](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Számítógépcsoport használata egy napló keresése
Egy számítógépcsoport hozta létre a lekérdezésben napló keresési függvényében, amely általában a következő szintaxist a alias kezelésére használhatja:

  `Table | where Computer in (ComputerGroup)`

Például a következő segítségével adja vissza UpdateSummary rekordok csak számítógépek mycomputergroup nevű számítógép csoport.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Importált számítógépcsoportokat és belefoglalt számítógépeik tárolódnak a **ComputerGroup** tábla.  Például a következő lekérdezés alakítanák vissza számítógépeknek a listáját, a tartományi számítógépek csoportban az Active Directoryból. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A következő lekérdezés csak a számítógépek UpdateSummary rekordok meghaladná a tartományi számítógépek.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Számítógép rekordcsoportjának
A Naplóelemzési munkaterület minden létrehozott Active Directory vagy a WSUS számítógépcsoport-tagság a rekord jön létre.  Ezeket a rekordokat típusa lehet **ComputerGroup** , és a tulajdonságok az alábbi táblázatban.  Rekordok nem jönnek létre a napló keresések alapján számítógépcsoportokhoz.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computer |A számítógép nevét. |
| Csoport |A csoport nevét. |
| GroupFullName |Teljes elérési útja a csoporthoz, beleértve a forrás és a forrás neve. |
| GroupSource |Forrás-csoport a gyűjtése történt. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |A forrás az összegyűjtött a csoport nevére.  Active Directory szolgáltatásban ez pedig a tartomány nevét. |
| ManagementGroupName |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök, ez pedig AOI -\<munkaterület azonosítója\> |
| TimeGenerated |Dátum és idő, a számítógép csoport létrehozásakor vagy frissítésekor. |

## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.  

