---
title: Magánfelhő jogosultságokkal – Azure VMware megoldás által CloudSimple eszkalálása
description: Ismerteti, hogyan lehet eszkalálni a jogosultságokat a vCenter feljogosított a magánfelhőben
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332850"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>A CloudSimple portálról Magánfelhő vCenter jogosultságainak eszkalációjára 

A saját Magánfelhő rendszergazdai hozzáféréshez CloudSimple jogosultságok eszkalációjára ideiglenesen is.  Emelt szintű jogosultságokkal telepítse a VMware-megoldások, identitás források hozzáadása és felhasználók kezeléséhez.

Új felhasználók a vCenter SSO tartományon létrehozhatók és vcenter-hozzáférést adott.  Ha új felhasználók létrehozására, azokat hozzá a CloudSimple beépített csoportokat vCenter eléréséhez.  További információkért lásd: [CloudSimple Magánfelhő engedélyezési modellekkel a VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Nincs semmilyen konfigurációs módosítást az összetevőket. Eszkalált az emelt szintű állapotban végrehajtott műveletek negatívan befolyásolhatja a rendszer, vagy is, hogy a rendszer elérhetetlenné válik.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="escalate-privileges"></a>Jogosultságok eszkalációja

1. Hozzáférés a [CloudSimple portál](access-cloudsimple-portal.md).

2. Nyissa meg a **erőforrások** lapra, jelölje be a Magánfelhő, amelyekhez eszkalálni a jogosultságokat.

3. Az összefoglalás lapon alatt alján **vSphere jogosultságok módosítása**, kattintson a **Escalate**.

    ![Módosítsa a vSphere-jogosultság](media/escalate-private-cloud-privilege.png)

4. Válassza ki a vSphere felhasználói típusát.  Csak **CloudOwner@cloudsimple.local** helyi felhasználói eszkalálva is lehet.

5. Válassza ki a escalate időtartam alatt a legördülő listából. Válassza ki a lehető legrövidebb idő, amely lehetővé teszi, hogy a feladat végrehajtásához.

6. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy megértette a kockázatokat.

    ![Párbeszédpanel jogosultság eszkalálása](media/escalate-private-cloud-privilege-dialog.png)

7. Kattintson az **OK** gombra.

8. A kiterjesztési folyamat néhány percet is igénybe vehet. Amikor végzett, kattintson az **OK** gombra.

A jogosultságok eszkalálását kezdődik, és a kijelölt időszak végéig tart.  Bejelentkezhet a saját magánfelhő-alapú felügyeleti feladatokat végezheti el.

> [!IMPORTANT]
> Csak egy felhasználó is rendelkezik eszkalálta a jogosultságokat.  A felhasználói jogosultságok kell eszkalálása megszüntetéséhez, mielőtt segítséget is kér egy másik felhasználói jogosultságok.

## <a name="extend-privilege-escalation"></a>Jogosultságok eszkalálását kiterjesztése

Ha a feladatok végrehajtásához további időre van szüksége, kiterjesztheti a jogosultság-eszkalációs időszak.  Válassza ki a további eszkalálása időtartam, amely lehetővé teszi, hogy a felügyeleti feladatok végezhetők el.

1. Az a **erőforrások** > **Magánfelhők** CloudSimple portálon, válassza ki, amelyhez hozzá kívánja kiterjeszteni a jogosultságok eszkalálását a Magánfelhő.

2. Az Összefoglalás lap alsó részén kattintson **kiterjesztése a jogosultságok eszkalálását**.

    ![Jogosultságok eszkalálását kiterjesztése](media/de-escalate-private-cloud-privilege.png)

3. A legördülő listából válassza ki a egy escalate időközt. Tekintse át az új eszkalációs befejezési időt.

4. Kattintson a **mentése** bővítése az időközt.

## <a name="de-escalate-privileges"></a>Vonja eszkalálni a jogosultságokat

Ha befejeződött a felügyeleti feladatokat, megszüntetése az jogosultságok eszkalációjára kell.  

1. Az a **erőforrások** > **Magánfelhők** CloudSimple portálon, válassza ki a privát felhő, amelynek meg szeretné megszüntetéséhez eszkalálni a jogosultságokat.

2. Kattintson a **megszüntetéséhez eszkalálása**.

3. Kattintson az **OK** gombra.

> [!IMPORTANT]
> Hibák elkerülése érdekében jelentkezzen ki a vCenter, és jelentkezzen be újra, miután a problémák megszüntetése továbbítása jogosultságokkal.

## <a name="next-steps"></a>További lépések

* [VCenter-identitás források beállítása az Active Directory használatára](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* A biztonsági mentési megoldás telepítése [munkaterhelési virtuális gépek biztonsági mentését](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)