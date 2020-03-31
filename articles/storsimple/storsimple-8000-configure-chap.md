---
title: Chap konfigurálása StorSimple 8000 sorozatú eszközhöz | Microsoft dokumentumok
description: A feladatlehangzati hitelesítési protokoll (CHAP) storSimple-eszközön való konfigurálásának ismertetése.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267962"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Chap konfigurálása a StorSimple eszközhöz

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a CHAP-et a StorSimple-eszközhöz. Az ebben a cikkben részletezett eljárás a StorSimple 8000 sorozatú eszközökre vonatkozik.

A CHAP a Challenge Handshake Authentication Protocol rövidítése. Ez egy olyan hitelesítési séma, amelyet a kiszolgálók használnak a távoli ügyfelek identitásának ellenőrzésére. Az ellenőrzés megosztott jelszón vagy titkos titokon alapul. Chap lehet egyirányú (egyirányú) vagy kölcsönös (kétirányú). A CHAP egyik módja az, amikor a cél hitelesíti a kezdeményezőt. Kölcsönös vagy fordított CHAP-ben a cél hitelesíti a kezdeményezőt, majd a kezdeményező hitelesíti a célt. A kezdeményező hitelesítése célhitelesítés nélkül valósítható meg. A célhitelesítés azonban csak akkor valósítható meg, ha a kezdeményező hitelesítésis implementálva van.

Ajánlott eljárásként azt javasoljuk, hogy a CHAP-et használja az iSCSI-biztonság fokozására.

> [!NOTE]
> Ne feledje, hogy az IPSEC jelenleg nem támogatott a StorSimple eszközökön.

A StorSimple eszköz CHAP-beállításai a következő módokon konfigurálhatók:

* Egyirányú vagy egyirányú hitelesítés
* Kétirányú, kölcsönös vagy fordított hitelesítés

Ezekben az esetekben az eszköz portálját és a kiszolgálói iSCSI-kezdeményező szoftvert kell konfigurálni. A konfiguráció részletes lépéseit az alábbi oktatóanyag ismerteti.

## <a name="unidirectional-or-one-way-authentication"></a>Egyirányú vagy egyirányú hitelesítés

Egyirányú hitelesítés esetén a cél hitelesíti a kezdeményező. Ehhez a hitelesítéshez konfigurálnia kell a CHAP-kezdeményező beállításait a StorSimple eszközön és az iSCSI-kezdeményező szoftveren az állomáson. A StorSimple-eszköz és a Windows-gazdagép részletes eljárásait a következő ismertetik.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Az eszköz konfigurálása egyirányú hitelesítésre

