---
title: Az Azure Service Bus gyakran feltett kérdések (GYIK) | Microsoft dokumentumok
description: Ez a cikk választ ad az Azure Service Bus-szal kapcsolatos gyakori kérdésekre.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760249"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus – gyakori kérdések (GYAKORI KÉRDÉSEK)

Ez a cikk a Microsoft Azure Service Bus szolgáltatással kapcsolatos gyakori kérdéseket ismerteti. Az [Azure-támogatási gyik-eket](https://azure.microsoft.com/support/faq/) az Azure általános díjszabási és támogatási információkért is felteheti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Általános kérdések az Azure Service Bus szolgáltatással kapcsolatban
### <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?
[Az Azure Service Bus](service-bus-messaging-overview.md) egy aszinkron üzenetküldő felhőplatform, amely lehetővé teszi az adatok küldését a függetlenített rendszerek között. A Microsoft ezt a szolgáltatást szolgáltatásként kínálja, ami azt jelenti, hogy a használatához nem kell saját hardvert üzemeltetnie.

### <a name="what-is-a-service-bus-namespace"></a>Mi az a Service Bus-névtér?
A [névtér](service-bus-create-namespace-portal.md) egy hatókör-tárolót biztosít a Service Bus-erőforrások címzéséhez az alkalmazáson belül. A névtér létrehozása a Service Bus használatához szükséges, és az első lépések egyike az első lépéseknek.

### <a name="what-is-an-azure-service-bus-queue"></a>Mi az Azure Service Bus-várólista?
A [Service Bus-várólista](service-bus-queues-topics-subscriptions.md) olyan entitás, amelyben az üzenetek tárolódnak. A várólisták akkor hasznosak, ha több alkalmazással rendelkezik, vagy egy elosztott alkalmazás több részével, amelyeknek kommunikálniuk kell egymással. A várólista hasonló a terjesztési központhoz, mivel több terméket (üzenetet) fogad, majd arról a helyről küld.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Mik azok az Azure Service Bus-témakörök és előfizetések?
A témakör várólistáként jeleníthető meg, és ha több előfizetést használ, gazdagabb üzenetkezelési modellé válik; lényegében egy-a-többhöz kommunikációs eszköz. Ez a közzétételi/előfizetési modell (vagy *pub/sub)* lehetővé teszi, hogy egy olyan alkalmazás, amely üzenetet küld egy több előfizetéssel rendelkező témakörnek, több alkalmazás is megkapja az üzenetet.

### <a name="what-is-a-partitioned-entity"></a>Mi az a particionált entitás?
A hagyományos várólistát vagy témakört egyetlen üzenetközvetítő kezeli, és egy üzenettárolóban tárolja. Csak az alapszintű és a standard üzenetkezelési szinteken támogatott, [a particionált várólisták vagy témakör](service-bus-partitioning.md) kezeli több üzenet brókerek és több üzenettárolókban tárolt. Ez a funkció azt jelenti, hogy a particionált várólista vagy témakör teljes átviteli teljesítményét már nem korlátozza egyetlen üzenetközvetítő vagy üzenettároló teljesítménye. Ezenkívül az üzenettároló ideiglenes kimaradása nem teszi elérhetetlenné a particionált várólistát vagy témakört.

Particionált entitások használata esetén a rendezés nem biztosított. Abban az esetben, ha egy partíció nem érhető el, továbbra is küldhet és fogadhat üzeneteket a többi partícióról.

 A particionált entitások már nem támogatottak a [prémium szintű termékváltozatban.](service-bus-premium-messaging.md) 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Milyen portokat kell megnyitni a tűzfalon? 
Az alábbi protokollok segítségével küldhet és fogadhat üzeneteket az Azure Service Bus segítségével:

- Advanced Message Queueing Protocol (AMQP)
- Service Bus Messaging Protocol (SBMP)
- HTTP

Tekintse meg az alábbi táblázatot a kimenő portok meg kell nyitnia, hogy használja ezeket a protokollokat az Azure Event Hubs kommunikálni. 

| Protocol (Protokoll) | Portok | Részletek | 
| -------- | ----- | ------- | 
| AMQP | 5671 és 5672 | Lásd: [AMQP protokollútmutató](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 hogy 9354 | Lásd: [Csatlakoztatási mód](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Milyen IP-címekre van szükségem az engedélyezési listához?
A kapcsolatokhoz megfelelő IP-címek és fehér listák megkereséséhez kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot a parancssorból: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Jegyezze fel a `Non-authoritative answer`visszaadott IP-címet a ban. Ez az IP-cím statikus. Az egyetlen időpont, amikor megváltozna, ha visszaállítja a névteret egy másik fürtre.

Ha a zónaredundanciát használja a névtérhez, néhány további lépést kell tennie: 

1. Először futtassa az nslookup-ot a névtéren.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Jegyezze fel a nevet a **nem mérvadó válasz** szakaszban, amely az alábbi formátumok egyikében található: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Futtassa az nslookup-ot az s1, s2 és s3 utótagokkal rendelkező mindegyikhez, hogy lekérhesd mindhárom példány IP-címét, amely három rendelkezésre állási zónában fut, 


## <a name="best-practices"></a>Ajánlott eljárások
### <a name="what-are-some-azure-service-bus-best-practices"></a>Melyek az Azure Service Bus ajánlott eljárásai?
A [Service Bus használatával a teljesítmény javulásának gyakorlati ismertetése][Best practices for performance improvements using Service Bus] című témakörben olvashat, amely ismerteti, hogyan optimalizálhatja a teljesítményt az üzenetek cseréjekor.

### <a name="what-should-i-know-before-creating-entities"></a>Mit kell tudnom az entitások létrehozása előtt?
A várólista és a témakör alábbi tulajdonságai nem módosíthatók. Vegye figyelembe ezt a korlátozást, amikor az entitásokat kiépíti, mivel ezek a tulajdonságok nem módosíthatók új helyettesítő entitás létrehozása nélkül.

* Particionálás
* Munkamenetek
* Duplikálás észlelése
* Expressz entitás

## <a name="pricing"></a>Díjszabás
Ez a szakasz választ ad néhány gyakori kérdést a Service Bus díjszabási struktúrával kapcsolatban.

A [Service Bus díjszabási és számlázási](https://azure.microsoft.com/pricing/details/service-bus/) cikk ismerteti a számlázási mérőszámok a Service Bus. A Service Bus díjszabási beállításaival kapcsolatos további információkért lásd: [Service Bus díjszabási részletek.](https://azure.microsoft.com/pricing/details/service-bus/)

Az [Azure-támogatási gyik-eket](https://azure.microsoft.com/support/faq/) az Azure általános díjszabási információkért is felkeresheti. 

### <a name="how-do-you-charge-for-service-bus"></a>Hogyan kell fizetni a Service Bus?
A Service Bus díjszabásáról a [Service Bus díjszabási részletei című témakörben talál teljes körű tájékoztatást.][Pricing overview] A feljegyzett árakon kívül a kapcsolódó adatátvitelért is díjat számítunk fel azon adatközponton kívüli kimenő forgalomért, amelyben az alkalmazás ki van építve.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>A Service Bus milyen használata függ adatátvitelhez? Mi nem az?
Egy adott Azure-régión belüli adatátvitel díjmentesen biztosított, valamint a bejövő adatátvitel. A régión kívüli adatátvitelre kikell fizetni a kijáratot, amely [itt](https://azure.microsoft.com/pricing/details/bandwidth/)található.

### <a name="does-service-bus-charge-for-storage"></a>A Service Bus díjat számít fel a tárolásért?
Nem, a Service Bus nem számít fel díjat a tárolásért. Van azonban egy kvóta korlátozza a várólistánként/témakörenként megőrzött adatok maximális mennyiségét. Lásd a következő GYIK-et.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Szolgáltatásbusz standard névtérrel rendelkezem. Miért jelennek meg a $system erőforráscsoport díjai?
Az Azure Service Bus nemrég frissítette a számlázási összetevőket. Emiatt, ha rendelkezik egy Service Bus Standard névtérrel, az "/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" erőforrássorok at az "$system" erőforráscsoportban láthatja.

Ezek a díjak a Service Bus Standard névtér kiépített Azure-előfizetésenkénti alapdíjat jelölik. 

Fontos megjegyezni, hogy ezek nem új díjak, azaz az előző számlázási modellben is léteztek. Az egyetlen változás az, hogy most már szerepel a "$system". Ez az új számlázási rendszer kontraints, amely csoportosítja előfizetési szintű díjak, nem kötődik egy adott erőforrás, a "$system" erőforrás-azonosító.

## <a name="quotas"></a>Kvóták

A Service Bus-korlátok és kvóták listáját a [Service Bus-kvóták áttekintése című témakörben találja.][Quotas overview]

### <a name="does-service-bus-have-any-usage-quotas"></a>A Service Bus rendelkezik használati kvótákkal?
Alapértelmezés szerint minden felhőszolgáltatás hoz létre egy összesített havi használati kvótát, amely az ügyfél összes előfizetése között számítja ki. Ha ezeknél a korlátoknál többre van szüksége, bármikor felveheti a kapcsolatot az ügyfélszolgálattal, hogy megértse az Ön igényeit, és megfelelően módosítsa ezeket a korlátokat. A Service Bus esetében az összesített használati kvóta havi 5 milliárd üzenet.

Bár a Microsoft fenntartja a jogot, hogy letiltson egy olyan ügyfélfiókot, amely egy adott hónapban túllépte a használati kvótákat, e-mail értesítéseket küld a rendszer, és több kísérletet tesz az ügyféllel való kapcsolatfelvételre, mielőtt bármilyen műveletet végrehajtana. A kvótákat meghaladó ügyfelek továbbra is felelősek a kvótákat meghaladó díjakért.

Az Azure más szolgáltatásaihoz is a Service Bus bizonyos kvótákat kényszerít ki az erőforrások méltányos használata érdekében. Ezekről a kvótákról további részleteket a [Service Bus-kvóták áttekintése című témakörben talál.][Quotas overview]

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hogyan kezeljük az 1 MB méretű üzeneteket >?
A Service Bus üzenetküldő szolgáltatásai (várólisták és témakörök/előfizetések) lehetővé teszik, hogy az alkalmazás legfeljebb 256 KB (standard szintű) vagy 1 MB (prémium szintű) méretű üzeneteket küldjön. Ha 1 MB-nál nagyobb méretű üzenetekkel foglalkozik, használja a [blogbejegyzésben](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)leírt jogcím-ellenőrzési mintát.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem tudok névteret létrehozni, miután egy másik előfizetésből letettem? 
Ha töröl egy névteret egy előfizetésből, várjon 4 órát, mielőtt újra létrehozza azt ugyanazzal a névvel egy másik előfizetésben. Ellenkező esetben a következő hibaüzenet `Namespace already exists`jelenhet meg: . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Melyek az Azure Service Bus API-k és a javasolt műveletek által létrehozott kivételek?
A Service Bus alóli lehetséges kivételek listáját a [Kivételek áttekintése című témakörben][Exceptions overview]találja.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Mi az a megosztott hozzáférésű aláírás, és mely nyelvek támogatják az aláírás generálását?
A megosztott hozzáférésű aláírások az SHA-256 biztonságos kivonatain vagy URI-in alapuló hitelesítési mechanizmus. A Node.js, PHP, Java, Python és C# fájlban történő saját aláírások létrehozásáról a [Megosztott hozzáférésű aláírások][Shared Access Signatures] ról szóló cikkben olvashat.

## <a name="subscription-and-namespace-management"></a>Előfizetés- és névtérkezelés
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan telepíthetek át egy névteret egy másik Azure-előfizetésbe?

Az [Azure Portal](https://portal.azure.com) vagy a PowerShell-parancsok használatával áthelyezheti a névteret az egyik Azure-előfizetésből a másikba. A művelet végrehajtásához a névtérnek már aktívnak kell lennie. A parancsokat végrehajtó felhasználónak rendszergazdai jogosultságnak kell lennie mind a forrás-, mind a cél-előfizetéseken.

#### <a name="portal"></a>Portál

Ha az Azure Portalon szeretné áttelepíteni a Service Bus-névtereket egy másik előfizetésbe, kövesse az [itt](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)található utasításokat. 

#### <a name="powershell"></a>PowerShell

A PowerShell-parancsok következő sorozata áthelyez egy névteret az egyik Azure-előfizetésből a másikba. A művelet végrehajtásához a névtérnek már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak a forrás- és a cél-előfizetések rendszergazdájának kell lennie.

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
Ha többet szeretne megtudni a Service Busszolgáltatásról, olvassa el az alábbi cikkeket:

* [Az Azure Service Bus Premium bemutatása (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Az Azure Service Bus Premium bemutatása (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus – áttekintés](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
