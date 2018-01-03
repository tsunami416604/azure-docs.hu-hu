---
title: "Egy aktív DNS-név áttelepítése az Azure App Service |} Microsoft Docs"
description: "Ismerje meg, hogyan telepíthet át egy egyéni DNS-tartománynév, amely már hozzá van rendelve egy élő webhelyet az Azure App Service állásidő nélkül."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.openlocfilehash: cd04be2046a23901471cb7bd0da9e0ed2d514d0d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Egy aktív DNS-név áttelepítése az Azure App Service

Ez a cikk bemutatja, hogyan telepíthet át egy aktív DNS-név [Azure App Service](../app-service/app-service-web-overview.md) állásidő nélkül.

Áttelepítésekor a élő hely és a DNS-tartománynevet az App Service, a DNS-név már kiszolgál élő forgalmat. Az áttelepítés során az állásidő DNS-feloldás elkerülhető, az aktív DNS-név megelőző jelleggel kötése App Service-alkalmazás.

Ha nem aggódik DNS-feloldás az állásidő, lásd: [egy meglévő egyéni DNS-névvel hozzárendelése az Azure Web Apps](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató befejezése:

- [Győződjön meg arról, hogy az App Service alkalmazás nem ingyenes szint](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>A tartománynév megelőző jelleggel kötése

Az egyéni tartománynév megelőző jelleggel kötését, elérheti a DNS-rekordok módosítása előtt az alábbi:

- Tartomány tulajdonjogának ellenőrzése
- A tartománynév, az alkalmazás engedélyezése

Végül a környezetbe való áttelepítéskor a egyéni DNS-neve a régi webhely az App Service-alkalmazást, lesz állásidő nélkül a DNS-feloldás eredményének.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Tartomány ellenőrzési rekord létrehozása

Ellenőrizze a tartomány tulajdonosa, vegye fel a TXT-rekord. A TXT-rekord a maps _awverify.&lt; altartomány >_ való  _&lt;alkalmazásnév >. azurewebsites.net_. 

A TXT-rekord van szüksége a DNS-rekord, amelyet át szeretne függ. Tekintse meg a következő táblázat (`@` általában jelenti. a legfelső szintű tartomány):

| DNS-rekord példa | TXT-állomás | TXT érték |
| - | - | - |
| @ (root) | _awverify_ | _&lt;alkalmazásnév >. azurewebsites.net_ |
| a webszolgáltatáshoz (rész) | _awverify.www_ | _&lt;alkalmazásnév >. azurewebsites.net_ |
| \*(helyettesítő) | _awverify.\*_ | _&lt;alkalmazásnév >. azurewebsites.net_ |

A DNS-rekordok lapon jegyezze fel a rekordtípust a DNS-név, amelyet át szeretne. App Service leképezéseket a CNAME és A rekordok támogatja.

### <a name="enable-the-domain-for-your-app"></a>A tartomány, az alkalmazás engedélyezése

Az a [Azure-portálon](https://portal.azure.com), válassza ki az alkalmazás oldal bal oldali navigációs **egyéni tartományok**. 

![Az egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az a **egyéni tartományok** lapon jelölje be a  **+**  melletti ikon **állomásnév hozzáadása**.

![Adja hozzá a gazdagép neve](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a teljesen minősített tartománynevét, hogy hozzáadta a TXT-rekord, például a `www.contoso.com`. Egy helyettesítő tartomány (például \*. contoso.com), minden DNS-neve, amely megfelel a helyettesítő tartományt is használhatja. 

Válassza ki **érvényesítése**.

A **állomásnév hozzáadása** gomb aktiválásakor. 

Győződjön meg arról, hogy **állomásnév rekordtípus** értéke a DNS-áttelepíteni kívánt típusát.

Válassza ki **állomásnév hozzáadása**.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Ez eltarthat egy ideig, megjelennek az alkalmazás új gazdagép **egyéni tartományok** lap. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Az egyéni DNS-neve most már engedélyezve van az Azure alkalmazásban. 

## <a name="remap-the-active-dns-name"></a>Adja meg újból az aktív DNS-név

Ehhez csak dolog balra van ismételt leképezését, az aktív DNS-rekord App Service mutasson. Jobb most, még mindig mutat a régi helyről.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Másolja az alkalmazás IP-cím (csak egy a rekordot)

Ha egy CNAME rekordot a rendszer újramegfeleltetése, hagyja ki az ebben a szakaszban. 

Adja meg újból az A rekord, az App Service alkalmazás külső IP-címet, amely megjelenik-e a kell a **egyéni tartományok** lap.

Zárja be a **állomásnév hozzáadása** lap választásával **X** jobb felső sarokban. 

Az a **egyéni tartományok** lapon, az alkalmazás IP-cím másolásához.

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>A DNS-bejegyzésének frissítése

Vissza a tartomány-szolgáltató DNS-rekordok oldalán válassza ki a DNS-rekord adja meg újból.

Az a `contoso.com` : Példa a gyökérkönyvtár, például az alábbi táblázatban szereplő példák A vagy CNAME rekord remap: 

| FQDN-példa | Bejegyzéstípus | Gazdagép | Érték |
| - | - | - | - |
| a contoso.com (root) | A | `@` | IP-cím a [másolja az alkalmazás IP-cím](#info) |
| a www.contoso.com (rész) | CNAME | `www` | _&lt;alkalmazásnév >. azurewebsites.net_ |
| \*. contoso.com (helyettesítő) | CNAME | _\*_ | _&lt;alkalmazásnév >. azurewebsites.net_ |

A beállítások mentéséhez.

DNS-lekérdezések kell kezdődnie, az App Service alkalmazáshoz feloldása után azonnal DNS-propagálás történik.

## <a name="next-steps"></a>További lépések

Tudnivalók az App Service egy egyéni SSL-tanúsítvány kötését.

> [!div class="nextstepaction"]
> [Egy meglévő egyéni SSL-tanúsítvány kötését az Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
