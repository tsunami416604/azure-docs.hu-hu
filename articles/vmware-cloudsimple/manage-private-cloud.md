---
title: Azure VMware-megoldás által CloudSimple Magánfelhő kezelése
description: CloudSimple magánjellegű felhő erőforrásait és tevékenységek kezeléséhez elérhető lehetőségeket ismerteti
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333243"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Magánfelhő-erőforrások és tevékenységek kezelése

Magánfelhők CloudSimple portálon kezelhetők.  Ellenőrizze a állapotát, a rendelkezésre álló erőforrások, a tevékenység a magánfelhőhöz és az egyéb beállításokat a CloudSimple portálról.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Hozzáférés a [CloudSimple portál](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>A privát felhők listájának megtekintése

A **Magánfelhők** lapján a **erőforrások** lap felsorolja az összes privát felhő az előfizetésében. Nevét, a vSphere száma fürtök, hely, a privát felhő és az erőforrás információkat aktuális állapotát.

![Magánfelhő lap](media/manage-private-cloud.png)

Válassza ki a Private Cloud, műveleteket és további információkat.

## <a name="private-cloud-summary"></a>Privát felhő összegzése

A kijelölt privát felhő átfogó összegzésének megtekintése.  Összefoglaló lap tartalmazza a DNS-kiszolgálók a privát felhőben üzembe helyezett.  DNS-t beállíthatja, továbbítja a helyi DNS-kiszolgálók a privát felhő DNS-kiszolgálók.  A DNS-továbbítást további információkért lásd: [DNS konfigurálása a helyszíni privát felhő vCenter névfeloldásra](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Privát felhő összegzése](media/private-cloud-summary.png)

### <a name="available-actions"></a>Rendelkezésre álló műveletek

* [Indítsa el a vSphere client](https://docs.azure.cloudsimple.com/vsphere-access/). Hozzáférés a vcenter-kiszolgáló a Magánfelhő számára.
* [Csomópontok beszerzési](create-nodes.md). Csomópontok hozzáadása a Magánfelhő.
* [Bontsa ki a](expand-private-cloud.md). Csomópontok hozzáadása a Magánfelhő.
* **Frissítés**. Frissítse a információkat ezen az oldalon.
* **Törlés**. A Magánfelhő bármikor törölheti. **Mielőtt törölné, győződjön meg arról, hogy biztonsági másolatot az összes rendszerekkel és adatokkal.** Magánfelhő törlése törli a virtuális gépek, vCenter konfigurációs és adatokat. Kattintson a **törlése** a kiválasztott Magánfelhő összefoglaló részben. Törlés, a következő összes Magánfelhő adatot egy biztonságos, erősen kompatibilis a Törlés folyamatban törölve lesz.
* [A vSphere-jogosultságok módosítása](escalate-private-cloud-privileges.md).  Eszkalálni a jogosultságokat a privát felhőben.

## <a name="private-cloud-vlanssubnets"></a>Privát felhő VLAN-OK/alhálózatok

A kiválasztott Magánfelhő meghatározott VLAN-OK/alhálózatok listájának megtekintéséhez.  A lista tartalmazza a VLAN-OK/alhálózatok a magánfelhő létrehozásakor létrehozott felügyeleti.

![Magánfelhő - VLAN-OK/alhálózatok](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Rendelkezésre álló műveletek

* [VLAN-OK/alhálózatok](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adja hozzá a Magánfelhő egy VLAN/részére.

Válassza ki a következő műveletek egy VLAN/alhálózat
* [Tűzfal táblázat csatolása](https://docs.azure.cloudsimple.com/firewall/). A Magánfelhő csatolása egy tűzfal táblát.
* **Szerkesztése**
* **Törlés** (csak felhasználó által megadott VLAN-OK/alhálózatok)

## <a name="private-cloud-activity"></a>Magánfelhő-tevékenység

Megtekintheti a kiválasztott Magánfelhő az alábbi adatokat.  Az ügyféltevékenységgel kapcsolatos információk az összes tevékenység a kiválasztott privát felhő szűrt listája.  Ezen a lapon látható legutóbbi tevékenységek legfeljebb 25.

* Legutóbbi riasztások
* Legutóbbi események
* Legutóbbi tevékenységek
* Legutóbbi naplózása

![Magánfelhő - tevékenység](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Felhőbeli állványt

Felhőalapú állványokon, a Magánfelhő építőkövei. Minden állványban egy kapacitási egység, biztosít. CloudSimple automatikusan konfigurálja a felhő állványt létrehozásakor vagy Magánfelhő bővülő alapján.  Felhőbeli állványt teljes listájának megtekintéséhez, beleértve a Magánfelhő, hogy minden hozzá van rendelve.

![Magánfelhő - felhő állványt](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

VMware-kezelési erőforrásainak és virtuális gépek, amelyeken jelenleg a privát felhő listája. Információ a szoftververzió, teljesen minősített tartománynevét (FQDN) és az erőforrások IP-címét tartalmazza.

![Magánfelhő - vSphere felügyeleti hálózat](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>További lépések

* [Az Azure-ban a VMware virtuális gépeket felhasználására](quickstart-create-vmware-virtual-machine.md)
* Tudjon meg többet [Magánfelhők létrehozása](cloudsimple-private-cloud.md)