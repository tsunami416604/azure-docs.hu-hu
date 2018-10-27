---
title: Konfigurálja a súlyozott ciklikus időszeletelési forgalom-útválasztási módszer az Azure Traffic Managerrel |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan forgalom Ciklikus időszeleteléses metódus a Traffic Managerrel
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
ms.openlocfilehash: 132cbecdad8303414f15ff3f18caaccd6d430ded
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154925"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Súlyozott forgalom-útválasztási módszer a Traffic Manager konfigurálása

Forgalom útválasztási módszer egyik biztosítanak megegyező végpontokat, amely tartalmazza a felhőszolgáltatásokat és webhelyeket, és a forgalom küldése minden egyes egyaránt. Ez a típusú forgalom-útválasztási módszer konfigurálása lépései.

> [!NOTE]
> Az Azure Web App már Ciklikus időszeleteléses terheléselosztást a webhelyekhez (amely több adatközpontot is tartalmazhat, amelyeket) egy Azure-régión belül biztosít. A TRAFFIC Manager lehetővé teszi különböző adatközpontokban található webhely elosztani a forgalmat.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Súlyozott forgalom-útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresősávjában keressen a **Traffic Manager-profilok** és kattintson a profil nevét, amelyet szeretne az útválasztási módszer konfigurálása.
3. Az a **Traffic Manager-profil** panelen ellenőrizze, hogy a cloud services és a webhelyeket, amelyeket fel szeretne venni a konfiguráció jelen.
4. Az a **beállítások** területén kattintson **konfigurációs**, majd a a **konfigurációs** panelen adja meg a következőket:
    1. A **forgalom-útválasztási módszer beállításait**, győződjön meg arról, hogy a forgalom-útválasztási módszer **súlyozott**. Ha nem, kattintson a **súlyozott** a legördülő listából.
    2. Állítsa be a **Végpontfigyelő beállításai** azonos az összes minden végpontot a profiljában, az alábbiak szerint:
        1. Válassza ki a megfelelő **protokoll**, és adja meg a **Port** számát. 
        2. A **elérési** írja be a perjellel */*. Végpontok monitorozása, az elérési útvonalat és fájlnevet kell megadnia. A perjel "/" érvényes bejegyzés a relatív elérési útja, és azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
        3. Kattintson a lap tetején **mentése**.
5. A módosítások teszteléséhez a konfiguráció a következő:
    1.  A portál keresősávjában, keresse meg a Traffic Manager-profil nevét és a Traffic Manager-profil az eredmények között kattintson, amely jelenik meg.
    2.  Az a **Traffic Manager** profil panelen, kattintson a **áttekintése**.
    3.  A **Traffic Manager-profil** panel megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ezzel használhatja az ügyfelek (például megnyitásával, egy webböngésző használatával), mint a megfelelő végpontra van irányítva határozza meg az útválasztási típus. Ebben az esetben az összes kérelem irányul a Ciklikus időszeleteléses módon végpontot.
6. Miután a Traffic Manager-profil működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón a vállalata tartománynevét átirányítása a Traffic Manager szolgáltatásbeli tartománynévre.

![Súlyozott forgalom-útválasztási módszer használatával a Traffic Manager konfigurálása][1]

## <a name="next-steps"></a>További lépések

- Ismerje meg [prioritású forgalom-útválasztási módszer](traffic-manager-configure-priority-routing-method.md).
- Ismerje meg [teljesítmény forgalom-útválasztási módszer](traffic-manager-configure-performance-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- Ismerje meg, hogyan [Traffic Manager beállításainak tesztelése](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
