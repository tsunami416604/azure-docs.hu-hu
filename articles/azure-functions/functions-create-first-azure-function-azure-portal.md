<properties
   pageTitle="Függvény létrehozása az Azure Portal használatával | Microsoft Azure"
   description="Első Azure-függvényét, egy kiszolgáló nélküli alkalmazást, kevesebb mint két perc alatt építhet fel."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="glenga"/>

#Függvény létrehozása az Azure Portal használatával

##Áttekintés
Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető eszköz, amely bővíti a meglévő Azure alkalmazásplatformot, lehetővé téve események által kiváltott kód megvalósítását más Azure-szolgáltatásokban, SaaS-termékekben, valamint a helyszíni rendszerekben. Az Azure Functions segítségével alkalmazásai igény szerint méretezhetők, és Ön csak a felhasznált erőforrásokért fizet. Az Azure Functions segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakör bemutatja, hogyan hozható létre az Azure Portal használatával egy egyszerű „hello world” Node.js-alapú Azure-függvény, amelyet egy HTTP-eseményindító hív meg. Mielőtt függvényeket hozhatna létre az Azure Portalon, explicit módon létre kell hoznia egy függvényalkalmazást az Azure App Service-ben. Ha szeretné, hogy a függvényalkalmazás automatikusan jöjjön létre, tekintse meg [az Azure Functions gyors üzembe helyezéséről szóló másik oktatóanyagot](functions-create-first-azure-function.md), amely egyszerűbb gyors üzembe helyezést biztosít, és egy videót is tartalmaz.

##Függvényalkalmazás létrehozása

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Kövesse az alábbi lépéseket egy függvényalkalmazás létrehozásához az Azure Portalon.

Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be az Azure-fiókjával.

2. Kattintson az **+Új** > **Web + mobil** > **Függvényalkalmazás** elemre, válassza ki az **Előfizetés** típusát, írjon be egy egyedi (**Alkalmazásnevet**) a függvényalkalmazás azonosításához, majd adja meg az alábbi beállításokat:

    + **[Erőforráscsoport](../azure-portal/resource-group-portal.md/)**: Válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak. Választhat meglévő erőforráscsoportot is, viszont így előfordulhat, hogy nem tud majd dinamikus App Service-csomagot létrehozni a függvényalkalmazáshoz.
    + **[App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**: Válasszon a *dinamikus* és a *klasszikus* lehetőség közül. 
        + **Dinamikus**: Az Azure Functions alapértelmezett csomagtípusa. Ha dinamikus csomagot választ, ki kell választania a **Helyet** is, és meg kell adnia a **Lefoglalt memóriát** (MB-ban). A memóriafoglalás költségekre gyakorolt hatásáról az[Azure Functions díjszabását ismertető dokumentumban](https://azure.microsoft.com/pricing/details/functions/) talál további információt. 
        + **Klasszikus**: A klasszikus App Service-csomag esetében létre kell hoznia egy **App Service-csomagot/-helyet **, vagy ki kell választania egy meglévőt. Ezek a beállítások határozzák meg az alkalmazáshoz társított [helyet, szolgáltatásokat, költségeket és számítási erőforrásokat](https://azure.microsoft.com/pricing/details/app-service/).  
    + **Tárfiók**: Minden függvényalkalmazáshoz szükség van egy tárfiókra. Választhat meglévő tárfiókot, vagy létrehozhat egy újat. 

    ![Új függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

3. Kattintson a **Létrehozás** elemre az új függvényalkalmazás létrehozásához és üzembe helyezéséhez.  

Most, hogy létrejött a függvényalkalmazás, létrehozhatja az első függvényét.

## Függvény létrehozása

Az alábbi lépések az Azure Functions első lépéseivel hoznak létre egy függvényt.

1. Az **Első lépések** lapon kattintson a **Webhook + API** és a **JavaScript** elemre, majd kattintson a **Függvény létrehozása** parancsra. Létrejön egy új, előre meghatározott Node.js-függvény. 

    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (Választható lehetőség) A gyorsindító ezen pontján lehetősége van megtekinteni egy bemutatót a portál Azure Functions-szolgáltatásairól.   A bemutató megtekintése vagy kihagyása után letesztelheti az új függvényt a HTTP-eseményindítóval.

##A függvény tesztelése

Mivel az Azure Functions gyorsindítók működő kódokat tartalmaznak, az új függvények azonnal tesztelhetők.

1. A **Develop** (Fejlesztés) lapon tekintse át a **Code** (Kód) ablakot, és figyeljen arra, hogy ez a Node.js-kód egy olyan HTTP-kérést vár, amelyben a *name* (név) érték vagy az üzenet törzsében, vagy egy lekérdezési karakterláncban van továbbítva. Ha a függvény fut, a rendszer ezt az értéket a válaszüzenetben adja vissza.

    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)

2. Görgessen le a **Request body** (Kéréstörzs) szövegdobozhoz, módosítsa a *Name* (Név) tulajdonságot a saját nevére, és kattintson a **Run** (Futtatás) parancsra. Látni fogja, ahogy egy próba HTTP-kérés aktiválja a végrehajtást, információ íródik a streamnaplókba, és a „hello” válasz megjelenik a **Kimenet** mezőben. 

3. Ugyanannak a függvénynek egy másik böngészőablakból vagy lapról történő aktiválásához másolja ki a **Function URL** (Függvény URL) értéket a **Develop** (Fejlesztés) lapról, és illessze be egy böngészőcímsorba, majd fűzze hozzá a `&name=yourname` karakterláncértéket, és nyomja le az Enter billentyűt. Ekkor a rendszer beírója a naplókba ugyanazt az információt, és a böngésző ugyanúgy megjeleníti a „hello” választ, mint az előző esetben.

##Következő lépések

Ez a gyorsindító egy nagyon egyszerű módot mutat be egy alapszintű, HTTP-eseményindítóval aktivált függvény végrehajtására. A következő témakörökben további információkat talál arról, hogyan használhatja fel alkalmazásaiban az Azure Functions által nyújtott lehetőségeket.

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
Különböző függvénytesztelési eszközöket és technikákat ír le.
+ [Az Azure Functions méretezése](functions-scale.md)  
Részletezi az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat, köztük a dinamikus szolgáltatáscsomagot, és segít a megfelelő csomag kiválasztásában. 
+ [Az Azure App Service-ről](../app-service/app-service-value-prop-what-is.md)  
Az Azure Functions az Azure App Service platform használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika. 

[AZURE.INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



<!--HONumber=sep12_HO2-->


