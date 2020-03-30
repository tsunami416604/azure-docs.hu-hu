---
title: Hozzáférési jogkivonat áthárítása egy felhasználói folyamaton keresztül az alkalmazáshoz
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adja át az OAuth 2.0 identitásszolgáltatók hozzáférési jogkivonatát egy felhasználói folyamat felhasználói folyamatában az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187782"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Hozzáférési jogkivonat áthárítása egy felhasználói folyamaton keresztül az alkalmazáshoz az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) [felhasználói folyamata](user-flow-overview.md) lehetőséget biztosít az alkalmazás felhasználóiszámára, hogy regisztráljanak vagy jelentkezzenek be egy identitásszolgáltatónál. Amikor az utazás elindul, az Azure AD B2C kap egy [hozzáférési jogkivonatot](tokens-overview.md) az identitásszolgáltatótól. Az Azure AD B2C ezt a jogkivonatot használja a felhasználó adatainak lekéréséhez. Engedélyezheti, hogy a felhasználói folyamat egy jogcím adja át a jogkivonatot az Azure AD B2C-ben regisztrált alkalmazásoknak.

Az Azure AD B2C jelenleg csak az [OAuth 2.0](authorization-code-flow.md) identitásszolgáltatók hozzáférési jogkivonatának átadását támogatja, beleértve a [Facebookot](identity-provider-facebook.md) és a [Google-t.](identity-provider-google.md) Az összes többi identitásszolgáltató esetében a jogcím üresen kerül visszaadásra.

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazásnak [v2 felhasználói folyamatot kell használnia.](user-flow-versions.md)
* A felhasználói folyamat oauth 2.0 identitásszolgáltatóval van konfigurálva.

## <a name="enable-the-claim"></a>A jogcím engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget, majd válassza ki a felhasználói folyamatot. Például **B2C_1_signupsignin1**.
5. Válassza az **Alkalmazásjogcímek** lehetőséget.
6. Engedélyezze az **identitásszolgáltató-hozzáférési jogkivonat-jogcímet.**

    ![Az identitásszolgáltató-hozzáférési jogkivonat jogcímének engedélyezése](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. A felhasználói folyamat mentéséhez kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

Amikor az alkalmazások at Azure AD B2C tesztelése, hasznos lehet, hogy `https://jwt.ms` az Azure AD B2C jogkivonat ot vissza, hogy tekintse át a benne lévő jogcímek.

1. A felhasználói folyamat Áttekintés lapján válassza a **Felhasználói folyamat futtatása**lehetőséget.
2. Az **Alkalmazás területen**válassza ki a korábban regisztrált alkalmazást. Az alábbi példában a jogkivonat megtekintéséhez `https://jwt.ms`a **Válasz URL-címének** meg kell jelennie.
3. Kattintson **a Felhasználói folyamat futtatása**elemre, majd jelentkezzen be a fiók hitelesítő adataival. Az identitásszolgáltató hozzáférési jogkivonatának a **idp_access_token** jogcímben kell lennie.

    A következő példához hasonló példát kell látnia:

    ![Dekódolt token jwt.ms idp_access_token kiemelve](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>További lépések

További információ az [Azure AD B2C-jogkivonatok áttekintésében.](tokens-overview.md)
