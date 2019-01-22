---
title: Az Azure Active Directory B2B együttműködés korlátozásai |} A Microsoft Docs
description: Aktuális korlátozások az Azure Active Directory B2B-együttműködés
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: f338d587759748e64a6cb4822494e7b6da367feb
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426998"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Az Azure AD B2B együttműködés korlátozásai
Az Azure Active Directory (Azure AD) B2B-együttműködés jelenleg a jelen cikkben ismertetett korlátozások vonatkoznak.

## <a name="possible-double-multi-factor-authentication"></a>Lehetséges dupla többtényezős hitelesítés
Az Azure AD B2B a multi-factor authentication, az erőforrás-szervezetben (a meghívó szervezetet) kényszeríthető. Ez a megközelítés okait részletezi [feltételes hozzáférés B2B-együttműködés felhasználók](conditional-access.md). Ha egy partner már a multi-factor authentication szolgáltatás beállítása és érvényesítendő, a felhasználók a hitelesítés végrehajtásához egyszer a saját szervezetben való lehet, majd ismét az Öné.

## <a name="instant-on"></a>Instant-on
A B2B-együttműködés folyamatok hozunk felhasználók hozzáadása a címtárhoz, és dinamikusan frissíteni őket során vendégmeghívás beváltása, alkalmazás-hozzárendelés és így tovább. A frissítések és az írási műveletek általában egy példány egy címtárban, és replikálni kell az összes példányán. Replikáció befejezése után az összes példányt frissíti. Egyes esetekben, amikor az objektum ír vagy frissítve az egy példány és a hívás beolvasni az objektum egy másik példányhoz, a replikáció késése akkor fordulhat elő. Ha ez történik, frissítse vagy érdekében próbálkozzon újra. Ha egy alkalmazást, az API-val, az újrapróbálkozások az egyes visszatartási egy megfelelő, a védelmi vonalként gyakorlat a probléma enyhítése.

## <a name="azure-ad-directories"></a>Az Azure AD-címtár
Az Azure AD B2B van vonatkoznak az Azure AD szolgáltatás directory korlátok. A felhasználó létrehozhat könyvtárak száma és a könyvtárak száma, amely egy felhasználó vagy a Vendég felhasználó is tartozhat, lásd: [az Azure AD szolgáltatási korlátozások](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés a következő cikkekben talál:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés meghívók delegálása](delegate-invitations.md)

