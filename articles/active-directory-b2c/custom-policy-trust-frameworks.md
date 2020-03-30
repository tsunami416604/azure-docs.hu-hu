---
title: Referencia – megbízhatósági keretrendszerek az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: Az Azure Active Directory B2C egyéni szabályzatokkal és az identitáskezelési keretrendszerrel kapcsolatos témakör.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ffa25eec9c4b668f428d8e8b5a780a5fe4625a2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188885"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Megbízhatósági keretrendszerek definiálása az Azure AD B2C identitáskezelési keretrendszerrel

Az Azure Active Directory B2C (Azure AD B2C) egyéni szabályzatok, amelyek az identitásélmény-keretrendszer biztosítja a szervezet egy központi szolgáltatás. Ez a szolgáltatás csökkenti az identitás-összevonás összetettségét egy nagy érdeklődési közösségben. Az összetettség egyetlen bizalmi kapcsolatra és egyetlen metaadat-cserére csökken.

Az Azure AD B2C egyéni szabályzatok az identitáskezelési élménykeretrendszer használatával teszile, hogy válaszoljon a következő kérdésekre:

- Melyek azok a jogi, biztonsági, adatvédelmi és adatvédelmi irányelvek, amelyeket be kell tartani?
- Kik a kapcsolattartók és milyen folyamatokkal lehet akkreditált résztvevővé válni?
- Kik az akkreditált személyazonossági információszolgáltatók (más néven "jogcímszolgáltatók"), és mit kínálnak?
- Kik az akkreditált függő felek (és adott esetben mire van szükségük)?
- Melyek a résztvevőkre vonatkozó technikai "a vezetéken" együttműködési követelmények?
- Melyek azok az operatív "futásidejű" szabályok, amelyeket be kell tartatni a digitális azonosítóadatok cseréjekor?

Ezeknek a kérdéseknek a megválaszolására az Azure AD B2C egyéni szabályzatok, amelyek az identitásélmény-keretrendszer a megbízhatósági keretrendszer (TF) konstrukció. Nézzük meg ezt a konstrukciót, és mit nyújt.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>A megbízhatósági keretrendszer és az összevonási felügyeleti alapítvány megismerése

A megbízhatósági keretrendszer azon identitási, biztonsági, adatvédelmi és adatvédelmi irányelvek írásos specifikációja, amelyeknek az érdeklődési közösség résztvevőinek meg kell felelniük.

Az összevont identitás alapul a végfelhasználói identitásbiztonság eléréséhez internetes méretekben. Az identitáskezelés harmadik felekre történő delegálásával a végfelhasználó egyetlen digitális identitása több függő féllel is felhasználható.

Az identitásbiztosítás megköveteli, hogy az identitásszolgáltatók (IdPs) és az attribútumszolgáltatók (AtPs) tartsák be az adott biztonsági, adatvédelmi és működési szabályzatokat és gyakorlatokat.  Ha nem tudnak közvetlen ellenőrzéseket végezni, a függő entitásoknak bizalmi kapcsolatokat kell kialakítaniuk az általuk választott idp-kkel és atp-kkel.

A hogy a fogyasztók és a digitális identitásadatok szolgáltatóinak száma nő, nehéz folytatni a bizalmi kapcsolatok páros kezelését, vagy akár a hálózati kapcsolathoz szükséges technikai metaadatok páros cseréjét.  Az összevonási központok csak korlátozott sikerrel oldották meg ezeket a problémákat.

### <a name="what-a-trust-framework-specification-defines"></a>Mit határoz meg a megbízhatósági keretrendszer specifikációja?
A TF-ek az Open Identity Exchange (OIX) megbízhatósági keretrendszer modelljének a kapocsai, ahol minden érdekközösséget egy adott TF-specifikáció szabályoz. Az ilyen TF specifikáció a következőket határozza meg:

- **Az érdeklődésre számot tartó közösség biztonsági és adatvédelmi mutatói a következők meghatározásával:**
    - A résztvevők által kínált/megkövetelt megbízhatósági szintek; például a digitális azonosító adatok hitelességére vonatkozó megbízhatósági minősítések rendezett készlete.
    - A résztvevők által kínált/megkövetelt védelmi szintek (LOP); például a digitális identitásadatok védelmére vonatkozó, az érdeklődésre számot tartó közösség résztvevői által kezelt megbízhatósági minősítések rendezett készlete.

- **A résztvevők által felajánlott/igényelt digitális azonosító adatok leírása.**

- **A digitális azonosító adatok előállítására és felhasználására, és így a LOA és a LOP mérésére vonatkozó műszaki politikák. Ezek az írásbeli házirendek általában a következő házirend-kategóriákat tartalmazzák:**
    - Személyazonosság-ellenőrzésházirendek, például: *Milyen erősen ellenőrzik egy személy személyazonossági adatait?*
    - Biztonsági házirendek, például: *Mennyire védik az információk integritását és titkosságát?*
    - Adatvédelmi irányelvek, például: *Milyen ellenőrzéssel rendelkezik egy felhasználó a személyes azonosításra alkalmas adatok (PII) felett?*
    - Túlélési szabályzatok, például: *Ha egy szolgáltató beszünteti működését, hogyan működik a menetképes végzettel és a személyazonosításra alkalmas személyekkel kapcsolatos* folytonosság és védelem?

