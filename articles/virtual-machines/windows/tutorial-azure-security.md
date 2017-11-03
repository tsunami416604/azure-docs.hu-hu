---
title: "Az Azure Security Center és a Windows virtuális gépek Azure-ban |} Microsoft Docs"
description: "További tudnivalók a Azure Windows virtuális gép az Azure Security Center biztonsági."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: adb00e28b0b204858a763f83836ee2ac96f8f9e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Virtuális gép biztonsági figyelje az Azure Security Centerben

Az Azure Security Center segítségével, hogy lássák az Azure-erőforrás biztonsági eljárásokat. A Security Center kínál, integrált biztonsági figyelés. Ellenkező esetben szabályzatkezelést fenyegetések azonosítására képes. Ebben az oktatóanyagban további tudnivalók az Azure Security Center, és hogyan:
 
> [!div class="checklist"]
> * Adatgyűjtés beállítása
> * Biztonsági házirendek beállítása
> * Megtekintheti és konfigurációs állapotát kapcsolatos problémák megoldása
> * Tekintse át a fenyegetést észlelt  

## <a name="security-center-overview"></a>A Security Center áttekintése

A Security Center lehetséges a virtuális gép (VM) konfigurációs problémák azonosítja, és biztonsági fenyegetések megcélzott. Ezek közé tartozik a virtuális gépek hálózati biztonsági csoportok, a nem titkosított lemezek és a találgatásos Remote Desktop Protocol (RDP) támadások hiányoznak. Az információ jelenik meg a Security Center irányítópultjának könnyen olvasható diagramokban.

Hozzáférni a Security Center irányítópultjának, az Azure portálon, a menüben válassza ki a **Security Center**. Az irányítópulton tekintse meg az Azure környezetben biztonsági állapotát, található az aktuális javaslatok számát, és fenyegetést riasztások aktuális állapotának megtekintése. A részletek megtekintéséhez magas szintű diagramok bővítheti.

![Security Center irányítópultjának](./media/tutorial-azure-security/asc-dash.png)

A Security Center túllép adatok felderítés javaslatokkal szolgál, hogy az észlelt problémákat. Például ha egy virtuális Gépet egy hálózati biztonsági csoport nélkül lett telepítve, a Security Center ajánlás olyan környezetekben, a javítási lépéseket, amelyek jeleníti meg. A Security Center kontextusában maradjanak automatikus javítási kap.  

