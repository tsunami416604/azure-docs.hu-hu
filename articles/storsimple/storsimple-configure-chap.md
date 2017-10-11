---
title: "A CHAP konfigurálása a StorSimple 8000 series eszköz |} Microsoft Docs"
description: "A StorSimple eszközön a Challenge Handshake Authentication Protocol (CHAP) beállításának módját ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 467044d7-7885-4382-90bd-3148dbbd341f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 36b4e73d0336deb9560d44163fc5330d1c9d775c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-chap-for-your-storsimple-device"></a>A CHAP konfigurálása a StorSimple eszköz
Ez az oktatóanyag azt ismerteti, hogyan CHAP konfigurálása a StorSimple eszközt. Ebben a cikkben ismertetett eljárás a StorSimple 8000 series, valamint a StorSimple 1200-as eszközök vonatkozik.

A CHAP hitelesítési protokoll jelenti. Egy távoli ügyfelek identitása érvényesítéséhez kiszolgálók által használt hitelesítési séma. Az ellenőrzés megosztott jelszó vagy titkos kulcs alapul. Lehet, hogy a CHAP egyirányú (egyirányú) vagy a kölcsönös (kétirányú). Egyirányú CHAP akkor, ha a cél hitelesíti az kezdeményező. Kölcsönös vagy fordított CHAP, másrészt megköveteli, hogy a cél hitelesítéséhez a kezdeményező és a kezdeményező hitelesítse a cél. Kezdeményező hitelesítés nélkül cél hitelesítés valósítható meg. Cél hitelesítési esetén alkalmazhatóak azonban csak akkor, ha a kezdeményező hitelesítési is tartalmazzák. 

Ajánlott eljárásként azt javasoljuk, hogy használja-e CHAP iSCSI-biztonság javítása érdekében.

> [!NOTE]
> Ne feledje, hogy IPSEC jelenleg nem támogatott a StorSimple eszközökhöz.
> 
> 

A CHAP-beállításokat a StorSimple eszközön a következőképpen konfigurálható:

* Egyirányú vagy egyirányú hitelesítés
* Kétirányú vagy fordított vagy kölcsönös hitelesítés

Az egyes ezekben az esetekben a portál az eszköz és a kiszolgáló iSCSI-kezdeményező szoftver kell megadni. Ez a konfiguráció részletes lépéseit a következő oktatóanyag ismerteti.

## <a name="unidirectional-or-one-way-authentication"></a>Egyirányú vagy egyirányú hitelesítés
Egyirányú hitelesítést a tároló hitelesíti a kezdeményező. Ez a hitelesítés meg kell adni a CHAP-kezdeményező beállításokat a StorSimple eszközön, és az iSCSI-kezdeményező szoftver a gazdagépen. A StorSimple eszköz és a Windows-állomás részletes eljárásait olvashat.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Az eszköz egyirányú hitelesítés konfigurálása
1. A klasszikus Azure portálon a a **eszközök** lapján kattintson a **konfigurálása** fülre.
   
    ![A CHAP-kezdeményező](./media/storsimple-configure-chap/IC740943.png)
2. Ezen a lapon, majd a görgessen lefelé a **CHAP-kezdeményező** szakasz:
   
   1. Adjon meg egy felhasználónevet a CHAP-kezdeményező.
   2. Adjon meg egy jelszót a CHAP-kezdeményező.
      
    > [!IMPORTANT]
    > A CHAP-felhasználónév kevesebb mint 233 karaktereket tartalmazhat. A CHAP-jelszó 12 és 16 karakter hosszúságú lehet. Hosszabb felhasználónevet vagy jelszót a Windows-állomás hitelesítési hibát eredményez.
   
   3. Erősítse meg a jelszót.
3. Kattintson a **Save** (Mentés) gombra. Egy megerősítő üzenet jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Egyirányú hitelesítés konfigurálása a Windows gazdagép-kiszolgálón
1. A Windows gazdagép-kiszolgálón indítsa el az iSCSI-kezdeményező.
2. Az a **iSCSI-kezdeményező tulajdonságai** ablak, hajtsa végre a következő lépéseket:
   
   1. Kattintson a **felderítési** fülre.
      
       ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-configure-chap/IC740944.png)
   2. Kattintson a **Portal felderítése**.
3. Az a **tárolókapu felderítése** párbeszédpanel:
   
   1. Adja meg az eszköz IP-címét.
   2. Kattintson a **speciális**.
      
       ![Tárolókapu felderítése](./media/storsimple-configure-chap/IC740945.png)
4. Az a **speciális beállítások** párbeszédpanel:
   
   1. Válassza ki a **CHAP engedélyezése bejelentkezés** jelölőnégyzetet.
   2. Az a **neve** mezőben adja meg a felhasználónevet, a CHAP-kezdeményező a klasszikus portálon megadott.
   3. Az a **cél titkos** mezőbe a jelszót a CHAP-kezdeményező a klasszikus portálon megadott.
   4. Kattintson az **OK** gombra.
      
       ![Általános speciális beállítások](./media/storsimple-configure-chap/IC740946.png)
