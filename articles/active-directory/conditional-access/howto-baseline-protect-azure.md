---
title: Az alapházirend megköveteli az MFA használatát a Service Management szolgáltatásban (előzetes verzió) – Azure Active Directory
description: Feltételes hozzáférési szabályzat az MFA megköveteléséhez Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab2aa4415345747a0e87b90ef0a7ee770ef3465
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608121"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Alapterv házirend: MFA megkövetelése a Service Management szolgáltatáshoz (előzetes verzió)

Lehetséges, hogy különböző Azure-szolgáltatásokat használ a szervezetben. Ezeket a szolgáltatásokat Azure Resource Manager API-val lehet felügyelni:

* Azure Portal
* Azure PowerShell
* Azure CLI

A Azure Resource Manager használata a szolgáltatások kezeléséhez magas jogosultsági szintű művelet. Azure Resource Manager megváltoztathatja a bérlői szintű konfigurációkat, például a szolgáltatás beállításait és az előfizetés számlázását. Az egytényezős hitelesítés számos különböző támadáshoz, például az adathalászathoz és a jelszó-permetezéshez van kitéve. Ezért fontos annak ellenőrzése, hogy a hozzáférés engedélyezése előtt a többtényezős hitelesítés megkövetelése érdekében a felhasználók identitását szeretné-e elérni a Azure Resource Manager és a frissítési konfigurációkhoz.

Az MFA megkövetelése a **Service Management szolgáltatáshoz** olyan alapkonfigurációs szabályzat [, amely a](concept-baseline-protection.md) Azure Portal, Azure POWERSHELL vagy az Azure CLI-t elérő felhasználók számára megköveteli az MFA használatát. Ez a szabályzat minden olyan felhasználóra vonatkozik, amely a Azure Resource Managerhoz fér, függetlenül attól, hogy rendszergazda-e.

Ha ez a szabályzat engedélyezve van a bérlőben, az Azure felügyeleti erőforrásaiba bejelentkező összes felhasználó a multi-Factor Authentication szolgáltatással lesz feltámadva. Ha a felhasználó nincs regisztrálva az MFA-ban, akkor a folytatáshoz a felhasználónak regisztrálnia kell a Microsoft Authenticator alkalmazás használatával.

Ha az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)használatával szeretne interaktív bejelentkezést végezni, használja a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmagot.

```PowerShell
Connect-AzAccount
```

A futtatáskor ez a parancsmag jogkivonatsztringet jelenít meg. A bejelentkezéshez másolja ezt a karakterláncot, és illessze [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  be egy böngészőbe. A rendszer ekkor hitelesíti a PowerShell-munkamenetet az Azure-hoz való csatlakozáshoz.

Ha az [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)használatával szeretne interaktív bejelentkezést végezni, futtassa az az [login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőablakot, és a parancssor utasításait követve meg kell adnia egy engedélyezési kódot a böngészőben való [https://aka.ms/devicelogin](https://aka.ms/devicelogin) navigálás után. Ezt követően jelentkezzen be a fiókja hitelesítő adataival a böngészőben.

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel az **MFA megkövetelése a Service Management** -szabályzathoz az összes Azure Resource Manager felhasználóra vonatkozik, a zökkenőmentes üzembe helyezés érdekében számos szempontot kell figyelembe venni. Ezen megfontolások közé tartozik az Azure AD azon felhasználói és szolgáltatási alapelveinek azonosítása, amelyeken nem végezhető el az MFA, valamint a szervezet által használt, a modern hitelesítést nem támogató alkalmazások és ügyfelek.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció házirend engedélyezése

A házirend **alapkonfigurációjának házirendje: Az MFA megkövetelése a Service Management** szolgáltatásban (előzetes verzió) előre konfigurálva van, és a felső részen jelenik meg, amikor a Azure Portal a feltételes hozzáférés panelre lép.

A szabályzat engedélyezése és a rendszergazdák általi védelem:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. A szabályzatok listájában válassza ki **az alapházirend elemet: MFA megkövetelése a Service Management (** előzetes verzió) szolgáltatáshoz.
1. Állítsa be a házirend **engedélyezése** beállítást a **szabályzat azonnali használatára**.
1. Kattintson a **Mentés**gombra.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [A feltételes hozzáférés alapkonfigurációjának védelmi házirendjei](concept-baseline-protection.md)
* [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md)
* [Mi a feltételes hozzáférés a Azure Active Directory?](overview.md)