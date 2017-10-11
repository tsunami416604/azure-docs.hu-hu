---
title: "Virtuális gép (klasszikus) létrehozása a PowerShell segítségével több hálózati adapterrel rendelkező |} Microsoft Docs"
description: "Megtudhatja, hogyan hozza létre és konfigurálja a virtuális gépek több hálózati adapterrel, PowerShell-lel rendelkező."
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a1a3952c-2dcc-4977-bd7a-52d623c1fb07
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 68ccc1cac22e593b099729fe68c6bee63df44d9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-classic-with-multiple-nics"></a>Több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása
Virtuális gépek (VM) létrehozása az Azure-ban, és csatlakoztassa a virtuális gépek mindegyikének több hálózati adapterek (NIC). Több hálózati adapter áll számos hálózati virtuális készülékeket, például az alkalmazások biztosításán és WAN-optimalizálást megoldások követelményeit. Több hálózati adapterrel is közötti hálózati forgalom elkülönítést biztosítani.

![Több hálózati adapter a virtuális gép](./media/virtual-networks-multiple-nics/IC757773.png)

Az ábrán látható virtuális gép három hálózati adaptert, és csatlakoznak egy másik alhálózat.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager.

* Internet felé néző VIP (klasszikus üzembe helyezés) csak akkor támogatott a "alapértelmezett" adapteren zajlik. Nincs a csak egy VIP – IP-címét az alapértelmezett hálózati adaptert.
* Ilyenkor (klasszikus üzembe helyezés) példány szint nyilvános IP (LPIP) címek nem támogatottak a több hálózati adapter virtuális gépeket.
* A hálózati adapterek sorrendje a virtuális gépen belül véletlenszerű, és az Azure-infrastruktúra frissítései során is változhat. Azonban az IP-címeket, és a megfelelő ethernet MAC címek lesz változatlan marad. Tegyük fel például, **Eth1** ; 10.1.0.100 IP-cím és MAC-cím 00-0D-3A-B0-39-0D tartalmaz, az Azure infrastruktúra-frissítési és újraindítás után azt módosítani: **Eth2**, de IP- és MAC párosítás változatlan marad. Ha újraindításra az ügyfél által kezdeményezett, a hálózati adapter rendelés változatlan marad.
* A cím az egyes hálózati adapterek minden egyes virtuális gépen kell lennie az alhálózat, a egyetlen virtuális gép több hálózati adapter egyes hozzárendelhetők legyenek címek, amelyek ugyanazon az alhálózaton.
* A Virtuálisgép-méretet, amelyet létrehozhat egy virtuális gép hálózati adapterek számát határozza meg. Hivatkozás a [Windows Server](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) virtuális gép méretének annak meghatározásához, hány hálózati ADAPTERT támogat minden egyes Virtuálisgép-méretet. 

## <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportokkal (NSG-k)
Egy erőforrás-kezelő telepítése esetén a a hálózati adapterek, a virtuális gép is lehet társítani, a hálózati biztonsági csoport (NSG), például minden hálózati adaptert egy virtuális gépen, amelyen engedélyezve van több hálózati adapter. Ha egy hálózati adapter hozzá van rendelve egy címet, ahol az alhálózaton társítva van egy NSG-t egy alhálózaton belül, majd a az alhálózat NSG is alkalmazni kell a hálózati adaptert. Alhálózatok társítása NSG-ket, mellett is társíthat egy hálózati adapter egy NSG.

Ha egy alhálózat társítva egy NSG-t, és egy hálózati adapter belül alhálózaton külön-külön társítva van egy NSG-t, a társított NSG-szabályok vonatkoznak a **flow-rendelési** a átadta-e virtuális gépbe vagy onnan a hálózati forgalom irányát megfelelően:

