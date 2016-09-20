<properties
    pageTitle="Ismerkedés a Mobile Services Windows Áruházbeli JavaScript-alkalmazásokkal való használatával | Azure Mobile Services"
    description="Ez az oktatóanyag ismerteti, hogyan kezdheti el az Azure Mobile Services használatát a Windows Áruházbeli alkalmazások fejlesztésére a JavaScriptben."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="javascript"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="glenga"/>

# A Mobile Services használatának első lépéseit

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create a Windows App with Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md) (Windows-alkalmazás létrehozása a Mobile Apps használatával).  

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást Windows Áruházbeli JavaScript-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű *Teendőlista* alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. A létrehozandó mobilszolgáltatás JavaScriptet használ a kiszolgálóoldali üzleti logikához. 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Windowshoz készült Visual Studio 2013 Express]

## Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új Windows Áruházbeli alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezési szolgáltatással létrehozhat egy új Windows 8.1 Áruházbeli JavaScript-alkalmazást, amely kapcsolódik a mobilszolgáltatáshoz.

1.  A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.


2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **Windows** elemre, és bontsa ki a **Create a new Windows Store app** (Új Windows Áruházbeli alkalmazás létrehozása) elemet.

3. Ha még nem tette, töltse le és telepítse a [Visual Studio 2013][Windowshoz készült Visual Studio 2013 Express] a helyi számítógépére vagy virtuális gépére.

4. Kattintson a **Create TodoItem table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

5. A **Download and run your app** (Alkalmazás letöltése és futtatása) menüben válassza ki az alkalmazás nyelvét, és kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolódó *Teendőlista* mintaalkalmazáshoz. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## A Windows-alkalmazás futtatása

Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, tömörítse ki a fájlokat a számítógépre, és nyissa meg a megoldásfájlt a Visual Studióban.

2. Nyomja le az **F5** billentyűt a projekt újraépítéséhez és az alkalmazás indításához.

3. Az alkalmazásban írjon be egy jelentéssel bíró szöveget az **Insert a TodoItem** (Tennivaló beszúrása) mezőbe, például *Az oktatóanyag befejezése*, majd kattintson a **Save** (Mentés) gombra.

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek az alkalmazás második oszlopában jelennek meg.

4. (Nem kötelező) Futtassa ismét az alkalmazást, és figyelje meg, hogy az előző lépésben mentett adatok betöltődnek a mobilszolgáltatásból az alkalmazás indítása után.
 
4. A [klasszikus Azure portál] visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItems** táblára.

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

>[AZURE.NOTE] A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a default.js fájlban.

## Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megismerheti a [HTML-hez/JavaScripthez készült Mobile Services-ügyfél](mobile-services-html-how-to-use-client-library.md) használatát. 

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[A mobilszolgáltatás-példány meghatározása]:#define-mobile-service-instance
[Következő lépések]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Windowshoz készült Visual Studio 2013 Express]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[klasszikus Azure portál]: https://manage.windowsazure.com/



<!--HONumber=sep16_HO1-->


