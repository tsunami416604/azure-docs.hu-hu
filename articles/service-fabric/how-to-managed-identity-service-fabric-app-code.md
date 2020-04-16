---
title: Felügyelt identitás használata alkalmazással
description: Felügyelt identitások használata az Azure Service Fabric alkalmazáskódjában az Azure Services eléréséhez.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: cbdb1190ec3238a6accd34db3025e08c194d60b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415623"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>A Service Fabric-alkalmazások felügyelt identitásának kihasználása az Azure-szolgáltatások eléréséhez

A Service Fabric-alkalmazások kihasználhatják a felügyelt identitásokat az Azure Active Directory-alapú hitelesítést támogató azure-erőforrások eléréséhez. Az alkalmazások beszerezhetnek egy [hozzáférési jogkivonatot,](../active-directory/develop/developer-glossary.md#access-token) amely az identitását képviseli, amely rendszer- vagy felhasználó-hozzárendelt lehet, és "tulajdonosi" jogkivonatként használhatják egy másik szolgáltatás , más néven [védett erőforrás-kiszolgáló](../active-directory/develop/developer-glossary.md#resource-server)hitelesítéséhez. A jogkivonat a Service Fabric-alkalmazáshoz rendelt identitást jelöli, és csak az azure-erőforrásoknak (beleértve az SF-alkalmazásokat is) lesz kiadva, amelyek megosztják az identitást. A [felügyelt identitások részletes](../active-directory/managed-identities-azure-resources/overview.md) leírását, valamint a rendszerhez rendelt és a felhasználó által hozzárendelt identitások közötti különbséget a felügyelt identitások részletes leírását olvassa el. A cikkben egy felügyelt identitás-alapú Service Fabric-alkalmazást fogunk az [ügyfélalkalmazásként](../active-directory/develop/developer-glossary.md#client-application) hivatkozni.

> [!IMPORTANT]
> A felügyelt identitás egy Azure-erőforrás és egy egyszerű szolgáltatás közötti társítást jelöli az erőforrást tartalmazó előfizetéshez társított megfelelő Azure AD-bérlőben. Mint ilyen, a Service Fabric környezetében felügyelt identitások csak az Azure-erőforrásokként üzembe helyezett alkalmazások támogatottak. 

> [!IMPORTANT]
> A Service Fabric-alkalmazás felügyelt identitásának használata előtt az ügyfélalkalmazásnak hozzáférést kell biztosítani a védett erőforráshoz. Tekintse meg az [Azure-szolgáltatások listáját, amelyek támogatják az Azure AD-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) a támogatás ellenőrzéséhez, majd a megfelelő szolgáltatás dokumentációját a szükséges erőforrásokhoz való identitás-hozzáférés engedélyezéséhez szükséges konkrét lépésekért. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Hozzáférési jogkivonat beszerzése REST API használatával
A felügyelt identitásra engedélyezett fürtökben a Service Fabric futásidejű egy localhost-végpontot tesz elérhetővé, amelyet az alkalmazások hozzáférési jogkivonatok beszerzésére használhatnak. A végpont a fürt minden csomópontján elérhető, és az adott csomópont összes entitása számára elérhető. A jogosult hívók hozzáférési jogkivonatokat kaphatnak a végpont hívásával és egy hitelesítési kód bemutatásával; a kódot a Service Fabric futásideje hozza létre minden egyes egyes egyes szolgáltatáskód-csomag aktiválásához, és az adott szolgáltatáskódcsomagot üzemeltető folyamat élettartamához van kötve.

Pontosabban a felügyelt identitás-kompatibilis Service Fabric-szolgáltatás környezete a következő változókkal lesz elmezve:
- 'IDENTITY_ENDPOINT': a szolgáltatás felügyelt identitásának megfelelő localhost végpont
- 'IDENTITY_HEADER': a szolgáltatást az aktuális csomóponton jelölő egyedi hitelesítési kód
- 'IDENTITY_SERVER_THUMBPRINT': A szolgáltatásháló felügyelt identitáskiszolgálójának ujjlenyomata

> [!IMPORTANT]
> Az alkalmazáskódnak bizalmas adatként kell tekintenie a "IDENTITY_HEADER" környezeti változó értékét – nem szabad naplózni vagy más módon terjeszteni. A hitelesítési kód nem rendelkezik értékkel a helyi csomóponton kívül, vagy a szolgáltatás tői a szolgáltatás által üzemeltetett folyamat leállítása után, de a Service Fabric szolgáltatás identitását képviseli, és így ugyanolyan óvintézkedéseket kell kezelni, mint maga a hozzáférési jogkivonat.

Jogkivonat beszerzéséhez az ügyfél a következő lépéseket hajtja végre:
- URI-t hoz, ha összefűzi a felügyelt identitásvégpontot (IDENTITY_ENDPOINT érték) az API-verzióval és a jogkivonathoz szükséges erőforrással (közönséggel).
- get http(s) kérelmet hoz létre a megadott URI-hoz
- hozzáadja a megfelelő kiszolgálói tanúsítvány-ellenőrzési logikát
- hozzáadja a hitelesítési kódot (IDENTITY_HEADER értéket) fejlécként a kérelemhez
- benyújtja a kérelmet

A sikeres válasz egy JSON-hasznos adatot tartalmaz, amely az eredményül kapott hozzáférési jogkivonatot, valamint az azt leíró metaadatokat tartalmaz. A sikertelen válasz a hiba magyarázatát is tartalmazza. A hibakezelésről az alábbiakban olvashat.

A hozzáférési jogkivonatokat a Service Fabric különböző szinteken (csomópont, fürt, erőforrás-szolgáltató szolgáltatás) gyorsítótárazja, így a sikeres válasz nem feltétlenül jelenti azt, hogy a jogkivonat közvetlenül a felhasználói alkalmazás kérésére lett kiadva. A jogkivonatok élettartamuknál rövidebb ideig lesznek gyorsítótárazva, így egy alkalmazás garantáltan kap egy érvényes jogkivonatot. Javasoljuk, hogy az alkalmazáskód gyorsítótárazza magát minden általa beszerzett hozzáférési jogkivonatot; a gyorsítótárazási kulcsnak tartalmaznia kell (a közönség származtatását). 


> [!NOTE]
> Az egyetlen elfogadott API-verzió jelenleg `2019-07-01-preview`, és változhat.

Mintakérelem:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
ahol:

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy adatokat szeretne beolvasni a végpontból. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | A Service Fabric-alkalmazások felügyelt identitásvégpontja, amelyet a IDENTITY_ENDPOINT környezeti változó n keresztül biztosít. |
| `api-version` | Egy lekérdezési karakterlánc-paraméter, amely a felügyelt identitástoken-szolgáltatás API-verzióját adja meg; jelenleg az egyetlen `2019-07-01-preview`elfogadott érték a , és változhat. |
| `resource` | A lekérdezési karakterlánc paraméter, amely a célerőforrás alkalmazásazonosítóuri-ját jelzi. Ez a kiadott jogkivonat `aud` (közönség) jogcímének lesz a megfelelő jogcím. Ebben a példában egy jogkivonatot kér az Azure Key\/Vault eléréséhez, amelynek alkalmazásazonosító URI-ja https: /vault.azure.net/. |
| `Secret` | Egy HTTP-kérelem fejléce mező, amelyet a Service Fabric felügyelt identitásjogkivonat-szolgáltatás a Service Fabric-szolgáltatások a hívó hitelesítéséhez szükséges. Ezt az értéket az SF futásidejű IDENTITY_HEADER környezeti változón keresztül biztosítja. |


Mintaválasz:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
ahol:

| Elem | Leírás |
| ------- | ----------- |
| `token_type` | A token típusa; ebben az esetben a "tulajdonos" hozzáférési jogkivonat, ami azt jelenti, hogy a jogkivonat előadója ("tulajdonos") a jogkivonat tervezett tárgya. |
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos REST API hívása kor a `Authorization` jogkivonat "tulajdonosi" jogkivonatként van beágyazva a kérelem fejlécmezőjébe, lehetővé téve az API számára a hívó hitelesítését. | 
| `expires_on` | A hozzáférési jogkivonat lejárati időbélyege; az "1970-01-01T0:0:0Z UTC" között eltelt másodpercek számaként jelenik meg, és megfelel a jogkivonat jogcímének. `exp` Ebben az esetben a token 2019-08-08T06:10:11+00:00 (Az RFC 3339-ben) lejár|
| `resource` | Az az erőforrás, amelyhez a hozzáférési `resource` jogkivonatot kiadták, a kérelem lekérdezési karakterlánc-paraméterén keresztül megadott; megfelel a token "aud" követelésének. |


## <a name="acquiring-an-access-token-using-c"></a>Hozzáférési jogkivonat beszerzése C használatával #
A fenti lesz, a C #:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

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
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>A Key Vault elérése egy Service Fabric-alkalmazásból felügyelt identitás használatával
Ez a minta a fentiekből épül fel a key vaultban felügyelt identitás használatával tárolt titkos kulcs elérésének bemutatására.

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

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
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

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Hibakezelés
A HTTP-válasz fejlécének "állapotkód" mezője a kérelem sikeres állapotát jelzi; a "200 OK" állapot sikeresnek számít, és a válasz tartalmazza a fent leírt hozzáférési jogkivonatot. A következőkben a lehetséges hibaválaszok rövid felsorolása szerepel.

| Állapotkód | Hiba oka | Hogyan kell kezelni |
| ----------- | ------------ | ------------- |
| 404 Nem található. | Ismeretlen hitelesítési kód, vagy az alkalmazáshoz nem volt felügyelt identitás rendelve. | Helyesbítse az alkalmazás beállítását vagy a tokenbeszerzési kódot. |
| 429 Túl sok kérés. |  Elérte az AAD vagy az SF által bevezetett fojtószelep-korlátot. | Próbálkozzon újra exponenciális visszalépéssel. Lásd az alábbi útmutatást. |
| 4xx Hiba kérésre. | Egy vagy több kérelemparaméter helytelen volt. | Ne próbálkozzon újra.  További információkért vizsgálja meg a hiba részleteit.  A 4xx hibák tervezési idejű hibák.|
| 5xx Hiba a szolgáltatásból. | A felügyelt identitásalrendszer vagy az Azure Active Directory átmeneti hibát adott vissza. | Ez biztos, hogy próbálja meg újra egy rövid idő után. Az újrapróbálkozáskor a fojtási állapot (429) is előfordulhat.|

Hiba esetén a megfelelő HTTP-választörzs egy JSON-objektumot tartalmaz a következő hibarészleteivel:

| Elem | Leírás |
| ------- | ----------- |
| code | Hibakód. |
| correlationId | A hibakereséshez használható korrelációs azonosító. |
| message | A hiba részletes leírása. **A hibaleírások bármikor változhatnak. Ne függj magától a hibaüzenettől.**|

Mintahiba:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Az alábbiakban a felügyelt identitásokra jellemző tipikus Service Fabric-hibák listája látható:

| Kód | Üzenet | Leírás | 
| ----------- | ----- | ----------------- |
| SecretHeaderNemFound | Titkos titok nem található a kérelem fejlécében. | A hitelesítési kód nem lett megadva a kérelemhez. | 
| ManagedIdentityNemFound | A felügyelt identitás nem található a megadott alkalmazásállomáshoz. | Az alkalmazás nem rendelkezik identitással, vagy a hitelesítési kód ismeretlen. |
| ArgumentNullOrEmpty | Az "erőforrás" paraméter nem lehet null vagy üres karakterlánc. | Az erőforrás (közönség) nem lett megadva a kérelemben. |
| InvalidApiVersion | Az api-version '' nem támogatott. A támogatott verzió a "2019-07-01-preview". | Hiányzó vagy nem támogatott API-verzió a kérelem URI-jában megadva. |
| InternalServerError | Hiba történt. | Hiba történt a felügyelt identitás alrendszerben, valószínűleg a Service Fabric-veremen kívül. A legvalószínűbb ok az erőforráshoz megadott helytelen érték (ellenőrizze, hogy nincs-e záró "/"?) | 

## <a name="retry-guidance"></a>Újrapróbálkozási útmutató 

Az egyetlen újrapróbálható hibakód általában 429 (túl sok kérés); belső szerver hibák/5xx hibakódok újrapróbálkozásra lehetnek, bár az ok állandó lehet. 

A sávszélesség-szabályozási korlátok a felügyelt identitás alrendszer – különösen a "upstream" függőségek (a felügyelt identitás Azure-szolgáltatás vagy a biztonságos jogkivonat-szolgáltatás) kezdeményezett hívások száma vonatkoznak. A Service Fabric gyorsítótárazza a jogkivonatokat a folyamat különböző szintjein, de az érintett összetevők elosztott jellege miatt a hívó inkonzisztens szabályozási válaszokat tapasztalhat (azaz egy alkalmazás egyetlen csomópontján/példányán szabályozást kap, de nem egy másik csomóponton, miközben jogkivonatot kér ugyanarra az identitásra.) Ha a szabályozási feltétel be van állítva, az ugyanabból az alkalmazásból származó további kérelmek sikertelenek lehetnek a 429-es HTTP-állapotkóddal (túl sok kérelem), amíg a feltétel nincs törölve.  

A szabályozás miatt sikertelen kérelmek et exponenciális visszalépéssel érdemes újra megpróbálni, az alábbiak szerint: 

| Hívási index | A fogadásra vonatkozó intézkedés 429 | 
| --- | --- | 
| 1 | Várjon 1 másodpercet, és próbálkozzon újra |
| 2 | Várjon 2 másodpercet, majd próbálkozzon újra |
| 3 | Várjon 4 másodpercet, és próbálkozzon újra |
| 4 | Várjon 8 másodpercet, és próbálkozzon újra |
| 4 | Várjon 8 másodpercet, és próbálkozzon újra |
| 5 | Várjon 16 másodpercet, és próbálkozzon újra |

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói
Tekintse meg az [Azure-hitelesítést támogató Azure-szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-msi.md) az Azure AD-t támogató erőforrások listáját és a megfelelő erőforrás-azonosítókat.

## <a name="next-steps"></a>További lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás elérésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
