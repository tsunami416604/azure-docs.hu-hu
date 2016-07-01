<properties
    pageTitle="Ismerkedés az Azure Mobile Services PhoneGap/cordova-alkalmazásokkal való használatával | Microsoft Azure"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services PhoneGappel való használatában az iOS-, Android- és Windows Phone-alkalmazások fejlesztéséhez."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="multiple"
    ms.topic="get-started-article" 
    ms.date="02/10/2016"
    ms.author="ggailey777"/>

# A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű _Teendőlista_ alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja.

A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![][3]

### További követelmények

Az oktatóprogram elvégzéséhez a következőkre lesz szükség:

+ PhoneGap-eszközök (a Windows Phone 8-projektekhez 3.2-es vagy újabb verzió szükséges).

+ Egy aktív Microsoft Azure-fiók.

+ A PhoneGap támogatja a többplatformos fejlesztést. A PhoneGap-eszközökön kívül telepítenie kell az egyes célplatformokhoz szükséges eszközöket is:

    - Windows Phone: Telepítse a [Windows Phone-hoz készült Visual Studio 2012 Expresst](https://go.microsoft.com/fwLink/p/?LinkID=268374)
    - iOS: Telepítse az [Xcode]-ot (4.4-es vagy újabb verzió szükséges)
    - Android: Telepítse az [Android Developer Tools][Android SDK]-t
        <br/>(Az Androidhoz készült Mobile Services SDK az Android 2.2-es és újabb verzióin futó alkalmazásokat támogatja. A gyors üzembe helyezés alkalmazás használatához Android 4.2-es vagy újabb verzió szükséges.)

## Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új PhoneGap-alkalmazás létrehozása

Ebben a szakaszban egy, a mobilszolgáltatásához kapcsolódó új PhoneGap-alkalmazást hoz létre.

1.  A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **PhoneGap** elemre, és bontsa ki a **Create a new PhoneGap app** (Új PhoneGap-alkalmazás létrehozása) részt.

    ![][0]

    Ez megjeleníti a mobilszolgáltatáshoz kapcsolódó új PhoneGap-alkalmazás létrehozásának három egyszerű lépését.

    ![][1]

3. Ha még nem tette, töltse le és telepítse a PhoneGapet, valamint legalább az egyik platformfejlesztői eszközt (Windows Phone, iOS vagy Android).

4. Kattintson a **Create TodoItem table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

5. A **Download and run your app** (Alkalmazás letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolódó _Teendőlista_ mintaalkalmazáshoz, továbbá letölti a Mobile Services JavaScript SDK-t is. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Az új PhoneGap-alkalmazás futtatása

Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

1.  Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, és tömörítse ki a fájlokat a számítógépre.

2.  Nyissa meg és futtassa a projektet az egyes platformokra vonatkozó alábbi utasítások szerint.

    + **Windows Phone 8**

        1. Windows Phone 8: Nyissa meg az .sln-fájlt a Windows Phone-hoz készült Visual Studio 2012 Express **platforms\wp8** mappájában.

        2. Nyomja le az **F5** billentyűt a projekt újraépítéséhez és az alkalmazás indításához.

        ![][2]

    + **iOS**

        1. Nyissa meg a projektet az Xcode **platform/ios** mappájában.

        2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához az iPhone-emulátorban, amely ebben a projektben az alapértelmezés.

        ![][3]

    + **Android**

        1. Az Eclipse-ben kattintson a **File** (Fájl), majd az **Import** (Importálás) elemre, bontsa ki az **Android** elemet, kattintson az **Existing Android Code into Workspace** (Meglévő Android-kód a munkaterületbe) parancsra, majd kattintson a **Next** (Tovább) gombra.

        2. Kattintson a **Browse** (Tallózás) parancsra, keresse meg a kitömörített projektfájlok helyét, kattintson az **OK** gombra, győződjön meg róla, hogy a TodoActivity projekt ki van választva, majd kattintson a **Finish** (Befejezés) elemre. <p>Ezzel importálja a projektfájlokat az aktuális munkaterületre.</p>

        3. A **Run** (Futtatás) menüben kattintson a **Run** (Futtatás) parancsra a projekt indításához az Android-emulátorban.

            ![][4]

        >[AZURE.NOTE]A projekt az Android-emulátorban való futtatásához meg kell határoznia legalább egy Android virtuális eszközt (AVD). Ezeket az eszközöket az AVD Manager alkalmazással hozhatja létre és kezelheti.


3. Miután elindította az alkalmazást valamelyik fenti emulátorban, írjon valamit a szövegmezőbe, és kattintson az **Add** (Hozzáadás) gombra.

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a **TodoItem** táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

    > [AZURE.IMPORTANT] A platformprojekt módosításai felül lesznek írva, ha a főprojektet újraépíti a PhoneGap-eszközökkel. Ezért a módosításokat inkább a projekt www gyökérkönyvtárában végezze az alábbi szakaszban leírtak szerint.

4. A [klasszikus Azure portál] visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItem** táblára.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)


## Alkalmazásfrissítések végrehajtása és projektek újraépítése az egyes platformokon

1. A módosításokat a kódfájlokon a „www” könyvtárban hajtsa végre, amely ebben az esetben a „todolist/www” könyvtár.

2. Ellenőrizze, hogy a célplatform mindegyik eszköze elérhető-e a rendszerbeli elérési úton.

2. Nyisson meg egy parancssort a projekt gyökérkönyvtárában, és futtassa az alábbi platformspecifikus parancsok egyikét:

    + **Windows Phone**

        Futtassa a következő parancssort a Visual Studio Developer-parancssorból:

            phonegap local build wp8

    + **iOS**

        Nyissa meg a terminált, és futtassa az alábbi parancsot:

            phonegap local build ios

    + **Android**

        Nyisson meg egy parancssort vagy terminálablakot, és futtassa a következő parancsot.

            phonegap local build android

4. Mindegyik projektet a megfelelő fejlesztői környezetben nyissa meg, az előző szakaszban vázoltak szerint.

>[AZURE.NOTE]A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a page.js fájlban.

## Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* **[Hitelesítés hozzáadása az alkalmazáshoz]**  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.  

* **[Leküldéses értesítések hozzáadása az alkalmazáshoz](https://msdn.microsoft.com/magazine/dn879353.aspx)**  
  Ismerje meg, hogyan regisztrálhat a leküldéses értesítésekre, és küldhet ilyeneket az alkalmazásnak.

* **[A Mobile Services HTML/JavaScript használati fogalmi referenciája](mobile-services-html-how-to-use-client-library.md)**  
  Tudjon meg többet arról, hogyan férhet hozzá az adatokhoz, hívhat meg egyedi API-kat és végezhet hitelesítést a JavaScript ügyféloldali kódtár segítségével.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Hitelesítés hozzáadása az alkalmazáshoz]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[klasszikus Azure portál]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Windows Phone-hoz készült Visual Studio 2012 Express]: https://go.microsoft.com/fwLink/p/?LinkID=268374
 


<!--HONumber=Jun16_HO2-->


