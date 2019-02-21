---
title: A személyes Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazás – az Azure Active Directory |} A Microsoft Docs
description: Hogyan lehet hozzáadni a személyes Microsoft-fiókok, például az Outlook.com- vagy Xbox LIVE kétfaktoros ellenőrzéshez a Microsoft Authenticator alkalmazást.
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
ms.openlocfilehash: 67cb3d049cc2d2f26be6d62270e677a5cbcd39cc
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455041"
---
# <a name="add-your-personal-microsoft-accounts"></a>A személyes Microsoft-fiókok hozzáadása
Adja hozzá a személyes Microsoft-fiókok esetében például Outlook.com-os és az Xbox LIVE, a Microsoft Authenticator alkalmazás a szokásos kétfaktoros ellenőrzési folyamata és a bejelentkezési módszer beállításának phone.

- **Standard szintű kétfaktoros-ellenőrzési módszert.** Írja be a felhasználónevét és jelszavát jelentkeztethetik az eszközre éppen bejelentkezett, és válassza akár a Microsoft Authenticator alkalmazás értesítést küld, vagy ha szeretne-e a társított ellenőrző kód másolása a **fiókok** képernyőjén a Microsoft Authenticator alkalmazást.

- **Beállításának bejelentkezési módszerrel.** A felhasználónevét írja be az eszköz személyes Microsoft-fiókja van bejelentkezik, és a mobileszköz használatával győződjön meg arról, hogy az ujjlenyomat-alapú, a face vagy a PIN-kód használatával. Ezzel a módszerrel nem kell a jelszavát adja meg.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítse a Microsoft Authenticator alkalmazást. Ha még nem tette, hogy, kövesse a [töltse le és telepítse az alkalmazást](user-help-auth-app-download-install.md) cikk.

## <a name="add-your-personal-microsoft-account"></a>A személyes Microsoft-fiók hozzáadása
A személyes Microsoft-fiókjával is hozzáadhat, kétfaktoros ellenőrzési első bekapcsolásával, majd adja hozzá a fiókot az alkalmazáshoz.

>[!Note]
>Ha azt tervezi, a személyes Microsoft-fiók beállításának telefonos bejelentkezés csak használja, nem kell a kétfaktoros ellenőrzés. Javasoljuk azonban, a fokozott biztonság, kapcsolja be a kétfaktoros ellenőrzése.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés

1. A számítógépen nyissa meg a [biztonsággal kapcsolatos alapok](https://account.microsoft.com/security) lapon, és jelentkezzen be személyes Microsoft-fiókjával. Például: alain@outlook.com.

2. Alsó részén a **biztonsággal kapcsolatos alapok** lappján válassza a **további biztonsági beállítások** hivatkozásra.

    ![Biztonsági alapvető beállítások lapról a "további biztonsági beállítások" hivatkozás kiemelésével](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Nyissa meg a **kétlépéses ellenőrzés** szakaszt, és válassza ki a szolgáltatás **a**. Is kikapcsolhatja, itt Ha már nem szeretné használni, a személyes fiókjával.

### <a name="add-your-microsoft-account-to-the-app"></a>A Microsoft-fiók hozzáadása az alkalmazáshoz

1. Nyissa meg a Microsoft Authenticator alkalmazást a mobileszközén.

2. Válassza ki **fiók hozzáadása** származó a **testreszabása és vezérlési** a jobb felső sarokban lévő ikonra.

    ![Fiókok oldala, amelyen a Testreszabás és ellenőrzési ikonja kiemelve](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Az a **fiók hozzáadása** lapon a **személyes fiók**.

4. Jelentkezzen be a személyes fiók, a megfelelő e-mail-címmel (például alain@outlook.com), majd **tovább**.

    >[!Note]
    >Ha a személyes Microsoft-fiók nem rendelkezik, akkor hozzon létre egyet, itt.

5. Adja meg a jelszót, és válassza **jelentkezzen be a**.

    A személyes fiók hozzáadódik a Microsoft Authenticator alkalmazást.

## <a name="next-steps"></a>További lépések

- Miután hozzáadta a fiókok az alkalmazáshoz, bejelentkezhet az Authenticator alkalmazás használatával az eszközén. További információkért lásd: [jelentkezzen be az alkalmazás](user-help-auth-app-sign-in.md).

- IOS rendszerű eszközökhöz is készíthető a fiók hitelesítő adatait, és a felhőhöz kapcsolódó alkalmazás beállításait, például a fiókok sorrendjét. További információkért lásd: [biztonsági mentés és helyreállítás a Microsoft Authenticator alkalmazás](user-help-auth-app-backup-recovery.md).
