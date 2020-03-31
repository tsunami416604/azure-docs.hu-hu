---
title: Javítások a "Nem tud eljutni innen" hiba - Azure AD
description: Keresse meg a lehetséges javításokat, hogy miért kapja meg a "Innen nem juthat el" hibaüzenetet.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2484de4e554d16ba049d206981a44654ede28a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190025"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>A "Innen nem lehet eljutni" hibaüzenet lehetséges javításaihoz

A szervezet belső webalkalmazásainak vagy szolgáltatásainak elérésekor hibaüzenet jelenhet meg, amely szerint innen **nem juthat el.** Ez az üzenet azt jelenti, hogy a szervezet olyan házirendet hozott létre, amely megakadályozza, hogy az eszköz hozzáférjen a szervezet erőforrásaihoz. Bár előfordulhat, hogy a probléma megoldásához kapcsolatba kell lépnie az ügyfélszolgálattal, az alábbiakat először kipróbálhatja.

## <a name="make-sure-youre-using-a-supported-browser"></a>Annak megkell jegetése, hogy támogatott böngészőt használ
Ha a **Nem érhető el innen** üzenet jelenik meg, amely azt mondja, hogy nem támogatott böngészőből próbál hozzáférni a szervezet webhelyeihez, ellenőrizze, hogy melyik böngészőt futtatja.

![Böngészőtámogatással kapcsolatos hibaüzenet](media/user-help-device-remediation/browser-version.png)

A probléma megoldásához az operációs rendszer alapján telepítenie és futtatnia kell egy támogatott böngészőt. Windows 10 használata esetén a támogatott böngészők közé tartozik a Microsoft Edge, az Internet Explorer és a Google Chrome. Ha más operációs rendszert használ, ellenőrizheti a [támogatott böngészők](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)teljes listáját.

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Győződjön meg arról, hogy támogatott operációs rendszert használ
Győződjön meg arról, hogy az operációs rendszer támogatott verzióját futtatja, beleértve a következőket:

- **Windows-ügyfél.** Windows 7 vagy újabb.

- **Windows Server rendszerben.** Windows Server 2008 R2 vagy újabb rendszer.

- **Macos.** macOS X vagy újabb

- **Android és iOS.** Az Android és iOS mobil operációs rendszerek legújabb verziója

A probléma megoldásához telepítenie kell és futtatnia kell egy támogatott operációs rendszert.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Az eszköz csatlakoztatása a hálózathoz
Ha a **Nem érhető el innen** üzenet jelenik meg, amely szerint az eszköz nem felel meg a szervezet hozzáférési szabályzatának, győződjön meg arról, hogy csatlakozott az eszközhöz a szervezet hálózatához.

![A hálózattal kapcsolatos hibaüzenet](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Annak ellenőrzése, hogy az eszköz csatlakozik-e a hálózathoz
1. Jelentkezzen be a Windows rendszerbe munkahelyi vagy iskolai fiókjával. Például: alain@contoso.com.

2. Virtuális magánhálózaton (VPN) vagy DirectAccess-en keresztül csatlakozhat a szervezet hálózatához.

3. A csatlakozás után nyomja le a **Windows billentyű+L billentyűkombinációt** az eszköz zárolásához.

4. Oldja fel az eszköz zárolását a munkahelyi vagy iskolai fiókjával, majd próbálja meg újra elérni a problémás alkalmazást vagy szolgáltatást.

    Ha ismét a Nem tud eljutni innen hibaüzenet **jelenik meg,** válassza a **További részletek** hivatkozást, majd lépjen kapcsolatba az ügyfélszolgálattal a részletekkel.

### <a name="to-join-your-device-to-your-network"></a>Az eszköz csatlakoztatása a hálózathoz
Ha az eszköz nem csatlakozik a szervezet hálózatához, az egyiket teheti meg:

- **Csatlakozzon a munkahelyi eszközéhez.** Csatlakozzon a munkahelyi Windows 10-es eszközéhez a szervezet hálózatához, hogy hozzáférhessen a potenciálisan korlátozott erőforrásokhoz. További információt és részletes utasításokat a [Munkahelyi eszköz csatlakoztatása a szervezet hálózatához](user-help-join-device-on-network.md)című témakörben talál.

- **Regisztrálja személyes eszközét a munkahelyére.** Regisztrálja személyes eszközét, általában egy telefont vagy táblagépet a szervezet hálózatán. Az eszköz regisztrálása után hozzáférhet a szervezet korlátozott erőforrásaihoz. További információt és részletes utasításokat a [Személyes eszköz regisztrálása a szervezet hálózatán](user-help-register-device-on-network.md)című témakörben talál.

## <a name="next-steps"></a>További lépések
- [Mi az a MyApps portál?](active-directory-saas-access-panel-introduction.md)

- [Bejelentkezés telefonnal a jelszó helyett](user-help-auth-app-sign-in.md)
