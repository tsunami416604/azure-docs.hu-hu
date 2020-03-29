---
title: B2B együttműködési licencelési útmutató - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B-együttműködéshez nincs szükség fizetős Azure AD-licencre, de fizetős funkciókat is kaphat a B2B vendégfelhasználók számára
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868847"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Az Azure Active Directory B2B együttműködési licencelési útmutatója

Az Azure Active Directory (Azure AD) vállalkozások közötti (B2B) együttműködéssel külső felhasználókat (vagy "vendégfelhasználókat") hívhat meg a fizetős Azure AD-szolgáltatások használatára. Egyes funkciók ingyenesek, de minden fizetős Azure AD-funkciók esetén legfeljebb öt vendégfelhasználót hívhat meg minden olyan Azure AD-kiadási licenchez, amely egy alkalmazott vagy egy nem vendég felhasználó számára rendelkezik a bérlőben.

> [!NOTE]
> Az Azure AD-díjszabással és a B2B együttműködési funkciókkal kapcsolatos részletekért tekintse meg az [Azure Active Directory díjszabását.](https://azure.microsoft.com/pricing/details/active-directory/)

A B2B vendégfelhasználó licencelése automatikusan kiszámításra és jelentésre kerül az 1:5 arány alapján. 

Emellett a vendégfelhasználók további licencelési követelmények nélkül is használhatják az ingyenes Azure AD-funkciókat. A vendégfelhasználók akkor is hozzáférhetnek az ingyenes Azure AD-funkciókhoz, ha nem rendelkezik fizetős Azure AD-licenccel. 

## <a name="examples-calculating-guest-user-licenses"></a>Példák: Vendégfelhasználói licencek számítása
Miután eldönti, hogy hány vendégfelhasználónak kell hozzáférnie a fizetős Azure AD-szolgáltatásokhoz, győződjön meg arról, hogy elegendő Azure AD-alapú licenccel rendelkezik a szükséges 1:5 arányú vendégfelhasználók lefedéséhez. Néhány példa:

- 100 vendégfelhasználót szeretne meghívni az Azure AD-alkalmazásokba vagy -szolgáltatásokba, és hozzáférést szeretne biztosítani. 50 ilyen vendégfelhasználók, azt is szeretné, hogy többfa és feltételes hozzáférés, így ezek a funkciók 10 Azure AD Premium P1 licencek lesz szüksége. Ha azt tervezi, hogy az Identity Protection funkciókat a vendég felhasználók, szüksége lesz az Azure AD Premium P2 licencek azonos 1:5 arány, hogy fedezze a vendég felhasználók.
- 60 vendégfelhasználót szeretne meghívni, akiknek mind többdíjú hitelesítést igényelnek, ezért legalább 12 Azure AD Premium P1 licenccel kell rendelkeznie. 10 alkalmazottja van az Azure AD Premium P1 licencekkel, amelyek legfeljebb 50 vendégfelhasználót engedélyeznek az 1:5 licencelési arány alatt. További 10 vendégfelhasználó lefedéséhez két további Prémium P1 licencet kell vásárolnia.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi erőforrásokat az Azure AD B2B együttműködés:

* [Az Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
