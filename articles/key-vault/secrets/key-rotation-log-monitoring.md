---
title: A végpontok közötti kulcsfontosságú rotációs és naplózási Azure Key Vault beállítása | Microsoft Docs
description: Ez a útmutató segítséget nyújt a kulcsok elforgatásának beállításához és a Key Vault-naplók figyeléséhez.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a5aaef50f12bfec89cf5e883ed6b1c85fa984ad6
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995971"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Azure Key Vault beállítása a kulcsfontosságú rotációs és naplózási szolgáltatással

## <a name="introduction"></a>Introduction (Bevezetés)

A Key Vault használata után elkezdheti a kulcsok és titkok tárolására. Az alkalmazásai többé nem kell megőrizniük a kulcsokat vagy a titkokat, de igény szerint kérhetik őket a tárból. A Key Vault lehetővé teszi a kulcsok és a titkos kódok frissítését anélkül, hogy az hatással lenne az alkalmazás viselkedésére, ami a kulcs-és titkos felügyeleti lehetőségek széles körét nyitja meg.

Ez a cikk bemutatja, hogyan valósítható meg a Storage-fiók kulcsainak ütemezett rotációja, hogyan figyelhetők meg a Key Vault-naplók, és hogyan lehet riasztásokat felvenni váratlan kérelmek esetén. 

Először létre kell hoznia egy Key vaultot a választott módszer használatával:

- [Azure Key Vault titkos kód beállítása és beolvasása az Azure CLI használatával](quick-create-cli.md)
- [Azure Key Vault titkos kulcsának beállítása és beolvasása Azure PowerShell használatával](quick-create-powershell.md)
- [Azure Key Vault titkos kulcsának beállítása és beolvasása Azure Portal használatával](quick-create-portal.md)


## <a name="store-a-secret"></a>Titkos kód tárolása

Annak engedélyezéséhez, hogy az alkalmazás Key Vault titkos kulcsot kérjen, először létre kell hoznia a titkot, és fel kell töltenie a tárolóba.

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. A PowerShell megkapja az ehhez a fiókhoz társított összes előfizetést. A PowerShell alapértelmezés szerint az elsőt használja.

Ha több előfizetéssel rendelkezik, előfordulhat, hogy meg kell adnia a kulcstartó létrehozásához használtt. Adja meg a következőt a fiókhoz tartozó előfizetések megtekintéséhez:

```powershell
Get-AzSubscription
```

A naplózni kívánt kulcstartóhoz tartozó előfizetés megadásához írja be a következőt:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Mivel ez a cikk azt mutatja be, hogy a Storage-fiók kulcsa titkosként van tárolva, a Storage-fiók kulcsát kell megszereznie.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

A titkos kulcs (ebben az esetben a Storage-fiók kulcsa) beolvasása után a kulcsot egy biztonságos karakterláncra kell konvertálnia, majd létre kell hoznia egy titkos kulcsot a kulcstartóban.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ezután szerezze be a létrehozott titok URI-JÁT. Erre az URI-ra szüksége lesz egy későbbi lépésben a Key Vault meghívásához és a titkos kód lekéréséhez. Futtassa a következő PowerShell-parancsot, és jegyezze fel az azonosító értékét, amely a titok URI-ja:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Az alkalmazás beállítása

Most, hogy rendelkezik egy titkos kulccsal, a kód használatával kérheti le és használhatja azt néhány további lépés végrehajtása után.

Először regisztrálnia kell az alkalmazást Azure Active Directory. Ezután tájékoztassa Key Vault az alkalmazás adatait, hogy az alkalmazástól érkező kéréseket engedélyezze.

