---
title: Az Azure Stack Edge-eszköz visszaküldése | Microsoft Docs
description: Az Azure Stack Edge-eszköz visszaküldését ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 29ecd2e90812da533eb59db71a876b6d23f20918
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067064"
---
# <a name="return-your-azure-stack-edge-device"></a>Az Azure Stack Edge-eszköz visszaküldése

Ez a cikk az adatok törlését és az Azure Stack Edge-eszköz visszaküldését ismerteti. Az eszköz visszaadása után törölheti az eszközhöz társított erőforrást is.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Adatok törlése az eszközön lévő adatlemezekről
> * Eszköz visszaküldésének kezdeményezése Azure Portal
> * Az eszköz becsomagolása és a pickup beosztása
> * Az erőforrás törlése Azure Portal

## <a name="erase-data-from-the-device"></a>Adatok törlése az eszközről

Az eszközön lévő adatlemezek adatainak törléséhez alaphelyzetbe kell állítania az eszközt. Az eszközt a helyi webes felületen vagy a PowerShell-felületen keresztül állíthatja alaphelyzetbe.

Az Alaphelyzetbe állítás előtt hozzon létre egy másolatot a helyi adatmennyiségről az eszközön, ha szükséges. Az adatok az eszközről egy Azure Storage-tárolóba másolhatók.

Az eszköz visszaküldését még az eszköz alaphelyzetbe állítása előtt is elindíthatja. 

Ha az eszközt a helyi webes felületen szeretné visszaállítani, hajtsa végre az alábbi lépéseket.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > eszköz alaphelyzetbe állítása**elemre.
2. Válassza az **eszköz alaphelyzetbe állítása**lehetőséget.

    ![Eszköz alaphelyzetbe állítása](media/azure-stack-edge-return-device/device-reset-1.png)

3. Ha a rendszer megerősítést kér, tekintse át a figyelmeztetést, és kattintson az **Igen** gombra a folytatáshoz.

    ![Alaphelyzetbe állítás megerősítése](media/azure-stack-edge-return-device/device-reset-2.png)  

Az Alaphelyzetbe állítás törli az adat-adatlemezekről. Az eszközön tárolt adatmennyiségtől függően ez a folyamat körülbelül 30-40 percet vesz igénybe.

