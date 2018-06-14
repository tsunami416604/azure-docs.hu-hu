---
title: Kommunikáció - a Microsoft fenyegetés modellezési eszköz - biztonsági Azure |} Microsoft Docs
description: a fenyegetések modellezése eszköz felfedett fenyegetések megoldást
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 71bbe53595f2afab50d6220f335d615ada957a85
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019483"
---
# <a name="security-frame-communication-security--mitigations"></a>Biztonsági keret: Kommunikációs biztonsági |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Az Event Hubs használatával SSL/TLS biztonságos kommunikáció](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Ellenőrizze a jogosultsággal, és ellenőrizze, hogy az egyéni szolgáltatásokat vagy az ASP.NET-lapok tiszteletben CRM biztonsági](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[A kapcsolódáshoz adatkezelési átjárót a helyszíni SQL Server az Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identity Serverben** | <ul><li>[Győződjön meg arról, hogy az Identity Serverben irányuló összes forgalom HTTPS-kapcsolaton keresztül](#identity-https)</li></ul> |
| **Webalkalmazás** | <ul><li>[Ellenőrizze az X.509 SSL, a TLS és DTLS létesített kapcsolatok hitelesítéséhez használt tanúsítványok](#x509-ssltls)</li><li>[Az egyéni tartomány SSL tanúsítvány konfigurálása az Azure App Service-ben](#ssl-appservice)</li><li>[Az összes forgalom kényszerítheti az Azure App Service HTTPS-kapcsolaton keresztül](#appservice-https)</li><li>[HTTP szigorú a Transport Security (HSTS) engedélyezése](#http-hsts)</li></ul> |
| **Adatbázis** | <ul><li>[Ellenőrizze az SQL server-kapcsolat titkosítási és a tanúsítvány érvényesítése](#sqlserver-validation)</li><li>[Kényszerített titkosított kommunikációt az SQL-kiszolgálón](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy Azure Storage kommunikáció HTTPS-KAPCSOLATON keresztül](#comm-storage)</li><li>[MD5 kivonatoló érvényesítése után blob letöltése, ha HTTPS nem engedélyezhető.](#md5-https)</li><li>[Az SMB 3.0 kompatibilis ügyfél használatával biztosíthatja a Azure fájlmegosztásokat adatok az átvitel közbeni titkosítás](#smb-shares)</li></ul> |
| **Mobileszköz ügyfél** | <ul><li>[Tanúsítvány rögzítését megvalósítása](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Engedélyezze a HTTPS - az átviteli csatornát](#https-transport)</li><li>[WCF: Set Message biztonsági EncryptAndSign és védelmi szint](#message-protection)</li><li>[WCF: Legkevésbé jogosultsági szintű fiók használata a WCF-szolgáltatás futtatásához](#least-account-wcf)</li></ul> |
| **Webes API** | <ul><li>[Az összes forgalom kényszerítése a webes API-k való HTTPS-kapcsolaton keresztül](#webapi-https)</li></ul> |
| **Azure Redis Cache** | <ul><li>[Győződjön meg arról, hogy Azure Redis Cache kommunikáció SSL-en keresztül](#redis-ssl)</li></ul> |
| **Az IoT-mező átjáró** | <ul><li>[Biztonságos eszköz és a mező átjáró közötti kommunikáció](#device-field)</li></ul> |
| **Az IoT átjáró** | <ul><li>[Az átjáró a kommunikációt az SSL/TLS biztonságos eszköz](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Az Event Hubs használatával SSL/TLS biztonságos kommunikáció

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítés és a biztonsági modell – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Biztonságos SSL/TLS használatával az Event Hubs AMQP- vagy HTTP-kapcsolatok |

## <a id="priv-aspnet"></a>Ellenőrizze a jogosultsággal, és ellenőrizze, hogy az egyéni szolgáltatásokat vagy az ASP.NET-lapok tiszteletben CRM biztonsági

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ellenőrizze a jogosultsággal, és ellenőrizze, hogy az egyéni szolgáltatásokat vagy az ASP.NET-lapok tiszteletben CRM biztonsági |

## <a id="sqlserver-factory"></a>A kapcsolódáshoz adatkezelési átjárót a helyszíni SQL Server az Azure Data Factory

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Data Factory | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Kapcsolódószolgáltatás-típusok - Azure és a helyi |
| **Hivatkozások**              |[Adatok áthelyezése másik helyszíni és az Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [az adatkezelési átjáró](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Lépések** | <p>Az adatok felügyeleti átjáró (DMG) eszköze corpnet vagy egy tűzfal mögött található védett adatforrásokhoz való kapcsolódáshoz szükséges.</p><ol><li>A számítógép zárolása elkülöníti a DMG eszközt, és megakadályozza, hogy káros vagy az adatok forrásgépen megfigyelő hibás programok. (E.g. telepíteni kell a legújabb frissítéseket, minimális engedélyezése szükséges portok ellenőrzött fiókok átadásához, a naplózás engedélyezve van, lemez engedélyezhető a titkosítás stb.)</li><li>Adatok átjárókulcs kell forgatni rendszeres időközönként, vagy amikor megújítja a DMG szolgáltatásfiók jelszavát</li><li>Adatok továbbítására hivatkozás szolgáltatáson keresztül titkosítva kell lennie</li></ol> |

## <a id="identity-https"></a>Győződjön meg arról, hogy az Identity Serverben irányuló összes forgalom HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IdentityServer3 - kulcsokat, aláírás és titkosítás](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - telepítés](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | Alapértelmezés szerint a IdentityServer igényli a HTTPS-KAPCSOLATON keresztül elérni az összes bejövő kapcsolat. Feltétlenül kötelező, hogy IdentityServer kommunikáció csak biztonságos átvitelnél keresztül hajtja végre. Nincsenek bizonyos telepítési helyzetekben, például az SSL-feladatkiszervezést, ahol ezt a követelményt mérsékelhető. További információ a hivatkozások a az Identity Serverben telepítési oldal jelenik meg. |

## <a id="x509-ssltls"></a>Ellenőrizze az X.509 SSL, a TLS és DTLS létesített kapcsolatok hitelesítéséhez használt tanúsítványok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>SSL, a TLS és DTLS használó alkalmazások teljesen ellenőrizni kell az entitások csatlakoznak X.509-tanúsítványokat. Ez magában foglalja a tanúsítványok ellenőrzése:</p><ul><li>Tartománynév</li><li>Érvényességi dátumok (kezdő és a lejárati dátum)</li><li>Visszavonási állapotát</li><li>Használat (például kiszolgálóhitelesítéshez kiszolgálók, ügyfelek ügyfél-hitelesítés)</li><li>Megbízhatósági láncában. Egy legfelső szintű hitelesítésszolgáltató (CA), a platform megbízható vagy explicit módon a rendszergazda által konfigurált tanúsítványok programhoz</li><li>A tanúsítvány nyilvános kulcsa kulcshossza > 2048 bit</li><li>Kivonatoló algoritmus kell lennie az SHA256 vagy újabb verzió |

## <a id="ssl-appservice"></a>Az egyéni tartomány SSL tanúsítvány konfigurálása az Azure App Service-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [HTTPS engedélyezése az alkalmazásoknak az Azure App Service-ben](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Lépések** | Alapértelmezés szerint Azure már lehetővé teszi, hogy HTTPS a helyettesítő tanúsítványt minden alkalmazást a *. azurewebsites.net tartományban. Azonban minden helyettesítő karakteres tartományok, például nincs olyan biztonságos, mint az egyéni tartománynév használatával saját tanúsítvánnyal [olvassa el a](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Javasoljuk, hogy az egyéni tartomány, a telepített alkalmazások lesznek elérhetők, keresztül SSL engedélyezése|

## <a id="appservice-https"></a>Az összes forgalom kényszerítheti az Azure App Service HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [Az Azure App Service HTTPS kényszerítése](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Lépések** | <p>Bár az Azure már lehetővé teszi, hogy HTTPS az Azure app Service szolgáltatások helyettesítő tanúsítványt a tartomány *. azurewebsites.net, akkor kényszeríti ki a HTTPS PROTOKOLLT. Látogatók is hozzáférhetnek a HTTP-n, az alkalmazás biztonsági veszélyeztethetik keresztül alkalmazást, és ezért HTTPS rendelkezik explicit módon kikényszerítendő. ASP.NET MVC alkalmazások használhatják a [RequireHttps szűrő](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) , amely előírja, hogy egy nem biztonságos HTTP-kérelem újra küldésének HTTPS-KAPCSOLATON keresztül.</p><p>Azt is megteheti az URL-újraíró modult, amely tartalmazza az Azure App Service segítségével HTTPS kényszerítéséhez. URL-újraíró modult lehetővé teszi a fejlesztők olyan szabályok, bejövő kérelmek alkalmazott előtt be kell adni a kérelmek az alkalmazáshoz. URL-újraíró szabályok vannak meghatározva, a web.config fájlban tárolja az alkalmazás gyökérkönyvtárában</p>|

### <a name="example"></a>Példa
Az alábbi példa tartalmaz URL-újraíró egy egyszerű szabályt, amely arra kényszeríti a HTTPS PROTOKOLLT használó minden bejövő forgalom
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Ez a szabály működik vissza egy HTTP-állapotkód 301 (Állandó átirányítás), amikor a felhasználó kéri egy lap HTTP-n keresztül. A 301 átirányítja a kérést a kért látogató azonos URL-címre, de a kérelem HTTP részét lecseréli HTTPS. Például HTTP://contoso.com HTTPS://contoso.com átirányítják. 

## <a id="http-hsts"></a>HTTP szigorú a Transport Security (HSTS) engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [OWASP HTTP szigorú a Transport Security Cheat lap](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Lépések** | <p>HTTP szigorú átviteli biztonsági (HSTS) olyan biztonsági opt-in fejlesztések, amelyek egy adott webalkalmazás révén egy különleges válaszfejléc van megadva. Miután egy támogatott böngésző megkapja ezt a fejlécet, hogy a böngésző megakadályozza, hogy a megadott tartományhoz HTTP Protokollon keresztül küldött összes kommunikációt, és ehelyett küldi a kommunikáció minden esetben HTTPS PROTOKOLLON keresztül. Megakadályozza a HTTPS kattintson Rákérdezés a böngészők keresztül is.</p><p>HSTS végrehajtásához a következő válaszfejléc ki konfigurálni egy webhely globálisan, a kódban, vagy a konfigurációban. Strict-Transport-Security: max-age=300; includeSubDomains HSTS addresses the following threats:</p><ul><li>Felhasználói könyvjelzők vagy manuálisan http://example.com típusokat és a-átjárójának támadók hatálya alá tartozik: HSTS automatikusan átirányítja a cél tartomány HTTPS a HTTP-kérelmek</li><li>Célja, hogy csak HTTPS webes alkalmazás HTTP hivatkozásokat tartalmaz, vagy HTTP-kapcsolaton keresztül tartalmat szolgáltat: HSTS automatikusan átirányítja a cél tartomány HTTPS a HTTP-kérelmek</li><li>Egy-átjárójának támadó megpróbálja intercept érvénytelen tanúsítványt használó áldozata felhasználó érkező forgalmat, és a felhasználó elfogadja a hibás tanúsítvány reméli: HSTS nem engedélyezi a felhasználó az érvénytelen tanúsítványt üzenet felülbírálására</li></ul>|

## <a id="sqlserver-validation"></a>Ellenőrizze az SQL server-kapcsolat titkosítási és a tanúsítvány érvényesítése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | SQL Azure  |
| **Attribútumok**              | SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Gyakorlati tanácsok írása a biztonságos kapcsolati karakterláncok SQL-adatbázis](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Lépések** | <p>SQL-adatbázis és az ügyfél alkalmazás közötti minden kommunikáció titkosítása mindig Secure Sockets Layer (SSL) használatával. SQL-adatbázis nem támogatja a titkosított kapcsolatokat. Az alkalmazás kódja vagy eszközök tanúsítványok érvényesítésére, explicit módon titkosított kapcsolatot kér, és nem bízik meg a kiszolgálói tanúsítványokat. Az alkalmazás kódja vagy az eszközök nem kérő titkosított kapcsolatot, ha azok még megérkeznek a titkosított kapcsolatokat</p><p>Azonban előfordulhat, hogy érvényesíteni a kiszolgálói tanúsítványokat, és így lesz téve a "man a középső" támadások. Az ADO.NET alkalmazáskód tanúsítványok érvényesítésére, állítsa be `Encrypt=True` és `TrustServerCertificate=False` az adatbázis-kapcsolati karakterláncot. SQL Server Management Studio segítségével tanúsítványok ellenőrzéséhez nyissa meg a kiszolgáló párbeszédpanel csatlakozás. Kattintson a kapcsolat titkosítása a kapcsolat tulajdonságai lapon</p>|

## <a id="encrypted-sqlserver"></a>Kényszerített titkosított kommunikációt az SQL-kiszolgálón

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | A helyi üzemeltetésű |
| **Attribútumok**              | SQL verzió - MsSQL2016, SQL - MsSQL2012, SQL verzió - verzió MsSQL2014 |
| **Hivatkozások**              | [Engedélyezze a titkosított kapcsolatokat az adatbázismotorhoz való csatlakozáshoz](https://msdn.microsoft.com/library/ms191192)  |
| **Lépések** | SSL engedélyezése titkosítás növeli a hálózatokon, SQL Server-példány és az alkalmazások között továbbított adatok biztonsága érdekében. |

## <a id="comm-storage"></a>Győződjön meg arról, hogy Azure Storage kommunikáció HTTPS-KAPCSOLATON keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure Storage átviteli szintű titkosítást – HTTPS-kapcsolaton keresztül](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Lépések** | Az Azure Storage adatok az átvitel közbeni védelme érdekében mindig a HTTPS protokollt használják, amikor tárolási objektumokat a REST API-k hívása vagy eléréséhez. Emellett megosztott hozzáférési aláírásokkal, amely használható a delegálása az Azure Storage-objektumokhoz való hozzáférést, tartalmaznak, adja meg, hogy csak a HTTPS protokollt használja, megosztott hozzáférési aláírásokkal, győződjön meg arról, hogy küldje el a SAS-tokenje hivatkozások használ használatakor a megfelelő protokollt.|

## <a id="md5-https"></a>MD5 kivonatoló érvényesítése után blob letöltése, ha HTTPS nem engedélyezhető.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Hivatkozások**              | [A Windows Azure-blobot MD5 áttekintése](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Lépések** | <p>Windows Azure Blob szolgáltatás biztosítja az adatok sértetlenségét, mind az alkalmazás és a szállítási réteg mechanizmusokat biztosít. Ha bármilyen okból kell használni a HTTP helyett HTTPS és a blokkblobokhoz dolgozik, használhatja MD5 ellenőrzése érdekében ellenőrizni az átvitel során a blobok</p><p>Ez segít hálózati transport layer hibák elleni védelem, de nem feltétlenül közvetítő támadások. Ha a HTTPS-t, amely átvitelszintű biztonság, akkor használja, MD5 ellenőrzése redundáns és szükségtelen is használhatja.</p>|

## <a id="smb-shares"></a>Az SMB 3.0 kompatibilis ügyfél használatával biztosíthatja a Azure fájlmegosztásokat adatok az átvitel közbeni titkosítás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | StorageType - fájl |
| **Hivatkozások**              | [Az Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [az Azure File Storage SMB támogatása a Windows-ügyfelek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Lépések** | Az Azure File Storage támogatja a HTTPS PROTOKOLLT, ha a REST API használatával, de több általánosan használt SMB-fájlmegosztás a virtuális Géphez van csatolva. SMB 2.1 nem támogatja a titkosítást, tehát kapcsolatokat csak az Azure-ban ugyanabban a régióban belül engedélyezett. Azonban az SMB 3.0 támogatja a titkosítást, és használható a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10, lehetővé téve a kereszt-régió eléréséhez, és akkor is igaz, az asztalon hozzáférést. |

## <a id="cert-pinning"></a>Tanúsítvány rögzítését megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, Windows Phone |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A tanúsítvány és nyilvános kulcs rögzítése](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Lépések** | <p>Tanúsítvány rögzítését defends-átjárójának (MITM) támadások ellen. Rögzítési a rendszer a gazdagép társítva a várt X509 tanúsítvány vagy nyilvános kulcs. Miután a tanúsítvány nyilvános kulcs ismert vagy állomás látható, a tanúsítvány nyilvános kulcs társított, vagy "rögzített" a gazdagép. </p><p>Ebből kifolyólag során az ellenfél megkísérli SSL MITM támadás tegye, SSL-kézfogás során a támadó kiszolgálóról a kulcsot a rögzített tanúsítvány kulcsa eltérő lesz és a kérelmet a rendszer elveti, megelőzve MITM tanúsítvány rögzítését érhető el a ServicePointManager a végrehajtási `ServerCertificateValidationCallback` delegálni.</p>|

### <a name="example"></a>Példa
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>Engedélyezze a HTTPS - az átviteli csatornát

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | Az alkalmazás konfigurációjának meg kell győződnie arról, hogy HTTPS PROTOKOLLT használja a bizalmas adatokat az elérésére.<ul><li>**Magyarázat:** Ha egy alkalmazás kezeli a bizalmas adatokat, és üzenet szintű titkosítást használ, akkor azt csak akkor engedélyezhető egy átviteli titkosított csatornán keresztül kommunikálnak.</li><li>**JAVASLATOK:** győződjön meg arról, hogy a HTTP-átvitel le van tiltva, és engedélyezze a HTTPS átviteli protokoll helyett. Helyettesítse be például a `<httpTransport/>` rendelkező `<httpsTransport/>` címke. Ne használja a hálózati konfigurációt (tűzfal) garantálja, hogy az alkalmazás csak elérhető egy biztonságos csatornán keresztül. Az alkalmazás világnézeti szempontból nem függ a hálózat és a biztonságot.</li></ul><p>A gyakorlati szempontjából a hálózat védelme felelős személyek mindig követi nyomon az alkalmazás biztonsági követelményeinek, azok fejleszteni.</p>|

## <a id="message-protection"></a>WCF: Set Message biztonsági EncryptAndSign és védelmi szint

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Lépések** | <ul><li>**Magyarázat:** Ha a védelmi szintje "none" az üzenet védelmi letiltja. Titkosítás és integritásának megfelelő szintű beállítás érhető el.</li><li>**JAVASLATOK:**<ul><li>Ha `Mode=None` -üzenet védelem letiltása</li><li>Ha `Mode=Sign` -jeleket azonban nem titkosítja az üzenetet; kell használni, amikor az adatok integritásának fontos</li><li>Ha `Mode=EncryptAndSign` -jelek és titkosítja az üzenetet</li></ul></li></ul><p>Fontolja meg a titkosítási kikapcsolása, és csak az üzenetet aláíró, ha csupán be kell semmiképp bizalmas adatok integritásának ellenőrzését. Ez akkor lehet hasznos, a műveletek vagy szolgáltatási szerződések, amely az eredeti küldő érvényesítenie kell, de nem bizalmas adatai átkerülnek a. Ha a védelmi szint csökkentését, ügyeljen arra, hogy az üzenet nem tartalmaz személyazonosításra alkalmas adatok (PII).</p>|

### <a name="example"></a>Példa
A következő példákban látható konfigurálása a szolgáltatás és a művelet csak alá az üzenetet. Szolgáltatási szerződés példa `ProtectionLevel.Sign`: az alábbiakban található a szolgáltatási szerződés szinten ProtectionLevel.Sign használatának példája: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Példa
Művelet szerződés példa `ProtectionLevel.Sign` (a tanúsítványhasználat pontos szabályzása): az alábbiakban egy példa annak `ProtectionLevel.Sign` OperationContract szinten:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Legkevésbé jogosultsági szintű fiók használata a WCF-szolgáltatás futtatásához

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Lépések** | <ul><li>**Magyarázat:** admin vagy magas jogosultsági fiókkal WCF-szolgáltatások nem futnak. szolgáltatások biztonsági sérülés esetén nagy jelentőségű eredményez.</li><li>**JAVASLATOK:** gazdagéphez a WCF-szolgáltatást, mert az alkalmazás támadási felület csökkentése, és csökkentheti a fellépő potenciális károknak, ha vannak megtámadott legkevésbé jogosultsági szintű fiók használata. Ha a fiók infrastruktúra erőforrások, például a MSMQ, az eseménynaplóban, teljesítményszámlálók és a fájlrendszerben további hozzáférési jogosultságra van szüksége, megfelelő engedélyeket biztosítani kell ezeket az erőforrásokat, hogy a WCF-szolgáltatás sikeresen futtathatók.</li></ul><p>Ha a szolgáltatás egy adott erőforráshoz nevében az eredeti hívó hozzáférésre van szüksége, segítségével megszemélyesítés és a delegálás flow egy alsóbb rétegbeli engedélyezési, ellenőrizze a hívó identitását. A fejlesztési forgatókönyvek használja a helyi hálózati szolgáltatás fiók, amely speciális beépített fiók, amely korlátozott jogosultsággal rendelkezik. Egy éles telepítési forgatókönyvhöz hozzon létre egy egyéni legkevésbé jogosultsági szintű tartományi szolgáltatásfiókot.</p>|

## <a id="webapi-https"></a>Az összes forgalom kényszerítése a webes API-k való HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A webes API-vezérlőben SSL kényszerítése](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Lépések** | Ha egy alkalmazás egy HTTPS- és a HTTP-kötést, az ügyfelek továbbra is használhatja HTTP a webhelyhez való hozzáféréshez. Ennek megelőzése érdekében egy művelet szűrő használatával győződjön meg arról, hogy védett API-k kérelmek mindig HTTPS-KAPCSOLATON keresztül.|

### <a name="example"></a>Példa 
A következő kód bemutatja egy webes API hitelesítési szűrő, amely ellenőrzi az SSL-hez: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Ez a szűrő hozzáadása a webes API-műveleteket, SSL megkövetelése: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Győződjön meg arról, hogy Azure Redis Cache kommunikáció SSL-en keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Redis Cache | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Azure Redis SSL-támogatás](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Lépések** | Redis kiszolgáló nem támogatja az SSL arculata azonban Azure Redis Cache nem. Ha az Azure Redis Cache csatlakozik, és az ügyfél támogatja az SSL, például a StackExchange.Redis, akkor SSL kell használnia. Nem SSL port az új Azure Redis Cache példány alapértelmezés szerint le van tiltva. Győződjön meg arról, hogy a biztonságos alapértelmezett értéke, nem módosulnak kivéve, ha egy függőséget az SSL redis-ügyfelek támogatása. |

Vegye figyelembe, hogy a Redis célja, hogy megbízható ügyfelek megbízható környezetek belül érhető el. Ez azt jelenti, hogy általában nem érdemes teszi közzé a Redis-példány közvetlenül az internethez vagy, általában a nem megbízható ügyfelek ahol közvetlenül hozzáférhetnek a Redis TCP-port vagy a UNIX szoftvercsatorna-alapú környezethez. 

## <a id="device-field"></a>Biztonságos eszköz és a mező átjáró közötti kommunikáció

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT-mező átjáró | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | IP-alapú eszközök esetén a kommunikációs protokollt általában sikerült kell beágyazva egy SSL/TLS-csatorna az adatok védelmére átvitel. Egyéb protokollok, amelyek nem támogatják az SSL/TLS vizsgálja meg, hogy a protokoll, amely biztonsági transport vagy üzenet rétegben biztonságos verziója van. |

## <a id="device-cloud"></a>Az átjáró a kommunikációt az SSL/TLS biztonságos eszköz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Válassza ki a kommunikációs protokollja](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Lépések** | Használja az SSL/TLS biztonságos HTTP/AMQP vagy MQTT protokollokat. |
