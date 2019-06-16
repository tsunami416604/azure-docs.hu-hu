---
title: Az Azure Cosmos DB az Azure Active Directory ügyféltanúsítvány-alapú hitelesítés
description: Útmutató a tárelérési kulcsok a tanúsítványalapú hitelesítéshez az Azure AD identitás beállítása az Azure Cosmos DB-ből.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: eb8c98df0f015244adf06a9b57f2223509f1f081
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082962"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-account"></a>Az Azure AD identitás kulcsokhoz történő hozzáféréshez egy Azure Cosmos-fiókból a Tanúsítványalapú hitelesítés

Ügyféltanúsítvány-alapú hitelesítés lehetővé teszi, hogy az ügyfélalkalmazás az Azure Active Directory (Azure AD) használatával hitelesíteni ügyféltanúsítvánnyal. Tanúsítvány alapú hitelesítést végezhet egy gépen, ahol meg kell identitást, például egy helyszíni gépre vagy virtuális gép az Azure-ban. Az alkalmazás ezután olvashatja az Azure Cosmo DB kulcsok közvetlenül az alkalmazás a kulcsok nélkül. Ez a cikk bemutatja, hogyan hozzon létre egy minta Azure AD-alkalmazást, konfigurálja a tanúsítványalapú hitelesítéshez, jelentkezzen be Azure-ban az új alkalmazásazonosító, és ezután lekéri a kulcsok az Azure Cosmos-fiókjából. Ez a cikk az identitás beállítása az Azure Powershellt, és biztosít egy C# mintaalkalmazást, amely hitelesíti, és az Azure Cosmos-fiók kulcsok keresztül fér hozzá.  

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [legújabb verzió](/powershell/azure/install-az-ps) az Azure PowerShell.

