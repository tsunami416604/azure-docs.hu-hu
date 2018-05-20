---
title: Ha az Azure AD tárolja az Európai ügyfelek identitásadatok |} Microsoft Docs
description: További információk a Microsoft Azure Active Directory ahol identitás kapcsolatos adatokat tárolja az Európai ügyfelek.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 19dc163dbb6dd296a417f5c313a36c7f7c9e50d7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Ha a Microsoft Azure Active Directory (Azure AD) tárol azonosító adataihoz az Európai ügyfelek
Az Azure AD segít a felhasználói identitások kezelésére, és az eszközintelligencia-vezérelt hozzáférési házirendeket készíthetnek, amelyek a szervezet erőforrások biztonságossá tételére. Azonosító adatok egy helyre, amely rendelkezik a címe alapján a szervezet a szolgáltatásra való előfizetéskor biztosított tárolja. Például ha előfizetett Office 365 vagy Azure. Az azonosító adatok tárolására vonatkozó információ, használhatja a [hol található a található adatokhoz?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) a Microsoft Trust Center szakasza.

Az Azure AD-kapcsolódó Európai azonosító adataihoz az Európai adatközpontjaiban marad, amíg nincsenek az USA adatközpontok jellemzően tárolt öt felhasználóval kapcsolatos attribútumait. Ezek az attribútumok GivenName, a Vezetéknév, a userPrincipalName, a tartomány és a PasswordHash. A PasswordHash attribútum lehet kivételt, és nem tárolja az Egyesült Államokban, ha valaki egy helyszíni, összevont hitelesítési módszereket, hogy leállítja az Azure AD szinkronizálása PasswordHash értékét használja. Ezenkívül nincs néhány működő, a szolgáltatással kapcsolatos adat, amely a normál szükséges az Azure AD művelet, amely az Egyesült Államok tárolja, és nem tartalmaz személyes adatokat.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Az Európai ügyfelek Európai adatközpontok kívül tárolt adatok

Az Azure AD-kapcsolódó Európai identitásadatok, Európai alapú címmel rendelkező szervezeteknek az Európai adatközpontjaiban marad. Az Európai adatközpontjaiban nem tárolt az Azure AD-adatokat tartalmazza:

- **Identitás-kapcsolódó attribútumok**

    A következő identitás-kapcsolódó attribútumok replikálja az Amerikai Egyesült Államokban:

    - givenName
    - vezetéknév;
    - userPrincipalName
    - Tartomány
    - PasswordHash
    - sourceAnchor
    - AccountEnabled
    - passwordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID AZONOSÍTÓJA

- **A Microsoft Azure többtényezős hitelesítés (MFA) és az Azure AD az önkiszolgáló jelszó-változtatási (SSPR)**
    
    Többtényezős hitelesítés minden felhasználói adatokat nyugalmi az Európai adatközpontjaiban tárolja. Azonban néhány MFA szolgáltatással kapcsolatos adatokat tárolja az Egyesült Államok, beleértve:
    
    - Kéttényezős hitelesítés és a hozzá kapcsolódó személyes adatokat tárolódhat az Egyesült Államok többtényezős Hitelesítést vagy az önkiszolgáló jelszó-Változtatási használata.
        - Előfordulhat, hogy minden kéttényezős hitelesítéssel telefonhívást vagy SMS végrehajtania USA üzemeltető szolgáltatók.
        - Leküldéses értesítések a Microsoft Authenticator alkalmazással értesítést kapnak a gyártó értesítési szolgáltatása (Apple vagy a Google), Európa kívül esetleg szükséges.
        - OATH-kódokat mindig az USA érvényesítése 
    - Néhány többtényezős Hitelesítést, önkiszolgáló jelszó-Változtatási naplókat az Egyesült Államok függetlenül hitelesítési típus 30 napig tárolja.

- **A Microsoft Azure Active Directory B2C (az Azure AD B2C)**

    Az Azure AD B2C-tároló összes felhasználói adatot nyugalmi Európai adatközpontokban. (A személyes adatok eltávolítása) műveleti naplókat azonban a hely, ahol a személy fér hozzá a szolgáltatások maradjon. Például ha egy B2C-felhasználó hozzáfér a szolgáltatáshoz az Egyesült Államokban, a műveleti naplókat marad az Egyesült Államokban Emellett minden házirend konfigurációs személyes adatot nem tartalmazó adatai csak az Egyesült Államok Házirend-beállításokkal kapcsolatos további információkért tekintse meg a [Azure Active Directory B2C: beépített házirendek](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies) cikk.

- **A Microsoft Azure Active Directory B2B (az Azure AD B2B)** 
    
    Az Azure AD B2B tároló összes felhasználói adatot nyugalmi Európai adatközpontokban. Azonban B2B belül USA adatközpontok táblában tárolja a nem személyes metaadatait. Ez a táblázat például redeemUrl, invitationTicket, erőforrás bérlői azonosító, InviteRedirectUrl és InviterAppId mezőket tartalmazza.

- **A Microsoft Azure Active Directory tartományi szolgáltatások (az Azure AD DS)**

    Az Azure Active Directory tartományi szolgáltatások felhasználói adatok és az ügyfél által megadott Azure virtuális hálózat ugyanazon a helyen tárolja. Igen, ha a hálózaton kívül Európa, az adatok replikálása és Európa kívül tárolt.

- **Szolgáltatások és alkalmazások integrálása az Azure AD**

    Minden olyan szolgáltatásokat és alkalmazásokat, amelyekbe beépül az Azure AD van az azonosító adatok eléréséhez. Értékelje ki minden egyes szolgáltatást és alkalmazást hogyan identitásadatok dolgoz fel, hogy adott szolgáltatást és alkalmazást, és hogy megfelelnek-e a vállalat adattárolási követelmények meghatározásához.

    Microsoft-szolgáltatások adatok rezidens kapcsolatos további információkért tekintse meg a [hol található a található adatokhoz?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) a Microsoft Trust Center szakasza.

## <a name="next-steps"></a>További lépések
A szolgáltatások és funkciók a fent leírt kapcsolatos további információkért lásd: ezek a cikkek.
- [Ismerkedés az Azure Active Directoryban](get-started-azure-ad.md)
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Az Azure AD az önkiszolgáló jelszóváltoztatás](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [Mi az Azure Active Directory B2C?](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [Mi az az Azure AD B2B együttműködés?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Az Azure Active Directory (AD) tartományi szolgáltatásokban](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
