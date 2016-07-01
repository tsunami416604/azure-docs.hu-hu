<properties
    pageTitle="Logic Apps-alkalmazás létrehozása | Microsoft Azure"
    description="Útmutató SaaS szolgáltatásokhoz csatlakozó Logic Apps-alkalmazás létrehozásához"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="app-service\logic"
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="stepsic"/>

# Új, SaaS szolgáltatásokhoz csatlakozó logikai alkalmazás létrehozása

| Rövid összefoglalás |
| --------------- |
| [A Logic Apps szolgáltatás definíciós nyelve](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [A Logic Apps-összekötők dokumentációja](../connectors/apis-list.md) |
| [Logic Apps-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Ebben a témakörben bemutatjuk, hogyan kezdheti meg mindössze néhány perc alatt az [App Services Logic Apps](app-service-logic-what-are-logic-apps.md) szolgáltatás használatát. Végigvezetjük a munkafolyamaton, amelynek segítségével beállíthatja, hogy az Ön érdeklődésére számot tartó Twitter-üzeneteket a rendszer elküldje egy adott postafiókba.

Az itt részletezett forgatókönyvhöz a következőkre lesz szüksége:

- Azure-előfizetés
- Twitter-fiók
- Office 365-fiók

## Olyan új logikai alkalmazás létrehozása, amely Twitter-üzeneteket küld Önnek e-mailben

1. Az Azure portál irányítópultján válassza a **Piactér** lehetőséget. 
2. A Teljes tartalom résznél keressen a „logikai alkalmazás” kifejezésre, majd válassza a **Logikai alkalmazás (előnézet)** lehetőséget. Alternatív megoldásként válassza az **Új**, majd a **Web + mobil** lehetőséget, és végül a **Logikai alkalmazás (előnézet)** elemet. 
3. Adjon nevet a logikai alkalmazásnak, válasszon App Service-csomagot, majd válassza a **Létrehozás** lehetőséget.  
    Ennél a lépésnél úgy vesszük, hogy Ön rendelkezik App Service-csomaggal, és ismeri a szükséges tulajdonságokat. Ha nem, ne aggódjon. A továbblépés előtt olvassa el az [Azure App Service plans in-depth overview](azure-web-sites-web-hosting-plans-in-depth-overview.md) (Azure App Service-csomagok részletes áttekintése) című cikkben leírtakat. 

4. Amikor először megnyílik a logikai alkalmazás, eseményindítóra lesz szüksége. Az eseményindító-kereső mezőben keressen a **twitter** kifejezésre, majd válassza ki a megfelelő elemet.

7. Ezt követően írja be, hogy milyen kulcsszóra szeretne keresni a Twitteren. 
    ![Keresés a Twitteren](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Kattintson a plusz ikonra, majd válassza az **Új művelet** vagy az **Új feltétel** lehetőséget:  
    ![Plusz ikon](./media/app-service-logic-create-a-logic-app/plus.png)
6. Ha az **Új művelet** lehetőséget választja, megjelenik minden összekötő és az hozzájuk tartozó összes elérhető művelet. Itt kiválaszthatja, hogy mely összekötőket és műveleteket kíván hozzáadni a logikai alkalmazáshoz. Választhatja például az **Office 365 – E-mail küldése** lehetőséget vagy más Office 365-műveletet:  
    ![Műveletek](./media/app-service-logic-create-a-logic-app/actions.png)

7. Ezt követően ki kell töltenie a kívánt e-mailre vonatkozó paramétereket:  ![Paraméterek](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Végül a **Mentés** lehetőséget választva élesítheti a logikai alkalmazást.

## Létrehozott logikai alkalmazás kezelése

A logikai alkalmazás a fentiek elvégzésével működésbe lépett. Az ütemezett munkafolyamat lefutásakor a rendszer megkeresi a megadott hashtaggel ellátott Twitter-üzeneteket. Ha az alkalmazás a feltételnek megfelelő Twitter-üzenetet talál, elhelyezi a Dropboxban. Végül tekintsük át, hogyan lehet letiltani az alkalmazást, illetve ellenőrizni működését.

1. Kattintson a képernyő bal oldalán található **Böngészés** elemre, majd válassza a **Logic Apps** lehetőséget.

2. Az aktuális állapot és az általános információk megtekintéséhez kattintson az újonnan létrehozott logikai alkalmazásra.

3. Az új logikai alkalmazás módosításához kattintson a **Triggers and Actions** (Eseményindítók és műveletek) elemre.

5. Az alkalmazás kikapcsolásához kattintson a parancssáv **Letiltás** elemére.

Az elmúlt kevesebb mint 5 percben létrehozott és beállított egy egyszerű, a felhőben futó logikai alkalmazást. A Logic Apps funkcióinak használatával kapcsolatban további információkat talál a [Use logic app features (Logikai alkalmazások funkcióinak használata)] című cikkben. Ha magukról a logikai alkalmazás definícióiról szeretne többet megtudni, olvassa el a [logikaialkalmazás-definíciók készítését](app-service-logic-author-definitions.md) bemutató cikket.

<!-- Shared links -->
[Azure portál]: https://portal.azure.com
[Use logic app features (Logikai alkalmazások funkcióinak használata)]: app-service-logic-create-a-logic-app.md



<!--HONumber=Jun16_HO2-->


