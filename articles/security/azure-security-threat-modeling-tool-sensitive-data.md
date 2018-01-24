---
title: "-Microsoft fenyegetés modellezési eszköz – bizalmas adatok Azure |} Microsoft Docs"
description: "a fenyegetések modellezése eszköz felfedett fenyegetések megoldást"
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
ms.openlocfilehash: 8d7189ea4b01d43cea709e3300d8ed71d266f5c9
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="security-frame-sensitive-data--mitigations"></a>Biztonsági keret: Bizalmas adatok |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy bináris formában vannak, ha bizalmas információkat tartalmaznak](#binaries-info)</li><li>[Vegye figyelembe a titkosított fájlrendszer (EFS) használatával bizalmas felhasználói adatok védelme](#efs-user)</li><li>[Győződjön meg arról, hogy az alkalmazás a fájlrendszeren tárolt bizalmas adatok titkosítása](#filesystem)</li></ul> | 
| **Webalkalmazás** | <ul><li>[Győződjön meg arról, hogy bizalmas tartalom nem tárolja a böngészőben](#cache-browser)</li><li>[Webes alkalmazás konfigurációs fájlja bizalmas adatokat tartalmazó szakasz titkosítása](#encrypt-data)</li><li>[Explicit módon letiltja az automatikus kiegészítés HTML attribútum bizalmas űrlapokban és a bemeneti adatok](#autocomplete-input)</li><li>[Győződjön meg arról, hogy a felhasználó képernyőn való megjelenítésének bizalmas adatok van maszkolva](#data-mask)</li></ul> | 
| **Adatbázis** | <ul><li>[Dinamikus adatmaszkolási bizalmas adatok nem rendszerjogosultságú kitettség felhasználók megvalósítása](#dynamic-users)</li><li>[Győződjön meg arról, hogy a jelszavak sózott kivonatoló formátumban kell tárolni](#salted-hash)</li><li>[Győződjön meg arról, hogy adatbázismezőknek bizalmas adatok titkosítva van](#db-encrypted)</li><li>[Győződjön meg arról, hogy adatbázis szintű titkosítást (TDE) engedélyezve van](#tde-enabled)</li><li>[Győződjön meg arról, hogy titkosítva legyenek-e az adatbázis biztonsági mentése](#backup)</li></ul> | 
| **Webes API** | <ul><li>[Győződjön meg arról, hogy az webes API-ra vonatkozó bizalmas adatok nem tárolódik a böngésző tárolási](#api-browser)</li></ul> | 
| Az Azure Document DB rendszerbe | <ul><li>[Az Azure Cosmos Adatbázisba tárolt bizalmas adatok titkosítása](#encrypt-docdb)</li></ul> | 
| **Azure IaaS virtuális gép megbízhatósági kapcsolat határán** | <ul><li>[Virtuális gépek által használt lemezek titkosítására használt Azure Disk Encryption](#disk-vm)</li></ul> | 
| **Service Fabric megbízhatósági kapcsolat határán** | <ul><li>[Service Fabric-alkalmazások titkos kulcsainak titkosítása](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Biztonsági modellezési üzleti egység/csoportok használható, és ha szükséges](#modeling-teams)</li><li>[Kritikus entitások funkciót megosztott minimalizálása érdekében](#entities)</li><li>[A kockázatok a Dynamics CRM megosztás szolgáltatást és jó biztonsági gyakorlat a felhasználók képzése](#good-practices)</li><li>[Közé tartozik a megjelenítő konfigurációs részletek a kivételek kezelése proscribing fejlesztési szabványok szabály](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Használja az Azure Storage Service Encryption (SSE) for Data at Rest (Preview)](#sse-preview)</li><li>[Bizalmas adatok tárolásához Azure Storage ügyféloldali titkosítás használata](#client-storage)</li></ul> | 
| **Mobileszköz ügyfél** | <ul><li>[Érzékeny vagy telefonok helyi tárhelyen, személyazonosításra alkalmas adatok titkosítása](#pii-phones)</li><li>[A végfelhasználók számára kiosztása előtt létrehozott bináris takarják](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Tanúsítvány vagy a Windows clientCredentialType beállítása](#cert)</li><li>[WCF-biztonsági mód nem engedélyezett.](#security)</li></ul> | 

## <a id="binaries-info"></a>Győződjön meg arról, hogy bináris formában vannak, ha bizalmas információkat tartalmaznak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy bináris rejtjelezett vannak, ha azok tartalmaznak olyan bizalmas adatokat, például kereskedelmi titkok, bizalmas üzleti logika, amely kell nem fordított irányú. Ez a szerelvények visszafejtés leállítása. Eszközök, például `CryptoObfuscator` erre a célra használható. |

## <a id="efs-user"></a>Vegye figyelembe a titkosított fájlrendszer (EFS) használatával bizalmas felhasználói adatok védelme

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Vegye figyelembe, hogy a titkosított fájlrendszer (EFS) használatával bizalmas felhasználói adatok védelme a fizikai hozzáféréssel rendelkező szemléltetik a számítógépre. |

## <a id="filesystem"></a>Győződjön meg arról, hogy az alkalmazás a fájlrendszeren tárolt bizalmas adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy az alkalmazás a fájlrendszeren tárolt bizalmas adatok titkosítva van (például a DPAPI-t használ), ha az EFS nem kényszeríthető. |

## <a id="cache-browser"></a>Győződjön meg arról, hogy bizalmas tartalom nem tárolja a böngészőben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, Web Forms, MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Böngészők célokra és adatok gyorsítótárazása előzmények tárolhatja. A gyorsítótárazott fájlok tárolják, mint az ideiglenes internetfájlok mappa Internet Explorer egy mappában. Ha az adott lapok újra hivatkozunk, a böngésző megjeleníti azokat a gyorsítótárból. Ha bizalmas információk (például a címet, a hitelkártya adatait, az társadalombiztosítási szám vagy a felhasználónév) a felhasználó számára, majd ezeket az információkat lehet webböngésző gyorsítótárában tárolt, és ezért lekérhető keresztül vizsgálata folyamatban van a gyorsítótárban, vagy egyszerűen nyomja le a böngésző "Újra" gombra. Állítsa be a cache-control válasz állomásfejléc-érték "no-tárolóba" minden lap. |

### <a name="example"></a>Példa
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Példa
Ez a szűrő keresztül hajtható végre. Használható a következő példa: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>Webes alkalmazás konfigurációs fájlja bizalmas adatokat tartalmazó szakasz titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Útmutató: Az ASP.NET 2.0 használatával DPAPI konfigurációs szakasz titkosítása](https://msdn.microsoft.com/library/ff647398.aspx), [adja meg egy védett Konfigurációszolgáltatót](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Azure Key Vault használatával alkalmazás titkos kulcsok védelme](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Lépések** | Konfigurációs fájlok, például a Web.config fájlként appsettings.json gyakran használt bizalmas adatokat, beleértve a felhasználónevek, jelszavak, adatbázis-kapcsolati karakterláncok és titkosítási kulcsok tárolásához. Ha ezt az információt nem védi, az alkalmazás ki van téve a támadók vagy rosszindulatú felhasználók megszerezni a bizalmas adatokat, például a fiók felhasználói nevét és a jelszavak, a adatbázis nevét és a kiszolgálók nevei. Alapján a központi telepítési típus (azure vagy a helyszínen), titkosítsa a bizalmas DPAPI-t vagy szolgáltatásokat, mint az Azure Key Vault segítségével konfigurációs fájljainak szakaszait. |

## <a id="autocomplete-input"></a>Explicit módon letiltja az automatikus kiegészítés HTML attribútum bizalmas űrlapokban és a bemeneti adatok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN: automatikus kiegészítés attribútum](http://msdn.microsoft.com/library/ms533486(VS.85).aspx), [automatikus kiegészítési funkciójának használatával HTML](http://msdn.microsoft.com/library/ms533032.aspx), [HTML-tisztítási biztonsági](http://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete., újra?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Lépések** | Az automatikus kiegészítés attribútum Megadja, hogy kell-e az űrlap a be- és kikapcsolása autocomplete kapcsolatos rendelkeznek. Ha automatikus kiegészítési funkciója, a böngésző automatikusan befejezése előtt a felhasználó megadott értékek alapján értékeket. Például amikor egy új nevet és jelszót is meg kell adni egy űrlapon, és az űrlap elküldése, a böngésző megkérdezi, hogy ha a jelszó mentésére. Ezt követően amikor a képernyő jelenik meg, a név és jelszó automatikusan kitölti, vagy van befejezni, mert a név is meg kell adni. Helyi hozzáféréssel rendelkező támadó tudta megszerezni a tiszta szöveges jelszavak, a böngésző gyorsítótárából. Alapértelmezés szerint engedélyezve van az automatikus kiegészítés, és explicit módon le kell tiltani. |

### <a name="example"></a>Példa
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Győződjön meg arról, hogy a felhasználó képernyőn való megjelenítésének bizalmas adatok van maszkolva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Amikor a képernyőn megjelenő kell legyen maszkolva a bizalmas adatokat, például jelszavakat, hitelkártyaszámokat tartalmazó SSN stb. Ez az, hogy megakadályozza a jogosulatlan személyek fér hozzá az adatokhoz (például a képernyőre pillant-surfing jelszavak, technikai támogatási csoporthoz SSN felhasználóval megtekintése). Győződjön meg arról, hogy ezek az adatok elemek nem láthatók el a formázatlan szöveges megfelelően maszkolva. Azt kell őket (pl. bemenetként elfogadása közben végrehajtott fontos. Adjon meg típust = a "password") valamint vissza a program a következő képernyő (pl. megjelenítési csak az utolsó 4 számjegyét a hitelkártya száma). |

## <a id="dynamic-users"></a>Dinamikus adatmaszkolási bizalmas adatok nem rendszerjogosultságú kitettség felhasználók megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL - 12-es verzió, az SQL-verzió - verzió MsSQL2016 |
| **Hivatkozások**              | [Dinamikus Adatmaszkolási](https://msdn.microsoft.com/library/mt130841) |
| **Lépések** | Dinamikus adatmaszkolási célja a felhasználók, akik nem rendelkeznek hozzáféréssel a adatok megtekintését, akadályozza meg, hogy bizalmas adatok felfedésnek korlátozni. Dinamikus adatmaszkolási nem célja, hogy akadályozza meg adatbázis közvetlenül az adatbázishoz csatlakozással, és elérhetővé tenni a bizalmas adatok teljes körű lekérdezések futtatása. Dinamikus adatmaszkolási kiegészítő más SQL Server biztonsági funkciók (naplózás, a titkosítás és sorszintű biztonság...), és a szolgáltatás használatához azokat együtt továbbá a bizalmas adatok hatékonyabb védelme érdekében ajánlott a az adatbázis. Vegye figyelembe, hogy ez a funkció csak az SQL Server 2016 kezdve és az Azure SQL Database által támogatott. |

## <a id="salted-hash"></a>Győződjön meg arról, hogy a jelszavak sózott kivonatoló formátumban kell tárolni

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Jelszó Hashing .NET titkosítási API-k használatával](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Lépések** | Jelszavak egyéni felhasználói tároló adatbázisok nem kell tárolni. Jelszó-kivonatok helyette védőérték értékekkel kell tárolni. Ellenőrizze, hogy a felhasználó a védőérték mindig egyedi és b-crypt program segítségével, a titkosítási-s vagy PBKDF2 alkalmazása előtt tárolja a jelszót, a találgatásos kényszerített lehetősége 150 000 hurkok minimális munkahelyi tényező iterációs számaival együtt.| 

## <a id="db-encrypted"></a>Győződjön meg arról, hogy adatbázismezőknek bizalmas adatok titkosítva van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | SQL-verzió - minden |
| **Hivatkozások**              | [Az SQL server bizalmas adatok titkosítása](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [hogyan: egy SQL Server adatok oszlop titkosítása](https://msdn.microsoft.com/library/ms179331), [tanúsítvány titkosítása](https://msdn.microsoft.com/library/ms188061) |
| **Lépések** | Érzékeny adatok, például a hitelkártyaszámukat rendelkezik az adatbázisban titkosításra. Adatok titkosíthatók oszlopszintű titkosítással vagy egy alkalmazás függvény a titkosítási funkciókat használja. |

## <a id="tde-enabled"></a>Győződjön meg arról, hogy adatbázis szintű titkosítást (TDE) engedélyezve van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [SQL Server átlátható adattitkosítás (TDE) ismertetése](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Lépések** | Segíti a bizalmas adatbázis adatainak titkosításához és a tanúsítvánnyal az adatok titkosításához használt kulcs védelme SQL server átlátszó Data Encryption (TDE) szolgáltatása. Ez megakadályozza, hogy ezek a kulcsok adatok használatával. TDE adatok védelmét "Inaktív", ami azt jelenti, az adat- és naplófájljai. Ez lehetővé teszi a sok törvényi, a szabályozókat és a különböző iparágak létrehozott iránymutatásokat ahhoz, hogy. |

## <a id="backup"></a>Győződjön meg arról, hogy titkosítva legyenek-e az adatbázis biztonsági mentése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL - 12-es verzió, az SQL-verzió - verzió MsSQL2014 |
| **Hivatkozások**              | [SQL-adatbázis a biztonsági mentés titkosítása](https://msdn.microsoft.com/library/dn449489) |
| **Lépések** | SQL Server biztonsági másolat létrehozása közben az adatok titkosítása rendelkezzen. A titkosítási algoritmus és a titkosító (tanúsítvánnyal vagy aszimmetrikus kulcs) megadásával a biztonsági másolat létrehozásakor egy hozhat létre egy titkosított biztonságimásolat-fájl. |

## <a id="api-browser"></a>Győződjön meg arról, hogy az webes API-ra vonatkozó bizalmas adatok nem tárolódik a böngésző tárolási

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | Identity Provider - ADFS, identitásszolgáltató - az Azure AD |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Bizonyos implementációkban érzékeny összetevőihez webes API-hitelesítéshez szükséges böngésző helyi tároló vannak tárolva. Például az Azure AD hitelesítési összetevők például a adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key stb.</p><p>Ezen összetevők után érhetők el az összes jelentkezzen ki, vagy a böngésző le van zárva. Ha egy ellenfél hozzáférnek ezen összetevők, többé felhasználhatja őket a védett erőforrások (API-k) eléréséhez. Győződjön meg arról, hogy az összes érzékeny összetevőihez kapcsolódnak Web API nem tárolódik a böngésző tároló. Azokban az esetekben, ahol az ügyféloldali tárolási elkerülhetetlen (például egyetlen oldal alkalmazások (SPA) használó Implicit OpenIdConnect/OAuth adatfolyamok kell tárolni helyi hozzáférési jogkivonatok), használja tárolási lehetőségek a nincs megőrzését. például inkább SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Példa
A JavaScript alábbi részlet egy egyéni hitelesítési tár hitelesítési összetevők tárol a helyi tároló származik. Ilyen megvalósítások el kell kerülni. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>A Cosmos DB tárolt bizalmas adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure Document DB rendszerbe | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Alkalmazás szinten bizalmas adatok titkosítására dokumentum DB tárolása előtt, vagy a bizalmas adatok tárolása egyéb tárolási megoldások, például az Azure Storage vagy az Azure SQL| 

## <a id="disk-vm"></a>Virtuális gépek által használt lemezek titkosítására használt Azure Disk Encryption

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure IaaS virtuális gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A virtuális gépek által használt lemezek Azure Disk Encryption titkosítása](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Lépések** | <p>Az Azure Disk Encryption az új szolgáltatása, amely jelenleg előzetes verzióban érhetők. Ez a funkció lehetővé teszi, hogy az operációs rendszer és az infrastruktúra-szolgáltatási virtuális gép által használt adatok lemezek titkosítását. A Windows a meghajtók titkosítása szabványos BitLocker titkosítás technológia használatával. Linux a lemez titkosítása a DM-Crypt technológia használatával. Ez integrálva van az Azure Key Vault lehetővé teszi a lemez titkosítási kulcsok kezeléséhez, és szabályozhatja. Az Azure Disk Encryption megoldás a következő három titkosítási forgatókönyvet támogatja:</p><ul><li>Engedélyezze a titkosítást a felhasználói által titkosított fájlok és az ügyfél által megadott titkosítási kulcs esetében, amelyek tárolódnak az Azure Key Vault létrehozott új IaaS virtuális gépeken.</li><li>Engedélyezheti a titkosítást az Azure piactérről létrehozott új IaaS virtuális gépeket.</li><li>Engedélyezze a titkosítást a meglévő infrastruktúra-szolgáltatási virtuális gépeken már fut az Azure-ban.</li></ul>| 

## <a id="fabric-apps"></a>Service Fabric-alkalmazások titkos kulcsainak titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [A Service Fabric-alkalmazások titkos kulcsok kezelése](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Lépések** | Titkos kulcsok lehet bármely bizalmas adatokat, például a tárolási kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem egyszerű szöveges kezelje. Az Azure Key Vault segítségével kezelheti a kulcsok és titkos a service fabric-alkalmazások. |

## <a id="modeling-teams"></a>Biztonsági modellezési üzleti egység/csoportok használható, és ha szükséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Biztonsági modellezési üzleti egység/csoportok használható, és ha szükséges |

## <a id="entities"></a>Kritikus entitások funkciót megosztott minimalizálása érdekében

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Kritikus entitások funkciót megosztott minimalizálása érdekében |

## <a id="good-practices"></a>A kockázatok a Dynamics CRM megosztás szolgáltatást és jó biztonsági gyakorlat a felhasználók képzése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | A kockázatok a Dynamics CRM megosztás szolgáltatást és jó biztonsági gyakorlat a felhasználók képzése |

## <a id="exception-mgmt"></a>Közé tartozik a megjelenítő konfigurációs részletek a kivételek kezelése proscribing fejlesztési szabványok szabály

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Közé tartozik a megjelenítő konfigurációs részletek a kivételek kezelése fejlesztési kívül proscribing fejlesztési szabványok szabály. Ellenőrizze, hogy ez a kód értékelést vagy a rendszeres ellenőrzés részeként.|

## <a id="sse-preview"></a>Használja az Azure Storage Service Encryption (SSE) for Data at Rest (Preview)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Hivatkozások**              | [Az Azure Storage Service Encryption for Data at Rest (Preview)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Lépések** | <p>Az Azure Storage szolgáltatás titkosítási (SSE) inaktív adatok segítségével és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni. Ezzel a funkcióval a Azure Storage automatikusan titkosítja az adatokat a tárhelyre megőrzése előtt, és lekérése előtt visszafejti. A titkosítási, visszafejtési és kulcskezelés az teljesen átlátható a felhasználók számára. SSE csak a blokkblobokat, lapblobokat és hozzáfűző blobok vonatkozik. A más típusú adatok, beleértve a táblák, üzenetsorok és fájlok, nem lesznek titkosítva.</p><p>Titkosítás és visszafejtés munkafolyamat:</p><ul><li>Az ügyfél engedélyezi a titkosítás használatát a storage-fiók</li><li>Ha az ügyfél új adatokat ír (PUT Blob, PUT blokk, PUT lap, stb.) a Blob storage; minden egyes van titkosítva, 256 bites AES titkosítással, a legerősebb blokk Rejtjelek közül</li><li>Ha a felhasználói adatokat (a Blob LEKÉRÉSE, stb.) hozzáférésre van szüksége, adatai automatikusan visszafejtett a felhasználó számára való visszaküldés előtt.</li><li>Védelem le van tiltva, ha új írási műveletek többé nem lesznek titkosítva, és a meglévő titkosított adatok titkosítva maradnak, amíg a felhasználó által írni. Titkosítás engedélyezve van, amíg a Blob storage írások lesz titkosítva. Az adatok állapotát nem változtatja meg a felhasználó váltása a tárfiók titkosítás engedélyezése vagy tiltása</li><li>Minden titkosítási kulcs tárolása, titkosított és a Microsoft által felügyelt</li></ul><p>Ne feledje, hogy jelenleg, Microsoft által felügyelt használt a titkosítási kulcsokat. Microsoft eredetileg állít elő, a kulcsok, és tárolja biztonságos kulcsok, valamint rendszeres elforgatási belső Microsoft házirend által definiált konfigurációjának kialakításához. A jövőben az ügyfelek megkapják képes kezelni a saját > titkosítási kulcsokat, és adja meg a Microsoft által felügyelt kulcsok áttelepítési elérési kulcsok ügyfél által felügyelt.</p>| 

## <a id="client-storage"></a>Bizalmas adatok tárolásához Azure Storage ügyféloldali titkosítás használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [oktatóanyag: titkosításához és visszafejtéséhez az Azure Key Vault használatával a Microsoft Azure Storage blobs](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [adattárolás az Azure-Blobba Tárolás az Azure titkosítási kiterjesztésű](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Lépések** | <p>Az Azure Storage ügyféloldali kódtára a .NET Nuget-csomag támogatja a titkosított adatok ügyfélalkalmazásokon belüli feltöltése az Azure Storage, és az adatok visszafejtése során az ügyfél letöltése előtt. A kódtár emellett támogatja az Azure Key Vault rendszerrel való integrálást a tárfiókkulcs-kezelés biztosítása érdekében. Ügyféloldali titkosítása működése rövid leírása itt található:</p><ul><li>Az Azure Storage-ügyfél SDK állít elő, a tartalom titkosítási kulcs (CEK), amely a szimmetrikus kulcs egy egyszeri használata</li><li>Felhasználói adatok titkosítása a CEK</li><li>A CEK ezt követően (titkosított) (KEK-) kulcs titkosítási kulcs használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspár vagy egy szimmetrikus kulcsot kell és is kezelhetők helyileg vagy az Azure Key Vault tárolja. A tárolási ügyfélen soha nem a KEK elérésére. Egyszerűen meghívja a Key Vault által biztosított kulcs alkalmazásburkoló algoritmus. Az ügyfelek használhat egyéni szolgáltatók alkalmazásburkoló/kicsomagolásával Ha szeretné, hogy azok kulcs</li><li>A titkosított adatok majd feltöltése az Azure Storage szolgáltatásba. Ellenőrizze a hivatkozásokra a references szakaszában, az alacsony szintű megvalósítás részletei.</li></ul>|

## <a id="pii-phones"></a>Érzékeny vagy telefonok helyi tárhelyen, személyazonosításra alkalmas adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Generic, Xamarin  |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Beállításainak és funkcióinak az eszközök a Microsoft Intune-házirendek kezelése](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [kulcslánc Valet](https://components.xamarin.com/view/square.valet) |
| **Lépések** | <p>Ha az alkalmazás írja az érzékeny adatok, például a felhasználó személyhez köthető adatokat (e-mail, telefonszám, utónevét, vezetéknevét, beállítások stb.) -mobileszköz a fájlrendszerben, majd azt titkosítani kell a helyi fájlrendszer írása előtt. Ha az alkalmazás vállalati alkalmazás, így megismerkedhet a Windows Intune-nal közzétételi alkalmazás lehetőségét.</p>|

### <a name="example"></a>Példa
Intune a következő biztonsági házirendeknek bizalmas adatok védelmének konfigurálható: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Példa
Ha az alkalmazás nem vállalati alkalmazás, majd a megadott platform keystore használatához keychains a titkosítási kulcsokat, amely kriptográfiai művelet használatával hajthatja végre a fájlrendszeren. Következő kódrészletet a xamarin segítségével kulcslánc hívóbetű mutatja be: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>A végfelhasználók számára kiosztása előtt létrehozott bináris takarják

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A .NET-hez titkosítási címmódosítás](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Lépések** | Generált bináris fájlokat (szerelvények apk belül) kell rejtjelezett, visszafejtés szerelvények leállítására. Eszközök, például `CryptoObfuscator` erre a célra használható. |

## <a id="cert"></a>Tanúsítvány vagy a Windows clientCredentialType beállítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Erősítse meg](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | Egy UsernameToken használata a titkosítatlan szöveges jelszót titkosítatlan csatornán keresztül mutatja meg a jelszót, hogy a támadók számára is figyelheti a SOAP-üzenetekkel. A UsernameToken használó szolgáltatók előfordulhat, hogy fogadja el a jelszavak nem titkosított szövegként küldi. Egyszerű szöveges jelszavak küldése titkosítatlan csatornán keresztül is elérhetővé teheti a támadók számára a SOAP-üzenetet is figyelheti a hitelesítő adatot. | 

### <a name="example"></a>Példa
A következő WCF szolgáltató konfigurációja a UsernameToken használja: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Tanúsítvány vagy a Windows clientCredentialType beállítása 

## <a id="security"></a>WCF-biztonsági mód nem engedélyezett.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Biztonsági mód - átvitel, biztonsági üzemmód - üzenet |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Királyság erősítse](https://vulncat.fortify.com/en/vulncat/index.html), [WCF biztonsági alapok CoDe magazin](http://www.codemag.com/article/0611051) |
| **Lépések** | Nincs transport vagy üzenet biztonság van definiálva. Alkalmazások, amelyek üzenetek nélkül transport vagy biztonsági nem garantálható, hogy a épsége vagy bizalmassága üzenetek üzenetet továbbítani. Ha egy WCF biztonsági kötése nincs értékre van állítva, átviteli és üzenet biztonsági le vannak tiltva. |

### <a name="example"></a>Példa
A következő konfiguráció a biztonsági mód beállítása None. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Példa
Biztonsági mód között az összes szolgáltatási kötések nem öt lehetséges biztonsági mód: 
* Nincs. Biztonsági kikapcsolása. 
* Átviteli. Használja a kölcsönös hitelesítés és az üzenet védelmi biztonsági átviteli. 
* Üzenet. Kölcsönös hitelesítés és az üzenet védelmi üzenetbiztonság használ. 
* Mindkettő. Lehetővé teszi az átvitel és (csak MSMQ támogatja ezt) üzenet szintű biztonsági beállításokat. 
* TransportWithMessageCredential. Az üzenet és az üzenet védelmi átadott hitelesítő adatokat, és a szállítási réteg által biztosított kiszolgálói hitelesítési. 
* TransportCredentialOnly. Ügyfél hitelesítő adatait, a szállítási réteg át lettek adva, és nem üzenet védelem akkor lép életbe. Használja az üzenet és a transport biztonsági sértetlenségét és bizalmasságát az üzenetek védelme érdekében. Az alábbi konfigurációs jelzi a szolgáltatás használata a transport security üzenet hitelesítő adatokkal.
```
<system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
</system.serviceModel> 
```
