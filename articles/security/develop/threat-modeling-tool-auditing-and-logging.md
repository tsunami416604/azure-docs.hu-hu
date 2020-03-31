---
title: Naplózás és naplózás - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
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
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728414"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Biztonsági keret: Naplózás és naplózás | Enyhítése 

| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Azonosítsa a bizalmas entitásokat a megoldásban, és valósítsa meg a változásnaplózást](#sensitive-entities)</li></ul> |
| **Webalkalmazás** | <ul><li>[Naplózás és naplózás kényszerítése az alkalmazásban](#auditing)</li><li>[Győződjön meg arról, hogy a napló elforgatása és elválasztása a helyén van](#log-rotation)</li><li>[Annak ellenőrzése, hogy az alkalmazás nem naplózza a bizalmas felhasználói adatokat](#log-sensitive-data)</li><li>[Annak ellenőrzése, hogy a naplózási és naplófájlok korlátozott hozzáféréssel rendelkeznek-e](#log-restricted-access)</li><li>[A felhasználókezelési események naplózásának ellenőrzése](#user-management)</li><li>[Győződjön meg arról, hogy a rendszer beépített védelemmel rendelkezik a visszaélésekkel szemben](#inbuilt-defenses)</li><li>[Webalkalmazások diagnosztikai naplózásának engedélyezése az Azure App Service-ben](#diagnostics-logging)</li></ul> |
| **Adatbázis** | <ul><li>[Annak ellenőrzése, hogy engedélyezve van-e a bejelentkezésnaplózás az SQL Server kiszolgálón](#identify-sensitive-entities)</li><li>[Fenyegetésészlelés engedélyezése az Azure SQL-ben](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Storage-statisztika használata az Azure Storage hozzáférésének naplózásához](#analytics)</li></ul> |
| **WCF** | <ul><li>[Megfelelő naplózás megvalósítása](#sufficient-logging)</li><li>[Megfelelő naplózási hibakezelés megvalósítása](#audit-failure-handling)</li></ul> |
| **Webes API** | <ul><li>[A naplózás és naplózás kényszerítése a webes API-n](#logging-web-api)</li></ul> |
| **IoT field átjáró** | <ul><li>[A megfelelő naplózás és naplózás kényszerítése a Field Gateway-en](#logging-field-gateway)</li></ul> |
| **IoT felhőátjáró** | <ul><li>[Győződjön meg arról, hogy a megfelelő naplózás és naplózás kényszerítve van a Cloud Gateway-en](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Azonosítsa a bizalmas entitásokat a megoldásban, és valósítsa meg a változásnaplózást

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | A megoldásban lévő, bizalmas adatokat tartalmazó entitások azonosítása és az adott entitásokon és mezőkön végzett változásnaplózás megvalósítása |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Naplózás és naplózás kényszerítése az alkalmazásban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | Engedélyezze a naplózást és a naplózást az összes összetevőn. A naplónaplóknak rögzíteniük kell a felhasználói környezetet. Azonosítsa az összes fontos eseményt, és naplózza ezeket az eseményeket. Központosított naplózás megvalósítása |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Győződjön meg arról, hogy a napló elforgatása és elválasztása a helyén van

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | <p>A naplórotáció egy automatikus folyamat, amelyet a rendszerfelügyelet során használnak, és amelyben a dátummal ellátott naplófájlok archiválásra kerülnek. A nagy alkalmazásokat futtató kiszolgálók gyakran naplóznak minden kérést: a terjedelmes naplókkal szemben a naplórotáció a naplók teljes méretének korlátozása, miközben továbbra is lehetővé teszi a legutóbbi események elemzését. </p><p>A naplóelválasztás alapvetően azt jelenti, hogy a naplófájlokat egy másik partíción kell tárolnia, ahol az operációs rendszer/alkalmazás fut, hogy elhárítsa a szolgáltatásmegtagadási támadást vagy az alkalmazás teljesítményének visszaminősítését</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Annak ellenőrzése, hogy az alkalmazás nem naplózza a bizalmas felhasználói adatokat

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | <p>Ellenőrizze, hogy nem naplózza-e a felhasználó által a webhelyre küldött bizalmas adatokat. Ellenőrizze a szándékos naplózást, valamint a tervezési problémák által okozott mellékhatásokat. A bizalmas adatok közé tartoznak például a következők:</p><ul><li>Felhasználói hitelesítő adatok</li><li>Társadalombiztosítási szám vagy egyéb azonosító információ</li><li>Hitelkártyaszámok vagy egyéb pénzügyi információk</li><li>Egészségügyi információk</li><li>Személyes kulcsok vagy más adatok, amelyek a titkosított adatok visszafejtésére használhatók</li><li>Az alkalmazás hatékonyabb támadására használható rendszer- vagy alkalmazásadatok</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Annak ellenőrzése, hogy a naplózási és naplófájlok korlátozott hozzáféréssel rendelkeznek-e

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | <p>Ellenőrizze, hogy a naplófájlokhoz való hozzáférési jogok megfelelően vannak-e beállítva. Az alkalmazásfiókoknak csak írási hozzáféréssel kell rendelkezniük, az operátorok nak és a támogatási személyzetnek pedig szükség szerint írásvédett hozzáféréssel kell rendelkezniük.</p><p>Rendszergazdák számlák az egyetlen számlák, amelyek teljes hozzáféréssel rendelkeznek. Ellenőrizze a Windows ACL-t a naplófájlokon, hogy megfelelően korlátozva vannak-e:</p><ul><li>Az alkalmazásfiókoknak csak írási hozzáféréssel kell rendelkezniük</li><li>Az üzemeltetőknek és a támogató személyzetnek szükség szerint csak olvasható hozzáféréssel kell rendelkeznie</li><li>A rendszergazdák az egyetlen fiókok, amelyeknek teljes hozzáféréssel kell rendelkezniük</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>A felhasználókezelési események naplózásának ellenőrzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | <p>Győződjön meg arról, hogy az alkalmazás figyeli a felhasználókezelési eseményeket, például a sikeres és sikertelen felhasználói bejelentkezéseket, a jelszó-visszaállítást, a jelszómódosításokat, a fiókzárolást, a felhasználói regisztrációt. Ezzel segít felismerni és reagálni a potenciálisan gyanús viselkedést. Azt is lehetővé teszi, hogy összegyűjtse a műveletek adatait; például nyomon követni, hogy ki fér hozzá az alkalmazáshoz</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Győződjön meg arról, hogy a rendszer beépített védelemmel rendelkezik a visszaélésekkel szemben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket**                   | <p>Olyan ellenőrzéseket kell bevezetni, amelyek biztonsági kivételt képeznek az alkalmazással való visszaélés esetén. Pl. Ha a bemeneti ellenőrzés érvényben van, és a támadó olyan rosszindulatú kódot próbál beadni, amely nem felel meg a regex-nek, biztonsági kivétel térhet el, amely a rendszerrel való visszaélésre utalhat</p><p>Ajánlott például biztonsági kivételeket naplózni, és a következő problémák esetén műveleteket kell végrehajtani:</p><ul><li>Bemenet-ellenőrzés</li><li>CSRF megsértése</li><li>Oktalan kényszerítés (felhasználónként és erőforrásonként a kérelmek felső határa)</li><li>Fájlfeltöltés megsértése</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Webalkalmazások diagnosztikai naplózásának engedélyezése az Azure App Service-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - Azure |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Az Azure beépített diagnosztikát biztosít az App Service-webalkalmazások hibakereséséhez. Az API-alkalmazásokra és a mobilalkalmazásokra is vonatkozik. Az App Service-webalkalmazások diagnosztikai funkciókat biztosítanak a webkiszolgálóról és a webalkalmazásból származó információk naplózásához.</p><p>Ezek logikailag webkiszolgáló diagnosztikára és alkalmazásdiagnosztikára vannak leválasztva</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Annak ellenőrzése, hogy engedélyezve van-e a bejelentkezésnaplózás az SQL Server kiszolgálón

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Bejelentkezésnaplózás konfigurálása](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Lépéseket** | <p>Az adatbázis-kiszolgáló bejelentkezési naplózását engedélyezni kell a jelszókitalálási támadások észleléséhez/megerősítéséhez. Fontos, hogy rögzítse a sikertelen bejelentkezési kísérleteket. A sikeres és sikertelen bejelentkezési kísérletek rögzítése további előnyökkel jár a törvényszéki vizsgálatok során</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Fenyegetésészlelés engedélyezése az Azure SQL-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure |
| **Attribútumok**              | SQL verzió - V12 |
| **Referencia**              | [Az SQL Database fenyegetésészlelésének első lépései](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Lépéseket** |<p>A fenyegetésészlelés észleli az adatbázispotenciális biztonsági fenyegetéseit jelző rendellenes adatbázis-tevékenységeket. Ez egy új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy észleljék és reagáljanak a potenciális fenyegetésekre, amikor azok előfordulnak a rendellenes tevékenységekre vonatkozó biztonsági riasztások biztosításával.</p><p>A felhasználók az Azure SQL Database Auditing segítségével fedezhetik fel a gyanús eseményeket, és megállapíthatják, hogy azok az adatbázis adatainak elérésére, megsértésére vagy kihasználására tett kísérlet eredményeként jönnek-e.</p><p>A fenyegetésészlelés egyszerűvé teszi az adatbázist fenyegető potenciális fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, vagy fejlett biztonsági figyelőrendszereket kellene kezelnie</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Az Azure Storage-statisztika használata az Azure Storage hozzáférésének naplózásához

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A |
| **Referencia**              | [Az engedélyezési típus figyelése a Storage Analytics szolgáltatással](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Lépéseket** | <p>Az azure Storage Analytics minden egyes tárfiókhoz engedélyezheti a naplózási és a metrikák adatainak tárolását. A tárolási elemzési naplók fontos információkat, például hitelesítési módszert használ valaki, amikor a tárolóhoz való hozzáférés.</p><p>Ez nagyon hasznos lehet, ha szorosan őrzi a tárolóhoz való hozzáférést. Például a Blob Storage beállíthatja az összes tárolók magánjellegű, és egy SAS-szolgáltatás használatát az alkalmazások ban. Ezután rendszeresen ellenőrizheti a naplókat, hogy a blobok érhetők-e el a tárfiók kulcsai használatával, ami a biztonság megsértését jelezheti, vagy ha a blobok nyilvánosak, de nem kellene.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Megfelelő naplózás megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Lépéseket** | <p>A megfelelő ellenőrzési nyomvonal hiánya egy biztonsági incidens után akadályozhatja a törvényszéki erőfeszítéseket. A Windows Kommunikációs Alaprendszer (WCF) lehetővé teszi a sikeres és/vagy sikertelen hitelesítési kísérletek naplózását.</p><p>A sikertelen hitelesítési kísérletek naplózása figyelmeztetheti a rendszergazdákat a lehetséges találgatásos támadásokra. Hasonlóképpen a sikeres hitelesítési események naplózása is hasznos naplózási nyomvonalat biztosíthat, ha egy legitim fiók biztonsága sérül. A WCF szolgáltatásbiztonsági naplózási szolgáltatásának engedélyezése |

### <a name="example"></a>Példa
Az alábbi példakonfiguráció engedélyezve van a naplózásban
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

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Megfelelő naplózási hibakezelés megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Lépéseket** | <p>A kifejlesztett megoldás úgy van beállítva, hogy ne hozzon létre kivételt, ha nem ír naplóba. Ha a WCF úgy van beállítva, hogy ne okozzen kivételt, ha nem tud írni a naplóba, a program nem kap értesítést a hibaról, és előfordulhat, hogy a kritikus biztonsági események naplózása nem történik meg.</p>|

### <a name="example"></a>Példa
Az `<behavior/>` alábbi WCF konfigurációs fájl eleme arra utasítja a WCF-et, hogy ne értesítse az alkalmazást, ha a WCF nem ír naplóba.
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
Állítsa be a WCF-et úgy, hogy értesítse a programot, ha nem tud naplónaplóba írni. A programnak rendelkeznie kell egy alternatív értesítési séma, amely figyelmezteti a szervezetet, hogy a naplók karbantartása nem történik meg. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>A naplózás és naplózás kényszerítése a webes API-n

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Engedélyezze a naplózást és a webes API-k naplózását. A naplónaplóknak rögzíteniük kell a felhasználói környezetet. Azonosítsa az összes fontos eseményt, és naplózza ezeket az eseményeket. Központosított naplózás megvalósítása |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>A megfelelő naplózás és naplózás kényszerítése a Field Gateway-en

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT field átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Ha több eszköz csatlakozik egy field gateway-hez, győződjön meg arról, hogy az egyes eszközök csatlakozási kísérletei és hitelesítési állapota (sikeres vagy sikertelen) a Field Gateway-en van.</p><p>Azokban az esetekben, ahol a Field Gateway az egyes eszközök IoT Hub hitelesítő adatait tartja karban, győződjön meg arról, hogy a naplózás végrehajtása a hitelesítő adatok beolvasásakor történik. Dolgozzon ki egy folyamatot, amely rendszeresidőközönként feltölti a naplókat az Azure IoT Hub/storage hosszú távú megőrzése érdekében.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Győződjön meg arról, hogy a megfelelő naplózás és naplózás kényszerítve van a Cloud Gateway-en

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Bevezetés az IoT Hub-műveletek figyelésébe](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Lépéseket** | <p>Tervezze meg az IoT Hub-üzemeltetésfigyelésen keresztül gyűjtött naplózási adatok gyűjtését és tárolását. Engedélyezze a következő figyelési kategóriákat:</p><ul><li>Eszközidentitás-műveletek</li><li>Eszközről felhőbe irányuló kommunikáció</li><li>Felhőből az eszközre irányuló kommunikáció</li><li>Kapcsolatok</li><li>Fájlfeltöltések</li></ul>|