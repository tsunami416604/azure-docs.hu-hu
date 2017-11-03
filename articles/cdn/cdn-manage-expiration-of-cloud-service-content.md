---
title: "Az Azure CDN webes tartalom lejáratának kezelése |} Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Az Azure CDN Azure Web Apps/Cloud Services, az ASP.NET, vagy az IIS tartalom lejáratának kezelése
> [!div class="op_single_selector"]
> * [Az Azure Web Apps/Felhőszolgáltatások, ASP.NET, vagy az IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Storage-blob szolgáltatás](cdn-manage-expiration-of-blob-content.md)
> 
> 

Fájlok forráskiszolgálóról bármely nyilvánosan elérhető webkiszolgáló Azure CDN mindaddig, amíg az idő-live (TTL) lejárta gyorsítótárazható.  Az élettartam határozza meg a [ *Cache-Control* fejléc](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) az a HTTP-válasz a forráskiszolgálóról.  Ez a cikk ismerteti, hogyan telepíthet `Cache-Control` fejlécek Azure Web Apps, Azure Cloud Services, az ASP.NET-alkalmazások és az Internet Information Services helyeket, amelyek hasonló módon konfigurált.

> [!TIP]
> Választhatja a fájl nincs TTL beállítása.  Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap.
> 
> Fájlok és egyéb erőforrásokhoz való hozzáférés felgyorsítása érdekében Azure CDN szolgáltatás működésével kapcsolatos további információkért lásd: a [Azure CDN áttekintése](cdn-overview.md).
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>A beállítás a Cache-Control fejlécek konfigurációban
Statikus tartalom, lemezképek és a stíluslapok, például szabályozhatja a frissítés gyakoriságának módosítása a **applicationHost.config** vagy **web.config** a webes alkalmazás forrásfájljairól.  A **system.webServer\staticContent\clientCache** állítja be a konfigurációs fájlban a `Cache-Control` fejlécet a tartalomhoz. A **web.config**, a konfigurációs beállítások hatással lesz minden mappában, és minden almappa, kivéve, ha a almappa szintjén felülbírálható.  Például állítsa be alapértelmezett élő idő a legfelső szintű 3 napig az összes statikus tartalmat rendelkeznie, de rendelkezik egy almappát, a 6 órás gyorsítótár beállítással több változó tartalma.  A **applicationHost.config**, a hely összes alkalmazás érinti, de felülbírálhatók **web.config** fájlok az alkalmazásokban.

A következő XML-beállítás példáját mutatja be **clientCache** adhatja meg a 3 nap maximális élettartama:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Adja meg **UseMaxAge** ad hozzá egy `Cache-Control: max-age=<nnn>` a válaszok fejlécének megadott érték alapján a **CacheControlMaxAge** attribútum. A timespan formátuma esetében az **cacheControlMaxAge** attribútum <days>.<hours>:<min>:<sec>. További információ a **clientCache** csomópont, lásd: [Ügyfélgyorsítótár <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>A kód a Cache-Control fejlécek beállítása
Az ASP.NET-alkalmazások, a CDN-t úgy, hogy programozott módon gyorsítótárazásának állíthatja be a **HttpResponse.Cache** tulajdonság. További információ a **HttpResponse.Cache** tulajdonság, lásd: [HttpResponse.Cache tulajdonság](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) és [HttpCachePolicy osztály](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Ha szeretné a programozott módon gyorsítótár alkalmazás tartalmát az ASP.NET, győződjön meg arról, hogy a tartalom van megjelölve, kérelmeznék HttpCacheability értékre állításával *nyilvános*. Emellett győződjön meg arról, hogy a gyorsítótár érvényesítő van-e állítva. A gyorsítótár-érvényesítő utolsó módosítás lehet SetLastModified, vagy az egy etag érték beállítása időbélyeg beállítása SetETag meghívásával. Másik lehetőségként azt is megadhatja a gyorsítótár lejárati ideje SetExpires meghívásával, vagy a korábban a jelen dokumentumban ismertetett alapértelmezett gyorsítótár heurisztikus számíthat.  

Például a gyorsítótár tartalmát egy óra, vegye fel a következő:  

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

