---
title: Az Azure - a Socket.io használata node.js-alkalmazás
description: Ismerje meg, hogy a socket.io használata az Azure-ban üzemeltetett node.js-alkalmazásokban.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: 0fae47f248d5662b69a0d1a12c82b7ded33badd6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001983"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>A Socket.IO kódtár használatával Node.js-csevegőalkalmazás létrehozásához, egy Azure-Felhőszolgáltatás

A Socket.IO összetevő biztosítja a valós idejű kommunikációt a node.js-kiszolgáló és az ügyfelek között. Ez az oktatóanyag végigvezeti egy szoftvercsatorna üzemeltetéséhez. I/o-alapú csevegőalkalmazás létrehozásához az Azure-ban. A Socket.IO kódtár további információkért lásd: [a Socket.IO kódtár](http://socket.io).

Az elkészült alkalmazás képernyőképe nem éri el:

![Az Azure-ban üzemeltetett szolgáltatás böngészőablak][completed-app]  

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a következő termékek és termékverziók telepítve a példában ez a cikk a sikeres végrehajtásához:

* A [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) telepítése
* [Node.js](https://nodejs.org/download/) telepítése
* Telepítés [Python 2.7.10 verzió](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Egy Felhőszolgáltatás-projekt létrehozása
Az alábbi lépéseket a felhőszolgáltatás-projekt, amely üzemelteti a Socket.IO-alkalmazás létrehozása.

1. Az a **Start menü** vagy **kezdőképernyőjén**, keressen **Windows PowerShell**. Végül kattintson a jobb gombbal **Windows PowerShell** válassza **Futtatás rendszergazdaként**.
   
    ![Az Azure PowerShell ikon][powershell-menu]
2. Hozzon létre egy könyvtárat nevű **c:\\csomópont**. 
   
        PS C:\> md node
3. Lépjen a **c:\\csomópont** könyvtár
   
        PS C:\> cd node
4. Adja meg a következő parancsok futtatásával hozzon létre egy új megoldás nevű **chatapp** és a egy feldolgozói szerepkörben nevű **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    A következő válasz jelenik meg:
   
    ![Az új-azureservice és hozzáadása azurenodeworkerrolecmdlets kimenete](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>A Csevegés példa letöltése
Ehhez a projekthez a csevegési példa az ezzel a [a Socket.IO kódtár GitHub-adattár]. Hajtsa végre az alábbi lépéseket a példában töltse le, és adja hozzá a korábban létrehozott projekt.

1. A tárház helyi példányának létrehozása használatával a **Klónozás** gombra. Is használhatja a **ZIP** gombra kattintva töltse le a projektet.
   
   ![A böngésző ablakában megtekintését https://github.com/LearnBoost/socket.io/tree/master/examples/chat, a kiemelt ZIP letöltése ikon][chat-example-view]
2. Keresse meg a helyi tárház a könyvtárstruktúra érkeznek, amíg a **példák\\Csevegés** könyvtár. Ez a könyvtár tartalmának másolása a **C:\\csomópont\\chatapp\\WorkerRole1** korábban létrehozott könyvtár.
   
   ![Explorer, a példák tartalmának\\az archívum kinyert Csevegés könyvtár][chat-contents]
   
   A fenti képernyőfelvételen kiemelt elemeket is átmásolja a fájlokat a **példák\\Csevegés** könyvtár
3. A a **C:\\csomópont\\chatapp\\WorkerRole1** könyvtár, törölje a **server.js** fájlt, és nevezze át a **app.js** a fájl **server.js**. Ezzel eltávolítja az alapértelmezett **server.js** által korábban létrehozott fájl a **Add-AzureNodeWorkerRole** parancsmagot, és lecseréli a csevegési példából származó alkalmazás-fájllal.

### <a name="modify-serverjs-and-install-modules"></a>Módosítsa a Server.js és -modulok telepítése
Az alkalmazás tesztelése az Azure-emulátorban, előtt bizonyos kisebb módosításokat kell azt. Hajtsa végre az alábbi lépéseket a server.js fájlhoz:

1. Nyissa meg a **server.js** fájlt a Visual Studio vagy bármilyen szövegszerkesztőben.
2. Keresés a **modul függőségek** server.js elején szakaszt, és módosítsa a sor tartalmazó **sio = require('..//..//lib//socket.io')** való **sio = require('socket.io')** alább látható módon:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Annak érdekében, hogy az alkalmazás megfelelő portot figyeli, nyissa meg a server.js a Jegyzettömb vagy a kedvenc szerkesztőjében, és módosítsa a következő sort lecserélésével **3000** a **process.env.port** alább látható módon:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

A módosítások mentése után **server.js**, kövesse az alábbi lépéseket a szükséges modulok telepítéséhez, és tesztelje az alkalmazást az Azure emulatorban:

1. Használatával **Azure PowerShell-lel**, lépjen a **C:\\csomópont\\chatapp\\WorkerRole1** könyvtárra, és használja, a következő parancsot a modulok telepítéséhez az alkalmazás számára szükséges:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Ez telepíti a modulokat a package.json fájlban felsorolt. A parancs befejeződése után az alábbihoz hasonló kimenetnek kell megjelennie:
   
   ![A kimenete az npm install parancs][The-output-of-the-npm-install-command]
2. Mivel ebben a példában eredetileg a Socket.IO GitHub-adattár egy része volt, és közvetlenül a relatív elérési út a Socket.IO kódtár hivatkozik, a Socket.IO kódtár nem hivatkozik a package.json fájlt, hogy telepítse a következő parancsot a:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Tesztelhet és helyezhet üzembe
1. Indítsa el az emulátorban a következő parancsot:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Ha az emulátorban, például indításával problémák merülnek fel.: Start-AzureEmulator: Váratlan hiba történt.  Részletek: Észlelt váratlan hiba a kommunikációs objektumot System.ServiceModel.Channels.ServiceChannel, nem használható a kommunikációhoz mert Faulted állapotban van.
   
      Telepítse újra a AzureAuthoringTools v 2.7.1-es verziójához és AzureComputeEmulator v 2.7 – győződjön meg arról, hogy a verzió megegyezik.
   >
   >


2. Nyisson meg egy böngészőt, és navigáljon a **http://127.0.0.1**.
3. Amikor a böngészőablakban megnyitja, adjon meg egy becenevet, és nyomja le adja.
   Ez lehetővé teszi, hogy egy adott becenév, üzeneteket tehet közzé. Többfelhasználós működésének teszteléséhez nyissa meg az ugyanazon URL-cím használatával további böngészőablakot, és adja meg a különböző beceneveinek.
   
   ![Két böngészőablakot a felhasználó1, felhasználó2 üzeneteit megjelenítése](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Az alkalmazás tesztelése után állítsa le az emulátorban a következő parancs kiadása:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Az üzembe helyezése az Azure-ba, használja a **Publish-AzureServiceProject** parancsmagot. Példa:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Ügyeljen arra, hogy adjon meg egy egyedi nevet, ellenkező esetben a közzétételi folyamat meghiúsul. Az üzembe helyezés befejeztével a böngészőben nyissa meg, és keresse meg a telepített szolgáltatáson.
   > 
   > Ha hibaüzenet jelenik meg, amely megállapítja, hogy a megadott előfizetés neve nem szerepel az importált közzétételi profilt, le kell töltenie és előtt üzembe helyezni az Azure-előfizetéséhez tartozó közzétételi profil importálásához. Tekintse meg a **üzembe helyezése az Azure-ban az alkalmazás** szakaszában [hozhat létre, és az Azure Cloud Service Node.js-alkalmazás üzembe helyezése](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Az Azure-ban üzemeltetett szolgáltatás böngészőablak][completed-app]
   
   > [!NOTE]
   > Ha hibaüzenet jelenik meg, amely megállapítja, hogy a megadott előfizetés neve nem szerepel az importált közzétételi profilt, le kell töltenie és előtt üzembe helyezni az Azure-előfizetéséhez tartozó közzétételi profil importálásához. Tekintse meg a **üzembe helyezése az Azure-ban az alkalmazás** szakaszában [hozhat létre, és az Azure Cloud Service Node.js-alkalmazás üzembe helyezése](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Az alkalmazás most már fut az Azure-ban, és továbbíthat az csevegési üzeneteket a Socket.IO kódtár használatával különböző ügyfelek között.

> [!NOTE]
> Az egyszerűség kedvéért a minta korlátozódik Csevegés ugyanazon csatlakozó felhasználók között. Ez azt jelenti, hogy ha a felhőszolgáltatás létrehoz két feldolgozói szerepkör példányai, felhasználók csak tudják Csevegés csatlakozik a feldolgozói szerepkör ugyanazon másokkal. A szolgáltatásalkalmazás alkalmassá tétele több szerepkörpéldány vertikális felskálázásához olyan technológia, például a Service Bus segítségével megoszthatja a Socket.IO összetevő-állapot tárolása a példányok között. Példák: a Service Bus-üzenetsorok és témakörök használati minták a [Azure SDK for Node.js GitHub-adattár](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan lehet egy Azure-Felhőszolgáltatásban lévő üzemeltetett alapszintű csevegőalkalmazás létrehozása a. Bemutatja, hogyan működtethető az Azure-webhelyen lévő alkalmazás kapcsolatban lásd: [hozhat létre a Node.js-csevegőalkalmazás Socket.IO segítségével az Azure-webhelyek][chatwebsite].

További információ: a [Node.js fejlesztői központ](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: http://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[A Socket.IO kódtár GitHub-adattár]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


