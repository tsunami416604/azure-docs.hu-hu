---
title: A korlátozott kísérleti projekt áthelyezése az Azure-bA
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan helyezze át egy korlátozott próbaverzió-projektet az Azure-bA.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056877"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>A korlátozott próbaverzió projekt áthelyezése az Azure-bA

A Custom Vision Service befejeződik, annak áthelyezése az Azure-ba, mivel korlátozott próbaverzió projektek Azure-on kívül támogatása lezárta. Ez a dokumentum bemutatja, hogyan másolja a korlátozott próbaverzió projekt egy Azure-erőforrás a Custom Vision API-k használatával.

A korlátozott próbaverzió projektek megjelenítésének támogatása a [Custom Vision webhely](https://customvision.ai) 2019. márciusi 25. napon fejeződött be. Ez a dokumentum most bemutatja, hogyan használhatja a Custom Vision API-k, az egy [áttelepítési python-szkriptet](https://github.com/Azure-Samples/custom-vision-move-project) a Githubon) a projekthez, hogy egy Azure-erőforrás másolása.

További részleteket, beleértve a fő határidőket a korlátozott próbaverziós elavulásának folyamat tájékozódhat a [kibocsátási megjegyzések](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) vagy korlátozott próbaverziós projektek tulajdonosainak küldött e-mail-üzeneteket.

A [áttelepítési parancsfájl](https://github.com/Azure-Samples/custom-vision-move-project) lehetővé teszi, hogy hozza létre újra a projekt letöltésével és címkék, régiók, majd feltölti és az aktuális ismétléskor lemezképeket. Hagyja meg az új projekt az új előfizetés, amely majd betaníthatja az.

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy Microsoft-fiók vagy -ba való bejelentkezéshez használni kívánt Azure Active Directory (AAD) fiók társított érvényes Azure-előfizetést a [Custom Vision webhely](https://customvision.ai). 
    - Ha nem rendelkezik Azure-fiók [hozzon létre egy fiókot](https://azure.microsoft.com/free/) ingyenes.
    - Az előfizetésekre és erőforrásokra vonatkozó Azure fogalmakat szeretné megismerni, tekintse meg a [Azure fejlesztői útmutatójából.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Egyéni vizuális erőforrások létrehozása az Azure Portalon

A Custom Vision Service használata az Azure-ban, szüksége lesz a Custom Vision betanítási és Predikciós erőforrások létrehozása a [az Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Egyetlen erőforrást több projektet társíthatók. További részleteket tartalmaz [díjszabás és korlátok](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) érhető el. Továbbra is ingyenesen használhatja a Custom Vision Service, válassza ki a F0 szintre az Azure Portalon. 

> [!NOTE]
> Ha áthelyezi a Custom Vision projekt egy Azure-erőforrás, az megörökli a mögöttes [engedélyek]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) adott Azure-erőforrás. Ha a szervezet más felhasználói az Azure-projektjéhez az erőforrás tulajdonosa, lesz elérni a projekthez a a [Custom Vision webhely](https://customvision.ai). Hasonlóképpen az erőforrások törlése eltávolítja a projekteket.  

## <a name="find-your-limited-trial-project-information"></a>A kísérleti projekt korlátozott információkat

Helyezze át a projekthez, szüksége lesz a _azonosító projekt_ és _képzési kulcs_ az áttelepíteni kívánt projekt. Ha ezt az információt nem rendelkezik, látogasson el a [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) az azonosítóhoz, és minden, a projektek kulcs. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Másolja a projekt az Azure-ban a Python-mintakód segítségével

Kövesse a [kód utasításokat minta](https://github.com/Azure-Samples/custom-vision-move-project), a korlátozott próbaverziós kulccsal, és a "forrás" anyagok és a kulcsát, a "cél" során létrehozott új Azure-erőforrás azonosítója projektet.

Alapértelmezés szerint minden korlátozott próbaverzió-projektet az USA déli középső velünk a Kapcsolatot Azure-régióban üzemel.

## <a name="next-steps"></a>További lépések

A projekt már át lett helyezve az Azure-erőforrás. A betanítási és Predikciós kulcsokat írt alkalmazások frissíteni kell.

A projekt megtekintése a [Custom Vision webhely](https://customvision.ai), jelentkezzen be ugyanazzal a fiókkal jelentkezzen be az Azure portal segítségével. Ha nem látja a projekthez, erősítse meg, hogy biztosan megfeleljen az ugyanabban a címtárban lévő a [Custom Vision webhely](https://customvision.ai) a könyvtárként, ahol az erőforrások az Azure Portalon található. Az Azure portál és CustomVision.ai válassza ki a címtárat a képernyő jobb felső sarkában lévő legördülő felhasználói menüből.