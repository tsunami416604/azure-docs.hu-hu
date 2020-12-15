---
title: A jogkivonat élettartamának alapértelmezett értékeinek módosítása egyéni Azure AD-alkalmazásokhoz
description: Az Azure AD-ben fejleszthető, az alkalmazásra vonatkozó jogkivonat élettartamára vonatkozó szabályzatok frissítése
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 90b4468693736068bd54b62082c43a1988202e6f
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509104"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonat élettartamának alapértelmezett értékeinek módosítása egyéni fejlesztésű alkalmazásokhoz

Ez a cikk bemutatja, hogyan használható az Azure AD PowerShell a jogkivonat-élettartam szabályzatának beállításához. Prémium szintű Azure AD lehetővé teszi az alkalmazások fejlesztői és bérlői rendszergazdái számára a nem bizalmas ügyfelek számára kiállított jogkivonatok élettartamának konfigurálását. A jogkivonat élettartamára vonatkozó szabályzatok a bérlőre érvényesek, vagy az elérni kívánt erőforrások.

> [!IMPORTANT]
> Május 2020. után a bérlők többé nem tudják konfigurálni a frissítési és a munkamenet-jogkivonat élettartamát.  A Azure Active Directory a házirendekben a meglévő frissítési és munkamenet-jogkivonat konfigurációját a 2021. január 30. után nem fogja megbecsülni. A hozzáférési token élettartamát továbbra is beállíthatja az elavulás után. További információért olvassa el [a konfigurálható jogkivonat élettartamait az Azure ad-ben](./active-directory-configurable-token-lifetimes.md).
> Végrehajtotta [](../conditional-access/howto-conditional-access-session-lifetime.md)   Az Azure ad feltételes hozzáférésének hitelesítési munkamenet-kezelési képességeit. Ezt az új funkciót használhatja a frissítési jogkivonat élettartamának konfigurálásához a bejelentkezési gyakoriság beállításával.  

A jogkivonat-élettartam szabályzatának beállításához le kell töltenie az [Azure ad PowerShell-modult](https://www.powershellgallery.com/packages/AzureADPreview).
Futtassa a **kapcsolat-AzureAD-Confirm** parancsot.

Az alábbi példa olyan házirendet igényel, amely megköveteli, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban. Ezzel a szabályzattal állítható be a hozzáférési/azonosító tokenek élettartama, valamint a többtényezős munkamenet-tokenek maximális kora a webalkalmazás egyszerű szolgáltatásnév számára. Hozza létre a szabályzatot, és rendelje hozzá az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév ObjectId is le kell kérnie.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>További lépések

* Tekintse meg a [konfigurálható jogkivonat élettartamait az Azure ad-ben](./active-directory-configurable-token-lifetimes.md) , amelyből megtudhatja, hogyan konfigurálhatja az Azure ad által kiadott jogkivonat-élettartamokat, beleértve a jogkivonat-élettartamok beállítását a szervezet összes alkalmazásához, egy több-bérlős alkalmazáshoz vagy a szervezet egy adott egyszerű szolgáltatásához. 
* [Azure AD-jogkivonat – dokumentáció](./id-tokens.md)