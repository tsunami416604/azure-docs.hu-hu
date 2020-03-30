---
title: Az Azure Data Box Edge eszköz visszaküldése vagy cseréje | Microsoft dokumentumok
description: Bemutatja, hogyan adja vissza vagy cserélje le az Azure Data Box Edge-eszközt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651100"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Az Azure Data Box Edge-eszköz visszaküldése vagy cseréje

Ez a cikk ismerteti, hogyan törölheti az adatokat, és majd adja vissza az Azure Data Box Edge-eszközt. Miután visszaküldte az eszközt, törölheti az eszközhöz társított erőforrást, vagy megrendelhet egy csereeszközt.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az adatok törlése az eszközön lévő adatlemezekről
> * Nyisson meg egy támogatási jegyet az eszköz visszaküldéséhez
> * Csomagolja össze a készüléket, és ütemezze be a felvételt
> * Az erőforrás törlése az Azure Portalon
> * Csereeszköz beszereznie

## <a name="erase-data-from-the-device"></a>Adatok törlése a készülékről

Az adatok nak az eszköz adatlemezeiről való törléséhez alaphelyzetbe kell állítania az eszközt. Alaphelyzetbe állíthatja az eszközt a helyi webes felhasználói felület vagy a PowerShell-felület használatával.

Az alaphelyzetbe állítás előtt szükség esetén hozzon létre egy másolatot a helyi adatokról az eszközön. Az adatokat az eszközről egy Azure Storage-tárolóba másolhatja.

Ha alaphelyzetbe szeretné állítani az eszközt a helyi webes felhasználói felület használatával, kövesse az alábbi lépéseket.

1. A helyi webes felhasználói felületen nyissa meg **a Karbantartás > eszköz alaphelyzetbe állítását.**
2. Válassza **az Eszköz visszaállítása**lehetőséget .

    ![Eszköz alaphelyzetbe állítása](media/data-box-edge-return-device/device-reset-1.png)

3. Amikor megerősítést kér, tekintse át a figyelmeztetést, és a folytatáshoz válassza az **Igen** lehetőséget.

    ![Alaphelyzetbe állítás megerősítése](media/data-box-edge-return-device/device-reset-2.png)  

Az alaphelyzetbe állítás törli az adatokat az eszköz adatlemezeiről. Az eszközön lévő adatok mennyiségétől függően ez a folyamat körülbelül 30-40 percet vesz igénybe.

