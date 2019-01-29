---
title: A vizsgálati és naplózási – Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
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
ms.openlocfilehash: 1d67f981991796b81ba3ab6540631e6d62be8077
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55092149"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Biztonsági keret: A vizsgálati és naplózási |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Azonosítsa a bizalmas entitásokat a megoldásban, és változásának naplózása megvalósítása](#sensitive-entities)</li></ul> |
| **Webalkalmazás** | <ul><li>[Győződjön meg arról, hogy az alkalmazás a vizsgálati és naplózási van kényszerítve](#auditing)</li><li>[Győződjön meg arról, hogy naplóváltás és elkülönítése helyen](#log-rotation)</li><li>[Győződjön meg arról, hogy az alkalmazás nem naplózza a bizalmas felhasználói adatok](#log-sensitive-data)</li><li>[Győződjön meg arról, hogy naplózási és a naplófájlok korlátozott hozzáféréssel rendelkezik](#log-restricted-access)</li><li>[Győződjön meg arról, hogy felhasználói eseményt naplóz](#user-management)</li><li>[Győződjön meg arról, hogy rendelkezik-e a rendszer a beépített, való visszaélés elleni védelem](#inbuilt-defenses)</li><li>[Az Azure App Service web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése](#diagnostics-logging)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg arról, hogy bejelentkezési-naplózás engedélyezve van az SQL Server](#identify-sensitive-entities)</li><li>[Az Azure SQL fenyegetésészlelés engedélyezése](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Storage-hozzáférés naplózása az Azure Storage Analytics használatával](#analytics)</li></ul> |
| **WCF** | <ul><li>[Elegendő naplózási megvalósítása](#sufficient-logging)</li><li>[Végrehajtja a megfelelő naplózási hiba kezelése](#audit-failure-handling)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a vizsgálati és naplózási kényszerítve a webes API-hoz](#logging-web-api)</li></ul> |
| **IoT helyszíni átjáró** | <ul><li>[Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul a helyszíni átjárók](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul Felhőátjáró](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Azonosítsa a bizalmas entitásokat a megoldásban, és változásának naplózása megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | Azonosítsa a bizalmas adatokat tartalmazó megoldás entitásokat és változásának naplózása az adott entitások és mezők megvalósítása |

## <a id="auditing"></a>Győződjön meg arról, hogy az alkalmazás a vizsgálati és naplózási van kényszerítve

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | Engedélyezze a vizsgálati és naplózási lévő valamennyi összetevőnél. Auditnaplók rögzíteni kell a felhasználói környezetet. Azonosítsa az összes fontos eseményekről, és az események. A központi naplózás megvalósítása |

## <a id="log-rotation"></a>Győződjön meg arról, hogy naplóváltás és elkülönítése helyen

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Naplóváltás egy automatizált folyamattal, amelyben dátummal naplófájlok archivált rendszerfelügyelet használatban. Nagy méretű alkalmazások gyakran futtató kiszolgálók irányuló minden kérés naplózására: terjedelmes naplók esetén naplóváltás módja a naplók összesített mérete korlátozza miközben továbbra is lehetővé teszi a legutóbbi események elemzését. </p><p>Elkülönítések jelentkezzen alapvetően azt jelenti, hogy a napló tárolásához rendelkezik nevű fájlt egy másik partíció, ha az operációs rendszer vagy alkalmazás fut egy szolgáltatásmegtagadási támadást vagy az alkalmazás alacsonyabb verziójúra változtatása megelőzése céljából teljesítménye</p>|

## <a id="log-sensitive-data"></a>Győződjön meg arról, hogy az alkalmazás nem naplózza a bizalmas felhasználói adatok

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Ellenőrizze, hogy egy felhasználó beküld bizalmas adatok nem jelentkezik a helyhez. Ellenőrizze, hogy szándékos naplózás, valamint a tervezési szempontokat által okozott mellékhatással. Bizalmas adatok közé:</p><ul><li>Felhasználó hitelesítő adatai</li><li>Társadalombiztosítási szám vagy egyéb azonosításra</li><li>Hitelkártyaszámokat és egyéb pénzügyi adatait</li><li>Egészségügyi információk</li><li>Titkos kulcsok vagy egyéb adatok, amelyek felhasználhatók a titkosított adatok visszafejtésére.</li><li>Rendszer vagy alkalmazás-információkat, amelyek segítségével hatékonyabban támadásokkal szemben az alkalmazás</li></ul>|

## <a id="log-restricted-access"></a>Győződjön meg arról, hogy naplózási és a naplófájlok korlátozott hozzáféréssel rendelkezik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Ellenőrizze, hogy a hozzáférési jogosultsága ahhoz, hogy a naplófájlok megfelelően vannak beállítva. Alkalmazásfiókok csak írási hozzáféréssel és a kezelők kell, és a támogatási csoporthoz csak olvasási hozzáféréssel kell rendelkeznie, igény szerint.</p><p>A rendszergazdák fiókok lesznek az egyetlen fiók, amely teljes körű hozzáféréssel kell rendelkeznie. Ellenőrizze, hogy biztosítsa azok megfelelően a korlátozott Windows naplófájlok ACL:</p><ul><li>Alkalmazás-fiókok csak írási hozzáféréssel kell rendelkeznie.</li><li>Operátorok és a támogatási személyzet rendelkeznie kell olvasási hozzáféréssel igény szerint</li><li>A rendszergazdák teljes hozzáféréssel kell rendelkeznie a csak fiókokat</li></ul>|

## <a id="user-management"></a>Győződjön meg arról, hogy felhasználói eseményt naplóz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Győződjön meg arról, hogy az alkalmazás figyeli a felhasználói felügyeleti események például a sikeres és sikertelen felhasználói bejelentkezéseket, a jelszó alaphelyzetbe állítása, jelszó módosítására, a fiók zárolása, a felhasználói regisztráció. Ennek során ez segít észlelése és reagálás a potenciálisan gyanús viselkedését. Emellett lehetővé teszi a műveletek adatgyűjtés; Ha például nyomon ki fér hozzá az alkalmazás</p>|

## <a id="inbuilt-defenses"></a>Győződjön meg arról, hogy rendelkezik-e a rendszer a beépített, való visszaélés elleni védelem

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések**                   | <p>Vezérlők helyen kell lennie, amely throw alkalmazás helytelen használat esetén a biztonsági kivételhibát. Például ha a bemenet-ellenőrzés van beállítva, és a egy támadó megpróbál kártevő kódja, amely nem egyezik meg a következő reguláris kifejezésre beszúrása, biztonsági kivételhiba is hibajelzést amely lehet a rendszer visszaélés lehetőségét előzetes</p><p>Például azt javasoljuk, hogy naplózza a biztonsági kivételek és a következő problémák végzett műveleteket:</p><ul><li>Bemenet-ellenőrzés</li><li>CSRF megsértések</li><li>Találgatásos támadás (felső korlátjának erőforrás felhasználónként kérések száma)</li><li>Fájl feltöltése megsértések</li><ul>|

## <a id="diagnostics-logging"></a>Az Azure App Service web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az Azure a beépített diagnosztika révén segít az Azure App Service-ben működtetett webalkalmazások hibakeresésében. Ez az API apps és mobile apps szolgáltatásban is érvényes. Az App Service web apps adja meg a diagnosztikai adatok naplózása a webalkalmazás-kiszolgáló és a webes alkalmazás funkciói.</p><p>Ezek vannak logikailag elkülönített web server diagnostics és az application diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Győződjön meg arról, hogy bejelentkezési-naplózás engedélyezve van az SQL Server

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Bejelentkezési naplózásának konfigurálása](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Lépések** | <p>Adatbázis bejelentkezési kiszolgálónaplózás engedélyezni kell észlelése/megerősítése jelszótalálgatásos támadásokkal szemben. Fontos rögzítése sikertelen bejelentkezési kísérlet. További előnye a rögzítés mindkét sikeres és sikertelen bejelentkezési kísérletek biztosít Törvényszéki vizsgálat során</p>|

## <a id="threat-detection"></a>Az Azure SQL fenyegetésészlelés engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Ismerkedés az SQL Database Threat Detection](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Lépések** |<p>Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz. Egy új réteget jelent, amely lehetővé teszi az ügyfelek bekövetkezésük pillanatában biztonsági riasztások révén a rendellenes tevékenységekre adott reagáljon a lehetséges veszélyforrásokra, és biztosít.</p><p>Felhasználók is elemezhetik a gyanús eseményeket, az Azure SQL Database naplózási funkciójának használatával határozza meg, ha azok eredmény eléréséhez, illetéktelen behatolás vagy biztonsági rés kiaknázása elleni az adatbázisban található adatok tett kísérlet.</p><p>A Fenyegetésészlelés is egyszerűvé teszi cím potenciális fenyegetések, anélkül, hogy szakértői biztonsági szakértelem vagy fejlett biztonsági kellene az adatbázishoz</p>|

## <a id="analytics"></a>Az Azure Storage-hozzáférés naplózása az Azure Storage Analytics használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | – |
| **Hivatkozások**              | [A Storage Analytics segítségével engedélyezési figyelőtípus](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Lépések** | <p>Egyes storage-fiókok egyik engedélyezheti a naplózást végez, és metrikák adatok tárolásához Azure Storage Analytics. A storage analytics naplók fontos információkat, például a tároló elérésekor, a személy által használandó hitelesítési módszernek adja meg.</p><p>Ez lehet igazán hasznos, ha szorosan esetlegesen korán hozzáférést a tárolóhoz. Például Blob Storage-ban is beállította a tárolók összes személyes és megvalósítását egy SAS-szolgáltatás használata során az alkalmazások. Ellenőrizze a naplókat rendszeresen e blobok érhetők el a tárfiók kulcsaihoz, amelyek a biztonság megsértése arra utalhat, használatával, vagy ha a blobok nyilvánosak legyenek, de azokat nem lehet.</p>|

## <a id="sufficient-logging"></a>Elegendő naplózási megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Lépések** | <p>Biztonsági incidensek után egy megfelelő auditnapló hiánya Törvényszéki erőfeszítéseket is akadályozhatják. Windows Communication Foundation (WCF) lehetővé teszi a sikeres és/vagy a sikertelen hitelesítési kísérletek naplózása.</p><p>Sikertelen hitelesítési kísérletek naplózása figyelmeztetheti a rendszergazdák az esetleges jelszókereséses támadásokkal szemben. Ehhez hasonlóan sikeres hitelesítési események naplózása biztosíthat egy hasznos auditnaplót, amikor egy jogosult fiók biztonsága sérül. A WCF szolgáltatás biztonsági naplózási szolgáltatás engedélyezése |

### <a name="example"></a>Példa
Az alábbiakban látható egy példa konfiguráció látható a naplózás engedélyezve van
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

## <a id="audit-failure-handling"></a>Végrehajtja a megfelelő naplózási hiba kezelése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Lépések** | <p>Fejlett megoldás nem az, hogy létrehozzon egy kivételt, ha auditálási naplóba való írása sikertelen van konfigurálva. Ha WCF van konfigurálva, nem kivételt, ha nem tudja írni az auditálási naplóba kerülnek, a program nem kapnak értesítést a hiba, és kritikus fontosságú biztonsági események naplózásának esetleg nem kerül sor.</p>|

### <a name="example"></a>Példa
A `<behavior/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF az alkalmazás nem értesíti, amikor WCF auditálási naplóba írás sikertelen.
```
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
```
Konfigurálja a WCF értesíteni a program minden alkalommal, amikor nem tudja írni az auditálási naplóba kerülnek. A program riasztás nem tartják be a szervezet, amely nyomvonala naplózási hely, ahol egy alternatív értesítés sémával kell rendelkezniük. 

## <a id="logging-web-api"></a>Győződjön meg arról, hogy a vizsgálati és naplózási kényszerítve a webes API-hoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Engedélyezze a vizsgálati és naplózási a webes API-k. Auditnaplók rögzíteni kell a felhasználói környezetet. Azonosítsa az összes fontos eseményekről, és az események. A központi naplózás megvalósítása |

## <a id="logging-field-gateway"></a>Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul a helyszíni átjárók

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT helyszíni átjáró | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Ha több eszközt a helyszíni átjárók csatlakoznak, győződjön meg arról, hogy kapcsolódási kísérletek és hitelesítési állapot (sikeres vagy sikertelen) az egyes eszközök naplózza és tartják fenn a helyszíni átjáró.</p><p>Emellett olyan esetekben, ahol a helyszíni átjárók karbantartása egyes eszközöket az IoT Hub hitelesítő adatait, győződjön meg arról, hogy naplózása történik meg, ha ezeket a hitelesítő adatokat olvassa be. Fejlesztés a folyamat rendszeres időközönként feltölteni a naplókat az Azure IoT Hub/storage hosszú távú megőrzés céljából.</p> |

## <a id="logging-cloud-gateway"></a>Győződjön meg arról, hogy megfelelő vizsgálati és naplózási megvalósul Felhőátjáró

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Bevezetés az IoT Hub-műveletek monitorozása](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Lépések** | <p>IoT Hub műveletek figyelése során gyűjtött naplózási adatok gyűjtése és tárolása a tervező. Engedélyezze a következő felügyeleti kategóriák:</p><ul><li>Eszközművelet identitás</li><li>Eszközről a felhőbe való kommunikáció</li><li>Felhőből az eszközre irányuló kommunikáció</li><li>Kapcsolatok</li><li>Fájlfeltöltések</li></ul>|