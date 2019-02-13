---
title: Az Azure Key Vault beállítása végpontok közötti kulcsforgatással és auditálás – Azure Key Vault |} A Microsoft Docs
description: Ez az útmutató segítségével kulcsforgatással és figyelési a key vault-naplók beállításához.
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
ms.openlocfilehash: deb50a71b179c3cb03d5da22e336c42b26fe0bfa
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106120"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Állítsa be az Azure Key Vault kulcsforgatással és vizsgálattal

## <a name="introduction"></a>Bevezetés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Miután egy kulcstartót, elkezdheti a kulcsok és titkos kulcsok tárolására használja. Az alkalmazások többé nem kell megőrizni a kulcsok vagy titkos adatait, de lehetőség igényelni azokat a tárolóból igény szerint. Ez lehetővé teszi, hogy frissítse a kulcsok és titkos kulcsok működésének megzavarása nélkül megtesztelheti az alkalmazás, amely nyit meg a kulcs és titkos kódok kezelése körül lehetőségeket szánt viselkedését.

>[!IMPORTANT]
> Ebben a cikkben szereplő példák csak illusztrációs célokat szolgálnak. Ezek nem tartozhat éles környezetben való használatra. 

Ez a cikk ismerteti:

- Titkos kulcs tárolása az Azure Key Vault használatával egy példát. Ebben az oktatóanyagban a titkos kulcs tárolása az Azure Storage-fiók kulcsát egy olyan alkalmazás. 
- Emellett a tárfiók kulcsát ütemezett rotációját megvalósítását mutatja be.
- Ez bemutatja, hogyan figyelheti a key vault-naplók és a riasztást, ha váratlan kérelmeket.

