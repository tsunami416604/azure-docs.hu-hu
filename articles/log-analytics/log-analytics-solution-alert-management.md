---
title: Azure Naplóelemzés megoldást kezelési riasztási |} Microsoft Docs
description: A riasztások kezelésében a Naplóelemzési megoldással elemzése összes a riasztásokat a környezetben.  Mellett konszolidálása riasztások Naplóelemzési vezérlőben akkor importálja riasztások csatlakoztatott System Center Operations Manager felügyeleti csoportokból származó Naplóelemzési.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: eb61a48e8c479db4742d65187b202655f29b032d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131047"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Az Azure Naplóelemzés riasztási felügyeleti megoldás

![Felügyeleti figyelmeztető ikon](media/log-analytics-solution-alert-management/icon.png)

A Riasztáskezelési megoldással a riasztásokat a Log Analyticshez tárházban összes elemzése.  Ezek a riasztások különböző forrásokból, beleértve az olyan forrásból származó jutott [Naplóelemzési által létrehozott](log-analytics-alerts.md) vagy [Nagios vagy Zabbix importált](log-analytics-linux-agents.md).  A megoldás is importálja a riasztások bármelyik [csatlakoztatott felügyeleti csoportok System Center Operations Manager](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Előfeltételek
A megoldás együttműködve biztosítja a Naplóelemzési tárházban típusú rekordok **riasztási**, ezért el kell végeznie a választott beállításokat kell megadnia ezeket a rekordokat gyűjtése.

- A Naplóelemzési riasztások [riasztási szabályok létrehozásához](log-analytics-alerts.md) közvetlenül a tárházban riasztási rekordok létrehozásához.
- Nagios és Zabbix riasztások [ezek a kiszolgálók konfigurálása](log-analytics-linux-agents.md) riasztások küldéséhez szolgáltatáshoz.
- A System Center Operations Manager-riasztások [az Operations Manager felügyeleti csoportjának összekapcsolása a Naplóelemzési munkaterület](log-analytics-om-agents.md).  Minden létrehozott System Center Operations Manager riasztást a rendszer importálta a Naplóelemzési.  

## <a name="configuration"></a>Konfiguráció
A Riasztáskezelési megoldás hozzáadni a Naplóelemzési munkaterület ismertetett eljárással [megoldások hozzáadása](log-analytics-add-solutions.md).  Nincs szükség további konfigurációra.

## <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoport csatlakoztatva van a Naplóelemzési munkaterület, majd a következő felügyeleti csomagjai vannak telepítve a System Center Operations Manager ebben a megoldásban hozzáadásakor.  Nincs, konfigurációs vagy a szükséges felügyeleti csomagok karbantartási.  

* A Microsoft System Center Advisor Riasztáskezelési (Microsoft.IntelligencePacks.AlertManagement)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](log-analytics-om-agents.md).

## <a name="data-collection"></a>Adatgyűjtés
### <a name="agents"></a>Ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|:--- |:--- |:--- |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Nem |A közvetlen Windows-ügynökök nem hoznak létre riasztásokat.  Napló Analytics riasztások az események hozhatók létre, és a Windows ügynökök gyűjtött teljesítményadatokat. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem |Közvetlen Linux-ügynökök nem hoznak létre riasztásokat.  Napló Analytics riasztások hozhatók létre az események és teljesítményadatok összegyűjtésére a Linux-ügynököt.  Nagios és Zabbix riasztások ezeken a kiszolgálókon, a Linux-ügynök igénylő gyűjtenek. |
| [System Center Operations Manager felügyeleti csoport](log-analytics-om-agents.md) |Igen |Az Operations Manager-ügynökök generált riasztások kézbesíteni a felügyeleti csoporthoz, és továbbítja a Naplóelemzési.<br><br>Közvetlen kapcsolat az Operations Manager-ügynökök szolgáltatáshoz nincs szükség. Riasztási adatokat a Naplóelemzési tárház továbbítódik a felügyeleti csoportból. |


### <a name="collection-frequency"></a>A gyűjtés gyakorisága
- Riasztási rekordok elérhetők a megoldás, amint a tárház tárolódnak.
- Riasztási adatokat küld az Operations Manager felügyeleti csoportból Naplóelemzési három percenként.  

## <a name="using-the-solution"></a>A megoldás használata
Ha a riasztás felügyeleti megoldás hozzáadni a Naplóelemzési munkaterület a **Riasztáskezelési** csempe az irányítópulton való rögzítéséhez kerül.  Ez a csempe egy száma és a grafikus ábrázolása az utolsó 24 órán belül létrehozott aktív riasztások számát jeleníti meg.  Az időtartomány nem módosítható.

![Kezelési riasztási csempe](media/log-analytics-solution-alert-management/tile.png)

Kattintson a **Riasztáskezelési** csempére kattintva nyissa meg a **Riasztáskezelési** irányítópult.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak.  Minden oszlop a felső 10 riasztások száma a megadott hatókör és időtartomány adott oszlop feltételeknek megfelelő sorolja fel.  Futtathat teljes listáját jeleníti meg, kattintson a napló keresés **láthatja az összes** alján az oszlop, vagy az oszlop fejlécére kattintva.

