---
title: Hálózati biztonsági csoportok engedélyezése a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan implementálhatja a Azure Security Center javaslatot a **hálózati biztonsági csoportok engedélyezéséhez**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60911445"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Hálózati biztonsági csoportok engedélyezése a Azure Security Centerban
Azure Security Center javasolja, hogy engedélyezzen egy hálózati biztonsági csoportot (NSG), ha még nincs engedélyezve az egyik. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják a Virtual Networkban lévő virtuálisgép-példányok hálózati forgalmát. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra. Emellett az egyes virtuális gépek felé irányuló forgalom tovább korlátozható egy NSG közvetlenül az adott virtuális géphez való társításával. További információ: [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)

Ha nincs engedélyezve a NSG, Security Center két javaslatot nyújt Önnek: Engedélyezze a hálózati biztonsági csoportokat az alhálózatokon, és engedélyezze a hálózati biztonsági csoportokat a virtuális gépeken. Kiválaszthatja, hogy melyik szintet, alhálózatot vagy virtuális gépet kívánja alkalmazni a NSG alkalmazásához.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok** panelen válassza a **hálózati biztonsági csoportok engedélyezése** az alhálózatokon vagy a virtuális gépeken lehetőséget.
   ![Hálózati biztonsági csoportok engedélyezése][1]
2. Ekkor megnyílik a panel, a kiválasztott javaslattól függően a **hiányzó hálózati biztonsági csoportok konfigurálása** az alhálózatokhoz vagy a virtuális gépekhez. Válasszon egy alhálózatot vagy egy virtuális gépet a NSG konfigurálásához.

   ![Alhálózat NSG konfigurálása][2]

   ![A virtuális gép NSG konfigurálása][3]
3. A **hálózati biztonsági csoport kiválasztása** panelen válasszon ki egy meglévő NSG, vagy válassza az **új létrehozása** lehetőséget egy NSG létrehozásához.

   ![Hálózati biztonsági csoport kiválasztása][4]

Ha létrehoz egy NSG, kövesse a [hálózati biztonsági csoport kezelése](../virtual-network/manage-network-security-group.md) NSG létrehozásához és a biztonsági szabályok megadásához szükséges lépéseket.

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatja, hogyan valósítja meg a "hálózati biztonsági csoportok engedélyezése" című Security Center ajánlást az alhálózatok vagy virtuális gépek számára. A NSG engedélyezésével kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport kezelése](../virtual-network/manage-network-security-group.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági Hírek és információk beszerzése.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
