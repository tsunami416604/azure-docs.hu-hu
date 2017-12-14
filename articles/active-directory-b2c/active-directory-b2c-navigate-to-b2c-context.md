---
title: "Azure Active Directory B2C: Átváltás B2C-bérlőre | Microsoft Docs"
description: "Útmutató az Active Directory B2C-bérlői kontextusára történő átváltáshoz"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.openlocfilehash: 6c1fd08c52f33a062d06e0593cbbe00346bb44f1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Átváltás az Azure AD B2C-bérlőre

Az Azure AD B2C konfigurálásához az Azure AD B2C-bérlői kontextusában kell lennie.

## <a name="log-into-azure-ad-b2c-tenant"></a>Bejelentkezés az Azure AD B2C-bérlőre

Az Azure AD B2C-bérlőre való navigáláshoz az Azure AD B2C-bérlő globális rendszergazdájaként kell bejelentkeznie az Azure Portalra.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
1. A bérlők között az e-mail-címére vagy a jobb felső sarokban található képre kattintva válthat.
1. A megjelenő `Directory` listában válassza ki a kezelni kívánt Azure AD B2C-bérlőt.

Az Azure Portal megjelenítése ekkor frissül.  Sikeresen bejelentkezett az Azure Portalra az Azure AD B2C-bérlője kontextusában.

## <a name="navigate-to-the-b2c-features-blade"></a>Lépjen a B2C funkciók paneljére

1. Kattintson a képernyő bal oldalán található **Tallózás** navigációs elemre.
1. Kattintson a **További szolgáltatások** elemre, majd keresse meg az `Azure AD B2C` elemet a bal oldali navigációs panelen.  (A bal oldali kezdőpulthoz való rögzítéshez kattintson az Azure AD B2C-től balra található csillagra.)
1. A B2C-funkciók paneljéhez való hozzáféréshez kattintson az **Azure AD B2C** elemre.
   
    ![A B2C-funkciók panel tallózásának képernyőképe](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> A B2C bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C funkciók panelhez. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.  A B2C-bérlőre az Azure Portal jobb felső sarkában található bérlőváltó használatával válthat át.
