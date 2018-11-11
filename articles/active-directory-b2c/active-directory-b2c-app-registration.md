---
title: Alkalmazás regisztrálása az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan regisztrálhatja alkalmazását Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8068c4a8f38cd33a1a0547f5db5079bc75c76ec1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013411"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Alkalmazás regisztrálása az Azure Active Directory B2C-vel

Hozhat létre egy [alkalmazás](active-directory-b2c-apps.md) , amely fogadja a felhasználói regisztrációt, és jelentkezzen be, először regisztrálnia kell az alkalmazást az Azure AD B2C-bérlő. Ez a cikk segít egy alkalmazás regisztrálása az Azure Active Directory (Azure AD) B2C-bérlő néhány perc múlva. Az útmutató befejeztével az alkalmazás regisztrálva lesz és használatra készen áll az Azure AD B2C-bérlőben.

## <a name="prerequisites"></a>Előfeltételek

A saját bérlő beszerzése a lépéseket követve [létrehozása az Azure Active Directory B2C-bérlő](tutorial-create-tenant.md).

Válassza ki a következő lépéseket az alkalmazás típusától függően:

- [Webalkalmazás regisztrációja](#register-a-web-application)
- [Webes API regisztrálása](#register-a-web-api)
- [Mobil vagy natív alkalmazás regisztrálása](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy nevet az alkalmazásnak. Például *testapp1*.
5. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
6. A **válasz URL-cím**, adja meg végpontot, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Például beállíthatja, hogy helyileg naslouchání `https://localhost:44316`. Ha még nem ismeri a portnak a számát, adjon meg egy helyőrző értéket, és később módosíthatja.
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Ha az alkalmazás Azure AD B2C által biztonságossá tett webes API-hívások, hozzon létre egy szeretné.

1. Válassza ki **kulcsok** majd **kulcs generálása**. 
2. Válassza ki **mentése** a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket fogja használni az alkalmazás kódjában alkalmazáskulcsként.
3. Válassza ki **API-hozzáférés**, kattintson a **Hozzáadás**, és válassza ki a webes API-t és a hatóköröket (engedélyeket).

## <a name="register-a-web-api"></a>Webes API regisztrálása

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy nevet az alkalmazásnak. Például *testapp2*.
5. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
6. A **válasz URL-cím**, adja meg végpontot, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Például beállíthatja, hogy helyileg naslouchání `https://localhost:44316`. Ha még nem ismeri a portnak a számát, adjon meg egy helyőrző értéket, és később módosíthatja.
7. A **Alkalmazásazonosító URI-t**, adja meg a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
8. Kattintson a **Create** (Létrehozás) gombra.
9. Válassza ki **közzétett hatókörök** , szükség szerint több hatókört is felvehet. Alapértelmezés szerint a `user_impersonation` hatókör van meghatározva. A `user_impersonation` hatókör lehetővé teszi más alkalmazások az API elérése a bejelentkezett felhasználó nevében. Ha szeretné, a `user_impersonation` hatókör eltávolítható.

## <a name="register-a-mobile-or-native-application"></a>Mobil vagy natív alkalmazás regisztrálása

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy nevet az alkalmazásnak. Például *testapp3*.
5. A **közé tartozik a webalkalmazás vagy webes API**válassza **nem**.
6. A **natív ügyfél belefoglalása**válassza **Igen**.
7. A **átirányítási URI-t**, adjon meg egy [átirányítási URI-t egy egyéni sémával rendelkező](active-directory-b2c-apps.md). Győződjön meg róla, válassza ki a megfelelő átirányítási URI-t, és nem tartalmaznak speciális karaktereket, például aláhúzásjeleket.
8. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Ha az alkalmazás Azure AD B2C által biztonságossá tett webes API-hívások, hozzon létre egy szeretné.

1. Válassza ki **kulcsok** majd **kulcs generálása**. 
2. Válassza ki **mentése** a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket fogja használni az alkalmazás kódjában alkalmazáskulcsként.
3. Válassza ki **API-hozzáférés**, kattintson a **Hozzáadás**, és válassza ki a webes API-t és a hatóköröket (engedélyeket).

## <a name="next-steps"></a>További lépések

Most, hogy az Azure AD B2C-vel regisztrált egy alkalmazást, [gyors üzembehelyezési oktatóanyagaink](active-directory-b2c-overview.md) segítségével elsajátíthatja a használatát.

> [!div class="nextstepaction"]
> [ASP.NET-webalkalmazás létrehozása regisztrációval, bejelentkezéssel és új jelszó kérésével](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
