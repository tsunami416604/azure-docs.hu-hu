---
title: Az Azure Key Vault beállítása végpontok közötti kulcsforgatással és auditálással |} A Microsoft Docs
description: Ez az útmutató segítségével állítsa be a kulcsforgatás és figyelheti a key vault-naplók segítségével.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 68fd33dc3e9def11f72b7aec14f83f86b8bb74d0
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749706"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Állítsa be az Azure Key Vault kulcsforgatással és vizsgálattal

## <a name="introduction"></a>Bevezetés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Miután egy kulcstartót, elkezdheti a kulcsok és titkos kulcsok tárolására használja. Az alkalmazások többé nem kell megőrizni a kulcsok vagy titkos adatait, de lehetőség igényelni azokat a tárolóból igény szerint. Key vault lehetővé teszi a kulcsok és titkos kódok frissítése az alkalmazás, amely nyit meg, hogy a kulcs és titkos kódok kezelése szánt viselkedésének módosítása nélkül.

>[!IMPORTANT]
> Ebben a cikkben szereplő példák csak illusztrációs célokat szolgálnak. Ezek nem szolgálnak, éles környezetben való használatra. 

Ez a cikk ismerteti:

- Titkos kulcs tárolása az Azure Key Vault használatával egy példát. Ez a cikk a titkos kulcs tárolása egy olyan alkalmazás az Azure storage-fiókkulcs. 
- Hogyan lehet megvalósítani egy ütemezett elforgatási szögét a tárfiók kulcsára.
- A kulcs figyelése auditnaplók tárolóját, és riasztást, ha váratlan kérelmeket.

> [!NOTE]
> Ez a cikk nem részletesen elmagyarázza, a kezdeti beállítás a key vault. Ez az információ: [Mi az Azure Key Vault?](key-vault-overview.md). Platformfüggetlen parancssori felületre vonatkozó utasításokat, lásd: [kezelése a Key Vault az Azure CLI-vel](key-vault-manage-with-cli2.md).

## <a name="set-up-key-vault"></a>A Key Vault beállítása

Ahhoz, hogy egy alkalmazás titkos Key vaultból, először kell létrehozni a titkos kulcsot és töltse fel azt a tárolót.

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg a felhasználónevet és jelszót az Azure-fiókjával. PowerShell ehhez a fiókhoz társított összes előfizetés fog kapni. PowerShell alapértelmezés szerint az első utótagcímkéjét használja.

Ha több előfizetéssel rendelkezik, akkor előfordulhat, hogy adja meg azt, amelyik a kulcstároló létrehozásához használt. Adja meg a fiókhoz tartozó előfizetések megtekintéséhez a következő:

```powershell
Get-AzSubscription
```

Ha az előfizetést, amelyhez a key vault fogja naplózás van társítva, adja meg:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Ez a cikk bemutatja a tárfiókkulcs tárolására, egy titkos kulcsot, mert be kell szereznie a tárfiók kulcsára.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Után (ami jelen esetben a tárfiók kulcsának) a titkos kód beolvasása, a kulcs átalakítása egy biztonságos karakterláncot, és majd létrehoz egy titkos kulcsot, ezt az értéket a key vaultban lévő.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ezután szerezze be az URI-t a titkos kulcsot hozott létre. Ez az URI egy későbbi lépésben meghívása a key vaultban, és a titkos kód beolvasása lesz szüksége. Futtassa a következő PowerShell-parancsot, és jegyezze fel az azonosító értékét, amely a titkos kulcs URI:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Az alkalmazás beállítása

Most, hogy már tárolt titkos kulcs, használhatja a kód beolvasásához és néhány további lépések végrehajtása után használhatja.

Először regisztrálnia kell az alkalmazást az Azure Active Directoryval. Ezután ossza meg a Key Vault az alkalmazással kapcsolatos információk, hogy az alkalmazás érkező kéréseket tesz lehetővé.

> [!NOTE]
> Az alkalmazás Azure Active Directory bérlőnél a key vaultban, létre kell hozni.

1. Nyissa meg **az Azure Active Directory**.
2. Válassza az **Alkalmazásregisztrációk** elemet. 
3. Válassza ki **új alkalmazásregisztráció** egy Azure Active Directory-alkalmazás hozzáadása.

    ![Nyissa meg az alkalmazások az Azure Active Directoryban](./media/keyvault-keyrotation/azure-ad-application.png)

4. A **létrehozás**, hagyja meg az alkalmazás típusát, **webalkalmazás / API** és nevezze el az alkalmazást. Adja meg az alkalmazás egy **bejelentkezési URL-**. Az URL-címet a demóba bármit is lehet.

    ![Hozzon létre az alkalmazás regisztrálása](./media/keyvault-keyrotation/create-app.png)

