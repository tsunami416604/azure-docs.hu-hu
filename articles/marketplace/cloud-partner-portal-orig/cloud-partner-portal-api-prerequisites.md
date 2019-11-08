---
title: API-előfeltételek | Azure piactér
description: A Cloud Partner Portal API-k használatának előfeltételei.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d3c2d89d3c3561e86047529e5b284e4481fc1652
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819707"
---
<a name="api-prerequisites"></a>API-előfeltételek
================

A Cloud Partner Portal API-k (egy egyszerű szolgáltatásnév és egy Azure Active Directory (Azure AD) hozzáférési token használatához két szükséges programozott eszközre van szükség.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Egyszerű szolgáltatásnév létrehozása a Azure Active Directory-bérlőben
----------------------------------------------------------------

Először létre kell hoznia egy egyszerű szolgáltatásnevet az Azure AD-bérlőben. Ez a bérlő a Cloud Partner Portalban a saját engedélyeit kapja meg. A kód a saját hitelesítő adatai helyett a bérlőt használó API-kat hívja meg.  Az egyszerű szolgáltatásnév létrehozásával kapcsolatos teljes körű magyarázatért lásd: a [portál használata egy Azure Active Directory alkalmazás és szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Adja hozzá a szolgáltatásnevet a fiókjához
-----------------------------------------

Most, hogy létrehozta a szolgáltatásnevet a bérlőben, felveheti felhasználóként a Cloud Partner Portal-fiókjába. A felhasználóhoz hasonlóan az egyszerű szolgáltatásnév a portál tulajdonosa vagy közreműködői is lehet.

A szolgáltatásnév hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Cloud Partner Portalba. 
2. Kattintson a bal oldali menüsorban található **felhasználók** elemre, majd válassza a **felhasználó hozzáadása**elemet.

   ![Felhasználó hozzáadása a portálhoz](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. A **típus** legördülő listából válassza ki az **egyszerű szolgáltatásnév** elemet, és adja hozzá a következő adatokat:

-   Az egyszerű szolgáltatásnév (például `spAccount`) **rövid neve** .
-   Az **alkalmazás azonosítója**. Az azonosító megkereséséhez nyissa meg az [Azure Portalt](https://portal.azure.com), kattintson a **Azure Active Directory**elemre, válassza a **Alkalmazásregisztrációk**lehetőséget, majd kattintson az alkalmazásra.
-   Az Azure AD-bérlőhöz tartozó **bérlői azonosító**, más néven **címtár-azonosító**. Ez az azonosító a [Azure Portal](https://portal.azure.com)Azure Active Directory oldalán, a **Tulajdonságok**területen található.
-   Az egyszerű szolgáltatásnév objektumának **azonosítója** . Ezt az azonosítót a Azure Portal kérheti le. Lépjen a **Azure Active Directoryra**, válassza a **Alkalmazásregisztrációk**lehetőséget, majd kattintson az alkalmazásra, majd kattintson az alkalmazás nevére a **helyi könyvtár felügyelt alkalmazás**területén. Ezután nyissa meg a **Tulajdonságok** lapot az objektumazonosító megkereséséhez. Győződjön meg arról, hogy nem az alkalmazáson belüli kezdeti objektumazonosító, hanem a felügyelt alkalmazásban található objektumazonosító.
-   A fiókhoz társított **szerepkör** , amelyet a rendszer a RBAC fog használni.

     ![Felügyelt alkalmazás hozzáadása a portálhoz](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kattintson a **Hozzáadás** gombra az egyszerű szolgáltatás fiókhoz való hozzáadásához.

   ![Egyszerű szolgáltatásnév hozzáadása](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Azure AD hozzáférési jogkivonat beszerzése
----------------------------

A Cloud Partner Portal API-k a következő eszközöket és protokollokat használják a hitelesítés során:

- Egy JSON Web Token (JWT) tulajdonosi jogkivonat, amely hozzáférést kér az erőforrásokhoz
- Az [OpenID Connect](https://openid.net/connect/) (OIDC) protokoll az identitás ellenőrzéséhez
- [Azure Active Directory (Azure ad) identitás-](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) szolgáltatóként

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
