---
title: Az Európai ügyfelei számára – Azure Active Directory Identity adattárolás |} A Microsoft Docs
description: Ismerje meg, az Azure Active Directory tárolja az Európai ügyfeleinek identitással kapcsolatos adatokat.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33704c3392726ccd12ca0fd74f26465a54697e11
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722146"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Az Európai ügyfelek az Azure Active Directory Identity adattárolás
Az identitás adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft online szolgáltatásokra, például az Office 365-re és az Azure-ra való feliratkozáskor. További információ a személyazonossági adatok tárolásáról: a Microsoft adatvédelmi központjának hol található az [adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

Az Európai adatközpontokon belül az Azure AD a személyazonossági adatok többségét megtartja az olyan ügyfelek számára, akik európai lakcímet biztosítanak. Ez a dokumentum az Azure AD-szolgáltatások által az Európán kívül tárolt adatokról nyújt információkat.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Többtényezős hitelesítés (MFA) Microsoft Azure
    
- Az összes telefonhívást vagy SMS-t használó kétfaktoros hitelesítés az Egyesült államokbeli adatközpontokból származik, és a globális szolgáltatók is átirányítják őket.
- Az Microsoft Authenticator alkalmazást használó leküldéses értesítések az Egyesült államokbeli adatközpontokból származnak. Emellett az eszköz gyártója által biztosított szolgáltatások is játszhatnak, és ezek a szolgáltatások valószínűleg Európán kívül vannak.
- Az OATH-kódok érvényesítése mindig az USA-ban történik. 

További információ arról, hogy az Azure multi-Factor Authentication-kiszolgáló (MFA-kiszolgáló) és a felhőalapú Azure MFA milyen felhasználói adatokat gyűjt, lásd: [Azure multi-Factor Authentication felhasználói adatok gyűjtése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>B2C Microsoft Azure Active Directory (Azure AD B2C)

Az Azure AD B2C házirend-konfigurációs és a kulcstároló-tárolók az Egyesült államokbeli adatközpontokban vannak tárolva. Ezek nem tartalmaznak felhasználói személyes adatszolgáltatásokat. A házirend-konfigurációkkal kapcsolatos további információkért tekintse meg a [Azure Active Directory B2C: Beépített szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) cikke.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>B2B Microsoft Azure Active Directory (Azure AD B2B) 
    
Az Azure AD B2B a beváltási hivatkozást és az átirányítási URL-címet az Egyesült államokbeli adatközpontokban tárolja. Ezen kívül az Egyesült államokbeli adatközpontokban is tároljuk azon felhasználók e-mail-címét, akik a VÁLLALATKÖZI meghívások fogadását lemondják.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory tartományi szolgáltatások (Azure AD DS)

Az Azure AD DS ugyanott tárolja a felhasználói adatokat, ahol a felhasználó által kiválasztott Azure-beli virtuális hálózat. Tehát ha a hálózat Európán kívül van, a rendszer replikálja az adatokat, és Európán kívül tárolja.

## <a name="other-considerations"></a>Egyéb szempontok

Az Azure AD-vel integrált szolgáltatások és alkalmazások hozzáférhetnek az azonosító adatokhoz. Értékelje ki az egyes szolgáltatásait és alkalmazásait annak meghatározásához, hogy az adott szolgáltatás és alkalmazás hogyan dolgozza fel az azonosító adatait, és hogy megfelelnek-e a vállalat adattárolási követelményeinek.

A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ [az adatok tárolási helyével](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) foglalkozó szakaszában találhat további információt.

## <a name="next-steps"></a>További lépések
A fent ismertetett szolgáltatásokkal és funkciókkal kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD önkiszolgáló jelszómódosítás](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Mi az az Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Mi az az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
