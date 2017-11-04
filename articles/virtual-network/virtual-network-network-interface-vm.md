---
title: "Adja hozzá a hálózati kapcsolatokat, vagy távolítsa el az Azure virtuális gépek |} Microsoft Docs"
description: "Útmutató a hálózati adapterek hozzáadása vagy eltávolítása a hálózati adapterek virtuális gépekről."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 7df1dfbea8c985907d5330819dc1e7bf1578aafa
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Adja hozzá a hálózati kapcsolatokat, vagy távolítsa el a virtuális gépek

Útmutató: virtuális gép létrehozásakor adja hozzá a meglévő hálózati illesztő, vagy adja hozzá, vagy távolítsa el a hálózati adapterek egy meglévő virtuális gép leállított (felszabadított) állapotában. Egy adott hálózati csatoló lehetővé teszi, hogy egy Azure virtuális gép (VM) Internet, Azure és a helyszíni erőforrások folytatott kommunikációhoz. A virtuális gépek is egy vagy több hálózati illesztőre van szükség. 

Ha szeretne hozzáadni, módosítása vagy eltávolítása IP-címek a hálózati illesztő, olvassa el a [hálózati illesztő IP-címeinek kezelése](virtual-network-network-interface-addresses.md) cikk. Ha meg kell létrehozni, módosítási vagy törlési hálózati adapterek, olvassa el a [kezelése a hálózati adapterek](virtual-network-network-interface.md) cikk.

## <a name="before"></a>Előkészületek

Esetlegesen szakasz ebben a cikkben szereplő lépésekkel befejezése előtt hajtsa végre a következő feladatokat:

