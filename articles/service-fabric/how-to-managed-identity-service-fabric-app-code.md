---
title: Azure Service Fabric – felügyelt identitás használata Service Fabric alkalmazásokkal | Microsoft Docs
description: Felügyelt identitások használata Service Fabric alkalmazás kódjából
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 528e1b0a353cdcd716f9bca63c423af7a6f12641
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958238"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Service Fabric alkalmazás felügyelt identitásának kihasználása az Azure-szolgáltatások eléréséhez

Service Fabric alkalmazások a felügyelt identitásokat használhatják a Azure Active Directory-alapú hitelesítést támogató egyéb Azure-erőforrások eléréséhez. Egy alkalmazás olyan [hozzáférési](../active-directory/develop/developer-glossary.md#access-token) jogkivonatot szerezhet be, amely az identitását jelképezi, amely lehet rendszer által hozzárendelt vagy felhasználó által hozzárendelt, és "tulajdonos" tokenként használva hitelesíti magát egy másik szolgáltatásnak – más néven [védett erőforrás-kiszolgálónak](../active-directory/develop/developer-glossary.md#resource-server)–. A jogkivonat a Service Fabric alkalmazáshoz rendelt identitást jelöli, és csak az adott identitást használó Azure-erőforrások (például SF-alkalmazások) számára lesz kibocsátva. Tekintse át [](../active-directory/managed-identities-azure-resources/overview.md) a felügyelt identitások áttekintő dokumentációját a felügyelt identitások részletes leírását, valamint a rendszer által hozzárendelt és a felhasználó által hozzárendelt identitások megkülönböztetését. Ebben a cikkben a felügyelt identitás-kompatibilis Service Fabric alkalmazásra fogunk hivatkozni. [](../active-directory/develop/developer-glossary.md#client-application)

> [!IMPORTANT]
> A felügyelt identitás az erőforrást tartalmazó előfizetéshez társított Azure AD-bérlőben az Azure-erőforrás és egy egyszerű szolgáltatásnév közötti társítást jelöli. A Service Fabric kontextusában a felügyelt identitások csak az Azure-erőforrásként üzembe helyezett alkalmazások esetében támogatottak. 

> [!IMPORTANT]
> A Service Fabric alkalmazás felügyelt identitásának használata előtt az ügyfélalkalmazás számára hozzáférést kell biztosítani a védett erőforráshoz. Tekintse meg az Azure [ad-hitelesítést támogató Azure-szolgáltatások](/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources) listáját a támogatás megkereséséhez, majd a megfelelő szolgáltatás dokumentációjában, amely alapján az identitás hozzáférést biztosít a fontos erőforrásokhoz. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Hozzáférési jogkivonat beszerzése REST API használatával
A felügyelt identitáshoz engedélyezett fürtökön a Service Fabric futtatókörnyezet elérhetővé teszi a localhost végpontot, melyet az alkalmazások használhatnak a hozzáférési tokenek beszerzéséhez. A végpont a fürt minden csomópontján elérhető, és elérhető az adott csomóponton lévő összes entitás számára. A jogosult hívók a végpont meghívásával és egy hitelesítési kód bemutatásával kaphatnak hozzáférési jogkivonatokat. a kódot a Service Fabric futtatókörnyezet hozza létre az egyes különböző szervizcsomag-csomagok aktiválásakor, és a szolgáltatási kód csomagjait futtató folyamat élettartamára van kötve.

A felügyelt identitás-kompatibilis Service Fabric szolgáltatás környezetét a következő változók szerint kell kiszámítani:
- "MSI_ENDPOINT": a localhost végpont, amely a szolgáltatás felügyelt identitásának megfelelő elérési úttal, API-verzióval és paraméterekkel rendelkezik
- "MSI_SECRET": egy olyan hitelesítési kód, amely átlátszatlan karakterlánc, és egyedi módon képviseli a szolgáltatást az aktuális csomóponton.

> [!NOTE]
> A "MSI_ENDPOINT" és a "MSI_SECRET" név a felügyelt identitások ("Managed Service Identity") előző megjelölésére hivatkozik, amely már elavult. A nevek szintén konzisztensek a más Azure-szolgáltatások által használt, a felügyelt identitásokat támogató környezeti változók neveivel.

> [!IMPORTANT]
> Az alkalmazás kódjának meg kell fontolnia a "MSI_SECRET" környezeti változó értékét bizalmas adatokként – nem szabad naplózni vagy egyéb módon terjeszteni. A hitelesítési kód nem rendelkezik a helyi csomóponton kívüli értékkel, vagy a szolgáltatást futtató folyamat megszakadt, de a Service Fabric szolgáltatás identitását jelöli, ezért a hozzáférési jogkivonattal azonos óvintézkedésekkel kell kezelni.

A jogkivonat beszerzéséhez az ügyfél a következő lépéseket hajtja végre:
- a felügyelt identitás végpontjának (MSI_ENDPOINT értékének) az API-verzióval és a tokenhez szükséges erőforrással (célközönséggel) való összefűzésével egy URI-t képez.
- létrehoz egy GET http-kérelmet a megadott URI-hoz
- hozzáadja a hitelesítési kódot (MSI_SECRET érték) a kérelem fejlécéhez.
- elküldi a kérést

A sikeres válasz tartalmazni fog egy JSON-adattartalmat, amely az eredményül kapott hozzáférési jogkivonatot, valamint az azt leíró metaadatokat tartalmazza. A sikertelen válasz a hiba magyarázatát is tartalmazni fogja. A hibák kezelésével kapcsolatos további részletekért lásd alább.

A hozzáférési jogkivonatokat a rendszer a különböző szinteken (csomópont, fürt, erőforrás-szolgáltatói szolgáltatás) Service Fabric gyorsítótárazza, így a sikeres válasz nem feltétlenül jelenti azt, hogy a tokent közvetlenül a felhasználói alkalmazás kérelmére adta ki. A rendszer gyorsítótárazza a tokeneket, hogy az élettartamuk kevesebb legyen, és hogy egy alkalmazás érvényes jogkivonatot kapjon. Azt javasoljuk, hogy az alkalmazás kódja gyorsítótárazza az általa beszerzett hozzáférési jogkivonatokat. a gyorsítótárazási kulcsnak tartalmaznia kell a célközönséget (a). 


Példa a kérelemre:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
ahol

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy a végpontról kívánja beolvasni az adatait. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | A felügyelt identitás végpontja Service Fabric alkalmazásokhoz, amelyeket a MSI_ENDPOINT környezeti változón keresztül biztosítanak. |
| `api-version` | Egy lekérdezési karakterlánc paraméter, amely megadja a felügyelt Identity token szolgáltatás API-verzióját; jelenleg az egyetlen elfogadott érték `2019-07-01-preview`a, és a változás változhat. |
| `resource` | Egy lekérdezési karakterlánc paraméter, amely a cél erőforrás alkalmazás-azonosító URI azonosítóját jelzi. Ez a kiállított jogkivonat `aud` (célközönség) jogcímeként jelenik meg. Ez a példa jogkivonatot kér a Azure Key Vault eléréséhez, amelynek az alkalmazás https://keyvault.azure.com/ -azonosító URI-ja. |
| `Secret` | Egy HTTP-kérelem fejlécének mezője, amelyet a Service Fabric felügyelt Identity jogkivonat szolgáltatás igényel Service Fabric-szolgáltatásokhoz a hívó hitelesítéséhez. Ezt az értéket az SF Runtime a MSI_SECRET környezeti változón keresztül írja elő. |


Példa a válaszra:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
ahol

| Elem | Leírás |
| ------- | ----------- |
| `token_type` | A jogkivonat típusa; Ebben az esetben egy "tulajdonos" hozzáférési token, ami azt jelenti, hogy a token tulajdonosa ("birtokosa") a jogkivonat tárgya. |
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos REST API hívásakor a jogkivonat a `Authorization` kérelem fejléc mezőjébe ágyazva "tulajdonos" tokenként van beágyazva, ami lehetővé teszi, hogy az API hitelesítse a hívót. | 
| `expires_on` | A hozzáférési token lejárati időbélyegzője; a (z) "1970-01-01T0:0: 0z UTC" számú másodpercet jelöli, és megfelel `exp` a jogkivonat jogcímenek. Ebben az esetben a jogkivonat a 2019-08-08T06:10:11 + 00:00 (az RFC 3339-ben) lejár|
| `resource` | Az erőforrás, amelyhez a hozzáférési jogkivonat ki lett állítva, a kérelem `resource` lekérdezési karakterlánc paraméterén keresztül lett megadva; a jogkivonat "AUD" jogcímének felel meg. |


## <a name="acquiring-an-access-token-using-c"></a>Hozzáférési jogkivonat beszerzése a használatávalC#
A fenti a C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Key Vault elérése Service Fabric alkalmazásban felügyelt identitás használatával
Ez a minta a fentiekben a felügyelt identitás használatával mutatja be egy Key Vault tárolt titok elérését.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }
```

## <a name="error-handling"></a>Hibakezelés
A HTTP-válasz fejlécének Állapotkód mezőjében a kérelem sikerességi állapota látható. a "200 OK" állapot jelzi a sikerességet, és a válasz a fent leírtak szerint tartalmazza a hozzáférési jogkivonatot. Az alábbiakban a lehetséges hibák rövid enumerálása látható.

| Állapotkód | Hiba oka | Kezelés |
| ----------- | ------------ | ------------- |
| 404 nem található. | Ismeretlen hitelesítési kód, vagy az alkalmazás nem rendelt hozzá felügyelt identitást. | Helyesbítse az alkalmazás telepítőjét vagy a jogkivonat-beszerzési kódot. |
| 429 túl sok kérés. |  Elérte a szabályozási korlátot, amely HRE vagy SF alapján lett kiszabva. | Próbálja megismételni az exponenciális leállítási. Lásd az alábbi útmutatást. |
| 4xx hiba a kérelemben. | Egy vagy több kérelem paramétere helytelen volt. | Ne próbálkozzon újra.  További információért tekintse meg a hiba részleteit.  a 4xx hibák a tervezési idejű hibák.|
| 5xx hiba a szolgáltatástól. | A felügyelt identitás alrendszer vagy Azure Active Directory átmeneti hibát adott vissza. | Rövid idő múlva nyugodtan próbálkozhat. Az újrapróbálkozáskor a szabályozás feltételeit (429) is elérheti.|

Ha hiba történik, a megfelelő HTTP-válasz törzse tartalmaz egy JSON-objektumot, amely a hiba részleteit tartalmazza:

| Elem | Leírás |
| ------- | ----------- |
| code | Hibakód. |
| correlationId | A hibakereséshez használható korrelációs azonosító. |
| message | Hiba részletes leírása. **A hibákkal kapcsolatos leírások bármikor megváltoztathatók. A nem függ magától a hibaüzenettől.**|

Minta hiba:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

A következő lista a felügyelt identitásokra jellemző jellemző Service Fabric hibák listáját tartalmazza:

| Kód | Message | Leírás | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | A titkos kód nem található a kérelem fejlécében. | A hitelesítési kódot nem adta meg a kérelem. | 
| ManagedIdentityNotFound | Nem található felügyelt identitás a megadott alkalmazás-gazdagéphez. | Az alkalmazás nem rendelkezik identitással, vagy a hitelesítési kód ismeretlen. |
| ArgumentNullOrEmpty | Az "erőforrás" paraméter nem lehet null értékű vagy üres karakterlánc. | A kérelemben nincs megadva az erőforrás (célközönség). |
| InvalidApiVersion | A (z) "" API-verzió nem támogatott. A támogatott verzió: "2019-07-01-Preview". | Hiányzó vagy nem támogatott API-verzió van megadva a kérelem URI azonosítójában. |
| InternalServerError | Hiba történt. | Hiba történt a felügyelt identitás alrendszerében, valószínűleg a Service Fabric veremön kívül. A legvalószínűbb ok az erőforráshoz megadott helytelen érték (a záró "/" jelölést kell megadnia?) | 

## <a name="retry-guidance"></a>Újrapróbálkozási útmutató 

Általában az egyetlen újrapróbálkozást lehetővé tevő hibakód 429 (túl sok kérés); a belső kiszolgálói hibák/5xx-hibakódok valószínűleg újrapróbálkoznak, de az ok lehet állandó. 

A szabályozás korlátozásai a felügyelt identitási alrendszerre irányuló hívások számára vonatkoznak – konkrétan a "felsőbb rétegbeli" függőségek (felügyelt identitás Azure szolgáltatás vagy a biztonságos jogkivonat szolgáltatás). Service Fabric gyorsítótárazza a tokeneket a folyamat különböző szintjein, de az érintett összetevők elosztott jellege miatt előfordulhat, hogy a hívó inkonzisztens szabályozási válaszokat észlel (például az alkalmazás egy csomópontján/példányán, de nem egy másik csomópont, miközben egy tokent kér ugyanahhoz az identitáshoz.) Ha a szabályozási feltétel be van állítva, az azonos alkalmazásból érkező további kérések meghiúsulnak az 429-as HTTP-állapotkód (túl sok kérés), amíg a feltétel nem törlődik.  

Azt javasoljuk, hogy a sávszélesség-szabályozás miatti kérelmeket az alábbi módon próbálja megismételni egy exponenciális leállítási: 

| Hívási index | Művelet a 429-es fogadáshoz | 
| --- | --- | 
| 1 | Várjon 1 másodpercet, és próbálkozzon újra |
| 2 | Várjon 2 másodpercet, és próbálkozzon újra |
| 3 | Várjon 4 másodpercet, és próbálkozzon újra |
| 4 | Várjon 8 másodpercet, és próbálkozzon újra |
| 4 | Várjon 8 másodpercet, és próbálkozzon újra |
| 5 | Várjon 16 másodpercet, és próbálkozzon újra |

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói
Tekintse meg az Azure ad- [hitelesítést támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-msi.md) az Azure ad-t támogató erőforrások listáját, valamint a hozzájuk tartozó erőforrás-azonosítókat.

## <a name="next-steps"></a>További lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)

## <a name="see-also"></a>Lásd még

* [Felügyelt identitások támogatásának](./concepts-managed-identity.md) áttekintése az Azure Service Fabric

* [Új telepítése](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-fürt felügyelt identitások támogatásával 

* [Felügyelt identitás engedélyezése](./configure-existing-cluster-enable-managed-identity-token-service.md) meglévő Azure Service Fabric-fürtben