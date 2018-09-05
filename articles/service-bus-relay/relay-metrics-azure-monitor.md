---
title: Az Azure Relay-metrikák az Azure Monitor (előzetes verzió) |} A Microsoft Docs
description: Azure Relay monitorozása az Azure Monitoring segítségével
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 6d9c154e4942e39d68c30ac52dee0f743e428b9e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696887"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Az Azure Relay-metrikák az Azure Monitor (előzetes verzió)

Az Azure Relay-metrikák biztosít az Azure-előfizetésében erőforrások állapotát. A metrikai adatok széles skáláját felmérheti a Relay-erőforrás, nem csak a névterek szintjén, hanem az entitások szintjén általános állapotát. A statisztikai lehet fontos, mert ezek segítségével figyelheti az Azure Relay állapotát. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. Mindkét hozzáférési metrikák keresztül is a [az Azure portal](https://portal.azure.com), vagy használja az Azure Monitor API-k (REST és .NET) és elemzési megoldásokkal, például az Operation Management Suite és az Event Hubs. További információkért lásd: [Azure Monitor-metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) az Azure monitorban.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, és kattintson a **metrikák (Peview)**. 

Dimenziók támogató metrikákhoz a kívánt dimenzió értékkel kell szűrni.

## <a name="billing"></a>Számlázás

Az Azure monitorban mérőszámok segítségével a jelenleg előzetes verzióban ingyenes. Azonban használ további megoldásokat, amelyek fogadni a mérőszámadatokat, akkor előfordulhat, hogy után kell díjat ezek a megoldások által. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. Emellett a számlázás a Log Analytics által speciális elemzésekre szolgáló Log Analytics-metrikák adatok streamelése az, ha.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. Az összes Azure Relay-metrikák támogatott idő időköz 1 perc.

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Sikert jelző Figyelőkapcsolatokra (előzetes verzió) | Az Azure Relay egy megadott időszakban végrehajtott sikeres figyelő kapcsolatok száma. <br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Figyelőkapcsolatokra-Ügyfélhibái (előzetes verzió)|Figyelő kapcsolatok használata esetén egy meghatározott időtartamon belül az ügyfél-hibák száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Figyelőkapcsolatokra-Kiszolgálóhibái (előzetes verzió)|A figyelő kapcsolatok egy meghatározott időtartamon belül a kiszolgáló hibák száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Sikert jelző feladói kapcsolatokra (előzetes verzió)|Egy megadott időszakban végrehajtott sikeres küldő kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra-Ügyfélhibái (előzetes verzió)|Küldő kapcsolatok használata esetén egy meghatározott időtartamon belül az ügyfél-hibák száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra-Kiszolgálóhibái (előzetes verzió)|Küldő kapcsolatok használata esetén egy meghatározott időtartamon belül kiszolgálóhibáinak száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Figyelőkapcsolatokra-TotalRequests (előzetes verzió)|Egy megadott időszakban figyelő kapcsolatok teljes száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra-TotalRequests (előzetes verzió)|A kapcsolati kérelmek egy meghatározott időtartamon belül a feladó által végzett.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Aktív kapcsolatai (előzetes verzió)|Egy adott időszakban aktív kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Aktív figyelői (előzetes verzió)|Egy adott időszakban aktív figyelők száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|ListenerDisconnects (előzetes verzió)|Egy meghatározott időtartamon belül a leválasztott figyelők száma.<br/><br/> Egység: bájt <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|SenderDisconnects (előzetes verzió)|Egy meghatározott időtartamon belül a leválasztott feladók száma.<br/><br/> Egység: bájt <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|

## <a name="memory-usage-metrics"></a>Memória használati metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Által átvitt bájtok (előzetes verzió)|Bájtok száma az adott időszakon belül át.<br/><br/> Egység: bájt <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Relay metrikák az Azure monitorban támogatja a következő dimenziókat. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| Az Azure Relay támogatja az üzenetküldési entitások a névtérben.|

## <a name="next-steps"></a>További lépések

Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




