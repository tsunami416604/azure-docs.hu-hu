---
title: "Azure számlázás szerepkörök használatával való hozzáférés kezelése |} Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Számlázási adatokat az Azure szerepköralapú hozzáférés-vezérlés használatával való hozzáférés kezelése

Ön hozzáférést biztosíthat az Azure számlázási adatokat a csoport tagjai számára úgy egyet az alábbi felhasználói szerepkörök hozzárendelése az előfizetéshez: Fiókadminisztrátor, szolgáltatás-rendszergazdát, társadminisztrátoraként, tulajdonos, közreműködő, olvasó és számlázási olvasó. Férhet számlázási adatokhoz a [Azure-portálon](https://portal.azure.com/), és használhatják a [számlázási API-k](billing-usage-rate-card-overview.md) programozott módon le a számlák (egyszer engedélyezte, bejövő) és a használat részleteiről. További információ szerepkörök adhatnak ki, és mely szerepköröket is mi, olvassa el [szerepkörök az Azure RBAC](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a>Felhasználó számlák eléréséhez

A Fiókadminisztrátor használatával bírálhatja felül a [Azure-portálon](https://portal.azure.com/) számlák a hozzáférést, a más felhasználók számára, és API-n keresztül.

1. A fiók rendszergazdaként, válassza ki az előfizetést a [előfizetések panel](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Azure-portálon.

1. Válassza ki **számlák** , majd **számlák elérésére**.

    ![Képernyőkép bemutatja, hogyan adhat hozzáférést számlák](./media/billing-manage-access/AA-optin.png)

1. Kapcsolja be **a** a hozzáférés a felhasználó az előfizetéshez, a módosítások mentése után töltse le a számla szerepkörök hatóköre.

    ![Képernyőfelvétel a ki-és delegálja a számla elérésére](./media/billing-manage-access/AA-optinAllow.png)

Engedélyezés lehetővé teszi, hogy szolgáltatás-rendszergazdát, társadminisztrátoraként, tulajdonos, közreműködő, olvasó és számlázási olvasó letölteni az Azure portálon PDF számlák előfizetésben. Azonban régebbi, mint a 2016. December számlák érhetők el csak az a fiók rendszergazdája most.

A Fiókadminisztrátor is konfigurálhatja az e-mailben küldött számlákat. További tudnivalókért lásd: [e-mailben a számla beolvasása](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Felhasználók hozzáadása a számlázási olvasó szerepkört

A számlázási olvasó szerepkört az előfizetés számlázási adatokat az Azure portál csak olvasási hozzáféréssel, és nem érhető el a szolgáltatásokat, például virtuális gépek és tárfiókok rendelkezik. A számlázási olvasó szerepkör hozzárendelése valaki hozzá kell férnie az előfizetés számlázási adatokat, de nem képes kezelni az Azure-szolgáltatásokhoz. Ez a szerepkör azon egy szervezet számára, akik csak Azure-előfizetések végrehajtani a pénzügyi és költség-kezelés megfelelő.

1. Válassza ki az előfizetést a [előfizetések panel](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Azure-portálon.

1. Válassza ki **hozzáférés-vezérlés (IAM)** majd **Hozzáadás**.

    ![Képernyőfelvétel az előfizetés panelen IAM](./media/billing-manage-access/select-iam.PNG)

1. Válasszon **számlázási olvasó** a a **Szerepkörválasztás** lap.

    ![Képernyőfelvétel a helyi menü Nézet számlázási olvasó](./media/billing-manage-access/select-roles.PNG)

1. Írja be az e-mailt a meghívót, majd kattintson a kívánt felhasználó **OK** a meghívó elküldésére.

    ![Adja meg e-mail címét meghívni a képernyőkép](./media/billing-manage-access/add-user.PNG)

1. Kövesse a meghívott felhasználó e-mailben szereplő utasításokat Jelentkezzen be egy számlázási olvasó.

    ![Képernyőkép a számlázási olvasó csoportszűrést Azure-portálon](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> A számlázási olvasó jelenleg előzetes verzióban érhető vagy szolgáltatás egyelőre nem támogatják a vállalati (EA) előfizetések vagy nem globális felhők.

## <a name="adding-users-to-other-roles"></a>Felhasználók más szerepkörök hozzáadása

Más szerepkörök, például a tulajdonos vagy közreműködő szerepkörrel, a felhasználók férhetnek hozzá csak számlázási adatokat, de az Azure-szolgáltatásokat is. Ezek a szerepkörök kezeléséhez, tekintse meg a [hozzáadása vagy módosítása, hogy az előfizetés vagy a szolgáltatások kezelése az Azure rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Ki férhet hozzá a [Account Center](https://account.windowsazure.com)?

Csak a Fiókadminisztrátor az Account center bejelentkezhet. A Fiókadminisztrátor az előfizetés jogi tulajdonosa. Alapértelmezés szerint a a személy, aki regisztrált a vagy vásárolt Azure-előfizetést a Fiókadminisztrátor, kivéve, ha a [előfizetés tulajdonjogának áthelyezése történt](billing-subscription-transfer.md) valaki másnak. A Fiókadminisztrátor előfizetések létrehozása, előfizetések megszakíthatja, módosítsa az előfizetés számlázási címét, és hozzáférési házirendek az előfizetés kezelése.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