- **A digitális személyazonosító adatok előállításához és felhasználásához szükséges műszaki profilok. Ezek a profilok a következők:**
    - Olyan hatókör-illesztések, amelyekhez digitális azonosító adatok állnak rendelkezésre egy adott loa-ban.
    - A vezetéken átnyúló átjárhatóságra vonatkozó műszaki követelmények.

- **A közösség résztvevői által elvégezhető különböző szerepkörök leírása, valamint az e szerepkörök teljesítéséhez szükséges képesítések.**

Így a TF specifikáció szabályozza az identitásinformációk cseréjét az érdekközösség résztvevői között: a függő felek, az identitás- és attribútumszolgáltatók, valamint az attribútumhitelesítők között.

A TF specifikáció egy vagy több dokumentum, amely referenciaként szolgál az érdekközösség irányításához, amely szabályozza a digitális azonosító adatok közösségen belüli érvényesítését és felhasználását. Ez egy dokumentált szabályzatok és eljárások célja, hogy bizalmat teremtsen a digitális identitások, amelyek et használnak az online tranzakciók tagjai között a közösség érdeke.

Más szóval a TF-specifikáció határozza meg az életképes összevont identitás-ökoszisztéma létrehozásának szabályait a közösség számára.

Jelenleg széles körű egyetértés van egy ilyen megközelítés előnyeiről. Nem kétséges, hogy a bizalmi keretelőírások igazolható biztonsági, biztonsági és adatvédelmi jellemzőkkel segítik elő a digitális identitásökoszisztémák fejlesztését, ami azt jelenti, hogy több érdekelt közösségben is felhasználhatók.

Ebből az okból az Azure AD B2C egyéni szabályzatok, amelyek az identitásélmény-keretrendszer használja a specifikáció, mint az alapja a tf a tf az együttműködés megkönnyítése érdekében.

Az Azure AD B2C egyéni szabályzatok, amelyek kihasználják az identitásélmény-keretrendszer képviseli a TF-specifikáció, mint az emberi és a gép által olvasható adatok keveréke. A modell egyes szakaszai (általában a vállalatirányítás felé irányuló szakaszok) a közzétett biztonsági és adatvédelmi szabályzat dokumentációjára való hivatkozásként jelennek meg a kapcsolódó eljárásokkal együtt (ha vannak ilyenek). Más szakaszok részletesen ismertetik a konfigurációs metaadatokat és a futásidejű szabályokat, amelyek megkönnyítik a működési automatizálást.

## <a name="understand-trust-framework-policies"></a>A megbízhatósági keretházirendek ismertetése

A megvalósítás tekintetében a TF specifikáció olyan házirendekből áll, amelyek lehetővé teszik az identitásviselkedés és -élmény teljes körű vezérlését.  Az Azure AD B2C egyéni szabályzatok, amelyek kihasználják az identitáskezelési élmény keretrendszer lehetővé teszi, hogy a saját TF az ilyen deklaratív szabályzatok, amelyek határozzák meg és konfigurálja:

- A dokumentum hivatkozás vagy hivatkozások, amelyek meghatározzák az összevont identitás ökoszisztéma a közösség, amely kapcsolódik a TF. Ezek a tf dokumentációra mutató hivatkozások. Az (előre meghatározott) működési "futásidejű" szabályok, vagy a felhasználói utak, amelyek automatizálják és/vagy szabályozzák a jogcímek cseréjét és használatát. Ezek a felhasználói utak loa(és LOP) kapcsolatban állnak. A házirendek ezért különböző LOA-kkal (és LOP-kkal) rendelkező felhasználói utazásokkal rendelkezhetnek.

- Az identitás- és attribútumszolgáltatók, vagy a jogcímszolgáltatók az érdeklődési közösségben és az általuk támogatott technikai profilok, valamint a rájuk vonatkozó (sávon kívüli) LOA/LOP akkreditáció.

- Az attribútumhitelesítőkkel vagy jogcímszolgáltatókkal való integráció.

- A függő felek a közösségben (következtetés szerint).

- A résztvevők közötti hálózati kommunikáció létrehozásához szolgáló metaadatok. Ez a metaadatok a technikai profilokkal együtt a tranzakció során a függő entitás és a közösség más résztvevői közötti "vezetéken" való átjárhatóság átjárására szolgálnak.

- A protokoll konvertálása, ha van ilyen (például SAML 2.0, OAuth2, WS-Federation és OpenID Connect).

- A hitelesítési követelmények.

- A többtényezős vezénylés, ha van ilyen.

- Megosztott séma az összes elérhető jogcímhez, és leképezés egy érdeklődési közösség résztvevőihez.

