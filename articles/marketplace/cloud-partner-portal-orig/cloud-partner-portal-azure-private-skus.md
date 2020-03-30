---
title: Privát ska-k és tervek | Azure Piactér
description: Saját termékkészlet használata az ajánlat elérhetőségének kezeléséhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280388"
---
<a name="private-skus-and-plans"></a>Privát ska-k és csomagok
============

A privát termékváltozatai lehetővé teszik, hogy az su-k elérhetőségét meghatározott ügyfelekre korlátozza. Ha egy termékváltozat magánjellegűként van megjelölve, az nem érhető el egyetlen nyilvános katalógusban sem, beleértve az [Azure Piactéren](https://azuremarketplace.microsoft.com) és az [Azure Portalon.](https://portal.azure.com) Az Azure Portalon csak a termékváltozathoz hozzáféréssel rendelkező ügyfelek láthatják. Ezenkívül arra is kérik őket, hogy hozzáférjenek a privát ajánlatokhoz.

>[!NOTE]
>A nyilvános termékváltozatokkal való ütközés elkerülése érdekében a privát termékváltozatoknak új egyedi termékváltozattal/csomagazonosítókkal kell rendelkezniük.

A privát sk-ok segítségével a következő eseteket kezelheti:

1.  Olyan szoftvereket tehet közzé, amelyeket csak nyilvánosan szeretne elérhetővé tenni bizonyos ügyfelek számára, és nem nyilvánosan elérhetővé.
2.  Nyilvános szoftverek változatainak közzététele egyedi árakon az adott ügyfelek számára.
3.  Nyilvános szoftverek változatainak közzététele személyre szabott leírással és kifejezésekkel (új ajánlattal).

Ha csak módosítani szeretné az árat, újra felhasználhatja a lemezeket egy másik termékváltozatból ugyanabban az ajánlatban. A privát sk-k, nem kell újra beküldeni a lemezeket a skus-ok között.

<a name="mark-a-sku-private"></a>Termékváltozat megjelölése magánjellegűként
---------------------

Ha egy termékváltozatot privátként szeretne megjelölni, kapcsolja be azt a beállítást, amely megkérdezi, hogy a termékváltozat magánjellegű-e:

![Termékváltozat megjelölése magánjellegűként](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

A lemezeket újra felhasználhatja egy másik termékváltozatban, és módosíthatja a díjszabást vagy a leírást. A lemezek újbóli felhasználásához válassza az **Igen** választ a "A termékváltozat újrafelhasználja a nyilvános termékváltozatból származó képeket" parancs.

Ha a termékváltozat magánjellegűként van megjelölve, és az ajánlat más termékváltozatokat is használ újrafelhasználható lemezekkel, jeleznie kell, hogy a termékváltozat újrafelhasználja a lemezeket egy másik termékváltozatból. A privát termékváltozat célcélközönségét is meg kell adnia.

>[!NOTE]
>A közzététel után a nyilvános termékváltozat nem tehető privátsá.

<a name="select-an-image"></a>Kép kijelölése
------------------

Új lemezeket biztosíthat a saját termékváltozathoz, vagy újra felhasználhatja ugyanazokat a lemezeket, amelyek már egy másik termékváltozatban vannak, csak a díjszabás vagy a leírás módosítása. A lemezek újbóli felhasználásához válassza az **Igen** választ a "A termékváltozat újrafelhasználja a lemezképet egy nyilvános termékváltozatból" parancs.

![Kép újrafelhasználásának jelzése](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Miután meggyőződt arról, hogy a termékváltozat újrafelhasználja a képeket, válassza ki a képek forrását vagy *alap* termékváltozatát:

![Kép kijelölése](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Az ajánlat közzétételekor a kiválasztott termékváltozatból származó képek elérhetővé lesznek a privát termékváltozat-azonosító alatt az egyéni díjakkal/kifejezésekkel. A privát termékváltozat csak a megcélzott közönség számára lenne látható.

A lemezkép frissítések, akkor csak akkor kell frissíteni az alapul szolgáló termékváltozat a rendszerkép. A színfalak mögött a privát termékváltozat képe is automatikusan frissül. Hasonlóképpen, ha törli a lemezképet az alapul szolgáló termékváltozatból, a rendszerkép is törlődik a privát termékváltozatból.

<a name="restricting-the-audience"></a>A célközönség korlátozása
------------------------

A privát ajánlatokat csak a megcélzott felhasználók találhatják meg és telepíthetik.
Jelenleg támogatjuk a felhasználók célzását az előfizetési azonosítók használatával.

Ezek az előfizetések manuális beviteli űrlapon keresztül adhatók meg **legfeljebb 10 előfizetéshez**, vagy egy CSV-fájl feltöltésével, amely **legfeljebb 20 000 előfizetést**tesz lehetővé.

Kézi bevitel korlátozott közönség számára:

![Célközönség manuális korlátozása](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV-feltöltés korlátozott közönség számára:

![Közönség korlátozása a CSV-vel](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Minta CSV-fájl tartalma:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Amikor manuális bejegyzésről CSV-feltöltési nézetre vagy CSV-ről manuális bejegyzésre vált, a termékváltozathoz hozzáféréssel rendelkező előfizetés-azonosítók régi listája nem marad meg. Egy figyelmeztetés jelenik meg, és a lista csak felülírja az ajánlat mentésekor.

<a name="managing-private-audiences"></a>Privát közönségek kezelése
-------------------------

**Annak érdekében, hogy a teljes ajánlat újbóli közzététele nélkül frissíthesse a közönséget, a kívánt közönségmódosításokat hajthatja végre (a felhasználói felület vagy az API használatával), majd indítsa el a "Privát közönségek szinkronizálása" műveletet.**

Ha a célközönség10 vagy kevesebb előfizetéssel rendelkezik, teljes mértékben kezelheti azt a CPP felhasználói felületével.

Ha a közönség több mint 10 előfizetéssel rendelkezik, kezelhető egy CSV-fájl használatával, amelyet feltölthet a CPP felhasználói felületére vagy az API-t.

Ha az API-t használja, és nem szeretné karbantartani a CSV-fájlt, az alábbi utasítások szerint közvetlenül kezelheti a közönséget az API használatával.

> [!NOTE]
> Használja az Azure-előfizetés-azonosító (csomagok és sk-ek) vagy a bérlői azonosító (csak tervek) a közönség hozzáadása a privát ajánlathoz.

###  <a name="managing-subscriptions-with-the-api"></a>Előfizetések kezelése az API-val

Az API segítségével feltölthetsz egy CSV-t, vagy közvetlenül kezelheted a célközönségedet (CSV használata nélkül). Általánosságban elmondható, hogy egyszerűen le kell `restrictedAudience` kérnie az ajánlatot, frissítenie kell az objektumot, majd vissza kell küldenie ezeket az ajánlatba a közönségtagok hozzáadásához vagy eltávolításához.

A következőképpen frissítheti programozott módon a közönséglistát:

1. [Az ajánlat adatainak lekérése:](cloud-partner-portal-api-retrieve-specific-offer.md)

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. A JPath-lekérdezés sel megkeresheti az ajánlat minden termékváltozatában a korlátozott közönségobjektumokat:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Frissítse az ajánlat korlátozott közönségobjektuma(i)t.

    **Ha eredetileg feltöltötte a privát ajánlatának előfizetési listáját a CSV-fájlból:**

    A *korlátozottAudience* objektum(ok) így fognak kinézni.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Minden egyes korlátozott közönségobjektumesetében:

    a. Töltse le `restrictedAudience.uploadedCsvUri`a tartalmát. A tartalom egyszerűen egy CSV fájl fejlécekkel. Példa:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Szükség szerint adjon hozzá vagy töröljön előfizetéseket a letöltött CSV-fájlban.

    c. Töltse fel a frissített CSV-fájlt egy helyre, például [az Azure Blob storage](../../storage/blobs/storage-blobs-overview.md) vagy a [OneDrive,](https://onedrive.live.com)és hozzon létre egy írásvédett hivatkozást a fájlhoz. Ez lesz az új *SasUrl*.

    d. Frissítse `restrictedAudience.uploadedCsvUri` a kulcsot az új *SasUrl-lel.*

    **Ha manuálisan adta meg a privát ajánlatához szükséges előfizetések eredeti listáját a Cloud Partner Portal portálról:**

    A *korlátozottAudience* objektum(ok) a következőkre fognak hasonlítani:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Minden korlátozott közönségobjektumhoz szükség szerint `restrictedAudience.manualEntries` vegyen fel vagy töröljön bejegyzéseket a listában.

4. A privát ajánlat minden egyes termékváltozatának *összes korlátozottközönség-objektumának* frissítése után [frissítse az ajánlatot:](cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Ezzel a frissített közönséglista már érvényben van.

<a name="previewing-private-offers"></a>Privát ajánlatok előnézete
-------------------------

Az előzetes verzió/előkészítési lépés során csak az ajánlatszintű előzetes verziós előfizetések férhetnek hozzá a termékváltozathoz. Ebben a tesztelési szakaszban megtekintheti az ajánlatot, ahogy az a megcélzott ügyfelek számára megjelenik.

Ajánlat szintű előzetes előfizetések a szakaszos ajánlatok eléréséhez:

![Előfizetés-azonosítók előzetes verzió](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Miután az ajánlat éles, csak a korlátozott közönség-előfizetések (manuális bevitel vagy CSV- n keresztül megadott) képes lesz megtekinteni és telepíteni a privát Termékváltozat. Azt javasoljuk, hogy **mindig a saját előfizetések a korlátozott közönség** a privát termékváltozat érvényesítési célokra.

>[!NOTE]
>Hibakeresési célokra a Microsoft támogatási és mérnöki csapatai is hozzáférhetnek ezekhez a privát ajánlatokhoz.
