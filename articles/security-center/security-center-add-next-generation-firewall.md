---
title: Újgenerációs tűzfal hozzáadása az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatainak **Újgenerációs tűzfal hozzáadása** és **útvonal traffice Újgenerációs tűzfalon keresztül csak**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a0f472d32e878412532927327a36fe674d4cf6bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261672"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Újgenerációs tűzfal hozzáadása az Azure Security Centerben
Az Azure Security Center javasolhatja egy új generációs tűzfal (NGFW) hozzáadása egy Microsoft-partner növelheti a védelmet. Ez a dokumentum végigvezeti erre egy példát.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **Újgenerációs tűzfal hozzáadása**.
   ![Újgenerációs tűzfal hozzáadása][1]
2. Az a **Újgenerációs tűzfal hozzáadása** panelen jelöljön ki egy végpontot.
   ![Válasszon végpontot][2]
3. Egy második **Újgenerációs tűzfal hozzáadása** panel nyílik meg. Választhatja egy meglévő megoldás használata, ha rendelkezésre áll, vagy létrehozhat egy újat. Ebben a példában érhetők el semmilyen meglévő megoldásokat, létrehozunk egy NGFW.
   ![Új generációs tűzfal létrehozása][3]
4. Szeretne létrehozni egy NGFW, integrált partnereink által kínált listájából válasszon ki egy megoldást. Ebben a példában kiválasztjuk **a Check Point**.
   ![Új generációs tűzfal-megoldás kiválasztása][4]
5. A **a Check Point** panel nyílik meg, így a partneri megoldással kapcsolatos információkat. Válassza ki **létrehozás** az információk panelen.
   ![Tűzfal-információk panel][5]
6. A **hozzon létre virtuális gépet** panel nyílik meg. Itt adhatja meg az NGFW futtató virtuális gép (VM) üzembe helyezése szükséges adatokat. Kövesse a lépéseket, és adja meg az NGFW szükséges adatokat. Válassza ki az OK gombra a alkalmazni.
   ![Újgenerációs futtató virtuális gép létrehozása][6]

## <a name="route-traffic-through-ngfw-only"></a>Csak az újgenerációs tűzfalon keresztül haladjon a forgalom
Lépjen vissza a **javaslatok** panelen. Egy új bejegyzést jött létre, miután hozzáadta az NGFW keresztül a Security Center nevű **irányíthatja a forgalmat Újgenerációs tűzfalon keresztül csak**. Ez a javaslat jön létre, csak akkor, ha telepítve az NGFW a Security Center segítségével. Ha az Internet felé néző végpontok, a Security Center javasolja, hogy konfigurálja a hálózati biztonsági csoportszabályok, amely a bejövő forgalom kényszeríti a virtuális géphez az Újgenerációs tűzfalon keresztül.

1. Az a **javaslatok panel**válassza **irányíthatja a forgalmat Újgenerációs tűzfalon keresztül csak**.
   ![Csak az újgenerációs tűzfalon keresztül haladjon a forgalom][7]
2. Ekkor megnyílik a panel **irányíthatja a forgalmat Újgenerációs tűzfalon keresztül csak**, irányíthatja a forgalmat a virtuális gépek listája látható. Válasszon ki egy virtuális gépet a listából.
   ![Válasszon ki egy virtuális Gépet][8]
3. Megnyílik egy panel, a kiválasztott virtuális géphez, kapcsolódó bejövő szabályok megjelenítése. Egy leírást nyújt a lehetséges következő lépésekről további információt. Válassza ki **bejövő szabályok szerkesztése** folytatásához egy bejövő szabály szerkesztését. Az elvárás, hogy **forrás** értéke **bármely** az Internet felé néző végpontokhoz az NGFW csatolt. A bejövő szabály tulajdonságainak kapcsolatos további információkért lásd: [biztonsági szabályok](../virtual-network/security-overview.md#security-rules).
   ![A hozzáférés korlátozásához szabályok konfigurálása][9]
   ![bejövő szabály szerkesztése][10]

## <a name="see-also"></a>Lásd még
Ez a dokumentum láthatta, hogyan valósíthat meg a Security Center javaslatait "Hozzáadása egy új generációs tűzfal." NGFWs és a Check Point partnermegoldás kapcsolatos további információkért tekintse meg a következőket:

* [Új generációs tűzfal](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [A Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi.

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
