---
title: "Virtuális hálózat létrehozása |} Az Azure Resource Manager-sablon |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Azure Resource Manager-sablonnal virtuális hálózatot."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81602766848a91331c8d811ea1c8ec3ffae44b96
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablonnal virtuális hálózat létrehozása

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket.
 
Ez a cikk azt ismerteti, hogyan hozhat létre egy Vnetet az Azure Resource Manager-sablonnal Resource Manager deployment használatával. Resource Manager-alapú VNetet létrehozhat egyéb eszközökkel is, illetve létrehozhat VNetet a klasszikus üzemi modellben is, ha az alábbi listából egy másik lehetőséget választ:

> [!div class="op_single_selector"]
- [Portál](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [Parancssori felület](virtual-networks-create-vnet-arm-cli.md)
- [Sablon](virtual-networks-create-vnet-arm-template-click.md)
- [Portál (klasszikus)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (klasszikus)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Parancssori felület (klasszikus)](virtual-networks-create-vnet-classic-cli.md)

Ismertetjük, hogy miként tölthet le és módosíthat egy meglévő ARM-sablont a GitHubból, és miként helyezheti üzembe a sablont a GitHubból, PowerShellből és az Azure parancssori felületéről.

Ha közvetlenül a GitHubból helyezi üzembe az ARM-sablont, változtatások nélkül, ugorjon a [sablon telepítése a githubból](#deploy-the-arm-template-by-using-click-to-deploy) lépésre.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon letöltése és megismerése
Töltse le a meglévő sablon létrehozhat egy Vnetet két alhálózattal a Githubból, hajtsa végre a módosításokat, előfordulhat, hogy szeretné, és újra felhasználhatja. Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg a [mintasablon lapot](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Kattintson az **azuredeploy.json**, majd a **RAW** elemre.
3. Mentse a fájlt egy helyi mappába a számítógépén.
4. Ha ismeri a sablonok, ugorjon a 7.
5. Nyissa meg az előbb mentett fájlt, és nézze meg az 5. sorban a **parameters** (paraméterek) alatt látható tartalmakat. Az ARM-sablonparaméterek az üzembe helyezés során kitölthető paraméterek helyőrzőiként működnek.
   
   | Paraméter | Leírás |
   | --- | --- |
   | **hely** |Az Azure-régió, ahol a VNet létrejön |
   | **vnetName** |Az új VNet neve |
   | **addressPrefix** |A VNet címtere CIDR-formátumban |
   | **subnet1Name** |Az első VNet neve |
   | **subnet1Prefix** |Az első alhálózat CIDR-blokkja |
   | **subnet2Name** |A második VNet neve |
   | **subnet2Prefix** |A második alhálózat CIDR-blokkja |
   
   > [!IMPORTANT]
   > A GitHubban fenntartott Azure Resource Manager-sablonok idővel módosulhatnak. Ne feledje el ellenőrizni a sablont, mielőtt használja.
   > 
   > 
6. Ellenőrizze a **resources** (erőforrások) alatt látható tartalmat, és figyelje meg a következőket:
   
   * **type**. A sablon által létrehozott erőforrástípus. Ebben az esetben a **Microsoft.Network/virtualNetworks**, ami egy VNetet jelöl.
   * **Név** Az erőforrás neve. Figyelje meg a **[parameters('vnetName')]** használatát, amely azt jelzi, hogy a nevet a felhasználó vagy egy paraméterfájl adja meg az üzembe helyezés során bemenetként.
   * **properties**. Az erőforrás tulajdonságainak listája. Ez a sablon a címteret és az alhálózati tulajdonságokat használja a VNet létrehozása során.
7. Lépjen vissza a [mintasablon lapra](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Kattintson az **azuredeploy-paremeters.json**, majd a **RAW** elemre.
9. Mentse a fájlt egy helyi mappába a számítógépén.
10. Nyissa meg az előbb mentett fájlt, és módosítsa a paraméterek értékeit. Értékét az alábbiak segítségével telepítheti a virtuális hálózat a forgatókönyvet ismerteti:

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Mentse a fájlt.


## <a name="deploy-the-template-using-powershell"></a>A PowerShell használatával sablon üzembe helyezése

Fejezze be a sablon PowerShell használatával történő telepítéséhez a következő lépéseket:

1. Telepítse és konfigurálja az Azure PowerShell; Ehhez hajtsa végre a lépéseket a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk.
2. Futtassa a következő parancsot új erőforráscsoport létrehozásához:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    A parancs létrehoz egy erőforráscsoportot *TestRG* a a *USA középső RÉGIÓJA* azure-régiót. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

    Várt kimenet:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. A következő paranccsal telepítheti az új VNet, akkor fent letöltött és módosított sablonnal és paraméterfájlokkal fájlokat használja:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Várt kimenet:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. A következő parancsot az új vnet tulajdonságainak megtekintéséhez:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Várt kimenet:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Kattintson a központi telepítése segítségével sablon üzembe helyezése

Használja fel a Microsoft által kezelt elérhető GitHub-tárakba feltöltött, előre definiált Azure Resource Manager sablonokban, és a Közösség. Ezek a sablonok telepített rögtön kívül a github webhelyen, vagy letölthető, és szükség szerint módosítva. A sablont, amely hoz létre egy Vnetet két alhálózattal telepítéséhez kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) címet.
2. Görgessen le a sablonok listáján, és kattintson a **101-vnet-two-subnets** elemre. Ellenőrizze a **README.md** fájlt a lent látható módon.

    ![A README.md fájl a GitHubban](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre. Szükség esetén adja meg az Azure bejelentkezési hitelesítő adatait. 
4. A **Parameters** (Paraméterek) panelen adja meg az értékeket, amelyekkel az új VNetet szeretné létrehozni, majd kattintson az **OK** gombra. Az alábbi ábrán a forgatókönyv esetében az értékek láthatók:
   
    ![Az ARM-sablon paraméterei](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Kattintson az **Erőforráscsoport** elemre, majd válassza ki az erőforráscsoportot, amelyhez a VNetet hozzá kívánja adni, vagy az **Új létrehozása** elemre kattintva adja hozzá a VNetet egy új erőforráscsoporthoz. Az alábbi ábrán láthatók az erőforráshoz tartozó nevű új erőforráscsoport-beállítások **TestRG**:

    ![Erőforráscsoport](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat.
7. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget.
8. Kattintson a **jogi feltételeket**, olvassa el a feltételeket, és kattintson a **megvásárlása** gombra kattintva elfogadja. 
9. A VNet létrehozásához kattintson a **Create** (Létrehozás) gombra.
   
    ![Üzembe helyezési csempe küldése a betekintő portálban](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Miután a telepítés befejeződött, az Azure portálon kattintson a **további szolgáltatások**, típus *virtuális hálózatok* szót a Szűrő mezőbe, amely akkor jelenik meg, majd kattintson a virtuális hálózatokat a virtuális hálózatok panelen. Kattintson a panel *TestVNet*. Az a *TestVNet* panelen kattintson a **alhálózatok** megtekintéséhez a létrehozott alhálózatok, az alábbi ábrán látható módon:
    
     ![VNet létrehozása a betekintő portálon](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan csatlakoztathat:

- virtuális gépeket (VM) virtuális hálózatokhoz – olvassa el a [Windows virtuális gép létrehozását](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vagy a [Linux virtuális gép létrehozását](../virtual-machines/linux/quick-create-portal.md) ismertető cikkeket. Ha nem szeretne egy VNetet és alhálózatot létrehozni a cikkben ismertetett lépések szerint, használhat meglévő VNetet és alhálózatot, amelyekhez kapcsolódhat.
- virtuális hálózatot más virtuális hálózatokhoz – olvassa el a [virtuális hálózatok csatlakoztatását](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) ismertető cikket.
- virtuális hálózatot helyszíni hálózathoz helyek közti virtuális magánhálózat (VPN) vagy ExpressRoute-kapcsolatcsoport használatával. Az elsajátításához olvassa el a [Virtuális hálózat csatlakoztatása helyszíni hálózathoz helyek közti VPN használatával](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) és a [Virtuális hálózat csatlakoztatása ExpressRoute-kapcsolatcsoporthoz](../expressroute/expressroute-howto-linkvnet-arm.md) eljárásokat ismertető cikkeket.
