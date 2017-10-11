---
title: "Támogatási jegy vagy a StorSimple 8000 Series eset létrehozása |} Microsoft Docs"
description: "Megtudhatja, hogyan jelentkezzen támogatási kérelmet, és a StorSimple 8000 series eszközön támogatási munkamenet indításához."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: alkohli;
ms.openlocfilehash: 4b5a14237ce79100f980b2186b2c3c887abaa296
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="contact-microsoft-support"></a>Forduljon a Microsoft támogatási szolgálatához.

A StorSimple Device Manager lehetővé teszi a **új támogatási kérelem jelentkezzen** belül a szolgáltatás összefoglaló panelre. Ha problémába ütközik a StorSimple-megoldás, létrehozhat egy szolgáltatási kérelem a technikai támogatási szolgálathoz. Egy online munkamenetben a támogatási szakértőhöz is szükség lehet a StorSimple eszköz támogatási munkamenet indításához. Ez a cikk bemutatja, hogyan:

* Megtudhatja, hogyan hozzon létre egy támogatási kérést.
* Hogyan kezelheti a támogatási kérelem életciklusa, az a portálon.
* Támogatási munkamenetet indítani a Windows PowerShell felületen, a StorSimple eszköz módját.

Tekintse át a [a StorSimple 8000 Series támogatási SLA-k és információk](https://msdn.microsoft.com/library/mt433077.aspx) a támogatási kérelem létrehozása előtt.

## <a name="create-a-support-request"></a>Támogatási kérés létrehozása

Attól függően, a [támogatási csomag](https://azure.microsoft.com/support/plans/), létrehozhat támogatási jegyeket egy problémát a StorSimple eszközt a StorSimple Device Manager szolgáltatás összefoglaló blade-ről. A következő lépésekkel hozzon létre egy támogatási kérést:

#### <a name="to-create-a-support-request"></a>A támogatási kérelem létrehozása

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A Szolgáltatásbeállítások összefoglaló panelen keresse meg **támogatási + hibaelhárítás** szakaszt, és kattintson a **új támogatja a kérelem**.
     
    ![Új portálon keresztül forduljon az MS-támogatás](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Az a **új támogatja a kérelem** panelen válassza **alapjai**. Az a **alapjai** panelen tegye a következőket:
   1. Az a **típusú** legördülő listában válassza **műszaki**.
   2. Az aktuális **előfizetés**, **szolgáltatás** típus, és a **erőforrás** (a StorSimple eszköz kezelő szolgáltatás) közül automatikusan választ. 
   3. Válassza ki a **támogatási csomag** a legördülő listából, ha több tervek az Ön előfizetéséhez rendelve van. Szüksége van ahhoz, hogy technikai támogatási szolgálatával fizetős támogatási csomagot.
   4. Kattintson a **Tovább** gombra.

       ![Új portálon keresztül forduljon az MS-támogatás](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Az a **új támogatja a kérelem** panelen válassza **lépés 2 probléma**. Az a **probléma** panelen tegye a következőket:
    
    1. Válassza ki a **súlyossági**.
    2. Adja meg, ha a készülék vagy a StorSimple Device Manager szolgáltatással kapcsolatos hibát.
    3. Válasszon egy **kategória** ehhez adja ki, és adjon meg több **részletek** a problémával kapcsolatos.
    4. Adja meg a kezdő dátum és idő, a probléma.
    5. Az a **fájlfeltöltés**, tallózással keresse meg a támogatási csomag a mappa ikonra.
    6. Ellenőrizze **diagnosztikai információkat**.
    7. Kattintson a **Tovább** gombra.

       ![Új portálon keresztül forduljon az MS-támogatás](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Az a **új támogatja a kérelem** panelen kattintson a **lépés 3 elérhetőségi adatait**. Az a **elérhetőségi adatai** panelen tegye a következőket:

    1. Az a **beállítások forduljon**, adja meg az elsődleges kapcsolattartási módszert, (telefonon vagy e-mailek) és a nyelv. A válaszidő automatikusan ki van jelölve, az előfizetési terv alapján.
    2. Adja meg a nevét, e-mail, opcionális forduljon, ország elérhetőségi adatait. Válassza ki a **kapcsolattartási adatok módosításainak mentése az a későbbi támogatási kérelmekhez** jelölőnégyzetet.
    3. Kattintson a **Create** (Létrehozás) gombra.
   
        ![Új portálon keresztül forduljon az MS-támogatás](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Microsoft Support ezen információk használatával érheti el, további információt, a diagnosztikai és a megoldás.
Miután elküldte a kérést, a támogatási szakértőhöz felveszi Önnel a lehető leghamarabb folytatja a kérelemhez.

## <a name="manage-a-support-request"></a>Egy támogatási kérést kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

#### <a name="to-manage-your-support-requests"></a>A támogatási kérelmek kezelése

1. Ahhoz, hogy a Súgó és támogatás oldalra, keresse meg **Tallózás > Súgó + támogatás**.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. A támogatási kérelmek táblázatos listája jelenik meg a **súgó + támogatás** panelen.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Jelölje ki, majd kattintson egy támogatási kérést. Az állapot és a részletek a kérelemhez tartozó tekintheti meg. Kattintson a **+ új üzenet** Ha azt szeretné, hogy a kérés nyomon követéséhez.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Támogatási munkamenetet indítani a Windows PowerShellben a StorSimple

Hárítsa el a StorSimple eszköz esetleg előforduló, szüksége lesz a Microsoft Support csoport bevonásához. Microsoft Support lehet szükség a támogatási munkamenet bejelentkezni az eszközt.

A következő lépésekkel támogatási munkamenet indításához:

#### <a name="to-start-a-support-session"></a>Támogatási munkamenet indításához

1. Az eszközhöz való hozzáféréshez, közvetlenül a soros konzol használatával, vagy egy telnet-munkamenet távoli számítógépről keresztül. Ehhez kövesse a lépéseket [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. A megnyitott munkamenetben nyomja le a **Enter** kulcsot használva egy parancssori ablakot.
3. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
4. A parancssorba írja be a következő jelszóval:
   
    `Password1`
5. A parancssorba írja be a következő parancsot:
   
    `Enable-HcsSupportAccess`
6. Egy titkosított karakterláncot jelennek majd meg. Másolja ezt a karakterláncot egy szövegszerkesztőbe, például a Jegyzettömböt.
7. Mentse ezt a karakterláncot, és az e-mailt küldeni a Microsoft Support.

> [!IMPORTANT]
> Letilthatja a támogatás elérését futtatásával `Disable-HcsSupportAccess`. A StorSimple eszköz megkísérli is támogatási hozzáférés letiltása a 8 órával azt követően, a munkamenet kezdeményezték. A StorSimple eszköz hitelesítő adatok módosítása a támogatási munkamenet kezdeményezése után célszerű.


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [diagnosztizálhatja és a StorSimple 8000 series eszköz kapcsolatos problémák megoldásához](storsimple-troubleshoot-deployment.md)
