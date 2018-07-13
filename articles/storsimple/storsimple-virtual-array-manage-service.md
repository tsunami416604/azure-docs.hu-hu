---
title: StorSimple-Eszközkezelő szolgáltatás üzembe helyezése |} A Microsoft Docs
description: Azt ismerteti, hogyan hozhat létre, és az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás törlése, és a szolgáltatás regisztrációs kulcsának kezelését ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688153"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>A StorSimple Virtual Array StorSimple-Eszközkezelő szolgáltatás üzembe helyezése
## <a name="overview"></a>Áttekintés

A StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple-eszközhöz csatlakozik. Miután létrehozta a szolgáltatást, az eszközök felügyeletére a Microsoft Azure Portalon, a böngészőben futó használhatja. Ez lehetővé teszi, hogy egységes, központi helyről, és ezáltal minimálisra csökkentik az adminisztratív terhek a StorSimple-Eszközkezelő szolgáltatáshoz csatlakoztatott eszközök figyelése.

A StorSimple-Eszközkezelő szolgáltatáshoz kapcsolódó gyakori feladatok a következők:

* Szolgáltatás létrehozása
* A szolgáltatás törlése
* Szolgáltatásregisztrációs kulcs lekérése
* A szolgáltatás regisztrációs kulcsának újragenerálása

Ez az oktatóanyag leírja, hogyan hajthat végre az előző feladatokban mindegyike. Ebben a cikkben található információk csak a StorSimple Virtual Arrayt alkalmazható. A StorSimple 8000 sorozat további információért látogasson el [a StorSimple Manager szolgáltatás üzembe helyezése](storsimple-manage-service.md).

## <a name="create-a-service"></a>Szolgáltatás létrehozása

Szolgáltatás létrehozása, szüksége lesz:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Egy aktív Microsoft Azure storage-fiók
* A számlázási adatokat, amely hozzáférés-kezelés szolgál

Hozzon létre egy tárfiókot, a szolgáltatások létrehozásakor, azt is beállíthatja.

Egy egyetlen szolgáltatással kezelheti a több eszközre. Azonban egy eszköz nem ívelhet át több szolgáltatást. Nagy vállalatok lehet együttműködni különböző előfizetésekben találhatóak, a szervezeteknek, illetve a telepítési helyek még több szolgáltatáspéldány.

> [!NOTE]
> Kezelheti a StorSimple 8000 sorozatú eszközök és virtuális StorSimple-tömbök StorSimple-Eszközkezelő szolgáltatás külön példányát kell.


A következő lépésekkel létrehoz egy szolgáltatást.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez előtt ellenőrizze, hogy nincsenek csatlakoztatott eszközök használják. Ha a szolgáltatás használja, kapcsolja ki a csatlakoztatott eszközök. Az inaktiválás műveletet az eszköz és a szolgáltatás közötti kapcsolat-kiszolgálóhoz, de megőrizni az eszközön lévő adatokat a felhőben.

> [!IMPORTANT]
> A szolgáltatás törlését követően a művelet nem vonható vissza. Bármely eszköz, amely a szolgáltatás használatával lett kell lennie a gyári beállításokat egy másik szolgáltatás a használat előtt. Ebben a forgatókönyvben az eszközt, valamint a konfiguráció a helyi adatok elvesznek.
 

A következő lépésekkel törölheti a szolgáltatást.

#### <a name="to-delete-a-service"></a>Szolgáltatás törlése

1. Lépjen a **összes erőforrás**. Keresse meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki a törölni kívánt szolgáltatást.
   
    ![Válassza ki a szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Nyissa meg a szolgáltatási irányítópult nincsenek a szolgáltatáshoz való kapcsolódás eszközök biztosítása érdekében. Ha nincsenek a szolgáltatásban regisztrált eszközök, a hatás szalagcím üzenetet is látni fogja. Kattintson a **Törlés** gombra.
   
    ![Szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Amikor a rendszer megerősítést kér, kattintson a **Igen** a a megerősítési értesítés. 
   
    ![Erősítse meg a szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Eltarthat néhány percig, a szolgáltatás törlését. Miután a szolgáltatás törlése sikeresen megtörtént, értesíteni fogjuk.
   
    ![Sikeres törlés](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A szolgáltatások listájába frissülnek.

 ![Szolgáltatások frissített listája](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése
Miután sikeresen létrehozott egy szolgáltatás, szüksége lesz a StorSimple-eszköz regisztrálása a szolgáltatással. Az első StorSimple eszköz regisztrálásához, szüksége lesz a szolgáltatás regisztrációs kulcsát. További eszközöket regisztrálni egy meglévő StorSimple szolgáltatásban, szüksége lesz a regisztráció és a szolgáltatásadat-titkosítási kulcs (amely jön létre az első eszköz regisztrációja során). A szolgáltatásadat-titkosítási kulcs kapcsolatos további információkért lásd: [StorSimple biztonsági](storsimple-security.md). A regisztrációs kulcs megkaphassa fér hozzá a **kulcsok** panelen a szolgáltatás.

A következő lépésekkel kérnie a Szolgáltatásregisztrációs kulcsot.

#### <a name="to-get-the-service-registration-key"></a>A Szolgáltatásregisztrációs kulcs lekérése
1. Az a **StorSimple-Eszközkezelő** panelen lépjen a **felügyeleti &gt;**  **kulcsok**.
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Az a **kulcsok** panel, a szolgáltatás regisztrációs kulcsának jelenik meg. Másolja ki a regisztrációs kulcs használatával a másolási ikonra. 

A szolgáltatás regisztrációs kulcsával tartsa biztonságos helyen. Ezt a kulcsot, valamint a szolgáltatásadat-titkosítási kulcs, ez a szolgáltatás további eszközöket regisztrálni kell. Miután beszerezte a szolgáltatás regisztrációs kulcsával, szüksége lesz az eszköz a Windows Powershellen keresztül a StorSimple-adapter konfigurálásához.

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
Szüksége lesz a szolgáltatás regisztrációs kulcsának újragenerálása, ha kulcsrotálás végrehajtásához szükséges, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. Amikor újragenerálja a kulcsot, akkor az új kulcs csak az azt követő eszközök regisztrálása szolgál. A már regisztrált eszközöket nem érinti ez a folyamat.

Hajtsa végre az alábbi lépések végrehajtásával hozza létre újra a Szolgáltatásregisztrációs kulcs.

#### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
1. Az a **StorSimple-Eszközkezelő** panelen lépjen a **felügyeleti &gt;**  **kulcsok**.
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Az a **kulcsok** panelen kattintson a **újragenerálása**.
   
   ![Kattintson a fiókkulcs újbóli létrehozása](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Az a **Szolgáltatásregisztrációs kulcs újragenerálása** panelen, és tekintse át a művelet kötelező, ha a kulcsok újbóli. Az ezt követő eszközök, amelyek regisztrálva vannak a szolgáltatás az új regisztrációs kulcsot fogja használni. Kattintson a **újragenerálása** megerősítéséhez. Értesíteni fogjuk a regisztráció befejezése után.
   
   ![Erősítse meg a kulcs újragenerálása](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Megjelenik egy új regisztrációs kulcsot.
   
    ![Erősítse meg a kulcs újragenerálása](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Másolja ezt a kulcsot, és mentse az új eszközök regisztrálásához a szolgáltatásban.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [Ismerkedés](storsimple-virtual-array-deploy1-portal-prep.md) a StorSimple Virtual Array használatának.
* Ismerje meg, hogyan [felügyelete a StorSimple-eszköz](storsimple-ova-web-ui-admin.md).

