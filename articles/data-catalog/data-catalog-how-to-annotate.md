---
title: "Adatforrások ellátása megjegyzésekkel hogyan |} Microsoft Docs"
description: "A cikk kiemelés az Azure Data Catalog, beleértve a rövid nevek, címkéket, leírásokat és szakértők az adategységeket módjáról útmutató."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 4518fc126c717cc79ca7950c0b1ddcd9f1d8c7d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-annotate-data-sources"></a>Adatforrások ellátása megjegyzésekkel
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a rendszer a vállalati adatforrások felderítési funkcionál. Más szóval a Data Catalog legyen útmutatás nyújtása a felhasználók felderítése, megismeréséhez és használatához adatforrások, és segítséget nyújt a szervezetek kihasználása érdekében a meglévő adatok. Ha egy adatforrás regisztrálva lett a Data Catalog, a metaadatai másolja, és a szolgáltatás indexelve, de a szövegegység nincs nem végződhet. A Data Catalog lehetővé teszi, hogy a felhasználók a saját leíró metaadatok – például a leírásokat és címkék – kiegészítik az adatforrásból a metaadatokat, és ellenőrizze az adatforrás több ember több érthető.

## <a name="annotation-and-crowdsourcing"></a>A jegyzet és közösségi hozzáadása
Mindenki rendelkezik véleményt. És Ez azért hasznos.
A Data Catalog felismeri, hogy különböző felhasználók rendelkeznek-e a vállalati adatforrások különböző szempontok szerint, és, hogy a szempontok mindegyikének igen hasznos lehet. Vegye figyelembe a következő forgatókönyvet:

* A rendszergazda tudja, hogy a szolgáltatásiszint-szerződés a kiszolgálókat vagy szolgáltatásokat üzemeltető az adatforrás.
* Az adatbázis-rendszergazda tudja, hogy az egyes adatbázisok és a megengedett ETL feldolgozási windows biztonsági mentés ütemezése.
* A rendszer tulajdonosának tudja, hogy a folyamat a felhasználók számára, hogy hozzáférést igényelhessen az adatforrás.
* Az adatok steward tudja, hogyan az eszközök és az adatforrás attribútumok hozzárendelését a vállalati adatok modell.
* Az elemző tudja az adatok használatáról az ő támogatja az üzleti folyamatok keretében.

A szempontok egy értékes, és a Data Catalog egy közösségi megosztási elvének metaadatot, amely lehetővé teszi, hogy a rögzített és biztosítja a regisztrált adatforrások átfogó képet mindegyiknél használja. A Data Catalog-portált használja, minden felhasználóhoz adhat hozzá és szerkeszthet saját megjegyzéseit, hogy más felhasználók által biztosított jegyzetek megtekintése közben.

## <a name="different-types-of-annotations"></a>A jegyzetek különböző típusaihoz
A Data Catalog a következő jegyzetek-típusokat támogatja:

| Megjegyzés | Megjegyzések |
| --- | --- |
| Rövid név |Rövid nevét meg kell adnia az adatok eszköz, szinten teszik az adategységeket több könnyen érthető. Rövid nevek lesz a leghasznosabb, ha az alapul szolgáló objektum neve mégis fontos kontextusinformációkat, rövidített vagy más módon nem értelmezhető, a felhasználók számára. |
| Leírás |Leírások lehet biztosítani a adategységet és az attribútum / oszlop szintek. Leírások a felhasználó leíró szabad formátumú rövid szöveges jegyzetek perspektíva az adategységet vagy használatát. |
| Címkék (felhasználói címkék) |Címkék lehet biztosítani a adategységet és az attribútum / oszlop szintek. Felhasználói címke található a felhasználó által definiált címkékhez, amelyek segítségével adategységeket vagy attribútumok kategorizálását. |
| Címkék (szószedet címkék) |Címkék lehet biztosítani a adategységet és az attribútum / oszlop szintek. Szószedet címke található a központilag meghatározott Szójegyzék adategységeket vagy üzleti taxonómia használatával attribútumok kategorizálásának használható. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása) |
| Szakértői |Szakértői meg kell adnia az adatok eszköz szintjén. Szakértők azonosítása felhasználók vagy csoportok szakértői meglátásokkal rendelkező adatokat is szolgáljanak, a felhasználók számára a regisztrált adatforrások felfedezése kapcsolattartási és kérdései merülnek fel, hogy a meglévő megjegyzéseknek nem válaszol. |
| Hozzáférés kérése |Kérelem adatok eléréséhez meg kell adnia az adatok eszköz szintjén. Ez az információ van a felhasználók számára egy adatforrásban, amely még nincs hozzáférési engedélye a felderíteni. Felhasználók adhat meg a felhasználó vagy csoport számára engedélyezi a hozzáférést, az URL-címe a folyamat vagy az eszköz hozzáférést igénylő felhasználók e-mail címét, vagy maga a folyamat szöveget adja meg. |
| Dokumentáció |Dokumentáció meg kell adnia az adatok eszköz szintjén. Eszköz dokumentációja rich text formátumú adatokat tartalmazó hivatkozásokat és a képeket, és amely tud biztosítani a továbbított adatokat nem leírásokat és címkék keresztül is. |

## <a name="annotating-multiple-assets"></a>Több eszköz ellátása megjegyzésekkel
A Data Catalog-portál lehetőséget választva egyszerre több adategységet, a felhasználók fűzhet egyetlen művelettel az összes kijelölt eszközökhöz. Jegyzetek az összes kijelölt eszközökhöz, így könnyen válassza ki, és adjon meg konzisztens leírását és címkék és szakértők kapcsolódó adategységeket fog vonatkozni.

> [!NOTE]
> Címkék és szakértők is is kell megadni, ha a Data Catalog adatokkal regisztrálásakor adategységeket forrás-regisztráló eszközzel.
>
>

Ha több táblák és nézetek, csak az oszlopok, hogy az összes kijelölt adatok eszközök rendelkezik közös kiválasztásával jelenik meg a Data Catalog-portál. Ez lehetővé teszi a felhasználók számára adja meg a címkéket és az összes oszlop leírásainak minden kiválasztott eszköz ugyanazzal a névvel.

## <a name="annotations-and-discovery"></a>Jegyzetek és felderítés
A Data Catalog search-index a regisztrálás során az adatforrásból metaadatokat ad hozzá, mint a felhasználó által megadott metaadatok is indexelt. Ez azt jelenti, hogy nem csak jegyzetek megkönnyítik a felhasználók számára, hogy ezek a felderítési adatokat, jegyzeteket is megkönnyítik a felhasználók számára a megjegyzésekkel ellátott adategységeket történő kereséssel célszerű azokat a fogalmakat.

## <a name="summary"></a>Összefoglalás
Egy adatforrás regisztrálása a Data Catalog lehetővé teszi, hogy az adatok felderíthető szerkezeti és leíró metaadatok másolja az adatforrás a katalógus szolgáltatásba. Miután egy adatforrás regisztrálva van, a felhasználók megadhatják, hogy könnyebben megtalálhatóvá és értelmezhetővé a belül a Data Catalog-portál jegyzetek.

## <a name="see-also"></a>Lásd még:
* [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóprogram lépésről lépésre való használatáról további információkat az adatforrások ellátása megjegyzésekkel.
