---
title: A számlázási fiók Azure Portalon való megtekintésével kapcsolatos hibák elhárítása
description: Ez a cikk segítséget nyújt a számlázási fióknak az Azure Portalon való megtekintése során felmerülő problémák elhárításában.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f701e41c62336bcd7638360a27a0fb4c3ce3ec7d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686666"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>A számlázási fiók Azure Portalon való megtekintésével kapcsolatos hibák elhárítása

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy személyes használat céljából regisztrált az Azure-ba. Emellett az Azure-hoz hozzáféréssel rendelkezhet a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik regisztrációhoz egy külön számlázási fiók tartozik. Ez a cikk segítséget nyújt a számlázási fióknak az Azure Portalon való megtekintése során felmerülő problémák elhárításában.

A számlázási fiókot az [Azure Cost Management + Számlázás](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) oldalon tekintheti meg.

A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókoknak az Azure Portalon történő megtekintését](view-all-accounts.md) ismertető cikket.

Ha nem tudja megtekinteni a számlázási fiókot az Azure Portalon, próbálkozzon az alábbiakkal:

## <a name="sign-in-to-a-different-tenant"></a>Jelentkezzen be egy másik bérlőbe

A számlázási fiók egy Azure Active Directory-bérlőhöz van társítva. Ha nem a megfelelő bérlőbe jelentkezik be, a Cost Management + Számlázás oldalon nem jelenik meg a számlázási fiókja. A következő lépésekkel váltson egy másik bérlőre az Azure Portalon, és tekintse meg a számlázási fiókjait ebben a bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A lap jobb felső sarkában válassza ki a profilját (e-mail-címét).
1. Válassza a **Címtár váltása** lehetőséget.  
    ![Képernyőkép a Címtár váltása lehetőség kiválasztásáról a portálon](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Válasszon ki egy címtárat a **Minden címtár** szakaszban.  
    ![Képernyőkép egy címtár kiválasztásáról a portálon](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Bejelentkezés másik e-mail-címmel

Néhány felhasználó több e-mail-címet használ az [Azure Portalra](https://portal.azure.com) való bejelentkezéshez. Nem minden e-mail-cím rendelkezik hozzáféréssel a számlázási fiókhoz. Ha olyan e-mail-címmel jelentkezik be, amely engedéllyel rendelkezik az erőforrások kezeléséhez, de a számlázási fiók megtekintésére nincs engedélye, akkor az Azure Portal [Cost Management + Számlázás](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) oldalán nem jelenik meg a számlázási fiók.

A számlázási fiók eléréséhez jelentkezzen be az Azure Portalra egy olyan e-mail-címmel, amely rendelkezik engedéllyel a számlázási fiók megtekintéséhez.

## <a name="sign-in-with-a-different-identity"></a>Bejelentkezés másik identitással

Egyes felhasználóknak két, ugyanazt az e-mail-címet használó identitásuk van: egy munkahelyi vagy iskolai fiókjuk és egy személyes fiókjuk. Jellemzően ezek közül csak az egyik rendelkezik engedéllyel a számlázási fiók megtekintéséhez. Egyetlen e-mail-címhez két identitás is tartozhat. Ha olyan identitással jelentkezik be, amely nem rendelkezik engedéllyel a számlázási fiók megtekintéséhez, a [Cost Management + Számlázás](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) oldalon nem jelenik meg a számlázási fiók. Az identitásváltáshoz hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) InPrivate- vagy inkognitóablakban.
1. Ha az e-mail-címe két identitással rendelkezik, akkor választhat a személyes fiók, illetve a munkahelyi vagy iskolai fiók használata közül. Válassza ki az egyik fiókot.
1. Ha az Azure Portal Cost Management + Számlázás oldalán nem jelenik meg a számlázási fiók, ismételje meg az 1. és a 2. lépést, és válassza ki a másik identitást.

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

Olvassa el az alábbi, a számlázással és az előfizetéssel kapcsolatos cikkeket, amelyek segíthetnek a problémák elhárításában.

- [Elutasított bankkártya](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Az előfizetésbe való bejelentkezéssel kapcsolatos problémák](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [Nem található előfizetés](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Vállalati költségek nézet letiltva](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
