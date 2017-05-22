---
title: "A Microsoft Azure Portal áttekintése"
description: "Ismerje meg, a Microsoft Azure Portalt használatát."
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/16/2015
ms.author: dwrede
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: e4c1c2b956b2cae673e60223ce777ba1096dce33
ms.contentlocale: hu-hu
ms.lasthandoff: 05/12/2017


---
# <a name="microsoft-azure-portal-overview"></a>A Microsoft Azure Portal áttekintése
A Microsoft Azure Portal egy központi felület, ahol kioszthatja és kezelheti az Azure-erőforrásokat.  Ez az oktatóanyag bemutatja a portált, illetve megmutatja, hogyan használhatja néhány főbb funkcióját

* Az **univerzális piactéren** a Microsoft és egyéb gyártók által fejlesztett alkalmazások ezrei között válogathat, amelyeket megvásárolhat és/vagy letölthet.
* Az **egységesített és méretezhető tallózási élménynek** köszönhetően könnyen megtalálhatja a fontos erőforrásokat és végrehajthat különböző felügyeleti műveleteket.
* A **konzisztens felügyeleti oldalak** (vagy panelek) segítségével az Azure számos szolgáltatását kezelheti a beállítások, műveletek, számlázási adatok, állapotfigyelés, használati adatok és sok más információ egységes megjelenítésével.
* A **személyes élmény** részeként létrehozhat egy testreszabott indítóképernyőt, amely azokat az információkat jeleníti meg, amelyeket látni kíván a bejelentkezéskor.  A csempéket tartalmazó mindegyik felügyeleti panelt testreszabhatja.
  
  ![Az Azure Portal felhasználói felületének bemutatása][UIOrientation]

## <a name="before-you-get-started"></a>A kezdés előtt
Érvényes Azure-előfizetéssel kell rendelkeznie az oktatóanyag megtekintéséhez.  Ha még nem rendelkezik ezzel, most [regisztrálhat egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).  Ha rendelkezik előfizetéssel, a portálhoz a <https://portal.azure.com> webhelyen férhet hozzá.

## <a name="how-to-create-a-resource"></a>Erőforrás létrehozása
Az Azure Piactéren elemek százait hozhatja létre egyetlen helyen.  Tegyük fel, hogy egy új Windows Server 2012 virtuális gépet kíván létrehozni.  Az +ÚJ felület a belépési pont a piactér kiemelt kategóriáinak gondosan összeválogatott listájához.  Mindegyik kategóriában kiemelt elemeket találhat, valamint egy hivatkozást a teljes piactérre, amely megjeleníti az összes kategóriát és keresést. Az új Windows Server 2012 virtuális gép létrehozásához hajtsa végre a következő műveleteket:  

1. A Windows Server 2012 kiemelt elem, ezért kiválaszthatja a Számítás kategóriából.  
2. Töltse ki az űrlap néhány alapvető mezőjét.
3. Kattintson a „Létrehozás” lehetőségre, és a virtuális gép kiépítése azonnal megkezdődik.

Az értesítési központ riasztást küld, amikor az erőforrás létrejött, majd megnyílik egy felügyeleti panel (az erőforrásokat később is bármikor megkeresheti).

![Portálkategóriák][PortalCategories]

## <a name="how-to-find-your-resources"></a>Az erőforrások megkeresése
A gyakran használt erőforrásokat akármikor rögzítheti a kezdőpulton, azonban előfordulhat, hogy a ritkábban használtakat tallózással kell megkeresnie.  Az alább látható tallózási központ segítségével érheti el az összes erőforrást.  Szűrhet előfizetés alapján, kiválaszthat/átméretezhet oszlopokat, illetve megnyithatja a felügyeleti paneleket az egyes elemekre kattintva.

![Tallózási központ][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Erőforrás-elérés kezelése és delegálása
Ezen a panelen csatlakozhat a virtuális géphez távoli asztali kapcsolattal, ellenőrizhet fontos teljesítménymutatókat, szabályozhatja a hozzáférést a virtuális géphez szerepköralapú hozzáférés-vezérléssel (RBAC), konfigurálhatja a virtuális gépet, illetve végezhet egyéb fontos felügyeleti műveleteket.  A hozzáférés szerepkörön alapuló delegálása fontos a tömeges felügyelethez.  További információkért kattintson [ide](active-directory/role-based-access-control-configure.md). Egy erőforráshoz való hozzáférés delegálásához hajtsa végre a következő műveleteket:

1. Keresse meg az erőforrást.
2. Kattintson az „Összes beállítás” lehetőségre az Essentials szakaszban.
3. Kattintson a „Felhasználók” lehetőségre a beállításlistában.
4. Kattintson a „Hozzáadás” gombra a parancssávon.
5. Válassza ki a felhasználót és a szerepkört.

![Erőforrás kezelése][ManageResource]

## <a name="how-to-get-help"></a>Segítségkérés
Ha problémába ütközik, mi szívesen segítünk Önnek.  A portál rendelkezik egy súgó és támogatási oldallal, amely segít a probléma megoldásában.  A [támogatási csomagjától](https://azure.microsoft.com/support/plans/) függően közvetlenül a portálon is létrehozhat támogatási jegyeket.  A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon. A súgó és támogatás oldalt a Tallózás -> Súgó + támogatás paranccsal is elérheti.  

![Súgó és támogatás][HelpSupport]

## <a name="summary"></a>Összefoglalás
Tekintsük át, hogy mit tudott meg ebből az oktatóanyagból:

* Megtudta, hogyan lehet regisztrálni, előfizetést beszerezni és elérni a portált
* Megismerte a portál felhasználói felületét, valamint megtudta, hogyan hozhat létre és kereshet meg erőforrásokat
* Megtudta, hogyan hozhat létre és kereshet meg erőforrásokat
* Megismerte a felügyeleti panelek struktúráját, illetve a különböző típusú erőforrások egységes kezelésének módját
* Megtudta, hogyan szabhatja testre a portált, hogy az Önnek fontos információkat helyezze a középpontba
* Megtudta, hogyan szabályozhatja az erőforrásokhoz való hozzáférést szerepköralapú hozzáférés-vezérléssel (RBAC)
* Megtudta, hogyan kérhet segítséget és támogatást

A Microsoft Azure Portal jelentősen leegyszerűsíti az alkalmazások felépítését és kezelését a felhőben.  A [felügyeleti blog](https://azure.microsoft.com/blog/topics/management/) követésével mindig naprakész maradhat, mivel folyamatosan [figyelünk a visszajelzésekre](https://feedback.azure.com/forums/223579-azure-preview-portal/) és fejlesztjük a portált.  [ScottGu blogja](http://weblogs.asp.net/scottgu) egy másik remek hely, ahol megtalálhatja az összes Azure-hírt.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

