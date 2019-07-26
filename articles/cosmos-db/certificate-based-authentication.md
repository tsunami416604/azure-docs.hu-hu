---
title: Tanúsítványalapú hitelesítés Azure Active Directory Azure Cosmos DB
description: Megtudhatja, hogyan konfigurálhat egy Azure AD-identitást tanúsítványalapú hitelesítésre a kulcsok Azure Cosmos DBból való eléréséhez.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 9d06cf334f08ba6ec9c47450d21d33733900ebe5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356573"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Tanúsítványalapú hitelesítés Azure AD-identitáshoz Azure Cosmos DB-fiók kulcsainak eléréséhez

A tanúsítványalapú hitelesítés lehetővé teszi az ügyfélalkalmazás hitelesítését Azure Active Directory (Azure AD) és egy ügyféltanúsítvány használatával. A tanúsítványalapú hitelesítést olyan gépen is elvégezheti, amelyre szüksége van egy identitásra, például egy helyszíni gépre vagy egy Azure-beli virtuális gépre. Az alkalmazás elolvashatja Azure Cosmos DB kulcsait anélkül, hogy a kulcsokat közvetlenül az alkalmazásban kelljen volna beolvasnia. Ez a cikk bemutatja, hogyan hozhat létre egy minta Azure AD-alkalmazást, hogyan konfigurálhatja tanúsítványalapú hitelesítésre, bejelentkezhet az Azure-ba az új alkalmazás-identitás használatával, majd lekéri a kulcsokat az Azure Cosmos-fiókból. Ez a cikk a Azure PowerShell használatával állítja be az identitásokat, és C# egy minta alkalmazást biztosít, amely hitelesíti és hozzáfér a kulcsokhoz az Azure Cosmos-fiókból.  

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Azure PowerShell [legújabb verzióját](/powershell/azure/install-az-ps) .

