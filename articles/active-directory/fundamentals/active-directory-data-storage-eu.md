---
title: Identitásadatok tárolása európai ügyfelek számára - Azure AD
description: Megtudhatja, hogy az Azure Active Directory hol tárolja az identitással kapcsolatos adatokat az európai ügyfelek számára.
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
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423003"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identitásadat-tárolás az azure-beli Active Directoryban az európai ügyfelek számára
Az identitásadatokat az Azure AD földrajzi helyen tárolja a szervezet által megadott cím alapján, amikor egy Microsoft Online-szolgáltatásra, például az Office 365-re és az Azure-ra iratkozik fel. A személyazonossági adatok tárolási helyéről a Microsoft Adatvédelmi központ Hol találhatók az [adatok?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Az európai címet megadva ügyfelek számára az Azure AD az európai adatközpontokban tartja a legtöbb identitásadatot. Ez a dokumentum az Azure AD-szolgáltatások által Európán kívül tárolt adatokról nyújt tájékoztatást.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure többtényezős hitelesítés (MFA)
    
- A telefonhívások vagy SMS-sel történő kétfaktoros hitelesítés amerikai adatközpontokból származik, és globális szolgáltatók is irányítják őket.
- A Microsoft Authenticator alkalmazással érkező leküldéses értesítések amerikai adatközpontokból származnak. Ezenkívül az eszközgyártók rakoncáttéba is kerülhetnek, és ezek a szolgáltatások európán kívül is előfordulhatnak.
- Az OATH-kódok érvényesítése mindig az USA-ban történik. 

Az Azure Multi-Factor Authentication Server (MFA Server) és a felhőalapú Azure MFA által gyűjtött felhasználói adatokról az [Azure multifactor authentication felhasználói adatgyűjtéscímű](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)témakörben talál további információt.

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Az Azure AD B2C-szabályzat konfigurációs adatait és a kulcstárolókat az amerikai adatközpontokban tárolják. Ezek nem tartalmaznak felhasználói személyes adatokat. A szabályzatkonfigurációkkal kapcsolatos további információért tekintse meg az [Azure Active Directory B2C beépített szabályzatait](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) ismertető cikket.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Az Azure AD B2B tárolja a meghívókat a beváltási hivatkozással, és átirányítja az URL-adatokat az amerikai adatközpontokban. Ezenkívül azoknak a felhasználóknak az e-mail-címét, akik leiratkoznak a B2B-meghívók fogadásáról, az amerikai adatközpontokban is tárolják.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory tartományi szolgáltatások (Azure AD DS)

Az Azure AD DS ugyanott tárolja a felhasználói adatokat, ahol a felhasználó által kiválasztott Azure-beli virtuális hálózat. Tehát ha a hálózat Európán kívül van, a rendszer replikálja az adatokat, és Európán kívül tárolja.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Összevonás a Microsoft Exchange Server 2013-ban
    
- Alkalmazásazonosító (AppID) – Az Azure Active Directory hitelesítési rendszer által az Exchange-szervezetek azonosítására létrehozott egyedi szám.
- Jóváhagyott összevont tartományok listája alkalmazáshoz
- Az alkalmazás jogkivonat-aláíró nyilvános kulcsa 

A Microsoft Exchange kiszolgálón történő összevonásról az [Összevonás: Exchange 2013 súgójában olvashat bővebben.](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Egyéb szempontok

Az Azure AD-vel integrálható szolgáltatások és alkalmazások hozzáférhetnek az identitásadatokhoz. Minden egyes szolgáltatás és alkalmazás kiértékelése annak meghatározására, hogy az adott szolgáltatás és alkalmazás hogyan dolgozza fel az identitásadatokat, és hogy azok megfelelnek-e a vállalat adattárolási követelményeinek.

A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ [az adatok tárolási helyével](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) foglalkozó szakaszában találhat további információt.

## <a name="next-steps"></a>További lépések
A fent leírt szolgáltatásokról és funkciókról az alábbi cikkekben talál további információt:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD önkiszolgáló jelszóátállítás](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Mi az az Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Mi az az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
