---
title: Sap IDES EHP7 SP3 üzembe helyezése az SAP ERP 6.0-hoz az Azure-ban | Microsoft dokumentumok
description: Sap IDES EHP7 SP3 üzembe helyezése az SAP ERP 6.0-hoz az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 3efd92226b7c69590f3960458ffec49b63b8364f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616703"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Sap IDES EHP7 SP3 üzembe helyezése az SAP ERP 6.0-hoz az Azure-ban
Ez a cikk bemutatja, hogyan telepíthet egy SQL Server rel és a Windows operációs rendszerrel futó SAP IDES-rendszert az SAP Cloud Appliance Library (SAP CAL) 3.0-s rendszeren keresztül. A képernyőképek lépésről lépésre mutatják a folyamatot. Egy másik megoldás üzembe helyezéséhez kövesse ugyanazokat a lépéseket.

Először is az SAP CAL, nyissa meg az [SAP Cloud Appliance Library](https://cal.sap.com/) webhelyén. Az SAP-nak van egy blogja az új [SAP Cloud Appliance Library 3.0-ról](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)is. 

> [!NOTE]
> 2017. május 29-től használhatja az Azure Resource Manager üzembe helyezési modelljét a kevésbé preferált klasszikus üzembe helyezési modell mellett az SAP CAL üzembe helyezéséhez. Azt javasoljuk, hogy használja az új Resource Manager telepítési modell, és figyelmen kívül hagyja a klasszikus központi telepítési modell.

Ha már létrehozott egy SAP CAL-fiókot, amely a klasszikus modellt használja, *létre kell hoznia egy másik SAP CAL-fiókot.* Ezt a fiókot kell kizárólag üzembe helyezni az Azure-ban a Resource Manager modell használatával.

Miután bejelentkezett az SAP CAL-ba, az első oldal általában a **Megoldások** laphoz vezet. Az SAP CAL-on kínált megoldások folyamatosan növekednek, ezért előfordulhat, hogy elég sokat kell görgetnie, hogy megtalálja a kívánt megoldást. A kiemelt Windows-alapú SAP IDES-megoldás, amely kizárólag az Azure-ban érhető el, bemutatja a telepítési folyamatot:

![SAP CAL megoldások](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Fiók létrehozása az SAP CAL-ban
1. Az SAP CAL-ba való első bejelentkezéshez használja az SAP S-felhasználót vagy az SAP-hoz regisztrált más felhasználót. Ezután definiálja az SAP CAL-fiók által használt SAP CAL az Azure-ban üzembe helyezése. A fiókdefinícióban a következőket kell tenni:

    a. Válassza ki a központi telepítési modell az Azure-ban (Resource Manager vagy klasszikus).

    b. Adja meg Azure-előfizetését. Egy SAP CAL-fiók csak egy előfizetéshez rendelhető hozzá. Ha egynél több előfizetésre van szüksége, létre kell hoznia egy másik SAP CAL-fiókot.
    
    c. Adja meg az SAP CAL engedélyt az Azure-előfizetésében való üzembe helyezéshez.

   > [!NOTE]
   >  A következő lépések bemutatják, hogyan hozhat létre egy SAP CAL-fiókot az Erőforrás-kezelő központi telepítések. Ha már rendelkezik egy SAP CAL-fiókkal, amely kapcsolódik a klasszikus üzembe helyezési modellhez, az alábbi lépéseket *kell* végrehajtania egy új SAP CAL-fiók létrehozásához. Az új SAP CAL-fiók nak telepítenie kell az Erőforrás-kezelő modellben.

1. Új SAP CAL-fiók létrehozásához a Fiókok lapon két lehetőség közül választhat az Azure:To create a new SAP CAL account, the **Accounts** page shows two choices for Azure: 

    a. **A Microsoft Azure (klasszikus)** a klasszikus üzembe helyezési modell, és már nem előnyös.

    b. **A Microsoft Azure** az új Resource Manager telepítési modell.

    ![SAP CAL-fiókok](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Az Erőforrás-kezelő modellben való üzembe helyezéshez válassza a **Microsoft Azure**lehetőséget.

    ![SAP CAL-fiókok](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Adja meg az **Azure-előfizetési azonosítót,** amely megtalálható az Azure Portalon. 

    ![SAP CAL-előfizetésazonosító](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Ha engedélyezni szeretné az SAP cal üzembe helyezését a megadott Azure-előfizetésben, kattintson a **Engedélyezés gombra.** A böngészőlapon a következő oldal jelenik meg:

    ![Bejelentkezés az Internet Explorer felhőszolgáltatásaiba](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Ha egynél több felhasználó szerepel a listában, válassza ki azt a Microsoft-fiókot, amely a kiválasztott Azure-előfizetés társadminisztrátora ként kapcsolódik. A böngészőlapon a következő oldal jelenik meg:

    ![Az Internet Explorer felhőszolgáltatásainak megerősítése](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Kattintson az **Elfogadás** lehetőségre. Ha az engedélyezés sikeres, az SAP CAL fiókdefiníció ismét megjelenik. Rövid idő elteltével egy üzenet megerősíti, hogy az engedélyezési folyamat sikeres volt.

1. Ha az újonnan létrehozott SAP CAL-fiókot hozzá szeretné rendelni a felhasználóhoz, írja be **a felhasználói azonosítóját** a jobb oldali szövegmezőbe, és kattintson a **Hozzáadás gombra.** 

    ![Fiók a felhasználói asszociációhoz](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Ha a fiókot az SAP-ügyféllicencbe való bejelentkezéshez használt felhasználóhoz szeretné társítani, kattintson a **Véleményezés**gombra. 

1. A felhasználó és az újonnan létrehozott SAP CAL-fiók közötti társítás létrehozásához kattintson a **Létrehozás gombra.**

    ![Felhasználó és fiók társítása](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Sikeresen létrehozott egy SAP CAL-fiókot, amely képes:

- Használja a Resource Manager-alapú üzemi modellt.
- Telepítse az SAP-rendszereket az Azure-előfizetésébe.

> [!NOTE]
> A Windows és az SQL Server alapú SAP IDES-megoldás telepítése előtt előfordulhat, hogy regisztrálnia kell egy SAP CAL-előfizetésre. Ellenkező esetben előfordulhat, hogy a megoldás **zároltként** jelenik meg az áttekintő lapon.

### <a name="deploy-a-solution"></a>Megoldás telepítése
1. Miután beállított egy SAP CAL-fiókot, válassza **az SAP IDES-megoldást Windows és SQL Server** rendszeren. Kattintson **a Példány létrehozása gombra,** és erősítse meg a használati és szerződési feltételeket. 

1. Az **Alapmód: Példány létrehozása** lapon a következőket kell a következőkre tudnia:

    a. Adjon meg egy példány **nevet**.

    b. Válasszon egy **Azure-régiót.** Előfordulhat, hogy több Azure-régiót szeretne kapni egy SAP CAL-előfizetéssel.

    c.  Adja meg a megoldás fő **jelszavát,** ahogy az látható:

    ![SAP CAL alapmód: Példány létrehozása](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Kattintson **a Létrehozás gombra.** Egy idő után, a megoldás méretétől és összetettségétől függően (az SAP CAL becslést ad), az állapot aktívként és használatra készként jelenik meg: 

    ![SAP CAL-példányok](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Az erőforráscsoport és az SAP CAL által létrehozott összes objektum megkereséséhez keresse meg az Azure Portalt. A virtuális gép az SAP CAL-ban megadott példánynévvel kezdve található.

    ![Erőforráscsoport-objektumok](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Az SAP CAL portálon nyissa meg az üzembe helyezett példányokat, és kattintson a **Csatlakozás**gombra. A következő előugró ablak jelenik meg: 

    ![Csatlakozás a példányhoz](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Mielőtt az üzembe helyezett rendszerekhez való csatlakozás egyik ével kapcsolatba léphetne, kattintson az **Első lépések útmutatója gombra.** A dokumentáció minden egyes kapcsolódási módszerhez megnevezi a felhasználókat. A felhasználók jelszavai a központi telepítési folyamat elején megadott fő jelszóra vannak beállítva. A dokumentációban más funkcionális felhasználók is megjelennek a jelszavukkal, amelyek segítségével bejelentkezhet a telepített rendszerbe.

    ![SAP üdvözlődokumentáció](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Néhány órán belül egy kifogástalan állapotú SAP IDES-rendszer üzembe kerül az Azure-ban.

Ha vásárolt egy SAP CAL-előfizetést, az SAP teljes mértékben támogatja a központi telepítéseket az Azure-beli SAP CAL-on keresztül. A támogatási várólista BC-VCM-CAL.

