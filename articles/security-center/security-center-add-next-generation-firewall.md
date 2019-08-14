---
title: A következő generációs tűzfal hozzáadása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan implementálhatja a Azure Security Center javaslatokat a **következő generációs tűzfal hozzáadásával** , és **csak a NGFW keresztül irányíthatja a forgalmat**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706986"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Új generációs tűzfal hozzáadása a Azure Security Center
Azure Security Center javasoljuk, hogy a biztonsági védelem növeléséhez vegyen fel egy új generációs tűzfalat (NGFW) egy Microsoft-partnertől. Ez a dokumentum végigvezeti a következő példán.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok** panelen válassza a **következő generációs tűzfal hozzáadása**elemet.
   ![Újgenerációs tűzfal hozzáadása][1]
2. A **következő generációs tűzfal hozzáadása** panelen válasszon ki egy végpontot.
   ![Végpont kiválasztása][2]
3. Megnyílik a **következő generációs tűzfal** panel második hozzáadása. Lehetősége van meglévő megoldás használatára, ha elérhető, vagy létrehozhat egy újat is. Ebben a példában nem érhető el meglévő megoldás, ezért hozzunk létre egy NGFW.
   ![Következő generációs tűzfal létrehozása][3]
4. NGFW létrehozásához válasszon ki egy megoldást az integrált partnerek listájából. Ebben a példában a jelölőnégyzetetválasztjuk.
   ![A következő generációs tűzfal megoldás kiválasztása][4]
5. Megnyílik a **pipa** panel, amely információt nyújt a partneri megoldásról. Válassza a **Létrehozás** lehetőséget az információ panelen.
   ![Tűzfal adatai panel][5]
6. Megnyílik a **virtuális gép létrehozása** panel. Itt megadhatja a NGFW futtató virtuális gép (VM) üzembe helyezéséhez szükséges adatokat. Kövesse a lépéseket, és adja meg a szükséges NGFW-információkat. Válassza az OK gombot az alkalmazáshoz.
   ![Virtuális gép létrehozása a NGFW futtatásához][6]

## <a name="route-traffic-through-ngfw-only"></a>Csak az újgenerációs tűzfalon keresztül haladjon a forgalom
Térjen vissza a **javaslatok** panelre. Új bejegyzés lett létrehozva, miután hozzáadta az NGFW-t a Security Centeron keresztül, és **csak NGFW keresztül irányította a forgalmat**. Ez a javaslat csak akkor jön létre, ha Security Centeron keresztül telepítette a NGFW. Ha internetes végpontokkal rendelkezik, Security Center javasolja, hogy konfigurálja a hálózati biztonsági csoportok azon szabályait, amelyek a NGFW keresztül kényszerítik a bejövő forgalmat a virtuális gépre.

1. A **javaslatok**panelen válassza a **forgalom átirányítása csak NGFW keresztül**lehetőséget.
   ![Csak az újgenerációs tűzfalon keresztül haladjon a forgalom][7]
2. Ekkor megnyílik a NGFW-alapú forgalom, amely azokat a virtuális gépeket sorolja fel, amelyeken **át**lehet irányítani a forgalmat. Válasszon ki egy virtuális gépet a listából.
   ![Virtuális gép kiválasztása][8]
3. Megnyílik a kiválasztott virtuális gép panelje, amely a kapcsolódó bejövő szabályokat jeleníti meg. A Leírás további információkat tartalmaz a lehetséges következő lépésekről. Válassza a **Bejövő szabályok szerkesztése** lehetőséget a Bejövő szabály szerkesztésének folytatásához. A várt érték az, hogy a **forrás** nincs BEÁLLÍTVA a NGFW kapcsolódó internetes végpontokhoz. További információ a Bejövő szabály tulajdonságairól: [biztonsági szabályok](../virtual-network/security-overview.md#security-rules).
   ![Szabályok konfigurálása a hozzáférés][9]
   ![-szerkesztési Bejövő szabály korlátozásához][10]

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudhatta, hogyan valósítja meg a "következő generációs tűzfal hozzáadása" című Security Center javaslatot. A NGFWs és a ellenőrzési pont partneri megoldásával kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Következő generációs tűzfal](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Ellenőrzési pont vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – útmutató a biztonsági házirendek konfigurálásához.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
