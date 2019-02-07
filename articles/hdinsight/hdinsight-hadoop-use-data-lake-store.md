---
title: Data Lake Storage használata az Azure HDInsight Hadoop-keretrendszerrel
description: Ismerje meg, hogyan kérdezhet le adatokat az Azure Data Lake Storage-ból, és hogyan tárolhatja az elemzések eredményeit.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 5ba12e48092c02f9628e15166c84e871310d7556
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816373"
---
# <a name="use-data-lake-storage-with-azure-hdinsight-clusters"></a>Data Lake Storage használata az Azure HDInsight-fürtök

HDInsight-fürtben lévő adatok elemzéséhez, az adatokat tárolhatja akár a [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage általános 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage általános 2](../storage/blobs/data-lake-storage-introduction.md), vagy mindkettőt. Mindkét tárolási lehetőség lehetővé teszi a számításhoz használt HDInsight-fürtök biztonságos törlését a felhasználói adatok elvesztése nélkül.

Ebből a cikkből megtudhatja, hogyan Data Lake Storage használható a HDInsight-fürtökkel. Az Azure Storage és a HDInsight-fürtök együttes használatával kapcsolatos tudnivalókért lásd: [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](hdinsight-hadoop-use-blob-storage.md). Egy HDInsight-fürt létrehozásával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage elérése egy biztonságos csatornán keresztül mindig van, így nem `adls` fájlrendszer-sémanév. Mindig `adl` használandó.


## <a name="availability-for-hdinsight-clusters"></a>A HDInsight-fürtök rendelkezésre állása

Az Apache Hadoop támogatja az alapértelmezett fájlrendszer. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight fürt létrehozása során is megadhat egy blob-tárolóba az Azure Storage, az alapértelmezett fájlrendszerként, vagy a HDInsight 3.5-ös és újabb verziók, választhat Azure Storage vagy az Azure Data Lake Storage legyen az alapértelmezett fájlrendszer néhány kivételek. 

HDInsight-fürtök kétféle módon használhatja a Data Lake Storage:

* Az alapértelmezett tárolóként
* Kiegészítő tárolóként, ahol az Azure Storage Blob az alapértelmezett tároló.

Jelen pillanatban csak egy részét a HDInsight-fürt alapértelmezett tároló és a további tárfiókok használata a Data Lake Storage típusú, illetve verziójú támogatási:

| A HDInsight-fürt típusa | Data Lake Storage alapértelmezett tárolóként | Data Lake Storage kiegészítő tárolóként| Megjegyzések |
|------------------------|------------------------------------|---------------------------------------|------|
| A HDInsight 3.6-os verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.5-ös verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.4-es verziója | Nem | Igen | |
| A HDInsight 3.3-as verziója | Nem | Nem | |
| A HDInsight 3.2-es verziója | Nem | Igen | |
| Storm | | |Használhatja a Data Lake Storage Storm-topológiából származó adatok írására. Használhatja a Data Lake Storage a referenciaadatoknál, amelyek olvashatók lesznek egy Storm-topológia szerint.|

> [!WARNING]  
> HDInsight HBase nem támogatott az Azure Data Lake Storage általános 1

Használatával a Data Lake Storage egy tárfiókot, nem befolyásolja a teljesítmény vagy képes olvasni vagy írni az Azure storage a fürtből.
## <a name="use-data-lake-storage-as-default-storage"></a>Data Lake Storage használja alapértelmezett tárolóként

Ha a HDInsight alapértelmezett tárolójaként a Data Lake Storage már telepítve van, a fürttel kapcsolatos fájlok a Data Lake Storage a következő helyen vannak tárolva:

    adl://mydatalakestore/<cluster_root_path>/

ahol `<cluster_root_path>` egy Data Lake Storage-ban létrehozott mappa neve. Megadja a gyökér elérési útját minden fürt számára, használhatja ugyanazt a Data Lake Storage-fiókot több fürthöz. Így olyan beállítással rendelkezhet, ahol:

* Az 1. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster1storage`
* A 2. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster2storage`

Figyelje meg, hogy mindét fürt ugyanazt a Data Lake Storage-fiókot használja-e **mydatalakestore**. Minden egyes fürt hozzáfér a saját gyökér fájlrendszeréhez a Data Lake Storage. Az Azure Portalon végzett üzembe helyezésekor a **/fürtök/\<fürtnév>** mappanevet kell használnia a gyökér elérési úthoz.

Hogy a Data Lake Storage-t alapértelmezett tárolóként használhassa, meg kell adnia a szolgáltatásnév hozzáférhessen az alábbi elérési utakhoz:

- A Data Lake Storage-fiók gyökere.  Példa: adl://mydatalakestore/.
- Az összes fürt mappája.  Példa: adl://mydatalakestore/clusters.
- Egy adott fürt mappája.  Példa: adl://mydatalakestore/clusters/cluster1storage.

