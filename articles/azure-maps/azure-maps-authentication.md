---
title: Hitelesítés Azure Mapssal | Microsoft Docs
description: Hitelesítés Azure Maps szolgáltatások használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8a6bc8b71f5f8edda76faa1a8d1b20417dfba1d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478656"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps használatával

Azure Maps a kérelmek hitelesítésének két módját támogatja: megosztott kulcs és Azure Active Directory (Azure AD). Ez a cikk ismerteti ezeket a hitelesítési módszereket, amelyek segítenek a megvalósításában.

## <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

A megosztott kulcsos hitelesítés egy Azure Maps fiók által generált kulcsokat ad át, és minden kérést Azure Maps.  A Azure Maps-fiók létrehozásakor két kulcs jön létre. A szolgáltatásokra Azure Maps minden egyes kérelem esetében az előfizetési kulcsot hozzá kell adni paraméterként az URL-címhez.

> [!Tip]
> Javasoljuk, hogy rendszeresen újragenerálja a kulcsokat. A szolgáltatás két kulccsal rendelkezik, így a másikra való újragenerálása közben egyetlen kulccsal kezelheti a kapcsolatokat. A kulcsok újragenerálása után frissítenie kell minden olyan alkalmazást, amely hozzáfér a fiókhoz az új kulcsok használatához.

A kulcsok megtekintésével kapcsolatos további információkért lásd a [hitelesítési adatok megtekintése](https://aka.ms/amauthdetails)című témakört.

## <a name="authentication-with-azure-active-directory-preview"></a>Hitelesítés Azure Active Directory (előzetes verzió)

A Azure Maps mostantól [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) integrációt biztosít a Azure Maps-szolgáltatások iránti kérések hitelesítéséhez. Az Azure AD identitás-alapú hitelesítést biztosít, beleértve a [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), amellyel felhasználói szintű, csoport szintű és alkalmazás-szintű hozzáférést biztosíthat Azure Maps erőforrásokhoz. Az alábbi szakaszokban megismerheti az Azure AD-vel való Azure Maps integrációjának fogalmait és összetevőit.

## <a name="authentication-with-oauth-access-tokens"></a>Hitelesítés OAuth hozzáférési jogkivonatokkal

Azure Maps elfogadja a **OAuth 2,0** hozzáférési jogkivonatokat egy Azure Maps-fiókot tartalmazó Azure-előfizetéshez társított Azure ad-bérlők számára. Azure Maps a következő jogkivonatokat fogadja el:

* Azure AD-felhasználók. 
* A felhasználók által delegált engedélyeket használó partneri alkalmazások.
* Felügyelt identitások az Azure-erőforrásokhoz.

A Azure Maps minden Azure Maps-fiókhoz létrehoz egy *egyedi azonosítót (ügyfél-azonosítót)* . Ha ezt az ügyfél-azonosítót további paraméterekkel kombinálja, a tokeneket az Azure AD-től kérheti az Azure-környezettől függően az alábbi táblázatban szereplő értékek megadásával.

| Azure-környezet   | Azure AD-jogkivonat végpontja |
| --------------------|-------------------------|
| Nyilvános Azure        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


További információ az Azure AD konfigurálásáról és a Azure Maps jogkivonatok igényléséről: [a Azure Maps hitelesítésének kezelése](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Az Azure AD-jogkivonatok igénylésével kapcsolatos általános információkért lásd: [Mi a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Azure Map-erőforrások igénylése OAuth-tokenekkel

Miután az Azure AD-ben kapott tokent, a következő két szükséges kérelem-fejléctel rendelkező kérelem elküldhető Azure Mapsra:

| Kérelem fejléce    |    Érték    |
|:------------------|:------------|
| x-MS-Client-ID    | 30d7cc....9f55|
| Engedélyezés     | Tulajdonos eyJ0e.... HNIVN |

> [!Note]
> `x-ms-client-id` az Azure Maps Authentication lapon megjelenő Azure Maps fiók-alapú GUID.

Az alábbi példa egy olyan Azure Maps Route-kérelemre mutat, amely egy OAuth-tokent használ:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Az ügyfél-azonosító megtekintésével kapcsolatos információkért lásd a [hitelesítési adatok megtekintése](https://aka.ms/amauthdetails)című témakört.

## <a name="control-access-with-rbac"></a>Hozzáférés vezérlése a RBAC

Az Azure AD lehetővé teszi, hogy a RBAC használatával szabályozza a biztonságos erőforrásokhoz való hozzáférést. Miután létrehozta Azure Maps-fiókját, és regisztrálja az Azure Maps Azure AD-alkalmazást az Azure AD-bérlőn belül, beállíthatja a felhasználók, csoportok, alkalmazások vagy Azure-erőforrások RBAC a Azure Maps-fiók portál lapján.

A Azure Maps támogatja az Azure AD-felhasználók,-csoportok,-alkalmazások és az Azure-szolgáltatások olvasási hozzáférés-vezérlését az Azure-erőforrások felügyelt identitásai révén.

![Azure Maps Adatolvasó (előzetes verzió)](./media/azure-maps-authentication/concept.png)

A RBAC-beállítások megtekintésével kapcsolatos további információkért lásd: [RBAC konfigurálása a Azure Mapshoz](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Felügyelt identitások az Azure-erőforrásokhoz és a Azure Maps

Az [Azure-erőforrásokhoz felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) biztosítják az Azure-szolgáltatásokat (Azure App Service, Azure functions, Azure Virtual Machines stb.) egy automatikusan felügyelt identitással, amely jogosult a Azure Maps-szolgáltatásokhoz való hozzáférésre.  

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az alkalmazások Azure AD-vel és Azure Maps-vel történő hitelesítésével kapcsolatban, tekintse meg a következő témakört: a [hitelesítés kezelése Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

* Ha többet szeretne megtudni a Azure Maps térképkezelés és az Azure AD hitelesítéséről, tekintse meg [a Azure Maps Térképkezelés használatát](https://aka.ms/amaadmc)ismertető témakört.