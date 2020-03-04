---
title: Data Lake Storage Gen1 használata a Hadoop az Azure HDInsight
description: Megtudhatja, hogyan kérdezheti le az adatait a Azure Data Lake Storage Gen1ból, és hogyan tárolhatja az elemzés eredményeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251093"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel

> [!Note]
> Új HDInsight-fürtök üzembe helyezése [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) használatával jobb teljesítmény és új funkciók.

A HDInsight-fürtben lévő adatelemzéshez az [Azure Storage](../storage/common/storage-introduction.md)-ban, [Azure Data Lake Storage 1](../data-lake-store/data-lake-store-overview.md). vagy [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)-ben tárolhatja az adattárakat. Az összes tárolási beállítás lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight-fürtöket a felhasználói adatvesztés nélkül.

Ebből a cikkből megtudhatja, hogyan működik a Data Lake Storage Gen1 HDInsight-fürtökkel. Az Azure Storage és a HDInsight-fürtök együttes használatával kapcsolatos tudnivalókért lásd: [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](hdinsight-hadoop-use-blob-storage.md). További információ a HDInsight-fürtök létrehozásáról: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> A Data Lake Storage Gen1 mindig egy biztonságos csatornán keresztül érhető el, így nincs `adls` filesystem-séma neve. Mindig `adl` használandó.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight-fürtök rendelkezésre állása

Apache Hadoop támogatja az alapértelmezett fájlrendszer fogalmát. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozási folyamata során megadhat egy BLOB-tárolót az Azure Storage-ban alapértelmezett fájlrendszerként, illetve a HDInsight 3,5-es és újabb verzióival, kiválaszthatja az Azure Storage vagy a Azure Data Lake Storage Gen1 alapértelmezett fájlrendszerként néhány kivétel. Vegye figyelembe, hogy a fürtnek és a Storage-fióknak ugyanabban a régióban kell lennie.

A HDInsight-fürtök két módon használhatják a Data Lake Storage Gen1:

* Az alapértelmezett tárolóként
* Kiegészítő tárolóként, ahol az Azure Storage Blob az alapértelmezett tároló.

Mostantól csak néhány HDInsight-fürt típusa/verziója támogatja a Data Lake Storage Gen1 alapértelmezett tárolóként és további Storage-fiókokkal:

| A HDInsight-fürt típusa | Data Lake Storage Gen1 alapértelmezett tárolóként | Data Lake Storage Gen1 további tárterületként| Megjegyzések |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 4,0-es verzió | Nem | Nem |A ADLS Gen1 nem támogatott a HDInsight 4,0 |
| A HDInsight 3.6-os verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.5-ös verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.4-es verziója | Nem | Igen | |
| A HDInsight 3.3-as verziója | Nem | Nem | |
| A HDInsight 3.2-es verziója | Nem | Igen | |
| Storm | | |A Storm-topológiából származó adatok írásához Data Lake Storage Gen1 használható. Data Lake Storage is használhat olyan hivatkozási információkhoz, amelyeket aztán egy Storm-topológia tud olvasni.|

> [!WARNING]  
> A HDInsight HBase nem támogatott a Azure Data Lake Storage Gen1

A Data Lake Storage Gen1 használata további tárolási fiókként nem befolyásolja a teljesítményt, illetve az Azure Storage-nak a fürtből való olvasását és írását.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 használata alapértelmezett tárolóként

Ha a HDInsight Data Lake Storage Gen1 alapértelmezett tárolóként van telepítve, a fürttel kapcsolatos fájlok a `adl://mydatalakestore/<cluster_root_path>/`ban tárolódnak, ahol a `<cluster_root_path>` a Data Lake Storage létrehozott mappa neve. Ha minden fürthöz megadja a gyökér elérési útját, ugyanazt a Data Lake Storage fiókot használhatja több fürthöz is. Így olyan beállítással rendelkezhet, ahol:

* Az 1. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster1storage`
* A 2. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster2storage`

Figyelje meg, hogy mindkét fürt ugyanazt a Data Lake Storage Gen1 fiókot használja **mydatalakestore**. Minden fürt hozzáfér a saját gyökérszintű fájlrendszeréhez Data Lake Storageban. Az Azure Portalon végzett üzembe helyezésekor a **/fürtök/\<fürtnév>** mappanevet kell használnia a gyökér elérési úthoz.

Ahhoz, hogy a Data Lake Storage Gen1t alapértelmezett tárolóként lehessen használni, meg kell adnia a szolgáltatás egyszerű elérését a következő elérési utakhoz:

* A Data Lake Storage Gen1 fiók gyökerét.  Példa: adl://mydatalakestore/.
* Az összes fürt mappája.  Példa: adl://mydatalakestore/clusters.
* Egy adott fürt mappája.  Példa: adl://mydatalakestore/clusters/cluster1storage.

Az egyszerű szolgáltatásnév létrehozásával és a hozzáférés megadásával kapcsolatos további információkért lásd: Data Lake Storage hozzáférés konfigurálása.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Tanúsítvány kinyerése az Azure kulcstartóból a fürt létrehozásához való használathoz

Ha az új fürt alapértelmezett tárolójának Azure Data Lake Storage Gen1 szeretné beállítani, és a szolgáltatás tanúsítványa Azure Key Vault van tárolva, néhány további lépés szükséges a tanúsítvány megfelelő formátumra való átalakításához. A következő kódrészletek bemutatják, hogyan hajthatja végre az átalakítást.

