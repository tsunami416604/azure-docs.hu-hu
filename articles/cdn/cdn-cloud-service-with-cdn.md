---
title: "Azure-felhőszolgáltatás integrálása az Azure CDN |} Microsoft Docs"
description: "Egy felhőszolgáltatás, amely egy integrált Azure CDN-végpont a tartalmat szolgáltat telepítése"
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f2849fe25fd0d5b3dc26598ffba7591cb7433161
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="intro"></a>Egy felhőalapú szolgáltatás integrálása az Azure CDN szolgáltatás használata
Egy felhőalapú szolgáltatás Azure CDN-t, a tartalom a felhőszolgáltatás helyről integrálható. Ez a megközelítés lehetővé teszi a következő előnyökkel jár:

* Egyszerűen regisztrálhat és lemezképek, parancsprogramok és a felhőszolgáltatás-projekt címtárakban stíluslapok frissítése
* Könnyedén frissíthet a felhőalapú szolgáltatás, például a jQuery vagy rendszerindítási verzió a NuGet-csomagok
* A webes alkalmazás és a CDN-kiszolgált tartalom minden az ugyanabban a Visual Studio felületén kezelése
* A webes alkalmazás és a CDN-kiszolgált tartalom egyesített telepítési munkafolyamat
* ASP.NET kötegelése és minification integrálása az Azure CDN szolgáltatás használata

## <a name="what-you-will-learn"></a>Amiről tanulni fog
Ebből az oktatóanyagból megtudhatja, hogyan:

