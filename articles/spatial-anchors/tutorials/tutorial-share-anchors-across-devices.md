---
title: 'Oktatóanyag: Horgonyok megosztása munkamenetek és eszközök között'
description: Ebben az oktatóanyagban megtudhatja, hogyan oszthatja meg az Azure Spatial Anchor-azonosítókat az Android/iOS-eszközök között unity-ben egy háttérszolgáltatással.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615495"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Oktatóanyag: Az Azure Spatial Anchors megosztása munkamenetek és eszközök között

Ebben az oktatóanyagban megtudhatja, hogyan [használhatja az Azure Spatial Anchors](../overview.md) segítségével horgonyok létrehozása egy munkamenet során, majd keresse meg őket, ugyanazon az eszközön vagy egy másikon. Ugyanezek a horgonyok is található több eszköz ugyanazon a helyen, és ugyanabban az időben.

![Kitartás](./media/persistence.gif)

Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, két vagy több eszközre telepíthető alkalmazással fog rendelkezni. Az egyik példány által létrehozott Azure Spatial Anchors megoszthatók a többiekkel.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Üzembe helyezhet egy ASP.NET Core Web App alkalmazást az Azure-ban, amely a horgonyok megosztására használható, és amely egy ideig tárolja őket a memóriában.
> * Konfigurálja az AzureSpatialAnchorsLocalSharedDemo jelenetet a Unity mintában a rövid útmutatókból, hogy kihasználhassa a Sharing Anchors Web App előnyeit.
> * Egy vagy több eszköz telepítése és futtatása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes megjegyezni, hogy bár ebben az oktatóanyagban a Unity és egy ASP.NET Core Web App használatával fog megjelenni, csak egy példát mutat hat arra, hogyan oszthatja meg az Azure Spatial Anchor-azonosítókat más eszközökön. Ugyanannak a célnak a eléréséhez más nyelvek és háttértechnológiák is használhatók. Emellett az oktatóanyagban használt ASP.NET Core Web App a .NET Core 2.2 SDK-tól függ. Ez jól fut a rendszeres Azure Web Apps (Windows), de jelenleg nem működik az Azure Web Apps linuxos.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>A mintaprojekt letöltése

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatásának üzembe helyezése

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Nyissa meg a Visual Studio `Sharing\SharingServiceSample` alkalmazást, és nyissa meg a projektet a mappában.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

A szolgáltatás VS-kódban való üzembe helyezése előtt létre kell hoznia egy erőforráscsoportot és egy App Service-csomagot.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Keresse meg az <a href="https://portal.azure.com/" target="_blank">Azure Portalon,</a> és jelentkezzen be az Azure-előfizetésbe.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el a **myResourceGroup** erőforráscsoportot, majd kattintson az **OK** gombra.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet.

Az **Üzemeltetési séma konfigurálása** párbeszédpanelen használja az alábbi beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service-csomag neve. |
| Hely | USA nyugati régiója | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | A fogadási funkciókat meghatározó [tarifacsomag.](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

Válassza **az OK gombot.**

Nyissa meg a Visual Studio-kódot, és nyissa meg a projektet a `Sharing\SharingServiceSample` mappában. Az <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">oktatóanyag követéséhez</a> telepítse a megosztási szolgáltatást a Visual Studio-kódon keresztül. A "Megnyitás a Visual Studio-kóddal" szakasztól kezdve követheti a lépéseket. Ne hozzon létre egy másik mvc projektet a fenti lépésben leírtak szerint, mivel már rendelkezik a telepítendő és közzéteendő projekttel– a SharingServiceSample.Do not create another mvc project as explained in the step above as you already have the project that needs to be deployed and published– the SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>A mintaalkalmazás telepítése

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unity-20193"></a>Egység 2019.3

A változások megszakadása miatt a Unity 2019.3 jelenleg nem támogatott. Kérjük, használja a Unity 2019.1 vagy 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban telepített egy ASP.NET Core Web App ot az Azure-ban, majd konfigurálta és üzembe helyezte a Unity-alkalmazást. Térbeli horgonyokat hozott létre az alkalmazással, és megosztotta őket más eszközökkel a ASP.NET Core Web App használatával.

Javíthatja a ASP.NET Core Web App, hogy az Azure Cosmos DB használatával a megosztott térbeli horgonyazonosítók tárolása. Az Azure Cosmos DB támogatás hozzáadása lehetővé teszi, hogy a ASP.NET Core Web App még ma hozzon létre egy horgonyt, és napokkal később visszatér, hogy újra megtalálhassa a webalkalmazásban tárolt horgonyazonosító használatával.

> [!div class="nextstepaction"]
> [Horgonyok tárolásához használja az Azure Cosmo DB-t](./tutorial-use-cosmos-db-to-store-anchors.md)

