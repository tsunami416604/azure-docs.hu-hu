---
title: A sablonok segítségével az API Management fejlesztői portál testreszabása – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre az Azure API Management fejlesztői portálon sablonok használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 90084f6c4fb270c34165bf12763109d9be2398aa
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446959"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>A sablonok használatával az Azure API Management fejlesztői portál testreszabása

A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése][modify-content-layout]
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles]
* [A portál által létrehozott oldalakhoz használt sablonok módosítása] [ portal-templates] (részletesen ebben az útmutatóban)

Sablonok segítségével testre szabhatja a rendszer által létrehozott fejlesztői portál oldalainak (például API-dokumentáció, termékek, felhasználói hitelesítés stb.) tartalmát. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxist, és a egy megadott készlet honosított karakterlánc-erőforrásokat, az ikonok és a Lapvezérlők, konfigurálhatja a lapok tartalmát tetszés szerint nagy rugalmasságot biztosít.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Fejlesztői portál sablonjainak áttekintése

A sablonok szerkesztését történik a **fejlesztői portál** rendszergazdaként bejelentkezve módosíthatóak. Érheti el először nyissa meg az Azure Portalon, és kattintson az **fejlesztői portál** az API Management-példány szolgáltatási eszköztárából.

A fejlesztői portál sablonjainak eléréséhez, kattintson a Testreszabás ikonra a testreszabási menü megjelenítéséhez, majd kattintson a bal oldali **sablonok**.

![Fejlesztői portál sablonjainak][api-management-customize-menu]

A sablonok listájának több kategória kiterjedő a különböző oldalakat a fejlesztői portál sablonjainak megjelenítése. Mindegyik sablon más, de szerkesztheti őket, és a módosítások közzététele a lépések ugyanazok. A sablon szerkesztéséhez kattintson a sablon nevét.

![Fejlesztői portál sablonjainak][api-management-templates-menu]

Egy sablon kattintva megnyílik a fejlesztői portál lap, amely testre szabható a sablon által. Ebben a példában a **termékek listáját** sablon jelenik meg. A **termékek listáját** sablon meghatározza a képernyőn, a vörös téglalap jelzi. 

![Termékek sablonlista][api-management-developer-portal-templates-overview]

Egyes sablonok, például a **felhasználói profil** sablonok testreszabása a különböző részei ugyanazon az oldalon. 

![Felhasználói profil sablonok][api-management-user-profile-templates]

A szerkesztő minden fejlesztői portál sablon két részből áll, az oldal alján jelenik meg. A bal oldalon a szerkesztési panelen a sablon, valamint a jobb oldali jeleníti meg a sablon az adatmodellt. 

