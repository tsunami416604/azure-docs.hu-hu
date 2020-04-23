---
title: Magánfelhő-jogosultságok eszkalálódása
titleSuffix: Azure VMware Solution by CloudSimple
description: Ez a témakör azt ismerteti, hogy miként bővítheti a jogosultságokat a magánfelhőben a vCenter felügyeleti funkcióihoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870478"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>A Privát felhőbeli vCenter-jogosultságok eszkalációja a CloudSimple portálról

A privát felhőbeli vCenter felügyeleti hozzáférés, ideiglenesen bővítheti a CloudSimple jogosultságait.  Emelt szintű jogosultságok használatával telepítheti a VMware-megoldásokat, identitásforrásokat adhat hozzá, és kezelheti a felhasználókat.

Új felhasználók hozhatók létre a vCenter SSO-tartományban, és hozzáférést kapnak a vCenterhez.  Amikor új felhasználókat hoz létre, adja hozzá őket a CloudSimple beépített csoportok hoz a vCenter eléréséhez.  További információ: [CloudSimple Private Cloud permission model of VMware vCenter](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/).

> [!CAUTION]
> Ne módosítsa a felügyeleti összetevők konfigurációját. Az eszkalált privilegizált állapotban végrehajtott műveletek hátrányosan befolyásolhatják a rendszert, vagy a rendszer elérhetetlenné válását okozhatják.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="escalate-privileges"></a>Jogosultságok eszkalációja

1. A [CloudSimple portál](access-cloudsimple-portal.md)elérése.

2. Nyissa meg az **Erőforrások** lapot, és válassza ki azt a magánfelhőt, amelyhez a jogosultságokat el szeretné fokozni.

3. A **VSphere-jogosultságok módosítása**csoport Összegzés lapjának alján kattintson az **Eszkalálás gombra.**

    ![VSphere jogosultság módosítása](media/escalate-private-cloud-privilege.png)

4. Válassza ki a vSphere felhasználói típusát.  Csak `CloudOwner@cloudsimple.local` a helyi felhasználó fokozható.

5. Válassza ki az eszkalációs időintervallumot a legördülő menüből. Válassza ki azt a legrövidebb időszakot, amely lehetővé teszi a feladat elvégzését.

6. Jelölje be a jelölőnégyzetet, ha meg szeretné erősíteni, hogy tisztában van a kockázatokkal.

    ![Jogosultság eszkalációja párbeszédpanel](media/escalate-private-cloud-privilege-dialog.png)

7. Kattintson az **OK** gombra.

8. Az eszkalációs folyamat eltarthat néhány percig. Amikor végzett, kattintson az **OK** gombra.

A jogosultság eszkalációja a kijelölt intervallum végéig tart.  Rendszergazdai feladatok elvégzéséhez jelentkezzen be a privát felhőbeli vCenterbe.

> [!IMPORTANT]
> Csak egy felhasználó rendelkezhet eszkalált jogosultságokkal.  Egy másik felhasználó jogosultságai eszkalálódása előtt meg kell fejtenie a felhasználó jogosultságait.

> [!CAUTION]
> Új felhasználókat csak a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* vagy *cloud-global-vm-admin-group csoporthoz*kell hozzáadni.  A *Rendszergazdák* csoportba hozzáadott felhasználók automatikusan törlődnek.  Csak a szolgáltatásfiókokat kell hozzáadni *a Rendszergazdák* csoporthoz, és a szolgáltatásfiókok nem használhatók a vSphere webes felhasználói felületére való bejelentkezéshez.

## <a name="extend-privilege-escalation"></a>Jogosultságeszkaláció kiterjesztése

Ha további időre van szüksége a feladatok elvégzéséhez, meghosszabbíthatja a jogosultságeszkalációs időszakot.  Válassza ki a további eszkalációs időintervallumot, amely lehetővé teszi a felügyeleti feladatok elvégzését.

1. A **CloudSimple** > portál erőforrások**magánfelhőiben** válassza ki azt a magánfelhőt, amelyhez ki szeretné terjeszteni a jogosultságeszkalációt.

2. Az Összegzés lap alján kattintson a **Jogosultságeszkaláció kiterjesztése**gombra.

    ![Jogosultságeszkaláció kiterjesztése](media/de-escalate-private-cloud-privilege.png)

3. Válasszon egy eszkalációs időintervallumot a legördülő menüből. Tekintse át az új eszkaláció befejezési idejét.

4. Az intervallum meghosszabbításához kattintson a **Mentés** gombra.

## <a name="de-escalate-privileges"></a>Jogosultságok eszkalációjának lealacsonyodása

Miután a felügyeleti feladatok befejeződtek, meg kell fokoznia a jogosultságokat.  

1. A **CloudSimple** > portál erőforrások**magánfelhőiben** válassza ki azt a magánfelhőt, amelyhez a jogosultságok eszkalációját szeretné kivenni.

2. Kattintson **az Eszkaláció gombra.**

3. Kattintson az **OK** gombra.

> [!IMPORTANT]
> A hibák elkerülése érdekében jelentkezzen ki a vCenterből, és jelentkezzen be újra a jogosultságok eszkalációjának eltörlése után.

## <a name="next-steps"></a>További lépések

* [VCenter-identitásforrások beállítása az Active Directory használatára](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* Biztonsági mentési megoldás telepítése a [számítási feladatok virtuális gépei](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/) számára