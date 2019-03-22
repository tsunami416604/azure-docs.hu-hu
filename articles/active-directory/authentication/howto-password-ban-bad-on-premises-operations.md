---
title: Az Azure AD jelszóvédelem műveletek és jelentéskészítés – Azure Active Directory
description: Az Azure AD jelszóvédelem üzembe helyezés utáni műveleteket, és jelentéskészítés
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca85007bb016cc98d1be61ce08865945e699ad4a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312427"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Az Azure AD jelszóvédelem eljárások

Befejezése után a [telepítése az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-deploy.md) a helyszínen, van néhány elemet, amely az Azure Portalon kell konfigurálni.

## <a name="configure-the-custom-banned-password-list"></a>Az egyéni letiltott jelszavak listájának konfigurálása

Kövesse a cikk útmutatást [konfigurálása a letiltott jelszavak egyéni lista](howto-password-ban-bad-configure.md) testreszabása a szervezet számára a letiltott jelszavak lista lépéseit.

## <a name="enable-password-protection"></a>Jelszavas védelem engedélyezése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszavas védelem**.
1. Állítsa be **engedélyezése a Windows Server Active Directory jelszavas védelem** való **Igen**
1. Említetteknek megfelelően az [üzembe helyezési útmutató](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), javasoljuk, hogy először állítsa a **mód** való **naplózási**
   * Ha elégedett a szolgáltatással, válthat a **mód** való **kényszerített**
1. Kattintson a **Mentés** gombra.

![Az Azure Portalon az Azure AD jelszóvédelem összetevők engedélyezése](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Vizsgálati üzemmód

Vizsgálati üzemmód célja arra, hogy a szoftver egy "Mi történik, ha" módban fusson. Minden egyes tartományvezérlő agent szolgáltatást egy bejövő jelszót a jelenleg aktív szabályzat alapján értékeli ki. A jelenlegi házirend rendszervizsgálati módban kell van konfigurálva, ha a "rossz" jelszavak Eseménynapló-üzenetek eredményez, de fogadja. Ez az az egyetlen különbség, naplózási és kényszerítése módban; között minden egyéb művelet futtatása azonos.

> [!NOTE]
> A Microsoft azt javasolja, hogy első üzembe helyezés és a tesztelés mindig kezd rendszervizsgálati módban. Eseményeket az eseménynaplóban, gondolja át, hogy bármely meglévő működési folyamatok kényszerítési módban engedélyezését követően elosztott, hogy majd ellenőrizni kell.

## <a name="enforce-mode"></a>Kényszerítési módra

Kényszerítése módban a végső konfiguráció jelent. Vizsgálati üzemmód újabb, mint minden tartományvezérlő ügynökszolgáltatás bejövő jelszavak a jelenleg aktív szabályzat alapján értékeli ki. Ha kényszerítési módban, ha engedélyezve van, a rendszer elutasítja számít, hogy a házirend szerint nem biztonságos jelszót.

Jelszó az Azure AD-jelszó DC védelmi ügynök elutasította a kényszerítési módban, amikor a végfelhasználó által látott látható hatást megegyezik a mi lenne láthatják Ha jelszavát a hagyományos helyszíni jelszó összetettségi kényszerítése elutasította. Például egy felhasználó lehet, hogy tekintse meg az alábbi hagyományos hibaüzenet jelenik meg a Windows logon\change jelszóképernyő:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ez az üzenet csak egy példa a több lehetséges kimenetek. Az adott hibaüzenetet a tényleges szoftver vagy a forgatókönyv, amely egy nem biztonságos jelszót megpróbálja függően változhat.

Érintett felhasználók kell előfordulhat, hogy az informatikai részleg az új követelményeinek megismeréséhez, és több tud biztonságos jelszó használata.

## <a name="enable-mode"></a>Mód engedélyezése

Ez a beállítás általában alapértelmezett engedélyezve (Igen) állapotban kell hagyni. Letiltva (nem), a beállítás konfigurálásával okoz az összes üzembe helyezett Azure AD-jelszó DC védelmi ügynökök, ahol az összes jelszavak ügyfélként fogad el egy videokártyának üzemmódba-van, ezért nem egyetlen ellenőrzési tevékenység fog végrehajtott ajánlattevőről (mert például még a naplózási események fog bocsátja ki).

## <a name="next-steps"></a>További lépések

[Az Azure AD jelszóvédelem figyelése](howto-password-ban-bad-on-premises-monitor.md)
