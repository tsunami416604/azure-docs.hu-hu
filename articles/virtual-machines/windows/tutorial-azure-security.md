---
title: Oktatóanyag – Azure Security Center használata Azure-beli Windows rendszerű virtuális gépekhez | Microsoft Docs
description: Ebben az oktatóanyagban megismeri az Azure Security Centert, és megtudhatja, hogyan védheti meg és teheti biztonságossá Windows rendszerű virtuális gépeit az Azure-ban.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b573208107b07b47b471d9c5247b362ef144099e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-use-azure-security-center-to-monitor-windows-virtual-machines"></a>Oktatóanyag: Az Azure Security Center használata Windows rendszerű virtuális gépek megfigyeléséhez

Az Azure Security Center segítségével áttekintheti az Azure-erőforrása biztonsági intézkedéseit. A Security Center integrált biztonsági monitorozást biztosít. Képes egyébként észrevétlenül maradó fenyegetések észlelésére. Ebben az oktatóanyagban megismeri az Azure Security Centert, és a következőket sajátíthatja el:

> [!div class="checklist"]
> * Adatgyűjtés beállítása
> * Biztonsági szabályzatok beállítása
> * A konfiguráció állapotára vonatkozó problémák megtekintése és javítása
> * Észlelt fenyegetések áttekintése

## <a name="security-center-overview"></a>Security Center – Áttekintés

A Security Center azonosítja a potenciális virtuálisgép-konfigurálási problémákat és a célzott biztonsági fenyegetéseket. Ezek lehetnek olyan virtuális gépek, amelyekről hiányoznak hálózati biztonsági csoportok, nem titkosított lemezek, valamint találgatásos módszeren alapuló RDP-támadások. Az információ könnyen értelmezhető diagramokon jelenik meg a Security Center irányítópultján.

A Security Center irányítópultjának eléréséhez válassza a **Security Center** elemet az Azure Portal menüjében. Az irányítópulton megtekintheti az Azure-környezet biztonsági állapotát, megtalálja az aktuális javaslatok számát, és megtekintheti a fenyegetési riasztások aktuális állapotát. Az egyes magas szintű diagramokat kibontva további részleteket tekinthet meg.

![A Security Center irányítópultja](./media/tutorial-azure-security/asc-dash.png)

A Security Center az adatészlelésen túl javaslatokat is ad az észlelt problémák megoldására. Ha például egy virtuális gépet csatolt hálózati biztonsági csoport nélkül telepít, a Security Center megjelenít egy, az elvégezhető javítási lépéseket ismertető javaslatot. A Security Center környezetének elhagyása nélkül kaphat automatizált javításokat.  

