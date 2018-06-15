---
title: Az Azure CDN webes tartalom lejáratának kezelése |} Microsoft Docs
description: Útmutató az Azure CDN Azure Web Apps/Cloud Services, az ASP.NET, vagy az IIS tartalom lejáratának kezelése.
services: cdn
documentationcenter: .NET
author: dksimpson
manager: akucer
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: mazha
ms.openlocfilehash: ec5470587454a35bc7606a3518d61bd3491d653b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765541"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Az Azure CDN webes tartalom lejáratának kezelése
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

A nyilvánosan elérhető forrás webkiszolgálók fájlok gyorsítótárazható az Azure Content Delivery Network (CDN) csak az idő-live (TTL) lejárta. Az élettartam határozza meg a `Cache-Control` fejléc a következő a HTTP-válasz a forráskiszolgálóról. Ez a cikk ismerteti, hogyan telepíthet `Cache-Control` a Web Apps szolgáltatása a Microsoft Azure App Service, Azure Cloud Services, az ASP.NET-alkalmazások és az Internet Information Services (IIS) helyeket, amelyek hasonló módon konfigurált fejlécek. Beállíthatja a `Cache-Control` fejléc vagy a konfigurációs fájlok vagy programon keresztül. 

Azt is meghatározhatja, Azure-portálról gyorsítótár beállításait úgy, hogy [szabályok gyorsítótárazás CDN](cdn-caching-rules.md). Ha egy vagy több gyorsítótárazás szabályokat, és állítsa be a gyorsítótár-viselkedést **felülbírálása** vagy **gyorsítótár megkerülése**, a megadott forrás gyorsítótárazási beállítások vannak megadva ebben a cikkben ismertetett figyelmen kívül lesznek hagyva. Általános gyorsítótárazási fogalmak kapcsolatos információkért lásd: [gyorsítótárazás működése](cdn-how-caching-works.md).

