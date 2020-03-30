---
title: Számítógépcsoportok az Azure Monitor naplólekérdezésében | Microsoft dokumentumok
description: Az Azure Monitor számítógépcsoportok lehetővé teszik a naplólekérdezések hatókörét egy adott számítógépkészletre.  Ez a cikk a számítógépcsoportok létrehozásához használható különböző módszereket ismerteti, és azokat a naplólekérdezésekben való használatukhoz.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274774"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Számítógépcsoportok az Azure Figyelő naplólekérdezéseiben
Az Azure Monitor számítógépcsoportok lehetővé teszik a [naplólekérdezések](../log-query/log-query-overview.md) hatókörét egy adott számítógépkészletre.  Minden csoport számítógépekkel van feltöltve, amelyek egy ön által meghatározott lekérdezéssel vagy különböző forrásokból származó csoportok importálásával vannak feltöltve.  Ha a csoport szerepel egy naplólekérdezésben, az eredmények a csoport számítógépeinek megfelelő rekordokra korlátozódnak.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Számítógépcsoport létrehozása
Az alábbi táblázatban szereplő módszerek bármelyikével létrehozhat egy számítógépcsoportot az Azure Monitorban.  Az egyes módszerekre vonatkozó részleteket az alábbi szakaszok ismertetik. 

| Módszer | Leírás |
|:--- |:--- |
| Lekérdezés naplózása |Hozzon létre egy naplólekérdezést, amely a számítógépek listáját adja vissza. |
| Log Search API |A Naplókeresési API segítségével programozott módon hozzon létre egy számítógépcsoportot egy naplólekérdezés eredményei alapján. |
| Active Directory |Automatikusan ellenőrizze az Active Directory-tartományban tagsággal rendelkező ügynökszámítógépek csoporttagságát, és hozzon létre egy csoportot az Azure Monitorban az egyes biztonsági csoportokhoz. (Csak Windows gépek)|
| Configuration Manager | Gyűjteményeket importálhat a Microsoft Endpoint Configuration Manager programból, és mindegyikhez hozzon létre egy csoportot az Azure Monitorban. |
| Windows Server Update Services |Automatikusan ellenőrizze a WSUS-kiszolgálókat vagy -ügyfeleket a célzási csoportokról, és hozzon létre egy csoportot az Azure Monitorban. |

### <a name="log-query"></a>Lekérdezés naplózása
A naplólekérdezésből létrehozott számítógépcsoportok tartalmazzák a megadott lekérdezés által visszaadott összes számítógépet.  Ez a lekérdezés a számítógépcsoport minden egyes használatakor fut, így a csoport létrehozása óta végrehajtott módosítások is megjelennek.  

A számítógépcsoporthoz bármilyen lekérdezést használhat, de a használatával külön `distinct Computer`számítógép-készletet kell visszaadnia.  A következő egy tipikus példa lekérdezés, amely segítségével a számítógép-csoport.

    Heartbeat | where Computer contains "srv" | distinct Computer

Az alábbi eljárással hozzon létre egy számítógépcsoportot az Azure Portalon végzett naplókeresésből.

1. Kattintson a **Naplók elemre** az **Azure-portál Azure-figyelő** menüjében.
1. Hozzon létre és futtasson olyan lekérdezést, amely a csoportban a kívánt számítógépeket adja vissza.
1. Kattintson a **mentés** gombra a képernyő tetején.
1. Módosítsa a **Mentés függvényként** **lehetőséget,** és válassza **a Lekérdezés mentése számítógépcsoportként**lehetőséget.
1. Adja meg az egyes tulajdonságok értékeit a táblázatban ismertetett számítógépcsoporthoz, majd kattintson a **Mentés gombra.**

Az alábbi táblázat a számítógépcsoportot definiáló tulajdonságokat ismerteti.

| Tulajdonság | Leírás |
|:---|:---|
| Név   | A portálon megjelenítendő lekérdezés neve. |
| Függvény aliasa | A lekérdezés számítógépcsoportjának azonosítására használt egyedi alias. |
| Kategória       | Kategória a lekérdezések rendszerezéséhez a portálon. |


### <a name="active-directory"></a>Active Directory
Ha úgy konfigurálja az Azure Monitort, hogy importálja az Active Directory-csoporttagságokat, elemzi a Log Analytics-ügynökkel összekapcsolt Windows-tartománybeli számítógépek csoporttagságát.  Az Azure Monitorban létrejön egy számítógépcsoport az Active Directory minden egyes biztonsági csoportjához, és minden Windows-számítógép hozzáadódik azokhoz a biztonsági csoportokhoz, amelyeknek tagjai.  Ezt a tagságot 4 óránként folyamatosan frissítjük.  

> [!NOTE]
> Az importált Active Directory-csoportok csak Windows-gépeket tartalmaznak.

Az Azure Monitor konfigurálásával importálhatja az Active Directory biztonsági csoportjait az Azure Portalon a Log Analytics-munkaterület **speciális beállításaiból.**  Válassza **a Számítógépcsoportok**, **az Active Directory**lehetőséget, majd **importálja az Active Directory csoporttagságokat a számítógépekről**.  Nincs szükség további konfigurációra.

![Számítógépcsoportok az Active Directoryból](media/computer-groups/configure-activedirectory.png)

