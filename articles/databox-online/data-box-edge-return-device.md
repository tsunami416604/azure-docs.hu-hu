---
title: Azure Data Box Edge eszköz visszaküldése vagy cseréje | Microsoft Docs
description: Leírja, hogyan lehet visszaadni vagy lecserélni az Azure Data Box Edge eszközt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651100"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Azure Data Box Edge eszköz visszaküldése vagy cseréje

Ez a cikk az adatok törlését és a Azure Data Box Edge eszköz visszaküldését ismerteti. Az eszköz visszaadása után törölheti az eszközhöz társított erőforrást, vagy megrendelheti a helyettesítő eszközt.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Adatok törlése az eszközön lévő adatlemezekről
> * Támogatási jegy megnyitása az eszköz visszaküldéséhez
> * Az eszköz becsomagolása és a pickup beosztása
> * Az erőforrás törlése Azure Portal
> * Helyettesítő eszköz beszerzése

## <a name="erase-data-from-the-device"></a>Adatok törlése az eszközről

Az eszközön lévő adatlemezek adatainak törléséhez alaphelyzetbe kell állítania az eszközt. Az eszközt a helyi webes felületen vagy a PowerShell-felületen keresztül állíthatja alaphelyzetbe.

Az Alaphelyzetbe állítás előtt hozzon létre egy másolatot a helyi adatmennyiségről az eszközön, ha szükséges. Az adatok az eszközről egy Azure Storage-tárolóba másolhatók.

Ha az eszközt a helyi webes felületen szeretné visszaállítani, hajtsa végre az alábbi lépéseket.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > eszköz alaphelyzetbe állítása**elemre.
2. Válassza az **eszköz alaphelyzetbe állítása**lehetőséget.

    ![Eszköz alaphelyzetbe állítása](media/data-box-edge-return-device/device-reset-1.png)

3. Ha a rendszer megerősítést kér, tekintse át a figyelmeztetést, és kattintson az **Igen** gombra a folytatáshoz.

    ![Alaphelyzetbe állítás megerősítése](media/data-box-edge-return-device/device-reset-2.png)  

Az Alaphelyzetbe állítás törli az adat-adatlemezekről. Az eszközön tárolt adatmennyiségtől függően ez a folyamat körülbelül 30-40 percet vesz igénybe.

