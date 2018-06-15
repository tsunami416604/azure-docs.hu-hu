---
title: Teljesítmény forgalom-útválasztási módszert használja az Azure Traffic Manager konfigurálása |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan Traffic Manager irányíthatja a forgalmat a végpontnak a legkisebb mértékű késleltetést konfigurálása
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 014aa646459cd64fca7c697419324caa3edaeeea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876312"
---
# <a name="configure-the-performance-traffic-routing-method"></a>A teljesítmény forgalom útválasztási módszer konfigurálása

A teljesítmény forgalom-útválasztási módszert közvetlen forgalom a végponthoz, a legkisebb mértékű késleltetést az ügyfél hálózati teszi lehetővé. A legkisebb mértékű késleltetést az Adatközpont jellemzően a legközelebbi földrajzi távolságot. A forgalom-útválasztási módszert nem fiókot használja a hálózati konfigurációban valós idejű módosításokat, vagy nem tölthető be.

##  <a name="to-configure-performance-routing-method"></a>Teljesítmény útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávban, keresse meg a **Traffic Manager-profilok** és kattintson a profil nevére, amelyet vonatkozó útválasztási módszer konfigurálása.
3. Az a **Traffic Manager-profil** panelen ellenőrizze, hogy a felhőszolgáltatás és a webhelyeket, amelyeket a konfigurációt szeretne megtalálhatók.
4. Az a **beállítások** területen kattintson **konfigurációs**, majd a a **konfigurációs** panelen, befejeződött, az alábbi módon:
    1. A **forgalom-útválasztási módszer beállításai**, a **útválasztási módszer** válasszon **teljesítmény**.
    2. Állítsa be a **végpont figyelőbeállítások** azonos összes minden végponton belül ezt a profilt, az alábbiak szerint:
        1. Válassza ki a megfelelő **protokoll**, és adja meg a **Port** számát. 
        2. A **elérési** írja be a perjellel  */* . Figyelő végpontokat, az elérési útnak és fájlnévnek kell megadnia. A perjel "/" relatív elérési útja érvényes bejegyzés, és azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
        3. Kattintson a lap tetején **mentése**.
5.  Tesztelje a módosításokat a konfigurációt az alábbiak szerint:
    1.  A portál keresősávban, keresse meg a Traffic Manager-profil nevét, majd kattintson az eredményeket a Traffic Manager-profilt, amely a jelennek meg.
    2.  Az a **Traffic Manager** profil panelen, kattintson a **áttekintése**.
    3.  A **Traffic Manager-profil** csempe megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ez használhatja olyan ügyfelek (például úgy, hogy keresse meg webböngészővel) az beszerzése irányítja át a megfelelő végpont, határozza meg az Útválasztás típusa. Ebben az esetben az összes rendszer kérést átirányítja a végpont és a legkisebb mértékű az ügyfél hálózatról.
6. A Traffic Manager-profil működik, ha a vállalata tartománynevét mutasson a Traffic Manager tartományneve a mérvadó DNS-kiszolgálón a DNS-rekord szerkesztése

![Teljesítmény forgalom-útválasztási módszert használja a Traffic Manager konfigurálása][1]

## <a name="next-steps"></a>Következő lépések

- További tudnivalók [forgalom-útválasztási módszert súlyozott](traffic-manager-configure-weighted-routing-method.md).
- További tudnivalók [prioritású virtuális gép útválasztási módszer](traffic-manager-configure-priority-routing-method.md).
- További tudnivalók [földrajzi útválasztási módszer](traffic-manager-configure-geographic-routing-method.md).
- Megtudhatja, hogyan [Traffic Manager-beállítások tesztelésére](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png