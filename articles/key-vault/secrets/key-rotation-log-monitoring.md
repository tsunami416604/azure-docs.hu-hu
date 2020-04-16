---
title: Az Azure Key Vault beállítása végpontok közötti kulcsrotációval és naplózással | Microsoft dokumentumok
description: Ezzel az útmutatóval segítséget nyújt a kulcselforgatás beállításához és a kulcstartó naplóinak figyeléséhez.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430252"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Az Azure Key Vault beállítása kulcsrotációval és naplózással

## <a name="introduction"></a>Introduction (Bevezetés)

Miután rendelkezik egy key vault, megkezdheti a kulcsok és titkos kulcsok tárolására. Az alkalmazások nak már nem kell megtartania a kulcsokat vagy a titkos kulcsokat, de szükség szerint kérheti őket a tárolóból. A kulcstartó lehetővé teszi a kulcsok és a titkos kulcsok frissítését anélkül, hogy befolyásolná az alkalmazás viselkedését, amely a kulcs- és titkos kezelés lehetőségeinek széles tárkait nyitja meg.

Ez a cikk bemutatja, hogyan valósíthatja meg a tárfiók kulcsok ütemezett rotációját, figyelheti a key vault naplózási naplókat, és riasztást a váratlan kérelmek esetén. 

Először létre kell hoznia egy key vaultot az Ön által választott módszerrel:

- [Az Azure Key Vault titkos adatainak beállítása és lekérése az Azure CLI használatával](quick-create-cli.md)
- [Az Azure Key Vault titkos adatainak beállítása és lekérése az Azure PowerShell használatával](quick-create-powershell.md)
- [Titkos kulcs beállítása és lekérése az Azure Key Vaultból az Azure Portal használatával](quick-create-portal.md)


## <a name="store-a-secret"></a>Titkos tárolása

Ahhoz, hogy egy alkalmazás lekérje a titkos kulcsot a Key Vaultból, először létre kell hoznia a titkos kulcsot, és fel kell töltenie azt a tárolóba.

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókjához megadott felhasználónevet és jelszót. A PowerShell leteszi a fiókhoz társított összes előfizetést. A PowerShell alapértelmezés szerint az elsőt használja.

Ha több előfizetéssel rendelkezik, előfordulhat, hogy meg kell adnia a key vault létrehozásához használt. A fiók előfizetései megtekintéséhez adja meg az alábbiakat:

```powershell
Get-AzSubscription
```

A naplózni kívánt kulcstartóhoz társított előfizetés megadásához írja be a következőt:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Mivel ez a cikk titkos kulcsként bemutatja a tárfiók kulcsának tárolását, be kell szereznie a tárfiók kulcsát.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

A titkos kulcs (ebben az esetben a tárfiók kulcsa) beolvasása után a kulcsot biztonságos karakterláncba kell konvertálnia, majd létre kell hoznia egy titkos kulcsot ezzel az értékkel a kulcstartóban.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ezután az URI-t a létrehozott titkos kulcsot. Szüksége lesz erre az URI-ra egy későbbi lépésben a key vault hívásához és a titkos kulcs beolvasásához. Futtassa a következő PowerShell-parancsot, és jegyezze fel az azonosító értéket, amely a titkos titok URI-ja:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Az alkalmazás beállítása

Most, hogy egy titkos tárolt, használhatja a kódot letölteni és használni, miután néhány további lépést.

Először regisztrálnia kell az alkalmazást az Azure Active Directoryval. Ezután mondja el a Key Vault nak az alkalmazás adatait, hogy engedélyezze az alkalmazásból érkező kérelmeket.

> [!NOTE]
> Az alkalmazást ugyanazon az Azure Active Directory-bérlőn kell létrehozni, mint a key vault.

1. Nyissa meg **az Azure Active Directoryt**.
2. Válassza **az Alkalmazásregisztrációk lehetőséget.** 
3. Válassza **az Új alkalmazás regisztráció az** Azure Active Directoryhoz való hozzáadásához válassza az új alkalmazás regisztrációját.

    ![Alkalmazások megnyitása az Azure Active Directoryban](../media/keyvault-keyrotation/azure-ad-application.png)

