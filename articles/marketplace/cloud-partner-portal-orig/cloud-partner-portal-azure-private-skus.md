---
title: Privát SKU-k és a tervek |} A Microsoft Docs
description: Hogyan kezelheti az ajánlatok elérhetőségéről privát SKU-k használatával.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: dcc8b9fef75ba13b607d7fdda5bd55cc710165b9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837014"
---
<a name="private-skus-and-plans"></a>Privát termékváltozatok és csomagok
============

Privát SKU-k lehetővé teszik a termékváltozatok rendelkezésre állásának korlátozása egy adott ügyfélnek. A Termékváltozat privát van megjelölve, ha nem érhető el a bármely nyilvános katalógus, amely a [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és a [az Azure portal](https://portal.azure.com). Az Azure Portalon csak a Termékváltozat-hozzáféréssel rendelkező felhasználók láthatják őket. Ezenkívül, akkor is lehet felszólítja a felhasználót szóló ajánlatok hozzáféréssel rendelkeznek.

>[!NOTE]
>Privát termékváltozatok rendelkeznie kell új egyedi Termékváltozata vagy előfizetése azonosító bármelyik a nyilvános termékváltozatok való ütközés elkerülése érdekében.

Privát SKU-k segítségével kezeli a következő esetekben:

1.  Tegye közzé a szoftver csak adott felhasználók számára a nyilvánosan elérhető, és nyilvánosan elérhető.
2.  Tegye közzé az adott ügyfélnek a testre szabott áron nyilvános szoftver változata létezik.
3.  Tegye közzé a testre szabott leírását és a feltételek (keresztül új ajánlat) nyilvános szoftver változata.

Ha szeretné módosítani az árat, felhasználhatja ugyanazt az ajánlatban Termékváltozatáról egy másikra a lemezeket. A privát SKU-k nem kell küldje el újra a lemezeket termékváltozatok között.

<a name="mark-a-sku-private"></a>A Termékváltozat privát megjelölése
---------------------

Egy privát Termékváltozatot megjelöléséhez váltsa át a beállítást, a Termékváltozat-e privát kéri:

![A Termékváltozat megjelölése privát](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Újból felhasználhatja a lemezek Termékváltozatáról egy másikra, és módosítani a díjszabási vagy leírása. Újból felhasználhatja a lemezeket, jelölje be **Igen** a "nem a Termékváltozat újra felhasználhatja a Termékváltozat nyilvános lemezképeket" válaszul kérni.

Ha a Termékváltozat személyesként van megjelölve, és az ajánlat a többi termékváltozat reuseable lemezekkel rendelkezik, is jelzi, hogy a Termékváltozat újból felhasználja a lemezeket egy másik Termékváltozat szükséges. Ön egyúttal a célközönség privát termékváltozat megadása kötelező.

>[!NOTE]
>Miután közzé van téve, nem hajtható végre saját nyilvános Termékváltozat.

<a name="select-an-image"></a>Kép kiválasztása
------------------

Adja meg az új lemezeket privát termékváltozat vagy újból felhasználhatja a megadott már egy másik SKU, csak a díjszabás módosítása vagy leírás azonos lemezek is. Újból felhasználhatja a lemezeket, jelölje be **Igen** az "Ez a Termékváltozat újra felhasználhatja kép Does egy nyilvános termékváltozatból" kérdésre adott válaszként.

![Jelzi a rendszerkép újból használni.](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Követően, győződjön meg arról, hogy a Termékváltozat újból felhasználja az Termékváltozatáról egy másikra képeit, meg kell határoznia a Termékváltozat, amely a képek forrását.

A következő képernyőn megjelenő utasításokat a privát Termékváltozat azonosítása lenne újra felhasználhatja a képeket a kiválasztott Termékváltozat show rögzítése:

![Kép kiválasztása](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Az ajánlat közzétételekor a képeket a kiválasztott Termékváltozat érhető el az egyéni díjszabás és használati privát SKU-azonosítója alapján kerül sor. A privát Termékváltozat csak akkor látható a célközönséghez.

Lemezkép frissítéseinek, csak szükségesek, ha az alapul szolgáló Termékváltozat-lemezképét. A színfalak mögött privát termékváltozat a képet fogja is automatikusan frissülnek. Ehhez hasonlóan az alapul szolgáló Termékváltozat törli a lemezképet, ha a kép lenne is távolítható el a privát Termékváltozat.

<a name="restricting-the-audience"></a>A célközönség korlátozása
------------------------

Saját ajánlatok is található, és csak a megcélzott felhasználók által telepített.
Előfizetés azonosítókat használó felhasználók jelenleg.

Ezeket az előfizetéseket lehet megadni egy manuális nevezési űrlapot **legfeljebb 10 előfizetések**, vagy egy CSV-fájl feltöltésével, amely lehetővé teszi, hogy **legfeljebb 20 000 előfizetések**.

Manuális bejegyzés korlátozott közönség számára:

![Manuálisan a célközönség korlátozása](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Fürt megosztott kötetei szolgáltatás feltöltés korlátozott közönség számára:

![Fürt megosztott kötetei szolgáltatás használatával korlátozhatja a célközönség](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Minta CSV-fájl tartalma:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Váltson át manuális bejegyzés csv-fájlba, töltse fel a nézet vagy fürtkötetről való manuális bejegyzés előfizetés azonosítókat a Termékváltozat-hozzáféréssel rendelkező régi listája nem őrződnek meg. A figyelmeztetés akkor jelenik meg, és a listában csak a rendszer felülírja az ajánlat mentése után.

<a name="sync-private-subscriptions"></a>Szinkronizálási saját előfizetések
-------------------------

Előfizetések hozzáadása egy privát Termékváltozat vagy a csomag közzétett ajánlatra, amikor nem kell újból közzéteszi a célközönség tudnivalókkal az ajánlatot. Egy Azure-előfizetés-azonosítója (a csomagok és az SKU-k) vagy a bérlő azonosítója (csak csomagok) egyszerűen használnak, célközönség hozzá.

<a name="previewing-private-offers"></a>Előnézet privát kínál
-------------------------

Az előnézet/előkészítés során lépést, csak az ajánlat szint előzetes előfizetések érhetik el a Termékváltozat. Ez a tesztelési fázisban ekkor ellenőrizheti, mely az ajánlat kellene kinéznie a megcélzott felhasználók, és minden típusú közzététel standard.

Ajánlat szint előzetes előfizetések előkészített ajánlatok eléréséhez:

![Előfizetés azonosítókat előzetes verzió](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Miután az ajánlat élő, csak a korlátozott közönség előfizetések (Manuális bejegyzés vagy CSV-n keresztül megadott) tudja megnézni és üzembe helyezése a privát Termékváltozat lesz. Azt javasoljuk, hogy Ön **mindig tartalmazza a korlátozott közönség a saját előfizetések** ellenőrzési célból privát termékváltozat.

>[!NOTE]
>Hibakeresési célokra a Microsoft támogatási és mérnöki csapataival is megkapják ezeket szóló ajánlatok a hozzáférést.
