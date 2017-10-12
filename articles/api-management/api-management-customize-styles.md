---
title: "A fejlesztői portál stílusainak testreszabása az Azure API Managementben | Microsoft Docs"
description: "Megtudhatja, hogyan módosíthatja a fejlesztői portál lapjainak stílusait az Azure API Managementben."
services: api-management
documentationcenter: 
author: vladvino
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
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>A fejlesztői portál stílusainak testreszabása az Azure API Managementben
A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése][modify-content-layout]
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles] (részletesen ebben az útmutatóban)
* [A portál által létrehozott oldalakhoz használt sablonok módosítása][portal-templates] (például: API-dokumentáció, termékek, felhasználói hitelesítés stb.)

## <a name="change-headers-styling"></a>Oldalelemek stílusának módosítása

A portál oldalain megjelenő színeket, betűtípusokat, méreteket, sorközöket és más stíluselemeket a stílusszabályok határozzák meg. 

A stílusszabályok a **fejlesztői portálon**, rendszergazdaként bejelentkezve módosíthatóak. Ehhez először nyissa meg az Azure Portalt, és kattintson a **Közzétevő portál** elemre az API Management-példány szolgáltatási eszköztárából.

![Közzétevő portál][api-management-management-console]

Ezután kattintson a **Fejlesztői portálra** a jobb felső sarokban. 

![Fejlesztői portál hivatkozása a közzétevő portálon][api-management-pp-dp-link]

A testreszabási eszköztár megnyitásához vigye az egérmutatót a testreszabási ikon fölé (vagy válassza ki azt), majd az eszköztárból válassza a „Stílusok” lehetőséget.

![Testreszabási eszköztár gombja][api-management-customization-toolbar-button]

A stílusszabályokat két fő módon szerkesztheti: áttekintheti az összes használt stílusszabály alapértelmezés szerint megjelenő listáját, és szükség szerint módosíthatja az egyes stílusokat, vagy kiválaszthatja a **Select an element on the page** (Elem kiválasztása az oldalon) beállítást, és ha az oldalon valahová kattint, kizárólag az adott elemhez tartozó stílusok fognak megjelenni.

![Testreszabás eszköztár][api-management-customization-toolbar]

Ehhez a példához kattintson az **Elem kiválasztása az oldalon** lehetőségre.  Ha ezután rámutat az egérrel az elemekre, ki lesznek emelve, ezzel jelezve, hogy kattintás esetén melyik elem stílusait kezdené el szerkeszteni. Vigye az egérmutatót a fejlécben szereplő szöveg fölé (általában itt a vállalat neve található), majd kattintson rá. A stílusszerkesztőben meg fognak jelenni a névvel és kategóriákkal rendelkező stílusszabályok. Minden szabály a kiválasztott elem egy formázási tulajdonságát jelöli. Például a fent kiválasztott fejléc szövegénél a szövegméret @font-size-h1, míg a betűtípus neve alternatívákkal @headings-font-family.

> Ha ismeri a [bootstrap][bootstrap] felépítését, ezek a szabályok valójában [LESS változók][LESS variables] a fejlesztői portál által használt bootstrap témában.
> 
> 

Módosítsa a fejléc szövegének színét. Válassza ki a **@headings-color** mező bejegyzését, és írja be a **#000000** kódot. Ez a fekete szín hexadecimális kódja. Eközben a szövegmező végén megjelenik egy színjelző négyzet. Ha rákattint a jelzőre, a színválasztó segítségével kiválaszthat egy színt.

![Színválasztó][api-management-customization-toolbar-color-picker]

A módosítások előnézete valós időben, végrehajtásukkal egyidejűleg megtekinthető, de kizárólag a rendszergazdáknak jelenik meg. Ha mindenki számára láthatóvá szeretné tenni a módosításokat, kattintson a **Közzététel** gombra a stílusszerkesztőben, és hagyja jóvá a módosításokat.

![Közzététel menü][api-management-customization-toolbar-publish-form]

> Az oldal bármely más elemére vonatkozó stílusszabályok módosításához kövesse ugyanazt az eljárást, mint a fejlécnél. Kattintson az **Elem kiválasztása az oldalon** lehetőségre a stílusszerkesztőben, válassza ki a kívánt elemet, majd kezdje el módosítani a képernyőn megjelenő stílusszabályok értékeit.
> 
> 


## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan szabhatja testre a fejlesztői portál oldalainak tartalmát a [fejlesztői portál sablonjainak](api-management-developer-portal-templates.md) használatával.

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/
