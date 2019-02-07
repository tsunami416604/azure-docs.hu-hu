---
title: Lehetséges okai a "Meg nem innen érheti el" hibaüzenet, az Azure Active Directoryban |} A Microsoft Docs
description: Hárítsa el a lehetséges okok a "Meg nem innen érheti el" hibaüzenetet kap.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 668d69921fd2001cd52bc864e5ee272d179a7bbc
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770469"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>A "Meg nem innen érheti el" hibaüzenetet a lehetséges okok
Arról, hogy a, hibaüzenetet kaphat közben: a szervezet belső webalkalmazásokat vagy szolgáltatásokat, **nem érheti el itt**. Ez az üzenet azt jelenti, a szervezet rendelkezik egy szabályzatot, amely megakadályozza, hogy az eszköz a szervezet erőforrásaihoz. Bár előfordulhat, hogy végül forduljon az ügyfélszolgálathoz a probléma megoldásához rendelkezik, az alábbiakban néhány dolgot, először próbálja meg.

## <a name="make-sure-youre-using-a-supported-browser"></a>Győződjön meg arról, hogy támogatott böngészőt használ
Ha a **nem érheti el itt** üzenetet kap arról, hogy próbál hozzáférni a szervezet helyekhez nem támogatott böngészőből, ellenőrizze, hogy milyen böngészőt futtatja.

![Böngészőtámogatás kapcsolatos hibaüzenet](media/user-help-device-remediation/browser-version.png)

A probléma megoldásához telepítse, és futtassa az operációs rendszer alapján egy támogatott böngészővel. A Windows 10 használata, a támogatott többek között a Microsoft Edge, Internet Explorer és a Google Chrome-ban. Ha eltérő operációs rendszert használ, ellenőrizheti a teljes listát [támogatott böngészők](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Győződjön meg arról, hogy egy támogatott operációs rendszert használja
Győződjön meg arról, hogy az operációs rendszer támogatott verzióját futtatja, többek között:

- **Windows Client.** Windows 7 vagy újabb.

- **A Windows Server.** A Windows Server 2008 R2 vagy újabb.

- **macOS.** macOS X vagy újabb

- **Android és IOS rendszerű.** Android és IOS rendszerű mobileszközök operációs rendszerek legújabb verziója

A probléma megoldásához telepítse, és egy támogatott operációs rendszert futtat.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Győződjön meg arról, hogy az eszköz csatlakozik a hálózathoz
Ha a **nem érheti el itt** üzenetet kap arról, hogy az eszköz megfelelőségi ki-e a szervezet hozzáférési szabályzatot, és ellenőrizze, hogy az eszköz csatlakozott a munkahelyi hálózathoz.

![Van-e a hálózaton kapcsolatos hibaüzenet](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Ellenőrizze, hogy az eszköz csatlakozik-e a hálózati
1. Jelentkezzen be munkahelyi vagy iskolai fiókjával Windows. Például: alain@contoso.com.

2. A virtuális magánhálózati (VPN) vagy Directaccessen keresztül a vállalati hálózathoz csatlakozni.

3. Miután csatlakozott, nyomja le az **Windows billentyű + l billentyűkombinációt** zárolja az eszközt.

4. Oldja fel az eszköz a munkahelyi vagy iskolai fiókjával, és ismételje meg a problémás alkalmazás eléréséhez, vagy újra a szolgáltatást.

    Ha megjelenik a **nem érheti el itt** hibaüzenet jelenik meg, válassza ki a **további részleteket** hivatkozásra, és, forduljon a Segélyszolgálathoz az adataival.

### <a name="to-join-your-device-to-your-network"></a>Az eszköz csatlakoztatása a hálózat
Ha az eszköz nem csatlakozik a vállalati hálózaton, két dolog egyikét teheti:

- **Eszköz munkahelyi csatlakoztatáson.** A munkahelyi tulajdonú Windows 10 rendszerű eszköz csatlakoztatása a vállalati hálózaton potenciálisan korlátozott erőforrások eléréséhez. További információk és részletes útmutatást lásd: [a munkahelyi eszköz csatlakoztatása a szervezet hálózati](user-help-join-device-on-network.md).

- **Regisztrálja a személyes eszköz munkahelyi számára.** Regisztrálja a személyes eszköz, általában egy telefonjához vagy táblagépéhez, a vállalati hálózaton. Az eszköz regisztrálása után hozzáférhet a szervezet korlátozott erőforrások. További információk és részletes útmutatást lásd: [regisztrálja a személyes eszköz a vállalati hálózaton](user-help-register-device-on-network.md).

## <a name="next-steps"></a>További lépések
- [Mi az a MyApps portálról?](active-directory-saas-access-panel-introduction.md)

- [Bejelentkezés telefonnal a jelszó helyett](user-help-auth-app-sign-in.md)