* Ha nem rendelkezik egy [Azure-előfizetés](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

## <a name="register-an-app-in-azure-ad"></a>Alkalmazás regisztrálása az Azure ad-ben

Ebben a lépésben a minta-webalkalmazáshoz regisztrálni fogja az Azure AD-fiókjával. Ez az alkalmazás újabb segítségével olvassa el a kulcsokat az Azure Cosmos-fiókjából. Használja az alábbi lépéseket kell regisztrálni egy alkalmazást: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure **Active Directory** panelen lépjen alkalmazás regisztrációk panelre, és válassza a **új regisztrációs**. 

   ![Új alkalmazás regisztrálása az Active Directoryban](./media/certificate-based-authentication/new-app-registration.png)

1. Töltse ki a **alkalmazás regisztrálása** űrlapját a következő adatokat:  

   * **Név** – adjon meg egy nevet az alkalmazásnak, bármilyen nevet, például: "mintaalkalmazás" lehet.
   * **Támogatott fióktípusok** – válasszon **fiókok csak a szervezeti könyvtárban adhatja meg (alapértelmezett könyvtár)** erőforrások engedélyezéséhez az alkalmazás eléréséhez az aktuális könyvtárban található. 
   * **Átirányítási URL-cím** – válassza ki az alkalmazás típusú **webes** , és adjon meg egy URL-címet, ahol az alkalmazás üzemel, bármely URL-cím lehet. Például megadhat egy tesztcélú URL-cím például `https://sampleApp.com` nem probléma, még akkor is, ha az alkalmazás nem létezik.

   ![Egy mintául szolgáló webalkalmazás regisztrációja](./media/certificate-based-authentication/register-sample-web-app.png)

1. Válassza ki **regisztrálása** az űrlap kitöltése után.

1. Az alkalmazás regisztrálása után jegyezze fel a **Application(client) azonosító** és **Objektumazonosító**, használhatja ezeket az adatokat a következő lépésben. 

   ![Az alkalmazás és az objektum azonosítók beszerzése](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Az Azure ad-modul telepítése

Ebben a lépésben az Azure AD PowerShell modul telepíteni fogja. Ez a modul az előző lépésben regisztrált alkalmazás Azonosítójának lekéréséhez, és rendelje hozzá, amelyek egy önaláírt tanúsítvány szükséges. 

1. Nyissa meg a Windows PowerShell ISE-ben rendszergazdai jogosultságokkal. Ha még nem tette meg, AZ PowerShell-modul telepítéséhez, és csatlakozzon az előfizetéséhez. Ha több előfizetéssel rendelkezik, a környezet aktuális előfizetésben beállíthatja, ahogyan az az alábbi parancsokat:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Telepítse és importálja a [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) modul

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Bejelentkezés az Azure AD-bA

Jelentkezzen be az Azure AD, ahol regisztrálta az alkalmazást. A Connect-AzureAD paranccsal jelentkezzen be a fiókjába, adja meg az Azure-fiók hitelesítő adatait az előugró ablakban. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Nyissa meg a Windows PowerShell ISE-t egy másik példányát, és hozzon létre egy önaláírt tanúsítványt, és olvassa el a tanúsítvány a kulcs a következő parancsokat:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>A tanúsítványalapú hitelesítő adat létrehozása 

Ezután futtassa az alábbi parancsokat az alkalmazás objektum Azonosítójának lekéréséhez és a tanúsítványalapú hitelesítő adat létrehozása. Ebben a példában egy év után lejár a tanúsítvány beállított, beállíthatja bármely szükséges záró dátum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

A fenti parancs az alábbi képernyőfelvételhez hasonlóan a kimenetet eredményezi:

![Tanúsítványalapú hitelesítő adat létrehozása kimenet](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Az új identitás használatára az Azure Cosmos-fiókok konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Lépjen az Azure Cosmos-fiókjába, nyissa meg a **hozzáférés-vezérlés (IAM)** panelen.

1. Válassza ki **Hozzáadás** és **szerepkör-hozzárendelés hozzáadása**. Adja hozzá a PéldaAlkalmazás az előző lépésben létrehozott **közreműködői** szerepkör az alábbi képernyőképen látható módon:

   ![Az új identitás használatára az Azure Cosmos-fiókok konfigurálása](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Válassza ki **mentése** után töltse ki az űrlapot


## <a name="access-the-keys-from-powershell"></a>A hívóbetűk powershellből

Ebben a lépésben jelentkezzen be az Azure az alkalmazás és a tanúsítvány létrehozása és a hozzáférési kulcsok az Azure Cosmos-fiók használatával. 

1. Először törölje az Azure-fiók hitelesítő adatai segítségével jelentkezzen be a fiókjába. Hitelesítő adatok a következő paranccsal törölheti:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Ezután ellenőrizze, hogy az alkalmazás hitelesítő adataival jelentkezzen be Azure Portalra, és az Azure Cosmos DB hívóbetűk:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Az előző parancs megjeleníti az elsődleges és másodlagos főkulcsok az Azure Cosmos-fiók. Az Azure Cosmos-fiókot, hogy ellenőrizze, hogy a get-kulcsok kérelem sikeres volt, és a "PéldaAlkalmazás" alkalmazás az eseményt kezdeményezett tevékenységnaplójának tekintheti meg. 
 
![Az Azure AD-ben a get-kulcsok hívás ellenőrzése](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>A kulcsának egy C# alkalmazás 

Ez a forgatókönyv-kulcsok elérésével is ellenőrizheti a C# alkalmazás. A következő C# Konzolalkalmazás, amely az Azure Cosmos DB-kulcsok férhetnek hozzá az alkalmazást, regisztrálni az Active Directory használatával. Ne feledje frissíteni a bérlő azonosítója, clientID, certName, erőforráscsoport neve, előfizetés-Azonosítójára, az Azure Cosmos-fiók neve részletei a kód futtatása előtt. 

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
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Ez a parancsfájl kimenete az elsődleges és másodlagos főkulcsok, az alábbi képernyőképen látható módon:

![csharp-alkalmazás kimenete](./media/certificate-based-authentication/csharp-application-output.png)

Az előző szakaszban hasonlóan megtekintheti a tevékenységnapló ellenőrzése, hogy a get-kulcsok kérelem eseményt kezdeményezett a(z) a "PéldaAlkalmazás" alkalmazás az Azure Cosmos-fiók. 


## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Key Vault használatával az Azure Cosmos-kulcsok](access-secrets-from-keyvault.md)

* [Az Azure Cosmos DB biztonsági attribútumok](cosmos-db-security-attributes.md)