5. Az alkalmazás Azure Active Directoryhoz való hozzáadása után megnyílik az alkalmazás oldalán. Válassza ki **beállítások**, majd válassza ki **tulajdonságok**. Másolás a **Alkalmazásazonosító** értéket. A későbbi lépésekben szüksége lehet.

Ezután akkor hozzon létre egy kulcsot az alkalmazáshoz, így azt használhatja az Azure Active Directoryval. A kulcs létrehozásához válassza **kulcsok** alatt **beállítások**. Jegyezze fel az Azure Active Directory-alkalmazás az újonnan létrehozott kulcsot. Egy későbbi lépésben szüksége lesz rá. Ebben a szakaszban hagyja követően a kulcs nem lesznek elérhetők. 

![Az Azure Active Directory-alkalmazás kulcsok](./media/keyvault-keyrotation/create-key.png)

A key vaultban az alkalmazás a beléptetés-visszavonásokat kapcsolatot hoz létre, mielőtt az alkalmazás és annak engedélyeivel kapcsolatos mondja el a key vaultban. A következő parancsot a tároló nevére és használja az Azure Active Directory-alkalmazás Alkalmazásazonosítója adja meg az alkalmazás **első** a key vaulthoz való hozzáférés.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Most már készen áll az alkalmazás hívások kiépítésének megkezdésére. Az alkalmazásban telepítenie kell a NuGet-csomagok az Azure Key Vault és az Azure Active Directory használata szükséges. A Visual Studio Csomagkezelő konzolról adja meg a következő parancsokat. A jelen cikk írásakor az az Azure Active Directory-csomag aktuális verziója 3.10.305231913, ezért a legújabb verzióra, és igény szerint.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Az alkalmazás kódjában hozzon létre egy osztályt, amely tárolja a az Azure Active Directory hitelesítési módszert. Ebben a példában az adott osztály neve **Utils**. Adja hozzá a következő `using` utasítást:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ezután adja hozzá a következő metódust a JWT jogkivonat lekérése az Azure Active Directoryból. Karbantarthatóság érdemes a kódolt karakterlánc-értékek helyezhetik át a webhely vagy alkalmazás konfigurációja.

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

Adja hozzá a Key Vault hívja meg és a titkos kód értékét a vizualizációhoz szükséges kódot. Először hozzá kell adnia a következő `using` utasítást:

```csharp
using Microsoft.Azure.KeyVault;
```

Adja hozzá a metódushívásokat indítja el a Key Vaultban, és a titkos kód beolvasása. Ezen módszer esetében adja meg a titkos kulcsot, amelyet az előző lépésben mentett URI. Vegye figyelembe a használatát a **GetToken** módszerrel a **Utils** korábban létrehozott osztályt.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Az alkalmazás futtatásakor kell hitelesítése az Azure Active Directoryhoz, és ezután a titkos érték lekérése az Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>A kulcsforgatás Azure Automation használatával

Most már készen áll, állítsa be a Key Vault titkos kódként tárolt értékek Elforgatás stratégiáját. Titkos kódok többféle módon is rotációja:

- Manuális folyamat részeként
- Programozott módon az API-hívás használatával
- Egy Azure Automation-szkript használatával

Ez a cikk az alkalmazásában együtt az Azure Automation PowerShell módosíthatja egy Azure storage-fiók hozzáférési kulcsot fog használni. Ezzel a kulccsal új kulcstartó titkos majd frissíteni fogja.

Ahhoz, hogy az Azure Automation a key vaultban lévő titkos értékeinek beállításához, be kell szereznie az ügyfél-azonosító nevű kapcsolat **AzureRunAsConnection**. Ezt a kapcsolatot az Azure Automation-példány létrejöttekor lett létrehozva. Ez Azonosítójának megkereséséhez válassza ki a **eszközök** az Azure Automation-példányból. Itt válassza ki a **kapcsolatok**, majd válassza ki a **AzureRunAsConnection** egyszerű szolgáltatást. Jegyezze fel a **ApplicationId** értéket.

