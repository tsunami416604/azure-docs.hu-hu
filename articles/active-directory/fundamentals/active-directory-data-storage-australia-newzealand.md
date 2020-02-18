---
title: Identitás-adattárolás ausztrál ügyfelek számára – Azure AD
description: Megtudhatja, hol tárolja a Azure Active Directory az identitással kapcsolatos adatait az ausztrál ügyfelei számára.
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
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371526"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Identitás-adattárolás a Azure Active Directory ausztrál ügyfelei számára

Az identitás adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft online szolgáltatásokra, például az Office 365-re és az Azure-ra való feliratkozáskor. További információ a személyazonossági adatok tárolásáról: a Microsoft adatvédelmi központjának [Hol található az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

> [!NOTE]
> Az Azure AD-vel integrált szolgáltatások és alkalmazások hozzáférhetnek az identitás felhasználói adataihoz. Értékelje ki az egyes szolgáltatásait és alkalmazásait, hogy megtudja, hogyan dolgozza fel az Identity Customer-adatait az adott szolgáltatás és alkalmazás, és hogy megfelelnek-e a vállalat adattárolási követelményeinek. További információ a Microsoft-szolgáltatások adattárolásáról: hol találhatók az adatok? a Microsoft adatvédelmi központjának szakasza.

Azon ügyfelek esetében, akik Ausztráliában adták meg az adott szolgáltatást, az Azure AD az ausztráliai adatközpontokban tárolja a szolgáltatások személyazonossági adatait: 
- Azure AD-címtár kezelése 
- Authentication

Az összes többi Azure AD-szolgáltatás a globális adatközpontokban tárolja az ügyféladatokat. A szolgáltatás adatközpontjának megkereséséhez lásd: [Azure Active Directory – hol találhatók az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Többtényezős hitelesítés (MFA) Microsoft Azure

Az MFA a globális adatközpontokban tárolja a személyazonossági ügyféladatokat. Ha többet szeretne megtudni a felhőalapú Azure MFA és az Azure MFA-kiszolgáló által gyűjtött és tárolt felhasználói adatokról, tekintse meg az [azure multi-Factor Authentication felhasználói adatok gyűjtését](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)ismertető témakört.

## <a name="next-steps"></a>Következő lépések
A fent ismertetett szolgáltatásokkal és funkciókkal kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
