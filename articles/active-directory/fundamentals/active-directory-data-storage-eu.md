---
title: Az Európai ügyfelei számára – Azure Active Directory Identity adattárolás |} A Microsoft Docs
description: Ismerje meg, az Azure Active Directory tárolja az Európai ügyfeleinek identitással kapcsolatos adatokat.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235160"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Az Európai ügyfelek az Azure Active Directory Identity adattárolás
Azonosító adatok az Azure AD által a szervezet szolgáltatja, amikor feliratkozik egy Microsoft Online szolgáltatáshoz, például az Office 365 és Azure-cím alapján egy földrajzi helyet tárolódik. Információ a azonosító adatok tárolására, használhatja a [hol van az adatok?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) a Microsoft Trust Center szakaszában.

Azok a vásárlóknak, akik egy címet, az Európai megadott az Azure AD biztosítja, hogy a azonosító adatok belül Európai adatközpontok legtöbb. Ez a dokumentum információt nyújt az Azure AD-szolgáltatások által Európa-on kívül tárolt adatokat.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>A Microsoft Azure multi-factor authentication (MFA)
    
- Minden a kétfaktoros hitelesítés használatával telefonhívással vagy SMS származik az USA-beli adatközpontok és a globális szolgáltatók által is kapcsolódóak.
- Leküldéses értesítések használatával a Microsoft Authenticator alkalmazás amerikai adatközpontok származik. Eszköz gyártójával konkrét szolgáltatások play és ezeket a szolgáltatásokat, talán kívül az Európai emellett is származhat.
- Az OATH-kódok érvényesítése mindig az USA-ban történik.

Az Azure multi-factor Authentication (MFA-kiszolgáló) és a felhőbeli Azure MFA gyűjtött felhasználói adatokkal kapcsolatos további információkért lásd: [Azure multi-factor Authentication felhasználói adatok gyűjtésének](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>A Microsoft Azure Active Directory B2C-vel (az Azure AD B2C)

Az Azure AD B2C szabályzat konfigurációs adatok és a kulcs tárolók Egyesült államokbeli adatközpontokban vannak tárolva. Ezek nem tartalmaznak minden olyan felhasználó személyes adatait. A házirend-beállításokkal kapcsolatos további információkért tekintse meg a [Azure Active Directory B2C: Beépített szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) cikk.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>A Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Az Azure AD B2B tárolók meghívók a beváltási hivatkozásra, és átirányítási URL-cím adatait az Egyesült Államokbeli adatközpontokban. Emellett olyan B2B meghívók fogadására leiratkozni felhasználó e-mail-címét is az Egyesült államokbeli adatközpontokban tárolja.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>A Microsoft Azure Active Directory Domain Services (Azure AD DS)

Az Azure AD DS ugyanott tárolja a felhasználói adatokat, ahol a felhasználó által kiválasztott Azure-beli virtuális hálózat. Tehát ha a hálózat Európán kívül van, a rendszer replikálja az adatokat, és Európán kívül tárolja.

## <a name="other-considerations"></a>Egyéb szempontok

Szolgáltatások és alkalmazások, amelyek integrálhatók az Azure AD identity adatok hozzáférése. Az egyes szolgáltatások és alkalmazások segítségével állapítja meg, hogyan identitási adatokat dolgoz fel, hogy adott szolgáltatást és alkalmazást, és hogy teljesítik a vállalat adattárolási követelmények kiértékeléséhez.

A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ [az adatok tárolási helyével](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) foglalkozó szakaszában találhat további információt.

## <a name="next-steps"></a>További lépések
Szolgáltatások és funkciók a fent leírt kapcsolatban további információkért tanulmányozza a következő cikkeket:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD önkiszolgáló jelszómódosítás](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Mi az az Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Mi az az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
