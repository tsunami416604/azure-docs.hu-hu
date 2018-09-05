---
title: Közös hozzáférésű Jogosultságkód engedélyezési áttelepítése az Azure Active Directory Access Control Service |} A Microsoft Docs
description: Access Control Service SAS-alkalmazások áttelepítésére
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: 39764ce2dce8e00d1b41179e7285a0cea1c808bf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698944"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Az Azure Active Directory Access Control Service közös hozzáférésű Jogosultságkód engedélyezési áttelepítése

Az Azure Relay-alkalmazások hagyományosan két különböző engedélyezési modell használatával többféle volna: a [közös hozzáférésű Jogosultságkód (SAS)](../service-bus-messaging/service-bus-sas.md) közvetlenül a továbbítási szolgáltatás által biztosított jogkivonat modell és a egy összevont modellt ahol felügyeletét, az engedélyezési szabályok által felügyelt belül a [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS), és az ACS-ből kapott jogkivonatok átadott Relay, amelyek engedélyezik a hozzáférést a kívánt funkciókhoz.

Az ACS-engedélyezési modellt hosszú felülírták [SAS engedélyezési](../service-bus-messaging/service-bus-authentication-and-authorization.md) modellt, és az összes dokumentáció, útmutatást és példáink segítségével kizárólag használata SAS még ma. Ezen felül az már nem lehet létrehozni, amely az ACS van párosítva új Relay-névterek.

SAS biztosítja azt az előnyt, hogy nem azonnal másik szolgáltatástól függ, de az ügyfél-hozzáférési a SAS-szabály neve és a szabály kulcsot, így közvetlenül egy ügyféltől közvetítőktől nélkül használható. SAS is könnyen integrálhatók egy módszert használja, amelyben egy ügyfél először át egy másik szolgáltatás jogosultsági ellenőrzés rendelkezik, és ezután van egy jogkivonatot állít ki. Az utóbbi megközelítés ACS használati módja hasonló, de lehetővé teszi, hogy az ACS-ben express nehezen alkalmazásspecifikus feltételek alapján kiállító hozzáférési jogkivonatok.

Az ACS függő összes meglévő alkalmazáshoz azt ajánljuk azonban számíthat a SAS inkább saját alkalmazásokat telepíthet át.

## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

ACS és a Relay keresztül megosztott ismerete integrálva van egy *aláírókulcs*. Az aláíró kulcsot az ACS-névtér hitelesítési jogkivonatok aláírásához használja, és győződjön meg arról, hogy a jogkivonat a párosított ACS-névtér által kibocsátott segítségével az Azure Relay által. Az ACS-névtér szolgáltatásidentitások és engedélyezési szabályokat tartalmazza. Az engedélyezési szabályai határozzák meg, melyik felügyeltszolgáltatás-identitás vagy melyik külső által kiállított jogkivonatok identitásszolgáltató lekérdezi a Relay-névtér tartozik hozzá milyen típusú graph leghosszabb előtag-egyeztetést formájában.

Például előfordulhat, hogy adja meg egy ACS-szabályt a **küldése** az elérési út előtagja a jogcímet `/` a felügyeltszolgáltatás-identitást, ami azt jelenti, hogy egy adott szabály alapján ACS által kiállított jogkivonatok jogosultságot az ügyfél a névtérben lévő összes entitáshoz való küldéséhez. Ha az elérési út előtag `/abc`, az identitás korlátozódik nevű entitások küldését `abc` vagy ezt az előtagot alatt vannak rendezve. Azt feltételezzük, hogy ez az áttelepítési útmutató az olvasók már ismeri a ezeket a fogalmakat.

Az áttelepítési forgatókönyvek három tág kategóriába sorolhatók:

