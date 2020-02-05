---
title: Az AVS Private Cloud-jogosultságok kiemelése – Azure VMware-megoldás AVS-vel
description: Ismerteti, hogyan lehet jogosultságokat kibővíteni az AVS privát felhőben a vCenter felügyeleti funkcióihoz
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 211960af359e19f93afef58162c5b09ae1d9b23f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025316"
---
# <a name="escalate-avs-private-cloud-vcenter-privileges-from-the-avs-portal"></a>AVS Private Cloud vCenter-jogosultságok kiemelése az AVS-portálról

Az AVS Private Cloud vCenter való rendszergazdai hozzáféréshez ideiglenesen megadhatja az AVS-jogosultságokat. Emelt szintű jogosultságok használatával VMware-megoldásokat telepíthet, identitás-forrásokat adhat hozzá, és kezelheti a felhasználókat.

Új felhasználók hozhatók létre a vCenter SSO-tartományon, és hozzáférhetnek a vCenter. Új felhasználók létrehozásakor vegye fel őket a vCenter elérésére szolgáló AVS-beli beépített csoportokba. További információ: [az AVS Private Cloud engedélyezési modellje VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Ne végezzen konfigurációs módosításokat a felügyeleti összetevőkön. Az emelt szintű jogosultságú állapotban végrehajtott műveletek hátrányosan befolyásolhatják a rendszereket, vagy okozhatják, hogy a rendszerek elérhetetlenné váljanak.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="escalate-privileges"></a>Jogosultságok eszkalációja

1. Hozzáférés az [AVS-portálhoz](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot, és válassza ki azt az AVS Private-felhőt, amelynek a jogosultságait meg szeretné jeleníteni.

3. Az összefoglalás lap alján, a **vSphere-jogosultságok módosítása**területen kattintson a **kiterjesztés**elemre.

    ![VSphere jogosultság módosítása](media/escalate-private-cloud-privilege.png)

4. Válassza ki a vSphere felhasználói típusát. Csak `CloudOwner@cloudsimple.local` helyi felhasználót lehet kibővíteni.

5. Válassza ki a legördülő menüből a kiterjesztés időintervallumát. Válassza ki a legrövidebb időszakot, amely lehetővé teszi a feladat befejezését.

6. Jelölje be a jelölőnégyzetet, és erősítse meg, hogy ismeri a kockázatokat.

    ![Jogosultság eszkalációja párbeszédpanel](media/escalate-private-cloud-privilege-dialog.png)

7. Kattintson az **OK** gombra.

8. A eszkalációs folyamat néhány percet is igénybe vehet. Amikor végzett, kattintson az **OK** gombra.

A jogosultságok eszkalációja megkezdődik, és a kijelölt intervallum végéig tart. Rendszergazdai feladatok elvégzéséhez bejelentkezhet az AVS Private Cloud vCenter.

> [!IMPORTANT]
> Csak egy felhasználó rendelkezhet megadható jogosultságokkal. Mielőtt egy másik felhasználó jogosultságait kiterjesztheti, meg kell szüntetnie a felhasználó jogosultságait.

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group*szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  A vSphere webes felhasználói felületén csak a szolgáltatási fiókokat kell felvenni a *rendszergazdák* csoportjába, és a szolgáltatásfiókok nem használhatók.

## <a name="extend-privilege-escalation"></a>Jogosultságok kiterjesztésének kiterjesztése

Ha további időre van szüksége a feladatok elvégzéséhez, kiterjesztheti a jogosultság-eszkalációs időszakot. Válassza ki a további eszkalációs időszakot, amely lehetővé teszi a felügyeleti feladatok végrehajtását.

1. Az AVS-portálon található **erőforrások** > **AVS Private-felhők** területen válassza ki azt az AVS Private-felhőt, amelyhez ki szeretné terjeszteni a jogosultságok eszkalációját.

2. Az összefoglalás lap alján kattintson a **jogosultságok kiterjesztésének kiterjesztése**elemre.

    ![Jogosultságok kiterjesztésének kiterjesztése](media/de-escalate-private-cloud-privilege.png)

3. A legördülő listából válasszon ki egy eszkalációs időintervallumot. Tekintse át az új eszkaláció befejezési időpontját.

4. Az intervallum meghosszabbításához kattintson a **Mentés** gombra.

## <a name="de-escalate-privileges"></a>Jogosultságok kiterjesztésének megszüntetése

A rendszergazdai feladatok elvégzése után meg kell szüntetnie a jogosultságok kiterjesztését. 

1. Az AVS-portálon az AVS **Private-felhők** > **erőforrásainál** válassza ki azt az AVS Private-felhőt, amelynek meg szeretné szüntetni a jogosultságait.

2. Kattintson a **deeszkaláció**elemre.

3. Kattintson az **OK** gombra.

> [!IMPORTANT]
> A hibák elkerülése érdekében jelentkezzen ki a vCenter, és jelentkezzen be ismét a defokozódó jogosultságok után.

## <a name="next-steps"></a>Következő lépések

* [VCenter-identitások beállítása a Active Directory használatára](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Biztonsági mentési megoldás telepítése a számítási [feladatok virtuális gépei](https://docs.azure.cloudsimple.com/backup-workloads-veeam/) számára