---
title: Azure VMware-megoldás CloudSimple gyors üzembe helyezése – VMware virtuális gépek használata az Azure-ban
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhatja és használhatja a VMware virtuális gépeket a Azure Portal Azure VMware-megoldással a CloudSimple használatával
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816651"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Gyors útmutató: VMware rendszerű virtuális gépek felhasználása az Azure-ban

Ha virtuális gépet szeretne létrehozni a Azure Portalban, használhatja a saját felhőalapú vCenter elérhető virtuálisgép-sablonokat. A vCenter-rendszergazdák további sablonokat is létrehozhatnak a privát felhőben.

## <a name="create-a-vm-template"></a>Virtuálisgép-sablon létrehozása

Először hozzon létre egy virtuális gépet a saját felhőben a vCenter felhasználói felület használatával. Sablon létrehozásához kövesse a VMware cikk a [virtuális gép klónozása a vSphere webes ügyfélprogramban található sablonban](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)leírt utasításait. A virtuálisgép-sablon tárolása a saját Felhőbeli vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portalban

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple Virtual Machines**.

3. Válassza a **Hozzáadás** lehetőséget.

    ![Hozzáadás kiválasztása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg a következő adatokat a virtuális gépről, majd válassza **a Next (tovább) gombot: Méret**.

    ![CloudSimple virtuális gép létrehozása – alapismeretek](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Mező | Leírás |
    | ------------ | ------------- |
    | **Előfizetés** | A saját felhőhöz társított Azure-előfizetés.  |
    | **Erőforráscsoport** | Az erőforráscsoport, amelyhez a virtuális gép hozzá lesz rendelve. Választhat egy meglévő csoportot, vagy létrehozhat egy újat. |
    | **Name** | A virtuális gép azonosítására szolgáló név.  |
    | **Location** | Az az Azure-régió, amelyben a virtuális gép üzemeltetve van.  |
    | **Privát felhő** | Az a CloudSimple, amelyben létre szeretné hozni a virtuális gépet. |
    | **ResourcePool** | Egy hozzárendelt erőforráskészlet a virtuális géphez. Válasszon a rendelkezésre álló erőforráskészlet közül. |
    | **vSphere-sablon** | A virtuális gép vSphere-sablonja.  |
    | **Felhasználónév** | A virtuális gép rendszergazdájának felhasználóneve (Windows-sablonok esetén).|
    | **Jelszó** |  A virtuális gép rendszergazdájának jelszava (Windows-sablonok esetén). |
    | **Jelszó megerősítése** | Az előző mezőben megadott jelszó. |

5. Válassza ki a virtuális gép számára a magok számát és a memória kapacitását. Ha a teljes CPU-virtualizálás elérhetővé kívánja tenni a vendég operációs rendszert, jelölje be a elérhetővé tétele a **vendég** operációs rendszernek jelölőnégyzetet. A hardveres virtualizációt igénylő alkalmazások a bináris fordítás vagy a paravirtualizációs nélküli virtuális gépeken is futtathatók. További információkért lásd a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware hardveres virtualizációs</a>szolgáltatás elérhetővé tételével foglalkozó cikket. Ha elkészült, kattintson **a Next (tovább) gombra: Konfigurációk**.

    ![CloudSimple virtuális gép létrehozása – méret](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurálja a hálózati adaptereket és a lemezeket az alábbi táblázatokban leírtak szerint, majd válassza a **felülvizsgálat + létrehozás**elemet.

    ![CloudSimple virtuális gép létrehozása – konfigurációk](media/create-cloudsimple-virtual-machine-configurations.png)

    Hálózati adapterek esetében válassza a **hálózati adapter hozzáadása** lehetőséget, majd adja meg a következő beállításokat:
    
    | Beállítás | Leírás |
    | ------------ | ------------- |
    | **Name** | Adja meg a felületet azonosító nevet.  |
    | **Hálózat** | Válasszon a saját Felhőbeli vSphere konfigurált elosztott portok listájából.  |
    | **Adapter** | Válasszon ki egy vSphere-adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információ: a <a href="https://kb.vmware.com/s/article/1001805" target="_blank">virtuális gép hálózati adapterét</a>kiválasztó vmware-cikk. |
    | **Bekapcsolás rendszerindításkor** | Adja meg, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

    Lemezek esetében válassza a **lemez hozzáadása** lehetőséget, majd konfigurálja a következő beállításokat:

    | Beállítás | Leírás |
    | ------------ | ------------- |
    | **Name** | Adjon meg egy nevet a lemez azonosításához.  |
    | **Méret** | Válasszon ki egy rendelkezésre álló méretet.  |
    | **SCSI-vezérlő** | Válasszon ki egy SCSI-vezérlőt a lemezhez.  |
    | **Mód** | A mód azt határozza meg, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon egyet a következő lehetőségek közül: <br> **Független állandó**: A lemezre írt összes módosítás véglegesen íródik.<br> **Független, nem állandó**: A lemezre írt módosításokat a rendszer elveti a virtuális gép kikapcsolásakor vagy alaphelyzetbe állításakor. A független, nem állandó mód lehetővé teszi, hogy a virtuális gépet mindig ugyanabban az állapotban indítsa újra. További információt a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware dokumentációjában</a>talál.

7. Az érvényesítés befejezése után tekintse át a beállításokat, és válassza a **Létrehozás**lehetőséget. A módosítások végrehajtásához válassza a felül található füleket, vagy válassza **az előző lehetőséget: Konfigurációk**.

    ![CloudSimple virtuális gép létrehozása – áttekintés + létrehozás](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>További lépések

* [CloudSimple virtuális gépek listájának megtekintése](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [CloudSimple virtuális gépek kezelése az Azure-ból](https://docs.azure.cloudsimple.com/azure-manage-vm/)
