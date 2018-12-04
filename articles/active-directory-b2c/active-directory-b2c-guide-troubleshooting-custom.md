---
title: Az Azure Active Directory B2C egyéni szabályzatokkal kapcsolatos problémák elhárítása |} A Microsoft Docs
description: Ismerje meg megközelítések a hibák megoldása, az Azure Active Directory B2C-vel egyéni szabályzatok használatakor.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8bb95ae776c329e67e9f9936213a9f4c2a0c8f62
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842594"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Az Azure AD B2C-vel egyéni szabályzatok és az identitás-kezelőfelületi keretrendszer hibaelhárítása

Ha használja az Azure Active Directory B2C-vel egyéni szabályzatok (Azure AD B2C-vel), Ön is szembesülhet kihívásokat állít be az identitás-élmény keretrendszer a szabályzat nyelvi XML formátumban.  Egyéni szabályzatok írásának tanulása lehet például egy új nyelv tanulási. Ebben a cikkben leírjuk, hogy eszközök és tippek, amelyek segítségével gyorsan felderítése, és problémák megoldására. 

> [!NOTE]
> Ez a cikk foglalkozik az Azure AD B2C-vel egyéni házirend-konfigurációjának hibaelhárítása. Azt nem oldja meg a függő gyártótól származó alkalmazás vagy az identitás-könyvtár.

## <a name="xml-editing"></a>XML-szerkesztő

Egyéni szabályzatok beállításához a leggyakrabban előforduló hiba: nem megfelelően formázott XML. A megfelelő XML-szerkesztő szinte elengedhetetlen. A megfelelő XML-szerkesztő natív módon jeleníti meg az XML, tartalom Tartománykereső, gyakori kifejezések prefills, tartja az XML-elemek indexelve és sémával ellenőrizheti. Az alábbiakban két a kedvenc XML-szerkesztők:

* [Visual Studio Code](https://code.visualstudio.com/)
* [A Jegyzettömb ++](https://notepad-plus-plus.org/)

XML-sémaérvényesítése azonosítja a hibákat, mielőtt, töltse fel az XML-fájl. A gyökérmappában található, az alapszintű csomag az XML-séma definíció TrustFrameworkPolicy_0.3.0.0.xsd beolvasása. További információ a dokumentációban, az XML-szerkesztőben keresse meg *XML-eszközök* és *XML-érvényesítés*.

Hasznos lehet az XML-szabályok áttekintése. Az Azure AD B2C formázási hibákat észlel, XML-elutasítja. Néha előfordul helytelen formátumú XML, előfordulhat, hogy a rendszer félrevezető hibaüzeneteket.

## <a name="upload-policies-and-policy-validation"></a>Töltse fel a házirendek és házirend-érvényesítés

 XML-fájl feltöltése érvényesítés megtörténik. A legtöbb hiba miatt sikertelen lesz. Érvényesítési tartalmazza a szabályzat fájlt tölt fel. A feltöltendő fájl hivatkozik (a függő entitás házirend fájlt, a bővítmények fájlt és az alap fájl)-fájlok a lánc is tartalmaz. 
 
 Általános érvényesítési hibák a következők:

Kódrészlet. hiba: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* A ClaimType érték lehet, hogy rosszul írta be, vagy a séma nem létezik.
* Takar értékeket kell definiálni legalább egy fájlt a házirendben. 
    Például:` <ClaimType Id="socialIdpUserId">`
* Ha takar a bővítmények fájlban van definiálva, de az alapszintű fájlban TechnicalProfile értékét is szolgál, az alap-fájl feltöltése hibát eredményez.

Kódrészlet. hiba: `...makes a reference to a ClaimsTransformation with id...`
* A hiba oka lehet ugyanaz, mint a ClaimType hiba.

Kódrészlet. hiba: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Ellenőrizze, hogy a Bérlőazonosító értékét a **\<TrustFrameworkPolicy\>** és **\<BasePolicy\>** elemek egyeznie a cél Azure AD B2C-bérlő.  

## <a name="troubleshoot-the-runtime"></a>A futtatókörnyezet hibaelhárítása

* Használat `Run Now` és `https://jwt.io` be webes vagy mobilalkalmazásaiba függetlenül a házirendek tesztelésére. A webhely úgy viselkedik, mint egy függő fél alkalmazást. Megjeleníti a tartalmát, a JSON webes jogkivonat (JWT) az Azure AD B2C-szabályzat által létrehozott. Létrehoz egy tesztelési kérelem az identitás-kezelőfelületi keretrendszer, használja a következő értékeket:
    * Name: TestApp
    * Webalkalmazást vagy webes API: nem
    * Natív ügyfél: nem

* Az exchange, az ügyfél böngészője és az Azure AD B2C-vel közötti üzenetek nyomon követése, használja a [Fiddler](https://www.telerik.com/fiddler). Ez segíthet álló, ahol a felhasználói interakciósorozat nem működik, a vezénylési lépésekben.

* A **fejlesztői mód**, használjon **Application Insights** az identitás-kezelőfelületi keretrendszer felhasználói interakciósorozat tevékenységét nyomon követéséhez. A **fejlesztői mód**, figyelheti, hogy az exchange között az identitás-kezelőfelületi keretrendszer és a különböző jogcímszolgáltatóktól technikai profilok, identitás-szolgáltatóktól, API-alapú szolgáltatások, például meghatározott jogcímek a Az Azure AD B2C felhasználói címtár- és egyéb szolgáltatások, például az Azure több többtényezős hitelesítés.  

## <a name="recommended-practices"></a>Ajánlott eljárások

**Tartsa meg az Ön forgatókönyvei több verzióját. A projekt az alkalmazással csoportosíthatja őket.** Az alap, bővítmények és függő fél fájlok is közvetlenül függ egymástól. Mentse őket egy csoportként. Új funkciók hozzáadásakor a szabályzatokhoz, folyamatosan működő külön verziók. Fázis működő verziók a saját fájlrendszer az alkalmazás kódjának kommunikálnak.  Az alkalmazások számos különböző függő fél szabályzatok a bérlő előfordulhat, hogy meghívása. A jogcímek, az Azure AD B2C-szabályzatok az általuk elvárt függő válhatnak.

**Fejlessze és tesztelje az ismert felhasználói utak technikai profilok.** Házirendek használata a tesztelt alapszintű csomag a technikai profilok beállítása. Ezeket külön-külön előtt tesztelje a saját felhasználói utak beépítheti azokat.

**Fejlessze és tesztelje a tesztelt, technikai profillal rendelkező felhasználói utak.** A vezénylési lépésekből egy felhasználói interakciósorozatában szereplő fokozatosan módosíthatja. Az importálni kívánt forgatókönyvek fokozatos felépítésére.

## <a name="next-steps"></a>További lépések

* A Githubban, töltse le a [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip-fájlt.
