---
title: VMware HCX hálózati szegmensek
description: A VMware HCX-hez négy hálózat szükséges.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583300"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

A VMware HCX négy hálózat szükséges:

- **Felügyeleti hálózat:** Általában ugyanaz a felügyeleti hálózat, amelyet a vSphere-fürt használ.  Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware-HCX (a telepítéstől függően nagyobb számokra lehet szükség).

- **VMotion hálózat:** Általában ugyanazt a hálózatot használják a vMotion a vSphere-fürtön.  Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware-HCX (a telepítéstől függően nagyobb számokra lehet szükség).  

   A vMotion-hálózatnak elérhetőnek kell lennie egy elosztott virtuális kapcsolón vagy vSwitch0. Ha nem, módosítsa a környezetet.

   > [!NOTE]
   > Ha a hálózat nincs átirányítva (privát), az OK.

- **Kimenő hálózat:** Létre kell hoznia egy új hálózatot a VMware HCX kimenő hálózathoz, és ki kell terjesztenie a vSphere-fürtre egy porttartomány használatával.  Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware-HCX (a telepítéstől függően nagyobb számokra lehet szükség).  

   > [!NOTE]
   > Az ajánlott módszer egy/29 hálózat létrehozása, de a hálózati méret is megtörténik.

- **Replikációs hálózat:** Hozzon létre egy új hálózatot a VMware HCX-replikációhoz, és terjessze ki a hálózatot a vSphere-fürtön egy porttartomány használatával.  Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware-HCX (a telepítéstől függően nagyobb számokra lehet szükség).

   > [!NOTE]
   > Az ajánlott módszer egy/29 hálózat létrehozása, de a hálózati méret is megtörténik.