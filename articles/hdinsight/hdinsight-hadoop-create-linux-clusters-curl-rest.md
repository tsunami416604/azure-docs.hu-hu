---
title: "Azure REST API - Azure Hadoop-fürtök létrehozása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre HDInsight-fürtök küldése az Azure Resource Manager-sablonok az Azure REST API-t."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/10/2017
ms.author: larryfr
ms.openlocfilehash: 9d4d8e16b69ca5b4cd5c042aec54b07ae01c5706
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="create-hadoop-clusters-using-the-azure-rest-api"></a>Az Azure REST API használatával Hadoop-fürtök létrehozása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan hozhat létre HDInsight-fürtöt Azure Resource Manager-sablonok és az Azure REST API használatával.

Az Azure REST API lehetővé teszi az Azure platformon, beleértve az új erőforrások, például a HDInsight-fürtök létrehozása a alkalmazáskönyvtár felügyeleti műveleteket.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!NOTE]
> A lépéseket, ez a dokumentum használatát a [curl (https://curl.haxx.se/)](https://curl.haxx.se/) segédprogram kommunikálni az Azure REST API-t.

## <a name="create-a-template"></a>Sablon létrehozása

Az Azure Resource Manager-sablonok leíró JSON-dokumentumok egy **erőforráscsoport** és minden-erőforrásokat (például a HDInsight.) Ez a sablon-alapú módszer lehetővé teszi a hdinsight egy sablont a szükséges erőforrások meghatározása.

A következő JSON-dokumentumhoz egy egyesülés következtében a sablon és a paraméterek fájlok [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), amely egy Linux-alapú fürtöt hoz létre az SSH-felhasználói fiók biztonságos jelszó használatával.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
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
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.5",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
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
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Ebben a példában a jelen dokumentumban leírt lépések használatban van. Példa lecserélésére az *értékek* a a **paraméterek** szakasz a fürt értékekkel.

> [!IMPORTANT]
> A sablon a feldolgozó csomópontok (4) alapértelmezett száma a HDInsight-fürtök használja. Ha több mint 32 munkavégző csomópontot, majd ki kell választania egy átjárócsomóponttal mérete legalább 8 maggal és 14 GB RAM-mal.
>
> A csomópont-méretek és a társuló költségeket további információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

A lépések részletes ismertetését lásd: [Ismerkedés az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) és az előfizetés használatával kapcsolódjon a `az login` parancsot.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]
> Ezeket a lépéseket egy rövidített verzióját a *egyszerű szolgáltatásnév létrehozása jelszóval* szakasza a [használja az Azure parancssori felület erőforrásokhoz való hozzáféréshez egyszerű szolgáltatás létrehozása](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentum. Ezeket a lépéseket, amellyel az Azure REST API hitelesítését szolgáltatásnevet létrehozni.

1. A parancssorból a következő paranccsal listát az Azure-előfizetését.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    A listában válassza ki az előfizetést, amelyet szeretne használni, és jegyezze fel a **ELŐFIZETÉS_AZONOSÍTÓJA** és __Tenant_ID__ oszlopok. Ezeket az értékeket menteni.

2. Az alábbi parancs segítségével hozzon létre egy alkalmazást az Azure Active Directoryban.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Cserélje le az értékeket a `--display-name`, `--homepage`, és `--identifier-uris` saját értékekkel. Adjon meg egy jelszót az új Active Directory-bejegyzés.

   > [!NOTE]
   > A `--home-page` és `--identifier-uris` értékek nem szükséges az interneten található tényleges weblap hivatkozik. Egyedi URI legyen.

   Ez a parancs által visszaadott érték a __Alkalmazásazonosító__ az új alkalmazás. Mentse ezt az értéket.

3. Az alábbi parancs segítségével hozzon létre egy egyszerű szolgáltatás használata a **Alkalmazásazonosító**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Ez a parancs által visszaadott érték a __Objektumazonosító__. Mentse ezt az értéket.

4. Rendelje hozzá a **tulajdonos** a szolgáltatás egyszerű történő szerepkör a **Objektumazonosító** érték. Használja a **előfizetés-azonosító** korábban beszerzett.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Egy hitelesítési token beszerzése

A következő paranccsal beolvasni egy hitelesítési jogkivonatot:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Állítsa be `$TENANTID`, `$APPID`, és `$PASSWORD` szerezte be, vagy a korábban használt értékekre.

Ha a kérelem sikeres, 200 adatsorozat választ kapnak, és az adott válasz törzsének egy JSON-dokumentum tartalmaz.

A JSON-dokumentum, a kérelem által visszaadott nevű elemet tartalmaz **access_token**. Értékének **access_token** hitelesítési kérelmek REST API használatával.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A következő segítségével hozzon létre egy erőforráscsoportot.

* Állítsa be `$SUBSCRIPTIONID` előfizetési azonosító kapott az egyszerű szolgáltatás létrehozása során.
* Állítsa be `$ACCESSTOKEN` számára a az előző lépésben kapott hozzáférési jogkivonat.
* Cserélje le `DATACENTERLOCATION` létrehozza az erőforráscsoport és erőforrások, az adatok központban. Például "déli középső Régiójában".
* Állítsa be `$RESOURCEGROUPNAME` szeretné használni a csoport neve:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Ha a kérelem sikeres, 200 adatsorozat választ kapnak, és az adott válasz törzsének csoporttal kapcsolatos adatokat tartalmazó JSON-dokumentum tartalmaz. A `"provisioningState"` elem értéke `"Succeeded"`.

## <a name="create-a-deployment"></a>Hozzon létre telepítést

Az erőforráscsoport a sablon telepítéséhez használja a következő parancsot.

* Állítsa be `$DEPLOYMENTNAME` ehhez a központi telepítéshez használni kívánt névre.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Ha mentette a sablont egy fájlba, a következő parancs helyett használhatja `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Ha a kérelem sikeres, 200 adatsorozat választ kapnak, és az adott válasz törzsének telepítési működésével kapcsolatos adatokat tartalmazó JSON-dokumentum tartalmaz.

> [!IMPORTANT]
> A központi telepítés elküldte, de még nem fejeződött be. Általában körülbelül 15, a központi telepítés befejezése több percet is igénybe vehet.

## <a name="check-the-status-of-a-deployment"></a>A központi telepítés állapotának ellenőrzése

A telepítés állapotának ellenőrzéséhez használja a következő parancsot:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Ez a parancs visszaadja a központi telepítés működésével kapcsolatos adatokat tartalmazó JSON-dokumentumból. A `"provisioningState"` elem tartalmazza-e a központi telepítés állapotát. Ha ez az elem értéke `"Succeeded"`, majd a telepítés sikeresen befejeződött.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtre, használja a következő megtudhatja, hogyan működnek a fürthöz.

### <a name="hadoop-clusters"></a>Hadoop-fürtök

* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-fürtökkel

* [Az a HDInsight HBase első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [A HDInsight HBase Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-fürtök

* [Java-topológiák fejlesztése hdinsight alatt futó Storm](storm/apache-storm-develop-java-topology.md)
* [A HDInsight alatt futó Storm Python-összetevőket használnak](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight alatt futó Storm topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)
