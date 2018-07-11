---
title: A LUIS a végpont kulcsok kezelése |} A Microsoft Docs
description: A programozott API, a végpont és a külső kulcsok kezelése a Language Understanding (LUIS) használatával.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 288c1456979ff9214b2f333768da65bd3da1a734
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917795"
---
# <a name="manage-your-luis-endpoint-keys"></a>A LUIS-végpont kulcsok kezelése
Egy kulcs használatával hozhat létre és a LUIS-alkalmazás közzététele vagy lekérdezni a végpont teszi lehetővé. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Fő fogalmak
Lásd: [kulcsok a LUIS](luis-concept-keys.md) LUIS létrehozási és -végpont főbb fogalmak megértéséhez.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Végponti kulcs hozzárendelése
Az a **alkalmazás közzététele** lapon, és már van egy kulcsot a **erőforrások és a kulcsok** tábla. Ez az az Authoring Tool (alapszintű) kulcs. 

1. A LUIS kulcs létrehozása a [az Azure portal](https://portal.azure.com). További utasításokért lásd: [egy az Azure-végpont kulcsának létrehozása](luis-how-to-azure-subscription.md).
 
2. A LUIS-kulcs az előző lépésben létrehozott hozzáadásához kattintson a **kulcs hozzáadása** gombra kattintva nyissa meg a **rendel a kulcs a** párbeszédpanel. 

    ![Az alkalmazás egy kulcs hozzárendelése](./media/luis-manage-keys/assign-key.png)
3. A párbeszédpanelen válassza ki a bérlő. 
 
    > [!Note]
    > Az Azure-ban egy bérlő az Azure Active Directory azonosító az ügyfél vagy a szervezeti szolgáltatáshoz társított jelöli. Ha korábban regisztrált egy Azure-előfizetés az egyéni Microsoft Account, már van bérlője. Amikor bejelentkezik az Azure Portalra, akkor automatikusan bejelentkezett [az alapértelmezett bérlőbe](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Szabadon használhatja ezt a bérlőt, de előfordulhat, hogy szeretne létrehozni egy szervezeti rendszergazdai fiókot.

4. Válassza ki a hozzáadni kívánt Azure LUIS-kulcs társítva az Azure-előfizetést.

5. Válassza ki az Azure LUIS-fiókot. A régió, a fiók zárójelben jelenik meg. 

    ![Válassza ki a kulcsot](./media/luis-manage-keys/assign-key-filled-out.png)

6. Után ez a végpont kulcs rendel hozzá, használhatja az összes endpoint lekérdezés. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Közzétételi régiók
További tudnivalók a közzététel [régiók](luis-reference-regions.md) többek között a közzététel [Európa](luis-reference-regions.md#publishing-to-europe), és [Ausztrália](luis-reference-regions.md#publishing-to-australia). Közzétételi régiója nem ugyanaz a létrehozási régiók. Ellenőrizze, hogy az Authoring Tool régió régióhoz tartozó, a közzétételi szeretné az alkalmazást hoz létre.

## <a name="unassign-key"></a>Kulcs visszavonása

* Az a **erőforrások és a kulcsok listája**, kattintson a Kuka ikonnal meg kívánja szüntetni az entitás mellett. Kattintson a **OK** a megerősítő üzenetben a törlés megerősítéséhez.
 
    ![Entitás hozzárendelésének megszüntetése](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Hozzárendelés a LUIS-kulcs nem törlődik az Azure-előfizetésből.

## <a name="next-steps"></a>További lépések

Az alkalmazás közzététele a kulcsa segítségével a **alkalmazás közzététele** lapot. Közzétételi útmutatásért lásd: [alkalmazás közzététele](luis-how-to-publish-app.md).