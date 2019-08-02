---
title: Bizalmas adatok – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: a Threat Modeling Toolban elérhető fenyegetések enyhítése
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
ms.openlocfilehash: d482ae375fca3824213b54c2045d114fa2f0bfbe
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727868"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Biztonsági keret: Bizalmas adatok | Enyhítését 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Számítógép-megbízhatósági kapcsolat határa** | <ul><li>[Győződjön meg arról, hogy a bináris fájlok el vannak-e torzítva, ha bizalmas adatokat tartalmaznak](#binaries-info)</li><li>[Érdemes titkosított fájlrendszert (EFS) használni a bizalmas felhasználói adatok védelméhez](#efs-user)</li><li>[Győződjön meg arról, hogy az alkalmazás által a fájlrendszerben tárolt bizalmas adatok titkosítva vannak](#filesystem)</li></ul> | 
| **Webalkalmazás** | <ul><li>[Ügyeljen arra, hogy a bizalmas tartalom ne legyen gyorsítótárazva a böngészőben](#cache-browser)</li><li>[A webalkalmazás bizalmas adatokat tartalmazó konfigurációs fájljainak részeinek titkosítása](#encrypt-data)</li><li>[Az automatikus kiegészítési HTML-attribútum explicit letiltása bizalmas űrlapokon és bemeneteken](#autocomplete-input)</li><li>[Győződjön meg arról, hogy a felhasználói képernyőn megjelenő bizalmas adatok maszkoltak](#data-mask)</li></ul> | 
| **Adatbázis** | <ul><li>[Dinamikus adatmaszkolás megvalósítása a bizalmas adatokkal nem rendelkező felhasználók korlátozása érdekében](#dynamic-users)</li><li>[Győződjön meg arról, hogy a jelszavakat sós kivonatoló formátumban tárolják](#salted-hash)</li><li>[Ügyeljen arra, hogy a bizalmas adatok titkosítva legyenek az adatbázis oszlopaiban](#db-encrypted)</li><li>[Győződjön meg arról, hogy az adatbázis-szintű titkosítás (TDE) engedélyezve van](#tde-enabled)</li><li>[Győződjön meg arról, hogy az adatbázis biztonsági mentései titkosítottak](#backup)</li></ul> | 
| **Webes API** | <ul><li>[Ügyeljen arra, hogy a webes API-ra vonatkozó bizalmas adatok ne legyenek tárolva a böngésző tárolójában](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Azure Cosmos DB tárolt bizalmas adatok titkosítása](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM-megbízhatósági kapcsolat határa** | <ul><li>[A Virtual Machines által használt lemezek titkosítása Azure Disk Encryption használatával](#disk-vm)</li></ul> | 
| **Service Fabric megbízhatósági kapcsolat határa** | <ul><li>[A titkok titkosítása Service Fabric alkalmazásokban](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Biztonsági modellezés végrehajtása, és szükség esetén üzleti egységek/csapatok használata](#modeling-teams)</li><li>[A megosztási funkciók elérésének csökkentése a kritikus entitásokon](#entities)</li><li>[A felhasználók betanítása a Dynamics CRM-megosztási szolgáltatáshoz kapcsolódó kockázatokkal és a jó biztonsági gyakorlattal](#good-practices)</li><li>[Fejlesztési szabványok szabályának betiltására, amely a konfiguráció részleteit mutatja a kivételek kezelése során](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Az Azure Storage Service Encryption (SSE) használata a REST-adatokhoz (előzetes verzió)](#sse-preview)</li><li>[Az ügyféloldali titkosítás használata bizalmas adatok tárolására az Azure Storage-ban](#client-storage)</li></ul> | 
| **Mobil ügyfél** | <ul><li>[A telefon helyi tárhelyére írt bizalmas vagy személyes adatok titkosítása](#pii-phones)</li><li>[A generált bináris fájlok elhomályosítása a végfelhasználók számára történő terjesztés előtt](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[ClientCredentialType beállítása a tanúsítványra vagy a Windowsra](#cert)</li><li>[WCF – a biztonsági üzemmód nincs engedélyezve](#security)</li></ul> | 

## <a id="binaries-info"></a>Győződjön meg arról, hogy a bináris fájlok el vannak-e torzítva, ha bizalmas adatokat tartalmaznak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy a bináris fájlok el vannak hajtva, ha olyan bizalmas információkat tartalmaznak, mint például a kereskedelmi titkok, a bizalmas üzleti logika, amely nem fordítható vissza. Ez a szerelvények fordított mérnöki leállítása. Ehhez hasonló `CryptoObfuscator` eszközök használhatók erre a célra. |

## <a id="efs-user"></a>Érdemes titkosított fájlrendszert (EFS) használni a bizalmas felhasználói adatok védelméhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Érdemes lehet titkosított fájlrendszert (EFS) használni a bizalmas, felhasználó-specifikus adatok védelmére az ellenfelektől a számítógéphez való fizikai hozzáféréssel. |

## <a id="filesystem"></a>Győződjön meg arról, hogy az alkalmazás által a fájlrendszerben tárolt bizalmas adatok titkosítva vannak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy az alkalmazás által a fájlrendszerben tárolt bizalmas adatok titkosítva vannak (például DPAPI használatával), ha az EFS nem kényszeríthető ki. |

## <a id="cache-browser"></a>Ügyeljen arra, hogy a bizalmas tartalom ne legyen gyorsítótárazva a böngészőben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Generic, Web Forms, MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A böngészők gyorsítótárazás és előzmények céljából tárolhatnak adatokat. Ezeket a gyorsítótárazott fájlokat egy mappában tárolja a rendszer, például az ideiglenes Internet Files mappát az Internet Explorer esetében. Ha ezeket az oldalakat újra megnevezik, a böngésző megjeleníti azokat a gyorsítótárból. Ha bizalmas adatok jelennek meg a felhasználó számára (például a címük, a bankkártya adatai, a társadalombiztosítási szám vagy a Felhasználónév), akkor ezeket az információkat a böngésző gyorsítótárában tárolhatja, így a böngésző gyorsítótárának vizsgálatával vagy egyszerűen nyomja meg a böngésző vissza gombját. A gyorsítótár-vezérlési válasz fejlécének beállítása az összes oldal "No-Store" értékére. |

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
Ezt egy szűrőn keresztül lehet megvalósítani. A következő példa használható: 
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

## <a id="encrypt-data"></a>A webalkalmazás bizalmas adatokat tartalmazó konfigurációs fájljainak részeinek titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Útmutató: A ASP.NET 2,0 konfigurációs fejezeteinek titkosítása](https://msdn.microsoft.com/library/ff647398.aspx)a DPAPI használatával, [védett konfigurációs szolgáltató](https://msdn.microsoft.com/library/68ze1hb2.aspx)megadásával, [Azure Key Vault használatával az alkalmazások titkos](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) adatainak védelméhez |
| **Lépések** | A konfigurációs fájlok, például a web. config, a appSettings. JSON gyakran a bizalmas adatok tárolására szolgálnak, beleértve a felhasználóneveket, a jelszavakat, az adatbázis-kapcsolati karakterláncokat és a titkosítási kulcsokat. Ha nem védik ezeket az információkat, az alkalmazás sebezhetővé válik a támadók és a rosszindulatú felhasználók számára bizalmas információk, például a fiókok felhasználónevei és jelszavaik, az adatbázisok nevei és a kiszolgálók nevei alapján. A központi telepítési típus (Azure/helyszíni) alapján Titkosítsa a konfigurációs fájlok bizalmas részeit a DPAPI vagy olyan szolgáltatásokkal, mint például a Azure Key Vault. |

## <a id="autocomplete-input"></a>Az automatikus kiegészítési HTML-attribútum explicit letiltása bizalmas űrlapokon és bemeneteken

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN: automatikus kiegészítési attribútum](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), automatikus [kiegészítés használatával HTML-ben](https://msdn.microsoft.com/library/ms533032.aspx), [HTML-tisztítási biztonsági rés](https://technet.microsoft.com/security/bulletin/MS10-071), [automatikus kiegészítés., újra?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Lépések** | Az automatikus kiegészítés attribútum azt adja meg, hogy az űrlapon be vagy ki kell-e kapcsolni az automatikus kiegészítést. Ha az automatikus kiegészítés be van kapcsolva, a böngésző automatikusan befejezi az értékeket a felhasználó által korábban megadott értékek alapján. Ha például egy új név és jelszó van megadva egy űrlapon, és az űrlap el van küldve, a böngésző megkérdezi, hogy a jelszót menteni kell-e. Ezután amikor megjelenik az űrlap, a rendszer automatikusan kitölti a nevet és a jelszót, és a név megadásakor megtörténik. A helyi hozzáféréssel rendelkező támadók megszerezhetik a titkosítatlan szöveges jelszót a böngésző gyorsítótárából. Alapértelmezés szerint az automatikus kiegészítés engedélyezve van, és explicit módon le kell tiltani. |

### <a name="example"></a>Példa
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Győződjön meg arról, hogy a felhasználói képernyőn megjelenő bizalmas adatok maszkoltak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A képernyőn megjelenő bizalmas adatokat, például a jelszavakat, a hitelkártya-számokat, a Taj-t stb. Ezzel megakadályozható, hogy jogosulatlan személyek hozzáférjenek az adatokhoz (pl.: a váll-szörfözés jelszavai, a támogatási személyzetnek a felhasználók megtekintő számú tagja). Győződjön meg arról, hogy ezek az adatelemek nem láthatók egyszerű szövegben, és megfelelően vannak maszkban. Ezt úgy kell figyelni, hogy bemenetként fogadja őket (például:. bemeneti típus = "password"), valamint a képernyőn visszamutatva (például csak a hitelkártyaszám utolsó 4 számjegyét jeleníti meg). |

## <a id="dynamic-users"></a>Dinamikus adatmaszkolás megvalósítása a bizalmas adatokkal nem rendelkező felhasználók korlátozása érdekében

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL-verzió – V12, SQL-verzió – MsSQL2016 |
| **Hivatkozik**              | [Dinamikus adatmaszkolás](https://msdn.microsoft.com/library/mt130841) |
| **Lépések** | A dinamikus adatmaszkolás célja a bizalmas adatok expozíciójának korlátozása, amely megakadályozza, hogy a felhasználók nem férhetnek hozzá az adatokhoz. A dinamikus adatmaszkolás nem akadályozza meg, hogy az adatbázis-felhasználók közvetlenül kapcsolódjanak az adatbázishoz, és olyan teljes körű lekérdezéseket futtassanak, amelyek bizalmas adatokat tesznek elérhetővé. A dinamikus adatmaszkolás kiegészíti a többi SQL Server biztonsági funkciót (naplózás, titkosítás, sor szintű biztonság...), és erősen ajánlott ezt a funkciót együtt használni, ha a bizalmas adatok jobb védelme érdekében a adatbázis. Vegye figyelembe, hogy ezt a funkciót csak a 2016-as és a Azure SQL Database-es SQL Servertól támogatott. |

## <a id="salted-hash"></a>Győződjön meg arról, hogy a jelszavakat sós kivonatoló formátumban tárolják

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Jelszó-kivonatolás .NET titkosítási API-k használatával](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Lépések** | A jelszavakat nem szabad egyéni felhasználói tároló adatbázisaiban tárolni. A jelszó-kivonatokat a Salt Values értékkel kell tárolni. Győződjön meg arról, hogy a felhasználóhoz tartozó só mindig egyedi, és a Jelszó tárolása előtt alkalmazza a b-crypt, az s-Crypt vagy a PBKDF2, valamint az 150 000 hurkok minimális munkatényező-iterációs számát, hogy kizárja a találgatások kikényszerítésének lehetőségét.| 

## <a id="db-encrypted"></a>Ügyeljen arra, hogy a bizalmas adatok titkosítva legyenek az adatbázis oszlopaiban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | SQL-verzió – mind |
| **Hivatkozik**              | [Bizalmas adatok titkosítása az SQL](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx)Serverben [, útmutató: Adatoszlopok titkosítása SQL Serverban](https://msdn.microsoft.com/library/ms179331), [tanúsítvány titkosítása](https://msdn.microsoft.com/library/ms188061) |
| **Lépések** | A bizalmas adatokat, például a bankkártya-számokat titkosítani kell az adatbázisban. Az adatok az oszlop szintű titkosítással vagy egy alkalmazás-függvénnyel titkosíthatók a titkosítási függvények használatával. |

## <a id="tde-enabled"></a>Győződjön meg arról, hogy az adatbázis-szintű titkosítás (TDE) engedélyezve van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [A SQL Server transzparens adattitkosítás ismertetése (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Lépések** | Az SQL Server transzparens adattitkosítás (TDE) funkciója segíti a bizalmas adatok titkosítását egy adatbázisban, és védi az adatok tanúsítvánnyal való titkosításához használt kulcsokat. Ezzel megakadályozhatja, hogy a kulcsok ne használják az adatok használatát. A TDE megvédi az adatok "nyugalmi állapotban", azaz az adatok és a naplófájlok tartalmát. Lehetővé teszi a különböző iparágakban bevezetett törvények, rendeletek és irányelvek betartását. |

## <a id="backup"></a>Győződjön meg arról, hogy az adatbázis biztonsági mentései titkosítottak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL-verzió – V12, SQL-verzió – MsSQL2014 |
| **Hivatkozik**              | [SQL-adatbázis biztonsági másolatának titkosítása](https://msdn.microsoft.com/library/dn449489) |
| **Lépések** | A SQL Server képes a biztonsági másolat létrehozása közben titkosítani az adatvédelmet. A titkosítási algoritmus és a titkosító (tanúsítvány vagy aszimmetrikus kulcs) megadásával biztonsági másolat létrehozásakor egy titkosított biztonságimásolat-fájlt is létrehozhat. |

## <a id="api-browser"></a>Ügyeljen arra, hogy a webes API-ra vonatkozó bizalmas adatok ne legyenek tárolva a böngésző tárolójában

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | Identitás-szolgáltató – ADFS, identitás-szolgáltató – Azure AD |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Bizonyos implementációkban a webes API-hitelesítéshez kapcsolódó bizalmas összetevők a böngésző helyi tárolójában tárolódnak. Ilyenek például az Azure AD-hitelesítési összetevők, például a adal. idtoken, a adal. alkalom. idtoken, a adal. Access. token. Key, a adal. token. Keys, a adal. State. login, a adal. Session. State, a adal. lejárat. Key stb.</p><p>Ezek az összetevők még a kijelentkezés vagy a böngésző bezárása után is elérhetők. Ha egy támadó hozzáfér ezekhez az összetevőkhöz, azt újra használhatja a védett erőforrások (API-k) eléréséhez. Győződjön meg arról, hogy a webes API-hoz kapcsolódó összes bizalmas összetevő nem a böngésző tárolójában van tárolva. Azokban az esetekben, amikor az ügyféloldali tárolók nem elkerülhetők (például egyoldalas alkalmazások (SPA), amelyek az implicit OpenIdConnect/OAuth-folyamatokat használják, a hozzáférési jogkivonatokat helyileg kell tárolni), a nem rendelkezik adatmegőrzési lehetőségekkel. például inkább a SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Példa
Az alábbi JavaScript-kódrészlet egy egyéni hitelesítési könyvtárból származik, amely a helyi tárolóban tárolja a hitelesítési összetevőket. Ezeket a megvalósításokat el kell kerülni. 
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

## <a id="encrypt-docdb"></a>Cosmos DB tárolt bizalmas adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A bizalmas adatok titkosítása az alkalmazás szintjén a Document DB-ben való tárolás előtt, vagy bármilyen bizalmas adat tárolása más tárolási megoldásokban, például az Azure Storage vagy az Azure SQL szolgáltatásban| 

## <a id="disk-vm"></a>A Virtual Machines által használt lemezek titkosítása Azure Disk Encryption használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure IaaS VM-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [A virtuális gépek által használt lemezek titkosítása Azure Disk Encryption használatával](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Lépések** | <p>A Azure Disk Encryption egy új szolgáltatás, amely jelenleg előzetes verzióban érhető el. Ez a funkció lehetővé teszi a IaaS virtuális gépek által használt operációsrendszer-lemezek és adatlemezek titkosítását. A Windows rendszerben a meghajtók titkosítása az iparági szabványnak megfelelő BitLocker titkosítási technológiával történik. A Linux rendszerben a lemezek titkosítása a DM-Crypt technológiával történik. Ez integrálva van Azure Key Vault, hogy lehetővé tegye a lemezes titkosítási kulcsok szabályozását és kezelését. A Azure Disk Encryption megoldás a következő három ügyfél-titkosítási forgatókönyvet támogatja:</p><ul><li>Engedélyezze a titkosítást az ügyfél által titkosított VHD-fájlokból létrehozott új IaaS virtuális gépeken, illetve az ügyfél által biztosított titkosítási kulcsokban, amelyeket a Azure Key Vault tárol.</li><li>Engedélyezze a titkosítást az Azure piactéren létrehozott új IaaS virtuális gépeken.</li><li>Engedélyezze a titkosítást az Azure-ban már futó meglévő IaaS virtuális gépeken.</li></ul>| 

## <a id="fabric-apps"></a>A titkok titkosítása Service Fabric alkalmazásokban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozik**              | [A titkok kezelése Service Fabric alkalmazásokban](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Lépések** | A titkok lehetnek bármilyen bizalmas információk, például a tárolási kapcsolatok karakterláncai, jelszavai vagy más olyan értékek, amelyeket nem szabad egyszerű szövegben kezelni. A Azure Key Vault segítségével kezelheti a Service Fabric-alkalmazásokban található kulcsokat és titkokat. |

## <a id="modeling-teams"></a>Biztonsági modellezés végrehajtása, és szükség esetén üzleti egységek/csapatok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Biztonsági modellezés végrehajtása, és szükség esetén üzleti egységek/csapatok használata |

## <a id="entities"></a>A megosztási funkciók elérésének csökkentése a kritikus entitásokon

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A megosztási funkciók elérésének csökkentése a kritikus entitásokon |

## <a id="good-practices"></a>A felhasználók betanítása a Dynamics CRM-megosztási szolgáltatáshoz kapcsolódó kockázatokkal és a jó biztonsági gyakorlattal

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A felhasználók betanítása a Dynamics CRM-megosztási szolgáltatáshoz kapcsolódó kockázatokkal és a jó biztonsági gyakorlattal |

## <a id="exception-mgmt"></a>Fejlesztési szabványok szabályának betiltására, amely a konfiguráció részleteit mutatja a kivételek kezelése során

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Vegyen fel egy fejlesztési szabványokra vonatkozó szabályt, amely a betiltására a fejlesztésen kívül a konfiguráció részleteit jeleníti meg. Ezt tesztelheti a Code Reviews vagy az időszakos ellenőrzés részeként.|

## <a id="sse-preview"></a>Az Azure Storage Service Encryption (SSE) használata a REST-adatokhoz (előzetes verzió)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType – blob |
| **Hivatkozik**              | [Azure-Storage Service Encryption a REST-adatokhoz (előzetes verzió)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Lépések** | <p>Az Azure Storage Service Encryption (SSE) for inaktív adatok segítenek megvédeni és védeni az adatait, hogy megfeleljenek a szervezet biztonsági és megfelelőségi kötelezettségvállalásainak. Ezzel a funkcióval az Azure Storage automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. A titkosítás, a visszafejtés és a kulcskezelő szolgáltatás teljes mértékben átlátható a felhasználók számára. Az SSE csak a Blobok, az oldal blobok és a hozzáfűzési Blobok esetében érvényes. A más típusú adattípusok, beleértve a táblákat, a várólistákat és a fájlokat, nem lesznek titkosítva.</p><p>Titkosítási és visszafejtési munkafolyamat:</p><ul><li>Az ügyfél engedélyezi a titkosítást a Storage-fiókon.</li><li>Amikor az ügyfél új adatot ír (blobot, PUT blokkot, PUT oldalt stb.) a blob Storage-ba, minden írás 256 bites AES-titkosítással van titkosítva, amely az egyik legerősebb blokk titkosítási lehetőség</li><li>Amikor az ügyfélnek hozzá kell férnie az adateléréshez (blob beolvasása stb.), a rendszer automatikusan visszafejti az adatfeldolgozást, mielőtt visszatér a felhasználóhoz.</li><li>Ha a titkosítás le van tiltva, az új írások már nem lesznek titkosítva, és a meglévő titkosított adatforgalom addig titkosítva marad, amíg a felhasználó át nem írja. Ha engedélyezve van a titkosítás, a blob Storage-ba történő írások titkosítva lesznek. Az adatállapot nem változik a felhasználó által a Storage-fiók titkosításának engedélyezése/letiltása között</li><li>Az összes titkosítási kulcsot a Microsoft tárolja, titkosítja és kezeli</li></ul><p>Vegye figyelembe, hogy jelenleg a titkosításhoz használt kulcsokat a Microsoft kezeli. A Microsoft létrehozza a kulcsokat eredetileg, és felügyeli a kulcsok biztonságos tárolását, valamint a belső Microsoft-házirend által meghatározott normál rotációt is. A jövőben az ügyfeleknek lehetősége nyílik a saját > titkosítási kulcsaik kezelésére, és áttelepítési útvonalat biztosítani a Microsoft által felügyelt kulcsokból az ügyfél által felügyelt kulcsokra.</p>| 

## <a id="client-storage"></a>Az ügyféloldali titkosítás használata bizalmas adatok tárolására az Azure Storage-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [oktatóanyag: Blobok titkosítása és visszafejtése Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/)a Azure Key Vault használatával, az Azure- [blob Storage biztonságos tárolása az Azure encryption Extensions bővítménnyel](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Lépések** | <p>Az Azure Storage ügyféloldali kódtára a .NET Nuget csomag támogatja az ügyfeleken belüli adattitkosítást az Azure Storage-ba való feltöltés előtt, és az adatvisszafejtést az ügyfélre való letöltés során. A kódtár emellett támogatja az Azure Key Vault rendszerrel való integrálást a tárfiókkulcs-kezelés biztosítása érdekében. Íme egy rövid leírás arról, hogyan működik az ügyféloldali titkosítás:</p><ul><li>Az Azure Storage ügyféloldali SDK létrehoz egy Content encryption Key (CEK) kulcsot, amely egy egyszer használatos szimmetrikus kulcs</li><li>A vásárlói adatai titkosítva vannak ezzel a CEK</li><li>Ezután a CEK (titkosított) a kulcs titkosítási kulcs (KEK) használatával burkolta. A KEK-et egy kulcs-azonosító azonosítja, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és kezelhető helyileg, vagy Azure Key Vault tárolható. Maga a Storage-ügyfél soha nem fér hozzá a KEK-hez. Csak a Key Vault által biztosított kulcs-körbefuttatási algoritmust hívja meg. Az ügyfelek dönthetnek úgy, hogy egyéni szolgáltatókat használnak a kulcsok becsomagolásához/kicsomagolásához, ha szeretnék</li><li>Ezt követően a rendszer feltölti a titkosított fájlokat az Azure Storage szolgáltatásba. Az alacsony szintű megvalósítás részleteiért olvassa el a hivatkozások szakaszban található hivatkozásokat.</li></ul>|

## <a id="pii-phones"></a>A telefon helyi tárhelyére írt bizalmas vagy személyes adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, Xamarin  |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Az eszközök beállításainak és funkcióinak kezelése Microsoft Intune szabályzatokkal és](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies) [kulcstartóval](https://components.xamarin.com/view/square.valet) |
| **Lépések** | <p>Ha az alkalmazás bizalmas adatokat ír, például a felhasználó személyes adatait (e-mail cím, telefonszám, Utónév, vezetéknév, beállítások stb.) -a mobil fájlrendszerén, a helyi fájlrendszerbe való írás előtt titkosítva kell lennie. Ha az alkalmazás vállalati alkalmazás, akkor vizsgálja meg az alkalmazás közzétételének lehetőségét a Windows Intune használatával.</p>|

### <a name="example"></a>Példa
Az Intune a következő biztonsági házirendekkel konfigurálható a bizalmas adatok védelme érdekében: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Példa
Ha az alkalmazás nem vállalati alkalmazás, akkor használja a platform által biztosított kulcstartót, a kulcsokat a titkosítási kulcsok tárolásához, amelyekkel a fájlrendszeren elvégezhető a titkosítási művelet. A következő kódrészlet bemutatja, hogyan érheti el a kulcsot a kulcstartóból a xamarin használatával: 
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

## <a id="binaries-end"></a>A generált bináris fájlok elhomályosítása a végfelhasználók számára történő terjesztés előtt

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Titkosítási titkosítás a .net-hez](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Lépések** | A létrehozott bináris fájlokat (az apk-ben lévő szerelvényeket) a szerelvények fordított mérnöki leállítása érdekében el kell készíteni. Ehhez hasonló `CryptoObfuscator` eszközök használhatók erre a célra. |

## <a id="cert"></a>ClientCredentialType beállítása a tanúsítványra vagy a Windowsra

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Megerősítsék](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Lépések** | A titkosítatlan csatornán keresztüli egyszerű szöveges jelszóval rendelkező UsernameToken használatával a jelszó a SOAP-üzeneteket Szippantó támadók számára is elérhető. A UsernameToken használó szolgáltatók elfogadják az egyszerű szöveges formátumban elküldhető jelszavakat. Az egyszerű szöveges jelszavak titkosítatlan csatornán keresztüli küldése lehetővé teszi a hitelesítő adatokat a SOAP-üzenetet Szippantó támadók számára. | 

### <a name="example"></a>Példa
A következő WCF-szolgáltató konfigurációja a UsernameToken használja: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
ClientCredentialType beállítása a tanúsítványhoz vagy a Windowshoz. 

## <a id="security"></a>WCF – a biztonsági üzemmód nincs engedélyezve

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Biztonsági mód – átvitel, biztonsági mód – üzenet |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [a WCF biztonsági kód magazin alapjai](https://www.codemag.com/article/0611051) |
| **Lépések** | Nincs megadva átviteli vagy biztonsági üzenet. Azok az alkalmazások, amelyek átviteli vagy üzenet nélküli üzeneteket továbbítanak, nem garantálják az üzenetek integritását vagy titkosságát. Ha a WCF biztonsági kötése none értékre van állítva, akkor a szállítás és az üzenet biztonsága is le van tiltva. |

### <a name="example"></a>Példa
A következő konfiguráció a biztonsági módot állítja be a none értékre. 
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
A biztonsági mód az összes szolgáltatási kötésben öt lehetséges biztonsági üzemmódot jelent: 
* Nincs. Kikapcsolja a biztonságot. 
* Közlekedési. A a Transport Security szolgáltatást használja a kölcsönös hitelesítéshez és az üzenetek védelméhez. 
* az üzenet. A kölcsönös hitelesítéshez és az üzenetek védelméhez használja az üzenetek biztonságát. 
* Mind. Lehetővé teszi a szállítás és az üzenet szintű biztonság beállítását (csak az MSMQ támogatja ezt). 
* TransportWithMessageCredential. A hitelesítő adatokat a rendszer az üzenet és az üzenet védelmével továbbítja, és a kiszolgáló hitelesítését a szállítási réteg adja meg. 
* TransportCredentialOnly. Az ügyfél hitelesítő adatait a rendszer átadja a szállítási rétegnek, és az üzenet védelmét nem alkalmazza a rendszer. Az üzenetek integritásának és titkosságának védelme a Transport és az Message Security használatával. Az alábbi konfiguráció azt jelzi, hogy a szolgáltatás az üzenet hitelesítő adataival használja a továbbítási biztonságot.
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
