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
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491056"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Számlázási adatokat az Azure-hoz való hozzáférés kezelése

Megadhat más a számlázási adatokat a hozzáférést a fiók az Azure Portalon. A számlázási szerepkörök típusát és az utasításokat a számlázási adatokhoz hozzáférést biztosítania a üzemelő kisszolgáló típusától az Ön számlázási fiókjához. A számlázási fiók típusának megállapításához lásd: [ellenőrizze az Ön számlázási fiókjához típusú](#check-the-type-of-your-billing-account).

A cikk a Microsoft Online Services program fiókokkal rendelkező vonatkozik. Ha Ön Azure-ügyfelek az egy nagyvállalati szerződés (EA) és a vállalati rendszergazda, a vállalati portál engedélyeket biztosíthat a részleg rendszergazdái és tulajdonosai. További információkért lásd: [rendszergazdai szerepkörök az Azure nagyvállalati szerződés ismertetése az Azure-ban](billing-understand-ea-roles.md). Ha Ön egy Microsoft Ügyfélszerződéséhez ügyfél megtekintéséhez [megismerheti a Microsoft Ügyfélszerződéséhez rendszergazdai szerepkörök az Azure-ban](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>A rendszergazdák a Microsoft Online Services program-fiókok

Egy fiók rendszergazdája az egyedüli tulajdonos, a Microsoft Online Service Program számlázási fiók. A szerepkör van rendelve egy személy, aki az Azure-ban regisztrált. A Fiókrendszergazdák előfizetések létrehozása, számlák megtekintése vagy módosítása az előfizetés számlázási különböző számlázási feladatok végrehajtására jogosult.

## <a name="give-others-access-to-view-billing-information"></a>Hozzáférést másoknak számlázási adatok megtekintése

A fiók rendszergazdája mások hozzáférést biztosíthat az Azure számlázási információihoz fiókjukban egy előfizetés a következő szerepkörök hozzárendelésével.

- Szolgáltatás-rendszergazda
- Társadminisztrátor
- Tulajdonos
- Közreműködő
- Olvasó
- Számlázási olvasó

Ezek a szerepkörök rendelkeznek hozzáféréssel a számlázási adatokat a [az Azure portal](https://portal.azure.com/). Ezek a szerepkörök hozzárendelt személyek is használhatja a [számlázási API-kat](billing-usage-rate-card-overview.md) programozott módon megszerezni a számlák és használati adatok.

Ha szerepköröket, lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

** Ha nagyvállalati szerződéssel rendelkező ügyfelek, fióktulajdonos rendelhet a fenti szerepkör többi felhasználója is a csapathoz. Azonban ezeknek a felhasználóknak az elszámolási adatok megtekintéséhez a vállalati rendszergazdának engedélyeznie kell a AO költségek megtekintése a vállalati portálon.


### <a name="opt-in"></a> Felhasználók számlák letöltésének engedélyezése

Után egy fiók rendszergazdája más felhasználókhoz rendelt a megfelelő szerepköröket, azokat be kell kapcsolnia a hozzáférés az Azure Portalon számlákat letölteni. 2016. December régebbi számlák érhetők el csak a fiók rendszergazdája.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/), mint fiók a rendszergazdák

1. A Keresés **Cost Management és számlázás**.

    ![Az Azure portál keresési bemutató képernyőkép](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Válassza ki **előfizetések** a bal oldali panelen. A hozzáférést, attól függően szükség lehet egy számlázási hatókör majd válassza ki és **előfizetések**.
 
    ![Képernyőkép, amely jeleníti meg az előfizetések kiválasztása](./media/billing-manage-access/billing-select-subscriptions.png)

1. Válassza ki **számlákat** , majd **számlához való hozzáférés**.

    ![Képernyőfelvétel bemutatja, hogyan számlákat hozzáférésének delegálása](./media/billing-manage-access/AA-optin.png)

1. Válassza ki **a** , és mentse.

    ![A képernyőfelvételen ki-és delegálja a számlához való hozzáférés](./media/billing-manage-access/AA-optinAllow.png)

A fiókadminisztrátor azt is beállíthatja, hogy a számlákat a rendszer elküldje e-mailben. További tudnivalók: [Számla kérése e-mailben](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Csak olvasási hozzáférést biztosít a számlázás

Rendelje hozzá a számlázási olvasó szerepkört az előfizetés számlázási adatokat, de nem kezelheti, vagy hozzon létre az Azure-szolgáltatások teszi csak olvasási hozzáférést igénylő valaki. Ez a szerepkör akkor megfelelő, akik felelősek a pénzügyi és a cost management Azure-előfizetés számára olyan szervezeti felhasználóknak.

A számlázási olvasó funkció előzetes verzióban érhető el, és nem globális felhőkben jelenleg nem támogatja.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/), mint fiók a rendszergazdák

1. A Keresés **Cost Management és számlázás**.

    ![Az Azure portál keresési bemutató képernyőkép](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Válassza ki **előfizetések** a bal oldali panelen. A hozzáférést, attól függően szükség lehet egy számlázási hatókör majd válassza ki és **előfizetések**.
 
    ![Képernyőkép, amely jeleníti meg az előfizetések kiválasztása](./media/billing-manage-access/billing-select-subscriptions.png)

1. Válassza ki **hozzáférés-vezérlés (IAM)** .
1. Válassza ki **Hozzáadás** az oldal tetején.

    ![Képernyőkép a gombra kattintva a szerepkör-hozzárendelés hozzáadása](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. Az a **szerepkör** legördülő menüben válassza ki **számlázási olvasó**.
1. Az a **kiválasztása** szövegmezőbe írja be a nevét, vagy e-mailben a hozzáadni kívánt felhasználó.
1. Válassza ki a felhasználót.
1. Kattintson a **Mentés** gombra.
    ![Képernyőkép a gombra kattintva a szerepkör-hozzárendelés hozzáadása](./media/billing-manage-access/billing-save-role-assignment.png)

1. Néhány pillanat múlva a felhasználó a számlázási olvasó szerepkört az előfizetés hozzárendelve.

** Ha nagyvállalati szerződéssel rendelkező ügyfelek, egy fiók tulajdonosa vagy a részleg rendszergazda rendelhet a számlázási olvasó szerepkör csapat tagjai. De a számlázási olvasó a szervezeti egységnek vagy a fiók számlázási adatainak megtekintéséhez, engedélyeznie kell a vállalati rendszergazda **AO költségek megtekintése** vagy **DA költségek megtekintése** szabályzatok a vállalati portálon.

## <a name="check-the-type-of-your-billing-account"></a>A Számlázási fiókja típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>További lépések

- Az egyéb szerepköröket, például a tulajdonos vagy közreműködő szerepkörrel, a felhasználók csak a számlázási adatokat, de az Azure-szolgáltatásokat is. Ezek a szerepkörök kezeléséhez, lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).
- Szerepkörök kapcsolatos további információkért lásd: [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
