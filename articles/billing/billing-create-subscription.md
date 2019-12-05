---
title: Egy további Azure-előfizetés létrehozása | Microsoft Docs
description: Megtudhatja, hogyan vehet fel új Azure-előfizetést az Azure Portalon.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: bd561f616c9e120a38f1c6b125e349684f82942e
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665610"
---
# <a name="create-an-additional-azure-subscription"></a>További Azure-előfizetés létrehozása

További előfizetést hozhat létre az Azure Portalon a [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), a [Microsoft-ügyfélszerződés](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) vagy a [Microsoft-partnerszerződés](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) számlázási fiókjához. A további előfizetések létrehozásának több célja lehet: az előfizetési korlát elérésének elkerülése, különálló környezetek létrehozása a biztonság érdekében, illetve az adatok elkülönítése megfelelőségi okokból.

Ha rendelkezik Microsoft Online Services Programba (MOSP) tartozó számlázási fiókkal, további előfizetéseket hozhat létre az [Azure regisztrációs portálján](https://account.azure.com/signup?offer=ms-azr-0003p).

A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókok az Azure Portalon történő megtekintését](billing-view-all-accounts.md) ismertető cikket.

## <a name="permission-required-to-create-azure-subscriptions"></a>Az Azure-előfizetések létrehozásához engedély szükséges

A következő engedélyekre van szüksége az előfizetések létrehozásához:

|Számlázási fiók  |Engedély  |
|---------|---------|
|Nagyvállalati Szerződés (EA) |  Fióktulajdonosi szerepkör a Nagyvállalati Szerződéses regisztrációban. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md).    |
|Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA) |  Tulajdonosi vagy közreműködői szerepkör a számlaszakaszban, a számlázási profilban vagy a számlázási fiókban. Vagy Azure-előfizetés létrehozója szerepkör a számlaszakaszban.  További információkért lásd [az előfizetés számlázási szerepköreit és azok feladatát](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Microsoft-partnerszerződés (MPA) |   Globális rendszergazda és rendszergazdai ügynök szerepkör a CSP-partnerszervezetben. További információ: [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](https://docs.microsoft.com/partner-center/permissions-overview)  Az Azure-előfizetések létrehozásához a felhasználónak be kell jelentkeznie a parnterbérlőbe.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Előfizetés létrehozása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá az **Előfizetések** kifejezésre.

   ![Képernyőkép az előfizetés kifejezés kereséséről a portálon](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Válassza a **Hozzáadás** lehetőséget.

   ![Képernyőkép az Előfizetések nézet Hozzáadás gombjáról](./media/billing-create-subscription/subscription-add.png)

1. Ha több számlázási fiókhoz is hozzáféréssel rendelkezik, válassza ki a számlázási fiókot, amelyhez előfizetést kíván létrehozni.

1. Töltse ki az űrlapot, és kattintson a **Létrehozás** lehetőségre. Az alábbi táblázatok tartalmazzák az egyes számlázási fiókokhoz tartozó űrlapmezőket.

**Nagyvállalati Szerződés**

|Mező  |Meghatározás  |
|---------|---------|
|Name (Név)     | A megjelenítendő név segítségével egyszerűen beazonosíthatja az előfizetést az Azure Portalon.  |
|Ajánlat     | Ha az előfizetést fejlesztésre vagy számítási feladatok tesztelésére szeretné használni, válassza a fejlesztéshez és teszteléshez készült EA Dev/Test csomagot, minden más esetben használja a Microsoft Azure Enterprise lehetőséget. EA Dev/Test-előfizetés létrehozásához engedélyezni kell a DevTest-ajánlatot a regisztrált fiókjához.|

**Microsoft-ügyfélszerződés**

|Mező  |Meghatározás  |
|---------|---------|
|Számlázási profil     | Az előfizetés díjai a kiválasztott számlázási profilra lesznek kiszámlázva. Ha csak egy előfizetéshez rendelkezik hozzáféréssel, a választási lehetőség ki van szürkítve.     |
|Számlázási szakasz     | Az előfizetés díjai a számlázási profil e számlázási szakaszán fognak megjelenni. Ha csak egy számlázási szakaszhoz rendelkezik hozzáféréssel, a választási lehetőség ki van szürkítve.  |
|Felkészülés     | Ha az előfizetést fejlesztésre vagy számítási feladatok tesztelésére szeretné használni, válassza a fejlesztéshez és teszteléshez készült Microsoft Azure-csomagot, minden más esetben használja a Microsoft Azure-csomag lehetőséget. Ha csak egy csomag van engedélyezve a számlázási profilhoz, a választási lehetőség ki van szürkítve.  |
|Name (Név)     | A megjelenítendő név segítségével egyszerűen beazonosíthatja az előfizetést az Azure Portalon.  |

**Microsoft-partnerszerződés**

|Mező  |Meghatározás  |
|---------|---------|
|Ügyfél    | Létrejön az előfizetés a kiválasztott ügyfél számára. Ha csak egy ügyfele van, a választási lehetőség ki van szürkítve.  |
|Viszonteladó    | Az a viszonteladó, aki szolgáltatásokat fog nyújtani az ügyfélnek. Ez egy nem kötelezően kitöltendő mező, és csak a közvetett szolgáltatókra vonatkozik a CSP kétrétegű modelljében. |
|Name (Név)     | A megjelenítendő név segítségével egyszerűen beazonosíthatja az előfizetést az Azure Portalon.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>További Azure-előfizetés létrehozása programozott módon

További előfizetéseket programozott módon is létrehozhat. További információért tekintse meg [az Azure-előfizetések programozott módon történő létrehozását](../azure-resource-manager/programmatically-create-subscription.md) ismertető cikket.

## <a name="next-steps"></a>További lépések

- [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](billing-add-change-azure-subscription-administrator.md)
- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Az Azure-előfizetés lemondása](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
