---
title: "Ellenőrizze az átviteli sebesség és a késleltetés metrikákat a tárfiókon, az Azure portálon |} Microsoft Docs"
description: "Ismerje meg, hogyan ellenőrizhető az átviteli sebesség és a késleltetés metrikák egy tárfiók a portálon."
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: b3102bd4e40e10fe88c12295794da37e359c56f1
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>A tárfiók átviteli sebesség és a késleltetés metrikáját ellenőrzése

Ez az oktatóanyag négy és egy sor utolsó részét. Az előző oktatóanyagok megtanulta, hogyan larges adatmennyiségek véletlenszerű le az Azure storage-fiók- és feltöltése. Az oktatóanyag bemutatja, hogyan használhatja metrikák átviteli sebesség és a késleltetés megtekintéséhez az Azure portálon.

A sorozat négy része a megismerheti, hogyan:

> [!div class="checklist"]
> * Diagramok konfigurálása az Azure-portálon
> * Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése

[Az Azure storage mérőszámainak](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Azure figyelő használja azokat a teljesítmény és rendelkezésre állását a tárfiók egyesített áttekintése.

## <a name="configure-metrics"></a>Metrikáinak megadása

Navigáljon a **metrikák (előzetes verzió)** alatt **beállítások** tárfiókba.

Válassza ki a Blob a **SUB szolgáltatás** legördülő listán.

A **METRIKA**, válasszon egyet a következő táblázatban található a metrikák:

A következő mérőszámok segítenek a késés és az alkalmazás átviteli sebesség. Adja meg a portálon a metrikák 1 perces átlagok szerepelnek. A tranzakció befejeződött egy percet közepén, hogy a percben adatok is, ha az átlagos halfed. Az alkalmazás feltöltése és letöltéséhez műveletek lettek túllépte az időkorlátot, és a tényleges időtartama a kimeneti megadott végrehajtásának a fájlok feltöltését és letöltését. Ezt az információt a teljes megértéséhez az átviteli sebesség a portál metrikák együtt használható.

|Metrika|Meghatározás|
|---|---|
|**Sikeres E2E késés**|A társzolgáltatás vagy a megadott API-művelet sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|
|**Sikeres kiszolgáló késleltetése**|Az Azure Storage által a sikeres kérelmek feldolgozásához használt átlagos ideje. Ez az érték nem tartalmazza a hálózati késés megadott SuccessE2ELatency. |
|**Tranzakciók**|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. A sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelmek tartozik. A példában a blokkméret 100 MB-ra állította be. Ebben az esetben minden 100 MB-os blokk tranzakció minősül.|
|**Érkező**|A érkező adatok mennyisége. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban. |
|**Kimenő forgalom**|A kimenő adatok mennyisége. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom. |

Válassza ki **utolsó 24 óra (automatikus)** melletti **idő**. Válasszon **elmúlt egy órában** és **perc** a **részletességet**, majd kattintson a **alkalmaz**.

![Tárolási fiók metrikák](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Több mint egy metrika a hozzájuk rendelt is lehet, de több metrika hozzárendelése letiltja csoport szerint.

## <a name="dimensions"></a>Dimenziók

[Dimenziók](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) mélyebb megismerhetők a diagramok és részletesebb információért használt. Különböző metrikák rendelkezik a különböző dimenziókban. Egy dimenzió, amely elérhető a **API-név** dimenzió. Ez a dimenzió megszakadás a diagram az egyes különálló API-hívás. Az első az alábbi képen látható egy példa a diagram teljes tranzakciók tárfiókok esetén. A második ábrán ugyanaz a diagramterület, de API-val nevet a kiválasztott dimenzió. Ahogy látja, minden tranzakció szerepel jogosultságot ad a további részleteket a hány hívások tett API-név.

![Tárolási fiók metrikák - tranzakciók nélkül dimenzió](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Tárolási fiók metrikák - tranzakciók](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez az szükséges, válassza ki az erőforráscsoportot a virtuális gép számára, és válassza a törlés.

## <a name="next-steps"></a>Következő lépések

A sorozat négy része megismerte a megoldást, például hogy miként metrikáját megtekintése:

> [!div class="checklist"]
> * Diagramok konfigurálása az Azure-portálon
> * Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése

Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített tárolási minták.

> [!div class="nextstepaction"]
> [Az Azure storage parancsfájl minták](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md