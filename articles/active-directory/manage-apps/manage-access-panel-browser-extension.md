---
title: Az Azure Access Panel IE bővítményének hibaelhárítása | Microsoft dokumentumok
description: A csoportházirend használata az Internet Explorer bővítmény telepítéséhez a Saját alkalmazások portálhoz.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723919"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Az Internet Explorer Access Panel bővítményének – problémamegoldás

Ez a cikk a következő problémák elhárítását segíti:

* Az Internet Explorer használata közben nem tudja elérni alkalmazásait a Saját alkalmazások portálon keresztül.
* A "Szoftver telepítése" üzenet akkor is megjelenik, ha már telepítette a szoftvert.

Ha Ön rendszergazda, olvassa [el Az Internet Explorer Hozzáférési panelbővítményének központi telepítése csoportházirenddel című témakört.](deploy-access-panel-browser-extension.md)

## <a name="run-the-diagnostic-tool"></a>A diagnosztikai eszköz futtatása

A hozzáférési panel bővítménysel a telepítési problémákat a Hozzáférési panel diagnosztikai eszköz letöltésével és futtatásával diagnosztizálhatja. 

A diagnosztikai eszköz letöltése és telepítése:

1. [A diagnosztikai eszköz letöltéséhez válassza ezt a hivatkozást.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Nyissa meg a fájlt, és bontsa ki a tartalmát a számítógépre.
1. Az eszköz futtatásához kattintson a jobb gombbal az *AccessPanelExtensionDiagnosticTool.js* nevű fájlra, és válassza a **Megnyitás** > **Microsoft Windows Based Script Host parancsot.**

    ![Megnyitás > Microsoft Windows-alapú parancsfájlállomással](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Tekintse át a megjelenő diagnosztikai eredményeket, és válassza az **Igen** lehetőséget a problémák megoldásához. **Megjelenik az Eredmények ellenőrzése** párbeszédpanelen, amely en arról van szó, hogy mi a teendő, ha a bővítmény nem működik.  
1. Olvassa el az üzenetet, és válassza **az OK gombot.**

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Annak ellenőrzése, hogy a Hozzáférési panel bővítmény engedélyezve van-e

Annak ellenőrzése, hogy engedélyezte-e az Internet Explorer hozzáférési panelbővítményét, a következők:

1. Az Internet Explorerben válassza a **Gear ikont** az ablak jobb felső sarkában, és válassza az **Internet beállítások lehetőséget**.
1. Nyissa meg a **Programok** lapot, és válassza **a Bővítmények kezelése**lehetőséget.
1. Válassza a Microsoft Corporation szakasz **Hozzáférési panelbővítmény** e-részében az **Microsoft Corporation** **Engedélyezés**lehetőséget.
1. A módosítások mentéséhez zárja be az Internet Explorer összes megnyitott böngészőablakát. A módosítás az Internet Explorer következő megnyitásakor lép érvénybe.

## <a name="enable-extensions-for-inprivate-browsing"></a>Bővítmények engedélyezése az InPrivate-böngészéshez

Bővítmények engedélyezése az InPrivate-böngészéshez:

1. Az Internet Explorerben válassza a **Gear ikont** az ablak jobb felső sarkában, és válassza az **Internet beállítások lehetőséget**.
1. Nyissa meg az **Adatvédelem** lapot, és ellenőrizze, hogy az **Eszköztárak és bővítmények letiltása az InPrivate-böngészés indításakor** jelölőnégyzet nincs-e bejelölve.
1. A módosítások mentéséhez zárja be az Internet Explorer összes megnyitott böngészőablakát. A módosítás az Internet Explorer következő megnyitásakor lép érvénybe.

## <a name="uninstall-the-access-panel-extension"></a>Az Access Panel bővítmény eltávolítása

Az Access Panel bővítmény eltávolítása a számítógépről:

1. A Vezérlőpulton keresse meg az *eltávolítást.*
1. A keresési eredmények között válassza a **Program eltávolítása**lehetőséget.

    ![A Program eltávolítása beállítás kiválasztása a Vezérlőpultról](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. A listában válassza a **Hozzáférési panel bővítmény** lehetőséget, majd az **Eltávolítás**lehetőséget.

    ![Az Access Panel bővítmény eltávolítása](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Ezután próbálja meg újra telepíteni a bővítményt, hogy lássa, megoldódott-e a probléma.

Ha problémákba ütközik a bővítmény eltávolításával, a [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) eszközzel is eltávolíthatja azt.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](what-is-single-sign-on.md)
* [Az Internet Explorer Access Panel extensionjének telepítése csoportházirenddel](deploy-access-panel-browser-extension.md)
