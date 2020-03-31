---
title: Azure VMware-megoldás a CloudSimple-től – Privát felhőbeli virtuális gépek kezelése az Azure-ban
description: A CloudSimple privát felhőbeli virtuális gépei kezelése az Azure Portalon, beleértve a lemezek hozzáadását, a virtuális gép kapacitásának módosítását és a hálózati csatolók hozzáadását
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014997"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>A CloudSimple private cloud virtuális gépei kezelése az Azure-ban

A [CloudSimple private cloud hoz létre](azure-create-vm.md)a virtuális gépek kezeléséhez jelentkezzen be az Azure [Portalra.](https://portal.azure.com) Keresse meg és válassza ki a virtuális (keresés az **összes szolgáltatás** vagy **virtuális gép** az oldalsó menüben).

## <a name="control-virtual-machine-operation"></a>A virtuális gép működésének vezérlése

A következő vezérlők a kijelölt virtuális gép **Áttekintés lapján** érhetők el.

| Vezérlés | Leírás |
| ------------ | ------------- |
| Kapcsolódás | Csatlakozzon a megadott virtuális géphez.  |
| Indítás | Indítsa el a megadott virtuális gép.  |
| Újraindítás | Állítsa le, majd kapcsolja be a megadott virtuális gép.  |
| Leállítás | Állítsa le az adott virtuális gép.  |
| Rögzítés | Készítsen egy lemezképet a megadott virtuális gépről, hogy más virtuális gépek létrehozásához is használható legyen. Lásd: [Általános virtuális gép felügyelt lemezképének létrehozása az Azure-ban című témakört.](../virtual-machines/windows/classic/capture-image.md)   |
| Áthelyezés | Ugrás a megadott virtuális gépre.  |
| Törlés | Távolítsa el a megadott virtuális gép.  |
| Frissítés | Frissítse a kijelzőn lévő adatokat.  |

### <a name="view-performance-information"></a>Teljesítményadatok megtekintése

**Az Áttekintés** lap alsó részén lévő diagramok a kiválasztott intervallum teljesítményadatait (utolsó órától 30 napig, az alapértelmezett az utolsó órában) jelennek meg. Az egyes diagramokon belül a kurzort oda-vissza mozgatva jelenítheti meg az intervallumon belül bármikor.

A következő diagramok jelennek meg.

| Elem | Leírás |
| ------------ | ------------- |
| CPU (átlag) | A CPU átlagos kihasználtsága százalékban a kiválasztott intervallumhoz.   |
| Network (Hálózat) | A hálózatba (MB) be- és kivezető forgalom a kiválasztott időközön keresztül.  |
| Lemezbájtjai | A lemezről beolvasott és lemezre (MB) írt összes adat a kiválasztott időközön keresztül.  |
| Lemezműveletek | A lemezműveletek átlagos sebessége (műveletek/másodperc) a kiválasztott intervallumhoz hasonlóan. |

## <a name="manage-vm-disks"></a>Virtuálisgép-lemezek kezelése

Virtuálisgép-lemez hozzáadásához nyissa meg a kijelölt virtuális gép **Lemezlapja.** Lemez hozzáadásához kattintson a **Lemez hozzáadása**gombra. Konfigurálja az alábbi beállításokat egy szövegközi beállítás megadásával vagy kiválasztásával. Kattintson a **Mentés** gombra.

   | Elem | Leírás |
   | ------------ | ------------- |
   | Név | Adjon meg egy nevet a lemez azonosításához.  |
   | Méret | Válasszon egyet a rendelkezésre álló méretek közül.  |
   | SCSI-vezérlő | Jelöljön ki egy SCSI-vezérlőt. A rendelkezésre álló vezérlők a különböző támogatott operációs rendszerektől függően változnak.  |
   | Mód | Azt határozza meg, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon az alábbi lehetőségek közül: <br> - Független állandó: A lemezre írt összes adat véglegesen meg van írva.<br> - Független, nem állandó: A lemezre írt módosítások elvesznek, amikor kikapcsolja vagy alaphelyzetbe állítja a virtuális gépet.  Ez a mód lehetővé teszi, hogy mindig indítsa újra a virtuális gép ugyanabban az állapotban. További információt a [VMware dokumentációjában](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)talál. |

Lemez törléséhez jelölje ki, és kattintson a **Törlés gombra.**

## <a name="change-the-capacity-of-the-vm"></a>A virtuális gép kapacitásának módosítása

A virtuális gép kapacitásának módosításához nyissa meg a kijelölt virtuális gép **Méret** lapját. Adja meg az alábbi a következők valamelyikét, majd kattintson a **Mentés gombra.**

| Elem | Leírás |
| ------------ | ------------- |
| Magok száma | A virtuális géphez rendelt magok száma.  |
| Hardveres virtualizáció | Jelölje be a jelölőnégyzetet, ha a hardveres virtualizációt a vendég operációs rendszer számára szeretné elérhetővé tenni. Lásd a VMware [cikkexpose VMware hardveres virtualizáció .](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html) |
| Memória mérete | Válassza ki a virtuális gépszámára lefoglalandó memória mennyiségét.  

## <a name="manage-network-interfaces"></a>Hálózati adapterek kezelése

Kapcsolat hozzáadásához kattintson a **Hálózati adapter hozzáadása gombra.** Konfigurálja az alábbi beállításokmindegyikét egy szövegközi beállítás megadásával vagy beállításával. Kattintson a **Mentés** gombra.

   | Vezérlés | Leírás |
   | ------------ | ------------- |
   | Név | Adjon meg egy nevet a kapcsolat azonosításához.  |
   | Network (Hálózat) | Válasszon a privát felhőbeli vSphere konfigurált hálózatainak listájából.  |
   | Adapter | Válasszon ki egy vSphere adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információt a VMware tudásbáziscikkében [talál, amely hálózati adapter kiválasztása a virtuális géphez.](https://kb.vmware.com/s/article/1001805) |
   | Bekapcsolás a rendszerindításkor | Válassza ki, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

Hálózati adapter törléséhez jelölje ki, és kattintson a **Törlés gombra.**
