---
title: "SAP IDES EHP7 SP3 telepítése SAP ERP 6.0 az Azure-on |} Microsoft Docs"
description: "SAP ERP 6.0 Azure SAP IDES EHP7 SP3 telepítése"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>SAP ERP 6.0 Azure SAP IDES EHP7 SP3 telepítése
Ez a cikk ismerteti, hogyan telepítse az SQL Server és a Windows operációs rendszeren futó Azure keresztül a felhő készülék teszteléshez (SAP CAL) 3.0 SAP IDES rendszert. A pillanatképek megjelenítése a folyamatot. Egy másik megoldás telepítéséhez kövesse a lépéseket.

Indítsa el a SAP ügyféllicenccel, keresse fel a [felhő készülék teszteléshez](https://cal.sap.com/) webhely. SAP is rendelkezik egy új blog [SAP felhő készülék könyvtár 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
2017. május 29. frissítésétől mellett a kevésbé előnyben részesített klasszikus üzembe helyezési modellel, az Azure Resource Manager telepítési modell segítségével telepítheti az SAP-CAL. Azt javasoljuk, hogy az új központi telepítési Resource Manager modellt használja, és figyelmen kívül hagyhatja a klasszikus üzembe helyezési modellben.

Ha már létrehozott egy SAP naptár fiókja a klasszikus modellt használó *kell létrehoznia egy másik SAP naptár fiókja*. Ezt a fiókot kell kizárólag telepíteni az Azure Resource Manager modellt használja.

Miután bejelentkezik az SAP-CAL, az első lap általában vezet, hogy a **megoldások** lap. Az SAP-CAL kínált megoldások egyenletesen növekednek, szükség lehet a kívánt megoldás keresése túl kicsit görgessen. A kijelölt Windows-alapú SAP IDES megoldás, amely kizárólag az Azure-on elérhető mutatja be a telepítési folyamat:

![SAP CAL megoldások](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Az SAP-CAL, hozzon létre egy fiókot
1. Jelentkezzen be az SAP-CAL először, használja az SAP-felhasználó vagy más SAP regisztrált felhasználó. Majd adja meg egy SAP naptár fiókja Azure készülékek telepítendő az SAP-CAL által használt. A fiók-definícióban kell:

    a. (Erőforrás-kezelő vagy klasszikus) Azure telepítési modell kiválasztása.

    b. Adja meg az Azure-előfizetéshez. Egy SAP naptár fiókja csak egyetlen előfizetéssel is hozzárendelhető. Ha egynél több előfizetéssel, hozzon létre egy másik SAP naptár fiókja szeretné.
    
    c. Az SAP-CAL engedélyt üzembe helyezés az Azure-előfizetéshez.

    > [!NOTE]
    A következő lépések bemutatják, hogyan a Resource Manager üzembe helyezések SAP CAL-fiók létrehozása. Ha már rendelkezik egy SAP naptár fiókja, amely csatolva van a klasszikus üzembe helyezési modellel, *kell* lépések SAP CAL új fiók létrehozása. Az új SAP CAL fiókot kell üzembe helyezni a Resource Manager modellt.

2. Új SAP CAL-fiók létrehozása a **fiókok** lapon látható két választási lehetőség az Azure-bA: 

    a. **A Microsoft Azure (klasszikus)** a klasszikus üzembe helyezési modellel, és már nem elsődleges.

    b. **A Microsoft Azure** az új erőforrás-kezelő telepítési modell.

    ![SAP CAL fiókok](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    A Resource Manager modellt a telepítéséhez válassza ki a **Microsoft Azure**.

    ![SAP CAL fiókok](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Adja meg az Azure **előfizetés-azonosító** , amely az Azure portálon található. 

    ![SAP CAL előfizetés-azonosító](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. Az SAP-CAL való üzembe helyezés az Azure-előfizetés az Ön által definiált engedélyezésére, kattintson a **engedélyezés**. A következő lap a böngészőlapon jelenik meg:

    ![Az Internet Explorer cloud services – bejelentkezési](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Ha egynél több felhasználó szerepel a listán, válassza ki a Microsoft-fiókkal, amely csatolva van a kijelölt Azure-előfizetés coadministrator kell. A következő lap a böngészőlapon jelenik meg:

    ![Az Internet Explorer felhőalapú szolgáltatások megerősítése](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Kattintson a **elfogadása**. Ha az engedélyezési sikeres, az SAP-CAL fiók definíció újra jeleníti meg. Rövid idő múlva üzenet jelzi, hogy az engedélyezési folyamat sikeres volt-e.

7. Az újonnan létrehozott SAP naptár fiókja hozzárendelése a felhasználóhoz, adja meg a **Felhasználóazonosító** jobbra, majd a szövegmezőben **Hozzáadás**. 

    ![Fiók és felhasználói társítása](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. A fiók társítása a felhasználót, hogy jelentkezzen be az SAP-CAL segítségével, kattintson a **felülvizsgálati**. 

9. A felhasználó és az újonnan létrehozott SAP naptár fiókja közötti társítás létrehozásához kattintson a **létrehozása**.

    ![Felhasználók való fiók társítása](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Sikeresen létrehozott egy SAP naptár fiókja, amely képes:

- A Resource Manager telepítési modellt használja.
- SAP rendszerek üzembe helyezés az Azure-előfizetéshez.

> [!NOTE]
Az SAP IDES megoldás, amely Windows és az SQL Server telepítése előtt szükség lehet egy SAP naptár-előfizetés regisztrálhat. Ellenkező esetben a megoldás lehet, hogy megjelennek, **zárolt** Áttekintés lap.

### <a name="deploy-a-solution"></a>A megoldás üzembe helyezéséhez
1. Miután beállította az SAP naptár fiókja, válassza ki a **az SAP IDES megoldás a Windows és az SQL Server** megoldás. Kattintson a **példány létrehozása**, és erősítse meg a használati és a használati feltételeket. 

2. Az a **alapvető mód: példány létrehozása** lapon kell:

    a. Adjon meg egy példány **neve**.

    b. Válassza ki az Azure **régió**. Szükség lehet az SAP-CAL előfizetői kínált több Azure-régiók eléréséhez.

    c.  Adja meg a fő **jelszó** a megoldás látható módon:

    ![SAP CAL Basic mód: Példány létrehozása](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Kattintson a **Create** (Létrehozás) gombra. Némi várakozás után attól függően, hogy méretét és összetettségét (az SAP-CAL biztosít becsült érték) megoldás állapota jelenik meg az aktív és használatra kész: 

    ![SAP CAL-példányok](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Az erőforráscsoport és az SAP-CAL által létrehozott minden objektum megkereséséhez nyissa meg az Azure-portálon. A virtuális gép található, hogy az SAP-CAL az adott példánynevet kezdve.

    ![Erőforrás objektumok](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. Az SAP-CAL-portál nyissa meg a telepített példányt, és kattintson a **Connect**. A következő megjelenő ablakban jelenik meg: 

    ![Kapcsolódjon ahhoz a példányhoz](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Kattintson a használata előtt a egyikére kapcsolódni a telepített rendszereken, **Getting Started Guide**. A dokumentáció a felhasználók számára a csatlakozási módszer nevet. A jelszavak azoknak a felhasználóknak a fő jelszót, amelyet megadott, a telepítési folyamat elején értékre van beállítva. A dokumentáció több működési másoknak szerepel a listában a jelszavukat, amelyek segítségével jelentkezzen be a telepített rendszer.

    ![Üdvözli a SAP-dokumentáció](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Néhány órán belül egy kifogástalan SAP IDES rendszer van telepítve az Azure-ban.

Ha egy SAP naptár-előfizetés vásárolta, SAP Azure teljes mértékben támogatja az SAP-CAL környezeteit. A támogatási várólista BC-VCM-CAL.

