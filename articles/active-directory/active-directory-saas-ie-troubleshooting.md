---
title: "Hibaelhárítás az Azure hozzáférési Panel bővítményét az Internet Explorer |} Microsoft Docs"
description: "Hogyan kell az Internet Explorer bővítményt a saját alkalmazások portál telepítése a csoportházirenddel."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 938d0b4046afa8c80eabe542f4541d0554948f5d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>A hozzáférési Panel bővítményét az Internet Explorer hibaelhárítása
Ez a cikk a következő problémák megoldásához nyújt segítséget:

* Még nem érhető el az alkalmazásokat a saját alkalmazások portálon keresztül az Internet Explorer használata során.
* Megjelenik a "Szoftver telepítése" üzenet, annak ellenére, hogy már telepítette a szoftvert.

Ha Ön rendszergazda, további információ: [központi telepítése a hozzáférési Panel bővítményét az Internet Explorer csoportházirend használatával](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>A diagnosztikai eszköz futtatása
Ha letölti és a hozzáférési Panel diagnosztikai eszközt futtató diagnosztizálhatja a telepítési problémákat a hozzáférési Panel kiterjesztésű:

1. [Ide kattintva letöltheti a diagnosztikai eszköz.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Nyissa meg a fájlt, és nyomja le az ENTER **az összes kibontása** gombra.
   
    ![Nyomja meg az összes kibontása](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Nyomja le az **kibontása** gombra a folytatáshoz.
   
    ![Nyomja le az Extract](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Futtassa az eszközt, kattintson a jobb gombbal a nevű fájlt **AccessPanelExtensionDiagnosticTool**, majd jelölje be **nyissa meg a > a Microsoft Windows alapú parancsfájlfuttató**.
   
    ![Nyissa meg a > a Microsoft Windows alapú parancsfájlfuttató](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Ekkor megjelenik a következő diagnosztikai ablak, amely leírja, milyen lehet még a telepítés.
   
    ![A diagnosztikai ablak minta](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Kattintson a "**Igen**", hogy a program a talált hibák elhárításában.
7. Ezek a módosítások mentéséhez zárja be a minden Internet Explorer ablakot, és ezután nyissa meg az Internet Explorert.<br />Ha továbbra sem tud hozzáférni az alkalmazásokat, próbálkozzon az alábbi lépéseket.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Ellenőrizze, hogy engedélyezve van-e a hozzáférési Panel bővítmény
Ellenőrizheti, hogy a hozzáférési Panel bővítmény engedélyezve van-e az Internet Explorerben:

1. Az Internet Explorer, kattintson a **fogaskerék ikonra** a az ablak jobb felső sarkában. Válassza ki **Internetbeállítások**.<br />(Az Internet Explorer korábbi verzióiban ez alatt található **eszközök > Internetbeállítások**.
   
    ![Kattintson az eszközök > Internetbeállítások](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Kattintson a **programok** lapra, majd kattintson a **bővítmények kezelése** gombra.
   
    ![Kattintson a bővítmények kezelése](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. Ezen a párbeszédpanelen válassza ki a **hozzáférési Panel bővítmény** , majd a **engedélyezése** gombra.
   
    ![Kattintson az Engedélyezés parancsra](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. A változtatások mentéséhez, zárjon be minden Internet Explorer-ablakban, és ezután nyissa meg az Internet Explorert.

## <a name="enable-extensions-for-inprivate-browsing"></a>Bővítmények engedélyezése az InPrivate-böngészés
Ha az InPrivate-böngészés mód használata esetén:

1. Az Internet Explorer, kattintson a **fogaskerék ikonra** a az ablak jobb felső sarkában. Válassza ki **Internetbeállítások**.<br />(Az Internet Explorer korábbi verzióiban ez alatt található **eszközök > Internetbeállítások**.
   
    ![A diagnosztikai ablak minta](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Lépjen a **adatvédelmi** lapra, majd **, törölje a jelet** feliratú jelölőnégyzet **letilthatja a eszköztárak és kiterjesztések, ha az InPrivate-böngészés indítása**</p>
   
    ![Törölje a jelet letiltása eszköztárak és kiterjesztések InPrivate-böngészés indításakor](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. A változtatások mentéséhez, zárjon be minden Internet Explorer-ablakban, és ezután nyissa meg az Internet Explorert.

## <a name="uninstall-the-access-panel-extension"></a>A hozzáférési Panel-kiterjesztés eltávolítása
A hozzáférési Panel bővítmény eltávolítása a számítógépről:

1. A billentyűzeten, nyomja meg a **Windows kulcs** a Start menü megnyitásához. Ha meg nyitva a menü, beírhatja semmit való kereséshez. Írja be a "Vezérlőpult", majd nyissa meg a **Vezérlőpult** amikor megjelenik a keresési eredmények között.
   
    ![Keresse meg a Vezérlőpult](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. A jobb felső sarkában a Vezérlőpulton, módosítsa a **megtekintéséhez** lehetőséggel **nagy ikonok**. Majd keresse meg és kattintson a **programok és szolgáltatások** gombra.
   
    ![A nézet cseréli a nagy ikonok megjelenítése](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. Válassza ki a listáról, **hozzáférési Panel bővítmény**, majd kattintson a **Eltávolítás** gombra.
   
    ![Kattintson az Eltávolítás gombra](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Ezután próbálkozzon a bővítmény ismételt használatával ellenőrizheti, ha a probléma megoldódott-e telepíteni.

Ha problémák lépnek fel a bővítmény eltávolítása, is eltávolíthatja azt használja a [Microsoft javítsa ki az](https://go.microsoft.com/?linkid=9779673) eszköz.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md)
* [A hozzáférési Panel bővítmény telepítése az Internet Explorer csoportházirend használatával](active-directory-saas-ie-group-policy.md)

