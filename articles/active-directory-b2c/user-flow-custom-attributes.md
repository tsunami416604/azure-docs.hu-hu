---
title: Egyéni attribútumok definiálása a Azure Active Directory B2Cban | Microsoft Docs
description: Az alkalmazáshoz tartozó egyéni attribútumok meghatározása Azure Active Directory B2Cban az ügyfelek adatainak gyűjtéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1d631c47225fac7d8a95541313593333a1399e07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87115965"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Egyéni attribútumok definiálása a Azure Active Directory B2Cban

 Minden ügyfélre kiterjedő alkalmazás egyedi követelményeknek kell megfelelnie az összegyűjtött adatokra vonatkozóan. A Azure Active Directory B2C (Azure AD B2C) bérlője az attribútumokban tárolt beépített információkkal (például Utónév, vezetéknév, város és irányítószám) rendelkezik. A Azure AD B2C használatával kiterjesztheti az egyes felhasználói fiókokban tárolt attribútumok készletét.

 Egyéni attribútumokat hozhat létre a [Azure Portalban](https://portal.azure.com/) , és felhasználhatja azokat a regisztrációs felhasználói folyamatokban, a regisztrációs vagy bejelentkezési felhasználói folyamatokban, illetve a profil szerkesztési felhasználói folyamataiban. Ezeket az attribútumokat a [Microsoft Graph API](manage-user-accounts-graph-api.md)használatával is elolvashatja és elvégezheti.

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget.

    ![Átváltás az Azure AD B2C-bérlőre](./media/user-flow-custom-attributes/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![B2C-bérlő kiemelve a címtár-és előfizetés-szűrőben](./media/user-flow-custom-attributes/select-directory.PNG)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki a **felhasználói attribútumok**elemet, majd kattintson a **Hozzáadás**gombra.
5. Adja meg az egyéni attribútum **nevét** (például "ShoeSize").
6. Válassza ki az **adattípust**. Csak a **String**, a **Boolean**és az **int** érték érhető el.
7. Igény szerint megadhat egy **leírást** az információs célokra.
8. Kattintson a **Létrehozás** elemre.

Az egyéni attribútum már elérhető a **felhasználói attribútumok** listájában és a felhasználói folyamatokban való használathoz. Egyéni attribútum csak akkor jön létre, amikor az első alkalommal használja a felhasználói folyamatokban, és nem, amikor hozzáadja a **felhasználói attribútumok**listájához.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Egyéni attribútum használata a felhasználói folyamatokban

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok**lehetőséget.
1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza ki a **felhasználói attribútumok** elemet, majd válassza ki az egyéni attribútumot (például "ShoeSize"). Kattintson a **Mentés** gombra.
1. Válassza ki az **alkalmazás jogcímeit** , majd válassza ki az egyéni attribútumot.
1. Kattintson a **Mentés** gombra.

Miután létrehozott egy új felhasználót egy olyan felhasználói folyamattal, amely az újonnan létrehozott egyéni attribútumot használja, az objektum [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)kérdezhető le. Azt is megteheti, hogy a felhasználói folyamat [Futtatás felhasználói folyamat futtatása](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) funkciójával ellenőrzi az ügyfél élményét. Ekkor látnia kell a **ShoeSize** a regisztrációs útvonalon összegyűjtött attribútumok listájában, és az alkalmazásnak visszaadott tokenben tekintheti meg.