Azt is megteheti, hogy az eszköz PowerShell-felületéhez csatlakozik, és a `Reset-HcsAppliance` parancsmag használatával törli az adatlemezekről származó adatokból. További információ: [az eszköz alaphelyzetbe állítása](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Ha új eszközre cserél vagy frissít, javasoljuk, hogy csak az új eszköz megérkezése után állítsa alaphelyzetbe az eszközt.
> - Az eszköz alaphelyzetbe állítása csak az eszközön lévő összes helyi adatmezőt törli. A felhőben tárolt adatok nem törlődnek, és gyűjti a [díjakat](https://azure.microsoft.com/pricing/details/storage/). Ezeket az eszközöket külön törölni kell egy felhőalapú tároló-felügyeleti eszköz (például [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)) használatával.

## <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása

A visszatérési folyamat elindításához hajtsa végre az alábbi lépéseket.

1. Nyisson meg egy támogatási jegyet Microsoft ügyfélszolgálata jelezve, hogy vissza kívánja adni az eszközt. Válassza ki a probléma típusát **Data Box Edge hardverként**.

    ![Támogatási jegy megnyitása](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Egy Microsoft ügyfélszolgálata mérnök felveszi Önnel A kapcsolatot. Adja meg a szállítási adatokat.
3. Ha visszatérési szállítási mezőbe van szüksége, kérheti. Az **Igen** kérdésre adandó válaszhoz **üres mezőt kell visszaadnia**.


## <a name="schedule-a-pickup"></a>Pickup beosztása

1. Állítsa le az eszközt. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > energiagazdálkodási beállítások**elemre.
2. Válassza a **Leállítás**lehetőséget. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. További információ: [Manage Power](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Húzza ki az energiaellátási kábeleket, és távolítsa el az összes hálózati kábelt az eszközről.
4. Készítse elő a szállítólevél-csomagot saját vagy az Azure-ból kapott üres mező használatával. Helyezze el az eszközt a dobozba, és az eszközzel szállított tápkábeleket.
5. Helyezze az Azure-ból kapott szállítási címkét a csomagba.
6. Egyeztessen egy csomagfelvételi időpontot helyi szolgáltatójával. Ha visszaadja az eszközt az USA-ban, akkor a fuvarozó a UPS vagy a FedEx lehet. Felvétel beosztása a UPS-sel:

    1. Hívja meg a helyi UPS-t (országspecifikus díjmentes szám).
    2. A hívásban adja meg a fordított szállítólevél nyomon követésének számát a nyomtatott címkén látható módon.
    3. Ha a nyomkövetési szám nincs feltüntetve, a UPS-nek további díjat kell fizetnie a felvétel során.

    A pickup ütemezése helyett a Data Box Edge a legközelebbi legördülő helyen is elvégezheti.

## <a name="delete-the-resource"></a>Erőforrás törlése

Miután az eszközt megkapta az Azure-adatközpontban, a rendszer megkeresi az eszközt a károsodás vagy az illetéktelen módosítás jeleinek ellenőrzéséhez.

- Ha az eszköz érintetlenül és jó formában érkezik, az adott erőforrás számlázási mérőszáma leáll. Microsoft ügyfélszolgálata felveszi Önnel a kapcsolatot, hogy erősítse meg az eszköz visszatérését. Ezután törölheti az eszközhöz társított erőforrást a Azure Portal.
- Ha az eszköz jelentősen megsérült, a bírságok vonatkozhatnak. További részletekért tekintse [meg az elveszett vagy sérült eszköz](https://azure.microsoft.com/pricing/details/databox/edge/) és a [termék használati feltételeivel](https://www.microsoft.com/licensing/product-licensing/products)kapcsolatos gyakori kérdéseket.  


Az eszközt törölheti a Azure Portalban:
-   Miután elvégezte a rendelést, és az eszközt a Microsoft előkészítette.
-   Miután visszaadta az eszközt a Microsoftnak, átadja a fizikai ellenőrzést az Azure-adatközpontban, és Microsoft ügyfélszolgálata hívásokat annak megerősítéséhez, hogy az eszköz vissza lett-e küldve.

Ha aktiválta az eszközt egy másik előfizetéshez vagy helyhez, a Microsoft egy munkanapon belül áthelyezi a rendelését az új előfizetésre vagy helyre. A megrendelés áthelyezése után törölheti ezt az erőforrást.


A következő lépésekkel törölheti az eszközt és az erőforrást Azure Portalban.

1. A Azure Portal lépjen az erőforráshoz, majd az **Áttekintés**elemre. A parancssorban válassza a **Törlés**lehetőséget.

    ![Törlés kiválasztása](media/data-box-edge-return-device/delete-resource-1.png)

2. Az **eszköz törlése** panelen írja be a törölni kívánt eszköz nevét, és válassza a **Törlés**lehetőséget.

    ![Törlés megerősítése](media/data-box-edge-return-device/delete-resource-2.png)

Az eszköz és a hozzá tartozó erőforrás sikeres törlése után értesítést kap.

## <a name="get-a-replacement-device"></a>Helyettesítő eszköz beszerzése

Szükség van egy helyettesítő eszközre, ha a meglévő eszköz hardveres hibával rendelkezik, vagy frissítésre van szüksége. Az eszköz hardveres hibája esetén hajtsa végre a következő lépéseket:

1. [Nyisson meg egy támogatási jegyet a hardveres probléma megoldásához](#open-a-support-ticket). Microsoft ügyfélszolgálata megállapítja, hogy ehhez a példányhoz nem érhető el egy mező típusú helyettesítő egység, vagy az eszköznek hardveres frissítésre van szüksége. Mindkét esetben a támogatás egy helyettesítő eszközt fog rendelni.
2. [Hozzon létre egy új erőforrást](data-box-edge-deploy-prep.md#create-a-new-resource) a helyettesítő eszközhöz. Ügyeljen arra, hogy jelölje be a jelölőnégyzetet a **Data Box Edge eszközön**. 
3. Miután megkapta a helyettesítő eszközt, [telepítse](data-box-edge-deploy-install.md) és [aktiválja](data-box-edge-deploy-connect-setup-activate.md) a helyettesítő eszközt az új erőforráson.
4. Az eredeti eszköz visszaküldéséhez kövesse az összes lépést:
    1. Nyisson meg egy másik jegyet az eredeti eszköz visszaküldéséhez.
    2. [Az eszközön lévő adat törlése](#erase-data-from-the-device).
    3. [Pickup beosztása](#schedule-a-pickup).
    5. [Törölje a](#delete-the-resource) visszaadott eszközhöz hozzárendelt erőforrást.



## <a name="next-steps"></a>Következő lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.
