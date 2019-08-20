---
title: 'Azure Active Directory Domain Services: Kerberos által korlátozott delegálás engedélyezése | Microsoft Docs'
description: Kerberos által korlátozott delegálás engedélyezése Azure Active Directory Domain Services felügyelt tartományokon
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612930"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Kerberos által korlátozott delegálás (KCD) konfigurálása felügyelt tartományon
Számos alkalmazásnak hozzá kell férnie az erőforrásokhoz a felhasználó környezetében. Active Directory támogatja a Kerberos-delegálás nevű mechanizmust, amely engedélyezi ezt a használati esetet. A delegálást úgy is korlátozhatja, hogy csak bizonyos erőforrások férhessenek hozzá a felhasználó környezetében. Azure AD Domain Services felügyelt tartományok különböznek a hagyományos Active Directory tartománytól, mivel azok biztonságosabban zárolva vannak.

Ez a cikk bemutatja, hogyan konfigurálhatja a Kerberos által korlátozott delegálást egy Azure AD Domain Services felügyelt tartományon.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos által korlátozott delegálás (KCD)
A Kerberos-delegálás lehetővé teszi, hogy egy fiók megszemélyesítse egy másik rendszerbiztonsági tag (például egy felhasználó) számára az erőforrásokhoz való hozzáférést. Vegye fontolóra egy olyan webalkalmazást, amely egy felhasználó kontextusában fér hozzá a háttérbeli webes API-hoz. Ebben a példában a webalkalmazás (egy szolgáltatásfiók vagy egy számítógép/számítógép fiók kontextusában fut) megszemélyesíti a felhasználót az erőforráshoz való hozzáféréskor (háttérbeli webes API). A Kerberos-delegálás nem biztonságos, mert nem korlátozza azokat az erőforrásokat, amelyeket a megszemélyesítő fiók a felhasználó kontextusában elérhet.

A Kerberos által korlátozott delegálás (KCD) korlátozza azokat a szolgáltatásokat/erőforrásokat, amelyekre a megadott kiszolgáló a felhasználó nevében működhet. A hagyományos KCD tartományi rendszergazdai jogosultságok szükségesek a szolgáltatás tartományi fiókjának konfigurálásához, és a fiókot egyetlen tartományra korlátozza.

A hagyományos KCD is van néhány probléma társítva. A korábbi operációs rendszerekben, ha a tartományi rendszergazda konfigurálta a szolgáltatás fiók alapú KCD, a szolgáltatás rendszergazdája nem tudta megismerni, hogy mely előtér-szolgáltatások lettek delegálva az általuk birtokolt erőforrás-szolgáltatásokhoz. Továbbá minden olyan előtér-szolgáltatás, amely egy erőforrás-szolgáltatásnak delegálható, potenciális támadási pontot jelentett. Ha egy előtér-szolgáltatást futtató kiszolgáló biztonsága sérült, és az erőforrás-szolgáltatásoknak való delegálásra lett konfigurálva, az erőforrás-szolgáltatásokat is veszélyeztetheti.

> [!NOTE]
> Azure AD Domain Services felügyelt tartományon nem rendelkezik tartományi rendszergazdai jogosultságokkal. Ezért a **hagyományos, fiók alapú KCD nem konfigurálható felügyelt tartományon**. Használjon erőforrás-alapú KCD a jelen cikkben leírtak szerint. Ez a mechanizmus még biztonságosabb.
>
>

## <a name="resource-based-kcd"></a>Erőforrás-alapú KCD
A Windows Server 2012-től kezdve a szolgáltatás-rendszergazdák lehetővé tehetik a korlátozott delegálás konfigurálását a szolgáltatáshoz. Ebben a modellben a háttér-szolgáltatás rendszergazdája engedélyezheti vagy megtagadhatja az egyes előtér-szolgáltatásokat a KCD használatával. Ez a modell **erőforrás-alapú KCD**néven ismert.

Az erőforrás-alapú KCD a PowerShell használatával van konfigurálva. A vagy `Set-ADComputer` `Set-ADUser` a parancsmagot használja attól függően, hogy a megszemélyesítési fiók egy számítógépfiók vagy egy felhasználói fiók/szolgáltatásfiók-e.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Erőforrás-alapú KCD konfigurálása felügyelt tartományhoz tartozó számítógépfiók esetén
Tegyük fel, hogy van egy webalkalmazása, amely a "contoso-webapp.contoso.com" számítógépen fut. Hozzá kell férnie az erőforráshoz (a "contoso-api.contoso.com"-on futó webes API-t) a tartományi felhasználók környezetében. Ehhez a forgatókönyvhöz az alábbi módon hozhat létre erőforrás-alapú KCD:

1. [Hozzon létre egy egyéni szervezeti egységet](create-ou.md). Az egyéni szervezeti egység felügyelt tartományba tartozó felhasználók számára történő felügyeletéhez engedélyeket delegálhat.
2. Csatlakozzon mindkét virtuális géphez (amely a webalkalmazást és a webes API-t futtatja) a felügyelt tartományhoz. Hozza létre ezeket a számítógép-fiókokat az egyéni szervezeti egységen belül.
3. Most konfigurálja az erőforrás-alapú KCD a következő PowerShell-parancs használatával:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> A webalkalmazás és a webes API számítógépfiókja olyan egyéni szervezeti egységben kell, hogy rendelkezzen az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel. A beépített "HRE DC Computers" tárolóban nem lehet beállítani erőforrás-alapú KCD a számítógépfiók számára.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Erőforrás-alapú KCD konfigurálása felügyelt tartományhoz tartozó felhasználói fiókhoz
Tegyük fel, hogy rendelkezik egy "appsvc" szolgáltatásfiókot futtató webalkalmazással, és a tartományi felhasználók környezetében hozzá kell férnie az erőforráshoz (egy "backendsvc" nevű webes API-t). Ebből a forgatókönyvből megtudhatja, hogyan állíthat be erőforrás-alapú KCD.

1. [Hozzon létre egy egyéni szervezeti egységet](create-ou.md). Az egyéni szervezeti egység felügyelt tartományba tartozó felhasználók számára történő felügyeletéhez engedélyeket delegálhat.
2. Csatlakoztassa a háttérbeli webes API/erőforrást futtató virtuális gépet a felügyelt tartományhoz. Hozza létre a számítógép-fiókját az egyéni szervezeti egységen belül.
3. Hozza létre a webalkalmazás egyéni szervezeti egységen belüli futtatásához használt szolgáltatásfiókot (például "appsvc").
4. Most konfigurálja az erőforrás-alapú KCD a következő PowerShell-parancs használatával:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> A háttérbeli webes API-nak és a szolgáltatásfiók számítógép-fiókjának is olyan egyéni szervezeti egységben kell lennie, amelyben az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel rendelkezik. Az erőforrás-alapú KCD nem konfigurálható a beépített "HRE DC Computers" tárolóban, illetve a beépített "HRE DC Users" tárolóban lévő felhasználói fiókokhoz. Így az Azure AD-ből szinkronizált felhasználói fiókok nem használhatók erőforrás-alapú KCD beállításához.
>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](tutorial-create-instance.md)
* [Kerberos által korlátozott delegálás – áttekintés](https://technet.microsoft.com/library/jj553400.aspx)
