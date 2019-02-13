---
title: Az Azure Service Bus – gyakori kérdések (GYIK) |} A Microsoft Docs
description: Azure Service Bus kapcsolatos gyakori kérdésekre ad választ.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: fce3c2975e4b82583aa09a3862f704f05a363828
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210594"
---
# <a name="service-bus-faq"></a>Service Bus – GYIK

Ez a cikk ismerteti a néhány gyakori kérdés a Microsoft Azure Service Bus szolgáltatásról. Emellett letöltheti a [– gyakori kérdések az Azure támogatja a](https://azure.microsoft.com/support/faq/) általános Azure díjszabását és támogatási információk.

## <a name="general-questions-about-azure-service-bus"></a>Azure Service Bus általános kérdések
### <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?
[Az Azure Service Bus](service-bus-messaging-overview.md) egy aszinkron üzenetkezelési felhőalapú platform, amely lehetővé teszi, hogy adatokat a leválasztott rendszerek között. A Microsoft szolgáltatás, ami azt jelenti, hogy nem kell üzemeltetésére használhatja azt a saját hardver ezt a szolgáltatást kínál.

### <a name="what-is-a-service-bus-namespace"></a>Mi a Service Bus-névtér?
A [névtér](service-bus-create-namespace-portal.md) egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül. Hozzon létre egy névteret kell a Service Bus és az első lépéseket ismertető az első lépéseket.

### <a name="what-is-an-azure-service-bus-queue"></a>Mi az Azure Service Bus-üzenetsor?
A [Service Bus-üzenetsorba](service-bus-queues-topics-subscriptions.md) olyan entitás, amelyben üzenetek találhatók. Üzenetsorok hasznosak, ha több alkalmazást, vagy ha több részből állnak kell kommunikálniuk egymással egy elosztott alkalmazás. A várólista hasonlít egy kulcskiosztó központ, hogy több termékeket (üzenetek) kapott, és elküldi az adott helyről.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Mik az Azure Service Bus-üzenettémák és előfizetések?
A témakör az alábbi ábra egy üzenetsorba, és ha több előfizetést használ, egy gazdagabb üzenetkezelési modellt; válik lényegében egy-a-többhöz típusú eszköz. Ez a közzétételi és előfizetési modell (vagy *pub/sub*) lehetővé teszi az alkalmazások, amelyek több alkalmazás által fogadott üzenet rendelkezik több előfizetést egy üzenettémához üzenetet küld.

### <a name="what-is-a-partitioned-entity"></a>Mit jelent a particionált entitás?
A hagyományos üzenetsor vagy témakör egyetlen üzenettovábbítóról kezeli, és egyetlen üzenetküldési tárolóra tárolja. Csak az alapszintű és Standard szintű üzenetkezelés támogatja a [particionált üzenetsorra vagy témakörbe](service-bus-partitioning.md) kezeli a több üzenetközvetítők és több üzenetküldési tároló tárolja. Ez a szolgáltatás azt jelenti, hogy a teljes átviteli képessége particionált üzenetsor vagy témakör már nem korlátozzák a teljesítmény egyetlen üzenettovábbítóról vagy üzenetküldési tárolóban. Emellett egy átmeneti szolgáltatáskimaradás az üzenetküldési tárolóban nem jelennek meg a particionált üzenetsor vagy témakör nem érhető el.

Rendezés nem biztosított, ha használata particionált entitások. Abban az esetben, ha egy partíció nem érhető el, továbbra is küldhet és a többi partíció-üzeneteket fogadjon.

 A particionált entitások már nem támogatottak a [prémium szintű Termékváltozat](service-bus-premium-messaging.md). 

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="what-are-some-azure-service-bus-best-practices"></a>Mik az Azure Service Bus ajánlott eljárásokat?
Lásd: [ajánlott eljárások a teljesítmény Service Bus használatával] [ Best practices for performance improvements using Service Bus] – Ez a cikk bemutatja, hogyan lehet optimalizálni a teljesítményt üzenetváltásokban.

### <a name="what-should-i-know-before-creating-entities"></a>Mit kell tudni entitások létrehozása előtt?
Egy üzenetsor és a témakör következő tulajdonságai nem módosíthatók. Fontolja meg ezt a korlátozást az entitások üzembe helyezésekor, ezek a Tulajdonságok helyettesítő új entitások létrehozása nélkül nem módosítható.

* Particionálás
* Munkamenetek
* Duplikálás észlelése
* Az expressz entitások

## <a name="pricing"></a>Díjszabás
Ez a szakasz néhány – gyakori kérdések a Service Bus díjszabási struktúrája ad választ.

A [a Service Bus díjszabása és számlázási](service-bus-pricing-billing.md) a cikk ismerteti a Service Bus a számlázási mérőszámok. További részletes információ a Service Bus díjszabási lehetőségek: [a Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/).

Emellett letöltheti a [Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/) általános Azure díjszabási információk. 

### <a name="how-do-you-charge-for-service-bus"></a>Hogyan tegye meg díja szerint számítjuk fel a Service Bus?
Teljes információ a Service Bus díjszabásáról: [díjszabása a Service Bus][Pricing overview]. A feltüntetett árak mellett díjkötelesek kapcsolódó adatforgalmi kimenő kívül az adatközpontot, amelyben az alkalmazás ki van építve.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Milyen a Service Bus használata, az adatátviteli vonatkoznak? Nincs mit jelent?
Egy adott Azure-régión belül minden olyan adatforgalom díjmentes, valamint bejövő adatforgalom biztosítunk. Adatátvitel a régión kívül van alkalmaz a kimenő forgalom költségeit, amely találhatók rajtuk [Itt](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Nem a Service Bus tárhelyért számítunk fel díjat?
Nem, a Service Bus nem tárhelyért számítunk fel díjat. Van azonban egy kvótakorlátozó maximális adatmennyiség / üzenetsor/témakör megőrizhetők. Tekintse meg a következő gyakori kérdések.

## <a name="quotas"></a>Kvóták

A Service Bus-korlátok és kvóták listáját lásd: a [Service Bus-kvóták áttekintése][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Rendelkezik a Service Bus használati kvóták?
Alapértelmezés szerint bármilyen felhőhöz szolgáltatás a Microsoft állítja be az összes olyan ügyfél-előfizetések számított egy összesített havi használati kvóta. Ha több mint ezeket a korlátokat, és igényeinek megfelelően állítsa be ezeket a korlátokat bármikor meg is forduljon az ügyfélszolgálathoz. A Service Bus az összesített használati kvóta havonta 5 milliárd üzeneteket.

A Microsoft fenntartja a jogot arra, hogy túllépte a használati kvóták az adott hónapban felhasználói fiók letiltása, amíg e-mail értesítések kiküldése, és tett kísérletet tett forduljon az ügyfél előtt műveleteknél véve. Ügyfelek kvóta túllépése esetén a kvóták túllépéséből eredő díjakat továbbra is felelős.

Más szolgáltatások az Azure-ban, a Service Bus adott kvótakészlet annak érdekében, hogy nincs-e az erőforrások valós használati érvénybe lépteti. További információt a kvóta annak a [Service Bus-kvóták áttekintése][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hogyan kezeléséhez az üzenetek > 1 MB méretű?
A Service Bus üzenetkezelési szolgáltatások (az üzenetsorok és üzenettémák, előfizetések) teszik lehetővé az alkalmazás méretű üzenetek küldése akár 256 KB-os (standard szintű) vagy 1 MB (prémium szintű). Ha 1 MB-nál nagyobb méretű üzenetek kezelése, használja a jogcím minta ismertetett [ebben a blogbejegyzésben](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem tudok-névtér létrehozása egy másik előfizetésből törlését követően? 
Ha egy névtér töröl egy előfizetést, várjon, amíg 4 óra, mielőtt újra egy másik előfizetésben található ugyanazzal a névvel. Ellenkező esetben a következő hibaüzenet jelenhet: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Mik az Azure Service Bus API-k és a javasolt lépések által előállított kivételeket?
Lehetséges a Service Bus-kivételek listáját lásd: [kivételek áttekintése][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Mi az a közös hozzáférésű Jogosultságkód és nyelveket támogatja az aláírás létrehozásához?
Közös hozzáférésű Jogosultságkódok olyan hitelesítési mechanizmust, SHA-256 biztonságos kivonatok vagy URI-k alapján. További információ a saját aláírásokat létrehozni a Node.js, PHP, Java vagy C\#, tekintse meg a [közös hozzáférési aláírások] [ Shared Access Signatures] cikk.

## <a name="subscription-and-namespace-management"></a>Előfizetés és a névtér-kezelés
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan migrálhatom a névtér egy másik Azure-előfizetésre?

Áthelyezheti a névtér egy Azure-előfizetésből egy másikba, segítségével a [az Azure portal](https://portal.azure.com) vagy a PowerShell-parancsokat. A művelet végrehajtásához a névtér már aktívnak kell lennie. A felhasználó a parancsok végrehajtása a forrás- és a célként megadott előfizetés rendszergazdájának kell lennie.

#### <a name="portal"></a>Portál

Az Azure portal használatával a Service Bus-névterek áttelepítése másik előfizetésre, kövesse az utasításokat [Itt](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

A következő lépéseket a PowerShell-parancsok névtér áthelyezi egy Azure-előfizetésből egy másikba. Ez a művelet végrehajtásához a névtér már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak kell lennie a forrás- és a célként megadott előfizetés rendszergazdája.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>További lépések
A Service Bus kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Introducing Azure Service Bus prémium szintű (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus prémium szintű) (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [A Service Bus – áttekintés](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
