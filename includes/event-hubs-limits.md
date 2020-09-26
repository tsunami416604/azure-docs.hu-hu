---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 31031462d9904e3554c19f47bc82f87746443693
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371727"
---
Az alábbi táblázatok az [Azure Event Hubsra](https://azure.microsoft.com/services/event-hubs/)vonatkozó kvótákat és korlátokat biztosítanak. További információ a Event Hubs díjszabásáról: [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Általános korlátok az összes szinten
Az alábbi korlátok az összes szinten gyakoriak. 

| Korlát |  Jegyzetek | Érték |
| --- |  --- | --- |
| Event Hubs névterek száma/előfizetés |- |100 |
| Az Event hubok száma névtérben | Az új Event hub létrehozására vonatkozó további kérelmeket a rendszer elutasítja. |10 |
| Partíciók száma az Event hub-ban |- |32 |
| Event hub-név mérete |- | 256 karakter |
| Fogyasztói csoport nevének mérete |- | 256 karakter |
| Nem EPOCH-fogadók száma fogyasztói csoportonként |- |5 |
| Engedélyezési szabályok száma névtérben | Az engedélyezési szabályok létrehozására vonatkozó további kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódushoz tartozó hívások száma |  - | 50 másodpercenként | 
| A virtuális hálózat (VNet) és az IP-konfigurációs szabályok száma | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Alapszintű és standard csomag
Az alábbi táblázat az alapszintű és a standard szintekhez eltérő korlátozásokat mutat be. 

| Korlát | Jegyzetek | Alapszintű | Standard |
| --- |  --- | -- | --- |
| Event Hubs esemény maximális mérete| &nbsp; | 256 KB | 1 MB |
| Fogyasztói csoportok száma az Event hub-ban | &nbsp; |1 |20 |
| AMQP-kapcsolatok száma névtérben | A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívási kód kivételt kap. |100 |5000|
| Esemény-adatok maximális megőrzési ideje | &nbsp; |1 nap |1-7 nap |
| Maximális átviteli egységek |Az átviteli egység korlátja meghaladja az adatok szabályozását, és létrehoz egy [kiszolgáló által foglalt kivételt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Ha nagyobb számú átviteli egységet szeretne igényelni egy standard szintű csomaghoz, a [támogatási kérést](/azure/azure-portal/supportability/how-to-create-azure-support-request). A [további átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) 20 blokkban érhetők el egy véglegesített vásárlás alapján. |20 | 20 | 

### <a name="dedicated-tier"></a>Dedikált szintű
Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

| Funkció | Korlátok |
| --- | ---|
| Sávszélesség |  20 ke |
| Névterek | 50/CU |
| Event Hubs |  1000/névtér |
| Üzenet mérete | 1 MB |
| Partíciók | 2000/CU |
| Fogyasztói csoportok | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 100 a csomagban foglalt |
| Üzenet megőrzési ideje | 90 nap, 10 TB tartalmaz/CU |
| Bejövő események | Tartalmazza |
| Rögzítés | Tartalmazza |


### <a name="schema-registry-limitations"></a>A séma beállításjegyzékének korlátai

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>A **standard** és a **dedikált** szint esetében azonos korlátok 
| Funkció | Korlát | 
| --- |  --- | -- |
| Séma-csoport nevének maximális hossza | 50 |  
| Séma nevének maximális hossza | 100 |    
| Méret bájt/séma szerint | 1 MB |   
| Tulajdonságok száma Schema csoportonként | 1024 |
| Méret bájtban csoportonként a Group tulajdonság kulcsa | 256 | 
| Méret bájtban csoportonként a Group tulajdonság kulcsa | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>A **standard** és a **dedikált** szint esetében eltérő korlátok 

| Korlát | Standard | Dedikált | 
| --- |  --- | -- | --- |
| A séma beállításjegyzékének mérete (névtér) Mega bájtban | 25 |  1024 |
| A séma-beállításjegyzékben (névtérben) lévő séma-csoportok száma| 1 (az alapértelmezett beállítás nélkül) | 1000 |
| Séma-verziók száma az összes sémakezelő csoportban | 25 | 10000 |





