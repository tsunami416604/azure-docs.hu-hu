---
title: Az Azure Active Directory B2C egyéni attribútumok |} A Microsoft Docs
description: Hogyan lehet egyéni attribútumok használata az Azure Active Directory B2C a felhasználók adatainak gyűjtésére.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 893dfbae96d2cfea01b1f281f888e9281bf582f9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441916"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Az Azure Active Directory B2C: Egyéni attribútumok használata a felhasználók adatainak gyűjtésére
Az Azure Active Directory (Azure AD) B2C-címtár tartalmaz egy beépített információk (attribútumok): utónév, Vezetéknév, város, postai irányítószám és más jellemzőket. Azonban a minden felől elérhető alkalmazásokban fogyasztói gyűjtéséhez a mi attribútumok egyedi követelményeket támaszt. Az Azure AD B2C bővítheti az egyes felhasználói fiók a tárolt attribútumokat. Létrehozhat egyéni attribútumokat a [az Azure portal](https://portal.azure.com/) , és a regisztrálási szabályzatok alább látható módon. Is olvasása és írása, ezek az attribútumok használatával a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Egyéni attribútumok használata [az Azure AD Graph API Directory Sémakiterjesztései](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
> 
> 

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása
1. [A B2C funkciók panelje az Azure Portalon lépjen a következő lépésekkel](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **felhasználói attribútumok**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy **neve** az egyéni attribútum (például "ShoeSize"), és szükség esetén egy **leírás**. Kattintson a **Create** (Létrehozás) gombra.
   
   > [!NOTE]
   > Csak a "String", "Logikai" és "Int" **adattípusok** jelenleg érhetők el.
   > 
   > 

Az egyéni attribútum már elérhető a listájában **felhasználói attribútumok**, és a regisztrálási szabályzatok használható.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Vlastní atribut használja a regisztrációs szabályzatban
1. [A B2C funkciók panelje az Azure Portalon lépjen a következő lépésekkel](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **Regisztrálási szabályzatok** lehetőségre.
3. Kattintson a regisztrációs szabályzatban (például "B2C_1_SiUp") való megnyitásához. Kattintson a **szerkesztése** a panel tetején.
4. Kattintson a **regisztrálási attribútumok** , és válassza ki az egyéni attribútum (például "ShoeSize"). Kattintson az **OK** gombra.
5. Kattintson a **alkalmazásjogcímek** , és válassza ki az egyéni attribútum. Kattintson az **OK** gombra.
6. Kattintson a **mentése** a panel tetején.

A házirend a "Futtatás most" funkció használatával ellenőrizze a felhasználói élményt. Kell most "ShoeSize" jelenik meg a felhasználói regisztráció során gyűjtött attribútumok listáját, és tekintse meg azt az alkalmazásnak küldött jogkivonatban.

## <a name="notes"></a>Megjegyzések
* Regisztrálási szabályzatok, valamint az egyéni attribútumokat is is használható a regisztrálási vagy bejelentkezési szabályzatok és Profilszerkesztési szabályzatok.
* Nincs egyéni attribútumok egy ismert korlátozás. Csak az első alkalommal szerepel, bármely házirend létrehozása, és nem hozzáadásakor listájához **felhasználói attribútumok**.

