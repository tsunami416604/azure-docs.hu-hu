---
title: "Az API Management developer portálon sablonok testreszabása-Azure |} Microsoft Docs"
description: "Ismerje meg, hogyan szabhatja testre a sablonok használatával Azure API Management fejlesztői portálján."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 8a2211e76150a90e4e10d79fd527decd3cbcc220
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Az Azure API Management developer portálon sablonok testreszabása

A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése][modify-content-layout]
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles]
* [Módosíthatja a sablonokat a portál által létrehozott lapok] [ portal-templates] (Ez az útmutató alapján)

Sablonok segítségével testre szabhatja a tartalmát a rendszer fejlesztői portál lapjai (pl. API docs, termékek, felhasználói hitelesítés, stb.). Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxis és a megadott készlete a honosított karakterlánc-erőforrások, ikonok és Lapvezérlők, konfigurálja a tartalmat, a lapok, ahogyan szeretné nagyfokú rugalmasságot biztosítanak.

## <a name="developer-portal-templates-overview"></a>Fejlesztői portál sablonok – áttekintés
A sablonok szerkesztése történik a **fejlesztői portálján** közben rendszergazdaként naplózva. Nincs először nyissa meg az Azure portálon, és kattintson a **Publisher portal** az API Management-példány service eszköztárból.

![Közzétevő portál][api-management-management-console]

Ezután kattintson a **Fejlesztői portálra** a jobb felső sarokban. 

![Fejlesztői portál menüjében][api-management-developer-portal-menu]

A fejlesztői portál sablonok eléréséhez kattintson a Testreszabás ikon megjelenítése a testreszabási menü, majd kattintson a bal oldali **sablonok**.

![Fejlesztői portál sablonok][api-management-customize-menu]

A sablonok listájának számos modulkategória közül a fejlesztői portálra különböző oldalain kiterjedő sablonok jeleníti meg. Minden sablon különböző, de szerkesztheti azokat, és a változtatásokat lépései megegyeznek. A sablon szerkesztéséhez kattintson a sablon nevét.

![Fejlesztői portál sablonok][api-management-templates-menu]

Kattintson egy sablon megnyitná a fejlesztői portálon, amely testre szabható, hogy a sablon alapján. Ebben a példában a **termékek listáját** sablon jelenik meg. A **termékek listáját** sablon meghatározza a képernyőn, a vörös téglalap jelölik. 

![Termékek sablon][api-management-developer-portal-templates-overview]

Egyes sablonok, például a **felhasználói profil** sablonok, testreszabása a különböző részei ugyanazon az oldalon. 

![Felhasználói profil sablonok][api-management-user-profile-templates]

Minden egyes fejlesztői portálsablon szerkesztője két részből áll, az oldal alján megjelenik. A bal oldalon a sablon a szerkesztési panelen, valamint a jobb oldali jeleníti meg a sablon az adatmodellt. 

A sablonszerkesztési ablaktábla a kód, amely szabályozza a Megjelenés és viselkedés a fejlesztői portálra a megfelelő lap tartalmaz. A sablon a jelölés során használja a [DotLiquid](http://dotliquidmarkup.org/) szintaxist. Egy népszerű szerkesztője a DotLiquid [tervezőknek DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). A sablon szerkesztése során végrehajtott módosítások jelennek meg valós időben a böngészőben, de azok nem láthatók az ügyfelek számára, amíg [mentése](#to-save-a-template) és [közzététele](#to-publish-a-template) a sablont.

![Sablon markup][api-management-template]

A **sablon adatok** ablaktáblán az adatokat az adatmodellbe való biztosít az entitásokat, amelyek egy megadott sablont használható. Ez az útmutató a jelenleg megjelenített a fejlesztői portálra élő adatok megjelenítésével biztosít. A sablon ablaktáblák bővítheti a négyszög jobb felső sarkában kattintson a **sablon adatok** ablaktáblán.

![Sablon adatmodell][api-management-template-data]

Az előző példában szereplő nincsenek két termékek jelenik meg a fejlesztői portálon megjelenő adatok lettek beolvasva a **sablon adatok** ablaktáblán, a következő példában látható módon.

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

A kód a **termékek listáját** sablon dolgozza fel a termékek számára megjelenített információk és hivatkozást minden egyes termék iteráció biztosítani a kívánt kimeneti adatokat. Megjegyzés: a `<search-control>` és `<page-control>` elemeinek a jelölés során. Ezek szabályozza, hogy a Keresés és a lap lapozás megjelenítését. `ProductsStrings|PageTitleProducts`honosított karakterlánc hivatkozás, amely tartalmazza a `h2` a fejléc szövege lap. Erőforrásait, Lapvezérlők és fejlesztői portál sablonok használható ikonok listáját lásd: [API Management fejlesztői portál sablonok referenciája](api-management-developer-portal-templates-reference.md).

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
A sablon mentéséhez kattintson a Mentés gombra a sablon-szerkesztőben.

![Sablon mentése][api-management-save-template]

A módosítások mentése nincsenek élő a fejlesztői portálra, amíg azok közzé lettek téve.

## <a name="to-publish-a-template"></a>A sablon közzététele
Mentett sablonok külön-külön vagy együtt tehetők közzé. Közzététele egy egyéni sablont, kattintson a közzététel a sablon-szerkesztőben.

![Sablon közzététele][api-management-publish-template]

Kattintson a **Igen** erősítse meg, és hogy a sablon megtalálható a fejlesztői portálján.

![Erősítse meg közzététele][api-management-publish-template-confirm]

A minden jelenleg közzé nem tett verzióival való közzétételéhez kattintson **közzététel** sablonok listájában. Közzé nem tett sablonok a sablonnevet követő csillag jelölik. Ebben a példában a **termékek listáját** és **termék** sablonok frissítése folyamatban van.

![Sablonok közzététele][api-management-publish-templates]

Kattintson a **testreszabások közzététele** megerősítéséhez.

![Erősítse meg közzététele][api-management-publish-customizations]

Újonnan közzétett sablonokat is azonnal hatékonyan a fejlesztői portálján.

## <a name="to-revert-a-template-to-the-previous-version"></a>Vissza az előző verzió sablon létrehozása
Vissza az előző közzétett verzió kívánt sablont, kattintson a sablon szerkesztőben visszaállításához.

![Sablon visszaállítása][api-management-revert-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Erősítse meg][api-management-revert-template-confirm]

A korábban közzétett sablont verziója élő a fejlesztői portálra a visszaállítási művelet végrehajtása után.

## <a name="to-restore-a-template-to-the-default-version"></a>A sablon visszaállítása az alapértelmezett verzió
Sablonok visszaállítása az alapértelmezett verzió két lépésből áll. Először a sablonok vissza kell állítani, és ezután a visszaállított verziók közzé kell tenni.

Ugyanazt a sablont az alapértelmezett verzió visszaállításához kattintson a visszaállítás sablon-szerkesztőben.

![Sablon visszaállítása][api-management-reset-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Erősítse meg][api-management-reset-template-confirm]

A alapértelmezett verzióját az összes sablon visszaállításához kattintson **visszaállítása az alapértelmezett sablonok** a sablon listában.

![Állítsa vissza a sablonok][api-management-restore-templates]

A visszaállított sablonok majd közzé kell tegye a külön-külön és egyszerre lépéseit [a sablon közzététele](#to-publish-a-template).

## <a name="next-steps"></a>Következő lépések
A fejlesztői portál sablonok, erőforrásait, ikonok és Lapvezérlők hivatkozás információkért lásd: [API Management fejlesztői portál sablonok referenciája](api-management-developer-portal-templates-reference.md).

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







