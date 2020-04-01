---
title: Oktatóanyag – A prioritásos forgalom útválasztásának konfigurálása az Azure Traffic Managerrel
description: Ez az oktatóanyag bemutatja, hogyan konfigurálható a prioritási forgalom útválasztási módszere a Traffic Managerben
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938726"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Oktatóanyag: A prioritásos forgalomútválasztási módszer konfigurálása a Traffic Managerben

A webhely módtól függetlenül az Azure-webhelyek már biztosítják az adatközponton (más néven régión) belüli webhelyek feladatátvételi funkcióit. A Traffic Manager feladatátvételt biztosít a különböző adatközpontokban lévő webhelyek számára.

A szolgáltatás feladatátvétel általános mintája az, hogy forgalmat küld egy elsődleges szolgáltatásba, és azonos biztonsági mentési szolgáltatások készletét biztosítja a feladatátvételhez. Az alábbi lépések bemutatják, hogyan konfigurálható ez a rangsorolt feladatátvétel az Azure felhőszolgáltatásaival és webhelyeivel:

## <a name="to-configure-the-priority-traffic-routing-method"></a>A prioritási forgalom útválasztási módjának konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávján keresse meg a **Traffic Manager-profilokat,** majd kattintson arra a profilnévre, amelyhez konfigurálni szeretné az útválasztási módszert.
3. A **Traffic Manager profilpanelen** ellenőrizze, hogy a felhőszolgáltatások és a konfigurációban szerepelhető webhelyek is jelen vannak-e.
4. A **Beállítások** csoportban kattintson a **Konfiguráció**gombra, és a **Konfiguráció** panelen az alábbiak szerint hajtsa végre:
    1. A **forgalomútválasztási módszer beállításainál**ellenőrizze, hogy a forgalomútválasztási módszer **prioritás-e.** Ha nem, kattintson a legördülő lista **Prioritás** gombjára.
    2. Állítsa be a **végpontfigyelő beállításait** azonosak a profil összes végpontjára vonatkozóan az alábbiak szerint:
        1. Válassza ki a megfelelő **protokollt**, és adja meg a **portszámát.** 
        2. A **Görbe** mezőbe */* írjon be egy perjelet . A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A perjel "/" a relatív elérési út érvényes bejegyzése, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezett).
        3. A lap tetején kattintson a **Mentés gombra.**
5. A **Beállítások csoportban** kattintson a **Végpontok gombra.**
6. A **Végpontok** panelen tekintse át a végpontok prioritási sorrendjét. Ha kiválasztja a **Prioritás** forgalom útválasztási módszert, a kiválasztott végpontok sorrendje számít. Ellenőrizze a végpontok prioritási sorrendjét.  Az elsődleges végpont felül van. Ellenőrizze a jelenlévő sorrendet. az összes kérelem az első végpontra lesz irányítva, és ha a Traffic Manager azt észleli, hogy nem megfelelő állapotú, a forgalom automatikusan átadja a következő végpontnak. 
7. A végpont prioritási sorrendjének módosításához kattintson a végpontra, és a megjelenített **Végpont** panelen kattintson a **Szerkesztés** gombra, és szükség szerint módosítsa a **Prioritás** értéket. 
8. A **végpontbeállítások** módosításához kattintson a Mentés gombra.
9. A konfigurációs módosítások befejezése után kattintson a lap alján található **Mentés** gombra.
10. Tesztelje a konfiguráció változásait az alábbiak szerint:
    1.  A portál keresősávján keresse meg a Traffic Manager profil nevét, és kattintson a Traffic Manager-profilra a megjelenített eredmények között.
    2.  A **Traffic Manager** profilpanelen kattintson az **Áttekintés gombra.**
    3.  A **Traffic Manager profilpanel** megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ezt bármely ügyfél használhatja (például webböngészővel odanavigálva) az útválasztási típus által meghatározott megfelelő végpontra irányítva. Ebben az esetben az összes kérelem az első végpontra lesz irányítva, és ha a Traffic Manager azt észleli, hogy nem megfelelő állapotú, a forgalom automatikusan átadja a következő végpontnak.
11. Miután a Traffic Manager-profil működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, és irányítsa a vállalati tartománynevet a Traffic Manager tartománynévre.

![Prioritásos forgalomútválasztási módszer konfigurálása a Traffic Manager használatával][1]

## <a name="next-steps"></a>További lépések


- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ a [teljesítmény-útválasztási módszerről.](traffic-manager-configure-performance-routing-method.md)
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- További információ a [Traffic Manager beállításainak teszteléséről.](traffic-manager-testing-settings.md)

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png