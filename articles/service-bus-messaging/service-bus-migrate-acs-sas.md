---
title: "Azure Active Directory hozzáférés-vezérlési szolgáltatásban át közös hozzáférésű Jogosultságkód engedélyezési |} Microsoft Docs"
description: "Hozzáférés-vezérlési szolgáltatásban alkalmazásokat SAS-át"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 7a2a55a6ad6a721a39c9f064aad817f841dd3235
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Az Azure Active Directory hozzáférés-vezérlési szolgáltatásban közös hozzáférésű Jogosultságkód engedélyezési áttelepítése

A Service Bus alkalmazások korábban volt kiválaszthatja, hogy két különböző engedélyezési modell használatával: a [közös hozzáférésű Jogosultságkód (SAS)](service-bus-sas.md) token modell, közvetlenül a Service Bus által biztosított, és egy összevont modell ahol kezeléséhez az engedélyezési szabályok által kezelt belül a [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS), és az ACS-től kapott jogkivonatok kerülnek átadásra a Service Bus, amelyek engedélyezik a kívánt funkciók.

Az ACS-engedélyezési modellt hosszú újabbra cserélték [SAS engedélyezési](service-bus-authentication-and-authorization.md) modellt, valamint az összes dokumentációját, útmutatás és példák kizárólag használja SAS ma. Ezenkívül az már nem lehet létrehozni új Service Bus-névterek ACS van párosítva.

SAS azzal az előnnyel jár, abban, hogy nincs azonnal másik szolgáltatástól függ, de az ügyfél-hozzáférési jogosultságot ad a SAS szabály nevét és a szabály kulcsának által használható közvetlenül nélkül bármely közvetítők ügyfélről. SAS is könnyen integrálható egy módszert, amelyben egy ügyfél először adjon át egy másik szolgáltatással jogosultsági ellenőrzés és a majd egy tokent ad ki. Ez utóbbi megközelítés az ACS-használati szokásokkal hasonló, de lehetővé teszi, hogy a kiállító hozzáférési jogkivonatok az ACS express nehezen alkalmazásspecifikus feltételek alapján.

Az összes meglévő alkalmazások esetében ACS függ azt ajánljuk telepítse át az alkalmazásaikat, helyette használják a SAS.

## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

ACS és a Service Bus integrált keresztül megosztott ismerete egy *aláírókulcsának*. Az aláírási kulcs segítségével egy ACS-névtér engedélyezési jogkivonatok aláírásához, és használják a Service Bus ellenőrzése, hogy a jogkivonat a párosított ACS-névtér nem adják. Az ACS-névtér tárolja a szolgáltatás-identitások és engedélyezési szabályok. Az engedélyezési szabályok határozza meg, melyik szolgáltatásidentitás vagy melyik külső által kiadott tokennek identitásszolgáltató lekérdezi a Service Bus-névtér része elérésére milyen típusú diagramot, talált leghosszabb előtag egyezés formájában.

Például előfordulhat, hogy adja meg az ACS-szabály a **küldése** az elérési út előtag alapján jogcím `/` hozzá egy szolgáltatás, ami azt jelenti, hogy egy adott szabály alapján ACS által kiadott tokennek az ügyfél jogok megadása a névtér összes entitásának küldeni. Ha az elérési út előtag `/abc`, az identitás korlátozódik nevű entitások küldése `abc` vagy ezt az előtagot alatt vannak rendszerezve. Feltételezzük, hogy az áttelepítési útmutató az olvasó már jártas a ezekről a fogalmakról.

Az áttelepítési forgatókönyvek sorolhatók három kategóriába sorolhatók:

1.  **Alapértelmezett értéke változatlan**. Néhány az ügyfelek egy [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objektum átadása az automatikusan létrehozott **tulajdonos** identitás- és a titkos kulcsot az ACS névtér esetén a Service Bus-névtér megfeleltetni szolgáltatás és ne vegyen fel új szabályokat.

2.  **Egyéni szolgáltatás identitásokat az egyszerű szabályok**. Egyes ügyfelek új szolgáltatás-identitások hozzáadása, és adja meg minden egyes új szolgáltatásidentitás **küldése**, **figyelésére**, és **kezelése** egy adott entitás engedélyeit.

3.  **Egyéni szolgáltatás identitásokat az összetett szabályok**. Kevés ügyfelet, hogy mely külsőleg kiállított jogkivonatokat a továbbítási jog van leképezve, vagy ha egyetlen szolgáltatásidentitás hozzá van rendelve a készletek megkülönböztetett keresztül több szabály több névtér elérési utakon található jogok összetett szabály.

Összetett szabálykészletek áttelepítési segítségért forduljon [az Azure támogatási](https://azure.microsoft.com/support/options/). A többi két esetben engedélyezze az egyszerű áttelepítést.

### <a name="unchanged-defaults"></a>Változatlan alapértelmezései

Ha az alkalmazás nem változott meg az ACS alapértelmezett értékeket, lecserélheti az összes [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) használatát a egy [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objektumot, és használja a névteret, előre konfigurált  **RootManageSharedAccessKey** helyett az ACS **tulajdonos** fiók. Vegye figyelembe, hogy az ACS mellett is **tulajdonos** fiók, ez a konfiguráció volt (és továbbra is az) általában nem ajánlott, mert a fiók szabály biztosítanak a névtér, például engedélyezett számára, hogy törli a teljes felügyeleti hatóság entitások.

### <a name="simple-rules"></a>Az egyszerű szabályok

Ha az alkalmazás egyszerű szabályok egyéni szolgáltatás-identitások használja, az áttelepítés akkor abban az esetben, ha létrehozott egy ACS-szolgáltatásidentitás, amely adjon meg egy konkrét várólistába helyezi a hozzáférés-vezérlés egyszerű. Ebben a forgatókönyvben a helyzet gyakran a Szolgáltatottszoftver-stílusú megoldások ahol egyes várólisták használatos az adott webhelyhez egy bérlői webhely vagy a fiókiroda és a szolgáltatás identitásának hidat jön létre. Ebben az esetben a megfelelő szolgáltatásidentitás telepíthető át egy közös hozzáférésű Jogosultságkód-szabályok, közvetlenül a várólistát. A szolgáltatásnév identitás válhat a SAS-szabály neve, és a szolgáltatás identitáskulcs válhat a szabály SAS-kulcsot. A jogosultságok a SAS-szabály nem, akkor a rendre megfelelő ACS konfigurált egyenértékű szabály az entitáshoz.

Ezen új és további konfigurációja SAS helyben a meglévő névtér, amely össze van vonva az ACS a végezheti el, és az áttelepítés befejeződött az ACS használatával ezt követően történik [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) Ahelyett, hogy [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). A névtér nem kell az ACS-től megszüntetése.

### <a name="complex-rules"></a>Összetett szabályok

SAS-szabályok nem célja, hogy fiókokat, de megnevezett aláírási kulcsokat, társított jogosultságok. Ilyen forgatókönyvek, amelyben az alkalmazás számos szolgáltatás-identitások hoz létre, és megadja számára hozzáférési jogosultsággal több entitások, vagy a teljes névtér továbbra is szükséges a jogkivonat-kiállító közvetítő. Ezt úgy szerezheti be által ilyen köztes útmutatást [lépjen kapcsolatba az ügyfélszolgálattal](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>További lépések

A Service Bus-hitelesítéssel kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [A Service Bus-hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md)
* [A Service Bus hitelesítési megosztott hozzáférési aláírásokkal](service-bus-sas.md)
* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)

