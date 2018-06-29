---
title: A vizsgálati és naplózási - modellezési eszköz Microsoft fenyegetés - Azure |} Microsoft Docs
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
ms.openlocfilehash: 8837dfaf156e5a4d07598f2c58694663a9ff5580
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029981"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Biztonsági keret: A vizsgálati és naplózási |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[A megoldásban bizalmas entitások azonosítására és változásának naplózása megvalósítása](#sensitive-entities)</li></ul> |
| **Webalkalmazás** | <ul><li>[Győződjön meg arról, hogy a vizsgálati és naplózási megvalósul az alkalmazás](#auditing)</li><li>[Ellenőrizze, hogy naplóváltás és elválasztási helyen](#log-rotation)</li><li>[Győződjön meg arról, hogy az alkalmazás nem naplózza bizalmas felhasználói adatok](#log-sensitive-data)</li><li>[Győződjön meg arról, hogy naplózási és naplófájlok rendelkezik-e a korlátozott hozzáférés](#log-restricted-access)</li><li>[Győződjön meg arról, hogy a felhasználó felügyeleti események bejelentkezve](#user-management)</li><li>[Győződjön meg arról, hogy rendelkezik-e a rendszer beépített védelmet való visszaélés elleni](#inbuilt-defenses)</li><li>[Az Azure App Service web Apps diagnosztikai naplózás engedélyezése](#diagnostics-logging)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg arról, hogy bejelentkezés naplózása engedélyezve van az SQL Server](#identify-sensitive-entities)</li><li>[Az Azure SQL fenyegetésészlelés engedélyezéséhez](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Azure Storage Analytics segítségével az Azure Storage-hozzáférés naplózása](#analytics)</li></ul> |
| **WCF** | <ul><li>[Elegendő naplózási megvalósítása](#sufficient-logging)</li><li>[Valósítja meg a megfelelő naplózási hiba kezelése](#audit-failure-handling)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a vizsgálati és naplózási megvalósul webes API](#logging-web-api)</li></ul> |
| **Az IoT-mező átjáró** | <ul><li>[Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul mező átjáró](#logging-field-gateway)</li></ul> |
| **Az IoT átjáró** | <ul><li>[Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul átjáró](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>A megoldásban bizalmas entitások azonosítására és változásának naplózása megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | A bizalmas adatokat tartalmazó megoldásban entitások azonosítása, és ezek entitásokat és mezőket a naplózás módosítása megvalósítása |

## <a id="auditing"></a>Győződjön meg arról, hogy a vizsgálati és naplózási megvalósul az alkalmazás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | A vizsgálati és naplózási összes engedélyezése. Naplók kell rögzítheti a felhasználói környezet. Az összes fontos események azonosítása, és az események. A központi naplózás megvalósítása |

## <a id="log-rotation"></a>Ellenőrizze, hogy naplóváltás és elválasztási helyen

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Naplóváltás egy automatikus folyamat, amelyben dátummal naplófájlok archivált rendszerfelügyelet használatban. Kiszolgálók, amelyek nagy alkalmazások gyakran irányuló minden kérés naplózására: nagyméretű naplók állásuk naplóváltás módja a korlátozza a teljes méretét, a naplók, miközben továbbra is lehetővé teszi a legutóbbi események elemzését. </p><p>Naplófájl elválasztási alapvetően azt jelenti, hogy a napló tárolására van lévő fájlok egy másik partíció, mint amelyen az operációs rendszer vagy alkalmazás fut egy szolgáltatásmegtagadási támadás, vagy az alkalmazás alacsonyabb verziójúra változtatása megelőzése érdekében a teljesítményét</p>|

## <a id="log-sensitive-data"></a>Győződjön meg arról, hogy az alkalmazás nem naplózza bizalmas felhasználói adatok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Ellenőrizze, hogy a hely nem jelentkezik elküldése bizalmas adatokat. Ellenőrizze, hogy szándékos naplózását, valamint a tervezési szempontokat által okozott hatásai. Bizalmas adatok közé tartoznak például:</p><ul><li>Felhasználói hitelesítő adatok</li><li>Társadalombiztosítási szám vagy egyéb fontos adatokat</li><li>Hitelkártyaszámok vagy egyéb pénzügyi információ</li><li>Állapottal kapcsolatos adatok</li><li>A titkos kulcsok vagy egyéb adatok visszafejteni a titkosított adatokat használható</li><li>Hatékonyabb támadható meg az alkalmazás használható rendszer vagy alkalmazás információk</li></ul>|

## <a id="log-restricted-access"></a>Győződjön meg arról, hogy naplózási és naplófájlok rendelkezik-e a korlátozott hozzáférés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Ellenőrizze, hogy hozzáférési jogokat a naplófájlokba megfelelően vannak beállítva. Alkalmazás fiókok csak írási hozzáféréssel és operátorok kell, és a technikai támogatási csoporthoz olvasási hozzáféréssel kell rendelkeznie, igény szerint.</p><p>Rendszergazdák fiókok azok a csak fiókok, amelyek teljes hozzáféréssel kell rendelkeznie. Ellenőrizze a naplófájlokat, azok megfelelően korlátozott Windows ACL:</p><ul><li>Alkalmazás fiókok csak írási hozzáféréssel kell rendelkeznie.</li><li>Operátorok és a technikai támogatási csoporthoz kell csak olvasási hozzáféréssel, igény szerint</li><li>A rendszergazdák csak fiókoknak teljes hozzáféréssel kell rendelkeznie</li></ul>|

## <a id="user-management"></a>Győződjön meg arról, hogy a felhasználó felügyeleti események bejelentkezve

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Győződjön meg arról, hogy az alkalmazás figyeli a felhasználó felügyeleti események például a sikeres és sikertelen a felhasználói bejelentkezéseket, a jelszó alaphelyzetbe állítása, jelszó-változtatásának, a fiók zárolása, a felhasználói regisztráció. Ennek során észleli, és reagálni azokra potenciálisan gyanús viselkedését ez segíti. Emellett lehetővé teszi az operations adatgyűjtést; Ahhoz például, hogy nyomon követése ki fér hozzá az alkalmazáshoz</p>|

## <a id="inbuilt-defenses"></a>Győződjön meg arról, hogy rendelkezik-e a rendszer beépített védelmet való visszaélés elleni

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Vezérlők helyen kell lennie, amely throw biztonsági kivétel alkalmazás helytelen használat esetén. Például ha egy támadó megpróbálja rosszindulatú kódot, amely nem egyezik meg a reguláris kifejezéssel szúrjon bemenet-ellenőrzéshez van beállítva, biztonsági kivételt is jelezni, amely lehet a rendszer visszaélés lehetőségét előzetes</p><p>Például azt javasoljuk, hogy a rendszer naplózza a biztonsági kivételek és a következő problémák végzett műveleteket:</p><ul><li>Bemenet-ellenőrzés</li><li>CSRF megsértése</li><li>Találgatásos (felhasználónként és erőforrás-kérelmek számát a felső határ)</li><li>Fájl feltöltése megsértése</li><ul>|

## <a id="diagnostics-logging"></a>Az Azure App Service web Apps diagnosztikai naplózás engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az Azure a beépített diagnosztika révén segít az Azure App Service-ben működtetett webalkalmazások hibakeresésében. Is vonatkozik, az API apps és a mobilalkalmazásait. App Service web apps naplózási információkat a webkiszolgáló és a webes alkalmazás diagnosztikai funkciók biztosítanak.</p><p>Ezek logikailag oszthatók web server diagnostics és az application diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Győződjön meg arról, hogy bejelentkezés naplózása engedélyezve van az SQL Server

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Bejelentkezési naplózásának konfigurálása](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Lépések** | <p>Adatbázis bejelentkezési naplózását engedélyezni kell észlelése/erősítse meg jelszó-előállító támadásokat. Fontos a sikertelen bejelentkezési kísérletek rögzítéséhez. Törvényszéki vizsgálatokat közben mindkét sikeres és sikertelen bejelentkezési kísérletek rögzítése biztosít további előnye</p>|

## <a id="threat-detection"></a>Az Azure SQL fenyegetésészlelés engedélyezéséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Az SQL-adatbázis Fenyegetésészlelés első lépései](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Lépések** |<p>A Fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket, amelyek esetleges biztonsági fenyegetéseket jelezhetnek a észleli. Biztonsági, amelyek segítségével a felhasználók észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások a rendellenes tevékenységek reagáljon a lehetséges veszélyforrásokra új réteget biztosít.</p><p>Felhasználók felfedezheti az Azure SQL Database Auditing segítségével határozza meg, ha azok hozzáférést, megsértik vagy az adatbázis adatai kihasználásához kísérlet eredménye gyanús eseményeket.</p><p>A Fenyegetésészlelés egyszerűen potenciális fenyegetések ellen az adatbázisba történő szakértői biztonsági vagy speciális biztonsági rendszerek figyelése kezelése nélkül</p>|

## <a id="analytics"></a>Azure Storage Analytics segítségével az Azure Storage-hozzáférés naplózása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | – |
| **Hivatkozások**              | [Storage Analytics segítségével engedélyezési figyelőtípus](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Lépések** | <p>Minden tárfiók egy engedélyezheti az Azure Storage Analytics naplózási elvégzéséhez és a metrikák adatainak tárolásához. A storage analytics naplók tárolási elérésekor valaki által használt hitelesítési módszert például fontos információkat tartalmaznak.</p><p>Ez valóban akkor hasznos, ha szorosan esetlegesen korán tároló elérésére lehet. Például a Blob Storage tárolóban után állítsa be a tároló összes magán is valósítja meg az SAS-szolgáltatás használata az alkalmazások teljes. Ellenőrizze rendszeresen a naplókat blobok a tárfiókok kulcsait, ami azt jelezheti a biztonság megsértése, érhető el, vagy ha a blobok olyan nyilvános, de nem lehetnek.</p>|

## <a id="sufficient-logging"></a>Elegendő naplózási megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Lépések** | <p>Egy biztonsági incidens után egy megfelelő napló hiánya Törvényszéki erőfeszítéseket is akadályozhatják. A Windows Communication Foundation (WCF) lehetővé teszi annak sikeres vagy sikertelen hitelesítési kísérlet bejelentkezni.</p><p>Naplózza a sikertelen hitelesítési kísérleteket esetleges találgatásos támadások rendszergazdák figyelmeztetést küld. Hasonlóképpen sikeres hitelesítési események naplózása biztosít hasznos napló amikor jogos fiók biztonsága sérül. A WCF szolgáltatás biztonsági eseményekre vonatkozó naplózási funkciója engedélyezése |

### <a name="example"></a>Példa
A következő egy példa konfiguráció, a naplózás engedélyezve
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Valósítja meg a megfelelő naplózási hiba kezelése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Lépések** | <p>Fejlett megoldás kiváltására van konfigurálva nem kivétel Ha és egy naplófájlba írása sikertelen. Ha WCF van beállítva, hogy ne kivételt jelez, ha az nem lehet írni, és egy naplófájlba, a program nem kapnak értesítést a hiba, és kritikus fontosságú biztonsági események naplózását esetleg nem kerül sor.</p>|

### <a name="example"></a>Példa
A `<behavior/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF az alkalmazás nem értesíti, amikor WCF és egy naplófájlba írása sikertelen.
````
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
````
Konfigurálja a WCF értesíteni a program, ha nem lehet írni, és egy naplófájlba. A program egy másik értesítési sémát kell, hogy a szervezet, amely a naplózási nyomvonala nem tartják riasztás. 

## <a id="logging-web-api"></a>Győződjön meg arról, hogy a vizsgálati és naplózási megvalósul webes API

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | A vizsgálati és naplózási a webes API-k engedélyezése. Naplók kell rögzítheti a felhasználói környezet. Az összes fontos események azonosítása, és az események. A központi naplózás megvalósítása |

## <a id="logging-field-gateway"></a>Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul mező átjáró

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT-mező átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Ha egy mező átjáró kapcsolódnak a több eszközön, győződjön meg kapcsolódási kísérletek és hitelesítési állapot (sikeres vagy sikertelen) az egyes eszközök naplózza, és kezelése a mező átjáró a.</p><p>Emellett olyan esetekben, ahol mező átjáró karbantartása az IoT-központ hitelesítő adatokat az egyes eszközök, győződjön meg arról, hogy naplózása történik meg, amikor a rendszer beolvassa a hitelesítő adatokat. A folyamat a naplók rendszeresen feltöltése az Azure IoT Hub/storage a hosszú távú megőrzési fejlesztéséhez.</p> |

## <a id="logging-cloud-gateway"></a>Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul átjáró

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Bevezetés az IoT-központ műveletek figyelése](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Lépések** | <p>Terv a összegyűjtése és tárolása naplózási keresztül gyűjtött adatokat az IoT Hub műveletek figyelését. Engedélyezze a következő felügyeleti kategória:</p><ul><li>Eszköz identitása műveletek</li><li>Eszköz-felhő kommunikáció</li><li>Felhő eszközre kommunikáció</li><li>Kapcsolatok</li><li>Fájlfeltöltések</li></ul>|