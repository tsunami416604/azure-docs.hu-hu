---
title: Aktív DNS-név migrálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan telepíthet át egy élő webhelyhez már hozzárendelt egyéni DNS-tartománynevet állásidő nélkül Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 703a151f801f65b968ecf93eaa97640c22a71bd2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073091"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Aktív DNS-név átmigrálása Azure App Service

Ez a cikk bemutatja, hogyan telepíthet át egy aktív DNS-nevet [Azure app Service](../app-service/overview.md) leállás nélkül.

Ha élő helyet és DNS-tartománynevet App Servicere telepít át, akkor a DNS-név már élő forgalmat fog szolgálni. Az áttelepítés során elkerülheti az állásidőt a DNS-feloldás során, ha az aktív DNS-nevet a App Service alkalmazás megelőző jelleggel köti.

Ha nem aggódik a DNS-feloldási állásidővel kapcsolatban, tekintse [meg a meglévő egyéni DNS-név leképezése Azure app Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Előfeltételek

A útmutató elvégzéséhez:

- Győződjön [meg arról, hogy a app Service alkalmazás nem ingyenes szintű](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Megelőző jelleggel tartománynév kötése

Egyéni tartományi megelőző jelleggel kötése esetén a következő műveleteket hajthatja végre a DNS-rekordok módosítása előtt:

- Tartomány tulajdonjogának ellenőrzése
- Az alkalmazás tartománynevének engedélyezése

Ha végül áttelepíti az egyéni DNS-nevet a régi helyről a App Service alkalmazásba, a DNS-feloldás nem lesz leállás.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Tartomány-ellenőrzési rekord létrehozása

A tartomány tulajdonjogának ellenőrzéséhez adjon hozzá egy TXT-rekordot. A TXT rekord leképezi a _awverify.&lt; altartomány >_  _&lt;AppName >. azurewebsites. net_. 

A szükséges TXT-rekord az áttelepíteni kívánt DNS-rekordtól függ. Példákat a következő táblázat tartalmaz (`@` általában a legfelső szintű tartományt jelenti):

| Példa DNS-rekordra | TXT-gazdagép | TXT-érték |
| - | - | - |
| \@legfelső szintű | _awverify_ | _&lt;AppName >. azurewebsites. net_ |
| www (Sub) | _awverify.www_ | _&lt;AppName >. azurewebsites. net_ |
| \*helyettesítő | _awverify.\*_ | _&lt;AppName >. azurewebsites. net_ |

A DNS-rekordok oldalon jegyezze fel az áttelepíteni kívánt DNS-név bejegyzéstípusát. A App Service támogatja a CNAME és A rekordok leképezését.

> [!NOTE]
> Bizonyos szolgáltatók (például a cloudflare `awverify.*` ) nem érvényes rekordok. Csak `*` Ehelyett használja.

> [!NOTE]
> A `*` helyettesítő rekordok nem ellenőrzik az altartományokat egy meglévő CNAME rekordkal. You may need to explicitly create a TXT record for each subdomain.


### <a name="enable-the-domain-for-your-app"></a>Az alkalmazás tartományának engedélyezése

A [Azure Portal](https://portal.azure.com)az alkalmazás lap bal oldali navigációs sávján válassza az **Egyéni tartományok**elemet. 

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az **Egyéni tartományok** lapon jelölje be az **+** **állomásnév hozzáadása**jelölőnégyzetet.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a TXT- `www.contoso.com`rekordhoz hozzáadott teljes tartománynevet (például). Helyettesítő karakteres tartományhoz ( \*például. contoso.com) bármilyen DNS-nevet használhat, amely megfelel a helyettesítő karakteres tartománynak. 

Válassza az **Érvényesítés** lehetőséget.

A **Gazdagépnév hozzáadása** gomb aktívvá válik. 

Győződjön meg arról, hogy az **állomásnév bejegyzéstípus** az áttelepíteni kívánt DNS-bejegyzéstípusra van beállítva.

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Eltarthat egy ideig, amíg az új gazdanév megjelenik az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Az egyéni DNS-név mostantól engedélyezve van az Azure-alkalmazásban. 

## <a name="remap-the-active-dns-name"></a>Az aktív DNS-név újratársítása

Az egyetlen teendő, hogy átirányítja az aktív DNS-rekordot, hogy App Service mutasson. Most még mindig a régi helyre mutat.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Az alkalmazás IP-címének másolása (csak rekord)

If you are remapping a CNAME record, skip this section. 

Egy rekord átadásához szüksége lesz a App Service alkalmazás külső IP-címére, amely az **Egyéni tartományok** lapon látható.

Az **állomásnév hozzáadása** oldal bezárásához kattintson a jobb felső sarokban található **X** elemre. 

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>A DNS-rekord frissítése

A tartományi szolgáltató DNS-rekordok lapján válassza ki a felvenni kívánt DNS-rekordot.

`contoso.com` A legfelső szintű tartományhoz például a következő táblázatban szereplő példákhoz hasonló módon kell felvennie az a vagy a CNAME rekordot: 

| Példa FQDN-re | Rekordtípus | Gazdagép | Value |
| - | - | - | - |
| contoso.com (root) | J | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| www\.-contoso.com (Sub) | CNAME | `www` | _&lt;AppName >. azurewebsites. net_ |
| \*. contoso.com (helyettesítő karakter) | CNAME | _\*_ | _&lt;AppName >. azurewebsites. net_ |

Mentse a beállításokat.

A DNS-lekérdezések a DNS-propagálás megkezdése után azonnal megoldják a App Service alkalmazás feloldását.

## <a name="next-steps"></a>További lépések

Útmutató egyéni SSL-tanúsítvány kötéséhez App Servicehoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni SSL-tanúsítvány kötése az Azure App Service-hez](app-service-web-tutorial-custom-ssl.md)
