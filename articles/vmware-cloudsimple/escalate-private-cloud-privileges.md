---
title: Privát Felhőbeli jogosultságok kiterjesztve
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerteti, hogyan lehet jogosultságokat kibővíteni a privát felhőben a vCenter felügyeleti funkcióihoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ee722f88f766178dd658600893b4497bf611be9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372771"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Saját Felhőbeli vCenter jogosultságok kiemelése a CloudSimple-portálról

A saját felhőalapú vCenter való rendszergazdai hozzáféréshez átmenetileg megadhatja a CloudSimple-jogosultságokat.  Emelt szintű jogosultságok használatával VMware-megoldásokat telepíthet, identitás-forrásokat adhat hozzá, és kezelheti a felhasználókat.

Új felhasználók hozhatók létre a vCenter SSO-tartományon, és hozzáférhetnek a vCenter.  Új felhasználók létrehozásakor a vCenter eléréséhez vegye fel őket a CloudSimple beépített csoportjaiba.  További információ: [CloudSimple Private Cloud Permission Model of VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Ne végezzen konfigurációs módosításokat a felügyeleti összetevőkön. Az emelt szintű jogosultságú állapotban végrehajtott műveletek hátrányosan befolyásolhatják a rendszereket, vagy okozhatják, hogy a rendszerek elérhetetlenné váljanak.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="escalate-privileges"></a>Jogosultságok eszkalációja

1. Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot, és válassza ki azt a privát felhőt, amelynek a jogosultságait meg szeretné jeleníteni.

3. Az összefoglalás lap alján, a **vSphere-jogosultságok módosítása**területen kattintson a **kiterjesztés**elemre.

    ![VSphere jogosultság módosítása](media/escalate-private-cloud-privilege.png)

4. Válassza ki a vSphere felhasználói típusát.  Csak `CloudOwner@cloudsimple.local` helyi felhasználót lehet kibővíteni.

5. Válassza ki a legördülő menüből a kiterjesztés időintervallumát. Válassza ki a legrövidebb időszakot, amely lehetővé teszi a feladat befejezését.

6. Jelölje be a jelölőnégyzetet, és erősítse meg, hogy ismeri a kockázatokat.

    ![Jogosultság eszkalációja párbeszédpanel](media/escalate-private-cloud-privilege-dialog.png)

7. Kattintson az **OK** gombra.

8. A eszkalációs folyamat néhány percet is igénybe vehet. Amikor végzett, kattintson az **OK** gombra.

A jogosultságok eszkalációja megkezdődik, és a kijelölt intervallum végéig tart.  A rendszergazdai feladatok elvégzéséhez bejelentkezhet a saját Felhőbeli vCenter.

> [!IMPORTANT]
> Csak egy felhasználó rendelkezhet megadható jogosultságokkal.  Mielőtt egy másik felhasználó jogosultságait kiterjesztheti, meg kell szüntetnie a felhasználó jogosultságait.

## <a name="extend-privilege-escalation"></a>Jogosultságok kiterjesztésének kiterjesztése

Ha további időre van szüksége a feladatok elvégzéséhez, kiterjesztheti a jogosultság-eszkalációs időszakot.  Válassza ki a további eszkalációs időszakot, amely lehetővé teszi a felügyeleti feladatok végrehajtását.

1. A CloudSimple-portálon található **privát felhők** > **erőforrásainál** válassza ki azt a privát felhőt, amelynek meg szeretné hosszabbítani a jogosultság-eszkalációját.

2. Az összefoglalás lap alján kattintson a **jogosultságok kiterjesztésének kiterjesztése**elemre.

    ![Jogosultságok kiterjesztésének kiterjesztése](media/de-escalate-private-cloud-privilege.png)

3. A legördülő listából válasszon ki egy eszkalációs időintervallumot. Tekintse át az új eszkaláció befejezési időpontját.

4. Az intervallum meghosszabbításához kattintson a **Mentés** gombra.

## <a name="de-escalate-privileges"></a>Jogosultságok kiterjesztésének megszüntetése

A rendszergazdai feladatok elvégzése után meg kell szüntetnie a jogosultságok kiterjesztését.  

1. A CloudSimple-portálon található **privát felhők** > **erőforrásainál** válassza ki azt a privát felhőt, amelynek a jogosultságait ki szeretné bővíteni.

2. Kattintson a **deeszkaláció**elemre.

3. Kattintson az **OK** gombra.

> [!IMPORTANT]
> A hibák elkerülése érdekében jelentkezzen ki a vCenter, és jelentkezzen be ismét a defokozódó jogosultságok után.

## <a name="next-steps"></a>Következő lépések

* [VCenter-identitások beállítása a Active Directory használatára](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Biztonsági mentési megoldás telepítése a számítási [feladatok virtuális gépei](https://docs.azure.cloudsimple.com/backup-workloads-veeam/) számára