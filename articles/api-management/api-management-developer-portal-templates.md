---
title: Az API Management fejlesztői portál testreszabása sablonok használatával
titleSuffix: Azure API Management
description: Ismerje meg, hogyan szabhatja testre az Azure API Management fejlesztői portálsablonok használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430793"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Az Azure API Management fejlesztői portál testreszabása sablonok használatával

A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése][modify-content-layout]
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles]
* [A portál által létrehozott oldalakhoz használt sablonok módosítása][portal-templates] (ez az útmutató magyarázata)

A sablonok a rendszer által létrehozott fejlesztői portállapok (például API-dokumentumok, termékek, felhasználói hitelesítés stb.) tartalmának testreszabására szolgálnak. [A DotLiquid](http://dotliquidmarkup.org/) szintaxis, valamint a honosított karakterlánc-erőforrások, ikonok és oldalvezérlők készletének használatával nagy rugalmasságot biztosít az oldalak tartalmának beállításához, ahogy azt jónak látja.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Fejlesztői portálsablonok – áttekintés

A szerkesztési sablonok a **fejlesztői portálról** történnek, miközben rendszergazdaként jelentkezik be. Ahhoz, hogy ott először nyissa meg az Azure Portalon, és kattintson a **Fejlesztői portál** az API Management-példány szolgáltatáseszköztáráról.

A fejlesztői portál sablonjainak eléréséhez kattintson a testreszabási menü bal oldalán látható testreszabási ikonra, majd a **Sablonok parancsra.**

![Fejlesztői portál sablonjai][api-management-customize-menu]

A sablonok listája a fejlesztői portál különböző lapjait lefedő sablonok több kategóriáját jeleníti meg. Minden sablon más, de a szerkesztésük és a módosítások közzétételének lépései megegyeznek. Sablon szerkesztéséhez kattintson a sablon nevére.

![Fejlesztői portál sablonjai][api-management-templates-menu]

Egy sablonra kattintva megnyílik a fejlesztői portál azon lapja, amely az adott sablon által testreszabható. Ebben a példában megjelenik a **Terméklista** sablon. A **Terméklista** sablon a képernyő piros téglalap által jelzett részét szabályozza.

![Terméklista sablon][api-management-developer-portal-templates-overview]

Egyes sablonok, például a **Felhasználói profil** sablonok, ugyanazon lap különböző részeit szabják testre.

![Felhasználói profilsablonok][api-management-user-profile-templates]

Az egyes fejlesztői portálsablonok szerkesztője két szakaszt jelenít meg az oldal alján. A bal oldali megjeleníti a sablon szerkesztőablakát, a jobb oldalon pedig a sablon adatmodelljét.

A sablonszerkesztő ablaktábla tartalmazza azt a jelölést, amely a fejlesztői portál megfelelő lapjának megjelenését és viselkedését szabályozza. A sablonban lévő jelölés a [DotLiquid szintaxist](http://dotliquidmarkup.org/) használja. A DotLiquid egyik népszerű szerkesztője a [DotLiquid a tervezők számára.](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) A sablonszerkesztés során végzett módosítások valós időben jelennek meg a böngészőben, de a sablon [mentéséig](#to-save-a-template) és [közzétételéig](#to-publish-a-template) nem láthatók az ügyfelek számára.

![Sablon jelölése][api-management-template]

A **Sablon adatablaka** útmutatást nyújt az adott sablonban használható entitások adatmodelljéhez. Ezt az útmutatót a fejlesztői portálon jelenleg megjelenített élő adatok megjelenítésével biztosítja. A sablonablaktáblák kibontásához kattintson a **Sablon adatablakának** jobb felső sarkában lévő téglalapra.

![Sablonadatmodell][api-management-template-data]

Az előző példában két olyan termék jelenik meg a fejlesztői portálon, amelyek a **Sablon adatok** ablaktáblában megjelenített adatokból származnak, ahogy az a következő példában látható:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

A **terméklista** sablonban szereplő jelölés úgy dolgozza fel az adatokat, hogy a kívánt kimenetet a termékek gyűjtésén keresztül iterálja, hogy információkat jelenítsen meg, és egy hivatkozást az egyes termékekre. Figyelje `<search-control>` `<page-control>` meg a jelölés és elemeit. Ezek szabályozzák a keresés és a lapozás vezérlőelemeinek megjelenítését az oldalon. `ProductsStrings|PageTitleProducts`Egy honosított karakterlánc-hivatkozás, amely az `h2` oldal fejlécszövegét tartalmazza. A fejlesztői portálsablonjaiban használható karakterlánc-erőforrások, lapvezérlők és ikonok listáját az [API Management fejlesztői portálsablonjainak hivatkozása tartalmazza.](api-management-developer-portal-templates-reference.md)

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Sablon mentése
Sablon mentéséhez kattintson a Mentés gombra a sablonszerkesztőben.

![Sablon mentése][api-management-save-template]

A mentett módosítások közzétételükig nem jelennek meg a fejlesztői portálon.

## <a name="to-publish-a-template"></a>Sablon közzététele
A mentett sablonok egyenként vagy együtt is közzétehetők. Egyedi sablon közzétételéhez kattintson a közzététel gombra a sablonszerkesztőben.

![Sablon közzététele][api-management-publish-template]

Kattintson **az Igen** gombra a sablon megerősítéséhez és élővé a fejlesztői portálon való élővé.

![Közzététel megerősítése][api-management-publish-template-confirm]

Az összes jelenleg közzé nem tett sablonverzió közzétételéhez kattintson a sablonok listájában a **Közzététel** gombra. A közzé nem tett sablonokat a sablon neve után csillag jelöli. Ebben a példában a **terméklista** és a **terméksablonok** közzétételre kerülnek.

![Sablonok közzététele][api-management-publish-templates]

A megerősítéshez kattintson a **Testreszabások közzététele gombra.**

![Közzététel megerősítése][api-management-publish-customizations]

Az újonnan közzétett sablonok azonnal hatályba lépnek a fejlesztői portálon.

## <a name="to-revert-a-template-to-the-previous-version"></a>Sablon visszaállítása az előző verzióra
Ha vissza szeretne állítani egy sablont az előző közzétett verzióra, kattintson a sablonszerkesztő visszaváltása elemre.

![Sablon visszaállítása][api-management-revert-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Jóváhagyás][api-management-revert-template-confirm]

A sablon korábban közzétett verziója a visszaállítási művelet befejezése után a fejlesztői portálon is megjelenik.

## <a name="to-restore-a-template-to-the-default-version"></a>Sablon visszaállítása az alapértelmezett verzióra
A sablonok visszaállítása az alapértelmezett verziójukra két lépésből áll. Először vissza kell állítani a sablonokat, majd közzé kell tenni a visszaállított verziókat.

Ha egyetlen sablont szeretne visszaállítani az alapértelmezett verzióra, kattintson a visszaállítás gombra a sablonszerkesztőben.

![Sablon visszaállítása][api-management-reset-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Jóváhagyás][api-management-reset-template-confirm]

Ha az összes sablont vissza szeretné állítani az alapértelmezett verzióra, kattintson a sablonlista **Alapértelmezett sablonok visszaállítása** gombjára.

![Sablonok visszaállítása][api-management-restore-templates]

A visszaállított sablonokat ezután egyenként vagy egyszerre kell közzétenni a [Sablon közzététele](#to-publish-a-template)című részben leírt lépéseket követve.

## <a name="next-steps"></a>További lépések
A fejlesztői portálsablonokkal, karakterlánc-erőforrásokkal, ikonokkal és lapvezérlőkkel kapcsolatos további tudnivalókért olvassa el [az API Management fejlesztői portálsablonjainak hivatkozási hivatkozását.](api-management-developer-portal-templates-reference.md)

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
