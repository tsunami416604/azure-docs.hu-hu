---
title: Egyéni attribútumok definiálása az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: Egyéni attribútumokat definiáljon az alkalmazáshoz az Azure Active Directory B2C-ben az ügyfelekkel kapcsolatos információk gyűjtéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186049"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Egyéni attribútumok definiálása az Azure Active Directory B2C-ben

 Minden ügyfél felé néző alkalmazás egyedi követelményeket támaszt az összegyűjtendő információkkal kapcsolatban. Az Azure Active Directory B2C (Azure AD B2C) bérlője az attribútumokban , például a keresztnévben, a vezetéknévben, a városban és az irányítószámban tárolt információk beépített készletével érkezik. Az Azure AD B2C segítségével kiterjesztheti az egyes ügyfél-fiókban tárolt attribútumok készletét.

 Egyéni attribútumokat hozhat létre az [Azure Portalon,](https://portal.azure.com/) és használhatja őket a regisztrációs felhasználói folyamatokban, a regisztrációs vagy bejelentkezési felhasználói folyamatokban, illetve a profilszerkesztési felhasználói folyamatokban. Ezeket az attribútumokat a [Microsoft Graph API](manage-user-accounts-graph-api.md)segítségével is elolvashatja és írhatja.

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget.

    ![Átváltás az Azure AD B2C-bérlőre](./media/user-flow-custom-attributes/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![B2C-bérlő kiemelve a Címtár- és Előfizetésszűrőben](./media/user-flow-custom-attributes/select-directory.PNG)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza **a Felhasználói attribútumok**lehetőséget, majd a **Hozzáadás**lehetőséget.
5. Adja meg az egyéni attribútum **nevét** (például "ShoeSize")
6. Válasszon **egy adattípust**. Csak **a Karakterlánc**, a **Logikai**és **az Int** érhető el.
7. Szükség esetén adjon meg **egy leírást** tájékoztató célokra.
8. Kattintson **a Létrehozás gombra.**

Az egyéni attribútum most már elérhető a **felhasználói attribútumok** listájában, és a felhasználói folyamatokban való használatra. Az egyéni attribútum oka csak akkor jön létre, amikor azt először használják bármely felhasználói folyamatban, és nem akkor, amikor hozzáadja a **Felhasználói attribútumok listájához.**

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Egyéni attribútum használata a felhasználói folyamatban

1. Az Azure AD B2C-bérlőben válassza a **Felhasználói folyamatok**lehetőséget.
1. A házirend (például a "B2C_1_SignupSignin") kiválasztásával nyissa meg.
1. Válassza a **Felhasználói attribútumok** lehetőséget, majd az egyéni attribútumot (például "ShoeSize"). Kattintson a **Mentés** gombra.
1. Válassza **az Alkalmazásjogcímeket,** majd az egyéni attribútumot.
1. Kattintson a **Mentés** gombra.

Miután létrehozott egy új felhasználót egy olyan felhasználói folyamat használatával, amely az újonnan létrehozott egyéni attribútumot használja, az objektum lekérdezhető a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)programban. Másik lehetőségként használhatja a [Felhasználói folyamat futtatása](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) funkciót a felhasználói folyamaton az ügyfélélmény ellenőrzéséhez. Most már látnia kell **shoesize** a regisztrációs út során gyűjtött attribútumok listájában, és tekintse meg az alkalmazásnak küldött jogkivonatban.
