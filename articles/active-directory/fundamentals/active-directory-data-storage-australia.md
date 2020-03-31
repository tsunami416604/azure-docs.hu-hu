---
title: Identitásadatok tárolása ausztrál és új-zélandi ügyfelek számára – Azure AD
description: Megtudhatja, hogy az Azure Active Directory hol tárolja az identitással kapcsolatos adatokat az ausztrál ügyfelek számára.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370292"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitásadat-tárolás ausztrál és új-zélandi ügyfelek számára az Azure Active Directoryban

Az identitásadatokat az Azure AD földrajzi helyen tárolja a szervezet által megadott cím alapján, amikor egy Microsoft Online-szolgáltatásra, például az Office 365-re és az Azure-ra iratkozik fel. Az identitásfelhasználói adatok tárolási helyéről a Microsoft Adatvédelmi központ Hol találhatók az [adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Az Azure AD-vel integrálható szolgáltatások és alkalmazások hozzáférhetnek az identitásügyfél-adatokhoz. Minden egyes szolgáltatás és alkalmazás kiértékelése annak meghatározására, hogy az adott szolgáltatás és alkalmazás hogyan dolgozza fel az identitásügyfél-adatokat, és hogy azok megfelelnek-e a vállalat adattárolási követelményeinek. A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ az adatok tárolási helyével foglalkozó szakaszában találhat további információt.

Azoknak az ügyfeleknek, akik ausztráliában vagy Új-Zélandon adtak meg címet, az Azure AD az ausztrál adatközpontokban tartja a szolgáltatások identitásadatait: 
- Az Azure AD címtárkezelése 
- Hitelesítés

Az összes többi Azure AD-szolgáltatás globális adatközpontokban tárolja az ügyféladatokat. Egy szolgáltatás adatközpontjának megkereséséhez olvassa el az [Azure Active Directory – Hol találhatók az adatok?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure többtényezős hitelesítés (MFA)

Az MFA globális adatközpontokban tárolja az identitásügyfél-adatokat. Ha többet szeretne megtudni a felhőalapú Azure MFA és az Azure MFA Server által gyűjtött és tárolt felhasználói adatokról, olvassa el az [Azure többtényezős hitelesítésfelhasználói adatgyűjtését.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)

## <a name="next-steps"></a>További lépések
A fent leírt szolgáltatásokról és funkciókról az alábbi cikkekben talál további információt:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
