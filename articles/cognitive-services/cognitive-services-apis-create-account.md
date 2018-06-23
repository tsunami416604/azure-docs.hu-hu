---
title: Kognitív szolgáltatások API-fiók létrehozása az Azure portálon |} Microsoft Docs
description: Hogyan Microsoft kognitív szolgáltatások API-fiók létrehozása az Azure portálon.
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
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347747"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Kognitív szolgáltatások API-fiók létrehozása az Azure-portálon

Microsoft kognitív Service API-k használatához először hozzon létre egy fiókot az Azure portálon.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

2. Kattintson a **+ hozzon létre egy erőforrást**.

3. A Azure piactérről, válassza a **AI + kognitív szolgáltatások** és elérhető API-kat listájának felderítése. Kattintson a **láthatja az összes** kognitív szolgáltatások API-k teljes listájának megtekintéséhez. Kattintson az API-t az Ön által választott, a folytatáshoz.

    ![Válassza ki a kognitív szolgáltatások API-k](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Az a **létrehozása** lapján adja meg a következő információkat:

   - **Fiók neve:** a fiók nevét. Azt javasoljuk, egy leíró nevet, például *AFaceAPIAccount*.

   - **Előfizetés:** jelöljön ki egy, amelyből van legalább elérhető Azure-előfizetések közreműködői engedélyekkel.

   - **API-típus:** válassza ki a használni kívánt kognitív Services API. A különböző kognitív szolgáltatások elérhető API-kat kapcsolatos további információkért látogasson el a [kognitív szolgáltatások](https://azure.microsoft.com/services/cognitive-services/) hely.

   - **A tarifacsomag:** a tényleges használatát és a kiválasztott lehetőségektől függ a kognitív Services-fiókhoz költségét. Minden API árazással kapcsolatos további információkért tekintse meg a [lapok árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Erőforráscsoport:** egy erőforráscsoportot az azonos életciklusát, engedélyek és házirendek erőforrások gyűjteménye. További tudnivalók az erőforráscsoportokról, [kezelése Azure-erőforrások portálon keresztül](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Az erőforráscsoport helye:** Ez elengedhetetlen, csak ha a kiválasztott API globális (helyre nem kötelező). Ha az API-t globális, és nem kötött helyre, azonban meg kell adnia egy helyét a kognitív szolgáltatások API fiókhoz társított metaadatokat tartalmazó ahhoz az erőforráscsoporthoz. Ezen a helyen nincs hatással van a futásidejű rendelkezésre állását a fiókját. Erőforráscsoport kapcsolatos további információkért lásd: [kezelése Azure-erőforrások portálon keresztül](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Online szolgáltatási feltételek kifejezetten nyugtázniuk:** ahhoz, hogy hozzon létre egy fiókot, előfizetésnél tulajdonos vagy közreműködő szerepkörrel rendelkező személyek (által definiált [átruházásához hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)) kell kifejezetten elismeri a feltételeket, amelyek a kognitív szolgáltatásaira vonatkoznak [Online szolgáltatási feltételek](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Az előfizetés tulajdonosa lehet tiltani egy adott erőforráscsoportban vagy előfizetést kognitív szolgáltatási fiókja létrehozásának [Azure házirendek](../azure-policy/azure-policy-introduction.md) a cikk következő [hozzárendelésére és kezelésére használ Azure-portálon erőforrás-házirendek](../azure-policy/assign-policy-definition.md) és a "nem engedélyezett erőforrástípusok" házirend-definíció rendelni, illetve megadó **Microsoft.CognitiveServices/accounts** a célként megadott erőforrás típusa.

     Ha a fiók létrehozása le lett tiltva, a következő hiba a fiókok létrehozásának időpontjában fog megjelenni:

     ![Fióklétrehozási hiba](media/cognitive-services-apis-create-account/error-message.png)

5. Rögzíti a fiókot az Azure portál Irányítópultjára, kattintson a **rögzítés az irányítópulton**.

6. A fiók létrehozásához kattintson a **Létrehozás** gombra.

Után a kognitív Services-fiók telepítése sikeres volt, kattintson a csempére az irányítópulton a fiók adatainak megtekintéséhez.

Használhatja a **végponti URL-cím** a a **áttekintése** szakasz és a kulcsot a **kulcsok** gombra API szakasz meghívja az alkalmazásokban.

![Fiók információk megjelenítése](media/cognitive-services-apis-create-account/display-account.png)

![Megjelenítési kulcsait](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>További lépések

Az összes Microsoft kognitív az elérhető szolgáltatások kapcsolatos további információkért lásd: [kognitív szolgáltatások](https://azure.microsoft.com/services/cognitive-services/).

A gyors üzembe helyezési útmutatók néhány példa kognitív szolgáltatások API-k használata:

 - [Számítógép stratégiai C# – gyorsútmutatók](/computer-vision/quickstarts/csharp.md)
 - [Python Szövegelemzések](/text-analytics/quickstarts/python.md)
 - [JavaScript API felület](/face/quickstarts/javascript.md)
