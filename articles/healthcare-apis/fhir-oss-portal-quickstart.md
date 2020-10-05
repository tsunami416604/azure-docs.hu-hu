---
title: 'Azure Portal: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése az Azure-hoz – Azure API a FHIR-hez'
description: Ez a rövid útmutató azt ismerteti, hogyan helyezhetők üzembe a Microsoft nyílt forráskódú FHIR-kiszolgáló a Azure Portal használatával.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90978599"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Gyors útmutató: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése Azure Portal használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy nyílt forráskódú FHIR-kiszolgálót az Azure-ban a Azure Portal használatával. Az egyszerű üzembe helyezési hivatkozásokat a [nyílt forráskódú tárházban](https://github.com/Microsoft/fhir-server) fogjuk használni

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="github-open-source-repository"></a>GitHub nyílt forráskódú adattár

Navigáljon a [GitHub-üzembe helyezés lapra](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) , és keresse meg az "üzembe helyezés az Azure-ban" gombokat:

>[!div class="mx-imgBorder"]
>![Nyílt forráskódú üzembe helyezési oldal](media/quickstart-oss-portal/deployment-page-oss.png)

Kattintson a telepítés gombra, és megnyílik a Azure Portal.

## <a name="fill-in-deployment-parameters"></a>Adja meg a telepítési paramétereket

Hozzon létre egy új erőforráscsoportot, és adjon meg egy nevet. Csak más kötelező paraméterek szerepelnek a szolgáltatásban és az SQL-rendszergazdai jelszóban.

>[!div class="mx-imgBorder"]
>![Egyéni telepítési paraméterek](media/quickstart-oss-portal/deployment-custom-parameters.png)

A részletek kitöltése után elindíthatja az üzemelő példányt.

## <a name="validate-fhir-server-is-running"></a>A FHIR-kiszolgáló futtatásának ellenőrzése

Az üzembe helyezés befejezése után rámutathat a böngészőre, `https://SERVICENAME.azurewebsites.net/metadata` hogy beszerezzen egy képességi utasítást. Egy percet vesz igénybe, hogy a kiszolgáló első alkalommal válaszoljon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki a kiépített erőforrásokat tartalmazó erőforráscsoportot, válassza az **erőforráscsoport törlése**elemet, és erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezte az Azure-hoz készült Microsoft nyílt forráskódú FHIR-kiszolgálót az előfizetésében. Ha szeretné megtudni, hogyan érheti el a FHIR API-t a Poster használatával, folytassa a Poster-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[FHIR API elérése a Poster használatával](access-fhir-postman-tutorial.md)