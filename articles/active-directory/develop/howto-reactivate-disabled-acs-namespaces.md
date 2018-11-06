---
title: Az Azure Access Control Service (ACS) letiltott névterek újraaktiválása
description: Megtudhatja, hogyan kereshet és engedélyezése az Azure Access Control Service (ACS) névteret, és láthatóan tartja őket engedélyezve van, amíg 2019. február 4. hosszabbítsa meg.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019167"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Hogyan: Reactivate le van tiltva az Access Control Service-névterek

2017 November jelentettük be, hogy a Microsoft Azure Access Control Service (ACS), az Azure Active Directory (Azure AD), a szolgáltatás 2018. November 7 határidővel kivezetjük.

Mivel ezt követően az ACS-előfizetések rendszergazdai e-mailek kapcsolatos ACS funkciókészletét 12 hónap, 9 hónap, 6 hónapra 3 hónap, 1 hónap, 2 héten, 1 hét, és 1 nap 2018. November 7 kivezetési dátuma előtt több e-mailt elküldtük Önnek.

2018. október 3. én bejelentettük (e-mailben és [egy blogbejegyzés](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)), akiknek az áttelepítést követően 2018. November 7. nem lehet befejezni a bővítmény ajánlat. A közlemény is tartalmazott utasításokat a bővítmény lekérdezéséhez.

## <a name="why-your-namespace-is-disabled"></a>Miért érdemes a névtér le van tiltva

Ha a bővítmény rögzítette még nem található, megkezdjük hogy tiltsa le a 2018. November 7-as Access Control-névtereket. Ha lemaradt a kommunikációhoz, és továbbra is szeretné elvégezni a beléptetést a bővítmény 2019. február 4. kövesse az alábbi szakaszokban található.

> [!NOTE]
> A PowerShell-parancsok futtatása, és hosszabbítsa az előfizetés az rendszergazdájának kell lennie.

## <a name="find-and-enable-your-acs-namespaces"></a>Keresse meg és az Access Control-névtereket engedélyezése

Az ACS PowerShell használatával az Access Control-névtereket listában, és aktiválja újra, amelyet le vannak tiltva.

1. Töltse le és telepítse az ACS PowerShell:
    1. Nyissa meg a PowerShell-galériából, és töltse le [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. A modul telepítése:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Az összes lehetséges parancsok listájának lekérése:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Segítség kérése egy bizonyos paranccsal, futtassa:

        ```
        Get-Help [Command-Name] -Full
        ```
    
        ahol `[Command-Name]` az ACS-parancs neve.
1. Az használatával érhető el az Azure-előfizetések listájának a **Get-AcsSubscription** parancsmagot.
1. Az ACS névterek használatával listázása a **Get-AcsNamespace** parancsmagot.
1. Győződjön meg arról, hogy erősítse meg, amelyek le vannak-e tiltva a névterek `State` van `Disabled`.

    [![Győződjön meg arról, hogy a névterek le vannak tiltva](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Is `nslookup {your-namespace}.accesscontrol.windows.net` , győződjön meg arról, ha a tartomány még mindig aktív.

1. Az ACS namespace(s) használatával engedélyezze a **engedélyezése – AcsNamespace** parancsmagot.

    Miután engedélyezte a namespace(s), kérheti egy bővítmény, úgy, hogy a namespace(s) nem tiltható le, mielőtt újra 2019. február 4. Ezt követően az ACS-összes kérelem sikertelen lesz.

## <a name="request-an-extension"></a>Hosszabbítsa

1. Az ACS-névtér felügyeleti portálon lépjen a `https://{your-namespace}.accesscontrol.windows.net`.
1. Válassza ki a **olvasási feltételek** gombra kattintva, olvassa el a [használati feltételek frissítése](https://azure.microsoft.com/support/legal/access-control/), amely fog irányítja át a frissített használati feltételeket tartalmazó oldalt.

    [![Az olvasási feltételek gomb kiválasztása](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Válassza ki **kérelem bővítmény** a szalagcímen az oldal tetején található. A gomb engedélyezése csak a elolvasása után a [használati feltételek frissítése](https://azure.microsoft.com/support/legal/access-control/).

    [![Válassza ki a bővítmény kérelem gomb](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. A bővítmény kérelem regisztrálása után a lap frissül, és a egy új szalagcím, amely a lap tetején.

    [![Frissített oldalt, a frissített szalagcímmel](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Súgó és támogatás

- Ha ez az útmutató lépéseinek futtatása a problémákat tapasztal, forduljon a [az Azure-támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Ha az ACS használatból való kivonást egyaránt kapcsolatos kérdésekért és Visszajelzésért, írjon nekünk az acsfeedback@microsoft.com.

## <a name="next-steps"></a>További lépések

- Tekintse át az ACS használatból való kivonást egyaránt az [Útmutató: az Azure Access Control Service át](active-directory-acs-migration.md).
