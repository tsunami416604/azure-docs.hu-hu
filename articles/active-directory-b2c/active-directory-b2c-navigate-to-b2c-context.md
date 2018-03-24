---
title: 'Azure Active Directory B2C: Átváltás B2C-bérlőre | Microsoft Docs'
description: Útmutató az Active Directory B2C-bérlői kontextusára történő átváltáshoz
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/13/2017
ms.author: davidmu
ms.openlocfilehash: 6469636ee05c2b28a24b1d559f0ee7569e784393
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Átváltás az Azure AD B2C-bérlőre

Az Azure AD B2C konfigurálásához az Azure AD B2C-bérlői kontextusában kell lennie.

## <a name="log-into-azure-ad-b2c-tenant"></a>Bejelentkezés az Azure AD B2C-bérlőre

Az Azure AD B2C-bérlőre való navigáláshoz az Azure AD B2C-bérlő globális rendszergazdájaként kell bejelentkeznie az Azure Portalra.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
1. A bérlők között az e-mail-címére vagy a jobb felső sarokban található képre kattintva válthat.
1. A megjelenő `Directory` listában válassza ki a kezelni kívánt Azure AD B2C-bérlőt.

Az Azure Portal frissül.  Sikeresen bejelentkezett az Azure Portalra az Azure AD B2C-bérlője kontextusában.

## <a name="navigate-to-the-b2c-features-pane"></a>Lépjen a B2C funkciók ablaktáblára

1. Kattintson a képernyő bal oldalán található **Tallózás** navigációs elemre.
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az `Azure AD B2C` elemet a bal oldali navigációs ablaktáblán.  (A bal oldali kezdőpulthoz való rögzítéshez kattintson az Azure AD B2C-től balra található csillagra.)
1. A B2C-funkciók ablaktábla eléréséhez kattintson az **Azure AD B2C** elemre.
   
    ![A B2C-funkciók ablaktábla tallózásának képernyőképe](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> A B2C-bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C-funkciók ablaktáblához. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.  A B2C-bérlőre az Azure Portal jobb felső sarkában található bérlőváltó használatával válthat át.
