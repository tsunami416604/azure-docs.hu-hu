<properties
   pageTitle="Az első Azure-függvény létrehozása | Microsoft Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/22/2016"
   ms.author="glenga"/>

#Az első Azure-függvény létrehozása

##Áttekintés
Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető eszköz, amely bővíti a meglévő Azure alkalmazásplatformot, lehetővé téve események által kiváltott kód megvalósítását más Azure-szolgáltatásokban, SaaS-termékekben, valamint a helyszíni rendszerekben. Az Azure Functions segítségével alkalmazásai igény szerint méretezhetők, és Ön csak a felhasznált erőforrásokért fizet. Az Azure Functions segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakör bemutatja, hogyan hozható létre az Azure Functions portálon az Azure Functions gyorsindítóval egy egyszerű „hello world” Node.js-függvény, amelyet egy HTTP-eseményindító hív meg. Az ehhez szükséges, a portálon elvégzendő lépésekről egy rövid videót is megtekinthet.

## Videó megtekintése

A következő videó bemutatja, hogyan végezheti el az ebben az oktatóprogramban található alapszintű lépéseket. 

[AZURE.VIDEO create-your-first-azure-function-simple]

##Függvény létrehozása a gyorsindítóból

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Kövesse az alábbi lépéseket az új függvényalkalmazás és az új függvény létrehozásához. Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával.

2. Adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. 

3. A **Quickstart** (Gyorsindítás) lapon kattintson a **Webhook + API** > **Create a function** (Függvény létrehozása) elemre. Létrejön egy új, előre meghatározott Node.js-függvény. 

4. (Választható lehetőség) A gyorsindító ezen pontján lehetősége van megtekinteni egy bemutatót a portál Azure Functions-szolgáltatásairól.   A bemutató megtekintése vagy kihagyása után letesztelheti az új függvényt a HTTP-eseményindítóval.

##A függvény tesztelése

Mivel az Azure Functions gyorsindítók működő kódokat tartalmaznak, az új függvények azonnal tesztelhetők.

1. A **Develop** (Fejlesztés) lapon tekintse át a **Code** (Kód) ablakot, és figyeljen arra, hogy ez a Node.js-kód egy olyan HTTP-kérést vár, amelyben a *name* (név) érték vagy az üzenet törzsében, vagy egy lekérdezési karakterláncban van továbbítva. Ha a függvény fut, a rendszer ezt az értéket a válaszüzenetben adja vissza.

2. Görgessen le a **Request body** (Kéréstörzs) szövegdobozhoz, módosítsa a *Name* (Név) tulajdonságot a saját nevére, és kattintson a **Run** (Futtatás) parancsra. Látni fogja, ahogy próba HTTP-kérés aktiválja a végrehajtást, információ íródik a streamnaplókba, és a „hello” válasz megjelenik az **Output** (Kimenet) mezőben. 

3. Ugyanannak a függvénynek egy másik böngészőablakból vagy lapról történő aktiválásához másolja ki a **Function URL** (Függvény URL) értéket a **Develop** (Fejlesztés) lapról, és illessze be egy böngészőcímsorba, majd fűzze hozzá a `&name=yourname` karakterláncértéket, és nyomja le az Enter billentyűt. Ekkor a rendszer beírója a naplókba ugyanazt az információt, és a böngésző ugyanúgy megjeleníti a „hello” választ, mint az előző esetben.

##Következő lépések

Ez a gyorsindító egy nagyon egyszerű módot mutat be egy alapszintű, HTTP-eseményindítóval aktivált függvény végrehajtására. A következő témakörökben további információkat talál arról, hogyan használhatja ki alkalmazásaiban az Azure Functions által nyújtott lehetőségeket.

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
Különböző függvénytesztelési eszközöket és technikákat ír le.
+ [Az Azure Functions méretezése](functions-scale.md)  
Részletezi az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat, köztük a dinamikus szolgáltatáscsomagot, és segít a megfelelő csomag kiválasztásában. 
+ [Az Azure App Service-ről](../app-service/app-service-value-prop-what-is.md)  
Az Azure Functions az Azure App Service platform használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika. 

[AZURE.INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



<!--HONumber=sep16_HO1-->


