---
title: API-előfeltételek – Azure Marketplace
description: Az Cloud Partner Portal API-k használatának előfeltételei.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: c435e171c7e3cabea4007c7b78cf60727bf55efd
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958229"
---
<a name="api-prerequisites"></a>API-előfeltételek
================

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

A Cloud Partner Portal API-k (egy egyszerű szolgáltatásnév és egy Azure Active Directory (Azure AD) hozzáférési token használatához két szükséges programozott eszközre van szükség.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Egyszerű szolgáltatásnév létrehozása a Azure Active Directory-bérlőben
----------------------------------------------------------------

Először létre kell hoznia egy egyszerű szolgáltatásnevet az Azure AD-bérlőben. Ez a bérlő a Cloud Partner Portalban a saját engedélyeit kapja meg. A kód a saját hitelesítő adatai helyett a bérlőt használó API-kat hívja meg.  Az egyszerű szolgáltatásnév létrehozásával kapcsolatos teljes körű magyarázatért lásd [: útmutató: a portál használata az erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatásnév létrehozásához](../active-directory/develop/howto-create-service-principal-portal.md).


<a name="add-the-service-principal-to-your-account"></a>Adja hozzá a szolgáltatásnevet a fiókjához
-----------------------------------------

Most, hogy létrehozta a szolgáltatásnevet a bérlőben, felveheti felhasználóként a Cloud Partner Portal-fiókjába. A felhasználóhoz hasonlóan az egyszerű szolgáltatásnév a portál tulajdonosa vagy közreműködői is lehet.

A szolgáltatásnév hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Cloud Partner Portalba. 
2. Kattintson a bal oldali menüsorban található **felhasználók** elemre, majd válassza a **felhasználó hozzáadása**elemet.

   ![Felhasználó hozzáadása a portálhoz](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. A **típus** legördülő listából válassza ki az **egyszerű szolgáltatásnév** elemet, és adja hozzá a következő adatokat:

-   Az **egyszerű szolgáltatásnév,** például: `spAccount` .
-   Az **alkalmazás azonosítója**. Az azonosító megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com), kattintson az **Azure Active Directory**elemre, válassza a **Alkalmazásregisztrációk**lehetőséget, majd kattintson az alkalmazásra.
-   Az Azure AD-bérlőhöz tartozó **bérlői azonosító**, más néven **címtár-azonosító**. Ez az azonosító a [Azure Portal](https://portal.azure.com)Azure Active Directory oldalán, a **Tulajdonságok**területen található.
-   Az egyszerű szolgáltatásnév objektumának **azonosítója** . Ezt az azonosítót a Azure Portal kérheti le. Lépjen a **Azure Active Directoryra**, válassza a **Alkalmazásregisztrációk**lehetőséget, majd kattintson az alkalmazásra, majd kattintson az alkalmazás nevére a **helyi könyvtár felügyelt alkalmazás**területén. Ezután nyissa meg a **Tulajdonságok** lapot az objektumazonosító megkereséséhez. Győződjön meg arról, hogy nem az alkalmazáson belüli kezdeti objektumazonosító, hanem a felügyelt alkalmazásban található objektumazonosító.
-   A fiókhoz társított **szerepkör** , amelyet a rendszer a RBAC fog használni.

     ![Felügyelt alkalmazás hozzáadása a portálhoz](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Kattintson a **Hozzáadás** gombra az egyszerű szolgáltatás fiókhoz való hozzáadásához.

   ![Egyszerű szolgáltatásnév hozzáadása](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Azure AD hozzáférési token beszerzése
----------------------------

A Cloud Partner Portal API-k a következő eszközöket és protokollokat használják a hitelesítés során:

- Egy JSON Web Token (JWT) tulajdonosi jogkivonat, amely hozzáférést kér az erőforrásokhoz
- Az [OpenID Connect](https://openid.net/connect/) (OIDC) protokoll az identitás ellenőrzéséhez
- [Azure Active Directory (Azure ad) identitás-](../active-directory/fundamentals/active-directory-whatis.md) szolgáltatóként

A JWT-token programozott beszerzésének két elve van:

- A .NET-hez készült Microsoft Authentication Library ([MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)) használata.  Ez a magasabb szintű megközelítés a .NET-fejlesztők számára ajánlott. 
- Tegyen **közzé egy HTTP Post** -kérést az Azure ad OAuth **jogkivonat** -végpontján, amely a következő űrlapot veszi igénybe:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Most átadhatja ezt a jogkivonatot az API-kérések engedélyezési fejlécének részeként.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Az ebben a hivatkozásban található összes API esetében az engedélyezési fejlécet a rendszer mindig átadja, ezért azt explicit módon nem említi.

Ha hitelesítési hibákba ütközik a kérelmében, olvassa el a [hitelesítési hibák elhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)című témakört.
