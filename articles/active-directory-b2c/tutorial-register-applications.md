---
title: Oktatóanyag – alkalmazás regisztrálása – az Azure Active Directory B2C-vel
description: Ismerje meg, hogyan regisztrálhat egy webalkalmazást az Azure Active Directory B2C az Azure portal használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c46d3153bdc5768836bce198af115f82e8469f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056280"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Oktatóanyag: Alkalmazás regisztrálása az Azure Active Directory B2C-vel

Mielőtt a [alkalmazások](active-directory-b2c-apps.md) kezelheti az Azure Active Directory (Azure AD) B2C-vel, azokat egy felügyelt bérlőhöz kell regisztrálni. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy webalkalmazást az Azure portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcs létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre a saját [Azure AD B2C-bérlő](tutorial-create-tenant.md), hozzon létre egyet most. Használhat egy meglévő Azure AD B2C-bérlőben.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy nevet az alkalmazásnak. Ha például *webapp1*.
5. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
6. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Ha például sikerült beállítani, hogy helyileg naslouchání `https://localhost:44316`. Ha még nem ismeri a portnak a számát, adjon meg egy helyőrző értéket, és később módosíthatja.

    Tesztelési célokra, például ebben az oktatóanyagban beállíthatja `https://jwt.ms` megjeleníti a tartalmát egy ellenőrzési token. A jelen oktatóanyag esetében állítsa be a **válasz URL-cím** való `https://jwt.ms`.

    A válasz URL-címet a sémával kell kezdődnie `https`, és valamennyi válasz URL-címértékekre egyetlen DNS-tartományba kell megosztani. Például, ha az alkalmazás válasz URL-Címének `https://login.contoso.com`, adhat hozzá, mint például az URL-cím `https://login.contoso.com/new`. Vagy olvassa el, a DNS altartományában `login.contoso.com`, mint például `https://new.login.contoso.com`. Ha azt szeretné, hogy az alkalmazás `login-east.contoso.com` és `login-west.contoso.com` , válasz URL-címek, hozzá kell adnia ezeket a válasz-URL az itt látható sorrendben: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Az utóbbi két adhat hozzá, mivel korábban már az első válasz URL-cím, altartományok `contoso.com`.

7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Ha az alkalmazás egy jogkivonat-kód cseréje, hozzon létre egy szeretné.

1. Az a **Azure AD B2C - alkalmazásokat** lapon, válassza ki a létrehozott alkalmazás, például *webapp1*.
2. Válassza ki **kulcsok** majd **kulcs generálása**.
3. Válassza ki **mentése** a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket használja, az alkalmazás titkos az alkalmazás kódjában.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcs létrehozása

Ezután megtudhatja, hogyan hozhat létre a felhasználói folyamatok, hogy engedélyezze a felhasználóknak a regisztráció, bejelentkezés, és a profiljuk kezelését.

> [!div class="nextstepaction"]
> [Az Azure Active Directory B2C felhasználói folyamatok létrehozása >](tutorial-create-user-flows.md)
