---
title: Adatforrások dokumentálása az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli az adatelemek dokumentálását az Azure Data Catalogban.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950192"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Adatforrások dokumentálása az Azure Data Catalogban

## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Más szóval az **Azure Data Catalog** arról szól, hogy segítsen az embereknek az adatforrások *felderítésében, megértésében*és használatában, és segítse a szervezeteket abban, hogy nagyobb értéket kapjanak a meglévő adataikból.

Ha egy adatforrás regisztrálva van az **Azure Data Catalog-ban,** a rendszer másolja és indexeli a metaadatait a szolgáltatás, de a történet nem ér véget. **Az Azure Data Catalog** azt is lehetővé teszi a felhasználók számára, hogy saját teljes dokumentációt, amely leírja a használat és a gyakori forgatókönyvek az adatforráshoz.

Az [adatforrások jegyzetelése](data-catalog-how-to-annotate.md)című részben megtudhatja, hogy az adatforrást ismertető szakértők címkékkel és leírással egészíthetik ki azt. Az **Azure Data Catalog** portál tartalmaz egy rich text szerkesztő, így a felhasználók teljes mértékben dokumentálja az adatelemek és tárolók. A szerkesztő bekezdésformázást tartalmaz, például címsorokat, szövegformázást, listajeles listákat, számozott listákat és táblázatokat.

Címkék és leírások nagy egyszerű jegyzeteket. Annak érdekében azonban, hogy az adatfelhasználók jobban megértsék az adatforrások használatát és az adatforrások üzleti forgatókönyveit, a szakértő teljes körű, részletes dokumentációt nyújthat. Az adatforrások könnyen dokumentálhatóak. Jelöljön ki egy adategységet vagy tárolót, és válassza a **Dokumentáció**lehetőséget.

![Dokumentáció lap egy adatkatalógusban](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Adateszközök dokumentálása
Az **Azure Data Catalog** dokumentációjának előnye lehetővé teszi, hogy a data catalog-t tartalomtárként használja az adateszközök teljes elbeszélésének létrehozásához. A tárolókat és táblákat leíró részletes tartalmakat is megismerheti. Ha már rendelkezik tartalommal egy másik tartalomtárban, például a SharePointban vagy egy fájlmegosztásban, hozzáadhatja az eszközdokumentációs hivatkozásokhoz, hogy hivatkozzon a meglévő tartalomra. Ez a funkció felderíthetővé teszi a meglévő dokumentumokat.

> [!NOTE]
> A dokumentáció nem szerepel a keresési indexben.
>

![Dokumentáció lap és hivatkozás a webhivatkozásra](media/data-catalog-documentation/data-catalog-documentation2.png)

A dokumentáció szintje az adateszköz-tároló jellemzőinek és értékének leírásától a tárolón belüli táblaséma részletes leírásáig terjedhet. A megadott dokumentáció szintjét az üzleti igényeknek kell vezérelnie. De általában, itt van néhány előnye és hátránya dokumentálása adateszközök:

* Dokumentum csak egy tároló: Minden tartalom egy helyen, de előfordulhat, hogy nem rendelkeznek a szükséges részleteket a felhasználók számára, hogy tájékozott döntést.
* Csak a táblák dokumentálása: A tartalom az adott objektumra jellemző, de a felhasználók nak több helye van a dokumentumok számára.
* Dokumentumtárolók és -táblázatok: A legátfogóbb megközelítés, de a dokumentumok további karbantartását vezetheti be.

## <a name="summary"></a>Összefoglalás
Az **adatforrások dokumentálása az Azure Data Catalog** segítségével olyan részletes elbeszélést hozhat létre az adateszközökről, amennyire szüksége van.  A hivatkozások használatával egy meglévő tartalomtárban tárolt tartalomra mutató hivatkozást használhat, amely összehozza a meglévő dokumentumokat és adatelemeket. Miután a felhasználók felfedezik a megfelelő adateszközöket, teljes dokumentációkészletet kaphatnak.
