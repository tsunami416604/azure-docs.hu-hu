---
title: Az Azure számlázási szerepkörök használatával való hozzáférés kezelése |} A Microsoft Docs
description: ''
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
ms.date: 08/22/2017
ms.author: cwatson
ms.openlocfilehash: 0a8b5532f00d5feb964109710132816a191298e7
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274772"
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Számlázási adatokat az Azure szerepköralapú hozzáférés-vezérlés használatával való hozzáférés kezelése

A csapata tagjainak úgy biztosíthat hozzáférést az Azure számlázási adatokhoz, hogy a következő felhasználói szerepkörök egyikét hozzárendeli az előfizetéséhez: fiókadminisztrátor, szolgáltatásadminisztrátor, társadminisztrátor, tulajdonos, közreműködő, olvasó vagy számlázás olvasója. Akkor férhet hozzá, a számlázási adatokat a [az Azure portal](https://portal.azure.com/), és használhatják a [számlázási API-k](billing-usage-rate-card-overview.md) (Miután választott, bejövő) számlák és használati adatok programozott módon megszerezni. További információ szerepkörök adhat ki, és mely szerepkörök mi megtekintéséhez látogasson [Azure RBAC-szerepkörök](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> További felhasználóknak engedélyt számlák

A fiók rendszergazdája használatával kell részt a [az Azure portal](https://portal.azure.com/) számlákat való hozzáférés engedélyezése, más felhasználók számára, és API-n keresztül.

1. A fiók rendszergazdája, válassza ki az előfizetését a [előfizetések paneljén](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) az Azure Portalon.

1. Válassza ki **számlákat** , majd **számlákat hozzáférést**.

    ![Képernyőfelvétel bemutatja, hogyan számlákat hozzáférésének delegálása](./media/billing-manage-access/AA-optin.png)

1. Kapcsolja be **a** a hozzáférést, majd menti a módosításokat, a felhasználók előfizetéshez számla letöltése szerepkörök hatóköre.

    ![A képernyőfelvételen ki-és delegálja a számlához való hozzáférés](./media/billing-manage-access/AA-optinAllow.png)

Engedélyezés lehetővé teszi, hogy szolgáltatás-rendszergazda, társ-rendszergazda, tulajdonos, közreműködő, olvasó és számlázási olvasó az előfizetés az Azure Portalon PDF számlákat letölteni. Azonban a számlákat régebbi, mint a 2016. December érhetők el csak a fiók rendszergazdája most.

A fiókadminisztrátor azt is beállíthatja, hogy a számlákat a rendszer elküldje e-mailben. További tudnivalók: [Számla kérése e-mailben](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Felhasználók hozzáadása a számlázási olvasó szerepkör

A számlázási olvasó szerepkör rendelkezik a csak olvasási hozzáféréssel az előfizetés számlázási adatainak az Azure Portalon, és nem elérhető, a szolgáltatások, például virtuális gépek és a storage-fiókok. Rendelje hozzá a számlázási olvasó szerepkört az előfizetés számlázási adatokat, de nem képes kezelni az Azure-szolgáltatásokhoz való hozzáférést igénylő valaki. Ez a szerepkör a szervezeten belüli felhasználók, akik csak az Azure-előfizetések végrehajtani a pénzügyi és a cost management megfelelő.

1. Válassza ki előfizetését az Azure Portal [Előfizetések paneljéről](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Válassza ki **hozzáférés-vezérlés (IAM)** majd **Hozzáadás**.

    ![Az előfizetés paneljén képernyőfelvételen IAM](./media/billing-manage-access/select-iam.PNG)

1. Válasszon **számlázási olvasó** a a **Szerepkörválasztás** lapot.

    ![A képernyőfelvételen a helyi menü Nézet számlázási olvasó](./media/billing-manage-access/select-roles.PNG)

1. Adja meg a meghívni kívánt felhasználó e-mail-címét, majd kattintson az **OK** gombra a meghívás elküldéséhez.

    ![Adja meg az e-mailek meghívás a megjelenítő képernyőkép](./media/billing-manage-access/add-user.PNG)

1. A meghívó e-mail útmutatását követve jelentkezzen be a Számlázás olvasója szerepkörben.

    ![Képernyőkép a számlázási olvasó láthatja az Azure Portalon](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> A számlázási olvasó funkció előzetes verzióban érhető el, és nem globális felhőkben jelenleg nem támogatja. Vállalati előfizetések költségek megtekintheti, ha a vállalati rendszergazda engedélyezte a költségek megtekintése.

## <a name="adding-users-to-other-roles"></a>Felhasználók hozzáadása a többi szerepkörhöz

Az egyéb szerepköröket, például a tulajdonos vagy közreműködő szerepkörrel, a felhasználók csak a számlázási adatokat, de az Azure-szolgáltatásokat is. Ezek a szerepkörök kezeléséhez, lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Férhet hozzá a [Account Center](https://account.windowsazure.com)?

Csak a Fiókadminisztrátor az Account Center webhelyen is bejelentkezhetnek. A fiók rendszergazdája az előfizetés a jogos tulajdonosa. Alapértelmezés szerint a a személy, aki regisztrált vagy az Azure-előfizetést vásárolt a fiók rendszergazdája, kivéve, ha a [előfizetés tulajdonjogát átruházták](billing-subscription-transfer.md) valaki más. A fiók rendszergazdája előfizetések létrehozása, megszakítja az előfizetések, módosítani az előfizetéshez tartozó számlázási címét, és az előfizetéshez tartozó hozzáférési szabályzatok kezelése.

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
