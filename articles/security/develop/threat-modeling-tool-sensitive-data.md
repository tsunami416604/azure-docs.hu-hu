---
title: Bizalmas adatok - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
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
ms.openlocfilehash: ab22e9843ca133274361838eeb49abbe326588dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502233"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Biztonsági keret: Érzékeny adatok | Enyhítése 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Gép megbízhatósági határa** | <ul><li>[Győződjön meg arról, hogy a bináris fájlok elvannak homályosítva, ha bizalmas információkat tartalmaznak](#binaries-info)</li><li>[Fontolja meg a titkosított fájlrendszer (EFS) használatával védi a bizalmas felhasználó-specifikus adatok](#efs-user)</li><li>[Győződjön meg arról, hogy az alkalmazás által a fájlrendszeren tárolt bizalmas adatok titkosítva vannak](#filesystem)</li></ul> | 
| **Webalkalmazás** | <ul><li>[Annak ellenőrzése, hogy a bizalmas tartalom nem lesz-e gyorsítótárazva a böngészőben](#cache-browser)</li><li>[A Web App bizalmas adatokat tartalmazó konfigurációs fájljainak titkosítása](#encrypt-data)</li><li>[Explicit módon tiltsa le az automatikus kiegészítés HTML attribútumot a bizalmas űrlapokon és bemeneteken](#autocomplete-input)</li><li>[Annak ellenőrzése, hogy a felhasználói képernyőn megjelenő bizalmas adatok maszkosak-e](#data-mask)</li></ul> | 
| **Adatbázis** | <ul><li>[Dinamikus adatmaszkolás megvalósítása a bizalmas adatexpozíció korlátozása érdekében nem kiemelt jogosultságú felhasználók számára](#dynamic-users)</li><li>[A jelszavak sós kivonatformátumban való tárolásának biztosítása](#salted-hash)</li><li>[Annak ellenőrzése, hogy az adatbázisoszlopokban lévő bizalmas adatok titkosítva vannak-e](#db-encrypted)</li><li>[Az adatbázisszintű titkosítás (TDE) engedélyezésének ellenőrzése](#tde-enabled)</li><li>[Az adatbázis biztonsági másolatainak titkosítása](#backup)</li></ul> | 
| **Webes API** | <ul><li>[Annak biztosítása, hogy a webes API-ra vonatkozó bizalmas adatok ne legyenek tárolva a böngésző tárhelyén](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Az Azure Cosmos DB-ben tárolt bizalmas adatok titkosítása](#encrypt-docdb)</li></ul> | 
| **Az Azure IaaS virtuálisgép megbízhatósági határa** | <ul><li>[Az Azure Disk Encryption használata a virtuális gépek által használt lemezek titkosításához](#disk-vm)</li></ul> | 
| **Szolgáltatásháló-kapcsolat megbízhatósági határa** | <ul><li>[Titkos kulcsok titkosítása a Service Fabric-alkalmazásokban](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Biztonsági modellezés végrehajtása és szükség esetén a Részlegek/csapatok használata](#modeling-teams)</li><li>[A kritikus entitások megosztási funkciójának minimálisra állítása](#entities)</li><li>[A felhasználók betanítása a Dynamics CRM megosztás funkciójával és a helyes biztonsági gyakorlatokkal kapcsolatos kockázatokra](#good-practices)</li><li>[Fejlesztési szabványokra vonatkozó tiltó szabály felvétele a kivételkezelés ben a konfigurációs adatok megjelenítésére](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Az Azure Storage Service Encryption (SSE) használata inaktív adatokhoz (előzetes verzió)](#sse-preview)</li><li>[Bizalmas adatok tárolása az Azure Storage-ban ügyféloldali titkosítással](#client-storage)</li></ul> | 
| **Mobil ügyfél** | <ul><li>[A telefonokhelyi tárhelyre írt bizalmas vagy személyazonosításra képes adatok titkosítása](#pii-phones)</li><li>[A létrehozott bináris fájlok elferdítése a végfelhasználóknak történő terjesztés előtt](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Az clientCredentialType beállítása tanúsítványra vagy Windows rendszerre](#cert)</li><li>[A WCF-biztonsági mód nincs engedélyezve](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Győződjön meg arról, hogy a bináris fájlok elvannak homályosítva, ha bizalmas információkat tartalmaznak

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy a bináris fájlok titkosak, ha olyan érzékeny információkat tartalmaznak, mint például üzleti titkok, bizalmas üzleti logika, amelyet nem szabad megfordítani. Ennek célja, hogy megállítsa a szerelvények visszafejtését. Ehhez `CryptoObfuscator` a célhoz hasonló eszközöket lehet használni. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Fontolja meg a titkosított fájlrendszer (EFS) használatával védi a bizalmas felhasználó-specifikus adatok

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Fontolja meg a titkosított fájlrendszer (EFS) használatát a bizalmas felhasználóspecifikus adatok védelmére a számítógéphez fizikai hozzáféréssel rendelkező ellenfelekkel szemben. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Győződjön meg arról, hogy az alkalmazás által a fájlrendszeren tárolt bizalmas adatok titkosítva vannak

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy az alkalmazás által a fájlrendszeren tárolt bizalmas adatok titkosítva vannak (pl. DPAPI használatával), ha az EFS nem érvényesíthető |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Annak ellenőrzése, hogy a bizalmas tartalom nem lesz-e gyorsítótárazva a böngészőben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, Webes űrlapok, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A böngészők gyorsítótárazási és előzménycélú adatokat tárolhatnak. Ezek a gyorsítótárazott fájlok egy mappában tárolódnak, például az Internet Explorer esetében az Ideiglenes internetfájlok mappában. Amikor ezeket az oldalakat ismét hivatkoznak, a böngésző megjeleníti őket a gyorsítótárból. Ha bizalmas információk jelennek meg a felhasználó számára (például címük, hitelkártyaadataik, társadalombiztosítási számuk vagy felhasználónevük), akkor ez eket a böngésző gyorsítótárában lehet tárolni, és így a böngésző gyorsítótárának vizsgálatával vagy a böngésző gyorsítótárának vizsgálatával vagy a egyszerűen nyomja meg a böngésző "Vissza" gombját. Állítsa a gyorsítótár-vezérlő válasz fejlécének értékét "no-store" értékre az összes oldalon. |

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
Ez szűrőn keresztül valósítható meg. A következő példa használható: 
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

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>A Web App bizalmas adatokat tartalmazó konfigurációs fájljainak titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Útmutató: Konfigurációs szakaszok titkosítása ASP.NET 2.0-ban a DPAPI használatával,](https://msdn.microsoft.com/library/ff647398.aspx) [Védett konfigurációs szolgáltató megadása,](https://msdn.microsoft.com/library/68ze1hb2.aspx) [Az Azure Key Vault használata az alkalmazástitkok védelmére](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Lépéseket** | A konfigurációs fájlokat, például a Web.config fájlt, az appsettings.json gyakran használják bizalmas adatok tárolására, például felhasználóneveket, jelszavakat, adatbázis-kapcsolati karakterláncokat és titkosítási kulcsokat. Ha nem védi ezeket az adatokat, az alkalmazás ki van téve a támadók vagy rosszindulatú felhasználók számára, akik bizalmas adatokat, például fiókfelhasználóneveket és jelszavakat, adatbázisneveket és kiszolgálóneveket szereznek be. A központi telepítés típusa (azure/on-prem) alapján titkosítsa a bizalmas fájlok bizalmas szakaszait a DPAPI vagy a szolgáltatások, például az Azure Key Vault használatával. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Explicit módon tiltsa le az automatikus kiegészítés HTML attribútumot a bizalmas űrlapokon és bemeneteken

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN: automatikus kiegészítés attribútum](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Automatikus kiegészítés használata HTML-ben](https://msdn.microsoft.com/library/ms533032.aspx), [HTML-fertőtlenítési biztonsági rés](https://technet.microsoft.com/security/bulletin/MS10-071), [Automatikus kiegészítés.,újra?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Lépéseket** | Az automatikus kiegészítés attribútum határozza meg, hogy az űrlapon be- vagy kikell-e lépni. Ha az automatikus kiegészítés be van kapcsolva, a böngésző automatikusan elvégzi az értékeket a felhasználó által korábban megadott értékek alapján. Ha például új nevet és jelszót ad meg egy űrlapon, és az űrlapot elküldi, a böngésző megkérdezi, hogy a jelszót menteni kell-e. Ezt követően, amikor az űrlap megjelenik, a név és a jelszó automatikusan kitöltődik, vagy kitöltődik, ahogy a nevet beírják. A helyi hozzáféréssel rendelkező támadó a böngésző gyorsítótárából szerezheti be a tiszta szöveges jelszót. Alapértelmezés szerint az automatikus kiegészítés engedélyezve van, és kifejezetten le kell tiltani. |

### <a name="example"></a>Példa
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Annak ellenőrzése, hogy a felhasználói képernyőn megjelenő bizalmas adatok maszkosak-e

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Az olyan érzékeny adatokat, mint a jelszavak, hitelkártyaszámok, SSN stb. Ez megakadályozza, hogy illetéktelen személyek hozzáférjenek az adatokhoz (pl. váll-szörfözés jelszavak, támogató személyzet megtekintésével SSN felhasználók száma) . Győződjön meg arról, hogy ezek az adatelemek nem láthatók egyszerű szövegként, és megfelelően maszkoltak.Ensure that these data elements are not visible in plain text and are appropriately maszk. Ezt gondoskodni kell abevitelükről (pl. beviteli típus="jelszó"), valamint visszajelenik a képernyőn (pl. csak a hitelkártyaszám utolsó 4 számjegyét jeleníti meg). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Dinamikus adatmaszkolás megvalósítása a bizalmas adatexpozíció korlátozása érdekében nem kiemelt jogosultságú felhasználók számára

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL verzió - V12, SQL verzió - MsSQL2016 |
| **Referencia**              | [Dinamikus adatmaszkolás](https://msdn.microsoft.com/library/mt130841) |
| **Lépéseket** | A dinamikus adatmaszkolás célja a bizalmas adatok expozíciójának korlátozása, megakadályozva, hogy azok a felhasználók, akik nem férnek hozzá az adatokhoz, megtekinthessék azadatokat. A dinamikus adatmaszkolás nak nem célja, hogy megakadályozza, hogy az adatbázis-felhasználók közvetlenül csatlakozzanak az adatbázishoz, és kimerítő lekérdezéseket futtassanak, amelyek a bizalmas adatok egyes darabjait teszik elérhetővé. A dinamikus adatmaszkolás kiegészíti az SQL Server egyéb biztonsági szolgáltatásait (naplózás, titkosítás, sorszintű biztonság...), és erősen ajánlott ezt a funkciót velük együtt használni a bizalmas adatok jobb védelme érdekében, hogy jobban védjék a bizalmas adatokat a Adatbázis. Kérjük, vegye figyelembe, hogy ezt a funkciót csak a 2016-os és az Azure SQL Database-től kezdődő SQL Server támogatja. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>A jelszavak sós kivonatformátumban való tárolásának biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Jelszókivonat a .NET kriptográfiai API-k használatával](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Lépéseket** | A jelszavakat nem szabad egyéni felhasználói tárolóadatbázisokban tárolni. A jelszókipárgásokat sóértékekkel kell tárolni. Győződjön meg róla, hogy a felhasználó sója mindig egyedi, és a jelszó tárolása előtt b-crypt, s-crypt vagy PBKDF2-t alkalmaz, a minimális munkatényező-iterációszám 150 000 hurkot tartalmaz, hogy megszüntesse a találgatáskényszerítés lehetőségét.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Annak ellenőrzése, hogy az adatbázisoszlopokban lévő bizalmas adatok titkosítva vannak-e

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | SQL verzió - minden |
| **Referencia**              | [Bizalmas adatok titkosítása az SQL-kiszolgálón](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Útmutató: Adatoszlop titkosítása az SQL Server kiszolgálón](https://msdn.microsoft.com/library/ms179331), [Titkosítás tanúsítványsal](https://msdn.microsoft.com/library/ms188061) |
| **Lépéseket** | A bizalmas adatokat, például a hitelkártyaszámokat titkosítani kell az adatbázisban. Az adatok oszlopszintű titkosítással vagy alkalmazásfunkcióval titkosíthatók a titkosítási függvények használatával. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Az adatbázisszintű titkosítás (TDE) engedélyezésének ellenőrzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az SQL Server transzparens adattitkosítás (TDE) ismertetése](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Lépéseket** | Az SQL-kiszolgáló transzparens adattitkosítási (TDE) szolgáltatása segít az adatbázis bizalmas adatainak titkosításában és az adatok tanúsítványokkal történő titkosításához használt kulcsok védelmében. Ez megakadályozza, hogy a kulcsok nélkül lévők használják az adatokat. A TDE védi az "inmegpihenésben" lévő adatokat, azaz az adatokat és a naplófájlokat. Ez biztosítja a képességét, hogy megfeleljen számos törvények, rendeletek, és iránymutatások at létrehozott különböző iparágakban. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Az adatbázis biztonsági másolatainak titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL verzió - V12, SQL verzió - MsSQL2014 |
| **Referencia**              | [SQL-adatbázis biztonsági másolatának titkosítása](https://msdn.microsoft.com/library/dn449489) |
| **Lépéseket** | Az SQL Server képes az adatok titkosítására biztonsági másolat létrehozása közben. A titkosítási algoritmus és a titkosító (tanúsítvány vagy aszimmetrikus kulcs) megadásával biztonsági másolat létrehozásakor titkosított biztonsági másolat készíthető. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Annak biztosítása, hogy a webes API-ra vonatkozó bizalmas adatok ne legyenek tárolva a böngésző tárhelyén

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | Identitásszolgáltató – ADFS, identitásszolgáltató – Azure AD |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Bizonyos implementációkban a webes API hitelesítéséhez kapcsolódó bizalmas összetevők a böngésző helyi tárolójában tárolódnak. Például az Azure AD hitelesítési összetevők, például adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key stb.</p><p>Mindezek az összetevők a kijelentkezés vagy a böngésző bezárása után is elérhetők. Ha egy ellenség hozzáférést kap ezekhez a műtermékekhez, újra felhasználhatja őket a védett erőforrások (API-k) eléréséhez. Győződjön meg arról, hogy a webes API-val kapcsolatos összes bizalmas összetevő nem tárolódik a böngésző tárolójában. Azokban az esetekben, ahol az ügyféloldali tárolás elkerülhetetlen (pl. egyoldalas alkalmazások (SPA), amelyek implicit OpenIdConnect/OAuth-folyamatokat kell tárolni a hozzáférési jogkivonatokat helyileg), használja tárolási lehetőségek nem rendelkeznek perzisztencia használatával. például a SessionStorage-t részesíti előnyben a LocalStorage-nal.</p>| 

### <a name="example"></a>Példa
Az alábbi JavaScript-kódrészlet egy egyéni hitelesítési kódtárból származik, amely a hitelesítési összetevőket a helyi tárolóban tárolja. Az ilyen megvalósításokat el kell kerülni. 
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

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>A Cosmos DB-ben tárolt bizalmas adatok titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Bizalmas adatok titkosítása alkalmazásszinten, mielőtt a db dokumentumban tárolna, vagy bármilyen bizalmas adatot tárolna más tárolási megoldásokban, például az Azure Storage-ban vagy az Azure SQL-ben| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Az Azure Disk Encryption használata a virtuális gépek által használt lemezek titkosításához

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure IaaS virtuálisgép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Azure Disk Encryption használata a virtuális gépek által használt lemezek titkosításához](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Lépéseket** | <p>Az Azure Disk Encryption egy új funkció, amely jelenleg előzetes verzióban érhető el. Ez a szolgáltatás lehetővé teszi az IaaS virtuális gép által használt operációsrendszer-lemezek és adatlemezek titkosítását. A Windows esetében a meghajtók az iparági szabványnak megfelelő BitLocker titkosítási technológiával vannak titkosítva. Linux esetén a lemezek a DM-Crypt technológiával vannak titkosítva. Ez integrálva van az Azure Key Vault segítségével a lemeztitkosítási kulcsok vezérléséhez és kezeléséhez. Az Azure Disk Encryption megoldás a következő három ügyféltitkosítási forgatókönyvet támogatja:</p><ul><li>Engedélyezze a titkosítást az ügyfél által titkosított virtuális merevlemez-fájlokból és az Azure Key Vaultban tárolt, ügyfél által biztosított titkosítási kulcsokból létrehozott új IaaS virtuális gépeken.</li><li>Engedélyezze a titkosítást az Azure Marketplace-ről létrehozott új IaaS virtuális gépeken.</li><li>Engedélyezze a titkosítást az Azure-ban már futó meglévő IaaS virtuális gépeken.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Titkos kulcsok titkosítása a Service Fabric-alkalmazásokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Szolgáltatásháló-kapcsolat megbízhatósági határa | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet - Azure |
| **Referencia**              | [Titkos kulcsok kezelése a Service Fabric-alkalmazásokban](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Lépéseket** | A titkos kulcsok lehetnek bizalmas információk, például tárolási kapcsolati karakterláncok, jelszavak vagy más értékek, amelyeket nem szabad egyszerű szövegként kezelni. Az Azure Key Vault használatával kulcsokat és titkos kulcsokat kezelhet a service fabric-alkalmazásokban. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Biztonsági modellezés végrehajtása és szükség esetén a Részlegek/csapatok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Biztonsági modellezés végrehajtása és szükség esetén a Részlegek/csapatok használata |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>A kritikus entitások megosztási funkciójának minimálisra állítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A kritikus entitások megosztási funkciójának minimálisra állítása |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>A felhasználók betanítása a Dynamics CRM megosztás funkciójával és a helyes biztonsági gyakorlatokkal kapcsolatos kockázatokra

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A felhasználók betanítása a Dynamics CRM megosztás funkciójával és a helyes biztonsági gyakorlatokkal kapcsolatos kockázatokra |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Fejlesztési szabványokra vonatkozó tiltó szabály felvétele a kivételkezelés ben a konfigurációs adatok megjelenítésére

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Fejlesztési szabványok szabályának felírása, amely a kivételkezelés fejlesztésen kívüli bizalmas részleteit mutatja. Ennek vizsgálata a kódfelülvizsgálatok vagy az időszakos ellenőrzés részeként.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Az Azure Storage Service Encryption (SSE) használata inaktív adatokhoz (előzetes verzió)

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Referencia**              | [Az Azure Storage Szolgáltatás titkosítása inaktív adatokhoz (előzetes verzió)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Lépéseket** | <p>Az Azure Storage Service Encryption (SSE) in-adattárolása segít megvédeni és védeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalásokat. Ezzel a funkcióval az Azure Storage automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. A titkosítás, a visszafejtés és a kulcskezelés teljesen átlátható a felhasználók számára. Az SSE csak blokkblobokra, lapblobokra és hozzáfűző blobokra vonatkozik. A többi adattípus, beleértve a táblákat, várólistákat és fájlokat, nem lesz titkosítva.</p><p>Titkosítási és visszafejtési munkafolyamat:</p><ul><li>Az ügyfél engedélyezi a titkosítást a tárfiókon</li><li>Amikor az ügyfél új adatokat (PUT Blob, PUT Block, PUT Page stb.) ír a Blob storage-ba; minden írási titkosított 256 bites AES titkosítással, az egyik legerősebb blokk rejtjel áll rendelkezésre</li><li>Ha az ügyfélnek hozzáférésre van szüksége az adatokhoz (GET Blob, stb.), az adatok automatikusan visszafejtésre kerülnek, mielőtt visszatérnének a felhasználóhoz</li><li>Ha a titkosítás le van tiltva, az új írási műveletek már nem lesznek titkosítva, és a meglévő titkosított adatok mindaddig titkosítva maradnak, amíg a felhasználó újra nem írja. Amíg a titkosítás engedélyezve van, a Blob storage-ba történő írások titkosítva lesznek. Az adatok állapota nem változik, ha a felhasználó a tárfiók titkosításának engedélyezése/letiltása között vált</li><li>Minden titkosítási kulcsot a Microsoft tárol, titkosít és kezel</li></ul><p>Kérjük, vegye figyelembe, hogy jelenleg a titkosításhoz használt kulcsokat a Microsoft kezeli. A Microsoft eredetileg létrehozza a kulcsokat, és kezeli a kulcsok biztonságos tárolását, valamint a Microsoft belső szabályzatában meghatározott rendszeres rotációt. A jövőben az ügyfelek saját >titkosítási kulcsaik kezelésére, valamint a Microsoft által felügyelt kulcsokról az ügyfél által felügyelt kulcsokra való áttelepítési útvonal megadására is lehetőséget kapnak.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Bizalmas adatok tárolása az Azure Storage-ban ügyféloldali titkosítással

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Ügyféloldali titkosítás és Azure Key Vault a Microsoft Azure Storage számára](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [oktatóanyag: Blobok titkosítása és visszafejtése a Microsoft Azure Storage-ban az Azure Key Vault használatával,](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/) [adatok biztonságos tárolása az Azure Blob Storage szolgáltatásban az Azure titkosítási bővítményekkel](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Lépéseket** | <p>Az Azure Storage Client Library for .NET Nuget csomag támogatja az adatok titkosítását az ügyfélalkalmazásokon belül, mielőtt feltöltené az Azure Storage-ba, és visszafejti az adatokat az ügyfélre való letöltés közben. A kódtár emellett támogatja az Azure Key Vault rendszerrel való integrálást a tárfiókkulcs-kezelés biztosítása érdekében. Itt van egy rövid leírást, hogyan ügyféloldali titkosítás működik:</p><ul><li>Az Azure Storage-ügyfél SDK tartalomtitkosítási kulcsot (CEK) hoz létre, amely egy egyszeri használatú szimmetrikus kulcs</li><li>Az ügyféladatok titkosítása ezzel a CEK-vel történik</li><li>A CEK ezután a kulcstitkosítási kulcs (KEK) használatával burkolódulatba (titkosít). A KEK egy kulcsazonosító val azonosítható, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és helyileg kezelhető vagy az Azure Key Vaultban tárolható. Maga a storage-ügyfél soha nem fér hozzá a KEK-hez. Csak meghívja a key wrap algoritmus, amely a Key Vault által biztosított. Az ügyfelek egyéni szolgáltatókat használhatnak a kulcstördeléshez/kicsomagoláshoz, ha azt szeretnék</li><li>A titkosított adatok at majd feltölti az Azure Storage szolgáltatásba. Ellenőrizze a hivatkozások a hivatkozások szakaszban az alacsony szintű végrehajtás részleteit.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>A telefonokhelyi tárhelyre írt bizalmas vagy személyazonosításra képes adatok titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Generikus, Xamarin  |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az eszközök beállításainak és szolgáltatásainak kezelése a Microsoft Intune-házirendekkel](https://docs.microsoft.com/mem/intune/configuration/), [Kulcskarikai kalak](https://components.xamarin.com/view/square.valet) |
| **Lépéseket** | <p>Ha az alkalmazás olyan bizalmas adatokat ír, mint a felhasználó személyazonosításra vonatkozó adatai (e-mail, telefonszám, keresztnév, vezetéknév, beállítások stb.) - a mobil fájlrendszer, akkor meg kell titkosítani, mielőtt írásban a helyi fájlrendszer. Ha az alkalmazás vállalati alkalmazás, akkor vizsgálja meg az alkalmazás Windows Intune használatával történő közzétételének lehetőségét.</p>|

### <a name="example"></a>Példa
Az Intune a következő biztonsági házirendekkel konfigurálható a bizalmas adatok védelme érdekében: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Példa
Ha az alkalmazás nem vállalati alkalmazás, akkor használja a platform által biztosított kulcstárolót, kulcsláncokat a titkosítási kulcsok tárolására, amelyek segítségével a kriptográfiai művelet a fájlrendszeren hajtható végre. A következő kódrészlet bemutatja, hogyan érheti el a kulcskulcsot a xamarin használatával: 
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

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>A létrehozott bináris fájlok elferdítése a végfelhasználóknak történő terjesztés előtt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Kriptográfia a .Net számára](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Lépéseket** | A létrehozott bináris fájlokat (apk-on belüli szerelvényeket) el kell takasztani, hogy megállítsák az összeállítások visszafejtését. Ehhez `CryptoObfuscator` a célhoz hasonló eszközöket lehet használni. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Az clientCredentialType beállítása tanúsítványra vagy Windows rendszerre

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Megerősítsék](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Lépéseket** | Ha egy usernametoken egy titkosítatlan csatornán keresztül egyszerű szöveges jelszóval használja, az elérhetővé teszi a jelszót a SOAP-üzeneteket szimatoló támadók számára. A UsernameToken szolgáltatást használó szolgáltatók elfogadhatják az egyszerű szövegként küldött jelszavakat. Ha egyszerű szöveges jelszavakat küld egy titkosítatlan csatornán, a hitelesítő adatokat olyan támadók számára teheti elérhetővé, akik megszagolhatják a SOAP-üzenetet. | 

### <a name="example"></a>Példa
A következő WCF-szolgáltató konfiguráció a UsernameToken nevet használja: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Állítsa az clientCredentialType típust tanúsítványra vagy Windows rendszerre. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>A WCF-biztonsági mód nincs engedélyezve

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, .NET keretrendszer 3 |
| **Attribútumok**              | Biztonsági mód - Szállítás, Biztonsági mód - Üzenet |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) [, alapjai WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Lépéseket** | Nincs megadva átvitel vagy üzenetbiztonság. Azok az alkalmazások, amelyek átvitel vagy üzenetbiztonság nélkül továbbítanak üzeneteket, nem garantálják az üzenetek integritását vagy titkosságát. Ha a WCF biztonsági kötése Nincs, mind az átvitel, mind az üzenetek biztonsága le van tiltva. |

### <a name="example"></a>Példa
A következő konfiguráció a biztonsági módot Nincs. 
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
Biztonsági mód Az összes szolgáltatáskötésben öt lehetséges biztonsági mód létezik: 
* Nincs. Kikapcsolja a biztonságot. 
* Közlekedési. Az átvitel biztonságát használja a kölcsönös hitelesítéshez és az üzenetek védelméhez. 
* Üzenetet. Üzenetbiztonságot használ a kölcsönös hitelesítéshez és az üzenetek védelméhez. 
* Mind. Lehetővé teszi az átviteli és üzenetszintű biztonság beállításainak biztosítását (ezt csak az MSMQ támogatja). 
* TransportWithMessageCredential . A hitelesítő adatokat az üzenettel együtt adja át, az üzenetvédelmet és a kiszolgáló hitelesítését pedig az átviteli réteg biztosítja. 
* TransportCredentialOnly. Az ügyfél hitelesítő adatait a rendszer az átviteli réteggel együtt adja át, és a rendszer nem alkalmazza az üzenetvédelmet. Az üzenetek sértetlenségének és titkosságának védelme érdekében használja a szállítás- és üzenetbiztonságot. Az alábbi konfiguráció arra utasítja a szolgáltatást, hogy az átviteli biztonságot üzenethitelesítő adatokkal használja.
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
