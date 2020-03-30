---
title: Társviszony-létesítés beállítása a Microsofttal
titleSuffix: Azure
description: Társviszony-létesítés – áttekintés
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908913"
---
# <a name="internet-peering-overview"></a>Internetes társviszony-létesítés – áttekintés

A társviszony-létesítés a Microsoft globális hálózata (AS8075) és a hálózat közötti összekapcsolás a Microsoft online szolgáltatásokból/Microsoft Azure-szolgáltatásokból/-hoz irányuló internetes forgalom cseréje céljából. A szolgáltatók vagy szolgáltatók bármelyik Edge-telephelyünkön kérhetik a Microsofttal való kapcsolattartást. A Microsoft minden kérést áttekint annak érdekében, hogy megfeleljen a társviszony-létesítési szabályzatunknak. A Microsoft-hálózattal létesíthet társviszony-létesítést kétféleképpen:

* **Közvetlen társviszony-létesítés:**

    A társviszony-létesítés a Microsoft és a Microsoft Edge hálózata közötti közvetlen fizikai kapcsolatokon keresztül jön létre. A BGP-munkamenetek ezeken a kapcsolatokon keresztül vannak konfigurálva az útválasztási szabályzatunk szerint, és előre egyeztetett megállapodást alkalmaznak. Ezt pni-nek is nevezik.

* **Cseretárs-létesítés:**

    Ez az Internetes csereprogramok (IX) szabványos nyilvános társviszony-létesítési kapcsolataira vonatkozik. A Microsoft-hálózat és a hálózat közötti fizikai kapcsolatok a IX által működtetett kapcsolóhálón keresztül jönnek létre. A BGP-munkamenetek a IX által biztosított IP-terület alapján vannak konfigurálva.

## <a name="benefits-of-peering-with-microsoft"></a>A Microsofttal való társviszony-létesítés előnyei
* Csökkentse az átviteli költségeket a Microsoft-forgalom microsoftos társviszony-létesítésével történő biztosításával.
* A hálózati ugrások és a Microsoft Edge-hálózat késésének csökkentésével javíthatja ügyfelei teljesítményét.
* Az ügyfélforgalmat a hálózat vagy az átviteli szolgáltató hálózatának hibái ellen úgy védheti meg, hogy a Microsofttal redundáns helyeken létesít.
* Ismerje meg a társviszony-létesítési kapcsolatok teljesítménymutatóit, és használja az elemzési adatokat a hálózat hibaelhárításához.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Az Azure használatának előnyei a társviszony-létesítés beállításához

Kérheti, hogy társviszony-létesítés a Microsoft az Azure PowerShell vagy a portál használatával. Az ily módon beállított társviszony-létesítés Azure-erőforrásként van kezelve, és a következő előnyökkel jár:
* Egyszerűsített és automatizálható lépések a Microsofttal való társviszony-létesítés beállításához és kezeléséhez.
* Gyors és egyszerű módja annak, hogy megtekinthesse és kezelje az összes társviszonyt egy helyen.
* Kövesse nyomon az összes kapcsolat állapotát és sávszélességét.
* Használhatja ugyanazt az előfizetést az Azure Cloud Services eléréséhez.

Ha már létrehozott társtársi kapcsolatot a Microsofttal, azokat **örökölt társviszony-létesítésnek nevezzük.** Dönthet úgy, hogy az azure-erőforrásként kezeli az ilyen társviszony-létesítéseket, hogy kihasználja a fenti előnyöket. Új társviszony-létesítési kérelem elküldéséhez vagy az örökölt társviszony-létesítés Azure-erőforrássá történő konvertálásához kövesse az alábbi **következő lépések** szakaszban található hivatkozásokat.

## <a name="peering-policy"></a>Társviszony-létesítési szabályzat
A Microsoft szelektív, de általában nyitott társviszony-létesítési szabályzattal rendelkezik. A társak kiválasztása teljesítmény, képesség alapján történik, és ahol kölcsönös előnyök állnak fenn, és bizonyos műszaki, kereskedelmi és jogi követelmények vonatkoznak rájuk. További információt a [társviszony-létesítési házirend című témakörben talál.](policy.md)

## <a name="faq"></a>GYIK
A társviszony-létesítésről szóló gyakori kérdésekről az [Internetes társviszony-létesítés – gyakori kérdések](faqs.md).

## <a name="next-steps"></a>További lépések

* A Közvetlen társviszony-létesítés microsoftos társviszony-létesítési lépéseiről a [Közvetlen társviszony-létesítési útmutató című útmutatót kell követnie.](walkthrough-direct-all.md)
* Ha többet szeretne megtudni az Exchange-társviszony-létesítés microsoftos beállításának lépéseiről, kövesse az [Exchange társviszony-létesítési útmutatóját](walkthrough-exchange-all.md)
* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](https://docs.microsoft.com/azure/networking/networking-overview) ismerkedhet meg.
