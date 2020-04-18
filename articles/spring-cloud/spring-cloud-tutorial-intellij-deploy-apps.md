---
title: Oktatóanyag – Az Azure Spring Cloud-alkalmazások üzembe helyezéséhez használja az IntelliJ-t
description: Az IntelliJ használatával alkalmazásokat telepíthet az Azure Spring Cloud szolgáltatásba.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: dbd0313c3edca8c5a875572fe52d7ec751ba65e8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641988"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Az Azure Spring Cloud-alkalmazások üzembe helyezéséhez használja az IntelliJ-t
Az Azure Spring Cloud IntelliJ beépülő modulja támogatja az alkalmazások telepítését az IntelliJ IDEA-ból.  

## <a name="prerequisites"></a>Előfeltételek
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA Community Edition, verzió [2020.1]](https://www.jetbrains.com/idea/download/#section=windows)
* Vagy: IntelliJ IDEA Ultimate Edition, verzió [2020.1]

## <a name="install-the-plug-in"></a>A beépülő modul beszerelése
Az IntelliJ IDEA 3.35.0 Azure Toolkit az IntelliJ **Plugins** felhasználói felületén található.

1. Indítsa el az IntelliJ-t.  Ha korábban már nyitott meg egy projektet, zárja be a projektet az üdvözlő párbeszédpanel megjelenítéséhez. Válassza a Jobb alsó hivatkozás **konfigurálása** lehetőséget, majd a **Beépülő modulok gombra** kattintva nyissa meg a beépülő modul konfigurációs párbeszédpanelét, és válassza a **Bővítmények telepítése lemezről**lehetőséget .

    ![Konfigurálás kiválasztása](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Keresse meg az Azure Toolkit for IntelliJ.Search for Azure Toolkit for IntelliJ.  Kattintson a **Telepítés gombra.**

    ![Bővítmény telepítése](media/spring-cloud-intellij-howto/install-plugin.png)

1. Kattintson **az Újraindítás AZ IDE gombra.**

## <a name="tutorial-procedures"></a>Bemutató eljárások
A következő eljárások egy Hello World alkalmazást telepítenek az IntelliJ IDEA használatával.

* Nyitott gs-tavaszi-boot projekt
* Üzembe helyezés az Azure Spring Cloud szolgáltatásban
* Adatfolyam-naplók megjelenítése

## <a name="open-gs-spring-boot-project"></a>Nyitott gs-tavaszi-boot projekt

1. Töltse le és csomagolja ki a forrás tárház a bemutató, vagy klón segítségével Git: git klónhttps://github.com/spring-guides/gs-spring-boot.git 
1. cd-t gs-spring-boot\complete.cd into gs-spring-boot\complete.
1. Nyissa meg az **Welcome** IntelliJ üdvözlőpárbeszédpanelt, és válassza a **Projekt importálása** lehetőséget az importálási varázsló megnyitásához.
1. Mappa `gs-spring-boot\complete` kijelölése.

    ![Projekt importálása](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Üzembe helyezés az Azure Spring Cloud szolgáltatásban
Az Azure-ba való üzembe helyezéshez be kell jelentkeznie az Azure-fiókjával, és ki kell választania az előfizetést.  A bejelentkezésrészleteia Telepítés és bejelentkezés című [témakörben](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in)található.

1. Kattintson a jobb gombbal a projektre az IntelliJ projektkezelőben, és válassza az **Azure** -> **Deploy to Azure Spring Cloud parancsot.**

    ![Üzembe helyezés az Azure 1-ben](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Fogadja el az alkalmazás nevét a **Név** mezőben. **A név** a konfigurációra vonatkozik, nem az alkalmazás nevére. A felhasználóknak általában nem kell módosítaniuk.
1. Fogadja el az azonosítót a **műtermék projektjéből.**
1. Válassza **az Alkalmazás:** lehetőséget, majd kattintson **az Alkalmazás létrehozása...** gombra.

    ![Üzembe helyezés az Azure 2-ben](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Írja be **az alkalmazás nevét,** majd kattintson **az OK gombra.**

    ![Üzembe helyezés az Azure-ban – RENDBEN](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Indítsa el a telepítést a **Futtatás** gombra kattintva. 

    ![Üzembe helyezés az Azure 3-ban](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. A beépülő modul `mvn package` futtatja a parancsot a projekten, majd létrehozza `package` az új alkalmazást, és telepíti a parancs által létrehozott jart.

1. Ha az alkalmazás URL-címe nem jelenik meg a kimeneti ablakban, az Azure Portalon. Navigáljon az erőforráscsoportból az Azure Spring Cloud példányába.  Ezután kattintson **az Alkalmazások**gombra.  A futó alkalmazás megjelenik a listában.

    ![Teszt URL-címének beszereznie](media/spring-cloud-intellij-howto/get-test-url.png)

1. Keresse meg az URL-címet a böngészőben.

    ![Navigálás a böngészőben 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Adatfolyam-naplók megjelenítése
Ahhoz, hogy a naplók:
1. Válassza az **Azure Explorer**, majd a **Spring Cloud**lehetőséget.
1. Kattintson a jobb gombbal a futó alkalmazásra.
1. Válassza a Legördülő listából válassza a **Streamelési naplók** lehetőséget.

    ![Streamelési naplók kiválasztása](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Jelölje ki a példányt.

    ![Példány kijelölése](media/spring-cloud-intellij-howto/select-instance.png)

1. A streamelési napló látható lesz a kimeneti ablakban.

    ![Streamelési napló kimenete](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="see-also"></a>Lásd még
* [Azure App Service-hez készült Hello World webalkalmazás létrehozása az IntelliJ használatával](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)
* [Java Spring alkalmazás előkészítése az Azure Spring Cloudban való telepítéshez](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment) 