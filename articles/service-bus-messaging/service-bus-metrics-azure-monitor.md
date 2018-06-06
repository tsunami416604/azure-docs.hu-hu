---
title: Az Azure Service Bus metrikát a Azure Monitor (előzetes verzió) |} Microsoft Docs
description: Azure-figyelés használatával figyelheti a Service Bus-entitások
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/31/2018
ms.author: sethm
ms.openlocfilehash: bb0c9fcc33d6f5b54a8c2c8ad3e356a485d6ccbb
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701294"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Az Azure Service Bus metrikát a Azure Monitor (előzetes verzió)

A Service Bus metrikák lehetővé teszi az erőforrások az Azure-előfizetése állapotát. A metrikai adatok széles skáláját felmérheti a Service Bus erőforrásainak, nem csak a névterek szintjén, hanem az entitás szintjén általános állapotát. A statisztikai információk lehet fontos, mint annak elősegítése, hogy a Service Bus állapotának figyelése. Metrikák is hozzájárulhat a kiváltó okának elhárítása anélkül, hogy az Azure ügyfélszolgálatához.

Az Azure biztosít, egységes felhasználói felületek keresztüli különböző Azure-szolgáltatásokhoz. További információkért lásd: [figyelése a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md) és a [.NET metrikák beolvasása Azure figyelő](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

## <a name="access-metrics"></a>Hozzáférés metrikák

Az Azure hozzáférési metrikák több lehetőség is biztosít. Mindkét hozzáférési metrikák keresztül is a [Azure-portálon](https://portal.azure.com), vagy használja az Azure figyelő API-k (REST és .NET) és elemzési megoldások, például Naplóelemzés és az Event Hubs. További információkért lásd: [Azure figyelő metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és érheti el az utolsó 30 napnyi adat. Ha szeretné megőrizni az adatokat egy hosszabb ideig, úgy archiválhatók metrikai adatok az Azure Storage-fiók. Ez úgy van konfigurálva a [diagnosztikai beállítások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) Azure-figyelőben.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés metrikákat a portálon

Adott idő alatt a figyelheti a metrikák a [Azure-portálon](https://portal.azure.com). A következő példa bemutatja, hogyan sikeres és a bejövő kérelmeket a fiók szintjén megtekintése:

![][1]

Metrikák közvetlenül a névtér keresztül is elérheti. Ehhez jelölje ki a névteret, és kattintson a **metrikák (Peview)**. Az entitás hatókörébe szűrt metrikák megjelenítéséhez jelölje ki az entitást, és kattintson **metrikák (előzetes verzió)**.

![][2]

A dimenziók támogató metrika szűrheti kell a kívánt dimenzió értékű.

## <a name="billing"></a>Számlázás

Metrikák Azure figyelőben használata során preview ingyenes. Azonban további megoldások, amelyek a metrikai adatok betöltési használatakor, akkor előfordulhat, hogy számlázni ezek a megoldások. Például kell fizetni Azure Storage által archiválja metrikák egy Azure Storage-fiókhoz. Is meg számlázása Naplóelemzési által, ha adatfolyam formájában a speciális elemzésekre szolgáló metrikák Naplóelemzési adatokat.

A következő mérőszámokat adhat a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt is elavulttá több metrikákat. Ehhez szükség lehet, hogy frissítse a hivatkozásokat. A "elavult" kulcsszó jelölésű metrikák módosítástól fogva nem támogatott.

Minden értékeihez Azure figyelő percenként küldi. A részletességet határozza meg az az időtartam, amelynek metrikák értékek jelenjenek meg. A támogatott időtartam alatt, az összes Service Bus-metrikát értéke 1 perc.

## <a name="request-metrics"></a>Kérelem metrikák

Megjeleníti az adatok és felügyeleti műveletek kérelmek számát.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| A bejövő kérések (előzetes verzió) | A megadott időszakban a Service Bus szolgáltatás felé intézett kérések száma. <br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Sikeres kérések (előzetes verzió)|A sikeres kérelmek száma a Service Bus szolgáltatáshoz adott időszakon belül.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kiszolgálóhibákat (előzetes verzió)|Hiba történt a Service Bus szolgáltatásban egy adott időszakban nem feldolgozott kérelmek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Felhasználó hibákat (előzetes verzió – tekintse meg a következő szakasz)|A megadott időszakban felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Szabályozottan halmozott kérelmek (előzetes verzió)|A használati túllépése miatt volt halmozódni kérelmek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

### <a name="user-errors"></a>Felhasználói hibák

A következő két típusú hibák besorolt felhasználói hibák:

1. Ügyfél oldali hibák (a HTTP 400 hibák lesznek).
2. Például üzenetek feldolgozása során előforduló hibákat [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Üzenet metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|A bejövő üzenetek (előzetes verzió)|Események vagy adott időszakon belül a Service Bus küldött üzenetek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kimenő üzenetek (előzetes verzió)|Események vagy adott időszakon belül a Service Bus fogadott üzenetek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="connection-metrics"></a>Kapcsolati metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|ActiveConnections (előzetes verzió)|Egy névtér, valamint egy entitás aktív kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kapcsolatok Opened (előzetes verzió)|A nyitott kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kapcsolatok lezárva (előzetes verzió)|Lezárt kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName |

## <a name="resource-usage-metrics"></a>Erőforrás-használati metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|CPU-használat / névtér (előzetes verzió)|A százalékos CPU-használat a névtér.<br/><br/> Egység: százaléka <br/> Összesítési típusát: maximális <br/> Dimenzió: EntityName|
|Memóriahasználat mérete / névtér (előzetes verzió)|A névtér százalékos memória használata.<br/><br/> Egység: százaléka <br/> Összesítési típusát: maximális <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Service Bus Azure a figyelő a metrikák a következő dimenziók támogatja. Dimenziók hozzáadása a metrikákat, nem kötelező megadni. Ha nem adja hozzá a dimenziók, metrikák meg van adva, a névterek szintjén. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|entityName| A Service Bus a névtér az üzenetküldési entitások támogatja.|

## <a name="next-steps"></a>További lépések

Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


