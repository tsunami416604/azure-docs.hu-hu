---
title: VMware HCX hálózati szegmensek
description: A VMware HCX-hez négy hálózat szükséges.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999628"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

A VMware HCX négy hálózatra van szükség:

- **Felügyeleti hálózat:** Általában ugyanaz a felügyeleti hálózat, amelyet a vSphere-fürt használ. Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.

   > [!NOTE]
   > Az ajánlott módszer egy/26 hálózat létrehozása. Egy/26 hálózatban akár 10 szolgáltatás-rácsvonalat és 60 hálózati Extendert (-1) használhat. Az Azure VMware megoldás privát felhők használatával nyolc hálózatot is kiterjesztheti.
   >
   
- **vMotion hálózat:** Általában ugyanazt a hálózatot használják a vMotion a vSphere-fürtön.  Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.  

   A vMotion-hálózatnak elérhetőnek kell lennie egy elosztott virtuális kapcsolón vagy vSwitch0. Ha nem, módosítsa a környezetet.

   > [!NOTE]
   > Ez a hálózat lehet privát (nem irányított).

- **Kimenő hálózat:** Létre kell hoznia egy új hálózatot a VMware HCX kimenő hálózathoz, és ki kell terjesztenie a vSphere-fürtre egy porttartomány használatával. Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.  

   > [!NOTE]
   > Az ajánlott módszer egy/26 hálózat létrehozása. Egy/26 hálózatban akár 10 szolgáltatás-rácsvonalat és 60 hálózati Extendert (-1) használhat. Az Azure VMware megoldás privát felhők használatával nyolc hálózatot is kiterjesztheti.
   >
   
- **Replikációs hálózat:** Ez nem kötelező. Hozzon létre egy új hálózatot a VMware HCX-replikációhoz, és terjessze ki a hálózatot a vSphere-fürtön egy porttartomány használatával. Legalább két IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.

   > [!NOTE]
   > Ez a konfiguráció csak akkor lehetséges, ha a helyszíni fürt gazdagépei dedikált replikációs VMkernel-hálózatot használnak.  Ha a helyszíni fürthöz nincs definiálva dedikált replikációs VMkernel hálózat, nem kell létrehoznia ezt a hálózatot.