* **Bejövő forgalom** először áthaladó amelynek célja a szóban forgó hálózati adapter az alhálózat, időt. az alhálózat NSG-szabályok előtt a hálózati adapter történő továbbításához, majd a hálózati adapter NSG-szabályok váltanak.
* **Kimenő forgalom** amelyek forrása a hálózati adapter a a hálózati időt. a hálózati adapter NSG-szabályok előtt áthaladó az alhálózatot, majd időt. az alhálózat NSG-szabályok első out zajlik.

További információ [hálózati biztonsági csoportok](virtual-networks-nsg.md) és alhálózatok, a virtuális gépek és a hálózati adapter társítását alkalmazásának módja alapján...

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>A klasszikus üzembe helyezési egy több hálózati adapter virtuális Gépre kiterjedő konfigurálása
Az alábbi utasítások segítségével hozhat létre egy több hálózati adapter virtuális gép 3 hálózati adaptert tartalmazó: az alapértelmezett hálózati adapter és két további hálózati adapterek. A konfigurálás lépéseinek végrehajtásához hozza létre a virtuális gépek, a szolgáltatás konfigurációs fájl töredéke alábbi megfelelően kell konfigurálni:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


A példában a PowerShell-parancsok futtatása előtt kell a következő előfeltételek teljesülését.

* Azure-előfizetés.
* A konfigurált virtuális hálózati. Lásd: [virtuális hálózat áttekintése](virtual-networks-overview.md) Vnetek további információt.
* Az Azure PowerShell legújabb verziójának letöltése, és telepítve. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Több hálózati adapterrel rendelkező virtuális gép létrehozása, az alábbi lépésekkel egy PowerShell-munkameneten belül minden parancs beírásával:

1. Válassza ki a Virtuálisgép-lemezkép Azure VM-lemezkép gyűjteményből. Vegye figyelembe, hogy a képek módosulnak, és régiónként elérhetők. Az alábbi példában megadott lemezkép módosítása vagy előfordulhat, hogy nem a régióban kell, ezért ügyeljen arra, hogy adja meg a lemezképet kell.

    ```powershell
    $image = Get-AzureVMImage `
    -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
    ```

2. Hozzon létre egy Virtuálisgép-konfiguráció.

    ```powershell
    $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
    -Image $image.ImageName –AvailabilitySetName "MyAVSet"
    ```

3. Az alapértelmezett rendszergazdai bejelentkezés létrehozása.

    ```powershell
    Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
    -Password "<YourAdminPassword>"
    ```

4. További hálózati adapterek hozzáadása a Virtuálisgép-konfigurációhoz.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
    -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
    Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
    -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
    ```

5. Adja meg az alhálózatot és IP-címet az alapértelmezett hálózati adaptert.

    ```powershell
    Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
    Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
    ```

6. Hozzon létre a virtuális Gépet a virtuális hálózat.

    ```powershell
    New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm
    ```

    > [!NOTE]
    > Az itt megadott virtuális hálózat már léteznie kell (ahogy azt az Előfeltételek). Az alábbi példa meghatározza nevű virtuális hálózat **MultiNIC-VNet**.
    >

## <a name="limitations"></a>Korlátozások
A következő korlátozások vonatkoznak, több hálózati adapter használata esetén:

* Több hálózati adapterrel rendelkező virtuális gépeket az Azure virtuális hálózatokról (Vnetekről) kell létrehozni. Nem-virtuális hálózat virtuális gépek több hálózati adapter nem állítható be.
* Minden virtuális gép rendelkezésre állási csoportba szeretné használni, vagy több hálózati adapterrel, vagy egy egyetlen hálózati adaptert. Nem lehet több hálózati adapter virtuális gépek és egyetlen, a hálózati adapter virtuális gépek rendelkezésre állási csoportok belül. Ugyanazok a szabályok vonatkoznak a virtuális gépek felhőszolgáltatásban. Több hálózati adapter virtuális gépekhez, azok megnyitásáig nem szükséges hálózati adapterek, azonos számú mindaddig, amíg minden rendelkeznek legalább két.
* Virtuális gép egyetlen hálózati adapter és több hálózati adapter (és fordítva) nem állítható be, ha telepítve van, törlésével és ismételt létrehozása nélkül.

