---
title: Munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazáshoz – Azure AD
description: Adja hozzá munkahelyi vagy iskolai fiókját a Microsoft Authenticator alkalmazáshoz, hogy ellenőrizze személyazonosságát a kétfaktoros ellenőrzés során.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063917"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Munkahelyi vagy iskolai fiók felvétele a Microsoft Authenticator alkalmazásba

Ha a szervezet kétfaktoros ellenőrzést használ, beállíthatja, hogy a munkahelyi vagy iskolai fiók a Microsoft Authenticator alkalmazást használja az ellenőrzési módszerek egyikeként.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben leírt lépéseket.

## <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

1. A számítógépen nyissa meg a [További biztonsági ellenőrzés](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) lapot.

    >[!Note]
    >Ha nem látja a **További biztonsági ellenőrzés** lapot, lehetséges, hogy a rendszergazda bekapcsolta a biztonsági adatok (előzetes verzió) felületét. Ebben az esetben kövesse a Biztonsági adatok [beállítása](security-info-setup-auth-app.md) a hitelesítő alkalmazás használatához című szakasz utasításait. Ha nem ez a helyzet, segítségért forduljon a szervezet ügyfélszolgálatához. A biztonsági adatokról a [Biztonsági adatok (előzetes verzió) című témakörben olvashat bővebben.](user-help-security-info-overview.md)

2. Jelölje be a Hitelesítő alkalmazás melletti **jelölőnégyzetet,** majd kattintson a **Konfigurálás gombra.**

    Megjelenik **a Mobilalkalmazás konfigurálása** lap.

    ![A QR-kódot tartalmazó képernyő](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a Fiók **hozzáadása** elemet a jobb felső sarokban a **Testreszabás és vezérlés** ikonon, majd válassza a Munkahelyi vagy iskolai fiók **lehetőséget.**

    >[!Note]
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md)című témakörben talál további információt.

4. A készülék kamerájával beszkacezheti a QR-kódot a **mobilalkalmazás konfigurálása** képernyőről a számítógépen, majd válassza a **Kész gombot.**

    >[!Note]
    >Ha a kamera nem tudja rögzíteni a QR-kódot, manuálisan is hozzáadhatja a fiókadatait a Microsoft Authenticator alkalmazáshoz a kétfaktoros ellenőrzéshez. További információt és annak módjáról a [Fiók manuális hozzáadása](user-help-auth-app-add-account-manual.md)című témakörben talál.

5. Tekintse át az eszközön lévő alkalmazás **Fiókok** képernyőjét, és győződjön meg arról, hogy a fiókja helyes, és hogy van-e társított hatjegyű ellenőrző kód. A nagyobb biztonság érdekében az ellenőrző kód 30 másodpercenként változik, megakadályozva, hogy valaki többször is használja a kódot.

    ![Fiókok képernyő](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>További lépések

- Miután hozzáadja fiókjait az alkalmazáshoz, bejelentkezhet az eszközön lévő Authenticator alkalmazással. További információt a [Bejelentkezés az alkalmazás használatával című témakörben](user-help-auth-app-sign-in.md)talál.

- Az iOS rendszert futtató eszközökön a fiók hitelesítő adatairól és a kapcsolódó alkalmazásbeállításokról, például a fiókok sorrendjéről is biztonsági másolatot tarthat a felhőbe. További információt a [Biztonsági másolat és helyreállítás a Microsoft Authenticator alkalmazással című témakörben talál.](user-help-auth-app-backup-recovery.md)
