---
title: Az alkalmazások magas rendelkezésre állásának biztosítása az Azure-beli VMware-ben való futtatáskor
description: A(z) CloudSimple magas rendelkezésre állási funkciói a CloudSimple magánfelhőben futó alkalmazások gyakori alkalmazáshibáinak kezelésére szolgálnak
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025350"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Az alkalmazások magas rendelkezésre állásának biztosítása az Azure-beli VMware-ben való futtatáskor

A CloudSimple megoldás magas rendelkezésre állást biztosít a VMware-en futó alkalmazások számára az Azure-környezetben. Az alábbi táblázat a hibaforgatókönyveket és a kapcsolódó magas rendelkezésre állási szolgáltatásokat sorolja fel.

| Hiba forgatókönyv | Alkalmazás védett? | Platform HA funkció | VMware HA funkció | Az Azure HA szolgáltatás |
------------ | ------------- | ------------ | ------------ | ------------- |
| Lemezhiba | IGEN | A sikertelen csomópont gyors cseréje | [A vSAN alapértelmezett tárolási házirendje](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Ventilátor hiba | IGEN | Redundáns ventilátorok, a meghibásodott csomópont gyors cseréje |  |  |
| Hálózati adapter hibája | IGEN | Redundáns hálózati adapter, a sikertelen csomópont gyors cseréje
| Állomás áramkimaradás | IGEN | Redundáns tápegység |  |  |
| ESXi állomás hiba | IGEN | a meghibásodott csomópont gyors cseréje | [VMware vSphere magas rendelkezésre állás](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Virtuális gép hibája | IGEN | [Terheléselosztók](load-balancers.md)  | [VMware vSphere magas rendelkezésre állás](https://www.vmware.com/products/vsphere/high-availability.html) | Azure load balancer állapot nélküli VMware virtuális gépekhez |
| Levélkapcsoló porthibája | IGEN | Redundáns hálózati adapter |  |  |
| Levélkapcsoló hibája | IGEN | Redundáns levélkapcsolók |  |  |
| Rack hiba | IGEN | Elhelyezési csoportok |  |  |
| Hálózati kapcsolat a helyszíni tartományvezérlővel | IGEN  | Redundáns hálózati szolgáltatások |  | Redundáns ER-áramkörök |
| Hálózati kapcsolat az Azure-ral | IGEN | |  | Redundáns ER-áramkörök |
| Adatközpont-hiba | IGEN |  |  | Rendelkezésre állási zónák |
| Regionális hiba | IGEN  |  |  | Azure-régiók |

Az Azure VMware Solution by CloudSimple a következő magas rendelkezésre állású funkciókat biztosítja.

## <a name="fast-replacement-of-failed-node"></a>A sikertelen csomópont gyors cseréje

A CloudSimple vezérlősík szoftver folyamatosan figyeli a VMware-fürtök állapotát, és észleli, ha egy ESXi-csomópont meghibásodik. Ezután automatikusan hozzáad egy új ESXi-állomást az érintett VMware-fürthöz a könnyen elérhető csomópontok készletéből, és kiveszi a meghibásodott csomópontot a fürtből. Ez a funkció biztosítja, hogy a VMware-fürt tartalékkapacitása gyorsan helyreálljon, így a fürt vSAN és VMware HA által biztosított rugalmassága helyreáll.

## <a name="placement-groups"></a>Elhelyezési csoportok

A privát felhőt létrehozó felhasználó kiválaszthat egy Azure-régiót és egy elhelyezési csoportot a kiválasztott régión belül. Az elhelyezési csoport csomópontok csoportja, amelyek több állványra, de ugyanazon a gerinchálózati szegmensen belül vannak elosztva. Az ugyanazon elhelyezési csoporton belüli csomópontok legfeljebb két további kapcsolóugrással érhetik el egymást. Az elhelyezési csoport mindig egyetlen Azure rendelkezésre állási zónán belül van, és több állványra terjed ki. A CloudSimple vezérlősík a legjobb erőfeszítés alapján több állványon is elosztja a magánfelhő csomópontjait. A különböző elhelyezési csoportok csomópontjai garantáltan különböző állványokba kerülnek.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A rendelkezésre állási zónák magas rendelkezésre állású ajánlatok, amelyek megvédik az alkalmazásokat és az adatokat az adatközponti hibáktól. A rendelkezésre állási zónák egy Azure-régión belüli speciális fizikai helyek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. Minden régió egy rendelkezésre állási zónával rendelkezik. További információ: [Mik a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundáns Azure ExpressRoute-áramkörök

Adatközpont-kapcsolat az ExpressRoute használatával az Azure virtuális hálózat redundáns áramkörök magas rendelkezésre állású hálózati kapcsolat.

## <a name="redundant-networking-services"></a>Redundáns hálózati szolgáltatások

A Private Cloud összes CloudSimple hálózati szolgáltatása (beleértve a VLAN-t, a tűzfalat, a nyilvános IP-címeket, az internetet és a VPN-t) úgy lett kialakítva, hogy magas rendelkezésre állású legyen, és támogassa a szolgáltatás SLA-ját.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 terheléselosztó állapot nélküli VMware virtuális gépekhez

A felhasználók egy Azure Layer 7 load balancer előtt a vmware-környezetben futó állapotnélküli webes réteg virtuális gépek a webes réteg magas rendelkezésre állásának elérése érdekében.

## <a name="azure-regions"></a>Azure-régiók

Az Azure-régió egy késésáltal meghatározott peremterületen üzembe helyezett és egy dedikált regionális alacsony késésű hálózaton keresztül csatlakoztatott adatközpontok készlete. További információt az [Azure Regions (Azure Regions) (Azure Regions) (További](https://azure.microsoft.com/global-infrastructure/regions)részletekért: Azure Regions) (Az