1.  **Alapértelmezett értéke változatlan**. Egyes ügyfelek egy [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objektum átadása az automatikusan létrehozott **tulajdonosa** identitás- és a titkos kulcsot az ACS-névtér, párosítva a Relay-névteret, a szolgáltatást, és hajtsa végre új szabályokat hozzáadni.

2.  **Az egyszerű szabályok egyéni szolgáltatásidentitások**. Egyes ügyfelek új szolgáltatásidentitások hozzáadása, és minden egyes új felügyeltszolgáltatás-identitás megadása **küldése**, **figyelésére**, és **kezelés** egy adott entitáshoz tartozó engedélyeket.

3.  **Az összetett szabályok egyéni szolgáltatásidentitások**. Nagyon kevés ügyfélnél összetett szabály melyik külsőleg kiállított jogkivonatok Relay jogosultságai vannak leképezve, vagy ha egy egyetlen felügyeltszolgáltatás-identitás van hozzárendelve a csoportok különböző több névteret útvonalait a több szabály jogosultságai.

Összetett szabálykészletek áttelepítési segítségért forduljon [az Azure-támogatás](https://azure.microsoft.com/support/options/). A két esetben engedélyezze az egyszerű áttelepítést.

### <a name="unchanged-defaults"></a>Változatlan alapértelmezései

Ha az alkalmazás nem változott az ACS alapértelmezett értékeket, akkor is cserélje le az összes [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) használatának egy [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objektumot, és az előre konfigurált névtér használata **RootManageSharedAccessKey** helyett az ACS **tulajdonosa** fiókot. Vegye figyelembe, hogy még az ACS-sel **tulajdonosa** fiók, ez a konfiguráció volt (és továbbra is az) általában nem ajánlott, mert a fiók szabály biztosít a névtér, például engedélyezett számára, hogy törli a teljes felügyeleti szolgáltatóként entitások.

### <a name="simple-rules"></a>Az egyszerű szabályok

Ha az alkalmazás egyéni szolgáltatásidentitások használja az egyszerű szabályok, az áttelepítés akkor havidíjba vannak foglalva, abban az esetben, ahol egy ACS felügyeltszolgáltatás-identitást biztosít egy adott továbbítóhoz hozzáférés-vezérlés lett létrehozva. Ebben a forgatókönyvben a helyzet gyakran az SaaS-stílusú olyan megoldások, egyes Relay-k használatát és a egy bérlői webhely vagy a fiókiroda a felügyeltszolgáltatás-identitás hidat hoz létre, hogy adott helyhez. Ebben az esetben a megfelelő felügyeltszolgáltatás-identitását egy közös hozzáférésű Jogosultságkód szabályhoz, közvetlenül a relay telepíthető át. A szolgáltatásnév-identitás válhat a SAS-szabály nevét, és a identitás kulcs válhat a szabály az SAS-kulcsot. A jogok a SAS-szabály vannak, akkor konfigurált egyenértékű rendre a alkalmazni az ACS a szabály az entitáshoz.

Ezen új és további konfigurációja helyben SAS végez bármely meglévő névtér vagy ACS összevont, és a migrálás ACS távolabbi ezt követően végzi használatával [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) helyett [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). A névtér nem kell az ACS-ből szeretné választani.

### <a name="complex-rules"></a>Összetett szabályok

SAS-szabályok nem jelentenek a fiókok lehet, de nevesített rights társított aláírási kulcsokat. Emiatt a forgatókönyvek, amelyben az alkalmazás számos szolgáltatásidentitások hoz létre, és engedélyezi azokat hozzáférési jogosultságok több entitások, vagy a teljes névtér továbbra is szükséges a jogkivonat kiállítása közvetítő. Szerezhet be ilyen egy közvetítői szerepet betöltő által útmutatást [ügyfélszolgálaton](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>További lépések

Az Azure Relay-hitelesítéssel kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Az Azure Relay-hitelesítés és engedélyezés](relay-authentication-and-authorization.md)
* [Service Bus-hitelesítés közös hozzáférésű jogosultságkódokkal](../service-bus-messaging/service-bus-sas.md)


