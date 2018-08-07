---
title: Az Azure Active Directory Aláírókulcs
description: Ez a cikk ismerteti az aláíró kulcsváltás ajánlott eljárások az Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 69dc56191667e65922d7d81116f4daf7a6e4b97a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576936"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Az Azure Active Directory aláírókulcs
Ez a cikk ismerteti, mit kell tudni az Azure Active Directory (Azure AD) biztonsági jogkivonatok aláírásához használt nyilvános kulcsok. Fontos megjegyezni, hogy a kulcsok váltása rendszeres időközönként, és a egy robotkart sikerült kell leváltani azonnal. Minden alkalmazás, amely az Azure AD használata programozott módon kezelni a kulcshoz kapcsolódó kulcsváltás folyamat vagy rendszeres manuális váltása folyamatot képesnek kell lennie. Tudni, hogyan működnek a kulcsokat, olvassa tovább az alkalmazás a Váltás hatásainak kiértékelését és frissítheti az alkalmazást, vagy időszakos manuális váltása folyamatot kulcsváltás kezelésére, ha szükséges.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Az Azure Active Directory aláírókulcs áttekintése
Az Azure AD és az azt használó alkalmazások között megbízhatósági kapcsolat létrehozása iparági szabványokon nyilvános kulcsú titkosítás használ. Gyakorlatilag, ez a következő módon működik: Azure ad-ben, amely egy nyilvános és titkos kulcsból álló párként áll aláíró kulcsot használ. Ha egy felhasználó bejelentkezik az Azure AD hitelesítésre használó alkalmazások, az Azure AD egy biztonsági jogkivonatot, amely tartalmazza a felhasználó adatai hoz létre. A jogkivonat aláírásával rendelkezik Azure AD-bA annak titkos kulcsát, az alkalmazásnak továbbítás előtt. Annak ellenőrzéséhez, hogy a jogkivonat érvényes, és az Azure ad-ből származó, az alkalmazásnak érvényesítenie kell a nyilvános kulccsal, amely tartalmazza a bérlő Azure AD által elérhetővé tett a jogkivonat aláírása [OpenID Connect-felderítési dokumentum](http://openid.net/specs/openid-connect-discovery-1_0.html) vagy SAML / WS-Fed [összevonási metaadatok dokumentuma](azure-ad-federation-metadata.md).

Biztonsági okokból az Azure AD aláíró kulcs tekercsben rendszeres időközönként, és vészhelyzet esetén sikerült kell leváltani azonnal. Minden olyan alkalmazás, amely integrálható az Azure ad-ben kezelni egy kulcsváltás eseményt, függetlenül attól, hogy milyen gyakran ez akkor fordulhat elő kell készíteni. Ha nem támogatja, és az alkalmazás megpróbálja használni egy lejárt kulcs ellenőrzése a jogkivonat aláírása, a bejelentkezési kérelem sikertelen lesz.

Nincs mindig egynél több érvényes kulcs az OpenID Connect-felderítési dokumentum és az összevonási metaadatok dokumentuma érhető el. Az alkalmazás használatához a kulcsokat a dokumentumban szereplő felkészültnek kell lennie, mivel előfordulhat, hogy egyetlen kulcs hamarosan fel lesz állítva, egy másik előfordulhat, hogy azok helyettesítéseivel, és így tovább.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Hogyan mérje fel, ha az alkalmazás hatással lesz, és mi a teendő információk
Hogyan kezeli az alkalmazás a kulcshoz kapcsolódó kulcsváltás attól függ, hogy változók, például az alkalmazás vagy milyen identitás protokoll és a könyvtár használt típusa. Az alábbi szakaszok az ellenőrzéséhez, hogy a leggyakoribb alkalmazások a kulcshoz kapcsolódó kulcsváltás érinti, és útmutatást nyújt az alkalmazás támogatja az automatikus átvitel, vagy manuálisan frissítse a kulcs frissítéséhez.

* [Natív ügyfélalkalmazások erőforrások elérése](#nativeclient)
* [Webes alkalmazások / erőforrások elérése API-k](#webclient)
* [Webes alkalmazások / API-k erőforrások védelméről, és az Azure App Services használatával](#appservices)
* [Webes alkalmazások / API-k használata a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication közbenső szoftveres erőforrások védelme](#owin)
* [Webes alkalmazások / API-k használata a .NET Core OpenID Connect vagy JwtBearerAuthentication közbenső szoftveres erőforrások védelme](#owincore)
* [Webes alkalmazások / API-k használata a Node.js passport-azure-ad-modul erőforrások védelme](#passport)
* [Webes alkalmazások / erőforrások védelme és a Visual Studio 2015 vagy Visual Studio 2017 használatával létrehozott API-k](#vs2015)
* [Erőforrások védelme és a Visual Studio 2013 létrehozott webalkalmazások](#vs2013)
* [Webes API-k erőforrások védelméről, és Visual Studio 2013-hoz létre](#vs2013_webapi)
* [Erőforrások védelme és a Visual Studio 2012-vel létrehozott webalkalmazások](#vs2012)
* [Erőforrások védelme és a Visual Studio 2010, a Windows Identity Foundation használatával 2008 o létrehozott webalkalmazások](#vs2010)
* [Webes alkalmazások / API-k használatával bármilyen más könyvtárat vagy manuális megvalósítása a támogatott protokollok erőforrások védelme](#other)

Ez az útmutató **nem** alkalmazható:

* Alkalmazások hozzáadása az Azure AD Application Gallery (beleértve az egyéni) külön útmutatás tartományállapot aláírókulcs rendelkezik. [További információt.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* A helyszíni keresztül alkalmazásproxyval közzétett alkalmazások nem kell aggódnia a aláírási kulcsokat.

### <a name="nativeclient"></a>Natív ügyfélalkalmazások erőforrások elérése
Az alkalmazásokat, amelyek csak érnek el erőforrásokat (vagyis) A Microsoft Graph, KeyVault, az Outlook API és más Microsoft APIs) általában csak egy token beszerzéséhez, és adja át azt mentén az erőforrás tulajdonosa. Tekintettel arra, hogy azok nem védett erőforrások, ne ellenőrizze a jogkivonatot, és ezért nem kell győződjön meg arról, megfelelően alá van írva.

Natív ügyfélalkalmazások, akár asztali, akár mobil-, ebbe a kategóriába tartoznak, és így nem érinti a váltás.

### <a name="webclient"></a>Webes alkalmazások / erőforrások elérése API-k
Az alkalmazásokat, amelyek csak érnek el erőforrásokat (vagyis) A Microsoft Graph, KeyVault, az Outlook API és más Microsoft APIs) általában csak egy token beszerzéséhez, és adja át azt mentén az erőforrás tulajdonosa. Tekintettel arra, hogy azok nem védett erőforrások, ne ellenőrizze a jogkivonatot, és ezért nem kell győződjön meg arról, megfelelően alá van írva.

Webes alkalmazások és a webes API-kat használ a csak az alkalmazásra vonatkozó folyamat (ügyfél-hitelesítő adatok / ügyféltanúsítvány), ebbe a kategóriába sorolhatók, és így nem érinti a kulcsváltás.

### <a name="appservices"></a>Webes alkalmazások / API-k erőforrások védelméről, és az Azure App Services használatával
Az Azure App Services hitelesítési / engedélyezési (EasyAuth) funkció már rendelkezik a szükséges logikával kulcsváltás automatikusan kezeli.

### <a name="owin"></a>Webes alkalmazások / API-k használata a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication közbenső szoftveres erőforrások védelme
Az alkalmazás a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication közbenső szoftverek, ha már rendelkezik a szükséges logikával kulcsváltás automatikusan kezeli.

Ellenőrizheti, hogy az alkalmazás az alábbi kódrészletek az alkalmazás Startup.cs vagy Startup.Auth.cs bármelyikét felismerésével ezek egyikét sem használ

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

### <a name="owincore"></a>Webes alkalmazások / API-k használata a .NET Core OpenID Connect vagy JwtBearerAuthentication közbenső szoftveres erőforrások védelme
Ha az alkalmazás a .NET Core OWIN OpenID Connect vagy JwtBearerAuthentication közbenső használ, már rendelkezik a szükséges logikával kulcsváltás automatikusan kezeli.

Ellenőrizheti, hogy az alkalmazás az alábbi kódrészletek az alkalmazás Startup.cs vagy Startup.Auth.cs bármelyikét felismerésével ezek egyikét sem használ

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

### <a name="passport"></a>Webes alkalmazások / API-k használata a Node.js passport-azure-ad-modul erőforrások védelme
Az alkalmazás a Node.js passport-ad-modul használ, ha már rendelkezik a szükséges logikával kulcsváltás automatikusan kezeli.

Azt is ellenőrizze, hogy az alkalmazás a passport-ad az alábbi kódrészletet az alkalmazás app.js keresés

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webes alkalmazások / erőforrások védelme és a Visual Studio 2015 vagy Visual Studio 2017 használatával létrehozott API-k
Ha az alkalmazás webes alkalmazást sablon a Visual Studio 2015 vagy Visual Studio 2017 használatával lett létrehozva, és a kiválasztott **munkahelyi és iskolai fiókok** származó a **hitelesítés módosítása** menüben, ha már van a szükséges logikával kulcsváltás automatikusan kezeli. A logikai az OWIN OpenID Connect közbenső szoftverek, a beágyazott kérdezi le, és az OpenID Connect-felderítési dokumentum kulcsait gyorsítótárazza, és rendszeres időközönként frissíti.

Adott hitelesítési megoldását manuálisan, ha az alkalmazás előfordulhat, hogy rendelkezik a szükséges kulcsváltás logikát. Írhat saját maga, vagy kövesse a kell [webes alkalmazások API-k használatával bármilyen más könyvtárat vagy manuális megvalósítása a támogatott protokollok /](#other).

### <a name="vs2013"></a>Erőforrások védelme és a Visual Studio 2013 létrehozott webalkalmazások
Ha az alkalmazás webes alkalmazást sablon a Visual Studio 2013 használatával lett létrehozva, és a kiválasztott **szervezeti fiókok** a a **hitelesítés módosítása** menüben már rendelkezik a szükséges logika a kulcshoz kapcsolódó kulcsváltás automatikusan kezeli. A logikai a szervezet egyedi azonosítóját és a kulcs aláírási információkat tárol a projekthez tartozó két adatbázistáblák. Az adatbázis a kapcsolati karakterláncot a projekt Web.config fájljában találja.

Adott hitelesítési megoldását manuálisan, ha az alkalmazás előfordulhat, hogy rendelkezik a szükséges kulcsváltás logikát. Írhat saját maga, vagy kövesse a kell [webes alkalmazások API-k használatával bármilyen más könyvtárat vagy manuális megvalósítása a támogatott protokollok /.](#other).

Az alábbi lépések segítségével, győződjön meg arról, hogy a logikai az alkalmazás megfelelően működik.

1. A Visual Studio 2013, nyissa meg a megoldást, és kattintson a a **Server Explorer** lapra a jobb oldali ablak.
2. Bontsa ki a **adatkapcsolatok**, **DefaultConnection**, majd **táblák**. Keresse meg a **IssuingAuthorityKeys** táblát, kattintson a jobb gombbal, és kattintson **táblák adatainak megjelenítése**.
3. Az a **IssuingAuthorityKeys** táblában, legalább egy sort, amely megfelel az ujjlenyomat értékét a kulcs lesz. Törli a sorokat a táblában.
4. Kattintson a jobb gombbal a **bérlők** táblát, és kattintson a **táblák adatainak megjelenítése**.
5. Az a **bérlők** táblában, legalább egy sort, amely megfelel egy egyedi könyvtárat bérlőazonosító lesz. Törli a sorokat a táblában. Ha nem törli a sorokat, mindkét a **bérlők** tábla és **IssuingAuthorityKeys** táblában, akkor megjelenik egy hibaüzenet futásidőben.
6. Hozhat létre, és futtassa az alkalmazást. Miután bejelentkezett a fiókjához, állítsa le az alkalmazást.
7. Lépjen vissza a **Server Explorer** , és tekintse meg az értékeket a **IssuingAuthorityKeys** és **bérlők** tábla. Láthatja, hogy azok rendelkeznek lett automatikusan tölteni az összevonási metaadatok dokumentuma megfelelő adataival.

### <a name="vs2013"></a>Webes API-k erőforrások védelméről, és Visual Studio 2013-hoz létre
Ha egy webes API-alkalmazás létrehozása a Visual Studio 2013 használatával a webes API-sablont, és ezután a kiválasztott **szervezeti fiókok** származó a **hitelesítés módosítása** menüben, akkor már rendelkezik a szükséges az alkalmazás logikáját.

Ha manuálisan konfigurálta a hitelesítés, kövesse az alábbi utasításokat a megtudhatja, hogyan konfigurálhatja a webes API a kulcsadatokat automatikus frissítését.

A következő kódrészletet bemutatja, hogyan lehet a legújabb kulcsok lekérése az összevonási metaadatok dokumentuma, és használja a [JWT jogkivonat kezelő](https://msdn.microsoft.com/library/dn205065.aspx) a jogkivonat érvényesítéséhez. A kódtöredék azt feltételezi, hogy használhatja a saját gyorsítótárazási mechanizmust a megőrzése a kulcsot az Azure ad-ből, a jövőbeli jogkivonatainak érvényesítéséhez szükséges az adatbázis, a konfigurációs fájlban vagy máshol legyen szó.

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

### <a name="vs2012"></a>Erőforrások védelme és a Visual Studio 2012-vel létrehozott webalkalmazások
Ha az alkalmazás a Visual Studio 2012-ben lett létrehozva, valószínűleg az az identitás- és hozzáférés eszköz használni az alkalmazás konfigurálásához. Is valószínű, hogy használja a [érvényesítése kibocsátó neve beállításjegyzék (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). A VINR információt a megbízható Identitásszolgáltatók (az Azure AD) és az azok által kiállított jogkivonatokban érvényesítéséhez használt kulcsok karbantartásáért felelős. A VINR is egyszerűen automatikus frissítése a kulccsal kapcsolatos adatokat tárolja a Web.config fájlt úgy, hogy letölti a legújabb összevonási metaadatok dokumentuma társított címtárban, ellenőrzi, hogy a konfiguráció nem naprakész a legújabb dokumentumot, és az új kulcs használatára, szükség szerint az alkalmazás frissítését.

A Kódminták vagy a Microsoft által biztosított forgatókönyv dokumentáció segítségével az alkalmazás hozta létre, ha a kulcshoz kapcsolódó kulcsváltás logikai már szerepel a projektben. Megfigyelheti, hogy az alábbi kódot a projekt már létezik. Ha az alkalmazás még nem rendelkezik a logikát, és adja hozzá, és győződjön meg arról, hogy megfelelően működik-e az alábbi lépésekkel.

1. A **Megoldáskezelőben**, vegyen fel egy hivatkozást a **System.IdentityModel** szerelvény számára a megfelelő projektet.
2. Nyissa meg a **Global.asax.cs** fájlt, és adja hozzá az alábbi irányelvek használatával:
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
4. Meghívása a **RefreshValidationSettings()** metódus az a **Application_Start()** metódus az **Global.asax.cs** látható módon:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Ha követte ezeket a lépéseket, az alkalmazás Web.config frissíti a rendszer a legújabb adatokat az összevonási metaadatok dokumentuma, beleértve a legújabb kulcsot is. Ez a frissítés történik, minden alkalommal, amikor az alkalmazáskészlet újraindul az IIS-ben; Alapértelmezés szerint az IIS újrahasznosítása alkalmazások 29 óránként van beállítva.

Kövesse az alábbi lépéseket, győződjön meg arról, hogy működik-e a kulcshoz kapcsolódó kulcsváltás logikát.

1. Miután ellenőrizte, hogy az alkalmazás a fenti kódot használ, nyissa meg a **Web.config** fájlt, és keresse meg a **<issuerNameRegistry>** letiltása, konkrétan a következő néhány sort:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Az a **<add thumbprint=””>** beállításban módosítsa az ujjlenyomat értékét egy másik bármely karakter lecserélésével. Mentse a **Web.config** fájlt.
3. Hozza létre az alkalmazást, és futtassa azt. A bejelentkezési folyamat elvégzése is, ha az alkalmazás sikeresen frissítése folyamatban van a kulcsot úgy, hogy letölti a szükséges adatokat a címtár összevonási metaadatok dokumentuma az. Ha előfizetéssel kapcsolatos problémák merülnek fel, győződjön meg arról, a módosítások az alkalmazás helyesek olvassa el a [hozzáadása bejelentkezés a webes alkalmazás Using Azure ad-ben](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) cikket, vagy töltsön le és az alábbi kódmintában vizsgálatával: [ Több-Bérlős felhőalapú alkalmazások az Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Erőforrások védelme és a Visual Studio 2008 vagy 2010 hoztak létre webes alkalmazásokat és a .NET 3.5 a Windows Identity Foundation (WIF) v1.0
Ha létrehozott egy alkalmazást, WIF 1.0-s verziójú, nincs megadott mód automatikusan frissítse az alkalmazás konfigurációját, és egy új kulcsot használja.

* *A legegyszerűbb módja* a FedUtil nincs lehetősége eszközök használatára a WIF SDK-t, amely a legújabb metaadat-dokumentum lekérése, és frissítse a konfigurációt tartalmazza.
* Az alkalmazás a .NET 4.5, amely tartalmazza a legújabb verziója található, a rendszer névtér WIF frissítése. Ezután a [érvényesítése kibocsátó neve beállításjegyzék (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) az alkalmazást az automatikus frissítések végrehajtásához.
* Hajtsa végre a manuális váltása a dokumentum végén található utasítások alapján.

Frissítse a konfigurációt a FedUtil használatára vonatkozó utasítások:

1. Győződjön meg arról, hogy rendelkezik-e a WIF 1.0-s verziója telepítve van a fejlesztési számítógépén a Visual Studio 2008 vagy a 2010 SDK. Is [innen tölthet le](https://www.microsoft.com/en-us/download/details.aspx?id=4451) Ha Ön még nem telepítette azt.
2. A Visual Studióban nyissa meg a megoldást, majd kattintson a jobb gombbal a megfelelő projektet, és válassza ki **összevonási metaadatok frissítése**. Ez a beállítás nem érhető el, ha FedUtil és/vagy a WIF 1.0-s verziójú SDK még nincs telepítve.
3. A rendszer kéri, válassza ki **frissítés** megkezdéséhez az összevonási metaadatok frissítése. Ha rendelkezik hozzáféréssel a server környezetet, ahol az alkalmazás üzemel, igény szerint használhatja a FedUtil [automatikus metaadat-frissítés scheduler](https://msdn.microsoft.com/library/ee517272.aspx).
4. Kattintson a **Befejezés** a frissítés befejezéséhez.

### <a name="other"></a>Webes alkalmazások / API-k használatával bármilyen más könyvtárat vagy manuális megvalósítása a támogatott protokollok erőforrások védelme
Ha van néhány más kódtár használatával, vagy manuálisan végrehajtott támogatott protokollok, kell tekintse át a könyvtárban vagy annak érdekében, hogy a kulcs az OpenID Connect-felderítési dokumentum vagy az összevonási metaadatok lekérésére a megvalósítás a dokumentum. Ellenőrizheti az egyik lehetőség, hogy irányuló összes hívást végre vagy az OpenID felderítési dokumentumot, vagy az összevonási metaadatok dokumentuma a kód vagy a kódtár kód a kereséshez.

Ha a kulcs tárol valahol vagy szoftveresen kötött az alkalmazásban, manuálisan lekérni a kulcsot, és frissítse a azt ennek megfelelően a dokumentum végén található utasításoknak egy manuális Váltás a végrehajtása. **Erősen javasolt, hogy az alkalmazás támogatja az automatikus helyettesítő növelheti** Ha növeli a helyettesítő kiadása ütemben történik az Azure AD vagy vészhelyzet jövőbeli megszakításait és a terhelés elkerülése érdekében ebben a cikkben a megközelítések vázlat valamelyik használatával sávon kívüli kulcsváltás.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Határozza meg, ha ez hatással lesz az alkalmazás tesztelése
Ellenőrizheti, hogy az alkalmazás támogatja az automatikus kulcsváltást a szkriptek letöltésére, valamint útmutatásai szerint [GitHub-adattárban.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Hogyan hajthat végre egy manuális váltása, ha az alkalmazás nem támogatja az automatikus váltása
Ha az alkalmazás **nem** támogatja az automatikus átvitel, szüksége lesz egy folyamat, amely rendszeresen figyeli az Azure AD a aláírási kulcsokat, és ennek megfelelően hajt végre egy manuális váltása. [GitHub-adattárban](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) parancsfájlok és ehhez útmutatást tartalmaz.

