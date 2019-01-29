---
title: 'Az Azure Active Directory tartományi szolgáltatások: Kerberos által korlátozott delegálás engedélyezése |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services felügyelt tartományokban a kerberos által korlátozott delegálás engedélyezése
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 5569344a2df560036b99dea40c466302f5e6fe4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159274"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Kerberos által korlátozott delegálás (KCD) konfigurálása felügyelt tartományhoz
Számos alkalmazás erőforrásokhoz kell hozzáférniük a felhasználó kontextusában. Az Active Directory támogatja a Kerberos-delegálás, amely lehetővé teszi a használatieset-nevű mechanizmus. Delegálás további, korlátozhatja, hogy csak meghatározott erőforrások elérhetők legyenek a felhasználó kontextusában. Az Azure AD Domain Services felügyelt tartomány eltérnek a hagyományos Active Directory-tartományokban, mivel biztonságosabban zárolt üzemmódban.

Ez a cikk bemutatja, hogyan Kerberos által korlátozott delegálás konfigurálása az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>A Kerberos által korlátozott delegálás (KCD)
Kerberos-delegálás lehetővé teszi, hogy az erőforrások eléréséhez (például egy felhasználó) egy másik rendszerbiztonsági tag megszemélyesítendő fiók. Fontolja meg egy webalkalmazást, amely hozzáfér a háttér-webes API-k a felhasználó kontextusában. Ebben a példában a webalkalmazás (szolgáltatásfiók vagy egy számítógép-vagy számítógépfiók környezetében fut) a felhasználó megszemélyesít, amikor eléri az erőforrást (webes API-t). Kerberos-delegálás nem biztonságos, mivel ez nem korlátozza az erőforrások a megszemélyesítő fiók hozzáférhessen a felhasználó kontextusában.

Kerberos által korlátozott delegálás (KCD) korlátozza a szolgáltatások és erőforrások, amelyhez az adott kiszolgáló műveleteket hajthat végre a felhasználó nevében. Hagyományos kcd Szolgáltatáshoz szükséges egy tartományi fiók egy szolgáltatás konfigurálásához tartományi rendszergazdai jogosultságokkal, és korlátozza a fiók egyetlen tartományra.

Hagyományos KCD is van társítva, néhány olyan probléma. A korábbi operációs rendszerekben a tartományi rendszergazda konfigurálta a fiók-alapú KCD szolgáltatáshoz, ha a szolgáltatás-rendszergazda kellett nem hasznos lehet tudni, hogy melyik előtér-szolgáltatás tulajdonosa volt az erőforrás-szolgáltatáshoz delegálva. És minden olyan előtér-szolgáltatás, amely az erőforrás-szolgáltatásnak delegálni tudott egy potenciális támadási pont jelöli. Ha egy kiszolgálót üzemeltető előtér-szolgáltatás feltörték, és azt történő erőforrás-szolgáltatáshoz történő delegálásra volt konfigurálva, az erőforrás-szolgáltatások is sérülhet.

> [!NOTE]
> Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, az nem kell tartományi rendszergazdai jogosultságokkal. Ezért **egy felügyelt tartományon nem lehet konfigurálni a hagyományos ügyfélalapú KCD**. Használja az erőforrás-alapú KCD, ebben a cikkben leírtak szerint. Ezt a mechanizmust is használata biztonságosabb.
>
>

## <a name="resource-based-kcd"></a>Erőforrás-alapú kcd Szolgáltatáshoz
A Windows Server 2012 és újabb verziók szolgáltatás-rendszergazdák nálunk növelheti szolgáltatásának teljesítményét a korlátozott delegálás konfigurálásához. Ebben a modellben a háttérszolgáltatás-rendszergazda engedélyezheti vagy tagadhatja meg adott előtér-szolgáltatás használatával a kcd Szolgáltatáshoz. Ez a modell más néven **erőforrás-alapú KCD**.

Erőforrás-alapú kcd Szolgáltatáshoz van konfigurálva a PowerShell használatával. Használja a `Set-ADComputer` vagy `Set-ADUser` parancsmagok, attól függően, hogy a megszemélyesítő fiókot egy fiókkal vagy egy felhasználói fiók vagy szolgáltatás-fiókot.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Erőforrás-alapú kcd Szolgáltatáshoz tartozó számítógépfiók konfigurálhatja egy felügyelt tartományon
Fel, hogy a számítógépen futó webalkalmazás "contoso100-webapp.contoso100.com". Az erőforrás eléréséhez szükséges (futó webes API "contoso100-api.contoso100.com") tartományi felhasználók környezetében. Itt látható, hogyan állíthat be erőforrás-alapú KCD ebben a forgatókönyvben:

1. [Hozzon létre egy egyéni szervezeti](active-directory-ds-admin-guide-create-ou.md). Delegálhatja az egyéni szervezeti Egységet a felügyelt tartományhoz tartozó felhasználó engedélyeit.
2. A felügyelt tartományhoz csatlakozzon a virtuális gépeket is (a webalkalmazás futó és a webes API futó). Ezek az egyéni szervezeti Egységben lévő számítógép fiókokat létrehozni.
3. Most konfigurálja az erőforrás-alapú kcd Szolgáltatáshoz a következő PowerShell-paranccsal:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> A számítógépfiókok, a web app és a webes API-t kell egy egyéni szervezeti egységben, ahol Ön jogosult erőforrás-alapú Kerberos konfigurálása. Erőforrás-alapú KCD a számítógépfiók számára a beépített "AAD DC számítógépek" tároló nem lehet konfigurálni.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Erőforrás-alapú KCD egy felhasználói fiók konfigurálása a felügyelt tartományon
Fel, hogy egy webalkalmazást, amely szolgáltatásfiókként "appsvc" fut, és a tartományi felhasználók kontextusában kell elérni az erőforrást (egy webes API szolgáltatásfiók - "backendsvc" néven fut). Itt látható, hogyan állíthat be erőforrás-alapú KCD ehhez a forgatókönyvhöz.

1. [Hozzon létre egy egyéni szervezeti](active-directory-ds-admin-guide-create-ou.md). Delegálhatja az egyéni szervezeti Egységet a felügyelt tartományhoz tartozó felhasználó engedélyeit.
2. A virtuális gép fut, a háttéralkalmazás webes API-erőforrás a felügyelt tartományhoz csatlakozzon. Hozzon létre saját számítógépfiókjukat az egyéni szervezeti belül.
3. Hozzon létre az egyéni szervezeti belül a webalkalmazás futtatásához használt szolgáltatásfiók (például appsvc).
4. Most konfigurálja az erőforrás-alapú kcd Szolgáltatáshoz a következő PowerShell-paranccsal:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> A háttéralkalmazás webes API a fiókját és a szolgáltatás fiók kell lennie egy egyéni szervezeti egységben, ahol Ön jogosult erőforrás-alapú Kerberos konfigurálása. Erőforrás-alapú KCD a beépített "AAD DC számítógépek" tárolóban számítógépfiók vagy a felhasználói fiókokat a beépített "AAD DC-felhasználók" tároló nem lehet konfigurálni. Az Azure ad-ből szinkronizált felhasználói fiókok, így nem használható erőforrás-alapú KCD beállítása.
>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Kerberos általi korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx)
