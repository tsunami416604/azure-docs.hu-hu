---
title: Tanúsítványalapú hitelesítés az Azure Cosmos DB és az Active Directory használatával
description: Ismerje meg, hogyan konfigurálhat egy Azure AD-identitást a tanúsítványalapú hitelesítéshez az Azure Cosmos DB kulcsainak eléréséhez.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365782"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Tanúsítványalapú hitelesítés egy Azure AD-identitáshoz az Azure Cosmos DB-fiók kulcsainak eléréséhez

A tanúsítványalapú hitelesítéssel az ügyfélalkalmazás ügyféltanúsítvánnyal hitelesíthető az Azure Active Directory (Azure AD) használatával. A tanúsítványalapú hitelesítést olyan gépen végezheti el, amelyen identitásra van szükség, például egy helyszíni gépen vagy egy Azure-beli virtuális gépen. Az alkalmazás ezután az Azure Cosmos DB-kulcsok olvasása anélkül, hogy a kulcsokat közvetlenül az alkalmazásban. Ez a cikk ismerteti, hogyan hozhat létre egy minta Azure AD-alkalmazást, konfigurálhatja a tanúsítványalapú hitelesítéshez, jelentkezzen be az Azure-ba az új alkalmazásidentitás használatával, majd lekéri a kulcsokat az Azure Cosmos-fiókból. Ez a cikk az Azure PowerShell segítségével állítja be az identitásokat, és egy C# mintaalkalmazást biztosít, amely hitelesíti és hozzáfér az Azure Cosmos-fiók kulcsaihoz.  

## <a name="prerequisites"></a>Előfeltételek

* Telepítse az Azure PowerShell [legújabb verzióját.](/powershell/azure/install-az-ps)

