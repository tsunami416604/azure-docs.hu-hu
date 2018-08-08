---
title: Privileged Identity Management előfizetések – Azure |} A Microsoft Docs
description: Az előfizetés és a licencelési követelmények kezelésére, és a bérlő Azure AD Privileged Identity Management használatával
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2fe80f01faae89256c96e23944025d3bd0c55811
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617190"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Az Azure Active Directory Privileged Identity Management előfizetési követelmények

Az Azure AD Privileged Identity Management érhető el az Azure AD Premium P2 kiadásában részeként. P2, és hogyan viszonyul prémium P1 szintű, az egyéb funkciókról további információ: [Azure Active Directory-kiadások](../active-directory-editions.md).

>[!NOTE]
Az Azure Active Directory (Azure AD) Privileged Identity Management volt az előzetes verzióban érhető el, ha voltak próbálja ki a szolgáltatást a bérlő nincs licenc ellenőrzése.  Most, hogy az Azure AD Privileged Identity Management elérte az általános rendelkezésre állás, egy próbaverziós vagy fizetős előfizetésre szerepelnie kell a bérlő követően, 2016. December Privileged Identity Management használatának folytatásához.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Erősítse meg a próbaverziós vagy fizetős előfizetésre

Ha nem biztos abban, hogy a szervezet rendelkezik-e a próbaverzióra vagy vásárolt előfizetést, majd ellenőrizheti, hogy van-e a bérlő előfizetés tartalmazza a V1-ben az Azure Active Directory modul a Windows PowerShell-parancsok segítségével. 
1. Nyisson meg egy PowerShell-ablakot.
2. Adja meg `Connect-MsolService` a bérlő felhasználói hitelesítést.
3. Adja meg `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Ez a parancs lekéri a bérlő az előfizetések listáját. Ha nincs visszaadott sor, szüksége lesz egy Azure AD Premium P2 próbaverziójának, vásárlás beszerzése az Azure AD Premium P2 szintű előfizetésre vagy EMS E5 csomagra szóló előfizetés az Azure AD Privileged Identity Management használatához.  A próbaverzió és az Azure AD Privileged Identity Management használatának megkezdése, olvassa el [Ismerkedés az Azure AD Privileged Identity Management](pim-getting-started.md).

Ha ez a parancs visszaadja a sor melyik SkuPartNumber "AAD_PREMIUM_P2" vagy "EMSPREMIUM" és a IsTrial értéke "True", ez azt jelzi, hogy az Azure AD Premium P2 próbaverziójára megtalálható a bérlőben.  Ha nincs engedélyezve az előfizetési állapotot, és nem kell vásárolni az Azure AD Premium P2 vagy az EMS E5 előfizetéssel, majd meg kell vásárolnia egy Azure AD Premium P2 szintű előfizetésre, vagy az EMS E5 csomagra szóló előfizetés az Azure AD Privileged Identity Management használatának folytatásához.

Az Azure AD Premium P2 keresztül érhető el egy [Microsoft nagyvállalati szerződés](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), a [Open mennyiségi licencprogramok](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx), és a [Cloud Solution Providers program](https://partner.microsoft.com/en-US/cloud-solution-provider). Az Azure és Office 365-előfizetők online az Azure AD Premium P2 szolgáltatást is megvásárolhatják.  További információ a prémium szintű Azure AD díjszabási és online rendezésének módját található [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Az Azure AD Privileged Identity Management bérlő nem érhető el

Az Azure AD Privileged Identity Management már nem érhető el, a bérlőben ha:
- A szervezet Azure AD Privileged Identity Management használta, előzetes verzióként állt, és vásárolja meg az Azure AD Premium P2 szintű előfizetésre, vagy az EMS E5 csomagra szóló előfizetés.
- A szervezet korábban egy Azure AD Premium P2 vagy az EMS E5 próbaverziójának lejárt.
- A szervezet korábban megvásárolt előfizetés lejárt.

Ha egy Azure AD Premium P2 szintű előfizetésre, vagy az EMS E5 csomagra szóló előfizetés lejár, vagy egy szervezet, amely előzetes verzióban érhető el az Azure AD Privileged Identity Management használta nem szerezhető be az Azure AD Premium P2- vagy EMS E5 előfizetéssel:

- Az Azure AD-szerepkörök állandó szerepkör-hozzárendelések nem érinti.
- Az Azure Portalon, valamint a Graph API-parancsmagok és az Azure AD Privileged Identity Management PowerShell-felületek az Azure AD Privileged Identity Management bővítmény már nem emelt szintű szerepkörök aktiválása, illetve kezelheti a kiemelt felhasználók eléréséhez, vagy a hozzáférési felülvizsgálatok a kiemelt szerepkörök végrehajtásához.
- Az Azure AD-szerepkörök jogosult szerepkör-hozzárendelés lesz eltávolítva, ahogy a felhasználók többé nem fognak tudni emelt szintű szerepkörök aktiválása.
- Minden folyamatban lévő hozzáférési felülvizsgálatok az Azure AD-szerepkörök megszűnik, és az Azure AD Privileged Identity Management konfigurációs beállítások törlődnek.
- Az Azure AD Privileged Identity Management a szerepkör-hozzárendelési módosítások már nem küld e-maileket.

## <a name="next-steps"></a>További lépések

- [Az Azure AD Privileged Identity Management – első lépések](pim-getting-started.md)
- [Szerepkörök az Azure AD Privileged Identity Management](pim-roles.md)
