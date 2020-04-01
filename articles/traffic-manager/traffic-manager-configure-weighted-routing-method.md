---
title: Oktatóanyag – Súlyozott ciklikus multiplexeléses forgalomútválasztás konfigurálása az Azure Traffic Managerrel
description: Ez az oktatóanyag bemutatja, hogyan lehet a forgalom terheléselosztása ciklikus multiplexelési módszerrel a Traffic Managerben
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 8bdc710b36cae70d29d32333f431b8a9dda154cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938712"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Oktatóanyag: Konfigurálja a súlyozott forgalomútválasztási módszert a Traffic Managerben

A közös forgalomútválasztási módszer minta az, hogy egy sor azonos végpontok, amelyek magukban foglalják a felhőszolgáltatások és a webhelyek, és a forgalom küldése minden egyenlően. Az alábbi lépések bemutatják, hogyan konfigurálható az ilyen típusú forgalomútválasztási módszer.

> [!NOTE]
> Az Azure Web App már rendelkezik ciklikus multiplexelés terheléselosztási funkció az Azure-régión belüli (amely több adatközpontok) webhelyeken található. A Traffic Manager lehetővé teszi a forgalom terjesztését a különböző adatközpontokban lévő webhelyek között.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>A súlyozott forgalomútválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávján keresse meg a **Traffic Manager-profilokat,** majd kattintson arra a profilnévre, amelyhez konfigurálni szeretné az útválasztási módszert.
3. A **Traffic Manager profilpanelen** ellenőrizze, hogy a felhőszolgáltatások és a konfigurációban szerepelhető webhelyek is jelen vannak-e.
4. A **Beállítások** csoportban kattintson a **Konfiguráció**gombra, és a **Konfiguráció** panelen az alábbiak szerint hajtsa végre:
    1. A **forgalomútválasztási módszer beállításainál**ellenőrizze, hogy a forgalomútválasztási módszer **súlyozott-e.** Ha nem, kattintson a legördülő listából a **Súlyozott** elemre.
    2. Állítsa be a **végpontfigyelő beállításait** azonosak a profil összes végpontjára vonatkozóan az alábbiak szerint:
        1. Válassza ki a megfelelő **protokollt**, és adja meg a **portszámát.** 
        2. A **Görbe** mezőbe */* írjon be egy perjelet . A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A perjel "/" a relatív elérési út érvényes bejegyzése, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezett).
        3. A lap tetején kattintson a **Mentés gombra.**
5. Tesztelje a konfiguráció változásait az alábbiak szerint:
    1.  A portál keresősávján keresse meg a Traffic Manager profil nevét, és kattintson a Traffic Manager-profilra a megjelenített eredmények között.
    2.  A **Traffic Manager** profilpanelen kattintson az **Áttekintés gombra.**
    3.  A **Traffic Manager profilpanel** megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ezt bármely ügyfél használhatja (például webböngészővel navigálva) az útválasztási típus által meghatározott megfelelő végpontra irányítva. Ebben az esetben minden kérelem minden végpont ciklikus multiplexelés módon van irányítva.
6. Miután a Traffic Manager-profil működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, és irányítsa a vállalati tartománynevet a Traffic Manager tartománynévre.

![Súlyozott forgalomútválasztási módszer konfigurálása a Traffic Manager használatával][1]

## <a name="next-steps"></a>További lépések

- További információ a [prioritásos forgalomútválasztási módszerről.](traffic-manager-configure-priority-routing-method.md)
- További információ a [teljesítményforgalom-útválasztási módszerről.](traffic-manager-configure-performance-routing-method.md)
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- További információ a [Traffic Manager beállításainak teszteléséről.](traffic-manager-testing-settings.md)

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