A sablon ablaktábla Szerkesztés a vezérlő megjelenését és viselkedését a megfelelő lapon, a fejlesztői portálon jelölések tartalmazza. A sablon a jelölés használja a [DotLiquid](http://dotliquidmarkup.org/) szintaxist. Egy népszerű szerkesztője a DotLiquid [tervezők számára DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). A sablon szerkesztése során végrehajtott módosítások megjelennek a valós idejű a böngészőben, de még nem láthatók az ügyfelek számára, amíg [mentése](#to-save-a-template) és [közzététele](#to-publish-a-template) a sablont.

![Sablon haszonkulcs][api-management-template]

A **sablon adatok** ablaktáblája tartalmaz egy útmutató, amellyel az adatmodellt az entitások, amelyek egy adott sablon használható. Ez az útmutató az élő adatokat, a fejlesztői portál jelenleg megjelenített megjelenítésével biztosít. A sablon ablaktáblán kattintson a jobb felső sarokban lévő téglalapjának bővítheti a **sablon adatok** ablaktáblán.

![Sablon adatmodell][api-management-template-data]

Az előző példában a fejlesztői portálon jelenik meg, amelyek lekérni a megjelenített adatok a két termék vannak a **sablon adatok** panelen, amint az alábbi példában látható:

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

Kódjával a **termékek listáját** sablon feldolgozza az adatokat adja meg a kívánt kimenetet az iterálás a termékek információkat és a egy hivatkozás megjeleníti a minden egyes termék a gyűjteményben. Megjegyzés: a `<search-control>` és `<page-control>` jelölőnyelvi elemeit. Ezek szabályozhatja, hogy a Keresés és a lapozófájl-a lap megjelenítéséhez. `ProductsStrings|PageTitleProducts` honosított karakterlánc hivatkozás, amely tartalmazza a `h2` az oldal a fejléc szövegét. Karakterlánc-erőforrásokat, Lapvezérlők és használható a fejlesztői portál sablonjainak ikonok listáját lásd: [API Management fejlesztői portál sablonjainak referencia](api-management-developer-portal-templates-reference.md).

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

## <a name="to-save-a-template"></a>A sablon mentése
A sablon mentéséhez kattintson a Mentés gombra a sablon szerkesztőben.

![Sablon mentése][api-management-save-template]

Amíg nem teszi közzé, amelyek mentett módosítások nem élő a fejlesztői portálon.

## <a name="to-publish-a-template"></a>A sablon közzététele
Mentett sablonok vagy egyenként, vagy minden egy helyen tehetők közzé. Egy egyéni sablon közzétételéhez kattintson a Közzététel gombra a sablon szerkesztőben.

![Sablon közzététele][api-management-publish-template]

Kattintson a **Igen** erősítse meg, és hogy a sablon a fejlesztői portál webhelyen.

![Erősítse meg közzététele][api-management-publish-template-confirm]

Az összes jelenleg közzé nem tett sablonverzióktól közzétételéhez kattintson **közzététel** a sablonok listájában. Közzé nem tett sablonok a sablon nevét a következő csillaggal van megjelölve. Ebben a példában a **termékek listáját** és **termék** sablonok, amelyek közzététele folyamatban van.

![Sablonok közzététele][api-management-publish-templates]

Kattintson a **testreszabások közzététele** megerősítéséhez.

![Erősítse meg közzététele][api-management-publish-customizations]

Újonnan közzétett sablonokat közvetlenül a fejlesztői portál hatékonyak.

## <a name="to-revert-a-template-to-the-previous-version"></a>Vissza a korábbi verzióra sablon
A visszaállítás a közzétett verziót egy olyan sablont, kattintson a sablon szerkesztőben vonható vissza.

![Sablon visszaállítása][api-management-revert-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Megerősítés][api-management-revert-template-confirm]

A korábban közzétett sablont verziója a fejlesztői portál élő a visszaállítási művelet befejeződése után.

## <a name="to-restore-a-template-to-the-default-version"></a>A sablon visszaállítása az alapértelmezett verzió
Sablonok visszaállítása az alapértelmezett verzió két lépésből áll. Először a sablonok vissza kell állítani, és ezután a visszaállított verzió közzé kell tenni.

Egy alapértelmezett verziója egyetlen sablont visszaállításához kattintson a visszaállítás a sablon szerkesztőben.

![Sablon visszaállítása][api-management-reset-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Megerősítés][api-management-reset-template-confirm]

Alapértelmezett verzióját az összes sablon visszaállításához kattintson **visszaállítása az alapértelmezett sablonok** a sablon listán.

![Sablonok visszaállítása][api-management-restore-templates]

A visszaállított sablonok majd közzé kell tenni külön-külön vagy mindet egyszerre a lépéseket követve [sablon közzétételéhez](#to-publish-a-template).

## <a name="next-steps"></a>További lépések
További információ a fejlesztői portál sablonjainak, a karakterlánc-erőforrásokat, az ikonok és a Lapvezérlők: [API Management fejlesztői portál sablonjainak referencia](api-management-developer-portal-templates-reference.md).

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