Másik lehetőségként csatlakozzon az eszköz PowerShell-felületéhez, és a `Reset-HcsAppliance` parancsmag segítségével törölje az adatokat az adatlemezekről. További információt [az Eszköz alaphelyzetbe állítása](data-box-edge-connect-powershell-interface.md#reset-your-device)című témakörben talál.

> [!NOTE]
> - Ha új eszközt cserél vagy frissít, azt javasoljuk, hogy csak az új eszköz érkezése után állítsa alaphelyzetbe az eszközt.
> - Az eszköz alaphelyzetbe állítása csak az összes helyi adatot törli az eszközről. A felhőben lévő adatok nem törlődnek, és [díjakat](https://azure.microsoft.com/pricing/details/storage/)gyűjt. Ezeket az adatokat külön kell törölni egy felhőalapú tárolókezelő eszközzel, például az [Azure Storage Explorer rel.](https://azure.microsoft.com/features/storage-explorer/)

## <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása

A visszaküldési folyamat megkezdéséhez tegye a következő lépéseket.

1. Nyisson meg egy támogatási jegyet a Microsoft támogatási szolgálatával, jelezve, hogy vissza kívánja küldeni az eszközt. Válassza ki a probléma típusát **Data Box Edge Hardware néven.**

    ![Támogatási jegy megnyitása](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. A Microsoft támogatási szakembere felveszi Önnel a kapcsolatot. Adja meg a szállítási adatokat.
3. Ha szüksége van egy vissza szállítási doboz, akkor kérheti azt. Válasz **Igen** a kérdésre **Szükség van egy üres doboz tért vissza**.


## <a name="schedule-a-pickup"></a>Felvétel ütemezése

1. Állítsa le az eszközt. A helyi webes felhasználói felületen nyissa meg **a Karbantartás > energiagazdálkodási beállítások lehetőséget.**
2. Válassza **a Leállítás**lehetőséget. A folytatáshoz kattintson a megerősítés kérésekor az **Igen** gombra. További információt a [Teljesítmény kezelése című](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)témakörben talál.
3. Húzza ki a hálózati kábeleket, és távolítsa el az összes hálózati kábelt a készülékből.
4. Készítse elő a szállítmánycsomagot a saját vagy az Azure-tól kapott üres doboz használatával. Helyezze a készüléket és a készülékhez mellékelt tápkábeleket a dobozba.
5. Rögzítse az Azure-tól kapott szállítási címkét a csomagon.
6. Egyeztessen egy csomagfelvételi időpontot helyi szolgáltatójával. Ha az eszközt az Egyesült Államokban adja vissza, a szolgáltatója lehet ups vagy FedEx. Felvétel ütemezése a UPS-szel:

    1. Hívja a helyi UPS-t (országspecifikus ingyenesen hívható szám).
    2. A hívásban adja meg a fordított szállítmánykövetési számot a nyomtatott címkén látható módon.
    3. Ha a fuvarlevélszámot nem adják meg, a UPS megköveteli, hogy a felvétel során további díjat fizessen.

    A felvétel ütemezése helyett a Data Box Edge-et is leadhatja a legközelebbi leadási helyen.

## <a name="delete-the-resource"></a>Az erőforrás törlése

Miután az eszköz megkapta az Azure adatközpontba, az eszköz ellenőrzi a károkat vagy a jogosulatlan beavatkozás jeleit.

- Ha az eszköz sértetlenül és jó állapotban érkezik, a számlázási mérő leáll az adott erőforráshoz. A Microsoft támogatási szolgálata felveszi Önnel a kapcsolatot, és meggyőződik arról, hogy az eszközt visszaküldték. Ezután törölheti az eszközhöz társított erőforrást az Azure Portalon.
- Ha a készülék jelentősen sérült, pénzbírság keletkezhet. A részleteket az elveszett vagy sérült eszközről és a [termékszolgáltatási feltételekről](https://www.microsoft.com/licensing/product-licensing/products)szóló [GYIK-ben](https://azure.microsoft.com/pricing/details/databox/edge/) találja.  


Törölheti az eszközt az Azure Portalon:
-   Miután letette a megrendelést, és mielőtt a Microsoft elkészíti az eszközt.
-   Miután visszaküldte az eszközt a Microsoftnak, átmegy a fizikai ellenőrzésen az Azure-adatközpontban, és a Microsoft támogatási szolgálata meghívja az eszköz visszaadásának megerősítését.

Ha egy másik előfizetéssel vagy helymeghatározással aktiválta az eszközt, a Microsoft egy munkanapon belül áthelyezi a rendelést az új előfizetésbe vagy helyre. A rendelés áthelyezése után törölheti ezt az erőforrást.


Az alábbi lépésekkel törölje az eszközt és az erőforrást az Azure Portalon.

1. Az Azure Portalon nyissa meg az erőforrást, majd **az Áttekintés ..** A parancssávon válassza a **Törlés gombot.**

    ![Törlés kijelölése](media/data-box-edge-return-device/delete-resource-1.png)

2. Az **Eszköz törlése** panelen írja be a törölni kívánt eszköz nevét, és válassza a **Törlés**lehetőséget.

    ![Törlés megerősítése](media/data-box-edge-return-device/delete-resource-2.png)

Értesítést kap, miután az eszköz és a társított erőforrás sikeresen törlődött.

## <a name="get-a-replacement-device"></a>Csereeszköz beszereznie

Csereeszközre akkor van szükség, ha a meglévő eszköz hardverhibával rendelkezik, vagy frissítést igényel. Az alábbi lépések végrehajtásával, ha az eszköz hardverhiba miatt van:

1. [Nyisson meg egy támogatási jegyet a hardverprobléma esetén.](#open-a-support-ticket) A Microsoft támogatási szolgálata megállapítja, hogy a mezőcsereegység (FRU) nem érhető el ehhez a példányhoz, vagy az eszközhardver-frissítésre szorul. Mindkét esetben a Support megrendel egy csereeszközt.
2. [Hozzon létre egy új erőforrást](data-box-edge-deploy-prep.md#create-a-new-resource) a csereeszközhöz. Ügyeljen arra, hogy jelölje be a Data Box Edge eszköz elleni **jelölőnégyzetet.** 
3. Miután megkapta a csereeszközt, [telepítse](data-box-edge-deploy-install.md) és [aktiválja](data-box-edge-deploy-connect-setup-activate.md) a csereeszközt az új erőforrással szemben.
4. Az eredeti eszköz visszaadásához kövesse az összes lépést:
    1. Nyisson meg egy másik jegyet az eredeti eszköz visszaküldéséhez.
    2. [Törölje az adatokat a készüléken](#erase-data-from-the-device).
    3. [Felvétel ütemezése](#schedule-a-pickup).
    5. [Törölje a](#delete-the-resource) visszaadott eszközhöz társított erőforrást.



## <a name="next-steps"></a>További lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.
