---
title: Az Azure AD jelszó védelmi előzetes műveleteket és jelentéskészítés
description: Az Azure AD jelszó védelmi előzetes üzembe helyezés utáni műveleteket, és jelentéskészítés
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 14aa52b6d424423f4863efa63f3e2e66b84dac70
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163945"
---
# <a name="preview-azure-ad-password-protection-post-deployment"></a>Előzetes verzió: Azure AD jelszó védelme üzembe helyezés utáni

|     |
| --- |
| Az Azure AD jelszóvédelem és a letiltott jelszavak egyéni lista a nyilvános előzetes verziójú funkciók az Azure Active Directory. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Befejezése után a [telepítése az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises.md) a helyszínen, van néhány elemet, amely az Azure Portalon kell konfigurálni.

## <a name="configure-the-custom-banned-password-list"></a>Az egyéni letiltott jelszavak listájának konfigurálása

Kövesse a cikk útmutatást [konfigurálása a letiltott jelszavak egyéni lista](howto-password-ban-bad.md) testreszabása a szervezet számára a letiltott jelszavak lista lépéseit.

## <a name="enable-password-protection"></a>Jelszavas védelem engedélyezése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszavas védelem (előzetes verzió)**.
1. Állítsa be **engedélyezése a Windows Server Active Directory jelszavas védelem** való **Igen**
1. Említetteknek megfelelően az [üzembe helyezési útmutató](howto-password-ban-bad-on-premises.md#deployment-strategy), javasoljuk, hogy először állítsa a **mód** való **naplózási**
   * Ha elégedett a szolgáltatással, válthat a **mód** való **kényszerített**
1. Kattintson a **Mentés** gombra.

![Az Azure Portalon az Azure AD jelszó-védelem összetevői engedélyezése](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Vizsgálati üzemmód

Vizsgálati üzemmód célja arra, hogy a szoftver egy "Mi történik, ha" módban fusson. Minden egyes tartományvezérlő agent szolgáltatást egy bejövő jelszót a jelenleg aktív szabályzat alapján értékeli ki. A jelenlegi házirend rendszervizsgálati módban kell van konfigurálva, ha a "rossz" jelszavak Eseménynapló-üzenetek eredményez, de fogadja. Ez az az egyetlen különbség, naplózási és kényszerítése módban; között minden egyéb művelet futtatása azonos.

> [!NOTE]
> A Microsoft azt javasolja, hogy első üzembe helyezés és a tesztelés mindig kezd rendszervizsgálati módban. Eseményeket az eseménynaplóban, gondolja át, hogy bármely meglévő működési folyamatok kényszerítési módban engedélyezését követően elosztott, hogy majd ellenőrizni kell.

## <a name="enforce-mode"></a>Kényszerítési módra

Kényszerítése módban a végső konfiguráció jelent. Vizsgálati üzemmód újabb, mint minden tartományvezérlő ügynökszolgáltatás bejövő jelszavak a jelenleg aktív szabályzat alapján értékeli ki. Ha kényszerítési módban, ha engedélyezve van, a rendszer elutasítja számít, hogy a házirend szerint nem biztonságos jelszót.

Jelszó az Azure AD jelszóvédelem DC ügynök elutasította a kényszerítési módban, amikor a végfelhasználó által látott látható hatást megegyezik a mi lenne láthatják Ha jelszavát a hagyományos helyszíni jelszó összetettségi kényszerítése elutasította. Például egy felhasználó előfordulhat, hogy tekintse meg az alábbi hagyományos hibaüzenet jelenik meg a logon\change jelszóképernyő:

"Nem sikerült frissíteni a jelszavát. Az új jelszó a megadott érték nem felel meg a hossza, összetettségi vagy előzményekre vonatkozó követelményeknek annak a tartománynak."

Ez az üzenet csak egy példa a több lehetséges kimenetek. Az adott hibaüzenetet a tényleges szoftver vagy a forgatókönyv, amely egy nem biztonságos jelszót megpróbálja függően változhat.

Érintett felhasználók kell előfordulhat, hogy az informatikai részleg az új követelményeinek megismeréséhez, és több tud biztonságos jelszó használata.

## <a name="usage-reporting"></a>Használati jelentések készítése

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag is használható előállításához tevékenység összegzését. Egy példa a parancsmag kimenete a következőképpen történik:

```
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

A parancsmag reporting hatóköre egy befolyásolhatja a – erdő, - tartomány vagy -DomainController paraméterek. Nem a paraméterek megadása azt jelenti, – erdőben.

> [!NOTE]
> Ez a parancsmag minden tartományvezérlő ügynökszolgáltatás rendszergazdai Eseménynapló távolról lekérdezésével működik. Ha az eseménynaplókban események nagy számú, a parancsmag végrehajtásához hosszú időt vehet igénybe. Emellett tömeges hálózati lekérdezések célja nagyméretű adathalmazok hatással lehet a tartományvezérlő teljesítményét. Ezért ez a parancsmag használandó gondosan az éles környezetben.

## <a name="next-steps"></a>További lépések

[Hibaelhárítás és a naplózási információk az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-troubleshoot.md)
