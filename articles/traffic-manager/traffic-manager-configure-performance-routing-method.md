---
title: Konfigurálja a teljesítmény forgalom-útválasztási módszer az Azure Traffic Managerrel |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan Traffic Manager konfigurálása a végpontra irányíthatja a forgalmat a legkisebb késés
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
ms.openlocfilehash: c378043a9e10a0aed5344ac3182af6163d217c7b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140468"
---
# <a name="configure-the-performance-traffic-routing-method"></a>A teljesítmény forgalom-útválasztási módszer konfigurálása

A teljesítmény forgalom-útválasztási módszer lehetővé teszi, hogy a forgalom irányítása a végpontra a legkisebb késést az ügyfél hálózati. Az adatközpontban a legkisebb késéssel rendelkező jellemzően a legközelebbi földrajzi távolság. A forgalom-útválasztási módszer nem a valós idejű módosításokat a hálózati konfigurációban fiók vagy betöltése.

##  <a name="to-configure-performance-routing-method"></a>Teljesítménycentrikus útválasztási mód konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávjában keressen a **Traffic Manager-profilok** és kattintson a profil nevét, amelyet szeretne az útválasztási módszer konfigurálása.
3. Az a **Traffic Manager-profil** panelen ellenőrizze, hogy a cloud services és a webhelyeket, amelyeket fel szeretne venni a konfiguráció jelen.
4. Az a **beállítások** területén kattintson **konfigurációs**, majd a a **konfigurációs** panelen adja meg a következőket:
    1. A **forgalom-útválasztási módszer beállításait**, a **útválasztási módszer** kiválasztása **teljesítmény**.
    2. Állítsa be a **Végpontfigyelő beállításai** azonos az összes minden végpontot a profiljában, az alábbiak szerint:
        1. Válassza ki a megfelelő **protokoll**, és adja meg a **Port** számát. 
        2. A **elérési** írja be a perjellel */*. Végpontok monitorozása, az elérési útvonalat és fájlnevet kell megadnia. A perjel "/" érvényes bejegyzés a relatív elérési útja, és azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
        3. Kattintson a lap tetején **mentése**.
5.  A módosítások teszteléséhez a konfiguráció a következő:
    1.  A portál keresősávjában, keresse meg a Traffic Manager-profil nevét és a Traffic Manager-profil az eredmények között kattintson, amely jelenik meg.
    2.  Az a **Traffic Manager** profil panelen, kattintson a **áttekintése**.
    3.  A **Traffic Manager-profil** panel megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ezzel használhatja az ügyfelek (például megnyitásával, egy webböngésző használatával), mint a megfelelő végpontra van irányítva határozza meg az útválasztási típus. Ebben az esetben az összes kérelem irányul, a végpont a legkisebb késéssel rendelkező az ügyfél hálózatról.
6. Miután a Traffic Manager-profil működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón a vállalata tartománynevét átirányítása a Traffic Manager szolgáltatásbeli tartománynévre.

![Teljesítmény forgalom-útválasztási módszer használatával a Traffic Manager konfigurálása][1]

## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- Ismerje meg, hogyan [Traffic Manager beállításainak tesztelése](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png