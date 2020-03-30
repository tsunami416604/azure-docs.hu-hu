---
title: Nem lehet hozzáférni a Data Lake tárolófájljaihoz az Azure HDInsightban
description: Nem lehet hozzáférni a Data Lake tárolófájljaihoz az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 21269f7d5a9ec832a49a613351702dd24be156af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894163"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Nem lehet hozzáférni a Data Lake tárolófájljaihoz az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue-acl-verification-failed"></a>Probléma: Az ACL ellenőrzése nem sikerült

A következőhöz hasonló hibaüzenet jelenik meg:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Ok

Előfordulhat, hogy a felhasználó visszavonta a rendszerszámos szolgáltatás(SP) engedélyeket a fájlokon/mappákon.

### <a name="resolution"></a>Megoldás:

1. Ellenőrizze, hogy az SP rendelkezik -e "x" engedéllyel az elérési út mentén való bejáráshoz. További információt az Engedélyek című [témakörben talál.](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html) Minta dfs parancs a Data Lake tárfiókfájljaihoz/mappáihoz való hozzáférés ellenőrzéséhez:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Állítsa be a szükséges engedélyeket az elérési út eléréséhez a végrehajtás alatt álló olvasási/írási művelet alapján. A különböző fájlrendszer-műveletekhez szükséges engedélyeket itt olvashatja.

---

## <a name="issue-service-principal-certificate-expiry"></a>Kiállítás: Az egyszerű szolgáltatás tanúsítványának lejárata

A következőhöz hasonló hibaüzenet jelenik meg:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Ok

Lehet, hogy az egyszerű szolgáltatáshoz való hozzáféréshez biztosított tanúsítvány lejárt.

1. SSH a headnode. Ellenőrizze a tárfiókhoz való hozzáférést a következő dfs paranccsal:

    ```
    hdfs dfs -ls /
    ```

1. Ellenőrizze, hogy a hibaüzenet hasonló-e a következőhöz:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Az url-ek egyike `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls`a ból.

1. Az OAuth token beolvasásához futtassa a következő fürtparancsot.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Az érvényes egyszerű szolgáltatás kimenetének a következőhez kell hasonlónak lennie:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Ha az egyszerű szolgáltatás tanúsítványa lejárt, a kimenet a következővel fog kinézni:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Bármely más, az Azure Active Directoryval kapcsolatos hiba/tanúsítványlal kapcsolatos hiba felismerhető az átjáró URL-címének pingelésével az OAuth-token lekéréséhez.

1. Ha a következő hibaüzenet jelenik meg, amikor megpróbálja elérni az ADLS-t a HDI-fürtből. Ellenőrizze, hogy a tanúsítvány lejárt-e a fent említett lépések végrehajtásával.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Megoldás:

Hozzon létre egy új tanúsítványt, vagy rendeljen hozzá meglévő tanúsítványt a következő PowerShell-parancsfájl használatával:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Meglévő tanúsítvány hozzárendeléséhez hozzon létre egy tanúsítványt, készítse elő a .pfx fájlt és a jelszót. Társítsa a tanúsítványt a fürt létrehozásához, és készítse elő az AppId-ot.

Hajtsa végre a PowerShell parancsot, miután a paramétereket a tényleges értékekkel helyettesítette.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
