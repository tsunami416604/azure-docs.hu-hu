---
title: Súlyozott Round-Robin Traffic Routing konfigurálása – Azure Traffic Manager
description: Ez a cikk azt ismerteti, hogyan lehet terheléselosztást használni egy ciklikus multiplexelés használatával Traffic Manager
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 0bfed558ec8db0ef715dad044c3965c1b1d8052b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040335"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>A súlyozott forgalom útválasztási módszerének konfigurálása Traffic Manager

A közös forgalom-útválasztási módszer minta az azonos végpontok készletének biztosítása, amely magában foglalja a Cloud Servicest és a webhelyeket, valamint a forgalmat egyaránt továbbítja. Az alábbi lépések azt ismertetik, hogyan konfigurálható az ilyen típusú forgalom-útválasztási módszer.

> [!NOTE]
> Az Azure-webalkalmazás már az Azure-régión belüli (több adatközpontot is tartalmazó) webhelyekhez biztosít ciklikusan részletes terheléselosztási funkciókat. Traffic Manager lehetővé teszi a forgalom terjesztését különböző adatközpontokban lévő webhelyek között.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>A súlyozott forgalom útválasztási módszerének konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresési sávján keresse meg a **Traffic Manager profilokat** , majd kattintson arra a profil nevére, amelyre az útválasztási módszert konfigurálni kívánja.
3. A **Traffic Manager profil** panelen ellenőrizze, hogy a konfigurációban szerepeltetni kívánt Cloud Services és websites is megtalálható-e.
4. A **Beállítások** szakaszban kattintson a **konfiguráció**elemre, majd a **konfiguráció** panelen hajtsa végre a következő lépéseket:
    1. A **forgalom-útválasztási módszer beállításainál**ellenőrizze, hogy a forgalmi útválasztási módszer **súlyozott**-e. Ha nem, kattintson a legördülő lista **súlyozása** elemre.
    2. A következő módon állítsa be a profil összes végpontján megegyező **Endpoint monitor beállításait** :
        1. Válassza ki a megfelelő **protokollt**, és adja meg a **portszámot** . 
        2. Az **elérési út** mezőbe írja be a perjel */* . A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A "/" perjel a relatív elérési útra érvényes bejegyzés, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezés).
        3. Kattintson a lap tetején található **Mentés**gombra.
5. Tesztelje a konfiguráció módosításait a következőképpen:
    1.  A portál keresési sávjában keressen rá a Traffic Manager profilnév nevére, és kattintson a Traffic Manager profilra a megjelenített eredmények között.
    2.  A **Traffic Manager** profil panelen kattintson az **Áttekintés**elemre.
    3.  A **Traffic Manager profil** panel az újonnan létrehozott Traffic Manager-profil DNS-nevét jeleníti meg. Ezt bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján. Ebben az esetben az összes kérelem minden végpontot ciklikus multiplexelés útján irányít.
6. Ha a Traffic Manager-profilja működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, hogy a vállalat tartománynevét a Traffic Manager tartománynevére mutassa.

![Súlyozott forgalom-útválasztási módszer konfigurálása Traffic Manager használatával][1]

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [prioritási forgalom útválasztási módszerét](traffic-manager-configure-priority-routing-method.md).
- A [teljesítmény-forgalom útválasztási módszerének](traffic-manager-configure-performance-routing-method.md)megismerése.
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- Ismerje meg, hogyan [tesztelheti Traffic Manager beállításait](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
