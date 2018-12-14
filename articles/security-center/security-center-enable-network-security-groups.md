---
title: Az Azure Security Center hálózati biztonsági csoportok engedélyezése |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **hálózati biztonsági csoportok engedélyezése**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: f10365f4acf62ba4719e89932f18433e70925ebf
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341762"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Az Azure Security Center hálózati biztonsági csoportok engedélyezése
Az Azure Security Center javasolja, hogy egy hálózati biztonsági csoport (NSG) engedélyezése, ha egy nem engedélyezett. NSG-k, amelyek engedélyezik vagy megtagadják a hálózati forgalmat a virtuális hálózatban futó Virtuálisgép-példányokat hozzáférés-vezérlési lista (ACL) szabályok listáját tartalmazzák. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra. Emellett a forgalmat egy adott virtuális gépre is lehet korlátozni további korlátozásokat egy NSG-t közvetlenül a virtuális Gépre. További tudnivalókért tekintse meg [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)

Ha nem rendelkezik NSG-k engedélyezve van, a Security Center ajánlásokat két Önnek: Hálózati biztonsági csoportok engedélyezése az alhálózatok és a hálózati biztonsági csoportok engedélyezése virtuális gépeken. Kiválaszthatja, melyik szint, alhálózatot vagy a alkalmazni az NSG-ket a virtuális gép.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **hálózati biztonsági csoportok engedélyezése** alhálózatok vagy virtuális gépeken.
   ![Hálózati biztonsági csoportok engedélyezése][1]
2. Ekkor megnyílik a panel **hiányzó hálózati biztonsági csoportok konfigurálása** alhálózatok vagy virtuális gépek, a javaslat kiválasztott függően. Válassza ki egy alhálózatot vagy egy NSG-t konfigurálhatja a virtuális gép.

   ![Alhálózati NSG konfigurálása][2]

   ![Virtuális gép NSG konfigurálása][3]
3. Az a **hálózati biztonsági csoport választása** panelen jelöljön ki egy létező NSG-t vagy **új létrehozása** létrehozni egy NSG-t.

   ![Hálózati biztonsági csoport választása][4]

Ha létrehoz egy NSG-t, kövesse a [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) hozzon létre egy NSG-t és a biztonsági szabályokat állíthat be.

## <a name="see-also"></a>Lásd még
Ez a cikk láthatta, hogyan valósíthat meg a Security Center javaslatait "A hálózati biztonsági csoportok engedélyezése" az alhálózatokra vagy virtuális gépekre. Az NSG-k használatának engedélyezésével kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)
* [A hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
