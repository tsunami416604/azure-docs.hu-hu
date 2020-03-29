---
title: Böngészőnyomkövetés rögzítése hibaelhárításhoz | Microsoft dokumentumok
description: Hálózati adatok rögzítése a böngésző nyomkövetéséből az Azure Portalon felmerülő problémák elhárításához.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310696"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Böngésző-nyomkövetés rögzítése hibaelhárításhoz

Ha az Azure Portalon kapcsolatos problémát hárítja el, és kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, javasoljuk, hogy először rögzítsen egy böngészőnyomkövetést és néhány további információt. Az összegyűjtött adatok a probléma bekövetkeztekor fontos részleteket nyújthatnak a portálról. Kövesse a cikkben leírt lépéseket a böngészőben használt fejlesztői eszközökhöz: Google Chrome vagy Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) vagy Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome és Microsoft Edge (Króm)

A Google Chrome és a Microsoft Edge (Chromium) egyaránt a [Chromium nyílt forráskódú projekten](https://www.chromium.org/Home)alapul. A következő lépések bemutatják, hogyan kell használni a fejlesztői eszközöket, amelyek nagyon hasonlóak a két böngészőben. További információ: [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) és [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Fontos, hogy a nyomkövetés megkezdése _előtt_ jelentkezzen be, hogy a nyomkövetés ne tartalmazzon a bejelentkezéshez kapcsolódó bizalmas információkat. 

1. Kezdje el rögzíteni a portálon megtett lépéseket a [Felvevő segítségével.](https://support.microsoft.com/help/22878/windows-10-record-steps)

1. A portálon keresse meg a probléma előfordulásának közvetlenül azt megelőző lépést.

1. Nyomja le az ![F12 billentyűt, vagy válassza a Képernyőkép a böngésző beállításaiikikat](media/capture-browser-trace/chromium-icon-settings.png) > ikon:**További eszközök** > **Fejlesztői eszközök**.

1. Alapértelmezés szerint a böngésző csak az éppen betöltött oldal nyomkövetési adatait tartja meg. Állítsa be a következő beállításokat, hogy a böngésző minden nyomkövetési információt megtarthassa, még akkor is, ha a repro több oldalra is szüksége van:

    1. Kattintson a **Hálózat** fülre, majd a **Napló megőrzése gombra.**

          ![Képernyőkép a "Napló megőrzése"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Válassza a **Konzol** lapot, válassza a **Konzolbeállítások (Napló** **megőrzése)** lehetőséget, majd a Napló megőrzése lehetőséget. A Beállítások ablaktábla bezárásához ismét válassza a **Konzolbeállítások** lehetőséget.

          ![Képernyőkép a "Napló megőrzése"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Válassza a **Hálózat** lapot, majd a **Hálózati napló rögzítésének leállítása** és a **Törlés**lehetőséget.

    ![Képernyőkép a "Hálózati napló rögzítésének leállítása" és a "Törlés"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Válassza **a Hálózati napló rögzítése**lehetőséget, majd a probléma reprodukálása a portálon.

    ![Képernyőkép: "Profilkészítési munkamenet indítása"](media/capture-browser-trace/chromium-start-session.png)

    Az alábbi képhez hasonló munkamenet-kimenet et fog látni.

    ![Képernyőkép a böngésző nyomkövetési eredményeiről](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Miután reprodukálta a portál váratlan viselkedését, válassza a **Hálózati napló felvételének leállítása**lehetőséget, majd a HAR **exportálása** és a fájl mentése lehetőséget.

    ![Képernyőkép az "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Stop Steps Recorder, és mentse a fájlt.

1. A böngészőfejlesztői eszközök ablaktáblán válassza a **Konzol** lapot. **Save as...**

    ![Képernyőkép a konzol kimenetéről](media/capture-browser-trace/chromium-console-select.png)

1. Csomagolja be a HAR fájlt, a konzol kimenetét és a képernyőfelvételt tömörített formátumban, például .zip formátumban, és ossza meg azt a Microsoft támogatásával.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

A következő lépések bemutatják, hogyan használhatja a fejlesztői eszközöket a Microsoft Edge (EdgeHTML) alkalmazásban. További információt a [Microsoft Edge (EdgeHTML) fejlesztői eszközei című témakörben talál.](/microsoft-edge/devtools-guide)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Fontos, hogy a nyomkövetés megkezdése _előtt_ jelentkezzen be, hogy a nyomkövetés ne tartalmazzon a bejelentkezéshez kapcsolódó bizalmas információkat. 

1. Kezdje el rögzíteni a portálon megtett lépéseket a [Felvevő segítségével.](https://support.microsoft.com/help/22878/windows-10-record-steps)

1. A portálon keresse meg a probléma előfordulásának közvetlenül azt megelőző lépést.

1. Nyomja le az ![F12 billentyűt, vagy válassza a Képernyőkép a böngésző beállításaiikikat](media/capture-browser-trace/edge-icon-settings.png) > ikon:**További eszközök** > **Fejlesztői eszközök**.

1. Alapértelmezés szerint a böngésző csak az éppen betöltött oldal nyomkövetési adatait tartja meg. Állítsa be a következő beállításokat, hogy a böngésző minden nyomkövetési információt megtarthassa, még akkor is, ha a repro több oldalra is szüksége van:

    1. Válassza a **Hálózat** lapot, majd törölje a jelet a **bejegyzések törlése jelölőnégyzetből a navigáláskor.**

          ![Képernyőkép a "Bejegyzések törlése navigálás közben"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Kattintson a **Konzol** fülre, majd a **Napló megőrzése gombra.**

          ![Képernyőkép a "Napló megőrzése"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Válassza a **Hálózat** lapot, majd a **Profilkészítés leállítása és** a Munkamenet **törlése**lehetőséget.

    ![Képernyőkép a "Profilkészítési munkamenet leállítása" és a "Munkamenet törlése"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Válassza **a Profilkészítés indítása munkamenet**lehetőséget, majd reprodukálja a problémát a portálon.

    ![Képernyőkép: "Profilkészítési munkamenet indítása"](media/capture-browser-trace/edge-start-session.png)

    Az alábbi képhez hasonló munkamenet-kimenet et fog látni.

    ![Képernyőkép a böngésző nyomkövetési eredményeiről](media/capture-browser-trace/edge-browser-trace-results.png)

1. Miután reprodukálta a portál váratlan viselkedését, válassza a **Profilkészítés leállítása munkamenet et,** majd az **Exportálás HAR-ként** lehetőséget, és mentse a fájlt.

    ![Képernyőkép az "Exportálás HAR-ként"](media/capture-browser-trace/edge-network-export-har.png)

1. Stop Steps Recorder, és mentse a fájlt.

1. A böngészőfejlesztői eszközök ablaktáblán válassza a **Konzol** lapot, és bontsa ki az ablakot. Helyezze a kurzort a konzol kimenetének elejére, majd húzza a mutatót, és jelölje ki a kimenet teljes tartalmát. Kattintson a jobb gombbal, majd válassza a **Másolás**parancsot, és mentse a konzol kimenetét egy szövegfájlba.

    ![Képernyőkép a konzol kimenetéről](media/capture-browser-trace/edge-console-select.png)

1. Csomagolja be a HAR fájlt, a konzol kimenetét és a képernyőfelvételt tömörített formátumban, például .zip formátumban, és ossza meg azt a Microsoft támogatásával.

## <a name="apple-safari"></a>Apple Safari

A következő lépések bemutatják, hogyan használhatja a fejlesztői eszközöket az Apple Safariban. További információt a [Safari fejlesztői eszközök – áttekintés című témakörben talál.](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)

1. A fejlesztői eszközök engedélyezése az Apple Safariban:

    1. Válassza a **Safari**lehetőséget, majd a **Beállítások**lehetőséget.

        ![Képernyőkép a Safari beállításairól](media/capture-browser-trace/safari-preferences.png)

    1. Válassza a **Speciális** lapot, majd a **menüsor Fejlesztése megjelenítése parancsát**.

        ![Képernyőkép a Safari speciális beállításairól](media/capture-browser-trace/safari-show-develop-menu.png)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Fontos, hogy a nyomkövetés megkezdése _előtt_ jelentkezzen be, hogy a nyomkövetés ne tartalmazzon a bejelentkezéshez kapcsolódó bizalmas információkat. 

1. Kezdje el rögzíteni a portálon megtett lépéseket. További információt [a Képernyő rögzítése a Macen című témakörben talál.](https://support.apple.com/HT208721)

1. A portálon keresse meg a probléma előfordulásának közvetlenül azt megelőző lépést.

1. Válassza **a Fejlesztés**lehetőséget, majd a **Webfelügyelő megjelenítése**lehetőséget.

    ![Képernyőkép a "Webfelügyelő megjelenítése"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Alapértelmezés szerint a böngésző csak az éppen betöltött oldal nyomkövetési adatait tartja meg. Állítsa be a következő beállításokat, hogy a böngésző minden nyomkövetési információt megtarthassa, még akkor is, ha a repro több oldalra is szüksége van:

    1. Kattintson a **Hálózat** fülre, majd a **Napló megőrzése gombra.**

          ![Képernyőkép a "Napló megőrzése"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Kattintson a **Konzol** fülre, majd a **Napló megőrzése gombra.**

          ![Képernyőkép a "Napló megőrzése"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Válassza a **Hálózat** lapot, majd a **Hálózati elemek törlése**lehetőséget.

    ![Képernyőkép: "Hálózati elemek törlése"](media/capture-browser-trace/safari-clear-session.png)

1. Reprodukálja a problémát a portálon. Az alábbi képhez hasonló munkamenet-kimenet et fog látni.

    ![Képernyőkép a böngésző nyomkövetési eredményeiről](media/capture-browser-trace/safari-browser-trace-results.png)

1. Miután reprodukálta a portál váratlan viselkedését, válassza az **Exportálás** és a fájl mentése lehetőséget.

    ![Képernyőkép az "Exportálás"](media/capture-browser-trace/safari-network-export-har.png)

1. Állítsa le a képernyőfelvevőt, és mentse a fájlt.

1. A böngészőfejlesztői eszközök ablaktáblán válassza a **Konzol** lapot, és bontsa ki az ablakot. Helyezze a kurzort a konzol kimenetének elejére, majd húzza a mutatót, és jelölje ki a kimenet teljes tartalmát. A Command-C paranccsal másolja a kimenetet, és mentse szövegfájlba.

    ![Képernyőkép a konzol kimenetéről](media/capture-browser-trace/safari-console-select.png)

1. Csomagolja be a HAR fájlt, a konzol kimenetét és a képernyőfelvételt tömörített formátumban, például .zip formátumban, és ossza meg azt a Microsoft támogatásával.

## <a name="next-steps"></a>További lépések

[Az Azure Portal áttekintése](azure-portal-overview.md)