---
title: "Kulcsváltás bejelentkezés az Azure AD |} Microsoft Docs"
description: "Ez a cikk ismerteti, amelyek az aláírási kulcs átfordulási ajánlott eljárások az Azure Active Directory"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: krassk
editor: 
ms.assetid: ed964056-0723-42fe-bb69-e57323b9407f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 228bb9058537af1e4eb38207c376c2eb86aee68c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Az Azure Active Directoryban kulcsváltás aláírása
Ez a témakör ismerteti, mit kell tudnia a nyilvános kulcsok biztonsági jogkivonatok aláírásához használt Azure Active Directory (Azure AD). Fontos megjegyezni, hogy rendszeres időközönként, és vészhelyzet esetén a kulcsok helyettesítő volt állítva azonnal. Minden alkalmazás, amely használhatja az Azure Active Directory kell tudni programozott módon a kulcsváltás folyamat, vagy a rendszeres manuális helyettesítő-folyamatot. Olvasási megértése, hogyan működnek a kulcsokat, továbbra is az alkalmazás a Váltás hatásának értékelése és az alkalmazás frissítésére, vagy kezelje a kulcsváltás, szükség esetén rendszeres manuális váltása folyamatot.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Az Azure AD-aláírókulcsok áttekintése
Az Azure AD és az azt használó alkalmazások között megbízhatósági kapcsolat létrehozása az ipari szabványok épülő nyilvános kulcsú titkosítást használ. Gyakorlatilag, ez a következő módon működik: az Azure AD nyilvános és titkos kulcsból álló kulcspárt tartalmazó aláíró kulcsot használ. Ha egy felhasználó jelentkezik be valamely alkalmazás az Azure AD használ a hitelesítéshez, az Azure AD létrehoz egy biztonsági jogkivonatot, amely a felhasználó adatait tartalmazza. Ez a token aláírta az Azure AD használatával a titkos kulcsot, az alkalmazásnak elküldés előtt. Győződjön meg arról, hogy a jogkivonat érvényes, és ténylegesen kezdeményezésű az Azure AD, hogy az alkalmazás ellenőrizni kell a jogkivonat aláírása a nyilvános kulccsal, hogy a bérlő tartalmazza az Azure AD által elérhetővé tett [OpenID Connect felderítési dokumentum](http://openid.net/specs/openid-connect-discovery-1_0.html) vagy SAML/WS-Fed [összevonási metaadat-dokumentum](active-directory-federation-metadata.md).

Biztonsági okokból az Azure AD-aláíró kulcs összesíti rendszeres időközönként, és vészhelyzet esetén volt állítva azonnal. Bármely alkalmazás, amely az Azure AD kezelni egy kulcsváltás eseményt, függetlenül attól, milyen gyakran ez akkor fordulhat elő kell készíteni. Ha nem, és az alkalmazás megpróbálja ellenőrzése a jogkivonat aláírása lejárt-kulcsot használ, a bejelentkezési kérelem sikertelen lesz.

Mindig egynél több érvényes kulcs rendelkezésre áll az OpenID Connect felderítés és az összevonási metaadatok dokumentum. Az alkalmazás minden a dokumentumban szereplő kulcsok használatához felkészültnek kell lennie, egy kulcs amint lehet, hogy lesz vonva, mivel egy másik előfordulhat, hogy azok helyettesítéseivel, és így tovább.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Annak ellenőrzéséhez, ha az alkalmazás az érintett hogyan és mit kell tenni információk
Az alkalmazás kezelésének kulcsváltás attól függ, hogy változók, például az alkalmazás-vagy milyen identitás protokoll és a könyvtár lett megadva. Az alábbi szakaszok ellenőrzéséhez, hogy a leggyakoribb alkalmazások is hatással van a kulcsváltás, és útmutatást nyújtanak az alkalmazás automatikus helyettesítő támogatásához, vagy manuálisan frissítse a kulcs frissítése.

* [Natív ügyfélalkalmazások erőforrások elérése](#nativeclient)
* [Webalkalmazások / API-k erőforrások elérése](#webclient)
* [Webalkalmazások / API-k erőforrások védelméről, és segítségével az Azure App Service szolgáltatások](#appservices)
* [Webalkalmazások / API-k használata a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication köztes erőforrások védelme](#owin)
* [Webalkalmazások / API-k használata a .NET Core OpenID Connect vagy JwtBearerAuthentication köztes erőforrások védelme](#owincore)
* [Webalkalmazások / API-k Node.js passport-azure-ad-modullal erőforrások védelme](#passport)
* [Webalkalmazások / API-k erőforrások védelméről, és a Visual Studio 2015-öt vagy a Visual Studio 2017](#vs2015)
* [Webalkalmazások erőforrások védelméről, és a Visual Studio 2013 létre](#vs2013)
* [Webes API-k erőforrások védelméről, és a Visual Studio 2013 létre](#vs2013_webapi)
* [Erőforrások védelme és a Visual Studio 2012 létrehozott webes alkalmazások](#vs2012)
* [Webalkalmazások erőforrások védelméről, és a Visual Studio 2010, a Windows Identity Foundation használatával 2008 o létre](#vs2010)
* [Webalkalmazások / API-k bármely más könyvtárak segítségével, vagy manuálisan végrehajtási a támogatott protokollok erőforrások védelme](#other)

Ez az útmutató **nem** alkalmazható:

* Az Azure AD Application Gallery (beleértve az egyéni) hozzáadott alkalmazások rendelkeznek jelenítik aláírókulcsok külön útmutatást. [További információt.](../active-directory-sso-certs.md)
* A helyszíni alkalmazásproxy keresztül közzétett alkalmazás nem kell foglalkoznia az aláírási kulcsokat.

### <a name="nativeclient"></a>Natív ügyfélalkalmazások erőforrások elérése
Erőforrások (azaz csak elérő alkalmazások A Microsoft Graph, KeyVault, Outlook API és más Microsoft APIs) általában csak jogkivonat beszerzése, és adja át mentén az erőforrás tulajdonosa. Figyelembe véve, hogy azok nem védett erőforrások, nem vizsgálja meg a jogkivonatot, és emiatt nem kell annak érdekében, hogy helyesen alá van írva.

Natív ügyfélalkalmazások, asztali vagy hordozható, ebbe a kategóriába tartozik, és így nem érinti a váltáshoz.

### <a name="webclient"></a>Webalkalmazások / API-k erőforrások elérése
Erőforrások (azaz csak elérő alkalmazások A Microsoft Graph, KeyVault, Outlook API és más Microsoft APIs) általában csak jogkivonat beszerzése, és adja át mentén az erőforrás tulajdonosa. Figyelembe véve, hogy azok nem védett erőforrások, nem vizsgálja meg a jogkivonatot, és emiatt nem kell annak érdekében, hogy helyesen alá van írva.

Webalkalmazások és webes API-khoz, az alkalmazás csak a folyamat által használt (ügyfél-hitelesítő adatok / ügyféltanúsítvány), ebbe a kategóriába tartozik, és így nem érinti a váltáshoz.

### <a name="appservices"></a>Webalkalmazások / API-k erőforrások védelméről, és segítségével az Azure App Service szolgáltatások
Az Azure App Services hitelesítési / engedélyezési (EasyAuth) funkció már kulcsváltás automatikusan kezeléséhez szükséges logika.

### <a name="owin"></a>Webalkalmazások / API-k használata a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication köztes erőforrások védelme
Ha az alkalmazás nem használja a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication köztes kulcsváltás automatikusan kezeléséhez szükséges logika már rendelkezik.

Úgy ellenőrizheti, hogy az alkalmazás ezek alapján az alkalmazás Startup.cs vagy Startup.Auth.cs alábbi kódtöredékek bármelyikéhez használ

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

### <a name="owincore"></a>Webalkalmazások / API-k használata a .NET Core OpenID Connect vagy JwtBearerAuthentication köztes erőforrások védelme
Ha az alkalmazás nem használja a .NET Core OWIN OpenID Connect vagy JwtBearerAuthentication köztes, kulcsváltás automatikusan kezeléséhez szükséges logika már rendelkezik.

Úgy ellenőrizheti, hogy az alkalmazás ezek alapján az alkalmazás Startup.cs vagy Startup.Auth.cs alábbi kódtöredékek bármelyikéhez használ

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

### <a name="passport"></a>Webalkalmazások / API-k Node.js passport-azure-ad-modullal erőforrások védelme
Ha az alkalmazás nem használja a Node.js passport-ad-modullal, kulcsváltás automatikusan kezeléséhez szükséges logika már rendelkezik.

Úgy ellenőrizheti, hogy az alkalmazás passport-ad a következő kódrészletet a az alkalmazás app.js keresése

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webalkalmazások / API-k erőforrások védelméről, és a Visual Studio 2015-öt vagy a Visual Studio 2017
Ha az alkalmazást a Visual Studio 2015-öt vagy a Visual Studio 2017 webes alkalmazás sablon használatával lett létrehozva, és a kiválasztott **munkahelyi és iskolai fiókok** a a **hitelesítés módosítása** menü már rendelkezik a szükséges logika kulcsváltás automatikusan kezelni. A programot, az OWIN OpenID Connect köztes ágyazott kéri le, és gyorsítótárba helyezi azt a kulcsokat az OpenID Connect felderítési dokumentum, és rendszeresen frissülnek.

Ha manuálisan adta hozzá hitelesítési a megoldáshoz, az alkalmazás esetleg nincs szükség kulcsváltás logika. Jegyezze meg, vagy kövesse a kell [webalkalmazások / API-k bármely más könyvtárak segítségével, vagy manuálisan végrehajtási a támogatott protokollok.](#other).

### <a name="vs2013"></a>Webalkalmazások erőforrások védelméről, és a Visual Studio 2013 létre
Ha az alkalmazás a Visual Studio 2013 webes alkalmazás sablon használatával lett létrehozva, és a kiválasztott **munkahelyi és iskolai fiókok** a a **hitelesítés módosítása** menü már rendelkezik a szükséges logika kulcsváltás automatikusan kezelni. A működési elvet tárolja a projekthez tartozó két adatbázistáblák a szervezet egyedi azonosító és az aláírási kulcs adataira. A kapcsolati karakterláncot az adatbázishoz a projekt Web.config fájlban található.

Ha manuálisan adta hozzá hitelesítési a megoldáshoz, az alkalmazás esetleg nincs szükség kulcsváltás logika. Jegyezze meg, vagy kövesse a kell [webalkalmazások / API-k bármely más könyvtárak segítségével, vagy manuálisan végrehajtási a támogatott protokollok.](#other).

A következő lépésekkel ellenőrizze, hogy a programot az alkalmazás megfelelően működik.

1. A Visual Studio 2013, nyissa meg a megoldást, és kattintson a a **Server Explorer** a jobb oldali lapján.
2. Bontsa ki a **adatkapcsolatok**, **DefaultConnection**, majd **táblák**. Keresse meg a **IssuingAuthorityKeys** tábla, kattintson a jobb gombbal, és kattintson a **tábla adatok megjelenítése**.
3. Az a **IssuingAuthorityKeys** table, legalább egy sort, amely megfelel az ujjlenyomat értékének lesz. Törli a sorokat a táblában.
4. Kattintson a jobb gombbal a **bérlők** tábla, és kattintson a **tábla adatok megjelenítése**.
5. Az a **bérlők** table, legalább egy sort, amely felel meg egy egyedi könyvtárat bérlő azonosítója lesz. Törli a sorokat a táblában. Ha nem törli a sorokat is a **bérlők** tábla és **IssuingAuthorityKeys** tábla, akkor hibaüzenet fog futásidőben.
6. Hozza létre, és futtassa az alkalmazást. Rendelkezik bejelentkezést követően a fiókjához, leállíthatja, az alkalmazás.
7. Lépjen vissza a **Server Explorer** , és tekintse meg az értékeket a **IssuingAuthorityKeys** és **bérlők** tábla. Láthatja, hogy azok rendelkeznek lett automatikusan tölteni az összevonási metaadatok dokumentumból a megfelelő információkkal.

### <a name="vs2013"></a>Webes API-k erőforrások védelméről, és a Visual Studio 2013 létre
Ha a webes API-alkalmazás létrehozása a Visual Studio 2013 a webes API-sablonnal, és akkor kiválasztva **munkahelyi és iskolai fiókok** a a **hitelesítés módosítása** menü, akkor már rendelkezik a szükséges az alkalmazás logikáját.

Ha manuálisan konfigurált hitelesítést, kövesse az alábbi áttekintésével megismerheti, hogyan konfigurálhatja a webes API-t automatikusan frissíteni az kulcs adatait.

A következő kódrészletet bemutatja, hogyan kell a legújabb kulcsok beszerzése az összevonási metaadatok dokumentum, és használja a [JWT jogkivonat-kezelő](https://msdn.microsoft.com/library/dn205065.aspx) ellenőrzése a jogkivonat. A kódrészletet feltételezi, hogy használni a saját gyorsítótárazást megőrzése a kulcsot a jövőbeli érvényesítse az Azure AD legyenek az adatbázis, a konfigurációs fájlban vagy máshol.

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
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
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

### <a name="vs2012"></a>Erőforrások védelme és a Visual Studio 2012 létrehozott webes alkalmazások
Ha az alkalmazást a Visual Studio 2012 lett létrehozva, valószínűleg az identitás- és hozzáférés eszköz konfigurálására szolgáló alkalmazás. Is valószínű, hogy használja a [ellenőrzése kibocsátó neve beállításjegyzék (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). A VINR megbízható identitás-szolgáltatóktól (az Azure AD) kapcsolatos információkat, valamint az általuk kiállított jogkivonatokat érvényesítéséhez használt kulcsok karbantartásáért felelős. A VINR is megkönnyíti, hogy automatikusan frissíteni a Web.config fájlban úgy, hogy letölti a legújabb összevonási metaadat-dokumentum társított a címtárban tárolt kulcs adatainak ellenőrzése, ha a konfiguráció a legújabb dokumentumon elavult és frissíteni az alkalmazást az új kulcs használatához szükség szerint.

A Kódminták vagy a Microsoft által biztosított forgatókönyv dokumentáció segítségével az alkalmazás hozott létre, ha a kulcsváltás logika már szerepel a projekthez. Megfigyelheti, hogy az alábbi kódot a projekt már létezik. Ha az alkalmazás nem rendelkezik a programot, az alábbi lépésekkel veheti fel, és ellenőrizze, hogy megfelelően működik-e.

1. A **Megoldáskezelőben**, adjon hozzá egy hivatkozást, a **System.IdentityModel** szerelvény a megfelelő projektet.
2. Nyissa meg a **Global.asax.cs** fájlt, és adja hozzá a következő irányelvek segítségével:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adja hozzá a következő metódust a **Global.asax.cs** fájlt:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Hívása a **RefreshValidationSettings()** metódust a **Application_Start()** metódus a **Global.asax.cs** látható módon:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Ha követte ezeket a lépéseket, az alkalmazás Web.config frissíti a dokumentumból összevonási metaadatok, többek között a legújabb kulcsok a legújabb adatokkal. A frissítés történik, minden alkalommal, amikor az alkalmazáskészlet újraindul, az IIS-ben; Alapértelmezés szerint az IIS újrahasznosítása alkalmazások 29 óránként van beállítva.

Győződjön meg arról, hogy működik-e a kulcsváltás programot az alábbi lépésekkel.

1. Miután ellenőrizte, hogy az alkalmazás a fenti olyan helykódot alkalmaz, nyissa meg a **Web.config** fájlt, és keresse meg a  **<issuerNameRegistry>**  blokk, kifejezetten keres a következő néhány sor:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Az a  **<add thumbprint=””>**  beállításban módosítsa az ujjlenyomat értékét azáltal, hogy egy másik bármely karakter. Mentse a **Web.config** fájlt.
3. Építenie az alkalmazást, és futtassa azt. Ha a bejelentkezési folyamat elvégzése az alkalmazás sikeresen frissíti a kulcs úgy, hogy letölti a szükséges adatokat a címtár összevonási metaadatok dokumentumból. Ha bejelentkezik problémát tapasztal, győződjön meg arról, a módosítások az alkalmazás olvasásával helyesek a [hozzáadása bejelentkezés a webes alkalmazás használata az Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) a témakörben vagy letöltése, és tanulmányozza a következő példakód: [ Az Azure Active Directory több-Bérlős felhőalapú alkalmazásnál](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Erőforrások védelme és a Visual Studio 2008 vagy 2010 webes alkalmazások és a Windows Identity Foundation (WIF) 1.0-s a .NET 3.5
Ha egy alkalmazás WIF 1.0-s verziója található, nincs megadott mechanizmus, hogy automatikusan frissítse az alkalmazás konfigurációját, és egy új kulcsot használják.

* *Legegyszerűbben* használja a FedUtil eszközt használunk erre a WIF SDK, így a legújabb metaadat-dokumentum beolvasása és a konfiguráció frissítését tartalmazza.
* A .NET 4.5, amely tartalmazza a legújabb verziója található, a rendszer egyik névtere WIF alkalmazás frissítése. Ezután a [ellenőrzése kibocsátó neve beállításjegyzék (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) az alkalmazást az automatikus frissítések végrehajtásához.
* Hajtsa végre a manuális váltása szereplő utasításokat az útmutató végén.

Útmutatás a FedUtil használatáról a konfiguráció frissítése:

1. Győződjön meg arról, hogy rendelkezik-e a WIF 1.0-s verziója a fejlesztői gépen telepítve van a Visual Studio 2008 vagy 2010 SDK. Is [töltse le innen](https://www.microsoft.com/en-us/download/details.aspx?id=4451) Ha nem telepítette még azt.
2. A Visual Studióban nyissa meg a megoldást, majd kattintson a jobb gombbal a megfelelő projektet, és válassza ki **összevonási metaadatok frissítése**. Ha ez a beállítás nem érhető el, FedUtil és/vagy a WIF 1.0-s SDK nem lett telepítve.
3. Válassza ki a parancssorból **frissítés** megkezdéséhez az összevonási metaadatok frissítése. Ha hozzáfér a server környezetet, ahol az alkalmazás üzemel, is használhat FedUtil tartozó [automatikus metaadat-frissítés Feladatütemező](https://msdn.microsoft.com/library/ee517272.aspx).
4. Kattintson a **Befejezés** a frissítési folyamat befejezéséhez.

### <a name="other"></a>Webalkalmazások / API-k bármely más könyvtárak segítségével, vagy manuálisan végrehajtási a támogatott protokollok erőforrások védelme
Ha néhány más szalagtár használata, vagy manuálisan végrehajtani a támogatott protokollok, szüksége lesz a könyvtárban vagy annak érdekében, hogy a kulcs az OpenID Connect felderítési dokumentum vagy az összevonási metaadatok lekérésére a megvalósítás áttekintése a dokumentum. Egy ez kereséséhez módja az OpenID felderítési dokumentum vagy az összevonási metaadatok dokumentum kimenő még a vagy a könyvtár-kódban a kereséshez.

Ha ezek a kulcsok tárolása valahol vagy szoftveresen kötött az alkalmazásban, manuálisan beolvasása a kulcs és a frissítés, ennek megfelelően szerint hajtsa végre egy manuális váltása szereplő utasításokat az útmutató végén. **Erősen ajánlott, hogy javítható-e az alkalmazás támogatja az automatikus helyettesítő** ebben a cikkben a megközelítések vázlat bármelyikét elkerülése érdekében jövőbeli megszakításait és a terhelés, hogy az Azure AD növeli az átfordulási ütemben történik, illetve hogy vészhelyzet esetén használja sávon kívüli kulcsváltás.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Állapítsa meg, amennyiben ez érinti az alkalmazás tesztelése
Ellenőrizheti, hogy az alkalmazás támogatja az automatikus kulcsváltást letöltése a parancsfájlok és utasításait követve [a GitHub-tárházban.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Hogyan hajthat végre egy manuális váltása, ha alkalmazás nem támogatja az automatikus váltása
Ha az alkalmazás **nem** támogatja az automatikus helyettesítő, szüksége lesz egy folyamat, amely rendszeresen figyeli az Azure AD által aláíró kulcsok, és ennek megfelelően hajt végre egy manuális váltása létrehozásához. [A GitHub-tárházban](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) parancsfájlok és ehhez útmutatást tartalmaz.

