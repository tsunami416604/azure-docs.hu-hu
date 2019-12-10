---
title: Régió rendelkezésre állása és az adattárolás
titleSuffix: Azure AD B2C
description: A régió rendelkezésre állása, az adatok tartózkodási helye és a Azure Active Directory B2C előzetes verziójú Bérlővel kapcsolatos információk.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1b09eb85df6748fed042731ac90ebbf20c65b702
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950510"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: régió rendelkezésre állása & adattárolás

A régió rendelkezésre állása és az adattárolás két nagyon különböző fogalom, amelyek eltérő módon vonatkoznak az Azure többi részétől való Azure AD B2Cra. Ez a cikk ismerteti a két fogalom közötti különbségeket, és összehasonlítja, hogyan vonatkoznak az Azure-ra és a Azure AD B2Cra.

A Azure AD B2C **általánosan elérhető világszerte** az **Egyesült Államok, Európa vagy Ázsia és a csendes-óceáni térség** **adattárolási** lehetőségével.

A [régió elérhetősége](#region-availability) arra utal, hogy a szolgáltatás hol használható.

Az [adatrezidens](#data-residency) a felhasználói adattárolási helyekre utal.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Azure AD B2C világszerte elérhető az Azure-beli nyilvános felhőben.

Ez eltér a modelltől, amelyet a legtöbb egyéb Azure-szolgáltatás követ, ami általában a *rendelkezésre állást* és az *adattárolást*is igénybe vette. Ebből az Azure- [termékek régió](https://azure.microsoft.com/regions/services/) oldalán és a [Active Directory B2C árképzési kalkulátorban](https://azure.microsoft.com/pricing/details/active-directory-b2c/)is láthat példát.

## <a name="data-residency"></a>Adattárolás helye

A Azure AD B2C Egyesült Államok, Európa vagy a Ázsia és a Csendes-óceáni térség régióban tárolja a felhasználói adattípusokat.

Az adattárolást a [Azure ad B2C bérlő létrehozásakor](active-directory-b2c-get-started.md)kiválasztott ország/régió határozza meg:

![Képernyőkép az előzetes verziójú bérlőről](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

A következő országok/régiók adatai találhatók a **Egyesült Államokban** :

> Egyesült Államok, Kanada, Costa Rica, Dominikai Köztársaság, Salvador, Guatemala, Mexikó, Panama, Puerto Rico és Trinidad & Tobago

**Európában** az alábbi országok és régiók adatai találhatók:

> Algéria, Ausztria, Azerbajdzsán, Bahrein, Fehéroroszország, Belgium, Bulgária, Horvátország, Ciprus, Cseh Köztársaság, Dánia, Egyiptom, Észtország, Finnország, Franciaország, Németország, Görögország, Magyarország, Izland, Írország, Egyesült Királyság, Izrael, Olaszország, Jordánia, Kazahsztán, Kenya, Kuvait, Lettország, Libanon, Liechtenstein, Litvánia, Luxemburg, Észak-Macedónia, Málta, Montenegró, Marokkó, Hollandia, Nigéria, Norvégia, Omán, Pakisztán, Lengyelország, Portugália, Katar, Románia, Oroszország, Szaúd-Arábia, Szerbia, Szlovákia, Szlovénia, Dél-Afrika, Spanyolország, Svédország Svájc, Tunézia, Törökország, Ukrajna, Egyesült Arab Emírségek és Egyesült Királyság.

A következő országok/régiók esetében **Ázsia és a csendes-óceáni térség** az adat:

> Afganisztán, Hongkong KKT, India, Indonézia, Japán, Korea, Malajzia, Fülöp-szigetek, Szingapúr, Sri Lanka, Tajvan és Thaiföld.

A következő országok/régiók a listához való felvételük folyamatban van. Egyelőre továbbra is használhatja a Azure AD B2Ct a fenti országok/régiók bármelyikének kiválasztásával.

> Argentína, Ausztrália, Brazília, Chile, Kolumbia, Ecuador, Irak, Új-Zéland, Paraguay, Peru, Uruguay és Venezuela.

## <a name="preview-tenant"></a>Bérlő előzetes verziója

Ha a B2C-bérlőt az Azure AD B2C's előzetes verziójának ideje alatt hozta létre, akkor valószínű, hogy a **bérlő típusa** az **előzetes verziójú bérlőt**mondja.

Ebben az esetben a bérlőt csak fejlesztési és tesztelési célokra kell használnia. Ne használjon előzetes verziójú bérlőt éles alkalmazásokhoz.

Egy előnézeti B2C-bérlőtől **nem érhető el áttelepítési útvonal** egy éles méretű B2C-bérlőre. Létre kell hoznia egy új B2C-bérlőt az éles alkalmazásokhoz.

Ismert problémák merültek fel, ha törli a B2C-bérlőt, és létrehoz egy éles méretű B2C-bérlőt ugyanazzal a tartománynévvel. *Egy másik tartománynévvel rendelkező, éles méretű B2C-bérlőt kell létrehoznia*.

![Képernyőkép az előzetes verziójú bérlőről](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)