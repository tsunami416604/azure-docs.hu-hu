---
title: Windows rendszert futtató Service Fabric-fürt létrehozása az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan telepíthet egy Windows Service Fabric-fürtöt egy Azure virtuális hálózatra és hálózati biztonsági csoportra a PowerShell használatával.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 086379e788966b300f988e06ec42c94b880b8281
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551718"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Oktatóanyag: Windows rendszert futtató Service Fabric-fürt telepítése egy Azure virtuális hálózatba

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan telepíthet egy Windows t futtató Azure Service Fabric-fürtöt egy [Azure virtuális hálózatba](../virtual-network/virtual-networks-overview.md) és [hálózati biztonsági csoportba](../virtual-network/virtual-networks-nsg.md) a PowerShell és egy sablon használatával. Ha elkészült, egy fürt fut a felhőben, amelyalkalmazásokat telepíthet. Az Azure CLI-t használó Linux-fürt létrehozásáról: [Biztonságos Linux-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-linux-cluster.md)című témakörben.

Ez az oktatóanyag egy éles forgatókönyvet ismertet. Ha tesztelési célokra kisebb fürtöt szeretne létrehozni, olvassa [el a Tesztfürt létrehozása](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)című témakört.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * Az Azure Active Directory-hitelesítés beállítása
> * Diagnosztikai gyűjtemény konfigurálása
> * Az EventStore szolgáltatás beállítása
> * Az Azure Monitor-naplók beállítása
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos fürt létrehozása az Azure-ban
> * [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse a [Service Fabric SDK és a PowerShell modult.](service-fabric-get-started.md)
* Telepítse [az Azure Powershellt.](https://docs.microsoft.com/powershell/azure/install-Az-ps)
* Tekintse át az [Azure-fürtök legfontosabb fogalmait.](service-fabric-azure-clusters-overview.md)
* [Tervezze meg és készüljön fel](service-fabric-cluster-azure-deployment-preparation.md) az éles fürt központi telepítésére.

A következő eljárások hozzon létre egy hét csomópontos Service Fabric-fürt. Az [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) használatával kiszámíthatja a Service Fabric-fürt azure-beli futtatásával kapcsolatban felmerült költségeket.

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse le a következő Azure Resource Manager-sablonfájlokat:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Ez a sablon egy hét virtuális gépből és három csomóponttípusból álló biztonságos fürtöt telepít egy virtuális hálózatba és egy hálózati biztonsági csoportba.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál. Az [azuredeploy.json][template] számos erőforrást telepít, beleértve a következőket.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban egy Windows-fürt az alábbi jellemzőkkel lesz konfigurálva:

* Három csomóponttípus.
* Öt csomópont az elsődleges csomópont típusában (konfigurálható a sablon paraméterek), és egy-egy csomópont minden a másik két csomóponttípusok.
* OS: Windows Server 2016 Datacenter tárolókkal (a sablon paraméterekben konfigurálható).
* Tanúsítvány védett (konfigurálható a sablon paraméterek).
* [A fordított proxy](service-fabric-reverseproxy.md) engedélyezve van.
* [A DNS-szolgáltatás](service-fabric-dnsservice.md) engedélyezve van.
* [Bronz tartóssági szintje](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (konfigurálható a sablon paraméterekben).
* [Silver megbízhatósági szintje](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható).
* Ügyfélkapcsolatvégpont: 19000 (a sablon paraméterekben konfigurálható).
* HTTP átjáró végpont: 19080 (konfigurálható a sablon paraméterek).

### <a name="azure-load-balancer"></a>Azure Load Balancer

A **Microsoft.Network/loadBalancers** erőforrásban egy terheléselosztó van konfigurálva. A mintavételek és a szabályok a következő portokhoz vannak beállítva:

* Ügyfélkapcsolatvégpont: 19000
* HTTP-átjáró végpontja: 19080;
* Alkalmazási port: 80
* Alkalmazási port: 443
* Service Fabric fordított proxyja: 19081.

Ha más alkalmazásportokra van szükség, módosítania kell a **Microsoft.Network/loadBalancers** erőforrást és a **Microsoft.Network/networkSecurityGroups** erőforrást a forgalom engedélyezéséhez.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuális hálózat, alhálózat és hálózati biztonsági csoport

A virtuális hálózat, az alhálózat és a hálózati biztonsági csoport neve a sablon paramétereiben van meghatározva. A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg, és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* Virtuális hálózati címtér: 172.16.0.0/20
* Service Fabric-alhálózat címtere: 172.16.2.0/23.

Az alábbi bejövő forgalmi szabályok vannak engedélyezve a **Microsoft.Network/networkSecurityGroups** erőforrásban. A portok értékét a sablon változóinak módosításával módosíthatja.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Efemer port tartomány: 49152-65534 (legalább 256 portra van szükség).
* alkalmazások által használható portok: 80 és 443,
* Alkalmazási port tartomány: 49152-65534 (szolgáltatás-szolgáltatás kommunikációra szolgál. Más portok nincsenek megnyitva a terheléselosztón).
* összes többi port letiltása.

Ha más alkalmazásportokra van szükség, módosítania kell a **Microsoft.Network/loadBalancers** erőforrást és a **Microsoft.Network/networkSecurityGroups** erőforrást a forgalom engedélyezéséhez.

### <a name="windows-defender"></a>Windows Defender
Alapértelmezés szerint a [Windows Defender víruskereső program](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) telepítve van és működőképes a Windows Server 2016 rendszeren. A felhasználói felület alapértelmezés szerint telepítve van néhány ska-ra, de nem szükséges. A sablonban deklarált minden csomóponttípus/virtuálisgép-méretezési csoport esetében az [Azure VM antimalware bővítménye](/azure/virtual-machines/extensions/iaas-antimalware-windows) kizárja a Service Fabric-könyvtárakat és -folyamatokat:

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

Az [azuredeploy.parameters.json][parameters] paraméterfájl számos, a fürt és a társított erőforrások üzembe helyezéséhez használt értéket meghatároz. A telepítéshez módosítani kívánt paraméterek az alábbiak:

**Paraméter** | **Példa értéke** | **Megjegyzések** 
|---|---|---|
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez. [A virtuális gép felhasználónévre vonatkozó követelményei](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez. [A virtuális gép jelszavas követelményei](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| A fürt neve. Csak betűket és számokat tartalmazhat. 3–23 karakter hosszú lehet.|
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány SHA1 ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3”</p> |
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie. </p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Például a "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Az Azure Active Directory-ügyfélhitelesítés beállítása
Az Azure-ban üzemeltetett nyilvános hálózatban telepített Service Fabric-fürtök esetében az ügyfél-csomópont kölcsönös hitelesítésre vonatkozó javaslat a következő:
* Használja az Azure Active Directoryt az ügyfélidentitáshoz.
* A HTTP-kommunikáció kiszolgálóidentitásához és SSL-titkosításához használjon tanúsítványt.

Az Azure Active Directory (Azure AD) beállítása a Service Fabric-fürt ügyfeleinek hitelesítéséhez [a fürt létrehozása](#createvaultandcert)előtt el kell végezni. Az Azure AD lehetővé teszi a szervezetek (más néven bérlők) az alkalmazások felhasználói hozzáférésének kezelése. 

A Service Fabric-fürt számos belépési pontot kínál a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorert](service-fabric-visualizing-your-cluster.md) és a [Visual Studio-t.](service-fabric-manage-application-in-visual-studio.md) Ennek eredményeképpen hozzon létre két Azure AD-alkalmazást a fürthöz való hozzáférés szabályozásához: egy webalkalmazás és egy natív alkalmazás.  Az alkalmazások létrehozása után a felhasználókat írásvédett és rendszergazdai szerepkörökhöz rendeli.

> [!NOTE]
> A fürt létrehozása előtt végre kell hajtsa végre a következő lépéseket. Mivel a parancsfájlok fürtneveket és végpontokat várnak, az értékeket meg kell tervezni, nem pedig a már létrehozott értékeket.

Ebben a cikkben feltételezzük, hogy már létrehozott egy bérlőt. Ha még nem, kezdje el olvasni [Az Azure Active Directory-bérlők beolvasása](../active-directory/develop/quickstart-create-new-tenant.md).

Az Azure AD Service Fabric-fürttel történő konfigurálása során bekövetkező lépések egyszerűsítése érdekében létrehoztunk egy Windows PowerShell-parancsfájlkészletet. [Töltse le a parancsfájlokat](https://github.com/Azure-Samples/service-fabric-aad-helpers) a számítógépre.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD-alkalmazások létrehozása és felhasználók hozzárendelése szerepkörökhöz
Hozzon létre két Azure AD-alkalmazást a fürthöz való hozzáférés szabályozásához: egy webalkalmazást és egy natív alkalmazást. Miután létrehozta a fürtet képviselő alkalmazásokat, rendelje hozzá a felhasználókat a [Service Fabric által támogatott szerepkörökhöz:](service-fabric-cluster-security-roles.md)csak olvasható és rendszergazda.

Futtassa `SetupApplications.ps1`a , és adja meg a bérlőazonosítót, a fürt nevét és a webalkalmazás válaszÁNAK URL-címét paraméterekként. Adja meg a felhasználók felhasználónevét és jelszavát. Példa:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> A nemzeti felhők (például az Azure Government, `-Location` az Azure China, az Azure Germany) esetében adja meg a paramétert.

A *TenantId*vagy a címtár-azonosító az [Azure Portalon](https://portal.azure.com)található. Válassza az **Azure Active Directory** > **tulajdonságai lehetőséget,** és másolja a **címtárazonosító** értékét.

*A ClusterName* a parancsfájl által létrehozott Azure AD-alkalmazások előtagozására szolgál. Nem kell pontosan egyeznie a tényleges fürt nevével. Csak megkönnyíti az Azure AD-összetevők leképezése a használatban lévő Service Fabric-fürthöz.

*A WebApplicationReplyUrl* az az alapértelmezett végpont, amelyet az Azure AD a bejelentkezés befejezése után visszaküld a felhasználóknak. Állítsa be ezt a végpontot a fürt Service Fabric Explorer-végpontjaként, amely alapértelmezés szerint a következő:

https://&lt;&gt;cluster_domain :19080/Explorer

A rendszer kéri, hogy jelentkezzen be egy olyan fiókba, amely rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. Miután bejelentkezik, a parancsfájl létrehozza a webes és natív alkalmazásokat a Service Fabric-fürt képviseletére. A bérlői alkalmazások az [Azure Portalon,](https://portal.azure.com)meg kell jelennie két új bejegyzést:

   * *Fürtnév*\_fürt
   * *Fürtnév-ügyfél*\_

A parancsfájl kinyomtatja a JSON által igényelt Resource Manager sablon a fürt létrehozásakor, ezért célszerű nyitva tartani a PowerShell ablak.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD-konfiguráció hozzáadása az Azure AD ügyfélhozzáféréshez való használatához
Az [azuredeploy.json ban][template]konfigurálja az Azure AD-t a **Microsoft.ServiceFabric/clusters** szakaszban. Adja hozzá a bérlői azonosító, a fürtalkalmazás-azonosító és az ügyfélalkalmazás-azonosító paramétereit.  

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

Adja hozzá a paraméterértékeket az [azuredeploy.parameters.json][parameters] paraméterfájlban. Példa:

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

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Diagnosztikai gyűjtemény konfigurálása a fürtön
Service Fabric-fürt futtatásakor célszerű a naplókat egy központi helyen lévő összes csomópontról gyűjteni. A naplók központi helyen történő elhelyezése segít a fürtben, illetve az adott fürtben futó alkalmazásokban és szolgáltatásokban felmerülő problémák elemzésében és elhárításában.

A naplók feltöltésének és gyűjtésének egyik módja az Azure Diagnostics (WAD) bővítmény használata, amely feltölti a naplókat az Azure Storage-ba, és rendelkezik a naplók küldésére az Azure Application Insights vagy az Event Hubs számára. Egy külső folyamat is használhatja az eseményeket a tárolóból, és helyezze el őket egy elemzési platform termék, például az Azure Monitor naplók vagy más log-elemzési megoldás.

Ha ezt az oktatóanyagot követi, a diagnosztikai gyűjtemény már be van állítva a [sablonban.][template]

Ha olyan meglévő fürttel rendelkezik, amely nem rendelkezik telepítve a Diagnosztika szolgáltatással, hozzáadhatja vagy frissítheti azt a fürtsablonon keresztül. Módosítsa a meglévő fürt létrehozásához vagy a sablon nak a portálról való letöltéséhez használt Erőforrás-kezelő sablont. Módosítsa a template.json fájlt a következő feladatok végrehajtásával:

Új tárolási erőforrás hozzáadása a sablon erőforrásszakaszához:
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

Ezután adja hozzá a tárfiók nevének és típusának paramétereit a sablon paraméterszakaszához. Cserélje le a helyőrző szöveges tárfiók nevét itt a kívánt tárfiók nevére.

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

Ezután adja hozzá az **IaaSDiagnostics** bővítményt a fürt egyes **Microsoft.Compute/virtualMachineScaleSets** **erőforrásai VirtualMachineProfile** tulajdonságának bővítménytömbjéhez.  Ha a [mintasablont][template]használja, három virtuálisgép-méretezési csoport van (a fürt minden csomóponttípusához tartozik egy).

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
Az EventStore szolgáltatás egy figyelési lehetőség a Service Fabric. Az EventStore lehetővé teszi a fürt vagy a munkaterhelések állapotának megértését egy adott időpontban. Az EventStore egy állapotalapú Service Fabric szolgáltatás, amely a fürtből származó eseményeket tart karban. Az esemény a Service Fabric Explorer, rest és API-k on keresztül érhető el. Az EventStore közvetlenül lekérdezi a fürtöt, hogy diagnosztikai adatokat kapjon a fürt bármely entitásáról, és a következőkre kell használni:

* A fejlesztés vagy tesztelés során felmerülő problémák diagnosztizálása, illetve a figyelési folyamat használata
* Annak ellenőrzése, hogy a fürtön végrehajtott felügyeleti műveletek feldolgozása megfelelő en van-e
* "Pillanatkép" beszerezni egy adott entitással való interakciót a Service Fabric



Az EventStore szolgáltatás fürtön való engedélyezéséhez adja hozzá a következőket a **Microsoft.ServiceFabric/clusters** erőforrás **fabricSettings** tulajdonságához:

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

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Az Azure Monitor-naplók beállítása a fürthöz

Az Azure Monitor naplók a mi javaslatunk a fürtszintű események figyelésére. Az Azure Monitor naplók beállítása a fürt figyelésére, engedélyeznie kell [a diagnosztika fürtszintű események megtekintéséhez.](#configure-diagnostics-collection-on-the-cluster)  

A munkaterületet csatlakoztatni kell a fürtből érkező diagnosztikai adatokhoz.  Ez a naplóadatok az *alkalmazásbandiagnosztikaistorage-névnév* tárfiók, a WADServiceFabric*EventTable, WADWindowsEventLogsTable és WADETWEventTable táblák.

Adja hozzá az Azure Log Analytics munkaterületet, és adja hozzá a megoldást a munkaterülethez:

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

Ezután adja hozzá a paramétereket
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

Ezután adja hozzá a változókat:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Adja hozzá a Log Analytics-ügynök bővítményt a fürt minden egyes virtuálisgép-méretezési csoportjához, és csatlakoztassa az ügynököt a Log Analytics-munkaterülethez. Ez lehetővé teszi a tárolók, alkalmazások és teljesítményfigyelés diagnosztikai adatainak gyűjtését. Azáltal, hogy a virtuálisgép-méretezési készlet erőforrás kiterjesztéseként adja hozzá, az Azure Resource Manager biztosítja, hogy minden csomópontra telepítve legyen, még a fürt méretezésekor is.

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

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. Az [azuredeploy.json][template] Resource Manager sablon létrehoz egy virtuális hálózatot, alhálózatot és hálózati biztonsági csoportot a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal. Éles fürtök esetén a hitelesítésszolgáltató tanúsítványát használja fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

A cikkben szereplő sablon egy olyan fürtöt telepít, amely a tanúsítvány ujjlenyomatát használja a fürttanúsítvány azonosítására. Két tanúsítvány nem rendelkezhet ugyanazzal az ujjlenyomattal, ami megnehezíti a tanúsítványkezelést. Az üzembe helyezett fürt tanúsítványujjlenyomatról tanúsítványnévre való váltása leegyszerűsíti a tanúsítványkezelést. Ha meg szeretné tudni, hogyan frissítheti a fürtöt úgy, hogy tanúsítványneveket használjon a tanúsítványkezeléshez, olvassa el [a Fürt módosítása tanúsítványra közös névkezelésre](service-fabric-cluster-change-cert-thumbprint-to-cn.md)című útmutatót.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

A következő parancsfájl a [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) parancsmag és egy sablon t használja egy új fürt az Azure-ban. A parancsmag létrehoz egy új kulcstartót az Azure-ban, és feltölti a tanúsítványt.

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
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Fürt létrehozása új, önaláírt tanúsítvánnyal

A következő parancsfájl a [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) parancsmag és egy sablon t használja egy új fürt az Azure-ban. A parancsmag létrehoz egy új kulcstartót az Azure-ban, új, önaláírt tanúsítványt ad hozzá a key vaulthoz, és helyileg letölti a tanúsítványfájlt.

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
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz

Csatlakozzon a fürthöz a Service Fabric PowerShell-modullal telepítve a Service Fabric SDK használatával.  Először telepítse a tanúsítványt a számítógépen az aktuális felhasználó Személyes (Saját) tárolójába. Futtassa az alábbi PowerShell-parancsot:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Most már készen áll a biztonságos fürthöz való csatlakozásra.

A **Service Fabric** PowerShell-modul számos parancsmagot biztosít a Service Fabric-fürtök, -alkalmazások és -szolgáltatások kezelésére. A biztonságos fürthöz való kapcsolódáshoz használja a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmagot. A tanúsítvány SHA1 ujjlenyomatával és kapcsolati végpontjával kapcsolatos részletek az előző lépés kimenetében találhatók.

Ha korábban beállította az Azure AD-ügyfélhitelesítést, futtassa a következő parancsot: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Ha nem állította be az Azure AD-ügyfélhitelesítést, futtassa a következő parancsot:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Ellenőrizze, hogy csatlakozik-e, és hogy a fürt kifogástalan [állapotban van-e a Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) parancsmag használatával.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag-sorozat további cikkei a létrehozott fürtöt használják. Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](service-fabric-cluster-delete.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

A következő oktatóanyagra való ugrás, amely ből megtudhatja, hogyan méretezheti a fürtöt.

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * Az Azure Active Directory-hitelesítés beállítása
> * Diagnosztikai gyűjtemény konfigurálása
> * Az EventStore szolgáltatás beállítása
> * Az Azure Monitor-naplók beállítása
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ezután a következő oktatóanyagra lépve megtudhatja, hogyan figyelheti a fürtöt.
> [!div class="nextstepaction"]
> [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
