---
title: Üzembe helyezése az Azure API FHIR az Azure Portal használatával
description: Az Azure API telepítése FHIR az Azure Portal használatával.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 8699abfa8cc9b6675c15c8cd9b7cbb5bb5e148cd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823501"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Gyors útmutató: Üzembe helyezése az Azure API FHIR Azure portal használatával

Ebben a rövid útmutatóban üzembe helyezése az Azure API az Azure Portallal FHIR fog elsajátíthatja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-new-resource"></a>Új erőforrás létrehozása

Nyissa meg a [Azure betekintő portál](https://preview.portal.azure.com) kattintson **erőforrás létrehozása**

![Erőforrás létrehozása](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Az Azure API FHIR keresése

Az Azure API FHIR szöveget a keresőmezőbe írja be a "FHIR" találja:

![Egészségügyi API-k keresése](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Az Azure API FHIR-fiók létrehozása

Válassza ki **létrehozás** FHIR-fiók egy új Azure API létrehozásához:

![Az Azure API FHIR-fiók létrehozása](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Adja meg a fiók adatai

Válasszon ki egy meglévő erőforráscsoportot vagy hozzon létre egy új, válasszon egy nevet a fiókhoz, és végül kattintson **felülvizsgálat + létrehozása**:

![Új egészségügyi api-részletek](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Erősítse meg a létrehozási, és await FHIR API üzembe helyezés.

## <a name="additional-settings"></a>További beállítások

Kattintson a **tovább: További beállítások** identitás objektumazonosítók engedélyezni kell az Azure API eléréséhez a FHIR konfigurálása:

![Engedélyezett objektumazonosítók konfigurálása](media/quickstart-paas-portal/configure-allowed-oids.png)

Lásd: [megkeresése az identitás objektumazonosítójának](find-identity-object-ids.md) részleteket identitás keresse meg az objektum azonosítóját a felhasználók számára, és egyszerű szolgáltatások.

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API képesség utasítás beolvasása

Ellenőrizze, hogy az új FHIR API-fiók van kiépítve, beolvasni egy képesség utasítást egy böngészőben a `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata`.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot, az Azure API-FHIR és minden kapcsolódó erőforrás. Ehhez válassza ki az erőforráscsoportot, amely FHIR-fiók, jelölje be az Azure API **erőforráscsoport törlése**, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure API helyezte a FHIR az előfizetésben helyezi. Megtudhatja, hogyan érheti el az FHIR API Postman használatával, folytassa a Postman-oktatóanyaggal.

>[!div class="nextstepaction"]
>[Hozzáférés FHIR API Postman használatával](access-fhir-postman-tutorial.md)