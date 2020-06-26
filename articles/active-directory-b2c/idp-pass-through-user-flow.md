---
title: Hozzáférési jogkivonat továbbítása az alkalmazás felhasználói folyamatán keresztül
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzáférési jogkivonatot a OAuth 2,0-identitás szolgáltatójának jogcímként a Azure Active Directory B2C felhasználói folyamatában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0446dd9e316e145cf93c503ab9634d4eedbc299b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85387830"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Hozzáférési token átadása egy felhasználói folyamaton keresztül az alkalmazáshoz Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) [felhasználói folyamata](user-flow-overview.md) lehetővé teszi az alkalmazás felhasználói számára, hogy regisztráljon vagy jelentkezzen be az identitás-szolgáltatóval. Az utazás megkezdésekor a Azure AD B2C [hozzáférési jogkivonatot](tokens-overview.md) kap az identitás-szolgáltatótól. Azure AD B2C a token használatával kéri le a felhasználó adatait. A felhasználói folyamat egy jogcímet engedélyez, hogy átadja a jogkivonatot a Azure AD B2Cban regisztrált alkalmazásoknak.

Azure AD B2C jelenleg csak az [OAuth 2,0](authorization-code-flow.md) Identity Providers hozzáférési jogkivonatának továbbítását támogatja, beleértve a [Facebookot](identity-provider-facebook.md) és a [Google](identity-provider-google.md)-t is. Az összes többi Identity Provider esetében a rendszer üresen adja vissza a jogcímet.

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazásnak [v2 felhasználói folyamatot](user-flow-versions.md)kell használnia.
* A felhasználói folyamat OAuth 2,0-es identitás-szolgáltatóval van konfigurálva.

## <a name="enable-the-claim"></a>Jogcím engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget, majd válassza ki a felhasználói folyamatot. Például **B2C_1_signupsignin1**.
5. Válassza az **Alkalmazásjogcímek** lehetőséget.
6. Engedélyezze az **identitás-szolgáltató hozzáférési jogkivonat** jogcímet.

    ![Az identitás-szolgáltató hozzáférési jogkivonat jogcímének engedélyezése](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. A felhasználói folyamat mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

Az alkalmazások Azure AD B2C-ben történő tesztelésekor hasznos lehet, hogy a Azure AD B2C token visszaadja a `https://jwt.ms` jogcímeket a jogcímek áttekintéséhez.

1. A felhasználói folyamat áttekintés lapján válassza a **felhasználói folyamat futtatása**lehetőséget.
2. **Alkalmazás**esetén válassza ki a korábban regisztrált alkalmazást. Az alábbi példában szereplő token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
3. Kattintson a **felhasználói folyamat futtatása**elemre, majd jelentkezzen be a fiókja hitelesítő adataival. Ekkor meg kell jelennie az Identitáskezelő hozzáférési jogkivonatának az **idp_access_token** jogcímben.

    Az alábbi példához hasonlónak kell megjelennie:

    ![Dekódolású token a jwt.ms-ben idp_access_token blokk kiemelve](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Következő lépések

További információ: [Azure ad B2C tokenek áttekintése](tokens-overview.md).
