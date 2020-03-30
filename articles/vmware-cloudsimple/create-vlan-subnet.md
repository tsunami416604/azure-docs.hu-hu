---
title: VLAN-ok/alhálózatok létrehozása – Azure VMware-megoldás a CloudSimple-től
description: Azure VMware-megoldások a CloudSimple által – Bemutatja, hogyan hozhat létre és kezelhet VLAN-okat/alhálózatokat a privát felhőkhöz, majd hogyan alkalmazhat tűzfalszabályokat.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565995"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>VLAN-ok/alhálózatok létrehozása és kezelése a privát felhők számára

Nyissa meg a VLAN-ok/Alhálózatok lapot a Hálózat lapon a privát felhők VLAN-jainak/alhálózatainak létrehozásához és kezeléséhez. A VLAN/alhálózat létrehozása után tűzfalszabályokat alkalmazhat.

## <a name="create-a-vlansubnet"></a>VLAN/alhálózat létrehozása

1. Az oldalsó menüben [elérheti a CloudSimple portált,](access-cloudsimple-portal.md) és válassza a **Hálózat** lehetőséget.
2. Válassza a **VLAN-ok/alhálózatok lehetőséget.**
3. Kattintson **a VLAN/Alhálózat létrehozása gombra.**

    ![VLAN/alhálózati lap](media/vlan-subnet-page.png)

4. Válassza ki a privát felhő az új VLAN/alhálózat.
5. Adjon meg egy VLAN-azonosítót.
6. Adja meg az alhálózat nevét.
7. Ha engedélyezni szeretné az útválasztást a VLAN-on (alhálózaton), adja meg az alhálózati CIDR-tartományt. Győződjön meg arról, hogy a CIDR-tartomány nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy az átjáró alhálózat.
8. Kattintson a **Küldés gombra.**

    ![VLAN/alhálózat létrehozása](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Privát felhőnként 30 VLAN kvótát kell kisajátítása. Ezek a korlátok növelhetők a [támogatási szolgálathoz való felvételsel.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Vlan-adatok használata elosztott portcsoport beállításához a vSphere-ben

Ha elosztott portcsoportot szeretne létrehozni a vSphere-ben, kövesse a vSphere hálózati útmutató "Elosztott portcsoport hozzáadása" című VMware-témakörének <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">utasításait.</a> Az elosztott portcsoport beállításakor adja meg a VLAN-adatokat a CloudSimple konfigurációból.

![Elosztott portcsoport](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Tűzfaltábla kijelölése

A tűzfaltáblák és a kapcsolódó szabályok a **Hálózati > tűzfal táblák** lapon vannak definiálva. A magánfelhő VLAN-ra/alhálózatára alkalmazandó tűzfaltábla kiválasztásához jelölje ki a VLAN/alhálózat lehetőséget a **VLAN-ok/alhálózatok** lapon a **Tűzfaltábla-melléklet** elemre. A tűzfaltáblák beállítására és a szabályok meghatározására vonatkozó utasításokat a [tűzfaltáblák](firewall.md) beállítására vonatkozó anamnézisben találja.

![Tűzfaltábla-hivatkozás](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Az alhálózat egy tűzfaltáblához társítható. A tűzfaltábla több alhálózathoz is társítható.

## <a name="edit-a-vlansubnet"></a>VLAN/alhálózat szerkesztése

VlAN/Alhálózat beállításainak szerkesztéséhez jelölje ki azt a **VLAN-ok/Alhálózatok** lapon, és kattintson a **Szerkesztés** ikonra. Módosítsa a módosításokat, és kattintson **a Submet gombra.**

## <a name="delete-a-vlansubnet"></a>VLAN/alhálózat törlése

Vlan/alhálózat törléséhez jelölje ki azt a **VLAN-ok/Alhálózatok** lapon, és kattintson a **Törlés** ikonra. A **megerősítéshez** kattintson a Törlés gombra.
