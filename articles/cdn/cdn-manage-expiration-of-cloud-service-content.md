---
title: Az Azure CDN a webes tartalmak elévülésének kezelése |} A Microsoft Docs
description: Ismerje meg az Azure CDN-ben Azure Web Apps-/ Cloud Services, ASP.NET, vagy az IIS-tartalmak elévülésének kezelése.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: c21ae227d74442be5701dd906180392b1e0fdf8b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525670"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Az Azure CDN a webes tartalmak elévülésének kezelése
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

A nyilvánosan elérhető-e forrás webkiszolgálók fájlok gyorsítótárazható az Azure Content Delivery Network (CDN) mindaddig, amíg azok idő-az-élettartam (TTL) lejárta. Az élettartam határozza meg a `Cache-Control` fejlécet a HTTP-válasz a forráskiszolgálóról. Ez a cikk azt ismerteti, hogyan állíthatja be `Cache-Control` fejlécek a Web Apps szolgáltatás a Microsoft Azure App Service, Azure Cloud Services, az ASP.NET-alkalmazások és az Internet Information Services (IIS) helyeken, amelyek mindegyike hasonló módon konfigurált. Beállíthatja a `Cache-Control` fejléc konfigurációs fájlok használatával vagy programozott módon. 

Gyorsítótár beállításai az Azure Portalon is beállításával szabályozhatja [CDN gyorsítótárazási szabályok](cdn-caching-rules.md). Ha hozzon létre egyet, vagy további gyorsítótárazási szabályokat, és állítsa a gyorsítótárazási viselkedés **felülbírálása** vagy **gyorsítótár megkerülése**, a forrás által biztosított gyorsítótárazási beállításait a cikkben leírtak szerint a rendszer figyelmen kívül hagyja. Általános gyorsítótárazási fogalmak kapcsolatos információkért lásd: [gyorsítótárazás működése](cdn-how-caching-works.md).

