---
title: Adatforrások jegyzetelése az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli, hogyan lehet az adatelemeket az Azure Data Catalogban jegyzetekkel látni, beleértve a rövid neveket, címkéket, leírásokat és szakértőket.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950266"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Adatforrások jegyzetelése az Azure Data Catalogban

## <a name="introduction"></a>Bevezetés

**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Más szóval a Data Catalog arról szól, hogy segítsen az embereknek az adatforrások felfedezésében, megértésében és használatában, és segítse a szervezeteket abban, hogy nagyobb értéket kapjanak meglévő adataikból. Ha egy adatforrás regisztrálva van a Data Catalog szolgáltatásban, a szolgáltatás másolja és indexeli a metaadatait, de a szövegegység nem ér véget. A Data Catalog lehetővé teszi a felhasználók számára, hogy saját leíró metaadataikat – például leírásokat és címkéket – adjanak meg az adatforrásból kinyert metaadatok kiegészítésére, valamint az adatforrás érthetőbbé teszik több ember számára.

## <a name="annotation-and-crowdsourcing"></a>Jegyzet és crowdsourcing
Mindenkinek van véleménye. És ez egy jó dolog.
A Data Catalog felismeri, hogy a különböző felhasználók eltérő perspektívával rendelkeznek a vállalati adatforrásokkal kapcsolatban, és hogy ezek a perspektívák értékesek lehetnek. Vegyük példaként a következő esetet:

* A rendszergazda ismeri az adatforrást üzemeltető kiszolgálók vagy szolgáltatások szolgáltatásiszint-szerződését.
* Az adatbázis rendszergazdája ismeri az egyes adatbázisok biztonsági mentési ütemezését és az engedélyezett ETL feldolgozási ablakokat.
* A rendszer tulajdonosa ismeri azt a folyamatot, amelyen a felhasználók hozzáférést kérhetnek az adatforráshoz.
* Az adatfelelős tudja, hogy az adatforrásban lévő eszközök és attribútumok hogyan felelnek meg a vállalati adatmodellnek.
* Az elemző tudja, hogyan használják fel az adatokat az általuk támogatott üzleti folyamatok összefüggésében.

Ezek a perspektívák mindegyike értékes, és a Data Catalog a crowdsourcing megközelítést használja a metaadatokhoz, amely lehetővé teszi, hogy mindegyiket rögzítsék és használják a regisztrált adatforrások teljes képének biztosításához. A Data Catalog portál használatával minden felhasználó hozzáadhatja és szerkesztheti saját jegyzeteit, miközben megtekintheti a más felhasználók által biztosított jegyzeteket.

## <a name="different-types-of-annotations"></a>Különböző típusú jegyzetek
A Data Catalog a következő típusú feliratokat támogatja:

| Jegyzet | Megjegyzések |
| --- | --- |
| Felhasználóbarát név |A rövid nevek az adateszköz szintjén adhatók meg, hogy az adateszközök könnyebben érthetőek legyenek. A rövid nevek akkor a leghasznosabbak, ha az alapul szolgáló objektumnév rejtélyes, rövidített vagy más módon nem értelmezhető a felhasználók számára. |
| Leírás |Leírások adhatók meg az adategység és az attribútum / oszlop szinten. A leírások szabad formátumú rövid szöveges jegyzetek, amelyek leírják a felhasználó nak az adateszközre vagy annak használatára vonatkozó perspektíváját. |
| Címkék (felhasználói címkék) |Címkéket lehet adni az adategység és attribútum / oszlop szinten. A felhasználói címkék olyan felhasználó által definiált címkék, amelyek az adatelemek vagy attribútumok kategorizálására használhatók. |
| Címkék (szószedetcímkék) |Címkéket lehet adni az adategység és attribútum / oszlop szinten. A szószedetcímkék központilag definiált szószedeti kifejezések, amelyek az adatelemek vagy attribútumok közös üzleti besorolással történő kategorizálására használhatók. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása) |
| Szakértők |A szakértők az adateszköz szintjén is rendelkezésre állnak. A szakértők azonosítják az adatokkal kapcsolatos szakértői perspektívákkal rendelkező felhasználókat vagy csoportokat, és kapcsolattartási pontként szolgálhatnak azon felhasználók számára, akik felfedezik a regisztrált adatforrásokat, és olyan kérdéseik vannak, amelyekre a meglévő jegyzetek nem válaszolnak. |
| Hozzáférés kérése |A kérelem-hozzáférési információk az adateszköz szintjén adhatók meg. Ez az információ azoknak a felhasználóknak szól, akik olyan adatforrást fedeznek fel, amelyhez még nem rendelkeznek hozzáférési engedéllyel. A felhasználók megadhatják annak a felhasználónak vagy csoportnak az e-mail címét, aki hozzáférést biztosít, annak a folyamatnak vagy eszköznek az URL-címét, amelyhez a felhasználóknak szükségük van a hozzáféréshez, vagy magát a folyamatot szövegként adhatják meg. |
| Dokumentáció |A dokumentáció az adateszköz szintjén is rendelkezésre áll. Az eszközdokumentáció olyan rich text információ, amely hivatkozásokat és képeket tartalmazhat, és amely a leírásokon és címkéken keresztül nem továbbított információkat nyújthat. |

## <a name="annotating-multiple-assets"></a>Több eszköz jegyzetelése
Ha több adateszközt jelöl ki a Data Catalog portálon, a felhasználók egyetlen műveletben jegyzetelhetik az összes kijelölt eszközt. A jegyzetek az összes kiválasztott eszközre vonatkoznak, így könnyen kiválaszthatja és egységes leírást és címkéket és szakértőket biztosít a kapcsolódó adateszközökhöz.

> [!NOTE]
> Címkék és szakértők is megadhatók az adatelemek regisztrálásakor a Data Catalog adatforrás-regisztrációs eszközzel.
>
>

Több tábla és nézet kijelölésétekesetén csak az összes kijelölt adateszköz közös oszlopai jelennek meg az adatkatalógus-portálon. Ez lehetővé teszi a felhasználók számára, hogy címkéket és leírásokat adjanak meg az összes kiválasztott eszköz azonos nevű oszlopához.

## <a name="annotations-and-discovery"></a>Jegyzetek és felfedezés
Csakúgy, mint a metaadatok kinyert adatforrás a regisztráció során hozzáadódik a Data Catalog keresési index, a felhasználó által megadott metaadatok is indexelt. Ez azt jelenti, hogy nem csak a jegyzetek megkönnyítik a felhasználók számára, hogy megértsék az általuk felfedezett adatokat, a jegyzetek is megkönnyítik a felhasználók számára, hogy felfedezzék a jegyzetekkel nem eshető adatokat a számukra ésszerű kifejezések használatával.

## <a name="summary"></a>Összefoglalás
Adatforrás regisztrálása a Data Catalog szolgáltatással lebonthatóvá teszi az adatokat azáltal, hogy szerkezeti és leíró metaadatokat másol az adatforrásból a Katalógus szolgáltatásba. Az adatforrás regisztrálása után a felhasználók olyan jegyzeteket adhatnak meg, amelyek megkönnyítik a felderítést és a megértést a Data Catalog portálon belül.

## <a name="see-also"></a>Lásd még
* [Az Azure Data Catalog oktatóanyagának első lépései](data-catalog-get-started.md) az adatforrások jegyzetelésével kapcsolatos részletes részletekért.
