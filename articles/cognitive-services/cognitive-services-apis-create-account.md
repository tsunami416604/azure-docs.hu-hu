---
title: Cognitive Services API-k létrehozása az Azure Portalon |} A Microsoft Docs
description: Hogyan lehet egy Microsoft Cognitive Services API-k létrehozása az Azure Portalon.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036153"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Cognitive Services API-k létrehozása az Azure Portalon

A Microsoft Cognitive Service API-k használatához először hozzon létre egy fiókot az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

2. Kattintson a **+ erőforrás létrehozása**.

3. Azure Marketplace-en, válassza a **mesterséges Intelligencia és Cognitive Services** és elérhető API-k listájának felderítése. Kattintson a **összes** a Cognitive Services API-k teljes listájának megtekintéséhez. Kattintson a választott folytatja az API-t.

    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Az a **létrehozás** lap, adja meg a következő információkat:

   - **Fiók neve:** a fiók nevét. Azt javasoljuk egy leíró nevet, például *AFaceAPIAccount*.

   - **Előfizetés:** válassza ki az egyik elérhető összes Azure-előfizetés, amelyben van legalább közreműködői engedélyekkel.

   - **API-típus:** válassza ki a Cognitive Services API létrehozásához használni szeretne. Különböző Cognitive Services API-kat kapcsolatos további információkért látogasson el a [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) hely.

   - **Tarifacsomag:** a Cognitive Services-fiók költsége attól függ, a tényleges használat és a választott beállításokat. Az egyes API díjszabással kapcsolatos további információkért tekintse meg a [díjszabási lapjait](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Erőforráscsoport:** erőforráscsoport az azonos életciklus, engedélyek és szabályzatok rendelkező erőforrások gyűjteménye. Az erőforráscsoportokkal kapcsolatos további tudnivalókért lásd: [Azure-erőforrások a portál](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Erőforráscsoport helye:** Ez kötelező, csak akkor, ha a kijelölt API egy globális (egy olyan helyre, nem kötelező). Ha az API-t globális és a egy olyan helyre nem kötött, azonban meg kell adnia egy a Cognitive Services API-fiókhoz társított metaadatokat tartalmazó az erőforráscsoport helyét. Ezen a helyen nem befolyásolja a fiók futásidejű rendelkezésre állását. Erőforráscsoport kapcsolatos további információkért lásd: [Azure-erőforrások a portál](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Nyugtázniuk Online szolgáltatási feltételek vonatkoznak:** annak érdekében, hogy hozzon létre egy fiókot, az előfizetésnél tulajdonos vagy közreműködő (által meghatározott [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)) kell kifejezetten elismeri a feltételeket, amelyek Cognitive Services a alkalmazni [Online szolgáltatási feltételek](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Az előfizetés tulajdonosa letilthatja egy adott erőforráscsoport vagy előfizetés keresztül a Cognitive Services-fiók létrehozásának [Azure házirendek](../azure-policy/azure-policy-introduction.md) a következő cikk [hozzárendelése és kezelése az Azure Portalon erőforrás-házirendek](../azure-policy/assign-policy-definition.md) és a "nem engedélyezett erőforrástípusok" szabályzatdefiníció hozzárendelése és megadásával **Microsoft.CognitiveServices/accounts** a cél erőforrás típusaként.

     A fióklétrehozás le lett tiltva, ha a következő hiba a fiók létrehozása idején fog megjelenni:

     ![Hiba történt a fiók létrehozása](media/cognitive-services-apis-create-account/error-message.png)

5. Rögzíti a fiókot az Azure portal irányítópultján, kattintson a **rögzítés az irányítópulton**.

6. A fiók létrehozásához kattintson a **Létrehozás** gombra.

A Cognitive Services-fiók sikeres telepítése után kattintson a csempére az irányítópulton, a fiók adatainak megtekintéséhez.

Használhatja a **végponti URL-cím** a a **áttekintése** szakaszban és a kulcsot a **kulcsok** szakasz API készítésének megkezdéséhez hívja az alkalmazásokban.

![Adatok megjelenítése a fiók](media/cognitive-services-apis-create-account/display-account.png)

![Fiókkulcsok megjelenítése](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>További lépések

A Microsoft Cognitive Services összes szolgáltatása elérhető kapcsolatos további információkért lásd: [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

A rövid útmutatók, néhány példa a Cognitive Services API-k használatával:

 - [Számítógép Vision C# gyorsútmutató](computer-vision/quickstarts/csharp.md)
 - [Szövegelemzés a Pythonnal](text-analytics/quickstarts/python.md)
 - [Face API a JavaScript](face/quickstarts/javascript.md)
