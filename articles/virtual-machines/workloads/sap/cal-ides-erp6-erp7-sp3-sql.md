---
title: SAP IDES EHP7 SP3 telepítése a SAP ERP 6.0 az Azure-ban |} A Microsoft Docs
description: SAP IDES EHP7 SP3 telepítése a SAP ERP 6.0 az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 93824c8f0e7667fcb58fd6b8292cddfa2b4a482a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441459"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>SAP IDES EHP7 SP3 telepítése a SAP ERP 6.0 az Azure-ban
Ez a cikk ismerteti, hogyan helyezhet üzembe egy Azure-ban az SAP Cloud Appliance Library (SAP CAL) 3.0-n keresztül futtatja az SQL Server és a Windows operációs rendszer SAP IDES rendszer. A képernyőfelvételek bemutatják a részletes folyamat. Egy másik megoldás telepítéséhez kövesse a lépéseket.

Az SAP CAL indításához nyissa meg a [SAP Cloud Appliance Library](https://cal.sap.com/) webhelyén. SAP is rendelkezik az új blog [SAP Cloud Appliance könyvtár 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
2017. május 29. a mellett a kevesebb az előnyben részesített klasszikus üzemi modellben az Azure Resource Manager üzemi modell segítségével az SAP CAL telepítheti. Azt javasoljuk, hogy használja az új Resource Manager-alapú üzemi modellt, és figyelmen kívül hagyja a klasszikus üzemi modellben.

Ha már létrehozott egy SAP CAL-fiókot a klasszikus modellt használó *egy másik SAP CAL-fiók létrehozásához szükséges*. Ezt a fiókot kizárólag telepítse az Azure-bA a Resource Manager-modell segítségével kell.

Miután bejelentkezik az SAP CAL, az első oldal általában vezet, hogy a **megoldások** lapot. A megoldások között érhetők el, az SAP CAL a folyamatosan növekvő, így előfordulhat, hogy kell megtekintéséhez görgessen teljesen egy kicsit a kívánt megoldás. A kijelölt SAP IDES Windows-alapú megoldás, amely kizárólag az Azure-ban érhető el a telepítési folyamat mutatja be:

![SAP CAL-megoldások](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Az SAP CAL-fiók létrehozása
1. Először jelentkezzen be az SAP CAL, az SAP S felhasználói vagy más felhasználó regisztrált az SAP. Majd adja meg a SAP CAL-fiókkal, amelyet az SAP CAL berendezések Azure-beli üzembe helyezéséhez. A fiók definíciójában kell tennie:

    a. Válassza ki az Azure-ban (Resource Manager vagy klasszikus) üzemi.

    b. Adja meg az Azure-előfizetésében. Egy SAP CAL-fiókot csak egy előfizetéssel is hozzárendelhető. Ha több előfizetéssel, hozzon létre egy másik SAP CAL-fiókot szeretne.
    
    c. Az SAP CAL engedélyt helyezze üzembe az Azure-előfizetésében.

    > [!NOTE]
    A következő lépések bemutatják, hogyan hozhat létre a Resource Manager üzembe helyezések SAP CAL-fiókot. Ha már rendelkezik egy SAP CAL-fiókkal, amely a klasszikus üzemi modell van csatolva, *kell* , kövesse az alábbi lépéseket egy új SAP CAL-fiók létrehozásához. Az új SAP CAL-fióknak rendelkeznie kell üzembe helyezni a Resource Manager-modellben.

1. Új SAP CAL-fiók létrehozása a **fiókok** lapon látható a két választási lehetőség az Azure-hoz: 

    a. **A Microsoft Azure (klasszikus)** a klasszikus üzemi modell, és már nem elsődleges.

    b. **A Microsoft Azure** az új Resource Manager üzemi modell.

    ![Az SAP CAL-fiókok](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    A Resource Manager-modellben üzembe helyezéséhez válassza **Microsoft Azure**.

    ![Az SAP CAL-fiókok](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Adja meg az Azure **előfizetés-azonosító** , amely az Azure Portalon található. 

    ![Az SAP CAL előfizetés-azonosító](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. A megadott Azure-előfizetésben helyezi üzembe helyezéséhez SAP CAL engedélyezésére, kattintson a **engedélyezés**. A következő lap a böngészőlapon jelenik meg:

    ![Az Internet Explorer cloud services – bejelentkezési](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Ha egynél több felhasználó szerepel a listán, válassza ki a Microsoft-fiókkal, amely kapcsolódik a kiválasztott Azure-előfizetés coadministrator lehet. A következő lap a böngészőlapon jelenik meg:

    ![Az Internet Explorer cloud services megerősítése](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Kattintson a **fogadja el**. Ha az engedélyezési művelet sikeres, az SAP CAL definíció újra jeleníti meg. Rövid idő után egy üzenet megerősíti, hogy az engedélyezési folyamat sikeres volt-e.

1. Az újonnan létrehozott SAP CAL-fiók hozzárendelése a felhasználóhoz, írja be a **felhasználói azonosító** a jobb oldalon, majd a szövegmezőben **Hozzáadás**. 

    ![Felhasználók társítása fiókot](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Kattintson a fiók társítása a felhasználót, hogy használhatja a bejelentkezni az SAP CAL, **felülvizsgálati**. 

1. A felhasználó és az újonnan létrehozott SAP CAL-fiók közötti társítás létrehozásához kattintson a **létrehozás**.

    ![Felhasználói fiók társítás](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Sikeresen létrehozott egy SAP CAL-fiókot, amely képes:

- Használja a Resource Manager-alapú üzemi modellt.
- SAP-rendszereinket helyezze üzembe az Azure-előfizetésében.

> [!NOTE]
A SAP IDES megoldás, amely Windows és az SQL Server telepítése előtt szüksége lehet egy SAP CAL-előfizetési regisztrálás szükséges. Ellenkező esetben a megoldás lehet, hogy megjelenjen **zárolt** az Áttekintés oldalon.

### <a name="deploy-a-solution"></a>Megoldás üzembe helyezése
1. Egy SAP CAL-fiók beállítása után válassza ki a **megoldás a SAP IDES Windows és az SQL Server** megoldás. Kattintson a **példány létrehozása**, és erősítse meg a használat és a használati feltételeket. 

1. Az a **egyszerű módban: példány létrehozása** lapon kell tennie:

    a. Adjon meg egy példányt **neve**.

    b. Válassza ki az Azure-beli **régió**. Szüksége lehet beolvasni a több Azure-régióban érhető el egy SAP CAL-előfizetést.

    c.  Adja meg a főkiszolgáló **jelszó** a megoldásban látható módon:

    ![Az SAP CAL alapszintű mód: Példány létrehozása](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Kattintson a **Create** (Létrehozás) gombra. Némi várakozás után a mérete és összetettsége, a megoldás (az SAP CAL biztosít becslés), az állapota, aktív és használatra kész: 

    ![SAP CAL-példányok](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Az erőforráscsoport és az SAP CAL által létrehozott minden objektum megkereséséhez nyissa meg az Azure Portalon. A virtuális gép található, hogy az SAP CAL az adott példánynevet kezdve.

    ![Erőforrás-csoport objektumok](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Az SAP CAL portálon lépjen az üzemelő példányokat, és kattintson **Connect**. Az alábbi felugró ablak jelenik meg: 

    ![Kapcsolódjon a példányhoz](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Kattintson a használata előtt a egyikét az üzembe helyezett rendszerhez való csatlakozáshoz, **Getting Started Guide**. A dokumentáció a felhasználók számára a csatlakozási módszer neveket. A jelszavak azoknak a felhasználóknak a főkiszolgáló jelszavát, a telepítési folyamat elején meghatározott vannak állítva. A dokumentáció más funkciógazdagabb felhasználók szerepel a listában a jelszavukat, amelyek segítségével jelentkezzen be a telepített rendszer.

    ![Üdvözli Önt az SAP-dokumentáció](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Néhány órán belül egy SAP IDES kifogástalan állapotú rendszerhez helyezünk üzembe az Azure-ban.

Az SAP egy SAP CAL-előfizetést vásárolt, teljes mértékben támogatja az SAP CAL üzembe helyezést az Azure-ban. A támogatási várólista BC-VCM-CAL.

