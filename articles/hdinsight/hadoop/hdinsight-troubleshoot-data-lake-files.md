---
title: Nem lehet hozzáférni Data Lake Storage-fájlokhoz az Azure HDInsight
description: Nem lehet hozzáférni Data Lake Storage-fájlokhoz az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/13/2019
ms.openlocfilehash: 3e4745d1c9e28e907d87298d1ab8ef3c9b104b1d
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014601"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Nem lehet hozzáférni Data Lake Storage-fájlokhoz az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue-acl-verification-failed"></a>Probléma: Az ACL ellenőrzése nem sikerült

A következőhöz hasonló hibaüzenet jelenik meg:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Ok

Előfordulhat, hogy a felhasználó visszavonta az egyszerű szolgáltatásnév (SP) engedélyeit a fájlokon vagy mappákon.

### <a name="resolution"></a>Megoldás:

1. Győződjön meg arról, hogy az SP "x" engedélyekkel rendelkezik az elérési út mentén való bejáráshoz. További információ: [engedélyek](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Példa a DFS-parancsra a Data Lake Storage-fiókban található fájlokhoz vagy mappákhoz való hozzáférés vizsgálatához:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Állítsa be a szükséges engedélyeket az elérési út eléréséhez az olvasási/írási művelet végrehajtása alapján. Itt találja a különböző fájlrendszeri műveletekhez szükséges engedélyeket.

---

## <a name="issue-service-principal-certificate-expiry"></a>Probléma: Egyszerű szolgáltatásnév-tanúsítvány lejárata

A következőhöz hasonló hibaüzenet jelenik meg:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Ok

Előfordulhat, hogy a szolgáltatásnév eléréséhez megadott tanúsítvány érvényessége lejárt.

1. SSH-t a átjárócsomóponthoz. A Storage-fiókhoz való hozzáférés ellenőrzését a következő DFS-parancs használatával:

    ```
    hdfs dfs -ls /
    ```

1. Győződjön meg arról, hogy a hibaüzenet a következőhöz hasonló:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Az egyik URL-cím beolvasása a következőből `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls`:.

1. Futtassa a következő cURL-parancsot az OAuth-jogkivonat lekéréséhez.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Egy érvényes egyszerű szolgáltatásnév kimenetének a következőhöz hasonlónak kell lennie:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Ha az egyszerű szolgáltatásnév tanúsítványa lejárt, a kimenet a következőképpen fog kinézni:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Az OAuth token lekéréséhez Pingeli az átjáró URL-címét, és minden más Azure Active Directory kapcsolódó hibát/tanúsítványt észlelt.

1. Ha a következő hibaüzenetet kap, amikor megpróbál hozzáférni a ADLS a HDI-fürtről. Ellenőrizze, hogy a tanúsítvány lejárt-e a fent említett lépések követésével.

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

Meglévő tanúsítvány hozzárendeléséhez hozzon létre egy tanúsítványt, és készítse elő a. pfx-fájlt és a jelszót. Társítsa a tanúsítványt az egyszerű szolgáltatáshoz, amelyhez a fürt létrejött, és hogy a AppId készen áll.

Futtassa a PowerShell-parancsot a paramétereknek a tényleges értékekkel való helyettesítése után.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