> [!NOTE]
> Ez az oktatóanyag részletesen elmagyarázza, a kezdeti beállítás a key vault nem javasolt. Ez az információ: [Mi az Azure Key Vault?](key-vault-overview.md). Platformfüggetlen parancssori felületre vonatkozó utasításokat, lásd: [kezelése a Key Vault parancssori felület használatával](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>A Key Vault beállítása

Ahhoz, hogy egy alkalmazás titkos Key vaultból, először kell létrehozni a titkos kulcsot és töltse fel azt a tárolót. Ez valósítható indítása az Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő paranccsal:

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. PowerShell ehhez a fiókhoz társított összes előfizetés fog kapni. PowerShell alapértelmezés szerint az első utótagcímkéjét használja.

Ha több előfizetéssel rendelkezik, akkor előfordulhat, hogy adja meg azt, amelyik a kulcstároló létrehozásához használt. Adja meg a fiókhoz tartozó előfizetések megtekintéséhez a következő:

```powershell
Get-AzSubscription
```

Ha az előfizetést, amelyhez a key vault követően a naplózandó van társítva, adja meg:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Ez a cikk bemutatja a tárfiókkulcs tárolására, egy titkos kulcsot, mert be kell szereznie a tárfiók kulcsára.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

(Ebben az esetben az a tárfiók kulcsának) a titkos kód beolvasása, után, amely egy biztonságos karakterláncra konvertálni és majd létrehoz egy titkos kulcsot, ezt az értéket a key vaultban lévő.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ezután szerezze be az URI-t a titkos kulcsot hozott létre. Ez szolgál egy későbbi lépésben, a titkos kód lekérése a key vault hívásakor. Futtassa a következő PowerShell-parancsot, és jegyezze fel az azonosító értékét, amely a titkos URI:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Az alkalmazás beállítása

Most, hogy már tárolt titkos kulcs, kód segítségével és használata. Ennek eléréséhez szükséges néhány lépésből áll. Az első és legfontosabb lépés az alkalmazás regisztrálása az Azure Active Directoryval, és ezután mondanunk az alkalmazással kapcsolatos információk a Key Vault, hogy az alkalmazás érkező kéréseket tesz lehetővé.

> [!NOTE]
> Az alkalmazás Azure Active Directory bérlőnél a key vaultban, létre kell hozni.
>
>

1. Keresse meg az Azure Active Directory.
2. Válasszon **alkalmazásregisztrációk** 
3. Válasszon **új alkalmazásregisztráció** , vegye fel az Azure Active Directory-alkalmazást.

    ![Nyissa meg az alkalmazások az Azure Active Directoryban](./media/keyvault-keyrotation/azure-ad-application.png)

4. Az a **létrehozás** szakaszban hagyja meg az alkalmazás típusát, **WEB APPLICATION AND/OR WEB API** és nevezze el az alkalmazást. Adja meg az alkalmazás egy **SIGN-ON URL**. Ebben a bemutatóban bármit is lehet.

    ![Hozzon létre az alkalmazás regisztrálása](./media/keyvault-keyrotation/create-app.png)

5. Az alkalmazás Azure Active Directoryhoz való hozzáadása után jut az alkalmazás oldalhoz. Válassza ki **beállítások** , majd a tulajdonságok. Másolás a **Alkalmazásazonosító** értéket. Ez a későbbi lépésekben szükség lesz.

Ezután akkor hozzon létre egy kulcsot az alkalmazás, így kommunikálhat az Azure Active Directoryban. A kulcs alatt hozhat létre az a **kulcsok** szakaszba **beállítások**. Jegyezze fel az újonnan létrehozott kulcs használható az Azure Active Directory-alkalmazás egy későbbi lépésben. Figyelje meg, hogy a kulcs nem lesz elérhető, lépjen ki ez a szakasz után. 

![Az Azure Active Directory-alkalmazás kulcsok](./media/keyvault-keyrotation/create-key.png)

Mielőtt bármilyen hívásokat az alkalmazásból a key vaultban történő létrehozásáról, a a key vault ossza meg az alkalmazást és annak engedélyeivel kapcsolatos. A következő parancsot a tároló nevére, és az Azure Active Directory-alkalmazás és a jogok Alkalmazásazonosítója veszi **első** az alkalmazás a key vaulthoz való hozzáférés.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Ezen a ponton készen áll az alkalmazás hívások kiépítésének megkezdésére. Az alkalmazásban telepítenie kell a NuGet-csomagok az Azure Key Vault és az Azure Active Directory használata szükséges. A Visual Studio Csomagkezelő konzolról adja meg a következő parancsokat. A jelen cikk írásakor az aktuális az Azure Active Directory-csomag verziója 3.10.305231913, ezért előfordulhat, hogy erősítse meg a legújabb verzióra, és ennek megfelelően frissülnek.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Az alkalmazás kódjában hozzon létre egy osztályt, amely tárolja a az Azure Active Directory hitelesítési módszert. Ebben a példában az adott osztály neve **Utils**. Adja hozzá a következő using utasítást:

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

Adja hozzá a Key Vault hívja meg és a titkos kód értékét a vizualizációhoz szükséges kódot. Először hozzá kell adnia a következő using utasítást:

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

Az Azure Key Vault titkos kódként tárolt értékek Elforgatás stratégia megvalósításához különféle lehetőségek állnak rendelkezésre. Titkos kódok forgatható manuális folyamat részeként, előfordulhat, hogy rotációja programozott API-hívás használatával, vagy előfordulhat, hogy rotációja egy Automation-szkript alapján. Ez a cikk az alkalmazásában fog használni az Azure PowerShell az Azure Automation szolgáltatással kombinálva módosítása az Azure Storage-fiók hozzáférési kulcsára. A key vault titkos majd frissíteni, hogy az új kulccsal.

Ahhoz, hogy az Azure Automation a key vaultban lévő titkos értékeinek beállításához, az ügyfél-Azonosítót kell szereznie a vászonhoz, és az Azure Automation-példány létrejöttekor létrehozott nevű kapcsolat. Válassza ki ezt az Azonosítót talál **eszközök** az Azure Automation-példányból. Itt válassza **kapcsolatok** majd válassza ki a **AzureRunAsConnection** egyszerű szolgáltatást. Jegyezze fel a **Alkalmazásazonosító**.

![Az Azure Automation ügyfél-azonosító](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

A **eszközök**, válassza a **modulok**. A **modulok**, jelölje be **katalógus**, majd keresse meg és **importálás** adott a következő modulok frissítése:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> A jelen cikk írásakor az csak a korábban feljegyzett modulok frissíteni kell a következő parancsprogram szükséges. Ha azt tapasztalja, hogy az automation-feladat meghiúsul, győződjön meg arról, hogy az összes szükséges modulokat és azok függőségeit importálta.
>
>

Miután az Azure Automation-kapcsolat a lekérdezés az Alkalmazásazonosítót, utasítsa a key vaultban, hogy az alkalmazás hozzáfér a tároló titkos kulcsainak frissítése. Ez a következő PowerShell-paranccsal végezhető el:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Majd **Runbookok** az Azure Automation-példányt, és válassza ki a **forgatókönyv hozzáadása**. Kattintson a **Gyors létrehozás** gombra. Nevezze el a runbookot, és válassza ki **PowerShell** a runbook típusaként. Lehetősége van, adjon meg egy leírást. Végül kattintson **létrehozás**.

![Runbook létrehozása](./media/keyvault-keyrotation/Create_Runbook.png)

A Lekérdezésszerkesztő panelén az új runbook illessze be a következő PowerShell-parancsfájlt:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
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

#Optionally you may set the following as parameters
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

A Lekérdezésszerkesztő panelén válassza a **teszt panel** tesztelni a parancsfájlt. Miután a parancsfájl hiba nélkül fut, kiválaszthatja **közzététel**, és a runbook vissza a runbook konfiguráció panelen ütemezés alkalmazhatja.

## <a name="key-vault-auditing-pipeline"></a>A Key Vault naplózási folyamat
Ha beállította a key vault, a naplók összegyűjtése a kulcstartó hozzáférési kérelmek naplózását kapcsolhatja. Ezek a naplók a kijelölt Azure Storage-fiókban vannak tárolva, és figyeli és elemzi, lekérhetik. Az alábbi forgatókönyvet használja az Azure functions, az Azure logic apps és a key vault-naplók hozhat létre egy folyamatot egy e-mailt küldjön, ha egy alkalmazást, amely egyezik a WebApp Alkalmazásazonosítója lekéri a titkos kulcsok a tárolóból.

Először engedélyeznie kell a key vault naplózásának. Ezt megteheti a következő PowerShell-parancsok (részletes információ megtekinthető [key vault-naplózás](key-vault-logging.md)):

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Ennek engedélyezése után a naplók összegyűjtése a kijelölt tárfiókba kezdő. Ezek a naplók tartalmaz eseményeket hogyan és mikor érhetők el a kulcstartók, és ki használja őket.

> [!NOTE]
> Elérheti a naplóinformációkat 10 perc után a kulcstartó műveletet. Általában csak ennyi ideig tartani.
>
>

A következő lépés [hozzon létre egy Azure Service Bus-üzenetsor](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Ez az, ahol a key vault-naplók leküld. Ha a naplózási üzenetek az üzenetsorban, a logikai alkalmazás felveszi őket, és kezelje őket. Hozzon létre egy service bus a következő lépéseket:

1. Hozzon létre a Service Bus-névtér (Ha már rendelkezik ilyennel, amelyet szeretne használni, ezt a 2. lépés kihagyása).
2. Keresse meg a service bus, az Azure Portalon, és válassza ki a névteret, hozzon létre egy üzenetsort a kívánt.
3. Válassza ki **erőforrás létrehozása**, **vállalati integráció**, **a Service Bus**, majd adja meg a szükséges adatokat.
4. Válassza ki a Service Bus-kapcsolati adatok kiválasztása a névteret, majd **kapcsolatadatok**. Ezt az információt a következő szakaszban kell.

Ezután [hozzon létre egy Azure-függvényt](../azure-functions/functions-create-first-azure-function.md) lekérdezik a key vault-naplók a tárfiókban található, és új szerepeljenek. Ez lesz a függvény, amely akkor aktiválódik, ütemezés szerint.

Azure-függvény létrehozásához válassza a **erőforrás létrehozása**, keressen a piactéren _Függvényalkalmazás_, és kattintson a **létrehozás**. A létrehozás során használja egy meglévő szolgáltatási csomag, vagy hozzon létre egy újat. Sikerült is választhatnak a dinamikus üzemeltetéséhez. A függvény üzemeltetési lehetőségeinek további részleteket tekinthet meg [az Azure Functions méretezése](../azure-functions/functions-scale.md).

Az Azure-függvény létrehozását követően keresse meg, és válasszon egy időzítő függvény és a C\#. Kattintson a **függvény létrehozása**.

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
> Ellenőrizze, hogy cserélje le a fenti kóddal, hogy a tárfiók mutasson, ahol a kulcstartónaplók írt található változókat, a korábban létrehozott service bus és a storage kulcstartónaplók egyedi elérési útja.
>
>

A függvény szerzi be a legújabb naplófájlban a tárfiókból ahol a kulcstartónaplók készültek, begyűjti a fájl a legújabb események és leküldi azokat egy Service Bus-üzenetsorba. Mivel egyetlen fájl rendelkezhet több esemény, hozzunk létre egy sync.txt fájlt, amely a függvény is kivételezett fel a legutóbbi esemény időbélyege meghatározásához. Ez biztosítja, hogy ne leküldi ugyanahhoz az eseményhez több alkalommal. A sync.txt fájl tartalmazza a legutóbbi észlelt esemény időbélyeggel. A naplók betöltésekor, alapján történő küldés időbélyegzője legyen annak biztosítása érdekében a megfelelő sorrendben rendezni kell.

Ennél a függvénynél további kódtárak pedig, amelyek nem érhetők el az Azure Functions beépített néhány hivatkozik. Az ezen, ellenőriznünk kell őket az Azure Functions a Nugetet használó. Válassza ki a **fájlok megtekintése** lehetőséget.

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

Követően **mentése**, az Azure Functions a szükséges bináris fájlokat tölti le.

Váltson a **integráció** lapra, és nevezze el az időzítő paraméter jelentéssel bíró belül a funkció használatához. Az a fenti kóddal, která bude volána az időzítő vár *myTimer*. Adjon meg egy [CRON-kifejezés](../app-service/webjobs-create.md#CreateScheduledCRON) módon: 0 \* \* \* \* \* az időzítő, amely újraindítja a függvényt, hogy percenként egyszer futtatni.

Az azonos **integráció** lapon maradva adja hozzá a típusú bemenetet **Azure Blob Storage**. Ez a sync.txt fájlt, amely tartalmazza a tekintett meg, a függvény által az utolsó esemény időbélyegzője fog mutatni. Ez a paraméter nevét a függvényen belül elérhető lesz. A fenti kóddal, az az Azure Blob Storage-bemenetet vár a paraméternév megadásához, hogy *inputBlob*. Válassza ki a tárfiókot, ahol fogja befolyásolni a sync.txt fájlt (Ez lehet ugyanaz vagy egy másik tárfiók). Az elérési út mezőben adja meg az elérési utat, ahol a fájl él-e a következő formátumban: {container-name}/path/to/sync.txt.

Adja hozzá a típusú kimenet *Azure Blob Storage* kimeneti. Ez az a bemenetben megadott sync.txt fájlt fog mutatni. Ez használatos a függvény által tekintett meg az utolsó esemény időbélyegzője írni. A fenti kód vár ezt a paramétert, která bude volána *outputBlob*.

A függvény ezen a ponton készen áll. Ügyeljen arra, hogy váltson vissza a **Develop** lapra, és mentse a kódot. Ellenőrizze a kimeneti ablakban, a fordítási hibákat, és ennek megfelelően javítsa ki azokat. A kód lefordításához, ha majd a kódot kell most már lehet a key vault naplóinak percenként ellenőrzi és leküldése bármely új események az alakzatot a megadott Service Bus-üzenetsorba. Minden alkalommal aktiválódik, a függvény a napló ablak írja naplózási információkat kell megjelennie.

### <a name="azure-logic-app"></a>Az Azure logic app

Ezután létre kell hoznia, amely fogadja az eseményeket, hogy a függvény van próbálna leküldeni a Service Bus-üzenetsorba, a tartalom elemzi és küld egy e-mailt az egyező feltétel alapján az Azure logic app.

[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) a **új > a logikai alkalmazás**.

A logikai alkalmazás létrehozása után keresse meg, és válassza a **szerkesztése**. A logic app-szerkesztő belül válassza **Service Bus-üzenetsor** , és adja meg a várólista csatlakozni a Service Bus hitelesítő adatait.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Ezután válassza ki **feltétel hozzáadása**. A feltétel váltson a speciális szerkesztő, és írja be a következő kódra, és cserélje le a tényleges APP_ID a webalkalmazás APP_ID:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Ez a kifejezés lényegében adja vissza **hamis** Ha a *appid* a beérkező eseményben (amely a Service Bus-üzenet törzsét) a rendszer nem a *appid* az alkalmazás.

Most hozzon létre egy művelet alatt **Ha nem, ne tegyen semmit**.

![Az Azure Logic App művelet kiválasztása](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Adja meg a műveletet, **Office 365 – e-mail küldése**. Töltse ki a mezőket hozhat létre egy e-mailt küldjön, ha adja vissza a meghatározott feltétel **hamis**. Ha nem rendelkezik Office 365-höz, sikerült tekintse alternatívák az azonos eredmények elérése érdekében.

Ezen a ponton rendelkezik egy teljes körű folyamatot, amely új a key vault-naplók percenként egyszer keres. Service bus-üzenetsorba leküldi megtalálja az új naplók. A logikai alkalmazás akkor aktiválódik, ha új üzenet a várólistában lévő hajtanak végre. Ha a *appid* belül az esemény nem egyezik a hívó alkalmazás Alkalmazásazonosítója, e-mailt küld.
