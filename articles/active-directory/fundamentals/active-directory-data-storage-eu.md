---
title: Az Európai ügyfelei számára – Azure Active Directory Identity adattárolás |} A Microsoft Docs
description: Ismerje meg, az Azure Active Directory tárolja az Európai ügyfeleinek identitással kapcsolatos adatokat.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/17/2018
ms.custom: it-pro, seodec18
ms.openlocfilehash: 371c7b2eb2f2e0e34dc80cf8fe6771620fa76765
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099937"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Az Európai ügyfelek az Azure Active Directory Identity adattárolás
Az Azure Active Directory (Azure AD) segítségével, amely a szervezet erőforrásaihoz biztonságossá intelligens megoldásokkal vezérelt hozzáférési szabályzatokat hozhat létre, így kezelheti a felhasználói identitásokat. Az identitásadatok tárolásának helye a szervezete által a szolgáltatásra történő előfizetéskor megadott cím alapján lesz meghatározva. Azaz amikor például az Office 365-re vagy az Azure-ra előfizetett. Ha pontosan szeretné meghatározni az identitásadatok helyét, tekintse meg a Microsoft biztonsági és adatkezelési központ [az adatok tárolási helyével](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) foglalkozó szakaszát.

Noha a legtöbb Azure AD-vel kapcsolatos európai identitásadat európai adatközpontokban marad, van öt felhasználóval kapcsolatos attribútum, amelyeknek a tárolása jellemzően USA-beli adatközpontokban történik. Ezek az attribútumok a következők: GivenName, Surname, userPrincipalName, Domain és PasswordHash. A PasswordHash attribútum kivételt képez, a rendszer nem tárolja az USA-ban, ha valaki olyan helyszíni, összevont hitelesítési módszert használ, amely megakadályozza, hogy a rendszer szinkronizálja a PasswordHash értékét az Azure AD-vel. Van ezenfelül pár olyan, a normál Azure AD-működéshez szükséges működési, szolgáltatásspecifikus adat, amelyet a rendszer az USA-ban tárol, és nem tartalmaz személyes adatokat.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Európai ügyfelekhez kapcsolódó, de nem európai adatközpontban tárolt adatok

Az európai székhellyel rendelkező szervezetekhez tartozó legtöbb, Azure AD-vel kapcsolatos európai identitásadat európai adatközpontokban marad. Az Azure AD az adatokat, amelyeket az Európai adatközpontokban tárolja, Egyesült államokbeli adatközpontok, emellett replikálva tartalmazza:

- **Identitással kapcsolatos attribútumok**

    Az alábbi identitással kapcsolatos attribútumokat replikálja a rendszer az Egyesült Államokba:

    - GivenName
    - vezetéknév;
    - userPrincipalName
    - Domain
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **A Microsoft Azure Multi-Factor Authentication Service (MFA) és az Azure AD-beli önkiszolgáló jelszómódosítás (SSPR)**
    
    Az MFA európai adatközpontokban tárolja az összes helyi felhasználói adatot. Bizonyos MFA-szolgáltatásspecifikus adatokat azonban a rendszer az USA-ban tárol, például a következőket:
    
    - Ha MFA-t vagy SSPR-t használ, előfordulhat, hogy a kétfaktoros hitelesítést és a hozzá kapcsolódó személyes adatokat a rendszer az USA-ban tárolja.
        - Minden telefonhívással vagy SMS-sel történő kétfaktoros hitelesítésnél előfordulhat, hogy USA-beli szolgáltató jár el.
        - A Microsoft Authenticator alkalmazást használó leküldéses értesítésekhez a gyártó értesítési szolgáltatásából (Apple vagy Google) származó értesítésekre van szükség, és előfordulhat, hogy az Európán kívüli.
        - Az OATH-kódok érvényesítése mindig az USA-ban történik. 
    - Bizonyos MFA- és SSPR-naplókat a rendszer a hitelesítés típusától függetlenül 30 napig az USA-ban tárol.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Az Azure AD B2C európai adatközpontokban tárolja az összes helyi felhasználói adatot. A működési naplók azonban (személyes adatok nélkül) azon a helyen maradnak, ahonnan a személy hozzáfér a szolgáltatásokhoz. Ha például egy B2C-felhasználó az USA-ból fér hozzá a szolgáltatáshoz, a működési naplók az USA-ban maradnak. Az USA-ban történik továbbá az összes személyes adatot nem tartalmazó szabályzatkonfigurációs adat tárolása is. A szabályzatkonfigurációkkal kapcsolatos további információért tekintse meg az [Azure Active Directory B2C beépített szabályzatait](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) ismertető cikket.

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Az Azure AD B2B európai adatközpontokban tárolja az összes helyi felhasználói adatot. A nem személyes metaadatokat azonban a B2B USA-beli adatközpontokban tárolja, táblázatokban. Ez a táblázat olyan mezőket tartalmaz például, mint a redeemUrl, az invitationTicket, a resource tenant Id, az InviteRedirectUrl és az InviterAppId.

- **Microsoft Azure Active Directory tartományi szolgáltatások (Azure AD DS)**

    Az Azure AD DS ugyanott tárolja a felhasználói adatokat, ahol a felhasználó által kiválasztott Azure-beli virtuális hálózat. Tehát ha a hálózat Európán kívül van, a rendszer replikálja az adatokat, és Európán kívül tárolja.

- **Az Azure AD-vel integrált szolgáltatások és alkalmazások**

    Az Azure AD-vel integrált szolgáltatások és alkalmazások hozzáférnek az identitásadatokhoz. Annak meghatározásához, hogy miként dolgozza fel egy adott szolgáltatás és alkalmazás az identitásadatokat, és hogy megfelelnek-e a vállalat adattárolási előírásainak, egyesével kell kiértékelni az egyes szolgáltatásokat és alkalmazásokat.

    A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ [az adatok tárolási helyével](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) foglalkozó szakaszában találhat további információt.

## <a name="next-steps"></a>További lépések
A fent ismertetett szolgáltatásokkal és funkciókkal kapcsolatban az alábbi cikkekben találhat további információt.
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD önkiszolgáló jelszómódosítás](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Mi az az Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Mi az az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
