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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577720"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Rövid útmutató – az Azure-ban a VMware virtuális gépeket felhasználására

Virtuális gép létrehozása az Azure Portalon, használja az előfizetéshez tartozó virtuálisgép-sablonok, amelyek a CloudSimple rendszergazda engedélyezte. Ezek a Virtuálisgép-sablonok találhatók a VMware-infrastruktúrára.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>CloudSimple virtuális gép létrehozása az Azure-ban van szükség a Virtuálisgép-sablonok

Hozzon létre egy virtuális gépet a magánfelhőben, a vCenter felhasználói felületén. Hozzon létre egy sablont, kövesse a [a vSphere webes ügyfélben a sablon a virtuális gép klónozása](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). A Virtuálisgép-sablon Store a magánfelhő vcenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon

1. A bal oldali menüben kattintson a **+** vagy **erőforrás létrehozása**.

2. A bal oldali menüben kattintson a **számítási**, és kattintson a **CloudSimple virtuális gép**.

3. Kattintson a **megerősítése** , győződjön meg arról, hogy szeretné-e egy új virtuális gép létrehozása.

4. Az alapszintű konfigurációs beállítása az alábbi táblázatban leírtak szerint, és kattintson a **tovább: Méret**.

    | Mező | Leírás |
    | ------------ | ------------- |
    | Előfizetés | Az Azure-előfizetése társítva a magánfelhő üzembe helyezése.  |
    | Erőforráscsoport | Üzembe helyezési csoport, amely a virtuális gép hozzá lesz rendelve. Válasszon egy meglévő csoportot, vagy hozzon létre egy újat. |
    | Name (Név) | A virtuális gépek azonosítására szolgáló név.  |
    | Location egység | Azure-régió, amelyben ez a virtuális gép üzemel.  |
    | Erőforráskészlet | A virtuális gép fizikai erőforrásokat. Válassza ki az elérhető erőforráskészletek. |
    | vSphere Template | Írja be a virtuális gép operációsrendszer-sablon.  |
    | Felhasználónév | A virtuális gép rendszergazdai felhasználóneve. |
    | Jelszó jelszó megerősítése | A virtuális gép rendszergazdai jelszava.  |

5. Válassza ki a magok számától és a virtuális gép memória-kapacitás.

6. (Nem kötelező) Ha szeretné elérhetővé tenni a vendég operációs rendszer teljes CPU-virtualizálási, válassza ki a **tegye elérhetővé a vendég operációs rendszer** jelölőnégyzetet.
Ez a beállítás lehetővé teszi, hogy a bináris fordítási vagy paravirtualizációs nem használó virtuális gépek futtatása a hardvervirtualizálást igénylő alkalmazásokhoz. További információkért olvassa el a VMware [elérhetővé VMware hardver virtualizálásra](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Kattintson a **tovább: Konfigurációs**.

8. Konfigurálja a hálózati adapterek és a lemezek az alábbi táblázatban leírtak szerint.

    Kattintson a hálózati adapterek **Hozzáadás hálózati adapter** és a következő beállításokat.

    | Vezérlés | Leírás |
    | ------------ | ------------- |
    | Name (Név) | Adja meg a kapcsolat nevét.  |
    | Network (Hálózat) | Válassza ki a listából a Magánfelhő vsphere konfigurált hálózatok.  |
    | Adapter | Válassza ki a vSphere-adapter a virtuális Géphez konfigurált elérhető típusok listájából. További információkért olvassa el a VMware Tudásbázis [egy hálózati adaptert a virtuális géphez választása](https://kb.vmware.com/s/article/1001805). |
    | Rendszerindításkor bekapcsolása | Döntse el, hogy a hálózati adapter hardvere engedélyezze, ha a virtuális gép akkor indul el. Az alapértelmezett érték **engedélyezése**. |

    A lemezek, kattintson a **hozzáadás lemez** és a következő beállításokat.

    | Elem | Leírás |
    | ------------ | ------------- |
    | Name (Név) | Adjon meg egy nevet, a lemez azonosításához.  |
    | Méret | Válassza ki az elérhető méretek egyikét.  |
    | SCSI-vezérlő | Válassza ki egy SCSI-vezérlő. A rendelkezésre álló vezérlők eltérőek lehetnek a különböző támogatott operációs rendszerek esetén.  |
    | Mód | Azt határozza meg, hogyan a lemez szerepel-e a pillanatképek. Válasszon egyet az alábbi lehetőségek közül: <br> -Állandó független: A lemezre írt összes adat végleg van megírva.<br> -Független nem állandó: Amikor kikapcsolásához vagy a virtuális gép alaphelyzetbe állítása a a lemezre írt módosítások elvesznek.  Független nem állandó mód lehetővé teszi, hogy minden esetben indítsa újra a virtuális gép ugyanabban az állapotban. További információkért lásd: a [VMware dokumentációjában.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Tekintse át a beállításokat. Végezze el a módosításokat, kattintson a lap tetején.

10. Kattintson a **létrehozás** a beállítások mentéséhez és a virtuális gép létrehozásához.

## <a name="next-steps"></a>További lépések

* [CloudSimple virtuális gépek listájának megtekintése](https://docs.azure.cloudsimple.com/azurelistvms/)
* [CloudSimple virtuális gépek kezelése az Azure-ból](https://docs.azure.cloudsimple.com/azureoverviewpage/)