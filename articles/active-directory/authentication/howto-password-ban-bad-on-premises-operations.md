---
title: Az Azure AD jelszó védelmi előzetes műveleteket és jelentéskészítés
description: Az Azure AD jelszó védelmi előzetes üzembe helyezés utáni műveleteket, és jelentéskészítés
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6a61fdeaf1a751ab4001257335abdcbd6fac9cbf
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739464"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Előzetes verzió: Azure AD jelszó védelmi eljárások

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Befejezése után a [telepítése az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-deploy.md) a helyszínen, van néhány elemet, amely az Azure Portalon kell konfigurálni.

## <a name="configure-the-custom-banned-password-list"></a>Az egyéni letiltott jelszavak listájának konfigurálása

Kövesse a cikk útmutatást [konfigurálása a letiltott jelszavak egyéni lista](howto-password-ban-bad-configure.md) testreszabása a szervezet számára a letiltott jelszavak lista lépéseit.

## <a name="enable-password-protection"></a>Jelszavas védelem engedélyezése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszavas védelem (előzetes verzió)**.
1. Állítsa be **engedélyezése a Windows Server Active Directory jelszavas védelem** való **Igen**
1. Említetteknek megfelelően az [üzembe helyezési útmutató](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), javasoljuk, hogy először állítsa a **mód** való **naplózási**
   * Ha elégedett a szolgáltatással, válthat a **mód** való **kényszerített**
1. Kattintson a **Mentés** gombra.

![Az Azure Portalon az Azure AD jelszó-védelem összetevői engedélyezése](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Vizsgálati üzemmód

Vizsgálati üzemmód célja arra, hogy a szoftver egy "Mi történik, ha" módban fusson. Minden egyes tartományvezérlő agent szolgáltatást egy bejövő jelszót a jelenleg aktív szabályzat alapján értékeli ki. A jelenlegi házirend rendszervizsgálati módban kell van konfigurálva, ha a "rossz" jelszavak Eseménynapló-üzenetek eredményez, de fogadja. Ez az az egyetlen különbség, naplózási és kényszerítése módban; között minden egyéb művelet futtatása azonos.

> [!NOTE]
> A Microsoft azt javasolja, hogy első üzembe helyezés és a tesztelés mindig kezd rendszervizsgálati módban. Eseményeket az eseménynaplóban, gondolja át, hogy bármely meglévő működési folyamatok kényszerítési módban engedélyezését követően elosztott, hogy majd ellenőrizni kell.

## <a name="enforce-mode"></a>Kényszerítési módra

Kényszerítése módban a végső konfiguráció jelent. Vizsgálati üzemmód újabb, mint minden tartományvezérlő ügynökszolgáltatás bejövő jelszavak a jelenleg aktív szabályzat alapján értékeli ki. Ha kényszerítési módban, ha engedélyezve van, a rendszer elutasítja számít, hogy a házirend szerint nem biztonságos jelszót.

Jelszó az Azure AD jelszóvédelem DC ügynök elutasította a kényszerítési módban, amikor a végfelhasználó által látott látható hatást megegyezik a mi lenne láthatják Ha jelszavát a hagyományos helyszíni jelszó összetettségi kényszerítése elutasította. Például egy felhasználó lehet, hogy tekintse meg az alábbi hagyományos hibaüzenet jelenik meg a Windows logon\change jelszóképernyő:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ez az üzenet csak egy példa a több lehetséges kimenetek. Az adott hibaüzenetet a tényleges szoftver vagy a forgatókönyv, amely egy nem biztonságos jelszót megpróbálja függően változhat.

Érintett felhasználók kell előfordulhat, hogy az informatikai részleg az új követelményeinek megismeréséhez, és több tud biztonságos jelszó használata.

## <a name="enable-mode"></a>Mód engedélyezése

Ez a beállítás általában alapértelmezett engedélyezve (Igen) állapotban kell hagyni. Ez a beállítás le van tiltva (nem) konfigurálása miatt összes üzembe helyezett Azure AD-jelszavamat DC védelmi ügynököket, ahol az összes jelszavak ügyfélként fogad el egy videokártyának üzemmódba-van, ezért nem egyetlen ellenőrzési tevékenység fog végrehajtott ajánlattevőről (mert például még a naplózási események fog bocsátja ki).

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
> Ez a parancsmag minden olyan tartományvezérlőre, amely egy Powershell-munkamenet megnyitásával működik. Ahhoz, hogy sikeres, a Powershell távoli munkamenet-támogatás engedélyezni kell az egyes tartományvezérlőkön, és az ügyfél megfelelő jogosultságokkal kell rendelkeznie. Powershell távoli munkamenet követelményeiről további információkért futtassa a "Get-Help about_Remote_Troubleshooting" Powershell-ablakban.

> [!NOTE]
> Ez a parancsmag minden tartományvezérlő ügynökszolgáltatás rendszergazdai Eseménynapló távolról lekérdezésével működik. Ha az eseménynaplókban események nagy számú, a parancsmag végrehajtásához hosszú időt vehet igénybe. Emellett tömeges hálózati lekérdezések célja nagyméretű adathalmazok hatással lehet a tartományvezérlő teljesítményét. Ezért ez a parancsmag használandó gondosan az éles környezetben.

## <a name="next-steps"></a>További lépések

[Hibaelhárítási és figyelési az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-troubleshoot.md)