> [!TIP]
> Ha szeretné, nincs TTL be egy fájl. Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap során, kivéve, ha beállította az Azure portálon szabályok gyorsítótárazás. Ez az alapértelmezett élettartam csak azokra az általános webes kézbesítési optimalizálás. A nagy méretű fájlok optimalizálást az alapértelmezett élettartam egy nap, az pedig médiaadatfolyam-továbbítást optimalizálást, az alapértelmezett élettartam egy év.
> 
> Fájlok és egyéb erőforrásokhoz való hozzáférés felgyorsítása érdekében Azure CDN szolgáltatás működésével kapcsolatos további információkért lásd: [az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Gyorsítótárazás szabályok CDN használatával Cache-Control fejlécek beállítása
A webalkalmazás-kiszolgáló beállítása az előnyben részesített módszere `Cache-Control` fejléc, hogy gyorsítótárazási szabályokat használja az Azure portálon. Gyorsítótárazás szabályok CDN kapcsolatos további információkért lásd: [vezérlő Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának](cdn-caching-rules.md).

> [!NOTE] 
> Gyorsítótárazás szabályok csak esetén érhetők el **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok. A **verizon Azure CDN Premium** -profilok kell használnia a [Azure CDN szabálymotor](cdn-rules-engine.md) a a **kezelése** hasonló funkciókat a portálon.

**Nyissa meg a CDN gyorsítótárazási szabályok lapot a**:

1. Az Azure portálon válassza ki a CDN-profil, majd válassza ki a végpontot a webkiszolgálón.

2. A beállítások a bal oldali ablaktáblában jelölje ki a **szabályok gyorsítótárazás**.

   ![CDN-gyorsítótárazási szabályok gomb](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   A **szabályok gyorsítótárazás** lap jelenik meg.

   ![CDN-gyorsítótárazási lap](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Beállítása egy webes kiszolgáló Cache-Control fejlécek globális gyorsítótárazási szabályok használatával:**

1. A **szabályok gyorsítótárazás globális**, beállíthatja **lekérdezési karakterláncok gyorsítótárazásának működése** való **lekérdezési karakterláncok figyelmen kívül** , és **gyorsítótárazásának** való **Bírálja felül**.
      
2. A **gyorsítótár lejárati időtartam**, adja meg a 3600 a **másodperc** gépen, illetve az 1 a **óra** mezőbe. 

   ![CDN globális gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   A globális gyorsítótárszabályt beállítja a gyorsítótárazás időtartama egy óra, és hatással van a végpont minden kérelemhez. Felülbírálja a `Cache-Control` vagy `Expires` az eredeti kiszolgálóra a végpont által megadott által küldött HTTP-fejléceket.   

3. Kattintson a **Mentés** gombra.

**Beállítása webkiszolgálón fájl Cache-Control fejlécek gyorsítótárazási egyéni szabályok használatával:**

1. A **egyéni szabályok gyorsítótárazás**, hozzon létre két egyező feltételek:

     a. Az első egyező feltétel beállítása **feltételének** való **elérési** , és írja be `/webfolder1/*` a **más értékkel egyezik**. Állítsa be **gyorsítótárazásának** való **felülbírálása** , és adja meg a 4 a **óra** mezőbe.

     b. A második egyezés feltétel beállítása **feltételének** való **elérési** , és írja be `/webfolder1/file1.txt` a **más értékkel egyezik**. Állítsa be **gyorsítótárazásának** való **felülbírálása** , és adja meg a 2 a **óra** mezőbe.

    ![CDN egyéni gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárszabályt négyórás a azon fájlok esetében a gyorsítótárazás időtartama beállítja a `/webfolder1` a forrás kiszolgálón, a végpont által megadott mappában. A második szabály felülbírálja az első szabály a `file1.txt` csak fájlt, és beállítja egy két órás az gyorsítótárazás időtartama.

2. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>A konfigurációs fájlok Cache-Control fejlécek beállítása
Statikus tartalom, lemezképek és a stíluslapok, például szabályozhatja a frissítés gyakoriságának módosítása a **applicationHost.config** vagy **Web.config** a webalkalmazás konfigurációs fájlok. Beállítása a `Cache-Control` fejlécet a tartalomhoz, használja a `<system.webServer>/<staticContent>/<clientCache>` vagy fájlban.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config fájlok használata
A **ApplicationHost.config** a legfelső szintű fájl az IIS-konfigurációs rendszer. A konfigurációs beállításai a egy **ApplicationHost.config** fájl hatással a hely összes alkalmazást, de bármely beállítások felülbírálják **Web.config** webalkalmazás található fájlokat.

### <a name="using-webconfig-files"></a>A Web.config fájlok segítségével
Az egy **Web.config** fájl, testreszabhatja a módját a teljes webalkalmazás vagy egy adott könyvtár a webes alkalmazás viselkedik. Általában kell legalább egy **Web.config** fájl a webalkalmazás gyökérmappájában. Az egyes **Web.config** fájl egy adott mappában található a konfigurációs beállítások hatással mindent mappában és annak almappáiban, kivéve, ha azok felülbírálják a almappa szinten egy másik **Web.config** fájl. 

Beállíthatja például egy `<clientCache>` eleme egy **Web.config** fájl a gyökérmappában található azon a webes alkalmazás a webes alkalmazás három teljes napig az összes statikus tartalom gyorsítótárazása. Azt is megteheti egy **Web.config** fájlt egy almappában több változó tartalmú (például `\frequent`), és a `<clientCache>` elemben, amely a almappa tartalom gyorsítótárazása hat órán keresztül. A nettó eredménye, hogy a teljes tartalom webhely kivételével minden tartalom három napig gyorsítótárazza a `\frequent` könyvtár, amely csak hat órán át gyorsítótárazza.  

A következő XML-konfigurációs fájl példa bemutatja, hogyan telepíthet a `<clientCache>` elemet adja meg a maximális élettartamát három nap:  

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

## <a name="next-steps"></a>További lépések
* [Részletekért olvassa el a **clientCache** elem](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Olvassa el a dokumentációt a **HttpResponse.Cache** tulajdonság](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Olvassa el a dokumentációt a **HttpCachePolicy osztály**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Gyorsítótárazás fogalmak megismerése](cdn-how-caching-works.md)
