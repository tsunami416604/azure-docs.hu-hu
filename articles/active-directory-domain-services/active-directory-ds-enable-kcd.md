---
title: "Az Azure Active Directory tartományi szolgáltatások: A kerberos által korlátozott delegálás engedélyezése |} Microsoft Docs"
description: "Kerberos által korlátozott delegálás a felügyelt tartományok Azure Active Directory tartományi szolgáltatások engedélyezése"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0235944ef89cab7af152664651711edd5e80e632
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Kerberos által korlátozott delegálás (KCD) konfigurálása a felügyelt tartományhoz
Számos alkalmazás a felhasználó környezetében erőforrások eléréséhez szükséges. Az Active Directory támogatja-e a Kerberos-delegálás, amely lehetővé teszi a használati eset nevű. Delegálás további, korlátozhatja, hogy csak adott erőforrások elérhetők legyenek a felhasználó környezetében. Az Azure AD tartományi szolgáltatások felügyelt tartományok különböznek a hagyományos Active Directory-tartományok, mivel biztonságosabban zárolva miatt.

Ez a cikk bemutatja, hogyan kerberos által korlátozott delegálás konfigurálása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos által korlátozott delegálás (KCD)
Kerberos-delegálás lehetővé teszi, hogy a fiók megszemélyesíthet-e egy másik rendszerbiztonsági tag (például egy felhasználó) férhetnek hozzá az erőforrásokhoz. Fontolja meg egy webalkalmazást, aki hozzáfér a háttér-webes API-k a felhasználó környezetében. Ebben a példában a webes alkalmazás (szolgáltatásfiók vagy egy számítógép-vagy számítógépfiók környezetében fut) a felhasználót megszemélyesítő, amikor eléri az erőforrást (webes API). Kerberos-delegálás nem biztonságos, mivel nem korlátozza, hogy az erőforrások a megszemélyesítő fiók hozzáférhessen a felhasználó környezetében.

Kerberos által korlátozott delegálás (KCD) korlátozza a szolgáltatások/erőforrásokat, amelyhez a megadott kiszolgáló működhet-e a felhasználó nevében. Hagyományos Kerberos által korlátozott Delegálás szükséges egy tartományi fiók egy szolgáltatás konfigurálásához tartományi rendszergazdai jogosultságokkal, és korlátozza az a fiók egyetlen tartományra.

Hagyományos Kerberos által korlátozott Delegálás is van néhány olyan probléma, társítva. A korábbi operációs rendszerekben Ha a tartományi rendszergazda konfigurálta fiók alapú Kerberos által korlátozott Delegálás a szolgáltatás, akkor a szolgáltatás-rendszergazda nem tudta megállapítani, hogy melyik előtér-szolgáltatás tulajdonosa volt erőforrás-szolgáltatáshoz delegálva volna. És bármely előtér-szolgáltatás, amely az erőforrás-szolgáltatásnak delegálni sikerült potenciális támadási felület. Ha egy kiszolgálót, amely előtér-szolgáltatás tárolt biztonsági szempontból sérült, és erőforrás-szolgáltatáshoz delegálásának konfigurálása, az erőforrás-szolgáltatások is utaló jeleket.

> [!NOTE]
> Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, a nem tartományi rendszergazdai jogosultságokkal rendelkezik. Ezért **hagyományos Kerberos által korlátozott Delegálás nem konfigurálható egy felügyelt tartomány**. Erőforrás-alapú Kerberos által korlátozott Delegálás használata, ebben a cikkben leírtak szerint. Ez a módszer használata is biztonságosabb.
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>Erőforrás-alapú kerberos által korlátozott delegálás
Windows Server 2012 és újabb verziók esetében szolgáltatás-rendszergazdák ettől kezdve a szolgáltatás korlátozott delegálás konfigurálásához lehetőséget. Ebben a modellben a háttér-szolgáltatás-rendszergazda engedélyezheti vagy tagadhatja meg adott előtér-szolgáltatásokat a Kerberos által korlátozott Delegálás használatával. Ez a modell nevezik **erőforrás-alapú kerberos által korlátozott delegálás**.

Erőforrás-alapú Kerberos által korlátozott Delegálás konfigurálva van a PowerShell használatával. A Set-ADComputer vagy a Set-ADUser parancsmag attól függően, hogy a megszemélyesítő fiókot számítógépfiók vagy a felhasználói fiók vagy szolgáltatás-fiók használatával.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Erőforrás-alapú számítógép fiók Kerberos által korlátozott Delegálás konfigurálása a felügyelt tartományhoz
Tegyük fel, a webes alkalmazás a számítógépen futó "contoso100-webapp.contoso100.com". Az erőforrás eléréséhez szükséges (a futó webes API "contoso100-api.contoso100.com") a tartományi felhasználóknak a környezetben. Itt látható, hogyan állíthat be az erőforrás-alapú ebben a forgatókönyvben a Kerberos által korlátozott Delegálás.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Erőforrás-alapú felhasználói fiókok Kerberos által korlátozott Delegálás konfigurálása a felügyelt tartományhoz
Feltételezi, hogy a webes alkalmazás futtatásához használt szolgáltatásfiók "appsvc", és a tartományi felhasználók környezetében kell elérni az erőforrást (a webes API futtatásához használt szolgáltatásfiók - "backendsvc"). Itt látható, hogyan állíthat be az erőforrás-alapú ebben a forgatókönyvben a Kerberos által korlátozott Delegálás.

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Kerberos általi korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx)
