---
title: Identitás-adattárolás az ausztrál és az új-zélandi ügyfelek számára – Azure AD
description: Megtudhatja, hol tárolja a Azure Active Directory az identitással kapcsolatos adatait az ausztrál ügyfelei számára.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97d651a1a6ce8ef412c148f16d0aa4412df94cb0
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799548"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitás-adattárolás az ausztráliai és új-zélandi ügyfelek számára Azure Active Directory

Az identitás adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft online szolgáltatásokra, például az Office 365-re és az Azure-ra való feliratkozáskor. További információ a személyazonossági adatok tárolásáról: a Microsoft adatvédelmi központjának [Hol található az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

> [!NOTE]
> Az Azure AD-vel integrált szolgáltatások és alkalmazások hozzáférhetnek az identitás felhasználói adataihoz. Értékelje ki az egyes szolgáltatásait és alkalmazásait, hogy megtudja, hogyan dolgozza fel az Identity Customer-adatait az adott szolgáltatás és alkalmazás, és hogy megfelelnek-e a vállalat adattárolási követelményeinek. A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ az adatok tárolási helyével foglalkozó szakaszában találhat további információt.

Az ausztráliai vagy új-zélandi címeket szolgáltató ügyfelek esetében az Azure AD az ausztráliai adatközpontokban tárolja a szolgáltatásokhoz tartozó személyazonossági adatait: 
- Azure AD-címtár kezelése 
- Hitelesítés

Az összes többi Azure AD-szolgáltatás a globális adatközpontokban tárolja az ügyféladatokat. A szolgáltatás adatközpontjának megkereséséhez lásd: [Azure Active Directory – hol találhatók az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Többtényezős hitelesítés (MFA) Microsoft Azure

Az MFA a globális adatközpontokban tárolja a személyazonossági ügyféladatokat. Ha többet szeretne megtudni a felhőalapú Azure MFA és az Azure MFA-kiszolgáló által gyűjtött és tárolt felhasználói adatokról, tekintse meg az [azure multi-Factor Authentication felhasználói adatok gyűjtését](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)ismertető témakört.

## <a name="next-steps"></a>Következő lépések
A fent ismertetett szolgáltatásokkal és funkciókkal kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
