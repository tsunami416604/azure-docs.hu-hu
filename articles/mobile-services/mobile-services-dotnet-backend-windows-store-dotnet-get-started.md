<properties
    pageTitle="Ismerkedés a Mobile Services for Windows Universal alkalmazásokkal | Microsoft Azure"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services for Universal Windows-alkalmazásfejlesztések első lépéseiben C# környezetben."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/11/2016"
    ms.author="glenga"/>


# <a name="getting-started"> </a>A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create a Windows App with Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md) (Windows-alkalmazás létrehozása a Mobile Apps használatával).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást univerzális Windows-alkalmazásokhoz az Azure Mobile Services használatával. A univerzális Windows-alkalmazás megoldások Windows Áruház 8.1- és Windows Phone Áruház 8.1-alkalmazásokhoz készült projekteket és egy közös projektet is tartalmaznak. További információkért lásd a [Build universal Windows apps that target Windows and Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx) (A Windows és Windows Phone platformot egyaránt célzó univerzális Windows-alkalmazások építése) című témakört.

Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű *Teendőlista* alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. Az így létrehozott mobilszolgáltatás a támogatott .NET-nyelveket használó Visual Studio használatával kezeli a kiszolgálóoldali üzleti logikát és a mobilszolgáltatást. Jelen témakörben a JavaScript háttérrendszer verzióját ismertető szakasz mutatja be olyan mobilszolgáltatások létrehozását, amelyek lehetővé teszik a kiszolgálóoldali üzleti logika írását a JavaScript használatával.

>[AZURE.NOTE]Ez a témakör bemutatja, hogyan hozhat létre egy új mobilszolgáltatás-projektet és univerzális Windows-alkalmazást a klasszikus Azure portál használatával. A Visual Studio 2013 Update 3 használatával azt is megteheti, hogy új mobilszolgáltatás-projektet ad egy meglévő Visual Studio-megoldáshoz. További információ: [Mobile Services-szolgáltatások hozzáadása egy meglévő alkalmazáshoz](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md)

>Mobilszolgáltatás hozzáadása egy Windows Phone 8.0- vagy Windows Phone Silverlight 8.1-alkalmazásprojekthez: [Mobile Services-szolgáltatások hozzáadása egy meglévő Windows Phone-alkalmazáshoz](mobile-services-windows-phone-get-started-data.md)

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott akár 10 ingyenes mobilszolgáltatást a próbaidőszak után is használhatja. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started%2F).
* [Visual Studio 2013].

## Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Új univerzális Windows-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezés szolgáltatással létrehozhat egy új alkalmazást, vagy módosíthat egy meglévő alkalmazást a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy, a mobilszolgáltatásához csatlakozó új univerzális Windows-alkalmazást hoz létre.

1. A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **Windows** elemre, és bontsa ki a **Create a new Windows Store app** (Új Windows Áruházbeli alkalmazás létrehozása) elemet.

    Ez megjeleníti a mobilszolgáltatáshoz csatlakozó új Windows Áruházbeli alkalmazás létrehozásának három egyszerű lépését.

    ![A Mobile Services gyors üzembe helyezési lépései](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Ha még nem tette, töltse le és telepítse a [Visual Studio 2013] eszközt a helyi számítógépén vagy virtuális gépén.

4. Az **Alkalmazás és szolgáltatás letöltése és futtatása helyileg** menüben válassza ki a Windows Áruház-alkalmazás nyelvét, és kattintson a **Letöltés** gombra.

    Ezzel letölt egy megoldást, amely a mobilszolgáltatás és a mobilszolgáltatáshoz kapcsolt _Tennivalók listája_ mintaalkalmazás projektjeit is tartalmazza. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Alkalmazás tesztelése a helyi mobilszolgáltatáson

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a MainPage.xaml.cs fájlban.


## Mobilszolgáltatás közzététele

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>A Közös kódú projektben nyissa meg az App.xaml.cs fájlt, keresse meg a <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>-példányt létrehozó kódot, tegyen megjegyzés jelzést az ezen ügyfelet a <em>localhost</em> használatával létrehozó kód mellé, és törölje a jelzést az ügyfelet a távoli mobilszolgáltatás URL-címével létrehozó kód mellől, amely a következőképpen néz ki:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

    <p>The client will now access the mobile service published to Azure.</p></li>
</ol>

## Alkalmazás tesztelése az Azure-on tárolt mobilszolgáltatáson

Most, hogy a mobilszolgáltatás közzé lett téve, és az ügyfél csatlakozik az Azure-on tárolt távol mobilszolgáltatáshoz, futtathatjuk az alkalmazást úgy, hogy az Azure-t használjuk elemtárolásra.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]


## Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* [Mobile Services-szolgáltatások hozzáadása egy meglévő alkalmazáshoz][Bevezetés az adatok használatába]
  <br/>További információk az adatok tárolásáról és lekérdezéséről a Mobile Services használatával.

* [Bevezetés az offline adatszinkronizálás használatába]
  <br/>Megismerheti, hogyan teheti gyorsabban kezelhetővé és robusztusabbá alkalmazását az offline adatszinkronizálás segítségével.

* [Hitelesítés hozzáadása a Mobile Services-alkalmazáshoz][Bevezetés a hitelesítés használatába]
  <br/>Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Leküldéses értesítések hozzáadása az alkalmazáshoz][Bevezetés a leküldéses értesítések használatába]
  <br/>Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazásnak.

* [Mobile Services .NET-háttérrendszer hibaelhárítása]
  <br/> Ismerje meg, hogyan diagnosztizálhatja és javíthatja ki a Mobile Services .NET-háttérrendszerekkel kapcsolatban felmerülő hibákat.

További információk az univerzális Windows-alkalmazásokról: [Több eszközplatform támogatása egyetlen mobileszközről](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs)

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->

<!-- Images. -->



<!-- URLs. -->
[Visual Studio 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Bevezetés az adatok használatába]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Bevezetés az offline adatszinkronizálás használatába]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Bevezetés a hitelesítés használatába]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Bevezetés a leküldéses értesítések használatába]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript és HTML]: mobile-services-win8-javascript/
[klasszikus Azure portál]: https://manage.windowsazure.com/
[Mobile Services .NET-háttérrendszer hibaelhárítása]: mobile-services-dotnet-backend-how-to-troubleshoot.md



<!--HONumber=Jun16_HO2--->


