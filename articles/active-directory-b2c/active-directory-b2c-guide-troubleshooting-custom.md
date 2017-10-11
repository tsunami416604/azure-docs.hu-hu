---
title: "Az Azure Active Directory B2C: Egyéni szabályzatokkal kapcsolatos problémák elhárítása |} Microsoft Docs"
description: "További információk a megközelítések hibák elhárításához, amikor egyéni házirendek használata az Azure Active Directoryban."
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.openlocfilehash: 023390ba36a74217503ff8b3076ad17978fe3fb8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Egyéni házirendek az Azure AD B2C és identitás élmény keretrendszer hibaelhárítása

Ha az Azure Active Directory B2C használata egyéni házirendeket (az Azure AD B2C), Ön is szembesülhet kihívást állítja be a házirend language XML formátumban identitás élmény keretében.  Egyéni házirendek írni tanulási lehet például egy új nyelvi tanulási. Ebben a cikkben azt ismertetik, eszközök és tippek, amelyek segítségével gyorsan felderítése és problémák megoldására. 

> [!NOTE]
> Ez a cikk foglalkozik hibaelhárítása az Azure AD B2C egyéni házirend-konfigurációt. Azt nem hárítsa el a függő entitás alkalmazás vagy az identitás-könyvtárban.

## <a name="xml-editing"></a>XML-szerkesztése

Egyéni házirendek beállításának a leggyakrabban előforduló hiba: nem megfelelően formázott XML. Majdnem elengedhetetlen a helyes XML-szerkesztő. A megfelelő XML-szerkesztő meg XML natív módon, tartalom Tartománykereső, közös feltételek prefills, XML-elemek indexelt tartja, valamint ellenőrizhesse a séma. Az alábbiakban a kedvenc XML-szerkesztő két:

* [Visual Studio Code](https://code.visualstudio.com/)
* [A Jegyzettömb ++](https://notepad-plus-plus.org/)

XML-sémaérvényesítése észleli a hibákat, mielőtt feltölti az XML-fájl. Az alapszintű csomag gyökérmappájában az XML-schema definíció TrustFrameworkPolicy_0.3.0.0.xsd beolvasása. További információ a dokumentáció az XML-szerkesztőben keresse meg *XML-eszközök* és *XML-érvényesítés*.

Bizonyára hasznosnak találja az XML-szabályok áttekintése hasznos. Az Azure AD B2C formázási hibákat észlel, XML-elutasítja. Alkalmanként helytelen formátumú XML okozhat, amelyek félrevezető hibaüzeneteket.

## <a name="upload-policies-and-policy-validation"></a>Házirendek és házirend érvényesítésének feltöltése

 XML-fájl feltöltése érvényesítés automatikus. A legtöbb hiba miatt sikertelen feltöltés. Érvényesítési a feltölteni kívánt házirendfájlt tartalmazza. A feltöltendő fájl hivatkozik (a függő entitás házirendfájl a bővítmények fájl és az alap fájl) fájlok lánc is tartalmaz. 
 
 Gyakori ellenőrzési hibák a következők:

Hiba kódtöredékre:`... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* A ClaimType érték lehet, hogy hibásan írta be, vagy nem létezik a sémában.
* Legalább egy fájlt a házirend a ClaimType értékeket kell definiálni. 
    Például:` <ClaimType Id="socialIdpUserId">`
* ClaimType a bővítmények fájlban van definiálva, de is szerepel, az alap fájlban TechnicalProfile értéket, ha az alap fájl feltöltése hibát eredményez.

Hiba kódtöredékre:`...makes a reference to a ClaimsTransformation with id...`
* A hiba oka lehet, hogy meg kell egyeznie a ClaimType: hiba.

Hiba kódtöredékre:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Ellenőrizze, hogy a tenantid-azonosító értékét a  **\<TrustFrameworkPolicy\>**  és  **\<BasePolicy\>**  elemek felel meg a cél az Azure AD B2C-bérlő.  

## <a name="troubleshoot-the-runtime"></a>A futtatókörnyezet hibaelhárítása

* Használjon `Run Now` és `https://jwt.io` segítségével tesztelheti azokat a web- vagy mobilalkalmazás függetlenül. A webhely úgy viselkedik, mint egy függő entitás alkalmazást. A tartalom az a JSON webes jogkivonat (JWT) az Azure AD B2C-házirend által létrehozott jeleníti meg. Teszt-alkalmazás létrehozásának identitás élmény keretében, a következő értékeket használja:
    * Name: TestApp
    * Webalkalmazást vagy webes API: nincs
    * Natív ügyfél: nincs

* Az ügyfél böngészője és az Azure AD B2C között az üzenetváltás követéséhez használja [Fiddler](http://www.telerik.com/fiddler). Ez segítheti utalhat, hogy ha a felhasználó használatában a vezénylési lépésekből a sikertelen.

* A **fejlesztői mód**, használjon **Application Insights** a identitás élmény keretrendszer felhasználói használatában tevékenységét nyomon követéséhez. A **fejlesztői mód**, figyelheti az exchange között a különböző jogcímszolgáltatóktól, amelyeket műszaki profilokhoz, például a Identitásszolgáltatók, API-alapú szolgáltatások, a felhasználó Azure AD B2C címtár és egyéb szolgáltatások, például Azure több-Factor-hitelesítés és identitás élmény keretében jogcímek.  

## <a name="recommended-practices"></a>Ajánlott eljárások

**Tartsa meg az esetek több verziója. Az alkalmazás a projekthez csoportosításához.** Az alap, a fájlnévkiterjesztéseket, valamint a függő entitás fájlok közvetlenül egymástól függenek. Csoportként mentse őket. Új szolgáltatások jelentek meg a szabályzatokkal, figyeljen külön működő verziók. Szakasz működő verziók saját fájlrendszer léphetnek kapcsolatba az alkalmazás kódját.  Az alkalmazások számos különböző függő entitás házirendek bérlőjében előfordulhat, hogy meghívni. A jogcímeket, amely azokat az Azure AD B2C-házirendek várható függő válhatnak.

**Kialakításához, és műszaki profilokat az ismert felhasználói utak teszteléséhez.** Tesztelt alapszintű csomag házirendek segítségével a műszaki profilok beállítása. Ezeket külön-külön előtt tesztelje a saját felhasználói utak beépítse őket.

**Kialakításához, és a felhasználó utak tesztelt műszaki profilok teszteléséhez.** A vezénylési lépésekből egy felhasználó út Növekményesen módosítása. A tervezett forgatókönyvek fokozatosan felépítéséhez.

## <a name="next-steps"></a>Következő lépések

* A Githubon töltse le a [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip fájlt.
