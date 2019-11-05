---
title: Aktív DNS-név migrálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan telepíthet át egy élő webhelyhez már hozzárendelt egyéni DNS-tartománynevet állásidő nélkül Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 172003b13807720df2431a3610947b36d8303fed
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470364"
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

A tartomány tulajdonjogának ellenőrzéséhez adjon hozzá egy TXT-rekordot. A TXT rekord a _awverify.&lt;altartományból >_ , hogy _&lt;AppName >. azurewebsites. net_. 

A szükséges TXT-rekord az áttelepíteni kívánt DNS-rekordtól függ. Példákat a következő táblázat tartalmaz (`@` általában a legfelső szintű tartományt jelöli):

| Példa DNS-rekordra | TXT-gazdagép | TXT-érték |
| - | - | - |
| \@ (root) | _awverify_ | _&lt;AppName >. azurewebsites. net_ |
| www (Sub) | _awverify. www_ | _&lt;AppName >. azurewebsites. net_ |
| \* (helyettesítő karakter) | _awverify.\*_ | _&lt;AppName >. azurewebsites. net_ |

A DNS-rekordok oldalon jegyezze fel az áttelepíteni kívánt DNS-név bejegyzéstípusát. A App Service támogatja a CNAME és A rekordok leképezését.

> [!NOTE]
> Bizonyos szolgáltatók (például a CloudFlare) esetében a `awverify.*` nem érvényes rekord. Ehelyett csak `*` használjon.

> [!NOTE]
> A helyettesítő karakterek `*` rekordok nem ellenőrzik az altartományokat egy meglévő CNAME rekordkal. Előfordulhat, hogy explicit módon létre kell hoznia egy TXT-rekordot mindegyik altartományhoz.


### <a name="enable-the-domain-for-your-app"></a>Az alkalmazás tartományának engedélyezése

A [Azure Portal](https://portal.azure.com)az alkalmazás lap bal oldali navigációs sávján válassza az **Egyéni tartományok**elemet. 

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az **Egyéni tartományok** lapon válassza az **állomásnév hozzáadása**elem melletti **+** ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a TXT-rekordhoz hozzáadott teljes tartománynevet (például `www.contoso.com`). Helyettesítő karakteres tartományhoz (például \*. contoso.com) bármilyen DNS-nevet használhat, amely megfelel a helyettesítő karakteres tartománynak. 

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

Ha egy CNAME rekordot újra hozzárendel, ugorja át ezt a szakaszt. 

Egy rekord átadásához szüksége lesz a App Service alkalmazás külső IP-címére, amely az **Egyéni tartományok** lapon látható.

Az **állomásnév hozzáadása** oldal bezárásához kattintson a jobb felső sarokban található **X** elemre. 

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>A DNS-rekord frissítése

A tartományi szolgáltató DNS-rekordok lapján válassza ki a felvenni kívánt DNS-rekordot.

A `contoso.com` gyökértartomány esetében például a következő táblázatban szereplő példáknak megfelelően a (z) vagy a CNAME rekord újratársítása: 

| Példa FQDN-re | Rekordtípus | Gazdagép | Érték |
| - | - | - | - |
| contoso.com (root) | A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| www\.contoso.com (Sub) | CNAME | `www` | _&lt;AppName >. azurewebsites. net_ |
| \*. contoso.com (helyettesítő karakter) | CNAME | _\*_ | _&lt;AppName >. azurewebsites. net_ |

Mentse a beállításokat.

A DNS-lekérdezések a DNS-propagálás megkezdése után azonnal megoldják a App Service alkalmazás feloldását.

## <a name="active-domain-in-azure"></a>Aktív tartomány az Azure-ban

Áttelepítheti az aktív egyéni tartományt az Azure-ban, előfizetések között vagy ugyanazon az előfizetésen belül. A leállás nélküli áttelepítés esetében azonban a forrásoldali alkalmazásnak és a célként megadott alkalmazásnak egy adott időpontban ugyanahhoz az egyéni tartományhoz kell rendelnie. Ezért győződjön meg arról, hogy a két alkalmazás nincs telepítve ugyanarra a központi telepítési egységre (belső nevén webtárhely). A tartománynév csak egy alkalmazáshoz rendelhető hozzá minden egyes telepítési egységben.

Az alkalmazás üzembe helyezési egységét az FTP/S URL-cím `<deployment-unit>.ftp.azurewebsites.windows.net`jának tartománynevével tekintheti meg. Ellenőrizze, hogy a telepítési egység különbözik-e a forrásoldali alkalmazás és a cél alkalmazás között. Az alkalmazás központi telepítési egységét az [app Service-csomag](overview-hosting-plans.md) határozza meg. Az Azure véletlenszerűen választja ki a csomag létrehozásakor, és nem módosítható. Az Azure-ban csak két csomag van ugyanabban a telepítési egységben, amikor [ugyanabban az erőforráscsoportban *és* ugyanabban a régióban hozza létre őket](app-service-plan-manage.md#create-an-app-service-plan), de nem rendelkezik logikai értékkel, hogy a csomagok különböző üzembe helyezési egységekben legyenek. Az egyetlen módja, ha egy másik üzembe helyezési egységben is létrehoz egy csomagot, hogy egy új erőforráscsoport vagy régió számára hozzon létre egy csomagot, amíg egy másik üzembe helyezési egységet nem kap.

## <a name="next-steps"></a>További lépések

Útmutató egyéni SSL-tanúsítvány kötéséhez App Servicehoz.

> [!div class="nextstepaction"]
> [SSL-tanúsítvány kötése Azure App Service](configure-ssl-bindings.md)
