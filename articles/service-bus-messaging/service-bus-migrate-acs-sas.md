---
title: Azure Service Bus – Áttelepítés a megosztott hozzáférésű aláírás engedélyezésére
description: Megtudhatja, hogy miként migrálhat az Azure Active Directory hozzáférés-vezérlési szolgáltatásból a megosztott hozzáférés-aláírás engedélyezésére.
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 532bbaf0b983b2d4310780686777cbe895afebe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774619"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Service Bus – Áttelepítés az Azure Active Directory hozzáférés-vezérlési szolgáltatásáról a megosztott hozzáférés-aláírás engedélyezésére

A Service Bus-alkalmazások korábban két különböző engedélyezési modellt is választhattak: a közvetlenül a Service Bus által biztosított [megosztott hozzáférésű aláírási (SAS)](service-bus-sas.md) jogkivonat-modellt, és egy összevont modellt, ahol az engedélyezési szabályok kezelését az [Azure Active Directory](/azure/active-directory/) hozzáférés-vezérlési szolgáltatás (ACS) kezeli, és az ACS-ből beszerzett jogkivonatokat átadják a Service Bus-nak a kívánt funkciókhoz való hozzáférés engedélyezéséhez.

Az ACS engedélyezési modell már régóta felülírta a [SAS-hitelesítés,](service-bus-authentication-and-authorization.md) mint az előnyben részesített modell, és minden dokumentáció, útmutatás t és minták kizárólag a SAS ma. Ezenkívül már nem lehet új Service Bus-névtereket létrehozni, amelyek az ACS-vel vannak párosítva.

A SAS azzal az előnnyel rendelkezik, hogy nem függ azonnal egy másik szolgáltatástól, hanem közvetlenül az ügyféltől is használható közvetítők nélkül, ha hozzáférést biztosít az ügyfélnek a SAS-szabály nevéhez és szabálykulcsához. A SAS könnyen integrálható egy olyan megközelítéssel, amelyben az ügyfélnek először át kell mennie egy engedélyezési ellenőrzésen egy másik szolgáltatással, majd jogkivonatot kell kiadnia. Az utóbbi megközelítés hasonló az ACS-használati mintához, de lehetővé teszi a hozzáférési jogkivonatok kiadását az ACS-ben nehezen fejezhetővé, alkalmazásspecifikus feltételek alapján.

Az ACS-től függő összes meglévő alkalmazás esetében arra kérjük az ügyfeleket, hogy inkább a SAS-ra támaszkodva telepítsék át alkalmazásaikat.

## <a name="migration-scenarios"></a> Áttelepítési forgatókönyvek

Az ACS és a Service Bus az *aláíró kulcs*közös ismeretein keresztül integrálódik. Az aláíró kulcsot egy ACS-névtér használja az engedélyezési jogkivonatok aláírásához, és a Service Bus használja annak ellenőrzésére, hogy a jogkivonatot a párosított ACS-névtér adta-e ki. Az ACS-névtér szolgáltatásidentitásokat és engedélyezési szabályokat tartalmaz. Az engedélyezési szabályok határozzák meg, hogy mely szolgáltatásidentitás vagy egy külső identitásszolgáltató által kibocsátott jogkivonat kapja meg, hogy a Service Bus névtérgráf egy részéhez milyen típusú hozzáférést kap, leghosszabb előtag egyezés formájában.

Egy ACS-szabály például adhat a **Küldés** `/` jogcímet egy szolgáltatásidentitás elérési útelőtagja, ami azt jelenti, hogy az ACS által a szabály alapján kibocsátott jogkivonat megadja az ügyfélnek a névtér ben lévő összes entitásnak való küldéshez való jogot. Ha az elérési `/abc`út előtagja, az identitás az `abc` adott előtag alatt elnevezett vagy rendezett entitások nak való küldésre korlátozódik. Feltételezzük, hogy az áttelepítési útmutató olvasói már ismerik ezeket a fogalmakat.

Az áttelepítési forgatókönyvek három fő kategóriába sorolhatók:

