---
title: Windows Azure-ban futó Service Fabric-fürt létrehozása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan helyezheti üzembe Windows Service Fabric-fürtön az Azure virtuális hálózat és a hálózati biztonsági csoport PowerShell-lel.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 28f115e356c8852174b923f4891f93ad435ce7d7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498162"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Oktatóanyag: Windows rendszerű Azure-beli virtuális hálózatban a Service Fabric-fürt üzembe helyezése

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan-rendszert futtató, Windows Azure Service Fabric-fürt üzembe helyezése egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md) és [hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md) PowerShell és a egy sablon használatával. Ha elkészült, rendelkezni, amelyre alkalmazásokat telepíthet a felhőben futó fürttel. Az Azure CLI Linux-fürt létrehozása: [biztonságos Linux-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Ez az oktatóanyag egy éles forgatókönyvet ismertet. Ha azt szeretné, hozzon létre egy kisebb fürtöt tesztelési célokra, lásd: [egy tesztfürt létrehozása](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * Az Azure Active Directory-hitelesítés beállítása
> * Diagnosztikai gyűjtésének konfigurálása
> * Az EventStore szolgáltatás beállítása
> * Állítsa be az Azure Monitor naplóira
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos fürt létrehozása az Azure-ban
> * [-Fürt monitorozása](service-fabric-tutorial-monitor-cluster.md)
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Service Fabric SDK és a PowerShell-modult](service-fabric-get-started.md).
* Telepítse a [Azure Powershell-modul 4.1-es vagy újabb verzióját](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Tekintse át a kapcsolatos főbb fogalmakat [Azure fürtök](service-fabric-azure-clusters-overview.md).
* [Megtervezheti és előkészítheti a](service-fabric-cluster-azure-deployment-preparation.md) éles fürt üzembe helyezéséhez.

Az alábbi eljárással létrehozunk egy hét csomópontos Service Fabric-fürtön. Használja a [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) Service Fabric-fürtön az Azure-ban való futtatásával járó költségek kiszámításához.

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse le a következő Azure Resource Manager-sablonfájlokat:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Ez a sablon egy virtuális hálózatot és a egy hálózati biztonsági csoport hét virtuális gépek és a három csomóponttípusok biztonságos fürt üzembe helyezése.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál. A [azuredeploy.json] [ template] számos, többek között az alábbiakat telepíti.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban egy Windows-fürt az alábbi jellemzőkkel lesz konfigurálva:

* Három csomópont típusa.
* Öt csomópont van az elsődleges csomóponttípusban (a sablon paramétereiben konfigurálható), és a egy csomópont minden más két csomóponttípus.
* Operációs rendszer: A Windows Server 2016 Datacenter tárolókkal (a sablon paramétereiben konfigurálható).
* Tanúsítványon alapuló védelem (a sablon paramétereiben konfigurálható).
* [Fordított proxy](service-fabric-reverseproxy.md) engedélyezve van.
* [DNS-szolgáltatás](service-fabric-dnsservice.md) engedélyezve van.
* [Tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) bronz (a sablon paramétereiben konfigurálható).
* [Megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) ezüst (a sablon paramétereiben konfigurálható).
* ügyfélkapcsolati végpont: 19000 (a sablon paramétereiben konfigurálható).
* HTTP-átjáró végpontja: a 19080-as (a sablon paramétereiben konfigurálható).

### <a name="azure-load-balancer"></a>Azure Load Balancer

Az a **Microsoft.Network/loadBalancers** erőforrás, egy terheléselosztó van konfigurálva. Minták és szabályok vannak beállítva a következő portokat:

* ügyfélkapcsolati végpont: 19000
* HTTP-átjáró végpontja: 19080
* alkalmazásport: 80
* alkalmazásport: 443
* A Service Fabric fordított proxy: 19081

Ha más alkalmazás portok van szükség, módosítani kell a **Microsoft.Network/loadBalancers** erőforrás és a **Microsoft.Network/networkSecurityGroups** engedélyezi a forgalmat az erőforrás.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuális hálózat, alhálózat és hálózati biztonsági csoport

A virtuális hálózat, az alhálózat és a hálózati biztonsági csoport neve a sablon paramétereiben van meghatározva. A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* virtuális hálózat címtere: 172.16.0.0/20
* A Service Fabric-alhálózat címtere: 172.16.2.0/23

Az alábbi bejövő forgalmi szabályok vannak engedélyezve a **Microsoft.Network/networkSecurityGroups** erőforrásban. A portok értékét a sablon változóinak módosításával módosíthatja.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Csomópontok közötti kommunikáció: 1025, 1026, 1027
* A rövid élettartamú porttartomány: 49152 65534 (legalább 256 port szükséges).
* Alkalmazások által használható portok: 80 és 443
* Alkalmazás porttartománya: 49152 65534 (szolgáltatások közötti kommunikációhoz használt. Más portok nem nyílik meg a terheléselosztó).
* összes többi port letiltása.

Ha más alkalmazás portok van szükség, módosítani kell a **Microsoft.Network/loadBalancers** erőforrás és a **Microsoft.Network/networkSecurityGroups** engedélyezi a forgalmat az erőforrás.

### <a name="windows-defender"></a>Windows Defender
Alapértelmezés szerint a [a Windows Defender víruskereső program](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) telepítve és működőképes a Windows Server 2016-on. A felhasználói felület néhány termékváltozat alapértelmezés szerint telepítve van, de nincs szükség. Minden egyes csomópont típusa vagy Virtuálisgép-méretezési deklarált a sablonban a [Azure virtuális gép az Antimaleware bővítmény](/azure/virtual-machines/extensions/iaas-antimalware-windows) szolgál a Service Fabric-könyvtárak és folyamatok kizárása:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Sablon paramétereinek megadása

Az [azuredeploy.parameters.json][parameters] paraméterfájl számos, a fürt és a társított erőforrások üzembe helyezéséhez használt értéket meghatároz. Módosíthatja az üzemelő példány paramétereit a következők:

**A paraméter** | **Példaérték** | **Megjegyzések** 
|---|---|---|
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez. [Virtuális gép követelményei felhasználónév](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez. [Virtuális gép jelszókövetelményeinek](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| A fürt neve. Csak betűket és számokat tartalmazhat. 3–23 karakter hosszú lehet.|
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány SHA1 ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3”</p> |
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie. </p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Például "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory-ügyfél-hitelesítés beállítása
Service Fabric-fürtök az Azure-ban üzemeltetett nyilvános hálózaton üzembe helyezett az ügyfél és a csomópont közötti kölcsönös hitelesítés ajánlás van:
* Azure Active Directory használata az ügyfelek identitását.
* Tanúsítvány használata a kiszolgáló identitása és a HTTP-kommunikációhoz SSL-titkosítást.

Az Azure Active Directory (Azure AD) beállítása a Service Fabric-fürt-ügyfelek hitelesítésére kell elvégezni, mielőtt [a fürt létrehozása](#createvaultandcert). Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). 

Service Fabric-fürt kínál a különböző belépési pontok annak felügyeleti funkciójához, beleértve a webalapú [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) és [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Ennek eredményeképpen hoz létre, ki férhet hozzá a fürt két Azure AD-alkalmazások: egy webalkalmazás és a egy natív alkalmazást.  Az alkalmazások létrehozása után felhasználók hozzárendelése csak olvasható, és rendszergazdai szerepkörök.

> [!NOTE]
> A következő lépéseket kell elvégeznie, a fürt létrehozása előtt. A parancsfájlok várhatóan a fürt nevét és a végpontok, mert az értékeket meg kell tervezni, és nem az, hogy már létrehozott értékeket.

Ez a cikk feltételezzük, hogy már létrehozott egy bérlőt. Ha még nem, először olvassa el [Azure Active Directory-bérlő beszerzése](../active-directory/develop/quickstart-create-new-tenant.md).

Szükséges lépések eltérőek konfigurálása az Azure AD-ben a Service Fabric-fürt leegyszerűsítése létrehoztuk a Windows PowerShell-parancsprogramok halmaza. [Töltse le a parancsfájlok](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) a számítógépre.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Az Azure AD-alkalmazások létrehozása és a felhasználók szerepkörökhöz rendelése
Hozzon létre két Azure AD-alkalmazást a fürthöz való hozzáférés szabályozásához: egy webalkalmazás és a egy natív alkalmazást. Az alkalmazások, amelyek a fürt létrehozása után rendelje hozzá a felhasználókat, hogy a [szerepkörök a Service Fabric által támogatott](service-fabric-cluster-security-roles.md): csak olvasható és a rendszergazdával.

Futtatás `SetupApplications.ps1`, és meg paraméterekként a bérlő azonosítója, a fürt neve és a webes alkalmazás válasz URL-cím. Adja meg a felhasználóneveket és jelszavakat, a felhasználók számára. Példa:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Az országos felhők (például az Azure Government, Azure China esetén az Azure Germany), adja meg a `-Location` paraméter.

Annak a *TenantId*, vagy a címtár-azonosító, az a [az Azure portal](https://portal.azure.com). Válassza ki **Azure Active Directory** > **tulajdonságok** , és másolja a **címtár-azonosító** értéket.

*ClusterName* az Azure AD-alkalmazások, a parancsfájl által létrehozott előtagot használja. Ennek nem kell pontosan egyezik a tényleges fürt neve. Csak egyszerűbbé teszi az Azure AD-összetevők leképezése a Service Fabric-fürt használja.

*WebApplicationReplyUrl* van az alapértelmezett végpont, amely az Azure AD a felhasználóknak ad vissza, miután a bejelentkezés befejezéséhez. Állítsa be ezt a végpontot a Service Fabric Explorert a fürtben, amely alapértelmezés szerint az átemelt:

https://&lt;cluster_domain&gt;:19080/Explorer

Kéri, hogy jelentkezzen be az Azure AD-bérlőhöz tartozó rendszergazdai jogosultságokkal rendelkező fiókkal. Miután bejelentkezett, a parancsfájl a webes és natív alkalmazások, amelyek a Service Fabric-fürtöt hoz létre. A bérlő alkalmazásaiban az [az Azure portal](https://portal.azure.com), két új bejegyzést kell megjelennie:

   * *ClusterName*\_fürt
   * *ClusterName*\_ügyfél

A parancsfájl megjeleníti a JSON, így célszerű hagyja megnyitva a PowerShell-ablakot, hogy a fürt létrehozásakor a Resource Manager sablonhoz szükséges.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD használata az ügyfél hozzáférésének az Azure AD-konfiguráció hozzáadása
Az a [azuredeploy.json][template], konfigurálja az Azure AD-t a **Microsoft.ServiceFabric/clusters** szakaszban. Paraméterek hozzáadása a bérlő azonosítója, fürt Alkalmazásazonosító és ügyfél-alkalmazás azonosítója.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Adja hozzá a paraméter értékét a [azuredeploy.parameters.json] [ parameters] paramétereket tartalmazó fájlt. Példa:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Diagnosztikai adatgyűjtési konfigurálása a fürtön
Service Fabric-fürtön futtatja, esetén érdemes egy központi helyen összes csomópontja a naplók gyűjtését. A naplók kellene egy központi helyen segítségével elemezheti és a fürtben, vagy az alkalmazások és szolgáltatások a fürtben futó problémák elhárítása.

Fel-és naplók gyűjtése az egyik módja az Azure Diagnostics (WAD) bővítménye, amely feltölti a naplókat az Azure Storage, és a naplók elküldése az Azure Application Insights és az Event Hubs lehetősége is van. A külső folyamatok használatával olvassa az eseményeket a storage-ból, és a egy elemzési platform termék, például az Azure Monitor naplóira vagy egy másik log-elemzési megoldás helyezze el őket.

Ha az ebben az oktatóanyagban, diagnosztikai gyűjteményét már konfigurálva van az a [sablon][template].

Ha meglévő fürtöt, amely nem rendelkezik telepített diagnosztikai, adja hozzá, vagy frissítse a fürt sablon használatával. Módosítsa a Resource Manager-sablon, amellyel a meglévő fürt létrehozásához, vagy letöltheti a sablont a portálról. Módosítsa a template.json fájlt az alábbi feladatok végrehajtásával:

Új storage-erőforrás hozzáadása az erőforrások szerepelnek a sablonban:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Ezután adjon hozzá paramétereket a tárfiók nevét, és írja be a sablon a paraméterek szakaszához. Szeretné, cserélje le a helyőrző szöveg tárfiók neve kerül itt nevét a storage-fiók.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Ezután adja hozzá a **IaaSDiagnostics** tömbjének extensions kiterjesztése a **VirtualMachineProfile** minden egyes tulajdonság **Microsoft.Compute/virtualMachineScaleSets** az erőforrás a fürtben.  Ha használja a [mintasablon][template], nincsenek a három virtuális gép méretezési (az egyes csomóponttípusok a fürt egyik).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Az EventStore szolgáltatás konfigurálása
Az EventStore szolgáltatás figyelési lehetőség csak a Service Fabricben. Az EventStore lehetővé teszi a fürt vagy az adott számítási feladatok állapotának megjelenítése az idő. Az EventStore egy állapotalapú Service Fabric-szolgáltatás, amely fenntartja az események a fürtből. Az esemény a Service Fabric Explorert, REST és API-k keresztül érhetők el. EventStore lekérdezi a közvetlenül a diagnosztikai adatok gyorsan minden entitás, a fürtben a fürt, és könnyebben használható:

* Fejlesztési vagy tesztelési időben diagnosztizálhatja a problémákat, vagy ahol a monitorozási folyamatban használni lehet, hogy
* Győződjön meg arról, hogy felügyeleti műveleteket a fürtön készítésének feldolgozott megfelelően
* "Pillanatkép" hogyan Service Fabric egy adott entitás implementálására beolvasása



A fürtön a EventStore szolgáltatás engedélyezéséhez adja hozzá a következőt a **fabricSettings** tulajdonságát a **Microsoft.ServiceFabric/clusters** erőforrás:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Állítsa be a fürt az Azure Monitor naplóira

Az Azure Monitor naplóira Javaslataink fürt események figyelésére. Az Azure Monitor naplóira be, a fürt monitorozásához, rendelkeznie kell [diagnosztika engedélyezve van a fürt-szintű események megtekintéséhez](#configure-diagnostics-collection-on-the-cluster).  

A munkaterület csatlakoztatva kell lennie a fürt származó diagnosztikai adatokhoz.  A napló adatokat tárolja a *applicationDiagnosticsStorageAccountName* tárfiókot, a WADServiceFabric * címke WADWindowsEventLogsTable és WADETWEventTable táblákat.

Adja hozzá az Azure Log Analytics-munkaterületet, és a megoldás a munkaterület hozzáadása:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Ezután adjon hozzá paramétereket
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Ezután adjon hozzá változókat:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Adja hozzá a Log Analytics agent bővítmény minden egyes virtuálisgép-méretezési állítsa be a fürt és az ügynök csatlakoztatása a Log Analytics-munkaterületre. Ez lehetővé teszi a tárolók, alkalmazások és alkalmazásteljesítmény-figyelési gyűjtését diagnosztikai adatait. Bővítményeként való hozzáadásával a virtuálisgép-méretezési készlet erőforrás, az Azure Resource Manager biztosítja, hogy megkapja-e telepítve minden csomóponton, még ha méretezése a fürtben.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. A [azuredeploy.json] [ template] Resource Manager-sablont hoz létre egy virtuális hálózat, alhálózat és hálózati biztonsági csoport a Service Fabric. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal. Éles fürtök esetén egy hitelesítésszolgáltatótól származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

Ez a cikk a sablon üzembe helyez egy fürtöt, amely a tanúsítvány ujjlenyomatát a fürttanúsítvány azonosítására használ. Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a tanúsítványkezelés is rendelkezhet. A tanúsítvány köznapi nevek a tanúsítvány-ujjlenyomatok egy üzembe helyezett fürt közötti váltás egyszerűbbé teszi a tanúsítványok kezelése. A tanúsítvány köznapi nevek a tanúsítványok kezeléséhez használja a fürt frissítése, olvassa el [tanúsítvány köznapi neve felügyeleti fürt módosítása](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

Az alábbi szkript a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) parancsmag és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancsmag létrehoz egy új kulcstárolót az Azure-ban, és feltölti a tanúsítványt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Fürt létrehozása az új, önaláírt tanúsítvány használatával

Az alábbi szkript a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) parancsmag és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancsmag létrehoz egy új kulcstárolót az Azure-ban, hozzáad egy új önaláírt tanúsítványt a key vaultban, és letölti helyben a tanúsítványfájlt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz

Csatlakozzon a fürthöz a Service Fabric SDK telepítése a Service Fabric PowerShell-modul segítségével.  Először telepítse a tanúsítványt a számítógépen az aktuális felhasználó Személyes (Saját) tárolójába. Futtassa az alábbi PowerShell-parancsot:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Most már készen áll a biztonságos fürthöz való csatlakozáshoz.

A **Service Fabric** PowerShell-modul számos parancsmagot biztosít a Service Fabric-fürtök, -alkalmazások és -szolgáltatások kezelésére. A biztonságos fürthöz való kapcsolódáshoz használja a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmagot. A tanúsítvány SHA1 ujjlenyomatával és kapcsolati végpontjával kapcsolatos részletek az előző lépés kimenetében találhatók.

Ha korábban már beállított Azure AD ügyfél-hitelesítés, futtassa a következő parancsot: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Ha nem állít be az Azure AD ügyfél-hitelesítés, futtassa a következő parancsot:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Ellenőrizze, hogy csatlakozott, és a fürt állapota kifogástalan a használatával a [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) parancsmagot.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag-sorozatban található további cikkek a létrehozott fürt használja. Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](service-fabric-cluster-delete.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyagra lépve megismerheti a fürtök skálázásának.

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * Az Azure Active Directory-hitelesítés beállítása
> * Diagnosztikai gyűjtésének konfigurálása
> * Az EventStore szolgáltatás beállítása
> * Állítsa be az Azure Monitor naplóira
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ezután folytassa a következő oktatóanyaggal, a fürt figyelése.
> [!div class="nextstepaction"]
> [-Fürt monitorozása](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
