---
title: Azure VMware-megoldások (AVS) saját felhő kezelése
description: Ismerteti az AVS saját Felhőbeli erőforrásainak és tevékenységének kezeléséhez rendelkezésre álló képességeket
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014827"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Az AVS Private Cloud-erőforrások és-tevékenységek kezelése

Az AVS privát felhők kezelése az AVS-portálról történik. Keresse meg az állapotot, a rendelkezésre álló erőforrásokat, az AVS Private Cloud tevékenységeit és az AVS portál egyéb beállításait.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-avs-portal"></a>Hozzáférés az AVS-portálhoz

Hozzáférés az [AVS-portálhoz](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Az AVS privát felhők listájának megtekintése

Az **erőforrások** lap **AVS Private felhők** lapja felsorolja az előfizetésben található összes AVS Private-felhőt. Az adatok közé tartozik a név, a vSphere-fürtök száma, a hely, az AVS-beli saját felhő aktuális állapota és az erőforrás-információ.

![AVS Private Cloud oldal](media/manage-private-cloud.png)

További információért és műveletekhez válasszon egy AVS Private-felhőt.

## <a name="avs-private-cloud-summary"></a>AVS Private Cloud Összefoglaló

Tekintse meg a kiválasztott AVS privát felhő átfogó összegzését. Az összefoglalás lapon az AVS Private Cloud-on üzembe helyezett DNS-kiszolgálók szerepelnek. A DNS-továbbítást beállíthatja a helyszíni DNS-kiszolgálókról az AVS Private Cloud DNS-kiszolgálókra. A DNS-továbbítással kapcsolatos további információkért lásd: [DNS beállítása névfeloldáshoz az AVS Private Cloud vCenter a helyszínen](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![AVS Private Cloud Összefoglaló](media/private-cloud-summary.png)

### <a name="available-actions"></a>Elérhető műveletek

* [Indítsa el a vSphere-ügyfelet](https://docs.azure.cloudsimple.com/vsphere-access/). Az AVS Private Cloud vCenter elérése.
* [Megvásárlási csomópontok](create-nodes.md). Csomópontok hozzáadása ehhez az AVS privát felhőhöz.
* [Bontsa ki](expand-private-cloud.md)a elemet. Csomópontok hozzáadása ehhez az AVS privát felhőhöz.
* **Frissítés**. Az oldalon található információk frissítése.
* **Törlés**. Bármikor törölheti az AVS Private-felhőt. **A törlés előtt győződjön meg róla, hogy minden rendszerről és adattal készített biztonsági másolatot.** Az AVS Private Cloud törlése törli az összes virtuális gépet, a vCenter-konfigurációt és az adatkészletet. Kattintson a **Törlés** elemre a kiválasztott AVS Private Cloud (összefoglalás) szakaszban. A törlést követően az összes AVS Private Cloud-adat törlődik egy biztonságos, szigorúan kompatibilis törlési folyamatba.
* [VSphere-jogosultságok módosítása](escalate-private-cloud-privileges.md) Adja meg a jogosultságait ezen az AVS Private-felhőben.

## <a name="avs-private-cloud-vlanssubnets"></a>AVS Private Cloud VLAN-ok/alhálózatok

Megtekintheti a megadott VLAN-ok/alhálózatok listáját a kiválasztott AVS privát felhőhöz. A lista tartalmazza az AVS Private Cloud létrehozásakor létrehozott felügyeleti VLAN-okat/alhálózatokat.

![AVS Private Cloud – VLAN-ok/alhálózatok](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Elérhető műveletek

* [VLAN-ok/alhálózatok hozzáadása](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adjon hozzá egy VLAN/részhalmazt ehhez az AVS privát felhőhöz.

Válasszon VLAN-t/alhálózatot a következő műveletekhez
* [Tűzfalszabály csatolása](https://docs.azure.cloudsimple.com/firewall/). Csatoljon egy tűzfal-táblázatot ehhez az AVS Private Cloudhoz.
* **Szerkesztése**
* **Törlés** (csak a felhasználó által definiált VLAN-ok/alhálózatok)

## <a name="avs-private-cloud-activity"></a>AVS Private Cloud-tevékenység

Tekintse meg a következő információkat a kiválasztott AVS privát felhőhöz. A tevékenység adatai a kiválasztott AVS privát felhő összes tevékenységének szűrt listája. Ezen az oldalon legfeljebb 25 legutóbbi tevékenység látható.

* Legutóbbi riasztások
* Legutóbbi események
* Legutóbbi feladatok
* Legutóbbi naplózás

![AVS Private Cloud – tevékenység](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Felhőalapú állványok

A Cloud racks az AVS Private Cloud építőelemei. Mindegyik rack kapacitási egységet biztosít. Az AVS a saját beállítások alapján automatikusan beállítja a Felhőbeli állványokat, amikor létrehoz vagy kibővít egy AVS Private-felhőt. Megtekintheti a felhőalapú állványok teljes listáját, beleértve az összes hozzá rendelt AVS Private Cloud-t.

![AVS Private Cloud – felhőalapú állványok](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere felügyeleti hálózat

Az AVS Private Cloud-on jelenleg konfigurált VMware felügyeleti erőforrások és virtuális gépek listája. Az információk közé tartozik a szoftver verziója, a teljes tartománynév (FQDN) és az erőforrások IP-címe.

![AVS Private Cloud-vSphere felügyeleti hálózat](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Következő lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ az [AVS Private felhőkről](cloudsimple-private-cloud.md)