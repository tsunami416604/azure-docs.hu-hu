---
title: Azure Relay – gyakori kérdések | Microsoft dokumentumok
description: Ez a cikk az Azure Relay szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: d5032b427316a3c4e07013af4e8214e239a6efb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514000"
---
# <a name="azure-relay-faqs"></a>Azure Relay – gyakori kérdések

Ez a cikk választ ad az [Azure Relayvel](https://azure.microsoft.com/services/service-bus/)kapcsolatos gyakori kérdésekre . Az Azure általános díjszabási és támogatási információiért tekintse meg az [Azure-támogatási gyIK-et.](https://azure.microsoft.com/support/faq/)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-relay"></a>Mi az az Azure Relay?
Az [Azure Relay szolgáltatás](relay-what-is-it.md) megkönnyíti a hibrid alkalmazások azáltal, hogy biztonságosabban tegye elérhetővé a vállalati vállalati hálózaton belül található szolgáltatásokat a nyilvános felhőben. A szolgáltatásokat tűzfalkapcsolat megnyitása és a vállalati hálózati infrastruktúra beavatkozása nélkül is elérhetővé teheti.

### <a name="what-is-a-relay-namespace"></a>Mi az a Relé névtér?
A [névtér](relay-create-namespace-portal.md) egy hatókör-tároló, amely az alkalmazáson belüli továbbítási erőforrások címzéséhez használható. A Továbbítás használatához létre kell hoznia egy névteret. Ez az első lépések egyike a kezdéshez.

### <a name="what-happened-to-service-bus-relay-service"></a>Mi történt a Service Bus Relay szolgáltatással?
A korábban elnevezett Service Bus Relay szolgáltatás neve [MOSTANTÓL WCF-továbbító.](service-bus-relay-tutorial.md) Ezt a szolgáltatást a szokásos módon használhatja. A hibrid kapcsolatok szolgáltatás egy azure BizTalk-szolgáltatásokból átültetett szolgáltatás frissített verziója. A WCF-továbbító és a hibrid kapcsolatok továbbra is támogatottak.

## <a name="pricing"></a>Díjszabás
Ez a szakasz választ ad néhány gyakori kérdést a továbbító árképzési struktúrával kapcsolatban. Az [Azure-támogatás gyakori kérdések](https://azure.microsoft.com/support/faq/) et is megtekintheti az azure-díjszabással kapcsolatos általános információkról. A továbbítási díjszabásról a [Service Bus díjszabási részletei című témakörben talál teljes körű tájékoztatást.][Pricing overview]

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hogyan kell fizetni a hibrid kapcsolatok ért és a WCF-továbbítóért?
A továbbítási díjszabásról a [Hibrid kapcsolatok és a WCF-rellézek][Pricing overview] táblázatban a Service Bus díjszabási részletek lapján talál. Az ezen az oldalon feltüntetett árakon kívül a kapcsolódó adatátvitelért is díjat számítunk fel azon adatközponton kívüli kimenő forgalomért, amelyben az alkalmazás ki van építve.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hogyan kell kiszámláznom a hibrid kapcsolatokat?
Íme három példa számlázási forgatókönyvek hibrid kapcsolatok:

*   1. forgatókönyv:
    *   Egyetlen figyelővel rendelkezik, például a hibrid kapcsolatok kezelőjének egy példánya telepítve van, és folyamatosan fut az egész hónapban.
    *   A hónap során 3 GB adatot küld a kapcsolaton keresztül. 
    *   A teljes díj 5 dollár.
*   2. forgatókönyv:
    *   Egyetlen figyelővel rendelkezik, például a hibrid kapcsolatok kezelőjének egy példánya telepítve van, és folyamatosan fut az egész hónapban.
    *   A hónap során 10 GB adatot küld a kapcsolaton keresztül.
    *   A teljes díj 7,50 dollár. Ez $5 a kapcsolatés az első 5 GB + $ 2,50 a további 5 GB-os adatok.
*   3. forgatókönyv:
    *   A hibrid kapcsolatok kezelőjének két példánya van telepítve, és folyamatosan fut az egész hónapban.
    *   A hónap során 3 GB adatot küld az A kapcsolaton keresztül.
    *   A hónap során 6 GB adatot küld a B kapcsolaton keresztül.
    *   A teljes díj 10,50 dollár. Ez $5 az A kapcsolatért + $5 a B + $0.50 kapcsolathoz (a hatodik gigabájthoz a B kapcsolaton).

Vegye figyelembe, hogy a példákban használt árak csak a hibrid kapcsolatok előzetes időszakban alkalmazhatók. Az árak a hibrid kapcsolatok általános elérhetőségét követően változhatnak.

### <a name="how-are-hours-calculated-for-relay"></a>Hogyan számítják ki az órákat a Relé esetében?

A WCF-továbbító csak a standard szintű névterekben érhető el. A relék árazási és [kapcsolati kvótái](../service-bus-messaging/service-bus-quotas.md) egyébként nem változtak. Ez azt jelenti, hogy a relék továbbra is az üzenetek (nem a műveletek) száma és a továbbítási órák alapján töltődnek fel. További információt a ["Hibrid kapcsolatok és WCF-relléék"](https://azure.microsoft.com/pricing/details/service-bus/) táblázatban talál a díjszabás részletei lapon.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Mi a teendő, ha egynél több figyelő csatlakozik egy adott reléhöz?
Bizonyos esetekben előfordulhat, hogy egy továbbító több csatlakoztatott figyelők. A relé akkor tekinthető nyitottnak, ha legalább egy továbbítófigyelő csatlakozik hozzá. Figyelők hozzáadása egy nyitott továbbító hoz további továbbítási órákban. A továbbítóhoz kapcsolódó továbbító-feladók (a továbbítóknak üzenetet meghívó vagy továbbító kliensek) száma nincs hatással a továbbítási órák kiszámítására.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hogyan számítja ki az üzenetek mérője a WCF-relékhez?
(**Ez csak a WCF relékre vonatkozik. Az üzenetek nem jelentenek költséget a hibrid kapcsolatok esetében.)**

A továbbítók számlázható üzeneteit általában a korábban ismertetett felügyelt entitások (várólisták, témakörök és előfizetések) esetében használt módszerrel számítják ki. Van azonban néhány figyelemre méltó különbség.

Üzenet küldése a Service Bus-továbbító kezeli a "teljes átmenő" küldanek a továbbító figyelő, amely fogadja az üzenetet. A rendszer nem kezeli a Service Bus-továbbító küldési műveletként, majd a továbbítófigyelőnek történő kézbesítést. A reléfigyelővel szembeni kérés-válasz stílusú szolgáltatás meghívása (legfeljebb 64 KB) két számlázható üzenetet eredményez: egy számlázható üzenetet a kérelemhez és egy számlázható üzenetet a válaszhoz (feltéve, hogy a válasz is 64 KB vagy kisebb). Ez nem más, mint egy ügyfél és egy szolgáltatás közötti közvetítői várólista használata. Ha egy ügyfél és egy szolgáltatás közötti közvetítői várólistát használ, ugyanaz a kérés-válasz minta esetén kérésküldése szükséges a várólistába, amelyet a várólistából a szolgáltatásba történő dequeue/delivery követ. Ezt követi egy válasz küldése egy másik várólistába, és egy dequeue/delivery arról a várólistáról az ügyfélnek. Az azonos méretű feltételezések használata az egész (legfeljebb 64 KB) a közvetített várólista-minta 4 számlázható üzenetet eredményez. Kétszer annyi üzenetet kell fizetnie, hogy ugyanazt a mintát valósítsa meg, mint a relé használatával. Természetesen vannak előnyei a várólisták használatával ennek a mintának, például a tartósság és a terhelés kiegyenlítése. Ezek az előnyök indokolhatják a többletköltséget.

A **netTCPRelay** WCF kötéssel megnyitott relék az üzeneteket nem egyedi üzenetekként, hanem a rendszeren keresztül áramló adatfolyamként kezelik. Ha ezt a kötést használja, csak a küldő és a figyelő láthatja az elküldött és fogadott üzenetek keretezését. A **netTCPRelay** kötést használó relék esetében az összes adat adatfolyamként lesz kezelve a számlázható üzenetek kiszámításához. Ebben az esetben a Service Bus kiszámítja az egyes továbbítókon keresztül küldött vagy fogadott adatok teljes mennyiségét 5 perces alapon. Ezután elosztja a teljes adatmennyiséget 64 KB-mal, hogy meghatározza az adott továbbítás számlázható üzeneteinek számát az adott időszakban.

## <a name="quotas"></a>Kvóták
| Kvóta neve | Hatókör |  Megjegyzések | Érték |
| --- | --- | --- | --- |
| Egyidejű hallgatók egy relé |Entitás |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. |25 |
| Egyidejű továbbítási kapcsolatok a szolgáltatás névterében lévő összes továbbítási végponton ként |Névtér |- |5000 |
| Végpontok továbbítása szolgáltatásnévtérenként |Névtér |- |10,000 |
| A [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) és a [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) kötési relék üzenetmérete |Névtér |A rendszer elutasítja az e kvótákat meghaladó bejövő üzeneteket, és a hívókód kivételt fogad. |64 KB |
| A [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) és [a NetTcpRelayBinding kötési](/dotnet/api/microsoft.servicebus.nettcprelaybinding) relék üzenetmérete |Névtér |Nincs korlátozva az üzenetek méretére vonatkozóan. |Korlátlan |

### <a name="does-relay-have-any-usage-quotas"></a>Rendelkezik a Relay használati kvótával?
Alapértelmezés szerint minden felhőszolgáltatás hoz létre egy összesített havi használati kvótát, amely az ügyfél összes előfizetésére kiszámítva. Megértjük, hogy időnként az Ön igényei túllépik ezeket a korlátokat. Bármikor felveheti a kapcsolatot az ügyfélszolgálattal, hogy megérthessük az Ön igényeit, és megfelelően módosíthassuk ezeket a korlátokat. A Service Bus esetében az összesített használati kvóták a következők:

* 5 milliárd üzenet
* 2 millió reléóra

Bár fenntartjuk a jogot, hogy letiltsunk egy olyan fiókot, amely meghaladja a havi használati kvótákat, e-mail értesítést küldünk, és többször is megpróbáljuk felvenni a kapcsolatot az ügyféllel, mielőtt bármilyen intézkedést tennénk. Azok az ügyfelek, akik túllépik ezeket a kvótákat, továbbra is felelősek a többletköltségekért.

### <a name="naming-restrictions"></a>Elnevezési korlátozások
A továbbító névtér nevének 6 és 50 karakter közötti hosszúságúnak kell lennie.

## <a name="subscription-and-namespace-management"></a>Előfizetés- és névtérkezelés
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan telepíthetek át egy névteret egy másik Azure-előfizetésbe?

Ha egy névteret egyik Azure-előfizetésből egy másik előfizetésbe szeretne áthelyezni, használhatja az [Azure Portalt,](https://portal.azure.com) vagy használhatja a PowerShell-parancsokat. Ha egy névteret át szeretne helyezni egy másik előfizetésbe, a névtérnek már aktívnak kell lennie. A parancsokat futtató felhasználónak rendszergazdai felhasználónak kell lennie mind a forrás-, mind a cél-előfizetéseken.

#### <a name="azure-portal"></a>Azure portál

Ha az Azure Portal használatával szeretné áttelepíteni az Azure Relay névtereket az egyik előfizetésből egy másik előfizetésbe, olvassa el az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe című témakört.](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal) 

#### <a name="powershell"></a>PowerShell

Ha a PowerShell használatával áthelyezhet egy névteret az egyik Azure-előfizetésből egy másik előfizetésbe, használja a következő parancssorozatot. A művelet végrehajtásához a névtérnek már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak rendszergazdai felhasználónak kell lennie mind a forrás-, mind a cél-előfizetéseken.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Melyek az Azure Relay API-k által létrehozott kivételek és a javasolt műveletek, amelyeket elkészíthet?
A gyakori kivételek és a javasolt műveletek leírását a [Kivételek továbbítása (Relé) (Továbbítás) (Továbbítás)][Relay exceptions]témakörben tetszhet.

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Mi az a megosztott hozzáférési aláírás, és milyen nyelvekkel hozhatok létre aláírást?
A megosztott hozzáférésű aláírások (SAS) az SHA-256 biztonságos kivonatain vagy URI-in alapuló hitelesítési mechanizmus. A Node.js, PHP, Python, Java, C és C#nyelven történő saját aláírások létrehozásáról a [Service Bus-hitelesítés megosztott hozzáférésű aláírásokkal][Shared Access Signatures]című témakörben talál további információt.

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Lehetséges a relévégpontok engedélyezési listája?
Igen. A továbbító ügyfél teljes körűen minősített tartománynevek használatával kapcsolatot létesít az Azure Relay szolgáltatással. Az ügyfelek hozzáadhatnak `*.servicebus.windows.net` egy bejegyzést a DNS-engedélyezési listát támogató tűzfalakhoz.

## <a name="next-steps"></a>További lépések
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
