---
title: Privát SKU-i és csomagok | Azure piactér
description: Saját SKU-ket használhat az ajánlat rendelkezésre állásának kezeléséhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280388"
---
<a name="private-skus-and-plans"></a>Privát SKU-i és csomagok
============

A privát SKU-kal korlátozható a SKU-i rendelkezésre állása adott ügyfelek számára. Ha egy SKU privátként van megjelölve, nem érhető el semmilyen nyilvános katalógusban, beleértve az [Azure piactéren](https://azuremarketplace.microsoft.com) és a [Azure Portal](https://portal.azure.com). A Azure Portalon csak az SKU-hoz hozzáférő ügyfelek láthatják. Emellett a rendszer arra is kéri, hogy hozzáférjenek a privát ajánlatokhoz.

>[!NOTE]
>A privát SKU-nak új egyedi SKU-/csomag-azonosítóval kell rendelkeznie, hogy elkerülje az ütközést a nyilvános SKU-kal.

A következő forgatókönyvek kezelésére használhat privát SKU-ket:

1.  Közzéteheti azokat a szoftvereket, amelyeket csak az adott ügyfelek számára, nyilvánosan elérhetővé szeretne tenni.
2.  A nyilvános szoftverek variációit egyedi díjszabással teheti közzé adott ügyfelek számára.
3.  A nyilvános szoftverek variációinak közzététele testreszabott leírással és kifejezésekkel (új ajánlaton keresztül).

Ha csak az árat szeretné módosítani, egy másik SKU lemezeit is felhasználhatja ugyanazon az ajánlaton belül. A privát SKU-kal nem kell újraküldenie a lemezeket az SKU-ban.

<a name="mark-a-sku-private"></a>SKU megjelölése privátként
---------------------

Ha magánjellegűként kívánja megjelölni az SKU-t, kapcsolja be azt a lehetőséget, hogy az SKU magán:

![SKU megjelölése privátként](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Újra felhasználhatja a lemezeket egy másik SKU-ban, és módosíthatja a díjszabást vagy a leírást. Ha újra szeretné használni a lemezeket, válassza az **Igen** lehetőséget az "ezt az SKU-t használó lemezképek újrafelhasználása nyilvános SKU-ból" üzenetre válaszként.

Ha az SKU privátként van megjelölve, és az ajánlat más SKU-ket tartalmaz újrahasználható lemezekkel, akkor meg kell jelölnie, hogy az SKU újrahasználja a lemezeket egy másik SKU-ból. A magánhálózati SKU számára is meg kell adnia a célközönséget.

>[!NOTE]
>Közzététel után a nyilvános SKU-t nem lehet magánjellegűvé tenni.

<a name="select-an-image"></a>Rendszerkép kiválasztása
------------------

Megadhat új lemezeket a privát SKU-hoz, vagy felhasználhatja a másik SKU-ban már megadott lemezeket, csak a díjszabást vagy a leírást. Ha újra szeretné használni a lemezeket, válassza az **Igen** lehetőséget válaszként a következőre: "ezt az SKU-t használja fel egy nyilvános SKU-ból".

![Képek újrafelhasználásának jelzése](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Miután megerősítette, hogy az SKU újra felhasználja a rendszerképeket, válassza ki a lemezkép forrását vagy *Alap* SKU-jának a következőt:

![Rendszerkép kiválasztása](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Amikor közzéteszi az ajánlatot, a kiválasztott SKU-ról származó lemezképek elérhetővé válnak a privát SKU-AZONOSÍTÓban az egyéni díjak/feltételek alapján. A privát SKU csak a célközönség számára látható.

A képfrissítések esetében csak az alapul szolgáló SKU rendszerképének frissítésére van szükség. A háttérben a privát SKU-hoz tartozó rendszerkép is automatikusan frissül. Hasonlóképpen, ha törli a rendszerképet a mögöttes SKU-ból, akkor a rendszerkép is el lesz távolítva a privát SKU-ból.

<a name="restricting-the-audience"></a>A célközönség korlátozása
------------------------

A privát ajánlatokat csak a megcélzó felhasználók tudják megtalálni és üzembe helyezni.
Jelenleg az előfizetés-azonosítókat használó felhasználók célzása támogatott.

Ezeket az előfizetéseket manuálisan, **legfeljebb 10 előfizetésre**vagy egy CSV-fájl feltöltésével lehet megadni, amely **legfeljebb 20 000 előfizetést**tesz lehetővé.

A korlátozott célközönség manuális bejegyzése:

![Célközönség manuális korlátozása](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV-feltöltés a korlátozott célközönség számára:

![A célközönség korlátozása CSV használatával](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Minta CSV-fájl tartalma:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Ha a manuális bejegyzésről CSV-feltöltés nézetre vagy CSV-ről manuálisra vált, a rendszer nem őrzi meg az SKU-hoz hozzáférő előfizetési azonosítók régi listáját. A rendszer figyelmeztetést jelenít meg, és a lista csak az ajánlat mentésekor kerül felülírásra.

<a name="managing-private-audiences"></a>Privát célközönségek kezelése
-------------------------

**Annak érdekében, hogy a teljes ajánlat újbóli közzététele nélkül frissítse a célközönséget, a célközönséget módosítani szeretné (a felhasználói felületen vagy az API-val), majd kezdeményezheti a "privát célközönségek szinkronizálása" műveletet.**

Ha a célközönség 10 vagy kevesebb előfizetés, akkor teljes mértékben a CPP felhasználói felületének használatával kezelheti.

Ha a célközönsége több mint 10 előfizetés, akkor kezelheti azt egy CSV-fájllal, amelyet feltöltheti a CPP felhasználói felületén vagy az API-val.

Ha az API-t használja, és nem szeretne CSV-fájlt fenntartani, a célközönséget közvetlenül az API-val kezelheti az alábbi utasítások alapján.

> [!NOTE]
> Az Azure-előfizetési azonosító (csomagok és SKU-EK) vagy a bérlői azonosító (csak a csomagok) használatával adhat hozzá célközönséget a privát ajánlathoz.

###  <a name="managing-subscriptions-with-the-api"></a>Előfizetések kezelése az API-val

Az API-val feltölthet egy CSV-fájlt, vagy közvetlenül is kezelheti a célközönségét (CSV használata nélkül). Általánosságban elmondható, hogy egyszerűen le kell kérnie az ajánlatot `restrictedAudience` , frissítenie kell az objektumot, majd ezeket a módosításokat vissza kell küldenie az ajánlatba, hogy hozzá lehessen adni vagy el lehessen távolítani a célközönség tagjait.

A célközönségek listájának programozott frissítése a következőképpen történik:

1. [Ajánlati adatai lekérése](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. A korlátozott célközönségű objektumok keresése az ajánlat minden SKU-jának használatával ezzel a JPath-lekérdezéssel:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Frissítse az ajánlathoz tartozó korlátozott célközönség-objektum (oka) t.

    **Ha eredetileg feltöltötte a CSV-fájlból származó privát ajánlat előfizetési listáját:**

    A *restrictedAudience* -objektum (ok) ehhez hasonlóan fog kinézni.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Minden korlátozott célközönség objektum esetében:

    a. Töltse le a tartalmát `restrictedAudience.uploadedCsvUri`. A tartalom egyszerűen egy fejléceket tartalmazó CSV-fájl. Például:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Szükség szerint vegyen fel vagy töröljön előfizetéseket a letöltött CSV-fájlban.

    c. Töltse fel a frissített CSV-fájlt egy helyre, például az [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) -ba vagy a [OneDrive](https://onedrive.live.com)-ra, és hozzon létre egy csak olvasható hivatkozást a fájlra. Ez lesz az új *sas URL*.

    d. Frissítse a `restrictedAudience.uploadedCsvUri` kulcsot az új *sas URL*.

    **Ha manuálisan adta meg a privát ajánlathoz tartozó előfizetések eredeti listáját a Cloud Partner Portalből:**

    A *restrictedAudience* -objektum (ok) a következőhöz hasonló módon fog kinézni:

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

    a. Minden korlátozott célközönség objektum esetében szükség szerint adjon hozzá vagy töröljön bejegyzéseket `restrictedAudience.manualEntries` a listában.

4. Ha befejezte a privát ajánlat egyes SKU- *restrictedAudience* összes objektumának frissítését, [frissítse az ajánlatot](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Ezzel a frissített célközönségek listája már érvényben van.

<a name="previewing-private-offers"></a>Privát ajánlatok előzetes megtekintése
-------------------------

Az előzetes/előkészítési lépés során csak az ajánlati szint előzetes verziójának előfizetései férhetnek hozzá az SKU-hoz. A tesztelési fázisban megtekintheti az ajánlatot, ahogy az a megcélzott ügyfeleknek is megjelenhet.

Ajánlat szintű előzetes előfizetések a szakaszos ajánlatok eléréséhez:

![Előfizetési azonosítók előnézete](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Ha az ajánlat élőben működik, csak a korlátozott célközönségű előfizetések (manuálisan vagy CSV-n keresztül megadott) megtekinthetik és telepíthetik a privát SKU-t. Azt javasoljuk, hogy **mindig a saját előfizetéseit** a saját SKU-ban érvényesítse a korlátozott közönség számára.

>[!NOTE]
>Hibakeresési célból a Microsoft támogatási és mérnöki csapatai is hozzáférhetnek ezekhez a privát ajánlatokhoz.
