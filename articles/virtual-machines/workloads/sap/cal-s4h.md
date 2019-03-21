---
title: SAP S/4HANA vagy BW/4hana-t egy Azure virtuális gép üzembe helyezése |} A Microsoft Docs
description: SAP S/4HANA vagy BW/4hana-t egy Azure virtuális gép üzembe helyezése
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c59fcf43cb4767f1d95d769dfce4d5c8755e45ee
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990481"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>SAP S/4HANA vagy BW/4hana-t az Azure-ban üzembe helyezése
Ez a cikk ismerteti, hogyan helyezhet üzembe az S/4hana-t az Azure-ban az SAP Cloud Appliance Library (SAP CAL) 3.0 használatával. Egyéb SAP HANA-alapú megoldások, például a BW/4HANA, üzembe helyezéséhez kövesse a lépéseket.

> [!NOTE]
> Az SAP CAL kapcsolatos további információkért látogasson el a [SAP Cloud Appliance Library](https://cal.sap.com/) webhelyén. SAP is rendelkezik egy blog kapcsolatban a [SAP Cloud Appliance könyvtár 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> 2017. május 29. a mellett a kevesebb az előnyben részesített klasszikus üzemi modellben az Azure Resource Manager üzemi modell segítségével az SAP CAL telepítheti. Azt javasoljuk, hogy használja az új Resource Manager-alapú üzemi modellt, és figyelmen kívül hagyja a klasszikus üzemi modellben.

## <a name="step-by-step-process-to-deploy-the-solution"></a>A megoldás részletes folyamata

Képernyőfelvételek szélén a következő folyamat bemutatja, hogyan telepítse az S/4hana-t az Azure-ban az SAP CAL segítségével. A folyamat más megoldások, például a BW/4hana-t ugyanúgy működik.

A **megoldások** lap közül mutat be néhányat az elérhető SAP CAL HANA-alapú megoldásokkal az Azure-ban. **SAP S/4HANA 1610-es FPS01, Fully-Activated készülék** a középső sorában szerepel:

![SAP CAL-megoldások](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Az SAP CAL-fiók létrehozása
1. Először jelentkezzen be az SAP CAL, az SAP S felhasználói vagy más felhasználó regisztrált az SAP. Majd adja meg a SAP CAL-fiókkal, amelyet az SAP CAL berendezések Azure-beli üzembe helyezéséhez. A fiók definíciójában kell tennie:

    a. Válassza ki az Azure-ban (Resource Manager vagy klasszikus) üzemi.

    b. Adja meg az Azure-előfizetésében. Egy SAP CAL-fiókot csak egy előfizetéssel is hozzárendelhető. Ha több előfizetéssel, hozzon létre egy másik SAP CAL-fiókot szeretne.

    c. Az SAP CAL engedélyt helyezze üzembe az Azure-előfizetésében.

   > [!NOTE]
   >  A következő lépések bemutatják, hogyan hozhat létre a Resource Manager üzembe helyezések SAP CAL-fiókot. Ha már rendelkezik egy SAP CAL-fiókkal, amely a klasszikus üzemi modell van csatolva, *kell* , kövesse az alábbi lépéseket egy új SAP CAL-fiók létrehozásához. Az új SAP CAL-fióknak rendelkeznie kell üzembe helyezni a Resource Manager-modellben.

1. Hozzon létre egy új SAP CAL-fiókot. A **fiókok** lapon látható három lehetősége az Azure-hoz: 

    a. **A Microsoft Azure (klasszikus)** a klasszikus üzemi modell, és már nem elsődleges.

    b. **A Microsoft Azure** az új Resource Manager üzemi modell.

    c. **Windows Azure 21Vianet által üzemeltetett** a klasszikus üzemi modellt használó Kínában lehetőség van.

    A Resource Manager-modellben üzembe helyezéséhez válassza **Microsoft Azure**.

    ![Az SAP CAL fiók adatai](./media/cal-s4h/s4h-pic-2a.png)

1. Adja meg az Azure **előfizetés-azonosító** , amely az Azure Portalon található.

   ![Az SAP CAL-fiókok](./media/cal-s4h/s4h-pic3c.png)

1. A megadott Azure-előfizetésben helyezi üzembe helyezéséhez SAP CAL engedélyezésére, kattintson a **engedélyezés**. A következő lap a böngészőlapon jelenik meg:

   ![Az Internet Explorer cloud services – bejelentkezési](./media/cal-s4h/s4h-pic4c.png)

1. Ha egynél több felhasználó szerepel a listán, válassza ki a Microsoft-fiókkal, amely kapcsolódik a kiválasztott Azure-előfizetés coadministrator lehet. A következő lap a böngészőlapon jelenik meg:

   ![Az Internet Explorer cloud services megerősítése](./media/cal-s4h/s4h-pic5a.png)

1. Kattintson a **fogadja el**. Ha az engedélyezési művelet sikeres, az SAP CAL definíció újra jeleníti meg. Rövid idő után egy üzenet megerősíti, hogy az engedélyezési folyamat sikeres volt-e.

1. Az újonnan létrehozott SAP CAL-fiók hozzárendelése a felhasználóhoz, írja be a **felhasználói azonosító** a jobb oldalon, majd a szövegmezőben **Hozzáadás**.

   ![Felhasználók társítása fiókot](./media/cal-s4h/s4h-pic8a.png)

1. Kattintson a fiók társítása a felhasználót, hogy használhatja a bejelentkezni az SAP CAL, **felülvizsgálati**. 
 
1. A felhasználó és az újonnan létrehozott SAP CAL-fiók közötti társítás létrehozásához kattintson a **létrehozás**.

   ![Felhasználók és az SAP CAL fiók közötti](./media/cal-s4h/s4h-pic9b.png)

Sikeresen létrehozott egy SAP CAL-fiókot, amely képes:

- Használja a Resource Manager-alapú üzemi modellt.
- SAP-rendszereinket helyezze üzembe az Azure-előfizetésében.

Most már elindíthatja az S/4hana-t helyezze üzembe a felhasználói előfizetés az Azure-ban.

> [!NOTE]
> A folytatás előtt határozza meg, hogy rendelkezik-e az Azure vCPU-kvóták az Azure H-sorozatú virtuális gépek. Jelenleg az SAP CAL H-sorozatú virtuális gépek az Azure központi telepítéséhez használja az SAP HANA-alapú megoldások némelyike. Az Azure-előfizetéshez esetleg nincs H-sorozat vCPU-kvóták a H-sorozat esetében. Ha igen, szüksége lehet beolvasni a H-sorozat legalább 16 Vcpu-kvóta az Azure ügyfélszolgálatától.
> 
> [!NOTE]
> Az SAP CAL az Azure-ban a megoldás üzembe helyezésekor, előfordulhat, hogy csak egy Azure-régiót kiválaszthatja. Az SAP CAL által javasolt eltérő Azure-régióban való üzembe helyezéséhez SAP CAL előfizetést vásárolni kell. Emellett szüksége lehet nyisson meg egy üzenetet az SAP a CAL fiók engedélyezve van, hogy az eredetileg javasolt eltérő Azure-régióban.

### <a name="deploy-a-solution"></a>Megoldás üzembe helyezése

A megoldás üzembe helyezni a **megoldások** az SAP CAL lapján. Az SAP CAL van két feladatütemezések üzembe helyezéséhez:

- Egy alapszintű sorozat, amely egy oldalt használja a rendszer definiálásához
- Egy speciális sorozat, amely a Virtuálisgép-méretek bizonyos lehetőséget kínál 

Bemutatjuk, hogyan üzembe helyezés itt alapszintű elérési útját.

1. Az a **fiókadatok** lapon kell tennie:

    a. Válasszon egy SAP CAL-fiókot. (Egy a Resource Manager üzemi modellel üzembe helyezéséhez rendelt fiókot használja.)

    b. Adjon meg egy példányt **neve**.

    c. Válassza ki az Azure-beli **régió**. Az SAP CAL javasol egy régiót. Ha egy másik Azure-régióban van szüksége, és nem rendelkezik az SAP CAL-előfizetéssel, az SAP CAL előfizetés sorrendjének szüksége.

    d. Adja meg a főkiszolgáló **jelszó** nyolc vagy kilenc csomópontos karaktereket a megoldáshoz. A jelszó szolgál a különböző összetevők rendszergazdái számára.

   ![Az SAP CAL alapszintű mód: Példány létrehozása](./media/cal-s4h/s4h-pic10a.png)

1. Kattintson a **létrehozás**, és a megjelenő üzenetpanelen kattintson **OK**.

   ![SAP CAL támogatott Virtuálisgép-méretek](./media/cal-s4h/s4h-pic10b.png)

1. Az a **titkos kulcs** párbeszédpanelen kattintson a **Store** a titkos kulcs tárolása az SAP CAL. A titkos kulcs jelszavas védelem használatához kattintson **letöltése**. 

   ![Az SAP CAL titkos kulcsa](./media/cal-s4h/s4h-pic10c.png)

1. Olvassa el az SAP CAL **figyelmeztetés** üzenetet, és kattintson az **OK**.

   ![Az SAP CAL figyelmeztetés](./media/cal-s4h/s4h-pic10d.png)

    Most már az üzemelő példány akkor kerül sor. Némi várakozás után a mérete és összetettsége, a megoldás (az SAP CAL biztosít becslés), az állapota, aktív és használatra kész.

1. A virtuális gépek, a többi kapcsolódó erőforrást egy erőforráscsoportban az gyűjtött megkereséséhez nyissa meg az Azure Portalon: 

   ![Az új portálon üzembe helyezett SAP CAL-objektumok](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Az SAP CAL portálon az állapot akkor jelenik meg **aktív**. Kattintson a megoldáshoz történő csatlakoztatásáról **Connect**. Ez a megoldás különböző beállításokat szeretne csatlakozni a különböző összetevők legyenek üzembe helyezve.

   ![SAP CAL-példányok](./media/cal-s4h/active_solution.png)

1. Kattintson a használata előtt a egyikét az üzembe helyezett rendszerhez való csatlakozáshoz, **Getting Started Guide**. 

   ![Kapcsolódjon a példányhoz](./media/cal-s4h/connect_to_solution.png)

    A dokumentáció a felhasználók számára a csatlakozási módszer neveket. A jelszavak azoknak a felhasználóknak a főkiszolgáló jelszavát, a telepítési folyamat elején meghatározott vannak állítva. A dokumentáció más funkciógazdagabb felhasználók szerepel a listában a jelszavukat, amelyek segítségével jelentkezzen be a telepített rendszer. 

    Például az SAP grafikus felhasználói Felülettel, amely előre telepítve van a Windows távoli asztali gép használatakor a S/4 rendszer kinéznie:

   ![Az előre telepített SAP grafikus felhasználói felületen SM50](./media/cal-s4h/gui_sm50.png)

    Vagy a DBACockpit használja, ha a példány ehhez hasonló lehet:

   ![SM50 a DBACockpit SAP grafikus felhasználói felületen](./media/cal-s4h/dbacockpit.png)

Néhány órán belül egy megfelelő SAP S/4 készülék üzemel az Azure-ban.

Az SAP egy SAP CAL-előfizetést vásárolt, teljes mértékben támogatja az SAP CAL üzembe helyezést az Azure-ban. A támogatási várólista BC-VCM-CAL.







