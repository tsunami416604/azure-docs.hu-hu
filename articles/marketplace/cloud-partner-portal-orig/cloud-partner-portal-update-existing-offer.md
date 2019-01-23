---
title: Egy létező ajánlat frissítése az Azure Marketplace-en |} A Microsoft Docs
description: Azt ismerteti, hogyan frissíthető egy meglévő Azure Marketplace-en ajánlat használatával a Cloud Partner portálra.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 4bf11c0a084cb0241125a1ed546f48c1c5455de3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449452"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Egy létező ajánlat frissítése az Azure Marketplace-en
==============================================

Számos különböző típusú frissítések után ez már nincs meg az élő alkalmazhat az ajánlatot.   A [Cloud Partner Portalon](https://cloudpartner.azure.com/) segítségére lehetnek annak érdekében, hogy a módosítások helyesek, beleértve a ajánlat szerkesztési számos funkcióval:

-  Új virtuális gép (VM) lemezkép-verzió hozzáadása egy meglévő Termékváltozat
-  Régiókban érhető el a Termékváltozat módosítása
-  Hozzáadás, új termékváltozatok
-  Marketplace-en metaadatainak a ajánlatok és termékváltozatok frissítése 
-  Kínál az utólagos elszámolású díjszabás frissítése
-  Szolgáltatások összehasonlítása
-  Szolgáltatásokat előzményeit

Az ajánlat vagy a Termékváltozat módosítása után azt kell tehető közzé, mielőtt a módosítások az élő esemény indításra.
Ez a cikk végigvezeti a különböző aspektusait a Virtuálisgép-ajánlat frissítése.


<a name="unpermitted-changes-to-vm-offersku"></a>Virtuálisgép-ajánlat/SKU engedett módosításai
-----------------------------------

Van néhány attribútum, amely nem módosítható, amint az ajánlat élő Azure Marketplace-en vagy Virtuálisgép-ajánlat.

-  Ajánlat azonosítója és az ajánlat Gyártóazonosítója.
-  Meglévő termékváltozatok SKU-azonosítója.
-  Adatlemez meglévő SKU-k száma.
-  Számlázási és licencelési modell módosítja a meglévő termékváltozatokra.


<a name="updating-the-vm-image-version-for-a-sku"></a>A termékváltozat a Virtuálisgép-lemezkép verziója frissítése
---------------------------------------

Az Ön ajánlatát a Termékváltozat a Virtuálisgép-rendszerképet is frissítve lett-e a további funkciók, a biztonsági javítások, stb. Ilyen esetekben a érdemes frissíteni a Virtuálisgép-rendszerképet, amely a Termékváltozat hivatkozik. A következő lépések használatával frissíteni egy Virtuálisgép-rendszerképet. 

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2.  A **minden ajánlat**, keresse meg az ajánlat, amelyet szeretne frissíteni.
3.  Alatt a **termékváltozatok** képernyő, kattintson a termékváltozat, amelynek a virtuális gép rendszerkép, hogy frissíteni szeretné.
4.  A **lemez verziója**, kattintson a **+ új lemez verziója** egy új Virtuálisgép-lemezkép hozzáadása.
5.  Adja meg az új Virtuálisgép-rendszerképek **lemez verziója**. A lemez verziója szükséges a [sémantická verze](http://semver.org/) formátumban. Verziók az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük. Győződjön meg arról, hogy, adja meg az új verzió nagyobb, mint a korábbi verziók, ellenkező esetben az új verziószámmal nem fog megjelenni az Azure Portalon, illetve az Azure Marketplace-en, tegye közzé újra.
6.  A **OS VHD URL-cím**, adja meg az operációs rendszer virtuális Merevlemeze számára létrehozott közös hozzáférésű jogosultságkód (SAS) URI-t. Vegye figyelembe, hogy az adatlemezek száma a Termékváltozat különböző verziói között nem módosítható. Ha a korábbi verziók adatlemezek konfigurálva volt, az új verzió is rendelkeznie kell konfigurált adatlemezét.
7.  Kattintson a **közzététel** felhőplatformos termékeiért lekérni a új virtuális gép verzióját, az Azure Marketplace-en és az Azure Portalon válassza a közzététel munkafolyamat.


<a name="changing-regions-a-sku-is-available-in"></a>Régió módosítása a Termékváltozat érhető el
--------------------------------------

Idővel előfordulhat, hogy szeretné az ajánlat/SKU több régióban elérhetővé.
Azt is megteheti érdemes leállítani az ajánlat/SKU támogatása egy adott régióban.
Ezek a módosítások végrehajtásához kövesse az alábbi lépéseket.

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2.  A **minden ajánlat** keresse meg az ajánlat, amelyet szeretne frissíteni.
3.  Alatt a **termékváltozatok** alkotnak, kattintson a termékváltozat esetén, amelyet szeretne frissíteni, amelynek régiók rendelkezésre állása.
4.  Kattintson a a **kiválasztása országok** gomb alatt a **ország/régió rendelkezésre állási** mező.
5.  Az előugró régiónkénti elérhetőség régiók hozzáadása/eltávolítása a azt szeretné, hogy ez a termékváltozat.
6.  Kattintson a **közzététel** felhőplatformos termékeiért a közzététel munkafolyamat frissíteni az SKU-régiók rendelkezésre állása.

A Termékváltozat egy új régióban elérhetővé van folyamatban, létre kell keresztül adott régió díjszabás megadásának lehetőségét a **díjszabási adatok exportálása** funkciót. Egy régiót vissza után előtt ad hozzá, ha nem lehet frissíteni, a díjszabás, mivel a díjszabási használata nem engedélyezett.


<a name="adding-a-new-sku"></a>Egy új Termékváltozatban hozzáadása
----------------

Ahhoz, hogy egy új Termékváltozatban érhető el a létező ajánlat, kövesse az alábbi lépéseket.

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2.  A **minden ajánlat** keresse meg az ajánlat, amelyet szeretne frissíteni.
3.  Alatt a **termékváltozatok** alkotnak, kattintson a **adja hozzá az új Termékváltozat** , és adja meg egy **SKU-azonosítója** az előugró.
4.  Kövesse a részletes lépések a többi [közzététele egy virtuális gépet az Azure piactéren](./cloud-partner-portal-publish-virtual-machine.md).
5.  Kattintson a **közzététel** felhőplatformos termékeiért az új Termékváltozat az élő esemény indításra van a közzététel a munkafolyamatot.


<a name="updating-offer-marketplace-metadata"></a>Az ajánlat marketplace metaadatok frissítésekor.
------------------------------------

Előfordulhat, hogy a forgatókönyvek, ahol frissítenie kell az ajánlat például frissítheti a vállalati emblémát, és így tovább társított marketplace metaadatokat. Az alábbi lépések segítségével frissíteni az ajánlatot metaadatokat.

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2.  A **minden ajánlat** keresse meg az ajánlat, amelyet szeretne frissíteni.
3.  Ugrás a **Marketplace** alkotnak, és kövesse az utasításokat követve végezze el a módosításokat.
4.  Kattintson a **közzététel** felhőplatformos termékeiért a módosításokat az élő esemény indításra van a közzététel a munkafolyamatot.


<a name="updating-pricing-on-published-offers"></a>Közzétett ajánlatokat Tarifacsomagjának frissítése
------------------------------------

Miután az utólagos számlázású közzé van téve, egy meglévő Termékváltozat ára nem növelhető, de létrehozhat egy új Termékváltozatban ugyanazt az ajánlatnak, törölje a régi Termékváltozat, és majd közzé az ajánlatot még. Azt is lehetővé teszi, hogy csökkentse a díjak a már közzétett ajánlatok. Csökkentheti az ajánlat ár:

1.  Kattintson az SKU-t, amelynek díjszabása csökkentése érdekében.
2.  Ha megadta, 1 x 1 díjszabását grafikus felhasználói Felülettel, az ár közvetlenül a felhasználói felületen módosíthatja. Ha beállította az importálási/exportálási számolótábla keresztül díjszabás, csak csökkentheti az importálási/exportálási funkciójával árak.
3.  Kattintson a **Save** (Mentés) gombra.
4.  Tegye közzé újra az ajánlat és az élő esemény indításra.

A díjszabás az új ügyfelek számára látható lesz, miután teljesen működés a webhelyen, és új ügyfelek fog kezdem meg új csökkent.

A meglévő ügyfelek az árcsökkenést meg fognak jelenni visszamenőlegesen a Start a számlázási ciklus során, ami az árcsökkenést hatékony vált.
Ha rendelkezik már lett számlázásra a ciklus során, ami árcsökkenés történt, visszatérítés kapja meg, hogy biztosítsák a csökkent díj a következő elszámolási időszakban.


<a name="simplified-currency-pricing"></a>Egyszerűsített pénznem díjszabása
---------------------------

2018 szeptember kezdődően új szakasz nevű **pénznem díjszabás egyszerűsített** lenne az Ön számára láthatók. A Microsoft Azure Marketplace-en üzleti van egyszerűsítésével világszerte több kiszámítható díjszabás és a gyűjtemény ügyfelei engedélyezésével. Ez egyszerűsítheti érik el az ügyfelek feléjük azt pénznemek számának csökkentésével.

Az új szakasz ezen új pénznemekben díjszabás vesz igénybe. Után minden ügyfél ezen új elszámolási pénznemekben át lettek telepítve, az eredeti díjszabási szakaszban kivonjuk a forgalomból, és csak a "Pénznem díjszabás egyszerűsített" szakaszban marad.

Amíg nem módosul a 2018. November 1. viselkedésmintáit beállítása egy új ár régiókra vonatkozó elszámolás pénznem kell. Nem lehet növelheti a régiók viselkedésmintáit nem módosul az elszámolás pénznem díja. Viselkedésmintáit a pénznem módosul a régiók a következők:

| Ország                  | ISO2 kódot | Egyszerűsített Számlázás pénzneme |
|--------------------------|-----------|-----------------------------|
| **Algeria**              | DZ        | USD                         |
| **Argentína**            | AR        | USD                         |
| **Bahrein**              | BH        | USD                         |
| **Belarus**              | BY        | USD                         |
| **Brazília**               | BR        | BRL                         |
| **Bulgária**             | BG        | EUR                         |
| **Chile**                | CL        | USD                         |
| **Colombia**             | CO        | USD                         |
| **Costa Rica**           | CR        | USD                         |
| **Horvátország**              | HR        | EUR                         |
| **Cseh Köztársaság**       | CZ        | EUR                         |
| **Egyiptom**                | EG        | USD                         |
| **Guatemala**            | GT        | USD                         |
| **Hongkong**            | HK        | USD                         |
| **Magyarország**              | HU        | EUR                         |
| **Izland**              | IS        | EUR                         |
| **Indonézia**            | ID (Azonosító)        | USD                         |
| **Izrael**               | IL        | USD                         |
| **Jordánia**               | JO        | USD                         |
| **Kazahsztán**           | KZ        | USD                         |
| **Kenya**                | KE        | USD                         |
| **Kuvait**               | KW        | USD                         |
| **Liechtenstein**        | LI        | EUR                         |
| **Macedónia (V.J.K.)**     | MK        | USD                         |
| **Malajzia**             | MY        | USD                         |
| **Mexikó**               | MX        | USD                         |
| **Montenegró**           | ME        | USD                         |
| **Marokkó**              | MA        | USD                         |
| **Nigéria**              | NG        | USD                         |
| **Oman**                 | OM        | USD                         |
| **Pakisztán**             | PK        | USD                         |
| **Paraguay**             | PY        | USD                         |
| **Peru**                 | PE        | USD                         |
| **Fülöp-szigetek**          | PH        | USD                         |
| **Lengyelország\***             | PL        | EUR                         |
| **Qatar**                | QA        | USD                         |
| **Románia**              | RO        | EUR                         |
| **Saudi Arabia**         | SA        | USD                         |
| **Serbia**               | RS        | USD                         |
| **Szingapúr**            | SG        | USD                         |
| **Dél-Afrika**         | ZA        | USD                         |
| **Thaiföld**             | TH        | USD                         |
| **Trinidad és Tobago**  | TT        | USD                         |
| **Tunézia**              | TN        | USD                         |
| **Törökország**               | TR        | USD                         |
| **Ukrajna**              | UA        | USD                         |
| **Egyesült Arab Emírségek** | AE        | USD                         |
| **Uruguay**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Az ajánlat közzététele az API-k használatakor láthatóvá teheti a belül az ajánlat nevű új szakasz **JSON**. Ez akkor lehet feliratozva `virtualMachinePricingV2` vagy `monthlyPricingV2`ajánlat típusától függően.

Ha megkerüléséhez bármilyen kérdése van, lépjen kapcsolatba az Azure piactér támogatási.


<a name="compare-feature"></a>A szolgáltatás összehasonlítása
---------------

Amikor módosítja a már közzétett ajánlat, használhatja a *összehasonlítása* funkció a végzett módosítások naplózását. Hasonlítsa össze használatához:

1.  A szerkesztési folyamat bármely mozzanata, kattintson a **összehasonlítása** gomb az ajánlatban.

2.  Marketing eszközök és metaadatok verzióinak egymás mellett tekintheti meg.


<a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei
-----------------------------

Közzétételi tevékenységi előzményeket megtekintéséhez kattintson a a **előzmények** Cloud Partner portálra, a bal oldali navigációs fülre. Itt lesz, amely az Azure Marketplace-ajánlat teljes élettartama során került sor időbélyegzővel műveletek megtekintéséhez.
