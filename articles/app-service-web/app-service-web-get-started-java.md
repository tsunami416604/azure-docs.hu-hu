---
title: "Hozza létre az első Java-webappját öt perc alatt az Azure-ban | Microsoft Docs"
description: "Egy egyszerű Java-alkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webalkalmazások futtatása az App Service-ben."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: cephalin;robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 75e51ca45a899c6b6fa123346aa3c5860fd1600d
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Hozza létre az első Java-webappját öt perc alatt az Azure-ban

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Ez a rövid útmutató segítséget nyújt az első Java-webalkalmazás mindössze néhány perc alatt történő üzembe helyezéséhez az [Azure App Service](../app-service/app-service-value-prop-what-is.md) használatával. Az oktatóanyag végére egy egyszerű, Java-alapú működő webalkalmazás válik elérhetővé a felhőben.

![Webalkalmazás kikeresése](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag bemutatja, hogyan hozhat létre és helyezhet üzembe Java-webalkalmazást az Azure-ban a Java EE-fejlesztőknek készült Eclipse IDE használatával. Ha az Eclipse még nincs telepítve, ingyenesen letöltheti a http://www.eclipse.org/ webhelyről.

A Java-webalkalmazások Azure-ban való közzétételének leegyszerűsítése érdekében az oktatóanyagban ismertetett lépésekben az [Eclipse-hez készült Azure-eszközkészletet](/azure/azure-toolkit-for-eclipse) használjuk. Az eszközkészlet telepítésével kapcsolatos útmutatásért lásd [az Eclipse-hez készült Azure-eszközkészlet telepítésével](/azure/azure-toolkit-for-eclipse-installation) foglalkozó cikket.

> [!NOTE]
>
> Emellett a JetBrains által kifejlesztett [IntelliJ IDEA](https://www.jetbrains.com/idea/) is használható az oktatóanyagban ismertetett lépések végrehajtására. A lépések némelyike ebben a fejlesztői környezetben eltérhet, bár létezik egy, [az IntelliJ-hez készült Azure-eszközkészlet](/azure/azure-toolkit-for-intellij) is, amellyel leegyszerűsítheti a közzétételi folyamatot az adott IDE-ben.
>

Az oktatóanyagban található lépések elvégzéséhez egy Azure-előfizetésre is szüksége lesz. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Dinamikus webes projekt létrehozása az Eclipse-ben

Az Eclipse-ben válassza a **File** (Fájl) menüt, majd a **New** (Új), végül pedig a **Dynamic Web Project** (Dinamikus webes projekt) lehetőséget.

A **New Dynamic Web Project** (Új dinamikus webprojekt) párbeszédpanelen adja a **MyFirstJavaOnAzureWebApp** nevet a projektnek, és válassza a **Finish** (Befejezés) lehetőséget.
   
![Dinamikus webes projekt párbeszédpanel](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

> [!NOTE]
>
> Ha egy helyi futtatókörnyezet, például az [Apache Tomcat](https://tomcat.apache.org/) telepítve van, megadhatja azt a **Target runtime** (Cél futtatókörnyezet) mezőben.
>

Miután a dinamikus webes projekt létrejött, adjon hozzá egy új JSP-oldalt a projektböngészőben. Bontsa ki a projektet, a jobb gombbal kattintson a **WebContent** mappára, majd kattintson a **New** (Új), végül pedig a **JSP File** (JSP fájl) elemre.

![New JSP File (Új JSP-fájl) menü](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

Amikor megjelenik a New JSP File (Új JSP fájl) párbeszédpanel, adja a fájlnak az **index.jsp** nevet, a szülőmappa neve maradjon **MyFirstJavaOnAzureWebApp/WebContent**, majd kattintson a **Next** (Tovább) gombra.

![New JSP File (Új JSP-fájl) párbeszédpanel](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

A New JSP File (Új JSP-fájl) párbeszédpanel második oldalán nevezze el a fájlt **index.jsp** néven, a szülőmappa neve maradjon **MyFirstJavaOnAzureWebApp/WebContent**, majd kattintson a **Finish** (Befejezés) gombra.

![New JSP File (Új JSP-fájl) párbeszédpanel](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-2.png)

Amikor az új oldal megnyílik az Eclipse-ben, a meglévő `<body></body>` szakasz helyébe illessze az alábbi kódot:

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Mentse az oldal módosításait.

## <a name="publish-your-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

A webalkalmazások üzembe helyezéséhez az Azure-ban az Eclipse-hez készült Azure-eszközkészlet több szolgáltatását is igénybe vesszük.

A közzétételi folyamat indításához használja az alábbi módszerek egyikét:

* Kattintson a jobb gombbal a projektre az Eclipse **projektböngészőjében**, majd kattintson az **Azure**, végül pedig a **Publish as Azure Web App** (Közzététel Azure-webalkalmazásként) lehetőségre.

   ![A Publish as Azure Web App (Közzététel Azure-webalkalmazásként) helyi menü](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

* Kattintson a **közzétételi** ikonra az Eclipse eszköztárjában, majd a **Publish as Azure Web App** (Közzététel Azure-webalkalmazásként) elemre.

   ![A Publish as Azure Web App (Közzététel Azure-webalkalmazásként) lenyíló menü](./media/app-service-web-get-started-java/publish-as-azure-web-app-drop-down-menu.png)

Ha még nem jelentkezett be Azure-fiókjába, a rendszer felszólítja, hogy jelentkezzen be. Ehhez a következőket kell tennie:

1. Két különböző lehetőség is rendelkezésre áll az Azure-fiókba való bejelentkezéshez. A jelen oktatóanyag esetében válassza az **Interaktív** lehetőséget.

   ![Azure bejelentkezési párbeszédpanel](./media/app-service-web-get-started-java/azure-signin-dialog-box.png)

1. Írja be Azure-beli hitelesítő adatait, majd kattintson a **Sign In** (Bejelentkezés) gombra.

   ![Azure bejelentkezési párbeszédpanel](./media/app-service-web-get-started-java/azure-login-dialog-box.png)

1. Válassza ki Azure-előfizetéseit, majd kattintson a **Select** (Kijelölés) elemre.

   ![Azure bejelentkezési párbeszédpanel](./media/app-service-web-get-started-java/select-azure-subscriptions-dialog-box.png)

> [!NOTE]
>
> Az **Interaktív** és az **Automatizált** bejelentkezéssel kapcsolatos részletes útmutatásokat az [Azure az Eclipse-hez készült Azure-eszközkészletre vonatkozó bejelentkezési utasításait leíró](https://go.microsoft.com/fwlink/?linkid=846174) cikkben olvashatja.
>

Miután bejelentkezett Azure-fiókjába, megjelenik a **Deploy Web App** (Webalkalmazás üzembe helyezése) párbeszédpanel. Ha most először tesz közzé webalkalmazást az Azure-ban, az App Services lista üres lesz. Ebben az esetben, vagy ha egy új App Service-szolgáltatást szeretne létrehozni, a következő lépés egy új App Service létrehozása. Ehhez kattintson a **Create** (Létrehozás) gombra.

![Deploy Web App (Webalkalmazás üzembe helyezése) párbeszédpanel](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

Amikor megjelenik az **Create App Service** (App Service létrehozása) párbeszédpanel, a következő induló adatokat kell megadnia:

* A webalkalmazás egyedi neve, amely a webalkalmazás DNS-címe lesz. Ha például a **MyJavaWebApp** nevet adja meg, a cím a *myjavawebapp.azurewebsites.net* lesz.

* A webalkalmazás által használt webes tároló. Például: **Newest Tomcat 8.5** (Legújabb Tomcat 8.5).

* Az Azure-előfizetése.

   ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

Ha még nem rendelkezik App Service-csomagokkal, vagy ha szeretne egy új szolgáltatáscsomagot létrehozni, a következő információkat kell megadnia:

* Az új szolgáltatáscsomag egyedi neve. Ez a név jelenik meg, ha a későbbiekben webalkalmazásokat tesz közzé az Azure-eszközkészlet használatával, és ez a név jelenik meg az [Azure Portalon](https://portal.azure.com) a fiókja kezelésekor.

* A földrajzi hely, ahol a szolgáltatáscsomag létrejön.

* A szolgáltatáscsomag tarifacsomagja.

   ![App Service-csomag létrehozása](./media/app-service-web-get-started-java/create-app-service-plan.png)

Ezután kattintson a **Resource group** (Erőforráscsoport) lapra. Ha még nem rendelkezik erőforráscsoportokkal, vagy ha szeretne egy új erőforráscsoportot létrehozni, adnia kell egy egyedi nevet az új erőforráscsoportnak. Más esetben válasszon ki egy erőforráscsoportot a legördülő menüből.

![App Service-csomag létrehozása](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

Végül kattintson a **JDK** lapra. Itt több lehetőség is fel van sorolva, amelyek segítségével a fejlesztők külső vagy egyedi Java Developer Kiteket (JDK-kat, fejlesztői készleteket) adhatnak meg. A jelen oktatóanyagban válassza a **Default** (Alapértelmezett) lehetőséget, majd kattintson a **Create** (Létrehozás) gombra.

![App Service-csomag létrehozása](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)

Az Azure-eszközkészlet elkezdi az új App Service létrehozását, és a feldolgozás közben megjelenít egy folyamatjelző panelt.

![App Service létrehozásának állapotjelző sávja](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

Miután az új App Service létrejött, az utolsó dolog, amelyet még el kell döntenie, hogy a webalkalmazást az új webhely gyökerén helyezi-e üzembe. Ha például van egy App Service a *wingtiptoys.azurewebsites.net* helyen, és nem a gyökérnél végzi el az üzembe helyezést, a **MyFirstJavaOnAzureWebApp** nevű webalkalmazás a *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp* helyen lesz üzembe helyezve.

![Webalkalmazás üzembe helyezése a gyökéren](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

Miután minden előző lépést végrehajtott, kattintson a **Deploy** (Üzembe helyezés) lehetőségre a webalkalmazás közzétételéhez az Azure-ban.

![Webalkalmazás üzembe helyezése az Azure-ban](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

Gratulálunk! Sikeresen végrehajtotta a webalkalmazás üzembe helyezését az Azure-ban! Most megtekintheti a webalkalmazás előnézetét az Azure-webhelyen:

![Webalkalmazás kikeresése](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="updating-your-web-app"></a>Webalkalmazás frissítése

Miután sikeresen közzétette a webalkalmazást az Azure-ban, a webalkalmazás frissítése már sokkal egyszerűbb folyamat lesz. Az alábbi lépések azt mutatják be, hogyan teheti közzé a webalkalmazás módosításait.

Először módosítsa a korábbi JSP-kódmintát, hogy a cím a mai dátum legyen:

```jsp
<%@ page
    language="java"
    contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"
    import="java.text.SimpleDateFormat"
    import="java.util.Date" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<% SimpleDateFormat date = new SimpleDateFormat("yyyy/MM/dd"); %>
<title><% out.println(date.format(new Date())); %></title>
</head>
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
</html>
```

Miután mentette a módosításait, kattintson a jobb gombbal a projektre az Eclipse **projektböngészőjében**, majd kattintson az **Azure**, végül pedig a **Publish as Azure Web App** (Közzététel Azure-webalkalmazásként) lehetőségre.

![Frissített webalkalmazás közzététele](./media/app-service-web-get-started-java/publish-updated-web-app-context-menu.png)

Amikor megjelenik a **Deploy Web App** (Webalkalmazás üzembe helyezése) párbeszédpanel, a listában megtalálható lesz a korábban létrehozott App Service. A webalkalmazás frissítéséhez mindössze annyit kell tennie, hogy kiemeli az App Service-t, és a **Deploy** (Üzembe helyezés) gombra kattint a módosítások közzétételéhez.

![Webalkalmazás üzembe helyezése az Azure-ban](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

> [!NOTE]
>
> Ha a webalkalmazást az App Service gyökerében helyezi üzembe, minden egyes alkalommal újra be kell jelölnie a **Deploy to root** (Üzembe helyezés a gyökérnél) beállítást, amikor közzéteszi a módosításokat.
>

Miután közzétette a módosításokat, láthatja, hogy az oldal címe a mai dátumra változik a böngészőben.

![Webalkalmazás kikeresése](./media/app-service-web-get-started-java/browse-web-app-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A webapp törléséhez használja az Azure-eszközkészlet részét képező **Azure Explorert**. Ha az **Azure Explorer** nézet még nem látható az Eclipse-ben, a következő lépések végrehajtásával jelenítheti meg:

1. Kattintson a **Window** (Ablak), majd a **Show View** (Nézet megjelenítése) és az **Other** (Egyéb) elemre.

   ![Show View (Nézet megjelenítése) menü](./media/app-service-web-get-started-java/show-azure-explorer-view-1.png)

2. Amikor megjelenik a **Show View** (Nézet megjelenítése) párbeszédpanel, válassza az **Azure Explorer** lelehetőséget, és kattintson az **OK** gombra.

   ![Show View (Nézet megjelenítése) párbeszédpanel](./media/app-service-web-get-started-java/show-azure-explorer-view-2.png)

Ahhoz, hogy törölje a webalkalmazást az Azure Explorerből, nyissa ki a **Web Apps** (Webalkalmazások) csomópontot, majd kattintson jobb gombbal a webalkalmazásra, és válassza a **Delete** (Törlés) lehetőséget.

![Webalkalmazás törlése](./media/app-service-web-get-started-java/delete-web-app-context-menu.png)

Amikor a program rákérdez a webalkalmazás törlésére, kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések

A Java IDE környezetekhez készült Azure-eszközkészlettel kapcsolatos további információkért lásd az alábbi hivatkozásokat:

* [Az Eclipse-hez készült Azure-eszközkészlet (a jelen cikk)](../azure-toolkit-for-eclipse.md)
  * [Az Eclipse-hez készült Azure-eszközkészlet újdonságai](../azure-toolkit-for-eclipse-whats-new.md)
  * [Az Eclipse-hez készült Azure-eszközkészlet telepítése](../azure-toolkit-for-eclipse-installation.md)
  * [Bejelentkezési utasítások az Eclipse-hez készült Azure-eszközkészlethez](https://go.microsoft.com/fwlink/?linkid=846174)
* [Az IntelliJ-hez készült Azure-eszközkészlet](../azure-toolkit-for-intellij.md)
  * [Az IntelliJ-hez készült Azure-eszközkészlet újdonságai](../azure-toolkit-for-intellij-whats-new.md)
  * [Az IntelliJ-hez készült Azure-eszközkészlet telepítése](../azure-toolkit-for-intellij-installation.md)
  * [Bejelentkezési utasítások az IntelliJ-hez készült Azure-eszközkészlethez](https://go.microsoft.com/fwlink/?linkid=846179)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ](https://azure.microsoft.com/develop/java/) és [Java-eszközök a Visual Studio Team Serviceshez](https://java.visualstudio.com/).

