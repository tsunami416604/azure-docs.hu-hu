---
title: 'Rövid útmutató: VMware virtuális gépek fogyasztása az Azure-ban'
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerje meg, hogyan konfigurálhatja és használhatja a VMware virtuális gépeket az Azure Portalról a CloudSimple Azure VMware-megoldása segítségével
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019553"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Rövid útmutató – VMware virtuális gépek fogyasztása az Azure-ban

Virtuális gép létrehozásához az Azure Portalon, használja a virtuális gép sablonokat, amelyek a CloudSimple rendszergazda engedélyezte az előfizetéshez. A virtuálisgép-sablonok a VMware-infrastruktúrában találhatók.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>A CloudSimple virtuálisgép-létrehozáshoz az Azure-ban virtuálisgép-sablonszükséges

Hozzon létre egy virtuális gépet a privát felhőben a vCenter felhasználói felületéről. Sablon létrehozásához kövesse a [Virtuális gép klónozása egy sablonhoz a vSphere webügyfélben](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)található utasításokat. A virtuális gép sablon tanonc a private cloud vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple virtuális gépeket.**

3. Kattintson a **Hozzáadás** gombra.

    ![CloudSimple virtuális gép létrehozása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg az alapvető adatokat, és kattintson **a Tovább: Méret gombra.**

    ![CloudSimple virtuális gép létrehozása – alapok](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Mező | Leírás |
    | ------------ | ------------- |
    | Előfizetés | A privát felhőhöz társított Azure-előfizetés.  |
    | Erőforráscsoport | Erőforrás-csoport, amelyhez a virtuális gép lesz rendelve. Kiválaszthat egy meglévő csoportot, vagy létrehozhat egy újat. |
    | Név | A virtuális gép azonosítására szolgáló név.  |
    | Hely | Az Azure-régió, amelyben ez a virtuális gép található.  |
    | Magánfelhő | CloudSimple private cloud, ahol létre szeretné hozni a virtuális gépet. |
    | Erőforráskészlet | A virtuális gép leképezett erőforráskészlet. Válasszon a rendelkezésre álló erőforráskészletek közül. |
    | vSphere sablon | vSphere sablon a virtuális géphez.  |
    | Felhasználónév | A virtuális gép rendszergazdájának felhasználóneve (Windows-sablonokhoz).|
    | Jelszó |  A virtuális gép rendszergazdájának (Windows-sablonokhoz) készült jelszava. |
    | Jelszó megerősítése | Erősítse meg a jelszót. |

5. Válassza ki a virtuális gép magok és memóriakapacitásának számát, majd kattintson a **Tovább: Konfigurációk gombra.** Jelölje be a jelölőnégyzetet, ha a teljes CPU-virtualizációt a vendég operációs rendszernek szeretné elérhetővé tenni. A hardveres virtualizációt igénylő alkalmazások bináris fordítás vagy paravirtualizáció nélkül futtathatók virtuális gépeken. További információt a VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware hardveres virtualizációval kapcsolatos terméke</a>című cikkben talál.

    ![CloudSimple virtuális gép létrehozása – méret](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurálja a hálózati adaptereket és lemezeket az alábbi táblázatokban leírtak szerint, majd kattintson a **Véleményezés + létrehozás gombra.**

    ![CloudSimple virtuális gép létrehozása - konfigurációk](media/create-cloudsimple-virtual-machine-configurations.png)

    Hálózati csatolók esetén kattintson a **Hálózati adapter hozzáadása** gombra, és adja meg a következő beállításokat.

    | Vezérlés | Leírás |
    | ------------ | ------------- |
    | Név | Adjon meg egy nevet a kapcsolat azonosításához.  |
    | Network (Hálózat) | Válasszon a privát felhőbeli vSphere konfigurált elosztott portcsoportjának listájából.  |
    | Adapter | Válasszon ki egy vSphere adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információt a VMware tudásbáziscikkében <a href="https://kb.vmware.com/s/article/1001805" target="_blank">talál, amely hálózati adapter kiválasztása a virtuális géphez.</a> |
    | Bekapcsolás a rendszerindításkor | Válassza ki, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

    Lemezek esetén kattintson a **Lemez hozzáadása** gombra, és adja meg a következő beállításokat.

    | Elem | Leírás |
    | ------------ | ------------- |
    | Név | Adjon meg egy nevet a lemez azonosításához.  |
    | Méret | Válasszon egyet a rendelkezésre álló méretek közül.  |
    | SCSI-vezérlő | Jelöljön ki egy SCSI-vezérlőt a lemezhez.  |
    | Mód | Azt határozza meg, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon az alábbi lehetőségek közül: <br> - Független állandó: A lemezre írt összes adat véglegesen meg van írva.<br> - Független, nem állandó: A lemezre írt módosítások elvesznek, amikor kikapcsolja vagy alaphelyzetbe állítja a virtuális gépet.  A független, nem állandó mód lehetővé teszi, hogy mindig ugyanabban az állapotban indítsa újra a virtuális gép. További információt a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware dokumentációjában</a>talál.

7. Az ellenőrzés befejezése után tekintse át a beállításokat, és kattintson a **Létrehozás gombra.** A módosítások hozadékához kattintson a lap tetejére, vagy kattintson.

    ![CloudSimple virtuális gép létrehozása - felülvizsgálat](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>További lépések

* [CloudSimple virtuális gépek listájának megtekintése](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [A CloudSimple virtuális gép kezelése az Azure-ból](azure-manage-vm.md)
