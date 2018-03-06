---
title: "Állítsa be a Azure Key Vault-végpontok közötti fő elforgatás és naplózási |} Microsoft Docs"
description: "Ez az útmutató segítségével rendszerrel legfontosabb rotációjával és figyelési kulcstároló naplóit."
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: jodehavi;stgriffi
ms.openlocfilehash: edc9a870257536235c2bef139dc4d1ead7dd7a8f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Az Azure Key Vault beállítása végpontok közötti kulcsforgatással és auditálással
## <a name="introduction"></a>Bevezetés
Miután létrehozta a kulcstároló, lesz a kulcsok és titkos kulcsok tárolására, hogy a tároló használatának megkezdéséhez. Az alkalmazások többé nem kell megőrizni a kulcsok vagy titkos kulcsok, hanem fog kérni azokat a key vault a igény szerint. Ez lehetővé teszi a kulcsok és titkos kulcsok frissítése az alkalmazás, így akár a kulcs és a titkos felügyeleti lehetőségek széles választékát viselkedésének módosítása nélkül.

>[!IMPORTANT]
> Ebben a cikkben szereplő példák csak illusztrációs célokat szolgálnak. Ezek nem üzemi használatra lettek tervezve. 

Ez a cikk végigvezeti az Azure Key Vault használatával használatával egy titkos kulcsot, ebben az esetben egy Azure Storage-fiók kulcsát, hogy egy alkalmazás egy példát. Is egy ütemezett elforgatási szögét a tárfiók kulcsa végrehajtását mutatja be. Végül azt végigvezeti bemutatjuk a kulcstartót naplók figyelésére, és riasztást, ha a nem várt kérelmeket.

