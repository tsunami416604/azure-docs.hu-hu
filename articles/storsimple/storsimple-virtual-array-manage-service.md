---
title: StorSimple Eszközkezelő szolgáltatás telepítése | Microsoft dokumentumok
description: Bemutatja, hogyan hozhat létre és törölheti a StorSimple Eszközkezelő szolgáltatást az Azure Portalon, és ismerteti a szolgáltatás regisztrációs kulcsának kezelését.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267455"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás telepítése a StorSimple virtuális tömbhöz

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A StorSimple Eszközkezelő szolgáltatás fut a Microsoft Azure-ban, és több StorSimple-eszközhöz csatlakozik. Miután létrehozta a szolgáltatást, használhatja az eszközök kezelésére a Microsoft Azure portal fut a böngészőben. Ez lehetővé teszi a StorSimple Eszközkezelő szolgáltatáshoz egyetlen központi helyről csatlakoztatott összes eszköz figyelését, ezáltal minimalizálva a felügyeleti terheket.

A StorSimple Eszközkezelő szolgáltatással kapcsolatos gyakori feladatok a következők:

* Szolgáltatás létrehozása
* Szolgáltatás törlése
* Szolgáltatásregisztrációs kulcs lekérése
* A szolgáltatás regisztrációs kulcsának újragenerálása

Ez az oktatóanyag ismerteti, hogyan hajthatja végre az egyes előző feladatokat. Az ebben a cikkben található információk csak a StorSimple virtuális tömbökre vonatkoznak. A StorSimple 8000 sorozattal kapcsolatos további információkért [látogasson el a StorSimple Manager szolgáltatás telepítéséhez.](storsimple-manage-service.md)

## <a name="create-a-service"></a>Szolgáltatás létrehozása

Szolgáltatás létrehozásához a következőkre van szükség:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Aktív Microsoft Azure tárfiók
* A hozzáférés-kezeléshez használt számlázási adatok

Azt is választhatja, hogy hozzon létre egy tárfiókot, amikor létrehozza a szolgáltatást.

Egyetlen szolgáltatás több eszközt is kezelhet. Egy eszköz azonban nem terjedhet ki több szolgáltatásra. Egy nagyvállalat több szolgáltatáspéldánysal is rendelkezhet különböző előfizetésekkel, szervezetekkel vagy akár üzembe helyezési helyekkel.

> [!NOTE]
> A StorSimple Device Manager szolgáltatás külön példányaira van szükség a StorSimple 8000 sorozatú eszközök és a StorSimple virtuális tömbök kezeléséhez.


A szolgáltatás létrehozásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Szolgáltatás törlése

A szolgáltatás törlése előtt győződjön meg arról, hogy nem használják a csatlakoztatott eszközök. Ha a szolgáltatás használatban van, inaktiválja a csatlakoztatott eszközöket. Az inaktiválási művelet megszakítja az eszköz és a szolgáltatás közötti kapcsolatot, de megőrzi az eszköz adatait a felhőben.

> [!IMPORTANT]
> A szolgáltatás törlése után a művelet nem vonható vissza. A szolgáltatást használó eszközöknek gyári alaphelyzetbe kell állítaniuk, mielőtt egy másik szolgáltatással használható lenne. Ebben a forgatókönyvben az eszközön lévő helyi adatok, valamint a konfiguráció elvesznek.
 

A szolgáltatás törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-service"></a>Szolgáltatás törlése

1. Lépjen a **Minden erőforrás** menüpontra. Keresse meg a StorSimple Eszközkezelő szolgáltatást. Válassza ki a törölni kívánt szolgáltatást.
   
    ![A törölni kívánt szolgáltatás kiválasztása](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Lépjen a szolgáltatás irányítópultjára, és győződjön meg arról, hogy nincsenek eszközök a szolgáltatáshoz csatlakoztatva. Ha nincs enek regisztrálva eszköz a szolgáltatáshoz, akkor egy szalagcímüzenet is megjelenik. Kattintson a **Törlés** gombra.
   
    ![Szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Amikor megerősítést kér, kattintson az **Igen** gombra a megerősítő értesítésben. 
   
    ![Szolgáltatás törlésének megerősítése](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. A szolgáltatás törlése eltarthat néhány percig. A szolgáltatás sikeres törlése után értesítést kap.
   
    ![Sikeres szolgáltatástörlés](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A szolgáltatások listája frissül.

 ![A szolgáltatások frissített listája](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése
Miután sikeresen létrehozott egy szolgáltatást, regisztrálnia kell a StorSimple-eszközt a szolgáltatással. Az első StorSimple-eszköz regisztrálásához szüksége lesz a szolgáltatás regisztrációs kulcsára. További eszközök regisztrálásához egy meglévő StorSimple szolgáltatás, szüksége lesz mind a regisztrációs kulcs és a szolgáltatás adattitkosítási kulcs (amely a regisztráció során az első eszközön jön létre). A szolgáltatás adattitkosítási kulcsáról további információt a [StorSimple biztonság című](storsimple-security.md)témakörben talál. A regisztrációs kulcsot a szolgáltatás **Keys** paneljének elérésével szerezheti be.

Hajtsa végre az alábbi lépéseket a szolgáltatás regisztrációs kulcsának levételéhez.

#### <a name="to-get-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának beszerezése
1. A **StorSimple Eszközkezelő** panelen nyissa meg a **Felügyeleti &gt; ** **kulcsok lapot.**
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. A **Kulcsok** panelen megjelenik egy szolgáltatásregisztrációs kulcs. Másolja a regisztrációs kulcsot a másolásikonra. 

Tartsa a szolgáltatás regisztrációs kulcsát biztonságos helyen. Szüksége lesz erre a kulcsra, valamint a szolgáltatás adattitkosítási kulcsára, hogy további eszközöket regisztrálhasson ezzel a szolgáltatással. A szolgáltatás regisztrációs kulcsának beszerzése után konfigurálnia kell az eszközt a Windows PowerShell storSimple-kapcsolaton keresztül.

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
Újra kell generálnia egy szolgáltatásregisztrációs kulcsot, ha kulcselforgatást kell végrehajtania, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. A kulcs újragenerálásakor az új kulcs csak a későbbi eszközök regisztrálására szolgál. A már regisztrált eszközöket ez a folyamat nem érinti.

A szolgáltatásregisztrációs kulcs újragenerálásához hajtsa végre az alábbi lépéseket.

#### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
1. A **StorSimple Eszközkezelő** panelen nyissa meg a **Felügyeleti &gt; ** **kulcsok lapot.**
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. A **Billentyűk** panelen kattintson a **Regenerate gombra.**
   
   ![Kattintson az újragenerálás gombra](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. A **Szolgáltatás regisztrációs kulcsának újragenerálása** panelen tekintse át a kulcsok újragenerálásakor szükséges műveletet. A szolgáltatásban regisztrált összes további eszköz az új regisztrációs kulcsot fogja használni. A megerősítéshez kattintson a **Regenerate gombra.** A regisztráció befejezése után értesítést kap.
   
   ![Kulcs újragenerálásának megerősítése](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Egy új szolgáltatásregisztrációs kulcs jelenik meg.
   
    ![Kulcs újragenerálásának megerősítése](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Másolja a kulcsot, és mentse el az új eszközök regisztrálásához ezzel a szolgáltatással.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezdheti el](storsimple-virtual-array-deploy1-portal-prep.md) a StorSimple virtuális tömb.
* További információ [a StorSimple eszköz felügyeletéről.](storsimple-ova-web-ui-admin.md)

