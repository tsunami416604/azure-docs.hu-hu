---
title: Számítógépcsoportok a Azure Monitor log lekérdezésekben | Microsoft Docs
description: A Azure Monitorban lévő számítógépcsoportok lehetővé teszik a naplózási lekérdezések hatókörét a számítógépek adott csoportjára.  Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel számítógépcsoportok hozhatók létre, és hogyan használhatók a naplók a naplózási lekérdezésekben.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 9ef0f2810252b73921fc0a72f2e523262c760bab
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932657"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Számítógépcsoportok a Azure Monitor log lekérdezésekben
A Azure Monitorban lévő számítógépcsoportok lehetővé teszik a [naplózási lekérdezések](../log-query/log-query-overview.md) hatókörét a számítógépek adott csoportjára.  Minden csoport a számítógépekkel együtt van feltöltve a megadott lekérdezés használatával vagy különböző forrásokból származó csoportok importálásával.  Ha a csoport egy napló lekérdezésében szerepel, az eredmények a csoport számítógépeinek megfelelő rekordokra korlátozódnak.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Számítógépcsoport létrehozása
Az alábbi táblázatban felsorolt módszerek bármelyikével létrehozhat egy számítógépcsoportot a Azure Monitorban.  Az egyes módszerekkel kapcsolatos részletes információkat az alábbi részben találja. 

| Módszer | Leírás |
|:--- |:--- |
| Napló lekérdezése |Hozzon létre egy olyan log-lekérdezést, amely a számítógépek listáját adja vissza. |
| Naplókeresési API |A log Search API használatával programozott módon hozhat létre számítógépcsoportot a naplófájlok eredményei alapján. |
| Active Directory |A csoport tagjainak automatikus vizsgálata minden olyan ügynök számítógépén, amely egy Active Directory tartomány tagja, és minden egyes biztonsági csoport számára létrehoz egy csoportot Azure Monitor. (Csak Windows-gépek esetén)|
| Configuration Manager | Gyűjtemények importálása a System Center Configuration Managerból, és mindegyikhez hozzon létre egy csoportot a Azure Monitorban. |
| Windows Server Update Services |Automatikusan vizsgálja meg a WSUS-kiszolgálókat vagy-ügyfeleket a célcsoportok célzásához, és hozzon létre egy csoportot Azure Monitorban. |

### <a name="log-query"></a>Napló lekérdezése
A napló lekérdezésből létrehozott számítógépcsoportok tartalmazzák az Ön által meghatározott lekérdezés által visszaadott összes számítógépet.  A rendszer minden alkalommal futtatja ezt a lekérdezést, hogy a csoport létrehozása óta minden változás tükröződik.  

Bármilyen lekérdezést használhat egy számítógépcsoport számára, de a `distinct Computer`használatával a számítógépek különálló készletét kell visszaadnia.  Az alábbiakban egy tipikus példát tartalmazó lekérdezés látható, amelyet számítógép-csoportként használhat.

    Heartbeat | where Computer contains "srv" | distinct Computer

A következő eljárással hozhat létre számítógépcsoport-keresést a Azure Portal.

1. A Azure Portal **Azure monitor** menüjében kattintson a **naplók** elemre.
1. Hozzon létre és futtasson egy lekérdezést, amely visszaadja a csoportba felhasználandó számítógépeket.
1. Kattintson a képernyő felső részén található **Mentés** gombra.
1. Módosítsa a **Mentés másként** **funkciót** , és válassza a **lekérdezés mentése számítógépcsoportként**lehetőséget.
1. Adja meg a táblázatban leírt számítógépcsoport minden tulajdonságának értékeit, majd kattintson a **Mentés**gombra.

A következő táblázat a számítógépcsoport definiálásának tulajdonságait ismerteti.

| Tulajdonság | Leírás |
|:---|:---|
| Név   | A portálon megjelenítendő lekérdezés neve. |
| Függvény aliasa | Egyedi alias, amely a számítógépcsoport azonosítására szolgál a lekérdezésben. |
| Kategória       | A lekérdezéseknek a portálon való rendszerezésének kategóriája. |


### <a name="active-directory"></a>Active Directory
Ha a Azure Monitor Active Directory csoporttagság importálására konfigurálja, akkor a a Log Analytics ügynökkel rendelkező Windows tartományhoz csatlakozó számítógépek csoportjának tagságát elemzi.  A rendszer létrehoz egy számítógépcsoportot Azure Monitor a Active Directory minden egyes biztonsági csoportjához, és mindegyik Windows-számítógép hozzá lesz adva a azon biztonsági csoportokhoz tartozó számítógép-csoportokhoz, amelyek tagjai a csoportnak.  Ez a tagság 4 óránként folyamatosan frissül.  

> [!NOTE]
> Az importált Active Directory csoportok csak Windows rendszerű gépeket tartalmaznak.

A Azure Monitor konfigurálható úgy, hogy Active Directory biztonsági csoportokat importáljon a Azure Portal Log Analytics munkaterületének **speciális beállításaiból** .  Válassza ki a **számítógépcsoportok**elemet, **Active Directory**, majd **importálja Active Directory csoporttagság a számítógépekről**.  Nincs szükség további konfigurációra.

![Számítógépcsoportok Active Directory](media/computer-groups/configure-activedirectory.png)

