---
title: Önkiszolgáló bejelentkezési portál B2B-együttműködéshez – Azure AD
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff18d3d9cae6e887abbd9fb1845de62386062b2b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386707"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Önkiszolgáló Azure AD B2B együttműködési regisztrációhoz

Az ügyfelek sokféleképpen hasznát vehetik a végfelhasználók számára az [Azure Portalon](https://portal.azure.com) és az [alkalmazás-hozzáférési panelen](https://myapps.microsoft.com) elérhetővé tett, beépített funkcióknak. A B2B-felhasználók bevezetésének munkafolyamatát azonban szükséges lehet a vállalat igényeinek megfelelően testre szabni.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Azure AD-jogosultságok kezelése VÁLLALATKÖZI vendég felhasználói regisztrációhoz

Meghívó szervezetként előfordulhat, hogy nem tudja, hogy az egyes külső közreműködők Mikor férhetnek hozzá az erőforrásaihoz. Az Ön által felügyelt szabályzatoknak megfelelő módon kell megadnia a partnerek vállalatai számára a bejelentkezést. Ha engedélyezni szeretné a más szervezetek felhasználói számára a hozzáférés kérését, és a jóváhagyást a vendég fiókkal kell kiépíteni, és a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez van rendelve, akkor az [Azure ad jogosultság-kezelési](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) szolgáltatással konfigurálhatja azokat a házirendeket, amelyek [a külső felhasználók hozzáférését kezelik](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B meghívó API

A szervezetek használhatják a [Microsoft Graph meghívó Manager API](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) -t, hogy saját bevezetési tapasztalatokat hozzanak létre a B2B vendég felhasználói számára. Ha önkiszolgáló B2B vendég felhasználói regisztrációt szeretne biztosítani, javasoljuk, hogy használja az [Azure ad-jogosultságok kezelését](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Ha azonban saját felhasználói élményt szeretne létrehozni, a [meghívás API létrehozása](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) lehetőséggel automatikusan elküldheti a testreszabott meghívót a B2B-felhasználónak, például a következőt:. Vagy az alkalmazás használhatja a létrehozási válaszban visszaadott inviteRedeemUrl a saját meghívóját (a választott kommunikációs mechanizmuson keresztül) a meghívott felhasználó számára.

## <a name="next-steps"></a>Következő lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködésének licencelése](licensing-guidance.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
