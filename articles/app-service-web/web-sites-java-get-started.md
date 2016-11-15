---
title: "Java-webalkalmazás létrehozása az Azure App Service-ben | Microsoft Docs"
description: "Ez az oktatóanyag ismerteti a Java-webalkalmazások telepítését az Azure App Service szolgáltatásban."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d6e73cc3-8b71-4742-a197-3edeabc6a289
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: acacfbead6cf0d68ccfeb5e818a2b04f2be9b902


---
# <a name="create-a-java-web-app-in-azure-app-service"></a>Java-webalkalmazás létrehozása az Azure App Service szolgáltatásban
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ez az oktatóanyag bemutatja, hogyan hozható létre Java-webalkalmazás [az Azure App Service szolgáltatásban] az [Azure Portal] használatával. Az Azure portál egy webes felület, amely az Azure-erőforrások kezelésére használható.

> [!NOTE]
> Az oktatóanyag elvégzéséhez Microsoft Azure-fiókra lesz szüksége. Ha nincs fiókja, [aktiválhatja a Visual Studio előfizetői előnyeit], vagy [regisztráljon egy ingyenes próbaverzióra].
> 
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása] oldalra. Itt azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="java-application-options"></a>A Java-alkalmazások beállításai
A Java-alkalmazások többféleképpen is beállíthatók az App Service-webalkalmazásokban. 

