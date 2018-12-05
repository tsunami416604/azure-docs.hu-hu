---
title: Azure REST API – Azure használatával az Apache Hadoop-fürtök létrehozása
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtök Azure Resource Manager-sablonok az Azure REST API elküldésével.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: hrasheed
ms.openlocfilehash: b6ec48085d5dd2ea31543e208e8d32b954cb0bca
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872846"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Az Azure REST API használatával az Apache Hadoop-fürtök létrehozása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ismerje meg, hogyan hozhat létre egy HDInsight-fürtöt az Azure Resource Manager-sablon és az Azure REST API használatával.

Az Azure REST API felügyeleti műveletek végrehajtása a szolgáltatások az Azure platformon, beleértve az új erőforrások, például a HDInsight-fürtök létrehozását teszi lehetővé.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!NOTE]
> A lépéseket, a jelen dokumentum-használat a [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) segédprogram az Azure REST API folytatott kommunikációhoz.

## <a name="create-a-template"></a>Sablon létrehozása

Az Azure Resource Manager-sablonok JSON-dokumentumok, amelyek bemutatják, olyan **erőforráscsoport** és az összes-erőforrásokat (például a HDInsight.) Ez a sablon-alapú módszer lehetővé teszi a HDInsight egy sablont a szükséges erőforrások meghatározása.

A következő JSON-dokumentum a sablon és paraméterek fájlok egyesülés [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), ami létrehoz egy Linux-alapú fürtöt az SSH-felhasználói fiókhoz jelszót használ.

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

Ebben a példában a jelen dokumentumban leírt lépések használatban van. Cserélje le a példában *értékek* a a **paraméterek** szakasz azokra az értékekre a fürt számára.

> [!IMPORTANT]
> A sablon a feldolgozó csomópontok (4) alapértelmezett száma egy HDInsight-fürtöt használ. Ha azt tervezi, hogy több mint 32 feldolgozó csomópontokat, majd jelöljön ki egy fő csomópont méretének legalább 8 maggal és 14 GB ram.
>
> További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

Kövesse a leírt lépéseket [Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) , és az előfizetés használatával kapcsolódhat a `az login` parancsot.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]
> Ezen lépések végrehajtása egy rövidített verzióját a *egyszerű szolgáltatás létrehozása a jelszó* szakaszában a [erőforrások eléréséhez egy szolgáltatásnév létrehozásához használható Azure CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentumot. Ezeket a lépéseket az Azure REST API-t hitelesítésre használt egyszerű szolgáltatás létrehozása.

1. Egy parancssorból a következő paranccsal listázhatja az Azure-előfizetést.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    A listában válassza ki az előfizetést, amelyet szeretne használni, és jegyezze fel a **Subscription_ID** és __Tenant_ID__ oszlopokat. Mentse ezeket az értékeket.

2. A következő paranccsal hozzon létre egy alkalmazást az Azure Active Directoryban.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    A tartozó értékeket cserélje le a `--display-name`, `--homepage`, és `--identifier-uris` a saját értékeire. Adjon meg egy jelszót az új Active Directory-bejegyzést.

   > [!NOTE]
   > A `--home-page` és `--identifier-uris` értékek nem az interneten lévő üzemeltetett tényleges weblap hivatkoznia kell. Egyedi URI-k kell lenniük.

   Ez a parancs által visszaadott értéke a __Alkalmazásazonosító__ az új alkalmazás. Mentse ezt az értéket.

3. A következő paranccsal hozzon létre egy egyszerű szolgáltatás használatával a **Alkalmazásazonosító**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Ez a parancs által visszaadott értéke a __Objektumazonosító__. Mentse ezt az értéket.

4. Rendelje hozzá a **tulajdonosa** szerepkört a szolgáltatás egyszerű történő a **Objektumazonosító** értéket. Használja a **előfizetés-azonosító** korábban szerzett be.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>A hitelesítési jogkivonat beszerzése

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

Állítsa be `$TENANTID`, `$APPID`, és `$PASSWORD` kapott, vagy a korábban használt értékeket.

Ha a kérelem sikeres, 200 sorozat választ kap, és a válasz törzse tartalmazza a JSON-dokumentumok.

A kérelem által visszaadott JSON-dokumentumok nevű elemet tartalmaz **access_token**. Az érték **access_token** hitelesítési kéréseket a REST API segítségével.

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

A következő használatával hozzon létre egy erőforráscsoportot.

* Állítsa be `$SUBSCRIPTIONID` az egyszerű szolgáltatás létrehozása során kapott Azonosítót az előfizetés.
* Állítsa be `$ACCESSTOKEN` , az előző lépésben kapott hozzáférési jogkivonat.
* Cserélje le `DATACENTERLOCATION` kíván létrehozni az erőforráscsoportot és az erőforrások, a data-központban. Például: USA déli középső Régiója".
* Állítsa be `$RESOURCEGROUPNAME` szeretné használni a csoport neve:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Ha a kérelem sikeres, 200 sorozat választ kap, és a válasz törzse tartalmazza a csoport adatait tartalmazó JSON-dokumentumok. A `"provisioningState"` elem tartalmazza a egy értéke `"Succeeded"`.

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

Az alábbi parancs segítségével helyezheti üzembe a sablont az erőforráscsoporthoz.

* Állítsa be `$DEPLOYMENTNAME` ehhez a központi telepítéshez használni kívánt nevet.

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

Ha a kérelem sikeres, 200 sorozat választ kap, és a válasz törzse tartalmazza a központi telepítési művelettel kapcsolatos adatokat tartalmazó JSON-dokumentumok.

> [!IMPORTANT]
> Az üzemelő példány elküldése megtörtént, de nem fejeződött be. Általában körülbelül 15., a központi telepítés befejezése több percet is igénybe vehet.

## <a name="check-the-status-of-a-deployment"></a>A központi telepítési állapotának ellenőrzése

Az üzembe helyezés állapotának ellenőrzéséhez használja a következő parancsot:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Ez a parancs visszaadja a központi telepítési művelettel kapcsolatos adatokat tartalmazó JSON-dokumentumok. A `"provisioningState"` elem tartalmazza a központi telepítés állapotát. Ha ez az elem értéke tartalmaz `"Succeeded"`, majd a telepítés sikeresen befejeződött.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, a következő segítségével megtudhatja, hogyan működik a fürttel.

### <a name="apache-hadoop-clusters"></a>Az Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [Apache Hadoop MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Az Apache HBase-fürtök

* [A HDInsight Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Az Apache HBase on HDInsight Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök

* [Java-topológiák fejlesztése a HDInsight az Apache stormmal](storm/apache-storm-develop-java-topology.md)
* [Az Apache Storm on HDInsight használata a Python-összetevők](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight Apache Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)
