---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7c994f85d90e94d514bb4e4f91a6644ed45432c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66455197"
---
1. Töltse le az ügyfél SDK rövid útmutatók, a következő platformokhoz:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Ha az iOS-projekt le kell töltenie "azuresdk – iOS -\*.zip" a [legújabb GitHub-kiadás](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Csomagolja ki, és adja hozzá a `MicrosoftAzureMobile.framework` fájlt a projekt gyökérkönyvtárában.
    >

2. Adatbázis-kapcsolat hozzáadása vagy meglévő kapcsolattal csatlakozni kell. Először határozza meg, hogy lesz egy adattár létrehozása vagy használjon egy meglévőt.

    - **Új adattár létrehozása**: Ha egy adattár létrehozása, használja a következő rövid útmutatót:

        [Rövid útmutató: Ismerkedés az Azure SQL Database önálló adatbázisok](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Meglévő adatforrás**: Kövesse az alábbi utasításokat, ha azt szeretné használni a meglévő adatbázis-kapcsolat

        1. Az SQL adatbázis-kapcsolati karakterlánc-formátum – `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Nevét, a kiszolgáló ez tekintheti meg az adatbázis áttekintő oldala, és általában "server_name.database.windows.net" formájában.
            **{port}**  általában 1433.
            **{your_catalogue}**  Az adatbázis nevét.
            **{your_username}**  Felhasználónevet az adatbázis eléréséhez.
            **{your_password}**  Az adatbázis elérésére szolgáló jelszó.

            [További információ az SQL-kapcsolati karakterlánc-formátum](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Adja hozzá a kapcsolati karakterláncot a **mobilalkalmazás** az App Service-ben, kapcsolati karakterláncok az alkalmazás használatával kezelheti a **konfigurációs** lehetőséget a menüben.

            Kapcsolati karakterlánc hozzáadása:

            1. Kattintson a **Alkalmazásbeállítások** fülre.

            2. Kattintson a **[+] új kapcsolati karakterlánc**.

            3. Meg kell adnia **neve**, **érték** és **típus** a kapcsolati karakterlánc.

            4. Típus **neve** , `MS_TableConnectionString`

            5. Értéknek kell lennie, mielőtt lépésben kialakított a karakterláncban.

            6. Válasszon egy kapcsolati karakterláncot egy SQL Azure adatbázishoz hozzáadásakor **Rendszerkarbantartás** alatt **típus**.

3. Az Azure Mobile Apps SDK-k a Node.js és a .NET-háttérrendszerek rendelkezik.

   - **Node.js háttérrendszer**
    
     Ha Node.js rövid útmutatójában található alkalmazás használatához, kövesse az alábbi utasításokat.

     1. Az Azure Portalon lépjen a **könnyen kezelhető táblák**, ezt a képernyőt.
      
        ![Könnyen kezelhető táblák csomópont](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Ellenőrizze, hogy az SQL-kapcsolati sztring már meg van adva az az **konfigurációs** fülre. Ezután jelölje be **tudomásul veszem, hogy ezzel felülírja az összes hely tartalmának** , és kattintson a **TodoItem tábla létrehozása** gombra.
     
        ![Könnyen kezelhető táblák csomópont-konfiguráció](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. A **könnyen kezelhető táblák**, kattintson a **+ Hozzáadás** gombra.
    
        ![Könnyen kezelhető táblák csomópont hozzáadása gomb](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Hozzon létre egy `TodoItem` névtelen hozzáférés tartalmazó tábla.
      
        ![Könnyen kezelhető táblák csomópont tábla hozzáadása](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET-háttérrendszer**
    
        Ha .NET gyorsindítási alkalmazást használni, kövesse az alábbi utasításokat.

        1. Töltse le az Azure Mobile Apps .NET kiszolgálói projektet, a a [azure-mobile-alkalmazások – gyors útmutatók tárház](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Hozza létre a .NET server projektet helyileg a Visual Studióban.

        3. A Visual Studióban nyissa meg a Megoldáskezelőben kattintson a jobb gombbal a `ZUMOAPPNAMEService` projektre, kattintson a **közzététel**, látni fogja a `Publish to App Service` ablak. Ha Mac rendszeren dolgozik, tekintse meg az egyéb módokon üzembe helyezni az alkalmazást [Itt](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![A Visual studio közzététel](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Válassza ki **App Service-ben** megfelelően cél közzététele, majd kattintson **meglévő**, majd kattintson a **közzététel** gombra az ablak alján.

        5. Jelentkezzen be a Visual Studio az Azure-előfizetésében először kell. Válassza ki a `Subscription`, `Resource Group`, majd válassza ki az alkalmazás nevére. Amikor elkészült, kattintson a **OK**, ez lesz helyi üzembe helyezés a .NET kiszolgálói projektet, hogy az éppen illeszkedik az App Service-ben. Az üzembe helyezést, a rendszer átirányítja `http://{zumoappname}.azurewebsites.net/` a böngészőben.
        
           ![Háttérbeli működik](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
