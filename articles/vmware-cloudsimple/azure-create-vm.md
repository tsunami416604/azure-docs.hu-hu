---
title: Azure VMware-megoldás a CloudSimple-től – Virtuális gép létrehozása az Azure-ban virtuálisgép-sablonokkal
description: Bemutatja, hogyan hozhat létre virtuális gépeket az Azure-ban virtuálisgép-sablonok használatával a VMware-infrastruktúrán a CloudSimple private cloud szolgáltatáshoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244692"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Virtuális gép létrehozása az Azure-ban virtuálisgép-sablonok használatával a VMware-infrastruktúrán

Virtuális gépet hozhat létre az Azure Portalon a Virtuálisgép-sablonok használatával a VMware-infrastruktúrán, amelyet a CloudSimple rendszergazda engedélyezett az előfizetéshez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-cloudsimple-virtual-machine"></a>CloudSimple virtuális gép létrehozása

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple virtuális gépeket.**

3. Kattintson a **Hozzáadás** gombra.

    ![CloudSimple virtuális gép létrehozása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg az alapvető adatokat, kattintson **a Tovább: Méret gombra.**

    > [!NOTE]
    > A CloudSimple virtuálisgép-létrehozása az Azure-ban virtuálisgép-sablont igényel.  Ez a virtuálisgép-sablon a private cloud vCenter-en kell lennie.  Hozzon létre egy virtuális gépet a privát felhőn a vCenter felhasználói felületéről a kívánt operációs rendszerrel és konfigurációkkal.  A [virtuális gép klónozása a vSphere webügyfél sablonjának](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)utasításait használva hozzon létre egy sablont.

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
    | Felhasználónév | A virtuális gép rendszergazdájának felhasználóneve (Windows-sablonokhoz)|
    | Jelszó <br>Jelszó megerősítése | A virtuális gép rendszergazdájának (Windows-sablonokhoz) készült jelszava.  |

5. Válassza ki a virtuális gép magok és memóriakapacitásának számát, majd kattintson a **Tovább: Konfigurációk gombra.** Jelölje be a jelölőnégyzetet, ha a teljes CPU-virtualizációt a vendég operációs rendszernek szeretné elérhetővé tenni, hogy a hardveres virtualizációt igénylő alkalmazások bináris fordítás vagy paravirtualizáció nélkül futtathatók virtuális gépeken. További információt a VMware [VMware hardveres virtualizációval kapcsolatos terméke](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)című cikkben talál.

    ![CloudSimple virtuális gép létrehozása – méret](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurálja a hálózati adaptereket és lemezeket az alábbi táblázatokban leírtak szerint, majd kattintson a **Véleményezés + létrehozás gombra.**

    ![CloudSimple virtuális gép létrehozása - konfigurációk](media/create-cloudsimple-virtual-machine-configurations.png)

    Hálózati csatolók esetén kattintson a **Hálózati adapter hozzáadása** gombra, és adja meg a következő beállításokat.

    | Vezérlés | Leírás |
    | ------------ | ------------- |
    | Név | Adjon meg egy nevet a kapcsolat azonosításához.  |
    | Network (Hálózat) | Válasszon a privát felhőbeli vSphere konfigurált elosztott portcsoportjának listájából.  |
    | Adapter | Válasszon ki egy vSphere adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információt a VMware tudásbáziscikkében [talál, amely hálózati adapter kiválasztása a virtuális géphez.](https://kb.vmware.com/s/article/1001805) |
    | Bekapcsolás a rendszerindításkor | Válassza ki, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

    Lemezek esetén kattintson a **Lemez hozzáadása** gombra, és adja meg a következő beállításokat.

    | Elem | Leírás |
    | ------------ | ------------- |
    | Név | Adjon meg egy nevet a lemez azonosításához.  |
    | Méret | Válasszon egyet a rendelkezésre álló méretek közül.  |
    | SCSI-vezérlő | Jelöljön ki egy SCSI-vezérlőt a lemezhez.  |
    | Mód | Azt határozza meg, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon az alábbi lehetőségek közül: <br> - Független állandó: A lemezre írt összes adat véglegesen meg van írva.<br> - Független, nem állandó: A lemezre írt módosítások elvesznek, amikor kikapcsolja vagy alaphelyzetbe állítja a virtuális gépet.  A független, nem állandó mód lehetővé teszi, hogy mindig ugyanabban az állapotban indítsa újra a virtuális gép. További információt a [VMware dokumentációjában](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)talál.

7. Az ellenőrzés befejezése után tekintse át a beállításokat, és kattintson a **Létrehozás gombra.** A módosítások hozadékához kattintson a lap tetejére, vagy kattintson.

    ![CloudSimple virtuális gép létrehozása - felülvizsgálat](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>CloudSimple virtuális gépek listájának megtekintése

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple virtuális gépeket.**

3. Válassza ki azt a felhőt, amelyen a privát felhő t létrehozta.

    ![A CloudSimple virtuális gépek listája](media/list-cloudsimple-virtual-machines.png)

A CloudSimple virtuális gépek listája tartalmazza az Azure Portalról létrehozott virtuális gépeket.  Virtuális gépek a magánfelhő vCenter a leképezett vCenter erőforráskészlet jelenik meg a listában.  
