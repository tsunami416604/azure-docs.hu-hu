---
title: Azure VMware-megoldás a CloudSimple által – VSAN titkosítás konfigurálása magánfelhőhöz
description: A vSAN szoftveres titkosítási szolgáltatás konfigurálása, hogy a CloudSimple private cloud az Azure virtuális hálózatában futó kulcskezelő kiszolgálóval működhessen.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020641"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>VSAN titkosítás konfigurálása a CloudSimple private cloud szolgáltatáshoz

Konfigurálhatja a vSAN szoftveres titkosítási funkciót, így a CloudSimple private cloud az Azure virtuális hálózatában futó kulcskezelő kiszolgálóval dolgozhat.

A VMware vSAN-titkosítás használata esetén külső KMIP 1.1-kompatibilis, harmadik féltől származó kulcskezelő kiszolgáló (KMS) eszközt kell használni. A VMware által hitelesített és az Azure-ban elérhető támogatott KMS-t használhatja ki.

Ez az útmutató bemutatja, hogyan használhatja a HyTrust KeyControl KMS-t egy Azure virtuális hálózatban futó használatával. Hasonló megközelítés használható bármely más, harmadik féltől származó, harmadik féltől származó KMS-megoldáshoz a vSAN-hoz.

Ez a KMS megoldás a következőket igényli:

* Telepítsen, konfiguráljon és kezeljen egy VMware-tanúsítvánnyal rendelkező, külső gyártótól származó KMS-eszközt az Azure virtuális hálózatában.
* Adja meg saját licenceit a KMS eszközhöz.
* VSAN-titkosítás konfigurálása és kezelése a magánfelhőben az Azure virtuális hálózatában futó, külső gyártótól származó KMS-eszközzel.

## <a name="kms-deployment-scenario"></a>KMS telepítési forgatókönyv

A KMS-kiszolgálófürt az Azure virtuális hálózatában fut, és a magánfelhő vCenterből érhető el az IP-cím a konfigurált Azure ExpressRoute-kapcsolaton keresztül.

