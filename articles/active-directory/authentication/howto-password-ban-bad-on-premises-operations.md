---
title: Azure AD jelszavas védelmi műveletek és jelentéskészítés – Azure Active Directory
description: Azure AD jelszavas védelem telepítés utáni műveletek és jelentéskészítés
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
ms.openlocfilehash: b5ff7f0bbf1bf474a611ae033165bca6dfaac676
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097634"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Az Azure AD jelszavas védelem működési eljárásai

Az [Azure ad jelszavas védelem helyszíni telepítésének](howto-password-ban-bad-on-premises-deploy.md) befejezése után van néhány elem, amelyet be kell állítani a Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Egyéni tiltott jelszavak listájának konfigurálása

Kövesse az [Egyéni tiltott jelszavak konfigurálása](howto-password-ban-bad-configure.md) című cikkben ismertetett útmutatást, amelyekkel testre szabhatja a tiltott jelszavak listáját a szervezet számára.

## <a name="enable-password-protection"></a>Jelszavas védelem engedélyezése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a **Azure Active Directory**, a **hitelesítési módszereket**, majd a **jelszavas védelmet**.
1. **Engedélyezze a jelszavas védelem engedélyezése beállítást a Windows Server Active Directory** az **Igen** értékre
1. Az [üzembe helyezési útmutatóban](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)említetteknek megfelelően javasolt a **mód** beállítása a **naplózásra** .
   * A funkció használata után átválthat a mód **kényszerített** **állapotára**
1. Kattintson a **Save** (Mentés) gombra

![Az Azure AD jelszavas védelem összetevőinek engedélyezése a Azure Portalban](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Vizsgálati üzemmód

A vizsgálati mód célja, hogy a szoftvert "mi if" módban futtassa. Minden tartományvezérlő ügynök-szolgáltatás a jelenleg aktív házirendnek megfelelően kiértékel egy bejövő jelszót. Ha az aktuális házirend naplózási módban van konfigurálva, a "rossz" jelszavak az Eseménynapló-üzeneteket eredményezik, de elfogadják őket. Ez az egyetlen különbség a naplózás és a kikényszerítés mód között; minden más művelet ugyanazt a műveletet futtatja.

> [!NOTE]
> A Microsoft azt javasolja, hogy a kezdeti üzembe helyezés és a tesztelés mindig vizsgálati módban induljon el. Az Eseménynaplóban lévő eseményeket figyelni kell, hogy meg lehessen állapítani, hogy a meglévő működési folyamatok megzavarják-e a kényszerített mód engedélyezése után.

## <a name="enforce-mode"></a>Érvényesítési mód

A kikényszerítés mód végső konfigurációként szolgál. Ahogy a fenti vizsgálati mód esetében, minden egyes tartományvezérlő ügynök szolgáltatás a jelenleg aktív szabályzatnak megfelelően kiértékeli a bejövő jelszavakat. Ha a kényszerített mód engedélyezve van, akkor a rendszer visszautasít egy, a szabályzatnak megfelelően biztonságosnak ítélt jelszót.

Ha az Azure AD jelszavas védelmet biztosító tartományvezérlő ügynöke visszautasítja a jelszót, a végfelhasználók által látott látható hatás megegyezik azzal, amit látni fognak, ha a hagyományos helyszíni jelszó-bonyolultsági kényszerítéssel elutasította a jelszavát. Előfordulhat például, hogy egy felhasználó a következő hagyományos hibaüzenetet jeleníti meg a Windows logon\change jelszavának képernyőjén:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ez az üzenet csak egy példát mutat be több lehetséges eredményre. Az adott hibaüzenet attól függően változhat, hogy milyen szoftvert vagy forgatókönyvet próbál meg beállítani egy nem biztonságos jelszó megadására.

Előfordulhat, hogy az érintett végfelhasználóknak az informatikai munkatársakkal kell dolgozniuk az új követelmények megismeréséhez és a biztonságos jelszavak kiválasztásához.

> [!NOTE]
> Az Azure AD jelszavas védelme nem szabályozza az ügyfélszámítógép által a gyenge jelszavak elutasításakor megjelenő hibaüzenetet.

## <a name="enable-mode"></a>Mód engedélyezése

Ennek a beállításnak az alapértelmezett engedélyezett (igen) állapotban kell maradnia. Ha ezt a beállítást Letiltva (nem) állítja be, a rendszer az összes telepített Azure AD jelszavas védelmi TARTOMÁNYVEZÉRLŐi ügynököt egy olyan nyugalmi módba helyezi, amelyben az összes jelszó el van fogadva, és az érvényesítési tevékenységek nem lesznek végrehajtva (például még naplózási események nélkül is). lesz kibocsátva).

## <a name="next-steps"></a>További lépések

[Az Azure AD jelszavas védelem figyelése](howto-password-ban-bad-on-premises-monitor.md)
