---
title: Útválasztási beállítások konfigurálása nyilvános IP-címekhez – Azure Portal
description: Ismerje meg, hogyan hozhat létre egy nyilvános IP-címet egy internetes forgalom útválasztási beállításával
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 21d9f318ef18b7ffb49a95ce495c09f1fa46ec1a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491315"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>A nyilvános IP-cím útválasztási beállításainak konfigurálása a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az [útválasztási](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview) beállításokat a nyilvános IP-címekhez tartozó ISP-hálózat (**internetes** beállítás) használatával. A nyilvános IP-cím létrehozása után a következő Azure-erőforrásokkal társíthatja a bejövő és kimenő adatforgalmat az internethez:

* Virtuális gép
* Virtuálisgép-méretezési csoport
* Azure Kubernetes Service (AKS)
* Internetkapcsolattal rendelkező Load Balancer
* Application Gateway
* Azure Firewall

Alapértelmezés szerint a nyilvános IP-cím útválasztási beállítása az összes Azure-szolgáltatáshoz a Microsoft globális hálózatra van állítva, és bármely Azure-szolgáltatáshoz társítható.

> [!IMPORTANT]
> Az útválasztási preferencia jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Nyilvános IP-cím létrehozása útválasztási beállítással
1. Jelentkezzen be az [Azure Portalra](https://preview.portal.azure.com/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a *nyilvános IP-cím* kifejezést.
3. A keresési eredmények között válassza a **nyilvános IP-cím** elemet. Ezután a **nyilvános IP-cím** lapon válassza a **Létrehozás** lehetőséget.
3. Az **útválasztási** beállítások lehetőségnél válassza az **Internet** lehetőséget.

      ![Nyilvános IP-cím létrehozása](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > A nyilvános IP-címek IPv4- vagy IPv6-címekkel jönnek létre. Az útválasztási beállítások azonban jelenleg csak az IPV4-t támogatják.

A fenti létrehozott nyilvános IP-címet egy Windows vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális géppel társíthatja. A CLI szakasz az oktatóanyag oldalon: [nyilvános IP-cím hozzárendelése egy virtuális géphez](associate-public-ip-address-vm.md#azure-cli) , hogy a nyilvános IP-címet a virtuális géphez rendelje. A fent létrehozott nyilvános IP-címet egy [Azure Load Balancer](../load-balancer/load-balancer-overview.md)is társíthatja, ha hozzárendeli a terheléselosztó előtér **-konfigurációjához.** A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd.

## <a name="next-steps"></a>További lépések
- További információ a [nyilvános IP-címekről az útválasztási beállításokkal](routing-preference-overview.md).
- [Konfigurálja a virtuális gép útválasztási beállításait](tutorial-routing-preference-virtual-machine-portal.md).
- [Konfigurálja a nyilvános IP-cím útválasztási beállításait a PowerShell használatával](routing-preference-powershell.md).
- További információ az Azure [-beli nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).
