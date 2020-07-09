---
title: 'Gyors útmutató: Azure API üzembe helyezése a FHIR Azure Portal használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a FHIR, és hogyan konfigurálhatja a beállításokat a Azure Portal használatával.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e729597e9d83c4e6096fe52b577b052d94ca4799
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "84820251"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Gyors útmutató: Azure API üzembe helyezése a FHIR Azure Portal használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a FHIR a Azure Portal használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-new-resource"></a>Új erőforrás létrehozása

Nyissa meg a [Azure Portal](https://portal.azure.com) , és kattintson az **erőforrás létrehozása** elemre.

![Erőforrás létrehozása](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>A FHIR készült Azure API keresése

A FHIR készült Azure API-t a "FHIR" kifejezés beírásával keresheti meg a keresőmezőbe:

![Egészségügyi API-k keresése](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Azure API létrehozása FHIR-fiókhoz

Válassza a **Létrehozás** lehetőséget egy új Azure API FHIR-fiók létrehozásához:

![Azure API létrehozása FHIR-fiókhoz](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Adja meg a fiók adatait

Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, válassza ki a fiók nevét, és végül kattintson a **felülvizsgálat + létrehozás**gombra:

![Új egészségügyi API – részletek](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Erősítse meg a létrehozását, és várja meg a FHIR API üzembe helyezését.

## <a name="additional-settings"></a>További beállítások

Kattintson a **Tovább gombra: további beállítások** a hatóság, a célközönség, az azonosító objektumok azon azonosítóinak konfigurálásához, amelyek számára engedélyezni kell az Azure API FHIR való hozzáférését, az intelligens FHIR, ha szükséges, valamint az adatbázis átviteli sebességének konfigurálását:

- **Szolgáltató:** Különböző Azure AD-bérlőt is megadhat, ha a szolgáltatáshoz hitelesítési szolgáltatóként jelentkezett be.
- **Célközönség:** Az ajánlott eljárás és az alapértelmezett beállítás az, hogy a célközönség a FHIR-kiszolgáló URL-címére van beállítva. Itt módosíthatja. A célközönség azonosítja azt a címzettet, akinek a tokent szánták. Ebben a kontextusban úgy kell beállítani, hogy a FHIR API-t jelölő valami legyen.
- **Engedélyezett objektumazonosítók:** Megadhatja azokat az azonosító objektum-azonosítókat, amelyek számára engedélyezni szeretné az Azure API FHIR való elérését. További információt a felhasználók és a szolgáltatások objektumazonosító-AZONOSÍTÓjának megkereséséhez az [Identity Object azonosítóinak keresése](find-identity-object-ids.md) útmutatóban találhat.  
- **Intelligens a FHIR proxyn:** Engedélyezheti az intelligens szolgáltatást a FHIR proxyn. Az FHIR-proxyn keresztüli SMART konfigurálásával kapcsolatos további információkért lásd: oktatóanyag [Az Azure API for FHIR Smart on FHIR proxyn](https://docs.microsoft.com/azure/healthcare-apis/use-smart-on-fhir-proxy)  
- **Kiosztott átviteli sebesség (ru/s):** Itt megadhatja a FHIR Azure API-hoz tartozó alapul szolgáló adatbázis átviteli beállításait. Ez a beállítás később is módosítható az adatbázis panelen. További részletekért tekintse meg az [adatbázis-beállítások konfigurálása](configure-database.md) lapot.


![Engedélyezett objektumazonosítók konfigurálása](media/quickstart-paas-portal/configure-audience.png)

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API-képesség utasításának beolvasása

Annak ellenőrzéséhez, hogy az új FHIR API-fiók ki van-e építve, egy böngészőn keresztül megjelenítheti a képesség utasításait `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot, a FHIR készült Azure API-t és az összes kapcsolódó erőforrást. Ehhez válassza ki az FHIR-fiókhoz tartozó Azure API-t tartalmazó erőforráscsoportot, válassza az **erőforráscsoport törlése**elemet, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezte a FHIR készült Azure API-t az előfizetésében. Ha további beállításokat szeretne megadni a FHIR készült Azure API-ban, folytassa a további beállítások útmutatóval.

>[!div class="nextstepaction"]
>[További beállítások a FHIR készült Azure API-ban](azure-api-for-fhir-additional-settings.md)
