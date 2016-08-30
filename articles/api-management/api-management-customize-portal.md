<properties
    pageTitle="A fejlesztői portál testreszabása az Azure API Management szolgáltatásban | Microsoft Azure"
    description="Megtudhatja, hogyan szabhatja testre a fejlesztői portált az Azure API Management szolgáltatásban."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="sdanie"/>

# A fejlesztői portál testreszabása az Azure API Management szolgáltatásban

Ez az útmutató ismerteti, hogyan módosíthatja a fejlesztői portál megjelenését és működését az Azure API Management szolgáltatásban, hogy következetes legyen a márkájával.

## <a name="change-page-headers"> </a>Az oldalfejléc szövegének vagy emblémájának módosítása

A portálok testreszabásának egyik fő szempontja az oldalak tetején lévő szöveg lecserélése a vállalat nevére vagy emblémájára.

A fejlesztői portál tartalmát a klasszikus Azure portálon keresztül elérhető közzétevő portálon lehet módosítani. Az API-kat közzétevő portál eléréséhez kattintson a **Kezelés** lehetőségre az API Management szolgáltatáshoz tartozó klasszikus Azure portálon.

![Közzétevő portál][api-management-management-console]

A fejlesztői portál egy tartalomkezelő rendszeren, vagy más néven CMS-en alapul. Az oldalakon megjelenő fejléc egy speciális tartalomtípus, amelyet widgetnek neveznek. A widget tartalmának szerkesztéséhez kattintson a bal oldali **Fejlesztői portál** menü **Widgetek** elemére, majd válassza ki a listából a **Fejléc** nevű widgetet.

![Widgetek fejléc][api-management-widgets-header]

A fejléc tartalmát a **Törzs** mezőben lehet szerkeszteni. Módosítsa a szöveget „Fabrikam fejlesztői portál”-ra, majd kattintson az oldal alján található **Mentés** gombra.

Most már a fejlesztői portál mindegyik oldalán az új fejléc fog megjelenni.

> Ha a közzétevő portálon tartózkodik, és meg szeretné nyitni a fejlesztői portált, kattintson a **Fejlesztői portál** lehetőségre a felső menüsoron.

## <a name="change-headers-styling"> </a>A fejlécek stílusának módosítása

A portál oldalain megjelenő színeket, betűtípusokat, méreteket, sorközöket és más stíluselemeket a stílusszabályok határozzák meg. A stílusok szerkesztéséhez kattintson a **Megjelenés** elemre a közzétevő portál **Fejlesztői portál** menüjében, majd kattintson a **Testreszabás megkezdése** lehetőségre a stílusszerkesztő engedélyezéséhez.

A böngésző átvált a fejlesztő portál egy rejtett oldalára, amely tartalommintákat és példákat tartalmaz a webhelyen alkalmazott összes stílusszabályra. A stílusszerkesztő megnyitásához mozgassa a kurzort az oldal bal szélső részén található vékony, szürke függőleges sávra. Ekkor megjelenik a szerkesztő eszköztár.

![Testreszabás eszköztár][api-management-customization-toolbar]

A stílusszabályok szerkesztésének két fő módja van – A **Minden szabály szerkesztése** lehetőség felsorolja az összes használatban lévő stílusszabályt, az **Elem kiválasztása** lehetőséggel pedig kiválaszthat egy elemet az oldalról, ahol éppen tartózkodik, és csak a választott elem stílusai jelennek meg.

Ebben a szakaszban csak a fejlécek stílusát szeretnénk módosítani. A stílusszerkesztő eszköztáron kattintson az **Elem kiválasztása** elemre, majd kattintson a **Testreszabni kívánt elem kiválasztása** lehetőségre. Ha ezután rámutat az egérrel az elemekre, ki lesznek emelve, ezzel jelezve, hogy kattintás esetén melyik elem stílusait kezdené el szerkeszteni. A fejlécen mutasson az egérrel a vállalat nevére („Fabrikam fejlesztői portál”, ha követte az előző szakasz utasításait), majd kattintson rá. A stílusszerkesztőben meg fognak jelenni a névvel és kategóriákkal rendelkező stílusszabályok.