Ha a csoportok importálása megtörtént, a menü felsorolja a csoporttagság által észlelt számítógépek számát és az importált csoportok számát.  A hivatkozások bármelyikére kattintva visszaküldheti a **ComputerGroup** -rekordokat ezekkel az információkkal.

### <a name="windows-server-update-service"></a>Windows Server Update szolgáltatás
Ha a WSUS-csoporttagságok importálását Azure Monitor konfigurálja, az elemzi a Log Analytics ügynökkel rendelkező számítógépek célcsoport-kezelési csoportjának tagságát.  Ha ügyféloldali célzást használ, a Azure Monitorhoz csatlakoztatott és a WSUS-célcsoportok részét képező bármely számítógép rendelkezik a csoporttagság importálásával Azure Monitor. Ha kiszolgálóoldali célzást használ, akkor a Log Analytics ügynököt a WSUS-kiszolgálóra kell telepíteni ahhoz, hogy a csoporttagság adatai Azure Monitorba legyenek importálva.  Ez a tagság 4 óránként folyamatosan frissül. 

A Azure Monitor konfigurálható úgy, hogy a Azure Portal Log Analytics munkaterületének **speciális beállításaiból** importálja a WSUS-csoportokat.  Válassza ki a **számítógépcsoportok**, a **WSUS**, majd a **WSUS-csoporttagságok importálása**lehetőséget.  Nincs szükség további konfigurációra.

![A WSUS-beli számítógépcsoportok](media/computer-groups/configure-wsus.png)

Ha a csoportok importálása megtörtént, a menü felsorolja a csoporttagság által észlelt számítógépek számát és az importált csoportok számát.  A hivatkozások bármelyikére kattintva visszaküldheti a **ComputerGroup** -rekordokat ezekkel az információkkal.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Ha a Azure Monitor Configuration Manager gyűjteményi tagságok importálására konfigurálja, akkor minden gyűjteményhez létrehoz egy számítógépcsoportot.  A gyűjtemény tagsági adatait 3 óránként kéri le a rendszer, hogy a számítógépcsoportok naprakészek maradjanak. 

Configuration Manager gyűjtemények importálása előtt [csatlakoznia kell a Configuration Managerhoz a Azure monitorhoz](collect-sccm.md).  

![SCCM származó számítógépcsoportok](media/computer-groups/configure-sccm.png)

A gyűjtemények importálása után a menü felsorolja az észlelt csoporttagság és az importált csoportok számát.  A hivatkozások bármelyikére kattintva visszaküldheti a **ComputerGroup** -rekordokat ezekkel az információkkal.

## <a name="managing-computer-groups"></a>Számítógépcsoportok kezelése
Megtekintheti a naplózási lekérdezésből létrehozott számítógép-csoportokat, illetve a naplóbeli keresési API-t a Azure Portal Log Analytics munkaterületének **speciális beállításai** között.  Válassza **ki a számítógépcsoportok elemet** , majd **mentse a csoportokat**.  

Kattintson az **Eltávolítás** oszlopban található **x** elemre a számítógépcsoport törléséhez.  Kattintson a **tagok megtekintése** ikonra egy csoport számára a csoport naplóbeli keresésének futtatásához, amely a tagjait adja vissza.  A számítógépcsoport nem módosítható, hanem törölnie kell, majd újra létre kell hoznia a módosított beállításokkal.

![Mentett számítógépcsoportok](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Számítógépcsoport használata egy naplózási lekérdezésben
A lekérdezésekben egy, az alias függvényként való kezelésével létrehozott számítógépcsoportot használ, jellemzően a következő szintaxissal:

  `Table | where Computer in (ComputerGroup)`

Például az alábbi paranccsal adhat vissza updateSummary típusú-rekordokat csak a mycomputergroup nevű számítógépcsoport számítógépeihez.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Az importált számítógépcsoportok és a hozzájuk tartozó számítógépek a **ComputerGroup** táblában tárolódnak.  Például a következő lekérdezés visszaküldi a tartományi számítógépek csoport számítógépeinek listáját Active Directoryból. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A következő lekérdezés csak a tartományi számítógépeken lévő számítógépek updateSummary típusú rekordjait fogja visszaadni.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Számítógépcsoport rekordjai
A rendszer létrehoz egy rekordot a Log Analytics munkaterületen minden Active Directory vagy WSUS szolgáltatásból létrehozott számítógépcsoport-tagsághoz.  Ezek a rekordok egy **ComputerGroup** rendelkeznek, és a következő táblázatban található tulajdonságokkal rendelkeznek.  A rendszer nem hoz létre rekordokat a számítógép-csoportokhoz a naplózási lekérdezések alapján.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |A tag számítógépének neve. |
| `Group` |A csoport neve. |
| `GroupFullName` |A csoport teljes elérési útja, beleértve a forrás-és a forrás nevét. |
| `GroupSource` |Az a forrás, amelyből a csoportot gyűjtötték. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Annak a forrásnak a neve, amelyből a csoportot gyűjtötték.  Active Directory esetén ez a tartománynév. |
| `ManagementGroupName` |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetében ez az AOI-\<munkaterület-azonosító\> |
| `TimeGenerated` |A számítógépcsoport létrehozásának vagy frissítésének dátuma és időpontja. |

## <a name="next-steps"></a>Következő lépések
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .  

