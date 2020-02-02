---
title: Oktatóanyag – prioritási forgalom útválasztásának konfigurálása az Azure Traffic Manager
description: Ez az oktatóanyag ismerteti, hogyan konfigurálhatja a prioritási forgalom útválasztási módszerét Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: ca223a19ff7ddeae95878f941f3cf295664e62b4
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938726"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Oktatóanyag: a prioritási forgalom útválasztási módszerének konfigurálása Traffic Manager

A webhelyek módjától függetlenül az Azure websites szolgáltatás már biztosít feladatátvételi funkciókat az adatközponton belüli (más néven régió) webhelyekhez. A Traffic Manager a különböző adatközpontokban lévő webhelyek feladatátvételét teszi lehetővé.

A szolgáltatás feladatátvételének általános mintája egy elsődleges szolgáltatás felé irányuló forgalom elküldése, és a feladatátvételhez azonos biztonsági mentési szolgáltatások készletének biztosítása. A következő lépések elmagyarázzák, hogyan konfigurálhatja ezt a rangsorolt feladatátvételt az Azure Cloud Services és websites szolgáltatással:

## <a name="to-configure-the-priority-traffic-routing-method"></a>A prioritási forgalom útválasztási módszerének konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresési sávján keresse meg a **Traffic Manager profilokat** , majd kattintson arra a profil nevére, amelyre az útválasztási módszert konfigurálni kívánja.
3. A **Traffic Manager profil** panelen ellenőrizze, hogy a konfigurációban szerepeltetni kívánt Cloud Services és websites is megtalálható-e.
4. A **Beállítások** szakaszban kattintson a **konfiguráció**elemre, majd a **konfiguráció** panelen hajtsa végre a következő lépéseket:
    1. A **forgalom-útválasztási módszer beállításainál**ellenőrizze, hogy a forgalom-útválasztási módszer **prioritás**-e. Ha nem, kattintson a **prioritás** lehetőségre a legördülő listából.
    2. A következő módon állítsa be a profil összes végpontján megegyező **Endpoint monitor beállításait** :
        1. Válassza ki a megfelelő **protokollt**, és adja meg a **portszámot** . 
        2. Az **elérési út** mezőbe írja be a perjel */* . A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A "/" perjel a relatív elérési útra érvényes bejegyzés, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezés).
        3. Kattintson a lap tetején található **Mentés**gombra.
5. A **Beállítások** szakaszban kattintson a **végpontok**elemre.
6. A **végpontok** panelen tekintse át a végpontok prioritási sorrendjét. Ha a **prioritási** forgalom útválasztási módszerét választja, a kiválasztott végpontok sorrendje számít. Ellenőrizze a végpontok prioritási sorrendjét.  Az elsődleges végpont felül van. Ellenőrizze a megjelenő sorrendet. az összes kérelem át lesz irányítva az első végpontra, és ha Traffic Manager észleli, hogy sérült, akkor a forgalom automatikusan átadja a következő végpontot. 
7. A végpont prioritási sorrendjének módosításához kattintson a végpontra, majd a megjelenő **végpont** panelen kattintson a **Szerkesztés** elemre, és szükség szerint módosítsa a **prioritási** értéket. 
8. Kattintson a **Save (Mentés** ) gombra a végpont beállításainak módosításához.
9. A konfigurációs módosítások elvégzése után kattintson a lap alján található **Mentés** gombra.
10. Tesztelje a konfiguráció módosításait a következőképpen:
    1.  A portál keresési sávjában keressen rá a Traffic Manager profilnév nevére, és kattintson a Traffic Manager profilra a megjelenített eredmények között.
    2.  A **Traffic Manager** profil panelen kattintson az **Áttekintés**elemre.
    3.  A **Traffic Manager profil** panel az újonnan létrehozott Traffic Manager-profil DNS-nevét jeleníti meg. Ezt bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján. Ebben az esetben a rendszer az első végponthoz irányítja a kérelmeket, és ha Traffic Manager észleli, hogy nem kifogástalan állapotú, akkor a forgalom automatikusan átadja a következő végpontot.
11. Ha a Traffic Manager-profilja működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, hogy a vállalat tartománynevét a Traffic Manager tartománynevére mutassa.

![Prioritási forgalom útválasztási módszerének konfigurálása Traffic Manager használatával][1]

## <a name="next-steps"></a>Következő lépések


- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ a [teljesítmény-útválasztási módszerről](traffic-manager-configure-performance-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- Ismerje meg, hogyan [tesztelheti Traffic Manager beállításait](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png