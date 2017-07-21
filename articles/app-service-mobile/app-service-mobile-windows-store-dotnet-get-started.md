---
title: "Mobile Apps-t használó Univerzális Windows-platform (UWP) létrehozása | Microsoft Docs"
description: "Az útmutató bevezeti Önt az Azure-alapú, az Univerzális Windows-platformra (UWP) C#, Visual Basic vagy JavaScript nyelven történő alkalmazásfejlesztésre szolgáló mobil-háttéralkalmazások használatába."
services: app-service\mobile
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 1a031c4858bcbc75ee807ba520e1b22c89471498
ms.contentlocale: hu-hu
ms.lasthandoff: 02/16/2017

---
# <a name="create-a-windows-app"></a>Windows-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat Univerzális Windows Platform- (UWP-) alkalmazásokhoz. További információ: [Mi a Mobile Apps szolgáltatás?](app-service-mobile-value-prop.md). Az alábbiakban az elkészült alkalmazás képernyőképei láthatók:

![Az elkészült asztali alkalmazás](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Asztali számítógépen futó alkalmazás.

![Az elkészült mobilalkalmazás](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Telefonon futó alkalmazás.

Az oktatóanyag végrehajtása feltétele a Mobile Apps UWP-alkalmazásokra vonatkozó összes többi oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio Community 2015] vagy újabb verzió.

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/mobile/) oldalra. Itt azonnal létrehozhat egy ideiglenes, induló mobilalkalmazást az App Service szolgáltatásban – kötelezettségek és a bankkártyaadatok megadása nélkül.
>
>

## <a name="create-a-new-azure-mobile-app-backend"></a>Új Azure Mobile Apps-háttéralkalmazás létrehozása
Új Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="configure-the-server-project"></a>Kiszolgálóprojekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Az ügyfélprojekt letöltése és futtatása
Miután konfigurálta a Mobile Apps-háttéralkalmazást, létrehozhat új ügyfélalkalmazást, vagy módosíthat egy meglévő alkalmazást az Azure-hoz való kapcsolódáshoz. Ebben a részben le fogja tölteni egy UWP-alkalmazás sablonprojektjét, amely a Mobile Apps-háttéralkalmazásához való csatlakozásnak megfelelően van kialakítva.

1. Mobile Apps-háttéralkalmazásának korábbi, **Gyorsindítás** paneljén kattintson az **Új alkalmazás létrehozása** > **Letöltés** elemre, majd bontsa ki a tömörített projektfájlokat a helyi számítógépen.

    ![Gyorssablonra épülő Windows-projekt letöltése](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (Nem kötelező.) Az UWP-alkalmazásprojektet hozzáadhatja kiszolgálóprojektként ugyanahhoz a megoldáshoz. Ez mind az alkalmazás, mind az ugyanabban a Visual Studio-megoldásban lévő háttéralkalmazás hibakeresését és tesztelését megkönnyíti, ha szeretné ezt elvégezni. Ahhoz, hogy egy UWP-alkalmazásprojektet hozzá tudjon adni a megoldáshoz, a Visual Studio 2015-ös vagy újabb verzióját kell használnia.
3. Az UWP-alkalmazást használja kezdőprojektként, és nyomja le az F5 billentyűt az alkalmazás telepítéséhez és futtatásához.
4. Az alkalmazásban írjon be egy értelmes, például *Az oktatóanyag befejezése* szöveget az **Insert a TodoItem** (Teendő hozzáadása) szövegmezőbe, majd kattintson a **Save** (Mentés) gombra.

    ![Az elkészült, gyorssablonra épülő Windows-projekt asztali számítógépen](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára.
5. (Nem kötelező.) Állítsa le az alkalmazást, és indítsa újra egy másik eszközön vagy mobilemulátoron.

    ![Az elkészült, gyorssablonra épülő Windows-projekt telefonon](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Érdemes tudni, hogy az előző lépésben mentett adatok betöltése az Azure-ból, az UWP-alkalmazás indítását követően történik.

## <a name="next-steps"></a>Következő lépések
* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203

