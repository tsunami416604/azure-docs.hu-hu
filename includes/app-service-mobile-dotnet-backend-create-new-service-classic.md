---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729407"
---
1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **+ új** > **Web + mobil** > **mobilalkalmazás**, majd adja meg egy nevet a Mobile Apps-háttéralkalmazás számára.
3. A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot vagy hozzon létre egy újat (az alkalmazás nevének használatával). 
4. A **App Service-csomag**, az alapértelmezett csomag (az a [Standard szintű](https://azure.microsoft.com/pricing/details/app-service/)) van kiválasztva. Kiválaszthat egy másik App Service-csomagot is, vagy [létrehozhat egy újat](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Az App Service-csomag beállításai határozzák meg a [helyét, a szolgáltatások, a költségek és a számítási erőforrások](https://azure.microsoft.com/pricing/details/app-service/) az alkalmazáshoz társított. További információt az App Service-ben csomagok és a egy új csomag létrehozása egy másik tarifacsomagban réteg és a kívánt helyen, lásd: [Azure App Service díjcsomagjainak részletes áttekintése](../articles/app-service/overview-hosting-plans.md).
   
5. Kattintson a **Létrehozás** gombra. Ebben a lépésben létrehozza a Mobile Apps-háttéralkalmazást. 
6. Az a **beállítások** panelen az új Mobile Apps háttéralkalmazáshoz, válassza ki **gyors üzembe helyezési** > saját ügyfélalkalmazás-platformjára > **adatbázis csatlakoztatása**. 
   
   ![Csatlakozás egy adatbázishoz szolgáló kiválasztások](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Az a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **SQL Database** > **hozzon létre egy új adatbázist**. Adja meg az adatbázis nevét, válasszon egy tarifacsomagot, és válassza **kiszolgáló**. Ezt az új adatbázist többször is felhasználhatja. Ha már rendelkezik adatbázissal ugyanazon a helyen, választhatja a **Meglévő adatbázis használata** lehetőséget is. A másik helyet, mert sávszélességhez kötődő költségekkel és nagyobb késleltetéssel járhat az adatbázis használata nem ajánlott.
   
   ![Adatbázis kiválasztása](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Az a **új kiszolgáló** panelen adjon meg egy egyedi kiszolgálónevet a a **kiszolgálónév** adja meg a felhasználónevét és jelszavát, válassza ki a **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének**, Válasszaki **OK**. Ebben a lépésben létrehozza az új adatbázist.
9. Térjen vissza a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **kapcsolati karakterlánc**, adja meg a felhasználónevet és jelszót értékeket az adatbázis, és válassza ki **OK**. 

   Várjon néhány percet, amíg az adatbázis sikeresen telepített, akkor a folytatás előtt.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
