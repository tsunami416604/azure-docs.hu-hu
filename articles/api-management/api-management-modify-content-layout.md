---
title: "A fejlesztői portál oldaltartalmának módosítása az Azure API Managementben | Microsoft Docs"
description: "Megtudhatja, hogyan szerkesztheti az oldaltartalmakat a fejlesztői portálon az Azure API Managementben."
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Oldalak tartalmának és elrendezésének módosítása a fejlesztői portálon az Azure API Managementben
A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése][modify-content-layout] (magyarázat az útmutatóban)
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles]
* [A portál által létrehozott oldalakhoz használt sablonok módosítása][portal-templates] (például: API-dokumentáció, termékek, felhasználói hitelesítés stb.)

## <a name="page-structure"></a>A fejlesztői portál oldalainak szerkezete

A fejlesztői portál egy tartalomkezelő rendszeren alapul. Az egyes oldalak elrendezése kis oldalelemek készleteire, más néven widgetekre épül:

![A fejlesztői portál oldalszerkezete][api-management-customization-widget-structure]

Az összes widget szerkeszthető. 
* Az egyes oldalak fő tartalmai a „Tartalmak” widgetben találhatók. Egy oldal tartalmának szerkesztésekor ennek a widgetnek a tartalmát szerkeszti.
* Az összes oldalelrendezési elem a többi widgetben található. Az ezeken a widgeteken végrehajtott módosítások az összes oldalra érvényesek lesznek. A továbbiakban ezeket „elrendezési widgeteknek” nevezzük.

A napi szintű oldalszerkesztés során általában csak a Tartalom widget szerkesztésére kerül sor, amely minden egyes laphoz különböző tartalommal rendelkezik.

## <a name="modify-layout-widget"></a>Elrendezési widget tartalmának módosítása

A fejlesztői portál tartalmát az Azure Portalon keresztül elérhető közzétevő portálon lehet módosítani. Az eléréséhez kattintson a **Közzétevő portál** elemre az API Management-példány szolgáltatási eszköztárából.

![Közzétevő portál][api-management-management-console]

A widget tartalmának szerkesztéséhez kattintson a bal oldali **Fejlesztői portál** menü **Widgetek** elemére. Ebben a példában a Fejléc widget tartalmát módosítjuk. Válassza ki a listából a **Fejléc** nevű widgetet.

![Widgetek fejléc][api-management-widgets-header]

A fejléc tartalmát a **Törzs** mezőben lehet szerkeszteni. Módosítsa a szöveget a kívánt értékekre, majd kattintson az oldal alján található **Mentés** gombra.

Most már a fejlesztői portál mindegyik oldalán az új fejléc fog megjelenni.

> Ha a közzétevő portálon tartózkodik, és meg szeretné nyitni a fejlesztői portált, kattintson a **Fejlesztői portál** lehetőségre a felső menüsoron.
> 
> 

## <a name="edit-page-contents"></a>Oldal tartalmának szerkesztése

A létező tartalomoldalak listájának megtekintéséhez kattintson a **Tartalom** lehetőségre a közzétevő portál **Fejlesztői portál** menüjében.

![Tartalom kezelése][api-management-customization-manage-content]

A fejlesztői portál kezdőlapján megjelenített tartalmak szerkesztéséhez kattintson a **Kezdőlap** lehetőségre. Hajtsa végre a kívánt módosításokat, tekintse meg az előnézetet, ha szeretné, majd kattintson a **Közzététel most** parancsra, hogy mindenki számára láthatóvá tegye őket.

> A kezdőlap egy speciális elrendezést használ, amellyel megjelenítheti a szalagcímeket az oldal tetején. Ez a szalagcím nem szerkeszthető a **Tartalom** szakaszban. A szalagcím szerkesztéséhez kattintson a **Fejlesztői portál** menü **Widgetek** elemére, válassza az **Aktuális réteg** listában a **Kezdőlap** lehetőséget, majd nyissa meg a **Kiemelt szakasz** **Szalagcím** elemét. A widget tartalmai ugyanúgy szerkeszthetők, mint bármely más oldal.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles]
* [A portál által létrehozott oldalakhoz használt sablonok módosítása][portal-templates] (például: API-dokumentáció, termékek, felhasználói hitelesítés stb.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
