---
title: Az Azure VMware-megoldás kezelése a CloudSimple private cloud szolgáltatásával
description: A CloudSimple private cloud-erőforrások és -tevékenységek kezeléséhez rendelkezésre álló képességek ismertetése
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869301"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Magánfelhő-erőforrások és -tevékenységek kezelése

A privát felhők kezelése a CloudSimple portálról történik.  Ellenőrizze az állapotot, a rendelkezésre álló erőforrásokat, a magánfelhőben végzett tevékenységeket és a CloudSimple portál egyéb beállításait.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

A [CloudSimple portál](access-cloudsimple-portal.md)elérése.

## <a name="view-the-list-of-private-clouds"></a>Privát felhők listájának megtekintése

Az **Erőforrások** lap **Privát felhők** lapja az előfizetésben lévő összes privát felhőt listázza. Az információ tartalmazza a nevét, a vSphere-fürtök számát, a helyet, a magánfelhő aktuális állapotát és az erőforrás-adatokat.

![Privát felhőlap](media/manage-private-cloud.png)

További információkért és műveletekért válasszon ki egy magánfelhőt.

## <a name="private-cloud-summary"></a>Privát felhő összegzése

Tekintse meg a kiválasztott magánfelhő átfogó összegzését.  Az összefoglaló lap a magánfelhőn telepített DNS-kiszolgálókat tartalmazza.  A DNS-továbbítást az intézményi DNS-kiszolgálókról a magánfelhőDNS-kiszolgálóira állíthatja be.  A DNS-továbbításról a [DNS-továbbításról a DNS konfigurálása a helyszíni magánfelhő-vCenter névfeloldása című témakörben talál.](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/)

![Privát felhő összegzése](media/private-cloud-summary.png)

### <a name="available-actions"></a>Elérhető műveletek

* [Indítsd el a vSphere klienst.](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access) A private cloud virtuális központhoz való hozzáférés.
* [Beszerzési csomópontok](create-nodes.md). Adja hozzá a csomópontokat ehhez a magánfelhőhöz.
* [Bontsa ki a csomópontot.](expand-private-cloud.md) Adja hozzá a csomópontokat ehhez a magánfelhőhöz.
* **Frissítés**. Frissítse az ezen az oldalon található információkat.
* **Törölje**a gombot. A magánfelhőt bármikor törölheti. **A törlés előtt győződjön meg arról, hogy minden rendszerről és adatról biztonsági másolatot tett.** A magánfelhő törlése törli az összes virtuális gépet, vCenter-konfigurációt és adatot. Kattintson a **Törlés gombra** a kijelölt magánfelhő összefoglaló szakaszában. A törlést követően az összes privát felhőbeli adat törlődik egy biztonságos, rendkívül megfelelő törlési folyamat során.
* [Változás vSphere jogosultságok](escalate-private-cloud-privileges.md).  Bővítse jogosultságait ezen a magánfelhőn.

## <a name="private-cloud-vlanssubnets"></a>Privát felhőalapú VLANS/alhálózatok

A kijelölt magánfelhőhöz definiált VLAN-ok/alhálózatok listájának megtekintése.  A lista tartalmazza a felügyeleti VLAN-ok/alhálózatok létrehozásakor létrehozott magánfelhő.

![Privát felhő - VLAN-ok/Alhálózatok](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Elérhető műveletek

* [VLANS/Alhálózatok hozzáadása](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/). VlAN/részhalmaz hozzáadása ehhez a privát felhőhöz.

VLAN/alhálózat kiválasztása a következő műveletekhez
* [Tűzfaltábla csatolása](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/). Tűzfaltábla csatolása ehhez a magánfelhőhöz.
* **Szerkesztés**
* **Törlés** (csak a felhasználó által definiált VLAN-ok/alhálózatok)

## <a name="private-cloud-activity"></a>Privát felhőtevékenység

Tekintse meg a következő információkat a kiválasztott private cloud.  A tevékenységinformáció a kiválasztott magánfelhő összes tevékenységének szűrt listája.  Ezen az oldalon legfeljebb 25 legutóbbi tevékenység látható.

* Legutóbbi riasztások
* Legutóbbi események
* Legutóbbi feladatok
* Legutóbbi ellenőrzés

![Privát felhő - Tevékenység](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Felhőállványok

A felhőállványok a privát felhő építőkövei. Minden állvány egy egységnyi kapacitást biztosít. A CloudSimple automatikusan konfigurálja a felhőállványokat a beállítások alapján, amikor privát felhőt hoz létre vagy bővít ki.  Tekintse meg a felhőállványok teljes listáját, beleértve a magánfelhőt is, amelyhez mindegyik hozzá van rendelve.

![Privát felhő - Felhőállványok](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere-felügyeleti hálózat

A Virtuálisgép-szolgáltatások felügyeleti erőforrásainak és a magánfelhőben jelenleg konfigurált virtuális gépek listája. Az információ tartalmazza a szoftver verzióját, a teljesen minősített tartománynevet (FQDN) és az erőforrások IP-címét.

![Privát felhő - vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)