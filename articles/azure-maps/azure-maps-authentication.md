---
title: Hitelesítés az Azure Maps |} A Microsoft Docs
description: Hitelesítés az Azure Maps-szolgáltatások használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9dfe4024607e106565984d6d49de94d793bf7a8f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010412"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps használatával

Az Azure Maps-kérések hitelesítéséhez kétféleképpen támogatja: Megosztott kulcs és az Azure Active Directory (Azure AD). Ez a cikk ismerteti ezek a hitelesítési módszerek az útmutató segítségével a megvalósítás.

## <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

Megosztott kulcsos hitelesítés az Azure Maps-fiók az Azure Maps minden egyes kérelemmel által létrehozott kulcsok továbbítja.  Két kulcsot az Azure Maps-fiók létrehozásakor jönnek létre. Az egyes kérések az Azure Maps-szolgáltatásokhoz való az előfizetési kulcs hozzá kell adnia egy paraméterként az URL-címre.

> [!Tip]
> Azt javasoljuk, hogy a kulcsok rendszeres újragenerálása. Két kulcs mellékelt éppen úgy, hogy a kapcsolatok fenntartásához egy kulccsal amíg az egyiket. A kulcsok újragenerálásakor, minden olyan alkalmazást, hogy az új kulcsok használandó frissíteni szeretné.

A kulcsok megtekintésével kapcsolatos további információkért lásd: [hitelesítési részleteinek megtekintéséhez](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Hitelesítés az Azure Active Directoryval (előzetes verzió)

Az Azure Maps kínál [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) -integráció a hitelesítési kérések az Azure Maps-szolgáltatásokhoz. Az Azure AD identitás-alapú hitelesítést biztosít többek között [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), az Azure Maps-erőforrások felhasználói szintű és alkalmazásszintű hozzáférést. A következő szakaszok segíthetnek a fogalmakat és az Azure Maps-integráció az Azure AD-összetevők ismertetése.

## <a name="authentication-with-oauth-access-tokens"></a>Hitelesítés az OAuth hozzáférési tokenek

Az Azure Maps fogad **OAuth 2.0** hozzáférési jogkivonatokat az Azure-előfizetéssel, amely tartalmazza az Azure Maps-fiók társított Azure AD-bérlőt. Az Azure Maps a jogkivonatokat fogad el:

* Az Azure AD-felhasználók. 
* Külső alkalmazások, amelyek a felhasználók delegált engedélyeket használ.
* Felügyelt identitások az Azure-erőforrásokhoz.

Az Azure Maps-hoz létre egy *egyedi azonosítóját (ügyfél-azonosító)* minden az Azure Maps-fiók. Az ügyfél-azonosító kombinálva további paraméterek, a következő érték megadásával jogkivonatokat kérhet az Azure ad-ből:

```
https://login.microsoftonline.com
```
Konfigurálja az Azure AD és az Azure Maps jogkivonatokat kérhetnek kapcsolatos további információkért lásd: [Azure Maps-kezelés hitelesítési](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Az Azure ad-ből kérő jogkivonatok kapcsolatos általános információkért lásd: [a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Az OAuth-jogkivonatok Azure-térképekhez erőforrás-kérelmek

Egy tokent az Azure ad-ből fogadását követően kérelmet lehet küldeni az Azure Maps kulcstartón a következő két szükséges kérelem fejlécei:

| Kérelem fejléce    |    Érték    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Engedélyezés     | Tulajdonosi eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id` az Azure Maps hitelesítési oldalon megjelenő Azure Maps tárfiókalapú GUID van.

Íme egy példa az Azure Maps útvonalat kiválasztani a kérés által használt OAuth-token:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Az ügyfél-azonosító megtekintésével kapcsolatos további információkért lásd: [hitelesítési részleteinek megtekintéséhez](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Hozzáférés vezérlése RBAC használata

Az Azure AD lehetővé teszi a hozzáférést RBAC használatával védett erőforrásokhoz. Miután az Azure Maps-fiók létrehozása és az Azure Maps az Azure AD alkalmazás regisztrálása az Azure AD-bérlő belül, akkor állíthatja RBAC egy felhasználó, alkalmazás vagy Azure-erőforrást az Azure Maps-fiók portál oldalon.

Az Azure Maps olvasási hozzáférés-vezérlést támogató egyedi az Azure AD felhasználók, alkalmazások és az Azure szolgáltatást a felügyelt identitások használatával az Azure-erőforrásokhoz.

![Az Azure Maps olvasója (minta)](./media/azure-maps-authentication/concept.png)

További információ az RBAC-beállítások megtekintése: [RBAC konfigurálása az Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Az Azure-erőforrások és az Azure Maps felügyelt identitásokból

[Felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure-szolgáltatások (Azure App Service, Azure Functions, Azure Virtual Machines és így tovább), hogy jogosult-e automatikusan felügyelt identitással rendelkezni az Azure Maps-szolgáltatásokhoz való hozzáférés.  

## <a name="next-steps"></a>További lépések

* További információ az Azure AD-alkalmazás hitelesítéséhez és az Azure Maps, lásd: [Azure Maps-kezelés hitelesítési](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Az Azure Maps térkép vezérlőelem és az Azure AD hitelesítése kapcsolatos további információkért lásd: [az Azure Maps térkép vezérlőelem használata](https://aka.ms/amaadmc).