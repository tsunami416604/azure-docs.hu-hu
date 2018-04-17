---
title: Hozzon létre egy Azure virtuális gép a hálózat elérését gyorsítja fel |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Linux virtuális gép a hálózat elérését gyorsítja fel.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 718990b69cc75709af819ad7df9a77ad0f8f33ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>A hálózat elérését gyorsítja fel Linux virtuális gép létrehozása

> [!IMPORTANT] 
> Virtuális gépek léteznie kell a hálózat elérését gyorsítja fel engedélyezve van. Ez a funkció nem engedélyezhető a meglévő virtuális gépeken. Az alábbi lépésekkel engedélyezése a hálózat elérését gyorsítja fel:
>   1. Törölje a virtuális gépet.
>   2. Hozza létre a virtuális gép a hálózat elérését gyorsítja fel engedélyezve van.
>

Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy Linux virtuális gép (VM) a hálózat elérését gyorsítja fel. Gyorsított hálózatkezelés lehetővé teszi, hogy az egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez, a hálózati teljesítmény nagy mértékben javítva. A nagy teljesítményű elérési út nincs hatással a gazdagép a datapath, így csökkentve a késést, a jitter és a CPU felhasználását, a legnagyobb igénybevételt jelentő munkaterheléseit támogatott Virtuálisgép-típusokon való használatra. Az alábbi képen látható rendelkező és anélküli gyorsított hálózatkezelés két virtuális gépek közötti kommunikáció:

![Összehasonlítása](./media/create-vm-accelerated-networking/accelerated-networking.png)

