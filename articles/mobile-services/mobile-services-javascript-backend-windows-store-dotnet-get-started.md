<properties
    pageTitle="Ismerkedés a Mobile Services Windows Áruházbeli alkalmazásokkal való használatával (C#) | Microsoft Azure"
    description="Ez az oktatóanyag ismerteti, hogyan kezdheti el az Azure Mobile Services használatát a Windows Áruházbeli alkalmazások fejlesztésére C# környezetben."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="get-started-article" 
    ms.date="07/21/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>A Mobile Services használatának első lépéseit

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create a Windows App](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md) (Windows-alkalmazás létrehozása).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást univerzális Windows-alkalmazásokhoz az Azure Mobile Services használatával. A univerzális Windows-alkalmazás megoldások Windows Áruház 8.1- és Windows Phone Áruház 8.1-alkalmazásokhoz készült projekteket és egy közös projektet is tartalmaznak. További információkért lásd a [Build universal Windows apps that target Windows and Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx) (A Windows és Windows Phone platformot egyaránt célzó univerzális Windows-alkalmazások építése) című témakört.

Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű *Teendőlista* alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. A létrehozandó mobilszolgáltatás JavaScriptet használ a kiszolgálóoldali üzleti logikához. Az olyan mobilszolgáltatások létrehozásával kapcsolatban, amelyek lehetővé teszik a kiszolgálóoldali üzleti logika írását a támogatott .NET-es nyelveken a Visual Studio használatával, lásd a témakör A .NET háttérrendszer verziója című szakaszát.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott akár 10 ingyenes mobilszolgáltatást a próbaidőszak után is használhatja. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Windowshoz készült Visual Studio 2013 Express]

## Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új univerzális Windows-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezés szolgáltatással létrehozhat egy új univerzális Windows-alkalmazást, vagy módosíthat egy meglévő Windows Áruház- vagy Windows Phone-alkalmazásprojektet a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy, a mobilszolgáltatásához csatlakozó új univerzális Windows-alkalmazást hoz létre.

1.  A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.


2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **Windows** elemre, és bontsa ki a **Create a new Windows Store app** (Új Windows Áruházbeli alkalmazás létrehozása) elemet.

    Ez megjeleníti a mobilszolgáltatáshoz csatlakozó új Windows Áruházbeli alkalmazás létrehozásának három egyszerű lépését.

    ![A Mobile Services gyors üzembe helyezési lépései](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Ha még nem tette, töltse le és telepítse a [Windowshoz készült Visual Studio 2013 Express] a helyi számítógépére vagy virtuális gépére.

4. Kattintson a **Create TodoItem table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

5. A **Download and run your app** (Alkalmazás letöltése és futtatása) menüben válassza ki az alkalmazás nyelvét, és kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolódó *Teendőlista* mintaalkalmazáshoz. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## A Windows-alkalmazás futtatása

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a MainPage.xaml.cs fájlban.

## Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* [Bevezetés az offline adatszinkronizálás használatába] Megismerheti, hogyan teheti gyorsabban kezelhetővé és robusztusabbá alkalmazását az offline adatszinkronizálás segítségével.

* [Hitelesítés hozzáadása a Mobile Services-alkalmazáshoz][Bevezetés a hitelesítés használatába]  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Leküldéses értesítések hozzáadása az alkalmazáshoz][Bevezetés a leküldéses értesítések használatába]  
  Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazás számára.

* [A .NET ügyféloldali kódtár használata](mobile-services-dotnet-how-to-use-client-library.md)  
 Megtudhatja, hogyan kérdezheti le a mobilszolgáltatást, hogyan kezelheti az adatokat, és hogyan érheti el az egyedi API-kat.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[A mobilszolgáltatás-példány meghatározása]:#define-mobile-service-instance
[Következő lépések]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Bevezetés az offline adatszinkronizálás használatába]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Bevezetés a hitelesítés használatába]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Bevezetés a leküldéses értesítések használatába]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md
[Windowshoz készült Visual Studio 2013 Express]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[klasszikus Azure portál]: https://manage.windowsazure.com/
 


<!--HONumber=sep16_HO1-->


