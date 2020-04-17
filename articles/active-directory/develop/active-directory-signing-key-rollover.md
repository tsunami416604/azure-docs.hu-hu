---
title: Aláírási kulcsváltás az Azure AD-ben
description: Ez a cikk az Azure Active Directory aláírási kulcs váltásával kapcsolatos gyakorlati tanácsait ismerteti
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: e0a38eb03df3d1da64172842fb6eca3cd762f9cd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537236"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Aláírási kulcsváltás az Azure Active Directoryban
Ez a cikk ismerteti, mit kell tudni a nyilvános kulcsokat, amelyek az Azure Active Directoryban (Azure AD) a biztonsági jogkivonatok aláírásához használt. Fontos megjegyezni, hogy ezek a kulcsok rendszeres időközönként megfordulnak, és vészhelyzetben azonnal átállíthatók. Az Azure AD-t használó összes alkalmazásnak képesnek kell lennie a kulcsváltási folyamat programozott kezelésére, vagy egy időszakos manuális váltási folyamat létrehozásához. Folytatódik olvasó, hogy megértsék, hogyan működnek a kulcsok, hogyan lehet felmérni a váltás hatását az alkalmazás, és hogyan frissítheti az alkalmazást, vagy hozzon létre egy időszakos manuális átgörgetési folyamat kezelésére kulcs váltás, ha szükséges.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Az aláíró kulcsok áttekintése az Azure AD-ben
Az Azure AD az iparági szabványokra épülő nyilvános kulcsú kriptográfia segítségével teremti meg a bizalmat saját maga és az azt használó alkalmazások között. A gyakorlatban ez a következő módon működik: Az Azure AD egy aláíró kulcsot használ, amely egy nyilvános és személyes kulcspárból áll. Amikor egy felhasználó bejelentkezik egy olyan alkalmazásba, amely az Azure AD-t használja a hitelesítéshez, az Azure AD létrehoz egy biztonsági jogkivonatot, amely a felhasználó adatait tartalmazza. Ezt a jogkivonatot az Azure AD a személyes kulcsával írja alá, mielőtt visszaküldené az alkalmazásnak. Annak ellenőrzéséhez, hogy a jogkivonat érvényes-e, és az Azure AD-ből származik-e, az alkalmazásnak érvényesítenie kell a jogkivonat aláírását a bérlő [OpenID Connect felderítési dokumentumában](https://openid.net/specs/openid-connect-discovery-1_0.html) vagy saml/WS-Fed [összevonási metaadat-dokumentumban](../azuread-dev/azure-ad-federation-metadata.md)található Azure AD által elérhetővé tett nyilvános kulcs használatával.

Biztonsági okokból az Azure AD aláíró kulcs a rendszeres időközönként, és vészhelyzet esetén azonnal átlehet görgetni. Minden olyan alkalmazás, amely integrálja az Azure AD-vel kell készíteni egy kulcs váltás esemény nem számít, milyen gyakran fordulhat elő. Ha nem, és az alkalmazás megkísérli egy lejárt kulcs használatával ellenőrizze a jogkivonat aláírása, a bejelentkezési kérelem sikertelen lesz.

Az OpenID Connect felderítési dokumentumban és az összevonási metaadat-dokumentumban mindig egynél több érvényes kulcs érhető el. Az alkalmazásnak készen kell állnia a dokumentumban megadott kulcsok bármelyikének használatára, mivel az egyik kulcs hamarosan elgördülhet, egy másik lehet a csere, és így tovább.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Hogyan lehet felmérni, hogy az Ön kérelmét érinti-e, és mi a teendő?
Az, hogy az alkalmazás hogyan kezeli a kulcsváltást, olyan változóktól függ, mint például az alkalmazás típusa vagy a használt identitásprotokoll és kódtár. Az alábbi szakaszok felmérik, hogy a kulcsváltás érinti-e a leggyakoribb alkalmazástípusokat, és útmutatást ad az alkalmazás automatikus váltásának támogatásához vagy a kulcs manuális frissítéséhez.

* [Erőforrásokeléréséhez hozzáférő natív ügyfélalkalmazások](#nativeclient)
* [Erőforrásokhoz hozzáférő webalkalmazások / API-k](#webclient)
* [Az Erőforrásokat védő és az Azure App Services használatával készült webalkalmazások / API-k](#appservices)
* [A .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication köztes szoftverrel működő erőforrásokat védő webalkalmazások / API-k](#owin)
* [A .NET Core OpenID Connect vagy jwtBearerAuthentication köztes szoftverrel erőforrásokat védő webalkalmazások / API-k](#owincore)
* [A Node.js passport-azure-ad modult használó erőforrásokat védő webalkalmazások / API-k](#passport)
* [Az erőforrásokat védő és a Visual Studio 2015-ös vagy újabb verzióival létrehozott webalkalmazások / API-k](#vs2015)
* [Erőforrásokat védő és a Visual Studio 2013-mal létrehozott webalkalmazások](#vs2013)
* Az erőforrásokat védő és a Visual Studio 2013-mal létrehozott webes API-k
* [Erőforrásokat védő és a Visual Studio 2012-vel létrehozott webalkalmazások](#vs2012)
* [Erőforrásokat védő és a Visual Studio 2010 2008 o segítségével a Windows Identity Foundation használatával létrehozott webalkalmazások](#vs2010)
* [Webalkalmazások / API-k, amelyek más könyvtárak használatával védik az erőforrásokat, vagy manuálisan valósítják meg a támogatott protokollok bármelyikét](#other)

Ez az útmutató **nem** alkalmazható:

* Az Azure AD alkalmazásgalériából hozzáadott alkalmazások (beleértve az egyéni) külön útmutatást kapnak az aláírási kulcsoktekintetében. [További információ.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Az alkalmazásproxyn keresztül közzétett helyszíni alkalmazásoknak nem kell aggódniuk a kulcsok aláírása miatt.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Erőforrásokeléréséhez hozzáférő natív ügyfélalkalmazások
Azok az alkalmazások, amelyek csak erőforrásokhoz férnek hozzá (azaz Microsoft Graph, KeyVault, Outlook API és más Microsoft API-k) általában csak szerezzen be egy jogkivonatot, és adja át az erőforrás tulajdonosa. Mivel nem védik az erőforrásokat, nem ellenőrzik a jogkivonatot, ezért nem kell biztosítaniuk, hogy megfelelően alá legyen írva.

A natív ügyfélalkalmazások , akár asztali, akár mobil eszközök, ebbe a kategóriába tartoznak, és így a görgetés nem érinti őket.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Erőforrásokhoz hozzáférő webalkalmazások / API-k
Azok az alkalmazások, amelyek csak erőforrásokhoz férnek hozzá (azaz Microsoft Graph, KeyVault, Outlook API és más Microsoft API-k) általában csak szerezzen be egy jogkivonatot, és adja át az erőforrás tulajdonosa. Mivel nem védik az erőforrásokat, nem ellenőrzik a jogkivonatot, ezért nem kell biztosítaniuk, hogy megfelelően alá legyen írva.

A csak alkalmazás-folyamatot használó webes alkalmazások és webes API-k (ügyfélhitelesítő adatok / ügyféltanúsítvány) ebbe a kategóriába tartoznak, és így a váltás nem érinti őket.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Az Erőforrásokat védő és az Azure App Services használatával készült webalkalmazások / API-k
Az Azure App Services hitelesítési / engedélyezési (EasyAuth) funkciója már rendelkezik a kulcsváltás automatikus kezeléséhez szükséges logikával.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>A .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication köztes szoftverrel működő erőforrásokat védő webalkalmazások / API-k
Ha az alkalmazás a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication köztes szoftvert használja, már rendelkezik a kulcsváltás automatikus kezeléséhez szükséges logikával.

Megerősítheti, hogy az alkalmazás használja ezeket a következő kódrészletek az alkalmazás Startup.cs vagy Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>A .NET Core OpenID Connect vagy jwtBearerAuthentication köztes szoftverrel erőforrásokat védő webalkalmazások / API-k
Ha az alkalmazás a .NET Core OWIN OpenID Connect vagy JwtBearerAuthentication köztes szoftvert használja, már rendelkezik a kulcsváltás automatikus kezeléséhez szükséges logikával.

Megerősítheti, hogy az alkalmazás használja ezeket a következő kódrészletek az alkalmazás Startup.cs vagy Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>A Node.js passport-azure-ad modult használó erőforrásokat védő webalkalmazások / API-k
Ha az alkalmazás a Node.js passport-ad modult használja, már rendelkezik a kulcsváltás automatikus kezeléséhez szükséges logikával.

Megerősítheti, hogy az alkalmazás passport-ad megkeresi a következő kódrészletet az alkalmazás app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Az erőforrásokat védő és a Visual Studio 2015-ös vagy újabb verzióival létrehozott webalkalmazások / API-k
Ha az alkalmazás a Visual Studio 2015-ben vagy újabb verzióban egy webalkalmazás-sablon használatával készült, és a **Módosítási hitelesítés** menüben a **Munkahelyi vagy iskolai fiókok lehetőséget** választotta, már rendelkezik a kulcsváltás automatikus kezeléséhez szükséges logikával. Ez az OWIN OpenID Connect köztes szoftverbe ágyazott logika lekéri és gyorsítótárazza a kulcsokat az OpenID Connect felderítési dokumentumból, és rendszeresen frissíti azokat.

Ha manuálisan adta hozzá a hitelesítést a megoldáshoz, előfordulhat, hogy az alkalmazás nem rendelkezik a szükséges kulcsváltási logikával. Meg kell írni, hogy magát, vagy kövesse a lépéseket a [webes alkalmazások / API-k segítségével bármely más könyvtárak vagy manuálisan megvalósítása bármely támogatott protokollok](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Erőforrásokat védő és a Visual Studio 2013-mal létrehozott webalkalmazások
Ha az alkalmazás a Visual Studio 2013-ban egy webalkalmazás-sablon használatával készült, és a **Szervezeti fiókok lehetőséget** választotta a **Módosításhitelesítés** menüből, már rendelkezik a kulcsváltás automatikus kezeléséhez szükséges logikával. Ez a logika a szervezet egyedi azonosítóját és az aláíró kulcs adatait a projekthez társított két adatbázistáblában tárolja. Az adatbázis kapcsolati karakterlánca a projekt Web.config fájljában található.

Ha manuálisan adta hozzá a hitelesítést a megoldáshoz, előfordulhat, hogy az alkalmazás nem rendelkezik a szükséges kulcsváltási logikával. Saját kezűleg kell írnia, vagy követnie kell a webes alkalmazások / API-k lépéseit [bármely más kódtár használatával, vagy manuálisan kell végrehajtania a támogatott protokollok bármelyikét.](#other). .

A következő lépések segítségével ellenőrizheti, hogy a logika megfelelően működik-e az alkalmazásban.

1. A Visual Studio 2013-ban nyissa meg a megoldást, majd kattintson a jobb oldali ablak **Kiszolgálókezelő** fülére.
2. Bontsa ki **az Adatkapcsolatok**, **A Alapértelmezettkapcsolat**, majd **a Táblák csomópontot.** Keresse meg az **IssuingAuthorityKeys** táblát, kattintson rá a jobb gombbal, majd kattintson **a Táblaadatok megjelenítése parancsra.**
3. Az **IssuingAuthorityKeys** táblában legalább egy sor lesz, amely megfelel a kulcs ujjlenyomatértékének. Törölje a tábla sorait.
4. Kattintson a jobb gombbal a **Bérlők** táblára, majd kattintson a **Táblaadatok megjelenítése parancsra.**
5. A **Bérlők** táblában lesz legalább egy sor, amely megfelel egy egyedi címtár-bérlői azonosító. Törölje a tábla sorait. Ha nem törli a sorokat mind a **Bérlők** táblában, mind **az IssuingAuthorityKeys** táblában, futásidőben hibaüzenet et fog kapni.
6. Hozza létre és futtassa az alkalmazást. Miután bejelentkezett a fiókjába, leállíthatja az alkalmazást.
7. Térjen vissza a **Kiszolgálókezelőhöz,** és tekintse meg az **IssuingAuthorityKeys** és a Tenants tábla **értékeit.** Észre fogja venni, hogy a rendszer automatikusan újrabenépesítette őket az összevonási metaadat-dokumentumból származó megfelelő információkkal.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Az erőforrásokat védő és a Visual Studio 2013-mal létrehozott webes API-k
Ha létrehozott egy webes API-alkalmazást a Visual Studio 2013-ban a Webes API-sablon használatával, majd a **Szervezeti fiókok** lehetőséget választotta a **Módosítási hitelesítés** menüből, akkor már rendelkezik a szükséges logikával az alkalmazásban.

Ha manuálisan konfigurálta a hitelesítést, kövesse az alábbi utasításokat, hogy megtudja, hogyan konfigurálhatja a webes API-t a legfontosabb információk automatikus frissítésére.

A következő kódrészlet bemutatja, hogyan szerezheti be a legújabb kulcsokat az összevonási metaadat-dokumentumból, majd a [JWT-jogkivonat-kezelő](https://msdn.microsoft.com/library/dn205065.aspx) segítségével érvényesítheti a jogkivonatot. A kódrészlet feltételezi, hogy a saját gyorsítótárazási mechanizmust fogja használni a kulcs megőrzéséhez az Azure AD jövőbeli jogkivonatai érvényesítéséhez, legyen az egy adatbázis, konfigurációs fájl vagy más hol.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Erőforrásokat védő és a Visual Studio 2012-vel létrehozott webalkalmazások
Ha az alkalmazás a Visual Studio 2012-ben készült, valószínűleg az Identitás és az Access eszközt használta az alkalmazás konfigurálásához. Az is valószínű, hogy a [kiállítói névjegyzéket (VALIDating Issuer Registry, VINR)](https://msdn.microsoft.com/library/dn205067.aspx)használja. A VINR felelős a megbízható identitásszolgáltatók (Azure AD) és az általuk kiadott jogkivonatok érvényesítéséhez használt kulcsok karbantartásáért. A VINR megkönnyíti a Web.config fájlban tárolt legfontosabb információk automatikus frissítését a könyvtárhoz társított legújabb összevonási metaadat-dokumentum letöltésével, annak ellenőrzésével, hogy a konfiguráció elavult-e a legújabb dokumentummal, és szükség esetén frissíti az alkalmazást az új kulcs használatára.

Ha az alkalmazást a Microsoft által biztosított kódminták vagy forgatókönyv-dokumentációk bármelyikével hozta létre, a kulcsváltási logika már szerepel a projektben. Észre fogja venni, hogy az alábbi kód már létezik a projektben. Ha az alkalmazás még nem rendelkezik ezzel a logikával, kövesse az alábbi lépéseket a hozzáadáshoz, és ellenőrizze, hogy megfelelően működik-e.

1. A **Solution Explorer**alkalmazásban adjon hozzá hivatkozást a **System.IdentityModel** összeállításhoz a megfelelő projekthez.
2. Nyissa meg a **Global.asax.cs** fájlt, és adja hozzá a következőket az irányelvek használatával:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adja hozzá a következő módszert a **Global.asax.cs** fájlhoz:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. A **RefreshValidationSettings()** metódus meghívása a **Global.asax.cs Application_Start()** **metódusában** az alábbi módon:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Miután követte ezeket a lépéseket, az alkalmazás Web.config frissül a legújabb információkat az összevonási metaadat-dokumentum, beleértve a legújabb kulcsokat. Ez a frissítés minden alkalommal megtörténik, amikor az alkalmazáskészlet újrahasznosul az IIS-ben; alapértelmezés szerint az IIS úgy van beállítva, hogy 29 óránként újrahasznosítsa az alkalmazásokat.

Az alábbi lépéseket követve ellenőrizze, hogy a kulcsváltási logika működik-e.

1. Miután meggyőződött arról, hogy az alkalmazás a fenti kódot használja, nyissa meg a **Web.config** fájlt, és keresse meg a ** \<issuerNameRegistry>** blokkot, és keresse meg a következő néhány sort:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Az ** \<add thumbprint="">** beállításban módosítsa az ujjlenyomat értékét úgy, hogy bármely karaktert egy másikkarakterre cserél. Mentse a **Web.config** fájlt.
3. Készítse el az alkalmazást, majd futtassa azt. Ha el tudja végezni a bejelentkezési folyamatot, az alkalmazás sikeresen frissíti a kulcsot a szükséges adatok letöltésével a címtár összevonási metaadat-dokumentumából. Ha problémái vannak a bejelentkezéssel, győződjön meg arról, hogy az alkalmazás módosításai helyesek, olvassa el a Bejelentkezés hozzáadása a [webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) az Azure AD használatával cikket, vagy töltse le és vizsgálja meg a következő kódmintát: [Több-bérlős felhőalapú alkalmazás az Azure Active Directoryhoz.](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Erőforrásokat védő és a Visual Studio 2008-as vagy 2010-es, valamint a Windows Identity Foundation (WIF) 1.0-s és a .NET 3.5-ös v1.0-s alkalmazásával létrehozott webalkalmazások
Ha egy alkalmazást a WIF 1.0-s, nincs biztosított mechanizmus automatikusan frissíti az alkalmazás konfigurációját egy új kulcsot.

* *Legegyszerűbb módja* Használja a WIF SDK-ban található FedUtil eszközhasználatot, amely lekérheti a legújabb metaadat-dokumentumot, és frissítheti a konfigurációt.
* Frissítse az alkalmazást a .NET 4.5-re, amely tartalmazza a Rendszer névtérben található WIF legújabb verzióját. Ezután az [érvényesítői névjegyzék (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) segítségével automatikus frissítéseket hajthat végre az alkalmazás konfigurációjában.
* A kézikönyv váltását a jelen útmutató végén található utasításoknak megfelelően hajtsa végre.

A FedUtil használatára vonatkozó utasítások a konfiguráció frissítéséhez:

1. Ellenőrizze, hogy a Visual Studio 2008 vagy 2010 fejlesztői gépén telepítve van-e a WIF 1.0-s sdk-je. Letöltheti [innen,](https://www.microsoft.com/en-us/download/details.aspx?id=4451) ha még nem telepítette.
2. A Visual Studióban nyissa meg a megoldást, majd kattintson a jobb gombbal a megfelelő projektre, és válassza **az Összevonási metaadatok frissítése parancsot.** Ha ez a lehetőség nem érhető el, a FedUtil és/vagy a WIF 1.0-s SDK nincs telepítve.
3. A kérdésben válassza a **Frissítés** lehetőséget az összevonási metaadatok frissítésének megkezdéséhez. Ha hozzáfér a kiszolgálói környezethez, ahol az alkalmazás található, tetszés szerint használhatja a FedUtil [automatikus metaadat-frissítési ütemezőjét.](https://msdn.microsoft.com/library/ee517272.aspx)
4. A frissítési folyamat befejezéséhez kattintson a **Befejezés** gombra.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webalkalmazások / API-k, amelyek más könyvtárak használatával védik az erőforrásokat, vagy manuálisan valósítják meg a támogatott protokollok bármelyikét
Ha más könyvtárat használ, vagy manuálisan valósítja meg a támogatott protokollok bármelyikét, át kell tekintenie a tárat vagy a megvalósítást, hogy megbizonyosodjon arról, hogy a kulcsot az OpenID Connect felderítési dokumentumból vagy az összevonási metaadat-dokumentumból olvassa be. Ennek egyik módja, ha rákeres a kódra vagy a tár kódjára az OpenID felderítési dokumentumba vagy az összevonási metaadat-dokumentumba irányuló hívásokra.

Ha a kulcs tárolása valahol, vagy az alkalmazásban kódolt, manuálisan lekérheti a kulcsot, és ennek megfelelően frissítheti a manuális görgetést, a jelen útmutató dokumentum végén található utasítások nak megfelelően. **Erősen ajánlott, hogy az alkalmazás támogatja** az automatikus görgetés a jelen cikkben ismertetett megközelítések bármelyikének használatával a jövőbeli zavarok és a terhelés elkerülése érdekében, ha az Azure AD növeli a görgetési ütem, vagy egy vészhelyzeti sávon kívüli görgetés.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Hogyan tesztelje az alkalmazást annak megállapítására, hogy ez hatással lesz-e
Ellenőrizheti, hogy az alkalmazás támogatja-e az automatikus kulcsváltást a parancsfájlok letöltésével és a [GitHub-tárházban](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) található utasításokat követve.

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Manuális váltás végrehajtása, ha az alkalmazás nem támogatja az automatikus átgörgetést
Ha az alkalmazás **nem** támogatja az automatikus váltást, létre kell hoznia egy folyamatot, amely rendszeresen figyeli az Azure AD aláíró kulcsait, és ennek megfelelően manuális átgörgetést hajt végre. [Ez a GitHub-tárház](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) parancsfájlokat és utasításokat tartalmaz erre vonatkozóan.