![Az Azure Automation ügyfél-azonosító](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

A **eszközök**válassza **modulok**. Válassza ki **katalógus**, és keressen rá, és importálja a frissített verzióit a következő modulok mindegyikének:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> A jelen cikk írásakor az csak a korábban feljegyzett modulok frissíteni kell a következő parancsprogram szükséges. Ha az automation-feladat meghiúsul, győződjön meg arról, hogy az összes szükséges modulokat és azok függőségeit importálása.

Miután az Azure Automation-kapcsolat az alkalmazás azonosítója már lekért, utasítsa a key vault arról, hogy az alkalmazás jogosultsága a tároló titkos kulcsainak frissítése. Használja a következő PowerShell-parancsot:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Majd **Runbookok** az Azure Automation-példányt, és válassza ki a **Runbook hozzáadása**. Kattintson a **Gyors létrehozás** gombra. A runbook neve, és válassza ki **PowerShell** a runbook típusaként. Hozzáadhat egy leírást. Végül válassza **létrehozás**.

![Runbook létrehozása](./media/keyvault-keyrotation/Create_Runbook.png)

A Lekérdezésszerkesztő panelén az új runbook illessze be a következő PowerShell-parancsfájlt:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzAccount `
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
New-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

A Lekérdezésszerkesztő panelén válassza **teszt panel** tesztelni a parancsfájlt. Miután a parancsfájl hiba nélkül fut, kijelölheti **közzététel**, és a runbook a runbook konfiguráció panelen ütemezés alkalmazhatja.

## <a name="key-vault-auditing-pipeline"></a>A Key Vault naplózási folyamat

Ha beállította a key vault, a naplók összegyűjtése a kulcstartó hozzáférési kérelmek naplózását kapcsolhatja. Ezek a naplók a kijelölt Azure storage-fiókban vannak tárolva, és figyeli és elemzi, lekérhetik. Az alábbi forgatókönyv az Azure functions, az Azure logic apps és a kulcstartó naplók használatával hozzon létre egy folyamatot, amely e-mailt küld, amikor egy alkalmazást, amely nem felel meg a WebApp Alkalmazásazonosítója lekéri a titkos kulcsok a tárolóból.

Először engedélyeznie kell a key vault naplózásának. Használja a következő PowerShell-parancsokat. (Láthatja, hogy a teljes körű információkat a [Ez a cikk a key vault-naplózás kapcsolatos](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Naplózás engedélyezése után auditnaplók indítsa el a kijelölt tárfiókban tárolja. Ezek a naplók tartalmaz eseményeket hogyan és mikor érhetők el a kulcstartók, és ki használja őket.

> [!NOTE]
> Elérheti a naplóinformációkat 10 perc után a kulcstartó műveletet. Gyakran lesz elérhető, mint korábban.

A következő lépés [hozzon létre egy Azure Service Bus-üzenetsor](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Ennek az üzenetsornak, ahol a kulcstartó auditnaplók leküld. Ha a napló üzenetek az üzenetsorban, a logikai alkalmazás felveszi őket, és kezelje őket. Hozzon létre egy Service Bus-példány a következő lépéseket:

1. Hozzon létre a Service Bus-névtér (Ha már rendelkezik ilyennel, amelyet szeretne használni, ugorjon a 2. lépés).
2. Keresse meg a Service Bus-példányt az Azure Portalon, és válassza ki a névteret szeretne létrehozni az üzenetsort.
3. Válassza ki **erőforrás létrehozása** > **vállalati integráció** > **a Service Bus**, majd adja meg a szükséges adatokat.
4. Jelölje ki a névteret, és válassza a Service Bus-kapcsolati adatok keresése **kapcsolatadatok**. Ez a szakasz a kell ezt az információt.

Ezután [hozzon létre egy Azure-függvényt](../azure-functions/functions-create-first-azure-function.md) lekérdezik a kulcstároló naplóit a tárfiókban található, és új szerepeljenek. Ez a függvény egy ütemezés szerint aktiválódik.

Azure-függvényalkalmazás létrehozásához válassza **erőforrás létrehozása**, keressen a piactéren **Függvényalkalmazás**, majd válassza ki **létrehozás**. A létrehozás során használja egy meglévő szolgáltatási csomag, vagy hozzon létre egy újat. Kérheti a dinamikus üzemeltető is. Az Azure Functions szolgáltatási beállításokkal kapcsolatos további információkért lásd: [az Azure Functions méretezése](../azure-functions/functions-scale.md).

Az Azure-függvényalkalmazás létrehozása után nyissa meg azt, és válassza a **időzítő** forgatókönyv és **C\#**  a nyelvhez. Válassza ki **függvény létrehozása**.

![Az Azure Functions indítása panel](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Az a **Develop** fülre, cserélje le a run.csx kódot az alábbira:

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
> Módosítsa a változókat a fenti kóddal, mutasson a tárfiókhoz, ahol a kulcstartónaplók írták, a korábban létrehozott Service Bus-példányhoz és a megadott elérési útját a kulcstartó storage-naplók.

A függvény szerzi be a legújabb naplófájlban a tárfiókból ahol a kulcstartónaplók készültek, begyűjti a fájl a legújabb események és leküldi azokat egy Service Bus-üzenetsorba. 

Egyetlen fájl több esemény is rendelkezik, mert hozzunk létre egy sync.txt fájlt, amely a függvény is kivételezett fel a legutóbbi esemény időbélyege meghatározásához. Ezzel a fájllal biztosítja, hogy ne leküldi ugyanahhoz az eseményhez több alkalommal. 

A sync.txt fájl tartalmazza a legutóbbi észlelt esemény időbélyeg. A naplók betöltésekor azok rendezett, győződjön meg arról, hogy azok már megrendelt megfelelően az időbélyegek alapján.

Ennél a függvénynél néhány további kódtárak pedig, amelyek nem érhetők el az Azure Functions beépített hivatkozik. Ezek a könyvtárak tartalmazzák, az Azure Functions szolgáltatást, kérje le őket NuGet használatával kell. Alatt a **kód** jelölje ki **fájlok megtekintése**.

!["A fájlok megtekintése" lehetőség](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adja hozzá az alábbi tartalommal project.json nevű fájlba:

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

Miután kiválasztotta **mentése**, az Azure Functions a szükséges bináris fájlokat tölti le.

Váltson a **integráció** lapra, és nevezze el az időzítő paraméter jelentéssel bíró belül a funkció használatához. A fenti kóddal, a függvény azt várja az időzítő, která bude volána *myTimer*. Adjon meg egy [CRON-kifejezés](../app-service/webjobs-create.md#CreateScheduledCRON) az időzítő az alábbiak szerint: `0 * * * * *`. Ez a kifejezés miatt a függvényt, hogy percenként egyszer futtatni.

Az azonos **integráció** lapon maradva adja hozzá a típusú bemenetet **Azure Blob storage**. A bemeneti tekintett meg, a függvény által a legutóbbi esemény időbélyege tartalmazó sync.txt fájlt fog mutatni. A bemeneti paraméter nevének megadásával fogják elérni a függvényen belül. A fenti kóddal, az az Azure Blob storage bemenetet vár a paraméternév megadásához, hogy *inputBlob*. Válassza ki a tárfiókot, ahol a sync.txt fájlban található (Ez lehet ugyanaz vagy egy másik tárfiók). Az elérési út mezőben adja meg a következő formátumban a fájl elérési útja `{container-name}/path/to/sync.txt`.

Adja hozzá a típusú kimenet **Azure Blob storage**. Ez a kimenet a sync.txt fájlt a bemeneti adatok meghatározott fog mutatni. Ez a kimenet írása tekintett meg a legutóbbi esemény időbélyege használják a függvényt. A fenti kód vár ezt a paramétert, která bude volána *outputBlob*.

A függvény készen áll. Ügyeljen arra, hogy váltson vissza a **Develop** lapra, és mentse a kódot. Ellenőrizze a kimeneti ablakban, a fordítási hibákat, és szükség esetén javítsa ki őket. A kód lefordításához, ha majd a kódot kell most már lehet a key vault naplóinak percenként ellenőrzi és leküldése új eseményeket a meghatározott a Service Bus-üzenetsorba. Minden alkalommal aktiválódik, a függvény a napló ablak írja naplózási információkat kell megjelennie.

### <a name="azure-logic-app"></a>Az Azure logic app

Következő lépésként létre kell hoznia, amely fogadja az eseményeket, hogy a függvény van próbálna leküldeni a Service Bus-üzenetsorba, a tartalom elemzi és küld egy e-mailt az egyező feltétel alapján az Azure logic app.

[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) kiválasztásával **erőforrás létrehozása** > **integrációs** > **logikai alkalmazás**.

A logikai alkalmazás létrehozása után nyissa meg azt, és válassza **szerkesztése**. A logic app-szerkesztőben válassza **Service Bus-üzenetsor** , és adja meg a várólista csatlakozni a Service Bus hitelesítő adatait.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Válassza ki **feltétel hozzáadása**. A feltétel a speciális szerkesztő váltson, és adja meg a következő kódot. Cserélje le *APP_ID* a webalkalmazás tényleges azonosítót:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Ez a kifejezés lényegében adja vissza **hamis** Ha a *appid* a beérkező eseményben (amely a Service Bus-üzenet törzsét) a nem a *appid* az alkalmazás.

Most hozzon létre egy művelet alatt **Ha nem, nincs művelet**.

![Az Azure Logic Apps művelet kiválasztása](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

A művelet kiválasztása **Office 365 – e-mail küldése**. Töltse ki a mezőket hozhat létre egy e-mailt küldjön, ha adja vissza a meghatározott feltétel **hamis**. Ha nem rendelkezik Office 365-höz, kereshet alternatívákat az azonos eredmények elérése érdekében.

Most már rendelkezik egy teljes körű folyamatot, amely az új kulcstartó auditnaplókhoz percenként egyszer. Service Bus-üzenetsorba, leküldéses megtalálja az új naplók. A logikai alkalmazás akkor aktiválódik, ha új üzenet a várólistában lévő hajtanak végre. Ha a *appid* belül az esemény a hívó alkalmazás Alkalmazásazonosítója nem egyezik, az e-mailt küld.
