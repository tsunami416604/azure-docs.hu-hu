---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461836"
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
     
        1. Új API létrehozása – Módosíthatja közvetlenül az Azure Portalon, vagy módosíthatja a kódot helyileg a fejlesztői környezetben, majd közzéteheti az Azure-ban. Kattintson `App Service Editor (Preview)` az `Development Tools` alatt a menüre, amely böngészőben szerkeszti az alkalmazáskódot.
        
        2. Kattintson, `Go` és ha az App Service Editor megnyílik, akkor teljes mértékben ellenőrzése alatt a forráskódot. Feltételezve, hogy már telepítette az express és az azure-mobile-apps csomagot az npm install paranccsal, kattintson a WWWROOT api mappára az egyéni API létrehozásához vagy szerkesztéséhez. Módosítsa a kódfájlt, és a módosítások automatikusan mentésre kerülnek.
        
        3. Teljes hozzáférése van az alkalmazásadatok tárolására használt Azure SQL Database-hez. Az adatbázisban egyszerűen hozhat létre új táblákat.
 
   - **.NET-háttérrendszer**
    
        Ha a .NET gyorsindítási alkalmazást szeretné használni, kövesse az alábbi utasításokat.

        1. Töltse le az Azure Mobile Apps .NET server projektet az [azure-mobile-apps-quickstarts tárházból.](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)

        2. A .NET kiszolgálóprojekt helyi létrehozása a Visual Studióban.

        3. A Visual Studio programban nyissa meg `ZUMOAPPNAMEService` a Megoldáskezelőt, kattintson `Publish to App Service` a jobb gombbal a projektre, kattintson a Közzététel **parancsra,** és megjelenik egy ablak. Ha macen dolgozik, itt más módon is telepítheti az [alkalmazást.](https://docs.microsoft.com/azure/app-service/deploy-local-git)
        
           ![Vizuális stúdió közzététele](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Válassza **az App Service** közzétételi célként lehetőséget, majd kattintson a Meglévő **kijelölése**gombra, majd kattintson az ablak alján található **Közzététel** gombra.

        5. Először be kell jelentkeznie a Visual Studióba az Azure-előfizetésével. Jelölje `Subscription`ki `Resource Group`a , majd az alkalmazás nevét. Ha készen áll, kattintson az **OK**gombra, és ez telepíti a helyileg telepített .NET kiszolgálóprojektet az App Service háttérrendszerébe. Amikor a telepítés befejeződik, a `http://{zumoappname}.azurewebsites.net/` böngészőben átirányítjuk.                   
