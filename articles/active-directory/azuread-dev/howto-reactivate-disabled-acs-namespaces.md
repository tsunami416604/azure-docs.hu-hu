---
title: A letiltott Azure Access Control Service (ACS) névterek újraaktiválása
description: Keresse meg és engedélyezze az Azure Access Control Service (ACS) névtereit, és kérjen egy bővítményt, hogy 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ff0ce05b13fea8409475e3415c5d810d7c79769a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154865"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Útmutató: A letiltott hozzáférés-vezérlési szolgáltatás névterei újraaktiválása

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

2017. november i bejelentése szerint a Microsoft Azure Access Control Service (ACS), az Azure Active Directory (Azure AD) szolgáltatása 2018.

Azóta e-maileket küldtünk az ACS-előfizetések rendszergazdai e-mailjéhez az ACS-nyugdíjról 12 hónap, 9 hónap, 6 hónap, 3 hónap, 1 hónap, 2 hét, 1 hét és 1 nappal a 2018.

2018. október 3-án bejelentettük (e-mailben és [blogbejegyzésben)](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)egy bővítményajánlatot azoknak az ügyfeleknek, akik nem tudják befejezni az áttelepítést 2018. november 7. A bejelentés hez a hosszabbítás kérelmezésére is utasítást adott.

## <a name="why-your-namespace-is-disabled"></a>Miért van letiltva a névtér?

Ha még nem engedélyezte a bővítményt, 2018. Már kérte a 2019. február 4-i hosszabbítást; ellenkező esetben nem fogja tudni engedélyezni a névtereket a PowerShellen keresztül.

> [!NOTE]
> A PowerShell-parancsok futtatásához és bővítmény kéréséhez az előfizetés szolgáltatás-rendszergazdájának vagy társrendszergazdájának kell lennie.

## <a name="find-and-enable-your-acs-namespaces"></a>Az ACS-névterek megkeresése és engedélyezése

Az ACS PowerShell segítségével felsorolhatja az összes ACS-névterét, és újraaktiválhatja a letiltottakat.

1. Az ACS PowerShell letöltése és telepítése:
    1. Nyissa meg a PowerShell-galériát, és töltse le [az Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Telepítse a modult:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Az összes lehetséges parancs listájának bekerülési száma:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Ha segítségre van szüksége egy adott parancshoz, futtassa a következő parancsot:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        hol `[Command-Name]` van az ACS parancs neve.
1. Csatlakozzon az ACS-hez a **Connect-AcsAccount** parancsmag használatával. 

    Előfordulhat, hogy a parancs futtatása előtt módosítania kell a végrehajtási házirendet a **Set-ExecutionPolicy** futtatásával.
1. Az elérhető Azure-előfizetések listája a **Get-AcsSubscription** parancsmag használatával.
1. Sorolja fel az ACS-névtereket a **Get-AcsNamespace** parancsmag használatával.
1. Ellenőrizze, hogy a névterek le `State` `Disabled`vannak-e tiltva a( névterek) megerősítésével.

    [![A névterek letiltásának ellenőrzése](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Azt is `nslookup {your-namespace}.accesscontrol.windows.net` ellenőrizheti, hogy a tartomány aktív-e még.

1. Engedélyezze az ACS-névtér(eke)t az **Enable-AcsNamespace** parancsmag használatával.

    Miután engedélyezte a névtér(ek)et, kérhet egy bővítményt, hogy a névtér(ek) ne legyenek újra letiltva 2019. február 4.-e előtt. Ezen időpont után az ACS-hez intézett összes kérés sikertelen lesz.

## <a name="request-an-extension"></a>Hosszabbítás kérése

2019. január 21-től új hosszabbítási kérelmeket fogadunk.

2019. február 4-re szóló kiterjesztést kérő ügyfelek számára megkezdjük a névterek letiltását. Továbbra is újra engedélyezheti a névtereket a PowerShellen keresztül, de a névterek 48 óra elteltével újra le lesznek tiltva.

2019. március 4-e után az ügyfelek a PowerShellen keresztül már nem engedélyezhetnek újra névtereket.

A további bővítmények et a továbbiakban nem hagyja jóvá automatikusan. Ha további időre van szüksége az áttelepítéshez, lépjen kapcsolatba az [Azure-támogatással](https://portal.azure.com/#create/Microsoft.Support) a részletes áttelepítési ütemterv biztosításához.

### <a name="to-request-an-extension"></a>Hosszabbítás kérése

1. Jelentkezzen be az Azure Portalra, és hozzon létre egy [új támogatási kérelmet.](https://portal.azure.com/#create/Microsoft.Support)
1. Töltse ki az új támogatási kérelem űrlapot az alábbi példában látható módon.

    | Támogatási kérelem mező | Érték |
    |-----------------------|--------------------|
    | **Probléma típusa** | `Technical` |
    | **Előfizetés** | Az előfizetés beállítása |
    | **Service** | `All services` |
    | **Erőforrás** | `General question/Resource not available` |
    | **Probléma típusa** | `ACS to SAS Migration` |
    | **Tárgy** | A probléma leírása |

   ![Példa egy új technikai támogatási kérelemre](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

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

- Ha az útmutató követése után bármilyen problémába ütközik, forduljon az [Azure ügyfélszolgálatához.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
- Ha kérdése vagy visszajelzése van az ACS nyugdíjba vonulásával kapcsolatban, lépjen kapcsolatba velünk a . acsfeedback@microsoft.com

## <a name="next-steps"></a>További lépések

- Tekintse át az ACS-megszüntetéssel kapcsolatos információkat [a Következő: Áttelepítés az Azure Access Control Service szolgáltatásból](active-directory-acs-migration.md)című részben.
