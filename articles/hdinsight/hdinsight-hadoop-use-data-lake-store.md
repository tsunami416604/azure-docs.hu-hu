---
title: A Data Lake Storage Gen1 használata a Hadoop segítségével az Azure HDInsightban
description: Ismerje meg, hogyan kérdezheti le az adatokat az Azure Data Lake Storage Gen1-ből, és hogyan tárolhatja az elemzés eredményeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251093"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>A Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel

> [!Note]
> Az [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) használatával új HDInsight-fürtöket helyezhet üzembe a jobb teljesítmény és az új funkciók érdekében.

A HDInsight-fürt adatainak elemzéséhez az azure [storage,](../storage/common/storage-introduction.md)az [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)vagy az Azure Data Lake Storage Gen [2](../storage/blobs/data-lake-storage-introduction.md)szolgáltatásban tárolhatja az adatokat. Minden tárolási beállítás lehetővé teszi a számítási célokra használt HDInsight-fürtök biztonságos törlését a felhasználói adatok elvesztése nélkül.

Ebben a cikkben megtudhatja, hogyan működik a Data Lake Storage Gen1 hdinsight-fürtökkel. Az Azure Storage és a HDInsight-fürtök együttes használatával kapcsolatos tudnivalókért lásd: [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](hdinsight-hadoop-use-blob-storage.md). A HDInsight-fürtök létrehozásáról az [Apache Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál további információt.

> [!NOTE]  
> A Data Lake Storage Gen1 mindig biztonságos csatornán keresztül `adls` érhető el, így nincs fájlrendszerséma neve. Mindig `adl` használandó.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight-fürtök elérhetősége

Az Apache Hadoop támogatja az alapértelmezett fájlrendszer fogalmát. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozási folyamata során alapértelmezett fájlrendszerként megadhat egy blobtárolót az Azure Storage-ban, vagy a HDInsight 3.5 és újabb verziók segítségével kiválaszthatja az Azure Storage vagy az Azure Data Lake Storage Gen1 alapértelmezett fájlrendszert egy néhány kivételtől eltekintve. Vegye figyelembe, hogy a fürt és a tárfiók üzemeltetni kell ugyanabban a régióban.

A HDInsight-fürtök kétféleképpen használhatják a Data Lake Storage Gen1-et:

* Az alapértelmezett tárolóként
* Kiegészítő tárolóként, ahol az Azure Storage Blob az alapértelmezett tároló.

Jelenleg csak néhány HDInsight-fürttípus/-verzió támogatja a Data Lake Storage Gen1 alapértelmezett tárként és további tárfiókként való használatát:

| A HDInsight-fürt típusa | Data Lake Storage Gen1 alapértelmezett tárolóként | Data Lake Storage Gen1 kiegészítő tárolóként| Megjegyzések |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 4.0-s verzió | Nem | Nem |Az ADLS Gen1 nem támogatja a HDInsight 4.0-s |
| A HDInsight 3.6-os verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.5-ös verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.4-es verziója | Nem | Igen | |
| A HDInsight 3.3-as verziója | Nem | Nem | |
| A HDInsight 3.2-es verziója | Nem | Igen | |
| Storm | | |A Data Lake Storage Gen1 segítségével adatokat írhat egy Storm-topológiából. A Data Lake Storage referenciaadatokat is használhat, amelyeket a Storm-topológia ezután elolvashat.|

> [!WARNING]  
> A HDInsight HBase nem támogatott az Azure Data Lake Storage Gen1 szolgáltatással

A Data Lake Storage Gen1 használata további tárfiókként nem befolyásolja a teljesítményt, illetve az Azure storage-ba való olvasásvagy írás lehetőségét a fürtből.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>A Data Lake Storage Gen1 használata alapértelmezett tárolóként

Ha a HDInsight alapértelmezett tárolóként a Data Lake Storage Gen1 szolgáltatással `adl://mydatalakestore/<cluster_root_path>/`van `<cluster_root_path>` telepítve, a fürthöz kapcsolódó fájlok a alkalmazásban tárolódnak, ahol a Data Lake Storage-ban létrehozott mappa neve. Az egyes fürtök gyökérelérési útjának megadásával ugyanazt a Data Lake Storage-fiókot használhatja egynél több fürthöz. Így olyan beállítással rendelkezhet, ahol:

* Az 1. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster1storage`
* A 2. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster2storage`

Figyelje meg, hogy mindkét fürt ugyanazt a Data Lake Storage Gen1 fiókot használja **mydatalakestore.** Minden fürt saját gyökérfájlrendszerhez fér hozzá a Data Lake Storage-ban. Az Azure Portalon végzett üzembe helyezésekor a **/fürtök/\<fürtnév>** mappanevet kell használnia a gyökér elérési úthoz.

Ahhoz, hogy a Data Lake Storage Gen1 alapértelmezett tárolóként használható legyen, a következő elérési utakhoz kell biztosítania a szolgáltatásegyszerű hozzáférést:

* A Data Lake Storage Gen1 fiók gyökér.  Példa: adl://mydatalakestore/.
* Az összes fürt mappája.  Példa: adl://mydatalakestore/clusters.
* Egy adott fürt mappája.  Példa: adl://mydatalakestore/clusters/cluster1storage.

Az egyszerű szolgáltatás létrehozása és a hozzáférési engedélyek létrehozásáról a Data Lake Storage-hozzáférés konfigurálása című témakörben talál további információt.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Tanúsítvány kinyerése az Azure Keyvaultból a fürt létrehozásához

Ha azt szeretné, hogy az Azure Data Lake Storage Gen1 alapértelmezett tárolóként egy új fürt höz, és a szolgáltatás egyszerű szolgáltatás az Azure Key Vault tárolja, néhány további lépésre van szükség a tanúsítvány konvertálása a megfelelő formátumban. A következő kódrészletek bemutatják, hogyan hajthatja végre a konverziót.

Először töltse le a tanúsítványt `SecretValueText`a Key Vaultból, és bontsa ki a .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Ezután alakítsa át a `SecretValueText` tanúsítványt.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Ezután használhatja `$identityCertificate` az új fürt központi telepítéséhez, mint a következő kódrészletben:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>A Data Lake Storage Gen1 használata további tárként

A Data Lake Storage Gen1 további tárolóként is használhatja a fürtöt. Ilyen esetekben a fürt alapértelmezett tárolója lehet egy Azure Storage Blob vagy egy Data Lake Storage-fiók. Ha hdinsight-feladatokat futtat a Data Lake Storage-ban tárolt adatokon további tárolóként, a fájlok teljesen minősített elérési útját kell használnia. Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Vegye figyelembe, hogy most nincs **cluster_root_path** az URL-címben. Ennek az az oka, hogy a Data Lake Storage ebben az esetben nem alapértelmezett tároló, így mindössze annyit kell tennie, hogy megadja a fájlok elérési útját.

Ahhoz, hogy a Data Lake Storage Gen1 további tárolóként használhassa, csak a szolgáltatásegyszerű hozzáférést kell biztosítania a fájlok tárolási útvonalaihoz.  Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Az egyszerű szolgáltatás létrehozása és a hozzáférési engedélyek létrehozásáról a Data Lake Storage-hozzáférés konfigurálása című témakörben talál további információt.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Több Data Lake Storage-fiók használata

A Data Lake Storage-fiók hozzáadása továbbiként, és egynél több Data Lake Storage-fiók hozzáadása úgy valósul meg, hogy a HDInsight-fürt engedélyt ad egy ércben lévő adatokra több Data Lake Storage-fiókhoz. Lásd: A Data Lake Storage-hozzáférés konfigurálása.

## <a name="configure-data-lake-storage-access"></a>A Data Lake Storage-hozzáférés konfigurálása

A Data Lake Storage-hozzáférés konfigurálásához a HDInsight-fürtről, rendelkeznie kell egy Azure Active Directory (Azure AD) egyszerű szolgáltatás. Kizárólag Azure AD-rendszergazdák hozhatnak létre szolgáltatásnevet. A szolgáltatásnevet egy tanúsítvánnyal kell létrehozni. További információkért lásd a [fürtök HDInsightban történő beállításának rövid útmutatóját](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) és a [Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) témakört.

> [!NOTE]  
> Ha az Azure Data Lake Storage Gen1-et a HDInsight-fürt további tárolójaként fogja használni, javasoljuk, hogy ezt a fürt létrehozása közben tegye meg a jelen cikkben leírtak szerint. Az Azure Data Lake Storage Gen1 hozzáadása további tárolóként egy meglévő HDInsight-fürthöz nem támogatott forgatókönyv.

A Data Lake Storage Gen1 hozzáférés-vezérlési modelljének alapjairól az [Azure Data Lake Storage Gen1 hozzáférés-vezérlése című témakörben talál](../data-lake-store/data-lake-store-access-control.md)további információt.

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

A Data Lake Storage-ban található fájlok hdinsight-fürtről többféleképpen érhetők el.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel a fürtgyökérhez vezető elérési utat a következőre cserélheti:

    ```
    adl:///<file path>
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Példák adatelérési példák

Példák alapulnak [ssh kapcsolatot](./hdinsight-hadoop-linux-use-ssh-unix.md) a fürt fő csomópontja. A példák mindhárom URI-sémát használják. Cserélje `DATALAKEACCOUNT` `CLUSTERNAME` ki és a megfelelő értékeket.

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancsok

1. Hozzon létre egy egyszerű fájlt a helyi tárolón.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürttárolón.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürttárolóba.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. A fürttároló könyvtártartalmának listázása.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive-tábla létrehozása

Három fájlhely jelenik meg szemléltető célokra. Tényleges végrehajtáshoz csak az `LOCATION` egyik tételt használja.

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

## <a name="identify-storage-path-from-ambari"></a>Az Ambari tárolási útvonalának azonosítása

A konfigurált alapértelmezett tároló teljes elérési útjának azonosításához keresse `fs.defaultFS` meg a **HDFS** > **configs billentyűt,** és írja be a szűrőbeviteli mezőbe.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>HDInsight-fürtök létrehozása a Data Lake Storage Gen1 szolgáltatással

Az alábbi hivatkozásokat a Data Lake Storage Gen1-hez való hozzáféréssel rendelkező HDInsight-fürtök létrehozásáról szóló részletes útmutatásért használhatja.

* [A portál használata](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [A PowerShell használata (a Data Lake Storage Gen1 alapértelmezett tárolóként)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell használata (a Data Lake Storage Gen1 további tárolóként)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sablonok használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>A HDInsight-tanúsítvány frissítése a Data Lake Storage Gen1-hozzáférésért

A következő példa PowerShell-kód beolvassa a tanúsítványt egy helyi fájlból vagy az Azure Key Vault, és frissíti a HDInsight-fürt az új tanúsítvány eléréséhez Azure Data Lake Storage Gen1. Adja meg saját HDInsight-fürtnevét, erőforráscsoport nevét, előfizetés-azonosítót, alkalmazásazonosítót, a tanúsítvány helyi elérési útját. Amikor a rendszer kéri, írja be a jelszót.

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a HDFS-kompatibilis Azure Data Lake Storage Gen1-et a HDInsight-mal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Ismerkedés az Azure HDInsight-mal](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rövid útmutató: Fürtök beállítása a HDInsightban](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Hozzon létre egy HDInsight-fürtöt a Data Lake Storage Gen1 használatához az Azure PowerShell használatával](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md)
* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezésével](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