- Ismerje meg, hány hálózati adapterekkel kapcsolatos minden egyes Linux és a Windows virtuális gép mérete alapján támogatja a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének cikkeket.
- Jelentkezzen be a Azure [portal](https://portal.azure.com), az Azure parancssori felület (CLI), vagy az Azure PowerShell használata az Azure-fiók. Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha a feladat-ebben a cikkben a PowerShell-parancsokkal [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a legfrissebb telepítve az Azure PowerShell-parancsmagjaival. Ha segítséget szeretne kérni a PowerShell-parancsaihoz, valamint példákkal, írja be a `get-help <command> -full`.
- Ha ebben a cikkben a feladatokat az Azure parancssori felület (CLI) parancsokkal [telepítése és konfigurálása az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a telepített Azure CLI legújabb verziója. Segítség kérése parancssori felület parancsait, írja be a következőt `az <command> --help`. Ahelyett, hogy a parancssori felület és a szükséges előfeltételek telepítése, az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj **> _** gomb tetején a [portal](https://portal.azure.com).

## <a name="about"></a>Hálózati felületek és virtuális gépek

Hozzáadhat (csatolása) egy virtuális géphez a virtuális Gépet, a megadott hálózati kapcsolat létrehozásakor meglévő hálózati illesztő jelenleg nincs csatolva másik virtuális gép. A hálózati adaptert hozzáadása vagy eltávolítása (leválasztani) a hálózati adaptert egy meglévő virtuális gépről, megadva a virtuális gép leállított (felszabadított) állapotban van. Ha egy virtuális Gépet az Azure portál használatával hoz létre, a portál létrehozza a hálózati adaptert, alapértelmezett beállításokkal. A portál nem engedélyezi, hogy:

- Adja meg, amikor a virtuális gép létrehozása meglévő hálózati illesztő
- Több hálózati adapterrel rendelkező virtuális gép létrehozása
- Adjon meg egy nevet a hálózati adapter (a portál létrehoz egyet a hálózati illesztő alapértelmezett néven)

Azure PowerShell vagy a CLI hozhat létre a hálózati adaptert vagy virtuális gép nem használható a portál az előző attribútumokkal. Az alábbi szakaszok a feladatok befejezése előtt vegye figyelembe a következő korlátozások és viselkedéshez:

- Az összes Virtuálisgép-méretek legalább két hálózati adapterrel, azonban az egyes Virtuálisgép-méretek támogatja a több mint két hálózati adapterrel. A múltban egyes Virtuálisgép-méretek csak egy hálózati adapterrel támogatott. Megtudhatja, hogy hány hálózati adapterek minden virtuális gép mérete támogatja, olvassa el a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének cikkeket. 
- A múltban hálózati illesztők csak nem kell hozzá, amely támogatja több hálózati adapterrel, és létre lettek hozva legalább két hálózati adapterrel rendelkező virtuális gépeket. Meg nem lehet hozzáadni a hálózati adaptert egy virtuális Gépet egy hálózati csatoló hoztak létre akkor is, ha a Virtuálisgép-méretet támogatja több hálózati adapterrel. Ezzel szemben csak eltávolítható hálózati adapterek legalább három hálózati felületek, a virtuális gép alapján, mert legalább két hálózati illesztők mindig kell hozniuk legalább két hálózati adapterrel létrehozott virtuális gépeket. Ezek a megkötések egyike sem már érvényesek. Ön most tetszőleges számú (akár a virtuális gép mérete által támogatott számmal) hálózati adapterrel rendelkező virtuális gép létrehozása és hozzáadhat és eltávolíthat a hálózati adapterek száma (a virtuális gép leállított (felszabadított) állapotában), mindaddig, amíg a virtuális gép mindig legalább egy hálózati adapterrel rendelkezik.
- Alapértelmezés szerint az első hálózati adapter egy virtuális gépre van definiálva, a *elsődleges* hálózati illesztőt. A virtuális gép más hálózati felületek *másodlagos* hálózati illesztőt.
- Elsődleges hálózati illesztők rendeli hozzá egy alapértelmezett átjáró Azure DHCP-kiszolgálók, a teljesség másodlagos hálózati adapterrel. Másodlagos hálózati adapter nem rendelkezik alapértelmezett átjárót, mivel azok nem kommunikálhatnak kívül az alhálózati erőforrások alapértelmezés szerint. Engedélyezheti a másodlagos hálózati adapterrel való kommunikációhoz az alhálózati kívüli erőforrások [egy virtuális gép operációs rendszerben több hálózati adapter az útválasztási](#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces).
- Alapértelmezés szerint minden kimenő forgalom a virtuális gép által kiküldött a hozzárendelt elsődleges IP-konfigurációja az elsődleges hálózati adapter IP-címet. Szabályozhatja, hogy melyik IP-címet a kimenő forgalom a virtuális gép operációs rendszerben használatos, de alapértelmezés szerint az elsődleges hálózati kapcsolaton keresztül.
- A múltban belül az azonos rendelkezésre állási csoport összes virtuális gép volt egy egyetlen vagy több hálózati illesztőre van szükség. Több hálózati adapterrel rendelkező virtuális gépek most létezhet az azonos rendelkezésre állási készletbe, akár a virtuális gép mérete által támogatott számra. A rendelkezésre állási készlet létrehozásakor azt, ha csak egy virtuális gép adhat hozzá. Rendelkezésre állási csoportokra vonatkozó további információkért olvassa el a [az Azure virtuális gépek rendelkezésre állásának kezelése](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) cikk.
- Amíg az azonos virtuális gép hálózati adapterek különböző alhálózatokon történik egy Vneten belül lehet csatlakoztatni, a hálózati adapterek összes kapcsolódnia kell ugyanazt a virtuális hálózatot.
- Egy Azure Load Balancer háttér címkészletet bármely IP-konfiguráció bármely elsődleges vagy másodlagos hálózati adapter IP-címeket adhat hozzá. Korábban csak az elsődleges IP-címet az elsődleges hálózati illesztő sikerült felvenni egy háttér címkészletet. IP-címek és konfigurációkkal kapcsolatos további tudnivalókért olvassa el a [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md) cikk.
- A virtuális gép törlése nem érinti a hálózati adapterek, amelyek kapcsolódnak. A virtuális gép törlésekor a hálózati adapterek le vannak választva a virtuális gépről. A hálózati adapterek különböző virtuális gépek hozzáadása, vagy törölje őket.
- Ha egy hálózati illesztőnek egy titkos IPv6-címet kap, csatolhat azt egy virtuális Gépet a virtuális gép létrehozásakor. Egy virtuális géphez hozzárendelt IPv6-címmel rendelkező hálózati illesztő nem lehet csatolni a virtuális gép létrehozása után. Ha egy virtuális gép létrehozásakor csatlakoztat egy adott hálózati csatoló hozzárendelt saját IPv6-címmel, csak csatolhat, hogy a hálózati illesztő a virtuális gép, függetlenül attól, hogy hány hálózati illesztők támogatja a Virtuálisgép-méretet. Lásd: [hálózati illesztő IP-címek](virtual-network-network-interface-addresses.md) további információt az IP-címek hozzárendelése a hálózati adapterek.

## <a name="vm-create"></a>Meglévő hálózati adapterek hozzáadása egy új virtuális géphez

Amikor létrehoz egy virtuális Gépet a portálon keresztül, a portál létrehoz egy adott hálózati csatoló alapértelmezett beállításokkal, és csatolja a virtuális gép meg. Nem a meglévő hálózati adapterek hozzáadása egy új virtuális Gépet, vagy hozzon létre egy virtuális gép több hálózati adapterrel, az Azure portál használatával. Mindkét a parancssori felületen vagy a PowerShell használatával teheti meg. Tetszőleges számú hálózati illesztőt a virtuális gép támogatja a Virtuálisgép-méretet hoz létre, adhat hozzá. Több hány hálózati adapterekkel kapcsolatos minden egyes Virtuálisgép-méretet támogatja, olvassa el a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének cikkeket. A hálózati adapterek ad hozzá egy virtuális gép jelenleg egy másik virtuális géphez nem lehet csatolni. Hálózati illesztők létrehozásával kapcsolatos további tudnivalókért olvassa el a [kezelése a hálózati adapterek](virtual-network-network-interface.md#create-a-network-interface) cikk.

### <a name="routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces"></a>Egy virtuális gép operációs rendszerben több hálózati adapter az útválasztási

Azure rendeli hozzá az első (elsődleges) hálózati illesztő a virtuális géphez csatolt alapértelmezett átjárót. Azure nem rendel hozzá egy virtuális géphez csatolt további (másodlagos) hálózati adapterek alapértelmezett átjárót. Emiatt nem sikerül kommunikálni az alhálózatot, amely a másodlagos hálózati adaptert, alapértelmezés szerint kívüli erőforrásokhoz. Másodlagos hálózati adapterrel, azonban kommunikálhat az alhálózati kívüli erőforrásokhoz, ha a kommunikáció engedélyezése az operációs rendszer különböző operációs rendszereken.

### <a name="windows"></a>Windows

Kövesse az alábbi lépéseket a Windows parancssorba:

1. Futtassa a `route print` parancsot, amely a kimenetet visszaadja a kimeneti két csatlakoztatott hálózati adapterrel rendelkező virtuális gép a következőhöz hasonló:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Ebben a példában **Microsoft Hyper-V hálózati Adapter 4** (7) objektumfelület. a másodlagos hálózati adapter, amely nincs hozzárendelve alapértelmezett átjárót.

2. Egy parancssorból futtassa a `ipconfig` parancsot mely IP-cím hozzá van rendelve a másodlagos hálózati illesztő megtekintéséhez. Ebben a példában 192.168.2.4 felület 7 van hozzárendelve. A másodlagos hálózati adapter nincs alapértelmezett átjárójának címét adja vissza.

3. A cím az alhálózat a másodlagos hálózati adapter az alhálózat átjáró kívül szánt forgalom irányításához, futtassa a következő parancsot:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Az átjáró cím az alhálózat első IP-címét (ikonra.1 végződése) definiálva az alhálózat címtartománya. Ha nem szeretné kívül az alhálózat összes forgalmat, hozzáadhatja egyéni útvonalak adott helyre, helyette. Ha csak a másodlagos hálózati illesztő forgalom átirányítása a 192.168.3.0 például hálózati, írja be a parancsot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Ellenőrizze az erőforrás a 192.168.3.0 sikeres kommunikáció hálózati, írja be például a következő parancsot a ping 192.168.3.4 7 (192.168.2.4) felhasználói felületén:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Nyissa meg az ICMP, még a következő paranccsal pingelés az eszköz a Windows tűzfalon keresztül szeretne:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Erősítse meg a hozzáadott útvonal az útválasztási táblázatban, írja be a következőt a `route print` parancsot, amely kimenet az alábbihoz hasonló:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Az útvonal meglétüket *192.168.1.1* alatt **átjáró**, a útvonalat, amely alapértelmezés szerint az elsődleges hálózati adapter van-e. Az útvonal *192.168.2.1* alatt **átjáró**, a hozzáadott útvonalat.

### <a name="linux"></a>Linux

Mivel az alapértelmezett viselkedés gyenge állomás útválasztási használ, érdemes másodlagos hálózati adapter forgalma ugyanazon alhálózaton lévő erőforrások közötti korlátozása. Ha az másodlagos hálózati adapterrel kommunikációs kívül az alhálózat van szüksége, útválasztási szabályokat, amelyek lehetővé teszik a virtuális gép küldésére és fogadására a forgalmat egy adott hálózati illesztőn keresztül kell létrehoznia. Ellenkező esetben forgalom tartozó eth1, például nem tudja feldolgozni megfelelően a meghatározott alapértelmezett útvonalat. Konfigurálja az útválasztási szabályokat, lásd: [a több hálózati adapter konfigurálása Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics).

> [!WARNING]
> Ha egy hálózati illesztőnek egy titkos IPv6-címet kap, csak adhat a hálózati illesztő a virtuális gépet a virtuális gép létrehozásakor. A virtuális gép létrehozásakor vagy után a virtuális gép létrehozott, egy IPv6-cím hozzá van rendelve a hálózati adaptert egy virtuális géphez csatolt lehető leghosszabbak egynél több hálózati adapter nem csatolható a virtuális gép. Lásd: [hálózati illesztő IP-címek](virtual-network-network-interface-addresses.md) további információt az IP-címek hozzárendelése a hálózati adapterek.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az virtuális gép létrehozása](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Meglévő hálózati illesztő hozzáadása egy meglévő virtuális Gépen

Tetszőleges számú hálózati illesztőt egy virtuális géphez a virtuális gép méretétől, hálózati illesztőre visszaigazolása támogatja adhat hozzá. Megtudhatja, hogy hány hálózati adapterek minden virtuális gép mérete támogatja, olvassa el a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének cikkeket. Hozzá szeretné adni a hálózati adaptert a virtuális támogatnia kell a számát, a hálózati adapterek hozzáadását tervezi, és a leállított (felszabadított) állapotban kell lennie. A hozzáadni kívánt hálózati adapterek jelenleg egy másik virtuális géphez nem lehet csatolni. Hálózati adapterek nem adható hozzá egy meglévő virtuális gépre az Azure portál használatával. Hálózati adapterek hozzáadása egy meglévő virtuális gépre, a parancssori felületen vagy a PowerShell kell használnia. 

Azure rendeli hozzá az első (elsődleges) hálózati illesztő a virtuális géphez csatolt alapértelmezett átjárót. Azure nem rendel hozzá egy virtuális géphez csatolt további (másodlagos) hálózati adapterek alapértelmezett átjárót. Emiatt nem sikerül kommunikálni az alhálózatot, amely a másodlagos hálózati adaptert, alapértelmezés szerint kívüli erőforrásokhoz. Másodlagos hálózati adapterrel, azonban kommunikálhat a alhálózati kívüli erőforrásokhoz. Ha szükséges, hogy a másodlagos hálózati adapterek kívül az alhálózati erőforrásokkal folytatott kommunikáció, lásd: [egy virtuális gép operációs rendszerben több hálózati adapter az útválasztási](#routing-within-a virtual-machine-operating-system-with-multiple-network-interfaces).

> [!WARNING]
> Ha egy adott hálózati csatoló saját IPv6-címnek rendelve, azt nem lehet hozzáadni egy meglévő virtuális gépet. Csak hozzáadhat hozzárendelt saját IPv6-címmel rendelkező hálózati illesztő a virtuális gép egy virtuális gép létrehozásakor. Lásd: [hálózati illesztő IP-címek](virtual-network-network-interface-addresses.md) további információt az IP-címek hozzárendelése a hálózati adapterek.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm hálózati adapter hozzáadása](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (hivatkozás) vagy [részletes lépései](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Adja hozzá AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépései](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a>A virtuális gépek nézet hálózati illesztők

Megtekintheti a jelenleg a virtuális Géphez csatlakozik, mindegyik hálózati interfész konfigurációjával kapcsolatos további hálózati adapterek, és mindegyik hálózati interfész hozzárendelt IP-címeket. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózat közreműködő szerepkört az előfizetés. Szerepkörök hozzárendelése a fiókokhoz kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *virtuális gépek*. Ha **virtuális gépek** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **virtuális gépek** panel, amelyen megjelenik, kattintson a virtuális hálózati adaptert használjon a megtekinteni kívánt nevét.
4. Az a **beállítások** kijelölésre a virtuális gépek paneljét, hogy a virtuális gép megjelenik, kattintson a **hálózati**. Hálózati illesztő beállításai és módosítsa azokat, olvassa el a [kezelése a hálózati adapterek](virtual-network-network-interface.md) cikk. Hozzáadása, módosítása, vagy távolítsa el a hálózati illesztő rendelt IP-címek című témakörben olvashat [kezelése IP-címek](virtual-network-network-interface-addresses.md).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm megjelenítése](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>Távolítsa el a hálózati adaptert egy virtuális gépből

A kívánt eltávolítása (vagy leválasztása) a hálózati adaptert a virtuális gép leállított (felszabadított) állapotban kell lennie, és kell jelenleg rendelkezik, legalább két hálózati adaptere csatlakozik. Eltávolíthatja a hálózati csatolóhoz, de a virtuális gép mindig rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható. Ha eltávolítja az elsődleges hálózati illesztő, Azure rendel hozzá az elsődleges attribútum a hálózati adaptert a virtuális géphez csatlakozik, a leghosszabb. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózat közreműködő szerepkört az előfizetés. Szerepkörök hozzárendelése a fiókokhoz kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *virtuális gépek*. Ha **virtuális gépek** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **virtuális gépek** panel, amelyen megjelenik, kattintson a el kívánja távolítani a hálózati adaptert a virtuális gép nevére.
4. Az a **beállítások** kijelölésre a virtuális gépek paneljét, hogy a virtuális gép megjelenik, kattintson a **hálózati**. Hálózati illesztő beállításai és módosítsa azokat, olvassa el a [kezelése a hálózati adapterek](virtual-network-network-interface.md) cikk. Hozzáadása, módosítása, vagy távolítsa el a hálózati illesztő rendelt IP-címek című témakörben olvashat [kezelése IP-címek](virtual-network-network-interface-addresses.md).
5. Kattintson a **X leválasztani a hálózati illesztő**.
6. Válassza ki a hálózati illesztő leválasztása a legördülő listából, majd kattintson a kívánt **OK**.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[Távolítsa el a virtuális gép hálózati az](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (hivatkozás) vagy [részletes lépései](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépései](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Következő lépések
Hozzon létre egy virtuális gép több hálózati adapterrel vagy IP-címek, olvassa el a következő cikkeket:

**Parancsok**

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[Parancssori felület](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy hálózati adapter virtuális több IPv4-címekkel|[Parancssori felület](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy hálózati adapter virtuális magánhálózati IPv6-cím (mögött egy Azure Load Balancer)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
