---
title: Egyéni attribútumok megadása az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az alkalmazás egyéni attribútumok az Azure Active Directory B2C az ügyfelei adatainak gyűjtésére.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968774"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Egyéni attribútumok megadása az Azure Active Directory B2C-vel

 Minden ügyfél felé irányuló kérelmet kell gyűjteni az információkat egyedi követelményeket támaszt. Az Azure Active Directory (Azure AD) B2C-bérlő tartalmaz egy beépített az attribútumok, például utónév, Vezetéknév, város és irányítószám tárolt adatok. Az Azure AD B2C bővítheti az egyes felhasználói fiók a tárolt attribútumokat. 
 
 Létrehozhat egyéni attribútumokat a [az Azure portal](https://portal.azure.com/) , és ezek a regisztrálási szabályzatok, a regisztrálási vagy bejelentkezési szabályzatok, vagy a Profilszerkesztési szabályzatok használatára. Is olvasása és írása, ezek az attribútumok használatával a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Az Azure AD B2C-vel egyéni attribútumok használata [az Azure AD Graph API Directory Sémakiterjesztései](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **felhasználói attribútumok**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve** az egyéni attribútum (például "ShoeSize")
6. Válasszon egy **adattípus**. Csak **karakterlánc**, **logikai**, és **Int** érhetők el.
7. Megadhatja egy **leírás** tájékoztató jellegű. 
8. Kattintson a **Create** (Létrehozás) gombra.

Az egyéni attribútum már elérhető a listájában **felhasználói attribútumok** és a szabályzatok a használatra. Vlastní atribut akkor csak az első alkalommal szerepel, bármely házirend létrehozása, és nem listájának hozzáadása **felhasználói attribútumok**.

## <a name="use-a-custom-attribute-in-your-policy"></a>Egyéni attribútum használata a szabályzatban

1. Válassza ki az Azure AD B2C-bérlőben **regisztrálási vagy bejelentkezési szabályzatok**.
2. Válassza ki a házirendet (például "B2C_1_SignupSignin") való megnyitásához. 
3. Kattintson a **Szerkesztés** gombra.
4. Válassza ki **regisztrálási attribútumok** , és válassza ki az egyéni attribútum (például "ShoeSize"). Kattintson az **OK** gombra.
5. Válassza ki **alkalmazásjogcímek** , és válassza ki az egyéni attribútum. Kattintson az **OK** gombra.
6. Kattintson a **Save** (Mentés) gombra.

Használhatja a **Futtatás most** a házirendet a felhasználói élmény ellenőrzése a funkciót. Meg kell jelennie **ShoeSize** az előfizetési út során gyűjtött attribútumok listáját, és nézze meg az alkalmazásnak küldött jogkivonatban.

