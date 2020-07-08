---
title: Azure-függvény létrehozása Javával és IntelliJ
description: Megtudhatja, hogyan hozhat létre és tehet közzé egy egyszerű, HTTP-alapú, kiszolgáló nélküli alkalmazást az Azure-ban Java és IntelliJ használatával.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7ced455e8124abed75dc7b2bbf7f92eb13613347
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556582"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Az első Azure-függvény létrehozása Javával és IntelliJ

Ez a cikk:
- [Kiszolgáló](https://azure.microsoft.com/overview/serverless-computing/) nélküli Function projekt létrehozása a IntelliJ IDEA használatával
- Az integrált fejlesztési környezet (IDE) függvényének tesztelésére és hibakeresésére szolgáló lépések a saját számítógépén
- Útmutatás a függvény projekt Azure Functions való üzembe helyezéséhez

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java-és IntelliJ-függvények fejlesztéséhez telepítse a következő szoftvereket:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Egy [Azure által támogatott Java Development Kit (JDK)](https://aka.ms/azure-jdks) a Java 8 rendszerhez
+ Telepített [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition vagy Community Edition
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ A legújabb [Function Core-eszközök](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Telepítés és bejelentkezés

1. Az IntelliJ IDEA beállításainak párbeszédpanelén (Ctrl+Alt+S) válassza a **Beépülő modulok** lehetőséget. Ezután keresse meg **az Azure Toolkit for IntelliJ-t** a **Marketplace**-en és kattintson a **Telepítés** elemre. A telepítést követően kattintson az **Újraindítás** elemre a beépülő modul aktiválásához. 

    ![Azure Toolkit for IntelliJ beépülő modul a Marketplace-en][marketplace]

2. Az Azure-fiókjába való bejelentkezéshez nyissa meg az **Azure Explorer** oldalsávot, majd kattintson az **Azure-bejelentkezés** ikonra a felső sávban (vagy az IDEA-menüben: **Eszközök/Azure/Azure-bejelentkezés**).
    ![Az IntelliJ Azure Sign In (Azure-bejelentkezés) parancsa][intellij-azure-login]

3. Az **Azure-bejelentkezés** ablakban válassza a **Bejelentkezés az eszközön** lehetőséget, majd kattintson a **Bejelentkezés** elemre ([egyéb bejelentkezési lehetőségek](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Az Azure bejelentkezési ablaka a kiválasztott Bejelentkezés az eszközön lehetőséggel][intellij-azure-popup]

4. Kattintson a **Copy&Open** (Másolás és megnyitás) elemre az **Azure Device Login** (Azure-bejelentkezés az eszközön) párbeszédpanelen.

   ![Az Azure bejelentkezési párbeszédpanele][intellij-azure-copycode]

5. A böngészőben illessze be az eszköz kódját (amelyet a rendszer az utolsó lépésben **Copy&Open** (Másolás és megnyitás) lehetőségre kattintáskor kimásolt), majd kattintson a **Next** (Tovább) gombra.

   ![Az eszközről történő bejelentkezéshez használt böngésző][intellij-azure-link-ms-account]

6. A **Előfizetések kiválasztása** párbeszédpanelen válassza ki a használni kívánt előfizetést, majd kattintson az **OK** gombra.

   ![Az Előfizetések kiválasztása párbeszédpanel][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Azure Toolkit for IntelliJ segítségével létrehoz egy helyi Azure Functions projektet. A cikk későbbi részében a függvény kódját közzé fogja tenni az Azure-ban. 

1. Nyissa meg a IntelliJ üdvözlő párbeszédpaneljét, válassza az *új projekt létrehozása* elemet új projekt varázsló megnyitásához, majd válassza a *Azure functions*lehetőséget.

    ![Functions-projekt létrehozása](media/functions-create-first-java-intellij/create-functions-project.png)

1. Válassza a *http-trigger*lehetőséget, majd kattintson a *tovább* gombra, és kövesse a varázslót a következő lapokon található összes konfiguráció átadásához. erősítse meg a projekt helyét, majd kattintson a *Befejezés*gombra. A Intellj ÖTLETE ekkor megnyitja az új projektet.

    ![Functions-projekt létrehozása – Befejezés](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>A függvényalkalmazás helyi futtatása

1. A kód megjelenítéséhez keresse meg `src/main/java/org/example/functions/HttpTriggerFunction.java` a generált kódot. A *17*. sorban láthatja, hogy van egy zöld *Futtatás* gomb, kattintson rá, és válassza az *"Azure-Function-vizsga..." futtatása*lehetőséget, majd láthatja, hogy a Function alkalmazás helyileg fut néhány naplóval.

    ![Helyi futtatási függvények projekt](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Helyi futtatási függvények kimenete](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. A függvényt úgy is kipróbálhatja, hogy a nyomtatott végpontot a böngészőből éri el, például: `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Helyi futtatási függvények teszt eredménye](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. A napló kinyomtatása is megtörténik az ÖTLETében, most a *Leállítás* gombra kattintva állítsa le a függvényt.

    ![Helyi futtatási függvények tesztelési naplója](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>A függvényalkalmazás helyi hibakeresése

1. Most próbálkozzon a függvényalkalmazás helyi hibakeresésével, kattintson a *hibakeresés* gombra az eszköztáron (ha nem jelenik meg, kattintson a *Megtekintés – > megjelenés – > eszköztár* az eszköztár engedélyezéséhez).

    ![Helyi hibakeresési függvények gomb](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. A Töréspont hozzáadásához kattintson a fájl *20* . sorára `src/main/java/org/example/functions/HttpTriggerFunction.java` , majd nyissa meg újra a végpontot, és `http://localhost:7071/api/HttpTrigger-Java?name=Azure` Keresse meg a töréspontot, és próbálja ki a további hibakeresési funkciókat, például a *Step*, a *Watch*, a *kiértékelés*. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra.

    ![Helyi hibakeresési függvények megszakítása](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>A függvényalkalmazás üzembe helyezése az Azure-ban

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, válassza az *Azure-> üzembe helyezés lehetőséget Azure functions*

    ![Függvények üzembe helyezése az Azure-ban](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Ha még nincs függvényalkalmazás, kattintson *a nem elérhető függvény lehetőségre, és kattintson az új létrehozása*elemre.

    ![Függvények üzembe helyezése az Azure Create app szolgáltatásban](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Írja be a Function alkalmazás nevét, és válassza a megfelelő előfizetés/platform/erőforráscsoport/App Service terv lehetőséget, itt létrehozhat egy erőforráscsoport/App Service tervet is. Ezután az Alkalmazásbeállítások változatlanok maradnak, kattintson az *OK* gombra, és várjon néhány percet az új függvény létrehozásához. Az *új Függvényalkalmazás létrehozása* után a folyamatjelző sáv eltűnik.

    ![Függvények üzembe helyezése az Azure-alkalmazás létrehozása varázslóban](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Válassza ki azt a Function alkalmazást, amelyet központilag telepíteni szeretne, (az imént létrehozott új Function alkalmazás automatikusan ki lesz választva). A függvények telepítéséhez kattintson a *Futtatás* gombra.

    ![Függvények üzembe helyezése az Azure Run szolgáltatásban](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Függvények üzembe helyezése az Azure log-ban](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Azure Functions kezelése az IDEA-ból

1. A függvények az *Azure Explorerrel* is kezelhetők a saját ötletében, kattintson a *függvényalkalmazásre*, majd az összes funkció itt látható.

    ![Függvények megtekintése az Explorerben](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Kattintással válassza ki az egyik függvényt, és kattintson a jobb gombbal a *Tulajdonságok megjelenítése* elemre a Részletek lap megnyitásához. 

    ![Függvények tulajdonságainak megjelenítése](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Kattintson a jobb gombbal a függvény *HttpTrigger-Java*elemre, és válassza az *trigger függvény*lehetőséget, majd láthatja, hogy a böngésző megnyílik az trigger URL-címével.

    ![Függvények üzembe helyezése az Azure Run szolgáltatásban](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>További függvények hozzáadása a projekthez

1. Kattintson a jobb gombbal a csomag *org. example. functions* elemre, és válassza a *New-> Azure functions osztály*elemet. 

    ![Függvények hozzáadása a projekt bejegyzéséhez](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Töltse ki az osztály neve *HttpTest* , és válassza a *HttpTrigger* elemet a Function osztály létrehozása varázslóban, kattintson az *OK* gombra a létrehozáshoz, így új függvényeket hozhat létre.

    ![Függvények hozzáadása a projekthez az trigger kiválasztása](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Függvények hozzáadása a projekt kimenetéhez](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Függvények tisztítása

1. Azure Functions törlése az Azure Explorerben
      
      ![Függvények hozzáadása a projekthez az trigger kiválasztása](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>További lépések

Létrehozott egy Java functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