| Oszlop | Leírás |
|:--- |:--- |
| Kritikus riasztások |Minden riasztás kiegészített egy kritikus riasztás neve szerint csoportosítva.  Kattintson a riasztás nevét. a riasztás összes rekordot ad vissza napló keresés futtatásához. |
| Figyelmeztető riasztások |Minden riasztás kiegészített egy figyelmeztető riasztás neve szerint csoportosítva.  Kattintson a riasztás nevét. a riasztás összes rekordot ad vissza napló keresés futtatásához. |
| Aktív SCOM-riasztások |Az összes riasztás összegyűjtése az Operations Manager bármely állapotú eltérő *lezárva* a riasztást kiváltó forrás szerint csoportosítva. |
| Az összes aktív riasztás |Minden riasztás kiegészített bármely riasztás neve szerint csoportosítva. Csak tartalmazza az Operations Manager riasztásait bármely állapotú eltérő *lezárva*. |

Görgessen jobbra, ha az irányítópulton jeleníti meg több közös lekérdezések végrehajtásához kattintson egy [naplófájl-keresési](log-analytics-log-searches.md) riasztási adatok.

![Riasztási kezelési irányítópult](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Riasztáskezelési megoldás elemzi a típusú rekordot **riasztási**.  Nagios vagy Zabbix gyűjtött vagy Naplóelemzési által létrehozott riasztásokat nem közvetlenül a megoldás által gyűjtött.

A megoldás riasztások importálása a System Center Operations Manager, és mindegyik típussal rendelkező létrehoz egy megfelelő rekordot **riasztási** és az egy SourceSystem **OpsManager**.  Ezeket a rekordokat az alábbi táblázatban a jellemzőkkel rendelkezik:  

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*Riasztás* |
| SourceSystem |*OpsManager* |
| AlertContext |Az adatelem XML-formátumú generálása a riasztást kiváltó részleteit. |
| AlertDescription |A riasztás részletes leírása. |
| AlertId |A riasztás GUID. |
| AlertName |A riasztás nevét. |
| AlertPriority |A riasztás prioritását. |
| AlertSeverity |A riasztás súlyossági szintje. |
| AlertState |A riasztás legutóbbi feloldási állapota. |
| LastModifiedBy |A riasztás legutóbbi módosító felhasználó neve. |
| ManagementGroupName |A felügyeleti csoport, ahol a riasztás generálása neve. |
| RepeatCount |Ennyiszer ugyanahhoz a azonos riasztás generálása óta feloldott objektum figyeli. |
| ResolvedBy |A felhasználó a riasztás feloldva neve. Üres, ha a riasztás még nincs megoldott. |
| SourceDisplayName |A megfigyelési objektum, amely a riasztást előidézte megjelenített neve. |
| SourceFullName |A riasztást kiváltó figyelő objektum teljes neve. |
| TicketId |A riasztás, ha a System Center Operations Manager környezet integrálva van egy folyamatot, a riasztások a jegyektől hozzárendelése Jegyazonosító megadására.  Üres nem jegy azonosítója hozzá van rendelve. |
| TimeGenerated |Dátum és idő, amely a riasztás létrejött. |
| TimeLastModified |Dátum és a riasztás legutóbbi módosításának időpontja. |
| TimeRaised |Dátum és idő, amely a riasztást. |
| TimeResolved |Dátum és idő, amely a riasztás lett feloldva. Üres, ha a riasztás még nincs megoldott. |

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által gyűjtött riasztási rekordok minta napló keres: 

| Lekérdezés | Leírás |
|:---|:---|
| Riasztási &#124; ahol SourceSystem "OpsManager" és a AlertSeverity == "error" és a TimeRaised == > ago(24h) |Az elmúlt 24 órában kiadott kritikus riasztások |
| Riasztási &#124; ahol AlertSeverity "figyelmeztetés" és a TimeRaised == > ago(24h) |Az elmúlt 24 órában kiadott figyelmeztető riasztások |
| Riasztási &#124; ahol SourceSystem == "OpsManager" és a AlertState! = "Lezárva" és a TimeRaised > ago(24h) &#124; összesíteni a Count = count() SourceDisplayName által |Az elmúlt 24 órában kiadott aktív riasztásokkal rendelkező források |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a AlertSeverity == "error" és a TimeRaised == > ago(24h) és AlertState! = "Lezárva" |Az elmúlt 24 órában, amelyek még mindig aktív kritikus riasztások |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a TimeRaised == > ago(24h) és AlertState == "Lezárva" |Az elmúlt 24 órában, amely a már lezárt riasztások |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a TimeRaised == > ago(1d) &#124; összesíteni a Count = count() AlertSeverity által |Az elmúlt 1 napban kiadott riasztások súlyosságuk szerint csoportosítva |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a TimeRaised == > ago(1d) &#124; RepeatCount desc rendezés |Az elmúlt 1 napban kiadott riasztások ismétléseik száma szerint rendezve |



## <a name="next-steps"></a>További lépések
* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](log-analytics-alerts.md).