* Ha nem rendelkezik [Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="register-an-app-in-azure-ad"></a>Alkalmazás regisztrálása az Azure AD-ben

Ebben a lépésben egy minta webalkalmazást fog regisztrálni az Azure AD-fiókjában. Ezt az alkalmazást később a rendszer a Azure Cosmos DB-fiók kulcsainak olvasásához használja. Egy alkalmazás regisztrálásához kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure **Active Directory** panelt, lépjen a Alkalmazásregisztrációk panelre, és válassza az **új regisztráció**lehetőséget. 

   ![Új alkalmazás regisztrálása Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Töltse ki az **alkalmazás regisztrálása** űrlapot a következő részletekkel:  

   * **Név** – adja meg az alkalmazás nevét, amely lehet bármilyen név, például "PéldaAlkalmazás".
   * **Támogatott fióktípus** – **csak ebben a szervezeti könyvtárban** válassza a fiókok lehetőséget, hogy az aktuális könyvtárban lévő erőforrások hozzáférjenek ehhez az alkalmazáshoz. 
   * **Átirányítási URL-cím** – válassza a **web** típusú alkalmazást, és adjon meg egy URL-címet, ahol az alkalmazás üzemeltetve van, bármilyen URL-cím lehet. Ebben a példában egy teszt URL-címet is megadhat, `https://sampleApp.com` például akkor is, ha az alkalmazás nem létezik.

   ![Minta webalkalmazás regisztrálása](./media/certificate-based-authentication/register-sample-web-app.png)

1. Az űrlap kitöltése után válassza a **regisztráció** lehetőséget.

1. Ha az alkalmazás regisztrálva van, jegyezze fel az **alkalmazás (ügyfél) azonosítóját** és az **objektumazonosítót**, majd a következő lépésekben fogja használni ezeket az adatokat. 

   ![Az alkalmazás és az objektum azonosítóinak beolvasása](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>A AzureAD modul telepítése

Ebben a lépésben telepíteni fogja az Azure AD PowerShell-modult. Ez a modul az előző lépésben regisztrált alkalmazás AZONOSÍTÓjának lekéréséhez és egy önaláírt tanúsítvány az alkalmazáshoz való hozzárendeléséhez szükséges. 

1. Windows PowerShell integrált parancsprogram-kezelési környezet megnyitása rendszergazdai jogosultságokkal. Ha még nem tette meg, telepítse az az PowerShell-modult, és kapcsolódjon az előfizetéséhez. Ha több előfizetéssel rendelkezik, beállíthatja az aktuális előfizetés környezetét az alábbi parancsokban látható módon:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. A [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) modul telepítése és importálása

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Jelentkezzen be az Azure AD-be

Jelentkezzen be az Azure AD-ba, ahol regisztrálta az alkalmazást. A AzureAD parancs használatával jelentkezzen be a fiókjába, és adja meg az Azure-fiókja hitelesítő adatait az előugró ablakban. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Nyissa meg a Windows PowerShell integrált parancsprogram-kezelési környezet egy másik példányát, és futtassa a következő parancsokat egy önaláírt tanúsítvány létrehozásához és a tanúsítványhoz tartozó kulcs beolvasásához:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Tanúsítvány alapú hitelesítő adat létrehozása 

Ezután futtassa a következő parancsokat az alkalmazás objektum-AZONOSÍTÓjának lekéréséhez és a tanúsítványalapú hitelesítő adatok létrehozásához. Ebben a példában a tanúsítványt egy év után kell lejárni, beállíthatja a szükséges befejezési dátumra.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

A fenti parancs az alábbi képernyőképhez hasonló kimenetet eredményez:

![Tanúsítványalapú hitelesítő adatok létrehozási kimenete](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Az Azure Cosmos-fiók konfigurálása az új identitás használatára

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **hozzáférés-vezérlés (iam) panelt** .

1. Válassza a **Hozzáadás** és a **szerepkör-hozzárendelés hozzáadása**lehetőséget. Adja hozzá az előző lépésben a **közreműködő** szerepkörrel létrehozott PéldaAlkalmazás az alábbi képernyőképen látható módon:

   ![Az Azure Cosmos-fiók konfigurálása az új identitás használatára](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Az űrlap kitöltése után válassza a **Mentés** lehetőséget.


## <a name="access-the-keys-from-powershell"></a>A kulcsok elérése a PowerShellből

Ebben a lépésben be kell jelentkeznie az Azure-ba az alkalmazás és a létrehozott tanúsítvány használatával, és elérheti az Azure Cosmos-fiókja kulcsait. 

1. Először törölje a fiókba való bejelentkezéshez használt Azure-fiók hitelesítő adatait. A hitelesítő adatok a következő paranccsal törölhetők:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Ezt követően ellenőrizze, hogy be tud-e jelentkezni Azure Portal az alkalmazás hitelesítő adataival, és hozzáfér a Azure Cosmos DB kulcsokhoz:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Az előző parancs megjeleníti az Azure Cosmos-fiók elsődleges és másodlagos főkulcsait. Megtekintheti az Azure Cosmos-fiók tevékenységi naplóját annak ellenőrzéséhez, hogy a kulcsok beolvasása kérelem sikeres volt-e, és hogy az eseményt a "PéldaAlkalmazás" alkalmazás kezdeményezte-e. 
 
![A kulcsok beolvasása hívás ellenőrzése az Azure AD-ben](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Kulcsok elérése C# alkalmazásból 

Ezt a forgatókönyvet egy C# alkalmazás kulcsaihoz való hozzáféréssel is ellenőrizheti. A következő C# konzol alkalmazás, amely Azure Cosmos db kulcsokhoz fér hozzá a Active Directory regisztrált alkalmazás használatával. A kód futtatása előtt frissítse a tenantId, a clientID, a certName, az erőforráscsoport nevét, az előfizetés AZONOSÍTÓját, az Azure Cosmos-fiók nevét. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Ez a parancsfájl az elsődleges és a másodlagos főkulcsokat az alábbi képernyőképen mutatja:

![csharp-alkalmazás kimenete](./media/certificate-based-authentication/csharp-application-output.png)

Az előző szakaszhoz hasonlóan megtekintheti az Azure Cosmos-fiókhoz tartozó műveletnapló adatait annak ellenőrzéséhez, hogy a "PéldaAlkalmazás" alkalmazás kezdeményezte-e a kulcsok kérése eseményt. 


## <a name="next-steps"></a>További lépések

* [Azure Cosmos-kulcsok biztonságossá tétele Azure Key Vault használatával](access-secrets-from-keyvault.md)

* [Biztonsági attribútumok az Azure Cosmos DB-hez](cosmos-db-security-attributes.md)
