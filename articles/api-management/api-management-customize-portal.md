---
title: "A fejlesztői portál testreszabása az Azure API Management szolgáltatásban | Microsoft Docs"
description: "Megtudhatja, hogyan szabhatja testre a fejlesztői portált az Azure API Management szolgáltatásban."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 13431648e87d085161ad278dc991d49f7872be34
ms.openlocfilehash: 60213f885020a5ba36d6ada0812f755e06b3c48b


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>A fejlesztői portál testreszabása az Azure API Management szolgáltatásban
Ez az útmutató ismerteti, hogyan módosíthatja a fejlesztői portál megjelenését és működését az Azure API Management szolgáltatásban, hogy következetes legyen a márkájával.

## <a name="change-page-headers"> </a>Az oldalfejléc szövegének vagy emblémájának módosítása
A portálok testreszabásának egyik fő szempontja az oldalak tetején lévő szöveg lecserélése a vállalat nevére vagy emblémájára.

A fejlesztői portál tartalmát az Azure Portalon keresztül elérhető közzétevő portálon lehet módosítani. Az eléréséhez kattintson a **Közzétevő portál** elemre az API Management-példány szolgáltatási eszköztárából.

![Közzétevő portál][api-management-management-console]

A fejlesztői portál egy tartalomkezelő rendszeren, vagy más néven CMS-en alapul. Az oldalakon megjelenő fejléc egy speciális tartalomtípus, amelyet widgetnek neveznek. A widget tartalmának szerkesztéséhez kattintson a bal oldali **Fejlesztői portál** menü **Widgetek** elemére, majd válassza ki a listából a **Fejléc** nevű widgetet.

![Widgetek fejléc][api-management-widgets-header]

A fejléc tartalmát a **Törzs** mezőben lehet szerkeszteni. Módosítsa a szöveget „Fabrikam fejlesztői portál”-ra, majd kattintson az oldal alján található **Mentés** gombra.

Most már a fejlesztői portál mindegyik oldalán az új fejléc fog megjelenni.

> Ha a közzétevő portálon tartózkodik, és meg szeretné nyitni a fejlesztői portált, kattintson a **Fejlesztői portál** lehetőségre a felső menüsoron.
> 
> 

## <a name="change-headers-styling"> </a>A fejlécek stílusának módosítása
A portál oldalain megjelenő színeket, betűtípusokat, méreteket, sorközöket és más stíluselemeket a stílusszabályok határozzák meg. Az egyes stílusok szerkesztéséhez a **fejlesztői portálon** nyissa meg a bal oldali testreszabási eszköztárat úgy, hogy az egérmutatót a testreszabási ikon fölé viszi, majd az eszköztáron kiválasztja a „styles” (stílusok) lehetőséget.

![Testreszabási eszköztár gombja][api-management-customization-toolbar-button]

A stílusszabályokat két fő módon szerkesztheti: áttekintheti az összes használt stílusszabály alapértelmezés szerint megjelenő listáját, és szükség szerint módosíthatja az egyes stílusokat, vagy kiválaszthatja a **Select an element on the page** (Elem kiválasztása az oldalon) beállítást, és ha az oldalon valahová kattint, kizárólag az adott elemhez tartozó stílusok fognak megjelenni.

Ebben a szakaszban csak a fejlécek stílusát szeretnénk módosítani. A stílusszerkesztő eszköztáron kattintson a **Select an element on the page** (Elem kiválasztása az oldalon) lehetőségre. 

![Testreszabás eszköztár][api-management-customization-toolbar]

Ha ezután rámutat az egérrel az elemekre, ki lesznek emelve, ezzel jelezve, hogy kattintás esetén melyik elem stílusait kezdené el szerkeszteni. A fejlécen mutasson az egérrel a vállalat nevére („Fabrikam fejlesztői portál”, ha követte az előző szakasz utasításait), majd kattintson rá. A stílusszerkesztőben meg fognak jelenni a névvel és kategóriákkal rendelkező stílusszabályok. Minden szabály a kiválasztott elem egy formázási tulajdonságát jelöli. Például a fent kiválasztott fejléc szövegénél a szövegméret @font-size-h1, míg a betűtípus neve alternatívákkal @headings-font-family.

> Ha ismeri a [bootstrap][bootstrap] felépítését, ezek a szabályok valójában [LESS változók][LESS variables] a fejlesztői portál által használt bootstrap témában.
> 
> 

Módosítsa a fejléc szövegének színét. Válassza ki a **@headings-color** mező bejegyzését, és írja be a **#000000** kódot. Ez a fekete szín hexadecimális kódja. Eközben a szövegmező végén megjelenik egy színjelző négyzet. Ha rákattint a jelzőre, a színválasztó segítségével kiválaszthat egy színt.

![Színválasztó][api-management-customization-toolbar-color-picker]

A módosítások előnézete valós időben, végrehajtásukkal egyidejűleg megtekinthető, de kizárólag a rendszergazdáknak jelenik meg. Ha mindenki számára láthatóvá szeretné tenni a módosításokat, kattintson a **Közzététel** gombra a stílusszerkesztőben, és hagyja jóvá a módosításokat.

![Közzététel menü][api-management-customization-toolbar-publish-form]

> Az oldal bármely más elemére vonatkozó stílusszabályok módosításához kövesse ugyanazt az eljárást, mint a fejlécnél. Kattintson az **Elem kiválasztása** lehetőségre a stílusszerkesztőben, válassza ki a kívánt elemet, majd kezdje el módosítani a képernyőn megjelenő stílusszabályok értékeit.
> 
> 

## <a name="edit-page-contents"> </a>Oldal tartalmának szerkesztése
A fejlesztői portál automatikusan létrehozott oldalakból áll, például API-k, Termékek, Alkalmazások, Problémák és kézzel írt tartalmak. Mivel egy tartalomkezelő rendszeren alapul, szükség szerint létre lehet hozni ilyen tartalmakat.

A létező tartalomoldalak listájának megtekintéséhez kattintson a **Tartalom** lehetőségre a közzétevő portál **Fejlesztői portál** menüjében.

![Tartalom kezelése][api-management-customization-manage-content]

A fejlesztői portál kezdőlapján megjelenített tartalmak szerkesztéséhez kattintson a **Kezdőlap** lehetőségre. Hajtsa végre a kívánt módosításokat, tekintse meg az előnézetet, ha szeretné, majd kattintson a **Közzététel most** parancsra, hogy mindenki számára láthatóvá tegye őket.

> A kezdőlap egy speciális elrendezést használ, amellyel megjelenítheti a szalagcímeket az oldal tetején. Ez a szalagcím nem szerkeszthető a **Tartalom** szakaszban. A szalagcím szerkesztéséhez kattintson a **Fejlesztői portál** menü **Widgetek** elemére, válassza az **Aktuális réteg** listában a **Kezdőlap** lehetőséget, majd nyissa meg a **Kiemelt szakasz** **Szalagcím** elemét. A widget tartalmai ugyanúgy szerkeszthetők, mint bármely más oldal.
> 
> 

## <a name="next-steps"> </a>Következő lépések
* Megtudhatja, hogyan szabhatja testre a fejlesztői portál oldalainak tartalmát a [fejlesztői portál sablonjainak](api-management-developer-portal-templates.md) használatával.

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-portal/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Feb17_HO1-->