Szolgáltatás egyszerű és a hozzáférés biztosításával létrehozására vonatkozó további információkért tekintse meg a Data Lake Storage konfigurálása hozzáférést.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>A tanúsítvány kinyerését Azure Keyvault használható a fürt létrehozásakor

Ha szeretné beállítani az ADLS az alapértelmezett tárolóként egy új fürt számára, és az Azure Key Vaultban tárolja a tanúsítványt a szolgáltatásnévhez tartozó, nincsenek néhány további lépéseket kell a megfelelő formátumba alakítsa át a tanúsítványt. Az alábbi kódtöredékek bemutatják, hogyan elvégzi az átalakítást.

Először töltse le a tanúsítványt a Key Vaultból, és bontsa ki a `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Ezután konvertálni a `SecretValueText` tanúsítványt.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Ezt követően használhatja a `$identityCertificate` helyezhet üzembe egy új fürtöt az alábbi kódrészletben látható módon:

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-as-additional-storage"></a>Data Lake Storage használata kiegészítő tárolóként

Data Lake Storage kiegészítő tárolóként, valamint a fürt használja. Ezekben az esetekben a fürt alapértelmezett tárolója lehet az Azure Storage Blob vagy egy Data Lake-tárfiókra. HDInsight-feladatokat futtat a kiegészítő tárolóként a Data Lake Storage tárolt adatokat, ha a fájlok a teljes elérési útját kell használnia. Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Vegye figyelembe, hogy most nincs **cluster_root_path** az URL-címben. Amely, mert Data Lake Storage nem alapértelmezett tároló ebben az esetben meg kell tehát a fájlok elérési útját adja meg.

Az, hogy a Data Lake Storage használata kiegészítő tárolóként, meg kell biztosítania a szolgáltatásnév hozzáférhessen a fájlokat tároló elérési utakhoz.  Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Szolgáltatás egyszerű és a hozzáférés biztosításával létrehozására vonatkozó további információkért tekintse meg a Data Lake Storage konfigurálása hozzáférést.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Egynél több Data Lake Storage-fiókok használata

További Data Lake Storage-fiók hozzáadása és hozzáadása egynél több Data Lake Storage fiókok a HDInsight-fürt számára, így az adatok egy vagy több Data Lake Storage-fiókok vannak történik. Lásd: Configure Data Lake Storage hozzáférést.

## <a name="configure-data-lake-storage-access"></a>Data Lake Storage-hozzáférés konfigurálása

A HDInsight-fürt a Data Lake Storage hozzáférés konfigurálásához rendelkeznie kell egy Azure Active directory (Azure AD) szolgáltatásnevet. Kizárólag Azure AD-rendszergazdák hozhatnak létre szolgáltatásnevet. A szolgáltatásnevet egy tanúsítvánnyal kell létrehozni. További információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), és [egyszerű szolgáltatás létrehozása az önkiszolgáló önaláírt tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Ha az Azure Data Lake Storage HDInsight-fürt kiegészítő tárolóként használni kívánja, javasoljuk, hogy ehhez a fürt létrehozásakor ebben a cikkben leírtak szerint. Kiegészítő tárolóként, meglévő HDInsight-fürtre a hozzáadása az Azure Data Lake Storage nem támogatja.
>

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

Többféleképpen is el tudja érni a fájlokat a Data Lake Storage HDInsight fürtök.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel a fürtgyökér elérési útját az adl:/// karakterláncra cseréli le. Így a fenti példában lecserélheti az `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` helyett az `adl:///` értéket használhatja.

        adl:///<file path>

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg. Ha például a fájl teljes elérési útja a következő:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Ehelyett a következő relatív elérési úttal is elérheti ugyanazon sample.log fájlt.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage"></a>Data Lake Storage-hozzáféréssel rendelkező HDInsight-fürtök létrehozása

A következő hivatkozások használata a Data Lake Storage hozzáféréssel rendelkező HDInsight-fürtök létrehozásának részletes útmutatást.

* [A Portal használata](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [(A Data Lake Storage, az alapértelmezett tároló) PowerShell-lel](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [(A Data Lake Storage kiegészítő tárolóként) PowerShell-lel](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sablonok használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-access"></a>A Data Lake Storage access HDInsight-tanúsítvány frissítése

Az alábbi PowerShell-kód példa olvassa be egy tanúsítványt egy helyi fájlból vagy az Azure Key Vaultban, és frissíti az új tanúsítvány el az Azure Data Lake Storage a HDInsight-fürt. Adja meg a saját HDInsight-fürt nevét, az erőforráscsoport nevéhez, előfizetés-azonosító, az alkalmazás Azonosítóját, a tanúsítvány elérési útja. Írja be a jelszót, amikor a rendszer kéri.

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

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhat HDFS-kompatibilis Azure Data Lake Storage HDInsight szolgáltatáshoz. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Hozzon létre egy HDInsight-fürtön az Azure PowerShell használatával a Data Lake Storage használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [Az Apache Hive használata a HDInsight][hdinsight-use-hive]
* [Az Apache Pig használata a HDInsight][hdinsight-use-pig]
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