* [Az Azure CDN-végpont integrálható a felhőalapú szolgáltatás, és a statikus tartalmat szolgáltat a weblapok Azure CDN](#deploy)
* [Adja meg a statikus tartalom gyorsítótár beállításait a felhőalapú szolgáltatás](#caching)
* [Az Azure CDN keresztül vezérlő műveletekből tartalmat](#controller)
* [Kötegelve szolgálnak, és minified Azure CDN tartalom megőrzése mellett a felhasználói élmény, a Visual Studio hibakeresési parancsfájl](#bundling)
* [Konfigurálhatja tartalék CSS és parancsfájlok, amikor az Azure CDN offline állapotban.](#fallback)

## <a name="what-you-will-build"></a>Mit fog létrehozni
Fogja telepíteni egy felhőalapú szolgáltatás webes szerepkör az alapértelmezett ASP.NET MVC-sablont, adja hozzá a kódot egy integrált Azure CDN, kép, a tartományvezérlő műveleti eredmény és az alapértelmezett JavaScript és CSS fájlok, például tartalom kiszolgálására és is létrehozható, a tartalék mechanizmusa kötegek és kiszolgálása között abban az esetben, ha a kapcsolat nélküli üzemmódban a CDN konfigurálásához.

## <a name="what-you-will-need"></a>Mit kell
Ez az oktatóanyag előfeltételei a következők:

* Az aktív [Microsoft Azure-fiók](/account/)
* A Visual Studio 2015 [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> A jelen oktatóanyag elvégzéséhez Azure-fiókra van szükség:
> 
> * Is [szabad nyissa meg az Azure-fiók](https://azure.microsoft.com/pricing/free-trial/) -kapott kreditek használatával kipróbálhatja a fizetős Azure-szolgáltatásokat, és még azok lejárta után is megtarthatja a fiókot, és használhatja az ingyenes Azure-szolgáltatások, például webhelyekhez.
> * Is [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -az MSDN-előfizetés biztosít Önnek krediteket minden hónapban, fizetős Azure-szolgáltatásokat is használhat.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>A felhőalapú szolgáltatás telepítése
Ebben a szakaszban fogja telepíteni az alapértelmezett ASP.NET MVC alkalmazássablon a Visual Studio 2015 egy felhőalapú szolgáltatás webes szerepkör, és integrálhatja egy új CDN-végponthoz. Kövesse az alábbi utasításokat:

1. A Visual Studio 2015, hozzon létre egy új Azure-felhőszolgáltatásban menü címen **fájl > Új > Projekt > felhő > Azure Cloud Service**. Adjon neki egy nevet, és kattintson a **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Válassza ki **ASP.NET webes szerepkör** , és kattintson a  **>**  gombra. Kattintson az OK gombra.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Válassza ki **MVC** kattintson **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Most tegye közzé a webes szerepkör egy Azure felhőszolgáltatást. Kattintson a jobb gombbal a felhőszolgáltatás-projekt, és válassza ki **közzététel**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Ha még nem írta be a Microsoft Azure, kattintson a **fiók hozzáadása...**  legördülő menüből, majd a **vegyen fel egy fiókot** menüpont.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. A bejelentkezési oldal jelentkezzen be az Azure-fiók aktiválásához használt Microsoft-fiókkal.
7. Ha be van jelentkezve, kattintson **következő**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Feltételezve, hogy egy felhőalapú szolgáltatás, vagy tárolási fiókja még nem hozott létre, a Visual Studio segítségével hozhat létre is. Az a **felhőalapú szolgáltatás létrehozása és a fiók** párbeszédpanelen írja be a kívánt szolgáltatás nevét, és válassza ki a kívánt régiót. Ezt követően kattintson a **Create** (Létrehozás) gombra.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. A közzétételi beállítások lapon ellenőrizze a konfigurációt, és kattintson **közzététel**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > A felhőszolgáltatások a közzétételi folyamat hosszú ideig tart. Az engedélyezéséhez a Web Deploy összes szerepkörök beállítás teheti a felhőalapú szolgáltatás sokkal gyorsabb a webes szerepkörök gyors (de ideiglenes) frissítéseket biztosító hibakeresést. Ez a beállítás további információkért lásd: [közzététele a felhőalapú szolgáltatás, az Azure-eszközökkel](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Ha a **Microsoft Azure tevékenységnapló** mutatja, hogy közzétételi állapotát **befejezve**, létrehozhat egy CDN-végpontot, amely integrálva van a felhőalapú szolgáltatás.
   
   > [!WARNING]
   > Ha a közzététel után, a központilag telepített a felhőalapú szolgáltatás egy hiba képernyő jelenik meg, valószínű, mert a felhőalapú szolgáltatás telepítése után használja a [vendég operációs rendszer, amely nem tartalmazza a .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Által a probléma megkerüléséhez [központi telepítése a .NET 4.5.2 az indítási feladatok](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Új CDN-profil létrehozása
A CDN-profil CDN-végpontok gyűjteménye.  Minden profil egy vagy több CDN-végpontot tartalmaz.  Előfordulhat, hogy több profil használatával rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján.

> [!TIP]
> Ha már van a CDN-profilt, amely a jelen oktatóanyag használni kívánt, lépjen [hozzon létre egy új CDN-végpont](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Új CDN-végpont létrehozása
**A tárfiók új CDN-végpont létrehozása**

1. Az a [Azure felügyeleti portálon](https://portal.azure.com), keresse meg a CDN-profilt.  Lehetséges, hogy a profilt az előző lépésben az irányítópulton rögzítette.  Amennyiben nem rögzítette, kattintson **Tallózás** gombra, majd a **CDN-profilok** elemre, végül kattintson arra a profilra, amelyet hozzá szeretne adni a végponthoz.
   
    Megjelenik a CDN-profil panelje.
   
    ![CDN-profil][cdn-profile-settings]
2. Kattintson a **Végpont hozzáadása** gombra.
   
    ![Végpont hozzáadása gomb][cdn-new-endpoint-button]
   
    Megjelenik a **Végpont hozzáadása** panel.
   
    ![Végpont hozzáadása panel][cdn-add-endpoint]
3. Adja meg a CDN-végpont kívánt nevét a **Név** mezőben.  A rendszer ezt a nevet fogja használni a gyorsítótárazott erőforrások eléréséhez a `<EndpointName>.azureedge.net` tartományban.
4. Az a **forrása típusa** legördülő menüből válassza *felhőalapú szolgáltatás*.  
5. Az a **forrásállomásnév** legördülő menüben válassza ki a felhőalapú szolgáltatás.
6. Hagyja meg az alapértelmezett értéket **forrás elérési útvonalának**, **forrás állomásfejlécét**, és **protokoll/forrásport**.  Meg kell adnia legalább egy protokollt (HTTP vagy HTTPS).
7. Az új végpont létrehozásához kattintson a **Hozzáadás** gombra.
8. A végpont a létrehozás után megjelenik a profil végpontjainak listájában. A listanézetben látható a gyorsítótárazott tartalom eléréséhez használható URL-cím, valamint a forrástartomány is.
   
    ![CDN-végpont][cdn-endpoint-success]
   
   > [!NOTE]
   > A végpont nem azonnal lesz használható.  A regisztráció a CDN a hálózaton keresztül propagálása 90 percig is eltarthat. A CDN-tartománynevet azonnal használható felhasználók 404 állapotkódot jelenhet meg addig, amíg a tartalom nem érhető el a CDN keresztül.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>A CDN-végpont tesztelése
A közzétételi állapot esetén **befejezve**, nyisson meg egy böngészőablakot, és navigáljon a  **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. A beállítás az URL-cím van:

    http://camservice.azureedge.net/Content/bootstrap.css

A következő forrás URL-címet a CDN-végpontot, amely megfelel:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Kiválasztásakor  **http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, attól függően, a böngésző kérni fogja letölteni, vagy nyissa meg a bootstrap.css, amely innen származik: a közzétett webes alkalmazást.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Hasonló módon érheti el bármely nyilvánosan elérhető URL-  **http://*&lt;serviceName >*.cloudapp.net/** rögtön a CDN-végpont. Példa:

* Egy .js fájlt a setup parancs/Script elérési útjáról
* Minden tartalom fájlt a /Content elérési útja
* Bármely tartományvezérlő/művelet
* Ha a lekérdezési karakterlánc engedélyezve van a CDN-végpontot, a lekérdezési karakterláncot tartalmazó összes URL-címe:

Valójában a fenti konfigurációjával tárolhatja a teljes körű felhőalapú szolgáltatás  **http://*&lt;cdnName >*.azureedge.net/**. Ha I navigáljon **http://camservice.azureedge.net/**, otthoni/Index jelenik meg a művelet eredménye.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Ez nem jelenti azt, azonban, hogy a rendszer mindig egy teljes körű felhőalapú szolgáltatás Azure CDN keresztül kiszolgálására jó ötlet. 

Statikus kézbesítési optimalizálásával CDN nem feltétlenül felgyorsítása dinamikus eszközöket, amelyek nem jelentenek gyorsítótárazható, kézbesítését vagy nagyon gyakran frissülnek, mivel a CDN-t kell beolvasnia az eszköz új verziójának a forráskiszolgálóról nagyon gyakran. A jelen esetben engedélyezhető [dinamikus hely Acceleration](cdn-dynamic-site-acceleration.md) CDN-végpont nem gyorsítótárazható dinamikus eszközök kézbesítését felgyorsítása érdekében különböző módszereket használó optimalizálása (DSA). 

Ha statikus és dinamikus tartalom vegyesen hellyel rendelkezik, megadhatja a statikus tartalom szolgáltatásához CDN (például általános webes kézbesítés) statikus optimalizálási típusú, és dinamikus tartalom vagy közvetlenül a forráskiszolgálóról szolgáltatásához, vagy a CDN-végpontok DSA optimalizálásával keresztül kapcsolva-eseti alapon. Ebből a célból már láthatta a CDN-végpont az egyes tartalomfájlok elérését. I bemutatja, hogyan egy adott vezérlő művelet egy adott CDN-végpont használatával látják el vezérlő műveletek Azure CDN keresztül tartalmat szolgálnak.

Ez esetben határozzák meg a felhőszolgáltatás-eseti alapon Azure CDN kiszolgálására mely tartalmát. Ebből a célból már láthatta a CDN-végpont az egyes tartalomfájlok elérését. I bemutatja, hogyan keresztül a CDN-végpont egy adott vezérlő művelet kiszolgálására [vezérlő műveletekből Azure CDN keresztül tartalmat](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>A felhőszolgáltatás a statikus fájlok gyorsítótárazási beállítások konfigurálása
Az Azure CDN integráció a felhőalapú szolgáltatás megadhatja, hogyan szeretne statikus tartalmat, hogy a CDN-végpont gyorsítótárazva. Ehhez nyissa meg a *Web.config* a webes szerepkör projekt (pl. WebRole1), és adja hozzá a `<staticContent>` elem `<system.webServer>`. Az alábbi XML konfigurálja a gyorsítótár 3 nap múlva lejár.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Ezután a, a felhőszolgáltatásban található összes statikus fájlok megfigyelheti ugyanaz a szabály a CDN-gyorsítótárban. Részletesebben vezérelhető, a gyorsítótár beállításait, vegye fel a *Web.config* fájlt egy mappába, és ott a beállítások. Például hozzáadhat egy *Web.config* fájlt a *\Content* mappa, és cserélje le a tartalmat a következő XML-kód:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Ez a beállítás hatására az összes statikus fájlok a *\Content* mappát 15 napig gyorsítótárazza.

Konfigurálásával kapcsolatos további információ a `<clientCache>` elem, lásd: [Ügyfélgyorsítótár &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

A [vezérlő műveletekből Azure CDN keresztül tartalmat](#controller), I is bemutatja, hogyan konfigurálja a tartományvezérlő műveleti eredmény-gyorsítótárának beállításait a CDN-gyorsítótárban.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Az Azure CDN keresztül vezérlő műveletekből tartalmat
Egy felhőalapú szolgáltatás webes szerepkör integrálása Azure CDN, esetén az Azure CDN keresztül vezérlő műveletekből tartalmat viszonylag egyszerű. Eltérő szolgál a felhőalapú szolgáltatás közvetlenül az Azure CDN (egy fent), [Martin Balliauw](https://twitter.com/maartenballiauw) bemutatja, hogyan teheti meg egy visszatöltött a tartományvezérlőre, MemeGenerator [csökkenti a késéseket, a webhely, az Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). I fog egyszerűen Reprodukálja azt itt.

Tegyük fel, hogy a felhő szolgáltatás memes generál egy fiatal Horváth Norris-lemezképen alapuló (által fénykép [Alan világos](http://www.flickr.com/photos/alan-light/218493788/)) Ez, például:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Rendelkezik egy egyszerű `Index` művelet, amely lehetővé teszi az ügyfelek számára a képet, adja meg a superlatives akkor hoz létre a meme után azok küldje el a műveletet. Mivel ez Horváth Norris, teheti meg ezen a lapon globálisan menet népszerűvé vált. Ez az Azure CDN félig dinamikus tartalmat szolgáltató jól szemlélteti.

Kövesse a fenti beállítása a tartományvezérlő műveleti lépéseket:

1. Az a *\Controllers* mappa, hozzon létre egy új .cs fájlt nevű *MemeGeneratorController.cs* , és cserélje le a tartalmat a következő kóddal. Ne felejtse el a kijelölt részét cserélje le a CDN-nevet.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Kattintson a jobb gombbal az alapértelmezett `Index()` műveletet, és kijelölheti **nézet hozzáadása**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Fogadja el az alábbi beállításokat, majd kattintson a **Hozzáadás**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Nyissa meg az új *Views\MemeGenerator\Index.cshtml* , és cserélje le a tartalmat a következő egyszerű HTML a superlatives elküldése:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Tegye közzé újra a felhőalapú szolgáltatás, és keresse meg  **http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** a böngészőben.

Ha küldje el a képernyőn értékeket `/MemeGenerator/Index`, a `Index_Post` tartozó műveleti módszer mutató hivatkozást ad vissza a `Show` megfelelő bemeneti azonosítóval tartozó műveleti módszer. A hivatkozásra kattintva érhető el a következő kódot:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Ha a helyi hibakereső van csatlakoztatva, majd elérhetővé válik a helyi átirányítással rendszeres hibakeresési élményt. Ha a felhőszolgáltatásban fut, majd azt fogja átirányítani Önt:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

A következő forrás URL-címet a CDN-végpontot, amely megfelel:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Ezután a `OutputCacheAttribute` attribútuma a `Generate` adhatja meg, hogyan művelet eredménye gyorsítótárazza, amely az Azure CDN esetében figyelembe vesszük metódust. Az alábbi kódot adja meg a gyorsítótár lejárati 1 óra (3600 másodperc).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Hasonlóképpen, szolgálhat bármely tartományvezérlő művelet a tartalmakat a felhőszolgáltatásban keresztül az Azure CDN és a kívánt gyorsítótár-beállítás.

A következő szakaszban I bemutatja, hogyan csomagolt és minified parancsfájlok és Azure CDN keresztül CSS kiszolgálásához.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>ASP.NET kötegelése és minification integrálása az Azure CDN szolgáltatás használata
Parancsfájlok és CSS stíluslapok ritkán módosítása, és az Azure CDN gyorsítótár elsődleges vár. A teljes webes szerepkör keresztül az Azure CDN szolgáltató legkönnyebben kötegelése és minification integrálása az Azure CDN szolgáltatás használata. Azonban előfordulhat, hogy nem kívánt ehhez, I bemutatja, hogyan be erre, miközben megőrzi, mint az ASP.NET kötegelése és minification, a kívánt develper élményt:

* Nagyszerű hibakeresési mód élmény
* Egyszerű telepítés
* Az ügyfelek számára a parancsfájl/CSS verziófrissítések azonnali frissítést
* Ha a CDN-végpontot nem sikerült tartalék mechanizmus
* Minimalizálása érdekében a kód módosítása

Az a **WebRole1** létrehozott projekt [Azure CDN-végpont integrálása az Azure webhelyén, és a statikus tartalmat szolgáltat a weblapok Azure CDN](#deploy), nyissa meg *App_Start\BundleConfig.cs* és vessen egy pillantást a `bundles.Add()` metódushívások.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

Az első `bundles.Add()` utasítás ad hozzá egy parancsfájl-csomagot a virtuális könyvtár `~/bundles/jquery`. Ezután nyissa meg *Views\Shared\_Layout.cshtml* köteg parancsprogramcímkéből megjelenítése hogyan megjelenítéséhez. Nem található a következő kódsort Razor kell:

    @Scripts.Render("~/bundles/jquery")

Futtatásakor a Razor kódot a Azure webes szerepkör, akkor jelenik meg egy `<script>` címke a parancsfájl köteg a következőhöz hasonló:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Azonban amikor futtatja azt a Visual Studio beírásával `F5`, azt, ezzel veszélyezteti a kötegben minden parancsfájl külön-külön (a fenti esetben csak egy parancsprogram fájl a kötegben):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Ez lehetővé teszi, hogy a fejlesztési környezet JavaScript-kód hibakeresési, miközben csökkenti (kötegelése) egyidejű kapcsolatok és jobb lesz a fájl letöltése teljesítmény (minification) éles környezetben. Már egy nagy funkció az Azure CDN integráció megőrzése érdekében. Továbbá, mivel a megjelenített csomagot már tartalmaz egy automatikusan létrehozott verzió-karakterláncnak, replikálni kívánt funkció, a Nugeten keresztül jQuery verziójára frissít, amikor frissíthető az ügyfél oldalán amint lehetséges.

Az alábbi lépéseket követve integrációs ASP.NET kötegelése és minification a CDN-végponthoz.

1. Vissza a *App_Start\BundleConfig.cs*, módosítsa a `bundles.Add()` egy másik használandó módszerek [köteg konstruktor](http://msdn.microsoft.com/library/jj646464.aspx), egy, a CDN-címet adja meg. Ehhez cserélje le a `RegisterBundles` metódus definícióját a következő kóddal:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Ügyeljen arra, hogy a csere `<yourCDNName>` az Azure CDN nevével.
   
    Egyszerű szavakkal állítja `bundles.UseCdn = true` és gondosan kialakított CDN URL-cím hozzá minden egyes csomagot. Ha például az első konstruktort a kódban:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    ugyanaz, mint a következő:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Ez a konstruktor közli az ASP.NET kötegelése és minification leképezése egyéni parancsfájlok, amikor helyileg indítja, de a megadott CDN-cím a szóban forgó parancsfájl eléréséhez. Azonban ügyeljen arra, hogy alaposan kialakított CDN URL-címet a két fontos jellemzők:
   
   * A CDN URL-címhez forrása `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, vagyis a parancsfájl-csomagot a felhőszolgáltatásban valójában a virtuális könyvtár.
   * Mivel a CDN konstruktor használ, a CDN script kód a köteg már nem tartalmaz a megjelenített URL-cím automatikusan létrehozott verzió-karakterlánca. Manuálisan létre kell hoznia egy egyedi verzió-karakterláncot, minden alkalommal, amikor a parancsfájl-csomag módosítása egy gyorsítótár-tévesztései, az Azure CDN kényszerítése. Egy időben a egyedi verzió-karakterlánca állandónak kell találatot eredményező gyorsítótárbeli kereséseinek, az Azure CDN maximalizálása a csomag telepítése után a központi telepítés élettartama keresztül.
   * A lekérdezési karakterlánc v = < W.X.Y.Z > ponttá a *Properties\AssemblyInfo.cs* a webes szerepkör projekt. Akkor is, amely tartalmazza a növekvő a szerelvény verziója, minden alkalommal, amikor Azure közzéteszi a telepítési munkafolyamat. Vagy csak módosíthatja *Properties\AssemblyInfo.cs* a projektet a verzió-karakterlánca automatikusan növekvő, minden alkalommal, amikor a build, használhat helyettesítő karaktereket a "*". Példa:
     
        [szerelvény: AssemblyVersion("1.0.0.*")]
     
     Bármely más stratégia létrehozásakor a központi telepítés során egy egyedi karakterlánc egyszerűsítésére itt fog működni.
2. Ismételt közzététele a felhőalapú szolgáltatás, és elérheti a kezdőlap.
3. Tekintse meg a lap HTML-kódja. Meg kell látni a CDN URL-cím, egy egyedi verzió-karakterláncot, minden alkalommal, amikor módosításokat ismételt közzététele a felhőalapú szolgáltatáshoz együtt jelenik meg. Példa:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. A Visual Studio hibakeresése a Visual Studio felhőszolgáltatás beírásával `F5`.,
5. Tekintse meg a lap HTML-kódja. Minden egyes külön-külön megjelenítve, hogy akkor is egy egységes élmény a Visual Studio hibakeresési parancsfájl továbbra is megjelenik.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Tartalék mechanizmus CDN URL-címek esetén
Azure CDN-végpont bármilyen okból nem sikerül, ha azt szeretné, a weblap kell intelligens ahhoz a forrás webkiszolgálóhoz hozzáférni a megoldásként JavaScript vagy rendszerindítási feltöltését. Súlyos, hogy elveszíti a képek a webhelyen, de sokkal több súlyos stíluslapok és parancsfájlok által biztosított kritikus fontosságú lap funkciókat elveszíti a CDN elérhetetlensége miatt.

A [köteg](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) osztály tartalmazza a tulajdonságot, [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) , amely lehetővé teszi, hogy a tartalék mechanizmus a CDN hiba konfigurálása. Ez a tulajdonság használatához kövesse az alábbi lépéseket:

1. Nyissa meg a webes szerepkör projekt *App_Start\BundleConfig.cs*, amelyikhez hozzáadta a CDN URL-cím az egyes [köteg konstruktor](http://msdn.microsoft.com/library/jj646464.aspx), és a következő módosításokat kiemelt tartalék mechanizmus hozzáadása az alapértelmezett csomagokat:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Ha `CdnFallbackExpression` értéke nem null értékű, parancsfájl van be a nézetmodellbe, a HTML-e a csomag sikeresen betöltődött, és ha nem, a csomag közvetlenül elérje a származási webkiszolgáló. Ez a tulajdonság kell állítani a JavaScript kifejezéshez teszteli, hogy a megfelelő CDN csomagot megfelelően be van töltve. A kifejezés teszteléséhez minden csomag szükséges a tartalom szerint változik. A fenti alapértelmezett csomagokat:
   
   * `window.jquery`jquery-{version} .js definiálva van
   * `$.validator`jquery.validate.js definiálva van
   * `window.Modernizr`modernizer-{version} .js definiálva van
   * `$.fn.modal`bootstrap.js definiálva van
     
     Észrevette, hogy, hogy adott nem állítható be a CdnFallbackExpression a `~/Cointent/css` csomagot. Ez azért, mert jelenleg nincs a [System.Web.Optimization hiba](https://aspnetoptimization.codeplex.com/workitem/104) , amelyek esetében a `<script>` helyett a várt tartalék CSS címkéjének `<link>` címke.
     
     Nincs, de jó [stílus köteg tartalék](https://github.com/EmberConsultingGroup/StyleBundleFallback) által kínált [decemberéig tanácsadás csoport](https://github.com/EmberConsultingGroup).
2. CSS a megoldás használatához hozzon létre egy új .cs fájlt a webes szerepkör projekt *App_Start* nevű mappát *StyleBundleExtensions.cs*, és cserélje le a tartalmat a [kód a Githubról](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. A *App_Start\StyleFundleExtensions.cs*, nevezze át névtere a webes szerepkör nevét (pl. **WebRole1**).
4. Lépjen vissza a `App_Start\BundleConfig.cs` , és módosítsa az utolsó `bundles.Add` kiemelt kódot a következő utasítást:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    A bővítmény metódus használja ugyanazt a parancsfájl a HTML-KÓDBAN a DOM ellenőrzéséhez az egyező osztály neve, a szabály nevét és a CSS-csomagot, és visszatér az eredeti webkiszolgáló meg, ha az nem található egyezés a szabály értéke.
5. Tegye közzé újra a felhőalapú szolgáltatás, és a kezdőlap elérése.
6. Tekintse meg a lap HTML-kódja. Keresse meg injected parancsfájlok a következőhöz hasonló:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Vegye figyelembe, hogy a CSS-köteg injected parancsfájl továbbra is tartalmaz a errant tartós a `CdnFallbackExpression` tulajdonság sorában:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    De óta első része a || kifejezés mindig ad vissza IGAZ (a sorban fölött, amely), a document.write() függvény soha nem fog futni.

## <a name="more-information"></a>További információ
* [Az Azure Content Delivery Network (CDN) áttekintése](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Az Azure CDN szolgáltatás használata](cdn-create-new-endpoint.md)
* [ASP.NET kötegelése és Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