1. Hozzon létre egy alkalmazást, és konfigurálja az **alkalmazás beállításait**.
   
    Az App Service többféle Tomcat- és Jetty-verziót biztosít, alapértelmezett konfigurációval. Ha az üzemeltetni kívánt alkalmazás a beépített verziók egyikével fog működni, ez a legegyszerűbb módszer a webes tároló beállítására – tökéletes választás, ha mindössze egy war-fájlt szeretne feltölteni egy webes tárolóba. Ha ezt a módszert használja, először létrehoz egy alkalmazást az Azure Portalon, majd az alkalmazás **Alkalmazásbeállítások** paneljére lépve kiválasztja a Java verzióját a kívánt Java webes tárolóval együtt. Ezzel a módszerrel a Java és a webes tároló is a Program Files mappából fut. A többi módszer a webes tárolót és esetlegesen a JVM-et is a lemezterületre helyezi. Ha ezt a modellt használja, a fájlrendszer ezen részén nem lesz engedélye a fájlok szerkesztésére. Ez azt jelenti, hogy ebben az esetben nem végezhet olyan műveleteket, mint a *server.xml* fájl konfigurálása, vagy a könyvtárfájlok áthelyezése a */lib* mappába. További információkért lásd az oktatóanyag [Java-webalkalmazás létrehozása és konfigurálása](#appsettings) című szakaszát.
2. Válasszon egy sablont az Azure Piactérről.
   
    Az Azure Piactéren olyan sablonok találhatók, amelyekkel automatikusan létrehozhatók és konfigurálhatók a Java-webalkalmazások Tomcat vagy Jetty webes tárolókkal. A sablonok által létrehozott webes tárolók testreszabhatók. További információkért lásd az oktatóanyag [Java-sablon használata az Azure Piactérről](#marketplace) című szakaszát.
3. Hozzon létre egy alkalmazást, majd manuálisan másolja át és szerkessze a konfigurációs fájlokat 
   
    Előfordulhat, hogy olyan egyéni Java-alkalmazást célszerű üzemeltetni, amely nem az App Service által biztosított webes tárolókba települ. Példa:
   
   * Az Ön Java-alkalmazása olyan Tomcat- vagy Jetty-verziót igényel, amelyet az App Service nem támogat közvetlenül, és nincs a katalógusban.
   * Az Ön Java-alkalmazása HTTP-kéréseket fogad, és nem települ WAR-fájlként egy meglévő webes tárolóba.
   * Teljesen új webes tárolót szeretne konfigurálni. 
   * Az App Service által nem támogatott Java-verziót szeretne használni és feltölteni.
     
     Ilyen esetben létrehozhatja az alkalmazást az Azure portálon, és manuálisan megadhatja a megfelelő futtatási fájlokat. Ekkor a fájlok beleszámítanak az App Service-csomaghoz tartozó tárhelykvótába. További információ: [Upload a custom Java web app to Azure (Egyéni Java-webalkalmazás feltöltése az Azure-ba)] .

## <a name="a-nameportala-create-and-configure-a-java-web-app"></a><a name="portal"></a>Java-webalkalmazás létrehozása és konfigurálása
Ez a szakasz bemutatja, hogyan hozható létre egy webalkalmazás és konfigurálható a Javához a portál **Alkalmazásbeállítások** paneljén.

1. Jelentkezzen be az [Azure Portal].
2. Kattintson az **Új > Web + mobil > Webalkalmazás** elemre.
   
    ![Új webalkalmazás][newwebapp]
3. A **Webalkalmazás** mezőben adjon meg egy nevet a webalkalmazás számára.
   
    Ennek a névnek egyedinek kell lennie az azurewebsites.net tartományban, mert a webalkalmazás URL-címe {név}.azurewebsites.net lesz. Ha a megadott név nem egyedi, egy piros felkiáltójel jelenik meg a szövegmezőben.
4. Válasszon egy **erőforráscsoportot**, vagy hozzon létre egy újat.
   
    További információk az erőforráscsoportokkal kapcsolatban: [Az Azure Portal használata az Azure-erőforrások kezeléséhez].
5. Válasszon ki egy **App Service-csomagot/-helyet**, vagy hozzon létre egy újat.
   
    További információk az App Service-csomagokról: [Az Azure App Service-csomagok áttekintése].
6. Kattintson a **Létrehozás** gombra.
   
    ![Webalkalmazás létrehozása][newwebapp2]
7. A webalkalmazás létrehozása után kattintson a következőre: **Webalkalmazások > {az Ön webalkalmazása}**.
   
    ![Webalkalmazás kiválasztása][selectwebapp]
8. A **Webalkalmazás** panelen kattintson a **Beállítások** lehetőségre.
9. Válassza az **Alkalmazásbeállítások** elemet.
10. Válassza ki a kívánt **Java-verziót**. 
11. Válassza ki a kívánt **Java-alverziót**. Ha a **Legújabb** lehetőséget választja, az alkalmazás az App Service-ben az adott Java-főverzióhoz elérhető legújabb alverziót fogja használni. A **Legújabb** elem csak az **Alkalmazásbeállítások** segítségével létrehozott Java-alkalmazásokhoz használható. Ha a katalógusból hozza létre az új Java-alkalmazást, a tároló és a JVM módosításait Önnek kell kezelnie. 
12. Válassza ki a kívánt **webes tárolót**. Ha egy **Legújabb** előtaggal rendelkező tárolónevet választ, az alkalmazás az App Service-ben az adott webes tároló főverziójához elérhető legfrissebb verziót fogja használni. 
    
    ![Webes tároló verziói][versions]
13. Kattintson a **Save** (Mentés) gombra.
    
    Kis idő elteltével webalkalmazása Java-alapúvá válik, és az Ön által kiválasztott webes tároló használatára lesz konfigurálva.
14. Kattintson a következőre: **Webalkalmazások > {az Ön webalkalmazása}**.
15. Kattintson az **URL-címre** az új webhelyre lépéshez.
    
    Ekkor a weblap megerősíti, hogy létrejött egy Java-alapú webalkalmazás.

## <a name="a-namemarketplacea-use-a-java-template-from-the-azure-marketplace"></a><a name="marketplace"></a> Java-sablon használata az Azure Marketplace-ről
Ez a szakasz ismerteti, hogyan használható az Azure Piactér Java-webalkalmazások létrehozására. Ugyanezzel a folyamattal létrehozhatók Java-alapú mobil- és API-alkalmazások is. 

1. Jelentkezzen be az [Azure Portal]
2. Kattintson az **Új > Piactér** elemre.
   
    ![Új piactér][newmarketplace]
3. Kattintson a **Web + mobil** elemre.
   
    Lehet, hogy balra kell görgetnie a **Piactér** panel megjelenítéséhez, ahol kiválaszthatja a **Web + mobil** lehetőséget.
4. A keresőmezőben adja meg egy Java-alkalmazáskiszolgáló nevét, például **Apache Tomcat** vagy **Jetty**, majd nyomja le az Enter billentyűt.
5. A keresési eredmények között kattintson a Java-alkalmazáskiszolgálóra.
   
    ![Webes mobil Jetty][webmobilejetty]
6. Az első **Apache Tomcat** vagy **Jetty** panelen kattintson a **Létrehozás** elemre.
   
    ![Jetty portál panel][jettyblade]
7. A következő **Apache Tomcat** vagy **Jetty** panelen adjon meg egy nevet a webalkalmazás számára a **Webalkalmazás** mezőben.
   
    Ennek a névnek egyedinek kell lennie az azurewebsites.net tartományban, mert a webalkalmazás URL-címe {név}.azurewebsites.net lesz. Ha a megadott név nem egyedi, egy piros felkiáltójel jelenik meg a szövegmezőben.
8. Válasszon egy **erőforráscsoportot**, vagy hozzon létre egy újat.
   
    További információk az erőforráscsoportokkal kapcsolatban: [Az Azure Portal használata az Azure-erőforrások kezeléséhez].
9. Válasszon ki egy **App Service-csomagot/-helyet**, vagy hozzon létre egy újat.
   
    További információk az App Service-csomagokról: [Az Azure App Service-csomagok áttekintése].
10. Kattintson a **Létrehozás** gombra.
    
    ![Jetty portál, Létrehozás][jettyportalcreate2]
    
    Az Azure kis időn belül, általában kevesebb mint egy perc alatt létrehozza az új webalkalmazást.
11. Kattintson a következőre: **Web apps (Webalkalmazások) > {az Ön webalkalmazása}**.
12. Kattintson az **URL-címre** az új webhelyre lépéshez.
    
    ![Jetty URL][jettyurl]
    
    A Tomcat tartalmaz egy alapértelmezett lapkészletet – ha a Tomcatet választotta, az alábbi példához hasonló lap jelenik meg.
    
    ![Webalkalmazás az Apache Tomcat használatával][tomcat]
    
    Ha a Jettyt választotta, az alábbi példához hasonló lap jelenik meg. A Jetty nem rendelkezik alapértelmezett lapkészlettel, így ugyanazt a JSP-t használja a rendszer, mint az üres Java-webhelyhez.
    
    ![Webalkalmazás a Jetty használatával][jetty]

Most, hogy létrehozta a webalkalmazást egy alkalmazástárolóval, tekintse meg a [További lépések](#next-steps) szakaszt az alkalmazás a webalkalmazásba való feltöltésével kapcsolatos információkért.

## <a name="next-steps"></a>Következő lépések
Ekkor egy Java-alkalmazáskiszolgáló fut a webalkalmazásában az Azure App Service szolgáltatásban. A kód a webalkalmazásban való telepítése: [Alkalmazás vagy weblap hozzáadása a Java-webalkalmazáshoz].

További információk Java-alkalmazások fejlesztéséről az Azure-ban: [Java fejlesztői központ].

<!-- URL List -->

[Alkalmazás vagy weblap hozzáadása a Java-webalkalmazáshoz]: ./web-sites-java-add-app.md
[Az Azure App Service-csomagok áttekintése]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure Portal]: https://portal.azure.com/
[aktiválhatja a Visual Studio előfizetői előnyeit]: http://go.microsoft.com/fwlink/?LinkId=623901
[regisztráljon egy ingyenes próbaverzióra]: http://go.microsoft.com/fwlink/?LinkId=623901
[Az App Service kipróbálása]: http://go.microsoft.com/fwlink/?LinkId=523751
[webalkalmazás az Azure App Service-ben]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java fejlesztői központban]: /develop/java/
[Az Azure Portal használata az Azure-erőforrások kezeléséhez]: ../azure-portal/resource-group-portal.md
[Upload a custom Java web app to Azure (Egyéni Java-webalkalmazás feltöltése az Azure-ba)]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png



<!--HONumber=Nov16_HO2-->


