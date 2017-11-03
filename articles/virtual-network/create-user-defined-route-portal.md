---
title: "Hozzon létre egy felhasználó által megadott útvonalat az útvonal hálózati forgalmat a hálózati virtuális készülék – az Azure-portálon keresztül |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy hálózati virtuális készüléken keresztül hálózati forgalom útválasztási útválasztást Azure alapértelmezett felülbírálásához felhasználói útvonalat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 736e48f9651d89a1f4e8e0ae72cdffebb8e9c6e0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Hozzon létre egy felhasználó által megadott útvonal - Azure-portálon

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre útvonalakat felhasználói irányíthatja a forgalmat a kettő közötti [virtuális hálózati](virtual-networks-overview.md) alhálózat egy hálózati virtuális készüléken keresztül. A hálózati virtuális készülék valójában egy hálózati alkalmazás, például egy tűzfal futtató virtuális gép. Virtuális készülékek előre konfigurált hálózati központilag telepíthető egy Azure virtuális hálózatban kapcsolatos további tudnivalókért tekintse meg a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Alhálózatok virtuális hálózatban, létrehozásakor az Azure létrehoz alapértelmezett [rendszerútvonalak](virtual-networks-udr-overview.md#system-routes) , amelyek lehetővé teszik az erőforrások kommunikálnak egymással, hogy az összes alhálózat az alábbi ábrán látható módon:

![Alapértelmezett útvonalak](./media/create-user-defined-route/default-routes.png)

Ebben az oktatóanyagban létrehozhat egy virtuális hálózati nyilvános, magán és DMZ-alhálózatok, az alábbi képen látható módon. Általában webkiszolgálók nyilvános alhálózathoz telepíthető, és egy alkalmazás vagy az adatbázis-kiszolgáló telepíthető titkos alhálózathoz. A hálózati virtuális készülék DMZ alhálózaton meghatalmazottjaként járhatnak el egy virtuális gép létrehozása, és szükség esetén hozzon létre egy virtuális gép minden alhálózatban a hálózati virtuális készülék keresztül kommunikáló. A nyilvános és titkos alhálózatok közötti összes forgalom a készülék keresztül történik, az alábbi ábrán látható módon:

![Felhasználó által megadott útvonalak](./media/create-user-defined-route/user-defined-routes.png)

A cikkben a Resource Manager telepítési modell, amely a felhasználó által definiált útvonalak létrehozásakor használata javasolt üzembe helyezési modellel egy felhasználó által megadott útvonal létrehozásának lépéseit. Ha egy felhasználó által megadott útvonal (klasszikus) létrehozásához szüksége, tekintse meg [hozzon létre egy felhasználó által megadott útvonal (klasszikus)](virtual-network-create-udr-classic-ps.md). Ha nem ismeri az Azure üzembe helyezési modellel, lásd: [megértéséhez Azure üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Felhasználó által definiált útvonalak kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak áttekintése](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Útvonalak és hálózati virtuális készülék létrehozása

1. **Előfeltételek**: két vnettel rendelkező virtuális hálózat létrehozása a lépések végrehajtásával [hozzon létre egy virtuális hálózatot](#create-a-virtual-network).
2. Miután létrehozta a virtuális hálózat egy webböngészőben, lépjen a [Azure-portálon](https://portal.azure.com). Jelentkezzen be a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Az a **keresési erőforrások** a portál felső mezőbe írja be *myResourceGroup*. Kattintson a **myResourceGroup** amikor megjelenik a keresési eredmények között. Az erőforráscsoport az előfeltételt jelentő részeként létrejött.
4. Kattintson a **myVnet**, az alábbi ábrán látható módon:

    ![Hálózati illesztő beállításai](./media/create-user-defined-route/virtual-network.png)

5. Hozzon létre egy alhálózatot a hálózati virtuális készülék:
 
    - A **myVnet**, kattintson a **alhálózatok** alatt **beállítások** bal oldalán.
    - Kattintson a **+ alhálózati**, az alábbi ábrán látható módon:

        ![Alhálózatok](./media/create-user-defined-route/subnets.png) 
    - Adja meg az alábbi értékeket **alhálózat hozzáadása**, majd kattintson a **OK**:

        |Beállítás|Érték|
        |-----|-----|
        |Név|DMZ|
        |Címtartomány (CIDR-blokkja)|10.0.2.0/24|

6. Virtuális gép hálózati virtuális készülék létrehozása:

    - A portál bal oldalán kattintson **+ új**, majd kattintson a **számítási**, és kattintson a **Windows Server 2016 Datacenter** vagy **Ubuntu Server 16.04 LTS**.
    - Adja meg a következő értékeket a a **alapjai** panel, amely akkor jelenik meg, majd kattintson **OK**.

        |Beállítás|Érték|
        |---|---|
        |Név|myVm-Nva|
        |Felhasználónév|azureuser|
        |Jelszó és a jelszó megerősítése|A jelszó|
        |Előfizetés|Jelölje ki az előfizetését|
        |Erőforráscsoport|Kattintson a **meglévő**, majd jelölje be **myResourceGroup**|
        |Hely|USA keleti régiója|
    - A a **méret kiválasztása** panel, amelyen megjelenik, kattintson a **DS1_V2 szabványos**, majd kattintson a **kiválasztása**.
    - Az a **beállítások** panel, amelyen megjelenik, kattintson a **virtuális hálózati**. Kattintson a **myVnet** a a **válasszon virtuális hálózati** panel, amely akkor jelenik meg.
    - Az a **beállítások** panelen kattintson a **alhálózati**. Kattintson a **DMZ** a a **alhálózat kiválasztása** panel, amely akkor jelenik meg. 
    - Hagyja meg az alapértelmezett értéket a többi beállítást, és kattintson a **OK**.
    - Kattintson a **létrehozása** a a **létrehozása** panel, amely akkor jelenik meg. A virtuális gép telepítését néhány percet vesz igénybe.

    > [!NOTE]
    > Mellett a virtuális gép létrehozása az Azure-portálon létrehoz egy nyilvános IP-címet, és alapértelmezés szerint rendeli a virtuális gép. Ha a virtuális gép éles környezetben volt telepítését, előfordulhat, hogy nem kíván egy nyilvános IP-címet rendel a virtuális gép. Ehelyett a hálózati virtuális készülék is hozzáférhetnek a virtuális hálózaton lévő más virtuális gépektől. Nyilvános IP-címekre vonatkozó további tudnivalókért lásd: [egy nyilvános IP-cím kezelése](virtual-network-public-ip-address.md).

7. Rendeljen hozzá statikus magánhálózati IP-cím, és IP-címek a hálózati illesztő továbbítása a portál az előző lépésben létrehozott. 
    - Az a **keresési erőforrások** a lap tetején mezőbe írja be *myVm-Nva*.
    - Kattintson a **myVm-Nva** amikor megjelenik a keresési eredmények között.
    - Kattintson a **hálózati** alatt **beállítások** bal oldalán.
    - Kattintson a nevére, a hálózati illesztő **myVm-Nva - hálózati adapterek**. A név **myvm-nva***X*, ahol *X* a portál által hozzárendelt szám.
    - Kattintson a **IP-konfigurációk** alatt **beállítások** a hálózati adapter, az alábbi ábrán látható módon:

        ![Hálózati illesztő beállításai](./media/create-user-defined-route/network-interface-settings.png)
        
    - Kattintson a **engedélyezve** a a **IP-továbbítás** beállítást, majd kattintson a **mentése**. IP-továbbítás engedélyezni kell mindegyik hálózati interfész, amely megkapja a forgalmat nem hozzárendelt IP-címet. Azure forrás vagy a cél ellenőrizze a hálózati adapter IP-továbbítás engedélyezése letiltja.
    - Kattintson a **ipconfig1** IP-konfigurációk közül.
    - Kattintson a **statikus** a **hozzárendelés** a privát IP-cím alapján **ipconfig1**, az alábbi ábrán látható módon:

        ![IP-konfiguráció](./media/create-user-defined-route/ip-configuration.png)
    - Ahogy az előző ábrán is látható, adja meg a *10.0.2.4* alatt **IP-cím** a a **privát IP-címbeállítások**. Egy statikus IP-cím hozzárendelése a hálózati illesztő biztosítja, hogy a cím nem módosítja a során a virtuális gép, a hálózati adapter csatlakozik. A megadott cím jelenleg nincs hozzárendelve a DMZ-alhálózatot, amely a hálózati adaptert egy másik erőforrás. 
    - A konfiguráció mentéséhez kattintson **mentése** alatt **ipconfig1**. A ipconfig1 ablak nem zárható be, amíg nem kap értesítést a portálon, a hálózati illesztő mentett.
 
8. Hozzon létre két útvonaltáblák. Az útvonaltáblák nulla vagy több útvonalat tartalmazza:

    - A portál bal oldalán kattintson **+ új** > **hálózati** > **útvonaltábla**.
    - Adja meg az alábbi értékeket **hoznia egy útválasztási táblázatot**, és kattintson a **létrehozása**:

        |Beállítás|Érték|
        |---|---|
        |Név|Nyilvános myRouteTable|
        |Előfizetés|Jelölje ki az előfizetését|
        |Erőforráscsoport|Válassza ki **meglévő**, majd jelölje be **myResourceGroup**|
        |Hely|USA keleti régiója|
    
    - Végezze el újra a 8. lépés előző részlépések, de az útvonaltábla neve *myRouteTable InPrivate-*.
9. Adja hozzá az útvonalakat a *myRouteTable nyilvános* útvonaltábla, és rendelje hozzá az útvonaltábla a *nyilvános* alhálózati:

    - Az a **keresési erőforrások** a portál felső mezőbe írja be *myRouteTable nyilvános*. Kattintson a **myRouteTable nyilvános** amikor megjelenik a keresési eredmények között.
    - A **myRouteTable nyilvános**, kattintson a **útvonalak** listájában **beállítások**.
    - Kattintson a **+ Hozzáadás** alatt **myRouteTable nyilvános - útvonalak**.
    -  Alapértelmezés szerint az Azure útvonalak forgalmat a virtuális hálózaton belül minden alhálózatok között. Hozzon létre módosíthatja úgy, hogy a forgalom útválasztási Azure alapértelmezett útvonalat a *nyilvános* alhálózati áthalad az NVA ahelyett, hogy közvetlenül a *titkos* alhálózati. Adja meg a következő értékeket a a **útvonal hozzáadása** , amely akkor jelenik meg, és kattintson a panel **OK**:

        |Beállítás|Érték|Magyarázat|
        |---|---|---|
        |Útvonal neve|ToPrivateSubnet|Ez az útvonal irányítja a forgalmat a titkos alhálózathoz, keresztül a virtuális hálózati berendezések.|
        |Címelőtag|10.0.1.0/24| A címelőtag belül bármely címekre küldött forgalom (10.0.1.0 - 10.0.1.254) a hálózati virtuális készülék küld.|
        |A következő ugrás típusa| Válassza ki **virtuális készülék**||
        |A következő ugrás címe|10.0.2.4| A statikus magánhálózati IP-címet a hálózati adapter csatlakozik a hálózathoz virtuális készülék. A csak egy címet adhat meg Ugrás típusa **virtuális készülék**.|

    - A **myRouteTable nyilvános**, kattintson a **alhálózatok** alatt **beállítások**. 
    - Kattintson a **+ társítás** alatt **myRouteTable nyilvános - alhálózatok**.
    - Kattintson a **virtuális hálózati** alatt **alhálózatot társítani**, majd kattintson a **myVnet**.
    - Kattintson a **alhálózati** alatt **alhálózatot társítani**, majd kattintson **nyilvános** alatt **alhálózat kiválasztása**. 
    - A konfiguráció mentéséhez kattintson **OK** alatt **alhálózatot társítani**. Egy alhálózat állhat nulla vagy egy útválasztási táblázatot társítva.
10. A Kész lépés 9 Ebben az esetben keresése **myRouteTable InPrivate-**, útvonal létrehozása a következő beállításokkal, majd az útvonaltábla való társítása a **titkos** alhálózata a **myVnet** virtuális hálózat:

    |Beállítás|Érték|Magyarázat|
    |---|---|---|
    |Útvonal neve|ToPublicSubnet|Ez az útvonal irányítja a forgalmat a nyilvános alhálózathoz keresztül a virtuális hálózati berendezések.|
    |Címelőtag|10.0.0.0/24| A címelőtag belül bármely címekre küldött forgalom (10.0.0.0 - 10.0.1.254) a hálózati virtuális készülék küld.|
    |A következő ugrás típusa| Válassza ki **virtuális készülék**||
    |A következő ugrás címe|10.0.2.4||

    A privát és nyilvános alhálózatban lévő erőforrásokat közötti hálózati forgalom halad keresztül a virtuális hálózati berendezések. 
11. **Választható lehetőség:** a nyilvános és Magánfelhő alhálózatban lévő virtuális gép létrehozása, és ellenőrizze, hogy a virtuális gépek közötti kommunikáció áthalad a hálózati virtuális készülék a lépések végrehajtásával [útválasztásiellenőrzése](#validate-routing). 
12. **Nem kötelező**: törli az Ön által létrehozott ebben az oktatóanyagban szereplő lépések végrehajtásával erőforrást [törli az erőforrást](#delete-resources).

## <a name="validate-routing"></a>Útválasztás ellenőrzése

1. Ha még nem tette meg, hajtsa végre a [útvonalak és hálózati virtuális készülék létrehozása](#create-routes-and-network-virtual-appliance).
2. Kattintson a **kipróbálás** a csoportban, amely a következő, amely az Azure-felhő rendszerhéj megnyitása gombra. Ha a rendszer kéri, jelentkezzen be az Azure használatával a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p). Az Azure-felhő rendszerhéj a szabad rendszerhéjakba az Azure parancssori felületével előre telepítve. 

    Az alábbi parancsfájlok, hozzon létre két virtuális gép, egy-egy a *nyilvános* alhálózatot és egy-egy a *titkos* alhálózat. A parancsfájlok IP-továbbítás ahhoz, hogy az operációs rendszer NVA operációs rendszerében a hálózati adapter irányíthatja a forgalmat a hálózati kapcsolaton keresztül is engedélyezheti. Egy éles NVA általában megvizsgálja a forgalom útválasztási azt megelőzően, de ebben az oktatóanyagban az egyszerű NVA csak irányítja a forgalmat tanulmányozza az nélkül. 

    Kattintson a **másolási** gombra a **Linux** vagy **Windows** parancsfájlok, hajtsa végre, és illessze be a parancsfájl tartalmát egy szövegszerkesztőben. Módosítsa a jelszavát a *adminPassword* változót, majd illessze be a parancsfájl az Azure felhőalapú rendszerhéjat. Futtassa a parancsfájlt az operációs rendszer választott a 6. lépésében létrehozása után a virtuális hálózati berendezések [útvonalak és hálózati virtuális készülék létrehozása](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Ellenőrizze a nyilvános és Magánfelhő alhálózatban lévő virtuális gépek közötti kommunikáció. 

    - Nyissa meg egy [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) vagy [távoli asztal](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) a nyilvános IP-címe (Windows) kapcsolatot a *myVm nyilvános* virtuális gépet.
    - A parancssorból a *myVm nyilvános* virtuális gépet, adja meg `ping myVm-Private`. Mivel az NVA irányítja a forgalmat a nyilvánosság elől a titkos alhálózathoz választ kap.
    - Az a *myVm nyilvános* virtuális gépet, a nyilvános és titkos alhálózatokat a virtuális gépek közötti nyomkövetést futtatni. Adja meg a megfelelő parancsot, amely a következő, az operációs rendszertől függően telepítette, akkor a virtuális gépek használata a nyilvános és Magánfelhő alhálózatok:
        - **Windows**: egy parancssorból futtassa a `tracert myvm-private` parancsot.
        - **Ubuntu**: futtassa a `tracepath myvm-private` parancsot.
      Forgalom átmegy 10.0.2.4 (NVA) (a virtuális gép személyes alhálózaton) 10.0.1.4 elérése előtt. 
    - Az előző lépéseket csatlakozva a *myVm InPrivate-* virtuális gép és a pingelés a *myVm nyilvános* virtuális gépet. A nyomkövetési útvonal 10.0.0.4 (a virtuális gép nyilvános alhálózaton) elérése előtt 10.0.2.4 keresztül utazás kommunikációs jeleníti meg.
    - **Opcionálisan**: a következő Ugrás az Azure két virtuális gép közötti érvényesítéséhez használja a következő ugrás funkció az Azure hálózati figyelőt. Hálózati figyelőt használatához először [hozzon létre egy Azure hálózati figyelőt példányt](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) számára a használni kívánt régiót. Ebben az oktatóanyagban az Amerikai keleti terület szolgál. Miután engedélyezte a hálózati figyelőt példánya a régió, adja meg a nyilvános és Magánfelhő alhálózatban lévő virtuális gépek között a következő ugrás adatainak megtekintéséhez a következő parancsot:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       A kimeneti adja vissza *10.0.2.4* , a **nextHopIpAddress** és *VirtualAppliance* , a **nextHopType**. 

> [!NOTE]
> Ebben az oktatóanyagban olvassanak, nyilvános IP-címek hozzá vannak rendelve a virtuális gépek használata a nyilvános és titkos alhálózatok, és minden hálózati port engedélyezve a hozzáférés az Azure mindkét virtuális gépekhez. Éles környezetben való használathoz a virtuális gépek létrehozásakor lehet, hogy nem nyilvános IP-címek kiosztása őket, és szűkítheti a hálózati forgalmat a magánhálózati alhálózathoz takarja el a hálózati virtuális készülék telepítésével, illetve a hálózati biztonsági csoportok hozzárendelése az alhálózatok hálózati adapter, vagy mindkettőt. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ebben az oktatóanyagban egy meglévő virtuális hálózatot, két alhálózattal igényel. Kattintson a **kipróbálás** a mezőbe, amely gyorsan a virtuális hálózat létrehozása a következő gombra. Kattintson a **kipróbálás** gombra kattint, megnyílik a [Azure Cloud rendszerhéj](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Bár a felhőalapú rendszerhéj a PowerShell vagy a Bash rendszerhéjat ebben a szakaszban fut, a virtuális hálózat létrehozása a rendszerhéjakba szolgál. A rendszerhéjakba az Azure parancssori felület telepítve van. Ha a felhő rendszerhéj kéri, jelentkezzen be az Azure használatával a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p). A jelen oktatóanyagban használt virtuális hálózat létrehozásához kattintson a **másolási** gombra kattint, a következő mezőbe, majd illessze be a parancsfájl az Azure-felhő rendszerhéj:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Virtuális hálózat létrehozása a portálon, a PowerShell vagy az Azure Resource Manager-sablonok használatával kapcsolatos további tudnivalókért lásd: [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Erőforrások törlése

Ez az oktatóanyag befejezése után előfordulhat, hogy törölni kívánja az erőforrásokat, amelyek hozott létre, így használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli. A portál meg van nyitva kövesse az alábbi lépéseket:

1. A portál keresési mezőbe, írja be a **myResourceGroup**. A keresési eredmények között kattintson **myResourceGroup**.
2. Az a **myResourceGroup** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroup**, és kattintson a **törlése**.

## <a name="next-steps"></a>Következő lépések

- Hozzon létre egy [magas rendelkezésre állású hálózati virtuális készülék](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuális hálózati berendezések gyakran rendelkezik, több hálózati adapterrel és IP-címek hozzárendelve. Megtudhatja, hogyan [hálózati adapterek hozzáadása egy meglévő virtuális gép](virtual-network-network-interface-vm.md#vm-add-nic) és [IP-címek hozzáadása a meglévő hálózati illesztő](virtual-network-network-interface-addresses.md#add-ip-addresses). Bár az összes virtuálisgép-méretek lehet kapcsolódik legalább két hálózati adapterrel, minden virtuális gép méretét támogatja a hálózati adapterek maximális száma. Hány hálózati adapterek minden egyes virtuális gép mérete által támogatott, lásd: [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretét. 
- Kényszerített bújtatás forgalom helyszíni keresztül felhasználói útvonal létrehozása egy [pont-pont VPN-kapcsolat](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