4. A **Létrehozás csoportban**hagyja az alkalmazás típusát **webalkalmazásként / API-ként,** és adjon nevet az alkalmazásnak. Adjon meg az alkalmazásnak egy **bejelentkezési URL-címet.** Ez az URL bármi lehet, amit akarsz ehhez a demóhoz.

    ![Alkalmazásregisztráció létrehozása](../media/keyvault-keyrotation/create-app.png)

5. Miután az alkalmazást hozzáadta az Azure Active Directoryhoz, megnyílik az alkalmazáslap. Válassza a **Beállítások**lehetőséget, majd a **Tulajdonságok**lehetőséget. Másolja az **alkalmazásazonosító** értékét. Szüksége lesz rá a későbbi lépésekben.

Ezután hozzon létre egy kulcsot az alkalmazáshoz, hogy kommunikálhassa az Azure Active Directoryt. Kulcs létrehozásához válassza a **Billentyűk** lehetőséget a **Beállítások csoportban.** Jegyezze fel az Azure Active Directory-alkalmazás újonnan létrehozott kulcsát. Egy későbbi lépésben szüksége lesz rá. A kulcs nem lesz elérhető, miután elhagyta ezt a szakaszt. 

![Az Azure Active Directory alkalmazáskulcsai](../media/keyvault-keyrotation/create-key.png)

Mielőtt bármilyen hívást hozna létre az alkalmazásból a key vaultba, meg kell mondania a key vault az alkalmazás és az engedélyek. A következő parancs a tároló nevét és az Azure Active Directory-alkalmazásalkalmazásbeli alkalmazásazonosítót használja az alkalmazás **hozzáférésének** biztosításához a key vaulthoz.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Most már készen áll az alkalmazáshívások létrehozásának megkezdésére. Az alkalmazásban telepítenie kell a NuGet-csomagokat, amelyek szükségesek az Azure Key Vault és az Azure Active Directory együttműködéséhez. A Visual Studio Package Manager konzolján adja meg a következő parancsokat. A cikk írásában az Azure Active Directory-csomag jelenlegi verziója 3.10.30.305231913, így erősítse meg a legújabb verziót, és szükség szerint frissítse.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Az alkalmazáskódban hozzon létre egy osztályt az Azure Active Directory-hitelesítés metódusának tárolására. Ebben a példában, hogy az osztály neve **Utils**. Adja hozzá `using` a következő utasítást:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ezután adja hozzá a következő módszert a JWT-jogkivonat azure-beli Active Directoryból való lekéréséhez. A karbantarthatóság érdekében érdemes lehet áthelyezni a kódolt karakterlánc-értékeket a web- vagy alkalmazáskonfigurációba.

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

Adja hozzá a kulcstároló hívásához szükséges kódot, és kérje le a titkos értéket. Először a következő `using` utasítást kell hozzáadnia:

```csharp
using Microsoft.Azure.KeyVault;
```

Adja hozzá a metódushívások at a Key Vault meghívásához és a titkos kulcs lekéréséhez. Ebben a módszerben adja meg a titkos URI,amely egy előző lépésben mentett. Vegye figyelembe a **GetToken** metódus használatát a korábban létrehozott **Utils** osztályból.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Az alkalmazás futtatásakor most már hitelesítse az Azure Active Directoryt, majd a titkos érték beolvasása az Azure Key Vaultból.

## <a name="key-rotation-using-azure-automation"></a>Kulcsrotáció az Azure Automation használatával

> [!IMPORTANT]
> Az Azure Automation runbookok `AzureRM` továbbra is megkövetelik a modul használatát.

Most már készen áll egy rotációs stratégia beállítására a Key Vault-titokként tárolt értékekhez. A titkok többféleképpen forgathatók:

- A manuális folyamat részeként
- Programozott módon API-hívások használatával
- Egy Azure Automation-parancsfájlon keresztül

