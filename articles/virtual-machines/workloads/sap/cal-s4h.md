---
title: SAP S/4HANA vagy BW/4HANA üzembe helyezése Azure-beli virtuális gépen | Microsoft dokumentumok
description: SAP S/4HANA vagy BW/4HANA üzembe helyezése Egy Azure virtuális gépen
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616210"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>SAP S/4HANA vagy BW/4HANA üzembe helyezése az Azure-ban
Ez a cikk ismerteti, hogyan telepítheti az S/4HANA az Azure-ban az SAP Cloud Appliance Library (SAP CAL) 3.0 használatával. Más SAP HANA-alapú megoldások, például a BW/4HANA üzembe helyezéséhez kövesse ugyanazokat a lépéseket.

> [!NOTE]
> Az SAP CAL-ről további információt az [SAP Cloud Appliance Library](https://cal.sap.com/) webhelyén talál. Az SAP-nak blogja is van az [SAP Cloud Appliance Library 3.0-ról.](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)
> 
> [!NOTE]
> 2017. május 29-től használhatja az Azure Resource Manager üzembe helyezési modelljét a kevésbé preferált klasszikus üzembe helyezési modell mellett az SAP CAL üzembe helyezéséhez. Azt javasoljuk, hogy használja az új Resource Manager telepítési modell, és figyelmen kívül hagyja a klasszikus központi telepítési modell.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Lépésről lépésre a megoldás üzembe helyezéséhez

A következő képernyőképek sorozata bemutatja, hogyan telepítheti az S/4HANA-t az Azure-on az SAP CAL használatával. A folyamat ugyanúgy működik más megoldások, mint például a BW/4HANA esetében.

A **Megoldások** lap az Azure-ban elérhető SAP CAL HANA-alapú megoldások at jeleníti meg. **SAP S/4HANA 1610 FPS01, Teljesen aktivált készülék** a középső sorban van:

![SAP CAL megoldások](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Fiók létrehozása az SAP CAL-ban
1. Az SAP CAL-ba való első bejelentkezéshez használja az SAP S-felhasználót vagy az SAP-hoz regisztrált más felhasználót. Ezután definiálja az SAP CAL-fiók által használt SAP CAL az Azure-ban üzembe helyezése. A fiókdefinícióban a következőket kell tenni:

    a. Válassza ki a központi telepítési modell az Azure-ban (Resource Manager vagy klasszikus).

    b. Adja meg Azure-előfizetését. Egy SAP CAL-fiók csak egy előfizetéshez rendelhető hozzá. Ha egynél több előfizetésre van szüksége, létre kell hoznia egy másik SAP CAL-fiókot.

    c. Adja meg az SAP CAL engedélyt az Azure-előfizetésében való üzembe helyezéshez.

   > [!NOTE]
   >  A következő lépések bemutatják, hogyan hozhat létre egy SAP CAL-fiókot az Erőforrás-kezelő központi telepítések. Ha már rendelkezik egy SAP CAL-fiókkal, amely kapcsolódik a klasszikus üzembe helyezési modellhez, az alábbi lépéseket *kell* végrehajtania egy új SAP CAL-fiók létrehozásához. Az új SAP CAL-fiók nak telepítenie kell az Erőforrás-kezelő modellben.

1. Hozzon létre egy új SAP CAL-fiókot. A Fiókok lapon három lehetőség közül választhat az **Azure:The Accounts** page shows three choices for Azure: 

    a. **A Microsoft Azure (klasszikus)** a klasszikus üzembe helyezési modell, és már nem előnyös.

    b. **A Microsoft Azure** az új Resource Manager telepítési modell.

    c. **A 21Vianet által üzemeltetett Windows Azure** egy olyan lehetőség Kínában, amely a klasszikus telepítési modellt használja.

    Az Erőforrás-kezelő modellben való üzembe helyezéshez válassza a **Microsoft Azure**lehetőséget.

    ![SAP CAL-fiók részletei](./media/cal-s4h/s4h-pic-2a.png)

1. Adja meg az **Azure-előfizetési azonosítót,** amely megtalálható az Azure Portalon.

   ![SAP CAL-fiókok](./media/cal-s4h/s4h-pic3c.png)

1. Ha engedélyezni szeretné az SAP cal üzembe helyezését a megadott Azure-előfizetésben, kattintson a **Engedélyezés gombra.** A böngészőlapon a következő oldal jelenik meg:

   ![Bejelentkezés az Internet Explorer felhőszolgáltatásaiba](./media/cal-s4h/s4h-pic4c.png)

1. Ha egynél több felhasználó szerepel a listában, válassza ki azt a Microsoft-fiókot, amely a kiválasztott Azure-előfizetés társadminisztrátora ként kapcsolódik. A böngészőlapon a következő oldal jelenik meg:

   ![Az Internet Explorer felhőszolgáltatásainak megerősítése](./media/cal-s4h/s4h-pic5a.png)

1. Kattintson az **Elfogadás** lehetőségre. Ha az engedélyezés sikeres, az SAP CAL fiókdefiníció ismét megjelenik. Rövid idő elteltével egy üzenet megerősíti, hogy az engedélyezési folyamat sikeres volt.

1. Ha az újonnan létrehozott SAP CAL-fiókot hozzá szeretné rendelni a felhasználóhoz, írja be **a felhasználói azonosítóját** a jobb oldali szövegmezőbe, és kattintson a **Hozzáadás gombra.**

   ![Fiók a felhasználói asszociációhoz](./media/cal-s4h/s4h-pic8a.png)

1. Ha a fiókot az SAP-ügyféllicencbe való bejelentkezéshez használt felhasználóhoz szeretné társítani, kattintson a **Véleményezés**gombra. 
 
1. A felhasználó és az újonnan létrehozott SAP CAL-fiók közötti társítás létrehozásához kattintson a **Létrehozás gombra.**

   ![Felhasználó az SAP CAL fiók társításához](./media/cal-s4h/s4h-pic9b.png)

Sikeresen létrehozott egy SAP CAL-fiókot, amely képes:

- Használja a Resource Manager-alapú üzemi modellt.
- Telepítse az SAP-rendszereket az Azure-előfizetésébe.

Most már elkezdheti üzembe helyezni az S/4HANA-t a felhasználói előfizetésében az Azure-ban.

> [!NOTE]
> Mielőtt folytatna, határozza meg, hogy rendelkezik-e Azure-vCPU-kvótákkal az Azure H sorozatú virtuális gépekhez. Jelenleg az SAP CAL az Azure H-sorozatú virtuális gépeit használja az SAP HANA-alapú megoldások telepítéséhez. Előfordulhat, hogy az Azure-előfizetés nem rendelkezik H-sorozatú vCPU-kvótákkal a H-sorozathoz. Ha igen, előfordulhat, hogy legalább 16 H sorozatú vCPU-kvótát kell leigazolnia az Azure-támogatáshoz.
> 
> [!NOTE]
> Amikor egy megoldást üzembe helyez az Azure-ban az SAP CAL, előfordulhat, hogy csak egy Azure-régiót választhat. Az SAP CAL által javasolttól eltérő Azure-régiókban való üzembe helyezéshez meg kell vásárolnia egy CAL-előfizetést az SAP-tól. Előfordulhat, hogy meg kell nyitnia egy üzenetet az SAP-val, hogy a CAL-fiók engedélyezve legyen az Eredetileg javasolttól eltérő Azure-régiókba való kézbesítéshez.

### <a name="deploy-a-solution"></a>Megoldás telepítése

Vessünk fel egy megoldást az SAP CAL **Megoldások** lapjáról. Az SAP CAL két szekvenciát telepít:

- Alapsorrend, amely egy oldalt használ a telepítendő rendszer meghatározásához
- Speciális szekvencia, amely bizonyos választási lehetőségeket biztosít a virtuális gép méretekkel 

Bemutatjuk az itteni bevetés hez vezető alapvető utat.

1. A **Fiók részletei** lapon a következőket kell megadnia:

    a. Válasszon egy SAP CAL-fiókot. (Az Erőforrás-kezelő telepítési modelljével való üzembe helyezéshez társított fiók használata.)

    b. Adjon meg egy példány **nevet**.

    c. Válasszon egy **Azure-régiót.** Az SAP CAL egy régiót javasol. Ha szüksége van egy másik Azure-régióban, és nem rendelkezik SAP CAL-előfizetéssel, meg kell rendelnie egy CAL-előfizetést az SAP-val.

    d. Adjon meg egy fő **jelszót** a nyolc vagy kilenc karakterből álló megoldáshoz. A jelszó a különböző összetevők rendszergazdái számára használatos.

   ![SAP CAL alapmód: Példány létrehozása](./media/cal-s4h/s4h-pic10a.png)

1. Kattintson a **Létrehozás gombra,** és a megjelenő üzenetpanelen kattintson az **OK gombra.**

   ![SAP CAL által támogatott virtuális gép méretei](./media/cal-s4h/s4h-pic10b.png)

1. A **Személyes kulcs** párbeszédpanelen kattintson az **Üzlet elemre** a személyes kulcs SAP-cal való tárolásához. A személyes kulcs jelszavas védelmének használatához kattintson a **Letöltés gombra.** 

   ![SAP CAL személyes kulcs](./media/cal-s4h/s4h-pic10c.png)

1. Olvassa el az SAP CAL **figyelmeztető** üzenetet, és kattintson **az OK**gombra.

   ![SAP CAL – figyelmeztetés](./media/cal-s4h/s4h-pic10d.png)

    Most a telepítés zajlik. Egy idő után, a ttól függően, hogy a méret és a komplexitás a megoldás (az SAP CAL becslést biztosít), az állapot aktívként jelenik meg, és használatra kész.

1. Az egyik erőforráscsoportban lévő többi társított erőforrással együtt gyűjtött virtuális gépek megkereséséhez nyissa meg az Azure Portalt: 

   ![Az új portálon telepített SAP CAL-objektumok](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Az SAP CAL portálon az állapot **aktív**állapotként jelenik meg. A megoldáshoz való csatlakozáshoz kattintson a **Csatlakozás gombra.** A megoldáson belül különböző lehetőségek vannak telepítve a különböző összetevőkhöz való csatlakozáshoz.

   ![SAP CAL-példányok](./media/cal-s4h/active_solution.png)

1. Mielőtt az üzembe helyezett rendszerekhez való csatlakozás egyik ével kapcsolatba léphetne, kattintson az **Első lépések útmutatója gombra.** 

   ![Csatlakozás a példányhoz](./media/cal-s4h/connect_to_solution.png)

    A dokumentáció minden egyes kapcsolódási módszerhez megnevezi a felhasználókat. A felhasználók jelszavai a központi telepítési folyamat elején megadott fő jelszóra vannak beállítva. A dokumentációban más funkcionális felhasználók is megjelennek a jelszavukkal, amelyek segítségével bejelentkezhet a telepített rendszerbe. 

    Ha például a Windows Távoli asztali gépen előtelepített SAP grafikus felhasználói felületet használja, az S/4 rendszer a következőkre néz ki:

   ![SM50 az előtelepített SAP GUI-ban](./media/cal-s4h/gui_sm50.png)

    Vagy ha a DBACockpit-et használja, a példány a következőkre néz ki:

   ![SM50 a DBACockpit SAP GRAFIKUS felhasználói felületén](./media/cal-s4h/dbacockpit.png)

Néhány órán belül egy kifogástalan állapotú SAP S/4-es készülék van telepítve az Azure-ban.

Ha vásárolt egy SAP CAL-előfizetést, az SAP teljes mértékben támogatja a központi telepítéseket az Azure-beli SAP CAL-on keresztül. A támogatási várólista BC-VCM-CAL.







