---
title: "Azure Service Fabric-fürt beállítása | Microsoft Docs"
description: "Rövid útmutató – Windows vagy Linux Service Fabric-fürt létrehozása az Azure-ban."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: de7fa7e6445e6eaf08bdcc8ae812611f20a98c34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Az első saját Service Fabric-fürt létrehozása az Azure-on
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A rövid útmutató segítségével csupán pár perc alatt létrehozhat egy öt csomópontot számláló, Windows- vagy Linux-alapú fürtöt az [Azure PowerShellen](https://msdn.microsoft.com/library/dn135248) vagy az [Azure Portalon](http://portal.azure.com) keresztül.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.

### <a name="create-the-cluster"></a>A fürt létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
2. Válassza a **Számítás** elemet az **Új** panelen, majd a **Service Fabric-fürt** elemet a **Számítás** panelen.
3. Töltse ki a Service Fabric **Alapvető beállítások** űrlapját. Az **Operációs rendszer** beállításban adja meg a Windows vagy Linux azon verzióját, amelyiken a fürt csomópontjait futtatni szeretné. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Hozzon létre egy új **Erőforráscsoportot**. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer létrehozza és együttesen kezeli az Azure-erőforrásokat. Amikor végzett, kattintson az **OK** gombra.

    ![A fürtbeállítás kimenete][cluster-setup-basics]

4. Töltse ki a **Fürtkonfiguráció** űrlapot.  A **Csomóponttípusok száma** elemnél adja meg az „1” értéket.

5. Válassza az **1. csomóponttípus (elsődleges)** lehetőséget, és töltse ki a **Csomóponttípus konfigurációja** űrlapot.  Írjon be egy csomóponttípust, és adja meg a [Tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) elemnél a „Bronz” értéket.  Válassza ki a virtuális gép méretét.

    A csomóponttípusok határozzák meg a virtuális gépek méretét, számát, egyedi végpontjait, valamint az adott típusú virtuális gépek egyéb beállításait. Mindegyik megadott csomóponttípus külön virtuálisgép-méretezési csoportként lesz beállítva, amely a virtuális gépek csoportként való üzembe helyezésére és felügyeletére használható. Mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy föl, eltérő nyitott portokkal rendelkezik, és eltérő kapacitásmetrikái lehetnek.  Az első – vagy elsődleges – csomóponttípus az, ahol a Service Fabric rendszerszolgáltatásai futnak, és ennek öt vagy annál több virtuális gépet kell tartalmaznia.

    A [kapacitástervezés](service-fabric-cluster-capacity.md) az éles rendszerek üzembe helyezésének lényeges lépése.  A jelen rövid útmutatóban azonban nem fog alkalmazásokat futtatni, így válassza a *DS1_v2 Standard* VM-méretet.  [Megbízhatósági szintként](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) adja meg az „Ezüst” értéket, a virtuálisgép-méretezési csoport kezdő kapacitását pedig állítsa 5-re.  

    Az egyedi végpontok portokat nyitnak meg az Azure Load Balancerben, hogy csatlakozni tudjon a fürtben futó alkalmazásokhoz.  Adja meg a „80, 8172” értéket a 80-as és a 8172-es portok megnyitásához.

    Ne jelölje be a **Speciális beállítások konfigurálása** jelölőnégyzetet, amely a TCP/HTTP felügyeleti végpontok, alkalmazásport-tartományok, [elhelyezési korlátozások](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) és [kapacitástulajdonságok](service-fabric-cluster-resource-manager-metrics.md) testreszabására szolgál.    

    Kattintson az **OK** gombra.

6. A **Fürtkonfiguráció** űrlapon állítsa a **Diagnosztika** beállítást **Be** értékre.  A jelen rövid útmutató során nem kell megadnia [hálóbeállítási](service-fabric-cluster-fabric-settings.md) tulajdonságokat.  A **Fabric verziója** elemnél válassza az **Automatikus** frissítési módot, így a Microsoft automatikusan frissíti a fürtön futó Fabric-kód verzióját.  Állítsa a módot **Manuálisra**, ha ki szeretne [választani egy támogatott verziót](service-fabric-cluster-upgrade.md), amelyre frissíteni szeretne. 

    ![Csomóponttípus konfigurálása][node-type-config]

    Kattintson az **OK** gombra.

7. Töltse ki a **Biztonság** űrlapot.  Ehhez a rövid útmutatóhoz válassza a **Nem biztonságos** beállítást.  Az éles számítási feladatokhoz azonban határozottan javasolt biztonságos fürtöket létrehozni, mivel a nem biztonságos fürtökhöz bárki név nélkül csatlakozhat, és ott felügyeleti tevékenységeket hajthat végre.  

    A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. A tanúsítványok Service Fabricban való használatával kapcsolatos további információkért lásd a [Service Fabric-fürtök biztonsági forgatókönyveit](service-fabric-cluster-security.md).  Az Azure Active Directoryval végzett felhasználóhitelesítés engedélyezéséhez, illetve a tanúsítványok alkalmazásbiztonsági célú beállításához [a fürtöt Resource Manager-sablonból hozza létre](service-fabric-cluster-creation-via-arm.md).

    Kattintson az **OK** gombra.

8. Tekintse át az összegzést.  Ha le szeretné tölteni a megadott beállítások alapján összeállított Resource Manager-sablont, válassza a **Sablon és paraméterek letöltése** lehetőséget.  A fürt létrehozásához kattintson a **Létrehozás** gombra.

    A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a „Harang” ikonra az állapotsor mellett, a képernyő jobb felső részén.) Ha a fürt létrehozásakor **A kezdőpulton rögzít** lehetőségre kattintott, a **Service Fabric-fürt üzembe helyezése** a **Kezdőpultra** rögzítve látható.

