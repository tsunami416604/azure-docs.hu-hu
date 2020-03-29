---
title: Node.js alkalmazás Socket.io használatával - Azure
description: Ismerje meg, hogyan használhatja socket.io az Azure-ban üzemeltetett node.js alkalmazásban.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360769"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Node.js csevegőalkalmazás létrehozása Socket.IO egy Azure Cloud-szolgáltatásban

Socket.IO valós idejű kommunikációt biztosít a node.js kiszolgáló és az ügyfelek között. Ez az oktatóanyag végigvezeti a foglalat üzemeltetésén. Io-alapú csevegőalkalmazás az Azure-ban. A Socket.IO vonatkozó további információt a socket.io című témakörben [talál.](https://socket.io)

A kitöltött alkalmazás ról képernyőkép az alábbi:

![Az Azure-ban üzemeltetett szolgáltatást megjelenítő böngészőablak][completed-app]  

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a cikkben szereplő példa sikeres befejezéséhez telepítve vannak a következő termékek és verziók:

* A [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) telepítése
* [Node.js](https://nodejs.org/download/) telepítése
* [Python 2.7.10-es verziójának](https://www.python.org/) telepítése

## <a name="create-a-cloud-service-project"></a>Felhőszolgáltatási projekt létrehozása
A következő lépések létre hozzák a felhőszolgáltatás-projektet, amely a Socket.IO alkalmazás üzemeltetéséhez.

1. A **Start menüben** vagy a **Kezdőképernyőn**keresse meg a **Windows PowerShell parancsot.** Végül kattintson a jobb gombbal a **Windows PowerShell** elemre, és válassza a **Futtatás rendszergazdaként parancsot.**
   
    ![Az Azure PowerShell ikonja][powershell-menu]
2. Hozzon létre egy **\\c: csomópont nevű könyvtárat.** 
   
        PS C:\> md node
3. Könyvtárak módosítása **a c:\\csomópont** könyvtárra
   
        PS C:\> cd node
4. Írja be a következő parancsokat egy **chatapp** nevű új megoldás és egy **WorkerRole1**nevű feldolgozói szerepkör létrehozásához:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    A következő választ fogja látni:
   
    ![Az új azureservice és add-azurenodeworkerroledlets kimenete](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>A csevegési példa letöltése
Ehhez a projekthez a [Socket.IO GitHub-tárházból]származó csevegési példát fogjuk használni. Hajtsa végre az alábbi lépéseket a példa letöltéséhez és hozzáadásához a korábban létrehozott projekthez.

1. Hozzon létre egy helyi másolatot a tárház segítségével a **Klónozás** gombra. A **ZIP** gombbal is letöltheti a projektet.
   
   ![Böngészőablak megtekintése https://github.com/LearnBoost/socket.io/tree/master/examples/chat, kiemelve a ZIP letöltési ikonnal](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigáljon a könyvtár szerkezete a helyi tárház, amíg meg nem érkezik a **példák\\chat** könyvtárba. Másolja a könyvtár tartalmát a korábban létrehozott **C:\\node\\chatapp\\WorkerRole1** könyvtárba.
   
   ![Explorer, az archívumból kinyert csevegőkönyvtár tartalmának\\megjelenítése][chat-contents]
   
   A kiemelt elemek a fenti képernyőképen a **fájlokat\\** másolt a példák chat könyvtár
3. A **C:\\\\chatapp\\WorkerRole1** könyvtárban törölje a **server.js** fájlt, majd nevezze át az **app.js** fájlt **kiszolgáló.js fájlra.** Ezzel eltávolítja az **Add-AzureNodeWorkerRole** parancsmag által korábban létrehozott alapértelmezett **server.js** fájlt, és lecseréli azt a csevegési példából származó alkalmazásfájlra.

### <a name="modify-serverjs-and-install-modules"></a>Server.js és modulok telepítése módosítása
Az alkalmazás tesztelése előtt az Azure-emulátor, néhány kisebb módosításokat kell végrehajtania. Hajtsa végre a kiszolgáló.js fájl következő lépéseit:

1. Nyissa meg a **server.js** fájlt a Visual Studióban vagy bármely szövegszerkesztőben.
2. Keresse meg a **modulfüggőségek szakaszt** a server.js elején, és módosítsa a **sio = require('. //.. lib//socket.io')** és **sio = require('socket.io')** között az alábbiak szerint:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Annak érdekében, hogy az alkalmazás a megfelelő porton figyelje a figyelést, nyissa meg a server.js-t a Jegyzettömbben vagy a kedvenc szerkesztőjében, majd módosítsa a következő sort a **3000-es** verziónak a **process.env.port** ra történő cseréjével az alábbi módon:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

A **server.js módosításai**mentése után a következő lépésekkel telepítse a szükséges modulokat, majd tesztelje az alkalmazást az Azure-emulátorban:

1. Az **Azure PowerShell**használatával módosítsa a könyvtárakat a **C:\\node\\chatapp\\WorkerRole1** könyvtárra, és a következő paranccsal telepítse az alkalmazás által igényelt modulokat:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Ez telepíti a package.json fájlban felsorolt modulokat. A parancs végrehajtásának befejeződése után a következőhöz hasonló kimenetnek kell megjelennie:
   
   ![Az npm install parancs kimenete][The-output-of-the-npm-install-command]
2. Mivel ez a példa eredetileg a Socket.IO GitHub-tárház része volt, és közvetlenül hivatkozott a Socket.IO könyvtárra relatív elérési út szerint, Socket.IO nem hivatkoztak a package.json fájlban, ezért a következő parancs kiadásával kell telepítenünk:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Tesztelés és üzembe helyezés
1. Indítsa el az emulátort a következő parancs kiadásával:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Ha problémákat tapasztal az emulátor elindításával kapcsolatban, pl.: Start-AzureEmulator : Váratlan hiba történt.  Részletek: Váratlan hiba történt: A System.ServiceModel.Channels.ServiceChannel kommunikációs objektum nem használható kommunikációra, mert hibás állapotban van.
   > 
   > Telepítse újra az AzureAuthoringTools 2.7.1-es verzióját és az AzureComputeEmulator 2.7-es verzióját – győződjön meg arról, hogy a verzió egyezik.

2. Nyisson meg egy **http://127.0.0.1**böngészőt, és keresse meg a it.
3. Amikor megnyílik a böngészőablak, írjon be egy becenevet, majd nyomja meg az Enter billentyűt.
   Ez lehetővé teszi, hogy üzeneteket küldjön egy adott becenévként. A többfelhasználós funkciók teszteléséhez nyisson meg további böngészőablakokat ugyanazzal az URL-címmel, és adjon meg különböző beceneveket.
   
   ![Két böngésző ablak, amely a User1 és a User2 csevegési üzeneteit jeleníti meg](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Az alkalmazás tesztelése után állítsa le az emulátort a következő parancs kiadásával:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Az alkalmazás azure-ba való üzembe helyezéséhez használja a **Publish-AzureServiceProject** parancsmag. Példa:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Ügyeljen arra, hogy egyedi nevet használjon, különben a közzétételi folyamat sikertelen lesz. A központi telepítés befejezése után a böngésző megnyílik, és a telepített szolgáltatásra navigál.
   > 
   > Ha hibaüzenetet kap arról, hogy a megadott előfizetési név nem létezik az importált közzétételi profilban, le kell töltenie és importálnia kell az előfizetés közzétételi profilját, mielőtt az Azure-ba telepítene. Tekintse meg **az alkalmazás üzembe helyezése az Azure-ba** [szakasza build és üzembe helyezett egy Node.js alkalmazás egy Azure Felhőszolgáltatás](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Az Azure-ban üzemeltetett szolgáltatást megjelenítő böngészőablak][completed-app]
   
   > [!NOTE]
   > Ha hibaüzenetet kap arról, hogy a megadott előfizetési név nem létezik az importált közzétételi profilban, le kell töltenie és importálnia kell az előfizetés közzétételi profilját, mielőtt az Azure-ba telepítene. Tekintse meg **az alkalmazás üzembe helyezése az Azure-ba** [szakasza build és üzembe helyezett egy Node.js alkalmazás egy Azure Felhőszolgáltatás](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Az alkalmazás most már fut az Azure-ban, és továbbíthatja a csevegési üzeneteket a különböző ügyfelek Socket.IO használatával.

> [!NOTE]
> Az egyszerűség kedvéért ez a minta az ugyanahhoz a példányhoz csatlakozó felhasználók közötti csevegésre korlátozódik. Ez azt jelenti, hogy ha a felhőszolgáltatás két feldolgozói szerepkör-példányt hoz létre, a felhasználók csak az ugyanahhoz a feldolgozói szerepkör-példányhoz kapcsolódó többiekkel cseveghetnek. Az alkalmazás méretezése több szerepkörpéldányok, használhat egy technológiát, például a Service Bus a Socket.IO áruház állapotának megosztására példányok között. Példák: A Service Bus-várólisták és a témakörök használati minták az [Azure SDK node.js GitHub-tárház.](https://github.com/WindowsAzure/azure-sdk-for-node)
> 
> 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Azure Cloud Service-ben üzemeltetett alapvető csevegőalkalmazást. Ha tudni szeretné, hogyan üzemeltethet iappegy Azure-webhelyen, olvassa [el a Node.js csevegőalkalmazás létrehozása Socket.IO egy Azure-webhelyen című témakört.][chatwebsite]

További információt a [Node.js Fejlesztői központban](https://docs.microsoft.com/azure/javascript/)talál.

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-tárház]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png





