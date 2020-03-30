---
title: A Microsoft Threat Modeling Tool kommunikációs biztonsága
titleSuffix: Azure
description: a fenyegetésmodellezési eszközben elérhető fenyegetések enyhítése
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 39ee0fa2dc973cd6c20756cae2024af79d1375dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294154"
---
# <a name="security-frame-communication-security--mitigations"></a>Biztonsági keret: Kommunikáció biztonsága | Enyhítése 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Biztonságos kommunikáció az Event Hub-hoz SSL/TLS használatával](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Ellenőrizze a szolgáltatásfiók jogosultságait, és ellenőrizze, hogy az egyéni szolgáltatások vagy ASP.NET oldalak tiszteletben tartják-e a CRM biztonságát](#priv-aspnet)</li></ul> |
| **Azure-adatgyár** | <ul><li>[Adatkezelési átjáró használata a helyszíni SQL Server és az Azure Data Factory csatlakoztatása közben](#sqlserver-factory)</li></ul> |
| **Identitás-kiszolgáló** | <ul><li>[Annak ellenőrzése, hogy az identitáskiszolgálóra irányuló összes forgalom HTTPS-kapcsolaton keresztül történik](#identity-https)</li></ul> |
| **Webalkalmazás** | <ul><li>[Az SSL-, TLS- és DTLS-kapcsolatok hitelesítéséhez használt X.509 tanúsítványok ellenőrzése](#x509-ssltls)</li><li>[SSL-tanúsítvány konfigurálása egyéni tartományhoz az Azure App Service-ben](#ssl-appservice)</li><li>[Az Azure App Service-be irányuló összes forgalom kényszerítése HTTPS-kapcsolaton keresztül](#appservice-https)</li><li>[A HTTP szigorú átviteli biztonságának engedélyezése (HSTS)](#http-hsts)</li></ul> |
| **Adatbázis** | <ul><li>[Sql-kiszolgálókapcsolat titkosításának és tanúsítvány-ellenőrzésének biztosítása](#sqlserver-validation)</li><li>[Titkosított kommunikáció kényszerítése AZ SQL-kiszolgálóval](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Annak biztosítása, hogy az Azure Storage-ba irányuló kommunikáció HTTPS-kapcsolaton keresztül történik](#comm-storage)</li><li>[MD5 kivonat ának ellenőrzése a blob letöltése után, ha a HTTPS nem engedélyezhető](#md5-https)</li><li>[Az SMB 3.0-val kompatibilis ügyfél használata az Azure fájlmegosztások átvitele közbeni adattitkosításának biztosításához](#smb-shares)</li></ul> |
| **Mobil ügyfél** | <ul><li>[Tanúsítványrögzítés megvalósítása](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS engedélyezése - Biztonságos átviteli csatorna](#https-transport)</li><li>[WCF: Az üzenet biztonsági védelmi szintjének beállítása EncryptAndSign](#message-protection)</li><li>[WCF: A WCF-szolgáltatás futtatásához használja a legkevésbé kiemelt jogosultságú fiókot](#least-account-wcf)</li></ul> |
| **Webes API** | <ul><li>[Az összes forgalom kényszerítése webes API-kra HTTPS-kapcsolaton keresztül](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Annak biztosítása, hogy az Azure Cache for Redis kommunikációja SSL-en keresztül imitorokon](#redis-ssl)</li></ul> |
| **IoT field átjáró** | <ul><li>[Biztonságos eszköz-helyszíni átjáró kommunikáció](#device-field)</li></ul> |
| **IoT felhőátjáró** | <ul><li>[Biztonságos eszköz-felhő átjáró kommunikáció SSL/TLS használatával](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Biztonságos kommunikáció az Event Hub-hoz SSL/TLS használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Eseményközpontok hitelesítése és biztonsági modellje – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépéseket** | Biztonságos AMQP- vagy HTTP-kapcsolatok az Event Hub-hoz SSL/TLS használatával |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Ellenőrizze a szolgáltatásfiók jogosultságait, és ellenőrizze, hogy az egyéni szolgáltatások vagy ASP.NET oldalak tiszteletben tartják-e a CRM biztonságát

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Ellenőrizze a szolgáltatásfiók jogosultságait, és ellenőrizze, hogy az egyéni szolgáltatások vagy ASP.NET oldalak tiszteletben tartják-e a CRM biztonságát |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Adatkezelési átjáró használata a helyszíni SQL Server és az Azure Data Factory csatlakoztatása közben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Data Factory | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Csatolt szolgáltatástípusok – Azure és helyszíni szolgáltatások |
| **Referencia**              |[Adatok áthelyezése a helyszíni és az Azure Data Factory között,](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway) [adatkezelési átjáró](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Lépéseket** | <p>Az Adatkezelési átjáró (DMG) eszköz szükséges a corpnet vagy tűzfal mögött védett adatforrásokhoz való csatlakozáshoz.</p><ol><li>A gép zárolása elszigeteli a DMG eszközt, és megakadályozza, hogy a hibás programok megsérüljenek vagy szaglásznak az adatforrásgépen. (Pl. a legújabb frissítéseket telepíteni kell, engedélyezni kell a minimálisan szükséges portokat, az ellenőrzött fiókok kiépítését, a naplózást, a lemeztitkosítást stb.)</li><li>A Data Gateway kulcsot gyakran el kell forgatni, vagy amikor a DMG szolgáltatásfiók jelszava megújul</li><li>A Link Szolgáltatáson keresztül történő adattovábbítást titkosítani kell</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Annak ellenőrzése, hogy az identitáskiszolgálóra irányuló összes forgalom HTTPS-kapcsolaton keresztül történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitás-kiszolgáló | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [IdentityServer3 - Kulcsok, aláírások és kriptográfia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - Telepítés](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépéseket** | Alapértelmezés szerint az IdentityServer megköveteli, hogy az összes bejövő kapcsolat HTTPS-kapcsolaton keresztül jöjjön létre. Teljesen kötelező, hogy az IdentityServer szolgáltatással való kommunikáció csak biztonságos átviteleken keresztül történik. Vannak bizonyos üzembe helyezési forgatókönyvek, például az SSL-kiszervezés, ahol ez a követelmény lehet lazítani. További információt az Identitáskiszolgáló telepítési lapján talál a hivatkozásokban. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Az SSL-, TLS- és DTLS-kapcsolatok hitelesítéséhez használt X.509 tanúsítványok ellenőrzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Az SSL-t, TLS-t vagy DTLS-t használó alkalmazásoknak teljes mértékben ellenőrizniük kell azoknak az entitásoknak az X.509 tanúsítványait, amelyekhez csatlakoznak. Ez magában foglalja a bizonyítványok ellenőrzését:</p><ul><li>Tartománynév</li><li>Érvényességi dátumok (mind a kezdő, mind a lejárati dátumok)</li><li>Visszavonásállapota</li><li>Használat (például kiszolgálók kiszolgálói hitelesítése, ügyfelek ügyfélhitelesítése)</li><li>Bizalmi lánc. A tanúsítványoknak olyan legfelső szintű hitelesítésszolgáltatóhoz (CA) kell láncolniuk, amelyet a platform megbízhatóvagy a rendszergazda kifejezetten konfigurált</li><li>A tanúsítvány nyilvános kulcsának kulcshosszát 2048 bites >kell</li><li>Kivonatoló algoritmus kell SHA256 és felett |

## <a name="configure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>SSL-tanúsítvány konfigurálása egyéni tartományhoz az Azure App Service-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - Azure |
| **Referencia**              | [HTTPS engedélyezése egy alkalmazáshoz az Azure App Service-ben](../../app-service/configure-ssl-bindings.md) |
| **Lépéseket** | Alapértelmezés szerint az Azure már engedélyezi a HTTPS-t minden olyan alkalmazáshoz, amely helyettesítő tanúsítvánnyal rendelkezik a *.azurewebsites.net tartományhoz. Azonban, mint minden helyettesítő tartományban, ez nem olyan biztonságos, mint egy egyéni tartomány használata saját tanúsítvánnyal [Refer](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Javasoljuk, hogy engedélyezze az SSL-t az egyéni tartományhoz, amelyen keresztül a telepített alkalmazás elérhető lesz|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Az Azure App Service-be irányuló összes forgalom kényszerítése HTTPS-kapcsolaton keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - Azure |
| **Referencia**              | [HTTPS kényszerítése az Azure App Service szolgáltatásban](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Lépéseket** | <p>Bár az Azure már engedélyezi a HTTPS-t az Azure alkalmazásszolgáltatásokhoz a tartomány *.azurewebsites.net, nem kényszeríti https. A látogatók továbbra is hozzáférhetnek az alkalmazáshoz HTTP használatával, ami veszélyeztetheti az alkalmazás biztonságát, ezért a HTTPS protokollt explicit módon kell érvényesíteni. ASP.NET MVC-alkalmazásoknak a [RequireHttps szűrőt](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) kell használniuk, amely egy nem biztonságos HTTP-kérelmet kényszerít a HTTPS-en keresztüli újraküldésre.</p><p>Másik lehetőségként az URL-újraírási modul, amely az Azure App Service tartalmazza a HTTPS kényszerítése. URL-újraírási modul lehetővé teszi a fejlesztők számára, hogy meghatározza a szabályokat, amelyek a bejövő kérelmekre, mielőtt a kérelmek et az alkalmazásnak. Az URL-újraírási szabályok az alkalmazás gyökerében tárolt web.config fájlban vannak definiálva</p>|

### <a name="example"></a>Példa
A következő példa egy alapvető URL-újraírási szabályt tartalmaz, amely az összes bejövő forgalmat HTTPS használatára kényszeríti
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
Ez a szabály úgy működik, hogy egy 301-es HTTP-állapotkódot (végleges átirányítást) ad vissza, amikor a felhasználó HTTP-t használó lapot kér. A 301-es átirányítja a kérelmet ugyanarra az URL-címre, mint a látogató kért, de lecseréli a kérelem HTTP-részét HTTPS-re. Például `HTTP://contoso.com` a ( a `HTTPS://contoso.com`) átirányítása a. 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>A HTTP szigorú átviteli biztonságának engedélyezése (HSTS)

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [OWASP HTTP Szigorú Transport Security Cheat Sheet](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Lépéseket** | <p>A HTTP Strict Transport Security (HSTS) egy speciális válaszfejléc használatával egy webalkalmazás által megadott opt-in biztonsági javítás. Ha egy támogatott böngésző megkapja ezt a fejlécet, a böngésző megakadályozza, hogy a kommunikáció HTTP-n keresztül a megadott tartományba érkezzenek, és ehelyett az összes kommunikációt HTTPS-en keresztül küldi el. Azt is megakadályozza, HTTPS átkattintási utasításokat a böngészőkben.</p><p>A HSTS megvalósításához a következő válaszfejlécet globálisan kell konfigurálni egy webhelyhez, akár kódban, akár konfigurációban. Szigorú közlekedésbiztonság: max-age=300; a includeSubDomains HSTS a következő fenyegetéseket kezeli:</p><ul><li>Felhasználói könyvjelzők vagy `https://example.com` manuális antropológus, és a beépített támadó nak van kitéve: A HSTS automatikusan átirányítja a HTTP-kérelmeket a céltartomány HTTPS-re</li><li>A tisztán HTTPS-nek szánt webalkalmazás véletlenül HTTP-hivatkozásokat tartalmaz, vagy HTTP-n keresztüli tartalmat szolgál ki: A HSTS automatikusan átirányítja a HTTP-kérelmeket a céltartomány HTTPS-re</li><li>A "man-in-the-middle" támadó érvénytelen tanúsítvánnyal próbálja elfogni az áldozat felhasználójától érkező forgalmat, és reméli, hogy a felhasználó elfogadja a hibás tanúsítványt: A HSTS nem engedélyezi a felhasználó számára az érvénytelen tanúsítványüzenet felülbírálását</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Sql-kiszolgálókapcsolat titkosításának és tanúsítvány-ellenőrzésének biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure  |
| **Attribútumok**              | SQL verzió - V12 |
| **Referencia**              | [Gyakorlati tanácsok az SQL-adatbázis biztonságos kapcsolati karakterláncainak írásához](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Lépéseket** | <p>Az SQL Database és az ügyfélalkalmazás közötti minden kommunikáció mindig titkosítva van a Secure Sockets Layer (SSL) használatával. Az SQL Database nem támogatja a titkosítatlan kapcsolatokat. A tanúsítványok alkalmazáskóddal vagy -eszközökkel történő érvényesítéséhez kifejezetten kérjen titkosított kapcsolatot, és ne bízzon meg a kiszolgálótanúsítványban. Ha az alkalmazáskód vagy -eszközök nem kérnek titkosított kapcsolatot, akkor is megkapják a titkosított kapcsolatokat</p><p>Előfordulhat azonban, hogy nem érvényesítik a kiszolgálói tanúsítványokat, és így ki vannak téve az "ember középen" támadásoknak. Tanúsítványok érvényesítéséhez ADO.NET alkalmazáskóddal, állítsa be `Encrypt=True` és `TrustServerCertificate=False` az adatbázis kapcsolati karakterláncában. Ha az SQL Server Management Studio szolgáltatáson keresztül szeretné érvényesíteni a tanúsítványokat, nyissa meg a Csatlakozás kiszolgálóhoz párbeszédpanelt. Kattintson a Kapcsolat tulajdonságai lapon a Kapcsolat titkosítása gombra</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Titkosított kommunikáció kényszerítése AZ SQL-kiszolgálóval

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | OnPrem között |
| **Attribútumok**              | SQL verzió - MsSQL2016, SQL verzió - MsSQL2012, SQL verzió - MsSQL2014 |
| **Referencia**              | [Titkosított kapcsolatok engedélyezése az adatbázis-kezelő motorral](https://msdn.microsoft.com/library/ms191192)  |
| **Lépéseket** | Az SSL-titkosítás engedélyezése növeli az SQL Server és az alkalmazások példányai között a hálózatokon továbbított adatok biztonságát. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Annak biztosítása, hogy az Azure Storage-ba irányuló kommunikáció HTTPS-kapcsolaton keresztül történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Azure Storage átviteli szintű titkosítás – HTTPS használata](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Lépéseket** | Az Azure Storage-adatok átvitel közbeni biztonságának biztosítása érdekében mindig használja a HTTPS protokollt a REST API-k hívásakor vagy a tárolóban lévő objektumok elérésekor. Emellett a megosztott hozzáférésű aláírások, amelyek az Azure Storage-objektumokhoz való hozzáférés delegálására használhatók, tartalmaznak egy lehetőséget annak megadására, hogy csak a HTTPS protokoll használható megosztott hozzáférésű aláírások használatakor, biztosítva, hogy a SAS-jogkivonatokkal kapcsolatot küldő a megfelelő protokollt.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>MD5 kivonat ának ellenőrzése a blob letöltése után, ha a HTTPS nem engedélyezhető

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Referencia**              | [Windows Azure Blob MD5 – áttekintés](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Lépéseket** | <p>A Windows Azure Blob szolgáltatás mechanizmusokat biztosít az adatok integritásának biztosítására mind az alkalmazás, mind az átviteli rétegeken. Ha bármilyen okból http-t kell használnia https helyett, és blokkblobokkal dolgozik, az MD5-ellenőrzés segítségével ellenőrizheti az átvitt blobok integritását</p><p>Ez segít a hálózati/átviteli réteg hibáielleni védelemben, de nem feltétlenül a köztes támadásokkal. Ha használhatja a HTTPS-t, amely átviteli szintű biztonságot nyújt, akkor az MD5-ellenőrzés használata felesleges és szükségtelen.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Az SMB 3.0-kompatibilis ügyfél használata az Azure-fájlmegosztások on-transit adattitkosításának biztosításához

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType - fájl |
| **Referencia**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB támogatás Windows-ügyfelek számára](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Lépéseket** | Az Azure File Storage támogatja a HTTPS-t a REST API használatakor, de gyakrabban használják smb fájlmegosztásként egy virtuális géphez. Az SMB 2.1 nem támogatja a titkosítást, így a kapcsolatok csak az Azure-ban ugyanabban a régióban engedélyezettek. Az SMB 3.0 azonban támogatja a titkosítást, és használható a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10 rendszerrel, lehetővé téve a régiók közötti hozzáférést, sőt az asztalon való hozzáférést is. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Tanúsítványrögzítés megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, Windows Phone |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Tanúsítvány és nyilvános kulcs rögzítése](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Lépéseket** | <p>Tanúsítvány rögzítése véd a Man-In-The-Middle (MITM) támadások ellen. A rögzítés az a folyamat, amelynek során egy állomást társítanak a várt X509-es tanúsítványukkal vagy nyilvános kulcsukkal. Ha egy tanúsítvány vagy nyilvános kulcs ismert vagy látható egy állomás, a tanúsítvány vagy a nyilvános kulcs van társítva, vagy "rögzített" az állomás. </p><p>Így amikor egy ellenség megpróbálja végrehajtani az SSL MITM támadást, az SSL-kézfogás során a támadó kiszolgálójának kulcsa eltér a rögzített tanúsítvány kulcsától, és a kérés el lesz `ServerCertificateValidationCallback` vetve, így a MITM-tanúsítvány rögzítése a ServicePointManager meghatalmazottjának megvalósításával érhető el.</p>|

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

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>HTTPS engedélyezése - Biztonságos átviteli csatorna

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | NET keret rendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Lépéseket** | Az alkalmazás konfigurációjának biztosítania kell, hogy a HTTPS a bizalmas információkhoz való minden hozzáféréshez használható legyen.<ul><li>**MAGYARÁZAT:** Ha egy alkalmazás bizalmas adatokat kezel, és nem használ üzenetszintű titkosítást, akkor csak titkosított átviteli csatornán keresztül szabad kommunikálnia.</li><li>**AJÁNLÁSOK:** Győződjön meg arról, hogy a HTTP-átvitel le van tiltva, és helyette engedélyezze a HTTPS-átvitelt. Cserélje le például a `<httpTransport/>` címkére. `<httpsTransport/>` Ne hagyatkozzon hálózati konfigurációra (tűzfalra) annak biztosítása érdekében, hogy az alkalmazás csak biztonságos csatornán keresztül érhető el. Filozófiai szempontból az alkalmazás nem függhet a hálózat biztonságától.</li></ul><p>Gyakorlati szempontból a hálózat biztosításáért felelős személyek nem mindig követik nyomon az alkalmazás biztonsági követelményeit, ahogy fejlődnek.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Az üzenet biztonsági védelmi szintjének beállítása EncryptAndSign

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Lépéseket** | <ul><li>**MAGYARÁZAT:** Ha a védelmi szint "none" lesz, letiltja az üzenetek védelmét. A titoktartás és a sértetlenség megfelelő szintű beállítással érhető el.</li><li>**Ajánlások:**<ul><li>when `Mode=None` - Letiltja az üzenetvédelmet</li><li>mikor `Mode=Sign` - Jelek, de nem titkosítja az üzenetet; akkor kell használni, ha az adatok integritása fontos</li><li>when `Mode=EncryptAndSign` - Jelek és titkosítja az üzenetet</li></ul></li></ul><p>Fontolja meg a titkosítás kikapcsolását, és csak akkor írja alá az üzenetet, ha csak az adatok sértetlenségét kell ellenőriznie, titoktartási aggályok nélkül. Ez olyan műveletek vagy szolgáltatási szerződések esetén lehet hasznos, amelyekben ellenőriznie kell az eredeti feladót, de a rendszer nem továbbít bizalmas adatokat. A védelmi szint csökkentésekor ügyeljen arra, hogy az üzenet ne tartalmazzon személyes adatokat.</p>|

### <a name="example"></a>Példa
A szolgáltatás és a művelet konfigurálása az üzenet aláírására az alábbi példákban látható. Szervizszerződés `ProtectionLevel.Sign`példa: A következő példa a ProtectionLevel.Sign szolgáltatás szerződés szintjén történő használatára: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Példa
Működési szerződés `ProtectionLevel.Sign` példa (a granular Control): A `ProtectionLevel.Sign` következő példa a használata a OperationContract szinten:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: A WCF-szolgáltatás futtatásához használja a legkevésbé kiemelt jogosultságú fiókot

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Lépéseket** | <ul><li>**MAGYARÁZAT:** Ne futtassa a WCF-szolgáltatásokat rendszergazdai vagy magas jogosultsági szintű fiók alatt. a szolgáltatások veszélyeztetése esetén ez nagy hatást fog eredményezni.</li><li>**AJÁNLÁSOK:** A WCF-szolgáltatás üzemeltetéséhez használjon egy legkevésbé kiemelt jogosultságú fiókot, mert csökkenti az alkalmazás támadási felületét, és csökkenti a lehetséges károkat, ha megtámadják. Ha a szolgáltatásfiók további hozzáférési jogokat igényel az infrastruktúra-erőforrásokhoz, például az MSMQ-hoz, az eseménynaplóhoz, a teljesítményszámlálókhoz és a fájlrendszerhez, megfelelő engedélyeket kell adni ezekhez az erőforrásokhoz, hogy a WCF szolgáltatás sikeresen fusson.</li></ul><p>Ha a szolgáltatásnak az eredeti hívó nevében bizonyos erőforrásokhoz kell hozzáférnie, a megszemélyesítés és a delegálás segítségével folytasa a hívó identitását egy alsóbb rétegbeli engedélyezési ellenőrzéshez. Fejlesztési forgatókönyv esetén használja a helyi hálózati szolgáltatásfiókot, amely egy speciális beépített fiók, amely csökkentett jogosultságokkal rendelkezik. Éles környezetben hozzon létre egy legkevésbé kiemelt jogosultságú egyéni tartományi szolgáltatásfiókot.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Az összes forgalom kényszerítése webes API-kra HTTPS-kapcsolaton keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [SSL kényszerítése webes API-vezérlőben](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Lépéseket** | Ha egy alkalmazás https- és HTTP-kötéssel is rendelkezik, az ügyfelek továbbra is használhatják a HTTP-t a webhely eléréséhez. Ennek megakadályozása érdekében használjon műveletszűrőt annak biztosítására, hogy a védett API-k kérései mindig HTTPS-kapcsolaton keresztül legyenek.|

### <a name="example"></a>Példa 
A következő kód egy Webes API-hitelesítési szűrőt jelenít meg, amely az SSL-t keresi: 
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
Adja hozzá ezt a szűrőt minden Olyan webes API-művelethez, amelyhez SSL szükséges: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-ssl"></a><a id="redis-ssl"></a>Annak biztosítása, hogy az Azure Cache for Redis kommunikációja SSL-en keresztül imitorokon

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Cache for Redis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Azure Redis SSL támogatása](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Lépéseket** | Redis-kiszolgáló nem támogatja az SSL a dobozból, de az Azure Cache for Redis nem. Ha csatlakozik az Azure Cache for Redis és az ügyfél támogatja az SSL, például StackExchange.Redis, majd ssl-t kell használnia. Alapértelmezés szerint a nem SSL-port le van tiltva az új Azure-gyorsítótár redis példányok. Győződjön meg arról, hogy a biztonságos alapértelmezett értékek nem változnak, kivéve, ha a redis-ügyfelek SSL-támogatástól függ. |

Kérjük, vegye figyelembe, hogy a Redis megbízható ügyfelek által ilegelt megbízható környezetekben érhető el. Ez azt jelenti, hogy általában nem jó ötlet a Redis-példányt közvetlenül az internetnek, vagy általában egy olyan környezetnek kitenni, ahol a nem megbízható ügyfelek közvetlenül hozzáférhetnek a Redis TCP-porthoz vagy unix-szoftvercsatornához. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Biztonságos eszköz-helyszíni átjáró kommunikáció

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT field átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | IP-alapú eszközök esetében a kommunikációs protokollt általában SSL/TLS-csatornába lehet ágyazni az átvitel közbeni adatok védelme érdekében. Más, nem az SSL/TLS protokollt nem támogató protokollok esetében vizsgálja meg, hogy vannak-e olyan biztonságos verziók, amelyek biztonságot nyújtanak az átviteli vagy üzenetrétegen. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Biztonságos eszköz-felhő átjáró kommunikáció SSL/TLS használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Válassza ki a kommunikációs protokollt](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Lépéseket** | Biztonságos HTTP/AMQP vagy MQTT protokollok SSL/TLS használatával. |
