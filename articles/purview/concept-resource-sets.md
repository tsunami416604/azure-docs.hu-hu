---
title: Az erőforrás-készletek ismertetése
description: Ez a cikk ismerteti, hogy milyen erőforrás-készleteket és hogyan hozza létre az Azure hatáskörébe.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552710"
---
# <a name="understanding-resource-sets"></a>Az erőforrás-készletek ismertetése

Ebből a cikkből megtudhatja, hogyan használja az Azure hatáskörébe az adategységek logikai erőforrásokhoz való leképezését az erőforrás-készletek használatával.

## <a name="background-info"></a>Háttér-információk

A méretezés alatt álló adatfeldolgozási rendszerek általában egyetlen táblát tárolnak egy lemezen több fájlként. Ez a koncepció az Azure hatáskörébe tartozik az erőforrás-készletek használatával. Az erőforrás-készlet a katalógus egyetlen objektuma, amely nagy mennyiségű eszközt képvisel a tárolóban.

Tegyük fel például, hogy a Spark-fürt megőrzött egy DataFrame egy ADLS Gen2 adatforrásban. Bár a Sparkban a tábla egyetlen logikai erőforráshoz hasonlít, a lemezen valószínűleg több ezer parketta-fájl található, amelyek mindegyike a teljes DataFrame-tartalom partícióját jelöli. A IoT és a webnaplók adatkezelési feladatának ugyanaz a kihívása. Képzelje el, hogy van egy érzékelője, amely másodpercenként többször is kiírja a naplófájlokat. Addig nem fog sokáig tartani, amíg az adott érzékelőből több százezer naplófájl van.

Ahhoz, hogy a nagy számú adategység egyetlen logikai erőforráshoz való leképezésének kihívásával foglalkozzon, az Azure hatáskörébe erőforrás-készleteket használ.

## <a name="how-azure-purview-detects-resource-sets"></a>Hogyan észleli az Azure hatáskörébe az erőforrás-készleteket?

Az Azure hatáskörébe csak az Azure-Blobok, a ADLS Gen1 és a ADLS Gen2 erőforrásainak észlelése támogatott.

Az Azure-beli hatáskörébe automatikusan az erőforrás-készlet felderítése nevű funkció használatával észleli az erőforrás-készleteket. Ez a funkció a vizsgálat során betöltött összes adatot megvizsgálja, és összehasonlítja a meghatározott minták egy halmazával.

Tegyük fel például, hogy egy olyan adatforrást keres, amelynek URL-címe `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Az Azure-beli hatáskörébe az elérésiút-szegmensek láthatók, és meghatározza, hogy a beépített mintázatoknak megfelelőek-e. Beépített mintákat tartalmaz a GUID-azonosítók, számok, dátumformátum, honosítási kódok (például en-US) stb. Ebben az esetben a szám minta *23*-ra illeszkedik. Az Azure-beli hatáskörébe feltételezi, hogy ez a fájl egy nevű erőforrás-készlet részét képezi `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Az URL-címekhez hasonlóan az Azure-beli `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` hatáskörébe is illeszkedik a honosítási minta és a szám minta, amely egy nevű erőforrás-készletet hoz létre `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Ennek a stratégiának a használatával az Azure hatáskörébe a következő erőforrások képezhetők le ugyanahhoz az erőforrás-készlethez `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Az Azure Data Lake Storage Gen2 mostantól általánosan elérhető. Javasoljuk, hogy már ma kezdje el használni. További információkért tekintse meg a [termék oldalát](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Az Azure hatáskörébe tartozó fájltípusok nem észlelhetők erőforrás-készletként

A hatáskörébe szándékosan nem kerül be a legtöbb dokumentum fájltípusa (például Word, Excel vagy PDF) erőforrás-készletként. A kivétel CSV, mivel ez egy közös particionált fájlformátum.

## <a name="how-azure-purview-scans-resource-sets"></a>Hogyan vizsgálja az Azure-beli hatáskörébe az erőforrás-készleteket?

Ha az Azure hatáskörébe olyan erőforrásokat észlel, amely úgy gondolja, hogy egy erőforrás-készlet részét képezi, a teljes vizsgálatból egy minta-vizsgálatra vált. Egy minta vizsgálatban csak a fájlok egy részhalmazát nyitja meg az erőforrás-készletben. Minden megnyitott fájl esetében a sémáját használja, és futtatja az osztályozó elemeit. Az Azure-beli hatáskörébe ezután megkeresi a legújabb erőforrást a megnyitott erőforrások között, és az adott erőforrás sémáját és besorolásait a katalógusban lévő teljes erőforráshoz tartozó bejegyzésben használja.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Milyen Azure-beli feladatok érhetők el az erőforrás-készletekről

Az egyes sémák és besorolások mellett az Azure hatáskörébe a következő információkat tárolja az erőforrás-készletekről:

- A legutóbb beolvasott partíciós erőforrásból származó adatok.
- Az erőforrás-készletet alkotó partíciós erőforrások összesített adatai.
- Partíciók száma, amely megmutatja, hogy hány partíciós erőforrást talált.
- Egy séma száma, amely azt mutatja, hogy hány egyedi séma található a mintában. Ez az érték egy 1 – 5 közötti szám, vagy az 5, 5 + értéknél nagyobb értékek esetén.
- A partíciós típusok listája, ha az erőforrás-készletben egynél több partíció szerepel. Előfordulhat például, hogy egy IoT-érzékelő XML-és JSON-fájlokat is kiállít, bár mindkettő logikailag ugyanahhoz az erőforráshoz tartozik.

## <a name="built-in-resource-set-patterns"></a>Beépített erőforrás-készlet mintázatai

Az Azure-beli hatáskörébe a következő erőforrás-set mintázatok támogatottak. Ezek a minták egy könyvtárban vagy egy fájlnév részeként is megjelenhetnek.

| Minta neve | Megjelenített név | Leírás |
|--------------|--------------|-------------|
| GUID         | GUID       | Globálisan egyedi azonosító, az [RFC 4122](https://tools.ietf.org/html/rfc4122)-ben meghatározottak szerint. |
| Szám       | N          | Egy vagy több számjegy. |
| Dátum-és időformátumok | N     | Az Azure-beli hatáskörébe különböző típusú dátum-és időformátumok használhatók, de az összes érték {N} s-sorozatra csökken. |
| 4ByteHex     | Hex        | Egy négyjegyű hexadecimális szám. |
| Honosítás | Loc        | A [BCP 47](https://tools.ietf.org/html/bcp47)-ben meghatározott nyelvi címke. Az Azure-beli hatáskörébe a kötőjelet (-) vagy aláhúzást (_) tartalmazó címkéket is támogat. Például en_ca és en-CA. |

## <a name="issues-with-resource-sets"></a>Erőforrás-készletekkel kapcsolatos problémák

Bár az erőforrás-készletek a legtöbb esetben jól működnek, a következő problémák merülhetnek fel, amelyekben az Azure hatáskörébe tartoznak:

- Az eszköz helytelen jelölése erőforrás-készletként
- Nem megfelelő erőforrás-készletbe helyezi az eszközt.
- Nem megfelelően jelöl meg egy eszközt, mert nem erőforrás-készlet

## <a name="next-steps"></a>Következő lépések

Az Data Catalog használatának megkezdéséhez tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
