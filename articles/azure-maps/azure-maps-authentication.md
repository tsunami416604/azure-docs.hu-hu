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
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119273"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps szolgáltatással

Az Azure Maps-kérések hitelesítéséhez kétféleképpen támogatja. Megosztott kulcs vagy Azure Active Directory (Azure AD-) ajánlat különböző módszerek minden Azure Maps küldött kérelem engedélyezéséhez. Ez a cikk az a célja, hogy mindkét hitelesítési módszereket, és segít a hitelesítés végrehajtását ismertetik.

## <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

Az Azure Maps létrehozott fiók kulcsok minden egyes kérelemmel továbbítja az Azure Maps megosztott kulcsos hitelesítés támaszkodik.  Két kulcsot az Azure Maps-fiók létrehozásakor jönnek létre.  Az Azure Maps-szolgáltatások minden kérelemhez paraméterként az URL-címet adni az előfizetési kulcs szükséges.

> [!Tip]
> Azt javasoljuk, hogy a kulcsok rendszeres újragenerálása. Két kulcs állnak rendelkezésre, így akkor is fenntartható a kapcsolatokat, amíg az egyiket egy kulcs használatával. A kulcsok újragenerálásakor, frissítenie kell minden olyan alkalmazást, hogy ez a fiók új kulcsok használatához.

A kulcsok megtekintéséhez lásd: [hitelesítés részletei](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Hitelesítés az Azure Active Directoryval (előzetes verzió)

Az Azure Maps kínál [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) integrációs hitelesítéséhez az Azure Maps szolgáltatásokra irányuló kérelmeket.  Az Azure AD biztosítja, hogy azonosítsa-alapú hitelesítés beleértve [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) felhasználó vagy alkalmazás szintű hozzáférést az Azure Maps-erőforrások. Ez a cikk célja segítségével megismerheti a fogalmak és az Azure Maps az Azure AD-integrációs összetevői.

## <a name="authentication-with-oauth-access-tokens"></a>Hitelesítés az OAuth hozzáférési tokenek

Az Azure Maps fogad **OAuth 2.0** hozzáférési jogkivonatokat az Azure-előfizetést, amely tartalmazza az Azure Maps-fiók társított Azure AD-bérlőt.  Az Azure Maps a jogkivonatokat fogad el:

* Az Azure AD-felhasználók 
* Harmadik féltől származó alkalmazások felhasználók delegált engedélyek használatával
* Azure-erőforrások felügyelt identitásai

Az Azure Maps-hoz létre egy `unique identifier (client ID)` minden az Azure Maps-fiók.  Ügyfél-azonosító van kombinálva további paraméterek, jogkivonatokat kérhet az Azure ad-ből az alábbi érték megadásával:

```
https://login.microsoftonline.com
```
Konfigurálja az Azure AD és a jogkivonatok kérése az Azure Maps vonatkozó további információkért lásd: [hogyan való kezeléséhez hitelesítést](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Az Azure AD-ből kérő jogkivonatok általános információkért lásd: [az Azure Active Directory hitelesítési alapok](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Az OAuth-jogkivonatok Azure-térképekhez erőforrások kérése

Egy tokent az Azure AD-ből igényelve, miután egy kérelmet lehet küldeni az Azure Maps kulcstartón a következő két szükséges kérelem fejlécei:

| Kérelem fejléce    |    Érték    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Engedélyezés     | Tulajdonosi eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id` az Azure Maps-fiók az Azure Maps hitelesítési lapon jelenik meg GUID alapján

Alább az Azure Maps útvonal kérelem például OAuth-jogkivonat használatával:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Az ügyfél-azonosító, tekintse meg [hitelesítés részletei](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>A szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés vezérlése

Egyik legfőbb jellemzője az Azure AD RBAC segítségével védett erőforrásokhoz való hozzáférés vezérli. Miután az Azure Maps-fiók létrejön, és az Azure Maps az Azure AD-alkalmazás regisztrálta az Azure AD-bérlő is most már tudja egy felhasználó, alkalmazás vagy Azure-erőforrás Azure-térképekhez fiók portál oldalán tartozó RBAC konfigurálása az. 

Az Azure Maps jelenleg támogatja az egyéni olvasási hozzáférés-vezérlés az Azure AD-felhasználók, alkalmazások vagy az Azure szolgáltatást, az Azure-erőforrások felügyelt identitások használatával.

![Koncepció](./media/azure-maps-authentication/concept.png)

Az RBAC-beállítások megtekintése: [útmutató tartozó RBAC konfigurálása az Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Az Azure-erőforrások és az Azure Maps felügyelt identitásokból

[Felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) adja meg az Azure-szolgáltatások (Azure App service, Azure Functions, a virtuális gépek, stb.), hogy jogosult-e az Azure Maps-szolgáltatásokhoz való hozzáférés automatikusan felügyelt identitással.  

## <a name="next-steps"></a>További lépések

* További információ az Azure AD-alkalmazás hitelesítéséhez és az Azure Maps, lásd: [hogyan való kezeléséhez hitelesítést](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Az Azure Maps, a térkép vezérlőelem és az Azure AD hitelesítése kapcsolatos további információkért lásd: [az Azure AD és az Azure Maps térkép vezérlőelem](https://aka.ms/amaadmc).