![Javaslatok](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Adatgyűjtés beállítása

Ha a virtuális gép biztonsági konfigurációjára kíváncsi, előbb be kell állítania a Security Center adatgyűjtését. Ennek során be kell kapcsolnia az adatgyűjtést, és létre kell hoznia egy Azure Storage-fiókot a gyűjtött adatok tárolásához. 

1. A Security Center irányítópultján kattintson a **Biztonsági szabályzat** elemre, és válassza ki az előfizetését. 
2. Az **Adatgyűjtés** elemnél válassza a **Be** lehetőséget.
3. Tárfiók létrehozásához válassza a **Tárfiók kiválasztása** elemet. Ezután válassza az **OK** lehetőséget.
4. A **Biztonsági szabályzat** panelen válassza a **Mentés** lehetőséget. 

Ekkor a rendszer telepíti a Security Center adatgyűjtési ügynökét minden virtuális gépen, és megkezdődik az adatgyűjtés. 

## <a name="set-up-a-security-policy"></a>Biztonsági szabályzat beállítása

A biztonsági szabályzatok segítségével határozhatók meg azok az elemek, amelyekhez a Security Center adatokat gyűjt és javaslatokat tesz. Az Azure-erőforrások különböző halmazaira különböző biztonsági szabályzatokat alkalmazhat. Noha alapértelmezés szerint a rendszer az Azure-erőforrásokat az összes szabályzatelem szerint értékeli ki, az egyes szabályzatelemek kikapcsolhatók minden Azure-erőforráshoz, vagy egy adott erőforráscsoporthoz. Ha további információt szeretne megtudni a Security Center biztonsági szabályzatairól olvassa el a [biztonsági szabályzatok az Azure Security Centerben történő beállítását](../../security-center/security-center-policies.md) ismertető részt. 

Biztonsági szabályzat beállítása az összes Azure-erőforráshoz:

1. A Security Center irányítópultján válassza a **Biztonsági szabályzat** lehetőséget, és válassza ki az előfizetését.
2. Válassza a **Megelőzési szabályzat** lehetőséget.
3. Kapcsolja be vagy ki az összes Azure-erőforrásra alkalmazni kívánt szabályzatelemeket.
4. Ha elkészült a beállítások kiválasztásával, válassza az **OK** elemet.
5. A **Biztonsági szabályzat** panelen válassza a **Mentés** lehetőséget. 

Egy szabályzat beállítása egy adott erőforráscsoporthoz:

1. A Security Center irányítópultján kattintson a **Biztonsági szabályzat** lehetőségre, és válasszon egy erőforráscsoportot.
2. Válassza a **Megelőzési szabályzat** lehetőséget.
3. Kapcsolja be vagy ki az erőforrásra alkalmazni kívánt szabályzatelemeket.
4. Az **ÖRÖKLÉS** elemnél válassza az **Egyedi** lehetőséget.
5. Ha elkészült a beállítások kiválasztásával, válassza az **OK** elemet.
6. A **Biztonsági szabályzat** panelen válassza a **Mentés** lehetőséget.  

Ezen az oldalon egy adott erőforráscsoporthoz is kikapcsolhatja az adatgyűjtést.

A következő példában létrehozunk egy egyedi szabályzatot egy *myResourceGroup* nevű erőforráscsoporthoz. Ebben a szabályzatban ki vannak kapcsolva a lemeztitkosításra és a webalkalmazási tűzfalra vonatkozó javaslatok.

![Egyedi szabályzat](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Virtuálisgép-konfiguráció állapotának megtekintése

Miután bekapcsolta az adatgyűjtést, és beállított egy biztonsági szabályzatot, a Security Center elkezd riasztásokat és javaslatokat küldeni. A virtuális gépek üzembe helyezésekor a rendszer telepíti az adatgyűjtési ügynököt. Ezután a Security Centerbe betöltődnek az új virtuális gépekhez tartozó adatok. A virtuális gép konfigurációs állapotára vonatkozó részletes adatokkal kapcsolatban [a virtuális gépek a Security Centerben történő védelmét](../../security-center/security-center-virtual-machine-recommendations.md) ismertető szakaszban olvashat bővebben. 

Ahogy gyűlnek az adatok, az egyes virtuális gépek erőforrás-állapotát és a kapcsolódó Azure-erőforrásokat a rendszer összesíti. Az információk egy könnyen értelmezhető diagramon jelennek meg. 

Az erőforrás állapotának megtekintése:

1.  A Security Center irányítópultjának **Erőforrások biztonsági állapota** területén válassza a **Compute** elemet. 
2.  A **Compute** panelen válassza a **Virtuális gépek** lehetőséget. Ebben a nézetben megtekintheti az összes virtuális gép konfigurációs állapotának összegzését.

![Compute-állapot](./media/tutorial-azure-security/compute-health.png)

A virtuális gépre vonatkozó összes javaslat megtekintéséhez válassza ki a virtuális gépet. A javaslatokról és a javításokról a jelen oktatóanyag következő szakaszában lesz részletesebben szó.

## <a name="remediate-configuration-issues"></a>Konfigurációs problémák javítása

Miután a Security Center elkezd feltöltődni konfigurációs adatokkal, a javaslatokat a rendszer a beállított biztonsági szabályzat alapján készíti el. Ha például egy virtuális gépet a társított hálózati biztonsági csoport nélkül állított be, a rendszer javasolja, hogy hozzon létre egyet. 

A javaslatok listájának megtekintése: 

1. A Security Center irányítópultján válassza a **Javaslatok** elemet.
2. Válasszon ki egy adott javaslatot. Megjelenik azon erőforrások listája, amelyekre vonatkozik a javaslat.
3. Egy javaslat alkalmazásához válasszon ki egy adott erőforrást. 
4. A javítási lépések végrehajtásához kövesse az utasításokat. 

A Security Center olyan végrehajtható lépéseket ajánl, amelyekkel a Security Center elhagyása nélkül kezelhet egy javaslatot. A következő példában a Security Center olyan hálózati biztonsági csoportot észlel, amelyhez tartozik egy korlátozás nélküli bejövő szabály. A javaslatok oldalán kiválaszthatja a **Bejövő szabályok szerkesztése** gombot. Megjelenik a szabály módosításához szükséges felhasználói felület. 

![Javaslatok](./media/tutorial-azure-security/remediation.png)

Ha a rendszer elvégzi a javaslatokhoz tartozó javításokat, megoldottként jelöli meg őket. 

## <a name="view-detected-threats"></a>Észlelt fenyegetések megtekintése

Az erőforrások konfigurációjára vonatkozó javaslatok mellett a Security Center megjelenít fenyegetésészlelési riasztásokat is. A biztonsági riasztások szolgáltatása az Azure-erőforrásokra leselkedő biztonsági fenyegetések felderítése érdekében összesíti az egyes virtuális gépekről gyűjtött adatokat, az Azure-beli hálózati naplókat és a csatlakoztatott partnermegoldásokat. A Security Center fenyegetésészlelési képességeivel kapcsolatban további információt [az Azure Security Center észlelési képességeit](../../security-center/security-center-detection-capabilities.md) ismertető szakaszban talál.

A biztonsági riasztások funkciójának használatához a Security Center tarifacsomagját *Ingyenesről* *Standardra* kell emelni. Ha erre a magasabb tarifacsomagra lép, elérhető egy 30 napos **ingyenes próbaidőszak**. 

A tarifacsomag módosítása:  

1. A Security Center irányítópultján kattintson a **Biztonsági szabályzat** elemre, és válassza ki az előfizetését.
2. Válassza a **Tarifacsomag** lehetőséget,
3. Válassza ki az új csomagot, majd kattintson a **Kiválasztás** elemre.
4. A **Biztonsági szabályzat** panelen válassza a **Mentés** lehetőséget. 

Miután módosította a tarifacsomagot, a rendszer feltölti a biztonsági riasztások diagramját, amint biztonsági fenyegetéseket észlel.

![Biztonsági riasztások](./media/tutorial-azure-security/security-alerts.png)

Az információk megtekintéséhez válasszon ki egy riasztást. Megjelenik például a fenyegetés leírása, az észlelési idő, minden fenyegetési kísérlet, valamint a javasolt javítás. Az alábbi példában a rendszer egy találgatásos RDP-támadást észlelt 294 meghiúsult RDP-kísérlettel. A rendszer felkínál egy javasolt megoldást.

![RDP-támadás](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban beállította az Azure Security Centert, és áttekintette a virtuális gépek Security Centerben való kezelését. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatgyűjtés beállítása
> * Biztonsági szabályzatok beállítása
> * A konfiguráció állapotára vonatkozó problémák megtekintése és javítása
> * Észlelt fenyegetések áttekintése

Ha szeretné megtudni, hogyan hozhat létre CI-/CD-folyamatot a Visual Studio Team Services és a Windows rendszerű virtuális gépeket futtató IIS segítségével, lépjen a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [Visual Studio Team Services CI-/CD-folyamat](./tutorial-vsts-iis-cicd.md)