> [!NOTE]
> Az alkalmazást a Key vaulttal megegyező Azure Active Directory bérlőn kell létrehozni.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban. Állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg és válassza ki az **Azure Active Directoryt**. A **kezelés**területen válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza az **új regisztráció**lehetőséget.
1. Az **alkalmazás regisztrálása**területen adja meg a felhasználók számára megjelenítendő, értelmezhető alkalmazás nevét.
1. A következőképpen adhatja meg, hogy ki használhatja az alkalmazást:

    | Támogatott fióktípusok | Leírás |
    |-------------------------|-------------|
    | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha üzletági (LOB) alkalmazást készít. Ez a lehetőség nem érhető el, ha nem regisztrálja az alkalmazást egy címtárban.<br><br>E lehetőség választása esetén az alkalmazás csak egy Azure AD-bérlős lesz.<br><br>Ez az alapértelmezett beállítás, ha az alkalmazást egy címtáron kívül regisztrálja. Ha címtáron kívül regisztrálja az alkalmazást, a több Azure AD-bérlős alkalmazás és a személyes Microsoft-fiókok használata lesz az alapértelmezés. |
    | **Tetszőleges szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha szeretne minden üzleti és az oktatási ügyfelet megcélozni.<br><br>E lehetőség választása esetén az alkalmazás csak több Azure AD-bérlős lesz.<br><br>Ha az alkalmazást csak az Azure AD-ben regisztrálta, akkor a **hitelesítés** lapon frissítheti azt, hogy az Azure ad több-bérlős legyen, és vissza az egybérlős példányra. |
    | **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha a lehető legszélesebb ügyfélkört szeretbé megcélozni.<br><br>E lehetőség választása esetén az alkalmazás több Azure AD-bérlős lesz, és személyes Microsoft-fiókok is használhatók lesznek.<br><br>Ha az alkalmazást Azure AD több-bérlős és személyes Microsoft-fiókkal regisztrálta, akkor ez a beállítás nem módosítható a felhasználói felületen. Ehelyett az alkalmazásjegyzék-szerkesztőt kell használnia a támogatott fióktípusok módosításához. |

1. Az **átirányítási URI (nem kötelező)** területen válassza ki az Ön által felépített alkalmazás típusát: **webes** vagy **nyilvános ügyfél (mobil & asztali)**. Ezután adja meg az alkalmazáshoz tartozó átirányítási URI-t vagy válasz URL-címét.

    * Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A `https://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
    * Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg az alkalmazáshoz tartozó értéket, például: `myapp://auth`.

1. Miután végzett, válassza a **Regisztrálás** lehetőséget.

    ![Megjeleníti a képernyőt, amely új alkalmazást regisztrál a Azure Portal](../media/new-app-registration.png)

Az Azure AD egy egyedi alkalmazást vagy ügyfelet rendel hozzá az alkalmazáshoz. A portál megnyitja az alkalmazás **Áttekintés** lapját. Jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét.

Ha képességeket szeretne hozzáadni az alkalmazáshoz, kiválaszthat más konfigurációs beállításokat, például a védjegyezést, a tanúsítványokat és a titkos kulcsokat, az API-engedélyeket és egyebeket.

