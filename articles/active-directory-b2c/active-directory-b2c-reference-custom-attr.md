---
title: Egyéni attribútumok definiálása a Azure Active Directory B2Cban | Microsoft Docs
description: Az alkalmazáshoz tartozó egyéni attribútumok meghatározása Azure Active Directory B2Cban az ügyfelek adatainak gyűjtéséhez.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6b0eac0b8cf7f61d76f90a4f769ba11abab6999
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065673"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Egyéni attribútumok definiálása a Azure Active Directory B2Cban

 Minden ügyfélre kiterjedő alkalmazás egyedi követelményeknek kell megfelelnie az összegyűjtött adatokra vonatkozóan. A Azure Active Directory B2C (Azure AD B2C) bérlője az attribútumokban tárolt beépített információkkal (például Utónév, vezetéknév, város és irányítószám) rendelkezik. A Azure AD B2C használatával kiterjesztheti az egyes felhasználói fiókokban tárolt attribútumok készletét.

 Egyéni attribútumokat hozhat létre a [Azure Portalban](https://portal.azure.com/) , és felhasználhatja azokat a regisztrációs felhasználói folyamatokban, a regisztrációs vagy bejelentkezési felhasználói folyamatokban, illetve a profil szerkesztési felhasználói folyamataiban. Ezeket az attribútumokat az [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)használatával is elolvashatja és elvégezheti. A Azure AD B2C egyéni attribútumai az [Azure AD Graph API Directory sémakezelő bővítményeket](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)használják.

> [!NOTE]
> Még fejlesztés alatt áll az újabb [Microsoft Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) támogatása Azure ad B2C bérlő lekérdezéséhez.
>

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget.

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![B2C-bérlő kiemelve a címtár-és előfizetés-szűrőben](./media/active-directory-b2c-reference-custom-attr/select-directory.PNG)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki a **felhasználói attribútumok**elemet, majd kattintson a **Hozzáadás**gombra.
5. Adja meg az egyéni attribútum **nevét** (például "ShoeSize").
6. Válassza kiaz adattípust. Csak a **String**, a **Boolean**és az **int** érték érhető el.
7. Igény szerint megadhat egy **leírást** az információs célokra.
8. Kattintson a **Create** (Létrehozás) gombra.

Az egyéni attribútum már elérhető a **felhasználói attribútumok** listájában és a felhasználói folyamatokban való használathoz. Egyéni attribútum csak akkor jön létre, amikor az első alkalommal használja a felhasználói folyamatokban, és nem, amikor hozzáadja a **felhasználói attribútumok**listájához.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Egyéni attribútum használata a felhasználói folyamatokban

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok**lehetőséget.
2. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
4. Válassza ki a **felhasználói attribútumok** elemet, majd válassza ki az egyéni attribútumot (például "ShoeSize"). Kattintson a **Save** (Mentés) gombra.
5. Válassza ki az **alkalmazás jogcímeit** , majd válassza ki az egyéni attribútumot.
6. Kattintson a **Save** (Mentés) gombra.

Miután létrehozott egy új felhasználót egy olyan felhasználói folyamattal, amely az újonnan létrehozott egyéni attribútumot használja, az objektum az [Azure ad Graph Explorerben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)kérdezhető le. Azt is megteheti, hogy a felhasználói folyamat [**Futtatás felhasználói folyamat futtatása**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) funkciójával ellenőrzi az ügyfél élményét. Ekkor látnia kell a **ShoeSize** a regisztrációs útvonalon összegyűjtött attribútumok listájában, és az alkalmazásnak visszaadott tokenben tekintheti meg.

