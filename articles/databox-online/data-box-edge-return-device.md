---
title: Vissza az Azure Data Box Edge-eszköz |} A Microsoft Docs
description: Lépjen vissza az Azure Data Box Edge-eszköz, és törölni a megrendelést, az eszköz módját ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468604"
---
# <a name="return-your-azure-data-box-edge-device"></a>Vissza az Azure Data Box Edge-eszköz

Ez a cikk ismerteti az adatok törlése, és visszaadja az Azure Data Box Edge-eszköz. Miután az eszköz már ad vissza, az eszközhöz társított erőforrás is törölheti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az eszköz ki az adatlemezeket az adatok törlése
> * Nyisson egy támogatási jegyet visszaszolgáltatni az eszközt
> * Az eszköz összegyűjtheti és ütemezhet begyűjtést
> * Törölje az erőforrást az Azure Portalon

## <a name="erase-data-from-the-device"></a>Az eszköz adatainak törlése

Az adatlemezeket az eszköz ki az adatok törlése, alaphelyzetbe állíthatja az eszközt kell. Alaphelyzetbe állíthatja az eszköz helyi webes felületén vagy a PowerShell felület használatával.

Mielőtt alaphelyzetbe állítja, létre kell hoznia a helyi adatokat az eszközön szükség esetén. Egy Azure Storage-tárolóba másolhatja az adatokat az eszközről.

Alaphelyzetbe állít egy eszközt a helyi webes felületén, hajtsa végre a következő lépéseket.

1. A helyi webes felhasználói felületen váltson **karbantartási > eszköz alaphelyzetbe állítása**.
2. Válassza ki **eszköz alaphelyzetbe állítása**.

    ![Eszköz alaphelyzetbe állítása](media/data-box-edge-return-device/device-reset-1.png)

3. Amikor a rendszer megerősítést kér, tekintse át a figyelmeztetést, és válasszon **Igen** folytatásához.

    ![Visszaállítás megerősítése](media/data-box-edge-return-device/device-reset-2.png)  

Az alaphelyzetbe állítás törli az adatokat az eszköz adatainak lemezekről. Az eszközön lévő adatok mennyiségétől függően a folyamat körülbelül 30 – 40 percet vesz igénybe.

Azt is megteheti, az eszköz és használata PowerShell-felületén csatlakozni a `Reset-HcsAppliance` parancsmag az adatlemezeket az adatok törlésére. További információkért lásd: [alaphelyzetbe állíthatja az eszközt](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Ha egy új eszköz frissítése vagy cseréje, azt javasoljuk, hogy az eszköz alaphelyzetbe állításakor csak azt követően érkezett-e az új eszköz.
> - Az eszköz alaphelyzetbe állítása csak törli az összes helyi adat ki az eszközt. Az adatok a felhőben nem törlődik, és összegyűjti a [díjak](https://azure.microsoft.com/pricing/details/storage/). Ezek az adatok külön-külön használatával egy felhőalapú tárolási felügyeleti eszköz, például akit törölni szükséges [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Hozzon létre egy támogatási jegyet

Az alábbi lépéseket a visszatérési megkezdéséhez.

1. Nyisson meg egy támogatási jegyet Support jelzi, hogy szeretné-e az eszköz visszaadása. Válassza ki a problémát, **Data Box Edge hardver**.

    ![Támogatási jegy megnyitása](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. A Microsoft Support szakember felveszi Önnel a kapcsolatot. Adja meg a szállítással kapcsolatos fejleményekről.
3. Ha a visszatérési szállítási mezőben van szüksége, kérheti. Válasz **Igen** kérdésre **kell egy üres mező való visszatéréshez**.


## <a name="schedule-a-pickup"></a>Ütemezhet begyűjtést

1. Állítsa le az eszközt. A helyi webes felhasználói felületen váltson **karbantartási > energiaellátási beállítások**.
2. Válassza ki **Leállítás**. Amikor a rendszer megerősítést kér, kattintson a **Igen** folytatásához. További információkért lásd: [power kezelése](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Válassza le a kábelek, és minden hálózati kábel eltávolítása az eszközről.
4. Készítse elő a a szállítás csomagot a saját vagy a üres be Azure-tól kapott segítségével. Helyezze el az eszközt, és a tápkábelek teljesített a mezőbe az eszközzel.
5. A szállítási címkét, hogy az Azure-ból a csomag elhelyezi.
6. Ütemezhet begyűjtést a regionális szolgáltató. Ha az Egyesült Államok ad vissza az eszközt, a szolgáltató UPS. Ütemezhet begyűjtést:

    1. Hívja meg a helyi UPS (országspecifikus díjmentesen ingyenes szám).
    2. A hívás az ajánlat a fordított szállítmány követési szám a nyomtatott felirat látható módon.
    3. A nyomkövetési azonosító szám nem ajánlott, ha a UPS kell, hogy külön díj fizetési során begyűjtést.

    Ahelyett, hogy a begyűjtés ütemezés is húzhatja a Data Box Edge, a legközelebbi gyűjtőhely ki.

## <a name="delete-the-resource"></a>Az erőforrás törlése

Az eszköz az Azure-adatközpont fogadását követően az eszköz sérülés vagy bármely illetéktelen jeleit van jutnak.

- Ha az eszköz nem sérültek, és jó minősége megérkezik, a számlázási mérőszámok leállítja, az adott erőforráshoz. A Microsoft Support felveszi Önnel a kapcsolatot annak ellenőrzéséhez, hogy az eszköz adott vissza. Megvan, törölheti az Azure Portalon az eszközhöz társított erőforrás.
- Ha az eszköz megérkezik jelentősen sérült, a bírságok lehetnek érvényben. További információkért lásd: a [gyakori kérdések az elveszett vagy sérült Data boxért](https://azure.microsoft.com/pricing/details/databox/edge/) és [a termék használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products).  


Törölheti az eszközt az Azure Portalon:
-   A rendelés elhelyezett és előtt az eszközt a Microsoft kész.
-   A Microsoft már az eszköz visszatér, miután átadja a fizikai vizsgálata az Azure-adatközpontban található, és Support meghívja annak ellenőrzéséhez, hogy az eszköz adott vissza.

Miután aktiválta az eszközt egy másik előfizetéssel vagy hellyel szemben, ha a Microsoft áthelyezi a rendeléséhez az új előfizetést vagy helyet egy munkanapon belül. A rendelés helyezik, miután ezt az erőforrást törölheti.


A következő lépésekkel törölheti az eszköz és az erőforrást az Azure Portalon.

1. Az Azure Portalon nyissa meg az erőforrás majd **áttekintése**. A parancssávon válassza ki a **törlése**.

    ![Válassza a Törlés](media/data-box-edge-return-device/delete-resource-1.png)

2. Az a **eszköz törlése** panelen adja meg az eszköz törlése, és válassza ki a **törlése**.

    ![Törlés jóváhagyása](media/data-box-edge-return-device/delete-resource-2.png)

Az eszköz után értesítést kap arról, és a társított erőforrás törlése sikerült.

## <a name="next-steps"></a>További lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.
