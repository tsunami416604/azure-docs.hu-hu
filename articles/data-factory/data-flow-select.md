---
title: Adatforgalom leképezése válassza az átalakítás lehetőséget
description: Azure Data Factory leképezési adatfolyam kiválasztása – átalakítás
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/08/2020
ms.openlocfilehash: 2d04de420f743e4fef4cff4bd2912559dae0886a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934177"
---
# <a name="mapping-data-flow-select-transformation"></a>Adatforgalom leképezése válassza az átalakítás lehetőséget


Használja ezt az átalakítást az oszlopok szelektivitásához (oszlopok számának csökkentése), alias-oszlopokhoz és adatfolyam-nevekhez, valamint az oszlopok átrendezéséhez.

## <a name="how-to-use-select-transformation"></a>Az átalakítás kiválasztása
Az átalakítás lehetőséget választva a teljes adatfolyamot vagy az adott adatfolyamban lévő oszlopokat, különböző neveket (aliasokat) rendelhet hozzá, majd az új neveket az adatfolyamatban később is hivatkozhat. Ez az átalakítás az önillesztési forgatókönyvek esetében hasznos. Ha önillesztést szeretne megvalósítani az ADF-adatforgalomban, egy streamet kell létrehoznia, amely az "új ág", majd közvetlenül ezt követően a "Select" átalakítás hozzáadásával történik. A stream ekkor egy új névvel fog rendelkezni, amellyel visszatérhet az eredeti streamhez, és létrehozhat egy önillesztést:

![Önálló csatlakozás](media/data-flow/selfjoin.png "Önálló csatlakozás")

A fenti ábrán az átalakító kijelölése felül van. Ez a "OrigSourceBatting" eredeti adatfolyamának aliasa. Az alatta lévő összekapcsolási átalakítóban láthatja, hogy a kiválasztott alias streamet használja a jobb oldali illesztéshez, ami lehetővé teszi, hogy ugyanarra a kulcsra hivatkozzon a belső illesztés bal & jobb oldalán.

A Select (kijelölés) lehetőséggel kiválaszthatja az adatfolyamatból az oszlopok kiválasztására szolgáló oszlopokat is. Ha például 6 oszlop van definiálva a fogadóban, de csak egy adott 3 értéket szeretne átalakítani, majd a fogadóba befolyni, akkor a Select átalakító használatával kiválaszthatja a csak a 3 értéket.

![Átalakítás kiválasztása](media/data-flow/newselect1.png "Alias kiválasztása")

## <a name="options"></a>Beállítások
* A "Select" alapértelmezett beállítása az összes bejövő oszlop belefoglalása, és az eredeti nevek megtartása. A streamet aliasként adhatja meg a Select átalakító nevének beállításával.
* Az egyes oszlopok aliasához törölje az "összes kijelölése" lehetőséget, és használja a lenti oszlop-hozzárendelést.
* Válassza az ismétlődések kihagyása lehetőséget az ismétlődő oszlopok a bemeneti vagy kimeneti metaadatokból való eltávolításához.

![Ismétlődések kihagyása](media/data-flow/select-skip-dup.png "Ismétlődések kihagyása")

* Ha úgy dönt, hogy kihagyja a duplikált elemeket, az eredmények megjelennek az ellenőrzés lapon. Az ADF megtartja az oszlop első előfordulását, és látni fogja, hogy az adott oszlop minden további előfordulása el lett távolítva a folyamatból.

> [!NOTE]
> A leképezési szabályok törléséhez nyomja le az **Alaphelyzetbe állítás** gombot.

## <a name="mapping"></a>Társítás
Alapértelmezés szerint a Select transzformáció automatikusan leképezi az összes oszlopot, amely továbbítja az összes bejövő oszlopot a kimeneten megegyező névre. A kimeneti adatfolyam neve, amely a Select Settings (beállítások) beállításban be van állítva, a stream új aliasnevét fogja meghatározni. Ha megtartja az automatikus leképezés beállítása beállítást, akkor a teljes adatfolyamot az összes oszlophoz társíthatja.

![Átalakítási szabályok kiválasztása](media/data-flow/rule2.png "Szabály alapú leképezés")

Ha aliast szeretne használni, távolítsa el, nevezze át, vagy átrendezi az oszlopokat, először ki kell kapcsolni az "automatikus leképezés" lehetőséget. Alapértelmezés szerint az "összes bemeneti oszlop" nevű alapértelmezett szabályt fogja látni. Ezt a szabályt akkor hagyhatja érvénybe, ha az összes bejövő oszlop számára engedélyezni szeretné, hogy a kimenete azonos névre legyen hozzárendelve.

Ha azonban egyéni szabályokat szeretne felvenni, kattintson a "leképezés hozzáadása" gombra. A mező-hozzárendeléssel megadhatja a leképezéshez és az aliashoz a bejövő és a kimenő oszlopnevek listáját. Válassza a "szabály alapú leképezés" lehetőséget a mintázattal egyező szabályok létrehozásához.

## <a name="rule-based-mapping"></a>Szabály alapú leképezés
Ha a szabály alapú leképezést választja, akkor a rendszer az ADF-et arra utasítja, hogy értékelje a megfelelő kifejezést a bejövő mintázat szabályainak megfelelően, és adja meg a kimenő mezők nevét. A mezők és a szabályokon alapuló leképezések tetszőleges kombinációját felveheti. A mezőneveket a forrástól érkező bejövő metaadatok alapján, az ADF futásidőben generálja a rendszer. A generált mezők neve a hibakeresés során és az adatelőnézet panelen tekinthető meg.

A minták megfeleltetésével kapcsolatos további részletek az [oszlop mintájának dokumentációjában](concepts-data-flow-column-pattern.md)olvashatók.

### <a name="use-rule-based-mapping-to-parameterize-the-select-transformation"></a>Szabály-alapú hozzárendelés használata a Select transzformáció parametrizálja
A parametrizálja az átalakítás kiválasztása szabály alapján történő leképezés használatával végezheti el. A kulcsszó ```name``` segítségével megvizsgálhatja a bejövő oszlopok nevét egy paraméterrel. Ha például egy ```mycolumn``` nevű adatáramlási paraméterrel rendelkezik, létrehozhat egy olyan kiválasztási átalakítási szabályt, amely mindig az adott oszlopnevet rendeli hozzá, ```mycolumn``` a mező nevére:

```name == $mycolumn```

## <a name="next-steps"></a>További lépések
* Miután a Select paranccsal átnevezi, átrendezi és alias oszlopokat, a fogadó [transzformáció](data-flow-sink.md) használatával az adatait egy adattárba helyezheti.
