---
title: Hozzon létre támogatási jegyet, vagy a StorSimple 8000 sorozat esetében |} A Microsoft Docs
description: Ismerje meg, hogyan naplótámogatási kérelem, és indítson el egy támogatási munkamenetet a a StorSimple 8000 sorozatú eszköz.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: fb8cfd4767f6bb9afe1b5731d3b4db1c68a73056
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107127"
---
# <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A StorSimple-Eszközkezelő lehetővé teszi, hogy **egy új támogatási jegyet** belül a szolgáltatásösszegző panel. Ha bármilyen problémába ütközik a StorSimple-megoldásokra, létrehozhat egy szolgáltatási kérelmet, ha technikai támogatást. Online-munkamenetben a támogatási szakértővel szükség lehet, indítson el egy támogatási munkamenetet a StorSimple eszközön. Ez a cikk végigvezeti:

* Hogyan hozzon létre egy támogatási kérést.
* Hogyan lehet egy támogatási kérést az életciklusnak a kezelésében a portálon belül.
* Hogyan indítson el egy támogatási munkamenetet a Windows PowerShell felületén, a StorSimple eszköz.

Tekintse át a [StorSimple 8000 sorozat támogatási SLA-k és információk](https://msdn.microsoft.com/library/mt433077.aspx) támogatási kérések létrehozása előtt.

## <a name="create-a-support-request"></a>Támogatáskérés létrehozása

Attól függően, hogy a [támogatási csomag](https://azure.microsoft.com/support/plans/), a StorSimple eszközön a StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén közvetlenül a is létrehozhat támogatási jegyeket, egy adott probléma megoldásához. A következő lépésekkel hozzon létre egy támogatási kérést:

#### <a name="to-create-a-support-request"></a>Támogatáskérés létrehozása

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A szolgáltatás összefoglalás panelén beállításait, lépjen a **támogatás + hibaelhárítás** szakaszt, és kattintson a **új támogatási kérelem**.
     
    ![Forduljon az MS támogatás az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Az a **új támogatási kérelem** panelen válassza ki **alapjai**. Az a **alapjai** panelen tegye a következőket:
   1. Az a **Problématípus** legördülő listában válassza **technikai**.
   2. Az aktuális **előfizetés**, **szolgáltatás** típusát, és a **erőforrás** (StorSimple-Eszközkezelő szolgáltatás) a rendszer automatikusan kiválasztja. 
   3. Válassza ki a **támogatási csomag** a legördülő listából, ha több csomagok az Ön előfizetéséhez rendelve van. Szüksége lesz egy fizetős támogatási csomag ahhoz, hogy technikai támogatást.
   4. Kattintson a **tovább**.

       ![Forduljon az MS támogatás az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Az a **új támogatási kérelem** panelen válassza ki **. lépés 2 probléma**. Az a **probléma** panelen tegye a következőket:
    
    1. Válassza ki a **súlyossági**.
    2. Adja meg, ha a probléma a készülék vagy a StorSimple-Eszközkezelő szolgáltatással kapcsolatos.
    3. Válasszon egy **kategória** ehhez adja ki, és adja meg a további **részletek** a problémával kapcsolatban.
    4. Adja meg a kezdő dátum és idő a probléma.
    5. Az a **fájlfeltöltés**, kattintson a mappa ikonra, tallózással keresse meg a támogatási csomagot.
    6. Ellenőrizze **diagnosztika adatok megosztása**.
    7. Kattintson a **tovább**.

       ![Forduljon az MS támogatás az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Az a **új támogatási kérelem** panelen kattintson a **lépés 3 elérhetőségi adatok**. Az a **kapcsolattartási adatok** panelen tegye a következőket:

   1. Az a **kapcsolatfelvételi lehetőségek**, adja meg az elsődleges kapcsolattartási módszer (telefonon vagy e-mailben) és a nyelvet. A válaszidő automatikusan ki van jelölve, az előfizetési csomag alapján.
   2. Adja meg a nevét, e-mail, ügyfél nem kötelező, ország az elérhetőségi adatok. Válassza ki a **kapcsolattartási adatok módosításainak mentése a későbbi támogatási kérelmekhez** jelölőnégyzetet.
   3. Kattintson a **Create** (Létrehozás) gombra.
   
       ![Forduljon az MS támogatás az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      A Microsoft Support ezen információk használatával kapcsolatos további információkat, a diagnosztikai és a megoldási Önnel.
      Miután elküldte a kérést, a támogatási szakember felveszi a kapcsolatot, amint lehetséges, hogy a kérés folytatásához.

## <a name="manage-a-support-request"></a>Egy támogatási jegy kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

#### <a name="to-manage-your-support-requests"></a>A támogatási kérések kezelése

1. A Súgó és támogatási oldallal lekéréséhez lépjen **Tallózás > Súgó + támogatás**.

    ![Támogatási kérések kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. A táblázatos listájában tekintse meg a támogatási kérelmek jelenik meg a **súgó + támogatás** panelen.

    ![Támogatási kérések kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Válassza ki, majd kattintson egy támogatási kérést. Megtekintheti az állapotát és a kérés részleteit. Kattintson a **+ új üzenet** Ha azt szeretné, ezt a kérelmet követéséhez.

    ![Támogatási kérések kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Indítson el egy támogatási munkamenetet a Windows PowerShell storsimple-höz készült

Bármilyen hibaelhárításra, amely a StorSimple-eszköz tapasztalhat, szüksége lesz a Microsoft Support csapat bevonása. Jelentkezzen be az eszköz egy támogatási munkamenetet használatával Support szükségessé.

A következő lépésekkel indítson el egy támogatási munkamenetet:

#### <a name="to-start-a-support-session"></a>Egy támogatási munkamenetet

1. Az eszköz eléréséhez, közvetlenül a soros konzol vagy egy telnet-munkamenet távoli számítógépről. Ehhez kövesse a lépéseket a [a PuTTY használata az eszköz soros konzoljához való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. A megnyitott munkamenetben nyomja le az **Enter** parancssor első kulcsát.
3. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**.
4. A parancssorba írja be a következő jelszóval:
   
    `Password1`
5. A parancssorba írja be a következő parancsot:
   
    `Enable-HcsSupportAccess`
6. Egy titkosított karakterláncot is megjelenik. Másolja ezt a karakterláncot egy szövegszerkesztőbe, például a Jegyzettömbben.
7. Mentse ezt a karakterláncot, és a Microsoft Support egy e-mail üzenet küldése.

> [!IMPORTANT]
> Letilthatja a támogatási hozzáférését futtatásával `Disable-HcsSupportAccess`. A StorSimple-eszköz ekkor is kísérlet el a támogatás letiltása után a munkamenetet kezdeményezett 8 óra. Ajánlott eljárás az, módosítsa a StorSimple eszköz hitelesítő adatait egy támogatási munkamenetet kezdeményezése után.


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [a StorSimple 8000 sorozatú eszköz kapcsolatos problémák diagnosztizálása és megoldása](storsimple-8000-troubleshoot-deployment.md)
