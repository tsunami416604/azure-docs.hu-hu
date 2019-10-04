---
title: Hozzon létre egy webalkalmazási tűzfal-házirendet az Azure-beli előtérben a Azure Portal használatával
titlesuffix: Azure web application firewall
description: Ismerje meg, hogyan hozhat létre webalkalmazási tűzfal-(WAF-) szabályzatot a Azure Portal használatával.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846390"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>WAF-szabályzat létrehozása az Azure bejárati ajtóhoz a Azure Portal használatával

Ez a cikk bemutatja, hogyan hozhat létre egy alapszintű Azure webalkalmazási tűzfal-(WAF-) szabályzatot, és hogyan alkalmazhatja azt egy előtér-gazdagépre az Azure bejárati Ajtóján.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban ismertetett [utasításokat követve hozzon létre egy bejárati ajtót. Hozzon létre egy előtérben lévő profilt](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>WAF szabályzat létrehozása

Először hozzon létre egy alapszintű WAF szabályzatot a felügyelt alapértelmezett szabálykészlet (DRS) használatával a portálon. 

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**> **WAF**keresése lehetőséget > válassza a webalkalmazási **tűzfal (előzetes verzió)** lehetőséget > válassza a **Létrehozás**lehetőséget.
2. A **WAF házirend létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription            |Válassza ki a bejárati ajtó előfizetés nevét.|
    | Resource group          |Válassza ki az első ajtóhoz tartozó erőforráscsoport nevét.|
    | Szabályzat neve             |Adja meg a WAF szabályzat egyedi nevét.|

   ![WAF szabályzat létrehozása](./media/waf-front-door-create-portal/basic.png)

3. A **WAF házirend létrehozása** lap **társítás** lapján válassza a előtér- **gazdagép hozzáadása**lehetőséget, adja meg a következő beállításokat, majd válassza a **Hozzáadás**lehetőséget:

    | Beállítás                 | Value                                              |
    | ---                     | ---                                                |
    | Bejárati ajtó              | Válassza ki a bejárati ajtó profiljának nevét.|
    | Előtér-gazdagép           | Válassza ki a bejárati ajtó gazdagépének nevét, majd válassza a **Hozzáadás**lehetőséget.|
    
    > [!NOTE]
    > Ha a előtér-gazdagép egy WAF-házirendhez van társítva, akkor a rendszer szürkén jelenik meg. Először el kell távolítania a előtér-gazdagépet a társított házirendből, majd újra társítania kell a előtér-gazdagépet egy új WAF-házirendhez.
1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás**lehetőséget.

## <a name="configure-waf-rules-optional"></a>WAF-szabályok konfigurálása (nem kötelező)

### <a name="change-mode"></a>Mód váltása

WAF szabályzat létrehozásakor az alapértelmezett WAF-házirend észlelési módban van. **Észlelési** módban a WAF nem blokkolja a kérelmeket, hanem a WAF-szabályoknak megfelelő kérelmeket naplózza a rendszer a WAF-naplókon.
Ha működés közben szeretné látni a WAF, az észleléstől kezdve a  **megelőzés**lehetőségre módosíthatja a mód beállításait. A **megelőzési** módban az alapértelmezett SZABÁLYKÉSZLET (DRS) által meghatározott szabályoknak megfelelő kérelmeket a rendszer letiltja és naplózza a WAF-naplókban.

 ![WAF házirend mód módosítása](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az egyéni **szabály hozzáadása** lehetőséget az **Egyéni szabályok** szakaszban. Ezzel elindítja az egyéni szabály konfigurálása lapot. Az alábbi példa egy egyéni szabály konfigurálását mutatja be egy kérelem blokkolására, ha a lekérdezési karakterlánc **blockme**tartalmaz.

![WAF házirend mód módosítása](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészlet (DRS)

Az Azure által felügyelt alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van. Ha le szeretne tiltani egy szabály csoportjának egy adott szabályát, bontsa ki a szabály csoporton  belüli szabályokat, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a **Letiltás** lehetőséget a fenti lapon. Ha módosítani szeretné a szabálykészlet egyes szabályainak típusát, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a fenti **módosítási művelet** fület.

 ![WAF-szabálykészlet módosítása](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure](waf-overview.md)webalkalmazási tűzfalat.
- További információ az [Azure](front-door-overview.md)bejáratáról.