5. Az a **célok** lapján a **iSCSI-kezdeményező tulajdonságai** ablakot, az eszköz állapotúnak kell lennie **csatlakoztatva**. Ha egy StorSimple 1200-as eszközt használ, majd minden olyan kötetre is csatlakoztatva, iSCSI-tároló alább látható módon. Ezért 3 – 4. lépést kell minden olyan kötetre kell ismételni.
   
    ![Külön célként csatlakoztatott kötetek](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Ha módosítja az iSCSI-név, az új nevet az új iSCSI-munkamenetek használható. Új beállításai nem érvényesek a meglévő munkamenetekhez, amíg a Kijelentkezés és bejelentkezés újra.
   > 
   > 

A Windows gazdakiszolgáló CHAP konfigurálásával kapcsolatos további információkért látogasson el [további szempontok](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Kétirányú vagy a kölcsönös hitelesítés
Kétirányú hitelesítést a tároló hitelesíti a kezdeményező, és ezután a kezdeményező hitelesíti a cél. A felhasználónak a CHAP-kezdeményező beállításait, valamint a fordított CHAP-beállítások konfigurálása az eszköz- és iSCSI-kezdeményező szoftver a gazdagépen. Az alábbi eljárásokat konfigurálásáról a kölcsönös hitelesítés, az eszközön, és a Windows-gazdagépen.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Az eszköz a kölcsönös hitelesítés konfigurálása
1. A klasszikus Azure portálon a a **eszközök** lapján kattintson a **konfigurálása** fülre.
   
    ![A CHAP-cél](./media/storsimple-configure-chap/IC740948.png)
2. Ezen a lapon, majd a görgessen lefelé a **CHAP-cél** szakasz:
   
   1. Adjon meg egy **fordított CHAP-felhasználónév** az eszközhöz.
   2. Adjon meg egy **fordított CHAP-jelszó** az eszközhöz.
   3. Erősítse meg a jelszót.
3. Az a **CHAP-kezdeményező** szakasz:
   
   1. Adjon meg egy **felhasználónév** az eszközhöz.
   2. Adjon meg egy **jelszó** az eszközhöz.
   3. Erősítse meg a jelszót.
4. Kattintson a **Save** (Mentés) gombra. Egy megerősítő üzenet jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Kétirányú hitelesítés konfigurálása a Windows gazdagép-kiszolgálón
1. A Windows gazdagép-kiszolgálón indítsa el az iSCSI-kezdeményező.
2. Az a **iSCSI-kezdeményező tulajdonságai** ablak, kattintson a **konfigurációs** fülre.
3. Kattintson a **CHAP**.
4. Az a **iSCSI kezdeményező kölcsönös CHAP titkos** párbeszédpanel:
   
   1. Típus a **fordított CHAP-jelszó** konfigurált a klasszikus Azure portálon.
   2. Kattintson az **OK** gombra.
      
       ![iSCSI kezdeményező kölcsönös CHAP titkos kulcs](./media/storsimple-configure-chap/IC740949.png)
5. Kattintson a **célok** fülre.
6. Kattintson a **Connect** gombra. 
7. Az a **csatlakozás cél** párbeszédpanel, kattintson a **speciális**.
8. Az a **speciális tulajdonságok** párbeszédpanel:
   
   1. Válassza ki a **CHAP engedélyezése bejelentkezés** jelölőnégyzetet.
   2. Az a **neve** mezőben adja meg a felhasználónevet, a CHAP-kezdeményező a klasszikus portálon megadott.
   3. Az a **cél titkos** mezőbe a jelszót a CHAP-kezdeményező a klasszikus portálon megadott.
   4. Válassza ki a **kölcsönös hitelesítés végrehajtása** jelölőnégyzetet.
      
       ![Speciális beállítások kölcsönös hitelesítés](./media/storsimple-configure-chap/IC740950.png)
   5. Kattintson a **OK** CHAP konfigurálása

A Windows gazdakiszolgáló CHAP konfigurálásával kapcsolatos további információkért látogasson el [további szempontok](#additional-considerations).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés
A **gyors csatlakozás** funkció nem támogatja a kapcsolatokat, amelyek CHAP engedélyezve van. Ha CHAP engedélyezve van, győződjön meg arról, hogy használja a **Connect** gomb, amellyel elérhető a **célok** kapcsolódni a cél fülre.

![Cél kapcsolódni](./media/storsimple-configure-chap/IC740947.png)

Az a **cél kapcsolódás** párbeszédpanelen látható, válassza ki a **vegye fel ezt a kapcsolatot a kedvenc célok** jelölőnégyzetet. Ez biztosítja, hogy minden alkalommal, amikor a számítógép újraindítását követően tett kísérlet a kapcsolat helyreállítására az iSCSI-kedvenc tárolókra.

## <a name="errors-during-configuration"></a>Konfigurálása során hibák
Ha a CHAP konfigurációja nem megfelelő, akkor valószínű, hogy egy **hitelesítési hiba** hibaüzenet jelenik meg.

## <a name="verification-of-chap-configuration"></a>A CHAP-konfiguráció ellenőrzése
Azt, hogy a CHAP használja a következő lépések végrehajtásával ellenőrizheti.

#### <a name="to-verify-your-chap-configuration"></a>A CHAP konfigurációjának ellenőrzése
1. Kattintson a **kedvenc célok**.
2. Jelölje ki a cél, amelyhez hitelesítés engedélyezett.
3. Kattintson a **részletek**.
   
    ![iSCSI-kezdeményező tulajdonságai kedvenc tárolók](./media/storsimple-configure-chap/IC740951.png)
4. Az a **kedvenc cél részletek** párbeszédpanelen jegyezze fel a bejegyzést a **hitelesítési** mező. Ha a konfigurálás sikeres volt, akkor kell kapnia **CHAP**.
   
    ![Kedvenc cél részletei](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple biztonsági](storsimple-security.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

