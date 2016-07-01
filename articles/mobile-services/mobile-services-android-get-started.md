<properties
    pageTitle="Ismerkedés az Azure Mobile Services for Android alkalmazásokkal (JavaScript háttérrendszer)"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services for Android-fejlesztések első lépéseiben (JavaScript háttérrendszerrel)."
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="reikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="04/08/2016"
    ms.author="ricksal"/>

# Ismerkedés a Mobile Services for Android rendszerrel (JavaScript háttérrendszer)

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create an Android app in Azure Mobile Apps](../app-service-mobile/app-service-mobile-android-get-started.md) (Android-alkalmazás létrehozása az Azure Mobile Apps szolgáltatásban).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást Android-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű **Teendőlista** alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja.

> [AZURE.VIDEO mobile-get-started-android]

A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Előfeltételek

Az oktatóprogram elvégzéséhez szükség lesz az [Android Developer Tools](https://developer.android.com/sdk/index.html) eszközökre, amelyek az Android Studio integrált fejlesztőkörnyezetet és a legújabb Android platformot foglalják magukban. Android 4.2 vagy újabb verzió szükséges.

A letöltött gyorsútmutató-projekt tartalmazza az Azure Mobile Services SDK for Android csomagot.

> [AZURE.IMPORTANT] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új Android-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyorsútmutatóval létrehozhat egy új alkalmazást, vagy módosíthat egy meglévő alkalmazást a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy, a mobilszolgáltatásához csatlakozó új Android-alkalmazást hoz létre.

1.  A klasszikus Azure portálon kattintson a **Mobile Services** lehetőségre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyorsindítási lap **Platform kiválasztása** részében kattintson az **Android** lehetőségre, és bontsa ki az **Új Android-alkalmazás létrehozása** részt.

    ![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

    Ez megjeleníti a mobilszolgáltatáshoz csatlakozó új Android-alkalmazás létrehozásának három egyszerű lépését.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Ha még nem tette, töltse le és telepítse az [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125) eszközöket a helyi számítógépén vagy virtuális gépén.

4. Kattintson a **TodoItem tábla létrehozása** lehetőségre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.


5. Most töltse le az alkalmazást a **Letöltés** gomb lenyomásával.

## Az Android-alkalmazás futtatása

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>További lépések
Most, hogy végzett a gyorsútmutatóval, ismerje meg, hogyan hajthat végre további fontos feladatokat a Mobile Services szolgáltatásban:

* [Bevezetés az adatok használatába]
  <br/>További információk az adatok tárolásáról és lekérdezéséről a Mobile Services használatával.

* [Bevezetés a hitelesítés használatába]
  <br/>Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Bevezetés a leküldéses értesítések használatába]
  <br/>Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazás számára.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- URLs. -->
[Első lépések (Eclipse)]: mobile-services-android-get-started-ec.md
[Bevezetés az adatok használatába]: mobile-services-android-get-started-data.md
[Bevezetés a hitelesítés használatába]: mobile-services-android-get-started-users.md
[Bevezetés a leküldéses értesítések használatába]: mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533




<!--HONumber=Jun16_HO2-->


