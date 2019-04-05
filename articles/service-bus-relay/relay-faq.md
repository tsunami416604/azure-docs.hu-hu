---
title: Az Azure Relay – gyakori kérdések |} A Microsoft Docs
description: Válaszok néhány Azure Relay – gyakori kérdések.
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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 2433f4b3563cc8b301d1815cccf5ab24406e8662
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045577"
---
# <a name="azure-relay-faqs"></a>Az Azure Relay – gyakori kérdések

Ebben a cikkben megválaszolunk néhány gyakori kérdések (GYIK) kapcsolatos [Azure Relay](https://azure.microsoft.com/services/service-bus/). Általános Azure díjszabását és támogatási információk: a [– gyakori kérdések az Azure támogatja a](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-relay"></a>Mi az az Azure Relay?
A [Azure Relay szolgáltatás](relay-what-is-it.md) megkönnyíti a hibrid alkalmazásait elérhetővé biztonságosan a vállalati hálózaton a nyilvános felhőben található szolgáltatások segít. A szolgáltatások tehetők közzé egy tűzfalkapcsolatot megnyitása nélkül, és anélkül, hogy zavaró módosításokat kellene végrehajtani a vállalati hálózati infrastruktúrában.

### <a name="what-is-a-relay-namespace"></a>Mi az a Relay-névteret?
A [névtér](relay-create-namespace-portal.md) egy hatókörkezelési tároló, amely a továbbító erőforrások használhatja az alkalmazásban. Létre kell hoznia egy névteret Relay használata. Ez az első lépéseket az első lépések egyikét.

### <a name="what-happened-to-service-bus-relay-service"></a>Mi történt a Service Bus Relay szolgáltatás?
A korábban elnevezett Service Bus Relay szolgáltatás új neve [WCF-továbbító](relay-wcf-dotnet-get-started.md). A szolgáltatás használatához a megszokott módon folytathatja. A hibrid kapcsolatok szolgáltatása egy olyan szolgáltatás, amely az Azure BizTalk Services rendszer lett visszaültetett frissített verzióját. A WCF-továbbító és a hibrid kapcsolatok egyaránt továbbra is támogatottak.

## <a name="pricing"></a>Díjszabás
Ez a szakasz néhány gyakori kérdés a díjszabási struktúrája Relayjel kapcsolatos ad választ. Azt is láthatja a [Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/) általános Azure díjszabási információk. A Relay díjszabással kapcsolatos részletes információkért lásd: [díjszabása a Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hogyan tegye meg díja szerint számítjuk fel a hibrid kapcsolatok és WCF-továbbító?
A Relay díjszabással kapcsolatos részletes információkért lásd: a [hibrid kapcsolatok és WCF-továbbítók] [ Pricing overview] a Service Bus díjszabási részleteit ismertető oldal a táblában. Amellett, hogy az oldalon feltüntetett árak díjkötelesek, amelyben az alkalmazás ki van építve az adatközponton kívül kimenő tartozó adatforgalom.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hogyan kell fizetnem a hibrid kapcsolatokhoz?
Az alábbiakban három számlázási példaforgatókönyvek hibrid kapcsolatok:

*   1. forgatókönyv:
    *   Rendelkezik egy egyetlen figyelője működik, például a Hibridkapcsolat-kezelő telepítve van és fut folyamatosan az egész hónapon egy példánya.
    *   A kapcsolat a hónap során 3 GB adatot továbbít. 
    *   A teljes díj összege az 5 USD.
*   2. forgatókönyv:
    *   Rendelkezik egy egyetlen figyelője működik, például a Hibridkapcsolat-kezelő telepítve van és fut folyamatosan az egész hónapon egy példánya.
    *   10 GB adatot továbbít a kapcsolaton, a hónap során.
    *   A teljes díj összege $7.50. Ez a kapcsolat és az első 5 GB-os $5 + $2,50 a további 5 GB adat.
*   3. forgatókönyv:
    *   Két olyan példányt, és a Hybrid Connections Manager telepítve van és fut folyamatosan az egész hónapban, a B rendelkezik.
    *   A kapcsolat a hónap során 3 GB adatot továbbít.
    *   A hónap során a B kapcsolaton 6 GB adatot továbbít.
    *   A teljes díj összege $10,50. Ez A kapcsolat 5 USD + 5 USD a B kapcsolat + 0,50 dollár (a B kapcsolaton hatodik gigabájt).

Fontos, hogy a példákban használt díjak alkalmazható csak a hibrid kapcsolatok az előzetes verzió ideje alatt. Az árak a Hybrid Connections általános forgalomba hozatalkor változhat.

### <a name="how-are-hours-calculated-for-relay"></a>Hogyan számítják ki óra a Relay?

A WCF-továbbító csak a Standard szintű névterek érhető el. Díjszabás és [kapcsolat kvóták](../service-bus-messaging/service-bus-quotas.md) tartozó továbbítók más módon nem változtak. Ez azt jelenti, hogy a továbbítók továbbra is számítunk fel az üzenetek (nem műveletek) és a továbbítási órák száma alapján. További információkért lásd: a ["Hibrid kapcsolatok és WCF-továbbítók"](https://azure.microsoft.com/pricing/details/service-bus/) a díjszabási lapon található táblázat.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Mi történik, ha van több figyelő kapcsolódik egy adott továbbítóhoz való?
Bizonyos esetekben egyetlen továbbítási előfordulhat, hogy több csatlakoztatott figyelők. Számít nyissa meg, ha legalább egy relay-figyelő kapcsolódik hozzá. A további továbbítási óra hozzáadása egy nyitott továbbító eredmények figyelők. A relay feladók (olyan ügyfelek, amelyek meghívása, vagy üzeneteket küldenek továbbítók) száma, amelyek csatlakoztatva vannak a továbbító nem befolyásolja a továbbítási kiszámításának.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hogyan számítják az üzenetek mérőszáma a WCF-továbbítók?
(**Ez csak érvényes WCF-továbbítók. Üzenetek nem, a költség, a hibrid kapcsolatokhoz.** )

Általában továbbítók a számlázandó üzenetek ugyanezzel a módszerrel, amely arra szolgál, az előzőekben leírt felügyelt entitások (üzenetsorok, témakörök és előfizetések) használatával számítják ki. Vannak azonban néhány jelentős különbség.

Egy üzenetet küld egy Service Bus relay-egységnek számít a relay-figyelő, amely fogadja az üzeneteket a "teljes keresztül" küldése. A Service Bus relay, az a relay-figyelő egy kézbesítési követ a küldési művelet nem számít. A kérés-válasz stílus szolgáltatásmeghívási (összesen legfeljebb 64 KB-os) szemben a relay két számlázandó üzenetek figyelő eredményez: a kérés és a válasz üzenet egy számlázandó üzenet egy számlázandó (feltéve, hogy a válasz egyben 64 KB-os vagy kisebb). Ez eltér attól az ügyfél és a egy szolgáltatás közötti résidőkiosztással egy üzenetsorba. Egy üzenetsor használatával résidőkiosztással egy ügyfél és a egy szolgáltatás között, ha a kérés-válasz minta egy kérelem küldése az üzenetsorba, kiegészítve a eltávolítása onnan/kézbesítési az üzenetsorból a szolgáltatás szükséges. Ide kerül egy válasz küldése egy másik üzenetsornak, és a egy eltávolítása onnan/kézbesítési az ügyfél számára, hogy az üzenetsorból. Az azonos mérete feltételezéseket egész (legfeljebb 64 KB-os) használ, a által üzenetsor mintája eredményez 4 számlázandó üzenetek. Kétszer ugyanazt a mintát, amely a továbbító végrehajtásában megvalósításához üzenetek számát számítjuk. Természetesen van arra, hogy ezt a mintát, például tartósságot érhet el, és a Terheléskiegyenlítés üzenetsorok használatával. Ezeket az előnyöket előfordulhat, hogy adja meg a további költségek.

Továbbítók használatával megnyitott a **netTCPRelay** WCF kötés üzenetek kezelni, nem az egyes üzenetek, hanem egy adatfolyam áthaladnak a rendszeren. Ha ezt a kötést használja, csak a küldő és a figyelőt, hogy az egyes üzenetek küldése és fogadása a keretező. A továbbítókat használó a **netTCPRelay** kötés esetén minden adat számít egy stream kiszámításához a számlázandó üzenetek. Ebben az esetben a Service Bus számítja ki a teljes adatmennyiség küldött vagy fogadott keresztül minden egyes relay 5 perces időközönként. Ezt követően, elosztja a teljes adatmennyiség 64 KB-os meghatározni, hogy a továbbító esetében a számlázandó üzenetek száma időszak során.

## <a name="quotas"></a>Kvóták
| Kvóta neve | Hatókör |  Megjegyzések | Érték |
| --- | --- | --- | --- |
| A továbbítási egyidejű figyelők |Entitás |További kapcsolatok későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. |25 |
| Egyidejű kapcsolatok egy szolgáltatási névtér összes továbbítási végpontok száma |Névtér |- |5000 |
| Szolgáltatásnévtér továbbítási végpontra |Névtér |- |10,000 |
| Üzenet mérete a [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) és [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) továbbítókat |Névtér |Ezek a kvóták túllépéséből bejövő üzenetek a rendszer elutasítja, és a hívó kód által fogadott kivétel. |64 KB |
| Üzenet mérete a [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) és [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) továbbítókat |Névtér |Üzenet mérete nincs korlátozva. |Korlátlan |

### <a name="does-relay-have-any-usage-quotas"></a>Rendelkezik-e Relay használati kvóták?
Alapértelmezés szerint minden olyan felhőalapú szolgáltatás, a Microsoft ügyfél-előfizetések összes számított egy összesített havi használati kvóta állítja be. Tisztában vagyunk vele, hogy időnként igényeinek meghaladhatja ezeket a korlátokat. Forduljon ügyfélszolgálat bármikor, ezért azt igényeinek, és ennek megfelelően módosítsa ezeket a korlátokat. A Service Bus az összesített használati kvóták a következők:

* 5 milliárd üzenetek
* 2 millió továbbítási óra

Bár a fenntartjuk a jogot arra, hogy tiltsa le, amely meghaladja a havi használati kvóták, értesítő e-mailt kínálunk, és azt, hogy több megkísérel kapcsolatba lépni az ügyfél előtt műveleteknél véve. Ezek a kvóták túllépéséből ügyfelek továbbra is felelős további díjak.

### <a name="naming-restrictions"></a>Vonatkozó elnevezési korlátozás
A Relay-névtér neve 6 és 50 karakter hosszúságúnak kell lennie.

## <a name="subscription-and-namespace-management"></a>Előfizetés és a névtér-kezelés
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan migrálhatom a névtér egy másik Azure-előfizetésre?

A névtér egy Azure-előfizetésből áthelyezése egy másik előfizetésben, használhatja a [az Azure portal](https://portal.azure.com) vagy a PowerShell-parancsokkal. Egy névtér áthelyezése egy másik előfizetést, a névtér már aktívnak kell lennie. A felhasználó a parancsok futtatása egy rendszergazda felhasználó a forrás és a cél előfizetések kell lennie.

#### <a name="azure-portal"></a>Azure Portal

Az Azure portal segítségével Azure Relay-névterek áttelepítése másik előfizetésre egy előfizetésből, lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

A PowerShell használatával egy Azure-előfizetéssel névtér áthelyezése egy másik előfizetésben, használja a következő parancssorozat. Ez a művelet végrehajtásához a névtér már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak kell lennie az előfizetések mind a forrás- és a egy rendszergazda felhasználó.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Mik a kivételek Azure Relay API-k által létrehozott, és a javasolt műveletek végezhetők el?
Gyakori kivételek és javasolt műveletek végezhetők el, olvassa el [kivételek továbbítási][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Mi az a közös hozzáférésű jogosultságkódot, és milyen nyelveket használhatok aláírást létrehozni?
A közös hozzáférésű Jogosultságkódok (SAS) olyan hitelesítési mechanizmust, SHA-256 biztonságos kivonatok vagy URI-k alapján. A saját aláírások létrehozását a Node, PHP, Java, C és C# kapcsolatos információkért lásd: [közös hozzáférésű jogosultságkódok használata a Service Bus-hitelesítés][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Az engedélyezési lista továbbítási végpontok is?
Igen. A relay-ügyfél létesít kapcsolatot az Azure Relay szolgáltatás a teljes tartománynevek használatával. Ügyfelek adhat hozzá egy bejegyzés `*.servicebus.windows.net` a DNS-engedélyezési támogató tűzfalak.

## <a name="next-steps"></a>További lépések
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Ismerkedés a csomópont](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
