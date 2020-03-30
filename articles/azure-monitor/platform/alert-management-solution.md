---
title: Riasztáskezelési megoldás az Azure Log Analytics szolgáltatásban | Microsoft dokumentumok
description: A Log Analytics riasztáskezelési megoldása segít a környezetösszes riasztás elemzésében.  A Log Analytics szolgáltatásban létrehozott riasztások konszolidálása mellett a csatlakoztatott System Center Operations Manager felügyeleti csoportokból származó riasztásokat is importálja a Log Analytics szolgáltatásba.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668383"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Riasztáskezelési megoldás az Azure Log Analytics szolgáltatásban

![A Riasztáskezelés ikonja](media/alert-management-solution/icon.png)

> [!NOTE]
>  Az Azure Monitor mostantól támogatja [a riasztások nagy méretekben való kezelésére](https://aka.ms/azure-alerts-overview)szolgáló továbbfejlesztett képességeket, beleértve azolyan [figyelési eszközök, mint a System Center Operations Manager, a Zabbix vagy a Nagios által létrehozott funkciókat.](https://aka.ms/managing-alerts-other-monitoring-services)
>  


A riasztáskezelési megoldás segít a Log Analytics-tárházban lévő összes riasztás elemzésében.  Ezek a riasztások különböző forrásokból származhatnak, beleértve a [Log Analytics által létrehozott](../../azure-monitor/platform/alerts-overview.md) vagy a [Nagios vagy Zabbix-ból importált](../../azure-monitor/learn/quick-collect-linux-computer.md)forrásokat is. A megoldás a csatlakoztatott System Center Operations Manager felügyeleti csoportokriasztásait is [importálja.](../../azure-monitor/platform/om-agents.md)

## <a name="prerequisites"></a>Előfeltételek
A megoldás a Log Analytics-tárházban lévő bármely **Alert**rekorddal együttműködik egy riasztástípussal, ezért a rekordok összegyűjtéséhez bármilyen konfigurációt el kell végeznie.

- A Log Analytics-riasztások esetén [hozzon létre riasztási szabályokat,](../../azure-monitor/platform/alerts-overview.md) hogy közvetlenül a tárházban hozzon létre riasztási rekordokat.
- Nagios és Zabbix riasztások esetén [konfigurálja ezeket a kiszolgálókat](../../azure-monitor/learn/quick-collect-linux-computer.md) úgy, hogy riasztásokat küldjenek a Log Analytics szolgáltatásnak.
- A System Center Operations Manager riasztásai esetén [csatlakoztassa az Operations Manager felügyeleti csoportját a Log Analytics-munkaterülethez.](../../azure-monitor/platform/om-agents.md)  A System Center Operations Manager alkalmazásban létrehozott riasztások importálása a Log Analytics programba történik.  

## <a name="configuration"></a>Konfiguráció
Adja hozzá a Riasztáskezelési megoldást a Log Analytics-munkaterülethez a [Megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című részben ismertetett folyamat tal. Nincs szükség további konfigurációra.

## <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoport csatlakozik a Log Analytics-munkaterülethez, akkor a következő felügyeleti csomagok települnek a System Center Operations Manager-be, amikor hozzáadja ezt a megoldást.  A felügyeleti csomagok nem konfigurálva vagy karbantartásra van szükség.

* Microsoft System Center Advisor riasztáskezelés (Microsoft.IntelligencePacks.AlertManagement)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Adatgyűjtés
### <a name="agents"></a>Ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|:--- |:--- |:--- |
| [Windows-ügynökök](agent-windows.md) | Nem |A közvetlen Windows-ügynökök nem hoznak létre riasztásokat.  A Log Analytics-riasztások a Windows-ügynököktől gyűjtött eseményekből és teljesítményadatokból hozhatók létre. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem |A közvetlen Linux-ügynökök nem hoznak létre riasztásokat.  A Log Analytics-riasztások a Linux-ügynököktől gyűjtött eseményekből és teljesítményadatokból hozhatók létre.  A Nagios és zabbix riasztásokat azoktól a szerverektől gyűjtik, amelyek a Linux-ügynököt igénylik. |
| [System Center Operations Manage felügyeleti csoport](../../azure-monitor/platform/om-agents.md) |Igen |Az Operations Manager-ügynökök által létrehozott riasztások at a felügyeleti csoportba továbbítja, majd továbbítja a Log Analytics szolgáltatásba.<br><br>Az Operations Manager-ügynökök és a Log Analytics közvetlen kapcsolata nem szükséges. A riasztási adatok továbbítása a felügyeleti csoportból a Log Analytics-tárházba. |


### <a name="collection-frequency"></a>A gyűjtés gyakorisága
- Riasztási rekordok érhetők el a megoldás, amint azok a tárházban tárolják.
- A riasztási adatokat az Operations Manager felügyeleti csoport hárompercenként elküldi a Log Analytics szolgáltatásnak.  

## <a name="using-the-solution"></a>A megoldás használata
Amikor hozzáadja a Riasztáskezelési megoldást a Log Analytics-munkaterülethez, a **Riasztáskezelés** csempe hozzáadódik az irányítópulthoz.  Ez a csempe az elmúlt 24 órában létrehozott aktív riasztások számát és grafikus megjelenítését jeleníti meg.  Ezt az időtartományt nem módosíthatja.

![Riasztáskezelés csempe](media/alert-management-solution/tile.png)

Kattintson a **Riasztáskezelés** csempére a **Riasztáskezelés** irányítópult megnyitásához.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak.  Minden oszlop a 10 legfontosabb riasztást sorolja fel az adott oszlop adott hatókörre és időtartományra vonatkozó feltételeinek megfelelő szám szerint.  A teljes listát tartalmazó naplókeresést úgy futtathatja, hogy az oszlop alján található **Összes** megtekintése gombra vagy az oszlopfejlécre kattint.

| Oszlop | Leírás |
|:--- |:--- |
| Kritikus riasztások |Minden riasztás, amelynek súlyossága kritikus riasztás neve szerint csoportosítva.  Kattintson egy riasztás nevére a riasztás összes rekordját visszaadó naplókeresés futtatásához. |
| Figyelmeztető jelzések |Minden figyelmeztetés súlyosságú figyelmeztetés riasztás neve szerint csoportosítva.  Kattintson egy riasztás nevére a riasztás összes rekordját visszaadó naplókeresés futtatásához. |
| Active System Center Operations Manager-riasztások |Az Operations Manager által a *riasztást* létrehozó forrás szerint csoportosított, zárt tól eltérő állapottal kapott összes riasztást. |
| Minden aktív értesítés |Minden riasztás bármilyen súlyosságú riasztás neve szerint csoportosítva. Csak a *Lezárt*állapottól eltérő állapotú Operations Manager-riasztásokat tartalmazza. |

Ha jobbra görget, az irányítópult több gyakori lekérdezést sorol fel, amelyekre kattintva [naplókeresést](../../azure-monitor/log-query/log-query-overview.md) végezhet a riasztási adatokra.

![Riasztáskezelés irányítópult](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Riasztáskezelés megoldás minden olyan rekordot elemez, amely egy riasztástípussal **van elhelyezve.**  A Log Analytics által létrehozott vagy a Nagios vagy zabbix által gyűjtött riasztásokat a megoldás nem gyűjti közvetlenül.

A megoldás importálási riasztások system center Operations Manager és létrehoz egy megfelelő rekordot minden egy-egy típusú **riasztás** és a SourceSystem of **OpsManager.**  Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:  

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*Riasztás* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Annak az adatelemnek a részletei, amely a riasztást XML formátumban hozta létre. |
| `AlertDescription` |A riasztás részletes leírása. |
| `AlertId` |A riasztás GUID azonosítója. |
| `AlertName` |A riasztás neve. |
| `AlertPriority` |A riasztás prioritási szintje. |
| `AlertSeverity` |A riasztás súlyossági szintje. |
| `AlertState` |A riasztás legutóbbi megoldási állapota. |
| `LastModifiedBy` |A riasztást utoljára módosító felhasználó neve. |
| `ManagementGroupName` |Annak a felügyeleti csoportnak a neve, ahol a riasztás létrejött. |
| `RepeatCount` |Azon alkalmak száma, amikor ugyanaz a riasztás jött létre ugyanahhoz a figyelt objektumhoz a feloldás óta. |
| `ResolvedBy` |A riasztást feloldó felhasználó neve. Üres, ha a riasztás még nem oldódott meg. |
| `SourceDisplayName` |A riasztást létrehozó figyelési objektum megjelenítendő neve. |
| `SourceFullName` |A riasztást létrehozó figyelési objektum teljes neve. |
| `TicketId` |A riasztás jegyazonosítója, ha a System Center Operations Manager környezet integrálva van a riasztásokjegy-hozzárendelési folyamatával.  Nincs jegyazonosítója. |
| `TimeGenerated` |A riasztás létrehozásának dátuma és időpontja. |
| `TimeLastModified` |A riasztás utolsó módosításának dátuma és időpontja |
| `TimeRaised` |A riasztás létrehozásának dátuma és időpontja. |
| `TimeResolved` |A riasztás feloldásának dátuma és időpontja. Üres, ha a riasztás még nem oldódott meg. |

## <a name="sample-log-searches"></a>Naplókeresési minták
Az alábbi táblázat mintanapló-kereséseket tartalmaz a megoldás által gyűjtött riasztási rekordokra: 

| Lekérdezés | Leírás |
|:---|:---|
| Riasztási &#124; ahol a SourceSystem == "OpsManager" és az AlertSeverity == "error" és a TimeRaised > ezelőtt(24h) |Az elmúlt 24 órában fellépő kritikus riasztások |
| Riasztási &#124; ahol AlertSeverity == "figyelmeztetés" és timeRaised > ezelőtt(24h) |Az elmúlt 24 órában figyelmeztető jelzések |
| Riasztási &#124; ahol a SourceSystem == "OpsManager" és a AlertState != "Closed" és a TimeRaised > ezelőtt(24h &#124;) a Count = count() összegzése a SourceDisplayName függvényben |Az elmúlt 24 órában bevezetett aktív riasztásokkal rendelkező források |
| Riasztási &#124; ahol a SourceSystem == "OpsManager" és az AlertSeverity == "error" és a TimeRaised > ezelőtt(24h) és alertstate != "Closed" |Az elmúlt 24 órában kapott kritikus riasztások, amelyek még aktívak |
| Riasztási &#124; ahol a SourceSystem == "OpsManager" és a TimeRaised > ezelőtt(24h) és alertstate == "Zárt" |Az elmúlt 24 órában már lezárt riasztások |
| Riasztási &#124; ahol a SourceSystem == "OpsManager" és a TimeRaised > ezelőtt(1d) &#124; összegezze a Count = count() függvényt AlertSeverity szerint |Az elmúlt 1 nap során feladott riasztások súlyosságuk szerint csoportosítva |
| Riasztási &#124; ahol a SourceSystem == "OpsManager" és a TimeRaised > ezelőtt(1d) &#124; repeatcount desc szerint rendez |Az elmúlt 1 nap során az ismétlési számlálási értékük szerint rendezve keltő riasztások |



## <a name="next-steps"></a>További lépések
* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](../../azure-monitor/platform/alerts-overview.md).
