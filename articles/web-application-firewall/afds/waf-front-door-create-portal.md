---
title: 'Oktatóanyag: webalkalmazási tűzfal-házirend létrehozása az Azure-beli előtérben a Azure Portal használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre webalkalmazási tűzfal-(WAF-) szabályzatot a Azure Portal használatával.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/07/2019
ms.author: victorh
ms.openlocfilehash: 602a8620ef35ea247c5865d97495ea43a7d7d93c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501112"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Oktatóanyag: webalkalmazási tűzfal szabályzatának létrehozása az Azure-beli előtérben a Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy alapszintű Azure webalkalmazási tűzfal-(WAF-) szabályzatot, és hogyan alkalmazhatja azt egy előtér-gazdagépen az Azure bejárati ajtón.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAF szabályzat létrehozása
> * Társítsa egy előtér-gazdagéphez
> * WAF-szabályok konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy bevezető ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Webalkalmazási tűzfal házirend létrehozása

Először hozzon létre egy alapszintű WAF szabályzatot a felügyelt alapértelmezett szabálykészlet (DRS) használatával a portálon. 

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**> **WAF**keresése lehetőséget > válassza a **webalkalmazási tűzfal (előzetes verzió)** lehetőséget > válassza a **Létrehozás**lehetőséget.
2. A **WAF házirend létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés            |Válassza ki a bejárati ajtó előfizetés nevét.|
    | Erőforráscsoport          |Válassza ki az első ajtóhoz tartozó erőforráscsoport nevét.|
    | Szabályzat neve             |Adja meg a WAF szabályzat egyedi nevét.|

   ![WAF szabályzat létrehozása](../media/waf-front-door-create-portal/basic.png)

3. A **WAF házirend létrehozása** lap **társítás** lapján válassza a előtér- **gazdagép hozzáadása**lehetőséget, adja meg a következő beállításokat, majd válassza a **Hozzáadás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Bejárati ajtó              | Válassza ki a bejárati ajtó profiljának nevét.|
    | Előtér-gazdagép           | Válassza ki a bejárati ajtó gazdagépének nevét, majd válassza a **Hozzáadás**lehetőséget.|
    
    > [!NOTE]
    > Ha a előtér-gazdagép egy WAF-házirendhez van társítva, akkor a rendszer szürkén jelenik meg. Először el kell távolítania a előtér-gazdagépet a társított házirendből, majd újra társítania kell a előtér-gazdagépet egy új WAF-házirendhez.
1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás**lehetőséget.

## <a name="configure-web-application-firewall-rules-optional"></a>Webalkalmazási tűzfalszabályok konfigurálása (nem kötelező)

### <a name="change-mode"></a>Mód váltása

WAF szabályzat létrehozásakor az alapértelmezett WAF-házirend **észlelési** módban van. **Észlelési** módban a WAF nem blokkolja a kérelmeket, hanem a WAF-szabályoknak megfelelő kérelmeket naplózza a rendszer a WAF-naplókon.
Ha működés közben szeretné látni a WAF, az **észleléstől** kezdve a **megelőzés**lehetőségre módosíthatja a mód beállításait. A **megelőzési** módban az alapértelmezett SZABÁLYKÉSZLET (DRS) által meghatározott szabályoknak megfelelő kérelmeket a rendszer letiltja és naplózza a WAF-naplókban.

 ![WAF házirend mód módosítása](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az egyéni **szabály hozzáadása** lehetőséget az **Egyéni szabályok** szakaszban. Ezzel elindítja az egyéni szabály konfigurálása lapot. Az alábbi példa egy egyéni szabály konfigurálását mutatja be egy kérelem blokkolására, ha a lekérdezési karakterlánc **blockme**tartalmaz.

![WAF házirend mód módosítása](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészlet (DRS)

Az Azure által felügyelt alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van. Ha le szeretne tiltani egy szabály csoportjának egy adott szabályát, bontsa ki a szabály csoporton belüli szabályokat, jelölje be a szabály száma előtt található **jelölőnégyzetet** , majd válassza a **Letiltás** lehetőséget a fenti lapon. Ha módosítani szeretné a szabálykészlet egyes szabályainak típusát, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a fenti **módosítási művelet** fület.

 ![WAF-szabálykészlet módosítása](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg az Azure-webalkalmazási tűzfal](../overview.md)
> további információ [Az Azure bejárati ajtóról](../../frontdoor/front-door-overview.md)