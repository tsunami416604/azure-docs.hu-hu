---
title: Aktív DNS-név migrálása az Azure App Service |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet át egyéni DNS-tartománynév, amely az Azure App Service-állásidő nélkül élő webhely már hozzá van rendelve.
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
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.openlocfilehash: a5d031622103183fa9aa7a3f3771a055fc16edb2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049976"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Aktív DNS-név migrálása az Azure App Service-ben

Ez a cikk bemutatja, hogyan telepíthet át egy aktív DNS-név [Azure App Service](../app-service/app-service-web-overview.md) állásidő nélkül.

Élő webhely és a DNS-tartománynév migrálása az App Service-, ha a DNS-név már van élő forgalmat bonyolító. Az áttelepítés során a DNS-feloldás állásidő elkerülhető, kötelező érvényű előrelátó módon aktív DNS-nevét az App Service-alkalmazást.

Ha Ön nem tudja a DNS-feloldás állásidő kapcsolatos, tekintse meg [meglévő egyéni DNS-név leképezése az Azure Web Apps](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához:

- [Győződjön meg arról, hogy az App Service-alkalmazást nem az ingyenes szinten](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>A tartománynév előrelátó módon kötése

Egyéni tartomány előrelátó módon kötni, elvégezni a DNS-rekordok módosítása előtt az alábbi:

- Tartomány tulajdonjogának ellenőrzése
- A tartomány nevét az alkalmazás engedélyezése

Végül pedig az egyéni DNS-nevet a régi helyről való migráláskor az App Service-alkalmazás, leállás nélkül az kell DNS-feloldás.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Tartomány-ellenőrzési rekord létrehozása

Tartomány tulajdonjogának ellenőrzéséhez adjon hozzá egy txt típusú rekordot. A TXT-rekord leképezi a _awverify.&lt; altartomány >_ való  _&lt;alkalmazásnév >. azurewebsites.net_. 

A TXT-rekordot kell attól függ, hogy a migrálni kívánt DNS-rekord. Példákért lásd a következő táblázat (`@` általában a gyökértartományt jelöli):

| DNS-rekord példa | Txt típusú gazdagép | Txt típusú érték |
| - | - | - |
| \@ (root) | _awverify_ | _&lt;alkalmazásnév >. azurewebsites.net_ |
| www (al) | _awverify.www_ | _&lt;alkalmazásnév >. azurewebsites.net_ |
| \* (helyettesítő karakter) | _awverify.\*_ | _&lt;alkalmazásnév >. azurewebsites.net_ |

Jegyezze fel a DNS-rekordok oldala a DNS-név, amelyeket szeretné áttelepíteni a rekord típusa. Az App Service támogatja a leképezéseket a CNAME és a egy a rekordot.

### <a name="enable-the-domain-for-your-app"></a>A tartomány az alkalmazás engedélyezése

Az a [az Azure portal](https://portal.azure.com), az oldal bal oldali navigációs sávján válassza **egyéni tartományok**. 

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az a **egyéni tartományok** lapon válassza ki a **+** melletti ikon **gazdagépnév hozzáadása**.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a teljesen minősített tartománynevét, amelyhez hozzáadta a TXT-rekordot, például `www.contoso.com`. Egy helyettesítő karaktert tartalmazó tartomány (például \*. contoso.com), használhat bármilyen DNS-név, amely megfelel a helyettesítő karaktert tartalmazó tartomány. 

Válassza az **Érvényesítés** lehetőséget.

A **Gazdagépnév hozzáadása** gomb aktívvá válik. 

Győződjön meg arról, hogy **gazdagépnév rekordtípusa** a migrálni kívánt DNS-rekordtípus értékre van állítva.

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Eltarthat egy ideig, amíg az új gazdanév megjelenik az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Az egyéni DNS-név engedélyezve van az Azure alkalmazásban. 

## <a name="remap-the-active-dns-name"></a>Az aktív DNS-név újramegfeleltetése

Ehhez a csak egy dolog maradt rendszer újramegfeleltetése aktív DNS-rekordját, mutasson az App Service-ben. Jobb, továbbra is mutat a régi webhelyre.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Másolja az alkalmazás IP-címet (csak egy a rekordot)

Ha egy CNAME-rekordot rendszer újramegfeleltetése, kihagyhatja ezt a szakaszt. 

Az App Service-alkalmazás külső IP-cím, amely jelenik meg kell újramegfeleltetése egy A rekordot, a **egyéni tartományok** lapot.

Zárja be a **gazdagépnév hozzáadása** lap választásával **X** jobb felső sarokban. 

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>A DNS-rekord frissítése

Vissza a tartományszolgáltatója DNS-rekordok oldala válassza ki a DNS-rekord újramegfeleltetése.

Az a `contoso.com` legfelső szintű tartomány példájában, például az alábbi táblázatban szereplő példák a vagy CNAME rekord újramegfeleltetése: 

| FQDN-példa | Rekordtípus | Gazdagép | Érték |
| - | - | - | - |
| a contoso.com (root) | A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| www.contoso.com (al) | CNAME | `www` | _&lt;alkalmazásnév >. azurewebsites.net_ |
| \*. contoso.com (helyettesítő karakter) | CNAME | _\*_ | _&lt;alkalmazásnév >. azurewebsites.net_ |

A beállítások mentéséhez.

DNS-lekérdezések el kell indulnia, közvetlenül az után történik, DNS-propagálás oldja fel az App Service-alkalmazást.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan köthet egyéni SSL-tanúsítvány App Service-ben.

> [!div class="nextstepaction"]
> [Meglévő egyéni SSL-tanúsítvány kötése az Azure Web Appshez](app-service-web-tutorial-custom-ssl.md)
