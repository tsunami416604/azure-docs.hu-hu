---
title: 'Oktatóanyag: WAF-szabályzat létrehozása az Azure Bejárati ajtajához – Azure portal'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy webalkalmazás-tűzfal (WAF) szabályzatot az Azure Portal használatával.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: be66a93ea4a518b26d973d222caf58e73b6986a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475841"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Oktatóanyag: Webalkalmazás-tűzfal házirend létrehozása az Azure Bejárati ajtaján az Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy alapvető Azure Web Application Firewall (WAF) szabályzatot, és alkalmazhatja azt az Azure Front Door előtér-gazdagépre.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAF-házirend létrehozása
> * Frontend állomáshoz társítva
> * WAF-szabályok konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Bejárati ajtó profil létrehozása a [Rövid útmutató: Bejárati ajtó profil létrehozása](../../frontdoor/quickstart-create-front-door.md)című részben leírt utasításokat követve. 

## <a name="create-a-web-application-firewall-policy"></a>Webalkalmazás-tűzfal házirend létrehozása

Először hozzon létre egy alap szintű WAF-házirendet felügyelt alapértelmezett szabálykészlettel (DRS) a portál használatával. 

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása**>**a WAF** keresését,>válassza a **Webalkalmazás tűzfalát (előzetes verzió)** lehetőséget, > válassza a **Létrehozás lehetőséget.**
2. A **Waf-házirend létrehozása** lap **Alapjai** lapján adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés            |Válassza ki a Bejárati ajtó előfizetés nevét.|
    | Erőforráscsoport          |Válassza ki a bejárati ajtó erőforráscsoport nevét.|
    | Házirend neve             |Adja meg a WAF-házirend egyedi nevét.|

   ![WAF-házirend létrehozása](../media/waf-front-door-create-portal/basic.png)

3. A **Waf-házirend létrehozása** lap **Társítás** lapján válassza az **Előtér-állomás hozzáadása**lehetőséget, adja meg a következő beállításokat, majd válassza a **Hozzáadás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Bejárati ajtó              | Válassza ki a bejárati ajtó profil nevét.|
    | Előtér-gazdagép           | Jelölje ki a bejárati ajtó állomásának nevét, majd válassza a **Hozzáadás lehetőséget.**|
    
    > [!NOTE]
    > Ha az előtér-állomás waf-házirendhez van társítva, az szürke színnel jelenik meg. Először el kell távolítania az előtér-állomást a társított házirendből, majd újra társítania kell az előtér-állomást egy új WAF-házirendhez.
1. Válassza **a Véleményezés + létrehozás**lehetőséget, majd a **Létrehozás lehetőséget.**

## <a name="configure-web-application-firewall-rules-optional"></a>Webalkalmazás-tűzfal szabályainak konfigurálása (nem kötelező)

### <a name="change-mode"></a>Mód váltása

WaF-házirend létrehozásakor az alapértelmezett WAF-házirend **észlelési** módban van. **Észlelési** módban a WAF nem blokkolja a kérelmeket, ehelyett a WAF-szabályoknak megfelelő kéréseket a WAF-naplók naplózzák.
A WAF működés közbeni megtekintéséhez módosíthatja a módbeállításokat **észlelésről** **megelőzésre.** **Megelőzés módban** az alapértelmezett szabálykészletben (DRS) meghatározott szabályoknak megfelelő kérelmeket a WAF-naplók blokkolják és naplózzák.

 ![WAF-házirend üzemmódjának módosítása](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabályt úgy hozhat létre, hogy az Egyéni szabályok szakaszban az **Egyéni szabály hozzáadása** lehetőséget **választja.** Ezzel elindítja az egyéni szabály konfigurációs lapját. Az alábbi példa egy egyéni szabály konfigurálására készült, amely blokkolja a kérelmeket, ha a lekérdezési karakterlánc **blockme -t**tartalmaz.

![WAF-házirend üzemmódjának módosítása](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Alapértelmezett szabálykészlet (DRS)

Az Azure által felügyelt alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van. Ha egy szabálycsoporton belül le szeretne tiltani egy szabályt, bontsa ki a szabálycsoporton belüli szabályokat, jelölje be a szabályszám előtti **jelölőnégyzetet,** és a fenti lapon válassza a **Letiltás** lehetőséget. Ha módosítani szeretné a szabálykészleten belüli egyes szabályok művelettípusait, jelölje be a szabályszám előtti jelölőnégyzetet, majd a fenti **Művelet módosítása** lapot.

 ![WAF szabálykészlet módosítása](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure webalkalmazás-tűzfalról](../overview.md)
> [További információ az Azure Bejárati ajtajáról](../../frontdoor/front-door-overview.md)