* Ha nem rendelkezik [Azure-előfizetéssel](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-an-app-in-azure-ad"></a>Alkalmazás regisztrálása az Azure AD-ben

Ebben a lépésben regisztrál egy minta webalkalmazást az Azure AD-fiókban. Ez az alkalmazás később az Azure Cosmos DB-fiókjából származó kulcsok olvasására szolgál. Alkalmazás regisztrálása a következő lépésekkel: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure **Active Directory** ablaktáblát, nyissa meg **az Alkalmazásregisztrációk** ablaktáblát, és válassza **az Új regisztráció**lehetőséget. 

   ![Új alkalmazásregisztráció az Active Directoryban](./media/certificate-based-authentication/new-app-registration.png)

1. Töltse ki a **Jelentkezési** lapot a következő adatokkal:  

   * **Név** - Adja meg az alkalmazás nevét, bármilyen név lehet, például "sampleApp".
   * **Támogatott fióktípusok** – Válassza **a Fiókok lehetőséget ebben a szervezeti címtárban (Alapértelmezett címtár),** hogy az aktuális címtárban lévő erőforrások hozzáférjenek ehhez az alkalmazáshoz. 
   * **Átirányítás URL -** Válassza ki a **webes** típusú alkalmazás, és adjon meg egy URL-t, ahol az alkalmazás található, akkor lehet bármilyen URL-t. Ebben a példában megadhat egy `https://sampleApp.com` teszt URL-t, például akkor is rendben van, ha az alkalmazás nem létezik.

   ![Mintawebalkalmazás regisztrálása](./media/certificate-based-authentication/register-sample-web-app.png)

1. Az űrlap kitöltése után válassza a **Regisztráció** lehetőséget.

1. Az alkalmazás regisztrálása után jegyezze fel az **Alkalmazás(ügyfél) azonosítóját** és **az objektumazonosítót,** és ezeket az adatokat a következő lépésekben fogja használni. 

   ![Az alkalmazás- és objektumazonosítók beszereznie](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Az AzureAD-modul telepítése

Ebben a lépésben telepíti az Azure AD PowerShell-modult. Ez a modul szükséges az előző lépésben regisztrált alkalmazás azonosítójának leigazolásához, valamint egy önaláírt tanúsítvány társításához. 

1. Nyissa meg a Windows PowerShell ISE-t rendszergazdai jogokkal. Ha még nem tette meg, telepítse az AZ PowerShell modult, és csatlakozzon az előfizetéshez. Ha több előfizetéssel rendelkezik, beállíthatja az aktuális előfizetés környezetét az alábbi parancsokban látható módon:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Az [AzureAD-modul](/powershell/module/azuread/?view=azureadps-2.0) telepítése és importálása

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Jelentkezzen be az Azure AD-be

Jelentkezzen be az Azure AD-be, ahol regisztrálta az alkalmazást. A Connect-AzureAD paranccsal jelentkezzen be a fiókjába, írja be az Azure-fiók hitelesítő adatait az előugró ablakban. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Nyissa meg a Windows PowerShell ISE egy másik példányát, és futtassa a következő parancsokat egy önaláírt tanúsítvány létrehozásához, és olvassa el a tanúsítványhoz társított kulcsot:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>A tanúsítványalapú hitelesítő adatok létrehozása 

Ezután futtassa a következő parancsokat az alkalmazás objektumazonosítójának levételéhez és a tanúsítványalapú hitelesítő adatok létrehozásához. Ebben a példában úgy állítjuk be a tanúsítványt, hogy egy év után lejár, beállíthatja azt a szükséges befejezési dátumra.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

A fenti parancs az alábbi képernyőképhez hasonló kimenetet eredményez:

![Tanúsítványalapú hitelesítő adatok létrehozása kimenete](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurálja az Azure Cosmos-fiókot az új identitás használatára

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure Cosmos-fiókot, nyissa meg a **hozzáférés-vezérlési (IAM)** panelt.

1. Válassza **a Hozzáadás** és **hozzáadás szerepkör-hozzárendelés lehetőséget.** Adja hozzá az előző lépésben létrehozott mintaalkalmazást a **Közreműködői** szerepkörrel az alábbi képernyőképen látható módon:

   ![Az Azure Cosmos-fiók konfigurálása az új identitás használatára](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Az űrlap kitöltése után válassza a **Mentés** lehetőséget

## <a name="register-your-certificate-with-azure-ad"></a>A tanúsítvány regisztrálása az Azure AD-vel

A tanúsítványalapú hitelesítő adatokat az Azure AD-ben az Azure Portalon társítani tudja az ügyfélalkalmazáshoz. A hitelesítő adatok társításához fel kell töltenie a tanúsítványfájlt a következő lépésekkel:

Az Ügyfélalkalmazás Azure-alkalmazásregisztrációjában:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure **Active Directory** ablaktábláját, nyissa meg az **alkalmazásregisztrációk** ablaktáblát, és nyissa meg az előző lépésben létrehozott mintaalkalmazást. 

1. Válassza **a Tanúsítványok & titkos kulcsokat,** majd a Tanúsítvány **feltöltése lehetőséget.** Tallózzon az előző lépésben létrehozott tanúsítványfájlban a feltöltéshez.

1. Válassza a **Hozzáadás** lehetőséget. A tanúsítvány feltöltése után megjelennek az ujjlenyomat, a kezdési dátum és a lejárati értékek.

## <a name="access-the-keys-from-powershell"></a>A kulcsok elérése a PowerShellből

Ebben a lépésben az alkalmazás és a létrehozott tanúsítvány használatával jelentkezik be az Azure-ba, és hozzáférhet az Azure Cosmos-fiók kulcsaihoz. 

1. Kezdetben törölje az Azure-fiók hitelesítő adatait, amelyekkel bejelentkezik a fiókjába. A hitelesítő adatokat a következő paranccsal törölheti:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Ezután ellenőrizze, hogy az alkalmazás hitelesítő adataival bejelentkezhet-e az Azure Portalra, és hozzáférhet az Azure Cosmos DB-kulcsokhoz:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Az előző parancs megjeleníti az Azure Cosmos-fiók elsődleges és másodlagos főkulcsait. Megtekintheti az Azure Cosmos-fiók tevékenységnaplóját, és ellenőrizheti, hogy a lekérési kulcsok kérése sikeres volt-e, és az eseményt a "sampleApp" alkalmazás kezdeményezi.

![A lehívási kulcsok hívásának ellenőrzése az Azure AD-ben](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>A kulcsok elérése C# alkalmazásból 

Ezt a forgatókönyvet is ellenőrizheti egy C# alkalmazás kulcsainak elérésével. A következő C# konzolalkalmazás, amely az Azure Cosmos DB-kulcsok eléréséhez az Active Directoryban regisztrált alkalmazás használatával. Győződjön meg arról, hogy a kód futtatása előtt frissítse a tenantId, clientID, certName, erőforráscsoport nevét, az előfizetés-azonosítót, az Azure Cosmos-fiók névadatait. 

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

Ez a parancsfájl az elsődleges és a másodlagos főkulcsokat adja ki az alábbi képernyőképen látható módon:

![csharp alkalmazás kimenete](./media/certificate-based-authentication/csharp-application-output.png)

Az előző szakaszhoz hasonlóan megtekintheti az Azure Cosmos-fiók tevékenységnaplóját, és ellenőrizheti, hogy a lekérési kulcsok kérési eseményét a "sampleApp" alkalmazás kezdeményezi-e. 


## <a name="next-steps"></a>További lépések

* [Azure Cosmos-kulcsok védelme az Azure Key Vaulttal](access-secrets-from-keyvault.md)

* [Az Azure Cosmos DB biztonsági alapkonfigurációja](security-baseline.md)
