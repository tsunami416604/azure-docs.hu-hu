---
title: "Sablonokkal - Azure HDInsight Hadoop-fürtök létrehozása |} Microsoft Docs"
description: "Ismerje meg a fürt létrehozása HDInsight Resource Manager-sablonok segítségével"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: jgao
ms.openlocfilehash: 82733e2a3025f932961122bad9d70c26896837b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Hadoop-fürtök létrehozása a Hdinsightban Resource Manager-sablonok használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebből a cikkből megismerheti az Azure Resource Manager-sablonok Azure HDInsight-fürtök létrehozásának számos módja. További információkért lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](../azure-resource-manager/resource-group-template-deploy.md). Más fürt létrehozása eszközeivel és szolgáltatásaival kapcsolatos információkért kattintson az ezen a lapon lévő lapválasztót, vagy tekintse meg [Fürtlétrehozási módszerekhez](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ez a cikk útmutatását, lesz szüksége:

* Egy [Azure-előfizetés](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Az Azure PowerShell és/vagy Azure CLI-t.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="resource-manager-templates"></a>Resource Manager-sablonok
A Resource Manager-sablon megkönnyíti, hogy egyetlen, koordinált műveletben a következő alkalmazás létrehozásához:
* A HDInsight-fürtök és a tőle függő erőforrások (például az alapértelmezett tárfiók)
* További erőforrások (például az Azure SQL Database Apache Sqoop használatával)

A sablon határozza meg az erőforrásokat, amelyek szükségesek az alkalmazás. Is meg az üzembe helyezéshez megadott paraméterek a felhasználótól a különböző környezetekhez tartozó értékeket. A sablon JSON és az üzemelő példány értékeit összeállításához használt kifejezések áll.

HDInsight sablon minták található [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/?term=hdinsight). Használja a platformok közötti [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) rendelkező a [erőforrás-kezelő bővítmény](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) vagy egy szövegszerkesztőben, hogy menti a sablont egy fájlba a munkaállomáson. Megismerheti, hogyan hívhatja meg a sablont más módszerekkel.

Erőforrás-kezelő sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Szerző Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md)
* [Alkalmazás üzembe helyezése az Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Sablonok készítése

Az Azure portál használatával állítsa be a fürt összes tulajdonságait, és mentse a sablon üzembe helyezése előtt. A sablon majd felhasználhatja.

**Egy sablon létrehozása az Azure portál használatával**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **új** kattintson a bal oldali menü **Eszközintelligencia + analitika**, és kattintson a **HDInsight**.
3. Kövesse az útmutatást követve adja meg az tulajdonságait. Választhatja a **Gyorslétrehozás** vagy a **egyéni** lehetőséget.
4. Az a **összegzés** lapra, majd **töltse le a sablon és a paraméterek**:

    ![HDInsight Hadoop létrehozása a fürt Resource Manager sablon letöltése](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download.png)

    A sablonfájl paraméterfájl és a sablon telepítéséhez használt mintakódok listájának megtekintéséhez:

    ![HDInsight Hadoop-fürt létrehozása Resource Manager sablon letöltési beállítások](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download-options.png)

    Itt a sablon letöltése, mentse a sablont a könyvtárban, vagy a sablon telepítéséhez.

    Egy sablont a könyvtárban eléréséhez kattintson **további szolgáltatások** a bal oldali menüben, majd kattintson a **sablonok** (alatt a **más** kategória).

    > [!Note]
    > A sablon és a paraméterek fájl együtt kell használni. Ellenkező esetben előfordulhat, hogy eredményt el nem várt. Például az alapértelmezett **clusterKind** tulajdonság értéke mindig **hadoop**, annak ellenére, hogy milyen, adja meg a sablon letöltése előtt.



## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

Ez az eljárás egy Hadoop-fürt hdinsightban hoz létre.

1. A JSON-fájl mentése a [függelék](#appx-a-arm-template) a munkaállomásra. A PowerShell-parancsfájlt, a fájl neve: `C:\HDITutorials-ARM\hdinsight-arm-template.json`.
2. Ha szükséges, állítsa a paramétereket és változókat.
3. A sablon futtassa a következő PowerShell-parancsfájl használatával:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>"
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and variables
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage account
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

    A PowerShell-parancsfájl a csak a fürt nevét konfigurálja. A tárfiók neve nem változtatható a sablonban. A fürt felhasználói jelszó megadására kéri. (Az alapértelmezett felhasználónév az **admin**.) Is kéri az SSH-felhasználói jelszó. (Az alapértelmezett SSH-felhasználónév **sshuser**.)  

További információkért lásd: [telepítés a következő PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template).

## <a name="deploy-with-cli"></a>A parancssori felület telepítése
Az alábbi példában az Azure parancssori felület (CLI). A fürt és a függő tárfiókot és a tároló létrehoz egy Resource Manager-sablon meghívásával:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"

Adja meg kéri:
* A fürt nevét.
* A fürt felhasználói jelszavát. (Az alapértelmezett felhasználónév az **admin**.)
* Az SSH-felhasználói jelszavát. (Az alapértelmezett SSH-felhasználónév **sshuser**.)

Az alábbi kód beágyazott paraméterek szolgál:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

## <a name="deploy-with-the-rest-api"></a>A REST API-t központi telepítése
Lásd: [REST API-val telepítése](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval
 Visual Studio használatával hozzon létre egy erőforráscsoport-projekt, és telepítse az Azure a felhasználói felületen keresztül. Milyen típusú erőforrásokat tartalmazza a projekt választja. Ezeket az erőforrásokat a rendszer automatikusan hozzáadja a Resource Manager-sablon. A projekt a sablon telepítéséhez PowerShell parancsfájlt is tartalmaz.

Visual Studio használatával az erőforráscsoportokhoz bemutatása, lásd: [létrehozása és telepítése a Visual Studio használatával Azure erőforráscsoport-sablonok a](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta rendelkezik többféle módon hozhat létre HDInsight-fürtöt. További tudnivalókért tekintse meg a következő cikkeket:

* Például a .NET ügyféloldali kódtár erőforrásoknak történő telepítésének, [erőforrások telepíteni a .NET-kódtárakra és egy sablon](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Részletes példa az alkalmazások központi telepítése, lásd: [kiépítése és mikroszolgáltatások kiszámítható módon tudja az Azure-ban telepítheti](../app-service/app-service-deploy-complex-application-predictably.md).
* Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](../solution-dev-test-environments.md).
* A szakaszok az Azure Resource Manager sablon kapcsolatos további tudnivalókért lásd: [sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).
* Az Azure Resource Manager-sablonokban használható függvények listáját lásd: [sablonfüggvényei](../azure-resource-manager/resource-group-template-functions.md).

## <a name="appendix-resource-manager-template-to-create-a-hadoop-cluster"></a>A függelék: Resource Manager-sablon egy Hadoop-fürt létrehozása
A következő Azure Resource Manager-sablon egy Linux-alapú Hadoop-fürt a függő Azure storage-fiókot hoz létre.

> [!NOTE]
> Ez a minta Hive metaadattárhoz és az Oozie metaadattárhoz konfigurációs információkat tartalmazza. Távolítsa el a szakasz, vagy konfigurálja a szakasz a sablon használata előtt.
>
>

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).keys[0].value]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

## <a name="appendix-resource-manager-template-to-create-a-spark-cluster"></a>A függelék: Resource Manager-sablon a Spark-fürt létrehozása

Ez a témakör a Resource Manager-sablon egy HDInsight Spark-fürt létrehozására használható. Ez a sablon tartalmazza konfigurációi `spark-defaults` és `spark-thrift-sparkconf` (az 1.6-os Spark-fürtök) és `spark2-defaults` és `spark2-thrift-sparkconf` (a Spark 2-fürtök). Továbbá a HDInsight számítja ki, és beállítja, mint a konfigurációk `spark.executor.instances`, `spark.executor.memory`, és `spark.executor.cores` a fürt mérete alapján. 

Bármely egy paraméter értéke a szakasz a sablonba részeként, HDInsight nélkül kiszámításához, a többi paraméter szakaszában azonos. Például paraméter `spark.executor.instances` szerepel a `spark-defaults` konfigurációs. Egy másik paraméter értéke (például `spark.yarn.exector.memoryOverhead`) található a `spark-defaults` konfigurációs, HDInsight nélkül kiszámításához, a `spark.executor.instances` paramétert is.

    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of the HDInsight cluster to create."
            }
        },
        "clusterLoginUserName": {
            "type": "string",
            "defaultValue": "admin",
            "metadata": {
                "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
            }
        },
        "clusterLoginPassword": {
            "type": "securestring",
            "metadata": {
                "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus",
            "metadata": {
                "description": "The location where all azure resources will be deployed."
            }
        },
        "clusterVersion": {
            "type": "string",
            "defaultValue": "3.5",
            "metadata": {
                "description": "HDInsight cluster version."
            }
        },
        "clusterWorkerNodeCount": {
            "type": "int",
            "defaultValue": 4,
            "metadata": {
                "description": "The number of nodes in the HDInsight cluster."
            }
        },
        "clusterKind": {
            "type": "string",
            "defaultValue": "SPARK",
            "metadata": {
                "description": "The type of the HDInsight cluster to create."
            }
        },
        "sshUserName": {
            "type": "string",
            "defaultValue": "sshuser",
            "metadata": {
                "description": "These credentials can be used to remotely access the cluster."
            }
        },
        "sshPassword": {
            "type": "securestring",
            "metadata": {
                "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
            }
        }
    },
    "variables": {
        "defaultApiVersion": "2017-06-01",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
    {
            "apiVersion": "2015-03-01-preview",
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "dependsOn": [],
            "properties": {
                "clusterVersion": "[parameters('clusterVersion')]",
                "osType": "Linux",
                "tier": "standard",
                "clusterDefinition": {
                    "kind": "[parameters('clusterKind')]",
                    "configurations": {
                        "gateway": {
                            "restAuthCredential.isEnabled": true,
                            "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                            "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                        },
                        "spark-defaults": {
                            "spark.executor.cores": "2"
                        },
                        "spark-thrift-sparkconf": {
                            "spark.yarn.executor.memoryOverhead": "896"
                        }
                    }
                },
                "storageProfile": {
                    "storageaccounts": [
                        {
                            "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                            "isDefault": true,
                            "container": "[parameters('clusterName')]",
                            "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).keys[0].value]"
                        }
                    ]
                },
                "computeProfile": {
                    "roles": [
                        {
                            "name": "headnode",
                            "minInstanceCount": 1,
                            "targetInstanceCount": 2,
                            "hardwareProfile": {
                                "vmSize": "Standard_D12"
                            },
                            "osProfile": {
                                "linuxOperatingSystemProfile": {
                                    "username": "[parameters('sshUserName')]",
                                    "password": "[parameters('sshPassword')]"
                                }
                            },
                            "virtualNetworkProfile": null,
                            "scriptActions": []
                        },
                        {
                            "name": "workernode",
                            "minInstanceCount": 1,
                            "targetInstanceCount": 4,
                            "hardwareProfile": {
                                "vmSize": "Standard_D4"
                            },
                            "osProfile": {
                                "linuxOperatingSystemProfile": {
                                    "username": "[parameters('sshUserName')]",
                                    "password": "[parameters('sshPassword')]"
                                    }
                                },
                                "virtualNetworkProfile": null,
                                "scriptActions": []
                            }
                        ]
                    }
                }
            }
        ]
    }
