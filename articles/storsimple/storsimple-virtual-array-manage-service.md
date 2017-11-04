---
title: "StorSimple Device Manager szolgáltatás üzembe helyezése |} Microsoft Docs"
description: "Azt ismerteti, hogyan hozhat létre, és törölje a StorSimple Device Manager szolgáltatást, az Azure portálon, és a szolgáltatás regisztrációs kulcsának kezelését ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>A StorSimple Device Manager szolgáltatás a StorSimple virtuális tömb üzembe helyezése
## <a name="overview"></a>Áttekintés

A StorSimple Device Manager szolgáltatás a Microsoft Azure-ban és több StorSimple eszközt csatlakozik. Miután létrehozta a szolgáltatást, az eszközök kezeléséhez a Microsoft Azure portálról böngészőjében futó használhatja. Ez lehetővé teszi, hogy egységes, központi helyről, ezáltal az adminisztratív terhek minimalizálása a StorSimple eszköz Manager szolgáltatáshoz csatlakozó eszközök figyelésére.

A StorSimple Device Manager szolgáltatáshoz kapcsolódó gyakori feladatok a következők:

* Szolgáltatás létrehozása
* A szolgáltatás törlése
* Szolgáltatásregisztrációs kulcs lekérése
* A szolgáltatás regisztrációs kulcsának újragenerálása

Ez az oktatóanyag leírja, hogyan hajthat végre az összes fenti feladatot. Ebben a cikkben szereplő információk a tulajdonság csak a StorSimple virtuális tömbök vonatkozik. További információ a StorSimple 8000 Series Ugrás [StorSimple Manager szolgáltatás telepítése](storsimple-manage-service.md).

## <a name="create-a-service"></a>Szolgáltatás létrehozása

Szolgáltatás létrehozása kell rendelkeznie:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Egy aktív Microsoft Azure storage-fiók
* A kezelési használt számlázási adatokat

Választhatja azt is, a storage-fiók létrehozásához, a szolgáltatás létrehozásakor.

Egyetlen szolgáltatás több eszközöket kezelheti. Azonban egy eszköz nem terjedhetnek ki több szolgáltatásra. A nagyvállalatok különböző előfizetések, szervezetek, vagy még a központi telepítési helyek több szolgáltatás példánya is lehet.

> [!NOTE]
> Kezelheti a StorSimple 8000 sorozat eszközeire és a StorSimple virtuális tömbök StorSimple Device Manager szolgáltatás külön példányát kell.


A következő lépésekkel szolgáltatás létrehozása.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>A szolgáltatás törlése

A szolgáltatás törlése előtt győződjön meg arról, hogy nem csatlakoztatott eszközök-k használják. Ha a szolgáltatás használja, kapcsolja ki a csatlakoztatott eszközök. Az inaktiválás művelet-kiszolgálón az eszköz és a szolgáltatás közötti kapcsolat, de a felhőben az eszköz adatok megőrzése érdekében.

> [!IMPORTANT]
> Szolgáltatás törlését követően a művelet nem vonható vissza. Minden olyan eszköz, amely a szolgáltatás használatával lett kell lennie a gyári beállításokra történő visszaállításának egy másik szolgáltatás használata előtt. Ebben a forgatókönyvben az eszközt, valamint a konfiguráció a helyi adatok elvesznek.
 

A következő lépésekkel törölni a szolgáltatást.

#### <a name="to-delete-a-service"></a>A szolgáltatás törlése

1. Ugrás a **összes erőforrás**. Keresse meg a StorSimple eszköz Manager szolgáltatást. Válassza ki a törölni kívánt szolgáltatást.
   
    ![Válassza ki a szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Nyissa meg a szolgáltatás irányítópultot, annak érdekében, hogy nincsenek a szolgáltatáshoz kapcsolódó eszközök. Ha nincsenek a szolgáltatáshoz regisztrált eszközök, ezenkívül megjelenik egy szalagcím üzenetet, amely hatással. Kattintson a **Törlés** gombra.
   
    ![Szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Amikor felszólítja a megerősítésre, kattintson **Igen** a megerősítési értesítés. 
   
    ![Szolgáltatás törlésének megerősítése](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. A szolgáltatás a törlendő néhány percig is eltarthat. Miután a szolgáltatás sikeresen törölt, értesítést fog kapni.
   
    ![Sikeres szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Szolgáltatások listája frissítve lesz.

 ![Szolgáltatás frissített listája](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése
Miután sikeresen létrehozott egy szolgáltatást, szüksége lesz a StorSimple eszköz regisztrálása a szolgáltatásban. Az első StorSimple eszköz regisztrálása, szüksége lesz a szolgáltatás regisztrációs kulcsának. További eszközök regisztrálása egy meglévő StorSimple szolgáltatásban, szüksége lesz a regisztrációs kulcsot, mind a szolgáltatásadat-titkosítási kulcs (amely a regisztráció során van az első eszközön létre). A szolgáltatásadat-titkosítási kulcs kapcsolatos további információkért lásd: [StorSimple biztonsági](storsimple-security.md). A regisztrációs kulcs kaphat fér hozzá a **kulcsok** panel a szolgáltatás.

A következő lépésekkel Szolgáltatásregisztrációs kulcs lekérése.

#### <a name="to-get-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcs beszerzése
1. Az a **StorSimple Device Manager** paneljén lépjen **felügyeleti &gt;**  **kulcsok**.
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Az a **kulcsok** panelen, a szolgáltatás regisztrációs kulcsának jelenik meg. Másolja a regisztrációs kulcs használatával a Másolás ikonra. 

A szolgáltatás regisztrációs kulcsának tartsa biztonságos helyen. Ezt a kulcsot, valamint a szolgáltatásadat-titkosítási kulcs, ez a szolgáltatás további eszközöket regisztrálni kell. Miután beszerezte a szolgáltatás regisztrációs kulcsának, szüksége lesz az eszköz StorSimple felület a Windows PowerShell segítségével konfigurálhatja.

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
Szüksége lesz egy Szolgáltatásregisztrációs kulcs újbóli létrehozása, ha kulcs elforgatás végrehajtásához szükséges, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. Amikor újragenerálja a kulcsot, az új kulccsal csak a következő eszközök regisztrálása. A már regisztrált eszközöket nem érinti ez a folyamat.

A következő lépésekkel egy Szolgáltatásregisztrációs kulcs újragenerálása.

#### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcs újragenerálásához
1. Az a **StorSimple Device Manager** paneljén lépjen **felügyeleti &gt;**  **kulcsok**.
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Az a **kulcsok** panelen kattintson a **újragenerálja**.
   
   ![Kattintson a újragenerálása](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Az a **Szolgáltatásregisztrációs kulcs újragenerálása** panelt, tekintse át a művelet megadása kötelező, ha a kulcsok újragenerálása vannak. Minden a további regisztrált eszközök ezzel a szolgáltatással az új regisztrációs kulcsot fogja használni. Kattintson a **újragenerálja** megerősítéséhez. Értesítést fog kapni a regisztráció befejezése után.
   
   ![Ellenőrizze a kulcs újragenerálása](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Egy új Szolgáltatásregisztrációs kulcs jelenik meg.
   
    ![Ellenőrizze a kulcs újragenerálása](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Másolja ki ezt a kulcsot, és mentse az új eszköz regisztrálása ezt a szolgáltatást.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [Ismerkedés](storsimple-virtual-array-deploy1-portal-prep.md) egy StorSimple virtuális tömbbel rendelkező.
* Megtudhatja, hogyan [felügyelete a StorSimple eszköz](storsimple-ova-web-ui-admin.md).

