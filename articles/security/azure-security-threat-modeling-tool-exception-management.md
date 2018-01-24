---
title: "Kivétel kezelése – Microsoft fenyegetés modellezési eszköz - Azure |} Microsoft Docs"
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
ms.openlocfilehash: 9a8e0154faccca356c7fb8ce93e43ce67cc0aae2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="security-frame-exception-management--mitigations"></a>Biztonsági keret: Kivételek kezelése |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - ne szerepeljen az serviceDebug csomópont a konfigurációs fájl](#servicedebug)</li><li>[WCF - ne szerepeljen az serviceMetadata csomópont a konfigurációs fájl](#servicemetadata)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a megfelelő kivételkezelést történik-e az ASP.NET Web API](#exception)</li></ul> |
| **Webalkalmazás** | <ul><li>[Nem teszi közzé a látható a hibaüzenetekben biztonsági részletei](#messages)</li><li>[Alapértelmezett hibakezelési lap végrehajtása](#default)</li><li>[Az üzembe helyezési módszer beállítása az IIS-ben kereskedelmi](#deployment)</li><li>[Kivételek biztonságosan kell-e sikertelen](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - ne szerepeljen az serviceDebug csomópont a konfigurációs fájl

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | A Windows kommunikációs keretrendszer (WCF) szolgáltatásait teszi közzé a hibakeresési adatok konfigurálható. Hibakeresési információ nem használható üzemi környezetben. A `<serviceDebug>` címke határozza meg, hogy a hibakeresési információ szolgáltatás engedélyezve van-e a WCF-szolgáltatás számára. Ha az attribútum includeExceptionDetailInFaults az alkalmazásból értéke true, kivételek adatai visszatér az ügyfelek számára. A támadók kihasználhatják a további információt a hibakeresés csatlakoztatni a célzott támadások keretében, adatbázis vagy más erőforrások, az alkalmazás által használt kimeneti kapnak. |

### <a name="example"></a>Példa
A következő konfigurációs fájl tartalmazza a `<serviceDebug>` címke: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Tiltsa le a szolgáltatást a hibakeresési adatok. Eltávolításával ehhez a `<serviceDebug>` címke az alkalmazás konfigurációs fájljából. 

## <a id="servicemetadata"></a>WCF - ne szerepeljen az serviceMetadata csomópont a konfigurációs fájl

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Általános, NET-keretrendszer 3 |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | Nyilvánosan kitettségének szolgáltatás információt biztosíthat a támadók értékes betekintést nyerhet hogyan azok használhatja ki a szolgáltatás. A `<serviceMetadata>` címke engedélyezi a metaadatok közzétételi szolgáltatást. Szolgáltatás metaadatai tartalmazhatnak bizalmas adatokat, amelyeket nem kell nyilvánosan elérhető. Minimális csak a megbízható felhasználóknak engedélyezze a metaadatok eléréséhez, és győződjön meg arról, hogy a szükségtelen adatokat nem lesz közzétéve. Még jobb teljes mértékben tiltsa le a metaadatokat közzétételére képes. A biztonságos WCF-konfiguráció nem tartalmaz a `<serviceMetadata>` címke. |

## <a id="exception"></a>Győződjön meg arról, hogy a megfelelő kivételkezelést történik-e az ASP.NET Web API

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ASP.NET webes API-t a kivételkezelő](http://www.asp.net/web-api/overview/error-handling/exception-handling), [-ellenőrzés az ASP.NET Web API minta](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépések** | Alapértelmezés szerint az ASP.NET Web API leginkább a nem kezelt kivételek lefordítását egy HTTP-válasz állapotkód:`500, Internal Server Error`|

### <a name="example"></a>Példa
Az API által visszaadott állapotkód vezérlésére `HttpResponseException` alább látható módon használhatja: 
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
A kivétel válasz további vezérlő a `HttpResponseMessage` osztály használható a lent látható módon: 
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
A nem kezelt kivételeket, amelyek nincsenek típusú `HttpResponseException`, kivétel szűrők segítségével. Kivétel szűrők valósítja meg a `System.Web.Http.Filters.IExceptionFilter` felületet. A legegyszerűbben egy kivételszűrőbe írása után kapcsolattípusokból származhatnak, amelyek a `System.Web.Http.Filters.ExceptionFilterAttribute` osztályhoz, és írja felül az OnException metódust. 

### <a name="example"></a>Példa
Íme egy szűrő, amely átalakítja `NotImplementedException` kivételek be HTTP-állapotkód `501, Not Implemented`: 
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

Többféleképpen is lehet regisztrálni a Web API kivételszűrőbe:
- Művelet
- Vezérlő
- Globálisan

### <a name="example"></a>Példa
A szűrő alkalmazása egy bizonyos művelet, a szűrő hozzáadása attribútumként műveletet: 
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
A szűrni az összes művelet a egy `controller`, vegye fel a szűrő attribútumot a `controller` osztály: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Példa
Globálisan vonatkoznak az összes webes API-vezérlő a szűrőt, vegye fel a szűrő egy példányát a `GlobalConfiguration.Configuration.Filters` gyűjtemény. A gyűjtemény kivételszűrők bármely webes API-vezérlő művelet vonatkozik. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Példa
A modell ellenőrzése, a modell állapota is lehet CreateErrorResponse metódusnak átadott alább látható módon: 
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

Ellenőrizze a hivatkozásokra a references szakaszában, különleges kezelést kapcsolatos további részletekért és a modell ellenőrzése ASP.Net Web API-ban. 

## <a id="messages"></a>Nem teszi közzé a látható a hibaüzenetekben biztonsági részletei

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Általános hibaüzenetek közvetlenül a felhasználó számára biztosított nélkül az alkalmazás bizalmas adatokat is beleértve. Bizalmas adatok közé tartoznak például:</p><ul><li>Kiszolgáló neve</li><li>Kapcsolati karakterláncok</li><li>Felhasználónevek</li><li>Jelszavak</li><li>SQL-eljárások</li><li>A dinamikus SQL-hibák részletei</li><li>A veremkivonatot és sornyi kód</li><li>A memóriában tárolt változók</li><li>Meghajtó és mappa</li><li>Alkalmazás telepítése pontok</li><li>Gazdagép konfigurációs beállításai</li><li>Egyéb részleteket a belső alkalmazás</li></ul><p>Az alkalmazáson belül az összes hiba túltöltés és nyújtó általános hibaüzenetek, valamint IIS belül az egyéni hibák engedélyezése segítségével információ felfedésének megakadályozására. SQL Server-adatbázis és a .NET kivételkezelő, más hibakezelési architektúrák, között is különösen részletes és rendkívül hasznos, ha egy rosszindulatú felhasználó, az alkalmazás profilkészítési szeretné. Közvetlenül nem jeleníti meg a .NET-kivételt osztályból származtatott osztályt tartalmát, és győződjön meg arról, hogy megfelelő kivételkezelést, hogy egy váratlan kivétel véletlenül kiváltott nem közvetlenül a felhasználók számára.</p><ul><li>Általános hiba üzenetek közvetlenül a felhasználót, hogy közvetlenül a kivétel/hibaüzenet található kötelező részletes absztrakt biztosítása</li><li>Ne jelenjen meg a .NET kivételosztály tartalmát közvetlenül a felhasználók számára</li><li>Feldolgozzák-e az összes hibaüzenet, és adott esetben tájékoztatja a felhasználót, egy általános hibaüzenetet kap meg az alkalmazás ügyfél keresztül</li><li>Nem tegye elérhetővé a kivételosztály tartalmát közvetlenül a felhasználó, különösen a visszatérési érték a `.ToString()`, vagy az üzenet vagy Veremkivonat tulajdonságok értékeit. Biztonságosan jelentkezzen ezt az információt, és a felhasználó több ártalmatlan üzenetben</li></ul>|

## <a id="default"></a>Alapértelmezett hibakezelési lap végrehajtása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az ASP.NET lapok beállításainak szerkesztése párbeszédpanel](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Lépések** | <p>Az ASP.NET-alkalmazások sikertelen lesz, és HTTP/1.x 500 belső kiszolgáló hibát okoz, vagy egy kapcsolószolgáltatás-konfigurációja (például kérelmek szűrése) esetén a lap nem jelenik meg, amikor egy hibaüzenetet fog készülni. Rendszergazdák kiválaszthatják, függetlenül attól, az alkalmazás megjelenjen-e egy rövid üzenetet az ügyfél, részletes hibaüzenet az ügyfél vagy helyi állomáshoz csak részletes hibaüzenet. A <customErrors> címke a Web.config szolgáltatásnak három módja van:</p><ul><li>**A:** határozza meg, hogy engedélyezve vannak-e az egyéni hibák. Ha nem defaultRedirect attribútum meg van adva, a felhasználók látni egy általános hiba. Az egyéni hibák jelennek meg a távoli ügyfelek és a helyi gazdagép</li><li>**OFF:** határozza meg, hogy van-e tiltva az egyéni hibák. A részletes ASP.NET hibák jelennek meg a távoli ügyfelek és a helyi gazdagép</li><li>**RemoteOnly:** határozza meg, hogy az egyéni hibák jelennek meg csak a távoli ügyfelekre mutató, és, hogy az ASP.NET-hibákat a helyi gazdagép számára jelennek meg. Ez az az alapértelmezett érték</li></ul><p>Nyissa meg a `web.config` az alkalmazás vagy a hely fájlt, és ügyeljen arra, hogy a címke vagy `<customErrors mode="RemoteOnly" />` vagy `<customErrors mode="On" />` definiálva.</p>|

## <a id="deployment"></a>Az üzembe helyezési módszer beállítása az IIS-ben kereskedelmi

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [központi telepítés elem (ASP.NET beállítási séma)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Lépések** | <p>A `<deployment retail>` kapcsoló éles IIS-kiszolgálókkal való használatra készült. A kapcsolót az alkalmazás képes létrehozni a nyomkövetési kimeneti oldalon letiltásával, letiltása, végfelhasználók számára a részletes hibaüzenetek megjelenítése lehetőséget, és a hibakeresési kapcsoló letiltása a lehető legjobb teljesítményt és a lehető legkisebb biztonsági információk szivárgást alkalmazás segítségével.</p><p>Gyakran, a kapcsolók és a kapcsolókról developer-arra irányul, például a sikertelen kérelmek nyomkövetésére vonatkozó és a hibakeresést, aktív fejlesztése során. Javasoljuk, hogy az üzembe helyezési módszer bármely az üzemi kiszolgáló frissítését a kereskedelmi állítható be. Nyissa meg a machine.config fájl, és győződjön meg arról, hogy `<deployment retail="true" />` továbbra is igaz értékre állítva.</p>|

## <a id="fail"></a>Kivételek biztonságosan kell-e sikertelen

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Biztonságos helyen sikertelen](https://www.owasp.org/index.php/Fail_securely) |
| **Lépések** | Alkalmazás biztonságosan sikertelen lesz. Bármelyik módszert, amely visszaadja az egy logikai érték, mely bizonyos döntés, alapján gondosan létrehozott kivételblokk kell rendelkeznie. Nincsenek nagy mennyiségű logikai hibák miatt, amely a biztonsági kérdések a, ha a kivételkezelő blokk carelessly ír a kötés.|

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
A fenti módszer mindig ad vissza IGAZ, ha kivétel történik. Ha a végfelhasználó biztosít egy nem megfelelően formázott URL-cím, a böngésző tiszteletben tartja, de a `Uri()` konstruktor nem, a rendszer kivételt jelez, és az áldozat megnyílik az érvényes, de nem megfelelően formázott URL-címet. 