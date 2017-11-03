---
title: "Új generációs tűzfal hozzáadása az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center javaslatait ** adja hozzá a következő generációs tűzfal ** és ** útvonal traffice keresztül NGFW csak **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 30589d0a943517c03394a3aae7c03c8094e78c1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Új generációs tűzfal hozzáadása az Azure Security Centerben
Az Azure Security Center javasolhatja új generációs tűzfal (NGFW) hozzáadása egy Microsoft-partner növelje a biztonsági védelmet. Ez a dokumentum útmutatást nyújt a példa bemutatja, hogyan ehhez.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **adja hozzá az új generációs tűzfal**.
   ![Újgenerációs tűzfal hozzáadása][1]
2. Az a **adja hozzá az új generációs tűzfal** panelen, jelöljön ki egy végpontot.
   ![Válasszon ki egy végpontot][2]
3. Egy második **adja hozzá az új generációs tűzfal** panel nyílik meg. Választhat egy meglévő megoldást használni, ha rendelkezésre áll, vagy létrehozhat egy újat. Ebben a példában nincsenek nincs meglévő megoldások, létrehozhatunk egy NGFW.
   ![Hozzon létre új generációs tűzfal][3]
4. Egy NGFW létrehozásához válassza a megoldás az integrált partnereink listájából. Ez a példa azt válassza **Check Point**.
   ![Új generációs tűzfal-megoldás kiválasztása][4]
5. A **Check Point** panel nyílik meg, biztosítva a partnermegoldáshoz kapcsolatos információkat. Válassza ki **létrehozása** az adatokat a panelen.
   ![Tűzfal információk panel][5]
6. A **hozzon létre virtuális gépet** panel nyílik meg. Itt adhatja meg az NGFW futó virtuális gépek (VM) lépett szükséges adatokat. Kövesse a lépéseket, és adja meg a szükséges NGFW adatokat. Kattintson az OK gombra alkalmazni.
   ![NGFW futtatásához a virtuális gép létrehozása][6]

## <a name="route-traffic-through-ngfw-only"></a>Csak az újgenerációs tűzfalon keresztül haladjon a forgalom
Lépjen vissza a **javaslatok** panelen. Egy új bejegyzést jött létre, miután hozzáadta a Security Center nevű keresztül egy NGFW **keresztül NGFW forgalmat csak**. Ez a javaslat jön létre, csak akkor, ha telepítette a NGFW biztonsági központon keresztül történik. Ha Internet felé néző végpontok, a Security Center azt javasolja, hogy a bejövő forgalom kényszerítheti a NGFW keresztül a virtuális gép hálózati biztonsági csoport szabályok konfigurálása.

1. Az a **javaslatok panel**, jelölje be **keresztül NGFW forgalmat csak**.
   ![Csak az újgenerációs tűzfalon keresztül haladjon a forgalom][7]
2. Ekkor megnyílik a panel **keresztül NGFW forgalmat csak**, amely felsorolja azokat a virtuális gépek, amelyek irányíthatja a forgalmat. Jelöljön ki egy virtuális Gépet a listából.
   ![Jelöljön ki egy virtuális Gépet][8]
3. A kijelölt virtuális gép ekkor megnyílik egy panel, kapcsolódó bejövő szabályok. Olyan leírást nyújt további információt a következő lépést. Válassza ki **bejövő szabályok szerkesztése** egy bejövő forgalomra vonatkozó szabály szerkesztése gombra. Az általános gyakorlat, hogy **forrás** értéke nem **bármely** az Internet felé néző végpontok az NGFW kapcsolódik. A bejövő szabály tulajdonságait kapcsolatos további információkért lásd: [NSG-szabályok](../virtual-network/virtual-networks-nsg.md#nsg-rules).
   ![A hozzáférés korlátozásához szabályok konfigurálása][9]
   ![bejövő forgalomra vonatkozó szabály szerkesztése][10]

## <a name="see-also"></a>Lásd még:
Ez a dokumentum bemutatta megvalósításához a Security Center javaslat "Vegyen fel új generációs tűzfal." NGFWs és a Check Point partnermegoldáshoz kapcsolatos további tudnivalókért olvassa el a következőket:

* [Új generációs tűzfal](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – megtudhatja, hogyan konfigurálhatja a biztonsági szabályzatokat.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

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
