---
title: Bizalmas adatokat – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
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
ms.openlocfilehash: 5e9104f59173c3d39ef2f2232ed2a9c6864cf84f
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892558"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Biztonsági keret: Bizalmas adatok |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy a bináris fájlok bizalmas adatokat tartalmazó vannak rejtjelezett](#binaries-info)</li><li>[Fontolja meg a bizalmas felhasználói adatok védelme érdekében használja a titkosított fájlrendszer (EFS)](#efs-user)</li><li>[Győződjön meg arról, hogy az alkalmazás a fájlrendszeren tárolt bizalmas adatok titkosítva van](#filesystem)</li></ul> | 
| **Webalkalmazás** | <ul><li>[Győződjön meg arról, hogy bizalmas tartalom nem tárolja a böngészőben](#cache-browser)</li><li>[Webes alkalmazás a konfigurációs fájlok bizalmas adatokat tartalmazó szakaszok titkosítása](#encrypt-data)</li><li>[Explicit módon letiltja az automatikus kiegészítés bizalmas űrlapokban és a bemenetek HTML-attribútum](#autocomplete-input)</li><li>[Győződjön meg arról, hogy a felhasználói képernyőn a bizalmas adatok maszkolva van](#data-mask)</li></ul> | 
| **Adatbázis** | <ul><li>[Dinamikus adatmaszkolás korlátozhatja a bizalmas adatok nem rendszerjogosultságú kitettség felhasználók megvalósítása](#dynamic-users)</li><li>[Győződjön meg arról, hogy a jelszavak tárolása sózott ujjlenyomat formátuma](#salted-hash)</li><li>[ Győződjön meg arról, hogy az adatbázis-oszlopokban bizalmas adatok titkosítva van](#db-encrypted)</li><li>[Győződjön meg arról, hogy adatbázis-szintű titkosítást (TDE) engedélyezve van](#tde-enabled)</li><li>[Győződjön meg arról, hogy titkosított adatbázis biztonsági mentése](#backup)</li></ul> | 
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a bizalmas adatokat a webes API-t nem tárolja böngésző storage-ban](#api-browser)</li></ul> | 
| Az Azure Document DB-ről | <ul><li>[Az Azure Cosmos DB-ben tárolt bizalmas adatok titkosítása](#encrypt-docdb)</li></ul> | 
| **Az Azure IaaS virtuális gép megbízhatósági kapcsolat határán** | <ul><li>[Az Azure Disk Encryption használatával virtuális gépek által használt lemezek titkosítása](#disk-vm)</li></ul> | 
| **Service Fabric megbízhatósági kapcsolat határán** | <ul><li>[Titkosítani a titkos kulcsokat a Service Fabric-alkalmazások](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Biztonsági modellezési az üzleti egység/csapatok használható, és ahol szükséges](#modeling-teams)</li><li>[Megosztott szolgáltatás kritikus entitások minimalizálása](#entities)</li><li>[A Dynamics CRM-megosztási szolgáltatás és az ajánlott biztonsági eljárások a kockázatokat a felhasználók betanítása](#good-practices)</li><li>[Kivételek kezelése konfigurációs részleteket megjelenítő proscribing fejlesztési szabványok szabály belefoglalása](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Az Azure Storage Service Encryption (SSE) használata for Data at Rest (előzetes verzió)](#sse-preview)</li><li>[Ügyféloldali titkosítás használata a bizalmas adatok tárolásához Azure Storage-ban](#client-storage)</li></ul> | 
| **Mobileszköz ügyfél** | <ul><li>[Bizalmas vagy telefonok helyi tárba írt PII adatok titkosítása](#pii-phones)</li><li>[A végfelhasználók számára terjesztése előtt létrehozott bináris rejtse](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[ ClientCredentialType tanúsítvány vagy a Windows beállítása](#cert)</li><li>[WCF-biztonsági mód nincs engedélyezve](#security)</li></ul> | 

## <a id="binaries-info"></a>Győződjön meg arról, hogy a bináris fájlok bizalmas adatokat tartalmazó vannak rejtjelezett

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy bináris rejtjelezett vannak, ha bizalmas adatokat, például kereskedelmi titkok, bizalmas üzleti logikát kell nem tartalmaznak. Ez a visszafejtés sestavení leállítása. Eszközök, például a `CryptoObfuscator` erre a célra használhatók. |

## <a id="efs-user"></a>Fontolja meg a bizalmas felhasználói adatok védelme érdekében használja a titkosított fájlrendszer (EFS)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Vegye figyelembe, hogy a támadók fizikai hozzáféréssel rendelkező bizalmas felhasználói adatok védelméhez a számítógép a titkosított fájlrendszer (EFS) használja. |

## <a id="filesystem"></a>Győződjön meg arról, hogy az alkalmazás a fájlrendszeren tárolt bizalmas adatok titkosítva van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy az alkalmazás a fájlrendszeren tárolt bizalmas adatok titkosítva van-e (pl. DPAPI-t használ), ha az EFS nem kényszeríthető. |

## <a id="cache-browser"></a>Győződjön meg arról, hogy bizalmas tartalom nem tárolja a böngészőben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Generic, Web Forms, MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Böngészők gyorsítótárazás és előzmények célokra adatokat tárolhat. Ezek a gyorsítótárazott fájlok tárolódnak, mint az ideiglenes internetfájlok mappa esetén az Internet Explorer egy mappában. Ezeket az oldalakat a újra hivatkozunk, amikor a böngésző megjeleníti őket a gyorsítótárból. Ha bizalmas információk is megjelennek a felhasználó (például a címe, hitelkártya adatait, társadalombiztosítási szám vagy felhasználónév), majd ezeket az információkat lehet tárolja a böngésző gyorsítótárát, és így lekérdezhető, vizsgálata folyamatban van a böngésző gyorsítótárát keresztül, vagy egyszerűen csak billentyű a böngészőben "Újra" gombra. Állítsa be a cache-control válasz fejléce érték "no-store" minden lap. |

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

## <a id="encrypt-data"></a>Webes alkalmazás a konfigurációs fájlok bizalmas adatokat tartalmazó szakaszok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Útmutató: Az ASP.NET 2.0 használatával DPAPI konfigurációs szakaszokat titkosítása](https://msdn.microsoft.com/library/ff647398.aspx), [adjon meg egy védett Konfigurációszolgáltató](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Azure Key Vault használatával az alkalmazás titkainak védelmére](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Lépések** | Konfigurációs fájlok, például a Web.config fájlként appsettings.json gyakran használják bizalmas adatokat, köztük a felhasználónevek, jelszavak, adatbázis-kapcsolati karakterláncok és titkosítási kulcsok tárolásához. Ha ezt az információt nem védi, az alkalmazás kitéve a támadók vagy rosszindulatú felhasználók például fiók felhasználóneveket és jelszavakat, adatbázisnevek és kiszolgálók nevei bizalmas információk beszerzése. A központi telepítési típus (az azure és helyszíni) alapján, a titkosítást a DPAPI-t vagy a szolgáltatások, például az Azure Key Vault használatával a konfigurációs fájlok bizalmas szakaszait. |

## <a id="autocomplete-input"></a>Explicit módon letiltja az automatikus kiegészítés bizalmas űrlapokban és a bemenetek HTML-attribútum

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN: az automatikus kiegészítés attribútum](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [automatikus kiegészítési funkciójának használatával HTML](https://msdn.microsoft.com/library/ms533032.aspx), [HTML-tisztító biztonsági](https://technet.microsoft.com/security/bulletin/MS10-071), [automatikus kiegészítés., újra?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Lépések** | Az automatikus kiegészítés attribútum megadja e űrlap automatikus kiegészítés kell rendelkeznie, és ki. Az automatikus kiegészítés esetén a böngésző automatikus elvégzéséhez, mielőtt a felhasználó megadott értékek alapján értékeket. Például ha egy új nevet és jelszót szerepel egy űrlapot, és az űrlap elküldésekor, a böngésző kéri Ha a jelszó mentéséhez. Ezt követően amikor a képernyő jelenik meg, a név és jelszó automatikusan kitölti, vagy végezhető el, ahogy a neve van megadva. Helyi hozzáféréssel rendelkező támadó a tiszta szöveges jelszó a böngésző gyorsítótárából szerezze be. Alapértelmezés szerint engedélyezve van az automatikus kiegészítés, és explicit módon le lesz tiltva. |

### <a name="example"></a>Példa
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Győződjön meg arról, hogy a felhasználói képernyőn a bizalmas adatok maszkolva van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Bizalmas adatok, például jelszavakat, hitelkártyaszámokat tartalmazó, SSN stb. kell lennie maszkolva, amikor a képernyőn megjelenő. Ez a megakadályozhatja, hogy jogosulatlan személyek (például a váll-surfing jelszavak, a felhasználók SSN sorszámok megtekintése a támogatási csoporthoz) adatokhoz fér hozzá. Győződjön meg arról, hogy ezek az adatok elemek szövegként nem láthatók, és megfelelően maszkolva. Azt kell őket (pl. bemenetként elfogadása közben elvégzi. bemeneti típus = "password") és megjelenítésének vissza a képernyő (például megjelenítendő csak az utolsó 4 számjegyét a hitelkártya száma). |

## <a id="dynamic-users"></a>Dinamikus adatmaszkolás korlátozhatja a bizalmas adatok nem rendszerjogosultságú kitettség felhasználók megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL - 12-es, az SQL-verzió - verzió MsSQL2016 |
| **Hivatkozások**              | [Dynamic Data Masking](https://msdn.microsoft.com/library/mt130841) |
| **Lépések** | A dinamikus adatmaszkolás célja, hogy korlátozza a bizalmas adatok, megakadályozza, hogy a felhasználók, akik nem hozzáféréssel kell rendelkeznie az adatok megtekintésekor, megjelenítését. Dinamikus adatmaszkolás nem célja, hogy az adatbázis-felhasználók közvetlenül csatlakozik az adatbázishoz, és tegye elérhetővé a bizalmas adatok következőben történő részletes ellentmondás-lekérdezések futtatásához. Dinamikus adatmaszkolás kiegészíti a többi SQL Server biztonsági funkciók (naplózás, titkosítás, sorszintű biztonság...), és a funkció használatához őket együtt emellett ahhoz, hogy az a bizalmas adatok védelme érdekében előírta erősen ajánlott a az adatbázis. Vegye figyelembe, hogy ez a funkció csak az SQL Server 2016 kezdve és az Azure SQL Database által támogatott. |

## <a id="salted-hash"></a>Győződjön meg arról, hogy a jelszavak tárolása sózott ujjlenyomat formátuma

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Jelszó Hashing .NET kriptográfiai API-k használatával](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Lépések** | Egyéni felhasználói store adatbázisban nem kell tárolja a jelszavakat. Jelszókivonatokat inkább védőérték értékekkel kell tárolni. Ellenőrizze, hogy a felhasználó a védőérték mindig egyedi és b-crypt, s-crypt vagy PBKDF2 alkalmazza, a jelszót, és 150 000 bővítheti, így lehetősége találgatásos kényszerítése minimális munkahelyi tényező iteráció számával együtt való tárolás előtt.| 

## <a id="db-encrypted"></a>Győződjön meg arról, hogy az adatbázis-oszlopokban bizalmas adatok titkosítva van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | SQL-verzió - minden |
| **Hivatkozások**              | [Az SQL server bizalmas adatok titkosítása](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [hogyan: Az SQL Server adatoszlopok titkosítása](https://msdn.microsoft.com/library/ms179331), [tanúsítvány titkosítása](https://msdn.microsoft.com/library/ms188061) |
| **Lépések** | Bizalmas adatok, például hitelkártyaszámokon rendelkezik titkosítását az adatbázisban. Képes az oszlopszintű encryption használatával titkosított adatok, vagy egy alkalmazás-függvényt a titkosítási funkciók használatával. |

## <a id="tde-enabled"></a>Győződjön meg arról, hogy adatbázis-szintű titkosítást (TDE) engedélyezve van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az SQL Server transzparens adattitkosításának (TDE) ismertetése](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Lépések** | Az SQL server transzparens adattitkosítási (TDE) funkciója segít a titkosított bizalmas adatok egy adatbázisban, és a tanúsítvánnyal az adatok titkosításához használt kulcsok védelmét. Ezzel megakadályozza a kulcsok nélkül az adatok használatával. TDE védi az adatokat "Inaktív", azaz az adathoz és naplófájlhoz. Ez lehetővé teszi a sok törvényekkel, előírásokkal és a különböző iparágak létrehozott iránymutatásokat ahhoz, hogy. |

## <a id="backup"></a>Győződjön meg arról, hogy titkosított adatbázis biztonsági mentése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL - 12-es, az SQL-verzió - verzió MsSQL2014 |
| **Hivatkozások**              | [Az SQL database biztonsági mentés titkosítása](https://msdn.microsoft.com/library/dn449489) |
| **Lépések** | Az SQL Server képes az adatok titkosítása a biztonsági másolat létrehozása közben. A titkosítási algoritmus és a jelszótitkosító futtatásához (tanúsítvánnyal vagy aszimmetrikus kulccsal) megadásával biztonsági másolat létrehozásakor egy titkosított biztonsági mentési fájl hozhat létre. |

## <a id="api-browser"></a>Győződjön meg arról, hogy a bizalmas adatokat a webes API-t nem tárolja böngésző storage-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | Identity Provider - ADFS, identitásszolgáltató – Azure ad-ben |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Egyes megvalósítások a webes API-hitelesítés-és nagybetűket összetevők böngésző helyi tároló vannak tárolva. Például az Azure AD hitelesítési összetevők például adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key stb.</p><p>Ezek az összetevők után sem érhetők el az összes jelentkezzen ki, vagy a böngésző be van zárva. Ha egy támadó lekérdezi ezeket az összetevőket a hozzáférést, hallgatója felhasználhatja őket a védett erőforrások (API-k) eléréséhez. Győződjön meg arról, hogy webes API-hoz kapcsolódó összes bizalmas összetevők nem a böngészőben a storage-ban tárolt. Azokban az esetekben, ahol a storage ügyféloldali elkerülhetetlen (például egyetlen lapon alkalmazások (SPA), az Implicit OpenIdConnect/OAuth folyamatok kell helyileg a hozzáférési jogkivonatok tárolására), használható tárolási lehetőségek a nincs megőrzését. például inkább SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Példa
Az alábbi JavaScript kódrészletet egy egyéni hitelesítési kódtár, amely a helyi tároló tárolja a hitelesítési összetevők származik. Az ilyen megvalósításokhoz el kell kerülni. 
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

## <a id="encrypt-docdb"></a>Cosmos DB-ben tárolt bizalmas adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure Document DB-ről | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Alkalmazási szinten bizalmas adatok titkosítását a document DB való tárolás előtt, vagy bizalmas adatok tárolása egyéb tárolási megoldások, mint például az Azure Storage vagy az Azure SQL| 

## <a id="disk-vm"></a>Az Azure Disk Encryption használatával virtuális gépek által használt lemezek titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure IaaS virtuális gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure Disk Encryption segítségével a virtuális gépek által használt lemezek titkosítása](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Lépések** | <p>Az Azure Disk Encryption egy új szolgáltatás, amely jelenleg előzetes verzióban érhető el. Ez a funkció lehetővé teszi, hogy az operációsrendszer-lemez és adatlemezek az IaaS virtuális gép által használt titkosítását. A Windows a meghajtók titkosítottak, iparági szabványnak megfelelő BitLocker titkosítási technológia használatával. A Linux rendszerre a lemezek titkosítottak, a DM-Crypt technológia használatával. Ez az Azure Key Vault lehetővé teszi a vezérlőelemet és kezelheti a lemeztitkosítási kulcsok van integrálva. Az Azure Disk Encryption megoldás a következő három ügyfél titkosítási forgatókönyveket támogatja:</p><ul><li>Ügyfél-titkosítású VHD-fájlok és az ügyfél által biztosított titkosítási kulcs, amelyeket a rendszer az Azure Key Vaultban létrehozott új IaaS virtuális gépek titkosításának engedélyezése.</li><li>Az Azure Marketplace-ről létrehozott új IaaS virtuális gépek titkosításának engedélyezése.</li><li>Azure-ban már futó meglévő IaaS virtuális gépek titkosításának engedélyezése.</li></ul>| 

## <a id="fabric-apps"></a>Titkosítani a titkos kulcsokat a Service Fabric-alkalmazások

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [Service Fabric-alkalmazásokat a titkos kulcsok kezelése](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Lépések** | Titkos kódok lehet a bizalmas adatokat, például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem szövegként kezelje. Az Azure Key Vault használatával kezelheti a kulcsokat és titkos kulcsokat a service fabric-alkalmazások. |

## <a id="modeling-teams"></a>Biztonsági modellezési az üzleti egység/csapatok használható, és ahol szükséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Biztonsági modellezési az üzleti egység/csapatok használható, és ahol szükséges |

## <a id="entities"></a>Megosztott szolgáltatás kritikus entitások minimalizálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Megosztott szolgáltatás kritikus entitások minimalizálása |

## <a id="good-practices"></a>A Dynamics CRM-megosztási szolgáltatás és az ajánlott biztonsági eljárások a kockázatokat a felhasználók betanítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | A Dynamics CRM-megosztási szolgáltatás és az ajánlott biztonsági eljárások a kockázatokat a felhasználók betanítása |

## <a id="exception-mgmt"></a>Kivételek kezelése konfigurációs részleteket megjelenítő proscribing fejlesztési szabványok szabály belefoglalása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Például egy fejlesztési szabványok szabály proscribing config részletekkel kívül fejlesztési kivételek kezelése. A kódellenőrzéseknél, vagy a rendszeres ellenőrzés részeként tesztelni.|

## <a id="sse-preview"></a>Az Azure Storage Service Encryption (SSE) használata for Data at Rest (előzetes verzió)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Hivatkozások**              | [Az Azure Storage Service Encryption for Data at Rest (előzetes verzió)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Lépések** | <p>Az Azure Storage Service Encryption (SSE) inaktív adatok segít a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok biztonságos megőrzésében. Ezzel a funkcióval az Azure Storage automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. A titkosítási, visszafejtési és kulcskezelési rendszer teljes mértékben átlátható a felhasználók számára. Az SSE csak blokkblobok, lapblobok és hozzáfűző blobok vonatkozik. A más típusú adatok, beleértve a táblák, üzenetsorok és -fájlok nem lesznek titkosítva.</p><p>Titkosítás és visszafejtés munkafolyamat:</p><ul><li>Az ügyfél lehetővé teszi, hogy a tárfiók titkosításának</li><li>Ha az ügyfél új adatokat ír (PUT Blob, Blokkelraktározási, lapra HELYEZZE, stb.) a Blob storage; 256 bites AES titkosítást, a legerősebb blokktitkosító elérhető egyik minden írási titkosítása</li><li>A vásárlói adatokat (a Blob LEKÉRÉSE, stb.) hozzáférésre van szüksége, amikor adatokat a felhasználó való visszatérés előtt automatikusan visszafejtett</li><li>Titkosítás le van tiltva, ha új írási műveletek többé nem lesznek titkosítva, és a meglévő titkosított adatok titkosítva maradnak, amíg a felhasználó által átírása. Titkosítás engedélyezve van, a Blob storage írási lesznek titkosítva. Az adatok az állapota nem változik a felhasználóval, a storage-fiók titkosításának engedélyezése vagy letiltása váltása</li><li>Az összes titkosítási kulcs tárolása, titkosított és a Microsoft által felügyelt</li></ul><p>Vegye figyelembe, hogy jelenleg használt a titkosítási kulcsokat a Microsoft felügyeli. A Microsoft eredetileg a kulcsokat hoz létre, és kezelheti a kulcsokat, valamint rendszeres elforgatási biztonságos tárolására, belső Microsoft-szabályzatban meghatározottak szerint. A jövőben ügyfelekre képes kezelni a saját > titkosítási kulcs, és adja meg a migrálási útvonal a Microsoft által felügyelt kulcsokkal az ügyfél által felügyelt kulcsokat.</p>| 

## <a id="client-storage"></a>Ügyféloldali titkosítás használata a bizalmas adatok tárolásához Azure Storage-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Ügyféloldali titkosítás és az Azure Key Vault a Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [oktatóanyag: Az Azure Key Vault használatával a Microsoft Azure Storage blobok titkosítása és visszafejtése](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [adattárolás az Azure Blob Storage Azure titkosítási bővítményekkel](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Lépések** | <p>Az Azure Storage ügyféloldali kódtárat for .NET Nuget csomag támogatja a belüli titkosítását az ügyfélalkalmazások előtt feltöltése az Azure Storage, az adatok visszafejtése során az ügyfélre történő letöltéskor. A kódtár emellett támogatja az Azure Key Vault rendszerrel való integrálást a tárfiókkulcs-kezelés biztosítása érdekében. Itt látható egy rövid leírást ügyféloldali titkosítása működése:</p><ul><li>Az Azure Storage ügyféloldali SDK állít elő, a tartalom titkosítási kulcsot (CEK), amely egyszer használható egy szimmetrikus kulcs</li><li>Vásárlói adatok titkosítását a rendszer a CEK</li><li>A CEK ezt követően (titkosítva) a kulcstitkosítási kulcs-(KEK) használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspárt alkotnak, vagy egy szimmetrikus kulcsot és is kezelhetők helyileg vagy az Azure Key Vaultban tárolt. A Storage-kliens maga soha nem rendelkezik hozzáféréssel a KEK. Csak a Key Vault által biztosított kulcs alkalmazásburkoló algoritmus hív meg. Ügyfeleink kiválaszthatják kulcs alkalmazásburkoló/kicsomagolási szükség esetén egyéni szolgáltató használata</li><li>A titkosított adatok majd fel van töltve az Azure Storage szolgáltatásba. Ellenőrizze a hivatkozások alacsony szintű megvalósítási részletei szakaszban található hivatkozásokat.</li></ul>|

## <a id="pii-phones"></a>Bizalmas vagy telefonok helyi tárba írt PII adatok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Generic, Xamarin  |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Beállításainak és funkcióinak az eszközökön a Microsoft Intune-házirendek kezelése](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), [kulcslánc Pótkulcs](https://components.xamarin.com/view/square.valet) |
| **Lépések** | <p>Ha az alkalmazás írja az érzékeny információkat, például a felhasználó személyazonosításra alkalmas adatok (e-mail, telefonszám, Utónév, Vezetéknév, beállítások stb.) – mobile a fájlrendszerben, majd azt titkosítani kell a helyi fájlrendszerben írása előtt. Ha az alkalmazás egy vállalati alkalmazást, majd Fedezze fel a Windows Intune-nal közzétételi alkalmazás lehetőségét.</p>|

### <a name="example"></a>Példa
Az Intune a következő biztonsági házirendeknek a bizalmas adatok védelme érdekében konfigurálható: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Példa
Ha az alkalmazás nem egy vállalati alkalmazást, majd a megadott platform keystore, keychains tárolni a titkosítási kulcs, mely kriptográfiai művelet használatával is elvégezhető a fájlrendszerben. Következő kódrészletet bemutatja, hogyan férhet hozzá a kulcshoz a xamarin segítségével kulcslánc: 
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

## <a id="binaries-end"></a>A végfelhasználók számára terjesztése előtt létrehozott bináris rejtse

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Titkosítási rejtjelezésre utaló jeleket, a .NET-hez](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Lépések** | Létrehozott bináris fájljait (apk belül szerelvények) kell lennie rejtjelezett leállítani a visszafejtés sestavení. Eszközök, például a `CryptoObfuscator` erre a célra használhatók. |

## <a id="cert"></a>ClientCredentialType tanúsítvány vagy a Windows beállítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Erősítse meg](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Lépések** | A jelszó is figyelheti a SOAP-üzenetet, akik egy UsernameToken használatával egy egyszerű szöveges jelszó titkosítatlan csatornán keresztül tesz elérhetővé. A UsernameToken használó szolgáltatók előfordulhat, hogy fogadja el a jelszót egyszerű szövegként küldi el. Egyszerű szöveges jelszavak küldése titkosítatlan csatornán keresztül, közzéteheti a használt hitelesítő adatok, akik a SOAP-üzenetet is figyelheti. | 

### <a name="example"></a>Példa
A következő WCF szolgáltató konfigurációja a UsernameToken használja: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Tanúsítvány vagy Windows clientCredentialType beállítva. 

## <a id="security"></a>WCF-biztonsági mód nincs engedélyezve

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Megfelelő technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Biztonsági mód - átvitel, biztonsági üzemmód - üzenet |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [WCF biztonsági alapok CoDe magazin](http://www.codemag.com/article/0611051) |
| **Lépések** | Nincs átvitele vagy üzenet biztonsági intézkedések lettek megadva. Alkalmazások, amelyek üzenetek nélkül az átviteli vagy a biztonsági nem garantálja a kódintegritási vagy az üzenetek titkosságát üzenet továbbítását. Ha WCF biztonsági kötés None értékre van állítva, átviteli és üzenet biztonsági le vannak tiltva. |

### <a name="example"></a>Példa
A következő konfiguráció a biztonsági mód beállítása None értékre. 
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
Biztonsági mód között van az összes szolgáltatáskötések öt lehetséges biztonsági módja: 
* Nincs. Biztonsági kikapcsolása. 
* Átviteli. Használja az átviteli biztonsági kölcsönös hitelesítést és az üzenet védelemre. 
* az üzenet. Üzenet biztonsági kölcsönös hitelesítést és az üzenet védelmet használ. 
* Mindkettő. Adja meg az átvitel és a (csak az MSMQ támogatja ezt) üzenet-szintű biztonsági beállításait teszi lehetővé. 
* TransportWithMessageCredential. Az üzenet és az üzenet védelmi átadott hitelesítő adatokat, és a szállítási réteg által biztosított kiszolgálói hitelesítés. 
* TransportCredentialOnly. Ügyfél-hitelesítő adatok lesznek átadva a szállítási réteg a, és nem jelenik a védelem akkor lép életbe. Szállítási és üzenet biztonsági védhetők az integritásra és üzenetek titkosságát. Az alábbi konfigurációval arról tájékoztatja, hogy a szolgáltatás használatára transport security üzenet hitelesítő adataival.
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