- Az összes jogcímátalakítások, valamint a lehetséges adatok minimalizálása ebben az összefüggésben, a jogcímek cseréjének és használatának fenntartása érdekében.

- A kötés és a titkosítás.

- A kárrendezés.

### <a name="understand-claims"></a>A követelések megértése

> [!NOTE]
> Együttesen hivatkozunk a kicserélhető összes lehetséges típusú identitásinformációra, mint "jogcímek": a végfelhasználó hitelesítési hitelesítő adataival, identitásellenőrzésével, kommunikációs eszközével, fizikai helyével, személyes azonosításra alkalmas attribútumaira vonatkozó jogcímek, és így tovább.
>
> A "claims" kifejezést használjuk - az "attribútumok" helyett -, mert az online tranzakciókban ezek az adatösszetevők nem olyan tények, amelyeket a függő entitás közvetlenül ellenőrizhet. Inkább olyan tényekkel kapcsolatos állítások, vagy állítások, amelyekhez a függő entitásnak elegendő bizalmat kell kifejlesztenie ahhoz, hogy megadja a végfelhasználó kért tranzakcióját.
>
> A "jogcímek" kifejezést is használjuk, mivel az Azure AD B2C egyéni szabályzatok, amelyek az Identitásélmény-keretrendszert használják, az összes típusú digitális identitásinformáció konzisztens módon történő cseréjének egyszerűsítésére szolgálnak, függetlenül attól, hogy az alapul szolgáló protokoll felhasználói hitelesítéshez vagy attribútumlekéréshez van definiálva.  Hasonlóképpen a "jogcímszolgáltatók" kifejezést használjuk az identitásszolgáltatókra, az attribútumszolgáltatókra és az attribútum-hitelesítőkre, ha nem akarunk különbséget tenni a konkrét funkciók között.

Így ezek szabályozzák az identitásadatok cseréjének módját a függő entitás, az identitás- és attribútumszolgáltatók, valamint az attribútumhitelesítők között. Ők szabályozzák, hogy mely identitás- és attribútumszolgáltatók szükségesek a függő entitás hitelesítéséhez. Ezeket tartományspecifikus nyelvnek (DSL) kell tekinteni, azaz olyan számítógépes nyelvnek, amely öröklődő alkalmazástartományra specializálódott, *ha* utasítások, polimorfizmus.

Ezek a szabályzatok a TF-konstrukció géppel olvasható részét képezik az Azure AD B2C egyéni szabályzatokban, amelyek az identitáskezelési keretrendszert használják. Ezek tartalmazzák az összes működési részletet, beleértve a jogcímszolgáltatók metaadatait és technikai profiljait, a jogcímséma-definíciókat, a jogcímátalakítási függvényeket és a felhasználói műveleteket, amelyek az operatív vezénylés és automatizálás megkönnyítése érdekében ki vannak töltve.

Ezek feltételezhetően *élő dokumentumok,* mert jó esély van arra, hogy tartalmuk idővel megváltozik a politikákban bejelentett aktív résztvevők tekintetében. Lehetőség van arra is, hogy a résztvevőség feltételei megváltoznak.

Az összevonás beállítása és karbantartása nagymértékben leegyszerűsödik azáltal, hogy megvédi a függő entitásokat a folyamatos megbízhatósági és kapcsolat-újrakonfigurálástól, mivel a különböző jogcímszolgáltatók/hitelesítők csatlakoznak vagy kilépnek (a közösség által képviselt) házirendek készletét.

Az interoperabilitás egy másik jelentős kihívás. A további jogcímszolgáltatókat/hitelesítőket integrálni kell, mivel a függő entitások valószínűleg nem támogatják az összes szükséges protokollt. Az Azure AD B2C egyéni szabályzatok megoldani ezt a problémát azáltal, hogy támogatja az iparági szabványnak megfelelő protokollok és alkalmazásával konkrét felhasználói utazások a kérelmek átültetésére, ha a függő felek és attribútumszolgáltatók nem támogatják ugyanazt a protokollt.

A felhasználói utak közé tartoznak a protokollprofilok és a metaadatok, amelyek a függő entitás és a többi résztvevő közötti "vezetéken" való átjárhatóság hozására szolgálnak. Vannak olyan működési futásidejű szabályok is, amelyek az identitásadatok cseréjére vonatkozó kérési/válaszüzenetekre vonatkoznak a tf specifikáció részeként a közzétett szabályzatok betartásának érvényesítése érdekében. A felhasználói utak ötlete kulcsfontosságú az ügyfélélmény testreszabásához. Azt is rávilágít, hogy a rendszer hogyan működik a protokoll szintjén.

Ennek alapján a függő entitásalkalmazások és portálok a környezetüktől függően meghívhatnak Azure AD B2C egyéni szabályzatokat, amelyek kihasználják az identitáskezelési keretrendszert egy adott szabályzat nevének továbbításával, és pontosan megkapják a viselkedést és az információcserét azt akarják, anélkül, hogy muss, felhajtás, vagy a kockázat.
