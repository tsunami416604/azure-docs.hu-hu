---
title: Támogatási jegy vagy eset létrehozása a StorSimple 8000 sorozathoz
description: Ismerje meg, hogyan naplózhatja a támogatási kérelmet, és hogyan indíthat támogatási munkamenetet a StorSimple 8000 sorozatú eszközön.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254988"
---
# <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A StorSimple Eszközkezelő lehetővé teszi egy **új támogatási kérelem naplózását** a szolgáltatás összefoglaló panelen belül. Ha bármilyen problémát tapasztal a StorSimple megoldás, létrehozhat egy technikai támogatási kérelmet. A támogatási szakemberrel folytatott online munkamenetben előfordulhat, hogy a StorSimple-eszközön is el kell indítania egy támogatási munkamenetet. Ez a cikk végigvezeti önt a következőkön:

* Támogatási kérelem létrehozása.
* Támogatási kérelem életciklusának kezelése a portálon belül.
* Támogatási munkamenet indítása a StorSimple-eszköz Windows PowerShell-felületén.

A támogatási kérelem létrehozása előtt tekintse át a [StorSimple 8000 sorozattámogatási SBA-kat és információkat.](https://msdn.microsoft.com/library/mt433077.aspx)

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

A támogatási [csomagtól](https://azure.microsoft.com/support/plans/)függően támogatási jegyeket hozhat létre a StorSimple-eszközön lévő problémákhoz közvetlenül a StorSimple Eszközkezelő szolgáltatás összefoglaló paneljéről. Támogatási kérelem létrehozásához hajtsa végre az alábbi lépéseket:

#### <a name="to-create-a-support-request"></a>Támogatási kérelem létrehozása

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A szolgáltatás összegzése panelbeállításaiban nyissa meg a **SUPPORT + HIBAELHÁRÍTÁS szakaszt,** és kattintson az **Új támogatási kérelem gombra.**
     
    ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Az **Új támogatási kérelem** panelen válassza az **Alapok lehetőséget.** Az **Alapok** panelen tegye a következő lépéseket:
   1. A **Probléma típus** legördülő listájában válassza a **Technical**lehetőséget.
   2. Az aktuális **előfizetés**, **szolgáltatástípus** és az **erőforrás** (StorSimple Eszközkezelő szolgáltatás) automatikusan kiválasztásra kerül. 
   3. Ha több csomag van társítva az előfizetéséhez, válasszon ki egy **támogatási csomagot** a legördülő listából. A technikai támogatás engedélyezéséhez fizetős támogatási csomagra van szükség.
   4. Kattintson a **Tovább** gombra.

       ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Az **Új támogatási kérelem** panelen válassza **a 2.** A **Probléma** panelen tegye a következő lépéseket:
    
    1. Válassza ki a **Súlyosság lehetőséget**.
    2. Adja meg, hogy a probléma a készülékkel vagy a StorSimple Eszközkezelő szolgáltatással kapcsolatos-e.
    3. Válasszon **kategóriát** a problémához, és adjon meg további **részleteket** a problémáról.
    4. Adja meg a probléma kezdő dátumát és időpontját.
    5. A **Fájl feltöltése területen**kattintson a mappa ikonra a támogatási csomag megkereséséhez.
    6. Jelölje be **a Diagnosztikai információk megosztása jelölőnégyzetet.**
    7. Kattintson a **Tovább** gombra.

       ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Az **Új támogatási kérelem** panelen kattintson **a 3.** Az **Elérhetőségpanelen** tegye a következő lépéseket:

   1. A **Kapcsolat opciók ban**adja meg a kívánt kapcsolattartási módot (telefon vagy e-mail) és a nyelvet. A válaszidő automatikusan kiválasztásra kerül az előfizetési csomag alapján.
   2. A Kapcsolattartási adatok ban adja meg nevét, e-mail címét, választható kapcsolattartóját, országát/régióját. Jelölje be a **Partnermódosítások mentése a jövőbeli támogatási kérelmekhez** jelölőnégyzetet.
   3. Kattintson **a Létrehozás gombra.**
   
       ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      A Microsoft támogatási szolgálata ezeket az információkat arra használja fel, hogy további információkért, diagnózisért és megoldásért forduljon Önhöz.
      A kérelem elküldése után egy támogatási szakember a lehető leghamarabb felveszi Önnel a kapcsolatot, hogy folytassa a kérést.

## <a name="manage-a-support-request"></a>Támogatási kérelem kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

#### <a name="to-manage-your-support-requests"></a>A támogatási kérelmek kezelése

1. A súgó és támogatás lap megugrásához keresse meg **a Tallózás > súgó + támogatás**.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Az összes támogatási kérelem táblázatos listája megjelenik a **Súgó + támogatás** panelen.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Jelöljön ki egy támogatási kérelmet, és kattintson rájuk. Megtekintheti a kérelem állapotát és részleteit. Kattintson **a + Új üzenet gombra,** ha nyomon szeretné követni ezt a kérést.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Támogatási munkamenet indítása a StorSimple-hez biztosított Windows PowerShellben

A StorSimple eszközzel kapcsolatos esetleges problémák elhárításához kapcsolatba kell lépnie a Microsoft támogatási csapatával. Előfordulhat, hogy a Microsoft támogatási szolgálatának támogatási munkamenetet kell használnia az eszközre való bejelentkezéshez.

A támogatási munkamenet elindításához hajtsa végre az alábbi lépéseket:

#### <a name="to-start-a-support-session"></a>Támogatási munkamenet indítása

1. Az eszköz közvetlenül a soros konzolon vagy egy távoli számítógéptel egy telnet munkameneten keresztül érheti el az eszközt. Ehhez kövesse a [PuTTY használata az eszköz soros konzoljához való csatlakozáslépéseit.](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)
2. A megnyíló munkamenetben nyomja le az **Enter** billentyűt a parancssor lekérése érdekében.
3. A soros konzol menüjében **Log in with full access**válassza az 1.
4. A kérdésre írja be a következő jelszót:
   
    `Password1`
5. A parancssorba írja be a következő parancsot:
   
    `Enable-HcsSupportAccess`
6. Egy titkosított karakterlánc jelenik meg Önnek. Másolja ezt a karakterláncot egy szövegszerkesztőbe, például a Jegyzettömbbe.
7. Mentse ezt a karakterláncot, és küldje el e-mailben a Microsoft támogatási szolgálatának.

> [!IMPORTANT]
> A futtatásával `Disable-HcsSupportAccess`letilthatja a támogatási hozzáférést. A StorSimple eszköz a munkamenet kezdeményezése után 8 órával megkísérli letiltani a támogatási hozzáférést. Ajánlott eljárás a StorSimple-eszköz hitelesítő adatainak módosítása a támogatási munkamenet megtiltása után.


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [diagnosztizálhatja és oldhatja meg a StorSimple 8000 sorozatú eszközével kapcsolatos problémákat](storsimple-8000-troubleshoot-deployment.md)
