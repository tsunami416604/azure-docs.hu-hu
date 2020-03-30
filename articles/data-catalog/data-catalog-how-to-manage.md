---
title: Adateszközök kezelése az Azure Data Catalogban
description: A cikk bemutatja, hogyan szabályozhatja az Azure Data Catalogban regisztrált adateszközök láthatóságát és tulajdonjogát.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736347"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Adateszközök kezelése az Azure Data Catalogban
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog adatforrás-felderítésre lett tervezve, így könnyen felfedezheti és megismerheti az elemzés elvégzéséhez és a döntések meghozatalához szükséges adatforrásokat. Ezek a felderítési képességek akkor gyakorolják a legnagyobb hatást, ha Ön és más felhasználók megtalálják és megértik a rendelkezésre álló adatforrások legszélesebb körét. Ezeket az elemeket szem előtt tartva a Data Catalog alapértelmezett viselkedése az, hogy az összes regisztrált adatforrás látható legyen és az összes katalógusfelhasználó számára észlelhető legyen.

A Data Catalog nem ad hozzáférést magához az adathoz. Az adathozzáférést az adatforrás tulajdonosa szabályozza. A Data Catalog segítségével felderítheti az adatforrásokat, és megtekintheti a katalógusban regisztrált forrásokhoz kapcsolódó metaadatokat.

Előfordulhatnak azonban olyan helyzetek, amikor az adatforrások csak bizonyos felhasználók vagy meghatározott csoportok tagjai számára legyenek láthatók. Ilyen esetekben a felhasználók a katalóguson belül átvehetik a regisztrált adateszközök tulajdonjogát, majd szabályozhatják a tulajdonukban lévő eszközök láthatóságát.

> [!NOTE]
> A jelen cikkben ismertetett funkciók csak az Azure Data Catalog standard kiadásában érhetők el. Az ingyenes kiadás nem biztosít képességeket a tulajdonjog és az adateszközök láthatóságának korlátozása.
>
>

## <a name="manage-ownership-of-data-assets"></a>Adateszközök tulajdonjogának kezelése
Alapértelmezés szerint a Data Catalogban regisztrált adateszközök nem tulajdonoltak. A katalógushoz hozzáféréssel rendelkező bármely felhasználó felfedezheti és jegyzetekkel láthatja el ezeket az eszközöket. A felhasználók saját tulajdonba vehetik a nem birtokolt adateszközöket, majd korlátozhatják a tulajdonukban lévő eszközök láthatóságát.

Ha a Data Catalog ban egy adateszköz a tulajdonban van, csak a tulajdonosok által engedélyezett felhasználók fedezhetik fel az eszközt, és tekinthetik meg a metaadatait, és csak a tulajdonosok törölhetik az eszközt a katalógusból.

> [!NOTE]
> Az adatkatalógusban való tulajdonjog csak a katalógusban tárolt metaadatokat érinti. A tulajdonjog nem biztosít semmilyen engedélyt az alapul szolgáló adatforrásra.
>
>

### <a name="take-ownership"></a>Saját tulajdonba vétel
A felhasználók az adatkatalógus-portál Tulajdonjogát a **Tulajdonosba vétel** lehetőség kiválasztásával vehetik át. A nem birtokolt adateszköz tulajdonjogának átvételéhez nincs szükség különleges engedélyekre. Bármely felhasználó saját tulajdonba vehet egy nem birtokolt adateszköz.

### <a name="add-owners-and-co-owners"></a>Tulajdonosok és társtulajdonosok hozzáadása
Ha egy adateszköz már a tulajdonában van, más felhasználók nem vehetik át egyszerűen a tulajdonjogot. Ezeket egy meglévő tulajdonosnak társtulajdonosként kell hozzáadnia. Bármely tulajdonos további felhasználókat vagy biztonsági csoportokat adhat hozzá társtulajdonosként.

> [!NOTE]
> Ajánlott eljárás, hogy legalább két személy tulajdonosaként bármely tulajdonában lévő adateszköz.
>
>

### <a name="remove-owners"></a>Tulajdonosok eltávolítása
Csakúgy, mint bármely eszköz tulajdonosadhat társtulajdonosok, minden eszköz tulajdonosa eltávolíthatja a társtulajdonos.

Az eszköz tulajdonosa, aki eltávolítja magát, mint a tulajdonos már nem kezelheti az eszközt. Ha az eszköz tulajdonosa eltávolítja magát, mint tulajdonost, és nincsenek más társtulajdonosok, az eszköz visszaáll egy nem birtokolt államba.

## <a name="control-visibility"></a>Láthatóság szabályozása
Az adateszközök tulajdonosai szabályozhatják a tulajdonukban lévő adateszközök láthatóságát. A láthatóság alapértelmezettként való korlátozása érdekében, ahol az összes data catalog-felhasználó felfedezheti és megtekintheti az adateszközt, az eszköz tulajdonosa átválthat a **Mindenki** beállításról a **Tulajdonosok & Ezek** a felhasználók elemre az eszköz tulajdonságaiban. A tulajdonosok ezután hozzáadhatnak bizonyos felhasználókat és biztonsági csoportokat.

> [!NOTE]
> Amikor csak lehetséges, az eszköz tulajdonjogára és láthatóságára vonatkozó engedélyeket a biztonsági csoportokhoz kell rendelni, nem pedig az egyes felhasználókhoz.
>
>

## <a name="catalog-administrators"></a>Katalógusrendszergazdák
A Data Catalog-rendszergazdák implicit módon társtulajdonosai a katalógusban lévő összes eszköznek. Az eszköztulajdonosok nem távolíthatják el a rendszergazdák láthatóságát, és a rendszergazdák kezelhetik a katalógusban lévő összes adateszköz tulajdonjogát és láthatóságát.

## <a name="summary"></a>Összefoglalás
A Data Catalog crowdsourcing modell metaadatok és az adateszközök felderítése lehetővé teszi, hogy minden katalógus felhasználók hozzájárulnak, és fedezze fel. A Data Catalog standard kiadása tulajdonjogi és felügyeleti célokra készült, hogy korlátozza az egyes adateszközök láthatóságát és használatát.
