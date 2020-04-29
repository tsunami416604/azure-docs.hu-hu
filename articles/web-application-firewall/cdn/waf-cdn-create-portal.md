---
title: 'Oktatóanyag: a Azure CDN-Azure Portal WAF szabályzatának létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure CDN webalkalmazási tűzfal (WAF) házirendet a Azure Portal használatával.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485593"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Oktatóanyag: WAF szabályzat létrehozása Azure CDN a Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy alapszintű Azure webalkalmazási tűzfal-(WAF-) szabályzatot, és hogyan alkalmazhatja azt egy Azure Content Delivery Network (CDN) végpontján.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAF szabályzat létrehozása
> * Társítsa egy CDN-végponthoz
> * WAF-szabályok konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy Azure CDN profilt és végpontot a gyors útmutató [: Azure CDN profil és végpont](../../cdn/cdn-create-new-endpoint.md)utasításait követve. 

## <a name="create-a-web-application-firewall-policy"></a>Webalkalmazási tűzfal házirend létrehozása

Először hozzon létre egy alapszintű WAF szabályzatot egy felügyelt alapértelmezett szabálykészlet (DRS) használatával a portálon.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**>**WAF** keresése lehetőséget>válassza a **webalkalmazási tűzfal** lehetőséget > válassza a **Létrehozás**lehetőséget.
2. A **WAF házirend létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Házirend a következőhöz:            |Válassza a Azure CDN (előzetes verzió) lehetőséget.|
    | Előfizetés            |Válassza ki a bejárati ajtó előfizetés nevét.|
    | Erőforráscsoport          |Válassza ki az első ajtóhoz tartozó erőforráscsoport nevét.|
    | Házirend neve             |Adja meg a WAF szabályzat egyedi nevét.|

   ![WAF szabályzat létrehozása](../media/waf-cdn-create-portal/basic.png)

3. A **WAF házirend létrehozása** lap **társítás** lapján válassza a **CDN-végpont hozzáadása**lehetőséget, adja meg a következő beállításokat, majd válassza a **Hozzáadás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | CDN-profil              | Válassza ki a CDN-profil nevét.|
    | Végpont           | Válassza ki a végpont nevét, majd válassza a **Hozzáadás**lehetőséget.|
    
    > [!NOTE]
    > Ha a végpont egy WAF-házirendhez van társítva, akkor a rendszer szürkén jeleníti meg. Először el kell távolítania a végpontot a társított szabályzatból, majd újra társítania kell a végpontot egy új WAF-házirendhez.
1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás**lehetőséget.

## <a name="configure-web-application-firewall-policy-optional"></a>Webalkalmazási tűzfal házirend konfigurálása (nem kötelező)

### <a name="change-mode"></a>Mód váltása

A WAF házirend alapértelmezés szerint *észlelési* módban van, amikor létrehoz egy WAF-házirendet. *Észlelési* módban a WAF nem blokkolja a kérelmeket. Ehelyett a WAF-szabályoknak megfelelő kérelmek naplózása a WAF-naplóknál történik.

Ha működés közben szeretné látni a WAF, az *észleléstől* kezdve a *megelőzés*lehetőségre módosíthatja a mód beállításait. A *megelőzési* módban az alapértelmezett SZABÁLYKÉSZLET (DRS) által meghatározott szabályoknak megfelelő kérelmeket a rendszer letiltja és naplózza a WAF-naplókban.

 ![WAF házirend mód módosítása](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az egyéni **szabály hozzáadása** lehetőséget az **Egyéni szabályok** szakaszban. Ekkor megnyílik az egyéni szabály konfigurációja lap. Az egyéni szabályoknak két típusa van: **egyezés a szabály** és a **díjszabási korlát** szabályainak megfelelően.

Az alábbi képernyőfelvételen egy egyéni egyeztetési szabály látható, amely letilt egy kérést, ha a lekérdezési karakterlánc tartalmazza a **blockme**értéket.

![WAF házirend mód módosítása](../media/waf-cdn-create-portal/custommatch.png)

A díjszabási korlát szabályai két további mezőt igényelnek: a **díjszabási korlát időtartamát** és a **díjszabási küszöbértéket (kérelmeket)** az alábbi példában látható módon:

![WAF házirend mód módosítása](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészlet (DRS)

Az Azure által felügyelt alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van. Ha le szeretne tiltani egy szabály csoportjának egy adott szabályát, bontsa ki a szabály csoporton belüli szabályokat, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a **Letiltás** lehetőséget a fenti lapon. Ha módosítani szeretné a szabálykészlet egyes szabályainak típusát, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a fenti **módosítási művelet** fület.

 ![WAF-szabálykészlet módosítása](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure webalkalmazási tűzfalról](../overview.md)
