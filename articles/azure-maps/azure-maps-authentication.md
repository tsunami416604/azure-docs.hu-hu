---
title: Hitelesítési módszerek | Microsoft Azure Maps
description: Ebben a cikkben az Azure Active Directory (Azure AD) és a megosztott kulcs hitelesítése. Mindkettő a Microsoft Azure Maps-szolgáltatásokhoz használatos. Ismerje meg, hogyan szerezheti be az Azure Maps előfizetési kulcsát.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335704"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps használatával

Az Azure Maps a kérelmek hitelesítésének két módját támogatja: a megosztott kulcsú hitelesítést és az Azure Active Directory-hitelesítést. Ez a cikk ismerteti ezeket a hitelesítési módszereket, amelyek segítséget nyújtanak az Azure Maps-szolgáltatások megvalósításának irányításához.

> [!NOTE]
> Az Azure Maps szolgáltatással való biztonságos kommunikáció javítása érdekében mostmár támogatjuk a Transport Layer Security (TLS) 1.2-es ( Transport Layer Security) 1.2-es támogatását, és a TLS 1.0 és 1.1 támogatását kivonuljuk. A szolgáltatás megszakításának elkerülése érdekében **frissítse a kiszolgálókat és az alkalmazásokat a TLS 1.2 használatára 2020.**  Ha jelenleg a TLS 1.x-et használja, értékelje ki a TLS 1.2-es készültséget, és dolgozzon ki egy áttelepítési tervet [a TLS 1.0 probléma megoldása](https://docs.microsoft.com/security/solving-tls1-problem)című részben ismertetett teszteléssel.

## <a name="shared-key-authentication"></a>Megosztott kulcs hitelesítése

 Elsődleges és másodlagos kulcsok jönnek létre az Azure Maps-fiók létrehozása után. Javasoljuk, hogy az elsődleges kulcsot használja előfizetési kulcsként, amikor megosztott kulcsos hitelesítéssel hívja az Azure Mapset. A megosztott kulcsú hitelesítés egy Azure Maps-fiók által létrehozott kulcsot ad át egy Azure Maps-szolgáltatásnak. Az Azure Maps-szolgáltatások minden egyes kérelemhez adja hozzá az *előfizetési kulcsot* paraméterként az URL-címhez. A másodlagos kulcs olyan esetekben használható, mint a kulcsváltás.  

A kulcsok Azure Portalon való megtekintéséről a [Hitelesítés kezelése című](https://aka.ms/amauthdetails)témakörben talál további információt.

> [!Tip]
> Javasoljuk, hogy rendszeresen regenerálja a kulcsokat. Két kulcs van ellátva, így az egyik kulccsal kapcsolat maradhat fenn a másik újragenerálása közben. Amikor újragenerálja a kulcsokat, frissítenie kell minden olyan alkalmazást, amely hozzáfér a fiókjához az új kulcsokkal.

## <a name="authentication-with-azure-active-directory-preview"></a>Hitelesítés az Azure Active Directoryval (előzetes verzió)

Az Azure Maps mostantól kéréshitelesítést kínál az Azure Active [Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)használatával. Az Azure AD identitásalapú hitelesítést biztosít, beleértve [a szerepköralapú hozzáférés-vezérlést (RBAC).](https://docs.microsoft.com/azure/role-based-access-control/overview) Az RBAC segítségével felhasználói szintű, csoportszintű vagy alkalmazásszintű hozzáférést biztosít az Azure Maps-erőforrásokhoz. A következő szakaszok az Azure Maps és az Azure AD integrációjának fogalmait és összetevőit ismertetik.

## <a name="authentication-with-oauth-access-tokens"></a>Hitelesítés OAuth hozzáférési jogkivonatokkal

Az Azure Maps elfogadja **az OAuth 2.0** hozzáférési jogkivonatokat az Azure AD-bérlők számára, amelyek egy Azure Maps-fiókot tartalmaznak. Az Azure Maps is elfogadja a jogkivonatokat:

* Azure AD-felhasználók
* A felhasználók által delegált engedélyeket használó partneralkalmazások
* Azure-erőforrások felügyelt identitásai

Az Azure Maps minden Egyes Azure Maps-fiókhoz létrehoz egy *egyedi azonosítót (ügyfélazonosítót).* Az Azure AD-től jogkivonatokat kérhet, ha ezt az ügyfélazonosítót további paraméterekkel kombinálja. Token igényléséhez adja meg az alábbi táblázatban szereplő értékeket az Azure-környezet alapján.

| Azure környezet   | Azure AD token végpont |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Az Azure AD konfigurálásáról és az Azure Maps tokenek igényléséről a [Hitelesítés kezelése az Azure Mapsben](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)című témakörben talál további információt.

A jogkivonatok Azure AD-től való kéréséről a [Mi a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Azure Map-erőforrások kérése OAuth-jogkivonatokkal

Miután az Azure AD jogkivonatot kap, az Azure Maps a következő kérésfejlécekkel küld egy kérést:

| Kérelem fejléce    |    Érték    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc....9f55|
| Engedélyezés     | HordozóeyJ0e....HNIVN |

> [!Note]
> `x-ms-client-id`az Azure Maps-fiókalapú GUID, amely megjelenik az Azure Maps hitelesítési oldalon.

Íme egy példa egy OAuth-jogkivonatot használó Azure Maps-útvonalkérelemre:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Az ügyfélazonosító megtekintéséről a [Hitelesítésrészleteik megtekintése című témakörben](https://aka.ms/amauthdetails)talál további információt.

## <a name="control-access-with-rbac"></a>Hozzáférés szabályozása az RBAC-kal

Az Azure AD-ben az RBAC használatával szabályozhatja a biztonságos erőforrásokhoz való hozzáférést. Állítsa be az Azure Maps-fiókot, és regisztrálja az Azure Maps Azure AD-bérlő. Az Azure Maps az Azure-erőforrások felügyelt identitásain keresztül támogatja az olvasási hozzáférés-vezérlést az egyes Azure AD-felhasználók, csoportok, alkalmazások, Azure-erőforrások és Azure-szolgáltatások számára. Az Azure Maps portálon beállíthat RBAC a kiválasztott szerepkörökhöz.

![Azure Maps adatolvasó (előzetes verzió)](./media/azure-maps-authentication/concept.png)

Az RBAC-beállítások megtekintéséről az [RBAC konfigurálása az Azure Maps szolgáltatáshoz](https://aka.ms/amrbac)című témakörben olvashat.

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Felügyelt identitások az Azure-erőforrásokhoz és az Azure Mapshez

[Az Azure-erőforrások felügyelt identitásai](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) automatikusan felügyelt identitást biztosítanak az Azure-szolgáltatások számára, amely jogosult az Azure Maps-szolgáltatások elérésére. Néhány példa a felügyelt identitások, a következők: Azure App Service, Azure Functions és az Azure virtuális gépek.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne tudni arról, hogy miként hitelesíthet egy alkalmazást az Azure AD-vel és az Azure Mapslel, olvassa el a Hitelesítés kezelése az Azure Mapsben című [témakört.](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

* Az Azure Maps Map Control és az Azure AD hitelesítéséről az [Azure Maps Map Control használata című](https://aka.ms/amaadmc)témakörben olvashat bővebben.
