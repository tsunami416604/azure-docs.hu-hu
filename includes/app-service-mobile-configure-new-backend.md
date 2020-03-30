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
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440220"
---
1. Töltse le az ügyfél SDK-rövid útmutatóit a következő platformokra:
    
    [iOS (C célkitűzés)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Ha az iOS-projektet használja, le kell töltenie\*az "azuresdk-iOS- .zip" kifejezést a [legújabb GitHub-kiadásból.](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest) Csomagolja ki `MicrosoftAzureMobile.framework` és adja hozzá a fájlt a projekt gyökéréhez.
    >

2. Hozzá kell adnia egy adatbázis-kapcsolatot, vagy csatlakoznia kell egy meglévő kapcsolathoz. Először határozza meg, hogy létrehoz-e egy adattatot, vagy egy meglévőt használ.

    - **Új adattár létrehozása:** Ha adattáratot szeretne létrehozni, használja az alábbi rövid útmutatót:

        [Rövid útmutató: Egyetlen adatbázis – első lépések az Azure SQL Database-ben](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Meglévő adatforrás**: Ha meglévő adatbázis-kapcsolatot szeretne használni, kövesse az alábbi utasításokat.

        1. SQL Adatbázis kapcsolati karakterlánca -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** A kiszolgáló neve, ez megtalálható az adatbázis áttekintő lapján, és általában "server_name.database.windows.net" formában van.
            **{port}** általában 1433.
            **{your_catalogue}** Az adatbázis neve.
            **{your_username}** Felhasználónév az adatbázis eléréséhez.
            **{your_password}** Jelszó az adatbázis eléréséhez.

            [További információ az SQL Connection String formátumról](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Adja hozzá a kapcsolati karakterláncot a **mobilalkalmazáshoz** az App Service szolgáltatásban, a menü **Konfiguráció beállításával** kezelheti az alkalmazás kapcsolati karakterláncait.

            Kapcsolati karakterlánc hozzáadása:

            1. Kattintson az **Alkalmazás beállításai** fülre.

            2. Kattintson **a [+] Új kapcsolati karakterláncra**.

            3. Meg kell **adnia a név,** **az érték** és a **típus** a kapcsolati karakterlánchoz.

            4. **Típus neve** másként`MS_TableConnectionString`

            5. Az értéknek a lépésben korábban létrehozott összekötő karakterláncnak kell lennie.

            6. Ha kapcsolati karakterláncot ad hozzá egy SQL Azure-adatbázishoz, válassza az **SQLAzure** lehetőséget **a típus**alatt.

3. Az Azure Mobile Apps SDK-kat tartalmaz a .NET és a Node.js háttérrendszerekhez.

   - **Node.js háttér**
    
     Ha a Node.js rövid útmutatót szeretné használni, kövesse az alábbi utasításokat.

     1. Az Azure Portalon nyissa meg az **Egyszerű táblázatok**, ekkor jelenik meg ez a képernyő.
      
        ![Egyszerű csomóponttáblák](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Győződjön meg arról, hogy az SQL-kapcsolati karakterlánc már hozzá van adva a **Konfiguráció** lapon. Ezután jelölje be az **I nyugtája, hogy ez felülírja** az összes webhely tartalmát, és kattintson a **TodoItem tábla létrehozása** gombra.
     
        ![Csomópont–egyszerű táblák konfigurációja](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Az **Egyszerű táblázatok ban**kattintson a + **Hozzáadás** gombra.
    
        ![Csomópont egyszerű asztalok hozzáadása gomb](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Hozzon `TodoItem` létre egy névtelen hozzáférésű táblát.
      
        ![Csomópont egyszerű táblák hozzáadása táblázat](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET-háttérrendszer**
    
        Ha a .NET gyorsindítási alkalmazást szeretné használni, kövesse az alábbi utasításokat.

        1. Töltse le az Azure Mobile Apps .NET server projektet az [azure-mobile-apps-quickstarts tárházból.](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)

        2. A .NET kiszolgálóprojekt helyi létrehozása a Visual Studióban.

        3. A Visual Studio programban nyissa meg `ZUMOAPPNAMEService` a Megoldáskezelőt, kattintson `Publish to App Service` a jobb gombbal a projektre, kattintson a Közzététel **parancsra,** és megjelenik egy ablak. Ha macen dolgozik, itt más módon is telepítheti az [alkalmazást.](https://docs.microsoft.com/azure/app-service/deploy-local-git)
        
           ![Vizuális stúdió közzététele](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Válassza **az App Service** közzétételi célként lehetőséget, majd kattintson a Meglévő **kijelölése**gombra, majd kattintson az ablak alján található **Közzététel** gombra.

        5. Először be kell jelentkeznie a Visual Studióba az Azure-előfizetésével. Jelölje `Subscription`ki `Resource Group`a , majd az alkalmazás nevét. Ha készen áll, kattintson az **OK**gombra, és ez telepíti a helyileg telepített .NET kiszolgálóprojektet az App Service háttérrendszerébe. Amikor a telepítés befejeződik, a `http://{zumoappname}.azurewebsites.net/` böngészőben átirányítjuk.                   