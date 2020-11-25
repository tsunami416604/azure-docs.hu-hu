---
title: További Azure-előfizetés létrehozása
description: Megtudhatja, hogyan vehet fel új Azure-előfizetést az Azure Portalon. A számlázási fiók űrlapjaival kapcsolatos információk és további elérhető erőforrások megtekintése.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 27594ff4d7673038e9de38c5969a615be60aeadf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844693"
---
# <a name="create-an-additional-azure-subscription"></a>További Azure-előfizetés létrehozása

További előfizetést hozhat létre az Azure Portalon a [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), a [Microsoft-ügyfélszerződés](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) vagy a [Microsoft-partnerszerződés](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) számlázási fiókjához. A további előfizetések létrehozásának több célja lehet: az előfizetési korlát elérésének elkerülése, különálló környezetek létrehozása a biztonság érdekében, illetve az adatok elkülönítése megfelelőségi okokból.

Ha rendelkezik Microsoft Online Services Programba (MOSP) tartozó számlázási fiókkal, további előfizetéseket hozhat létre az [Azure regisztrációs portálján](https://account.azure.com/signup?offer=ms-azr-0003p).

A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókok az Azure Portalon történő megtekintését](view-all-accounts.md) ismertető cikket.

## <a name="permission-required-to-create-azure-subscriptions"></a>Az Azure-előfizetések létrehozásához engedély szükséges

A következő engedélyekre van szüksége az előfizetések létrehozásához:

|Számlázási fiók  |Engedély  |
|---------|---------|
|Nagyvállalati Szerződés (EA) |  Fióktulajdonosi szerepkör a Nagyvállalati Szerződéses regisztrációban. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-ea-roles.md).    |
|Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA) |  Tulajdonosi vagy közreműködői szerepkör a számlaszakaszban, a számlázási profilban vagy a számlázási fiókban. Vagy Azure-előfizetés létrehozója szerepkör a számlaszakaszban.  További információkért lásd [az előfizetés számlázási szerepköreit és azok feladatát](understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Microsoft-partnerszerződés (MPA) |   Globális rendszergazda és rendszergazdai ügynök szerepkör a CSP-partnerszervezetben. További információ: [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview)  Azure-előfizetések létrehozásához a felhasználónak be kell jelentkeznie a partnerbérlőbe.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Előfizetés létrehozása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá az **Előfizetések** kifejezésre.

   ![Képernyőkép az előfizetés kifejezés kereséséről a portálon](./media/create-subscription/billing-search-subscription-portal.png)

1. Válassza a **Hozzáadás** lehetőséget.

   ![Képernyőkép az Előfizetések nézet Hozzáadás gombjáról](./media/create-subscription/subscription-add.png)

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

További előfizetéseket programozott módon is létrehozhat. További információ:

- [Nagyvállalati Szerződéshez tartozó előfizetések programozott létrehozása a legújabb API-val](programmatically-create-subscription-enterprise-agreement.md)
- [Microsoft Ügyfélszerződéshez tartozó előfizetések programozott létrehozása a legújabb API-val](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Microsoft Partnerszerződéshez tartozó előfizetések programozott létrehozása a legújabb API-val](Programmatically-create-subscription-microsoft-customer-agreement.md)

## <a name="next-steps"></a>További lépések

- [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](add-change-subscription-administrator.md)
- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez](../../governance/management-groups/create-management-group-portal.md)
- [Az Azure-előfizetés lemondása](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).