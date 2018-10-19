---
title: Blobtároló által aktivált függvények létrehozása az Azure-ban | Microsoft Docs
description: Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyeket az Azure Blob-tárolóba felvett elemek hívnak meg.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: a98e29ef73275d097dc5c61b3b905193b80b79b3
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48858581"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Azure Blob-tároló által aktivált függvény létrehozása

Ismerje meg, hogyan hozhat létre olyan függvényt, amelyet az Azure Blob-tárolóba történő fájlfeltöltés vagy az ott található fájlok frissítése aktivál.

![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Előfeltételek

+ A [Microsoft Azure Storage Explorer](http://storageexplorer.com/) letöltése és telepítése.
+ Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>A blobtároló által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ha nem ez az első, folytassa a harmadik lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-portal.png)

1. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

1. A keresőmezőbe írja be a `blob` kifejezést, majd válassza ki a **Blobalapú eseményindító** sablont.

1. Ha a rendszer kéri, válassza a **Telepítés** lehetőséget az Azure Storage-bővítmény és a függvényalkalmazáson belüli függőségek telepítéséhez. A telepítést követően válassza a **Folytatás** gombot.

    ![Kötési bővítmények telepítése](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

1. Használja az ábra alatti táblázatban megadott beállításokat.

    ![Hozza létre a blobtároló által aktivált függvényt.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Name (Név)** | Egyedi a függvényalkalmazásban | A blob által aktivált függvény neve. |
    | **Elérési út**   | samples-workitems/{name}    | A figyelt blobtárolóban található hely. A blob fájlneve a kötésben adható át a _name_ paraméterrel.  |
    | **Tárfiók kapcsolata** | AzureWebJobsStorage | Választhatja a függvényalkalmazás által már használt tárfiókkapcsolatot, vagy létrehozhat egy újat.  |

1. Kattintson a **Létrehozás** elemre a függvény létrehozásához.

Ezután csatlakozzon az Azure Storage-fiókjához, és hozza létre a **samples-workitems** tárolót.

## <a name="create-the-container"></a>A tároló létrehozása

1. A függvényben kattintson az **Integráció** elemre, bontsa ki a **Dokumentáció** elemet, és másolja a **Fiók neve** és a **Fiók kulcsa** értéket. Ezekkel a hitelesítő adatokkal csatlakozhat a tárfiókhoz. Ha már csatlakozott a tárfiókjához, folytassa a 4. lépéssel.

    ![Kérje le a tárfiókhoz való csatlakozáshoz szükséges hitelesítő adatokat.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Futtassa a [Microsoft Azure Storage Explorer](http://storageexplorer.com/) eszközt, kattintson a bal oldalon található csatlakozási ikonra, válassza a **Tárfiók nevének és kulcsának használata** lehetőséget, és kattintson a **Tovább** gombra.

    ![Futtassa a Storage Account Explorer eszközt.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Adja meg az 1. lépésben megállapított **Fiók neve** és **Fiók kulcsa** értéket, és kattintson a **Tovább**, majd a **Csatlakozás** gombra. 

    ![Adja meg a tároló hitelesítő adatait, és csatlakozzon.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Bontsa ki a csatolt tárfiókot, kattintson a jobb gombbal a **Blobtárolók** elemre, kattintson a **Blobtároló létrehozása** elemre, írja be a `samples-workitems` értéket, és nyomja meg az Enter billentyűt.

    ![Hozzon létre egy tárolási üzenetsort.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

A blobtároló létrehozása után tesztelheti a függvényt úgy, hogy feltölt egy fájlt a tárolóba.

## <a name="test-the-function"></a>A függvény tesztelése

1. Térjen vissza az Azure Portalra, keresse meg a függvényt, bontsa ki a **Naplók** elemet a lap alján, és győződjön meg arról, hogy a naplózási adatfolyam nincs leállítva.

1. A Storage Explorerben bontsa ki a tárfiókját, a **Blobtárolók** elemet és a **samples-workitems** tárolót. Kattintson a **Feltöltés**, majd a **Fájlok feltöltése...** elemre.

    ![Töltsön fel egy fájlt a blobtárolóba.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. A **Fájlok feltöltése** párbeszédpanelen kattintson a **Fájlok** mezőre. Válasszon egy fájlt a helyi számítógépen, például egy képfájlt, jelölje ki, és kattintson a **Megnyitás**, majd a **Feltöltés** elemre.

1. Lépjen vissza a függvény naplóihoz, és győződjön meg arról, hogy megtörtént a blob olvasása.

   ![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Amikor a függvényalkalmazás az alapértelmezett használatalapú csomagban fut, előfordulhat, hogy akár több is perc is eltelik a blob hozzáadása vagy frissítése és a függvény aktiválása között. Ha kis késleltetésre van szüksége a blob által aktivált függvényekhez, célszerű App Service-csomagban futtatnia a függvényalkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Létrehozott egy függvényt, amely akkor fut, amikor blob felvétele vagy frissítése történik a blobtárolóban. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információ a blobtároló eseményindítóiról: [Azure Functions – a blobtároló kötései](functions-bindings-storage-blob.md).
