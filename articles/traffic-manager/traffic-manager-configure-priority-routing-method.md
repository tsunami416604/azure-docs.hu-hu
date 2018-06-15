---
title: Prioritás forgalom-útválasztási módszert használja az Azure Traffic Manager konfigurálása |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a prioritás forgalom-útválasztási módszert a Traffic Manager
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
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876634"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Prioritás forgalom-útválasztási módszert a Traffic Manager konfigurálása

A webhely módot, függetlenül az Azure Websitesra már meg feladatátvételi funkcióját (más néven régiónként) adatközponton belüli webhelyekhez. A TRAFFIC Manager biztosít feladatátvételt különböző adatközpontokban webhelyekhez.

Szolgáltatás feladatátvétele egy közös mintát, hogy forgalmat küldeni egy elsődleges szolgáltatást, és adja meg a feladatátvételi azonos biztonsági mentési szolgáltatások. Az alábbi lépések ismertetik a rangsorolt feladatátvételi konfigurálása az Azure felhőszolgáltatások és webhelyek:

## <a name="to-configure-the-priority-traffic-routing-method"></a>A prioritás forgalom útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávban, keresse meg a **Traffic Manager-profilok** és kattintson a profil nevére, amelyet vonatkozó útválasztási módszer konfigurálása.
3. Az a **Traffic Manager-profil** panelen ellenőrizze, hogy a felhőszolgáltatás és a webhelyeket, amelyeket a konfigurációt szeretne megtalálhatók.
4. Az a **beállítások** területen kattintson **konfigurációs**, majd a a **konfigurációs** panelen, befejeződött, az alábbi módon:
    1. A **forgalom-útválasztási módszer beállításai**, győződjön meg arról, hogy a forgalom-útválasztási módszert **prioritás**. Ha nem, kattintson a **prioritás** a legördülő listából.
    2. Állítsa be a **végpont figyelőbeállítások** azonos összes minden végponton belül ezt a profilt, az alábbiak szerint:
        1. Válassza ki a megfelelő **protokoll**, és adja meg a **Port** számát. 
        2. A **elérési** írja be a perjellel  */* . Figyelő végpontokat, az elérési útnak és fájlnévnek kell megadnia. A perjel "/" relatív elérési útja érvényes bejegyzés, és azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
        3. Kattintson a lap tetején **mentése**.
5. Az a **beállítások** kattintson **végpontok**.
6. Az a **végpontok** panelt, tekintse át a prioritási sorrendet a végpontokat. Ha bejelöli a **prioritás** forgalom-útválasztási módszert, a kijelölt végpontok kérdések sorrendjét. Ellenőrizze a végpontok a prioritásuk szerinti sorrendben történik.  Az elsődleges végpont esetében felül. Ellenőrizze a sorrendnek jelenik meg. összes kérelmet továbbítja a első és a Traffic Manager azt észleli, hogy a nem megfelelő, a forgalom automatikusan átadja a feladatokat a következő végpontot. 
7. A végpont prioritásának módosításához kattintson a végponthoz, majd a a **végpont** panel, amelyen megjelenik, kattintson **szerkesztése** , és módosítsa a **prioritás** érték szükség szerint. 
8. Kattintson a **mentése** menteni a végpont beállításainak módosításához.
9. A konfigurációs módosítások elvégzése után kattintson **mentése** az oldal alján.
10. Tesztelje a módosításokat a konfigurációt az alábbiak szerint:
    1.  A portál keresősávban, keresse meg a Traffic Manager-profil nevét, majd kattintson az eredményeket a Traffic Manager-profilt, amely a jelennek meg.
    2.  Az a **Traffic Manager** profil panelen, kattintson a **áttekintése**.
    3.  A **Traffic Manager-profil** csempe megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ez használhatja olyan ügyfelek (például úgy, hogy keresse meg webböngészővel) az beszerzése irányítja át a megfelelő végpont, határozza meg az Útválasztás típusa. Ebben az esetben összes kérelem első legyenek átirányítva a Traffic Manager azt észleli, ha nyilvánítva, és a forgalom automatikusan átadja a feladatokat a következő végpontot.
11. A Traffic Manager-profil működik, ha a vállalata tartománynevét mutasson a Traffic Manager tartományneve a mérvadó DNS-kiszolgálón a DNS-rekord szerkesztése

![Prioritás forgalom-útválasztási módszert használja a Traffic Manager konfigurálása][1]

## <a name="next-steps"></a>Következő lépések


- További tudnivalók [forgalom-útválasztási módszert súlyozott](traffic-manager-configure-weighted-routing-method.md).
- További tudnivalók [teljesítmény útválasztási módszer](traffic-manager-configure-performance-routing-method.md).
- További tudnivalók [földrajzi útválasztási módszer](traffic-manager-configure-geographic-routing-method.md).
- Megtudhatja, hogyan [Traffic Manager-beállítások tesztelésére](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png