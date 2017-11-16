---
title: "Az Azure Service Bus gyakori kérdések (GYIK) |} Microsoft Docs"
description: "Azure Service Bus kapcsolatban gyakran feltett kérdésekre adott válaszok."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: e64e7d9f203debe19dfa222f501c7902cfe2ae98
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="service-bus-faq"></a>Service Bus – GYIK
A cikkből néhány gyakori kérdés a Microsoft Azure Service Bus kapcsolatban. Is letöltheti a [Azure támogatja – gyakori kérdések](http://go.microsoft.com/fwlink/?LinkID=185083) általános Azure tarifa- és támogatási információkat.

## <a name="general-questions-about-azure-service-bus"></a>Azure Service Bus kapcsolatos általános kérdésekre
### <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?
[Az Azure Service Bus](service-bus-messaging-overview.md) egy aszinkron üzenetkezelési felhő platform, amely lehetővé teszi a leválasztott rendszerek közötti adatküldéshez. A Microsoft a szolgáltatás biztosít szolgáltatásként, ami azt jelenti, hogy nem szeretne futtatni valamelyik saját hardver akarja használni.

### <a name="what-is-a-service-bus-namespace"></a>Mi az a Service Bus-névtér?
A [névtér](service-bus-create-namespace-portal.md) egy hatókörkezelési tárolót biztosít az alkalmazáson belül a Service Bus erőforrásainak kezeléséhez. Névtér létrehozása a Service Bus használata szükséges, és az első lépéseket, első lépések egyike.

### <a name="what-is-an-azure-service-bus-queue"></a>Mi az az Azure Service Bus-üzenetsorba?
A [Service Bus-üzenetsorba](service-bus-queues-topics-subscriptions.md) az üzeneteket tároló entitás. Várólisták hasznosak, ha több alkalmazást, vagy egymással kommunikálni kell elosztott alkalmazás több részét. A várólista egy kulcskiosztó központ hasonlít abban, hogy több termékeket (üzenet) kapott, és elküldi a helyről.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Mik az Azure Service Bus-üzenettémák és előfizetések?
A témakör is ábrázolt, várólista és használ több előfizetéssel, lesz egy gazdagabb üzenetkezelési modellt; lényegében egy-a-többhöz kommunikációs eszközt. Ez a közzétételi/előfizetési modell (vagy *pub/sub*) lehetővé teszi az alkalmazás, amely egy üzenetet küld egy témakör több előfizetéssel kell rendelkeznie, hogy több alkalmazás által fogadott üzenet.

### <a name="what-is-a-partitioned-entity"></a>Mi az a particionált entitás?
Hagyományos üzenetsor vagy témakör egyetlen üzenet ügynök által kezelt és egy üzenetküldési tárolóban tárolja. A [particionált üzenetsor vagy témakör](service-bus-partitioning.md) több üzenetet brókerek kezeli és több üzenetküldési tároló tárolja. Ez azt jelenti, hogy a teljes átviteli képessége – a particionált üzenetsor vagy témakör már nem korlátozzák a egyetlen üzenet broker vagy az üzenetküldési tárolóban teljesítményét. Ezenkívül átmenetileg nem működik az üzenetküldési tárolóban nem képezhető le egy particionált üzenetsor vagy témakör nem érhető el.

Vegye figyelembe, hogy a rendezés nem biztosított, ha particionált entitások. Abban az esetben, ha a partíció nem érhető el, továbbra is küldhet és a többi partíció érkező üzenetek fogadására.

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="what-are-some-azure-service-bus-best-practices"></a>Mik az Azure Service Bus tanácsokat?
Lásd: [ajánlott eljárások a teljesítménnyel kapcsolatos fejlesztések a Service Bus használatával] [ Best practices for performance improvements using Service Bus] – Ez a cikk ismerteti, hogyan optimalizálható a teljesítmény, ha üzenetváltásokban.

### <a name="what-should-i-know-before-creating-entities"></a>Mit kell tudnia entitások létrehozása előtt?
Egy üzenetsor és a témakör következő tulajdonságai nem módosíthatók. Megfontolnia ezen korlátozás az entitások, ha ezeket a tulajdonságokat nem lehet módosítani a csere új entitás létrehozása nélkül.

* Méret
* Particionálás
* Munkamenetek
* Kettős észlelés
* Entitás Express

## <a name="pricing"></a>Díjszabás
Ez a szakasz néhány kapcsolatos gyakori kérdések a Service Bus struktúra árképzési ad választ.

A [Service Bus árak és számlázás](service-bus-pricing-billing.md) a cikk ismerteti a Service Bus számlázási mérőszámok. A Service Bus beállítások árképzési vonatkozó információkat, lásd: [díjszabása Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Emellett letöltheti a [Azure támogatás – gyakori kérdések](http://go.microsoft.com/fwlink/?LinkID=185083) általános Azure-beli árakról. 

### <a name="how-do-you-charge-for-service-bus"></a>Hogyan tegye díjat számítanak, Service Bus?
A Service Bus árazással kapcsolatos részletes információkért lásd: [díjszabása Service Bus][Pricing overview]. Mellett az áttelepítés előtt feljegyzett árak van szó, a kimenő forgalom kívül az adatközpont, ahol az alkalmazás ki van építve a kapcsolódó adatátvitel.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>A Service Bus milyen használatát az adatátvitel függvényében? Mi az a nem?
Bármely adatátvitel belül egy adott Azure-régió, díjmentesen, valamint a bejövő adatforgalom valósul meg. Kimenő forgalom költségek, amelyek megtalálhatók az adatátvitel kívül egy régiót [Itt](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>A Service Bus a tároló nem díjat?
Nem, a Service Bus nem terheli a tároláshoz. Van azonban egy kvótát a várólista/témakör / fennállásának adatok maximális mérete korlátozza. Tekintse meg a következő gyakran ismételt kérdések.

## <a name="quotas"></a>Kvóták

A Service Bus-korlátozások és a kvóták listájáért lásd: a [Service Bus kvóták áttekintése][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Rendelkezik a Service Bus bármely használati kvóták?
Alapértelmezés szerint minden felhő szolgáltatás Microsoft állítja be az összes olyan ügyfél-előfizetések számított egy összesített havi memóriahasználati kvóta. Tudjuk, hogy szükség lehet több, mint a működés felső korlátjának, mert kérhet ügyfélszolgálat bármikor, hogy azt igényeinek megismeréséhez és annak megfelelően módosítsa a működés felső korlátjának. Service Bus a összesített memóriahasználati kvóta havonta 5 milliárd üzenetek esetén.

Azt fenntartja a jogot, hogy egy felhasználói fiókot, amely a használati kvóták túllépte az adott hónapban letiltása, amíg azt adja meg az e-mail értesítéseket, és többször megkísérelje el az ügyfél bármely megtétele előtt. Ezek mely százalékértékénél kéri meghaladó ügyfelek továbbra is felelőssége, amelyek mérete meghaladja a kvóták díjakat.

Más Azure-szolgáltatások, a Service Bus kikényszeríti egy adott kvóták győződjön meg arról, hogy van-e az erőforrások igazságos használati készletét. Ezek mely százalékértékénél kéri, a további információkat is talál a [Service Bus kvóták áttekintése][Quotas overview].

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Mik az egyes Azure Service Bus API-k és a javasolt lépések által létrehozott kivételek?
A lehetséges a Service Bus-kivételek listájáért lásd: [kivételek áttekintése][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Mi az, hogy egy közös hozzáférésű Jogosultságkód és nyelveket támogatja az aláírás generálása?
Megosztott hozzáférési aláírásokkal olyan hitelesítési mechanizmus, SHA-256 biztonságos kivonatok vagy URI-k alapján. További információ a saját aláírást létrehozni csomópont, PHP, Java és C\#, tekintse meg a [megosztott hozzáférési aláírásokkal] [ Shared Access Signatures] cikk.

## <a name="subscription-and-namespace-management"></a>Előfizetés és a névtér-kezelés
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan névtér át más Azure-előfizetések?

Áthelyezheti a névtér egy Azure-előfizetésből, segítségével a [Azure-portálon](https://portal.azure.com) vagy PowerShell-parancsokat. A művelet végrehajtásához a névtér már aktívnak kell lennie. A felhasználó a parancsok végrehajtása a forrás- és a célként megadott előfizetés rendszergazdájának kell lennie.

#### <a name="portal"></a>Portál

Az Azure-portál használatával a Service Bus-névtér át egy másik előfizetést, kövesse az utasításokat [Itt](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

A következő PowerShell parancssorrend helyezi át, a másikra a névtér egy Azure-előfizetésből. Ez a művelet végrehajtásához a névtér már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak rendszergazdai jogokkal kell rendelkeznie a forrás- és a cél előfizetések.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Következő lépések
A Service Bus kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Introducing Azure Service Bus prémium (blogbejegyzés)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus prémium (bemutatása) Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus áttekintése](service-bus-messaging-overview.md)
* [Az Azure Service Bus architektúrájának áttekintése](service-bus-fundamentals-hybrid-solutions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