## <a name="secondary-nics-access-to-other-subnets"></a>Másodlagos hálózati adapterek hozzáférést más alhálózatok
Alapértelmezés szerint a egy alapértelmezett átjáró, amely miatt a forgalom áramlását az másodlagos hálózati adaptereken kell lennie ugyanazon az alhálózaton belül korlátozza nem másodlagos hálózati adapter lesz konfigurálva. Ha a felhasználók a saját alhálózati kívül ügyfélcsatornája másodlagos hálózati adapterek engedélyezni kívánja, akkor kell bejegyzés hozzáadása az átjáró konfigurálásához, az alább ismertetett-útválasztási táblázatához.

> [!NOTE]
> A virtuális gépeket létrehozni, mielőtt a 2015. július lehet a hálózati adapterek összes beállított alapértelmezett átjárót. Az alapértelmezett átjáró, a másodlagos hálózati adapter nem távolítja el, a virtuális gép újraindítása. Az operációs rendszerek, Linux, például a gyenge állomás útválasztási modellt használó internetkapcsolat különböző hálózati adapterein használatakor a bemenő és kimenő forgalom meghibásodásához vezethet.
> 

### <a name="configure-windows-vms"></a>Windows virtuális gépek
Tegyük fel, hogy két hálózati adapterrel rendelkező virtuális gép Windows az alábbiak szerint:

* Elsődleges hálózati adapter IP-cím: 192.168.1.4
* Másodlagos hálózati adapter IP-cím: 192.168.2.5

A virtuális gép az IPv4-alapú útválasztási táblázatot néz ki:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Figyelje meg, hogy az alapértelmezett útvonalat (0.0.0.0) csak érhető el az elsődleges hálózati adapternek. Csak akkor férhessenek hozzá az erőforrásokhoz kívül az alhálózat a másodlagos hálózati adapter, az alább látható módon:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

A másodlagos hálózati adapter által az alapértelmezett útvonal hozzáadásához kövesse az alábbi lépéseket:

1. Egy parancssorból futtassa a parancsot az alábbi indexszámát azonosítja a másodlagos hálózati adapter:
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. Figyelje meg, a második bejegyzés a táblában (a példában) 27 az indexet.
3. A parancssorból futtassa a **útvonal hozzáadása** parancsot a lent látható módon. Ebben a példában meg 192.168.2.1 alapértelmezett átjáróként a másodlagos hálózati adapter:
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. A kapcsolat tesztelése, lépjen vissza a parancssort, és próbálja meg Pingelje meg egy másik alhálózat a másodlagos hálózati adapter látható egész elemeként eh az alábbi példa:
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. Alább látható módon is ellenőrizheti az útválasztási táblázatot az újonnan hozzáadott útvonal kereséséhez:
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Linux virtuális gépek
Linux virtuális gépekhez mivel az alapértelmezett viselkedés használ gyenge állomás útválasztási, azt javasoljuk, hogy a másodlagos hálózati adapterek forgalom csak az ugyanazon alhálózaton belüli korlátozódnak. Azonban ha bizonyos esetekben az alhálózat kívüli kapcsolatot igényelnek, felhasználók engedélyeznie kell a csoportházirend-alapú útválasztási annak érdekében, hogy a bemenő és kimenő forgalom használja-e az azonos hálózati adaptert.

## <a name="next-steps"></a>Következő lépések
* Telepítése [MultiNIC virtuális gépeket a 2-rétegbeli alkalmazás esetén a Resource Manager-telepítés a](virtual-network-deploy-multinic-arm-template.md).
* Telepítése [MultiNIC virtuális gépeket a 2-rétegbeli alkalmazás esetén a klasszikus üzembe helyezési a](virtual-network-deploy-multinic-classic-ps.md).

