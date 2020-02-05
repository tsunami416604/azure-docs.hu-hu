---
title: Az Azure VMware-megoldások (AVS) felügyeletével kapcsolatos főbb fogalmak
description: Az Azure VMware-megoldások (AVS) felügyeletével kapcsolatos főbb fogalmakat ismerteti
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025231"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Az Azure VMware Solutions (AVS) felügyeletével kapcsolatos főbb fogalmak

Az Azure VMware Solutions (AVS) felügyeletéhez az alábbi fogalmak ismerete szükséges:

* AVS-szolgáltatás, amely Azure VMware Solutions (AVS) szolgáltatásként jelenik meg
* AVS-csomópont, amely Azure VMware Solutions (AVS) csomópontként jelenik meg
* AVS Private Cloud
* Szolgáltatás hálózatkezelése
* AVS virtuális gép, amely Azure VMware-megoldásként (AVS) jelenik meg (virtuális gép)

## <a name="avs-service"></a>AVS-szolgáltatás

Az AVS szolgáltatással létrehozhatja és kezelheti a VMware-megoldásokhoz (AVS) társított összes erőforrást a Azure Portal. Hozzon létre egy szolgáltatási erőforrást minden olyan régióban, ahol használni szeretné a szolgáltatást.

További információ az [AVS szolgáltatásról](cloudsimple-service.md).

## <a name="avs-node"></a>AVS-csomópont

Az AVS-csomópontok olyan dedikált, operációs rendszer nélküli, hiperkonvergens számítási és tárolási gazdagépek, amelyeken a VMware ESXi hypervisor telepítve van. Ezt a csomópontot ezután beépítjük a VMware vSphereba, a vCenter, a vSAN és a NSX platformba. Az AVS hálózati szolgáltatások és az Edge hálózati szolgáltatások is engedélyezve vannak. Minden csomópont olyan számítási és tárolási kapacitási egységként szolgál, amelyet az [AVS privát felhők](cloudsimple-private-cloud.md)létrehozásához lehet kiépíteni. A csomópontokat olyan régióban kell kiépíteni vagy fenntartani, ahol az AVS szolgáltatás elérhető.

További információ az [AVS-csomópontokról](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>AVS Private Cloud

Az AVS Private Cloud egy elkülönített VMware stack-környezet, amelyet egy vCenter-kiszolgáló felügyel a saját felügyeleti tartományában. A VMware-verem ESXi-gazdagépeket, vSphere, vCenter, vSAN és NSX tartalmaz. A verem dedikált csomópontokon fut (dedikált és elkülönített operációs rendszer nélküli hardver), és a felhasználók a vCenter és a NSX Managert is tartalmazó natív VMware-eszközökkel használják. A dedikált csomópontok üzembe helyezése az Azure-beli helyszíneken történik, és az Azure kezeli őket. Az összes AVS Private-felhő szegmentálható és biztonságossá tehető hálózati szolgáltatások, például VLAN-ok, alhálózatok és tűzfalak használatával. A helyszíni környezettel és az Azure-hálózattal létesített kapcsolatok biztonságos, privát VPN-és Azure ExpressRoute-kapcsolatok használatával jönnek létre.

További információ az [AVS Private Cloud](cloudsimple-private-cloud.md)-ról.

## <a name="service-networking"></a>Szolgáltatás hálózatkezelése

Az AVS szolgáltatás egy olyan hálózatot biztosít a régión belül, ahol az AVS-szolgáltatás telepítve van. A hálózat egy olyan TCP-rétegbeli 3 címtartomány, amelynek alapértelmezés szerint engedélyezve van az Útválasztás. Az ebben a régióban létrehozott összes AVS Private-felhő és alhálózat további konfiguráció nélkül kommunikál egymással. A VLAN-ok használatával elosztott vCenter hozhat létre a virtuális helyi hálózatokon. Az alábbi hálózati szolgáltatásokkal konfigurálhatja és biztonságossá teheti a munkaterhelés-erőforrásokat az AVS Private Cloud-ban:

* [VLAN-ok és alhálózatok](cloudsimple-vlans-subnets.md)
* [Tűzfalak](cloudsimple-firewall-tables.md)
* [VPN-átjárók](cloudsimple-vpn-gateways.md)
* [Nyilvános IP-cím](cloudsimple-public-ip-address.md)
* [Azure-beli hálózati kapcsolatok](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>AVS virtuális gép

Az AVS szolgáltatással felügyelheti a VMware virtuális gépeket a Azure Portal. A vSphere-környezetből egy vagy több fürt vagy erőforráskészlet képezhető le ahhoz az előfizetéshez, amelyen a szolgáltatást létrehozták.

További információk:

* [AVS virtuális gépek](cloudsimple-virtual-machines.md)
* [Azure-előfizetés leképezése](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
