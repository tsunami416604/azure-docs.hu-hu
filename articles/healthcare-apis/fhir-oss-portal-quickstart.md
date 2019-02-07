---
title: Az Azure-ban az Azure Portal – a Microsoft Healthcare API-k nyílt forráskódú FHIR-kiszolgáló központi telepítése
description: Ez a rövid útmutató azt ismerteti, hogyan telepíti a Microsoft nyílt forráskódú FHIR-kiszolgálót az Azure Portal használatával.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 2bd7a7d0e332d281a0ca9a9017219b50a3fbb472
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823496"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Gyors útmutató: Telepítse a nyílt forráskódú FHIR server az Azure portal használatával

Ebben a rövid útmutatóban fog mutatja, helyezzen üzembe egy nyílt forráskódú FHIR kiszolgálót az Azure-ban az Azure portal használatával. Ezzel az egyszerű telepítés hivatkozások a [nyílt forráskódú tárház](https://github.com/Microsoft/fhir-server)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="github-open-source-repository"></a>Nyílt forráskódú GitHub-adattár

Keresse meg a [GitHub üzembe helyezés lap](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) , és keresse meg a "Üzembe helyezése az Azure-bA" gomb:

![Nyílt forráskódú üzembe helyezés lap](media/quickstart-oss-portal/deployment-page-oss.png)

A telepítés gombra, és megnyílik az Azure portal.

## <a name="fill-in-deployment-parameters"></a>Töltse ki az üzembe helyezéshez megadott paraméterek

Válassza ki, hozzon létre egy új erőforráscsoportot, és adjon meg egy nevet. Csak más kötelező paraméter a szolgáltatás nevét.

![Egyéni üzembehelyezési paramétereket](media/quickstart-oss-portal/deployment-custom-parameters.png)

Figyelje meg, hogy az üzembe helyezés közvetlenül a nyílt forráskódú tárházban a Githubon a fogja lekérni a forráskódban. Ha a tárház elágaztatott, rámutathat a saját és a egy adott ágra.

Miután kitöltötte a részleteit, indítsa el a központi telepítést.

## <a name="validate-fhir-server-is-running"></a>Érvényesítése FHIR kiszolgálón fut.

Az üzembe helyezés befejezése után a böngészőben a mutathat `https://SERVICENAME.azurewebsites.net/metadata` beszerzése egy képesség utasítást. Egy percet vesz a kiszolgáló válaszára először vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrás. Ehhez válassza ki az üzembe helyezett erőforrásokat tartalmazó erőforráscsoportot, válassza a **erőforráscsoport törlése**, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Microsoft Open FHIR forráskiszolgálón Azure helyezte az előfizetésben helyezi. Megtudhatja, hogyan érheti el az FHIR API Postman használatával, folytassa a Postman-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[Hozzáférés FHIR API Postman használatával](access-fhir-postman-tutorial.md)