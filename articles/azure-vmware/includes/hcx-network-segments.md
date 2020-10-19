---
title: VMware HCX hálózati szegmensek
description: A VMware HCX-hez négy hálózat szükséges.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173615"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

A VMware HCX négy hálózatra van szükség:

- **Felügyeleti hálózat:** Általában ugyanaz a felügyeleti hálózat, amelyet a vSphere-fürt használ. Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. (A telepítéstől függően nagyobb számra lehet szükség.)

- **vMotion hálózat:** Általában ugyanazt a hálózatot használják a vMotion a vSphere-fürtön.  Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. (A telepítéstől függően nagyobb számra lehet szükség.)  

   A vMotion-hálózatnak elérhetőnek kell lennie egy elosztott virtuális kapcsolón vagy vSwitch0. Ha nem, módosítsa a környezetet.

   > [!NOTE]
   > Ha a hálózat nincs átirányítva (privát), az OK.

- **Kimenő hálózat:** Létre kell hoznia egy új hálózatot a VMware HCX kimenő hálózathoz, és ki kell terjesztenie a vSphere-fürtre egy porttartomány használatával. Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. (A telepítéstől függően nagyobb számra lehet szükség.)  

   > [!NOTE]
   > Az ajánlott módszer egy/29 hálózat létrehozása, de a hálózati méret is megtörténik.

- **Replikációs hálózat:** Hozzon létre egy új hálózatot a VMware HCX-replikációhoz, és terjessze ki a hálózatot a vSphere-fürtön egy porttartomány használatával. Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. (A telepítéstől függően nagyobb számra lehet szükség.)

   > [!NOTE]
   > Az ajánlott módszer egy/29 hálózat létrehozása, de a hálózati méret is megtörténik.