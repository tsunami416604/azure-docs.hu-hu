---
title: A LUIS a kulcsok kezelése |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) segítségével a programozott API, a végpont és a külső kulcsok kezeléséhez.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 8e6e363649a0bdab5525de7b8e7abe9a53d14573
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266022"
---
# <a name="manage-your-luis-keys"></a>A LUIS kulcsok kezelése
A kulcs létrehozására és a LUIS alkalmazás közzététele, vagy a végpont lekérdezése teszi lehetővé. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Fő fogalmak
Lásd: [LUIS kulcsokat](luis-concept-keys.md) LUIS jelentéskészítő és -végpont alapfogalmakat megértése.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Rendelje hozzá a végpontkulcs
Az a **közzététel app** lapon, és már van egy kulcs az **erőforrások és a kulcsok** tábla. Ez az a szerzői műveletekhez (alapszintű) kulcs. 

1. A LUIS kulcs létrehozására a [Azure-portálon](https://portal.azure.com). További utasításokért lásd: [használata Azure előfizetés kulcs létrehozása](luis-how-to-azure-subscription.md).
 
2. A LUIS kulcs az előző lépésben létrehozott hozzáadásához kattintson a **kulcs hozzáadása** gombra kattintva nyissa meg a **kulcs hozzárendelése az alkalmazás** párbeszédpanel. 

    ![Az alkalmazás egy kulcs hozzárendelése](./media/luis-manage-keys/assign-key.png)
3. A párbeszédpanelen válassza ki a bérlő. 
 
    > [!Note]
    > Az Azure a bérlő az Azure Active Directory-azonosítója az ügyfél vagy a szolgáltatáshoz társított szervezet jelöli. A felhasználó már rendelkezik Azure-előfizetések az egyéni Microsoft Account, ha már van egy bérlő! Az Azure-portálon jelentkezik be, amikor automatikusan jelentkezik [az alapértelmezett bérlő](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Szabadon használhatja ezt a bérlőt, de előfordulhat, hogy szeretne létrehozni egy szervezeti rendszergazdai fiókot.

4. Válassza ki a hozzáadni kívánt Azure LUIS kulcshoz tartozó Azure-előfizetést.

5. Válassza ki az Azure LUIS fiókot. A fiók a régió zárójelben jelenik meg. 

    ![Válassza ki a kulcs](./media/luis-manage-keys/assign-key-filled-out.png)

6. A végpont kulcs hozzárendelése után használja az összes endpoint lekérdezésekben. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Közzétételi régiók
További információ a közzététel [régiók](luis-reference-regions.md) többek között a közzététel a [Európa](luis-reference-regions.md#publishing-to-europe), és [Ausztrália](luis-reference-regions.md#publishing-to-australia). Közzétételi régiók régiók szerzői eltérnek. Ellenőrizze, hogy a szerzői műveletek terület megfelelő kívánt közzétételi régió alkalmazást hoz létre.

## <a name="unassign-key"></a>Szüntesse meg a kulcs

* Az a **lista erőforrások és a kulcsok**, szüntesse meg kívánt entitás mellett is bin ikonra. Kattintson a **OK** a törlés megerősítéséhez a jóváhagyást kérő üzenet.
 
    ![Szüntesse meg az entitás](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> A LUIS kulcs hozzárendelés nem törli az Azure-előfizetésből.

## <a name="next-steps"></a>További lépések

A kulcs használatával a az alkalmazás közzététele a **közzététel app** lap. Közzétételi útmutatásért lásd: [közzététel app](PublishApp.md).

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website