Gyorsított hálózatkezelés nélkül, az összes hálózati forgalom mindkét a virtuális Gépet a virtuális kapcsoló és a gazdagép be kell járnia. A virtuális kapcsoló betartatja az összes, például a hálózati biztonsági csoportok, hozzáférés-vezérlési listák, elkülönítési és egyéb hálózati szempontból virtualizált szolgáltatások a hálózati forgalmat. Virtuális kapcsolók kapcsolatos további tudnivalókért olvassa el a [Hyper-V hálózatvirtualizálás és a virtuális kapcsoló](https://technet.microsoft.com/library/jj945275.aspx) cikk.

Gyorsított hálózattal, a hálózati forgalom érkezik a virtuális hálózati adapter (NIC), és a virtuális gép ezután kerül. A virtuális kapcsolót alkalmazó hálózati házirendben most kiszervezve, és a hardver alkalmazza. A hardver-házirend alkalmazását lehetővé teszi, hogy a hálózati adapter a hálózati forgalom erre a virtuális Gépet, a gazdagép és a virtuális kapcsolót, akkor alkalmazza, a fogadó minden házirend fenntartásával kihagyásával.

A gyorsított hálózat előnyeit, hogy engedélyezve van a virtuális gép csak vonatkoznak. A legjobb eredmény elérése érdekében az ideális legalább két virtuális gépeken, az azonos Azure Virtual Network (VNet) csatlakozik a funkció engedélyezése érdekében. Ha kommunikál a Vnetek vagy az összekötő a helyszíni, ez a funkció csak minimális befolyással van általános késleltetésű.

## <a name="benefits"></a>Előnyök
* **Késés csökkentése / magasabb csomag / másodperc (pps):** a virtuális kapcsoló eltávolítása a datapath eltávolítja a csomagok kiosztására szánt idejét a gazdagép a házirend-feldolgozás, a virtuális Gépen belül feldolgozható csomagok száma.
* **Alacsonyabb jitter:** virtuális kapcsoló feldolgozása függ a házirendet, szükség van, és a CPU-t, amely a feldolgozási terhelését. A házirendek betartatását, hogy a hardver kiszervezésével megszüntetéséhez, hogy a variancia közvetlenül a virtuális gép eltávolítása a gazdagép VM kommunikáció és az összes szoftver megszakítások és a környezet kapcsolók kézbesíti a csomagokat.
* **Csökkent a CPU-felhasználás:** kevesebb hálózati forgalom feldolgozása a CPU-használatot a virtuális kapcsoló a gazdagép megkerülésével vezet.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
* **Ubuntu 16.04**: 4.11.0-1013 vagy nagyobb kernel-verzió
* **SLES 12 SP3**: 4.4.92-6.18 vagy nagyobb kernel-verzió
* **RHEL 7.4**: 7.4.2017120423 vagy nagyobb kernel-verzió
* **CentOS 7.4**: 7.4.20171206 vagy nagyobb kernel-verzió

## <a name="supported-vm-instances"></a>Támogatott Virtuálisgép-példányok
Gyorsított hálózatkezelés legtöbb általános célú és számítási optimalizált példány mérete 4 vagy több Vcpu a támogatott. -Példányokon például D/DSv3 vagy E/ESv3, amely támogatja a Hyper-Threading technológia az elérését gyorsítja fel a hálózat támogatott 8 vagy több Vcpu a Virtuálisgép-példányok.  Támogatott adatsorokat: D/DSv2, D/DSv3, E/ESv3, F/Fs/Fsv2 és Ms/Mms. 

További információ a Virtuálisgép-példányok: [Linux Virtuálisgép-méretek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Régiók
Elérhető az összes Azure-régiók, valamint Azure Government felhők.

## <a name="limitations"></a>Korlátozások
A következő korlátozások vonatkoznak az e funkció használata esetén:

* **A hálózati illesztő létrehozása:** gyorsított hálózat csak akkor engedélyezhető, az új hálózati Nem engedélyezhető az egy meglévő hálózati adaptert.
* **Virtuális gép létrehozása:** A hálózati adapter engedélyezve gyorsított hálózattal csak akkor csatolható a virtuális géphez a virtuális gép létrehozásakor. A hálózati adapter nem lehet csatolni, egy meglévő virtuális gépre. Ha a virtuális gép hozzáadása egy meglévő rendelkezésre állási, a rendelkezésre állási csoport virtuális gépeinek kell is rendelkezik az elérését gyorsítja fel engedélyezett hálózati.
* **Telepítés csak Azure Resource Manageren keresztül:** virtuális gépek (klasszikus) nem állítható rendszerbe, a hálózat elérését gyorsítja fel.

Bár ez a cikk ismerteti a virtuális gép létrehozása az Azure parancssori felület használatával gyorsított hálózattal, is [virtuális gép létrehozása az Azure portál használatával gyorsított hálózattal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Létrehozásakor egy virtuális gépet a portálon, a **beállítások**, jelölje be **engedélyezve**a **elérését gyorsítja fel a hálózat**. Gyorsított hálózatkezelés lehetővé teszi nem szerepel a portálon, kivéve, ha a kijelölt egy [támogatott operációs rendszer](#supported-operating-systems) és [Virtuálisgép-méretet](#supported-vm-instances). A virtuális gép létrehozása után végre kell hajtania a utasításait [győződjön meg arról, hogy engedélyezve van-e a gyorsított hálózatkezelés](#confirm-that-accelerated-networking-is-enabled).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login). A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *myNic*, és *myVm*.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *centralus* helye:

```azurecli
az group create --name myResourceGroup --location centralus
```

Ki kell választania egy támogatott Linux régióban felsorolt [Linux az elérését gyorsítja fel hálózati](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet* egyetlen alhálózattal:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre egy hálózati biztonsági csoport [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

A hálózati biztonsági csoport több alapértelmezett szabályokat, amelyek közül az egyik letiltja az internetről érkező összes bejövő hozzáférést tartalmazza. Nyisson meg egy portot a virtuális gép SSH eléréséhez [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Hozzon létre egy adott hálózati csatoló gyorsított hálózatkezelés

Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) paranccsal. A nyilvános IP-cím nem szükséges, ha nem tervezi a virtuális gép az internetről való eléréséhez, de ez a cikk a végrehajtásához, szükség rá.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Hozzon létre egy hálózati illesztő – [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create) engedélyezett gyorsított hálózattal. Az alábbi példa létrehoz egy adott hálózati csatoló nevű *myNic* a a *mySubnet* alhálózata a *myVnet* virtuális hálózat és társult a  *myNetworkSecurityGroup* a hálózati illesztő hálózati biztonsági csoport:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>Hozzon létre egy virtuális Gépet, és csatlakoztassa a hálózati adapter
A virtuális gép létrehozásakor adja meg a hálózati adapter segítségével létrehozott `--nics`. Ki kell választania a méretre és a felsorolt terjesztési [Linux az elérését gyorsítja fel hálózati](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* UbuntuLTS kép és a méretet, amely támogatja a hálózat elérését gyorsítja fel (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Az összes Virtuálisgép-méretek és jellemzők listájáért lásd: [Linux Virtuálisgép-méretek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

A virtuális gép létrehozása után a következő egy példa a kimenetre hasonló kimenetet küld vissza. Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális Gépet, a későbbi lépésekben eléréséhez használt.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Győződjön meg arról, hogy engedélyezve van-e a gyorsított hálózatkezelés

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le `<your-public-ip-address>` a nyilvános IP-címmel hozzárendelni a virtuális gép alapján létrehozott, és cserélje le *azureuser* meg más értéket használatakor `--admin-username` a virtuális gép létrehozásakor.

```bash
ssh azureuser@<your-public-ip-address>
```

Adja meg a rendszerhéjakba `uname -r` és erősítse meg, hogy a rendszermag-verzió egyet a következő verziók vagy annál nagyobb:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Győződjön meg arról, a Mellanox VF eszköz a virtuális Géphez a van kitéve a `lspci` parancsot. A visszaadott kimenete a következő kimeneti hasonlít:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Egyeztesse a VF (virtuális függvény) a tevékenység a `ethtool -S eth0 | grep vf_` parancsot. Ha a kimeneti kimeneti gyorsított hálózatkezelés engedélyezve van és működik-e a következő mintához hasonló.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Gyorsított hálózatkezelés engedélyezve van a virtuális gép számára.
