---
title: 'Oktatóanyag: WAF-szabályzat létrehozása az Azure bejárati ajtóhoz – Azure Portal'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre webalkalmazási tűzfal-(WAF-) szabályzatot a Azure Portal használatával.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: ba344c3b1570c041e1602bdfcde1b3a4055dc396
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132754"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Oktatóanyag: webalkalmazási tűzfal szabályzatának létrehozása az Azure-beli előtérben a Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy alapszintű Azure webalkalmazási tűzfal-(WAF-) szabályzatot, és hogyan alkalmazhatja azt egy előtér-gazdagépen az Azure bejárati ajtón.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * WAF szabályzat létrehozása
> * Társítsa egy előtér-gazdagéphez
> * WAF-szabályok konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy bevezető ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Webalkalmazási tűzfal házirend létrehozása

Először hozzon létre egy alapszintű WAF szabályzatot a felügyelt alapértelmezett szabálykészlet (DRS) használatával a portálon. 

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**>**WAF** keresése lehetőséget>válassza a **webalkalmazási tűzfal (előzetes verzió)** lehetőséget > válassza a **Létrehozás**lehetőséget.
2. A **WAF házirend létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés            |Válassza ki a bejárati ajtó előfizetés nevét.|
    | Erőforráscsoport          |Válassza ki az első ajtóhoz tartozó erőforráscsoport nevét.|
    | Házirend neve             |Adja meg a WAF szabályzat egyedi nevét.|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Képernyőfelvétel: a W A F-szabályzat létrehozása lap, amely az előfizetés, az erőforráscsoport és a szabályzat nevének áttekintés + létrehozás gombját és listáját tartalmazza." border="false":::

3. A **WAF házirend létrehozása** lap **társítás** lapján válassza a előtér- **gazdagép hozzáadása**lehetőséget, adja meg a következő beállításokat, majd válassza a **Hozzáadás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Bejárati ajtó              | Válassza ki a bejárati ajtó profiljának nevét.|
    | Előtér-gazdagép           | Válassza ki a bejárati ajtó gazdagépének nevét, majd válassza a **Hozzáadás**lehetőséget.|
    
    > [!NOTE]
    > Ha a előtér-gazdagép egy WAF-házirendhez van társítva, akkor a rendszer szürkén jelenik meg. Először el kell távolítania a előtér-gazdagépet a társított házirendből, majd újra társítania kell a előtér-gazdagépet egy új WAF-házirendhez.
1. Válassza a **Felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget.

## <a name="configure-web-application-firewall-rules-optional"></a>Webalkalmazási tűzfalszabályok konfigurálása (nem kötelező)

### <a name="change-mode"></a>Mód váltása

WAF szabályzat létrehozásakor az alapértelmezett WAF-házirend **észlelési** módban van. **Észlelési** módban a WAF nem blokkolja a kérelmeket, hanem a WAF-szabályoknak megfelelő kérelmeket naplózza a rendszer a WAF-naplókon.
Ha működés közben szeretné látni a WAF, az **észleléstől** kezdve a **megelőzés**lehetőségre módosíthatja a mód beállításait. A **megelőzési** módban az alapértelmezett SZABÁLYKÉSZLET (DRS) által meghatározott szabályoknak megfelelő kérelmeket a rendszer letiltja és naplózza a WAF-naplókban.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Képernyőfelvétel: a W A F-szabályzat létrehozása lap, amely az előfizetés, az erőforráscsoport és a szabályzat nevének áttekintés + létrehozás gombját és listáját tartalmazza." border="false":::

### <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az egyéni **szabály hozzáadása** lehetőséget az **Egyéni szabályok** szakaszban. Ezzel elindítja az egyéni szabály konfigurálása lapot. Az alábbi példa egy egyéni szabály konfigurálását mutatja be egy kérelem blokkolására, ha a lekérdezési karakterlánc **blockme**tartalmaz.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Képernyőfelvétel: a W A F-szabályzat létrehozása lap, amely az előfizetés, az erőforráscsoport és a szabályzat nevének áttekintés + létrehozás gombját és listáját tartalmazza." border="false":::

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészlet (DRS)

Az Azure által felügyelt alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van. Ha le szeretne tiltani egy szabály csoportjának egy adott szabályát, bontsa ki a szabály csoporton belüli szabályokat, jelölje be a szabály száma előtt található **jelölőnégyzetet** , majd válassza a **Letiltás** lehetőséget a fenti lapon. Ha módosítani szeretné a szabálykészlet egyes szabályainak típusát, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a fenti **módosítási művelet** fület.

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="Képernyőfelvétel: a W A F-szabályzat létrehozása lap, amely az előfizetés, az erőforráscsoport és a szabályzat nevének áttekintés + létrehozás gombját és listáját tartalmazza." border="false":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure bejáratáról](../../frontdoor/front-door-overview.md)