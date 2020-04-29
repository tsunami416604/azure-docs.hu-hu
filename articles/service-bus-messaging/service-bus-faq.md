---
title: Azure Service Bus gyakori kérdések (GYIK) | Microsoft Docs
description: Ez a cikk a Azure Service Bus kapcsolatos gyakori kérdések (GYIK) néhány válaszát tartalmazza.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760249"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus – gyakran ismételt kérdések (GYIK)

Ez a cikk a Microsoft Azure Service Busokkal kapcsolatos gyakori kérdéseket tárgyalja. Az Azure- [támogatási GYIK](https://azure.microsoft.com/support/faq/) általános Azure-díjszabást és támogatási információkat is talál.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Általános kérdések a Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?
A [Azure Service Bus](service-bus-messaging-overview.md) egy aszinkron üzenetkezelő felhőalapú platform, amely lehetővé teszi a leválasztott rendszerek közötti adatküldést. A Microsoft ezt a szolgáltatást szolgáltatásként kínálja, ami azt jelenti, hogy nem kell saját hardvert üzemeltetni a használatához.

### <a name="what-is-a-service-bus-namespace"></a>Mi az Service Bus névtér?
A [névtér](service-bus-create-namespace-portal.md) egy hatókör-tárolót biztosít az alkalmazáson belüli Service Bus erőforrások kezeléséhez. A névtér létrehozása szükséges a Service Bus használatához, és az első lépések egyike.

### <a name="what-is-an-azure-service-bus-queue"></a>Mi az Azure Service Bus üzenetsor?
A [Service Bus üzenetsor](service-bus-queues-topics-subscriptions.md) olyan entitás, amelyben az üzenetek tárolódnak. A várólisták akkor hasznosak, ha több alkalmazással vagy egy elosztott alkalmazás több részével kell kommunikálni egymással. A várólista hasonló ahhoz a terjesztési központhoz, amelyben több termék (üzenet) érkezett, majd az adott helyről lesz küldve.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Mik azok a Azure Service Bus témakörök és előfizetések?
A témakörök üzenetsorként és több előfizetés használata esetén is megjeleníthetők, így gazdagabb üzenetkezelési modellvé válnak. lényegében egy-a-többhöz kommunikációs eszköz. Ez a közzétételi/előfizetési modell (vagy a *pub/sub*) lehetővé teszi egy alkalmazás számára, hogy több előfizetéssel rendelkező témakörhöz üzenetet küldjön, hogy több alkalmazás fogadja az üzenetet.

### <a name="what-is-a-partitioned-entity"></a>Mi az a particionált entitás?
Egy hagyományos üzenetsor vagy témakör kezelése egyetlen Message Broker által történik, és egy üzenetkezelő tárolóban tárolódik. Csak az alapszintű és a standard szintű üzenetkezelési szinten támogatott, a [particionált várólistákat vagy témákat](service-bus-partitioning.md) több üzenet-átvitelszervező kezeli, és több üzenetkezelési tárolóban tárolják. Ez a funkció azt jelenti, hogy egy particionált üzenetsor vagy témakör teljes átviteli sebességét már nem korlátozza egyetlen Message Broker vagy üzenetküldési tároló teljesítménye. Emellett az üzenetküldési tároló átmeneti kimaradása nem jeleníti meg a particionált üzenetsor vagy a témakör nem érhető el.

Particionált entitások használata esetén a rendelés nem biztosítható. Abban az esetben, ha egy partíció nem érhető el, továbbra is küldhet és fogadhat üzeneteket a többi partícióról.

 A particionált entitások már nem támogatottak a [Premium SKU](service-bus-premium-messaging.md)-ban. 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Milyen portokat kell megnyitni a tűzfalon? 
Az üzenetek küldéséhez és fogadásához a következő protokollokat használhatja Azure Service Bus:

- Advanced Message Queueing Protocol (AMQP)
- Service Bus üzenetküldési protokoll (SBMP)
- HTTP

Az alábbi táblázat tartalmazza azokat a kimenő portokat, amelyeket meg kell nyitni a protokollok Azure Event Hubs-vel való kommunikációhoz való használatához. 

| Protocol (Protokoll) | Portok | Részletek | 
| -------- | ----- | ------- | 
| AMQP | 5671 és 5672 | Lásd: [AMQP protokoll – útmutató](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 – 9354 | Lásd: [kapcsolati mód](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Milyen IP-címekre van szükségem az engedélyezési listához?
Az alábbi lépéseket követve megkeresheti a megfelelő IP-címeket a kapcsolatokhoz tartozó fehér listához:

1. Futtassa a következő parancsot egy parancssorból: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Jegyezze fel `Non-authoritative answer`a VISSZAADOTT IP-címet. Ez az IP-cím statikus. Ha a névteret egy másik fürtre állítja vissza, az egyetlen olyan időpontot kell megváltoztatnia, amelyik megváltozhat.

Ha a zóna redundanciát használja a névtérhez, néhány további lépést is végre kell hajtania: 

1. Először futtassa az nslookupt a névtéren.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Jegyezze fel a nevet a **nem mérvadó válasz** szakaszban, amely az alábbi formátumok egyike: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Futtassa az nslookupt mindegyikhez az S1, az S2 és az S3 utótaggal a három rendelkezésre állási zónában futó mindhárom példány IP-címeinek lekéréséhez. 


## <a name="best-practices"></a>Ajánlott eljárások
### <a name="what-are-some-azure-service-bus-best-practices"></a>Milyen Azure Service Bus ajánlott eljárások?
Lásd: [ajánlott eljárások a teljesítmény fejlesztéséhez Service Bus használatával][Best practices for performance improvements using Service Bus] – ez a cikk azt ismerteti, hogyan optimalizálható a teljesítmény az üzenetek cseréjekor.

### <a name="what-should-i-know-before-creating-entities"></a>Mit kell tudni az entitások létrehozása előtt?
A várólista és a témakör következő tulajdonságai nem változtathatók meg. Vegye figyelembe ezt a korlátozást az entitások kiépítésekor, mivel ezek a tulajdonságok nem módosíthatók új helyettesítő entitás létrehozása nélkül.

* Particionálás
* Munkamenetek
* Duplikálás észlelése
* Expressz entitás

## <a name="pricing"></a>Díjszabás
Ez a szakasz a Service Bus árképzési struktúrával kapcsolatos gyakori kérdésekre ad választ.

A [Service Bus díjszabása és a számlázási](https://azure.microsoft.com/pricing/details/service-bus/) cikk a Service Bus számlázási mérőszámait ismerteti. A Service Bus díjszabási lehetőségeivel kapcsolatos további információkért tekintse meg a [Service Bus díjszabási részleteit](https://azure.microsoft.com/pricing/details/service-bus/).

Az Azure- [támogatási GYIK](https://azure.microsoft.com/support/faq/) általános Azure-díjszabási információit is felkeresheti. 

### <a name="how-do-you-charge-for-service-bus"></a>Hogyan díjköteles a Service Bus?
A Service Bus díjszabásával kapcsolatos információkért tekintse meg a [Service Bus díjszabási részleteit][Pricing overview]. A feljegyzett díjak mellett a kimenő adatforgalomért is fizetnie kell azon adatközponton kívül, amelyben az alkalmazás üzembe lett helyezve.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Milyen Service Bus használatára vonatkozik az adatátvitel? Mi nem?
Az adott Azure-régión belüli adatforgalom díjmentesen, valamint a bejövő adatforgalomban is elérhető. A régión kívüli adatforgalomra a kimenő forgalom díja vonatkozik, amely [itt](https://azure.microsoft.com/pricing/details/bandwidth/)található.

### <a name="does-service-bus-charge-for-storage"></a>Service Bus díjat a tárterületért?
Nem, Service Bus a tárterületért nem számítunk fel díjat. Van azonban egy kvóta, amely korlátozza a várólistára vagy témakörre vonatkozó maximálisan megőrzött adatmennyiséget. Tekintse meg a következő gyakori kérdéseket.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Service Bus standard névtérrel rendelkezem. Miért jelenik meg a díjak a (z) $system erőforráscsoport alatt?
Azure Service Bus nemrég frissítettük a számlázási összetevőket. Ennek következtében, ha Service Bus standard szintű névtérrel rendelkezik, akkor a "$system" erőforráscsoport alatt a "/Subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" erőforráshoz tartozó sorok jelennek meg.

Ezek a díjak az Azure-előfizetések által Service Bus standard névteret kiépített alapdíj alapján jelennek meg. 

Fontos megjegyezni, hogy ezek nem új díjak, azaz az előző számlázási modellben is léteztek. Az egyetlen változás, hogy most már a "$system" alatt vannak felsorolva. Ez az új számlázási rendszer contraints miatt történik, amely az előfizetési szint díjait, nem egy adott erőforráshoz, a "$system" erőforrás-azonosító alatt csoportosítja.

## <a name="quotas"></a>Kvóták

Service Bus korlátok és kvóták listáját a [Service Bus kvóták áttekintésében][Quotas overview]találhatja meg.

### <a name="does-service-bus-have-any-usage-quotas"></a>Van Service Bus használati kvótája?
Alapértelmezés szerint bármely felhőalapú szolgáltatás esetében a Microsoft egy összesített havi használati kvótát állít be, amelyet az ügyfél összes előfizetése alapján számítunk fel. Ha ennél több korlátra van szüksége, bármikor felveheti a kapcsolatot az ügyfélszolgálattal, hogy megértse az igényeit, és megfelelően módosítsa ezeket a korlátokat. Service Bus esetében az összesített használati kvóta 5 000 000 000 üzenet havonta.

Míg a Microsoft fenntartja a jogot arra, hogy letiltsa a használati kvótákat egy adott hónapban, a rendszer elküldje az e-mailes értesítéseket, és a művelet végrehajtása előtt több kísérletet Kérjen a felhasználótól. Az ezen kvótákat meghaladó ügyfelek továbbra is felelősek a kvótákat túllépő díjakért.

Az Azure-ban más szolgáltatásokhoz hasonlóan a Service Bus meghatározott kvótákat kényszerít ki annak biztosítására, hogy az erőforrások valós kihasználtsága legyen. Ezekről a kvótákkal kapcsolatban a [Service Bus-kvóták áttekintésében][Quotas overview]talál további információt.

### <a name="how-to-handle-messages-of-size--1-mb"></a>1 MB méretű üzenetek kezelése >
Service Bus Messaging Services (várólisták és témakörök/előfizetések) lehetővé teszik, hogy az alkalmazás legfeljebb 256 KB-os (standard szintű) vagy 1 MB (prémium szint) méretű üzeneteket küldjön. Ha 1 MB-nál nagyobb méretű üzeneteket használ, használja az [ebben a blogbejegyzésben](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)leírt jogcím-ellenőrzési mintát.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem lehet névteret létrehozni egy másik előfizetésből való törlés után? 
Ha töröl egy névteret egy előfizetésből, várjon 4 órát, mielőtt újra létrehozza azt ugyanazzal a névvel egy másik előfizetésben. Ellenkező esetben a következő hibaüzenet jelenhet meg: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Melyek a Azure Service Bus API-k által generált kivételek és a javasolt műveletek?
A lehetséges Service Bus kivételek listáját lásd: [kivételek áttekintése][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Mi a közös hozzáférési aláírás, és milyen nyelveket támogat az aláírás létrehozása?
A közös hozzáférésű aláírások az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmusok. További információ a saját aláírások létrehozásáról a Node. js, a PHP, a Java, a Python és a C# nyelveken: [közös hozzáférésű aláírások][Shared Access Signatures] című cikk.

## <a name="subscription-and-namespace-management"></a>Előfizetés és névtér kezelése
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan áttelepíteni egy névteret egy másik Azure-előfizetésbe?

A névteret áthelyezheti egyik Azure-előfizetésből egy másikba, a [Azure Portal](https://portal.azure.com) vagy a PowerShell-parancsok használatával. A művelet végrehajtásához a névtérnek már aktívnak kell lennie. A parancsokat végrehajtó felhasználónak a forrás-és a cél előfizetések rendszergazdájának kell lennie.

#### <a name="portal"></a>Portál

Ha a Azure Portal segítségével szeretné áttelepíteni Service Bus névtereket egy másik előfizetésbe, kövesse az [alábbi](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)utasításokat. 

#### <a name="powershell"></a>PowerShell

A PowerShell-parancsok következő sora egy névteret helyez át egy másik Azure-előfizetésből. A művelet végrehajtásához a névtérnek már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak a forrás-és a cél előfizetések rendszergazdájának kell lennie.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Service Busről, tekintse meg a következő cikkeket:

* [Azure Service Bus Premium bemutatása (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium (Channel9) bemutatása](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus áttekintése](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