> [!TIP]
> Ha szeretné, nincs élettartam állítsa a fájl. Ebben az esetben az Azure CDN automatikusan alkalmazza a egy alapértelmezett élettartam hét nap, kivéve, ha már beállította az Azure Portalon gyorsítótárszabályok. Ez az alapértelmezett TTL csak azokra az általános webes kézbesítés optimalizálása. A nagyméretű fájlok optimalizálása az alapértelmezett élettartam egy nap, és az online médialejátszás optimalizálása, az alapértelmezett élettartam egy év.
> 
> Fájlok és más erőforrásokhoz való hozzáférés felgyorsítása érdekében az Azure CDN működésével kapcsolatos további információkért lásd: [áttekintése az Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN gyorsítótárazási szabályok használatával Cache-Control fejléc beállítása
Az előnyben részesített módszere a webkiszolgáló beállítása `Cache-Control` fejléc gyorsítótárazási szabályok használata az Azure Portalon. CDN gyorsítótárazási szabályok kapcsolatos további információkért lásd: [vezérlő Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal](cdn-caching-rules.md).

> [!NOTE] 
> Gyorsítótárazási szabályok csak esetén érhetők el **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** profilok. A **verizon Azure CDN Premium** profilok kell használnia a [Azure CDN szabálymotorral](cdn-rules-engine.md) a a **kezelés** portál a hasonló funkciókat.

**Nyissa meg a CDN gyorsítótárazási szabályok lapot a**:

1. Az Azure Portalon válassza ki a CDN-profil, majd válassza ki a végpont a webkiszolgáló.

1. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN gyorsítótárazási szabályok gomb](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN gyorsítótárazási lap](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Beállítása webes kiszolgáló Cache-Control fejléceket globális gyorsítótárazási szabályok használatával:**

1. Alatt **globális gyorsítótárszabályok**állítsa be **lekérdezési karakterláncok gyorsítótárazásának működése** való **lekérdezési karakterláncok kihagyása** és **gyorsítótárazási viselkedésének** , **Felülbírálás**.
      
1. A **gyorsítótár elévülési ideje**, adja meg a 3600 a **másodperc** mezőbe, vagy az 1 a **óra** mezőbe. 

   ![CDN globális gyorsítótárazási szabályokat példa](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   A globális gyorsítótárazási szabály beállítja egy egy órás gyorsítótárazás időtartama, és hatással van az összes kérelem a végponthoz. Ez a beállítás felülbírálja bármely `Cache-Control` vagy `Expires` a végpont által megadott a forráskiszolgáló által küldött HTTP-fejléceket.   

1. Kattintson a **Mentés** gombra.

**Fájl Cache-Control fejléceket az webkiszolgáló beállítása egyéni gyorsítótárazási szabályok használatával:**

1. A **egyéni gyorsítótárszabályok**, hozzon létre két egyezési feltételei:

     a. Az első egyezés feltétel beállítása **feltételnek megfelelő** való **elérési útja** , és adja meg `/webfolder1/*` a **érték egyezik**. Állítsa be **gyorsítótárazási viselkedésének** való **felülbírálása** , és adja meg a 4 a **óra** mezőbe.

     b. Állítsa be a második feltétel egyezik **feltételnek megfelelő** való **elérési** , és adja meg `/webfolder1/file1.txt` a **érték egyezik**. Állítsa be **gyorsítótárazási viselkedésének** való **felülbírálása** , és adja meg a 2 a **óra** mezőbe.

    ![CDN-t egyéni gyorsítótárazási szabályok példa](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárszabály beállítja az azon fájlok esetében négyórás a gyorsítótárazás időtartama a `/webfolder1` a forráskiszolgálón, a végpont által megadott mappába. A második szabály felülírja az első szabály az `file1.txt` csak fájlt, és beállítja a gyorsítótárazás időtartama, két óra.

1. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>A Cache-Control fejléc beállítása a konfigurációs fájlok használatával
A statikus tartalmat, például képeket vagy stíluslapok, szabályozhatja a frissítési gyakoriság módosítása a **applicationHost.config** vagy **Web.config** a webalkalmazás a konfigurációs fájlok. Beállítása a `Cache-Control` fejléc a tartalomhoz, használja a `<system.webServer>/<staticContent>/<clientCache>` elem mindkét fájlban.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config fájlok használata
A **ApplicationHost.config** a legfelső szintű fájlt az IIS-konfigurációs rendszer. A konfigurációs beállításokat egy **ApplicationHost.config** fájlt a hely az összes alkalmazás hatással, de a beállítások minden felül nem bírálja **Web.config** egy webalkalmazás meglévő fájlokat.

### <a name="using-webconfig-files"></a>A Web.config fájlok segítségével
Az egy **Web.config** fájl, testre szabható a módon a teljes webalkalmazás vagy egy adott könyvtár a webes alkalmazások működését. Általában rendelkeznie kell legalább egy **Web.config** a webalkalmazás gyökérmappájában lévő fájlt. Az egyes **Web.config** fájl egy adott mappából, a konfigurációs beállítások hatással mindent az adott mappában és annak almappáiban, hacsak felül nem bírálja őket a almappa szintjén egy másik **Web.config** a fájl. 

Például beállíthat egy `<clientCache>` eleme egy **Web.config** fájl a gyökérmappában található azon a webes alkalmazásba a webalkalmazásában három napig az összes statikus tartalom gyorsítótárazása. Azt is megteheti egy **Web.config** fájlt egy almappában több változó tartalommal (például `\frequent`), és állítsa annak `<clientCache>` elem az almappában tartalom gyorsítótárazása hat órán keresztül. Az eredmény a teljes tartalom webhely három nap, kivéve a tartalmát egyáltalán van gyorsítótárazva a `\frequent` könyvtár, amely csak hat órán keresztül van gyorsítótárazva.  

A következő XML-konfigurációs fájl példa bemutatja, hogyan állíthatja be a `<clientCache>` elem megadásához három nap maximális élettartama:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Használatához a **cacheControlMaxAge** attribútum, be kell értékét a **cacheControlMode** attribútumot `UseMaxAge`. Ez a beállítás miatt a HTTP-fejléc és irányelv `Cache-Control: max-age=<nnn>`, a választ adni. Az időtartam értéke formátumát a **cacheControlMaxAge** attribútum `<days>.<hours>:<min>:<sec>`. Az érték másodperc alakítja át, és értéket használja a `Cache-Control` `max-age` irányelv. További információ a `<clientCache>` elem, lásd: [Ügyfélgyorsítótár \<clientCache >](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Programozott módon a Cache-Control fejléc beállítása
Az ASP.NET-alkalmazások, a CDN gyorsítótárazási viselkedésének programozott módon beállításával szabályozhatja a **HttpResponse.Cache** .NET API-tulajdonság. További információ a **HttpResponse.Cache** tulajdonságot használja, lásd: [HttpResponse.Cache tulajdonság](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) és [HttpCachePolicy osztály](/dotnet/api/system.web.httpcachepolicy).  

A programozott alkalmazás tartalmak gyorsítótárazására az ASP.NET-ben kövesse az alábbi lépéseket:
   1. Győződjön meg arról, hogy a tartalom van megjelölve a gyorsítótárazható beállításával `HttpCacheability` való `Public`. 
   1. Állítsa be a gyorsítótár érvényesítő meghívásával az alábbi `HttpCachePolicy` módszerek:
      - Hívás `SetLastModified` beállításához egy időbélyeg értékét a `Last-Modified` fejléc.
      - Hívás `SetETag` egy értéket, az a `ETag` fejléc.
   1. Szükség esetén adja meg a gyorsítótár lejárati idő meghívásával `SetExpires` egy értéket, az a `Expires` fejléc. A jelen dokumentumban ismertetett korábban alapértelmezett gyorsítótár heurisztika ellenkező esetben a alkalmazni.

A gyorsítótár egy órán keresztül tartalom, például adja hozzá az alábbi C#-kódot:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>A Cache-Control fejléc tesztelése
A webes tartalom TTL beállításait egyszerűen ellenőrizheti. A böngésző [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), tesztelje, hogy a webes tartalom magában foglalja a `Cache-Control` válaszfejléc. Például egy eszköz is használható **wget**, [Postman](https://www.getpostman.com/), vagy [Fiddler](https://www.telerik.com/fiddler) a válaszfejlécek vizsgálatához.

## <a name="next-steps"></a>További lépések
* [Részleteket olvassa el a **clientCache** elem](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Olvassa el a dokumentációját a **HttpResponse.Cache** tulajdonság](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Olvassa el a dokumentációját a **HttpCachePolicy osztályban**](/dotnet/api/system.web.httpcachepolicy)  
* [Gyorsítótárazás fogalmak ismertetése](cdn-how-caching-works.md)
