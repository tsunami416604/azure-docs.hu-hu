---
title: "A Service Fabric-fürt létrehozása az Azure-ban |} Microsoft Docs"
description: "Ismerje meg, egy Windows vagy Linux Service Fabric-fürt létrehozása az Azure PowerShell használatával"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 217b9f2f0dfed5b095e1bac1c8146abf4753fadc
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-secure-cluster-in-azure-by-using-powershell"></a>Biztonságos fürt létrehozása az Azure PowerShell használatával
Ez a cikk az első egy sorozat része, amely megmutatja, hogyan helyezze át a felhőbe .NET-alkalmazás Azure Service Fabric-fürtök és a tárolók használatával oktatóanyagok. A következő lépések megtanulhatja a Service Fabric fürt létrehozása (Windows vagy Linux), amely futtatja az Azure-ban. Amikor végzett, hogy a biztonságos fürt, amely a felhőben, amely alkalmazások telepíthetők.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Telepítse a [Service Fabric SDK](service-fabric-get-started.md).
- Telepítés [Azure Powershell 4.1-es vagy újabb verziója](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). (Ha szükséges, [Azure PowerShell telepítése](/powershell/azure/overview) vagy [újabb verzióra való frissítés](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps).)


## <a name="create-a-service-fabric-cluster"></a>A Service Fabric-fürt létrehozása

Ezt a parancsfájlt hoz létre egy egycsomópontos, tekintse meg a Service Fabric fürt. Önaláírt tanúsítvány a fürt biztonságossá tételére. A parancsfájl létrehozza a tanúsítványt és a fürt, és akkor helyezi a tanúsítvány kulcstároló. Egy csomópontos fürtök túl egy virtuális gép nem méretezhető, és preview fürtök újabb verziói nem frissíthetők.

