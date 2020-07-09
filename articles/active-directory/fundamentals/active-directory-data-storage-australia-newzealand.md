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
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460534"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitás-adattárolás az ausztráliai és új-zélandi ügyfelek számára Azure Active Directory

Az identitás adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft online szolgáltatásokra, például az Office 365-re és az Azure-ra való feliratkozáskor. További információ a személyazonossági adatok tárolásáról: a Microsoft adatvédelmi központjának [Hol található az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

> [!NOTE]
> Az Azure AD-vel integrált szolgáltatások és alkalmazások hozzáférhetnek az identitás felhasználói adataihoz. Értékelje ki az egyes szolgáltatásait és alkalmazásait, hogy megtudja, hogyan dolgozza fel az Identity Customer-adatait az adott szolgáltatás és alkalmazás, és hogy megfelelnek-e a vállalat adattárolási követelményeinek. A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ az adatok tárolási helyével foglalkozó szakaszában találhat további információt.

Azon ügyfelek számára, akik Ausztráliában és Új-Zélandon adták meg a címeket, és az Azure AD ingyenes kiadását használják, az Azure AD a személyes adatok védelme érdekében az ausztráliai adatközpontokon belül marad. 

Az összes többi Azure AD Premium-szolgáltatás a globális adatközpontokban tárolja az ügyféladatokat. A szolgáltatás adatközpontjának megkereséséhez lásd: [Azure Active Directory – hol találhatók az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Többtényezős hitelesítés (MFA) Microsoft Azure

Az Azure AD-ben az MFA szolgáltatás a globális adatközpontokban tárolja az identitás-ügyféladatokat. Ha többet szeretne megtudni a felhőalapú Azure MFA és az Azure MFA-kiszolgáló által gyűjtött és tárolt felhasználói adatokról, tekintse meg az [azure multi-Factor Authentication felhasználói adatok gyűjtését](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)ismertető témakört. Ha az ügyfelek az MFA-t használják, az adatok a nyugalmi állapotban lévő ausztráliai adatközpontokon kívül lesznek tárolva. 

## <a name="next-steps"></a>További lépések
A fent ismertetett szolgáltatásokkal és funkciókkal kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Mi az a többtényezős hitelesítés?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