A csoportok importálása után a menü felsorolja az észlelt csoporttagsággal rendelkező számítógépek számát és az importált csoportok számát.  Ezekre a hivatkozásokra kattintva visszaküldheti a **ComputerGroup** rekordokat ezzel az információval.

### <a name="windows-server-update-service"></a>Windows Server update szolgáltatás
Ha úgy konfigurálja az Azure Monitort, hogy wsus-csoporttagságokat importáljon, elemzi a log analytics-ügynökkel rendelkező számítógépek célzási csoporttagságát.  Ha ügyféloldali célzást használ, minden olyan számítógép, amely csatlakozik az Azure Monitorhoz, és bármely WSUS-célzási csoport része, csoporttagságát importálta az Azure Monitorba. Ha kiszolgálóoldali célzást használ, a Log Analytics-ügynököt telepíteni kell a WSUS-kiszolgálóra annak érdekében, hogy a csoporttagsági adatok importálása az Azure Monitorba.  Ezt a tagságot 4 óránként folyamatosan frissítjük. 

Az Azure Monitor konfigurálásával importálhatja a WSUS-csoportokat a **Speciális beállításokból** az Azure Portalon a Log Analytics-munkaterületről.  Válassza a **Számítógépcsoportok**, **WSUS**lehetőséget, majd **importálja a WSUS csoporttagságokat.**  Nincs szükség további konfigurációra.

![Számítógépcsoportok a WSUS-tól](media/computer-groups/configure-wsus.png)

A csoportok importálása után a menü felsorolja az észlelt csoporttagsággal rendelkező számítógépek számát és az importált csoportok számát.  Ezekre a hivatkozásokra kattintva visszaküldheti a **ComputerGroup** rekordokat ezzel az információval.

### <a name="configuration-manager"></a>Configuration Manager
Ha úgy konfigurálja az Azure Monitort, hogy importálja a Configuration Manager-gyűjteménytagságokat, minden gyűjteményhez létrehoz egy számítógépcsoportot.  A gyűjteménytagsági adatok at 3 óránként olvassa be, hogy a számítógépcsoportok naprakészek maradjanak. 

A Configuration Manager-gyűjtemények importálása előtt csatlakoztatnia kell [a Configuration Manager t az Azure Monitorhoz.](collect-sccm.md)  

![Az SCCM számítógépcsoportjai](media/computer-groups/configure-sccm.png)

A gyűjtemények importálása után a menü felsorolja az észlelt csoporttagsággal rendelkező számítógépek számát és az importált csoportok számát.  Ezekre a hivatkozásokra kattintva visszaküldheti a **ComputerGroup** rekordokat ezzel az információval.

## <a name="managing-computer-groups"></a>Számítógépcsoportok kezelése
Megtekintheti a naplólekérdezésből vagy a Naplókeresési API-ból létrehozott számítógépcsoportokat az Azure Portalon a Log Analytics-munkaterület **speciális beállításaiból.**  Válassza a **Számítógépcsoportok,** majd a **Mentett csoportok lehetőséget.**  

A számítógépcsoport törléséhez kattintson az **Eltávolítás** oszlopban lévő **x** gombra.  Kattintson a **Tagok megtekintése** ikonra egy csoporthoz a csoport tagjait visszaadó naplókeresés futtatásához.  A számítógépcsoport nem módosítható, hanem törölnie kell, majd újra létre kell hoznia a módosított beállításokkal.

![Mentett számítógépcsoportok](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Számítógépcsoport használata naplólekérdezésben
A lekérdezésben naplólekérdezésből létrehozott számítógépcsoportot úgy használhatja, hogy az aliast függvényként kezeli, általában a következő szintaxissal:

  `Table | where Computer in (ComputerGroup)`

Az alábbi módon például csak a sajátszámítógépcsoport nak nevezett számítógépcsoport számítógépeinek Frissítés-összefoglaló rekordjait adja vissza.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Az importált számítógépcsoportok és a magukba foglalt számítógépek a **ComputerGroup** táblában tárolódnak.  A következő lekérdezés például a Tartományi számítógépek csoport számítógépeinek listáját adja vissza az Active Directoryból. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A következő lekérdezés csak a tartományi számítógépekhez adja vissza az UpdateSummary rekordokat.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Számítógépcsoport-rekordok
A Log Analytics-munkaterületen létrejön egy rekord az Active Directoryból vagy a WSUS szolgáltatásból létrehozott minden egyes számítógép-csoporttagsághoz.  Ezek a rekordok **számítógépcsoport-típussal** rendelkeznek, és az alábbi táblázatban található tulajdonságokkal rendelkeznek.  A naplólekérdezések alapján nem jönnek létre rekordok a számítógépcsoportokhoz.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*Számítógépcsoport* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |A tagszámítógép neve. |
| `Group` |A csoport neve. |
| `GroupFullName` |A csoport teljes elérési útja, beleértve a forrás- és forrásnevet is. |
| `GroupSource` |Forrás, hogy a csoport gyűjtötték. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientCélzás |
| `GroupSourceName` |Annak a forrásnak a neve, amelyből a csoportot gyűjtötték.  Az Active Directory esetében ez a tartománynév. |
| `ManagementGroupName` |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetében ez az\<AOI-munkaterület-azonosító\> |
| `TimeGenerated` |A számítógépcsoport létrehozásának vagy frissítésének dátuma és időpontja. |

## <a name="next-steps"></a>További lépések
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.  

