---
title: Azure VMware-megoldás által CloudSimple rövid útmutató – VMware virtuális gépek használata az Azure-ban
description: Ezt a gyorsútmutatót követve elsajátíthatja konfigurálása, és felhasználhatják a VMware virtuális gépek az Azure Portalról CloudSimple Azure VMware-megoldások használatával
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393496"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Gyors útmutató: VMware rendszerű virtuális gépek felhasználása az Azure-ban

Virtuális gép létrehozása az Azure Portalon, használhatja a magánfelhő vcenter elérhető virtuálisgép-sablonok. A vCenter-rendszergazda létrehozhat további sablonokat a magánfelhőbe.

## <a name="create-a-vm-template"></a>Virtuálisgép-sablon létrehozása

Először hozzon létre egy virtuális gépet a magánfelhőben a vcenter-kiszolgáló felhasználói felületének használatával. Hozzon létre egy sablont, hajtsa végre a VMware-cikkben lévő utasítások [a vSphere webes ügyfélben a sablon a virtuális gép klónozása](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). A Virtuálisgép-sablon Store a magánfelhő vcenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg **CloudSimple virtuális gépek**.

3. Válassza a **Hozzáadás** lehetőséget.

    ![Válassza a hozzáadása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg a következő információkat a virtuális gépet, majd **tovább: Méret**.

    ![Hozzon létre CloudSimple virtuálisgép - alapjai](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Mező | Leírás |
    | ------------ | ------------- |
    | **Előfizetés** | Az Azure-előfizetést a magánfelhő társított.  |
    | **Erőforráscsoport** | Az erőforráscsoport, amely a virtuális gép hozzá lesz rendelve. Válasszon egy meglévő csoportot, vagy hozzon létre egy újat. |
    | **Name (Név)** | A virtuális gép azonosító nevet.  |
    | **Hely** | Az Azure-régió, amelyben a virtuális gép üzemel.  |
    | **Magánfelhő** | A CloudSimple magánfelhőt, amelyben meg szeretné a virtuális gép létrehozásához. |
    | **ResourcePool** | A virtuális gép erőforrás-készlet. Válassza ki az elérhető erőforráskészletek. |
    | **vSphere Template** | A vSphere sablont a virtuális gép számára.  |
    | **Felhasználónév** | Azon felhasználó felhasználónevét, a VM-rendszergazda (a Windows-sablonok).|
    | **Jelszó** |  (A Windows-sablonok) a virtuális gép rendszergazdai jelszava. |
    | **Jelszó megerősítése** | Az előző mezőben megadott jelszót. |

5. Válassza ki a magok számától és a memória-kapacitás a virtuális gép számára. Válassza ki **tegye elérhetővé a vendég operációs rendszer** Ha szeretné elérhetővé tenni a teljes CPU-virtualizálás a vendég operációs rendszer. Hardveres virtualizálás igénylő alkalmazások bináris fordítási vagy paravirtualizációs nélkül a virtuális gépeken futtathatja. További információkért olvassa el a VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">elérhetővé VMware hardver virtualizálásra</a>. Ha elkészült, válassza ki a **tovább: Konfigurációk**.

    ![Create CloudSimple Virtual Machine - Size](media/create-cloudsimple-virtual-machine-size.png)

6. Az alábbi táblázatban leírtak szerint konfigurálja a hálózati adapterek és a lemezek, majd **felülvizsgálat + létrehozása**.

    ![Create CloudSimple Virtual Machine - Configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Válassza ki a hálózati adapterek **Hozzáadás hálózati adapter** , majd konfigurálja a következő beállításokat:
    
    | Beállítás | Leírás |
    | ------------ | ------------- |
    | **Name (Név)** | Adja meg a kapcsolat nevét.  |
    | **Hálózat** | Válassza ki a konfigurált elosztott portcsoportokat a magánfelhő vsphere listájából.  |
    | **Adapter** | Válasszon ki egy vSphere-adaptert a virtuális Géphez konfigurált elérhető típusok listájából. További információkért olvassa el a VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">egy hálózati adaptert a virtuális géphez választása</a>. |
    | **Rendszerindításkor bekapcsolása** | Döntse el, hogy a hálózati adapter hardvere engedélyezze, ha a virtuális gép akkor indul el. Az alapértelmezett érték **engedélyezése**. |

    Lemezek kiválasztása **hozzáadás lemez** , majd konfigurálja a következő beállításokat:

    | Beállítás | Leírás |
    | ------------ | ------------- |
    | **Name (Név)** | Adjon meg egy nevet, a lemez azonosításához.  |
    | **Méret** | Válassza ki az elérhető méretek egyikét.  |
    | **SCSI-vezérlő** | Válassza ki a lemez SCSI-vezérlőhöz.  |
    | **mód** | A mód meghatározza, hogyan a lemez szerepel-e a pillanatképek. Válasszon egyet az alábbi lehetőségek közül: <br> **Állandó független**: A lemezre írt összes módosítás végleg készültek.<br> **Nem állandó független**: Amikor kikapcsolásához vagy a virtuális gép alaphelyzetbe állítása a a lemezre írt módosítások elvesznek. Független nem állandó mód lehetővé teszi, hogy minden esetben indítsa újra a virtuális gép ugyanabban az állapotban. További információkért lásd: a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware dokumentációjában</a>.

7. Érvényesítés befejezése után tekintse át a beállításokat, és válassza ki **létrehozás**. Hajtsa végre a módosításokat, válassza a lap tetején, és válassza ki **előző: Konfigurációk**.

    ![Felülvizsgálat létrehozása CloudSimple virtuálisgép - + létrehozása](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>További lépések

* [CloudSimple virtuális gépek listájának megtekintése](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [CloudSimple virtuális gépek kezelése az Azure-ból](https://docs.azure.cloudsimple.com/azure-manage-vm/)
