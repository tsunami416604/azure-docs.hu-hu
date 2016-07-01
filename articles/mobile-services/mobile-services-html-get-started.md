<properties
    pageTitle="Ismerkedés az Azure Mobile Services for HTML/JavaScript alkalmazásokkal | Microsoft Azure"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services for HTML-fejlesztések első lépéseiben."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html5"
    ms.devlang="javascript"
    ms.topic="get-started-article" 
    ms.date="11/30/2015"
    ms.author="glenga"/>


# <a name="getting-started"> </a>A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Áttekintés 

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást HTML-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű *Teendőlista* alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. Megtekintheti ennek az oktatóanyagnak a következő videó verzióját. 

> [AZURE.VIDEO mobile-get-started-html]
 
A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![][0]

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, HTML-alkalmazásokkal kapcsolatos Mobile Services-oktatóanyag elvégzéséhez. PhoneGap/Cordova-alkalmazásokkal kapcsolatos információkért lásd az útmutató [PhoneGap/Cordova-verzió](mobile-services-javascript-backend-phonegap-get-started.md) szakaszát.

##Előfeltételek

Ezen oktatóanyag elvégzésének a következők a feltételei:

+ A helyi számítógépen futnia kell a következő webkiszolgálók egyikének:

    +  **Windows rendszeren**: IIS Express. Az IIS Express a [Microsoft Webplatform-telepítő] használatával telepíthető.
    +  **MacOS X rendszeren**: Python, amelynek már telepítve kell lennie.
    +  **Linux rendszeren**: Python. A [Python] kiszolgálónak a legújabb verzióját kell telepítenie.

    Az alkalmazás tárolására bármilyen webkiszolgálót használhat, de ezek azok a webkiszolgálók, amelyeket a letöltött parancsfájlok támogatnak.  

+ Egy HTML5-támogatással rendelkező webböngésző.
+ Egy Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank). 


## <a name="create-new-service"> </a>Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új HTML-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezés szolgáltatással létrehozhat egy új alkalmazást, vagy módosíthat egy meglévő alkalmazást a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy, a mobilszolgáltatásához csatlakozó új HTML-alkalmazást hoz létre.

1.  A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.


2. A gyorsindítási lap **Platform kiválasztása** részében kattintson a **Windows** lehetőségre, és bontsa ki az **Új HTML-alkalmazás létrehozása** részt.

    ![][6]

    Ez megjeleníti a mobilszolgáltatáshoz kapcsolódó új HTML-alkalmazás létrehozásának és üzemeltetésének három egyszerű lépését.

    ![][7]

3. Kattintson a **Create TodoItems table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

4. A **Download and run your app** (Alkalmazás letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a webhelyfájlokat a mobilszolgáltatáshoz kapcsolt _Tennivalók listája_ mintaalkalmazáshoz. Mentse el a tömörített fájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

5. A **Configure** (Konfigurálás) lapon ellenőrizze, hogy a(z) `localhost` már szerepel-e a **Allow requests from host names** (Kérések engedélyezése az állomásnevekről) listában az **Cross-Origin Resource Sharing (CORS)** (Eltérő eredetű erőforrás-megosztás (CORS)) felületen. Ha nincs a listán írja be a(z) `localhost` szöveget a **Host Name** (Állomásnév) mezőbe, majd kattintson a **Save** (Mentés) gombra.

    ![][9]

    > [AZURE.IMPORTANT] Ha a gyorsindító alkalmazást a localhost helyett egy másik webkiszolgálón helyezi üzembe, a **Kérések engedélyezése az állomásnevekről** listához hozzá kell adnia a webkiszolgáló állomásnevét. További információ: [Eltérő eredetű erőforrás-megosztás](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx)

## A HTML-alkalmazás üzemeltetése és futtatása

Az oktatóanyag utolsó szakaszát egy új alkalmazás a helyi számítógépen való üzemeltetése és futtatása teszi ki.

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, és tömörítse ki a fájlokat a számítógépre, majd indítsa el az alábbi parancsfájlok egyikét a **server** almappából.

    + **launch-windows** (Windows rendszerű számítógépek)
    + **launch-mac.command** (Mac OS X rendszerű számítógépek)
    + **launch-linux.sh** (Linux rendszerű számítógépek)

    > [AZURE.NOTE] Windows rendszerű számítógépeken, amikor a PowerShell a parancsfájl futtatási szándékának megerősítését kéri, írja be: `R` Lehetséges, hogy a webböngészője arra fogja figyelmeztetni, hogy ne futtassa a parancsfájlt, mert az az internetről lett letöltve. Ha ez történik, utasítsa a böngészőt a parancsfájl betöltésének folytatására.

    Ezzel a helyi számítógépen elindul egy webkiszolgáló, amely az új alkalmazást tárolja.

2. Nyissa meg a <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> URL-címet egy webböngészőben az alkalmazás indításához.

3. Az alkalmazásban írjon be valami jelentéssel bíró szöveget az **Új feladat megadása** mezőbe, például _Az oktatóanyag befejezése_, majd kattintson a **Hozzáadás** gombra.

    ![][10]

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek az alkalmazás második oszlopában jelennek meg.

    > [AZURE.NOTE] A mobilszolgáltatást adatok lekérésére és beszúrására meghívó kódot meg is tekintheti a page.js fájlban.

4. A [klasszikus Azure portál] visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItems** táblára.

    ![][11]

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![][12]

## <a name="next-steps"> </a>Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* **[Hitelesítés hozzáadása az alkalmazáshoz]**  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* **[A Mobile Services HTML/JavaScript használati fogalmi referenciája]**  
  Tudjon meg többet a Mobile Services with HTML/JavaScript használatáról


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[A mobilszolgáltatás-példány meghatározása]:#define-mobile-service-instance
[Következő lépések]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Hitelesítés hozzáadása az alkalmazáshoz]: mobile-services-html-get-started-users.md

[klasszikus Azure portál]: https://manage.windowsazure.com/
[Microsoft Webplatform-telepítő]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[A Mobile Services HTML/JavaScript használati fogalmi referenciája]: mobile-services-html-how-to-use-client-library.md
[Eltérő eredetű erőforrások megosztása]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 



<!--HONumber=Jun16_HO2--->


