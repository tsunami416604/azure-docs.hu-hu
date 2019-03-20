---
title: A CHAP konfigurálása a StorSimple 8000 sorozatú eszköz |} A Microsoft Docs
description: A StorSimple eszközön a Challenge Handshake Authentication Protocol (CHAP) beállításának módját ismerteti.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095160"
---
# <a name="configure-chap-for-your-storsimple-device"></a>A CHAP konfigurálása a StorSimple eszköz

Ez az oktatóanyag azt ismerteti, hogyan a CHAP konfigurálása a StorSimple eszközhöz. Ebben a cikkben ismertetett eljárás a StorSimple 8000 sorozatú eszközök vonatkozik.

A CHAP Challenge Handshake Authentication Protocol jelöli. Egy távoli ügyfelek identitásának ellenőrzése a kiszolgálók által használt hitelesítési sémával. Az ellenőrzés egy megosztott jelszó vagy titkos kód alapul. CHAP (egyirányú) egyik módja lehet vagy kezdődne (kétirányú). CHAP például amikor a célként megadott végzi a hitelesítést egy kezdeményező. A cél hitelesíti magát a kezdeményező kölcsönös vagy fordított CHAP, és majd a kezdeményező végzi a hitelesítést a cél. Kezdeményező hitelesítési cél hitelesítés nélkül valósítható meg. Cél hitelesítési azonban implementálható, csak akkor, ha a hitelesítést kezdeményező is valósít meg.

Ajánlott eljárásként azt javasoljuk, hogy használja-e CHAP iSCSI biztonságának növelése érdekében.

> [!NOTE]
> Ne feledje, hogy az IPSEC jelenleg nem támogatott a StorSimple eszközön.

A StorSimple eszközön a CHAP-beállítások konfigurálhatók a következő módon:

* Egyirányú vagy egyirányú hitelesítés
* Kétirányú vagy fordított vagy a kölcsönös hitelesítés

Az egyes ezekben az esetekben az eszköz és a kiszolgáló iSCSI-kezdeményező szoftver a portálon kell konfigurálni. Ehhez a konfigurációhoz a részletes lépéseket a következő oktatóanyagot ismerteti.

## <a name="unidirectional-or-one-way-authentication"></a>Egyirányú vagy egyirányú hitelesítés

Az egyirányú hitelesítési a cél hitelesíti magát a kezdeményező. A hitelesítés szükséges a CHAP-kezdeményező beállításokat konfigurál a StorSimple-eszköz és az iSCSI-kezdeményező szoftver a gazdagépen. A StorSimple-eszköz és a Windows-állomás részletes eljárásokat az alábbiakban ismertetjük.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Az eszköz egyirányú hitelesítés konfigurálása