![Javaslatok](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Adatgyűjtés beállítása

Előtt a virtuális gép biztonsági beállításokat tud bejutni látható, akkor be kell állítania a Security Center adatgyűjtés. Ebbe beletartozik az adatok gyűjtésének bekapcsolása és az összegyűjtött adatok tárolásához Azure storage-fiók létrehozása. 

1. A Security Center irányítópultján kattintson **biztonsági házirend**, majd válassza ki az előfizetését. 
2. A **adatgyűjtés**, jelölje be **a**.
3. A storage-fiók létrehozásához válassza **válasszon tárfiókot**. Ezt követően válassza **OK**.
4. Az a **biztonsági házirend** panelen válassza **mentése**. 

A Security Center adatok szolgáltatások ügynököt telepíti az összes virtuális gépeken, és adatgyűjtés megkezdése. 

## <a name="set-up-a-security-policy"></a>A biztonsági házirendben

Biztonsági házirendek határozzák meg a, amelynek a Security Center adatokat gyűjt, és ajánlásokat. Az Azure-erőforrások más-más részhalmazához különböző biztonsági házirendeket is alkalmazhat. Bár az alapértelmezés szerint minden házirendelemek szemben Azure-erőforrások értékelődnek ki, kikapcsolhatja az összes Azure-erőforrások vagy erőforráscsoport egyedi házirend elemek. A Security Center biztonsági házirendekkel kapcsolatos részletesebb információk: [biztonsági házirendek beállítása az Azure Security Centerben](../../security-center/security-center-policies.md). 

Az összes Azure-erőforrások biztonsági házirend beállítása:

1. Válassza ki a Security Center irányítópultjának **biztonsági házirend**, majd válassza ki az előfizetését.
2. Válassza ki **megakadályozási szabályzat**.
3. Kapcsolja be, vagy kapcsolja ki az összes Azure-erőforrások alkalmazni kívánt házirend-elemeket.
4. Amikor elkészült, válassza a beállítások, válassza ki a **OK**.
5. Az a **biztonsági házirend** panelen válassza **mentése**. 

Az adott erőforráscsoport szabályzat beállítása:

1. Jelölje ki a Security Center irányítópultjának **biztonsági házirend**, majd válassza ki egy erőforráscsoportot.
2. Válassza ki **megakadályozási szabályzat**.
3. Kapcsolja be, vagy kapcsolja ki az erőforráscsoport alkalmazni kívánt házirend-elemeket.
4. A **ÖRÖKLÉSI**, jelölje be **egyedi**.
5. Amikor elkészült, válassza a beállítások, válassza ki a **OK**.
6. Az a **biztonsági házirend** panelen válassza **mentése**.  

Ön is bármikor kikapcsolhatják az adatgyűjtést ezen a lapon megadott erőforráscsoport.

A következő példában egy egyedi házirend nevű erőforráscsoport létrejött *myResoureGroup*. Ezt a házirendet, a lemez titkosítása és a webes alkalmazás tűzfal javaslatok ki vannak kapcsolva.

![Egyedi házirend](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Virtuális gép konfigurációs állapotának megtekintése

Miután engedélyezve van a használatra vonatkozó adatok gyűjtésének és állíthat be a biztonsági házirendet, a Security Center veszi át a riasztások és javaslatok. Mivel a virtuális gépek vannak telepítve, az adatok gyűjtése ügynök telepítve van. A Security Center majd az új virtuális gépek feltöltve adatokkal. Részletes információ a virtuális gép konfigurációs állapotát: [a Security Center a virtuális gépek védelme](../../security-center/security-center-virtual-machine-recommendations.md). 

Összegyűjtött adatok, az erőforrás állapota minden egyes virtuális gép és a kapcsolódó Azure-erőforrás összesíti. Az információk könnyen olvasható diagramon látható. 

Erőforrás állapotának megtekintéséhez:

1.  A biztonsági Center irányítópultján a **erőforrás biztonsági állapota**, jelölje be **számítási**. 
2.  Az a **számítási** panelen válassza **virtuális gépek**. Ez a nézet a konfigurációs állapotának összegzését tartalmazza a virtuális géphez.

![Számítási állapota](./media/tutorial-azure-security/compute-health.png)

A virtuális gépek összes javaslatok megtekintéséhez válasszon a virtuális Gépet. Javaslatok, a javítási Ez az oktatóanyag következő szakasza részletesen ismertetnek.

## <a name="remediate-configuration-issues"></a>Konfigurációs problémák megoldásához

A konfigurációs adatok feltöltése a Security Center elindítása után javaslatok alapján készülnek a biztonsági házirend beállítása. Például ha egy virtuális Gépet egy társított hálózati biztonsági csoport nélkül be lett állítva, a javaslatokkal kattintva létrehozhat egyet. 

Az összes javaslatok listájának megtekintéséhez: 

1. Válassza ki a Security Center irányítópultjának **javaslatok**.
2. Válassza ki az adott javaslat. Megjelenik egy lista, amelynek erőforrásait a javaslat vonatkozik.
3. Alkalmaz egy javaslatot, válassza ki az adott erőforrás. 
4. Kövesse az utasításokat a javítási lépéseket. 

Sok esetben a Security Center itt végrehajtandó lépések, anélkül, hogy a Security Center ajánlást megoldására. A következő példában a Security Center észleli a hálózati biztonsági csoport, amely rendelkezik egy korlátlan bejövő szabályt. A javaslat lapon kiválaszthatja a **bejövő szabályok szerkesztése** gombra. A felhasználói felület, ahhoz szükséges, hogy módosítsa a szabály akkor jelenik meg. 

![Javaslatok](./media/tutorial-azure-security/remediation.png)

Javaslatok szervizelt vannak, mint megoldottként vannak beállítva. 

## <a name="view-detected-threats"></a>Észlelt fenyegetéseket megtekintése

Mellett erőforrás konfigurációs javaslatait a Security Center figyelmeztetések jeleníti meg. A biztonsági riasztások szolgáltatás összesíti az egyes virtuális gép, Azure hálózati naplók és összekapcsolt partnermegoldások biztonsági fenyegetések ellen Azure-erőforrások észlelését gyűjtött adatokat. A Security Center threat detection képességeivel kapcsolatos részletesebb információk: [az Azure Security Center az észlelési képességek](../../security-center/security-center-detection-capabilities.md).

A biztonsági riasztások funkció használatához az IP-címek Security Center hogy *szabad* való *szabványos*. Egy 30 napos **ingyenes próbaverzió** helyez át a magasabb szintű tarifacsomagban használható esetén érhető el. 

Ez a tarifacsomag módosítása:  

1. A Security Center irányítópultján kattintson **biztonsági házirend**, majd válassza ki az előfizetését.
2. Válassza ki **tarifacsomag**.
3. Az új réteget, majd válassza ki és **válasszon**.
4. Az a **biztonsági házirend** panelen válassza **mentése**. 

Miután megváltoztatta a tarifacsomagot, a biztonsági riasztások graph feltöltéséhez, mint a biztonsági fenyegetések észlelése kezdődik.

![Biztonsági riasztások](./media/tutorial-azure-security/security-alerts.png)

Válasszon ki egy riasztást, információk megtekintése. Például láthatja a fenyegetés, az észlelés időpontja, az összes fenyegetés kísérletek és javasolt elhárítási műveletek leírását. A következő példában RDP találgatásos támadás volt észlelhető, 294 sikertelen RDP próbálnak. Javasolt megoldás valósul meg.

![RDP-támadás](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban az Azure Security Center beállítása, és ezután tekintse át a virtuális gépek a Security Center. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatgyűjtés beállítása
> * Biztonsági házirendek beállítása
> * Megtekintheti és konfigurációs állapotát kapcsolatos problémák megoldása
> * Tekintse át a fenyegetést észlelt

A következő oktatóanyag áttekintésével megismerheti, hogyan CI/CD folyamat létrehozása a Visual Studio Team Services és az IIS-t futtató Windows virtuális gép továbblépés.

> [!div class="nextstepaction"]
> [A Visual Studio Team Services CI/CD-folyamat](./tutorial-vsts-iis-cicd.md)
