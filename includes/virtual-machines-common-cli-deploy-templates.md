
* [Virtuális gép gyors létrehozása az Azure-ban](#quick-create-a-vm-in-azure)
* [Virtuális gép központi telepítése sablonból az Azure-ban](#deploy-a-vm-in-azure-from-a-template)
* [Virtuális gép létrehozása egyéni rendszerképből](#create-a-custom-vm-image)
* [Virtuális hálózatot és terheléselosztót használó virtuális gép központi telepítése](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
* [Erőforráscsoport eltávolítása](#remove-a-resource-group)
* [Egy erőforráscsoport üzemelő példányához tartozó napló megjelenítése](#show-the-log-for-a-resource-group-deployment)
* [Virtuális gépre vonatkozó információk megjelenítése](#display-information-about-a-virtual-machine)
* [Csatlakozás Linux-alapú virtuális géphez](#log-on-to-a-linux-based-virtual-machine)
* [Virtuális gép leállítása](#stop-a-virtual-machine)
* [Virtuális gép elindítása](#start-a-virtual-machine)
* [Adatlemez csatolása](#attach-a-data-disk)

## <a name="getting-ready"></a>Előkészületek
Az Azure CLI Azure-erőforráscsoportokkal való használatához rendelkeznie kell az Azure CLI megfelelő verziójával és egy Azure-fiókkal. Ha nincs telepítve az Azure CLI, [telepítse](../articles/xplat-cli-install.md).

### <a name="update-your-azure-cli-version-to-090-or-later"></a>Az Azure CLI frissítése 0.9.0-s vagy újabb verzióra
Ha szeretné megtudni, telepítve van-e a 0.9.0-s vagy újabb verzió, írja be a következőt: `azure --version`.

```azurecli
azure --version
0.9.0 (node: 0.10.25)
```

Ha a verzió nem a 0.9.0-s vagy újabb, frissítenie kell az egyik natív telepítővel vagy az **npm** segítségével. Ehhez írja be a következőt: `npm update -g azure-cli`.

Vagy az alábbi [Docker rendszerkép](https://registry.hub.docker.com/u/microsoft/azure-cli/) segítségével futtassa az Azure CLI-t Docker-tárolóként. Futtassa a következő parancsot egy Docker-gazdagépről:

```bash
docker run -it microsoft/azure-cli
```

### <a name="set-your-azure-account-and-subscription"></a>Az Azure-fiók és -előfizetés beállítása
Ha még nincs Azure-előfizetése, de van MSDN-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/) is.

Ezután [jelentkezzen be interaktívan Azure-fiókjába](../articles/xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login). Ehhez írja be az `azure login` karakterláncot, majd kövesse az utasításokat az Azure-fiókjába való interaktív bejelentkezéshez. 

> [!NOTE]
> Ha rendelkezik munkahelyi vagy iskolai azonosítóval, és tudja, hogy még nem engedélyezte a kéttényezős hitelesítést, használhatja az `azure login -u` parancsot **is** a munkahelyi vagy iskolai azonosítójával együtt, így bejelentkezhet az interaktív munkamenet *nélkül*. Ha nem rendelkezik munkahelyi vagy iskolai azonosítóval, [létrehozhat egy munkahelyi vagy iskolai azonosítót a személyes Microsoft-fiókjából](../articles/virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), és bejelentkezhet ugyanígy.
>
>

A fiókjához tartozhat több előfizetés is. Az előfizetések listájának megjelenítéséhez írja be az `azure account list` karakterláncot. Az eredmény a következőhöz hasonló lesz:

```azurecli
azure account list
info:    Executing command account list
data:    Name                              Id                                    Tenant Id                            Current
data:    --------------------------------  ------------------------------------  ------------------------------------  -------
data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
```

Az aktuális Azure-előfizetés beállításhoz írja be a következőket. Azt az előfizetésnevet vagy azonosítót használja, amelyhez a kezelni kívánt erőforrások tartoznak.

```azurecli
azure account set <subscription name or ID> true
```

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>Váltás az Azure CLI erőforráscsoport módjára
Alapértelmezés szerint az Azure CLI szolgáltatás-felügyeleti módban indul el (**asm** mód). Az erőforráscsoport módra váltáshoz írja be a következőt.

```azurecli
azure config mode arm
```

## <a name="understanding-azure-resource-templates-and-resource-groups"></a>Az Azure-erőforrássablonok és -erőforráscsoportok ismertetése
A legtöbb alkalmazás különböző erőforrástípusokból épül fel (például egy vagy több virtuális gép és tárfiók, egy SQL-adatbázis, egy virtuális hálózat vagy egy tartalomkézbesítési hálózat). Az alapértelmezett Azure szolgáltatásfelügyeleti API és a klasszikus Azure portál ezeket az elemeket szolgáltatásonkénti módszerrel jelenítette meg. Ehhez a módszerhez egyesével kell üzembe helyezni és kezelni az egyes szolgáltatásokat (vagy más eszközök segítségével kell ezt végezni), és nem lehet őket logikailag egy telepítési egységként kezelni.

Az *Azure Resource Manager sablonjai* azonban lehetővé teszik, hogy logikailag egy telepítési egységként helyezze üzembe és kezelje ezeket a különböző erőforrásokat. Ahelyett hogy parancsonként adná meg az Azure számára, hogy mit helyezzen üzembe, az egész üzembe helyezést leírhatja egy JSON-fájlban (az összes erőforrást és az azokhoz tartozó konfigurációs és telepítési paramétereket), és utasítja az Azure-t, hogy az erőforrásokat egy csoportként helyezze üzembe.

Ezután az Azure CLI erőforrás-felügyeleti parancsaival kezelheti a csoport erőforrásait azok teljes életciklusában a következő műveletek végrehajtásához:

* A csoporton belüli összes erőforrás leállítása, indítása vagy törlése egyszerre.
* Szerepköralapú hozzáférés-vezérlési (RBAC) szabályok alkalmazása a biztonsági engedélyek zárolására.
* Auditálási műveletek.
* Erőforrások címkézése további metaadatokkal a jobb nyomon követés érdekében.

[Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md) című témakörből további információkat tudhat meg az Azure-erőforráscsoportokról és azok felhasználásáról. A sablonok készítésével kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](../articles/resource-group-authoring-templates.md).

## <a name="a-idquick-create-a-vm-in-azureatask-quick-create-a-vm-in-azure"></a><a id="quick-create-a-vm-in-azure"></a>Feladat: Virtuális gép gyors létrehozása az Azure-ban
Előfordulnak olyan esetek, hogy tudja, milyen rendszerképre van szüksége, és azonnal szüksége van a rendszerképből egy virtuális gépre, az infrastruktúra pedig nem olyan fontos – például egy üres virtuális gépen szeretne tesztelni valamit. Ilyenkor az `azure vm quick-create` paranccsal átadhatja a virtuális gép és az ahhoz tartozó infrastruktúra létrehozásához szükséges argumentumokat.

Először is hozzon létre egy erőforráscsoportot.

```azurecli
azure group create coreos-quick westus
info:    Executing command group create
+ Getting resource group coreos-quick
+ Creating resource group coreos-quick
info:    Created resource group coreos-quick
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
data:    Name:                coreos-quick
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

Ezután szüksége lesz egy rendszerképre. Ha az Azure CLI-vel szeretné megkeresni a rendszerképet, tekintse meg az [Azure virtuális gépek rendszerképének a PowerShell és az Azure CLI segítségével történő megkeresésével és kiválasztásával kapcsolatos](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részt. Ebben a cikkben azonban az alábbiakban látható egy rövid lista a népszerű rendszerképekről. A gyors létrehozáshoz a CoreOS Stable rendszerképét fogjuk használni.

> [!NOTE]
> A ComputeImageVersion esetében használhatja egyszerűen a „latest” paramétert a sablonnyelvben és az Azure CLI-ben is. Ez lehetővé teszi, hogy mindig a rendszerkép legújabb javított verzióját használhassa a parancsfájlok vagy sablonok módosítása nélkül. Ez az alábbiakban látható.
>
>

| Közzétevő neve | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |
| OpenLogic |CentOS |7 |7.0.201503 |
| OpenLogic |CentOS |7.1 |7.1.201504 |
| CoreOS |CoreOS |Beta |647.0.0 |
| CoreOS |CoreOS |Stable |633.1.0 |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |8.0.40459 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |1.0.0 |
| msopentech |Oracle-Database-12c-Weblogic-Server-12c |Standard |1.0.0 |
| msopentech |Oracle-Database-12c-Weblogic-Server-12c |Enterprise |1.0.0 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |12.0.2430 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |12.0.2430 |
| Canonical |UbuntuServer |12.04.5-LTS |12.04.201504230 |
| Canonical |UbuntuServer |14.04.2-LTS |14.04.201503090 |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |3.0.201503 |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |4.0.201503 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |5.0.201504 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |1.0.141204 |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |4.3.4665 |

A virtuális gép létrehozásához egyszerűen írja be az `azure vm quick-create` parancsot, és kövesse az utasításokat. A következőhöz hasonló eredményt kell kapnia:

```azurecli
azure vm quick-create
info:    Executing command vm quick-create
Resource group name: coreos-quick
Virtual machine name: coreos
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "coreos"
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Retrieving storage accounts
info:    Could not find any storage accounts in the region "westus", trying to create new one
+ Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"
+ Looking up the storage account cli9fd3fce49e9a9b3d14302
+ Looking up the NIC "coreo-westu-1430261891570-nic"
info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
+ Looking up the virtual network "coreo-westu-1430261891570-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "coreo-westu-1430261891570-vnet"
+ Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "coreo-westu-1430261891570-pip"
info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
+ Creating public ip "coreo-westu-1430261891570-pip"
+ Looking up the public ip "coreo-westu-1430261891570-pip"
+ Creating NIC "coreo-westu-1430261891570-nic"
+ Looking up the NIC "coreo-westu-1430261891570-nic"
+ Creating VM "coreos"
+ Looking up the VM "coreos"
+ Looking up the NIC "coreo-westu-1430261891570-nic"
+ Looking up the public ip "coreo-westu-1430261891570-pip"
data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
data:    ProvisioningState               :Succeeded
data:    Name                            :coreos
data:    Location                        :westus
data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :coreos
data:        Offer                       :coreos
data:        Sku                         :stable
data:        Version                     :633.1.0
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
data:
data:    OS Profile:
data:      Computer Name                 :coreos
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-30-72-E3
data:          Provisioning State        :Succeeded
data:          Name                      :coreo-westu-1430261891570-nic
data:          Location                  :westus
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :10.0.1.4
data:            Public IP address       :104.40.24.124
data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
info:    vm quick-create command OK
```

És már kész is az új virtuális gép.

## <a name="a-iddeploy-a-vm-in-azure-from-a-templateatask-deploy-a-vm-in-azure-from-a-template"></a><a id="deploy-a-vm-in-azure-from-a-template"></a>Feladat: Virtuális gép központi telepítése sablonból az Azure-ban
Az ezen szakaszokban található útmutatást követve telepíthet egy új Azure virtuális gépet sablonból az Azure CLI-vel. Ez a sablon létrehoz egy virtuális gépet egy új, egyetlen alhálózattal rendelkező virtuális hálózatban, és az `azure vm quick-create` parancstól eltérően lehetővé teszi, hogy leírhassa, pontosan mit szeretne, majd hibák nélkül megismételhesse a műveletet. Itt látható, amit a sablon létrehoz:

![](./media/virtual-machines-common-cli-deploy-templates/new-vm.png)

### <a name="step-1-examine-the-json-file-for-the-template-parameters"></a>1. lépés: A sablonparaméterek vizsgálata a JSON-fájlban
Az alábbiakban látható a sablon JSON-fájljának tartalma. (A sablon a [GitHubon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json) is megtalálható.)

A sablonok rugalmasak, tehát lehet, hogy a tervező, úgy döntött, hogy, sok paraméter közül lehet választani, de az is lehet, hogy egy kevésbé rugalmas sablont hozott létre, ahol csak néhány paraméter közül választhat. A sablon paraméterekként való átadásához szükséges információk beszerzéséhez nyissa meg a sablonfájlt (ebben a témakörben található alább egy sablon beágyazva), és vizsgálja meg a **paraméterek** értékeit.

Ebben az esetben a sablon a következők megadását kéri:

* Egyedi tárfióknév.
* A virtuális gép rendszergazdai felhasználójának neve.
* Jelszó.
* A külvilág által használható tartománynév.
* Ubuntu Server-verziószám – de a rendszer csak egyet fogad el egy listáról.

További információk a [felhasználónév- és jelszókövetelményekről](../articles/virtual-machines/virtual-machines-linux-faq.md#what-are-the-username-requirements-when-creating-a-vm).

Ha kiválasztotta ezeket az értékeket, készen áll arra, hogy létrehozzon egy csoportot az Azure-előfizetéséhez, majd telepítse ezt a sablont az előfizetésben.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "newStorageAccountName": {
    "type": "string",
    "metadata": {
        "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
    }
    },
    "adminUsername": {
    "type": "string",
    "metadata": {
        "description": "User name for the virtual machine."
    }
    },
    "adminPassword": {
    "type": "securestring",
    "metadata": {
        "description": "Password for the virtual machine."
    }
    },
    "dnsNameForPublicIP": {
    "type": "string",
    "metadata": {
        "description": "Unique DNS name for the public IP used to access the virtual machine."
    }
    },
    "ubuntuOSVersion": {
    "type": "string",
    "defaultValue": "14.04.2-LTS",
    "allowedValues": [
        "12.04.5-LTS",
        "14.04.2-LTS",
        "15.04"
    ],
    "metadata": {
        "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
    }
    }
},
"variables": {
    "location": "West US",
    "imagePublisher": "Canonical",
    "imageOffer": "UbuntuServer",
    "OSDiskName": "osdiskforlinuxsimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyUbuntuVM",
    "vmSize": "Standard_D1",
    "virtualNetworkName": "MyVNET",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
},
"resources": [
    {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('newStorageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('location')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('publicIPAddressName')]",
    "location": "[variables('location')]",
    "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "location": "[variables('location')]",
    "properties": {
        "addressSpace": {
        "addressPrefixes": [
            "[variables('addressPrefix')]"
        ]
        },
        "subnets": [
        {
            "name": "[variables('subnetName')]",
            "properties": {
            "addressPrefix": "[variables('subnetPrefix')]"
            }
        }
        ]
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('nicName')]",
    "location": "[variables('location')]",
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
    ],
    "properties": {
        "ipConfigurations": [
        {
            "name": "ipconfig1",
            "properties": {
            "privateIPAllocationMethod": "Dynamic",
            "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
            },
            "subnet": {
                "id": "[variables('subnetRef')]"
            }
            }
        }
        ]
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[variables('location')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
        "computername": "[variables('vmName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
        "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('ubuntuOSVersion')]",
            "version": "latest"
        },
        "osDisk": {
            "name": "osdisk",
            "vhd": {
            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
        }
        },
        "networkProfile": {
        "networkInterfaces": [
            {
            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
        ]
        }
    }
    }
]
}
```

### <a name="step-2-create-the-virtual-machine-by-using-the-template"></a>2. lépés: A virtuális gép létrehozása a sablonnal
Ha meghatározta a paraméterértékeket, létre kell hoznia egy erőforráscsoportot a sablon központi telepítéséhez, majd telepítenie kell a sablont.

Az erőforráscsoport létrehozásához írja be az `azure group create <group name> <location>` parancsot a kívánt csoportnévvel és annak az adatközpontnak a helyével, amelybe a központi telepítést végre szeretné hajtani. Ez gyorsan megtörténik:

```azurecli
azure group create myResourceGroup westus
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

Ekkor a központi telepítés létrehozásához hívja meg az `azure group deployment create` parancsot, és továbbítsa a következőket:

* A sablonfájlt (ha mentette a fenti JSON-sablont egy helyi fájlba).
* A sablon URI-jét (ha a GitHubon vagy más webcímen található fájlra kíván mutatni).
* Azt az erőforráscsoportot, amelybe a központi telepítést végezni kívánja.
* A központi telepítés nevét (nem kötelező).

A rendszer kérni fogja a JSON-fájl „parameters” szakaszában szereplő paraméterértékeket. Ha megadta az összes paraméterértéket, megkezdődik a központi telepítés.

Például:

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json myResourceGroup firstDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
newStorageAccountName: storageaccount
adminUsername: ops
adminPassword: password
dnsNameForPublicIP: newdomainname
```

A következő információtípust kapja:

```azurecli
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "firstDeployment"
+ Registering providers
info:    Registering provider microsoft.storage
info:    Registering provider microsoft.network
info:    Registering provider microsoft.compute
+ Waiting for deployment to complete
data:    DeploymentName     : firstDeployment
data:    ResourceGroupName  : myResourceGroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                   Type          Value
data:    ---------------------  ------------  -------------
data:    newStorageAccountName  String        storageaccount
data:    adminUsername          String        ops
data:    adminPassword          SecureString  undefined
data:    dnsNameForPublicIP     String        newdomainname
data:    ubuntuOSVersion        String        14.10
info:    group deployment create command OK
```


## <a name="a-idcreate-a-custom-vm-imageatask-create-a-custom-vm-image"></a><a id="create-a-custom-vm-image"></a>Feladat: Egyéni virtuális gép rendszerképének létrehozása
A fentiekben már áttekintettük a sablonok alapszintű használatát. Most hasonló utasításokkal hozunk létre egy egyéni virtuális gépet egy adott .vhd fájlból az Azure-ban egy sablon segítségével az Azure CLI-n keresztül. A különbség az, hogy ez a sablon egy megadott virtuális merevlemezből hoz létre egy virtuális gépet.

### <a name="step-1-examine-the-json-file-for-the-template"></a>1. lépés: A sablon JSON-fájljának vizsgálata
Az alábbiakban látható a jelen szakaszban példaként használt sablon JSON-fájljának tartalma. (A sablon a [GitHubon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json) is megtalálható.)

Itt is meg kell keresnie az alapértelmezett értékkel nem rendelkező paraméterekhez megadni kívánt értékeket. Az `azure group deployment create` parancs futtatásakor az Azure CLI kéri ezen értékek megadását.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters" : {
        "userImageStorageAccountName": {
            "type": "string",
            "defaultValue" : "userImageStorageAccountName"
        },
        "userImageStorageContainerName" : {
            "type" : "string",
            "defaultValue" : "userImageStorageContainerName"
        },
        "userImageVhdName" : {
            "type" : "string",
            "defaultValue" : "userImageVhdName"
        },
        "dnsNameForPublicIP" : {
            "type" : "string",
            "defaultValue": "uniqueDnsNameForPublicIP"
        },
        "adminUserName": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "osType" : {
            "type" : "string",
            "allowedValues" : [
                "windows",
                "linux"
            ]
        },
        "subscriptionId":{
            "type" : "string"
        },
        "location": {
            "type": "String",
            "defaultValue" : "West US"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A2"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue" : "myPublicIP"
        },
        "vmName": {
            "type": "string",
            "defaultValue" : "myVM"
        },
        "virtualNetworkName":{
            "type" : "string",
            "defaultValue" : "myVNET"
        },
        "nicName":{
            "type" : "string",
            "defaultValue":"myNIC"
        }
    },
    "variables": {
        "addressPrefix":"10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix" : "10.0.0.0/24",
        "subnet2Prefix" : "10.0.1.0/24",
        "publicIPAddressType" : "Dynamic",
        "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
        "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[parameters('publicIPAddressName')]",
        "location": "[parameters('location')]",
        "properties": {
            "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
            }
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "properties": {
        "addressSpace": {
            "addressPrefixes": [
            "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            {
            "name": "[variables('subnet1Name')]",
            "properties" : {
                "addressPrefix": "[variables('subnet1Prefix')]"
            }
            },
            {
            "name": "[variables('subnet2Name')]",
            "properties" : {
                "addressPrefix": "[variables('subnet2Prefix')]"
            }
            }
        ]
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[parameters('nicName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
        ],
        "properties": {
            "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                    },
                    "subnet": {
                        "id": "[variables('subnet1Ref')]"
                    }
                }
            }
            ]
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
        ],
        "properties": {
            "hardwareProfile": {
                "vmSize": "[parameters('vmSize')]"
            },
            "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
                "osDisk" : {
                    "name" : "[concat(parameters('vmName'),'-osDisk')]",
                    "osType" : "[parameters('osType')]",
                    "caching" : "ReadWrite",
                    "image" : {
                        "uri" : "[variables('userImageName')]"
                    },
                    "vhd" : {
                        "uri" : "[variables('osDiskVhdName')]"
                    }
                }
            },
            "networkProfile": {
                "networkInterfaces" : [
                {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                }
                ]
            }
        }
    }
    ]
}
```

### <a name="step-2-obtain-the-vhd"></a>2. lépés: A VHD beszerzése
Ehhez természetesen szükség van egy .vhd fájlra. Ha már rendelkezik eggyel az Azure-ban, használhatja azt, vagy feltölthet egyet.

Windows-alapú virtuális gép esetében tekintse meg a [Windows Server VHD létrehozása és feltöltése az Azure-ba](../articles/virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) című témakört.

Linux-alapú virtuális gép esetében tekintse meg a [Linux operációs rendszert tartalmazó virtuális merevlemez létrehozása és feltöltése](../articles/virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) című témakört.

### <a name="step-3-create-the-virtual-machine-by-using-the-template"></a>3. lépés: A virtuális gép létrehozása a sablon segítségével
Most már készen áll egy új virtuális gép létrehozására a .vhd alapján. Az `azure group create <location>` segítségével hozzon létre egy csoportot, ahová a központi telepítés történik majd:

```azurecli
azure group create myResourceGroupUser eastus
info:    Executing command group create
+ Getting resource group myResourceGroupUser
+ Creating resource group myResourceGroupUser
info:    Created resource group myResourceGroupUser
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
data:    Name:                myResourceGroupUser
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

Ezután hozza létre a központi telepítést. Ehhez a `--template-uri` kapcsolóval hívja meg a sablont közvetlenül (vagy a `--template-file` kapcsolóval használhat helyileg mentett fájlt). Mivel a sablonban meg vannak adva alapértelmezett értékek, a rendszer csak néhány dolog megadását kéri. Ha különböző helyekre telepíti a sablont, előfordulhat, hogy elnevezési ütközések fordulnak elő az alapértelmezett értékek miatt (különösen a létrehozott DNS-név esetében).

```azurecli
azure group deployment create \
> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
> myResourceGroup \
> customVhdDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
adminUserName: ops
adminPassword: password
osType: linux
subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

A következőhöz hasonló eredményt fog kapni:

```azurecli
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "customVhdDeployment"
+ Registering providers
info:    Registering provider microsoft.network
info:    Registering provider microsoft.compute
+ Waiting for deployment to complete
error:   Deployment provisioning state was not successful
data:    DeploymentName     : customVhdDeployment
data:    ResourceGroupName  : myResourceGroupUser
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                           Type          Value
data:    -----------------------------  ------------  ------------------------------------
data:    userImageStorageAccountName    String        userImageStorageAccountName
data:    userImageStorageContainerName  String        userImageStorageContainerName
data:    userImageVhdName               String        userImageVhdName
data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP
data:    adminUserName                  String        ops
data:    adminPassword                  SecureString  undefined
data:    osType                         String        linux
data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
data:    location                       String        West US
data:    vmSize                         String        Standard_A2
data:    publicIPAddressName            String        myPublicIP
data:    vmName                         String        myVM
data:    virtualNetworkName             String        myVNET
data:    nicName                        String        myNIC
info:    group deployment create command OK
```

## <a name="a-iddeploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balanceratask-deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a><a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>Feladat: Virtuális hálózatot és külső terheléselosztót használó több virtuális gépes alkalmazás üzembe helyezése
Ez a sablon lehetővé teszi, hogy két virtuális gépet hozzon létre egy terheléselosztó alatt, és konfiguráljon egy terheléselosztási szabályt a 80-as porton. Emellett ez a sablon üzembe helyez egy tárfiókot, egy virtuális hálózatot, és nyilvános IP-címet, egy rendelkezésre állási csoportot és hálózati adaptereket is.

![](./media/virtual-machines-common-cli-deploy-templates/multivmextlb.png)

Ezeket a lépéseket követve egy, a GitHub sablontárában lévő Resource Manager-sablon és Azure PowerShell-parancsok segítségével üzembe helyezhet egy több virtuális gépes alkalmazást, amely egy virtuális hálózatot és egy terheléselosztót használ.

### <a name="step-1-examine-the-json-file-for-the-template"></a>1. lépés: A sablon JSON-fájljának vizsgálata
Az alábbiakban látható a sablon JSON-fájljának tartalma. Ha a legújabb verzióra van szüksége, az [a GitHub sablontárában található](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). Ez a témakör a `--template-uri` kapcsolóval hívja meg a sablont, de használhatja a `--template-file` kapcsolót is, ha helyi verziót kíván továbbítani.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of resources"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of storage account"
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name"
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Admin password"
            }
        },
        "dnsNameforLBIP": {
            "type": "string",
            "metadata": {
                "description": "DNS for load balancer IP"
            }
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389,
            "metadata": {
                "description": "Back-end port"
            }
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM",
            "metadata": {
                "description": "Prefix to use for VM names"
            }
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB",
            "metadata": {
                "description": "Load balancer name"
            }
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic",
            "metadata": {
                "description": "Network interface name prefix"
            }
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP",
            "metadata": {
                "description": "Public IP name"
            }
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET",
            "metadata": {
                "description": "Virtual network name"
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "metadata": {
                "description": "Size of the VM"
            }
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "properties": { }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('location')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                            }
                        ]


                    }
                ]

            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('location')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE"

                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfiguration":
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfiguration":
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfiguration": {
                                "id": "[variables('frontEndIPConfigID')]"
                            },
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="step-2-create-the-deployment-by-using-the-template"></a>2. lépés: A központi telepítés létrehozása a sablonnal
Az `azure group create <location>` paranccsal hozzon létre egy erőforráscsoportot a sablonhoz. Ezután hozzon létre egy központi telepítést ebbe az erőforráscsoportba az `azure group deployment create` paranccsal. Ehhez át kell adnia az erőforráscsoportot, a központi telepítés nevét, valamint meg kell adnia a sablon alapértelmezett értékkel nem rendelkező paramétereinek értékét, amikor a rendszer erre kéri.

```azurecli
azure group create lbgroup westus
info:    Executing command group create
+ Getting resource group lbgroup
+ Creating resource group lbgroup
info:    Created resource group lbgroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
data:    Name:                lbgroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

Most az `azure group deployment create` parancs és a `--template-uri` kapcsoló segítségével telepítse a sablont. Készítse elő a paraméterértékeket, hogy megadja őket, amikor a rendszer kéri, ahogyan az az alábbiakban látható.

```azurecli
azure group deployment create \
> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
> lbgroup \
> newdeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
location: westus
newStorageAccountName: storagename
adminUsername: ops
adminPassword: password
dnsNameforLBIP: lbdomainname
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "newdeployment"
+ Registering providers
info:    Registering provider microsoft.storage
info:    Registering provider microsoft.compute
info:    Registering provider microsoft.network
+ Waiting for deployment to complete
data:    DeploymentName     : newdeployment
data:    ResourceGroupName  : lbgroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                   Type          Value
data:    ---------------------  ------------  ----------------------
data:    location               String        westus
data:    newStorageAccountName  String        storagename
data:    adminUsername          String        ops
data:    adminPassword          SecureString  undefined
data:    dnsNameforLBIP         String        lbdomainname
data:    backendPort            Int           3389
data:    vmNamePrefix           String        myVM
data:    imagePublisher         String        MicrosoftWindowsServer
data:    imageOffer             String        WindowsServer
data:    imageSKU               String        2012-R2-Datacenter
data:    lbName                 String        myLB
data:    nicNamePrefix          String        nic
data:    publicIPAddressName    String        myPublicIP
data:    vnetName               String        myVNET
data:    vmSize                 String        Standard_A1
info:    group deployment create command OK
```

Ez a sablon egy Windows Server rendszerképet telepít, de könnyen helyettesíthető lenne bármilyen Linux rendszerképpel is. Szeretne létrehozni egy Docker-fürtöt több Swarm-kezelővel? [Megteheti](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

## <a name="a-idremove-a-resource-groupatask-remove-a-resource-group"></a><a id="remove-a-resource-group"></a>Feladat: Erőforráscsoport eltávolítása
Ne feledje, hogy újból is végezhet telepítést egy erőforráscsoportba, de ha valamelyikre már nincs szüksége, az `azure group delete <group name>` paranccsal törölheti.

```azurecli
azure group delete myResourceGroup
info:    Executing command group delete
Delete resource group myResourceGroup? [y/n] y
+ Deleting resource group myResourceGroup
info:    group delete command OK
```

## <a name="a-idshow-the-log-for-a-resource-group-deploymentatask-show-the-log-for-a-resource-group-deployment"></a><a id="show-the-log-for-a-resource-group-deployment"></a>Feladat: Erőforráscsoport központi telepítési naplójának megjelenítése
Ez azonos a sablonok létrehozásakor és használatakor. Egy csoport központi telepítési naplója az `azure group log show <groupname>` paranccsal jeleníthető meg. Ez a parancs viszonylag sok információt jelenít meg, ami hasznos lehet annak megértéséhez, hogy miért – vagy miért nem – történt meg egy adott művelet. (Az üzemelő példányok hibaelhárításával, illetve általánosságban a hibákkal kapcsolatban az [Azure-telepítések gyakori hibáinak elhárítása az Azure Resource Managerrel](../articles/azure-resource-manager/resource-manager-common-deployment-errors.md) témájú cikkben talál további információt.)

Konkrét problémák megoldásához hasznosak lehetnek az olyan eszközök, mint például a **jq**, amellyel pontosabban lekérdezhetők különböző adatok, például az egyes javítandó hibák. Az alábbi példában a **jq** segítségével elemezzük az **lbgroup** telepítési naplóját az esetleges hibák kimutatásához.

```azurecli
azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'
```
A problémát gyorsan felismerheti, elháríthatja, majd megpróbálkozhat a kérdéses művelet újbóli végrehajtásával. A következő esetben a sablon egyszerre két virtuális gépet hozott létre, amely a .vhd fájlnál zárolást eredményezett. (A sablon módosítását követően az üzembe helyezés gyorsan és sikeresen megtörtént.)

```json
{
    "statusCode": "Conflict",
    "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"AcquireDiskLeaseFailed\",\"message\":\"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd.\"}]}}"
}
```

## <a name="a-iddisplay-information-about-a-virtual-machineatask-display-information-about-a-virtual-machine"></a><a id="display-information-about-a-virtual-machine"></a>Feladat: Virtuális gépre vonatkozó információk megjelenítése
Az adott erőforráscsoportba tartozó egyes virtuális gépek információit az `azure vm show <groupname> <vmname>` paranccsal lehet megtekinteni. Ha a csoportban több virtuális gép is található, előfordulhat, hogy először ki kell őket listázni az `azure vm list <groupname>` segítségével.

```azurecli
azure vm list zoo
info:    Executing command vm list
+ Getting virtual machines
data:    Name   ProvisioningState  Location  Size
data:    -----  -----------------  --------  -----------
data:    myVM0  Succeeded          westus    Standard_A1
data:    myVM1  Failed             westus    Standard_A1
```

Ezután keresheti meg a myVM1-et:

```azurecli
azure vm show zoo myVM1
info:    Executing command vm show
+ Looking up the VM "myVM1"
+ Looking up the NIC "nic1"
data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Failed
data:    Name                            :myVM1
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :MicrosoftWindowsServer
data:        Offer                       :WindowsServer
data:        Sku                         :2012-R2-Datacenter
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Windows
data:        Name                        :osdisk
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Windows Configuration:
data:        Provision VM Agent          :true
data:        Enable automatic updates    :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
data:          Primary                   :false
data:          Provisioning State        :Succeeded
data:          Name                      :nic1
data:          Location                  :westus
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :10.0.0.5
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
info:    vm show command OK
```

> [!NOTE]
> Ha szeretné programozottan tárolni és módosítani a konzolparancsok kimenetét, érdemes lehet egy JSON-elemzőeszközt (például **[jq](https://github.com/stedolan/jq)** vagy **[jsawk](https://github.com/micha/jsawk)**) vagy a feladatnak megfelelő nyelvi kódtárakat használni.
>
>

## <a name="a-idlog-on-to-a-linux-based-virtual-machineatask-log-on-to-a-linux-based-virtual-machine"></a><a id="log-on-to-a-linux-based-virtual-machine"></a>Feladat: Bejelentkezés egy Linux-alapú virtuális gépre
A Linux-alapú gépek csatlakoztatása általában SSH-n keresztül történik. További információ: [SSH használata Linuxon az Azure-on](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="a-idstop-a-virtual-machineatask-stop-a-vm"></a><a id="stop-a-virtual-machine"></a>Feladat: Virtuális gép leállítása
Futtassa ezt a parancsot:

```azurecli
azure vm stop <group name> <virtual machine name>
```

> [!IMPORTANT]
> Ennek a paraméternek a használatával megtarthatja a virtuális hálózat virtuális IP-címét, ha a virtuális gép az utolsó az adott virtuális hálózatban. <br><br> A `StayProvisioned` paraméter használata esetén is a virtuális gép után fog díjat fizetni.
>
>

## <a name="a-idstart-a-virtual-machineatask-start-a-vm"></a><a id="start-a-virtual-machine"></a>Feladat: Virtuális gép indítása
Futtassa ezt a parancsot:

```azurecli
azure vm start <group name> <virtual machine name>
```

## <a name="a-idattach-a-data-diskatask-attach-a-data-disk"></a><a id="attach-a-data-disk"></a>Feladat: Adatlemez csatolása
El kell döntenie, hogy új vagy adatokat már tartalmazó lemezt szeretne-e csatolni. Új lemez esetén a parancs létrehozza és egyben csatolja is a .vhd fájlt.

Új lemez csatolásához futtassa ezt a parancsot:

```azurecli
    azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>
```

Már meglévő adatlemez csatolásakor futtassa ezt a parancsot:

```azurecli
azure vm disk attach <resource-group> <vm-name> [vhd-url]
```

Ezt követően a lemezt a Linux esetében megszokott módon csatlakoztatni kell.

## <a name="next-steps"></a>Következő lépések
Számos további, az Azure CLI és az **arm** mód együttes használatával kapcsolatos példát találhat [a Mac, Linux és Windows eszközökhöz készült Azure CLI Azure Resource Managerrel történő használatával](../articles/xplat-cli-azure-resource-manager.md) foglalkozó cikkben. További információk az Azure-erőforrásokról és a kapcsolódó fogalmakról: [Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md).

További felhasználható sablonokat az [Azure-gyorssablonok](https://azure.microsoft.com/documentation/templates/) és a [Sablonokat használó alkalmazás-keretrendszerek](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) között talál.


<!--HONumber=Jan17_HO4-->


