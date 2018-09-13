---
title: Hozzon létre egy Azure virtuális gép gyorsított hálózatkezelésű |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Linux rendszerű virtuális gép gyorsított hálózatkezelés engedélyezett.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 5863160632e544ca542debcf74971e90ea452939
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716672"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelésű

Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy Linux rendszerű virtuális gép (VM) gyorsított hálózatkezelésű. Windows virtuális gép gyorsított hálózatkezelésű létrehozásához lásd: [hozzon létre egy Windows virtuális gép gyorsított hálózatkezelésű](create-vm-accelerated-networking-powershell.md). Gyorsított hálózatkezelés lehetővé teszi, hogy az egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez, nagy mértékben javítva a hálózati teljesítmény. A nagy teljesítményű elérési út megkerüli a gazdagép a datapath csökkenti a késés, a jitter és a Processzor kihasználtsága a legnagyobb hálózati számítási a támogatott Virtuálisgép-típusok való használatra. Az alábbi képen látható a két virtuális gépet, és anélkül gyorsított hálózatkezelés közötti kommunikáció:

![Összehasonlítás](./media/create-vm-accelerated-networking/accelerated-networking.png)

Gyorsított hálózatkezelés nélkül a-ból a virtuális gép minden hálózati forgalmat kell haladnak át, a gazdagép és a virtuális kapcsolóhoz. A virtuális kapcsoló az összes házirend betartatása, például a hálózati biztonsági csoportok, hozzáférés-vezérlési listák, elkülönítési és egyéb hálózati forgalom virtualizált hálózati szolgáltatások biztosít. Virtuális kapcsolók kapcsolatos további információkért olvassa el a [Hyper-V hálózatvirtualizálás és a virtuális kapcsoló](https://technet.microsoft.com/library/jj945275.aspx) cikk.

Gyorsított hálózatkezeléssel, a hálózati forgalom szolgáltatáshoz érkezik, a virtuális gép hálózati adapteréhez (NIC), és továbbítja a virtuális Gépet. Minden hálózati házirendek, amelyekre vonatkozik a virtuális kapcsoló most kiszervezett és hardver a alkalmazni. A hardver házirend alkalmazása lehetővé teszi, hogy a hálózati Adaptert a hálózati forgalom közvetlenül az a virtuális Gépet, a gazdagép és a virtuális kapcsolót, megkerülése, alkalmazza a gazdagép minden házirend fenntartásával.

A gyorsított hálózatkezelés előnyeit csak a virtuális gép, amely engedélyezve van a vonatkoznak. A legjobb eredmények érdekében ideális engedélyezze ezt a funkciót, legalább két virtuális gépet az azonos Azure Virtual Network (VNet). Való kommunikációhoz, virtuális hálózatok közötti vagy helyszíni összekötő, ez a funkció csak minimális befolyással van a teljes késést.

## <a name="benefits"></a>Előnyök
* **Rövidebb válaszidőt / magasabb szintű csomag / másodperc (pps):** a virtuális kapcsoló eltávolítása a datapath eltávolítja a csomagok töltött idő a gazdagép a csoportházirend feldolgozása, és növeli a dolgozhatók fel a virtuális gép csomagok száma.
* **Alacsonyabb jitter:** virtuális kapcsoló feldolgozása a szabályzatot, amely a alkalmazni mennyiségét és a munkaterhelés, a CPU, amely a feldolgozási függ. A házirend betartatása hardverre tehermentesítést eltávolítja az adott változékonyságát azáltal, hogy a csomagok közvetlenül a virtuális gép eltávolítása a gazdagépet a virtuális gép kommunikációs és az összes szoftver megszakítások és környezeti kapcsolók a.
* **Csökkent a CPU-kihasználtság:** kevesebb hálózati forgalom feldolgozása CPU-felhasználást eredményez a virtuális kapcsoló a gazdagép megkerülésével.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure katalógusából beépített támogatottak: 
* **Ubuntu 16.04** 
* **SLES 12 SP3** 
* **7.4 RHEL**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "kiterjeszthető" portolások kernel**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

### <a name="supported-vm-instances"></a>Támogatott Virtuálisgép-példányok
Gyorsított hálózatkezelés legtöbb általános célú és a 2 vagy több vcpu-k méretű számításra optimalizált példányok esetén támogatott.  A támogatott sorozat: D/DSv2 és az F/Fs

Hyper-Threading technológia támogató példányokon gyorsított hálózatkezelés támogatott 4 vagy több vcpu-k a Virtuálisgép-példányokon. Támogatott sorozat: a DSv3/D, E/ESv3, Fsv2 és Ms és Mms.

További információ a Virtuálisgép-példányokon: [Linux Virtuálisgép-méretek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Régiók
Elérhető minden nyilvános Azure-régióban, valamint az Azure Government felhőben.

### <a name="network-interface-creation"></a>Hálózati adapter létrehozása 
Gyorsított hálózatkezelés csak akkor engedélyezhető, egy új hálózati adapteren. Nem engedélyezhető az egy meglévő hálózati adapteren.
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>A futó virtuális gép gyorsított hálózatkezelés engedélyezése
Gyorsított hálózatkezelés engedélyezett nélkül támogatott virtuális gép mérete legfeljebb a funkció engedélyezve van a leállítása és felszabadítása.  
### <a name="deployment-through-azure-resource-manager"></a>Üzembe helyezés Azure Resource Manager használatával
Gyorsított hálózatkezelésű nem állítható rendszerbe a virtuális gépek (klasszikus).

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Linux rendszerű virtuális gép létrehozása az Azure gyorsított hálózatkezelés

Bár ez a cikk ismerteti a virtuális gép létrehozása az Azure CLI-vel gyorsított hálózatkezeléssel, is [virtuális gép létrehozása az Azure portal használatával gyorsított hálózatkezeléssel](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A virtuális gép létrehozásakor a portálon a **beállítások**válassza **engedélyezve**alatt **gyorsított hálózatkezelés**. Engedélyezheti a gyorsított hálózatkezelés nem jelenik meg a portálon, ha kiválasztott egy [támogatott operációs rendszert](#supported-operating-systems) és [Virtuálisgép-méret](#supported-vm-instances). A virtuális gép létrehozása után a következő témakör utasításait végrehajtásához szükséges [győződjön meg arról, hogy engedélyezve van-e a gyorsított hálózatkezelés](#confirm-that-accelerated-networking-is-enabled).

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) , és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login). A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa paraméterneveket foglalt *myResourceGroup*, *myNic*, és *myVm*.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *centralus* helye:

```azurecli
az group create --name myResourceGroup --location centralus
```

Válassza ki a felsorolt támogatott Linux-régióban [Linuxos gyorsított hálózatkezelést](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot *myVnet* egyetlen alhálózattal:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre egy hálózati biztonsági csoport [az network nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

A hálózati biztonsági csoport több alapértelmezett szabályokat, amelyek közül az egyik letiltja az internetről érkező összes bejövő hozzáférést tartalmaz. Nyisson meg egy portot a virtuális gép SSH-hozzáférésének engedélyezése [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Hozzon létre egy hálózati adapter gyorsított hálózatkezelés

Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) paranccsal. Nyilvános IP-cím nem szükséges, ha nem a virtuális gép elérhető az internetről, de a cikkben ismertetett lépések végrehajtásához, szükség rá.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Hozzon létre egy hálózati adaptert a [az network nic létrehozása](/cli/azure/network/nic#az_network_nic_create) gyorsított hálózatkezeléssel engedélyezve van. A következő példában létrehozunk egy hálózati adapter nevű *myNic* a a *mySubnet* alhálózatának a *myVnet* virtuális hálózathoz, és hozzárendeli a  *myNetworkSecurityGroup* hálózati biztonsági csoportot a hálózati adapterhez:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Hozzon létre egy virtuális Gépet, és a hálózati adapter csatolása
A virtuális gép létrehozásakor adja meg a hálózati adapter segítségével létrehozott `--nics`. Válasszon ki egy mérete és a felsorolt terjesztési [Linuxos gyorsított hálózatkezelést](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* a UbuntuLTS-rendszerkép, és amely támogatja a gyorsított hálózatkezelés (*Standard_DS4_v2*):

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

Az összes Virtuálisgép-méretek és jellemzői, lásd: [Linux Virtuálisgép-méretek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

A virtuális gép létrehozása után az alábbi példa kimenetében hasonló kimenetet ad vissza. Jegyezze fel a **publicIpAddress** értékét. Ezzel a címmel eléri a virtuális Gépet, a későbbi lépésekben.

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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Győződjön meg arról, hogy engedélyezve van-e a gyorsított hálózatkezelés

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le `<your-public-ip-address>` a nyilvános IP-cím rendelve a virtuális gép létrehozott, és cserélje le *azureuser* eltérő értékeket használatakor `--admin-username` a virtuális gép létrehozásakor.

```bash
ssh azureuser@<your-public-ip-address>
```

Adja meg a Bash felületen `uname -r` , és győződjön meg arról, hogy a kernel verziója egyet a következő verziójú vagy újabb:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Erősítse meg a Mellanox VF eszköz közvetlenül csatlakozik a virtuális Gépet a `lspci` parancsot. A visszaadott kimenetet az alábbi kimenet hasonlít:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Kérdezze a VF (virtuális függvény) tevékenységre a `ethtool -S eth0 | grep vf_` parancsot. Ha megjelenik a kimeneti kimeneti engedélyezve van a gyorsított hálózatkezelés és használatához a következő mintához hasonló.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Gyorsított hálózatkezelés engedélyezve van a virtuális géphez.

## <a name="enable-accelerated-networking-on-existing-vms"></a>A meglévő virtuális gépek gyorsított hálózatkezelés engedélyezéséhez
Létrehozott egy virtuális gép gyorsított hálózatkezelés nélkül, célszerű lehet engedélyezni ezt a funkciót egy meglévő virtuális Gépet.  A virtuális gép támogatnia kell a gyorsított hálózatkezelés az alábbi előfeltételek is fent leírt feltételek:

* A virtuális Gépnek kell lennie egy támogatott méret gyorsított hálózatkezelés
* A virtuális Gépet egy támogatott Azure Gallery-image (és a Linux-Kernelverzió) kell lennie.
* Rendelkezésre állási csoportban lévő összes virtuális gép vagy VMSS kell leállítva és felszabadítva bármely NIC gyorsított hálózatkezelés engedélyezése előtt

### <a name="individual-vms--vms-in-an-availability-set"></a>Az egyes virtuális gépek és virtuális gépek a rendelkezésre állási beállítása
Először állítsa le vagy szabadítsa fel a virtuális gép vagy, ha a rendelkezésre állási csoport, a csoport összes virtuális gépen:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Fontos, kérjük vegye figyelembe, hogy ha a virtuális gép külön-külön, egy rendelkezésre állási csoportban, akkor csak nélkül hozták létre kell az egyes virtuális gép gyorsított hálózatkezelés engedélyezéséhez állítsa le vagy szabadítsa fel.  Ha a gép egy rendelkezésre állási csoport létrehozásakor, a rendelkezésre állási csoportban található összes virtuális gép kell leállítva és felszabadítva lehet bármelyik hálózati adapter gyorsított hálózatkezelés engedélyezése előtt. 

Leállítását követően engedélyezze a hálózati adapteren a virtuális gép gyorsított Hálózatkezelés:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Indítsa újra a virtuális Gépet, vagy ha egy rendelkezésre állási csoportban, a csoport összes virtuális gépen, és győződjön meg arról, hogy engedélyezve van-e a gyorsított Hálózatkezelés: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS kissé eltérő, de a következő ugyanabban a munkafolyamatban.  Először állítsa le a virtuális gépek:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Miután a virtuális gépek leállnak, frissítse a gyorsított hálózatkezelés tulajdonság alatt a hálózati adapter:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Vegye figyelembe, hogy egy VMSS a virtuális gép olyan frissítések, amelyek a alkalmazni a frissítéseket az három különböző beállításokkal, automatikus, működés közbeni és manuális rendelkezik.  Ebben az útmutatóban a szabályzat automatikusra van beállítva, hogy a VMSS kiesik a módosítások újraindítás után azonnal.  Nastavit nA automatikus úgy, hogy a módosítások azonnal mértékének: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Végezetül indítsa újra a VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Egyszer, indítsa újra, várjon, amíg befejeződik a frissítéseket, de ha befejeződött, a VF jelenik meg a virtuális Gépen belül.  (Ellenőrizze, hogy egy támogatott operációs rendszer és a virtuális gép mérete használ.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Gyorsított hálózatkezelésű meglévő virtuális gépek átméretezése

Virtuális gépek gyorsított hálózatkezelésű engedélyezve van, amely támogatja a gyorsított hálózatkezelés virtuális gépek csak átméretezhetők.  

Egy Virtuálisgép-példányhoz, amely nem támogatja a gyorsított hálózatkezelés az átméretezés használatával nem méretezhetők át egy virtuális Gépet a gyorsított hálózatkezelés engedélyezett.  Ehelyett egy ilyen virtuális gépek átméretezése: 

* A virtuális gép leállítása vagy felszabadítási vagy egy rendelkezésre állási csoport/VMSS, ha a Leállítás/szabadítsa fel a set/VMSS összes virtuális gépen.
* Gyorsított hálózatkezelés le kell tiltani a hálózati adapteren a virtuális gép vagy egy rendelkezésre állási készlet/VMSS, a set/VMSS minden virtuális gépen belül.
* Gyorsított hálózatkezelés le van tiltva, miután a virtuális gép vagy rendelkezésre állási csoport/VMSS áthelyezhetők az új méret, amely nem támogatja a gyorsított hálózatkezelés és újraindul.  

