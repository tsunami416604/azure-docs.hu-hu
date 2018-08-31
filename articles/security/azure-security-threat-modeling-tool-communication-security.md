---
title: Kommunikációs biztonság – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
description: megoldások a fenyegetések között szerepelnek a Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 7e8afc02c738a2bba445b1d84b7cb899dfbb93a0
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301554"
---
# <a name="security-frame-communication-security--mitigations"></a>Biztonsági keret: Kommunikációs biztonság |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Biztonságos kommunikáció SSL/TLS eseményközpontba](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Ellenőrizze a jogosultsággal, és ellenőrizze, hogy az egyéni szolgáltatásokat vagy az ASP.NET-lapok tiszteletben CRM a biztonság](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Az adatkezelési átjáró használata az Azure Data Factory a helyszíni SQL Server a kapcsolódáshoz](#sqlserver-factory)</li></ul> |
| **Identitáskezelési kiszolgáló** | <ul><li>[Gondoskodjon arról, hogy minden forgalmat az identitás-kiszolgálót HTTPS-kapcsolaton keresztül](#identity-https)</li></ul> |
| **Webalkalmazás** | <ul><li>[Ellenőrizze az X.509 SSL, a TLS és DTLS kapcsolat hitelesítéséhez használt tanúsítványok](#x509-ssltls)</li><li>[Egyéni tartomány SSL-tanúsítvány konfigurálása az Azure App Service-ben](#ssl-appservice)</li><li>[Az összes forgalom kényszerített az Azure App Service-HTTPS-kapcsolaton keresztül](#appservice-https)</li><li>[HTTP szigorú Transport Security (HSTS) engedélyezése](#http-hsts)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg, hogy az SQL server-kapcsolat titkosítási és a tanúsítvány érvényesítése](#sqlserver-validation)</li><li>[Az SQL server titkosított kommunikáció kényszerítése](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Gondoskodjon arról, hogy az Azure Storage-kommunikáció HTTPS-kapcsolaton keresztül](#comm-storage)</li><li>[Blob letöltése, ha nem lehet engedélyezni a HTTPS után MD5-kivonat ellenőrzése](#md5-https)</li><li>[Győződjön meg, hogy az átvitel közbeni adatok titkosítását az Azure-fájlmegosztások az SMB 3.0 kompatibilis ügyfél használatával](#smb-shares)</li></ul> |
| **Mobileszköz ügyfél** | <ul><li>[Tanúsítvány rögzítését megvalósítása](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS engedélyezése – biztonságos átvitel csatorna](#https-transport)</li><li>[A WCF: Set Message biztonsági EncryptAndSign és védelmi szint](#message-protection)</li><li>[A WCF: Egy alacsonyabb szintű fiók használata a WCF-szolgáltatás futtatásához](#least-account-wcf)</li></ul> |
| **Webes API** | <ul><li>[Összes forgalom kényszerített webes API-k HTTPS-kapcsolaton keresztül](#webapi-https)</li></ul> |
| **Azure Redis Cache** | <ul><li>[Gondoskodjon arról, hogy az Azure Redis Cache kommunikáció SSL-en keresztül](#redis-ssl)</li></ul> |
| **IoT helyszíni átjáró** | <ul><li>[Eszköz biztonságossá tételéhez és a helyszíni átjárók közötti kommunikációhoz](#device-field)</li></ul> |
| **IoT átjáró** | <ul><li>[Biztonságos eszköz és átjáró közötti kommunikációhoz SSL/TLS segítségével](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Biztonságos kommunikáció SSL/TLS eseményközpontba

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Biztonságos SSL/TLS segítségével Event Hubs AMQP vagy HTTP-kapcsolatok |

## <a id="priv-aspnet"></a>Ellenőrizze a jogosultsággal, és ellenőrizze, hogy az egyéni szolgáltatásokat vagy az ASP.NET-lapok tiszteletben CRM a biztonság

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ellenőrizze a jogosultsággal, és ellenőrizze, hogy az egyéni szolgáltatásokat vagy az ASP.NET-lapok tiszteletben CRM a biztonság |

## <a id="sqlserver-factory"></a>Az adatkezelési átjáró használata az Azure Data Factory a helyszíni SQL Server a kapcsolódáshoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Data Factory | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Típusú társított szolgáltatás – Azure és helyszíni |
| **Hivatkozások**              |[ Adatok áthelyezése a helyszíni és az Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [adatkezelési átjáró](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Lépések** | <p>A Data Management Gateway (DMG) eszköz már csatlakozhat adatforrásokhoz, amelyeket a vállalati hálózat vagy tűzfal mögé.</p><ol><li>A gép sémákra elkülöníti a DMG-eszközt, és megakadályozza, hogy a hibás programok sérülhetnek vagy megfigyelő adatok forrásoldali virtuális gépen. (E.g. telepíteni kell a legújabb frissítéseket, minimális engedélyezése szükséges portok, ellenőrzött fiókok kiépítése, naplózás engedélyezve van, lemeztitkosítás engedélyezve stb.)</li><li>Data Gateway kulcsot kell forgatni gyakori időközönként, vagy minden alkalommal, amikor megújítja a DMG szolgáltatásfiók jelszavát</li><li>Hivatkozás szolgáltatáson keresztül adatok továbbítását titkosítva kell lennie</li></ol> |

## <a id="identity-https"></a>Gondoskodjon arról, hogy minden forgalmat az identitás-kiszolgálót HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitáskezelési kiszolgáló | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IdentityServer3 - kulcsokat, aláírás és titkosítás](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 – üzembe helyezés](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | Alapértelmezés szerint a IdentityServer megköveteli a HTTPS-kapcsolaton keresztül lesz az összes bejövő kapcsolat. Feltétlenül kötelező, hogy csak a biztonságos átvitel IdentityServer folytatott kommunikáció végezték. Vannak bizonyos központi telepítési forgatókönyv például SSL-kiürítés, ahol ezt a követelményt csökkenthető. További információ a hivatkozások az identitás-kiszolgálót üzembe helyezési oldalon talál. |

## <a id="x509-ssltls"></a>Ellenőrizze az X.509 SSL, a TLS és DTLS kapcsolat hitelesítéséhez használt tanúsítványok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az SSL, a TLS és DTLS használó alkalmazások teljes ellenőriznie kell az X.509-tanúsítványokat, az entitásokat, amelyekhez csatlakoznak. Ez tartalmazza a tanúsítványok ellenőrzése:</p><ul><li>Tartománynév</li><li>Érvényességi dátum (kezdő és a lejárati dátumok)</li><li>Visszavonás állapota</li><li>Használat (például kiszolgálóhitelesítéshez kiszolgálók, ügyfelek ügyfél-hitelesítés)</li><li>Megbízhatósági láncában. A legfelső szintű hitelesítésszolgáltatót (CA) a platform által megbízhatónak tartott, vagy explicit módon a rendszergazda által konfigurált tanúsítványok programhoz</li><li>Tanúsítvány nyilvános kulcsát a kulcs hosszúsága lehet > 2048 bit</li><li>Kivonatoló algoritmus kell lennie az SHA256 vagy újabb verzió |

## <a id="ssl-appservice"></a>Egyéni tartomány SSL-tanúsítvány konfigurálása az Azure App Service-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [HTTPS engedélyezése az Azure App Service-alkalmazás](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Lépések** | Alapértelmezés szerint az Azure már lehetővé teszi, hogy HTTPS és a egy helyettesítő tanúsítványt minden alkalmazás a *. azurewebsites.net tartomány. Azonban minden helyettesítő karakteres tartományok, mint már nem ugyanolyan biztonságosak, mint az egyéni tartomány használatával saját tanúsítvánnyal [vizsgálatával](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Javasoljuk, hogy az üzembe helyezett alkalmazás érhető el – egyéni tartomány SSL engedélyezése|

## <a id="appservice-https"></a>Az összes forgalom kényszerített az Azure App Service-HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [Az Azure App Service HTTPS kényszerítése](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Lépések** | <p>Bár az Azure már lehetővé teszi, hogy HTTPS az Azure app services helyettesítő tanúsítványt a tartomány *. azurewebsites.NET webhelyet, nem tesszük kötelezővé a HTTPS. Látogatók továbbra is hozzáférhetnek a HTTP-n keresztül veszélyeztethetik a az alkalmazás biztonsági alkalmazás, és ezért a HTTPS explicit módon léptethetők rendelkezik. ASP.NET MVC-alkalmazások használhatják a [RequireHttps szűrő](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) , amely előírja, hogy újra küldésének HTTPS-kapcsolaton keresztül nem titkosított HTTP-kérelem.</p><p>Másik lehetőségként az URL-újraíró modult, amely tartalmazza az Azure App Service segítségével HTTPS kényszerítése. URL-újraíró modult lehetővé teszi a fejlesztők előtt be kell adni a kérelmeket az alkalmazás bejövő kérelmek alkalmazott szabályok meghatározásához. Az URL Rewrite-szabályok határozzák meg a web.config fájlban tárolja az alkalmazás</p>|

### <a name="example"></a>Példa
Az alábbi példa tartalmazza az URL-újraíró egy egyszerű szabályt, amely arra kényszeríti a teljes bejövő forgalom HTTPS használatára
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
Ez a szabály működik visszaadó HTTP-állapotkód 301 (Állandó átirányítás), amikor a felhasználó igényel-e egy oldal, HTTP-n keresztül. A 301 átirányítja a kérést a kért a látogató ugyanazon URL-címre, de a HTTP-alapú részéhez, a kérelem cseréli a HTTPS. Ha például HTTP://contoso.com átirányítják HTTPS://contoso.com. 

## <a id="http-hsts"></a>HTTP szigorú Transport Security (HSTS) engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [OWASP HTTP szigorú Transport Security – Adatlap](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Lépések** | <p>HTTP szigorú Transport Security (HSTS) egy választható biztonsági fejlesztések révén egy különleges válaszfejléc webalkalmazás által meghatározott. Miután egy támogatott böngésző megkapja ezt a fejlécet, hogy a böngésző megakadályozza a minden kommunikáció HTTP-n keresztül a megadott tartományba küldött, és inkább küld minden kommunikáció HTTPS-kapcsolaton keresztül. Megakadályozza a HTTPS kattintson keresztül a böngészőkben utasításokat is.</p><p>HSTS megvalósításához, a következő válaszfejléc kell konfigurálni egy webhely globálisan, vagy a kód vagy a konfigurációs van. Szigorú-átviteli – biztonság: a max-age = 300; includeSubDomains HSTS megoldást a következő szemben:</p><ul><li>Felhasználói könyvjelzőket, vagy manuálisan típusok http://example.com , és a egy man-in-the-middle támadó használatára: HSTS automatikusan átirányítja a célként megadott tartomány HTTPS a HTTP-kérelmekre</li><li>Webalkalmazást, amely a célja, hogy csak HTTPS lehet véletlenül HTTP hivatkozásokat tartalmaz, vagy a HTTP-kapcsolaton keresztül tartalmat szolgál ki: HSTS automatikusan átirányítja a célként megadott tartomány HTTPS a HTTP-kérelmekre</li><li>Egy man-in-the-middle támadó próbál intercept érvénytelen tanúsítványt használó victim felhasználó érkező forgalmat, és a felhasználó elfogadja a hibás tanúsítvány reméli: HSTS nem engedélyezi a felhasználó felülbírálhatja a tanúsítvány érvénytelen üzenet</li></ul>|

## <a id="sqlserver-validation"></a>Győződjön meg, hogy az SQL server-kapcsolat titkosítási és a tanúsítvány érvényesítése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | SQL Azure  |
| **Attribútumok**              | SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Ajánlott eljárásokat az írásos biztonságos kapcsolati karakterláncok SQL Database-hez](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Lépések** | <p>Az SQL Database és az ügyfélalkalmazások közötti minden kommunikáció mindig Secure Sockets Layer (SSL) segítségével titkosítja. Az SQL Database nem támogatja a nem titkosított kapcsolatokat. Tanúsítványok alkalmazáskódban vagy eszközök ellenőrzéséhez, explicit módon titkosított kapcsolatot kérjen, és nem megbízható kiszolgálói tanúsítványok. Ha az alkalmazás kódja vagy eszközök egy titkosított kapcsolatot kér, továbbra is kapja titkosított kapcsolatokat</p><p>Azonban nem lehet érvényesíteni a kiszolgálói tanúsítványok, és ezért lesz téve a "közbeékelődéses" támadások. Az ADO.NET alkalmazáskóddal tanúsítványok érvényesítésére, állítsa be `Encrypt=True` és `TrustServerCertificate=False` az adatbázis-kapcsolati karakterláncot. Tanúsítványokat bocsát ki az SQL Server Management Studio ellenőrzéséhez nyissa meg a Csatlakozás kiszolgálóhoz párbeszédpanel. Kapcsolat titkosítása a kapcsolat tulajdonságai lapon kattintson a</p>|

## <a id="encrypted-sqlserver"></a>Az SQL server titkosított kommunikáció kényszerítése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Rendszert |
| **Attribútumok**              | Az SQL verziója – MsSQL2016, SQL - MsSQL2012, SQL-verzió - verzió MsSQL2014 |
| **Hivatkozások**              | [Az adatbázismotor titkosított kapcsolat engedélyezése](https://msdn.microsoft.com/library/ms191192)  |
| **Lépések** | SSL engedélyezése titkosítás növeli az SQL Server példányai és az alkalmazások közötti hálózatokon keresztül továbbított adatok biztonsága érdekében. |

## <a id="comm-storage"></a>Gondoskodjon arról, hogy az Azure Storage-kommunikáció HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure Storage átviteli szintű titkosítást – HTTPS-en keresztül](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Lépések** | Ahhoz, hogy az Azure Storage adatok átvitel biztonságát, mindig a HTTPS protokollt használja, ha a REST API-k hívása, vagy fér hozzá az objektumok tárolására. Adja meg, hogy csak a HTTPS protokollt használja, közös hozzáférésű Jogosultságkódokat, biztosítva, hogy bárki küldi ki az SAS-tokeneket hivatkozásokat használja használata esetén lehetőség felvételére is a közös hozzáférésű Jogosultságkódokat, amely használható delegálása az Azure Storage-objektumokhoz való hozzáférést, a megfelelő protokoll.|

## <a id="md5-https"></a>Blob letöltése, ha nem lehet engedélyezni a HTTPS után MD5-kivonat ellenőrzése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Hivatkozások**              | [Windows Azure-Blobba MD5 – áttekintés](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Lépések** | <p>Windows Azure Blob szolgáltatás biztosítja az adatok sértetlenségét mind az alkalmazás és az átviteli réteg mechanizmusokat biztosít. Ha bármilyen okból kell használni a HTTP helyett HTTPS és a blokkblobok dolgozik, használhatja MD5-ellenőrzést a folyamatban lévő átvitelben érintett blobok sértetlenségének ellenőrzése érdekében</p><p>Ez akkor hasznos, hálózati transport layer hibák elleni védelem, de nem feltétlenül köztes támadások. Ha a HTTPS, amely lehetővé teszi a biztonság, akkor az MD5-ellenőrzést használata redundáns és szükségtelen is használhatja.</p>|

## <a id="smb-shares"></a>Győződjön meg, hogy az átvitel közbeni adatok titkosítását az Azure-fájlmegosztások az SMB 3.0 kompatibilis ügyfél használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | StorageType - fájl |
| **Hivatkozások**              | [Az Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [az Azure File Storage SMB-támogatás Windows ügyfelek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Lépések** | Az Azure File Storage támogatja a HTTPS, a REST API használata esetén, de több gyakran használt SMB-fájlmegosztás a virtuális Géphez van csatlakoztatva. SMB 2.1 nem támogatja a titkosítást, így a kapcsolatokat csak engedélyezett ugyanabban a régióban az Azure-ban. Azonban az SMB 3.0-s támogatja a titkosítást, és használható a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10-es, amely lehetővé teszi a régiók közötti eléréséhez, és akkor is hozzáférést, az asztalon. |

## <a id="cert-pinning"></a>Tanúsítvány rögzítését megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, Windows Phone |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Tanúsítvány és nyilvános kulcs rögzítése](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Lépések** | <p>Tanúsítvány rögzítését lelt Man-In-The-Middle (MITM) támadások ellen. Rögzítés a rendszer egy gazdagép társítása a várt X509 tanúsítvánnyal vagy nyilvános kulccsal. Miután a tanúsítvány vagy a nyilvános kulcsok ismert vagy egy gazdagép látható, a tanúsítvány vagy a nyilvános kulcs kapcsolódó vagy a gazdagép "rögzített". </p><p>Amikor egy támadó megpróbál tegye SSL MITM támadásokkal szemben, SSL-kézfogás során a támadó kiszolgálóról a kulcsot a rögzített tanúsítvány kulcsa, eltérő lesz és a kérést a rendszer elveti, megelőzve MITM tanúsítvány rögzítését érhető el végrehajtási ServicePointManager a `ServerCertificateValidationCallback` delegálására.</p>|

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

## <a id="https-transport"></a>HTTPS engedélyezése – biztonságos átvitel csatorna

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | 3. NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Lépések** | Az alkalmazás konfigurációjában kell győződjön meg arról, hogy bizalmas adatokat az elérésére használja a HTTPS.<ul><li>**Magyarázat:** egy alkalmazás kezeli a bizalmas adatokat, és üzenet szintű titkosítást használ, akkor azt csak akkor engedélyezhető egy átviteli titkosított csatornán keresztül kommunikálnak.</li><li>**JAVASLATOK:** győződjön meg arról, hogy a HTTP-n le van tiltva, és engedélyezze a HTTPS átviteli helyette. Helyettesítse be például a `<httpTransport/>` az `<httpsTransport/>` címke. Ne támaszkodjon kizárólag egy hálózati konfigurációt (tűzfal) garantálja, hogy az alkalmazás csak biztonságos csatornán keresztül elérhetők. Egy világnézeti szempontjából az alkalmazás nem szabad függenie a hálózat és a biztonságot.</li></ul><p>A gyakorlati szempontjából a hálózat biztonságának beállítása felelős személyek nem mindig követik nyomon az alkalmazás biztonsági követelményeinek megfelelően Modellezhet.</p>|

## <a id="message-protection"></a>A WCF: Set Message biztonsági EncryptAndSign és védelmi szint

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Lépések** | <ul><li>**Magyarázat:** , amikor a védelem szintje "none", letiltja az üzenet védelmet. Titkosítás és a kódintegritási beállítás megfelelő szinten érhető el.</li><li>**JAVASLATOK:**<ul><li>Amikor `Mode=None` -üzenetet a védelem letiltása</li><li>Amikor `Mode=Sign` -jelentkezik azonban nem titkosítja az üzenetet; kell használni, amikor a fontos adatok integritásának megőrzése</li><li>Amikor `Mode=EncryptAndSign` -tünetek és titkosítja az üzenetet</li></ul></li></ul><p>Fontolja meg a titkosítási kikapcsolásával, és csak aláírási az üzenetet, amikor csak szüksége van semmiképp sem bizalmas adatok integritásának ellenőrzését. Ez akkor lehet hasznos műveletek vagy szolgáltatási szerződések érvényesítenie kell az eredeti küldő, de nem bizalmas adatok átvitele. Ha a védelmi szint csökkentését, ügyeljen arra, hogy az üzenet nem tartalmaz személyazonosításra alkalmas adatokat (PII).</p>|

### <a name="example"></a>Példa
A szolgáltatás és a művelet csak alá az üzenetet konfigurálása a következő példákban látható. Szolgáltatási szerződés példa `ProtectionLevel.Sign`: A következő egy példa a szolgáltatási szerződés szintjén ProtectionLevel.Sign használatával: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Példa
Művelet szerződés példa `ProtectionLevel.Sign` (a szabályozható): az alábbiakban egy példát a `ProtectionLevel.Sign` OperationContract szinten:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>A WCF: Egy alacsonyabb szintű fiók használata a WCF-szolgáltatás futtatásához

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Lépések** | <ul><li>**Magyarázat:** rendszergazdai vagy a magas jogosultságú fiók a WCF-szolgáltatások nem futnak. szolgáltatások biztonsági sérülés esetén nagy hatású eredményez.</li><li>**JAVASLATOK:** gazdagépre a WCF-szolgáltatást, mivel az alkalmazás támadási felület csökkentése, és csökkentheti a potenciális károknak, ha vannak megtámadott alacsonyabb szintű fiók használatára. Ha a szolgáltatás fiók van szüksége további hozzáférési jogok infrastruktúra-erőforrások, például az MSMQ, az Eseménynapló, teljesítményszámlálók és a fájlrendszerhez, megfelelő engedélyeket kell megadni az ezekhez az erőforrásokhoz, hogy a WCF szolgáltatás sikeresen futtatható.</li></ul><p>Ha a szolgáltatás nevében az eredeti hívó erőforrásoknál hozzáférésre van szüksége, használja megszemélyesítés és a delegálási áramlanak a hívójának identitását az alsóbb rétegbeli engedélyezési ellenőrzésére. Egy alkalmazásfejlesztési forgatókönyvet használja a helyi hálózati szolgáltatásfiók – amelyet a speciális beépített fiók, amely korlátozott jogosultsággal rendelkezik. Éles forgatókönyvekben hozzon létre egy egyéni tartomány alacsonyabb szintű szolgáltatásfiókot.</p>|

## <a id="webapi-https"></a>Összes forgalom kényszerített webes API-k HTTPS-kapcsolaton keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A webes API-vezérlő SSL kényszerítése](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Lépések** | Ha egy alkalmazás egy HTTPS- és a egy HTTP-kötést, az ügyfelek továbbra is használhatja a HTTP hozzáférhetnek a webhelyhez. Ennek megelőzése érdekében egy művelet szűrő használatával győződjön meg arról, hogy védett API-kérések mindig HTTPS-kapcsolaton keresztül.|

### <a name="example"></a>Példa 
A következő kód bemutatja egy webes API-t hitelesítési szűrő, amely ellenőrzi az SSL-hez: 
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
Adja hozzá ezt a szűrőt minden olyan webes API-műveletek, amely az SSL használatának megkövetelése: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Gondoskodjon arról, hogy az Azure Redis Cache kommunikáció SSL-en keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Redis Cache | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure redis Cache SSL-támogatás](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Lépések** | Redis Cache kiszolgáló nem támogatja az SSL beépített, de az Azure Redis Cache nem. Ha az Azure Redis Cache csatlakozik, és az ügyfél támogatja az SSL-t, például a StackExchange.Redis, akkor SSL kell használnia. Alapértelmezés szerint nem SSL port le van tiltva az új Azure Redis Cache-példányokban. Győződjön meg arról, hogy biztonságos alapértelmezett beállítást, nem módosulnak, ha nincs függőség az SSL-támogatás a redis-ügyfelek számára. |

Vegye figyelembe, hogy a Redis célja, hogy a belső megbízható környezetek megbízható ügyfelek számára érhető el. Ez azt jelenti, hogy általában nem érdemes közvetlenül az internethez vagy, általában nem megbízható ügyfeleket ahol közvetlenül hozzáférhet a Redis TCP-port vagy a UNIX-szoftvercsatorna-környezetben a Redis-példány elérhetővé. 

## <a id="device-field"></a>Eszköz biztonságossá tételéhez és a helyszíni átjárók közötti kommunikációhoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT helyszíni átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | IP-alapú eszközök esetén a az adatok védelmére átvitel csatorna SSL/TLS általában a kommunikációs protokoll sikerült beágyazva. Egyéb protokollok, amelyek nem támogatják az SSL/TLS vizsgálja meg, ha biztonságos verziói, a protokoll, amely a szállítási vagy üzenet rétegben biztonságot nyújtanak. |

## <a id="device-cloud"></a>Biztonságos eszköz és átjáró közötti kommunikációhoz SSL/TLS segítségével

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A kommunikációs protokoll kiválasztása](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Lépések** | HTTP/AMQP és MQTT protokoll használatával SSL/TLS biztonságos. |
