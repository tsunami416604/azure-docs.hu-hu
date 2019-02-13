---
title: Az Azure Access Control Service (ACS) letiltott névterek újraaktiválása
description: Megtudhatja, hogyan kereshet és engedélyezése az Azure Access Control Service (ACS) névteret, és láthatóan tartja őket engedélyezve van, amíg 2019. február 4. hosszabbítsa meg.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0078c260e09ec9fd31ecf2124bc4bf7ad7f92b0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173742"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Útmutató: Letiltott Access Control Service-névterek újraaktiválása

2017 November jelentettük be, hogy a Microsoft Azure Access Control Service (ACS), az Azure Active Directory (Azure AD), a szolgáltatás 2018. November 7 határidővel kivezetjük.

Mivel ezt követően az ACS-előfizetések rendszergazdai e-mail az ACS használatból való kivonást egyaránt 12 hónap, 9 hónap, 3 hónap, 1 hónap, 2 héten, 1 hét, és 1 nap 2018. November 7 kivezetési dátuma előtt 6 hónapra vonatkozó e-mailt elküldtük Önnek.

2018. október 3. én bejelentettük (e-mailben és [egy blogbejegyzés](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)), akiknek az áttelepítést követően 2018. November 7. nem lehet befejezni a bővítmény ajánlat. A közlemény is volt a bővítmény kérő vonatkozó utasításokat.

## <a name="why-your-namespace-is-disabled"></a>Miért érdemes a névtér le van tiltva

Ha a bővítmény rögzítette még nem található, kezdjük 2018. November 7-as Access Control-névtereket letiltása. Kell kért 2019. február 4. a bővítmény már; Ellenkező esetben nem lesz ahhoz, hogy a névterek Powershellen keresztül.

> [!NOTE]
> A PowerShell-parancsok futtatása, és hosszabbítsa szolgáltatás-rendszergazda vagy az előfizetés társadminisztrátorának kell lennie.

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

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        ahol `[Command-Name]` az ACS-parancs neve.
1. Az ACS használatával csatlakozhat a **Connect-AcsAccount** parancsmagot. 

    Szükség lehet a végrehajtási házirend módosításához futtassa **Set-ExecutionPolicy** a parancs futtatása előtt.
1. Az használatával érhető el az Azure-előfizetések listájának a **Get-AcsSubscription** parancsmagot.
1. Az ACS névterek használatával listázása a **Get-AcsNamespace** parancsmagot.
1. Győződjön meg arról, hogy erősítse meg, amelyek le vannak-e tiltva a névterek `State` van `Disabled`.

    [![Győződjön meg arról, hogy a névterek le vannak tiltva](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Is `nslookup {your-namespace}.accesscontrol.windows.net` , győződjön meg arról, ha a tartomány még mindig aktív.

1. Az ACS namespace(s) használatával engedélyezze a **engedélyezése – AcsNamespace** parancsmagot.

    Miután engedélyezte a namespace(s), kérheti egy bővítmény, úgy, hogy a namespace(s) nem tiltható le, mielőtt újra 2019. február 4. Ezt követően az ACS-összes kérelem sikertelen lesz.

## <a name="request-an-extension"></a>Hosszabbítsa

2019. január 21-én kezdődő új bővítmény kérések jegyében.

Névterek letiltását az ügyfelek, akik bővítmények 2019. február 4. a kért program kezdődik. Továbbra is engedélyezheti újra névterek Powershellen keresztül, de a névterek le lesz tiltva a 48 óra után.

2019. március 4 után ügyfelek már nem képes bármilyen névterek PowerShell-lel engedélyezze újra.

További bővítmények már nem lesz automatikusan jóváhagyja. Ha áttelepítéséhez további időre van szüksége, forduljon a [az Azure-támogatás](https://portal.azure.com/#create/Microsoft.Support) részletes áttelepítési ütemterv megadását.

### <a name="to-request-an-extension"></a>Egy bővítmény kérése

1. Jelentkezzen be az Azure Portalon, és hozzon létre egy [új támogatási kérelem](https://portal.azure.com/#create/Microsoft.Support).
1. Adja meg az új támogatási űrlap, az alábbi példában látható módon.

    | Támogatási kérelem mező | Érték |
    |-----------------------|--------------------|
    | **Probléma típusa** | `Technical` |
    | **Előfizetés** | Az előfizetés beállítása |
    | **Szolgáltatás** | `All services` |
    | **Erőforrás** | `General question/Resource not available` |
    | **Probléma típusa** | `ACS to SAS Migration` |
    | **Tárgy** | A probléma leírása |

  ![Új műszaki támogatási kérelem](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Súgó és támogatás

- Ha ez az útmutató lépéseinek futtatása a problémákat tapasztal, forduljon a [az Azure-támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Ha az ACS használatból való kivonást egyaránt kapcsolatos kérdésekért és Visszajelzésért, írjon nekünk az acsfeedback@microsoft.com.

## <a name="next-steps"></a>További lépések

- Tekintse át az ACS használatból való kivonást egyaránt az [hogyan: Az Azure Access Control Service át](active-directory-acs-migration.md).
