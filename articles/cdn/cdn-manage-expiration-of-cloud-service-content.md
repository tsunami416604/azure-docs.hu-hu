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
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Azure tartalom Delivery Network webes tartalom lejáratának kezelése
 az Azure CDN
> [!div class="op_single_selector"]
> * [Az Azure Web Apps/Felhőszolgáltatások, ASP.NET, vagy az IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Fájlok forráskiszolgálóról bármely nyilvánosan elérhető webkiszolgáló gyorsítótárazhatók az Azure Content Delivery Network (CDN) csak az idő-live (TTL) eltelt. Az élettartam határozza meg a [ `Cache-Control` fejléc](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) az a HTTP-válasz a forráskiszolgálóról. Ez a cikk ismerteti, hogyan telepíthet `Cache-Control` a Web Apps szolgáltatása a Microsoft Azure App Service, Azure Cloud Services, az ASP.NET-alkalmazások és az Internet Information Services helyeket, amelyek hasonló módon konfigurált fejlécek.

> [!TIP]
> Ha szeretné, nincs TTL be egy fájl. Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap.
> 
> Fájlok és egyéb erőforrásokhoz való hozzáférés felgyorsítása érdekében Azure CDN szolgáltatás működésével kapcsolatos további információkért lásd: [az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>A konfigurációs fájlok Cache-Control fejlécek beállítása
Statikus tartalom, lemezképek és a stíluslapok, például szabályozhatja a frissítés gyakoriságának módosítása a **applicationHost.config** vagy **web.config** a webes alkalmazás forrásfájljairól. A **system.webServer\staticContent\clientCache** a konfigurációs fájl készlet eleme a `Cache-Control` fejlécet a tartalomhoz. A **web.config** fájlok, a konfigurációs beállítások hatással mindent a mappában és annak almappáiban, kivéve, ha azok felülbírálják a almappa szinten. Például alapértelmezett TTL-t beállítás megadása a gyökérmappában három teljes napig az összes statikus tartalom gyorsítótárazása, és csak hat órán át gyorsítótárazza a benne lévő tartalom több változó tartalmú almappa beállítása. Bár a **applicationHost.config** beállításai befolyásolják a hely összes alkalmazás, azok minden meglévő beállításai felülbírálják **web.config** fájlok az alkalmazásokban.

A következő XML-példa bemutatja, hogyan telepíthet **clientCache** három nap maximális életkora adhatja meg:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Adja meg **UseMaxAge** hatására a `Cache-Control: max-age=<nnn>` fel kell venni a válasz fejléce alapján meghatározott érték a **CacheControlMaxAge** attribútum. A timespan formátumát a **cacheControlMaxAge** attribútum `<days>.<hours>:<min>:<sec>`. További információ a **clientCache** csomópont, lásd: [Ügyfélgyorsítótár <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>A beállítás a Cache-Control fejlécek kódban
Az ASP.NET-alkalmazások, a CDN-t úgy, hogy programozott módon gyorsítótárazásának szabályozhatja a **HttpResponse.Cache** tulajdonság. További információ a **HttpResponse.Cache** tulajdonság, lásd: [HttpResponse.Cache tulajdonság](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) és [HttpCachePolicy osztály](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Programozott módon tartalmak gyorsítótárazására való alkalmazása az ASP.NET kövesse az alábbi lépéseket:
   1. Győződjön meg arról, hogy a tartalmat úgy, hogy a gyorsítótárazható megjelölve `HttpCacheability` való *nyilvános*. 
   2. A gyorsítótár érvényesítő állította hívja a következő módszerek egyikét:
      - Hívás `SetLastModified` LastModified időbélyeg beállításához.
      - Hívás `SetETag` beállítani egy `ETag` érték.
   3. Szükség esetén adja meg a gyorsítótár lejárati ideje meghívásával `SetExpires`. Ellenkező esetben a korábban a jelen dokumentumban ismertetett alapértelmezett gyorsítótár heurisztikus alkalmazni.

A gyorsítótár egy órára tartalom, például vegye fel a következő C#-kódban:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Következő lépések
* [Részletekért olvassa el a **clientCache** elem](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Olvassa el a dokumentációt a **HttpResponse.Cache** tulajdonság](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Olvassa el a dokumentációt a **HttpCachePolicy osztály**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

