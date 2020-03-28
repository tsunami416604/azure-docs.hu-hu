---
title: 'Oktatóanyag: WAF-szabályzat létrehozása az Azure CDN-hez – Azure portal'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy webalkalmazás-tűzfal (WAF) szabályzatot az Azure CDN-en az Azure Portalhasználatával.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485593"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Oktatóanyag: Hozzon létre egy WAF-szabályzatot az Azure CDN-en az Azure Portalon

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy egyszerű Azure Web Application Firewall (WAF) szabályzatot, és alkalmazhatja azt egy végpontra az Azure Content Delivery Network (CDN) rendszerében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAF-házirend létrehozása
> * CDN-végponthoz társítva
> * WAF-szabályok konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy Azure CDN-profilt és végpontot a rövid útmutató utasításainak [követésével: Hozzon létre egy Azure CDN-profilt és végpontot.](../../cdn/cdn-create-new-endpoint.md) 

## <a name="create-a-web-application-firewall-policy"></a>Webalkalmazás-tűzfal házirend létrehozása

Először hozzon létre egy alapszintű WAF-házirendet egy felügyelt alapértelmezett szabálykészlettel (DRS) a portál használatával.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása**>**waf-keresés** lehetőséget,>válassza a **Webalkalmazás tűzfalát,** > válassza a **Létrehozás lehetőséget.**
2. A **Waf-házirend létrehozása** lap **Alapjai** lapján adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Házirend            |Válassza ki az Azure CDN (előzetes verzió).|
    | Előfizetés            |Válassza ki a Bejárati ajtó előfizetés nevét.|
    | Erőforráscsoport          |Válassza ki a bejárati ajtó erőforráscsoport nevét.|
    | Házirend neve             |Adja meg a WAF-házirend egyedi nevét.|

   ![WAF-házirend létrehozása](../media/waf-cdn-create-portal/basic.png)

3. A **Waf-házirend létrehozása** lap **Társítás** lapján válassza a **CDN-végpont hozzáadása**lehetőséget, adja meg a következő beállításokat, majd válassza a **Hozzáadás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | CDN-profil              | Válassza ki a CDN-profil nevét.|
    | Végpont           | Jelölje ki a végpont nevét, majd kattintson a **Hozzáadás gombra.**|
    
    > [!NOTE]
    > Ha a végpont waf-házirendhez van társítva, akkor szürkén jelenik meg. Először el kell távolítania a végpontot a társított házirendből, majd újra társítania kell a végpontot egy új WAF-házirendhez.
1. Válassza **a Véleményezés + létrehozás**lehetőséget, majd a **Létrehozás lehetőséget.**

## <a name="configure-web-application-firewall-policy-optional"></a>Webalkalmazás-tűzfal házirend konfigurálása (nem kötelező)

### <a name="change-mode"></a>Mód váltása

Alapértelmezés szerint a WAF-házirend *észlelési* módban van, amikor WAF-házirendet hoz létre. *Észlelési* módban a WAF nem blokkolja a kérelmeket. Ehelyett a WAF-szabályoknak megfelelő kérelmeket a WAF-naplók naplózzák.

A WAF működés közbeni megtekintéséhez módosíthatja a módbeállításokat *észlelésről* *megelőzésre.* *Megelőzés módban* az alapértelmezett szabálykészletben (DRS) meghatározott szabályoknak megfelelő kérelmeket a WAF-naplók blokkolják és naplózzák.

 ![WAF-házirend üzemmódjának módosítása](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az **Egyéni szabály hozzáadása** lehetőséget az Egyéni **szabályok** szakaszban. Ezzel megnyitja az egyéni szabály konfigurációs lapját. Az egyéni szabályoknak két típusa van: **egyezési szabály** és **sebességkorlát** szabály.

A következő képernyőképen egy egyéni egyezési szabály látható, amely blokkolja a kérést, ha a lekérdezési karakterlánc tartalmazza az **értékblockme**értéket.

![WAF-házirend üzemmódjának módosítása](../media/waf-cdn-create-portal/custommatch.png)

A díjkorlát-szabályokhoz két további mező szükséges: **A díjkorlát időtartama** és **a díjkorlát küszöbértéke (kérések)** a következő példában látható módon:

![WAF-házirend üzemmódjának módosítása](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészlet (DRS)

Az Azure által felügyelt alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van. Ha egy szabálycsoporton belül le szeretne tiltani egy szabályt, bontsa ki a szabálycsoporton belüli szabályokat, jelölje be a szabályszám előtti jelölőnégyzetet, és a fenti lapon válassza a **Letiltás** lehetőséget. Ha módosítani szeretné a szabálykészleten belüli egyes szabályok művelettípusait, jelölje be a szabályszám előtti jelölőnégyzetet, majd a fenti **Művelet módosítása** lapot.

 ![WAF szabálykészlet módosítása](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure webalkalmazás-tűzfalról](../overview.md)
