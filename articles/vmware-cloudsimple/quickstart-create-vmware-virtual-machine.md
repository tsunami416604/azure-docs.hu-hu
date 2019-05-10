---
title: CloudSimple rövid útmutató – Azure VMware-megoldások felhasználása VMware virtuális gépek az Azure-ban
description: Ismerje meg, hogyan konfigurálhat és VMware virtuális gépeket felhasználására által CloudSimple Azure VMware megoldást használ az Azure portálról
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0bb8d7a5a1ea30b704b44c9337cd28043597ff7
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209527"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Rövid útmutató – az Azure-ban a VMware virtuális gépeket felhasználására

Virtuális gép létrehozása az Azure Portalon, használja az előfizetéshez tartozó virtuálisgép-sablonok, amelyek a CloudSimple rendszergazda engedélyezte. Ezek a Virtuálisgép-sablonok találhatók a VMware-infrastruktúrára.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>CloudSimple virtuális gép létrehozása az Azure-ban van szükség a Virtuálisgép-sablonok

Hozzon létre egy virtuális gépet a magánfelhőben, a vCenter felhasználói felületén. Hozzon létre egy sablont, kövesse a [a vSphere webes ügyfélben a sablon a virtuális gép klónozása](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). A Virtuálisgép-sablon Store a magánfelhő vcenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg **CloudSimple virtuális gépek**.

3. Kattintson a **Hozzáadás**lehetőségre.

    ![CloudSimple virtuális gép létrehozása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg az alapvető információkat kattintson **tovább: mérete**.

    ![Hozzon létre CloudSimple virtuálisgép - alapjai](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Mező | Leírás |
    | ------------ | ------------- |
    | Előfizetés | A Magánfelhő társított Azure-előfizetés.  |
    | Erőforráscsoport | Az erőforráscsoport, amely hozzá lesz rendelve a virtuális Gépet. Válasszon egy meglévő csoportot, vagy hozzon létre egy újat. |
    | Name (Név) | A virtuális gépek azonosítására szolgáló név.  |
    | Location egység | Azure-régió, amelyben ez a virtuális gép üzemel.  |
    | Magánfelhő | CloudSimple Magánfelhő kívánja a virtuális gép létrehozásához. |
    | Erőforráskészlet | A virtuális Géphez csatlakoztatott erőforráskészletet. Válassza ki az elérhető erőforráskészletek. |
    | vSphere Template | a virtuális gép vSphere-sablont.  |
    | Felhasználónév | (A Windows-sablonok) a virtuális gép rendszergazdai felhasználóneve|
    | Jelszó |  (A Windows-sablonok) a virtuális gép rendszergazdai jelszava. |
    | Jelszó megerősítése | A jelszó megerősítése |

5. Válassza ki a magok számától és a virtuális Gépet, majd kattintson a memória-kapacitás **tovább: konfigurációk**. Jelölje be a jelölőnégyzetet, ha azt szeretné, a vendég operációs rendszer teljes CPU-virtualizálási elérhetővé. Hardveres virtualizálás igénylő alkalmazások bináris fordítási vagy paravirtualizációs nélkül a virtuális gépeken futtathatja. További információkért olvassa el a VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">elérhetővé VMware hardver virtualizálásra</a>.

    ![Hozzon létre CloudSimple virtuálisgép - mérete](media/create-cloudsimple-virtual-machine-size.png)

6. Az alábbi táblázatban leírtak szerint konfigurálja a hálózati adapterek és a lemezek, és kattintson a **felülvizsgálat + létrehozása**.

    ![CloudSimple virtuálisgép - konfigurációk létrehozása](media/create-cloudsimple-virtual-machine-configurations.png)

    Kattintson a hálózati adapterek **Hozzáadás hálózati adapter** és a következő beállításokat.
    
    | Vezérlés | Leírás |
    | ------------ | ------------- |
    | Name (Név) | Adja meg a kapcsolat nevét.  |
    | Network (Hálózat) | Jelölje be a Magánfelhő vsphere elosztott beállított port csoportot a listából.  |
    | Adapter | Válassza ki a vSphere-adapter a virtuális Géphez konfigurált elérhető típusok listájából. További információkért olvassa el a VMware Tudásbázis <a href="https://kb.vmware.com/s/article/1001805" target="_blank">egy hálózati adaptert a virtuális géphez választása</a>. |
    | Rendszerindításkor bekapcsolása | Döntse el, hogy a hálózati adapter hardvere engedélyezze, ha a virtuális gép akkor indul el. Az alapértelmezett érték **engedélyezése**. |

    A lemezek, kattintson a **hozzáadás lemez** és a következő beállításokat.

    | Elem | Leírás | 
    | ------------ | ------------- | 
    | Name (Név) | Adjon meg egy nevet, a lemez azonosításához.  | 
    | Méret | Válassza ki az elérhető méretek egyikét.  | 
    | SCSI-vezérlő | Válassza ki a lemez SCSI-vezérlőhöz.  |
    | Mód | Azt határozza meg, hogyan a lemez szerepel-e a pillanatképek. Válasszon egyet az alábbi lehetőségek közül: <br> -Állandó független: A lemezre írt összes adat végleg van megírva.<br> -Független nem állandó: Amikor kikapcsolásához vagy a virtuális gép alaphelyzetbe állítása a a lemezre írt módosítások elvesznek.  Független nem állandó mód lehetővé teszi, hogy minden esetben indítsa újra a virtuális gép ugyanabban az állapotban. További információkért lásd: a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware dokumentációjában</a>.

7. Miután az ellenőrzés befejeződött, tekintse át a beállításokat, és kattintson a **létrehozás**. Végezze el a módosításokat, kattintson a lap tetején, vagy kattintson.

    ![CloudSimple virtuális gép létrehozása – áttekintése](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>További lépések

* [CloudSimple virtuális gépek listájának megtekintése](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [CloudSimple virtuális gépek kezelése az Azure-ból](https://docs.azure.cloudsimple.com/azure-manage-vm/)
