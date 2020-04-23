---
title: Az Azure VMware-megoldás CloudSimple általi felügyeletének legfontosabb fogalmai
titleSuffix: Azure VMware Solution by CloudSimple
description: Az Azure VMware-megoldások CloudSimple általi felügyeletének legfontosabb fogalmai
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869028"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Az Azure VMware-megoldások CloudSimple általi felügyeletének legfontosabb fogalmai

Az Azure VMware-megoldások CloudSimple általi felügyeletéhez az alábbi fogalmak megértésére van szükség:

* CloudSimple szolgáltatás, amely a CloudSimple Által Azure VMware-megoldásokként jelenik meg - Szolgáltatás
* CloudSimple csomópont, amely a CloudSimple Által Azure VMware-megoldásokként jelenik meg - Csomópont
* CloudSimple magánfelhő
* Szolgáltatás hálózata
* CloudSimple virtuális gép, amely a CloudSimple Azure VMware-megoldásaként jelenik meg - Virtuális gép

## <a name="cloudsimple-service"></a>CloudSimple szolgáltatás

A CloudSimple szolgáltatással létrehozhatja és kezelheti a CloudSimple vmware-megoldásaihoz kapcsolódó összes erőforrást az Azure Portalról. Hozzon létre egy szolgáltatási erőforrást minden régióban, ahol használni kívánja a szolgáltatást.

További információ a [CloudSimple szolgáltatásról.](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>CloudSimple csomópont

A CloudSimple csomópont egy dedikált, csupasz fém, hyperconverged számítási és tárolási gazdagép, amelybe a VMware ESXi hipervizor telepítve van. Ez a csomópont ezután beépül a VMware vSphere, vCenter, vSAN és NSX platformokba. A CloudSimple hálózati szolgáltatások és a peremhálózati szolgáltatások is engedélyezve vannak. Minden csomópont számítási és tárolási kapacitásegységként szolgál, amelyet [a CloudSimple magánfelhők](cloudsimple-private-cloud.md)létrehozásához építhet ki. Olyan régióban, ahol a CloudSimple szolgáltatás érhető el, csomópontokat létesít vagy foglal le.

További információ a [CloudSimple csomópontjairól.](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>CloudSimple magánfelhő

A CloudSimple magánfelhő egy elkülönített VMware veremkörnyezet, amelyet egy vCenter-kiszolgáló saját felügyeleti tartományában kezel. A VMware verem tartalmazza esxi gazdagépek, vSphere, vCenter, vSAN és NSX. A verem dedikált csomópontokon fut (dedikált és elkülönített csupasz fém hardver), és a felhasználók által használt natív VMware eszközök, amelyek magukban foglalják a vCenter és az NSX Manager. A dedikált csomópontok az Azure-helyeken vannak telepítve, és az Azure kezeli őket. Minden magánfelhő menthető és védhető hálózati szolgáltatások, például VLAN-ok, alhálózatok és tűzfaltáblák használatával. A helyszíni környezethez és az Azure-hálózathoz való kapcsolatok biztonságos, privát VPN- és Azure ExpressRoute-kapcsolatok használatával jönnek létre.

További információ a [CloudSimple magánfelhőről.](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Szolgáltatás hálózata

A CloudSimple szolgáltatás régiónként biztosít egy hálózatot, ahol a CloudSimple szolgáltatás telepítve van. A hálózat egyetlen TCP Layer 3 címterület, ahol az útválasztás alapértelmezés szerint engedélyezve van. Az ebben a régióban létrehozott összes privát felhő és alhálózat további konfiguráció nélkül kommunikál egymással. A vCenteren elosztott portcsoportokat hoz létre a VLAN-ok használatával. A következő hálózati szolgáltatásokkal konfigurálhatja és biztonságossá teheti a számítási feladatok erőforrásait a magánfelhőben:

* [Virtuális helyi hálózatok és alhálózatok](cloudsimple-vlans-subnets.md)
* [Tűzfaltáblák](cloudsimple-firewall-tables.md)
* [VPN-átjárók](cloudsimple-vpn-gateways.md)
* [Nyilvános IP-cím](cloudsimple-public-ip-address.md)
* [Azure-hálózati kapcsolat](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuális gép

A CloudSimple szolgáltatással kezelheti a VMware virtuális gépeket az Azure Portalról. Egy vagy több fürtök vagy erőforráskészletek a vSphere-környezetből leképezhető az előfizetés, amelyen a szolgáltatás jön létre.

További információk:

* [CloudSimple virtuális gépek](cloudsimple-virtual-machines.md)
* [Azure-előfizetés-leképezés](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
