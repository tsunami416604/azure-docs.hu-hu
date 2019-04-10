---
title: Az Azure portal segítségével Azure bejárati ajtajának webes alkalmazás tűzfal házirend létrehozása
titlesuffix: Azure web application firewall
description: Útmutató a webes alkalmazás tűzfal (WAF) szabályzat létrehozása az Azure portal használatával.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/8/2019
ms.author: kumud;tyao
ms.openlocfilehash: b2fbe57a94ad4c5faad3318c03f797446bea4535
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288516"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Az Azure portal segítségével Azure bejárati ajtajának WAF szabályzat létrehozása

Ez a cikk ismerteti, hogyan hozhat létre egy alapszintű Azure-alapú webes alkalmazás-tűzfal (WAF) házirend, és alkalmazhatja azt egy előtér-gazdagépen található Azure bejárati ajtajának.

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy bejárati ajtajának profilt leírt utasítások alapján [a rövid útmutató: Hozzon létre egy bejárati ajtajának profilt](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>A WAF-szabályzat létrehozása

Először hozzon létre egy alapszintű WAF-házirend a felügyelt alapértelmezett szabály beállítása (DRS) a portál használatával. 

1. A képernyő bal felső sarkában válassza **erőforrás létrehozása**> keresse meg **WAF**> Válasszon **webalkalmazási tűzfal (előzetes verzió)** > válassza  **Hozzon létre**.
2. Az a **alapjai** lapján a **WAF-házirend létrehozása** lap, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **felülvizsgálat +létrehozása**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés            |Válassza ki a bejárati ajtajának előfizetés nevét.|
    | Erőforráscsoport          |Válassza ki a bejárati ajtajának erőforráscsoport-nevet.|
    | Házirend neve             |Adjon meg egy egyedi nevet a WAF-szabályzat.|

   ![A WAF-szabályzat létrehozása](./media/waf-front-door-create-portal/basic.png)

3. Az a **társítás** lapján a **WAF-házirend létrehozása** lapon jelölje be **előtér-gazdagép hozzáadása**, és adja meg a következő beállításokat, majd válassza ki **Hozzáadás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Bejárati ajtajának              | Válassza ki a bejárati ajtajának profil nevét.|
    | Előtérbeli gazdagép           | Válassza ki a bejárati ajtajának gazdagép nevét, majd válassza ki **Hozzáadás**.|
    
    > [!NOTE]
    > Az előtér-állomás a WAF-házirend társítva, ha azt látható módon szürkén jelenik meg. Először távolítsa el a frontend gazdagépet a társított házirendből, és újra társítani a előtér-gazdagép egy új WAF-házirend.
1. Válassza ki **felülvizsgálat + létrehozása**, majd **létrehozás**.

## <a name="configure-waf-rules-optional"></a>(Nem kötelező) WAF-szabályok konfigurálása

### <a name="change-mode"></a>Mód megváltoztatása

A WAF-házirend létrehozásakor az alapértelmezett WAF házirend alapértelmezés szerint **észlelési** mód. A **észlelési** mód, WAF nem blokkolja a kérések, ehelyett a WAF-naplókban a WAF-szabályoknak-kérés naplózásra kerül.
WAF működés megtekintéséhez módosíthatja a mód beállításait **észlelési** való **megelőzési**. A **megelőzési** módban kéri, hogy egyezik az alapértelmezett szabály beállítása (DRS) meghatározott szabályokat blokkolva vannak, és WAF-naplókban naplózza.

 ![WAF házirend módváltás](./media/waf-front-door-create-portal/policy.png)

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészletet (DRS)

Azure által felügyelt alapértelmezett szabálykészletet alapértelmezés szerint engedélyezve van. Egy szabály csoporton belül az egyes szabályok letiltása, bontsa ki az adott szabály csoport, válassza a szabályok a **jelölőnégyzet** elé a szabály száma, és válassza **letiltása** az lapon. Műveletek típusok módosításához szabályban egyéni szabályok beállítása, jelölje be a szabály száma elé, és válassza a **művelet módosítható** fenti fülre.

 ![WAF-szabálykészlet módosítása](./media/waf-front-door-create-portal/managed.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg [ Azure webalkalmazási tűzfal](waf-overview.md).
- Tudjon meg többet [Azure bejárati ajtajának](front-door-overview.md).




