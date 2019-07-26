---
title: Adja hozzá munkahelyi vagy iskolai fiókját a Microsoft Authenticator app-Azure Active Directoryhoz | Microsoft Docs
description: Munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazáshoz kétfaktoros ellenőrzéshez.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: a73500d17a0dd5d55e60700f7c0b6dbe92a3f96b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382534"
---
# <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

Ha a szervezet kétfaktoros ellenőrzést használ, beállíthatja a munkahelyi vagy iskolai fiókját, hogy az Microsoft Authenticator alkalmazást az egyik ellenőrzési módszerként használja.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

## <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

1. A számítógépen lépjen a [további biztonsági ellenőrzés](https://aka.ms/mfasetup) lapra.

    >[!Note]
    >Ha nem látja a **további biztonsági ellenőrzés** oldalt, lehetséges, hogy a rendszergazda bekapcsolta a biztonsági adatok (előzetes verzió) szolgáltatást. Ha ez a helyzet, kövesse a [biztonsági adatok beállítása a hitelesítő alkalmazás használatára](security-info-setup-auth-app.md) című szakasz utasításait. Ha ez nem így van, segítségért forduljon a szervezet ügyfélszolgálatához. További információ a biztonsági adatokról: [biztonsági adatok (előzetes verzió) – áttekintés](user-help-security-info-overview.md).

2. Jelölje be a hitelesítő **alkalmazás**melletti jelölőnégyzetet, majd válassza a **Konfigurálás**lehetőséget.

    Megjelenik a **Mobile App konfigurálása** oldal.

    ![A QR-kódot biztosító képernyő](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók**lehetőséget.

4. Az eszköz kamerájának használatával beszkennelheti a QR-kódot a **mobil alkalmazás konfigurálása** képernyőn a számítógépen, majd válassza a **kész**lehetőséget.

    >[!Note]
    >Ha a kamera nem tudja rögzíteni a QR-kódot, manuálisan is felveheti a fiók adatait a Microsoft Authenticator alkalmazásba kétfaktoros ellenőrzéshez. További információt és útmutatást a [fiók manuális hozzáadása](user-help-auth-app-add-account-manual.md)című témakörben talál.

5. Tekintse át az alkalmazás **fiókok** képernyőjét az eszközön, és győződjön meg róla, hogy a fiókja megfelelő, és hogy van-e hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

    ![Fiókok képernyő](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>További lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a hitelesítő alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