![Példa az újonnan regisztrált alkalmazások áttekintő oldalára](../media//new-app-overview-page-expanded.png)

Mielőtt bármilyen hívást hozna létre az alkalmazásból a kulcstartóba, meg kell adnia a Key vaultot az alkalmazásról és annak engedélyeiről. A következő parancs a tár nevét és az **alkalmazás (ügyfél) azonosítóját** használja a Azure Active Directory alkalmazásból, hogy az **alkalmazás hozzáférjen** a kulcstartóhoz.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Most már készen áll az alkalmazások létrehozásának megkezdésére. Az alkalmazásban telepítenie kell a Azure Key Vault és Azure Active Directory használatához szükséges NuGet-csomagokat. A Visual Studio Package Manager konzolon adja meg a következő parancsokat. A cikk írásakor a Azure Active Directory csomag aktuális verziója 3.10.305231913, ezért szükség szerint erősítse meg a legújabb verziót és a frissítést.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Az alkalmazás kódjában hozzon létre egy osztályt a Azure Active Directory-hitelesítés metódusának tárolására. Ebben a példában ez az osztály **utils**. Adja hozzá a `using` következő utasítást:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ezután adja hozzá a következő metódust az JWT-jogkivonat lekéréséhez Azure Active Directoryból. A karbantartáshoz érdemes lehet áthelyezni a rögzített karakterlánc-értékeket a webes vagy az alkalmazás-konfigurációba.

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

Adja hozzá a Key Vault meghívásához és a titkos érték beolvasásához szükséges kódot. Először hozzá kell adnia a következő `using` utasítást:

```csharp
using Microsoft.Azure.KeyVault;
```

Adja hozzá a metódus hívásait a Key Vault meghívásához és a titkos kód lekéréséhez. Ebben a metódusban megadhatja az előző lépésben mentett titkos URI-t. Jegyezze fel a **GetToken** metódus használatát a korábban létrehozott **utils** osztályból.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ha futtatja az alkalmazást, most hitelesítenie kell Azure Active Directory, majd le kell kérnie a titkos értéket a Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Kulcs elforgatása Azure Automation használatával

> [!IMPORTANT]
> Azure Automation runbookok továbbra is szükség van a `AzureRM` modul használatára.

Most már készen áll egy rotációs stratégia beállítására a Key Vault titokként tárolt értékekhez. A titkok több módon is elforgathatók:

- Manuális folyamat részeként
- Programozott módon API-hívások használatával
- Egy Azure Automation parancsfájlon keresztül

Ebben a cikkben a PowerShell és a Azure Automation együttes használatával módosíthatja az Azure Storage-fiók hozzáférési kulcsát. Ezt követően új kulccsal frissíti a Key Vault titkos kulcsát.

Annak engedélyezéséhez, Azure Automation hogy a Key vaultban meg lehessen adni a titkos értékeket, le kell kérnie a **azurerunasconnection elemet**nevű kapcsolódás ügyfél-azonosítóját. Ez a Kapcsolódás a Azure Automation példányának létrehozásakor jött létre. Az azonosító megkereséséhez válassza ki az **eszközök** elemet a Azure Automation-példányból. Onnan válassza a **kapcsolatok**lehetőséget, majd válassza ki a **azurerunasconnection elemet** egyszerű szolgáltatásnevet. Jegyezze fel a **ApplicationId** értékét.

![Azure Automation ügyfél-azonosító](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Az **eszközök**területen válassza a **modulok**elemet. Válassza **a**katalógus lehetőséget, majd keresse meg és importálja a következő modulok frissített verzióit:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> A cikk írásakor csak a korábban feljegyzett modulok szükségesek a következő parancsfájlhoz való frissítéshez. Ha az Automation-feladatok sikertelenek, ellenőrizze, hogy az összes szükséges modult és függőségeit importálta-e.

A Azure Automation-kapcsolatok alkalmazás-AZONOSÍTÓjának lekérése után meg kell adnia a kulcstartónak, hogy az alkalmazás jogosult-e a titkos kódok frissítésére a tárolóban. Használja a következő PowerShell-parancsot:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Ezután válassza a **runbookok** elemet a Azure Automation példány alatt, majd válassza a **Runbook hozzáadása**elemet. Kattintson a **Gyors létrehozás** gombra. Nevezze el a runbook, és válassza a **PowerShell** lehetőséget a runbook típusaként. Leírást adhat hozzá. Végül válassza a **Létrehozás**lehetőséget.

![Runbook létrehozása](../media/keyvault-keyrotation/Create_Runbook.png)

Illessze be a következő PowerShell-szkriptet a szerkesztő ablaktáblában az új runbook:

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

A szkript teszteléséhez a szerkesztő ablaktáblán kattintson a **teszt panel** elemre. A parancsfájl hiba nélküli futtatása után kiválaszthatja a **Közzététel**lehetőséget, majd a runbook-konfiguráció ablaktáblán alkalmazhat egy ütemezett runbook is.

## <a name="key-vault-auditing-pipeline"></a>Key Vault naplózási folyamat

A kulcstartó beállításakor bekapcsolhatja a naplózást, hogy összegyűjtse a Key vaulthoz benyújtott hozzáférési kérelmek naplóit. Ezek a naplók egy kijelölt Azure Storage-fiókban tárolódnak, és kihúzhatók, megfigyelhetők és elemezhetők. Az alábbi forgatókönyv az Azure functions, az Azure Logic apps és a Key-Vault naplókat használja egy olyan folyamat létrehozásához, amely e-mailt küld, ha egy alkalmazás, amely nem felel meg a webalkalmazás alkalmazás-AZONOSÍTÓjának, beolvassa a titkos kulcsokat a tárból.

Először engedélyeznie kell a naplózást a kulcstartón. Használja a következő PowerShell-parancsokat. (A cikk részletesen ismerteti a [Key-Vault-naplózást](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A naplózás engedélyezése után a rendszer a naplókat a kijelölt Storage-fiókban tárolja. Ezek a naplók azokat az eseményeket tartalmazzák, amelyekkel és a kulcstartók elérésével, valamint a kivel.

> [!NOTE]
> A naplózási adatokat 10 perccel a kulcstartó művelet után érheti el. Ez gyakran ennél hamarabb elérhető lesz.

A következő lépés [egy Azure Service Bus üzenetsor létrehozása](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Ez a várólista a Key-Vault naplók leküldésének helyét jelenti. Ha a naplózási napló üzenetei a várólistán vannak, a logikai alkalmazás felveszi őket, és elvégez rajtuk műveleteket. Hozzon létre egy Service Bus példányt a következő lépésekkel:

1. Hozzon létre egy Service Bus névteret (ha már rendelkezik egy használni kívánttal, ugorjon a 2. lépésre).
2. Keresse meg a Azure Portal Service Bus példányát, és válassza ki azt a névteret, amelyben létre kívánja hozni a várólistát.
3. Válassza az **erőforrás** > létrehozása**Vállalati integráció** > **Service Bus**lehetőséget, majd adja meg a szükséges adatokat.
4. Keresse meg a Service Bus a kapcsolatok adatait a névtér kiválasztásával, majd a **kapcsolatok adatainak**kiválasztásával. Ezekre az információkra szüksége lesz a következő szakaszhoz.

Ezután [hozzon létre egy Azure-függvényt](../../azure-functions/functions-create-first-azure-function.md) a Key Vault-naplók lekérdezéséhez a Storage-fiókon belül, és vegyen fel új eseményeket. Ezt a függvényt egy ütemezett időpontban indítja el a rendszer.

Azure Function-alkalmazás létrehozásához válassza az **erőforrás létrehozása**lehetőséget, keresse meg a piactéren a **függvényalkalmazás**, majd válassza a **Létrehozás**lehetőséget. A létrehozás során használhat egy meglévő üzemeltetési csomagot, vagy létrehozhat egy újat. Dönthet úgy is, hogy dinamikus üzemeltetést végez. További információ a Azure Functions üzemeltetési lehetőségeiről: [Azure functions skálázása](../../azure-functions/functions-scale.md).

Az Azure Function alkalmazás létrehozása után nyissa meg a következőt:, és válassza ki az **időzítő** forgatókönyvet, és a **C betűt\# ** a nyelvhez. Ezután válassza **a függvény létrehozása**lehetőséget.

![Azure Functions Start panel](../media/keyvault-keyrotation/Azure_Functions_Start.png)

A **fejlesztés** lapon cserélje le a Run. CSX kódot a következőre:

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
> Módosítsa az előző kódban szereplő változókat úgy, hogy az a Storage-fiókra mutasson, ahol a Key Vault-naplók meg vannak írva, a korábban létrehozott Service Bus példányra, valamint a Key-Vault Storage-naplók megadott elérési útjára.

A függvény felveszi a legújabb naplófájlt abból a Storage-fiókból, ahol a Key Vault-naplók meg vannak írva, megragadja a fájl legutóbbi eseményeit, és leküldi őket egy Service Bus-várólistára. 

Mivel egyetlen fájl több eseménnyel is rendelkezhet, létre kell hoznia egy Sync. txt fájlt, amelyet a függvény a legutóbbi esemény időbélyegének meghatározására is rákeres. A fájl használata biztosítja, hogy ne kelljen többször leküldeni ugyanazt az eseményt. 

A Sync. txt fájl a legutóbbi esemény időbélyegzőjét tartalmazza. A naplók betöltését követően a rendszer az időbélyegük alapján rendezi a naplókat, hogy a megfelelő sorrendben legyenek rendezve.

Ebben a függvényben egy pár további, a Azure Functions található dobozból nem elérhető függvénytárra hivatkozunk. A kódtárak felvételéhez Azure Functions kell őket a NuGet használatával történő lekéréséhez. A **kód** mezőben válassza a **fájlok megtekintése**lehetőséget.

!["Fájlok megtekintése" lehetőség](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Vegyen fel egy Project. JSON nevű fájlt a következő tartalommal:

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

A **Mentés**gombra kattintva Azure functions letölti a szükséges bináris fájlokat.

Váltson az **integráció** lapra, és adjon meg egy értelmes nevet a függvényen belül. Az előző kódban a függvény a *myTimer*nevű időzítőt várja. A következőképpen adja meg az időzítőhöz tartozó [cron](../../app-service/webjobs-create.md#CreateScheduledCRON) - `0 * * * * *`kifejezést:. Ez a kifejezés azt eredményezi, hogy a függvény percenként egyszer fut.

Az **integráció** lapon adja meg az **Azure Blob Storage**típus bemenetét. Ez a bemenet arra a Sync. txt fájlra mutat, amely a függvény által a legutóbbi esemény időbélyegét tartalmazza. Ez a bemenet a függvényen belül lesz elérhető a paraméter nevével. Az előző kódban az Azure Blob Storage-bemenet a *inputBlob*paraméter nevét várja. Válassza ki azt a Storage-fiókot, ahol a Sync. txt fájl található (ez lehet ugyanaz vagy egy másik Storage-fiók). Az elérési út mezőben adja meg a fájl elérési útját a `{container-name}/path/to/sync.txt`következő formátumban:.

Adja hozzá az **Azure Blob Storage**típusú kimenetet. Ez a kimenet a bemenetben megadott Sync. txt fájlra mutat. Ezt a kimenetet a függvény a legutóbbi esemény időbélyegének megírására használja. Az előző kód azt várja, hogy ezt a paramétert *outputBlob*nevezzük.

A függvény most már készen áll. Ügyeljen arra, hogy a **fejlesztés** lapra váltson vissza, és mentse a kódot. A fordítási hibákért tekintse meg a kimeneti ablakot, és szükség szerint javítsa ki azokat. Ha a kód fordítása megtörtént, a kódnak most be kell néznie a Key Vault-naplókat percenként, és minden új eseményt be kell tolnia a megadott Service Bus várólistára. A naplózási információkat a függvény minden indításakor a napló ablakba kell írni.

### <a name="azure-logic-app"></a>Azure logikai alkalmazás

Ezután létre kell hoznia egy Azure Logic app-alkalmazást, amely felveszi a függvény által az Service Bus várólistára irányuló eseményeket, elemzi a tartalmat, és egy megegyező feltétel alapján küld e-mailt.

[Hozzon létre egy logikai alkalmazást](../../logic-apps/quickstart-create-first-logic-app-workflow.md) az **erőforrás** > -**integrációs** > **logikai alkalmazás**létrehozása lehetőség kiválasztásával.

A logikai alkalmazás létrehozása után nyissa meg a elemet, és válassza a **Szerkesztés**lehetőséget. A Logic app Editorban válassza a **Service Bus üzenetsor** lehetőséget, és adja meg a Service Bus hitelesítő adatait, hogy az a várólistához kapcsolódjon.

![Azure Logic App Service Bus](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Válassza **a feltétel hozzáadása**lehetőséget. A feltételben váltson a speciális szerkesztőre, és adja meg a következő kódot. Cserélje le a *APP_IDt* a webalkalmazás tényleges alkalmazás-azonosítójával:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Ez a kifejezés lényegében **hamis** értéket ad vissza, ha a bejövő esemény *AppID* (amely az Service Bus üzenet törzse) nem az alkalmazás *AppID* .

Most hozzon létre egy műveletet, **Ha nem, semmit sem**.

![Azure Logic Apps válassza a művelet lehetőséget](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

A művelethez válassza az **Office 365 – e-mail küldése**lehetőséget. Ha a megadott feltétel **hamis**értéket ad vissza, töltse ki a mezőket, és hozzon létre egy e-mailt, amelyet el szeretne küldeni. Ha még nem rendelkezik Office 365-mel, keresse meg az azonos eredmények eléréséhez szükséges alternatívákat.

Mostantól egy teljes körű folyamattal rendelkezik, amely percenként egyszer új Key-Vault naplókat keres. Leküldi az új naplókat, amelyeket megtalál egy Service Bus üzenetsor számára. A logikai alkalmazás akkor aktiválódik, amikor új üzenet kerül a várólistába. Ha az eseményen belüli *AppID* nem felel meg a hívó alkalmazás alkalmazás-azonosítójának, e-mailt küld.