Minden szabály a kiválasztott elem egy formázási tulajdonságát jelöli. Például a fent kiválasztott fejléc szövegénél a szövegméret @font-size-h1, míg a betűtípus neve alternatívákkal @headings-font-family.

> Ha ismeri a [bootstrap][] felépítését, ezek a szabályok valójában [LESS változók][] a fejlesztői portál által használt bootstrap témában.

Módosítsa a fejléc szövegének színét. Válassza ki a **@headings-color** mező bejegyzését, és írja be a **#000000** kódot. Ez a fekete szín hexadecimális kódja. Eközben a szövegmező végén megjelenik egy színjelző négyzet. Ha rákattint a jelzőre, a színválasztó segítségével kiválaszthat egy színt.

![Színválasztó][api-management-customization-toolbar-color-picker]

Ha végrehajtotta a választott elem stílusainak módosítását, kattintson a **Változtatások előnézete** lehetőségre az eredmények megjelenítéséhez. Ezen a ponton még csak a rendszergazdák láthatják őket. Ha mindenki számára láthatóvá szeretné tenni a módosításokat, kattintson a **Közzététel** gombra a stílusszerkesztőben, és hagyja jóvá a módosításokat.

![Közzététel menü][api-management-customization-toolbar-publish-form]

> Az oldal bármely más elemére vonatkozó stílusszabályok módosításához kövesse ugyanazt az eljárást, mint a fejlécnél. Kattintson az **Elem kiválasztása** lehetőségre a stílusszerkesztőben, válassza ki a kívánt elemet, majd kezdje el módosítani a képernyőn megjelenő stílusszabályok értékeit.

## <a name="edit-page-contents"> </a>Oldal tartalmának szerkesztése

A fejlesztői portál automatikusan létrehozott oldalakból áll, például API-k, Termékek, Alkalmazások, Problémák és kézzel írt tartalmak. Mivel egy tartalomkezelő rendszeren alapul, szükség szerint létre lehet hozni ilyen tartalmakat.

A létező tartalomoldalak listájának megtekintéséhez kattintson a **Tartalom** lehetőségre a közzétevő portál **Fejlesztői portál** menüjében.

![Tartalom kezelése][api-management-customization-manage-content]

A fejlesztői portál kezdőlapján megjelenített tartalmak szerkesztéséhez kattintson a **Kezdőlap** lehetőségre. Hajtsa végre a kívánt módosításokat, tekintse meg az előnézetet, ha szeretné, majd kattintson a **Közzététel most** parancsra, hogy mindenki számára láthatóvá tegye őket.

> A kezdőlap egy speciális elrendezést használ, amellyel megjelenítheti a szalagcímeket az oldal tetején. Ez a szalagcím nem szerkeszthető a **Tartalom** szakaszban. A szalagcím szerkesztéséhez kattintson a **Fejlesztői portál** menü **Widgetek** elemére, válassza az **Aktuális réteg** listában a **Kezdőlap** lehetőséget, majd nyissa meg a **Kiemelt szakasz** **Szalagcím** elemét. A widget tartalmai ugyanúgy szerkeszthetők, mint bármely más oldal.

## <a name="next-steps"> </a>Következő lépések

-   Tekintse meg a [Bevezetés az API-k speciális konfigurálásába][] oktatóanyag többi témakörét.

[Az oldalfejlécek szövegének/emblémájának módosítása]: #change-page-headers
[A fejlécek stílusának módosítása]: #change-headers-styling
[Oldal tartalmának szerkesztése]: #edit-page-contents
[Következő lépések]: #next-steps

[klasszikus Azure portálon]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Bevezetés az API-k speciális konfigurálásába]: api-management-get-started-advanced.md
[bootstrap]: http://getbootstrap.com/
[LESS változók]: http://getbootstrap.com/css/



<!--HONumber=jun16_HO2-->


