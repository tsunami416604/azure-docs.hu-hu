---
title: API-előfeltételek | Azure Piactér
description: Előfeltételek ide-vissza a Cloud Partner Portal API-k használatával.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: df94cba1f77ae3ea8cf595e7c651af7a69108bb6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255974"
---
<a name="api-prerequisites"></a>API-előfeltételek
================

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Két szükséges programozott eszközök, amelyek a Cloud Partner Portal API-k használatához szükséges: egy egyszerű szolgáltatás és egy Azure Active Directory (Azure AD) hozzáférési jogkivonat.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-bérlőben
----------------------------------------------------------------

Először létre kell hoznia egy egyszerű szolgáltatás az Azure AD-bérlőben. Ez a bérlő saját engedélyeket kap a felhőpartneri portálon. A kód a személyes hitelesítő adatok használata helyett a bérlőként használt API-kat hívja meg.  Az egyszerű szolgáltatás létrehozásáról a [Portál használata az erőforrásokhoz hozzáférő Azure Active Directory-alkalmazások és egyszerű szolgáltatás létrehozásá című témakörben](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)található.


<a name="add-the-service-principal-to-your-account"></a>Az egyszerű szolgáltatás hozzáadása a fiókhoz
-----------------------------------------

Most, hogy létrehozta az egyszerű szolgáltatás a bérlőben, hozzáadhatja felhasználóként a Cloud Partner Portal-fiókhoz. Csakúgy, mint egy felhasználó, a szolgáltatás névadója lehet a tulajdonos vagy a portál közreműködője.

Az egyszerű szolgáltatás hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Cloud Partner Portálra. 
2. Kattintson a bal oldali menüsáv **Felhasználók** elemére, és válassza a **Felhasználó hozzáadása lehetőséget.**

   ![Felhasználó hozzáadása a portálhoz](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. A **Típus** legördülő menüben válassza az **Egyszerű szolgáltatás lehetőséget,** és adja meg a következő adatokat:

-   A szolgáltatásnév **rövid neve,** `spAccount`például .
-   Az **alkalmazás azonosítója**. Az azonosító megkereséséhez nyissa meg az [Azure Portalt,](https://portal.azure.com)kattintson az **Azure Active Directory**elemre, válassza az **Alkalmazásregisztrációk**lehetőséget, és kattintson az alkalmazásra.
-   A **bérlői azonosító**, más néven a **címtár-azonosító,** az Azure AD-bérlő. Ez az azonosító az Azure Active Directory lapján található az [Azure Portal on](https://portal.azure.com)Tulajdonságok **csoportban.**
-   Az **egyszerű szolgáltatásobjektum objektumazonosítója.** Ezt az azonosítót az Azure Portalon szerezheti be. Nyissa meg az **Azure Active Directoryt,** válassza **az Alkalmazásregisztrációk**lehetőséget, kattintson az alkalmazásra, és kattintson az alkalmazás nevére a **Helyi címtárban a Felügyelt alkalmazás csoportban.** Ezután lépjen a **Tulajdonságok** lapra az objektumazonosító megkereséséhez. Győződjön meg arról, hogy nem az alkalmazáson található kezdeti objektumazonosítót, hanem az objektumazonosítót a felügyelt alkalmazásban ragadja meg.
-   A fiókhoz társított **szerepkör,** amelyet az RBAC-hoz fog használni.

     ![Felügyelt alkalmazás hozzáadása a portálhoz](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kattintson a **Hozzáadás** gombra, ha hozzá szeretné adni az egyszerű szolgáltatást a fiókjához.

   ![Egyszerű szolgáltatás hozzáadása](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Azure AD hozzáférési token beszerzése
----------------------------

A Cloud Partner Portal API-k a következő eszközöket és protokollokat használják a hitelesítés során:

- JSON webtoken (JWT) tulajdonosi token az erőforrásokhoz való hozzáférés kéréséhez
- Az [OpenID Connect](https://openid.net/connect/) (OIDC) protokoll az identitás ellenőrzéséhez
- [Az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) mint identitásjogosultság

Két elvi megközelítés létezik a JWT token programozott megszerzésére:

- Használja a Microsoft Authentication Library for .NET[(MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)) programot.  Ez a magasabb szintű megközelítés a .NET fejlesztők számára ajánlott. 
- HTTP **POST-kérelem** az Azure AD oauth **token** végpontjára, amely a következő formában jelenik meg:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Most adja át ezt a jogkivonatot az API-kérelmek engedélyezési fejlécének részeként.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> A hivatkozásban szereplő összes API esetében az engedélyezési fejléc mindig átadottnak tűnik, így nem említi kifejezetten.

Ha hitelesítési hibákba ütközik a kérelemben, olvassa el [a Hitelesítési hibák elhárítása című témakört.](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
