---
title: Önkiszolgáló regisztrációs portál a B2B együttműködéshez - Azure AD
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195793"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Önkiszolgáló az Azure AD B2B együttműködési regisztrációhoz

Az ügyfelek sokféleképpen hasznát vehetik a végfelhasználók számára az [Azure Portalon](https://portal.azure.com) és az [alkalmazás-hozzáférési panelen](https://myapps.microsoft.com) elérhetővé tett, beépített funkcióknak. A B2B-felhasználók bevezetésének munkafolyamatát azonban szükséges lehet a vállalat igényeinek megfelelően testre szabni.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Azure AD jogosultságkezelés a B2B vendégfelhasználó-regisztrációhoz

Meghívó szervezetként előfordulhat, hogy nem tudja előre, hogy kik azok, akiknek hozzáférésre van szükségük az erőforrásokhoz. Szüksége van egy módja annak, hogy a partnerek felhasználói regisztrálhassanak az Ön által szabályozta szabályzatokkal. Ha azt szeretné, hogy más szervezetek felhasználói is kérhessenek hozzáférést, és jóváhagyásuk kor vendégfiókokkal kell kiépíteni őket, és csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez van rendelve, az [Azure AD jogosultságkezelés](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) segítségével konfigurálhatja [a külső felhasználók hozzáférését kezelő](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)házirendeket.

## <a name="azure-active-directory-b2b-invitation-api"></a>Az Azure Active Directory B2B meghívó API-ja

A szervezetek a [Microsoft Graph meghíváskezelő API-jával](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) saját bevezetési élményt hozhatnak létre a B2B vendégfelhasználók számára. Ha önkiszolgáló B2B vendégfelhasználó-regisztrációt szeretne kínálni, azt javasoljuk, hogy használja az [Azure AD jogosultságkezelést.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) Ha azonban saját felhasználói élményt szeretne létrehozni, használhatja a [meghíváslétrehozása API-t,](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) hogy automatikusan küldje el a testreszabott meghívó e-mailt közvetlenül a B2B felhasználónak, például. Vagy az alkalmazás használhatja a megváltatási válaszban visszaadott invite-t, hogy saját meghívót készítsön (a választott kommunikációs mechanizmuson keresztül) a meghívott felhasználónak.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködésének licencelése](licensing-guidance.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
