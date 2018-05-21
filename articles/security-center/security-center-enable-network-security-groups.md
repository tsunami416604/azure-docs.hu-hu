---
title: Engedélyezze a hálózati biztonsági csoportok az Azure Security Centerben |} Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás **engedélyezze a hálózati biztonsági csoportok**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 3c0ad4a0e1a5f4f2fd6def4f29599e2e55eb1a9d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Engedélyezze a hálózati biztonsági csoportok az Azure Security Centerben
Az Azure Security Center azt javasolja, hogy a hálózati biztonsági csoport (NSG) engedélyezése, ha egy már nincs engedélyezve. NSG tartalmaz, amelyek engedélyezik vagy megtagadják a Virtuálisgép-példány egy virtuális hálózat hálózati forgalmának hozzáférés-vezérlési lista (ACL) szabályok listáját. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra. Emellett az egyes virtuális gép is lehet korlátozni további korlátozásokat NGS társítása közvetlenül a virtuális gép. További részletek további [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)

Ha nem rendelkezik az NSG-k engedélyezve van, a Security Center ajánlásokat két Önnek: engedélyezése hálózati biztonsági csoportok alhálózatok és a hálózati biztonsági csoportok engedélyezése virtuális gépeken. Mely szint, az alhálózati vagy a virtuális Gépet, alkalmazza az NSG-k választja.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **engedélyezze a hálózati biztonsági csoportok** alhálózatok vagy virtuális gépeken.
   ![Hálózati biztonsági csoportok engedélyezése][1]
2. Ekkor megnyílik a panel **hiányzó hálózati biztonsági csoportok beállítása** alhálózatok vagy virtuális gépekhez, attól függően, hogy a kiválasztott javaslat. Válassza ki egy alhálózatot vagy egy virtuális gép konfigurálása egy NSG.

   ![NSG alhálózat konfigurálása][2]

   ![NSG a virtuális gép konfigurálása][3]
3. Az a **válassza a hálózati biztonsági csoport** panelen, jelöljön ki egy meglévő NSG vagy **hozzon létre új** létrehozni egy NSG.

   ![Hálózati biztonsági csoport választása][4]

Ha létrehoz egy NSG-t, kövesse a [kezelése az Azure portál használatával NSG-k](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) hozzon létre egy NSG-t és a biztonsági szabályokat állíthat be.

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatta a Security Center ajánlott "A hálózati biztonsági csoportok engedélyezése" alhálózatok és virtuális gépek megvalósításához. Az NSG-k engedélyezésére vonatkozó további tudnivalókért olvassa el a következőket:

* [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)
* [A hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) --az Azure biztonsági legfrissebb hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