Ebben a cikkben a PowerShell és az Azure Automation használatával módosíthatja az Azure storage-fiók hozzáférési kulcsát. Ezután frissíteni fogja a kulcstartó titkos kulcsát az új kulccsal.

Ahhoz, hogy az Azure Automation titkos értékeket állíthasson be a key vaultban, be kell szereznie az **AzureRunAsConnection**nevű kapcsolat ügyfélazonosítóját. Ez a kapcsolat az Azure Automation-példány létrehozásakor jött létre. Az azonosító megkereséséhez válassza **az Eszközök** lehetőséget az Azure Automation-példányból. Itt válassza a **Kapcsolatok**lehetőséget, majd válassza ki az **AzureRunAsConnection** egyszerű szolgáltatást. Jegyezze fel az **ApplicationId** értéket.

![Azure Automation-ügyfélazonosító](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Az **Eszközök**alkalmazásban válassza a **Modulok**lehetőséget. Válassza **a Galéria**lehetőséget, majd keresse meg és importálja az alábbi modulok frissített verzióit:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> A cikk írásakor csak a korábban említett modulokat kellett frissíteni a következő parancsfájlhoz. Ha az automatizálási feladat sikertelen, ellenőrizze, hogy importálta-e az összes szükséges modult és azok függőségeit.

Miután lekérte az azure automation-kapcsolat alkalmazásazonosítóját, meg kell mondania a key vaultnak, hogy az alkalmazás rendelkezik engedéllyel a trezor ban a titkos kulcsok frissítésére. Használja a következő PowerShell parancsot:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Ezután válassza a **Runbookok** lehetőséget az Azure Automation-példány alatt, majd a **Runbook hozzáadása**lehetőséget. Kattintson a **Gyors létrehozás** gombra. Nevezze el a runbookot, és válassza a **PowerShell** a runbook típusát. Leírást adhat hozzá. Végül válassza a **Létrehozás gombot.**

![Runbook létrehozása](../media/keyvault-keyrotation/Create_Runbook.png)

Illessze be a következő PowerShell-parancsfájlt az új runbook szerkesztőablakába:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
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

# Optionally you can set the following as parameters
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

A szerkesztő ablaktáblán válassza a **Teszt ablaktábla** lehetőséget a parancsfájl teszteléséhez. Miután a parancsfájl hiba nélkül fut, **kiválaszthatja a Közzététel**lehetőséget, majd a runbook konfigurációs ablaktáblájában alkalmazhatja a runbook ütemezését.

## <a name="key-vault-auditing-pipeline"></a>Key Vault naplózási folyamat

Amikor beállít egy key vault, bekapcsolhatja a naplózási naplók gyűjtése a key vault hozzáférési kérelmek. Ezek a naplók egy kijelölt Azure-tárfiókban tárolódnak, és kihúzhatók, figyelhetők és elemezhetők. A következő forgatókönyv az Azure-függvények, az Azure-logikai alkalmazások és a kulcs-vault naplózási naplók létrehozása egy folyamat, amely e-mailt küld, ha egy alkalmazás, amely nem felel meg a webalkalmazás alkalmazásazonosítója lekéri a trezorból titkokat.

Először engedélyeznie kell a bejelentkezést a key vault. Használja a következő PowerShell-parancsokat. (A [kulcstartó naplózásáról szóló cikkben](../general/logging.md)található részletek láthatók.)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A naplózás engedélyezése után a naplónaplók a kijelölt tárfiókban tárolódnak. Ezek a naplók eseményeket tartalmaznak arról, hogyan és mikor érhetők el a kulcstartók, és ki által.

> [!NOTE]
> A naplózási adatok at 10 perccel a key vault-művelet után érheti el. Ez gyakran elérhető hamarabb, mint ezt.

A következő lépés [egy Azure Service Bus-várólista létrehozása.](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) Ez a várólista, ahol a kulcstartó naplózási naplók leküldéses. Amikor a naplóüzenetek a várólistában vannak, a logikai alkalmazás felveszi őket, és fellép rajtuk. Hozzon létre egy Service Bus-példányt a következő lépésekkel:

1. Hozzon létre egy Service Bus-névteret (ha már rendelkezik használni kívánt névtérrel, ugorjon a 2. lépésre).
2. Tallózással keresse meg a Service Bus-példányt az Azure Portalon, és válassza ki a várólistát létrehozni kívánt névteret.
3. Válassza **az Erőforrás** > **létrehozása vállalati integrációs** > **szolgáltatásbusz**lehetőséget, majd adja meg a szükséges adatokat.
4. Keresse meg a Service Bus kapcsolat adatait a névtér kiválasztásával, majd a **Kapcsolatadatai**lehetőségkiválasztásával. Erre az információra a következő szakaszhoz szüksége lesz.

Ezután [hozzon létre egy Azure-függvényt](../../azure-functions/functions-create-first-azure-function.md) a tárfiókon belüli key vault-naplók lekérdezéséhez és az új események felvételéhez. Ez a funkció ütemezés szerint aktiválódik.

Azure-függvényalkalmazás létrehozásához válassza **az Erőforrás létrehozása**lehetőséget, keresse meg a piactéren a **Függvényalkalmazást,** majd válassza a **Létrehozás lehetőséget.** A létrehozás során használhat egy meglévő tárhelycsomagot, vagy létrehozhat egy újat. Azt is választhatja a dinamikus tárhely. Az Azure Functions üzemeltetési beállításairól az [Azure Functions méretezése](../../azure-functions/functions-scale.md)című témakörben talál további információt.

Az Azure függvényalkalmazás létrehozása után nyissa meg, és válassza ki az **időzítő** forgatókönyvet és a **C\# ** a nyelvet. Ezután válassza **a Funkció létrehozása**lehetőséget.

![Az Azure Functions Start panelje](../media/keyvault-keyrotation/Azure_Functions_Start.png)

A **Fejlesztés** lapon cserélje le a run.csx kódot a következőre:

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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

            //Required to order by time as they might not be in the file
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
> Módosítsa az előző kódban lévő változókat úgy, hogy a tárfiókra mutasson, ahol a key vault naplók vannak írva, a korábban létrehozott Service Bus-példányra és a key-vault storage-naplók adott elérési útvonalára.

A függvény felveszi a legújabb naplófájlt a tárfiókból, ahol a key vault naplók vannak írva, megragadja a legújabb eseményeket, hogy a fájlból, és leküldéses őket egy Service Bus-várólistába. 

Mivel egyetlen fájlnak több eseménye is lehet, hozzon létre egy sync.txt fájlt, amelyet a függvény is megnéz az utoljára felvett esemény időbélyegzőjének meghatározásához. A fájl használata biztosítja, hogy ne nyomja meg ugyanazt az eseményt többször. 

A sync.txt fájl az utoljára észlelt esemény időbélyegzőjét tartalmazza. A naplók betöltésekor az időbélyegzőik alapján kell rendezni őket, hogy megbizonyosodjanak a megfelelő sorrendről.

Ehhez a funkcióhoz hivatkozunk egy pár további könyvtárak, amelyek nem érhetők el a dobozon kívül az Azure Functions. Ahhoz, hogy ezeket a könyvtárakat, szükségünk van az Azure Functions lekéri őket a NuGet használatával. A **Kód** mezőben válassza a **Fájlok megtekintése**lehetőséget.

!["Fájlok megtekintése" beállítás](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Project.json nevű fájl hozzáadása a következő tartalommal:

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

Miután **kiválasztotta**a Mentés lehetőséget, az Azure Functions letölti a szükséges bináris fájlokat.

Váltson az **Integráció** lapra, és adjon értelmes nevet az időzítő paraméternek a függvényen belül. Az előző kódban a függvény elvárja, hogy az időzítőt *myTimer-nek*hívják. Az [időzítőcron-kifejezésének](../../app-service/webjobs-create.md#CreateScheduledCRON) megadása a `0 * * * * *`következőképpen: . Ez a kifejezés hatására a függvény percenként egyszer fut.

Ugyanazon az **Integráció** lapon adjon hozzá egy **Azure Blob storage**típusú bemenetet. Ez a bemenet a sync.txt fájlra mutat, amely a függvény által megtekintett utolsó esemény időbélyegzőjét tartalmazza. Ez a bemenet a függvényen belül érhető el a paraméter név használatával. Az előző kódban az Azure Blob storage bemeneti elvárja, hogy a paraméter neve *inputBlob*lesz. Válassza ki azt a tárfiókot, ahol a sync.txt fájl található (lehet ugyanaz vagy egy másik tárfiók). A görbemezőben adja meg a fájl `{container-name}/path/to/sync.txt`elérési útját formátumban.

**Azure Blob storage**típusú kimenet hozzáadása. Ez a kimenet a bemenetben megadott sync.txt fájlra mutat. Ezt a kimenetet a függvény az utolsó megtekintett esemény időbélyegzőjének írására használja. Az előző kód azt várja, hogy ezt a paramétert *outputBlob-nak*hívják.

A funkció készen áll. Ügyeljen arra, hogy visszaváltson a **Fejlesztés** lapra, és mentse a kódot. Ellenőrizze a kimeneti ablakban az esetleges fordítási hibákat, és szükség szerint javítsa ki azokat. Ha a kód lefordítja, majd a kódot kell ellenőriznia a key vault naplók percenként, és leadott új eseményeket a megadott Service Bus-várólistába. A naplózási adatokat minden alkalommal, amikor a függvény aktiválódik, a naplóablakba kell írnia a naplózási adatokat.

### <a name="azure-logic-app"></a>Azure-logikai alkalmazás

Ezután létre kell hoznia egy Azure-logikai alkalmazást, amely felveszi az eseményeket, amelyek a függvény leküldéses a Service Bus-várólistába, elemzi a tartalmat, és küld egy e-mailt egy feltétel alapján egyezés.

[Logikai alkalmazás létrehozása](../../logic-apps/quickstart-create-first-logic-app-workflow.md) az**Erőforrás-integrációs** >  **Create a resource** > **logikai alkalmazás**létrehozása lehetőséget választva.

A logikai alkalmazás létrehozása után nyissa meg, és válassza **a Szerkesztés**lehetőséget. A logikai alkalmazás szerkesztőjében válassza a **Service Bus-várólista** lehetőséget, és adja meg a Service Bus hitelesítő adatait a várólistához való csatlakoztatáshoz.

![Azure Logic App Service Bus](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Válassza **a Feltétel hozzáadása**lehetőséget. Ebben az állapotban váltson a speciális szerkesztőre, és írja be a következő kódot. Cserélje le *APP_ID* a webalkalmazás tényleges alkalmazásazonosítójára:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Ez a kifejezés lényegében **hamis** értéket ad vissza, ha a bejövő eseményből származó *alkalmazás (amely* a Service Bus-üzenet törzse) nem az alkalmazás *appidja.*

Most hozzon létre egy **műveletet, ha nem, nem csinál semmit**.

![Az Azure Logic Apps válaszd a műveletet](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

A művelethez válassza az **Office 365 – e-mail küldése**lehetőséget. Töltse ki a mezőket, hogy e-mailt hozzon létre, ha a megadott feltétel **hamis**értéket ad vissza. Ha nem rendelkezik Office 365-tel, keressen alternatívákat ugyanannak az eredménynek a eléréséhez.

Most már rendelkezik egy végpontok között futó folyamat, amely megkeresi az új key-vault naplózási naplók percenként egyszer. Leküldéses új naplók at talál egy Service Bus-várólistába. A logikai alkalmazás akkor aktiválódik, amikor egy új üzenet landol a várólistában. Ha az eseményen belüli *alkalmazásnem* egyezik meg a hívó alkalmazás azonosítójával, e-mailt küld.
