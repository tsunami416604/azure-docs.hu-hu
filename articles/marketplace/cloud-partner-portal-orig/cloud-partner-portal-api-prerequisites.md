---
title: API-Előfeltételek |} A Microsoft Docs
description: Az Előfeltételek felhasználóról a Cloud Partner Portal API-k használatával.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0d743d89c344ce9cb6eec116ed9c3e0cbe56f86e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878647"
---
<a name="api-prerequisites"></a>API-Előfeltételek
================

Nincsenek két szükséges programozási objektumokat, amelyekre szüksége van a Cloud Partner Portal API-k használata: szolgáltatásnév és a egy Azure Active Directory (Azure AD) hozzáférési tokent.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-bérlőben
----------------------------------------------------------------

Először az egyszerű szolgáltatás létrehozása az Azure AD-bérlőben. Ezt a bérlőt a Cloud Partner portálra a saját engedélykészletet lesz hozzárendelve. A kód fogja hívni az API-kat használja, mint a bérlő helyett a saját hitelesítő adataival.  Szolgáltatásnév létrehozása teljes ismertetése, lásd: [egy Azure Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>A szolgáltatásnév hozzáadása a fiókhoz
-----------------------------------------

Most, hogy létrehozta a szolgáltatás egyszerű, a bérlőben, ezt hozzáadhatja felhasználói Cloud Partner Portalon fiókjába. Csakúgy, mint a felhasználó az egyszerű szolgáltatás tulajdonosa vagy közreműködője, a portálon is lehet.

Az alábbi lépések segítségével hozzáadása a szolgáltatásnévhez:

1. A Cloud Partner portálra való bejelentkezéshez. 
2. Kattintson a **felhasználók** a bal oldali menüsáv a majd **felhasználó hozzáadása**.

   ![Felhasználó hozzáadása a portálon](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Az a **típus** legördülő menüben válassza **szolgáltatásnév** , és adja hozzá a következő adatokat:

-   A **rövid név** szolgáltatásnév számára, például `spAccount`.
-   A **Alkalmazásazonosító**. Ez az azonosító megkereséséhez nyissa meg a [az Azure Portal](https://portal.azure.com), kattintson a **Azure Active Directory**, válassza a **alkalmazásregisztrációk**, majd kattintson az alkalmazás a.
-   A **Bérlőazonosító**, más néven a **címtár-azonosító**, az Azure AD-bérlője számára. Ez az azonosító az Azure Active Directory oldalon találhatja a [az Azure portal](https://portal.azure.com)alatt **tulajdonságok**.
-   A **Objektumazonosító** a szolgáltatásnév-objektum számára. Ez az azonosító az Azure Portalról kaphat. Lépjen a **Azure Active Directory**, válassza a **alkalmazásregisztrációk**, kattintson az alkalmazás, és kattintson az alkalmazás neve mellett a **felügyelt alkalmazás a helyi címtárban**. Ezután nyissa meg a **tulajdonságok** lapon található az objektum azonosítóját. Ellenőrizze, hogy nem külső körvonalak, hogy az alkalmazás, hanem az Objektumazonosító, a felügyelt alkalmazás, kezdeti Objektumazonosítóját.
-   A **szerepkör** jelzi az RBAC a fiókhoz tartozó.

     ![Adjon hozzá egy felügyelt alkalmazást a portálra](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kattintson a **Hozzáadás** az egyszerű szolgáltatás hozzáadása a fiókjához.

   ![Szolgáltatásnév hozzáadása](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Az Azure AD hozzáférési token beszerzése
----------------------------

A Cloud Partner Portal API-k hitelesítés során a következő eszközöket és protokollokat használja:

- A JSON webes jogkivonat (JWT) tulajdonosi jogkivonat erőforrásokhoz való hozzáférés igénylése
- A [OpenID Connect](https://openid.net/connect/) (OIDC) protokoll személyazonosságát
- [Az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) az identitás-szolgáltatóként

Nincsenek alapvetően két módszer a JWT-token beszerzése a programozott módon:

- A Microsoft-hitelesítési tár használata a .NET-hez ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Ez magasabb szintű .NET-fejlesztők számára akkor ajánlott. 
- Győződjön meg arról, egy **HTTP POST** kérést az Azure AD oauth **token** végpont, amely nyilvánul meg:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Most ezt a jogkivonatot továbbíthatja az engedélyezési fejléc, az API-kérelmek részeként.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Ez az útmutató minden az API az engedélyezési fejléc mindig feltételezzük átadott, így nem explicit módon említett.

Ha hitelesítési hibák a kérelemből, lásd: [hitelesítéssel kapcsolatos hibák elhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