Azure-ban futó Service Fabric-fürt felmerült költségének kiszámítására, használja a [Azure árképzési Számológép](https://azure.microsoft.com/pricing/calculator/).
Service Fabric-fürtök létrehozásával kapcsolatos további információkért lásd: [a Service Fabric-fürt létrehozása az Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Nyissa meg a PowerShell-konzolban, jelentkezzen be az Azure, és válassza ki a kívánt előfizetést, a fürt központi telepítése:

   ```PowerShell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Fürt paraméterek

   A parancsfájl a következő paraméterek és fogalmak. Testre szabhatja a paramétereket, hogy illeszkedjen az igényekhez.

   | Paraméter       | Leírás | Ajánlott érték |
   | --------------- | ----------- | --------------- |
   | Hely | Az Azure-régió, amelybe telepítette a fürt. | Például *westeurope*, *eastasia*, vagy *eastus* |
   | Név     | A fürt neve szeretne létrehozni. A neve 4 – 23 karakterből kell állnia, és csak kisbetűket, számokat és kötőjeleket tartalmazhat rendelkezik. | Például *szamitogepnev-sfpreviewcluster* |
   | erőforráscsoport-név   | A fürt létrehozásához az erőforráscsoport neve. | Például *myresourcegroup* |
   | VmSku  | A virtuális gép használata a csomópontok Termékváltozat. | Bármilyen érvényes virtuális gépet Termékváltozat |
   | Operációs rendszer  | A virtuális gép használata a csomópontok operációs rendszer. | Bármilyen érvényes virtuális gépet az operációs rendszer |
   | keyVaultName | A fürthöz hozzárendelni az új kulcstároló neve. | Például *mykeyvault* |
   | ClusterSize | A fürtben lévő virtuális gépek száma (lehet *1* vagy *3-99*).| Adjon meg egy minta fürtöt csak egy virtuális gépet |
   | CertificateSubjectName | Létrejön a tanúsítvány tulajdonosának neve. | A következő formátumban:  *<name>* . *<location>* . cloudapp.azure.com |

### <a name="default-parameter-values"></a>A paraméterek alapértelmezett értéke
**Virtuális gép**: nem kötelező beállítások. Ha nem adja meg őket, a rendszergazda felhasználóneve alapértelmezett *vmadmin* és PowerShell jelszó megadását kéri a virtuális gépet a fürt létrehozása előtt.

**Portok**: alapértelmezés szerint a 80-as és 8081 portok. Az útmutatást követve adhat meg további portokat [portok a Service Fabric-fürtök](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Diagnosztika**: alapértelmezés szerint engedélyezett.

**DNS-szolgáltatás**: alapértelmezés szerint nincs engedélyezve.

**Fordított proxy**: alapértelmezés szerint nincs engedélyezve.

## <a name="create-the-cluster-with-your-parameters"></a>A fürt létrehozása a paraméterekkel

Miután eldöntötte, hogy illeszkedjen az igényekhez a paraméterek, a következő parancsot egy biztonságos Service Fabric-fürt és a tanúsítványok.

Módosíthatja ezt a parancsfájlt, hogy további paramétereket tartalmazza. A paraméterek a fürt létrehozásához további információkért lásd: a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) parancsmag.

>[!NOTE]
>Ez a parancs futtatása előtt létre kell hoznia egy mappát a fürt tanúsítvány tárolására szolgál.

```PowerShell

# Set the certificate variables. This creates and encrypts a password that Service Fabric will use.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

# You must create the folder where you want to store the certificate on your machine before you start this step.
$certfolder="c:\mycertificates\"

# Set the variables for common values. Change the values to fit your needs.
$clusterloc="WestUS"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. The possible values are 1 and 3-99.
$clustersize=1

# Create the Service Fabric cluster and its self-signed certificate. The OS you specify here lets you use this cluster with any applications that are also using containers.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

A létrehozási folyamat több percet is igénybe vehet. A konfiguráció befejezése után azt a fürt létrehozása az Azure-ban információkat jelenít meg. Ehhez a paraméterhez megadott elérési úton - CertificateOutputFolder könyvtárához a fürt tanúsítvány is másolja.

Vegye figyelembe a **ManagementEndpoint** a fürtöt, amely lehet például a következő URL-cím URL-címe: https://mycluster.westeurope.cloudapp.azure.com:19080.

## <a name="import-the-certificate"></a>A tanúsítvány importálása

Ha a fürt sikeresen létrejött, futtassa a következő parancs futtatásával győződjön meg arról, hogy használhatja-e az önaláírt tanúsítvány:

```PowerShell

# To connect to the cluster, install the certificate into the Personal (My) store of the current user on your computer.
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
-FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
-Password $certpwd
```

Ez a parancs telepíti a tanúsítványt a számítógép az aktuális felhasználó. A Service Fabric Explorer eléréséhez, és a fürt állapotának megtekintéséhez tanúsítványra van szükség.


## <a name="view-your-cluster-optional"></a>Megtekintheti a fürt (nem kötelező)

Miután a fürt mind az importált tanúsítvány, csatlakozzon a fürthöz, és állapotának megtekintése. Több módon csatlakozhatnak, vagy a Service Fabric Explorer vagy a PowerShell használatával.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
A Service Fabric Explorer keresztül fürt állapotát tekintheti meg. Ehhez keresse meg a **ManagementEndpoint** URL-címe a fürt, és válassza ki a tanúsítványt a számítógépen.

>[!NOTE]
>Service Fabric Explorer megnyitásakor egy tanúsítvány hibát látja, önaláírt tanúsítványt használ. Peremhálózati, fel kell kattintson **részletek**, majd kattintson a **nyissa meg a képernyőn látható weblapon** hivatkozásra. A Chrome-ban, meg kell kattintania **speciális**, majd kattintson a **folytatható** hivatkozásra.

### <a name="powershell"></a>PowerShell

A Service Fabric PowerShell-modul sok-parancsmagokat kínál a Service Fabric fürt, alkalmazások és szolgáltatások kezelése. Használja a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag kapcsolódjon a biztonságos fürthöz. A tanúsítvány ujjlenyomata és kapcsolat végpont adatait az előző lépésben kimenete található.

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
-KeepAliveIntervalInSec 10 `
-X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-StoreLocation CurrentUser -StoreName My
```

Is ellenőrizheti, hogy csatlakozott, és hogy a fürt használatával megfelelő állapotban a [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) parancsmag.

```PowerShell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóprogramban megismerte biztonságos Service Fabric-fürt létrehozása az Azure PowerShell használatával.

A következő előzetes telepítése egy meglévő alkalmazást a következő oktatóanyagot:
> [!div class="nextstepaction"]
> [A Docker Compose meglévő .NET alkalmazás központi telepítése](service-fabric-host-app-in-a-container.md)

 