1. Az Azure Portalon nyissa meg a StorSimple Eszközkezelő szolgáltatás. Kattintson **az Eszközök gombra,** és jelölje ki a CHAP-et konfigurálni kívánt eszközt. Nyissa meg **az Eszközbeállítások > biztonság lehetőséget.** A **Biztonsági beállítások** panelen kattintson a **CHAP**gombra.
   
    ![CHAP kezdeményező](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. A **CHAP** panelen és a **CHAP-kezdeményező** részben:
   
   1. Adja meg a CHAP-kezdeményező felhasználónevét.
   2. Adja meg a CHAP-kezdeményező jelszavát.
      
      > [!IMPORTANT]
      > A CHAP-felhasználónév nek 233 karakternél rövidebbnek kell lennie. A CHAP-jelszónak 12 és 16 karakter között kell lennie. A hosszabb felhasználónév vagy jelszó hitelesítési hibát eredményez a Windows gazdagépen.
   
   3. Erősítse meg a jelszót.

       ![CHAP kezdeményező](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kattintson a **Mentés** gombra. Megjelenik egy megerősítő üzenet. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Egyirányú hitelesítés konfigurálása a Windows gazdakiszolgálón
1. A Windows gazdakiszolgálón indítsa el az iSCSI-kezdeményezőt.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban hajtsa végre az alábbi lépéseket:
   
   1. Kattintson a **Felderítés** fülre.
      
       ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-configure-chap/IC740944.png)
   2. Kattintson **a Portál felderítése gombra.**
3. A **Célportál felderítése** párbeszédpanelen:
   
   1. Adja meg az eszköz IP-címét.
   2. Kattintson **a Speciális gombra.**
      
       ![A célportál felfedezése](./media/storsimple-configure-chap/IC740945.png)
4. A **Speciális beállítások** párbeszédpanelen:
   
   1. Jelölje be a **CHAP-napló engedélyezése** jelölőnégyzetet.
   2. A **Név** mezőben adja meg a CHAP-kezdeményezőhöz megadott felhasználónevet az Azure Portalon.
   3. A **cél titkos** mezőjében adja meg a CHAP-kezdeményezőhöz megadott jelszót az Azure Portalon.
   4. Kattintson az **OK** gombra.
      
       ![Általános speciális beállítások](./media/storsimple-configure-chap/IC740946.png)
5. Az **iSCSI-kezdeményező tulajdonságai** ablak **Célok** lapján az eszköz állapotának Csatlakoztatott állapotként kell **megjelennie.** Ha StorSimple 1200-as eszközt használ, akkor minden kötet iSCSI-tárolóként lesz csatlakoztatva. Ezért a 3-4.
   
    ![Külön célként csatlakoztatott kötetek](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Ha módosítja az iSCSI-nevet, a lesz az új név az új iSCSI-munkamenetekhez. Az új beállítások nem használatosak a meglévő munkamenetekhez, amíg ki nem jelentkezik, majd újra be nem jelentkezik.

A CHAP Windows gazdakiszolgálón történő konfigurálásáról további információt a További szempontok című témakörben [talál.](#additional-considerations)

## <a name="bidirectional-or-mutual-authentication"></a>Kétirányú vagy kölcsönös hitelesítés

A kétirányú hitelesítés során a cél hitelesíti a kezdeményező, majd a kezdeményező hitelesíti a célt. Ez az eljárás megköveteli a felhasználótól, hogy konfigurálja a CHAP kezdeményező beállításait, megfordítsa a CHAP-beállításokat az eszközön, és iSCSI-kezdeményező szoftvert az állomáson. Az alábbi eljárások a kölcsönös hitelesítés konfigurálásának lépéseit ismertetik az eszközön és a Windows gazdagépen.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Az eszköz konfigurálása kölcsönös hitelesítésre

1. Az Azure Portalon nyissa meg a StorSimple Eszközkezelő szolgáltatás. Kattintson **az Eszközök gombra,** és jelölje ki a CHAP-et konfigurálni kívánt eszközt. Nyissa meg **az Eszközbeállítások > biztonság lehetőséget.** A **Biztonsági beállítások** panelen kattintson a **CHAP**gombra.
   
    ![CHAP-cél](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Görgessen le ezen a lapon, és a **CHAP Cél** szakaszban:
   
   1. Adja meg az eszköz **fordított CHAP-felhasználónevét.**
   2. Adja meg az eszköz **fordított CHAP-jelszavát.**
   3. Erősítse meg a jelszót.
3. A **CHAP-kezdeményező** szakaszban:
   
   1. Adja meg az eszköz **felhasználónevét.**
   2. Adja meg az eszköz **jelszavát.**
   3. Erősítse meg a jelszót.

       ![CHAP kezdeményező](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kattintson a **Mentés** gombra. Megjelenik egy megerősítő üzenet. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Kétirányú hitelesítés konfigurálása a Windows gazdakiszolgálón

1. A Windows gazdakiszolgálón indítsa el az iSCSI-kezdeményezőt.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban kattintson a **Konfiguráció** fülre.
3. Kattintson **a CHAP**gombra.
4. Az **iSCSI-kezdeményező kölcsönös CHAP titkos** titkára párbeszédpanelen:
   
   1. Írja be az Azure Portalon konfigurált **FORDÍTOTT CHAP-jelszót.**
   2. Kattintson az **OK** gombra.
      
       ![iSCSI kezdeményező kölcsönös CHAP-titok](./media/storsimple-configure-chap/IC740949.png)
5. Kattintson a **Célok** fülre.
6. Kattintson a **Csatlakozás** gombra. 
7. A Csatlakozás a **célhoz** párbeszédpanelen kattintson a **Speciális gombra.**
8. A **Speciális tulajdonságok** párbeszédpanelen:
   
   1. Jelölje be a **CHAP-napló engedélyezése** jelölőnégyzetet.
   2. A **Név** mezőben adja meg a CHAP-kezdeményezőhöz megadott felhasználónevet az Azure Portalon.
   3. A **cél titkos** mezőjében adja meg a CHAP-kezdeményezőhöz megadott jelszót az Azure Portalon.
   4. Jelölje be a **Kölcsönös hitelesítés végrehajtása** jelölőnégyzetet.
      
       ![Speciális beállítások kölcsönös hitelesítés](./media/storsimple-configure-chap/IC740950.png)
   5. Kattintson az **OK** gombra a CHAP-konfiguráció befejezéséhez

A CHAP Windows gazdakiszolgálón történő konfigurálásáról további információt a További szempontok című témakörben [talál.](#additional-considerations)

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

A **Gyorscsatlakozás** szolgáltatás nem támogatja azokat a kapcsolatokat, amelyeken a CHAP engedélyezve van. Ha a CHAP engedélyezve van, győződjön meg arról, hogy a **Célok** lapon elérhető **Csatlakozás** gombbal csatlakozik egy célhoz.

![Csatlakozás a célhoz](./media/storsimple-configure-chap/IC740947.png)

A megjelenő Csatlakozás a **célhoz** párbeszédpanelen jelölje be a **Kapcsolat hozzáadása a kedvenc célok listájához** jelölőnégyzetet. Ez a beállítás biztosítja, hogy a számítógép minden újraindításakor megkísérlik visszaállítani a kapcsolatot az iSCSI kedvenc tárolóival.

## <a name="errors-during-configuration"></a>Hibák a konfiguráció során

Ha a CHAP-konfiguráció helytelen, akkor valószínűleg **hitelesítési hiba** hibaüzenet jelenik meg.

## <a name="verification-of-chap-configuration"></a>A CHAP-konfiguráció ellenőrzése

A következő lépések végrehajtásával ellenőrizheti, hogy a CHAP használatban van-e.

#### <a name="to-verify-your-chap-configuration"></a>A CHAP-konfiguráció ellenőrzése
1. Kattintson **a Kedvenc célok gombra.**
2. Válassza ki azt a célt, amelyhez engedélyezte a hitelesítést.
3. Kattintson **a Részletek gombra.**
   
    ![Az iSCSI-kezdeményező kedvenc tárolóit tulajdonságokkal elszeretné](./media/storsimple-configure-chap/IC740951.png)
4. A **Kedvenc célrészletek** párbeszédpanelen jegyezze fel a bejegyzést a **Hitelesítés** mezőben. Ha a konfiguráció sikeres volt, akkor a **CHAP**.
   
    ![Kedvenc célrészletek](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>További lépések

* További információ a [StorSimple biztonságáról.](storsimple-8000-security.md)
* További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

