---
title: 'Azure Portal: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése az Azure-hoz – Azure API a FHIR-hez'
description: Ez a rövid útmutató azt ismerteti, hogyan helyezhetők üzembe a Microsoft nyílt forráskódú FHIR-kiszolgáló a Azure Portal használatával.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5109c9a7c6432e42c6841b89cc28bde3e92c74aa
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "84820185"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Gyors útmutató: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése Azure Portal használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy nyílt forráskódú FHIR-kiszolgálót az Azure-ban a Azure Portal használatával. Az egyszerű üzembe helyezési hivatkozásokat a [nyílt forráskódú tárházban](https://github.com/Microsoft/fhir-server) fogjuk használni

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="github-open-source-repository"></a>GitHub nyílt forráskódú adattár

Navigáljon a [GitHub-üzembe helyezés lapra](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) , és keresse meg az "üzembe helyezés az Azure-ban" gombokat:

![Nyílt forráskódú üzembe helyezési oldal](media/quickstart-oss-portal/deployment-page-oss.png)

Kattintson a telepítés gombra, és megnyílik a Azure Portal.

## <a name="fill-in-deployment-parameters"></a>Adja meg a telepítési paramétereket

Hozzon létre egy új erőforráscsoportot, és adjon meg egy nevet. Csak más kötelező paraméter a szolgáltatás neve.

![Egyéni telepítési paraméterek](media/quickstart-oss-portal/deployment-custom-parameters.png)

Figyelje meg, hogy a telepítés közvetlenül a GitHubon lévő nyílt forráskódú tárházból fogja lekérni a forráskódot. Ha a tárházat összeállította, a saját és egy adott ág számára is rámutathat.

A részletek kitöltése után elindíthatja az üzemelő példányt.

## <a name="validate-fhir-server-is-running"></a>A FHIR-kiszolgáló futtatásának ellenőrzése

Az üzembe helyezés befejezése után rámutathat a böngészőre, `https://SERVICENAME.azurewebsites.net/metadata` hogy beszerezzen egy képességi utasítást. Egy percet vesz igénybe, hogy a kiszolgáló első alkalommal válaszoljon.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki a kiépített erőforrásokat tartalmazó erőforráscsoportot, válassza az **erőforráscsoport törlése**elemet, és erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezte az Azure-hoz készült Microsoft nyílt forráskódú FHIR-kiszolgálót az előfizetésében. Ha szeretné megtudni, hogyan érheti el a FHIR API-t a Poster használatával, folytassa a Poster-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[FHIR API elérése a Poster használatával](access-fhir-postman-tutorial.md)