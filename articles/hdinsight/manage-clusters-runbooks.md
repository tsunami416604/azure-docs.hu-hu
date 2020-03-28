---
title: 'Oktatóanyag: Fürtök létrehozásához használja az Azure Automation-runbookokat – Azure HDInsight'
description: Ismerje meg, hogyan hozhat létre és törölhet Azure HDInsight-fürtöket a felhőben futó parancsfájlokkal az Azure Automation runbookjaival.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553512"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Oktatóanyag: Azure HDInsight-fürtök létrehozása az Azure Automation segítségével

Az Azure Automation lehetővé teszi, hogy olyan parancsfájlokat hozzon létre, amelyek a felhőben futnak, és igény szerint vagy ütemezés alapján kezelik az Azure-erőforrásokat. Ez a cikk ismerteti, hogyan hozhat létre PowerShell runbookok létrehozása és törlése Azure HDInsight-fürtök.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A HDInsight-mal való együttműködéshez szükséges modulok telepítése.
> * A fürt létrehozása során szükséges hitelesítő adatok létrehozása és tárolása.
> * Hozzon létre egy új Azure Automation-runbookot egy HDInsight-fürt létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Egy meglévő [Azure Automation-fiók.](../automation/automation-quickstart-create-account.md)
* Egy meglévő [Azure Storage-fiók](../storage/common/storage-account-create.md), amely fürttárolóként lesz használva.

## <a name="install-hdinsight-modules"></a>HDInsight-modulok telepítése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki az Azure Automation-fiókok.
1. Válassza a **Modulok gyűjteményt a** **Megosztott erőforrások csoportban.**
1. Írja be az **AzureRM.Profile kifejezést** a mezőbe, és nyomja meg a beírást a kereséshez. Válassza ki a rendelkezésre álló keresési eredményt.
1. Az **AzureRM.profile** képernyőn válassza az **Importálás**lehetőséget. Jelölje be a jelölőnégyzetet az Azure-modulok frissítéséhez, majd kattintson **az OK gombra.**

    ![AzureRM.profile modul importálása](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Térjen vissza a modulok gyűjteményéhez a **Modulok gyűjtemény** megosztott **erőforrások csoportban**lehetőség kiválasztásával.
1. Írja be a **HDInsight típust.** Válassza **az AzureRM.HDInsight lehetőséget.**

    ![HDInsight-modulok tallózása](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. Az **AzureRM.HDInsight** panelen válassza az **Importálás** és **az OK lehetőséget.**

    ![AzureRM.HDInsight modul importálása](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

1. A **Megosztott erőforrások csoportban**válassza a **Hitelesítő adatok**lehetőséget.
1. Válassza **a Hitelesítő adatok hozzáadása**lehetőséget.
1. Adja meg a szükséges adatokat az **Új hitelesítő adatok** panelen. Ez a hitelesítő adat a fürt jelszavának tárolása, amely lehetővé teszi az Ambari-ba való bejelentkezést.

    | Tulajdonság | Érték |
    | --- | --- |
    | Név | `cluster-password` |
    | Felhasználónév | `admin` |
    | Jelszó | `SECURE_PASSWORD` |
    | Jelszó megerősítése | `SECURE_PASSWORD` |

1. Kattintson a **Létrehozás** gombra.
1. Ismételje meg ugyanezt a `ssh-password` folyamatot `sshuser` egy új hitelesítő adat hoz felhasználónévvel és egy ön által választott jelszóval. Kattintson a **Létrehozás** gombra. Ez a hitelesítő adatok a fürt SSH-jelszavának tárolására szolgálnak.

    ![hitelesítő adat létrehozása](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Runbook létrehozása fürt létrehozásához

1. Válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás csoportban.**
1. Válassza **a Runbook létrehozása**lehetőséget.
1. A **Runbook létrehozása** panelen adja meg a runbook `hdinsight-cluster-create`nevét, például . Válassza ki a **Powershell** a **Runbook típusú** legördülő legördülő.
1. Kattintson a **Létrehozás** gombra.

    ![runbook létrehozása](./media/manage-clusters-runbooks/create-runbook.png)

1. Írja be a következő kódot a **PowerShell Runbook szerkesztése** képernyőn, és válassza **a Közzététel**lehetőséget:

    ![runbook közzététele](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Runbook létrehozása fürt törléséhez

1. Válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás csoportban.**
1. Válassza **a Runbook létrehozása**lehetőséget.
1. A **Runbook létrehozása** panelen adja meg a runbook `hdinsight-cluster-delete`nevét, például . Válassza ki a **Powershell** a **Runbook típusú** legördülő legördülő.
1. Kattintson a **Létrehozás** gombra.
1. Írja be a következő kódot a **PowerShell Runbook szerkesztése** képernyőn, és válassza **a Közzététel**lehetőséget:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Runbookok végrehajtása

### <a name="create-a-cluster"></a>Fürt létrehozása

1. Tekintse meg az Automation-fiók Runbookok listáját a Folyamatautomatizálás csoport **Runbookok (Runbooks) (Runbooks) (Runbooks) (Runbooks) (Runbooks) (Runbooks) (Runbooks) (Futtatási könyvek)** listájának megtekintésével. **Process Automation**
1. Válassza `hdinsight-cluster-create`a lehetőséget, vagy a fürtlétrehozási runbook létrehozásakor használt nevet.
1. Válassza **az Indítás** lehetőséget a runbook azonnali végrehajtásához. A runbookok rendszeres időközönként futtathatók is. Tekintse meg [a runbook ütemezése az Azure Automationben című témakört](../automation/shared-resources/schedules.md)
1. Adja meg a parancsfájl szükséges paramétereit, és válassza az **OK gombot.** Ez létrehoz egy új HDInsight-fürtöt a **CLUSTERNAME** paraméterben megadott névvel.

    ![fürt runbook létrehozása parancsvégrehajtása](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Fürt törlése

Törölje a fürtöt `hdinsight-cluster-delete` a létrehozott runbook kiválasztásával. Válassza a **Start**lehetőséget, írja be a **CLUSTERNAME paramétert,** majd kattintson az **OK**gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje az Azure Automation-fiók, amely azért jött létre, hogy elkerüljék a nem kívánt díjakat. Ehhez keresse meg az Azure Portalon, válassza ki azt az erőforráscsoportot, amelyben létrehozta az Azure Automation-fiókot, válassza az Automation-fiókot, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Apache Hadoop-fürtök kezelése a HDInsightban az Azure PowerShell használatával](hdinsight-administer-use-powershell.md)