1. Az Azure Portalon nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Kattintson a **eszközök** , és válassza ki, majd kattintson a CHAP konfigurálása kívánt eszközt. Lépjen a **beállítások > Biztonság**. Az a **biztonsági beállítások** panelen kattintson a **CHAP**.
   
    ![CHAP-kezdeményező](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Az a **CHAP** panelen, majd a a **CHAP-kezdeményező** szakaszban:
   
   1. Adja meg a CHAP-kezdeményező felhasználó nevét.
   2. Adja meg egy jelszót a CHAP-kezdeményező.
      
      > [!IMPORTANT]
      > A CHAP-felhasználónév 233-nél kevesebb karaktert tartalmazhat. A CHAP-jelszó 12 és 16 karakter között kell lennie. Hosszabb felhasználónevet vagy jelszót a Windows-állomás hitelesítési hibát eredményez.
   
   3. Erősítse meg a jelszót.

       ![CHAP-kezdeményező](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kattintson a **Save** (Mentés) gombra. A megerősítést kérő üzenet jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Egyirányú hitelesítésének konfigurálása a Windows-gazdakiszolgálón
1. A Windows-állomás kiszolgálón indítsa el az iSCSI-kezdeményező.
2. Az a **iSCSI-kezdeményező tulajdonságai** ablakban hajtsa végre az alábbi lépéseket:
   
   1. Kattintson a **felderítési** fülre.
      
       ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-configure-chap/IC740944.png)
   2. Kattintson a **felderítése a portálon**.
3. Az a **felderítése** párbeszédpanel:
   
   1. Adja meg az eszköz IP-címét.
   2. Kattintson a **speciális**.
      
       ![Tárolókapu felderítése](./media/storsimple-configure-chap/IC740945.png)
4. Az a **speciális beállítások** párbeszédpanel:
   
   1. Válassza ki a **bejelentkezés engedélyezése CHAP** jelölőnégyzetet.
   2. Az a **neve** mezőben adja meg a felhasználónevet, az Azure Portalon a CHAP-kezdeményező megadott.
   3. Az a **a céloldali titkos kulcsot** mezőben adja meg a jelszót, amely az Azure Portalon a CHAP-kezdeményező megadott.
   4. Kattintson az **OK** gombra.
      
       ![Általános speciális beállítások](./media/storsimple-configure-chap/IC740946.png)
5. Az a **célok** lapján a **iSCSI-kezdeményező tulajdonságai** ablakot, az eszköz állapotúnak kell lennie **csatlakoztatva**. Ha egy StorSimple 1200-as eszközt használ, majd minden egyes kötethez van csatlakoztatva, iSCSI-tároló. Ezért 3-4. lépést kell ismételni minden kötet esetében.
   
    ![Külön célként csatlakoztatott kötetek](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Ha módosítja az iSCSI-név, az új név szolgál az új iSCSI-munkameneteket. Új beállítások nem használatosak a meglévő munkameneteket kijelentkezés és bejelentkezés újra.

További információ a CHAP konfigurálása a Windows kiszolgálón nyissa meg [további szempontok](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Kétirányú vagy a kölcsönös hitelesítés

A cél hitelesíti magát a kezdeményező kétirányú hitelesítést, és majd a kezdeményező végzi a hitelesítést a cél. Az eljáráshoz szükség van a felhasználó számára a CHAP-kezdeményező beállítások konfigurálása, a fordított irányú CHAP beállításainak az eszközön, és az iSCSI-kezdeményező szoftver a gazdagépen. A következő eljárások azt ismertetik, a lépéseket, a kölcsönös hitelesítés beállítása az eszközön, és a Windows-gazdagépen.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Az eszköz a kölcsönös hitelesítés beállítása

1. Az Azure Portalon nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Kattintson a **eszközök** , és válassza ki, majd kattintson a CHAP konfigurálása kívánt eszközt. Lépjen a **beállítások > Biztonság**. Az a **biztonsági beállítások** panelen kattintson a **CHAP**.
   
    ![CHAP-cél](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Görgessen lefelé az oldalon, majd a a **CHAP-céljának** szakaszban:
   
   1. Adjon meg egy **fordított CHAP-felhasználónév** az eszközhöz.
   2. Adjon meg egy **fordított CHAP-jelszó** az eszközhöz.
   3. Erősítse meg a jelszót.
3. Az a **CHAP-kezdeményező** szakaszban:
   
   1. Adjon meg egy **felhasználónév** az eszközhöz.
   2. Adjon meg egy **jelszó** az eszközhöz.
   3. Erősítse meg a jelszót.

       ![CHAP-kezdeményező](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kattintson a **Save** (Mentés) gombra. A megerősítést kérő üzenet jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>A Windows-gazdakiszolgálón kétirányú hitelesítés konfigurálása

1. A Windows-állomás kiszolgálón indítsa el az iSCSI-kezdeményező.
2. Az a **iSCSI-kezdeményező tulajdonságai** ablakban kattintson a **konfigurációs** fülre.
3. Kattintson a **CHAP**.
4. Az a **iSCSI kezdeményező kölcsönös CHAP-titok** párbeszédpanel:
   
   1. Írja be a **fordított CHAP-jelszó** konfigurált az Azure Portalon.
   2. Kattintson az **OK** gombra.
      
       ![iSCSI kezdeményező kölcsönös CHAP-titok](./media/storsimple-configure-chap/IC740949.png)
5. Kattintson a **célok** fülre.
6. Kattintson a **Connect** gombra. 
7. Az a **csatlakozzon a cél** párbeszédpanelen kattintson a **speciális**.
8. Az a **speciális tulajdonságok** párbeszédpanel:
   
   1. Válassza ki a **bejelentkezés engedélyezése CHAP** jelölőnégyzetet.
   2. Az a **neve** mezőben adja meg a felhasználónevet, az Azure Portalon a CHAP-kezdeményező megadott.
   3. Az a **a céloldali titkos kulcsot** mezőben adja meg a jelszót, amely az Azure Portalon a CHAP-kezdeményező megadott.
   4. Válassza ki a **hajtsa végre a kölcsönös hitelesítés** jelölőnégyzetet.
      
       ![Speciális beállítások a kölcsönös hitelesítés](./media/storsimple-configure-chap/IC740950.png)
   5. Kattintson a **OK** befejezéséhez a CHAP konfigurálása

További információ a CHAP konfigurálása a Windows kiszolgálón nyissa meg [további szempontok](#additional-considerations).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

A **gyors csatlakozás** funkció nem támogatja, amelyeken engedélyezve van a CHAP kapcsolatok. Ha engedélyezve van a CHAP, győződjön meg arról, hogy használja-e a **Connect** gombot, amely elérhető a **célok** kapcsolódni a cél fülre.

![Csatlakozhat a cél](./media/storsimple-configure-chap/IC740947.png)

Az a **cél csatlakozhat** párbeszédpanel jelenik meg, válassza ki a **ezt a kapcsolatot ad hozzá a kedvenc tárolók listájában** jelölőnégyzetet. Ez a beállítás biztosítja, hogy minden alkalommal, amikor a számítógép újraindul, kísérlet történik a kedvenc iSCSI-tárolók, a kapcsolat visszaállításához.

## <a name="errors-during-configuration"></a>Konfigurációs hibák

Ha a CHAP-konfigurációja helytelen, akkor valószínű, hogy Ön egy **hitelesítési hiba** hibaüzenet jelenik meg.

## <a name="verification-of-chap-configuration"></a>A CHAP-konfiguráció ellenőrzése

Ellenőrizheti, hogy a CHAP használatban van a következő lépések végrehajtásával.

#### <a name="to-verify-your-chap-configuration"></a>A CHAP beállításainak ellenőrzése
1. Kattintson a **kedvenc célok**.
2. Válassza ki a cél, amelyhez hitelesítés engedélyezve.
3. Kattintson a **részletek**.
   
    ![iSCSI-kezdeményező tulajdonságai kedvenc tárolók](./media/storsimple-configure-chap/IC740951.png)
4. Az a **kedvenc cél részletei** párbeszédpanelen jegyezze fel a bejegyzést a **hitelesítési** mező. Ha a konfigurálás sikeres volt, akkor a következő: **CHAP**.
   
    ![Kedvenc cél részletei](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [StorSimple biztonsági](storsimple-8000-security.md).
* Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

