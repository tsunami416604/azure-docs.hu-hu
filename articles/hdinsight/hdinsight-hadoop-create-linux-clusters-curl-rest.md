---
title: Apache Hadoop-fürtök létrehozása az Azure REST API-val – Azure
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöket az Azure Resource Manager-sablonok Azure REST API-ba való elküldésével.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 2680304bd73bdbae35b29b89f38ae2665615f5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239915"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Apache Hadoop-fürtök létrehozása az Azure REST API használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ismerje meg, hogyan hozhat létre egy HDInsight-fürtet egy Azure Resource Manager-sablon és az Azure REST API használatával.

Az Azure REST API lehetővé teszi, hogy felügyeleti műveleteket hajtson végre az Azure platformon üzemeltetett szolgáltatásokon, beleértve az új erőforrások, például a HDInsight-fürtök létrehozását.

> [!NOTE]  
> A jelen dokumentum ban leírt lépések a [curl (segédprogramhttps://curl.haxx.se/) ](https://curl.haxx.se/) az Azure REST API-val való kommunikáció.

## <a name="create-a-template"></a>Sablon létrehozása

Az Azure Resource Manager-sablonok olyan JSON-dokumentumok, amelyek egy **erőforráscsoportot** és a benne lévő összes erőforrást (például a HDInsight-ot) írják le. Ez a sablonalapú megközelítés lehetővé teszi, hogy egyetlen sablonban határozza meg a HDInsighthoz szükséges erőforrásokat.

A következő JSON-dokumentum a sablon- és [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)paraméterfájlok összevonása , amely egy Linux-alapú fürtöt hoz létre jelszóval az SSH felhasználói fiók védelméhez.

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
                       "clusterVersion": "3.6",
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
                                   "vmSize": "{}" 
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
                                   "vmSize": "{}"
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

Ez a példa a dokumentum lépéseiben használatos. Cserélje le a **paraméterek** szakaszban szereplő *példaértékeket* a fürt értékeire.

> [!IMPORTANT]  
> A sablon a HDInsight-fürt höz a munkavégző csomópontok (4) alapértelmezett számát használja. Ha 32-nél több munkavégző csomópontot tervez, akkor ki kell választania egy legalább 8 maggal és 14 GB-os ram-mal rendelkező főcsomópontméretet.
>
> További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Bejelentkezés Azure-előfizetésbe

Kövesse az Azure [CLI használatának első](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) lépéseit, és `az login` csatlakozzon az előfizetéshez a paranccsal című részben.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]  
> Ezek a lépések egy rövidített változata a *szolgáltatás létrehozása egyszerű jelszóval* szakasza az [Azure CLI használatával egy egyszerű szolgáltatás az erőforrások eléréséhez dokumentum.](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) Ezek a lépések hozzon létre egy egyszerű szolgáltatás, amely az Azure REST API-hitelesítéshez használt.

1. Egy parancssorból használja a következő parancsot az Azure-előfizetések listázásához.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    A listában jelölje ki a használni kívánt előfizetést, és jegyezze fel a **Subscription_ID** és __Tenant_ID__ oszlopokat. Mentse ezeket az értékeket.

2. A következő paranccsal hozzon létre egy alkalmazást az Azure Active Directoryban.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Cserélje le a `--display-name` `--homepage`, `--identifier-uris` és a saját értékeit. Adja meg az új Active Directory-bejegyzés jelszavát.

   > [!NOTE]  
   > A `--home-page` `--identifier-uris` és az értékek nem kell hivatkozni a tényleges weboldal házigazdája az interneten. Egyedi URI-knak kell lenniük.

   A parancsból visszaadott érték az új alkalmazás __alkalmazásazonosítója.__ Mentse ezt az értéket.

3. A következő paranccsal hozzon létre egy egyszerű szolgáltatásaz **alkalmazásazonosító használatával.**

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     A parancsból visszaadott érték az __objektumazonosító__. Mentse ezt az értéket.

4. Rendelje hozzá a **Tulajdonos** szerepkört az egyszerű szolgáltatáshoz az **Objektumazonosító** érték használatával. Használja a korábban beszerzett **előfizetés-azonosítót.**

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Hitelesítési jogkivonat beszereznie

A hitelesítési jogkivonat beolvasásához használja a következő parancsot:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Állítsa `$TENANTID` `$APPID`be `$PASSWORD` a , és a korábban kapott vagy használt értékekre.

Ha ez a kérés sikeres, 200-as sorozatú választ kap, és a választörzs egy JSON-dokumentumot tartalmaz.

A kérelem által visszaadott JSON-dokumentum **egy access_token**nevű elemet tartalmaz. A **access_token** értéke a REST API-ra vonatkozó hitelesítési kérelmekhez használatos.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Erőforráscsoport létrehozásához használja az alábbiakat.

* Állítsa `$SUBSCRIPTIONID` be a szolgáltatásnév létrehozása közben kapott előfizetés-azonosítót.
* Állítsa `$ACCESSTOKEN` be az előző lépésben fogadott hozzáférési jogkivonatot.
* Cserélje `DATACENTERLOCATION` le az erőforráscsoportot létrehozni kívánt adatközponttal és erőforrásokkal. Például "Usa déli középső régiója".
* Állítsa `$RESOURCEGROUPNAME` be a csoporthoz használni kívánt névre:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Ha ez a kérés sikeres, 200-as sorozatú választ kap, és a választörzs egy JSON-dokumentumot tartalmaz, amely a csoport adatait tartalmazza. Az `"provisioningState"` elem értéke `"Succeeded"`.

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A sablon erőforráscsoportra való központi telepítéséhez használja a következő parancsot.

* Állítsa `$DEPLOYMENTNAME` be a központi telepítéshez használni kívánt nevet.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Ha a sablont fájlba mentette, a következő `-d "{ template and parameters}"`parancsot használhatja a következő helyett:
>
> `--data-binary "@/path/to/file.json"`

Ha ez a kérés sikeres, 200-as sorozatú választ kap, és a választörzs egy JSON-dokumentumot tartalmaz, amely a központi telepítési műveletre vonatkozó információkat tartalmaz.

> [!IMPORTANT]  
> A központi telepítés elküldve, de még nem fejeződött be. A telepítés befejezéséhez több percig is eltarthat, általában 15 körül.

## <a name="check-the-status-of-a-deployment"></a>Telepítés állapotának ellenőrzése

A központi telepítés állapotának ellenőrzéséhez használja a következő parancsot:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Ez a parancs egy JSON-dokumentumot ad vissza, amely a telepítési művelettel kapcsolatos információkat tartalmaz. Az `"provisioningState"` elem a központi telepítés állapotát tartalmazza. Ha ez az elem `"Succeeded"`a értékét tartalmazza, akkor a telepítés sikeresen befejeződött.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, az alábbiakban megtudhatja, hogyan dolgozhat a fürttel.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase-lel a HDInsight-on](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java alkalmazások fejlesztése Apache HBase alkalmazáshoz a HDInsight-on](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök

* [Java topológiák fejlesztése apache stormhoz a HDInsighton](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata az Apache Storm ban a HDInsighton](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése az Apache Storm segítségével a HDInsighton](storm/apache-storm-deploy-monitor-topology-linux.md)
