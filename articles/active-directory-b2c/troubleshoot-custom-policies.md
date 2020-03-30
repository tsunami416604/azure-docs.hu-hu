---
title: Egyéni házirendek – egyéni házirendek – problémamegoldás az Azure Active Directory B2C-ben
description: Ismerje meg a hibák megoldásának módszereit, amikor egyéni szabályzatokkal dolgozik az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f65e98cec04991fe9edef1b81bcb3ecc3d93d76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186360"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Az Azure AD B2C egyéni szabályzatok és az identitáskezelési élmény keretrendszerének hibaelhárítása

Ha az Azure Active Directory B2C (Azure AD B2C) egyéni szabályzatok, előfordulhat, hogy a kihívások beállítása az identitásélmény-keretrendszer a házirend nyelve XML formátumban. Az egyéni házirendek írásának elsajátítása olyan lehet, mint egy új nyelv tanulása. Ebben a cikkben ismertetünk néhány olyan eszközt és tippet, amelyek segíthetnek a problémák felderítésében és megoldásában.

Ez a cikk az Azure AD B2C egyéni házirend-konfigurációjának hibaelhárítására összpontosít. Nem foglalkozik a függő entitás alkalmazásvagy identitáskönyvtár.

## <a name="xml-editing"></a>XML-szerkesztés

Az egyéni házirendek beállításának leggyakoribb hibája a nem megfelelően formázott XML. Egy jó XML-szerkesztő szinte elengedhetetlen. Megjeleníti az XML natív módon, színkódok tartalmát, előre kitölti a közös kifejezéseket, indexelten tartja az XML-elemeket, és érvényesítheti az XML-sémával szemben.

Két kedvenc szerkesztők [Visual Studio kód](https://code.visualstudio.com/) és [Jegyzettömb ++](https://notepad-plus-plus.org/).

Az XML-séma-érvényesítés azonosítja a hibákat az XML-fájl feltöltése előtt. Az [indítócsomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)gyökérmappájában az *TRUSTFRAMEWORKPOLICY_0.3.0.0.xsd*XML-sémadefiníciós fájl lekérte. Ha meg szeretné tudni, hogyan használhatja az XSD sémafájlt a szerkesztőben való érvényesítésre, keresse meg az *XML-eszközöket* és *az XML-érvényesítést* vagy hasonlót a szerkesztő dokumentációjában.

Hasznoslehet az XML-szabályok áttekintése. Az Azure AD B2C elutasítja az általa észlelt XML-formázási hibákat. Esetenként a helytelenül formázott XML félrevezető hibaüzeneteket okozhat.

## <a name="upload-policies-and-policy-validation"></a>Feltöltési irányelvek és házirend-érvényesítés

Az XML-házirendfájl érvényesítése feltöltéskor automatikusan történik. A legtöbb hiba a feltöltés sikertelensét okozza. Az érvényesítés magában foglalja a feltöltendő házirendfájlt. Tartalmazza a feltöltési fájláltal hivatkozott fájlok láncolatát is (a függő entitás házirendfájlját, a kiterjesztésfájlt és az alapfájlt).

A gyakori érvényesítési hibák közé tartoznak a következők:

> Hibakódrészlet:`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Lehet, hogy a ClaimType érték hibásan van beírva, vagy nem létezik a sémában.
* A ClaimType-értékeket a házirend legalább egy fájljában meg kell határozni.
    Például:`<ClaimType Id="issuerUserId">`
* Ha a ClaimType definiálva van a bővítőfájlban, de az alapfájltechnicalProfile értékében is használatos, az alapfájl feltöltése hibát eredményez.

> Hibakódrészlet:`...makes a reference to a ClaimsTransformation with id...`

* A hiba okai megegyezhetnek a ClaimType-hibával.

> Hibakódrészlet:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Ellenőrizze, hogy a TenantId-érték a `<TrustFrameworkPolicy\>` és `<BasePolicy\>` az elemek megfelelnek a cél Az Azure AD B2C bérlő.

## <a name="troubleshoot-the-runtime"></a>A futásidejű probléma elhárítása

* Használja a `https://jwt.ms` Futtatás **most** alkalmazást, és tesztelje a szabályzatokat a webtől vagy a mobilalkalmazástól függetlenül. Ez a weboldal úgy működik, mint egy függő fél alkalmazás. Az Azure AD B2C-szabályzat által létrehozott JSON-webtoken (JWT) tartalmát jeleníti meg.

    Hozzon létre egy tesztalkalmazást, `https://jwt.ms` amely átirányítható a jogkivonat-ellenőrzéshez:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Az ügyfélböngésző és az Azure AD B2C közötti üzenetváltás nyomon követéséhez használja a [Fiddler](https://www.telerik.com/fiddler)alkalmazást. Ez segíthet annak jelzését, ahol a felhasználói út sikertelen a vezénylési lépésekben.

* **A fejlesztési módban**az Application [Insights](troubleshoot-with-application-insights.md) segítségével nyomon követheti az Identitáskezelési keretrendszer felhasználói utazásának tevékenységét. **Fejlesztési módban**megfigyelheti a jogcímek cseréjét az identitásélmény-keretrendszer és a technikai profilok által meghatározott különböző jogcímszolgáltatók, például az identitásszolgáltatók, az API-alapú szolgáltatások, az Azure AD B2C felhasználói címtár és más szolgáltatások, például az Azure többtényezős hitelesítés e között.

## <a name="recommended-practices"></a>Ajánlott eljárások

**A forgatókönyvek több verzióját is megtarthatja. Csoportosítsa őket egy projektbe az alkalmazással.** Az alap, a bővítmények és a függő entitás fájlok közvetlenül függenek egymástól. Mentsd őket csoportként. Ahogy új funkciókkal egészül ki a szabályzatok, tartsa külön működő verziók. Szakaszos verziók a saját fájlrendszer az alkalmazás kódot, hogy kölcsönhatásba lépnek. Előfordulhat, hogy az alkalmazások számos különböző függő entitás házirendek egy bérlőben. Függővé válhatnak az Azure AD B2C-szabályzatok által elvárt jogcímektől.

**Technikai profilok kidolgozása és tesztelése ismert felhasználói utazásokkal.** A műszaki profilok beállításához használja a tesztelt kezdőcsomag-házirendeket. Tesztelje őket külön-külön, mielőtt beépítené őket a saját felhasználói utazásaiba.

**A felhasználói utak fejlesztése és tesztelése tesztelt műszaki profilokkal.** A felhasználói út vezénylési lépéseinek növekményes módosítása. Fokozatosan építsd fel a tervezett forgatókönyveket.

## <a name="next-steps"></a>További lépések

A GitHubon elérhető, töltse le az [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip archívumot. A tárházat klónozhatja is:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
