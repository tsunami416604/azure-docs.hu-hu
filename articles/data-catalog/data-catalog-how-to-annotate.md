---
title: Hogyan lehet Azure Data Catalog az adatforrások ellátása megjegyzésekkel
description: Útmutató a cikk az Azure Data Catalog, beleértve a rövid, érthető nevekkel, címkéket, leírásokat és szakértői adategységek dekorálása kiemelése.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: a6751a2cbb2a12d0d0ffe1eb0707ad4e8bc45ed8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053728"
---
# <a name="how-to-annotate-data-sources"></a>Adatforrások ellátása megjegyzésekkel
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely egy regisztrációs és felderítőrendszert biztosít a vállalati adatforrások. Más szóval a Data Catalog szól segít az embereknek felderíteni, ismertetése és adatforrások és segítve a szervezeteket kihasználása a meglévő adatok használatával. Ha egy adatforrás regisztrálva van a Data Catalog, másolt és a szolgáltatás indexeli metaadatait, de a történetet ott nem végződhet. A Data Catalog lehetővé teszi, hogy a felhasználók a saját leíró metaadatok – például a leírások és címkék – az adatforrásból kinyert metaadatok kiegészítése, és végezze el az adatforrás érhetőbbé több ember számára.

## <a name="annotation-and-crowdsourcing"></a>Jegyzet és közösségi hozzáadása
A vélemény mindenki rendelkezik. És Ez azért hasznos.
A Data Catalog felismeri, hogy különböző felhasználók rendelkeznek-e a vállalati adatforrások programban különböző szempontok szerint, és ezek perspektívák mindegyike, hogy értékes. Vegye figyelembe az alábbi forgatókönyvet:

* A rendszergazda tudja a szolgáltatásiszint-szerződés szolgáltatások vagy kiszolgálók, amelyeken az adatforrás.
* Az adatbázis-rendszergazda tudja, hogy minden egyes adatbázishoz, és az ETL megengedett feldolgozási windows biztonsági mentési ütemezését.
* A rendszer tulajdonosa tudja, hogy a folyamat a felhasználók számára az adatforráshoz való hozzáférés kérése.
* Az adatgazdai tudja, hogyan az eszközök és az adatforrás attribútumok leképezése a vállalati adatok modell.
* Az elemző tudja, hogy az adatok használatának módja a következőben az ő támogatja az üzleti folyamatok keretében.

A szempontok mindegyikének értékes, és a Data Catalog egy közösségi módszert használ, amely lehetővé teszi, hogy mindegyikhez rögzített és a egy regisztrált adatforrások átfogó képet biztosítanak a metaadatok. A Data Catalog-portál használata esetén minden egyes felhasználó is hozzáadhatnak és szerkeszthetnek a saját jegyzetek képes arra, hogy más felhasználók által biztosított jegyzetek megtekintése közben.

## <a name="different-types-of-annotations"></a>Különböző típusú jegyzetek
A Data Catalog jegyzetek a következő típusokat támogatja:

| Megjegyzés | Megjegyzések |
| --- | --- |
| Felhasználóbarát név |Rövid nevek, az eszköz szinten, hogy az adategységeket, könnyen érthető lehet biztosítani. Rövid nevek legtöbb hasznosak, ha az alapul szolgáló objektum neve érthetetlennek, rövidített vagy más módon nem értelmezhető, a felhasználók számára. |
| Leírás |Leírását lehet megadni a adategységet és attribútum / oszlop szinteket. Leírások, amelyek ismertetik a felhasználó rövid szöveges szabad formátumú jegyzetek perspektíva az adategységhez, vagy annak használatára. |
| Címkék (a felhasználói címkék) |Címkék lehet biztosítani a adategységet és attribútum / oszlop szinteket. A felhasználói címkék adategységek vagy attribútumok csoportosítására használható felhasználó által meghatározott címkék. |
| Címkék (szószedet címkék) |Címkék lehet biztosítani a adategységet és attribútum / oszlop szinteket. Szószedet a címkék olyan központilag meghatározott szószedet kifejezései adategységek vagy egy közös üzleti elnevezési rendszert használó attribútumok csoportosítására használható. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása) |
| Szakértők |Szakértői lehet biztosítani az adatokat az eszközintelligencia szintjén. Szakértői azonosíthatja a felhasználók vagy csoportok szakértői perspektívákat az adatok is és a felhasználók számára a regisztrált adatforrások felderítését kapcsolódási pontként szolgál és olyan kérdése van, a meglévő jegyzetek nem talált választ is. |
| Hozzáférés kérése |Az adatok eszköz szintjén is kell adni a kérelem hozzáférési adatokat. Ezt az információt a felhasználók számára, hogy azok még nem rendelkezik engedélyekkel eléréséhez adatforrásra van. Felhasználók adhat meg a felhasználót vagy csoportot, aki megadja a hozzáférést, az URL-címet, a folyamat és az eszköz eléréséhez, a felhasználók e-mail-címét, vagy maga a folyamat szöveget adja meg. |
| Dokumentáció |Dokumentáció az adatok eszköz szinten lehet biztosítani. Eszköz dokumentációja az rich text formátumú szöveg információt tartalmazó hivatkozásokat és a képeket, és amely biztosíthat semmilyen információt nem közvetített leírásokat és címkék. |

## <a name="annotating-multiple-assets"></a>Adategységek ellátása megjegyzésekkel
Egyszerre több adategységet a Data Catalog portál kiválasztásakor felhasználók is egyetlen művelettel az összes kijelölt adategységek dekorációkkal ellátni. Jegyzetek alkalmazza a kiválasztott eszközöket, megkönnyítve a kiválasztásához, és adjon meg konzisztens leírását és adatkészleteket, címkéket vagy szakértőket kapcsolódó adategységek.

> [!NOTE]
> A címkék és a szakértők is is kell megadni, ha az adatok katalógusadatok használatával való regisztrálásáról adategységeket regisztráló eszközzel forrás.
>
>

Amikor kiválasztja a több táblák és nézetek, csak az oszlopok, hogy az összes kijelölt eszközök rendelkezik a common data megjelennek a Data Catalog-portál. Ez lehetővé teszi a felhasználóknak megadniuk a címkék és leírások az összes oszlop összes kiválasztott eszköz ugyanazzal a névvel.

## <a name="annotations-and-discovery"></a>Széljegyzetek és felderítés
Ugyanúgy, mint a Data Catalog search-index a regisztráció során az adatforrásból kinyert metaadatokat ad hozzá, a felhasználó által megadott metaadatok is indexelve van. Ez azt jelenti, hogy, hogy nem csak jegyzetek könnyebben felhasználóknak megérteni az adatokat, azok felderítése, jegyzetek is megkönnyítik a felhasználók számára történő kereséssel a feltételeket, amelyeket számukra érthető legyen a jegyzettel ellátott adategységek felderítése.

## <a name="summary"></a>Összegzés
A Data Catalog adatforrás regisztrálása lehetővé teszi az adatok felderíthető szerkezeti és leíró metaadatokat másolja az adatforrás a Catalog szolgáltatásba. Miután egy adatforrás regisztrálva lett, a felhasználók megadhatják, hogy könnyebben megtalálhatóvá és értelmezhetővé a Data Catalog-portálon.

## <a name="see-also"></a>Lásd még
* [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) részletesen adatforrások ellátása megjegyzésekkel kapcsolatos oktatóanyagot.