1.  **Változatlan alapértelmezett értékek**. Egyes ügyfelek egy [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objektumot használnak, amely átadja az automatikusan létrehozott **tulajdonosi** szolgáltatásidentitást és titkos kulcsát az ACS névtérhez, párosítva a Service Bus névtérrel, és nem ad hozzá új szabályokat.

2.  **Egyéni szolgáltatásidentitások egyszerű szabályokkal.** Egyes ügyfelek új szolgáltatásidentitásokat adnak hozzá, és minden új szolgáltatásidentitást, **küldés,** **figyelés**és **kezelés** engedélyt adnak egy adott entitáshoz.

3.  **Egyéni szolgáltatásidentitások összetett szabályokkal.** Nagyon kevés ügyfél rendelkezik összetett szabálykészletekkel, amelyekben a külsőleg kiállított jogkivonatok le vannak képezve a továbbítási jogokhoz, vagy ahol egyetlen szolgáltatásidentitás hoz létre differenciált jogokat több névtérútvonalon több szabályon keresztül.

Az összetett szabálykészletek áttelepítésével kapcsolatos segítségért forduljon az [Azure támogatási szolgálatához.](https://azure.microsoft.com/support/options/) A másik két forgatókönyv lehetővé teszi az egyszerű áttelepítést.

### <a name="unchanged-defaults"></a>Változatlan alapértelmezett értékek

Ha az alkalmazás nem módosította az ACS alapértelmezett beállításait, lecserélheti az összes [SharedSecretTokenProvider-használatot](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) egy [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objektumra, és az ACS **tulajdonosi** fiók helyett az előre konfigurált **RootManageSharedAccessKey** névteret használhatja. Vegye figyelembe, hogy még az **ACS-tulajdonosi** fiókesetében is ez a konfiguráció általában nem ajánlott (és még mindig ajánlott), mivel ez a fiók/szabály teljes felügyeleti jogosultságot biztosít a névtér felett, beleértve az entitások törlésének engedélyezését is.

### <a name="simple-rules"></a>Egyszerű szabályok

Ha az alkalmazás egyszerű szabályokkal rendelkező egyéni szolgáltatásidentitásokat használ, az áttelepítés egyszerű abban az esetben, ha egy ACS-szolgáltatásidentitást hoztak létre egy adott várólistán való hozzáférés-vezérlés biztosítása érdekében. Ez a forgatókönyv gyakran előfordul saas-stílusú megoldások, ahol minden várólista egy bérlői hely vagy fiókiroda hídként használják, és a szolgáltatás identitásjön létre az adott helyhez. Ebben az esetben a megfelelő szolgáltatásidentitás áttelepíthető egy megosztott hozzáférésű aláírásszabályba, közvetlenül a várólistán. A szolgáltatás identitásneve válhat a SAS-szabály neve és a szolgáltatás identitáskulcs válhat a SAS szabály kulcs. A SAS-szabály jogosultságai ezután az entitásra vonatkozó ACS-szabállyal egyenértékűek.

A SAS új és további konfigurációját helyben is elvégezheti bármely meglévő névtérben, amely az ACS-vel van összefoglalva, és az ACS-ből való elmigrálás tkövetően a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) használatával történik a [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)helyett. A névteret nem kell leválasztani az ACS-ről.

### <a name="complex-rules"></a>Összetett szabályok

A SAS-szabályok nem fiókok, hanem a jogokhoz társított aláíró kulcsok. Mint ilyen, forgatókönyvek, amelyben az alkalmazás számos szolgáltatásidentitást hoz létre, és több entitás hoz létre hozzáférési jogokat, vagy a teljes névtér továbbra is szükség van egy jogkivonat-kibocsátó közvetítő. Az ilyen közvetítővel kapcsolatos útmutatást az [ügyfélszolgálattal való kapcsolatfelvétel útján kaphatja](https://azure.microsoft.com/support/options/)meg.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus-hitelesítésről, olvassa el az alábbi témaköröket:

* [Service Bus-hitelesítés és -engedélyezés](service-bus-authentication-and-authorization.md)
* [Service Bus-hitelesítés megosztott hozzáférésű aláírásokkal](service-bus-sas.md)

