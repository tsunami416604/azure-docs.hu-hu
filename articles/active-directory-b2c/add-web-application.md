---
title: Web API-alkalmazás hozzáadása – Azure Active Directory B2C | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá web API-alkalmazást az Active Directory B2C-bérlőhöz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190177"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Web API-alkalmazás hozzáadása az Azure Active Directory B2C-bérlőhöz

 Regisztráljon webes API-erőforrásokat a bérlőben, hogy azok el tudják fogadni és megválaszolják a hozzáférési jogkivonatot tartalmazó ügyfélalkalmazások kéréseit. Ez a cikk bemutatja, hogyan regisztrálhat egy webes API-t az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban.

Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
5. Adjon nevet az alkalmazásnak. Például *webapi1*.
6. A **Webapp/web API belefoglalása** és **az Implicit folyamat engedélyezése csoportban**válassza az **Igen**lehetőséget.
7. A **válasz URL-cím,** adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Az éles alkalmazásban a válasz URL-címét például `https://localhost:44332`a értékre állíthatja. Tesztelési célokra állítsa a `https://jwt.ms`válasz URL-címét a-ra.
8. **Az App ID URI mezőben**adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson **a Létrehozás gombra.**
10. A tulajdonságok lapon rögzítse a webalkalmazás konfigurálásakor használni kívánt alkalmazásazonosítót.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *webapi1*.
1. Az **ÁTirányítás URI,válassza**ki **a Web**lehetőséget, majd adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Egy éles alkalmazásban az átirányítási URI-t a `https://localhost:5000`végponthoz állíthatja be. A fejlesztés vagy tesztelés során `https://jwt.ms`beállíthatja, hogy a Microsoft tulajdonában lévő webalkalmazás, amely megjeleníti a dekódolt tartalmát egy jogkivonat (a token tartalmát soha nem hagyja el a böngészőt). A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-kat.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** a webes API kódjában való használatra.

Ha olyan alkalmazással rendelkezik, amely megvalósítja az implicit támogatási folyamatot, például egy JavaScript-alapú egyoldalas alkalmazást (SPA), az alábbi lépésekkel engedélyezheti a folyamatot:

1. A **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. Válassza **az Új felület kipróbálása** lehetőséget (ha látható).
1. Az **Implicit támogatás**csoportban jelölje be mind az **Access-jogkivonatokat,** mind az **Azonosító-jogkivonatokat** jelölőnégyzetekbe.
1. Kattintson a **Mentés** gombra.

* * *

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Engedélyek megadása

A védett webes API-t egy alkalmazásból, meg kell adnia az alkalmazás engedélyeket az API-t. Például [az oktatóanyag: Regisztráljon egy alkalmazást az Azure Active Directory B2C,](tutorial-register-applications.md)egy *webapp1* nevű webalkalmazás regisztrálva van az Azure AD B2C. Ezzel az alkalmazással hívhatja meg a webes API-t.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az alkalmazás regisztrálva van a védett webes API hívásához. A felhasználó hitelesíti magát az Azure AD B2C az alkalmazás használatához. Az alkalmazás beszerzi az Azure AD B2C engedélyezési engedélyt a védett webes API eléréséhez.
