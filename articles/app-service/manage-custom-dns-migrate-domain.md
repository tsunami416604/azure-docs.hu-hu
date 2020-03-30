---
title: Aktív DNS-név áttelepítése
description: Megtudhatja, hogyan telepítheti át az azure App Service-hez már hozzárendelt egyéni DNS-tartománynevet az Azure App Service-hez állásidő nélkül.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 79bd0a19a9bd8ebd100ed80ca0206656d73ef76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672359"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Aktív DNS-név áttelepítése az Azure App Service szolgáltatásba

Ez a cikk bemutatja, hogyan telepíthet át egy aktív DNS-nevet az [Azure App Service-be](../app-service/overview.md) állásidő nélkül.

Amikor egy élő webhelyet és annak DNS-tartománynevét áttelepíti az App Service szolgáltatásba, az a DNS-név már élő forgalmat szolgál ki. Az áttelepítés során elkerülheti a DNS-feloldás állásidejét, ha az aktív DNS-nevet megelőzően az App Service-alkalmazáshoz köti.

Ha nem aggódik a DNS-feloldásállás leállása miatt, olvassa el [A meglévő egyéni DNS-név hozzárendelése az Azure App Service szolgáltatáshoz](app-service-web-tutorial-custom-domain.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez:

- [Győződjön meg arról, hogy az App Service-alkalmazás nem az ingyenes csomagban van.](app-service-web-tutorial-custom-domain.md#checkpricing)

## <a name="bind-the-domain-name-preemptively"></a>A tartománynév megelőző kötése

Ha megelőző jelleggel köt egy egyéni tartományt, a DNS-rekordok módosítása előtt az alábbi módosításokat hajthatja végre:

- Tartomány tulajdonjogának ellenőrzése
- Az alkalmazás tartománynevének engedélyezése

Amikor végre áttelepíti az egyéni DNS-nevet a régi helyről az App Service alkalmazásba, a DNS-feloldásban nem lesz állásidő.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Tartomány-ellenőrzési rekord létrehozása

A tartomány tulajdonjogának ellenőrzéséhez vegyen fel Egy TXT rekordot. A TXT rekord leképezi _aawverify.&lt; aldomain>_ _ &lt;appname>.azurewebsites.net_. 

A szükséges TXT rekord az áttelepíteni kívánt DNS-rekordtól függ. Példák: az alábbi`@` táblázat (általában a gyökértartományt jelöli):

| Példa DNS-rekordra | TXT állomás | TXT érték |
| - | - | - |
| \@(gyökér) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \*(helyettesítő karakter) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

A DNS-rekordok lapon jegyezze fel az áttelepíteni kívánt DNS-név rekordtípusát. Az App Service támogatja a CNAME és Az A rekordok ból származó hozzárendeléseket.

> [!NOTE]
> Bizonyos szolgáltatók, például a `awverify.*` CloudFlare esetében, nem érvényes rekord. Csak `*` ezt használja helyette.

> [!NOTE]
> A `*` helyettesítő rekordok nem ellenőrzik a meglévő CNAME rekorddal rendelkező altartományokat. Előfordulhat, hogy minden altartományhoz explicit módon létre kell hoznia egy TXT rekordot.


### <a name="enable-the-domain-for-your-app"></a>A tartomány engedélyezése az alkalmazáshoz

Az [Azure Portalon](https://portal.azure.com)válassza az Egyéni tartományok **választógombot**az alkalmazáslap bal oldali navigációs sávján. 

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az **Egyéni tartományok** lapon jelölje **+** ki az **Állomásnév hozzáadása (Add hostname) ikont.**

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be azt a teljesen minősített tartománynevet, amelyhez a TXT rekordot hozzáadta, például `www.contoso.com`. Helyettesítő karakteres tartomány \*(például .contoso.com) esetén bármilyen DNS-nevet használhat, amely megfelel a helyettesítő tartománynak. 

Válassza az **Érvényesítés** lehetőséget.

A **Gazdagépnév hozzáadása** gomb aktívvá válik. 

Győződjön meg arról, hogy az **Állomásnév rekordtípusa** az áttelepíteni kívánt DNS-rekordtípusra van beállítva.

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Eltarthat egy ideig, amíg az új gazdanév megjelenik az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Az egyéni DNS-neve most már engedélyezve van az Azure-alkalmazásban. 

## <a name="remap-the-active-dns-name"></a>Az aktív DNS-név újrahozzárendelése

Az egyetlen dolog maradt hátra, hogy újraleképezze az aktív DNS-rekordot, hogy az App Service-re mutasson. Most, még mindig mutat a régi oldalon.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Az alkalmazás IP-címének másolása (csak rekord)

Ha CNAME rekordot újít le, hagyja ki ezt a szakaszt. 

A rekord újbóli hozzárendeléséhez szüksége van az App Service-alkalmazás külső IP-címére, amely az **Egyéni tartományok** lapon jelenik meg.

Zárja be a **HostName hozzáadása** lapot a jobb felső sarokban az **X** billentyűt választva. 

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>A DNS-rekord frissítése

A tartományszolgáltató DNS-rekordok lapján válassza ki az újraleképezni kívánt DNS-rekordot.

A `contoso.com` legfelső tartomány példában az A vagy cname rekordot az alábbi táblázatban szereplő példákhoz hasonlóan képezze le újra: 

| Példa a teljes tartománynévre | Rekordtípus | Gazdagép | Érték |
| - | - | - | - |
| contoso.com (gyökér) | A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (helyettesítő karakter) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Mentse a beállításokat.

A DNS-lekérdezések feloldása azonnal az App Service-alkalmazással kell kezdődnie, miután a DNS-propagálás megtörtént.

## <a name="active-domain-in-azure"></a>Aktív tartomány az Azure-ban

Áttelepíthet egy aktív egyéni tartományt az Azure-ban, az előfizetések között vagy ugyanazon az előfizetésen belül. Az ilyen, állásidő nélküli áttelepítéshez azonban a forrásalkalmazáshoz és a célalkalmazáshoz egy adott időpontban ugyanaz az egyéni tartomány van hozzárendelve. Ezért meg kell győződnie arról, hogy a két alkalmazás nincs telepítve ugyanabban a központi telepítési egységben (belső nevén webtárhely). A tartománynév minden központi telepítési egységben csak egy alkalmazáshoz rendelhető hozzá.

Az alkalmazás központi telepítési egységét az FTP/S URL-cím `<deployment-unit>.ftp.azurewebsites.windows.net`tartománynevének megtekintésével találhatja meg. Ellenőrizze, hogy a központi telepítési egység különbözik-e a forrásalkalmazás és a célalkalmazás között. Az alkalmazás központi telepítési egységét az [app service-csomag](overview-hosting-plans.md) határozza meg, amelyben van. Az Azure véletlenszerűen választja ki, amikor létrehozza a csomagot, és nem módosítható. Az Azure csak akkor győződjön meg arról, hogy két csomag ugyanabban a központi telepítési egységben van, amikor [ugyanabban az erőforráscsoportban *és* ugyanabban a régióban hozza létre őket,](app-service-plan-manage.md#create-an-app-service-plan)de nincs logika, amely biztosítja, hogy a csomagok különböző központi telepítési egységekben vannak. Az egyetlen módja annak, hogy hozzon létre egy tervet egy másik központi telepítési egység, hogy tartsa létre a tervet egy új erőforráscsoport vagy régió, amíg nem kap egy másik központi telepítési egység.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan köthat egyéni SSL-tanúsítványt az App Service-hez.

> [!div class="nextstepaction"]
> [Ssl-tanúsítvány kötése az Azure App Service szolgáltatáshoz](configure-ssl-bindings.md)
