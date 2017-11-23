---
title: "Azure tartalom Delivery Network webes tartalom lejáratának kezelése |} Microsoft Docs"
description: "Útmutató az Azure CDN Azure Web Apps/Cloud Services, az ASP.NET, vagy az IIS tartalom lejáratának kezelése."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: fe519c3ad5f99899277bf005929142c52a4c4724
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Azure tartalom Delivery Network webes tartalom lejáratának kezelése
> [!div class="op_single_selector"]
> * [Az Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

A fájlok forráskiszolgálóról bármely nyilvánosan elérhető webkiszolgáló gyorsítótárazhatók az Azure Content Delivery Network (CDN) csak az idő-live (TTL) lejárta. Az élettartam határozza meg a `Cache-Control` fejléc a következő a HTTP-válasz a forráskiszolgálóról. Ez a cikk ismerteti, hogyan telepíthet `Cache-Control` a Web Apps szolgáltatása a Microsoft Azure App Service, Azure Cloud Services, az ASP.NET-alkalmazások és az Internet Information Services (IIS) helyeket, amelyek hasonló módon konfigurált fejlécek. Beállíthatja a `Cache-Control` fejléc vagy a konfigurációs fájlok vagy programon keresztül.

> [!TIP]
> Ha szeretné, nincs TTL be egy fájl. Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap. Ez az alapértelmezett élettartam csak azokra az általános webes kézbesítési optimalizálás. A nagy méretű fájlok optimalizálást az alapértelmezett élettartam egy nap, az pedig médiaadatfolyam-továbbítást optimalizálást, az alapértelmezett élettartam egy év.
> 
> Fájlok és egyéb erőforrásokhoz való hozzáférés felgyorsítása érdekében Azure CDN szolgáltatás működésével kapcsolatos további információkért lásd: [az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>A konfigurációs fájlok Cache-Control fejlécek beállítása
Statikus tartalom, lemezképek és a stíluslapok, például szabályozhatja a frissítés gyakoriságának módosítása a **applicationHost.config** vagy **Web.config** a webalkalmazás konfigurációs fájlok. A `<system.webServer>/<staticContent>/<clientCache>` vagy fájlkészleteket eleme a `Cache-Control` fejlécet a tartalomhoz.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config fájlok használata
A **ApplicationHost.config** a legfelső szintű fájl az IIS-konfigurációs rendszer. A konfigurációs beállításai a egy **ApplicationHost.config** fájl hatással a hely összes alkalmazást, de bármely beállítások felülbírálják **Web.config** webalkalmazás található fájlokat.

### <a name="using-webconfig-files"></a>A Web.config fájlok segítségével
Az egy **Web.config** fájl, testreszabhatja a módját a teljes webalkalmazás vagy egy adott könyvtár a webes alkalmazás viselkedik. Általában kell legalább egy **Web.config** fájl a webalkalmazás gyökérmappájában. Az egyes **Web.config** fájl egy adott mappában található a konfigurációs beállítások hatással mindent mappában és annak almappáiban, kivéve, ha azok felülbírálják a almappa szinten egy másik **Web.config**fájlt. Beállíthatja például egy `<clientCache>` eleme egy **Web.config** fájl a gyökérmappában található azon a webes alkalmazás a webes alkalmazás három teljes napig az összes statikus tartalom gyorsítótárazása. Azt is megteheti egy **Web.config** fájlt egy almappában több változó tartalmú (például `\frequent`), és a `<clientCache>` elemben, amely a almappa tartalom gyorsítótárazása hat órán keresztül. A nettó eredménye, hogy a teljes tartalom webhely három nap, kivéve a tartalom gyorsítótárba fognak kerülni a `\frequent` könyvtár, amely csak hat órán át a rendszer gyorsítótárba fognak kerülni.  

A következő XML-példa bemutatja, hogyan telepíthet a `<clientCache>` elem három nap maximális életkora adhatja meg a konfigurációs fájlban:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Használatához a **cacheControlMaxAge** attribútum, meg kell adni az értékét a **cacheControlMode** attribútumot `UseMaxAge`. Ezt a beállítást, hogy a HTTP-fejléc és a direktíva, `Cache-Control: max-age=<nnn>`, fel kell venni a választ. A timespan érték, a formátuma a **cacheControlMaxAge** attribútum `<days>.<hours>:<min>:<sec>`. Az értékre alakítja át másodperc pedig a rendszer értékének a `Cache-Control` `max-age` direktívát. További információ a `<clientCache>` elem, lásd: [Ügyfélgyorsítótár <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>A Cache-Control fejlécek programozott módon beállítása
Az ASP.NET-alkalmazások, a CDN programozott módon gyorsítótárazásának úgy, hogy szabályozza a **HttpResponse.Cache** tulajdonság a .NET API. További információ a **HttpResponse.Cache** tulajdonság, lásd: [HttpResponse.Cache tulajdonság](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) és [HttpCachePolicy osztály](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Programozott módon tartalmak gyorsítótárazására való alkalmazása az ASP.NET kövesse az alábbi lépéseket:
   1. Győződjön meg arról, hogy a tartalmat úgy, hogy a gyorsítótárazható megjelölve `HttpCacheability` való `Public`. 
   2. A gyorsítótár érvényesítő beállítása a következő függvény meghívásával `HttpCachePolicy` módszerek:
      - Hívás `SetLastModified` egy timestamp értéket, az a `Last-Modified` fejléc.
      - Hívás `SetETag` adhat meg az értéket a `ETag` fejléc.
   3. Szükség esetén adja meg a gyorsítótár lejárati ideje meghívásával `SetExpires` adhat meg az értéket a `Expires` fejléc. Ellenkező esetben a korábban a jelen dokumentumban ismertetett alapértelmezett gyorsítótár heurisztikus alkalmazni.

A gyorsítótár egy órára tartalom, például vegye fel a következő C#-kódban:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>A Cache-Control fejléc tesztelése
A TTL-beállítások a webes tartalom könnyen ellenőrizheti. A böngésző [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), tesztelje, hogy a webes tartalom magában foglalja a `Cache-Control` válaszfejlécet. Például egy eszköz is használható **wget**, [Postman](https://www.getpostman.com/), vagy [Fiddler](http://www.telerik.com/fiddler) megvizsgálhatja a response fejlécekkel együtt.

## <a name="next-steps"></a>Következő lépések
* [Részletekért olvassa el a **clientCache** elem](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Olvassa el a dokumentációt a **HttpResponse.Cache** tulajdonság](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Olvassa el a dokumentációt a **HttpCachePolicy osztály**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