Először töltse le a tanúsítványt a Key Vaultból, és bontsa ki a `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Ezután alakítsa át a `SecretValueText`t egy tanúsítványra.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Ezután a `$identityCertificate` használatával telepítheti az új fürtöt a következő kódrészletbe:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 használata kiegészítő tárolóként

A Data Lake Storage Gen1 a fürt további tárterületként is használható. Ilyen esetekben a fürt alapértelmezett tárolója Azure Storage Blob vagy Data Lake Storage fiók lehet. Ha HDInsight-feladatokat futtat a Data Lake Storageban tárolt adatokon további tárolóként, akkor a fájlok teljes elérési útját kell használnia. Például:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Vegye figyelembe, hogy most nincs **cluster_root_path** az URL-címben. Ennek az az oka, hogy Data Lake Storage ebben az esetben nem alapértelmezett tároló, ezért mindössze annyit kell tennie, hogy megadja a fájlok elérési útját.

Ahhoz, hogy egy Data Lake Storage Gen1t további tárterületként lehessen használni, csak a szolgáltatás egyszerű hozzáférését kell megadnia a fájlok tárolására szolgáló elérési utakhoz.  Például:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Az egyszerű szolgáltatásnév létrehozásával és a hozzáférés megadásával kapcsolatos további információkért lásd: Data Lake Storage hozzáférés konfigurálása.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Több Data Lake Storage-fiók használata

Egy Data Lake Storage fiók további hozzáadásával és több Data Lake Storage-fiók hozzáadásával a HDInsight-fürtnek a további Data Lake Storage-fiókokban lévő adatokhoz való engedélyezésével biztosítható. Lásd: Data Lake Storage hozzáférés konfigurálása.

## <a name="configure-data-lake-storage-access"></a>Data Lake Storage hozzáférés konfigurálása

Ha Data Lake Storage hozzáférést szeretne konfigurálni a HDInsight-fürtről, rendelkeznie kell egy Azure Active Directory-(Azure AD-) egyszerű szolgáltatással. Kizárólag Azure AD-rendszergazdák hozhatnak létre szolgáltatásnevet. A szolgáltatásnevet egy tanúsítvánnyal kell létrehozni. További információkért lásd a [fürtök HDInsightban történő beállításának rövid útmutatóját](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) és a [Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) témakört.

> [!NOTE]  
> Ha a HDInsight-fürt számára további tárterületként kívánja használni a Azure Data Lake Storage Gen1, javasoljuk, hogy ezt a cikket a jelen cikkben ismertetett módon hozza létre a fürt létrehozásakor. Azure Data Lake Storage Gen1 hozzáadása egy meglévő HDInsight-fürthöz kiegészítő tárolóként nem támogatott forgatókönyv.

A Data Lake Storage Gen1 hozzáférés-vezérlési modelljének alapjaival kapcsolatos további információkért lásd: [Azure Data Lake Storage Gen1 hozzáférés-vezérlése](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

Több módon is hozzáférhet a Data Lake Storage lévő fájlokhoz egy HDInsight-fürtről.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel az elérési utat a fürt gyökerére cseréli le a következővel:

    ```
    adl:///<file path>
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Adatelérési példák

A példák a fürt fő csomópontjának [SSH-kapcsolatain](./hdinsight-hadoop-linux-use-ssh-unix.md) alapulnak. A példák mindhárom URI-sémát használják. Cserélje le a `DATALAKEACCOUNT` és a `CLUSTERNAME` értéket a megfelelő értékekre.

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancs

1. Hozzon létre egy egyszerű fájlt a helyi tárolóban.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürt tárterületén.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürt tárolójába.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. A fürt tárterületén lévő könyvtár tartalmának listázása.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Struktúra-tábla létrehozása

A szemléltető célokra három fájl helye látható. A tényleges végrehajtáshoz használja a `LOCATION` bejegyzések egyikét.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Tároló elérési útjának azonosítása a Ambari

A konfigurált alapértelmezett tároló teljes elérési útjának azonosításához lépjen a **HDFS** > **configs** elemre, és adja meg `fs.defaultFS` a szűrő beviteli mezőjében.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1hoz hozzáféréssel rendelkező HDInsight-fürtök létrehozása

Az alábbi hivatkozásokra kattintva részletesen megtudhatja, hogyan hozhat létre HDInsight-fürtöket Data Lake Storage Gen1hoz való hozzáféréssel.

* [A Portal használata](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [A PowerShell használata (Data Lake Storage Gen1 alapértelmezett tárolóként)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [A PowerShell használata (Data Lake Storage Gen1 kiegészítő tárolóként)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sablonok használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1-hozzáférés HDInsight-tanúsítványának frissítése

A következő példa PowerShell-kód beolvas egy tanúsítványt egy helyi fájlból vagy Azure Key Vault, és az új tanúsítvánnyal frissíti a HDInsight-fürtöt az Azure Data Lake Storage Gen1 eléréséhez. Adja meg a saját HDInsight-fürt nevét, az erőforráscsoport nevét, az előfizetés AZONOSÍTÓját, az alkalmazás AZONOSÍTÓját, a tanúsítvány helyi elérési útját. Ha a rendszer kéri, írja be a jelszót.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a HDFS-kompatibilis Azure Data Lake Storage Gen1t a HDInsight. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rövid útmutató: Fürtök beállítása a HDInsightban](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Hozzon létre egy HDInsight-fürtöt, amelyet a Data Lake Storage Gen1 használ a Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Adatok feltöltése a HDInsight](hdinsight-upload-data.md)
* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Azure Storage közös hozzáférésű aláírásait használva korlátozza az adathozzáférést a HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezés használatával](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
