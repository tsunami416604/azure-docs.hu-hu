---
title: "Az első függvény létrehozása az Azure-ban a Visual Studio használatával | Microsoft Docs"
description: "Hozzon létre és tegyen közzé egy egyszerű, HTTP-triggert használó függvényt az Azure-ban az Azure Functions Tools for Visual Studio használatával."
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
editor: 
tags: 
keywords: "azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 016179372d69dc63f5e5226723d87ac6e74b31fd
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="create-your-first-function-using-visual-studio"></a>Az első függvény létrehozása a Visual Studio használatával

Az Azure Functions lehetővé teszi, hogy a kód végrehajtása egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) környezetben anélkül, hogy először hozzon létre egy virtuális Gépet, vagy tegye közzé a webalkalmazást.

Ebben a témakörben elsajátíthatja, hogyan a Visual Studio 2017 eszközök, az Azure Functions segítségével hozza létre, és a "hello world" függvény helyi tesztelése. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök a Visual Studio 2017 15.3-as és újabb verziójában található Azure-fejlesztési számítási feladat részeként érhetők el.

![Azure-függvénykód Visual Studio-projektben](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [A Visual Studio 2017 verzió 15.3](https://www.visualstudio.com/vs/preview/) vagy újabb verzióját, beleértve a **Azure fejlesztési** munkaterhelés.

    ![Az Azure-fejlesztési számítási feladatot is tartalmazó Visual Studio 2017 telepítése](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)
    
[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-install-note.md)] 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Hozzon létre egy Azure Functions-projektet a Visual Studióban

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Most, hogy már létrehozta a projektet, hozza létre első függvényét.

## <a name="create-the-function"></a>A függvény létrehozása

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Add** (Hozzáadás)  > **New Item** (Új elem) lehetőséget. Válassza ki az **Azure Function** (Azure-függvény) elemet, majd kattintson az **Add** (Hozzáadás) lehetőségre.

2. Válassza ki a **HttpTrigger** elemet, adja meg a **Function Name** (Függvénynév) értékét, az **Access Rights** (Hozzáférési jog) lehetőségnél válassza az **Anonymous** (Névtelen) elemet, majd kattintson a **Create** (Létrehozás) lehetőségre. A létrehozott függvény HTTP-kéréssel bármely ügyfél részéről hozzáférhető. 

    ![Új Azure-függvény létrehozása](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

    A forráskód fájlja kerül a projekt, amely tartalmaz egy osztály, amely megvalósítja a funkciókódot. Ez a kód egy sablon, amely fogad egy név-érték és vissza Echok alapul. A **függvénynév** attribútum állítja be a függvény nevét. A **HttpTrigger** attribútum azt jelöli, az üzenet, amely elindítja a függvényt. 

    ![Függvény kódfájl](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

Most, hogy már létrehozta a HTTP-triggerrel aktivált függvényt, tesztelheti a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.  

1. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez.  Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.  

    ![Az Azure helyi futtatókörnyezete](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési karakterláncot, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. A hibakeresés leállításához kattintson a **Stop** (Leállítás) gombra a Visual Studio eszköztárában.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. Közvetlenül a Visual Studióból is létrehozhat függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel. Ahogyan korábban, most is az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet.

    A HTTP-triggert használó függvényt meghívó URL-cím így néz ki:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbiakban látható a böngészőben a távoli GET kérelemre a függvény által visszaadott válasz: 

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>Következő lépések

A Visual Studio segítéségével létrehozott egy egyszerű, HTTP-triggerrel aktivált függvényt tartalmazó C#-függvényalkalmazást. 

+ A projekt más típusú eseményindítók és kötések támogatására történő konfigurálásával az [Azure Functions Tools for Visual Studio](functions-develop-vs.md) [projekt helyi fejlesztésekhez való konfigurálásával](functions-develop-vs.md#configure-the-project-for-local-development) kapcsolatos szakaszában ismerkedhet meg.
+ További információ az Azure Functions Core Tools használatával végzett helyi tesztelésről és hibakeresésről: [Code and test Azure Functions locally](functions-run-local.md) (Az Azure-függvények kódolása és helyi tesztelése). 
+ A függvények .NET-osztálytárakként való fejlesztéséről további információért lásd [a .NET-osztálytárak és az Azure Functions használatát](functions-dotnet-class-library.md) ismertető cikket. 

