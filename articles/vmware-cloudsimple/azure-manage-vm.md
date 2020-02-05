---
title: Azure VMware Solutions (AVS) – AVS Private Cloud VM-EK kezelése az Azure-ban
description: Ismerteti, hogyan kezelhető az AVS Private Cloud VM a Azure Portalban, beleértve a lemezek hozzáadását, a virtuális gépek kapacitásának módosítását és a hálózati adapterek hozzáadását.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0cce1dc7ff3935a3174d4e96b553a5485950df73
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014997"
---
# <a name="manage-your-avs-private-cloud-virtual-machines-in-azure"></a>Az AVS Private Cloud Virtual Machines kezelése az Azure-ban

Az [AVS Private Cloud](azure-create-vm.md)-hoz létrehozott virtuális gépek kezeléséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com). Keresse meg és válassza ki a virtuális (Keresés az **összes szolgáltatás** vagy **Virtual Machines** oldalon a helyi menüben).

## <a name="control-virtual-machine-operation"></a>Virtuális gép műveletének vezérlése

A kiválasztott virtuális gép **Áttekintés** lapján a következő vezérlők érhetők el.

| Vezérlés | Leírás |
| ------------ | ------------- |
| Kapcsolódás | Kapcsolódjon a megadott virtuális géphez.  |
| Kezdés | Indítsa el a megadott virtuális gépet.  |
| Újraindítás | Állítsa le, majd kapcsolja be a megadott virtuális gépet.  |
| Leállítás | Állítsa le az adott virtuális gépet.  |
| Rögzítés | Rögzítsen egy rendszerképet a megadott virtuális gépről, hogy más virtuális gépek létrehozásához képként is használható legyen. Lásd: [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](../virtual-machines/windows/classic/capture-image.md).   |
| Áthelyezés | Ugrás a megadott virtuális gépre.  |
| Törlés | Távolítsa el a megadott virtuális gépet.  |
| Frissítés | A kijelzőben lévő Adatfrissítés.  |

### <a name="view-performance-information"></a>Teljesítményadatok megtekintése

Az **áttekintő** oldal alsó területén lévő diagramok a kiválasztott intervallumhoz tartozó teljesítményadatokat jelenítik meg (az utolsó órában az elmúlt 30 napban; az alapértelmezett érték az utolsó óra). Az egyes diagramokon az intervallumon belül bármikor megjelenítheti a numerikus értékeket úgy, hogy az egérmutatót a diagramon oda-vissza mozgatja.

A következő diagramok jelennek meg.

| Tétel | Leírás |
| ------------ | ------------- |
| CPU (átlag) | A CPU átlagos kihasználtsága a kiválasztott intervallum százalékában kifejezve.   |
| Network (Hálózat) | A hálózat (MB) bejövő és kimenő forgalma a kiválasztott intervallumban.  |
| Lemez bájtjai | A lemezről beolvasott és lemezre (MB) írt összes adat a kiválasztott intervallumon belül.  |
| Lemezműveletek | A lemezes műveletek átlagos sebessége (művelet/másodperc) a kijelölt intervallumban. |

## <a name="manage-vm-disks"></a>VM-lemezek kezelése

VM-lemez hozzáadásához nyissa meg a kiválasztott virtuális gép **lemezek** lapját. Lemez hozzáadásához kattintson a **lemez hozzáadása**elemre. Konfigurálja az alábbi beállításokat egy beágyazott lehetőség beírásával vagy kiválasztásával. Kattintson a **Mentés** gombra.

   | Tétel | Leírás |
   | ------------ | ------------- |
   | Name (Név) | Adjon meg egy nevet a lemez azonosításához.  |
   | Méret | Válasszon ki egy rendelkezésre álló méretet.  |
   | SCSI-vezérlő | Válasszon ki egy SCSI-vezérlőt. A rendelkezésre álló vezérlők eltérőek lehetnek a különböző támogatott operációs rendszereken.  |
   | Mód | Meghatározza, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon egyet a következő lehetőségek közül: <br> – Független állandó: a lemezre írt összes adattal véglegesen írásra kerül.<br> -Független, nem állandó: a lemezre írt módosításokat a rendszer a virtuális gép kikapcsolásakor vagy alaphelyzetbe állításakor elveti. Ez a mód lehetővé teszi, hogy mindig ugyanabban az állapotban indítsa újra a virtuális gépet. További információt a [VMware dokumentációjában](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)talál. |

Lemez törléséhez jelölje ki azt, és kattintson a **Törlés**gombra.

## <a name="change-the-capacity-of-the-vm"></a>A virtuális gép kapacitásának módosítása

A virtuális gép kapacitásának módosításához nyissa meg a kiválasztott virtuális gép **méret** lapját. Határozza meg a következők bármelyikét, majd kattintson a **Mentés**gombra.

| Tétel | Leírás |
| ------------ | ------------- |
| Magok száma | A virtuális géphez rendelt magok száma.  |
| Hardveres virtualizálás | Jelölje be a jelölőnégyzetet, hogy a hardveres virtualizálás elérhető legyen a vendég operációs rendszeren. Tekintse meg a [VMware-hardveres virtualizációs szolgáltatást támogató](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)VMware-cikket. |
| Memória mérete | Adja meg a virtuális gép számára lefoglalható memória mennyiségét.  

## <a name="manage-network-interfaces"></a>Hálózati adapterek kezelése

Illesztőfelület hozzáadásához kattintson a **hálózati adapter hozzáadása**lehetőségre. Konfigurálja az alábbi beállításokat egy beágyazott lehetőség beírásával vagy kiválasztásával. Kattintson a **Mentés** gombra.

   | Vezérlés | Leírás |
   | ------------ | ------------- |
   | Name (Név) | Adja meg a felületet azonosító nevet.  |
   | Network (Hálózat) | Válassza ki a konfigurált hálózatok listáját az AVS Private Cloud vSphere.  |
   | Adapter | Válasszon ki egy vSphere-adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információkért lásd a VMware tudásbázist a [virtuális gép hálózati adapterének kiválasztásával](https://kb.vmware.com/s/article/1001805)foglalkozó cikkben. |
   | Bekapcsolás rendszerindításkor | Adja meg, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

Hálózati adapter törléséhez jelölje ki azt, és kattintson a **Törlés**gombra.
