---
title: Kivételkezelés – Microsoft Fenyegetésmodellező Eszköz – Azure | Microsoft dokumentumok
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
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728078"
---
# <a name="security-frame-exception-management--mitigations"></a>Biztonsági keret: Kivételkezelés | Enyhítése 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- A serviceDebug csomópont nem szerepel a konfigurációs fájlban](#servicedebug)</li><li>[WCF- A szolgáltatásMetadata-csomópont nem szerepel a konfigurációs fájlban](#servicemetadata)</li></ul> |
| **Webes API** | <ul><li>[Annak ellenőrzése, hogy a webes API-ban megfelelő kivételkezelés történik-e ASP.NET](#exception)</li></ul> |
| **Webalkalmazás** | <ul><li>[A biztonsági részletek biztonsági részleteinek megjelenítése hibaüzenetekben](#messages)</li><li>[Alapértelmezett hibakezelési lap megvalósítása](#default)</li><li>[Telepítési módszer beállítása kiskereskedelemre az IIS-ben](#deployment)</li><li>[A kivételek nek biztonságosan meg kell hibásulniuk](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- A serviceDebug csomópont nem szerepel a konfigurációs fájlban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET keret rendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Lépéseket** | A Windows kommunikációs keretrendszer (WCF) szolgáltatásai beállíthatók a hibakeresési információk felfedésére. A hibakeresési információkat nem szabad éles környezetben használni. A `<serviceDebug>` címke határozza meg, hogy a hibakeresési információs szolgáltatás engedélyezve van-e egy WCF-szolgáltatáshoz. Ha az attribútum kivételrészletei true értékre van állítva, az alkalmazásból származó kivételadatok at visszaadja az ügyfeleknek. A támadók a hibakeresésből származó további információkat kihasználva csatlakoztathatják a keretrendszer, az adatbázis vagy az alkalmazás által használt egyéb erőforrások at célzó támadásokat. |

### <a name="example"></a>Példa
A következő konfigurációs `<serviceDebug>` fájl tartalmazza a címkét: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Tiltsa le a szolgáltatásban lévő hibakeresési adatokat. Ez úgy valósítható meg, `<serviceDebug>` hogy eltávolítja a címkét az alkalmazás konfigurációs fájljából. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- A szolgáltatásMetadata-csomópont nem szerepel a konfigurációs fájlban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Általános, NET keret rendszer 3 |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Lépéseket** | A szolgáltatással kapcsolatos információk nyilvános felfedése értékes betekintést nyújthat a támadóknak aszolgáltatás kihasználásának módjába. A `<serviceMetadata>` címke engedélyezi a metaadat-közzétételi szolgáltatást. A szolgáltatás metaadatai olyan bizalmas információkat tartalmazhatnak, amelyek nem lehetnek nyilvánosan hozzáférhetők. Legalább csak a megbízható felhasználók férhetnek hozzá a metaadatokhoz, és győződjön meg arról, hogy a szükségtelen információk nem érhetők el. Még jobb, teljesen tiltsa le a metaadatok közzétételének lehetőségét. A biztonságos WCF-konfiguráció `<serviceMetadata>` nem tartalmazza a címkét. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Annak ellenőrzése, hogy a webes API-ban megfelelő kivételkezelés történik-e ASP.NET

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Kivételkezelés ASP.NET webes API-ban](https://www.asp.net/web-api/overview/error-handling/exception-handling), [modellérvényesítés ASP.NET webes API-ban](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépéseket** | Alapértelmezés szerint ASP.NET webes API legtöbb nem fel nem fogott kivétele állapotkóddal rendelkező HTTP-válaszra lesz lefordítva.`500, Internal Server Error`|

### <a name="example"></a>Példa
Az API által visszaadott állapotkód szabályozásához az alábbi módon `HttpResponseException` használható: 
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
A kivételválasz további ellenőrzéséhez `HttpResponseMessage` az osztály az alábbi módon használható: 
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
A nem típusú `HttpResponseException`nem kezelt kivételek kiírásához kivételszűrők használhatók. A kivételszűrők `System.Web.Http.Filters.IExceptionFilter` megvalósítják az összeköttetést. A kivételszűrő írásának legegyszerűbb módja az `System.Web.Http.Filters.ExceptionFilterAttribute` osztályból való származtatás és az OnException metódus felülbírálása. 

### <a name="example"></a>Példa
Itt van egy szűrő, amely `NotImplementedException` átalakítja `501, Not Implemented`a kivételeket HTTP állapotkóddá: 
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

A webes API-kivételszűrő regisztrálásának több módja van:
- Művelet szerint
- Vezérlő szerint
- Globálisan

### <a name="example"></a>Példa
Ha a szűrőt egy adott műveletre szeretné alkalmazni, adja hozzá a szűrőt attribútumként a művelethez: 
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
Ha a szűrőt a szűrő `controller`összes műveletére alkalmazni szeretné, `controller` adja hozzá a szűrőt attribútumként az osztályhoz: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Példa
Ha a szűrőt globálisan szeretné alkalmazni az összes webes API-vezérlőre, adja hozzá a szűrő egy példányát a `GlobalConfiguration.Configuration.Filters` gyűjteményhez. A gyűjtemény ben lévő kivételszűrők minden webes API-vezérlő műveletre vonatkoznak. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Példa
A modell érvényesítése érdekében a modellállapot átadható a CreateErrorResponse metódusnak az alábbi módon: 
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

A hivatkozások részben további részleteket talál a webes API-ban ASP.NET webes API kivételes kezelésével és modellellenőrzésével kapcsolatos további részletekért. 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>A biztonsági részletek biztonsági részleteinek megjelenítése hibaüzenetekben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Az általános hibaüzenetek közvetlenül a felhasználónak adják meg a bizalmas alkalmazásadatok at. A bizalmas adatok közé tartoznak például a következők:</p><ul><li>Kiszolgálónevek</li><li>Kapcsolati sztringek</li><li>Felhasználónevek</li><li>Jelszavak</li><li>SQL eljárások</li><li>A dinamikus SQL-hibák részletei</li><li>Halomnyomat és kódsorok</li><li>A memóriában tárolt változók</li><li>Meghajtó- és mappahelyek</li><li>Alkalmazástelepítési pontok</li><li>Állomás konfigurációs beállításai</li><li>Egyéb belső alkalmazásrészletei</li></ul><p>Az alkalmazáson belüli összes hiba túltöltése és általános hibaüzenetek megadása, valamint az egyéni hibák engedélyezése az IIS-en belül segít megelőzni az adatok nyilvánosságra hozatalát. Az SQL Server adatbázis és a .NET kivételkezelés – többek között a hibakezelési architektúrák mellett – különösen részletesek és rendkívül hasznosak az alkalmazást profilozó rosszindulatú felhasználók számára. Ne jelenítse meg közvetlenül a .NET kivételosztályból származtatott osztály tartalmát, és győződjön meg arról, hogy megfelelő kivételkezeléssel rendelkezik, hogy egy váratlan kivétel ne merüljön fel véletlenül közvetlenül a felhasználóra.</p><ul><li>Általános hibaüzenetek közvetlenül a felhasználónak, amely elvonta a kivétel/hibaüzenetben található konkrét részleteket</li><li>Ne jelenítse meg a .NET kivételosztály tartalmát közvetlenül a felhasználónak</li><li>Az összes hibaüzenet túltöltése, és adott esetben az alkalmazásügyfélnek küldött általános hibaüzenet segítségével a felhasználó tájékoztatása</li><li>Ne tegye elérhetővé a Kivétel osztály tartalmát közvetlenül a `.ToString()`felhasználónak, különösen a visszatérési értéket, illetve az Message vagy A StackTrace tulajdonságok értékeit. Biztonságosan naplózza ezt az információt, és jelenítsen meg egy ártalmatlanabb üzenetet a felhasználónak</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Alapértelmezett hibakezelési lap megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az ASP.NET hibalapok beállításainak szerkesztése párbeszédablak](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Lépéseket** | <p>Ha egy ASP.NET alkalmazás meghibásodik, és HTTP/1.x 500 belső kiszolgálói hibát okoz, vagy egy szolgáltatáskonfiguráció (például a kérelemszűrés) megakadályozza a lap megjelenítését, hibaüzenet jelenik meg. A rendszergazdák eldönthetik, hogy az alkalmazás megjelenítse-e az ügyfélnek a baráti üzenetet, a részletes hibaüzenetet az ügyfélnek, vagy csak a localhost-nak a részletes hibaüzenetet. A `<customErrors>` web.config címkéje három módból rendelkezik:</p><ul><li>**Bekapcsolva:** Itt adható meg, hogy az egyéni hibák engedélyezve vannak-e. Ha nincs megadva alapértelmezett Redirect attribútum, a felhasználók általános hibát látnak. Az egyéni hibák a távoli ügyfelek és a helyi állomás számára jelennek meg</li><li>**Ki:** Itt adható meg, hogy az egyéni hibák le vannak tiltva. A részletes ASP.NET hibák a távoli ügyfelek és a helyi állomás számára jelennek meg</li><li>**RemoteOnly:** Itt adhatja meg, hogy az egyéni hibák csak a távoli ügyfelek számára jelenjenek meg, és hogy ASP.NET hibák jelenjenek meg a helyi állomásszámára. Ez az alapértelmezett érték</li></ul><p>Nyissa `web.config` meg az alkalmazás/hely fájlját, és `<customErrors mode="RemoteOnly" />` `<customErrors mode="On" />` győződjön meg arról, hogy a címke rendelkezik-e vagy van-e definiálva.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Telepítési módszer beállítása kiskereskedelemre az IIS-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [telepítési elem (ASP.NET Beállítások sémája)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Lépéseket** | <p>A `<deployment retail>` kapcsolót éles IIS-kiszolgálók használhatják. Ezzel a kapcsolóval segíti az alkalmazásokat a lehető legjobb teljesítménnyel és a lehető legkisebb biztonsági információszivárgással való futtatásban azáltal, hogy letiltja az alkalmazás azon képességét, hogy nyomkövetési kimenetet generáljon egy oldalon, letiltva a részletes hibaüzenetek megjelenítésének lehetőségét és a hibakeresési kapcsoló letiltását.</p><p>Az aktív fejlesztés során gyakran engedélyezve vannak a fejlesztőkre összpontosító kapcsolók és beállítások, például a sikertelen kérelmek nyomon követése és hibakeresése. Ajánlott, hogy a központi telepítési módszer minden éles kiszolgálón legyen kiskereskedelmi. nyissa meg a machine.config `<deployment retail="true" />` fájlt, és győződjön meg arról, hogy az igaz értékre van állítva.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>A kivételek nek biztonságosan meg kell hibásulniuk

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Sikertelen hiba biztonságosan](https://www.owasp.org/index.php/Fail_securely) |
| **Lépéseket** | Az alkalmazás nak biztonságosan meg kell hiúsulnia. Minden olyan módszernek, amely logikai értéket ad vissza, amely alapján bizonyos döntés történik, gondosan létre kell hoznia a kivételblokkot. Sok logikai hiba van, amelyek miatt a biztonsági problémák bekúsznak, amikor a kivételblokk gondatlanul van megírva.|

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
A fenti módszer mindig igaz értéket ad vissza, ha valamilyen kivétel történik. Ha a végfelhasználó olyan hibás URL-t ad meg, amelyet a böngésző tiszteletben tart, de a `Uri()` konstruktor nem, ez kivételt jelent, és az áldozat az érvényes, de hibásurl-re kerül. 
