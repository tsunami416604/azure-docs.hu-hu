---
title: Webes tartalmak lejáratának kezelése az Azure CDN-ben | Microsoft dokumentumok
description: Megtudhatja, hogyan kezelheti az Azure Web Apps/Cloud Services, ASP.NET vagy az IIS-tartalmak lejáratát az Azure CDN-ben.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: a701c332659181081184906a73826b7137d8c49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593718"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>A webes tartalmak elévülésének kezelése Azure CDN-ben
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob-tárhely](cdn-manage-expiration-of-blob-content.md)
> 

A nyilvánosan elérhető eredetű webkiszolgálókról származó fájlok az Azure Content Delivery Network (CDN) szolgáltatásban gyorsítótárazhatók, amíg el nem telik az élő (TTL) idő. A TTL-t `Cache-Control` az eredeti kiszolgáló HTTP-válaszának fejléce határozza meg. Ez a cikk bemutatja, hogyan állíthatja be `Cache-Control` a microsoft azure app service, az Azure Cloud Services, a ASP.NET-alkalmazások és az Internet Information Services (IIS) webhelyek webalkalmazások szolgáltatásának fejléceit, amelyek mindegyike hasonlóan van konfigurálva. A fejlécet `Cache-Control` konfigurációs fájlok használatával vagy programozott módon állíthatja be. 

A gyorsítótár-beállításokat az Azure Portalon is szabályozhatja a [CDN-gyorsítótárazási szabályok](cdn-caching-rules.md)beállításával. Ha egy vagy több gyorsítótárazási szabályt hoz létre, és a gyorsítótár-beállításokat **felülbírálásra** vagy **a gyorsítótár mellőzésére**állítja, a cikkben tárgyalt forrásszerinti gyorsítótárazási beállításokat a rendszer figyelmen kívül hagyja. Az általános gyorsítótárazási fogalmakról a [Gyorsítótárazás működése című](cdn-how-caching-works.md)témakörben talál további információt.

> [!TIP]
> Beállíthatja, hogy ne állítson be TTL-t egy fájlon. Ebben az esetben az Azure CDN automatikusan alkalmazza az alapértelmezett Hét napos TTL-t, kivéve, ha gyorsítótárazási szabályokat állított be az Azure Portalon. Ez az alapértelmezett TTL csak az általános webes kézbesítésoptimalizálásra vonatkozik. A nagy fájloptimalizálások esetében az alapértelmezett TTL egy nap, a médiaadatfolyam-optimalizálások esetében pedig egy év.
> 
> Ha többet szeretne tudni arról, hogy az Azure CDN hogyan gyorsítja fel a fájlokhoz és egyéb erőforrásokhoz való hozzáférést, olvassa el az Azure Content Delivery Network áttekintése című [témakört.](cdn-overview.md)
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>A Gyorsítótár-vezérlő fejléceinek beállítása CDN-gyorsítótárazási szabályok használatával
A webkiszolgáló fejlécének beállításának `Cache-Control` előnyben részesített módja a gyorsítótárazási szabályok használata az Azure Portalon. A CDN-gyorsítótárazási szabályokról további információt az [Azure CDN-gyorsítótárazási viselkedésének vezérlése gyorsítótárazási szabályokkal](cdn-caching-rules.md)című témakörben talál.

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon azure CDN Standard és** az **Akamai-profilokból származó Azure CDN Standard esetén** érhetők el. A **Verizon-profilokból származó Azure CDN Premium** esetén az [Azure CDN-szabályok motorját](cdn-rules-engine.md) kell használnia a Portál **kezelése** portálon hasonló funkciókhoz.

**A CDN-gyorsítótárazási szabályok lapjára való navigáláshoz:**

1. Az Azure Portalon válasszon ki egy CDN-profilt, majd válassza ki a webkiszolgáló végpontját.

1. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![A CDN gyorsítótárazási szabályai gomb](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN gyorsítótárazási lapja](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Webkiszolgáló gyorsítótár-vezérlő fejléceinek beállítása globális gyorsítótárazási szabályok használatával:**

1. A **Globális gyorsítótárazási szabályok**csoportban állítsa a **lekérdezési karakterláncok figyelési viselkedését** a **Lekérdezési karakterláncok figyelmen kívül hagyása** beállításra, és állítsa a **gyorsítótárazási viselkedést** **felülbírálásra.**
      
1. A **Gyorsítótár lejárati időtartamához**írja be a 3600 értéket a **Másodperc** mezőbe, az 1 értéket az **Órák** mezőbe. 

   ![Példa a CDN globális gyorsítótárazására](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Ez a globális gyorsítótár-szabály egy órás gyorsítótár-időtartamot állít be, és hatással van a végpontra vonatkozó összes kérelemre. Felülír minden `Cache-Control` `Expires` olyan vagy HTTP-fejlécet, amelyet a végpont által megadott forráskiszolgáló küld.   

1. Kattintson a **Mentés** gombra.

**Webkiszolgáló-fájl Cache-Control fejléceinek beállítása egyéni gyorsítótárazási szabályok használatával:**

1. Az **Egyéni gyorsítótárazási szabályok csoportban**hozzon létre két egyezési feltételt:

     a. Az első egyezési feltételnél állítsa `/webfolder1/*` az **Egyezés feltételt** a **Görbe** beállításra, és írja be a Match érték **mezőbe.** Állítsa a **gyorsítótárazás idúra viselkedést** **felülbírálásra,** és írja be a 4 értéket az **Órák** mezőbe.

     b. A második egyezési feltételnél állítsa `/webfolder1/file1.txt` az **Egyezés feltételt** a Görbe **beállításra,** és írja be a Match **value értéket.** Állítsa a **gyorsítótárazás idúra viselkedést** **felülbírálásra,** és írja be a 2 értéket az **Órák** mezőbe.

    ![Példa a CDN egyéni gyorsítótárazására](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárazási szabály négy órás gyorsítótár-időtartamot állít be a `/webfolder1` végpont által megadott forráskiszolgálón lévő mappában lévő fájlokra. A második szabály csak a `file1.txt` fájl első szabályát bírálja felül, és két órás gyorsítótár-időtartamot állít be.

1. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>A Cache-Control fejlécek beállítása konfigurációs fájlok használatával
Statikus tartalom, például képek és stíluslapok esetén az **applicationHost.config** vagy **a Web.config** konfigurációs fájlok módosításával szabályozhatja a frissítés gyakoriságát a webalkalmazáshoz. A tartalom `Cache-Control` fejlécének beállításához `<system.webServer>/<staticContent>/<clientCache>` használja bármelyik fájlban lévő elemet.

### <a name="using-applicationhostconfig-files"></a>Az ApplicationHost.config fájlok használata
Az **ApplicationHost.config** fájl az IIS konfigurációs rendszer gyökérfájlja. Az **ApplicationHost.config** fájl konfigurációs beállításai a webhelyen található összes alkalmazásra hatással vannak, de a webalkalmazáshoz létező **Web.config** fájlok beállításai felülbírálják őket.

### <a name="using-webconfig-files"></a>Web.config fájlok használata
A **Web.config** fájl segítségével testreszabhatja a teljes webalkalmazás vagy a webalkalmazás egy adott könyvtárának megfelelő viselkedését. Általában legalább egy **Web.config** fájl van a webalkalmazás gyökérmappájában. Egy adott mappában lévő minden **Web.config** fájl esetében a konfigurációs beállítások a mappa minden elemére és almappáira hatással vannak, kivéve, ha azalmappák szintjén egy másik **Web.config** fájl felülbírálja őket. 

Beállíthatja például, `<clientCache>` hogy a webalkalmazás gyökérmappájában egy **Web.config** fájl egy elem három napig gyorsítótárazhassa a webalkalmazás összes statikus tartalmát. **A Web.config** fájlt hozzáadhatja egy több változó tartalmú almappában (például ), és beállíthatja, `\frequent`hogy az `<clientCache>` elem hat órán keresztül gyorsítótárazza az almappa tartalmát. A nettó eredmény az, hogy a tartalom a teljes weboldal gyorsítótárazza `\frequent` három napig, kivéve minden olyan tartalmat a könyvtárban, amely a gyorsítótárban csak hat órán keresztül.  

A következő XML-konfigurációs fájl `<clientCache>` bemutatja, hogyan állíthatja be az elemet úgy, hogy legfeljebb három napos életkort adjon meg:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

A **cacheControlMaxAge** attribútum használatához a **cacheControlMode** attribútum értékét a `UseMaxAge`értékére kell állítania. Ez a beállítás miatt a `Cache-Control: max-age=<nnn>`HTTP-fejléc és az utasítás hozzáadódik a válaszhoz. A **cacheControlMaxAge** attribútum timespan értékének formátuma `<days>.<hours>:<min>:<sec>`. A program másodperces értékre konvertálja az `Cache-Control` `max-age` értékét, és az irányelv értékeként használja. Az elemről `<clientCache>` további információt az [Ügyfélgyorsítótár ügyfélgyorsítótár \<>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)című témakörben talál.  

## <a name="setting-cache-control-headers-programmatically"></a>A Gyorsítótár-vezérlő fejléceinek programozással való beállítása
A ASP.NET alkalmazások esetében a CDN-gyorsítótárazási viselkedést programozott módon szabályozhatja a .NET API **HttpResponse.Cache** tulajdonságának beállításával. A **HttpResponse.Cache** tulajdonságról a [HttpResponse.Cache tulajdonság](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) és a [HttpCachePolicy Class című témakörben](/dotnet/api/system.web.httpcachepolicy)talál további információt.  

Ha programozott módon szeretné gyorsítótárazni az alkalmazások tartalmát ASP.NET, kövesse az alábbi lépéseket:
   1. Ellenőrizze, hogy a tartalom gyorsítótárazhatóként van-e megjelölve a `HttpCacheability` `Public`beállítással. 
   1. Állítson be egy gyorsítótár-érvényesítőt `HttpCachePolicy` az alábbi módszerek egyikének felhívásával:
      - Hívás `SetLastModified` a fejléc időbélyegértékének beállításához. `Last-Modified`
      - Hívás `SetETag` a fejléc értékének beállításához. `ETag`
   1. Szükség esetén adja meg a `SetExpires` gyorsítótár lejárati `Expires` idejét a fejléc értékének beállításához. Ellenkező esetben a dokumentumban korábban ismertetett alapértelmezett gyorsítótár-heurisztika érvényes.

Ha például egy órán keresztül szeretné gyorsítótárazni a tartalmat, adja hozzá a következő C# kódot:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>A Gyorsítótár-vezérlő fejlécének tesztelése
Könnyedén ellenőrizheti a webes tartalom TTL beállításait. A böngésző [fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)ellenőrizze, hogy `Cache-Control` a webes tartalom tartalmazza-e a válaszfejlécet. A válaszfejlécek vizsgálatához használhat olyan eszközt is, mint **a wget,** [a Postman](https://www.getpostman.com/)vagy a [Fiddler.](https://www.telerik.com/fiddler)

## <a name="next-steps"></a>Következő lépések
* [Az **clientCache** elem részletei](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Olvassa el a **HttpResponse.Cache** tulajdonság dokumentációját](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [A **HttpCachePolicy osztály** dokumentációjának elolvasása](/dotnet/api/system.web.httpcachepolicy)  
* [További információ a gyorsítótárazási fogalmakról](cdn-how-caching-works.md)
