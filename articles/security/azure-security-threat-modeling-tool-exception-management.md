---
title: Kivétel kezelése – a Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
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
ms.openlocfilehash: ce748be7f11d440e656e4af5cdd3cee3bbc9e313
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302149"
---
# <a name="security-frame-exception-management--mitigations"></a>Biztonsági keret: Kivételek kezelése |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - ne tartalmazza serviceDebug csomópont konfigurációs fájlban](#servicedebug)</li><li>[WCF - ne tartalmazza serviceMetadata csomópont konfigurációs fájlban](#servicemetadata)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a megfelelő kivételkezelés történik-e az ASP.NET webes API-ban ](#exception)</li></ul> |
| **Webalkalmazás** | <ul><li>[Nem teszik elérhetővé a hibaüzeneteket biztonsági részletei ](#messages)</li><li>[Alapértelmezett hibakezelési oldal implementálása ](#default)</li><li>[Állítsa be az üzembe helyezési módszer a kereskedelmi az IIS-ben](#deployment)</li><li>[Kivételek biztonságosan kell-e sikertelen](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - ne tartalmazza serviceDebug csomópont konfigurációs fájlban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, 3. NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Lépések** | Hibakeresési információ elérhetővé a Windows Communication keretrendszer (WCF) services konfigurálható legyen. Hibakeresési információ nem használható éles környezetben. A `<serviceDebug>` címke határozza meg, hogy engedélyezve van-e a hibakeresési információkat a szolgáltatás egy WCF-szolgáltatás. Ha az attribútum includeExceptionDetailInFaults értéke igaz, kivétel adatai az alkalmazásból az ügyfeleknek visszaad. A támadók kihasználhatja a további információk a célzott támadások csatlakoztatni a keretrendszer, adatbázis vagy más erőforrások, az alkalmazás által használt kimeneti hibakeresés kapnak. |

### <a name="example"></a>Példa
A következő konfigurációs fájl tartalmazza a `<serviceDebug>` címkét: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Tiltsa le a hibakeresési információkat a szolgáltatásban. A eltávolításával lehet elvégezni a `<serviceDebug>` tag az alkalmazás konfigurációs fájlból. 

## <a id="servicemetadata"></a>WCF - ne tartalmazza serviceMetadata csomópont konfigurációs fájlban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Általános, 3. NET-keretrendszer |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Lépések** | Nyilvánosan is közzéteheti a szolgáltatással kapcsolatos információkat is biztosíthat a támadók hogyan, előfordulhat, hogy ki a szolgáltatás értékes betekintést. A `<serviceMetadata>` címke lehetővé teszi, hogy a metaadat-közzétételi szolgáltatás. Szolgáltatás metaadatai tartalmazhatnak bizalmas adatokat, amelyeket nem lehet nyilvánosan elérhető-e. Minimális csak a megbízható felhasználóknak engedélyezze a metaadatok eléréséhez, és győződjön meg arról, hogy a szükségtelen adatokat nem lesz közzétéve. Még jobb teljes mértékben tiltsa le a metaadatokat közzétételének lehetősége. Biztonságos WCF-konfiguráció nem tartalmaz a `<serviceMetadata>` címke. |

## <a id="exception"></a>Győződjön meg arról, hogy a megfelelő kivételkezelés történik-e az ASP.NET webes API-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC 5, 6 MVC |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ASP.NET webes API-k kivételkezelés](http://www.asp.net/web-api/overview/error-handling/exception-handling), [modell érvényesítése az ASP.NET webes API-k](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépések** | Alapértelmezés szerint az ASP.NET webes API-t a legtöbb nem kezelt kivételek fordítja HTTP-választ, állapotkód: `500, Internal Server Error`|

### <a name="example"></a>Példa
Az API által visszaadott állapotkódot szabályozásához `HttpResponseException` alább látható módon használható: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Példa
A kivétel választ, további vezérlőelem a `HttpResponseMessage` osztály használható a lent látható módon: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
A nem kezelt kivételeket, amelyek nem típusú `HttpResponseException`, kivétel szűrőket is használható. Kivétel szűrők megvalósítása a `System.Web.Http.Filters.IExceptionFilter` felületet. Írási egy kivétel szűrő legegyszerűbb módja az, hogy célosztályából származik a `System.Web.Http.Filters.ExceptionFilterAttribute` osztályt, és a OnException metódusnak. 

### <a name="example"></a>Példa
Itt van egy szűrő, amely átalakítja `NotImplementedException` be HTTP-állapotkód: kivételek `501, Not Implemented`: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Többféleképpen is lehet regisztrálni egy webes API-kivétel szűrő:
- Művelet
- Vezérlő
- globálisan

### <a name="example"></a>Példa
A szűrő alkalmazása egy bizonyos művelet, adja hozzá a szűrő attribútumaként a műveletre: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Példa
A szűrő alkalmazásához az összes művelet a egy `controller`, a szűrő hozzáadása egy attribútumot a `controller` osztály: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Példa
Globálisan vonatkoznak az összes webes API-vezérlő a szűrőt, adja hozzá a szűrőt, hogy egy példányát a `GlobalConfiguration.Configuration.Filters` gyűjtemény. Ebben a gyűjteményben lévő kivétel szűrők minden webes API-vezérlő művelet vonatkoznak. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Példa
A modell érvényesítése a modell állapot CreateErrorResponse metódus alább látható módon adható át: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Ellenőrizze a kivételes kezelésének további részleteiért hivatkozások szakaszban található hivatkozásokat és a modell érvényesítése az ASP.Net Web API 

## <a id="messages"></a>Nem teszik elérhetővé a hibaüzeneteket biztonsági részletei

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Általános hibaüzenetek közvetlenül a felhasználó számára biztosított alkalmazás bizalmas adatok használata nélkül. Bizalmas adatok közé:</p><ul><li>Kiszolgáló neve</li><li>Kapcsolati sztringek</li><li>Felhasználónevek</li><li>Jelszavak</li><li>SQL-eljárások</li><li>A dynamic SQL-hibák részletei</li><li>Híváslánc és sornyi kódot</li><li>Memóriában tárolt változók</li><li>Meghajtó és mappa helye</li><li>Alkalmazás telepítési pontok</li><li>Gazdagép konfigurációs beállításai</li><li>Más belső alkalmazás részletei</li></ul><p>Túltöltés hibákat az alkalmazáson belül és nyújtó általános hibaüzenetek, valamint az IIS belül egyéni hibák engedélyezése segít információ felfedésének megakadályozására. SQL Server-adatbázis és a .NET kivételkezelő, más hibakezelési architektúrák, többek között a következők különösen a részletes és rendkívül hasznos, ha egy rosszindulatú felhasználó profilkészítés az alkalmazást szeretné. Közvetlenül nem jeleníti meg a .NET-kivétel osztályból származtatott osztály tartalmát, és gondoskodjon arról, hogy megfelelő kivételkezelést, hogy nem véletlenül következik be közvetlenül a felhasználó számára a váratlan kivétel történt.</p><ul><li>Adja meg az általános hibaüzenetek közvetlenül, a felhasználót, hogy közvetlenül a kivétel/hibaüzenet található távolléti részletes absztrakt</li><li>Ne jelenjen meg a tartalmát egy .NET kivételosztály közvetlenül a felhasználó számára</li><li>Az összes hibaüzenet trap, és ha megfelelő tájékoztatja a felhasználót, egy általános hibaüzenetet kap meg az alkalmazás ügyfél-n keresztül</li><li>Nem teszik elérhetővé a kivételosztály tartalmát közvetlenül a felhasználó, különösen a visszatérési érték a `.ToString()`, vagy az üzenet vagy StackTrace tulajdonságának értékét. Biztonságosan jelentkezzen ezeket az információkat, és több ártalmatlan üzenet megjelenítése a felhasználónak</li></ul>|

## <a id="default"></a>Alapértelmezett hibakezelési oldal implementálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az ASP.NET lapok beállításainak szerkesztése párbeszédpanel](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Lépések** | <p>ASP.NET-alkalmazás meghiúsul, és a egy HTTP/1.x 500 belső kiszolgálóhiba miatt, vagy a szolgáltatás konfigurációs (például a kérelmek szűrése) esetén egy lap nem jelenik meg, amikor egy hibaüzenetet fog készülni. A rendszergazdák választhatják-e az alkalmazás az ügyfél, az ügyfél részletes hibaüzenet vagy csak a helyi állomáshoz részletes hibaüzenet kell megjelenítenie egy barátságos üzenet. A <customErrors> Web.config címke rendelkezik három mód:</p><ul><li>**A:** Megadja, hogy engedélyezve vannak-e az egyéni hibák. Ha nem defaultRedirect attribútum van megadva, a felhasználók látják egy általános hiba. Az egyéni hibák jelennek meg a távoli ügyfelek és a helyi gazdagép számára</li><li>**OFF:** Megadja, hogy a egyéni hibák le vannak tiltva. A távoli ügyfelek és a helyi gazdagép számára jelennek meg a részletes ASP.NET hibák</li><li>**RemoteOnly:** Megadja, hogy az egyéni hibák jelennek-e a távoli ügyfelekre mutató csak és, hogy az ASP.NET hibák jelennek meg a helyi gazdagép számára. Ez az az alapértelmezett érték</li></ul><p>Nyissa meg a `web.config` alkalmazás vagy webhely fájlt, és ügyeljen arra, hogy a címke vagy `<customErrors mode="RemoteOnly" />` vagy `<customErrors mode="On" />` definiálva.</p>|

## <a id="deployment"></a>Állítsa be az üzembe helyezési módszer a kereskedelmi az IIS-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [üzembe helyezés (schéma nastavení Technologie) elem](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Lépések** | <p>A `<deployment retail>` kapcsoló rendszer általi használatra éles IIS-kiszolgálókkal. Ezt a kapcsolót használja, a lehető legjobb teljesítményt és a lehető legkevesebb biztonsági információk szivárgás fussanak, tiltsa le az alkalmazás képes létrehozni a nyomkövetési egy oldalon, a részletes hibaüzenetek megjelenítésének lehetőségét letiltása a végfelhasználók és a hibakeresési kapcsoló letiltása.</p><p>Gyakran, a kapcsolók és a beállítások, amelyek a fejlesztőközpontú, mint például a sikertelen kérelmek nyomkövetési és a hibakeresés, aktív fejlesztés során. Javasoljuk, hogy a központi telepítési módszer bármelyik üzemi kiszolgálón kiskereskedelmi állítani. Nyissa meg a machine.config fájlban, és ellenőrizze, hogy `<deployment retail="true" />` igaz értékre állítva marad.</p>|

## <a id="fail"></a>Kivételek biztonságosan kell-e sikertelen

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Biztonságosan sikertelen](https://www.owasp.org/index.php/Fail_securely) |
| **Lépések** | Alkalmazás biztonságosan sikertelen lesz. Bármelyik módszert, amely egy logikai értéket, mely bizonyos döntés, alapján ad vissza kell alaposan létrehozott kivétel letiltása. Nincsenek logikai hibák miatt, amely a biztonsági kérdések kötés az, ha a kivétel blokk carelessly írt rengeteg.|

### <a name="example"></a>Példa
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
A fenti módszerrel mindig ad vissza IGAZ, ha kivétel történik. Ha a végfelhasználó számára biztosít egy helytelenül formázott URL-címe, a böngésző figyelembe veszi, de a `Uri()` konstruktor nem, ez kivételt ad és az áldozat megnyílik az érvényes, de helytelen URL-cím. 