![.. /media/KMS fürt az Azure virtuális hálózatában](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás telepítése

A telepítési folyamat a következő lépésekkel rendelkezik:

1. [Az előfeltételek teljesülésének ellenőrzése](#verify-prerequisites-are-met)
2. [CloudSimple portál: ExpressRoute-társviszony-létesítési adatok beszerzése](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure portal: A virtuális hálózat csatlakoztatása a magánfelhőhöz](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure portal: HyTrust KeyControl fürt telepítése a virtuális hálózatban](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: KMIP-kiszolgáló konfigurálása](#hytrust-webui-configure-the-kmip-server)
6. [vCenter felhasználói felület: A vSAN-titkosítás konfigurálása kms-fürt használatára az Azure virtuális hálózatában](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Az előfeltételek teljesülésének ellenőrzése

Ellenőrizze a telepítés előtt a következőket:

* A kijelölt KMS-szállító, -eszköz és -verzió szerepel a vSAN kompatibilitási listán.
* A kiválasztott szállító támogatja az eszköz azure-beli futtatásának egy verzióját.
* A KMS-eszköz Azure-verziója a KMIP 1.1-nek megfelelő.
* Már létre van hozva egy Azure Resource Manager és egy virtuális hálózat.
* A CloudSimple privát felhő már létre.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple portál: ExpressRoute-társviszony-létesítési adatok beszerzése

A telepítés folytatásához szüksége van az engedélyezési kulcsra és a társáramkör URI-jára az ExpressRoute-hoz, valamint az Azure-előfizetéshez való hozzáférésre. Ez az információ a CloudSimple portál virtuális hálózati kapcsolatlapján érhető el. További információt a [Virtuális hálózati kapcsolat beállítása a magánfelhővel című témakörben talál.](virtual-network-connection.md) Ha bármilyen problémája van az információ megszerzésével, nyisson meg egy [támogatási kérelmet.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure portal: A virtuális hálózat csatlakoztatása a magánfelhőhöz

1. Hozzon létre egy virtuális hálózati átjárót a virtuális hálózat számára az [ExpressRoute virtuális hálózati átjárókonfigurálása az Azure Portal használatával című](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)útmutató utasításait követve.
2. Kapcsolja össze a virtuális hálózatot a CloudSimple ExpressRoute-kapcsolattal a [Virtuális hálózat csatlakoztatása expressroute-kapcsolattal a portál használatával](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)című útmutató utasításait követve.
3. A CloudSimple-től fogadott üdvözlő e-mailben kapott CloudSimple ExpressRoute-kapcsolatadataival összekapcsolhatja a virtuális hálózatot az Azure-beli CloudSimple ExpressRoute-áramkörrel.
4. Adja meg az engedélyezési kulcsot és a társáramkör URI-ját, adjon nevet a kapcsolatnak, majd kattintson az **OK**gombra.

![CS-társkapcsolati URI biztosítása a virtuális hálózat létrehozásakor](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure portal: HyTrust KeyControl-fürt üzembe helyezése az Azure Resource Managerben a virtuális hálózatban

HyTrust KeyControl-fürt üzembe helyezéséhez az Azure Resource Manager a virtuális hálózatban, hajtsa végre a következő feladatokat. A részleteket a [HyTrust dokumentációjában](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) találja.

1. Hozzon létre egy Azure-hálózati biztonsági csoportot (nsg-hytrust) a megadott bejövő szabályokkal a HyTrust dokumentációutasításait követve.
2. Hozzon létre egy SSH-kulcspárt az Azure-ban.
3. Telepítse a kezdeti KeyControl-csomópontot a lemezképből az Azure Marketplace-en.  Használja a létrehozott kulcspár nyilvános kulcsát, és válassza az **nsg-hytrust lehetőséget** a KeyControl csomópont hálózati biztonsági csoportjaként.
4. Konvertálja a KeyControl privát IP-címét statikus IP-címmé.
5. SSH a KeyControl virtuális gép hez a nyilvános IP-cím és a korábban említett kulcspár titkos kulcsa használatával.
6. Amikor a rendszer az SSH-rendszerhéjban kéri, válassza ki, `No` hogy a csomópontot állítsa be kezdeti KeyControl csomópontként.
7. Adjon hozzá további KeyControl-csomópontokat az eljárás `Yes` 3-5.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: A KMIP-kiszolgáló konfigurálása

Nyissa meg https://*nyilvános ip*, ahol a *nyilvános ip* a KeyControl csomópont virtuális gépének nyilvános IP-címe. Kövesse a [HyTrust dokumentációjának](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)lépéseit.

1. [KMIP-kiszolgáló konfigurálása](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Tanúsítványköteg létrehozása vmware-titkosításhoz](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter felhasználói felület: A vSAN-titkosítás konfigurálása kms-fürt használatára az Azure virtuális hálózatában

Kövesse a HyTrust utasításokat [a KMS-fürt létrehozásához a vCenter alkalmazásban.](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)

![KMS-fürt részleteinek hozzáadása a vCenterben](media/vsan-config01.png)

A vCenterben nyissa meg **a Fürt > konfigurálása** lehetőséget, és válassza a vSAN **Általános** beállítását. Engedélyezze a titkosítást, és válassza ki a vCenterhez korábban hozzáadott KMS-fürtöt.

![VSAN-titkosítás engedélyezése és KMS-fürt konfigurálása a vCenter ben](media/vsan-config02.png)

## <a name="references"></a>Referencia

### <a name="azure"></a>Azure

[Virtuális hálózati átjáró konfigurálása az ExpressRoute számára az Azure Portal használatával](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Virtuális hálózat összekapcsolása ExpressRoute-kapcsolatcsoporttal a portálon](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust között

[HyTrust DataControl és a Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[KMPI-kiszolgáló konfigurálása](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Tanúsítványköteg létrehozása vmware-titkosításhoz](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[A KMS-fürt létrehozása a vSphere-ben](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
