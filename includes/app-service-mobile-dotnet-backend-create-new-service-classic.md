---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179801"
---
1. Jelentkezzen be az [Azure Portalra].
2. Válassza a **+NEW** > **Web + Mobile** > **Mobile App**lehetőséget, majd adja meg a mobilalkalmazások háttérkiszolgálójának nevét.
3. Az **Erőforráscsoport**csoport ban jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat (az alkalmazás nevével megegyező néven). 
4. **Az App Service-csomag**esetében az alapértelmezett csomag (a [Standard csomagban)](https://azure.microsoft.com/pricing/details/app-service/)van kiválasztva. Kiválaszthat egy másik App Service-csomagot is, vagy [létrehozhat egy újat](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Az App Service-csomag beállításai határozzák meg az alkalmazáshoz társított [helyet, funkciókat, költségeket és számítási erőforrásokat.](https://azure.microsoft.com/pricing/details/app-service/) Ha többet szeretne megtudni az App Service-csomagokról, és arról, hogyan hozhat létre új csomagot egy másik díjcsomagban és a kívánt helyen, tekintse meg az [Azure App Service-csomagok részletes áttekintését.](../articles/app-service/overview-hosting-plans.md)
   
5. Kattintson a **Létrehozás** gombra. Ez a lépés létrehozza a mobilalkalmazások háttérvégét. 
6. Az új mobilalkalmazások **háttérrendszerének Beállítások** ablaktáblájában válassza a **Gyorsindítás** > az ügyfélalkalmazás platformján, > **adatbázis csatlakoztatása**lehetőséget. 
   
   ![Kijelölések adatbázis csatlakoztatásához](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Az **Adatkapcsolat hozzáadása** ablaktáblában válassza az SQL > Database Create a new database **(Új**adatbázis**létrehozása**) lehetőséget. Írja be az adatbázis nevét, válasszon egy tarifacsomagot, majd válassza a **Kiszolgáló**lehetőséget. Ezt az új adatbázist többször is felhasználhatja. Ha már rendelkezik adatbázissal ugyanazon a helyen, választhatja a **Meglévő adatbázis használata** lehetőséget is. Nem javasoljuk az adatbázis használatát egy másik helyen, a sávszélesség-költségek és a magasabb késés miatt.
   
   ![Adatbázis kijelölése](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Az **Új kiszolgáló** ablaktáblán írjon be egy egyedi kiszolgálónevet a **Kiszolgáló neve** mezőbe, adjon meg egy bejelentkezési nevet és egy jelszót, válassza az **Azure-szolgáltatások hozzáférésének engedélyezése a kiszolgálóhoz**lehetőséget, és válassza **az OK gombot.** Ez a lépés létrehozza az új adatbázist.
9. Az **Adatkapcsolat hozzáadása** ablaktáblában válassza a **Kapcsolati karakterlánc**lehetőséget, adja meg az adatbázis bejelentkezési és jelszóértékeit, majd kattintson az **OK**gombra. 

   A folytatás előtt várjon néhány percet, amíg az adatbázis telepítése sikeresen megtörténik.

<!-- URLs. -->
[Azure-portál]: https://portal.azure.com/
