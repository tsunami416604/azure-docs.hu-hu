---
title: Azure-számlázás való hozzáférés kezelése |} A Microsoft Docs
description: Ismerje meg, hogy a csapat tagjai számára az Azure számlázási információihoz való hozzáférés biztosítása.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: cd137f8fb44b20c9a1b3a506ad2703fc780bae89
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806651"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Számlázási adatokat az Azure-hoz való hozzáférés kezelése

A legtöbb előfizetések fel a számlázási adatokhoz való hozzáférés a csapat tagjai **előfizetések** az Azure Portalon. Ha Ön nagyvállalati szerződéssel (EA-ügyfél) rendelkező Azure-ügyfelek és a vállalati rendszergazda, a vállalati portál engedélyeket biztosíthat a részleg rendszergazdái és tulajdonosai.

## <a name="give-access-to-billing"></a>Hozzáférést biztosít a számlázás

A csapat tagjai számára egyet az alábbi felhasználói szerepkörök hozzárendelésével nagyvállalati szerződéssel rendelkező ügyfelek kivételével az összes adhat hozzáférést az Azure számlázási adatokat:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor
- Tulajdonos
- Közreműködő
- Olvasó
- Számlázás olvasója

Ha szerepköröket, lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

Ezek a szerepkörök rendelkeznek hozzáféréssel a számlázási adatokat a [az Azure portal](https://portal.azure.com/). Ezekhez a szerepkörökhöz rendelt felhasználók is használhatja a [számlázási API-kat](billing-usage-rate-card-overview.md) programozott módon megszerezni a számlák és használati adatok. További információkért lásd: [Azure RBAC-szerepkörök](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Felhasználók számlák letöltésének engedélyezése

Miután a megfelelő szerepkörök hozzárendelése a csapat tagjai, a fiók rendszergazdája hozzáférés az Azure Portalon számlák letöltésének be kell kapcsolnia. 2016. December régebbi számlák érhetők el csak a fiók rendszergazdája.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A fiók rendszergazdája, válassza ki az előfizetését a [előfizetések paneljén](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) az Azure Portalon.

1. Válassza ki **számlákat** , majd **számlákat hozzáférést**.

    ![Képernyőfelvétel bemutatja, hogyan számlákat hozzáférésének delegálása](./media/billing-manage-access/AA-optin.png)

1. Válassza ki **a** , és mentse.

    ![A képernyőfelvételen ki-és delegálja a számlához való hozzáférés](./media/billing-manage-access/AA-optinAllow.png)

A fiókadminisztrátor azt is beállíthatja, hogy a számlákat a rendszer elküldje e-mailben. További tudnivalók: [Számla kérése e-mailben](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Csak olvasási hozzáférést biztosít a számlázás

Rendelje hozzá a számlázási olvasó szerepkört az előfizetés számlázási adatokat, de nem kezelheti, vagy hozzon létre az Azure-szolgáltatások teszi csak olvasási hozzáférést igénylő valaki. Ez a szerepkör akkor megfelelő, akik felelősek a pénzügyi és a cost management Azure-előfizetés számára olyan szervezeti felhasználóknak.

Ha Ön nagyvállalati szerződéssel rendelkező ügyfelek, egy fiók tulajdonosa vagy a részleg rendszergazda rendelhet a számlázási olvasó szerepkör csapat tagjai. De a számlázási olvasó a szervezeti egységnek vagy a fiók számlázási adatainak megtekintéséhez, engedélyeznie kell a vállalati rendszergazda **AO költségek megtekintése** vagy **DA költségek megtekintése** szabályzatok a vállalati portálon.

A számlázási olvasó funkció előzetes verzióban érhető el, és nem globális felhőkben jelenleg nem támogatja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki előfizetését az Azure Portal [Előfizetések paneljéről](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Válassza ki **hozzáférés-vezérlés (IAM)**.
1. Válassza ki **szerepkör-hozzárendelések** ehhez az előfizetéshez tartozó szerepkör-hozzárendelések megtekintéséhez.
1. Válassza ki **szerepkör-hozzárendelés hozzáadása**.
1. Az a **szerepkör** legördülő menüben válassza ki **számlázási olvasó**.
1. Az a **kiválasztása** szövegmezőbe írja be a nevét, vagy e-mailben a hozzáadni kívánt felhasználó.
1. Válassza ki a felhasználót.
1. Kattintson a **Mentés** gombra.
1. Néhány pillanat múlva a felhasználó hozzárendelve a számlázási olvasó szerepkört az előfizetések szintjén.
1. A számlázási olvasó kap egy e-mailt, jelentkezzen be a mutató hivatkozást.

    ![Képernyőkép a számlázási olvasó láthatja az Azure Portalon](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Részleg rendszergazda vagy a fiók tulajdonosa számlázási hozzáférés engedélyezése

A vállalati rendszergazda engedélyezheti a részleg rendszergazdái és tulajdonosai a használat részleteiről és a költségeket a részlegek és az általuk kezelt fiókokat.

1. Vállalati rendszergazdaként jelentkezzen be a [a nagyvállalati szerződések portáljának](https://ea.azure.com/).
1. Válassza ki **kezelése**.
1. Alatt **regisztrációs**, módosítsa a **DA költségek megtekintése** való **engedélyezve** számára a részleg rendszergazdát, hogy a nézet használat és költségek.
1. Változás **AO költségek megtekintése** való **engedélyezve** a fiók tulajdonosa nézet használat és költségek.


További információkért lásd: [rendszergazdai szerepkörök az Azure nagyvállalati szerződés ismertetése az Azure-ban](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Csak a fiók rendszergazdák hozzáférhetnek az Account Center webhelyen

A fiók rendszergazdája az előfizetés a jogos tulajdonosa. Alapértelmezés szerint a a személy, aki regisztrált vagy az Azure-előfizetést vásárolt a fiók rendszergazdája, kivéve, ha a [előfizetés tulajdonjogát átruházták](billing-subscription-transfer.md) valaki más. A fiók rendszergazdája is előfizetések létrehozása, előfizetések megszakítja, egy előfizetéshez tartozó számlázási címének módosítása és az előfizetés regisztrációját a hozzáférési szabályzatainak kezelésére a [Account Center](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>További lépések

- Az egyéb szerepköröket, például a tulajdonos vagy közreműködő szerepkörrel, a felhasználók csak a számlázási adatokat, de az Azure-szolgáltatásokat is. Ezek a szerepkörök kezeléséhez, lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).
- Szerepkörök kapcsolatos további információkért lásd: [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
