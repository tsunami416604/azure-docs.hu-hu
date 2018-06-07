---
title: Privileged Identity Management előfizetések - Azure |} Microsoft Docs
description: Az előfizetés és licencelési követelmények kezelésére, és a bérlő az Azure AD Privileged Identity Management használatát ismerteti
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9e49ddb8fca9ce193c92f27c307942d5f3d3d2fd
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807992"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Az Azure Active Directory Privileged Identity Management előfizetés követelményeinek

Az Azure AD Privileged Identity Management érhető el az Azure AD Premium P2 kiadása részeként. Funkciókról további információkat a más P2 és összehasonlítja azt az Premium P1, lásd: [Azure Active Directory-kiadások](../active-directory-editions.md).

>[!NOTE]
Ha az Azure Active Directory (Azure AD) Privileged Identity Management preview volt, volt nem licenc ellenőrzi a szolgáltatás kipróbálásához bérlőhöz.  Most, hogy az Azure AD Privileged Identity Management elérte az általánosan rendelkezésre álló, egy próbaverziós vagy fizetős előfizetésre továbbra is használja a Privileged Identity Management 2016. December után a bérlői jelen kell lennie.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Erősítse meg a próbaverziós vagy fizetős előfizetésre

Ha nem biztos abban, hogy a szervezet rendelkezik-e a próbaverziójának vagy megvásárolt előfizetést, majd ellenőrizheti hogy van-e a bérlő előfizetés az Azure Active Directory modul a Windows PowerShell V1 szereplő parancsok használatával. 
1. Nyisson meg egy PowerShell-ablakot.
2. Adja meg `Connect-MsolService` hitelesítsék magukat, a felhasználó az Ön bérelt szolgáltatásának.
3. Adja meg `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

A parancs segítségével lekérdezhető az Ön bérelt szolgáltatásának előfizetések listáját. Ha nincsenek visszaadott sorok, szüksége lesz egy Azure AD Premium P2 kipróbálásra, megvásárlásra beszerzése egy Azure AD Premium P2 előfizetés vagy az Azure AD Privileged Identity Management EMS E5 előfizetéssel.  A próbaidőszak, és a kezdő Azure AD Privileged Identity Management használatával, olvassa el [Ismerkedés az Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Ha ez a parancs visszaadja a sor mely SkuPartNumber "AAD_PREMIUM_P2" vagy "EMSPREMIUM" és IsTrial értéke "True", ez azt jelzi, hogy az Azure AD Premium P2 próbaverziójával megtalálható-e a bérlő.  Ha nincs engedélyezve az előfizetési állapotot, és Ön nem rendelkezik egy Azure AD Premium P2- vagy EMS E5 előfizetés vásárlása, majd meg kell vásárolnia egy Azure AD Premium P2 előfizetés vagy az EMS E5 előfizetés továbbra is használja az Azure AD Privileged Identity Management.

Az Azure AD Premium P2 keresztül érhető el egy [Microsoft nagyvállalati szerződés](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), a [nyissa meg a mennyiségi licencelési programon](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx), és a [megoldás szolgáltatók program](https://partner.microsoft.com/en-US/cloud-solution-provider). Office 365 és az Azure-előfizetők vásárolhatja online az Azure AD Premium P2.  További információ a Azure AD Premium árképzési és hogyan rendelhető online helyen találhatók [Azure Active Directory árképzési](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Az Azure AD Privileged Identity Management a bérlő nem érhető el

Az Azure AD Privileged Identity Management már nem lesz elérhető az Ön bérelt szolgáltatásának ha:
- A szervezet Azure AD Privileged Identity Management használt Preview történt és nem vásárol Azure AD Premium P2-előfizetés vagy az EMS E5 előfizetés esetén.
- A szervezet kellett az Azure AD Premium P2 vagy az EMS E5 próbaverzió lejárt.
- A szervezet kellett a megvásárolt előfizetése lejárt.

Ha az Azure AD Premium P2 előfizetés vagy az EMS E5 előfizetés lejár, vagy egy olyan szervezet, amely a képen használta az Azure AD Privileged Identity Management nem kap Azure AD Premium P2- vagy EMS E5 előfizetés:

- Az Azure AD-szerepkörök állandó szerepkör-hozzárendelések nem érinti.
- Az Azure-portálon, valamint a Graph API-parancsmagok és az Azure AD Privileged Identity Management PowerShell felületei az Azure AD Privileged Identity Management bővítmény már nem lesz a felhasználók aktiválása kiemelt szerepköröket, emelt szintű hozzáférések kezelése vagy hajtsa végre a hozzáférési felülvizsgálatát kiemelt szerepköröket.
- Az Azure AD-szerepkörök jogosult szerepkör hozzárendelése el lesz távolítva, felhasználók nem lesznek többé aktiválható kiemelt szerepköröket.
- Minden folyamatban lévő hozzáférés felülvizsgálatra az Azure AD-szerepkörök véget ér, és az Azure AD Privileged Identity Management konfigurációs beállításait.
- Az Azure AD Privileged Identity Management a szerepkör-hozzárendelési módosítások már nem küld e-maileket.

## <a name="next-steps"></a>További lépések

- [Ismerkedés az Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Szerepkörök az Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