> [!NOTE]
> Ez az oktatóanyag nem célja a részletesen ismertetik a kulcstartót kezdeti telepítése. Ezekről a [Get started with Azure Key Vault](key-vault-get-started.md) (Bevezetés az Azure Key Vault használatába) című cikkben találhat információt. Platformfüggetlen parancssori felületre vonatkozó utasításokat lásd: [kezelése Key Vault parancssori felület használatával](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>A Key Vault beállítása
Ahhoz, hogy egy alkalmazás titkos kulcs lekérése a Key Vault, először hozzon létre a titkos kulcsot, és töltse fel azt a tárolóban. Ehhez az Azure PowerShell-munkamenet indítása, a következő paranccsal Azure-fiókjába történő bejelentkezés:

```powershell
Login-AzureRmAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. PowerShell beolvassa az összes olyan előfizetést, ehhez a fiókhoz társított. PowerShell alapértelmezés szerint az elsőt használja.

Ha több előfizetéssel rendelkezik, akkor előfordulhat, hogy adja meg azt, amelyik a kulcstároló létrehozásához használt. Adja meg a fiókhoz tartozó előfizetések megjelenítéséhez a következőket:

```powershell
Get-AzureRmSubscription
```

Adja meg az előfizetést, a key vault naplózása akkor van társítva, írja be a következőt:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Mivel ez a cikk bemutatja, hogy a tárfiók kulcsára, titkos kulcs tárolása, ha előbb telepítik azokra a tárfiók kulcsára.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

A titkos kulcsot (ebben az esetben a tárfiók kulcsára) beolvasása, után kell átalakítani, amely egy biztonságos karakterláncot, és majd titkos kulcs létrehozása ezt az értéket a key vaultban lévő.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Az URI a következő lekérése a létrehozott titkos kulcsot. Ez szolgál egy későbbi lépésben hívásakor a key vault beolvasni a titkos kulcsot. A következő PowerShell-parancsot, és jegyezze fel az azonosító értéket, a titkos URI:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Az alkalmazás beállítása
Most, hogy a titkos kulcs tárolása, kód segítségével kérje le, majd használja. Ennek eléréséhez szükséges néhány lépésből áll. Az első és legfontosabb lépés az alkalmazás regisztrálása az Azure Active Directoryban, és majd közli az alkalmazással kapcsolatos adatok Key Vault, így engedélyezheti, hogy az alkalmazás érkező kérelmeket.

> [!NOTE]
> Az alkalmazás léteznie kell a azonos Azure Active Directory-bérlőt, mint a kulcstárolót.
>
>

Nyissa meg az Azure Active Directory az alkalmazások fülre.

![Nyissa meg az alkalmazások az Azure Active Directoryban](./media/keyvault-keyrotation/AzureAD_Header.png)

Válasszon **Hozzáadás** hozzáadhat egy alkalmazást az Azure Active Directoryban.

![A Hozzáadás gombra](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Hagyja meg az alkalmazás típusú **WEB APPLICATION AND/OR WEB API** és nevezze el az alkalmazást.

![Az alkalmazás neve](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Adja meg az alkalmazás egy **SIGN-ON URL** és egy **APP ID URI**. Ebben a bemutatóban bármilyen is lehetnek, és azokat később módosítható, ha szükséges.

![Adja meg a szükséges URI-azonosítók](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Az alkalmazás Azure Active Directoryba való hozzáadása után jut az alkalmazás oldalhoz. Kattintson a **konfigurálása** lapon, majd keresse meg és másolja a **ügyfél-azonosító** érték. Jegyezze fel a későbbi lépésekben az ügyfél-azonosító.

Ezt követően az alkalmazás kulcs létrehozása, az Azure Active Directory kommunikálhat. Ez alapján hozhat létre a **kulcsok** szakasz a **konfigurációs** fülre. Jegyezze fel az újonnan létrehozott kulcs használható az Azure Active Directory-alkalmazás egy későbbi lépésben.

![Az Azure Active Directory-alkalmazás kulcsok](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Mielőtt bármely hívást az alkalmazás a kulcstartót létrehozó, a a key vault kérje meg az alkalmazásról és az engedélyeket. A következő parancs időt vesz igénybe, a tároló neve és az ügyfél-Azonosítóját az Azure Active Directory-alkalmazás és biztosít **beolvasása** a kulcstartót eléréséhez alkalmazására.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Ekkor készen áll az alkalmazás hívások készítéséhez. Az alkalmazás telepítenie kell a NuGet-csomagok működjön együtt az Azure Key Vault és az Azure Active Directory szükséges. A Visual Studio Csomagkezelő konzolról adja meg a következő parancsokat. : Ez a cikk írásának pillanatában a az Azure Active Directory-csomag nem 3.10.305231913, így előfordulhat, hogy a legújabb verzióra, és ennek megfelelően szeretné.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Az alkalmazás kódjában hozzon létre egy osztályt, ahhoz, hogy az az Azure Active Directory hitelesítési módszert. Ebben a példában az adott osztály neve **Utils**. Adja hozzá a következő using utasítást:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ezután adja hozzá a következő metódust a JWT jogkivonat beolvasása az Azure Active Directoryból. A karbantartási követelmények érdemes lehet a kódolt karakterlánc-értékek áthelyezi a webhely vagy alkalmazás konfigurációja.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Adja hozzá a szükséges kódot Key Vault és a titkos érték beolvasása. Először hozzá kell adnia a következő using utasítást:

```csharp
using Microsoft.Azure.KeyVault;
```

Adja hozzá a metódushívások Key Vault meghívni, és a titkos kulcs beolvasása. Ez a módszer biztosítja a titkos kulcsot, amelyet az előző lépésben mentett URI. Vegye figyelembe a használatát a **GetToken** metódust a **Utils** korábban létrehozott osztályt.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Az alkalmazás futtatásakor kell hitelesítéséhez az Azure Active Directory és a titkos érték majd lekérése az Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Azure Automation használatával kulcs Elforgatás
Az Azure Key Vault titkok, tárolt értékek Elforgatás stratégiája megvalósításának számos lehetőség áll rendelkezésre. Titkos kulcsok forgatható el kézi folyamat részeként, akkor lehetséges, hogy forgatható programozott módon API-hívásokkal, vagy előfordulhat, hogy forgatható vállalja egy automatizálási parancsfájl. Ez a cikk alkalmazásában fog használni az Azure Automation szolgáltatásban, együttesen Azure PowerShell módosítása az Azure Storage-fiók hozzáférési kulcsot. A kulcstároló titkos kulcsot az új kulcs majd frissíti.

Ahhoz, hogy az Azure Automation key vaultban lévő titkos értékeinek beállításához, ha előbb telepítik azokra az ügyfél-azonosító nevű AzureRunAsConnection, az Azure Automation-példányt létrejöttekor létrehozott. Válassza ezt az Azonosítót található **eszközök** az Azure Automation-példányból. Ott, válasszon **kapcsolatok** , és válassza a **AzureRunAsConnection** szolgáltatás elvet. Vegye figyelembe a **Alkalmazásazonosító**.

![Azure Automation ügyfél-azonosítója](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

A **eszközök**, válassza a **modulok**. A **modulok**, jelölje be **gyűjtemény**, majd keresse meg és **importálási** frissített verziói, a következő modulok mindegyikének:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Ez a cikk írásának pillanatában, csak a korábban feljegyzett modulok frissítenie kell a következő parancsfájl szükséges. Ha talál meg, hogy az automation-feladat meghiúsul, győződjön meg arról, hogy importálta-e minden szükséges modulokat és függőségi viszonyaikat.
>
>

Után az alkalmazás-azonosítója az Azure Automation-kapcsolat, meg kell, hogy a kulcstároló, hogy az alkalmazás fér hozzá a tárolóban lévő titkos kulcsok frissítése. Ehhez a következő PowerShell-paranccsal:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Válassza ki, **Runbookok** az Azure Automation-példány, és válassza ki azt a **hozzáadása egy Runbook**. Kattintson a **Gyors létrehozás** gombra. A runbook neve, és válassza ki **PowerShell** a runbook típusa. Lehetősége van a adjon meg egy leírást. Végezetül kattintson **létrehozása**.

![Runbook létrehozása](./media/keyvault-keyrotation/Create_Runbook.png)

Illessze be a következő PowerShell-parancsfájlt az új runbook szerkesztő ablaktáblában:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

A szerkesztő ablaktáblában válassza **teszt ablaktábla** tesztelni a parancsfájlt. Miután a parancsfájl hiba nélkül fut-e, kijelölheti a **közzététel**, és újra a runbook konfigurációs ablaktábla a runbook ütemezés szerint alkalmazhatja.

## <a name="key-vault-auditing-pipeline"></a>Key Vault naplózási folyamat
Kulcstároló beállításakor bekapcsolása gyűjtött naplók a hozzáférési kérelmeket a key vault naplózását. Ezek a naplók a kijelölt Azure Storage-fiókban tárolt, és figyeli, és elemezni, lekért. Az alábbi forgatókönyvet az Azure functions az Azure logic apps és kulcstároló-naplók segítségével hozzon létre egy folyamatot, az e-mailt küld, ha olyan alkalmazás, amelynek felel meg az alkalmazás Azonosítóját a webalkalmazás titkok lekéri a tárolóból.

Először engedélyeznie kell a kulcstartót bejelentkezni. Ez a következő PowerShell-parancsok segítségével végezhető (teljes részletei láthatók [kulcs-tároló-naplózás](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Miután engedélyezve van, az auditnaplókat indítása a kijelölt tárfiókkal való gyűjtésére. Ezek a naplók tartalmaz arról, hogyan és mikor érhetők el a kulcstárolót, és ki eseményeket.

> [!NOTE]
> Elérheti a naplóinformációkat 10 perccel a kulcstartót művelet után. Általában lesz gyorsabb, mint ez.
>
>

A következő lépés [hozzon létre egy Azure Service Bus-üzenetsorba](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Ez azért, ahol a kulcstartót naplók leküldött vannak. Ha a naplózási üzenetek a várólista, a logikai alkalmazás felveszi őket, és kezelje őket. Hozzon létre egy service bus az alábbi lépéseket:

1. Service Bus-névtér létrehozása (Ha már rendelkezik egy, a, folytassa a 2. lépésben használni kívánt).
2. Keresse meg a service bus az Azure portálon, és válassza ki a létrehozandó sorból névteret.
3. Válassza ki **hozzon létre egy erőforrást**, **vállalati integrációs**, **Service Bus**, majd adja meg a szükséges adatokat.
4. Válassza ki a Service Bus kapcsolati információit a névtér kiválasztásával, majd **kapcsolatadatok**. Szüksége lesz ezt az információt a következő szakaszban.

Ezt követően [egy Azure-függvény létrehozása](../azure-functions/functions-create-first-azure-function.md) kérdezze le a kulcstároló naplóit a tárfiókon belül, és új események átvételéhez. Ez lesz az ütemezés szerint kiváltó függvényt.

Egy Azure-függvény létrehozása, válassza a **hozzon létre egy erőforrást**, keresése a piactéren _függvény App_, és kattintson a **létrehozása**. A létrehozás során egy meglévő üzemeltetési terv használja, vagy hozzon létre egy újat. Sikerült is választhat dinamikus üzemeltetéséhez. További részleteket a beállításokat tartalmazó függvény található [az Azure Functions méretezése](../azure-functions/functions-scale.md).

Az Azure-függvény létrehozása esetén keresse meg a fájlt, és válassza a időzítőt, függvény és C\#. Kattintson a **Ez a függvény létrehozása**.

![Az Azure Functions lépések panelen](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Az a **Develop** lapra, cserélje ki a run.csx kódot a következőre:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> Feltétlenül cserélje le a változók az előző kódban, hogy a tárfiók mutasson, amelyben a kulcstároló naplóit íródtak, a korábban létrehozott service bus és a megadott elérési útját a kulcstároló tárolási naplóit.
>
>

A függvény szerzi be a legújabb naplófájl a tárfiók a kulcstároló naplóit írt ahol, a fájl legújabb események grabs és leküldéses értesítések azokat a Service Bus-üzenetsorba. Mivel egyetlen fájl rendelkezhet több esemény, készítsen egy sync.txt fájlt, amely a függvény is ellenőrzi, hogy az kivételezett be a legutóbbi esemény időbélyege meghatározásához. Ez biztosítja, hogy nem leküldéses ugyanarra az eseményre több alkalommal. Ez a sync.txt fájl tartalmazza a legutóbbi észlelt esemény időbélyeg. A naplókat, ha be van töltve, a megfelelő sorrendben biztosításához időbélyeg alapján rendezni kell.

Ennél a függvénynél néhány további szalagtár szerepel, amely nem használható az Azure Functions mezőben kívüli jelenleg hivatkozik. Az ezen, igazolnia kell őket az Azure Functions NuGet segítségével. Válassza ki a **fájlok megtekintése** lehetőséget.

![Fájlok beállítás megtekintése](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

És adja hozzá a következő tartalommal project.json nevű fájlba:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Akkor **mentése**, az Azure Functions fogja letölteni a szükséges bináris fájlokat.

Váltás a **integráció** lapon és a időzítő paraméter adjon meg egy beszédes nevet a a függvényen belül. Az előző kód hívása időzítő vár *myTimer*. Adjon meg egy [CRON-kifejezés](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) az alábbiak szerint: 0 \* \* \* \* \* az időzítő, amely újraindítja a függvény percenként egyszer futtatásához.

Az azonos **integráció** lapon maradva adja hozzá a típusú bemeneti **Azure Blob Storage**. Ez a sync.txt fájlt, amely tartalmazza a tekintett meg, amelyet a függvény utolsó esemény időbélyegzője mutasson. Ez a paraméter neve függvényen belül elérhető lesz. Az előzőekben látható kód az Azure Blob Storage bemenetet vár a paraméternév megadásához, hogy *inputBlob*. Válassza ki a tárfiók, amelyben a sync.txt fájl lesznek tárolva (Ez lehet ugyanaz vagy egy másik tárolási fiókot). Az elérési út mezőben adja meg az elérési utat, ahol a formátum {container-name}/path/to/sync.txt a fájl él.

Adja hozzá egy kimeneti típusú *Azure Blob Storage* kimeneti. Ez a sync.txt fájl a bemeneti megadott mutasson. Ez használatos a függvény tekintett meg a legutóbbi esemény időbélyege írni. Az előzőekben látható kód várja a paraméter hívni *outputBlob*.

Ezen a ponton a függvény készen áll. Ügyeljen arra, hogy váltson vissza a **Develop** lapra, és mentse a kódot. A kimeneti ablakban, a fordítási hibákat, és ennek megfelelően javítsa ki azokat. Ha a kód lefordításához, majd a kódot kell most kell ellenőrzése a kulcstároló naplóit percenként és terjesztése a megadott Service Bus-üzenetsorba, új eseményeket. Minden alkalommal aktiválódik, a függvény a napló ablakban írja naplóinformációk kell megjelennie.

### <a name="azure-logic-app"></a>Az Azure Logic Apps alkalmazást
Ezután létre kell hoznia egy Azure logikai alkalmazás, amely szerzi be az eseményeket, hogy a funkció a Service Bus-üzenetsorba való küldését, elemzi a tartalom és elküld egy e-mailt az egyező feltétel alapján.

[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) címen **új > logikai alkalmazás**.

A logikai alkalmazás létrehozása után keresse meg a fájlt, és válassza a **szerkesztése**. A logic app szerkesztő választható **Service Bus-üzenetsorba** a várólista csatlakozni a Service Bus hitelesítő adataival.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Ezután válasszon **feltétel hozzáadása**. A feltételben a speciális szerkesztő váltson, és írja be a következő kódot, a tényleges APP_ID webalkalmazás APP_ID cseréje:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Ebben a kifejezésben lényegében adja vissza **hamis** Ha a *appid* a bejövő eseménytől (amely a Service Bus üzenet törzsét) nincs a *appid* az alkalmazás.

Ezután hozzon létre egy műveletet a **Ha nem, nem történik semmi**.

![Az Azure Logic App művelet kiválasztását.](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

A művelet kiválasztása **Office 365 – e-mail küldése**. Hozzon létre egy e-mailek küldése, ha a megadott feltétel visszaadja a mezők kitöltése **hamis**. Ha nem rendelkezik Office 365, sikerült megnézzük alternatívák ugyanaz az eredmény elérése érdekében.

Ezen a ponton rendelkezik egy teljes körű folyamatot, amely új kulcstartó naplók percenként egyszer keresi. Úgy találja, az új naplók az leküldi a service bus-üzenetsorba. A logikai alkalmazás lesz kiváltva, ha egy új üzenet a várólistában lévő fájljai. Ha a *appid* belül az esemény nem egyezik meg az alkalmazás Azonosítóját a hívó alkalmazás, egy e-mailt küld.
