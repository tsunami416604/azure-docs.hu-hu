---
title: Azure Relay metrikák az Azure Monitorban | Microsoft dokumentumok
description: Ez a cikk arról nyújt tájékoztatást, hogyan használhatja az Azure Monitor az Azure Relay állapotának figyeléséhez.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273116"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Az Azure Relay metrikák az Azure Monitorban 
Az Azure Relay metrikák az Azure-előfizetésben lévő erőforrások állapotát biztosítják. A metrikák adatainak gazdag készletével felmérheti a továbbítási erőforrások általános állapotát, nem csak a névtér szintjén, hanem az entitás szintjén is. Ezek a statisztikák fontosak lehetnek, mivel segítenek az Azure Relay állapotának figyelésében. Metrikák is segít elhárítani a kiváltó problémákat anélkül, hogy kapcsolatba lépni az Azure-támogatás.

Az Azure Monitor egységes felhasználói felületeket biztosít a különböző Azure-szolgáltatások figyeléséhez. További információ: [Figyelés a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md) és az [Azure Monitor metrikák lekérése a .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) minta a GitHubon.

> [!IMPORTANT]
> Ez a cikk csak az Azure Relay hibrid kapcsolatok szolgáltatására vonatkozik, a WCF-továbbítóra nem. 

## <a name="access-metrics"></a>Hozzáférési mutatók

Az Azure Monitor többféle módon érheti el a metrikákat. Metrikák at érhet el az [Azure Portalon](https://portal.azure.com)keresztül, vagy használhatja az Azure Monitor API-k (REST és .NET) és elemzési megoldások, például az Operation Management Suite és az Event Hubs. További információ: [Monitoring data collected by Azure Monitor](../azure-monitor/platform/data-platform.md).

A mérőszámok alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 napnyi adathoz férhet hozzá. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez az Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférési mutatók a portálon

Az [Azure Portalon](https://portal.azure.com)idővel figyelheti a metrikákat. A következő példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![][1]

A metrikákat közvetlenül is elérheti a névtéren keresztül. Ehhez válassza ki a névteret, majd kattintson a **Metrics **. 

A dimenziókat támogató mutatók esetében a kívánt dimenzióértékkel kell szűrnie.

## <a name="billing"></a>Számlázás

Metrikák használata az Azure Monitorban jelenleg ingyenes, míg előzetes verzióban. Azonban ha további megoldásokat használ, amelyek metrikaadatokat használnak, előfordulhat, hogy ezek a megoldások számlázásra kerül. Például az Azure Storage által számlázott, ha archiválja metrikák adatok egy Azure Storage-fiók. Az Azure Monitor-naplók is számláznak, ha metrikák adatait továbbítja az Azure Monitor naplóiba a speciális elemzéshez.

Az alábbi mutatók áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Több mutatót is elavulttal ítunk, mivel azok más néven kerülnek áthelyezésre. Ehhez szükség lehet a hivatkozások frissítésére. Az "elavult" kulcsszóval jelölt mérőszámok nem támogatottak a jövőben.

Az összes metrikaértékek percenként elküldésre kerülnek az Azure Monitornak. Az idő részletessége határozza meg azt az időintervallumot, amelyhez a metrikák értékei megjelennek. A támogatott időintervallum az összes Azure Relay metrikák 1 perc.

## <a name="connection-metrics"></a>Csatlakozási mutatók

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| ListenerConnections-sikeres  | Az Azure Relay-hez egy adott időszakban sikeresen létesített figyelőkapcsolatok száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Figyelőkapcsolatok-Ügyfélhiba |A figyelőkapcsolatokon egy adott időszakban előforduló ügyfélhibák száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Figyelőkapcsolatok-ServerHiba |A figyelőkapcsolatokon egy adott időszakban előforduló kiszolgálóhibák száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|SenderConnections-Sikeres |A megadott időszakban sikeres feladói kapcsolatok száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|SenderConnections-ClientError (Küldőkapcsolatok-ügyfélhiba) |A küldőkapcsolatokon egy adott időszakban előforduló ügyfélhibák száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|SenderConnections-ServerError |A kiszolgálóhibák száma a küldői kapcsolatokon egy adott időszakban.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|ListenerConnections-TotalRequests |A figyelőkapcsolatok teljes száma egy adott időszakban.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|SenderConnections-TotalRequests |A feladók által egy adott időszakban benyújtott csatlakozási kérelmek.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|ActiveConnections |Az aktív kapcsolatok száma. Ez az érték egy időponthoz(pont) érték.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|ActiveListeners |Az aktív hallgatók száma. Ez az érték egy időponthoz(pont) érték.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Figyelőleválasztás |A leválasztott figyelők száma egy adott időszakban.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Feladóleválasztás |A leválasztott feladók száma egy adott időszakban.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="memory-usage-metrics"></a>Memóriahasználati mutatók

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Átvitt bájtok |Egy adott időszakban átvitt bájtok száma.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="metrics-dimensions"></a>Metrikadimenziók

Az Azure Relay a következő dimenziókat támogatja az Azure Monitor metrikák. Dimenziók hozzáadása a metrikákhoz nem kötelező. Ha nem ad hozzá dimenziókat, a mérőszámok a névtér szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|Entitásnév| Az Azure Relay támogatja az üzenetküldési entitásokat a névtér ben.|

## <a name="next-steps"></a>További lépések

Tekintse meg az [Azure Monitoring áttekintését.](../monitoring-and-diagnostics/monitoring-overview.md)

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




