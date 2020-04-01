---
title: 'Oktatóanyag: Hozzon létre egy Apache Kafka REST-proxykompatibilis fürtöt a HDInsightban az Azure CLI használatával'
description: Ismerje meg, hogyan hajthat végre Apache Kafka-műveleteket egy Kafka REST-proxy használatával az Azure HDInsight ban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201884"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Oktatóanyag: Hozzon létre egy Apache Kafka REST-proxykompatibilis fürtöt a HDInsightban az Azure CLI használatával

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre apache Kafka [REST-proxykompatibilis](./rest-proxy.md) fürtöt az Azure HDInsightban az Azure parancssori felületén (CLI) keresztül. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Kafka egy nyílt forráskódú, elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál. A Kafka REST-proxy lehetővé teszi, hogy a Kafka-fürtöt [HTTP-n](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) keresztül i. Az Azure CLI a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. A fürt közvetlenül az SSH használatával érhető el. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat. További információ: [Csatlakozás az Apache Kafkához virtuális hálózaton keresztül.](./apache-kafka-connect-vpn-gateway.md)

Ebben az oktatóanyagban a következőket tanulhatja meg:

> [!div class="checklist"]
> * A Kafka REST proxy előfeltételei
> * Apache Kafka-fürt létrehozása az Azure CLI használatával

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure AD-vel regisztrált alkalmazás. A Kafka REST-proxyval való interakcióhoz írt ügyfélalkalmazások az alkalmazás azonosítóját és titkos ját használják az Azure-hitelesítéshez. További információt az [Alkalmazás regisztrálása a Microsoft identity platformmal](../../active-directory/develop/quickstart-register-app.md)című témakörben talál.

* Egy Azure AD biztonsági csoport a regisztrált alkalmazás tagjaként. Ez a biztonsági csoport szabályozza, hogy mely alkalmazások kommunikálhatnak a REST-proxyval. Az Azure AD-csoportok létrehozásáról további információt az [Alapszintű csoport létrehozása és tagok hozzáadása az Azure Active Directory használatával című témakörben talál.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

* Azure CLI. Győződjön meg arról, hogy legalább 2.0.79-es verzióval rendelkezik. Lásd: [Az Azure CLI telepítése.](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

1. Jelentkezzen be Azure-előfizetésbe.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Környezeti változók beállítása. A változók használata ebben az oktatóanyagban a Bash-en alapul. Más környezetekben kisebb eltérésekre lesz szükség.

    |Változó | Leírás |
    |---|---|
    |resourceGroupName|Cserélje le a RESOURCEGROUPNAME-t az új erőforráscsoport nevére.|
    |location|Cserélje le a LOCATION-t egy olyan régióra, ahol a fürt létre jön. Az érvényes helyek listájához `az account list-locations` használja a parancsot|
    |clusterName|Cserélje le a CLUSTERNAME-t az új fürt globálisan egyedi nevére.|
    |storageAccount|Cserélje le a STORAGEACCOUNTNAME-t az új tárfiók nevére.|
    |httpJelszó|Cserélje jelszó jelszóval a fürt bejelentkezési, **admin**.|
    |sshPassword|Cserélje le a PASSWORD-t a biztonságos rendszerhéj felhasználónevének jelszavára, **sshuser**.|
    |securityGroupName|Cserélje le a SECURITYGROUPNAME-t a Kafka rest proxy ügyfél AAD biztonsági csoportnevére. A változó átkerül `--kafka-client-group-name` a `az-hdinsight-create`paraméterbe.|
    |securityGroupID|Cserélje le a SECURITYGROUPID azonosítót a Kafka rest proxy ügyfél AAD biztonsági csoportazonosítójára. A változó átkerül `--kafka-client-group-id` a `az-hdinsight-create`paraméterbe.|
    |storageContainer tároló|Tárolótároló a fürt fogja használni, hagyja, ahogy van az oktatóanyag. Ez a változó a fürt nevével lesz beállítva.|
    |workernodeCount (workernodeCount)|A fürtben lévő munkavégző csomópontok száma, hagyja, ahogy van ebben az oktatóanyagban. A magas rendelkezésre állás biztosításához a Kafka legalább 3 munkavégző csomópontra van szükség|
    |fürttípus|HDInsight-fürt típusa, hagyja, ahogy van ebben az oktatóanyagban.|
    |clusterVersion|HDInsight fürtverziója, hagyjuk, ahogy van ebben az oktatóanyagban. A Kafka Rest Proxy 4.0 minimális fürtverzióját igényli.|
    |componentVersion|Kafka verzió, hagyjuk, mint-van ez a bemutató. A Kafka Rest Proxy használatához a 2.1 minimális összetevőverziója szükséges.|

    Frissítse a változókat a kívánt értékekkel. Ezután adja meg a CLI parancsokat a környezeti változók beállításához.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Hozza létre az erőforráscsoportot](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) az alábbi parancs megadásával:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) az alábbi parancs megadásával:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Bontsa ki az elsődleges kulcsot](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) az Azure Storage-fiókból, és tárolja egy változóban az alábbi parancs megadásával:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Hozzon létre egy Azure Storage-tárolót](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) az alábbi parancs megadásával:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Hozza létre a HDInsight-fürtöt.](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) A parancs beírása előtt vegye figyelembe a következő paramétereket:

    1. A Kafka-fürtök kötelező paraméterei:

        |Paraméter | Leírás|
        |---|---|
        |--típus|Az értéknek **Kafka**értékűnek kell lennie.|
        |--workernode-data-disks-csomópontonként|A feldolgozócsomópontonként használandó adatlemezek száma. A HDInsight Kafka csak adatlemezek esetén támogatott. Ez az oktatóanyag **2**értéket használ.|

    1. A Kafka REST proxy szükséges paraméterei:

        |Paraméter | Leírás|
        |---|---|
        |--kafka-menedzsment-csomópont méretű|A csomópont mérete. Ez az oktatóanyag a **Standard_D4_v2**értéket használja.|
        |--kafka-client-group-id|A Kafka rest proxy ügyfél AAD biztonsági csoportazonosítója. A $securityGroupID **változóból**a d. értéket adják át.|
        |--kafka-ügyfél-csoport-név|A Kafka rest proxy ügyfél AAD biztonsági csoportneve. A $securityGroupName **változóból**a d. értéket adják át.|
        |--verzió|A HDInsight-fürt verziójának legalább 4.0-nak kell lennie. A $clusterVersion **változóból**a d. értéket adják át.|
        |--komponens-verzió|A Kafka változatnak legalább 2.1-esnek kell lennie. A $componentVersion **változóból**a d. értéket adják át.|
    
        Ha rest proxy nélkül szeretné létrehozni a `--kafka-management-node-size` `--kafka-client-group-id`fürtöt, szüntesse meg a , és `--kafka-client-group-name` a `az hdinsight create` parancsból.

    1. Ha meglévő virtuális hálózattal rendelkezik, `--vnet-name` `--subnet`adja hozzá a paramétereket és a és az értékeit.

    A fürt létrehozásához írja be a következő parancsot:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    A fürt létrehozási folyamata több percig is eltarthat. Általában 15 körül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat számítunk fel, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Az erőforrások eltávolításához adja meg az alábbi parancsok mindegyikét vagy egy részét:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy Apache Kafka REST-proxyt az Azure HDInsightban az Azure CLI használatával, a Python-kód használatával kommunikálhat a REST-proxyval:

> [!div class="nextstepaction"]
> [Mintaalkalmazás létrehozása](./rest-proxy.md#client-application-sample)