Másik lehetőségként kapcsolódjon az eszköz PowerShell-felületéhez, és használja a `Reset-HcsAppliance` parancsmagot az adatok adatlemezekről való törléséhez. További információ: [az eszköz alaphelyzetbe állítása](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Ha új eszközre cserél vagy frissít, javasoljuk, hogy csak az új eszköz megérkezése után állítsa alaphelyzetbe az eszközt.
> - Az eszköz alaphelyzetbe állítása csak az eszközön lévő összes helyi adatmezőt törli. A felhőben tárolt adatok nem törlődnek, és gyűjti a [díjakat](https://azure.microsoft.com/pricing/details/storage/). Ezeket az eszközöket külön törölni kell egy felhőalapú tároló-felügyeleti eszköz (például [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)) használatával.

## <a name="initiate-device-return"></a>Eszköz visszaküldésének kezdeményezése

A visszatérési folyamat elindításához hajtsa végre az alábbi lépéseket.

1. Nyissa meg az Azure Stack Edge/Data Box Gateway erőforrást Azure Portal. Az **áttekintésben**nyissa meg a parancssáv a jobb oldali ablaktáblán, majd válassza az **eszköz visszaküldése**lehetőséget. 

    ![1. eszköz visszaküldése](media/azure-stack-edge-return-device/return-device-1.png)  

2. A **visszalépési eszköz** panelen, az **alapszintű részletek**területen:

    1. Adja meg az eszköz sorozatszámát. Az eszköz sorozatszámának beszerzéséhez nyissa meg az eszköz helyi webes FELÜLETét, és válassza az **Áttekintés**lehetőséget.  
    
    ![1. eszköz sorozatszáma](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Adja meg a szolgáltatás címkéjének számát, amely az eszköz egyedi öt vagy több karakteres azonosítója. A szolgáltatás címkéje az eszköz jobb alsó sarkában található (az eszközre való szembenézés során). Húzza ki az információs címkét (ez egy kiugró címke panel). Ez a panel olyan rendszerinformációkat tartalmaz, mint a szolgáltatási címke, a hálózati adapterek, a MAC-címek stb. 
    
    ![1. szolgáltatási címke száma](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. A legördülő listából válassza ki a visszatérés okát.

    ![Eszköz visszaküldése 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. A **szállítás részletei**területen:

    1. Adja meg a nevét, a vállalat nevét és a teljes céges címeket. Adjon meg egy munkahelyi telefonszámot, beleértve a körzetszámot és egy e-mail-azonosítót az értesítéshez.
    2. Ha visszatérési szállítási mezőbe van szüksége, kérheti. Az **Igen** kérdésre adandó válaszhoz **üres mezőt kell visszaadnia**.

    ![Visszaadott eszköz 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Tekintse át az **adatvédelmi feltételeket** , és jelölje be a megtekintett megjegyzéssel rendelkező jelölőnégyzetet, és fogadja el az adatvédelmi feltételeket.

5. Válassza a **visszatérés kezdeményezése**lehetőséget.

    ![4. visszaadott eszköz](media/azure-stack-edge-return-device/return-device-4.png) 

6. Ha az eszköz visszaadja a részleteket, e-mailben értesítheti az Azure Stack Edge operatív csapatot. Használhatja az e-mail-alkalmazást, feltéve, hogy az e-mail-alkalmazás telepítve van és konfigurálva van. Az Adatmásolást is elvégezheti, és e-mailben is elküldheti azokat.

    ![Eszköz visszaküldése 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Ha az Azure Stack Edge-műveleti csapat megkapja az e-mailt, a rendszer elküld egy fordított szállítólevél címkét. Ha ezt a címkét kapja, beállíthatja az eszköz felvételét a szállítóval. 

## <a name="schedule-a-pickup"></a>Pickup beosztása

Az alábbi lépéseket követve ütemezhet egy felvételt.

1. Állítsa le az eszközt. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > energiagazdálkodási beállítások**elemre.
2. Válassza a **Leállítás**lehetőséget. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. További információ: [Manage Power](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Húzza ki az energiaellátási kábeleket, és távolítsa el az összes hálózati kábelt az eszközről.
4. Készítse elő a szállítólevél-csomagot saját vagy az Azure-ból kapott üres mező használatával. Helyezze el az eszközt a dobozba, és az eszközzel szállított tápkábeleket.
5. Helyezze az Azure-ból kapott szállítási címkét a csomagba.
6. Egyeztessen egy csomagfelvételi időpontot helyi szolgáltatójával. Ha visszaadja az eszközt az USA-ban, akkor a fuvarozó a UPS vagy a FedEx lehet. Felvétel beosztása a UPS-sel:

    1. Hívja fel a helyi UPS-t (ország-/régióspecifikus ingyenes szám).
    2. A hívásban adja meg a fordított szállítólevél nyomon követésének számát a nyomtatott címkén látható módon.
    3. Ha a nyomkövetési szám nincs feltüntetve, a UPS-nek további díjat kell fizetnie a felvétel során.

    A felvétel ütemezése helyett az Azure Stack Edge-t is elhúzhatja a legközelebbi legördülő helyen.

## <a name="delete-the-resource"></a>Erőforrás törlése

Miután az eszközt megkapta az Azure-adatközpontban, a rendszer megkeresi az eszközt a károsodás vagy az illetéktelen módosítás jeleinek ellenőrzéséhez.

- Ha az eszköz érintetlenül érkezik, és jó állapotban van, akkor az adott erőforrás számlázási mérőszáma leáll. Microsoft ügyfélszolgálata felveszi Önnel a kapcsolatot, hogy erősítse meg az eszköz visszatérését. Ezután törölheti az eszközhöz társított erőforrást a Azure Portal.
- Ha az eszköz jelentősen megsérült, a bírságok vonatkozhatnak. További részletekért tekintse [meg az elveszett vagy sérült eszköz](https://azure.microsoft.com/pricing/details/databox/edge/) és a [termék használati feltételeivel](https://www.microsoft.com/licensing/product-licensing/products)kapcsolatos gyakori kérdéseket.  


Az eszközt törölheti a Azure Portalban:

- Miután elvégezte a rendelést, és az eszközt a Microsoft előkészítette.
- Miután visszaadta az eszközt a Microsoftnak, átadja a fizikai ellenőrzést az Azure-adatközpontban, és Microsoft ügyfélszolgálata hívásokat annak megerősítéséhez, hogy az eszköz vissza lett-e küldve.

Ha aktiválta az eszközt egy másik előfizetéshez vagy helyhez, a Microsoft egy munkanapon belül áthelyezi a rendelését az új előfizetésre vagy helyre. A megrendelés áthelyezése után törölheti ezt az erőforrást.


A következő lépésekkel törölheti az eszközt és az erőforrást Azure Portalban.

1. A Azure Portal lépjen az erőforráshoz, majd az **Áttekintés**elemre. A parancssorban válassza a **Törlés**lehetőséget.

    ![Törlés kiválasztása](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Az **eszköz törlése** panelen írja be a törölni kívánt eszköz nevét, és válassza a **Törlés**lehetőséget.

    ![Törlés megerősítése](media/azure-stack-edge-return-device/delete-resource-2.png)

Az eszköz és a hozzá tartozó erőforrás sikeres törlése után értesítést kap.


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [szerezhet be helyettesítő Azure stack Edge-eszközt](azure-stack-edge-replace-device.md).
