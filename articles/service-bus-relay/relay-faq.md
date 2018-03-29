---
title: Az Azure továbbító – gyakori kérdések |} Microsoft Docs
description: Azure Relayjel kapcsolatos gyakori kérdésekre adott válaszok.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: sethm
ms.openlocfilehash: d433fb916280e98dd0f2af61728596b8566be71b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-relay-faqs"></a>Az Azure továbbító – gyakori kérdések

Ebben a cikkben megválaszolunk néhány gyakran ismételt kérdések (GYIK) kapcsolatos [Azure továbbítási](https://azure.microsoft.com/services/service-bus/). Általános Azure tarifa- és támogatási információk: [Azure támogatja – gyakori kérdések](https://azure.microsoft.com/en-in/support/faq/).

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-relay"></a>Mi az az Azure Relay?
A [Azure továbbítási szolgáltatás](relay-what-is-it.md) segíti a hibrid alkalmazásait segítenek elérhetővé biztonságosan szolgáltatásokhoz, amelyek a nyilvános felhőben vállalati hálózaton belül találhatók. A szolgáltatások is elérhetővé teheti, tűzfal-kapcsolat létesítése és nélküli zavaró módosításokat kellene a vállalati hálózati infrastruktúrában végrehajtani.

### <a name="what-is-a-relay-namespace"></a>Mi az a továbbítási névtér?
A [névtér](relay-create-namespace-portal.md) egy hatókörkezelési tárolót, melyekkel továbbítási erőforrások kezeléséhez az alkalmazáson belül van. A névtér használatához létre kell hoznia. Ez az első lépések az első lépések egyikét.

### <a name="what-happened-to-service-bus-relay-service"></a>Mi történt a Service Bus Relay szolgáltatás?
A korábban elnevezett Service Bus Relay szolgáltatás neve [WCF továbbító](relay-wcf-dotnet-get-started.md). Továbbra is a megszokott módon használja ezt a szolgáltatást. A hibrid kapcsolat egy szolgáltatás, amely az Azure BizTalk szolgáltatások van lett visszaültetett frissített verziója. WCF továbbító és a hibrid kapcsolatok mindkét továbbra is használhatók.

## <a name="pricing"></a>Díjszabás
Ez a szakasz néhány gyakori kérdés a struktúra árképzési Relayjel kapcsolatos választ ad. Is látható [Azure támogatás – gyakori kérdések](http://go.microsoft.com/fwlink/?LinkID=185083) általános Azure-beli árakról. A továbbító árazással kapcsolatos részletes információkért lásd: [díjszabása Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hogyan tegye meg a díjat hibrid kapcsolatok és WCF továbbító?
A továbbító árazással kapcsolatos részletes információkért tekintse meg a [hibrid kapcsolatok és WCF-továbbítók] [ Pricing overview] tábla díjszabás részleteit megjelenítő oldalon a Service buson. Mellett az adott oldalon rögzített árakat van szó, a kimenő forgalom kívül az adatközpontban, amelyben az alkalmazás ki van építve a kapcsolódó adatátvitel.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hogyan vagyok számlázása a hibrid kapcsolatok?
Az alábbiakban a három számlázási példaforgatókönyvek hibrid kapcsolatok:

*   1. forgatókönyv:
    *   Lehetősége van egy figyelő, például a hibrid kapcsolatok Manager telepített és a teljes hónap folyamatosan futó példánya.
    *   A hónapban a kapcsolaton keresztül küldött adatok 3 GB. 
    *   A teljes költség: $5.
*   2. forgatókönyv:
    *   Lehetősége van egy figyelő, például a hibrid kapcsolatok Manager telepített és a teljes hónap folyamatosan futó példánya.
    *   10 GB adatot küld a hónapban a kapcsolaton keresztül.
    *   A teljes költség $7.50. Ez a kapcsolat és az első 5 GB $5 + $2.50 a további 5 GB adat.
*   3. forgatókönyv:
    *   Két olyan példányt, és a B telepítve, és a teljes hónap folyamatosan fut a hibrid kapcsolatok-kezelő rendelkezik.
    *   A hónapban A kapcsolaton keresztül küldött adatok 3 GB.
    *   A hónap során B kapcsolaton keresztül küld 6 GB adatot.
    *   A teljes költség $10,50. Ez a kapcsolat A $5 + $5 kapcsolat B + 0,50 $ (a B kapcsolaton hatodik GB).

Vegye figyelembe, hogy az a példákban szereplő árak a megfelelő csak a hibrid kapcsolatok próbaidőszak alatt. Árak általánosan rendelkezésre álló hibrid kapcsolatok alapján változhatnak.

### <a name="how-are-hours-calculated-for-relay"></a>Hogyan kiszámítása a továbbítási óra?

WCF továbbító csak a Standard csomag névterek érhető el. Tarifacsomag és [kapcsolat kvóták](../service-bus-messaging/service-bus-quotas.md) a továbbítók más módon nem változtak. Ez azt jelenti, hogy továbbítók számlázni üzenetek (nem műveletek) és továbbítási óra száma alapján. További információkért lásd: a ["Hibrid kapcsolatok és WCF továbbítók"](https://azure.microsoft.com/pricing/details/service-bus/) tábla árképzési részleteit megjelenítő oldalon.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Mi történik, ha egynél több figyelő egy adott továbbítóhoz való csatlakoztatva van?
Bizonyos esetekben egyetlen továbbítóként rendelkezhet több kapcsolódó figyelők. A továbbító nyissa meg tekintendő, ha legalább egy továbbító figyelő csatlakoztatva. Egy nyitott továbbító eredmények figyelői hozzáadása a további továbbítási óra. A továbbító feladók (ügyfelek invoke vagy üzenetek küldése továbbítók) száma, amelyek csatlakoztatva vannak egy továbbítót, nem érinti a továbbítási óra.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hogyan kiszámítása a WCF-továbbítókat a üzenetek mérő?
(**WCF továbbítók csak vonatkozik. Üzenetek csak egy hibrid kapcsolatok költséget.** )

Általában a továbbítók számlázható üzenetek ugyanezt a módszert a fentiekben ismertetett közvetített entitásokat (üzenetsorok, témakörök és előfizetések), amellyel segítségével számított. Van azonban néhány fontos különbség.

Egy üzenetet küld egy Service Bus-továbbító a rendszer kezeli a továbbítási figyelő, amely fogadja az üzenetet küldeni a "teljes keresztül". A Service Bus relay, egy a továbbítási figyelő kézbesítése követi egy küldési művelet nem minősül. Kérelem-válasz stílusú szolgáltatás hívása nem volt (legfeljebb 64 KB) szemben a továbbító két számlázható üzeneteket figyelő eredményezi: egy számlázható üzenetet a kérelem és a válasz egy számlázható üzenet (feltéve, hogy a válasz egyben 64 KB-os vagy kisebb). Ez eltér attól, hogy a várólista segítségével résidőkiosztással egy ügyfél és a szolgáltatás között. Ha egy ügyfél és a szolgáltatás között résidőkiosztással várólista használja, a azonos kérelem-válasz a kialakítás megköveteli egy kérelem küldése az üzenetsorba, követ egy dequeue/kézbesítési a várólistából a szolgáltatáshoz. Ide kerül egy válasz küldése egy másik várólistához, és egy dequeue/kézbesítése az ügyfélnek várólistában. Az azonos mérete feltételezéseket egész (legfeljebb 64 KB) használ, a várólista által mintát eredményez, 4 számlázható üzenet. Meg szeretné is fizetnie kell kétszer megvalósítása, amely a továbbító elvégzésében ugyanilyen mintájú üzenetek száma. Természetesen vannak előnyei a várólisták segítségével érhetők el ebben a mintában, például a tartósság és a terheléselosztás. Ilyen előnyt előfordulhat, hogy indokolják a további költségeket.

A megnyitott továbbítók a **netTCPRelay** WCF kötés üzenetek kezelni, nem egyedi üzenetekként, de a rendszer keresztül áramló adatok adatfolyamként. A kötés használata esetén csak a küldő és a figyelő látnak bele az egyes üzenetek küldésének és fogadásának keretezési. A használó továbbítja a **netTCPRelay** kötés, minden adatot a rendszer egy adatfolyam számlázható üzenetek kiszámításához. Ebben az esetben a Service Bus küldött, vagy minden egyes továbbítási 5 perces időközönként keresztül fogadott adatok mekkora számítja ki. Ezt követően azt osztja a teljes adatmennyiség szerint 64 KB-os annak meghatározásához, hogy a továbbító számlázható üzenetek száma az adott időszak alatt.

## <a name="quotas"></a>Kvóták
| Kvóta neve | Hatókör |  Megjegyzések | Érték |
| --- | --- | --- | --- |
| A továbbító egyidejű figyelőinek |Entitás |A további kapcsolatokhoz későbbi kérelmek azért lettek elutasítva, és kivételt megkapta a hívó kód. |25 |
| A névtér összes továbbítási végpontok egyidejű továbbító-kapcsolatok |Névtér |- |5000 |
| Továbbítási végpontok száma a szolgáltatás névtere |Névtér |- |10,000 |
| Üzenet mérete [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) és [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) továbbítja |Névtér |Meghaladnia ezek mely százalékértékénél kéri, hogy a bejövő üzenetek utasítja el, és egy kivételt a hívó kód érkezik. |64 KB |
| Üzenet mérete [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) és [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) továbbítja |Névtér |Az üzenet mérete nincs korlátozva. |Korlátlan |

### <a name="does-relay-have-any-usage-quotas"></a>Rendelkezik a továbbítási bármely használati kvóták?
Alapértelmezés szerint az összes felhőalapú szolgáltatás a Microsoft összes egy ügyfél-előfizetések számított egy összesített havi memóriahasználati kvóta állítja be. Tudjuk, hogy időnként igényeinek előfordulhat, hogy meghaladja ezt a korlátot. Felveheti a kapcsolatot az ügyfélszolgálat bármikor, így azt igényeinek megismeréséhez és annak megfelelően módosítsa a működés felső korlátjának. Service Bus a használati kvóták a következők:

* 5 milliárd üzenetek
* 2 millió továbbítási óra

Bár azt lefoglalni a jogot, hogy tiltsa le a fiókot, amelyek túllépik a havi használati kvóták, e-mailben értesítést nyújtunk és több biztosítjuk próbál meg, forduljon az ügyfél bármely megtétele előtt. Ügyfelek, amelyek mérete meghaladja a ezek mely százalékértékénél kéri továbbra is felelőssége felesleges költségek.

### <a name="naming-restrictions"></a>Vonatkozó elnevezési korlátozás
A továbbító névtér nevét 6 és 50 karakter hosszúságúnak kell lennie.

## <a name="subscription-and-namespace-management"></a>Előfizetés és a névtér-kezelés
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan névtér át más Azure-előfizetések?

A névtér egy Azure-előfizetésből áthelyezése egy másik előfizetést, használhatja a [Azure-portálon](https://portal.azure.com) vagy PowerShell-parancsokkal. Egy névtér áthelyezése egy másik előfizetést, a névtér már aktívnak kell lennie. A felhasználó a parancsok futtatásával egy rendszergazda felhasználó a forrás- és a cél előfizetések kell lennie.

#### <a name="azure-portal"></a>Azure Portal

Az Azure portál segítségével Azure továbbítási névterek telepítenek át egy előfizetés másik előfizetéssel, lásd: [erőforrások áthelyezése egy új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

PowerShell névtér áthelyezése egy másik előfizetést egy Azure-előfizetéssel, használja a következő parancssorozat. Ez a művelet végrehajtásához a névtér már aktívnak kell lennie, és a felhasználó a PowerShell-parancsok futtatásával egy rendszergazda felhasználó a forrás- és a cél előfizetések kell lennie.

```powershell
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Melyek azok a kivételek Azure továbbítási API-k által létrehozott, és a javasolt elvégezhető műveletek?
A közös kivételeket és a javasolt műveletek is igénybe vehet, olvassa el [kivételek továbbítása][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Mi az a közös hozzáférésű jogosultságkód, és milyen nyelveket használhatok aláírást létrehozni?
Megosztott hozzáférési aláírásokkal (SAS) olyan hitelesítési mechanizmus biztonságos SHA-256 kivonatokkal vagy URI-k alapján. A saját aláírások csomópont, PHP, Java, C és C# létrehozásával kapcsolatos információkért lásd: [Service Bus hitelesítési megosztott hozzáférési aláírásokkal][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Az engedélyezési lista továbbítási végpontok is?
Igen. A továbbító ügyfél teljes tartománynevek használatával lehetővé teszi az Azure-továbbítási szolgáltatáshoz. Az ügyfelek is hozzáadhat egy bejegyzést a `*.servicebus.windows.net` a tűzfalak, amely támogatja a DNS engedélyezett.

## <a name="next-steps"></a>További lépések
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared access signatures]: ../service-bus-messaging/service-bus-sas.md
