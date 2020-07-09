---
title: Az IE-hez készült Azure Access panel-bővítmény hibáinak megoldása | Microsoft Docs
description: Az Internet Explorer bővítmény telepítése a csoportházirend használatával a saját alkalmazások portálon.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763261"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Az Internet Explorerhez készült hozzáférési panel bővítmény hibáinak megoldása

Ez a cikk a következő problémák megoldásában nyújt segítséget:

* Az Internet Explorer használata közben nem férhet hozzá az alkalmazásaihoz a saját alkalmazások portálon.
* A szoftver telepítése üzenet jelenik meg, annak ellenére, hogy már telepítette a szoftvert.

Ha Ön rendszergazda, tekintse meg a következő témakört: [a hozzáférési panel bővítményének központi telepítése az Internet explorerhez csoportházirend használatával](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>A diagnosztikai eszköz futtatása

A hozzáférési panel bővítménnyel diagnosztizálhatja a telepítési problémákat, ha letölti és futtatja a hozzáférési panel diagnosztikai eszközét. 

A diagnosztikai eszköz letöltése és telepítése:

1. [Válassza ezt a hivatkozást a diagnosztikai eszköz letöltéséhez.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Nyissa meg a fájlt, és bontsa ki a tartalmat a számítógépére.
1. Az eszköz futtatásához kattintson a jobb gombbal a *AccessPanelExtensionDiagnosticTool.js* nevű fájlra, és válassza a **Megnyitás a**  >  **Microsoft Windows-alapú parancsfájl-gazdagép**használatával lehetőséget.

    ![Megnyitás > Microsoft Windows-alapú parancsfájl-gazdagép](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Tekintse át a megjelenő diagnosztikai eredményeket, és válassza az **Igen** lehetőséget a problémák megoldásához. Megjelenik az eredmények megtekintése párbeszédpanel, ahol **megtekintheti** , hogy mi a teendő, ha a bővítmény nem működik.  
1. Olvassa el az üzenetet, és kattintson **az OK gombra**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Győződjön meg arról, hogy a hozzáférési panel bővítmény engedélyezve van

Annak ellenőrzése, hogy engedélyezte-e a hozzáférési panel bővítményét az Internet Explorerben:

1. Az Internet Explorerben válassza a **fogaskerék ikont** az ablak jobb felső sarkában, és válassza az **Internetbeállítások**lehetőséget.
1. Lépjen a **programok** lapra, és válassza a **Bővítmények kezelése**lehetőséget.
1. Válassza a **hozzáférési panel bővítmény** elemet a **Microsoft Corporation** szakaszban, és válassza az **Engedélyezés**lehetőséget.
1. A módosítások mentéséhez zárjunk be minden megnyitott Internet Explorer böngészőablakot. A módosítás a következő alkalommal lép életbe, amikor megnyitja az Internet Explorert.

## <a name="enable-extensions-for-inprivate-browsing"></a>Bővítmények engedélyezése InPrivate-böngészéshez

Bővítmények engedélyezése InPrivate-böngészéshez:

1. Az Internet Explorerben válassza a **fogaskerék ikont** az ablak jobb felső sarkában, és válassza az **Internetbeállítások**lehetőséget.
1. Lépjen az **Adatvédelem** lapra, és győződjön meg arról, hogy az **eszköztárak és a bővítmények letiltása az InPrivate-böngészés indításakor** jelölőnégyzet üres.
1. A módosítások mentéséhez zárjunk be minden megnyitott Internet Explorer böngészőablakot. A módosítás a következő alkalommal lép életbe, amikor megnyitja az Internet Explorert.

## <a name="uninstall-the-access-panel-extension"></a>A hozzáférési panel kiterjesztésének eltávolítása

A hozzáférési panel bővítmény eltávolítása a számítógépről:

1. A Vezérlőpulton keressen az *Eltávolítás*elemre.
1. A keresési eredmények között válassza **a program eltávolítása**lehetőséget.

    ![Válassza a program eltávolítása lehetőséget a Vezérlőpultról.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. A listából válassza a **hozzáférési panel bővítmény** elemet, majd válassza az **Eltávolítás**lehetőséget.

    ![A hozzáférési panel kiterjesztésének eltávolítása](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Ezután próbálja meg újból telepíteni a bővítményt, hogy megtudja, a probléma megoldódott-e.

Ha a bővítmény eltávolításával kapcsolatos problémákba ütközik, a [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) eszköz használatával is eltávolíthatja.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Alkalmazás-hozzáférés és egyszeri bejelentkezés Azure Active Directory](what-is-single-sign-on.md)
* [Az Internet Explorer hozzáférési panel-bővítményének üzembe helyezése Csoportházirend használatával](deploy-access-panel-browser-extension.md)
