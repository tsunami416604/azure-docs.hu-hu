---
title: Hitelesítési módszerek | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, Azure Active Directory (Azure AD) és a megosztott kulcsos hitelesítést. Mindkettő a Microsoft Azure Maps-szolgáltatásokhoz használatos. Útmutató Azure Maps előfizetési kulcs beszerzéséhez.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ee8b166077c64ae5e0f2cce18ee0bc77e8c996f4
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210123"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps használatával

Azure Maps a kérelmek hitelesítésének két módját támogatja: megosztott kulcsos hitelesítés és Azure Active Directory hitelesítés. Ez a cikk ezeket a hitelesítési módszereket ismerteti Azure Maps szolgáltatások megvalósításának elősegítése érdekében.

## <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

 Az elsődleges és a másodlagos kulcsok a Azure Maps fiók létrehozása után jönnek létre. Javasoljuk, hogy a megosztott kulcsos hitelesítés használatával Azure Maps meghívásakor az elsődleges kulcsot használja előfizetési kulcsként. A megosztott kulcsos hitelesítés egy Azure Maps fiók által generált kulcsot ad át egy Azure Maps szolgáltatáshoz. A szolgáltatásokra Azure Maps minden egyes kérelemhez adja hozzá az *előfizetési kulcsot* paraméterként az URL-címhez. A másodlagos kulcsot olyan helyzetekben lehet használni, mint például a kulcsok változásai.  

A kulcsok a Azure Portalban való megtekintésével kapcsolatos információkért lásd: a [hitelesítés kezelése](https://aka.ms/amauthdetails).

> [!Tip]
> Javasoljuk, hogy rendszeresen újragenerálja a kulcsokat. Két kulccsal van ellátva, így a kapcsolatok egy kulccsal kezelhetők, miközben újragenerálja a másikat. A kulcsok újragenerálása után frissítenie kell minden olyan alkalmazást, amely hozzáfér a fiókjához az új kulcsokkal.

## <a name="authentication-with-azure-active-directory-preview"></a>Hitelesítés Azure Active Directory (előzetes verzió)

A Azure Maps mostantól a [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)használatával Azure Maps szolgáltatásokhoz történő hitelesítést kér. Az Azure AD identitás-alapú hitelesítést biztosít, beleértve a [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). A RBAC felhasználói szintű, csoport szintű vagy alkalmazás-szintű hozzáférést biztosít a Azure Maps erőforrásaihoz. A következő részekben a Azure Maps integrációjának az Azure AD-val való integrálásával kapcsolatos fogalmakat és összetevőket tárgyaljuk.

## <a name="authentication-with-oauth-access-tokens"></a>Hitelesítés OAuth hozzáférési jogkivonatokkal

Azure Maps elfogadja a **OAuth 2,0** hozzáférési jogkivonatokat egy Azure Maps-fiókot tartalmazó Azure-előfizetéshez társított Azure ad-bérlők számára. A Azure Maps a következő jogkivonatokat is elfogadja:

* Azure AD-felhasználók
* A felhasználók által delegált engedélyeket használó partneri alkalmazások
* Azure-erőforrások felügyelt identitásai

A Azure Maps minden Azure Maps-fiókhoz létrehoz egy *egyedi azonosítót (ügyfél-azonosítót)* . Ha az ügyfél-azonosítót további paraméterekkel kombinálja, kérhet tokeneket az Azure AD-től. Jogkivonat igényléséhez adja meg az alábbi táblázatban szereplő értékeket az Azure-környezet alapján.

| Azure-környezet   | Azure AD-jogkivonat végpontja |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


További információ az Azure AD konfigurálásáról és a Azure Maps jogkivonatok igényléséről: [a Azure Maps hitelesítésének kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Az Azure AD-jogkivonatok igénylésével kapcsolatos általános információkért lásd: [Mi a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Azure Map-erőforrások igénylése OAuth-tokenekkel

Miután az Azure AD jogkivonatot kap, Azure Maps egy kérést küld a következő kötelező kérelmek fejlécével:

| Kérelem fejléce    |    Érték    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
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

Az Azure AD-ben a RBAC használatával vezérelheti a biztonságos erőforrásokhoz való hozzáférést. Állítsa be Azure Maps-fiókját, és regisztrálja az Azure Maps Azure AD-bérlőt. A Azure Maps támogatja az Azure AD-felhasználók,-csoportok,-alkalmazások, Azure-erőforrások és Azure-szolgáltatások olvasási hozzáférés-vezérlését az Azure-erőforrások felügyelt identitásai révén. A Azure Maps-portál lapon beállíthatja a kiválasztott szerepkörökhöz tartozó RBAC.

![Azure Maps Adatolvasó (előzetes verzió)](./media/azure-maps-authentication/concept.png)

A RBAC-beállítások megtekintésével kapcsolatos további információkért lásd: [RBAC konfigurálása a Azure Mapshoz](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Felügyelt identitások az Azure-erőforrásokhoz és a Azure Maps

Az [Azure-erőforrások felügyelt identitásai](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) automatikusan felügyelt identitással biztosítanak Azure-szolgáltatásokat, amely jogosult a Azure Maps-szolgáltatások elérésére. Néhány példa a felügyelt identitásokra: Azure App Service, Azure Functions és Azure Virtual Machines.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni az alkalmazások Azure AD-vel és Azure Maps-vel történő hitelesítésével kapcsolatban, tekintse meg a következő témakört: a [hitelesítés kezelése Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

* Ha többet szeretne megtudni a Azure Maps térképkezelés és az Azure AD hitelesítéséről, tekintse meg [a Azure Maps Térképkezelés használatát](https://aka.ms/amaadmc)ismertető témakört.
