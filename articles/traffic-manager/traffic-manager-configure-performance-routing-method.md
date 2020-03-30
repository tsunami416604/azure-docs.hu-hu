---
title: Teljesítményforgalom-útválasztási módszer konfigurálása az Azure Traffic Manager használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Traffic Manager-t a forgalom nak a legkisebb késésű végpontra történő irányítására
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: f5e9b7690c28793a35c692a6125a6b11c7a140a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938760"
---
# <a name="configure-the-performance-traffic-routing-method"></a>A teljesítményforgalom-útválasztási módszer konfigurálása

A Teljesítmény forgalom útválasztási módszer lehetővé teszi, hogy a forgalmat a végpontra a legalacsonyabb késés az ügyfél hálózatáról. Általában a legalacsonyabb késésű adatközpont a legközelebbi földrajzi távolság. Ez a forgalom-útválasztási módszer nem tudja figyelembe venni a hálózati konfiguráció vagy betöltés valós idejű változásait.

##  <a name="to-configure-performance-routing-method"></a>A teljesítmény-útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávján keresse meg a **Traffic Manager-profilokat,** majd kattintson arra a profilnévre, amelyhez konfigurálni szeretné az útválasztási módszert.
3. A **Traffic Manager profilpanelen** ellenőrizze, hogy a felhőszolgáltatások és a konfigurációban szerepelhető webhelyek is jelen vannak-e.
4. A **Beállítások** csoportban kattintson a **Konfiguráció**gombra, és a **Konfiguráció** panelen az alábbiak szerint hajtsa végre:
    1. A **forgalomútválasztási módszer beállításainál**az **Útválasztás módszer** csoportban válassza a **Teljesítmény**lehetőséget.
    2. Állítsa be a **végpontfigyelő beállításait** azonosak a profil összes végpontjára vonatkozóan az alábbiak szerint:
        1. Válassza ki a megfelelő **protokollt**, és adja meg a **portszámát.** 
        2. A **Görbe** mezőbe */* írjon be egy perjelet . A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A perjel "/" a relatív elérési út érvényes bejegyzése, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezett).
        3. A lap tetején kattintson a **Mentés gombra.**
5.  Tesztelje a konfiguráció változásait az alábbiak szerint:
    1.  A portál keresősávján keresse meg a Traffic Manager profil nevét, és kattintson a Traffic Manager-profilra a megjelenített eredmények között.
    2.  A **Traffic Manager** profilpanelen kattintson az **Áttekintés gombra.**
    3.  A **Traffic Manager profilpanel** megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ezt bármely ügyfél használhatja (például webböngészővel odanavigálva) az útválasztási típus által meghatározott megfelelő végpontra irányítva. Ebben az esetben az összes kérelem az ügyfél hálózatáról a legalacsonyabb késésű végpontra kerül.
6. Miután a Traffic Manager-profil működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, és irányítsa a vállalati tartománynevet a Traffic Manager tartománynévre.

![Teljesítményforgalom-útválasztási módszer konfigurálása a Traffic Manager használatával][1]

## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- További információ a [Traffic Manager beállításainak teszteléséről.](traffic-manager-testing-settings.md)

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png