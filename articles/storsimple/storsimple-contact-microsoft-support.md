---
title: "A StorSimple 8000 series támogatási jegy naplózása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy támogatási kérést, és a StorSimple eszköz támogatási munkamenet indításához."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d25fb4902d37faca5358e5a9010e9e146e344e1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="contact-microsoft-support-for-your-storsimple"></a>A StorSimple a Microsoft támogatási szolgálatához
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a StorSimple Forduljon a Microsoft ügyfélszolgálatához](storsimple-8000-contact-microsoft-support.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

Ha problémába ütközik a Microsoft Azure StorSimple megoldás, létrehozhat egy szolgáltatási kérelem a technikai támogatási szolgálathoz. Egy online munkamenetben a támogatási szakértőhöz is szükség lehet a StorSimple eszköz támogatási munkamenet indításához. Ez a cikk bemutatja, hogyan:

* Megtudhatja, hogyan hozzon létre egy támogatási kérést.
* Támogatási munkamenetet indítani a Windows PowerShell felületen, a StorSimple eszköz módját.

Tekintse át a [a StorSimple 8000 Series támogatási SLA-k és információk](https://msdn.microsoft.com/library/mt433077.aspx) a támogatási kérelem létrehozása előtt.

## <a name="create-a-support-request"></a>hozzon létre támogatási kérést
A következő lépésekkel hozzon létre egy támogatási kérést:

#### <a name="to-create-a-support-request"></a>A támogatási kérelem létrehozása
1. Az a [a klasszikus Azure portálon](https://manage.windowsazure.com/), kattintson a jobb felső sarokban, a fiók nevére, és kattintson a **forduljon a Microsoft ügyfélszolgálatához**.
   
    ![Forduljon az MS-támogatás ManagementPortal keresztül](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. Az új Azure-portálra (portal.azure.com) irányítja. Kattintson a **új támogatja a kérelem** csempére.
   
    ![Új portálon keresztül forduljon az MS-támogatás](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    A képernyő jobb oldalán a **új támogatja a kérelem** ablaktáblán jelenik meg. 
   
    ![Új támogatási kérelem ablaktábla](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. Az a **alapjai** párbeszédpanelen adja meg a következőket:                                
   
   1. Az a **típusú** legördülő listában válassza **műszaki**.
   2. Válassza ki a **előfizetés** a legördülő listából.
   3. Az a **szolgáltatás** legördülő listában válassza **StorSimple**. 
   4. Válassza ki a **támogatási csomag** a legördülő listából. Szüksége van ahhoz, hogy technikai támogatási szolgálatával fizetős támogatási csomagot.
4. Kattintson a **Tovább** gombra. A **probléma** párbeszédpanel jelenik meg.
   
    ![Új támogatási kérelem ablaktábla](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. Az a **probléma** párbeszédpanelen adja meg a következőket:
   
   1. Válassza ki a **súlyossági** szint a legördülő listából.
   2. Válassza ki a **problématípust** a legördülő listából.
   3. Válassza ki a **kategória** a legördülő listából. 
   4. Az a **részletek** mezőben röviden ismertesse a problémát.
   5. Az a **időkereten belül** jelzik, hogy a dátum, idő és időzóna, a probléma legutóbbi előfordulásának megfelelő mezőbe.
   6. A **fájlfeltöltés**, tallózással keresse meg a támogatási csomag a mappa ikonra.
   7. Válassza ki a **diagnosztikai információkat** jelölőnégyzetet.
6. Kattintson a **Tovább** gombra. A **elérhetőségi adatai** párbeszédpanel jelenik meg.
   
    ![Új támogatási kérelem ablaktábla](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. Adja meg a kapcsolattartási adatait, és válasszon ki egy kapcsolattartási módszert, (telefonon vagy e-mailek). 
8. Válassza ki a **kapcsolattartási adatok módosításainak mentése az a későbbi támogatási kérelmekhez** jelölőnégyzetet.
9. Kattintson a **Create** (Létrehozás) gombra.

Miután elküldte a kérést, a támogatási szakértőhöz felveszi Önnel a lehető leghamarabb folytatja a kérelemhez.

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
> 
> 

