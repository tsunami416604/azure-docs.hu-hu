---
title: Az Azure Service Bus-metrikák az Azure Monitor (előzetes verzió) |} A Microsoft Docs
description: Azure Monitoring segítségével figyelheti a Service Bus-entitások
services: service-bus-messaging
documentationcenter: .NET
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/31/2018
ms.author: spelluru
ms.openlocfilehash: b4865c1ba7532910ef0b4a41a5a19d2880e37d6e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698961"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Az Azure Service Bus-metrikák az Azure Monitor (előzetes verzió)

Service Bus-metrikák biztosít az Azure-előfizetésében erőforrások állapotát. Metrikai adatok széles skáláját felmérheti a Service Bus-erőforrások, nem csak a névterek szintjén, hanem az entitások szintjén általános állapotát. A statisztikai lehet fontos, mert ezek segítségével figyelheti a Service Bus állapotát. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

> [!IMPORTANT]
> Ha nem egy entitás végzett minden művelet 2 órán át lett, a metrikák akkor indul el addig, amíg az entitás már nem üresjárati "0", érték megjelenítése.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. Mindkét hozzáférési metrikák keresztül is a [az Azure portal](https://portal.azure.com), vagy használja az Azure Monitor API-k (REST és .NET) és az elemzési megoldások, például a Log Analytics és az Event Hubs. További információkért lásd: [Azure Monitor-metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) az Azure monitorban.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, és kattintson a **metrikák (Peview)**. Az entitás hatókörének szűrt metrikák megjelenítéséhez válassza ki az entitást, és kattintson a **metrikák (előnézet)**.

![][2]

Dimenziók támogató metrikákhoz a kívánt dimenzió értékkel kell szűrni.

## <a name="billing"></a>Számlázás

Metrikák az Azure Monitor használata az előzetes verzió ingyenes. Azonban használ további megoldásokat, amelyek fogadni a mérőszámadatokat, akkor előfordulhat, hogy után kell díjat ezek a megoldások által. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. Emellett a számlázás a Log Analytics által speciális elemzésekre szolgáló Log Analytics-metrikák adatok streamelése az, ha.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. A Service Bus-metrikák támogatott idő időköz 1 perc.

## <a name="request-metrics"></a>Kérelem-metrikák

Számít az adatok és a felügyeleti műveletek kérések száma.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| A bejövő kérések (előnézet) | Egy meghatározott időtartamon belül a Service Bus szolgáltatás felé irányuló kérelmek száma. <br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Sikeres kérések (előnézet)|A sikeres kérelmek száma a Service Bus szolgáltatásnak egy meghatározott időtartamon belül.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Kiszolgálóhibák (előzetes verzió)|Egy meghatározott időtartamon belül a Service Bus szolgáltatás hibája miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Felhasználói hibák (előzetes verzió – tekintse meg a következő szakasz)|Egy meghatározott időtartamon belül a felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Szabályozott kérelmeinek száma (előzetes verzió)|Szabályozott volt, mert túllépte a használati kérelmek száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|

### <a name="user-errors"></a>Felhasználói hibák

A következő két típusú hibák besorolt felhasználói hibáinak száma:

1. Ügyfél kiszolgálóoldali hibák (a HTTP 400 hibákat adott).
2. Például üzenetek feldolgozása közben jelentkező hibákról [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Üzenet-metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek (előzetes verzió)|Események vagy adott időszakon belül Service Busnak küldött üzenetek számát.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Kimenő üzenetek (előzetes verzió)|Események vagy egy meghatározott időtartamon belül a Service Bus Beérkezett üzenetek száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Aktív kapcsolatai (előzetes verzió)|A névtér, valamint egy entitására aktív kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Kapcsolatok megnyitott (előzetes verzió)|Megnyitott kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Kapcsolat lezárva (előzetes verzió)|Lezárt kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName |

## <a name="resource-usage-metrics"></a>Erőforrás-használati metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Processzorhasználat névterenként (előzetes verzió)|Százalékos processzorhasználat a névteret.<br/><br/> Egység: %-os <br/> Összesítés típusa: maximális <br/> Dimenzió: EntityName|
|Memória mérete névterenként (előzetes verzió)|A névtér százalékos memóriahasználatát.<br/><br/> Egység: %-os <br/> Összesítés típusa: maximális <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Service Bus a következő dimenziókat támogatja a mérőszámok az Azure Monitor. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| A Service Bus a névtérben üzenetküldési entitások támogatja.|

## <a name="next-steps"></a>További lépések

Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


