---
title: NODE.js-alkalmazás Socket.io - Azure használatával
description: Ismerje meg, hogyan socket.io használható az Azure-platformon futó node.js-alkalmazás.
services: cloud-services
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: adegeo
ms.openlocfilehash: 0139c42334b53364aee8cfd29cfa771d47e2ccc8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>A Socket.IO Node.js csevegőalkalmazás létrehozása az Azure Cloud Service-kiszolgálón

Socket.IO biztosít a valós idejű kommunikációt a node.js-kiszolgáló és az ügyfelek között. Ez az oktatóanyag bemutatja, hogyan szoftvercsatorna birtoklás révén. IO Csevegés alkalmazás Azure-alapú. A Socket.IO további információkért lásd: [socket.io](http://socket.io).

A kész alkalmazás képernyőfelvételének alatt van:

![Az Azure-platformon futó szolgáltatás megjelenítő böngészőablak][completed-app]  

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a következő termékek és termékverziók telepítve sikeres végrehajtásához az ebben a cikkben példa:

* A [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) telepítése
* [Node.js](https://nodejs.org/download/) telepítése
* Telepítés [Python 2.7.10 verziója](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Egy Felhőszolgáltatás-projekt létrehozása
Az alábbi lépéseket a felhőszolgáltatás-projekt, amely üzemelteti a Socket.IO alkalmazás létrehozása.

1. Az a **Start menü** vagy **kezdőképernyőn**, keressen **Windows PowerShell**. Végül kattintson a jobb gombbal **Windows PowerShell** válassza **Futtatás rendszergazdaként**.
   
    ![Az Azure PowerShell ikon][powershell-menu]
2. Hozzon létre egy könyvtárat nevű **c:\\csomópont**. 
   
        PS C:\> md node
3. Lépjen a **c:\\csomópont** könyvtár
   
        PS C:\> cd node
4. Adja meg a következő parancsok futtatásával hozzon létre egy új megoldás nevű **chatapp** és a feldolgozói szerepkör nevű **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    A következő válasz jelenik meg:
   
    ![Az új-azureservice és hozzáadása azurenodeworkerrolecmdlets kimenete](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Töltse le a Csevegés – példa
Ebben a projektben a Csevegés példa fogjuk használni a [Socket.IO GitHub-tárházban]. A következő lépésekkel töltse le a példában, és adja hozzá a projekthez, korábban létrehozott.

1. A tárház helyi másolat létrehozása a **Klónozás** gombra. Is használhatja a **ZIP-** gombra kattintva töltse le a projektet.
   
   ![A böngésző ablak megjelenítése https://github.com/LearnBoost/socket.io/tree/master/examples/chat, a kiemelt ZIP letöltési ikon][chat-example-view]
2. Keresse meg a helyi tárház a könyvtárstruktúra, amíg megérkezik a a **példák\\Csevegés** könyvtár. A könyvtár tartalmának másolása a **C:\\csomópont\\chatapp\\WorkerRole1** korábban létrehozott könyvtár.
   
   ![Explorer, a példák tartalmának\\Csevegés directory kinyert az archívumban][chat-contents]
   
   A fenti képernyőfelvételen kiemelt elemeket a rendszer átmásolja a fájlokat a **példák\\Csevegés** könyvtár
3. Az a **C:\\csomópont\\chatapp\\WorkerRole1** könyvtár, törölje a **server.js** fájlt, és nevezze át a **app.js** fájl **server.js**. Ezzel eltávolítja az alapértelmezett **server.js** által korábban létrehozott fájl a **Add-AzureNodeWorkerRole** parancsmag és az alkalmazásfájl a Csevegés példa felülírja.

### <a name="modify-serverjs-and-install-modules"></a>Módosítsa a Server.js és -modulok telepítése
Mielőtt az alkalmazás tesztelése az Azure emulátorban, néhány kisebb módosításokat kell azt. Hajtsa végre a megfelelő server.js fájl az alábbi lépéseket:

1. Nyissa meg a **server.js** fájlt a Visual Studio vagy szövegszerkesztőben.
2. Keresés a **modul függőségek** server.js elején szakaszt, és módosítsa a sor tartalmazó **sio = require('..//..//lib//socket.io')** való **sio = require('socket.io')** alább látható módon:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Annak érdekében, hogy az alkalmazás a helyes portot figyeli, nyissa meg a server.js a Jegyzettömb vagy a kedvenc szerkesztőt, és módosítsa a következő sor tartományvezérlőkkel történő lecserélésével **3000** rendelkező **process.env.port** alább látható módon:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

A módosítások mentése után **server.js**, a következő lépésekkel telepítse a szükséges modulokat, és tesztelje az alkalmazás az Azure emulátorban:

1. Használatával **Azure PowerShell**, lépjen a **C:\\csomópont\\chatapp\\WorkerRole1** könyvtárra, és használja, a következő parancsot a modulok telepítése az alkalmazás számára szükséges:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Ez telepíti a package.json fájlban felsorolt modulok. A parancs befejezése után a következőhöz hasonló kimenetnek kell megjelennie:
   
   ![Az npm kimenete telepítési parancs][The-output-of-the-npm-install-command]
2. Mivel ebben a példában eredetileg a Socket.IO GitHub-tárházban része volt, és közvetlenül a Socket.IO könyvtár hivatkozik relatív elérési út, Socket.IO nem mutat hivatkozás a package.json fájlban, ezért azt telepítenie kell a következő parancsot:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Tesztelése és telepítése
1. Indítsa el az emulátorban a következő parancsot:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Ha problémák lépnek fel az emulátor, pl. indítása.: Start-AzureEmulator: nem várt hiba történt.  Részletek: Észlelt váratlan hiba a kommunikációs objektum System.ServiceModel.Channels.ServiceChannel, nem használható kommunikációra, mert Faulted állapotban van.
   
      Telepítse újra a AzureAuthoringTools v 2.7.1-es és AzureComputeEmulator v 2.7 – győződjön meg arról, hogy verziója megegyezik.
   >
   >


2. Nyisson meg egy böngészőt, és keresse meg **http://127.0.0.1**.
3. Amikor megnyílik a böngészőablakban, becenév adja meg, és nyomja le írja be.
   Ez lehetővé teszi, hogy egy adott becenév üzenetek elküldeni. Többfelhasználós működésének teszteléséhez nyissa meg az azonos URL-cím segítségével további böngészőablakot, és adjon meg másik becenevet.
   
   ![Két böngészőablakot felhasználó1, mind a Felhasználó2 üzeneteit megjelenítése](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Után az alkalmazás tesztelése, állítsa le az emulátorban a következő parancsot:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Telepítse központilag az alkalmazást, az Azure-ba, használja a **Publish-AzureServiceProject** parancsmag. Példa:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Ügyeljen arra, hogy az egyedi név, máskülönben a közzétételi folyamat meghiúsul. A telepítés befejezése után a böngésző megnyitásához, és keresse meg a telepített szolgáltatáson.
   > 
   > Ha hibaüzenet jelenik meg, amely meghatározza, hogy, hogy a megadott előfizetés nevét az importált közzétételi profil nem létezik, akkor töltse le, és az előfizetéshez, mielőtt telepítené az Azure közzétételi profil importálásához. Tekintse meg a **Azure az alkalmazás központi telepítését** szakasza [felépítéséhez és az Azure Cloud Service a Node.js-alkalmazás központi telepítése](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Az Azure-platformon futó szolgáltatás megjelenítő böngészőablak][completed-app]
   
   > [!NOTE]
   > Ha hibaüzenet jelenik meg, amely meghatározza, hogy, hogy a megadott előfizetés nevét az importált közzétételi profil nem létezik, akkor töltse le, és az előfizetéshez, mielőtt telepítené az Azure közzétételi profil importálásához. Tekintse meg a **Azure az alkalmazás központi telepítését** szakasza [felépítéséhez és az Azure Cloud Service a Node.js-alkalmazás központi telepítése](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Az alkalmazás mostantól az Azure-on fut, és továbbíthat csevegési üzeneteket a Socket.IO segítségével különböző ügyfelek között.

> [!NOTE]
> Az egyszerűség Ez a minta korlátozódik Csevegés ugyanazon csatlakozó felhasználók között. Ez azt jelenti, hogy a felhőszolgáltatás két szerepkör feldolgozópéldányok hoz létre, ha felhasználók csak tudnak megoszthatja másokkal a feldolgozói szerepkör példányt csatlakozik. Az alkalmazás működéséhez a szerepkör több példánya méretezéséhez olyan technológia, például a Service Bus segítségével a Socket.IO-állapot tárolása megosztani példányok. Tekintse meg a Service Bus-üzenetsorok és témakörök használati minták a [Azure SDK for Node.js GitHub-tárházban](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag megtanulta, hogyan hozhat létre egy Azure-Felhőszolgáltatásban tárolt alapvető Csevegés alkalmazást. Ez az alkalmazás az Azure-webhely üzemeltetésére, lásd: [összeállíthat egy Node.js Csevegés alkalmazást a Socket.IO segítségével az Azure webhelyén található][chatwebsite].

További információ: a [Node.js fejlesztői központ](/develop/nodejs/).

[chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

[Azure SLA]: http://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-tárházban]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


