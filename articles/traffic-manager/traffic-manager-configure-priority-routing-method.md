---
title: Konfigurálja a prioritás forgalom-útválasztási módszer az Azure Traffic Managerrel |} A Microsoft Docs
description: Ez a cikk ismerteti a prioritás forgalom-útválasztási módszert a Traffic Manager konfigurálása
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 52bfc73ad586bb3852e30a850d4ba50a113183d3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140417"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Prioritás forgalom-útválasztási módszert a Traffic Manager konfigurálása

A webhely működési függetlenül az Azure Websites már meg feladatátvételi funkció (más néven régióhoz) adatközponton belüli webhelyekhez. A TRAFFIC Manager feladatátvételt biztosít a különböző adatközpontokban található webhely számára.

A szolgáltatás feladatátvételének egyik küldeni a forgalmat a szolgáltatás elsődleges és feladatátvételi azonos biztonsági mentési szolgáltatások körét. Az alábbi lépéseket a rangsorolt feladatátvétel konfigurálása az Azure cloud services és websites ismertetik:

## <a name="to-configure-the-priority-traffic-routing-method"></a>A prioritás forgalom-útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávjában keressen a **Traffic Manager-profilok** és kattintson a profil nevét, amelyet szeretne az útválasztási módszer konfigurálása.
3. Az a **Traffic Manager-profil** panelen ellenőrizze, hogy a cloud services és a webhelyeket, amelyeket fel szeretne venni a konfiguráció jelen.
4. Az a **beállítások** területén kattintson **konfigurációs**, majd a a **konfigurációs** panelen adja meg a következőket:
    1. A **forgalom-útválasztási módszer beállításait**, győződjön meg arról, hogy a forgalom-útválasztási módszer **prioritású**. Ha nem, kattintson a **prioritású** a legördülő listából.
    2. Állítsa be a **Végpontfigyelő beállításai** azonos az összes minden végpontot a profiljában, az alábbiak szerint:
        1. Válassza ki a megfelelő **protokoll**, és adja meg a **Port** számát. 
        2. A **elérési** írja be a perjellel */*. Végpontok monitorozása, az elérési útvonalat és fájlnevet kell megadnia. A perjel "/" érvényes bejegyzés a relatív elérési útja, és azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
        3. Kattintson a lap tetején **mentése**.
5. Az a **beállítások** területén kattintson **végpontok**.
6. Az a **végpontok** panelen tekintse át a prioritási sorrendet a végpontokon. Amikor kiválasztja a **prioritású** forgalom-útválasztási módszer, a kiválasztott végpontok kérdések sorrendjét. Ellenőrizze a végpontok a prioritásuk szerinti sorrendben történik.  Az elsődleges végpont felül van. Ellenőrizze a rendelés jelenik meg. összes kérést átirányítja az első végpont a Traffic Manager észleli, ha nem megfelelő állapotú lesz, és a forgalom automatikusan átadja a feladatokat a következő végpont. 
7. Végpont prioritásának módosításához kattintson a végpontra, majd a a **végpont** panel, amelyen megjelenik, kattintson a **szerkesztése** , és módosítsa a **prioritású** érték szükség szerint. 
8. Kattintson a **mentése** menteni a végpont beállításainak módosításához.
9. Miután végzett a konfiguráció módosításait, kattintson a **mentése** az oldal alján.
10. A módosítások teszteléséhez a konfiguráció a következő:
    1.  A portál keresősávjában, keresse meg a Traffic Manager-profil nevét és a Traffic Manager-profil az eredmények között kattintson, amely jelenik meg.
    2.  Az a **Traffic Manager** profil panelen, kattintson a **áttekintése**.
    3.  A **Traffic Manager-profil** panel megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ezzel használhatja az ügyfelek (például megnyitásával, egy webböngésző használatával), mint a megfelelő végpontra van irányítva határozza meg az útválasztási típus. Ebben az esetben minden kérelemhez legyenek átirányítva az első végpont a Traffic Manager észleli, ha nem megfelelő állapotú lesz, és a forgalom automatikusan átadja a feladatokat a következő végpont.
11. Miután a Traffic Manager-profil működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón a vállalata tartománynevét átirányítása a Traffic Manager szolgáltatásbeli tartománynévre.

![Prioritás forgalom-útválasztási módszer használatával a Traffic Manager konfigurálása][1]

## <a name="next-steps"></a>További lépések


- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- Ismerje meg [Teljesítménycentrikus útválasztási mód](traffic-manager-configure-performance-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- Ismerje meg, hogyan [Traffic Manager beállításainak tesztelése](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png