---
title: Hozzon létre egy Azure virtuális gép gyorsított hálózatkezelés az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre linuxos virtuális gépet gyorsított hálózatkezelés engedélyezve.
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
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 05f8430efa31b39d49025fb8456108da229d3d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239826"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Linuxos virtuális gép létrehozása gyorsított hálózatkezeléssel az Azure CLI használatával

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Linux virtuális gépet (VM) gyorsított hálózattal. A Gyorsított hálózattal rendelkező Windows virtuális gép létrehozásáról a [Windows virtuális gép létrehozása gyorsított hálózattal](create-vm-accelerated-networking-powershell.md)című témakörben van. A gyorsított hálózatkezelés lehetővé teszi az egygyökérű I/O-virtualizációt (SR-IOV) a virtuális géphez, jelentősen javítva a hálózati teljesítményt. Ez a nagy teljesítményű elérési út megkerüli a gazdagép a datapath, csökkenti a késést, a vibráló és a CPU-kihasználtság, a támogatott virtuálisgép-típusok legigényesebb hálózati munkaterhelések használható. Az alábbi képen két virtuális gép közötti kommunikáció látható gyorsított hálózattal és anélkül:

![Összehasonlítás](./media/create-vm-accelerated-networking/accelerated-networking.png)

Gyorsított hálózatkezelés nélkül a virtuális gépbe be- és kifutó összes hálózati forgalomnak át kell haladnia a gazdagépen és a virtuális kapcsolón. A virtuális kapcsoló minden házirend-kényszerítést biztosít, például a hálózati biztonsági csoportokat, a hozzáférés-vezérlési listákat, az elkülönítést és a hálózati forgalomhoz való egyéb hálózati virtualizált szolgáltatásokat. Ha többet szeretne megtudni a virtuális kapcsolókról, olvassa el a [Hyper-V hálózati virtualizációs és virtuális](https://technet.microsoft.com/library/jj945275.aspx) kapcsolócikket.

A gyorsított hálózati kapcsolat, a hálózati forgalom érkezik a virtuális gép hálózati adapter (NIC), majd továbbítja a virtuális gép. A virtuális kapcsolóáltal alkalmazott összes hálózati házirend kivan töltve, és a hardverben is érvénybe lép. A hardveres házirend alkalmazása lehetővé teszi, hogy a hálózati adapter közvetlenül a virtuális gépre továbbítsa a hálózati forgalmat, megkerülve az állomást és a virtuális kapcsolót, miközben az állomásban alkalmazott összes házirendet fenntartja.

A gyorsított hálózatkezelés előnyei csak a virtuális gép, amely engedélyezve van. A legjobb eredmény érdekében ideális, ha ezt a funkciót legalább két, ugyanahhoz az Azure virtuális hálózathoz (VNet) csatlakoztatott virtuális gépen engedélyezi. Virtuális hálózatok közötti kommunikáció vagy a helyszíni csatlakozás, ez a szolgáltatás minimális hatással van az általános késésre.

## <a name="benefits"></a>Előnyök
* **Kisebb késleltetés / Magasabb csomagok másodpercenként (pps):** A virtuális kapcsoló eltávolítása a datapath eltávolítja az időt csomagok töltenek a gazdagép a házirend-feldolgozás, és növeli a csomagok számát, amelyek a virtuális gépen belül feldolgozható.
* **Csökkentett vibrátor:** A virtuális kapcsolók feldolgozása az alkalmazandó házirend mennyiségétől és a feldolgozást végző processzor terhelésétől függ. A házirend-kényszerítés hardverre való kiszervezése eltávolítja ezt a változékonyságot azáltal, hogy a csomagokat közvetlenül a virtuális gépnek kézbesíti, eltávolítja a gazdagép ről a virtuális gépre, valamint az összes szoftvermegszakítást és környezetkapcsolót.
* **Csökkent CPU-kihasználtság:** A virtuális kapcsoló megkerülése a gazdagépben kevesebb PROCESSZOR-kihasználtságot eredményez a hálózati forgalom feldolgozásához.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure Gallery-ből származó dobozból támogatottak: 
* **Ubuntu 14.04 a linux-azure kernellel**
* **Ubuntu 16.04 vagy újabb** 
* **SLES12 SP3 vagy újabb** 
* **RHEL 7.4 vagy újabb**
* **CentOS 7.4 vagy újabb**
* **CoreOS Linux**
* **Debian "Stretch" backport kernellel**
* **Oracle Linux 7.4 és újabb red hat kompatibilis kernel (RHCK)**
* **Oracle Linux 7.5 és újabb UEK 5-ös verzióval**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Korlátozások és korlátozások

### <a name="supported-vm-instances"></a>Támogatott virtuálisgép-példányok
Gyorsított hálózatkezelés támogatott a legtöbb általános célú és számítási optimalizált példány mérete2 vagy több vCPU-k.  Ezek a támogatott sorozatok a következők: D/DSv2 és F/Fs

A hyperthreading-et támogató példányokon a gyorsított hálózatkezelés 4 vagy több vCPU-val rendelkező virtuálisgép-példányokon támogatott. Támogatott sorozatok: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms és Ms/Mmsv2.

A virtuálisgép-példányokkal kapcsolatos további információkért tekintse meg a [Linux virtuális gép méreteit.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="custom-images"></a>Egyéni rendszerképek
Ha egyéni lemezképet használ, és a lemezkép támogatja az Accelerated Networking szolgáltatást, győződjön meg arról, hogy rendelkezik a Mellanox ConnectX-3 és a ConnectX-4 Lx NIC-ekkel való munkához az Azure-ban.

### <a name="regions"></a>Régiók
Az Azure minden nyilvános régiójában, valamint az Azure Government Clouds ban érhető el.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Gyorsított hálózatkezelés engedélyezése futó virtuális gépen
A támogatott virtuális gép mérete gyorsított hálózati engedélyezés nélkül csak akkor engedélyezve van a funkció, ha le van állítva, és felszabadított.  
### <a name="deployment-through-azure-resource-manager"></a>Telepítés az Azure Resource Manageren keresztül
A (klasszikus) virtuális gépek nem telepíthetők gyorsított hálózatkezeléssel.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Linuxos virtuális gép létrehozása az Azure Accelerated Networking segítségével
## <a name="portal-creation"></a>Portál létrehozása
Bár ez a cikk lépéseket tartalmaz egy virtuális gép létrehozásához gyorsított hálózatkezelés az Azure CLI használatával, az [Azure Portal on gyorsított hálózatkezeléssel is létrehozhat egy virtuális gépet.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Amikor virtuális gépet hoz létre a portálon, a **Virtuálisgép létrehozása** panelen válassza a **Hálózat** lapot.  Ezen a lapon lehetőség van a **gyorsított hálózatkezelésre.**  Ha [támogatott operációs rendszert](#supported-operating-systems) és virtuális [gépméretet](#supported-vm-instances)választott, ez a beállítás automatikusan "Be" állapotba kerül.  Ha nem, akkor feltölti a "Off" opciót gyorsított hálózat, és adja meg a felhasználónak egy ok, hogy miért nincs engedélyezve.   

* *Megjegyzés:* Csak a támogatott operációs rendszerek engedélyezhetők a portálon keresztül.  Ha egyéni lemezképet használ, és a lemezkép támogatja az accelerated networking, kérjük, hozza létre a virtuális gép cli vagy PowerShell használatával. 

A virtuális gép létrehozása után ellenőrizheti, hogy az Accelerated Networking engedélyezve van-e a Gyorsított hálózat kezelés engedélyezésének megerősítéséhez, [a Gyorsított hálózatkezelés engedélyezésének megerősítésére](#confirm-that-accelerated-networking-is-enabled)vonatkozó utasításokat követve.

## <a name="cli-creation"></a>ClI létrehozása
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Telepítse a legújabb [Azure CLI-t,](/cli/azure/install-azure-cli) és jelentkezzen be egy Azure-fiókba [az az login](/cli/azure/reference-index)használatával. A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *myResourceGroup*, *myNic*, és *myVm*.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *centralus* helyen:

```azurecli
az group create --name myResourceGroup --location centralus
```

Válasszon ki egy támogatott Linux régiót a [Linux gyorsított hálózatkezelésben.](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. A következő példa egy *myVnet* nevű virtuális hálózatot hoz létre egy alhálózattal:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create](/cli/azure/network/nsg)segítségével. A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

A hálózati biztonsági csoport számos alapértelmezett szabályt tartalmaz, amelyek közül az egyik letiltja az összes bejövő hozzáférést az internetről. Nyisson meg egy portot, hogy az SSH hozzáférhessen a virtuális géphez az [hálózati nsg szabály létrehozása:](/cli/azure/network/nsg/rule)

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Hálózati adapter létrehozása gyorsított hálózatkezeléssel

Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. Nyilvános IP-cím nem szükséges, ha nem tervezi a virtuális gép elérését az internetről, de a jelen cikkben leírt lépések végrehajtásához szükséges.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Hozzon létre egy hálózati [adaptert az az network nic létrehozásával,](/cli/azure/network/nic) amelynek engedélyezett a gyorsított hálózatkezelés. A következő példa létrehoz egy *myNic* nevű hálózati illesztőt a *myVnet* virtuális hálózat *mySubnet* alhálózatában, és a *myNetworkSecurityGroup* hálózati biztonsági csoportot társítja a hálózati adapterhez:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Virtuális gép létrehozása és a hálózati adapter csatolása
A virtuális gép létrehozásakor adja meg `--nics`a segítségével létrehozott hálózati adaptert. Válassza ki a linuxos gyorsított hálózatkezelésben felsorolt méretet és [disztribúciót.](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példa létrehoz egy *myVM* nevű virtuális gép az UbuntuLTS-lemezképpel és egy olyan mérettel, amely támogatja az accelerated networking *(Standard_DS4_v2)*

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

Az összes virtuális gép méretének és jellemzőjének listáját lásd: [Linux virtuális gép méretei.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

A virtuális gép létrehozása után a következő példa kimenethez hasonló kimenetet ad vissza. Jegyezze fel a **publicIpAddress** értékét. Ez a cím a virtuális gép további lépésekben való eléréséhez használható.

```output
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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Annak ellenőrzése, hogy engedélyezve van-e a gyorsított hálózatkezelés

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje `<your-public-ip-address>` le a létrehozott virtuális géphez rendelt nyilvános IP-címet, és cserélje `--admin-username` le *az azureusert,* ha a virtuális gép létrehozásakor más értéket használt.

```bash
ssh azureuser@<your-public-ip-address>
```

A Bash rendszerhéjból adja meg `uname -r` és erősítse meg, hogy a kernel verziója az alábbi vagy nagyobb verziók egyike-e:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Ellenőrizze, hogy a Mellanox VF eszköz ki `lspci` van téve a virtuális gép a parancsot. A visszaadott kimenet hasonló a következő kimenethez:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Ellenőrizze, hogy nincs-e tevékenység a `ethtool -S eth0 | grep vf_` VF (virtuális függvényen) a paranccsal. Ha a következő mintakimenethez hasonló kimenetet kap, a gyorsított hálózatkezelés engedélyezve van és működik.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Gyorsított hálózatkezelés most engedélyezve van a virtuális gép.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>A dinamikus kötés és a virtuális függvény visszavonásának kezelése 
Az alkalmazásoknak a virtuális gépben elérhető szintetikus hálózati adapteren kell keresztülfutniuk. Ha az alkalmazás fut közvetlenül a VF nic, nem fogadja az **összes** csomagot, amely a virtuális gép, mivel egyes csomagok jelennek meg a szintetikus felületen keresztül.
Ha egy alkalmazást a szintetikus hálózati adapteren keresztül futtat, garantálja, hogy az alkalmazás megkapja az **összes** hozzá szánt csomagot. Azt is gondoskodik arról, hogy az alkalmazás továbbra is fut, még akkor is, ha a VF visszavonásra kerül, amikor az állomás szervizelése. A szintetikus hálózati adapterhez kötelező érvényű alkalmazások **kötelező** követelményt jelentenek minden olyan alkalmazás számára, amely kihasználja a **gyorsított hálózatoláselőnyeit.**

## <a name="enable-accelerated-networking-on-existing-vms"></a>Gyorsított hálózatkezelés engedélyezése meglévő virtuális gépeken
Ha gyorsított hálózatkezelés nélkül hozott létre virtuális gép, ez a funkció egy meglévő virtuális gépen is engedélyezheti.  A virtuális gépnek támogatnia kell az accelerated networking-et a fent vázolt alábbi előfeltételek teljesítésével:

* A virtuális gépnek támogatott méretűnek kell lennie a gyorsított hálózatkezeléshez
* A virtuális gépnek egy támogatott Azure Gallery-lemezképnek (és Linux kernelverziónak) kell lennie.
* A rendelkezésre állási csoportban vagy a VMSS-ben lévő összes virtuális gépet le kell állítani/fel kell osztani, mielőtt bármilyen hálózati adapteren engedélyeznék a gyorsított hálózatkezelést.

### <a name="individual-vms--vms-in-an-availability-set"></a>Egyéni virtuális gépek & virtuális gépek egy rendelkezésre állási csoportban
A virtuális gép első leállítása/felszabadítása, vagy ha egy rendelkezésre állási csoport, a készlet összes virtuális gépe:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Fontos, vegye figyelembe, ha a virtuális gép külön-külön jött létre, rendelkezésre állási csoport nélkül, csak le kell állítania/felkell osztania az egyes virtuális gépek et a gyorsított hálózatkezelés engedélyezéséhez.  Ha a virtuális gép egy rendelkezésre állási csoport, a rendelkezésre állási csoportban található összes virtuális gépet le kell állítani/fel kell osztani, mielőtt a hálózati adapterek gyorsított hálózatba foglalását engedélyeznék. 

A leállított beállítás után engedélyezze a gyorsított hálózatkezelést a virtuális gép hálózati adapterén:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Indítsa újra a virtuális gépet, vagy ha egy rendelkezésre állási csoportban van, a készlet összes virtuális gépe, és ellenőrizze, hogy a gyorsított hálózatkezelés engedélyezve van-e: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
A VMSS kissé eltérő, de ugyanazt a munkafolyamatot követi.  Először állítsa le a virtuális gépeket:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

A virtuális gépek leállítása után frissítse a gyorsított hálózati tulajdonságot a hálózati adapter alatt:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Kérjük, vegye figyelembe, hogy a VMSS virtuális gép frissítéseket, amelyek a frissítéseket három különböző beállítások, automatikus, gördülő és kézi használatával.  Ezekben az utasításokban a házirend automatikus, így a VMSS azonnal felveszi a módosításokat az újraindítás után.  Állítsa be automatikusra, hogy a módosítások azonnal felvesd: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Végül indítsa újra a VMSS-t:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Miután újraindítja, várja meg, amíg a frissítések befejeződnek, de ha befejeződött, a VF jelenik meg a virtuális gép belsejében.  (Győződjön meg arról, hogy támogatott operációs rendszert és virtuális gépméretet használ.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Meglévő virtuális gépek átméretezése gyorsított hálózatkezeléssel

A gyorsított hálózatkezeléssel rendelkező virtuális gépek csak olyan virtuális gépekre méretezhetők át, amelyek támogatják az accelerated networking szolgáltatást.  

A gyorsított hálózatkezelés engedélyezve lévő virtuális gép nem méretezhető át olyan virtuálisgép-példányra, amely nem támogatja az újraméretezési művelet használatával végzett gyorsított hálózatkezelést.  Ehelyett az alábbi virtuális gépek egyikének átméretezéséhez: 

* Állítsa le/szabadítsa fel a virtuális gépet, vagy ha egy rendelkezésre állási csoportban/VMSS, állítsa le/szabadítsa fel az összes virtuális gép a készlet/VMSS.
* Gyorsított hálózatletiltást kell tiltani a hálózati adapteren a virtuális gép, vagy ha egy rendelkezésre állási készlet/VMSS, a készlet/VMSS összes virtuális gép.
* Miután a gyorsított hálózatkezelés le van tiltva, a virtuális gép/rendelkezésre állási készlet/VMSS áthelyezhető egy új méretre, amely nem támogatja az accelerated networking és újraindul.  