### <a name="connect-to-the-cluster-using-powershell"></a>Csatlakozás a fürthöz PowerShell használatával
Ha ellenőrizni szeretné, hogy a fürt fut-e, csatlakozzon hozzá a PowerShell-lel.  A ServiceFabric PowerShell-modul a [Service Fabric SDK](service-fabric-get-started.md)-val települ.  A [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag kiépít egy kapcsolatot a fürttel.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
A fürtökhöz való csatlakozást bemutató egyéb példákért lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md). Miután csatlakozott a fürthöz, a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmaggal listázza a fürtben lévő csomópontokat és az egyes csomópontok állapotinformációit. A **HealthState** tulajdonságnak *OK* értékűnek kell lennie minden csomópont esetében.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>A fürt eltávolítása
A Service Fabric-fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A Service Fabric-fürtök teljes törléséhez ezért az összes őket alkotó erőforrást is törölni kell. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése. A fürt törlésének egyéb módjaival, illetve egy erőforráscsoportba tartozó némely (de nem az összes) erőforrás törlésével kapcsolatban lásd a [fürt törlésével](service-fabric-cluster-delete.md) foglalkozó témakört.

Erőforráscsoport törlése az Azure Portalon:
1. Lépjen a törölni kívánt Service Fabric-fürtre.
2. Kattintson az **Erőforráscsoport** nevére a fürt alapvető erőforrásainak lapján.
3. Az **Erőforráscsoport alapvető erőforrásai** oldalon kattintson az **Erőforráscsoport törlése** gombra, és kövesse a lapon megjelenő utasításokat az erőforráscsoport törléséhez.
    ![Az erőforráscsoport törlése][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-windows-cluster"></a>Biztonságos Windows-fürt üzembe helyezése az Azure PowerShell-lel
1. Töltse le a számítógépre az [Azure PowerShell-modul 4.0-s vagy újabb](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) verzióját.

2. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot. 
    
    ```powershell

    Get-Command -Module AzureRM.ServiceFabric 
    ```

    Az alábbihoz hasonló kimenetnek kell megjelennie.

    ![ps-list][ps-list]

3. Jelentkezzen be az Azure-ba, és válassza ki azt az előfizetést, amelyikben létre szeretné hozni a fürtöt

    ```powershell

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId "Subcription ID" 
    ```

4. Futtassa az alábbi parancsot egy biztonságos fürt létrehozásához. Ne felejtse el testre szabni a paramétereket. 

    ```powershell
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
    $RDPuser="vmadmin"
    $RGname="mycluster" # this is also the name of your cluster
    $clusterloc="SouthCentralUS"
    $subname="$RGname.$clusterloc.cloudapp.azure.com"
    $certfolder="c:\mycertificates\"
    $clustersize=1 # can take values 1, 3-99

    New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder
    ```

    A parancs végrehajtása 10 perctől akár 30 percig is eltarthat, és a végén az alábbihoz hasonló kimenetnek kell megjelennie. A kimenet információkat tartalmaz a tanúsítványról, arról a KeyVault tárolóról, ahová feltöltötték, és a helyi mappáról, ahová másolták. 

    ![ps-out][ps-out]

5. Másolja ki a teljes kimenetet, és mentse egy szövegfájlba, mivel később hivatkoznia kell rá. Jegyezze fel az alábbi, kimenetből származó adatokat. 

    - **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
    - **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
    - **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
    - **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>A tanúsítvány telepítése helyi számítógépre
  
A fürthöz való csatlakozáshoz telepíteni kell a tanúsítványt az aktuális felhasználó Személyes (Saját) tárolójába. 

Futtassa az alábbi PowerShell-parancsot

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Most már készen áll a biztonságos fürthöz való csatlakozásra.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz 

A biztonságos fürthöz való csatlakozáshoz futtassa az alábbi PowerShell-parancsot. A tanúsítvány részleteinek meg kell egyezniük a fürt beállításához használt egyik tanúsítvány részleteivel. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


Az alábbi példa az eredményül kapott paramétereket mutatja be: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Az alábbi parancs futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>A fürt eltávolítása
A fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése. 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```
## <a name="use-azure-cli-to-deploy-a-secure-linux-cluster"></a>Biztonságos Linux-fürt üzembe helyezése az Azure CLI-vel

1. Telepítse az [Azure CLI 2.0-t](/cli/azure/install-azure-cli?view=azure-cli-latest) a számítógépre.
2. Jelentkezzen be az Azure-ba, és válassza ki azt az előfizetést, amelyikben létre szeretné hozni a fürtöt.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Futtassa az [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) parancsot egy biztonságos fürt létrehozásához.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Futtassa a következő parancssori felületi parancsot a fürthöz való csatlakozáshoz a tanúsítvány használatával.  Ha a hitelesítés ügyféltanúsítvány használatával történik, a tanúsítvány adatainak meg kell egyeznie egy, a fürtcsomópontokon telepített tanúsítvány adataival.  Önaláírt tanúsítvány esetén használja a `--no-verify` paramétert.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Az alábbi parancs futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Csatlakozás a közvetlenül a csomópontokhoz 

Egy Linux-fürt csomópontjaihoz való csatlakozáshoz használja az SSH-t, és adjon meg egy 3389-nél nagyobb portszámot. Például a korábban létrehozott ötcsomópontos fürt esetében a parancsok a következők lennének:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

## <a name="next-steps"></a>Következő lépések
Most, hogy üzembe helyezett egy fejlesztési fürtöt, megpróbálkozhat a következőkkel:
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)
* [Fürt eltávolítása](service-fabric-cluster-delete.md) 
* [Appokat helyezhet üzembe a PowerShell használatával](service-fabric-deploy-remove-applications.md)
* [Alkalmazások üzembe helyezése a CLI-vel](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG
