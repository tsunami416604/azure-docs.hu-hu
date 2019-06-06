---
title: Egyéni attribútumok megadása az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az alkalmazás egyéni attribútumok az Azure Active Directory B2C az ügyfelei adatainak gyűjtésére.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 16907cb06d7e291a3da5b9d30b4d88cf4d50abf3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509586"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Egyéni attribútumok megadása az Azure Active Directory B2C-vel

 Minden ügyfél felé irányuló kérelmet kell gyűjteni az információkat egyedi követelményeket támaszt. Az Azure Active Directory (Azure AD) B2C-bérlő tartalmaz egy beépített az attribútumok, például utónév, Vezetéknév, város és irányítószám tárolt adatok. Az Azure AD B2C bővítheti az egyes felhasználói fiók a tárolt attribútumokat. 
 
 Létrehozhat egyéni attribútumokat a [az Azure portal](https://portal.azure.com/) és használja őket a regisztrálási felhasználói folyamatok, a regisztrálási vagy bejelentkezési felhasználói folyamatok vagy a Profilszerkesztési felhasználói folyamatok. Is olvasása és írása, ezek az attribútumok használatával a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Az Azure AD B2C-vel egyéni attribútumok használata [az Azure AD Graph API Directory Sémakiterjesztései](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).  

> [!NOTE]
> Támogatja a újabb [Microsoft Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) az Azure AD B2C bérlő még fejlesztés alatt áll.
>

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
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

Az egyéni attribútum már elérhető a listájában **felhasználói attribútumok** és a felhasználói folyamatokban való használatra. Vlastní atribut akkor csak az első alkalommal szerepel, minden felhasználói folyamat létrehozása, és nem listájának hozzáadása **felhasználói attribútumok**. 


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Egyéni attribútum használata a felhasználói folyamat

1. Válassza ki az Azure AD B2C-bérlőben **felhasználókövetési adatai**.
2. Válassza ki a házirendet (például "B2C_1_SignupSignin") való megnyitásához. 
4. Válassza ki **felhasználói attribútumok** , és válassza ki az egyéni attribútum (például "ShoeSize"). Kattintson a **Save** (Mentés) gombra.
5. Válassza ki **alkalmazásjogcímek** , és válassza ki az egyéni attribútum. 
6. Kattintson a **Save** (Mentés) gombra.

Miután létrehozott egy új felhasználót a felhasználói folyamat, amely használja az újonnan létrehozott egyéni attribútum használatával, az objektum kérdezhetők le a [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Másik lehetőségként használhatja a [ **felhasználói folyamat futtatása** ](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) a felhasználói folyamatot, ellenőrizze a felhasználói élmény funkciót. Meg kell jelennie **ShoeSize** az előfizetési út során gyűjtött attribútumok listáját, és nézze meg az alkalmazásnak küldött jogkivonatban. 

