---
title: Alapvető házirendet service management (előzetes verzió) – az Azure Active Directory MFA megkövetelése
description: Feltételes hozzáférési szabályzatot a többtényezős hitelesítés az Azure Resource Manager
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
ms.openlocfilehash: 203b752f9da67ebf60e373fe7ce0893b4fd7fcb5
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560965"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Alapvető házirendet: Többtényezős hitelesítés a service management (előzetes verzió)

Előfordulhat, hogy használni az Azure-szolgáltatások különböző a szervezetben. Ezek a szolgáltatások kezelhetők az Azure Resource Manager API-n keresztül:

* Azure Portal
* Azure PowerShell
* Azure CLI

Azure Resource Manager használatával kezelheti a szolgáltatásokat a magas jogosultsági szintű műveletet. Az Azure Resource Manager az összes bérlőre vonatkozó konfiguráció, például a szolgáltatás beállításainak és az előfizetés számlázási is módosítható. Egyetlen többtényezős hitelesítés kitéve, számos különböző támadásoktól, például az adathalászatot vagy a jelszó megfelelő. Ezért fontos térni az Azure Resource Manager elérése és módosítása a konfigurációk, azzal, hogy a multi-factor Authentication hitelesítést, mielőtt engedélyezi a hozzáférést a felhasználók identitásának ellenőrzéséhez.

**Többtényezős hitelesítés a service management** van egy [alapvető házirendet](concept-baseline-protection.md) elérése az Azure Portalon, az Azure PowerShell vagy az Azure CLI bármely felhasználó többtényezős hitelesítés szükséges, amely. Ez a szabályzat minden felhasználóra elérése az Azure Resource Manager, függetlenül attól, ha egy rendszergazda vonatkozik.

Után ez a szabályzat engedélyezve van a bérlő, akkor minden felhasználó bejelentkezik az Azure felügyeleti erőforrásokhoz és a többtényezős hitelesítés merül fel. Ha a felhasználó MFA-kiszolgáló nincs regisztrálva, a felhasználó kell regisztrálni a Microsoft Authenticator alkalmazás használatával a folytatáshoz.

Interaktív bejelentkezés segítségével végrehajtásához [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/authenticate-azureps), használja a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmagot.

```PowerShell
Connect-AzAccount
```

A futtatáskor ez a parancsmag jogkivonatsztringet jelenít meg. Jelentkezzen be, másolja ezt a karakterláncot, és illessze be azt [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  egy böngészőben. A rendszer ekkor hitelesíti a PowerShell-munkamenetet az Azure-hoz való csatlakozáshoz.

Interaktív bejelentkezés segítségével végrehajtásához [Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)futtassa a [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben kell egy böngésző lap megnyitásához, és kövesse az utasításokat a parancssorban adja meg a hozzáférési kód után ellenőrizheti, hogy a [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) a böngészőben. Ezt követően jelentkezzen be a fiók hitelesítő adatait a böngészőben.

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel a **többtényezős hitelesítés megkövetelése Szolgáltatáskezelési** minden Azure Resource Manager-felhasználóra vonatkozik szabályzat, több szempontok lesz szükség ahhoz, hogy egy zökkenőmentes üzembe helyezési. Ezeket a szempontok közé tartoznak, felhasználók és az Azure AD nem tudja vagy nem kell végrehajtani, az MFA, valamint az alkalmazások és az ügyfelek a szervezet által használt, modern hitelesítést nem támogató szolgáltatás alapelvek azonosító.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció-házirend engedélyezése

A szabályzat **alapvető házirendet: Többtényezős hitelesítés a service management (előzetes verzió)** előre konfigurálva, és jelennek meg az oldal tetején a feltételes hozzáférés paneljén, az Azure Portalon lépve.

A szabályzat engedélyezéséhez, és a rendszergazdák védelmére:

1. Jelentkezzen be a **az Azure portal** globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.
1. Keresse meg a **az Azure Active Directory** > **feltételes hozzáférési**.
1. A házirendek listájából válassza ki **alapvető házirendet: Többtényezős hitelesítés a service management (előzetes verzió)** .
1. Állítsa be **házirend engedélyezése** való **a házirend azonnal használható**.
1. Kattintson a **mentése**.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Feltételes hozzáférés alapkonfiguráció alkalmazásvédelmi szabályzatok](concept-baseline-protection.md)
* [Öt lépést a személyazonosság-infrastruktúra védelme](../../security/azure-ad-secure-steps.md)
* [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)