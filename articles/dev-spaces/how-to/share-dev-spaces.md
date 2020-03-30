---
title: Az Azure Dev Spaces megosztása
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Megtudhatja, hogy az Azure Dev Spaces használatával hogyan oszthat meg fejlesztési területet az Azure Kubernetes szolgáltatásban a csapat többi tagja
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474407"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces megosztása

Az Azure Dev Spaces segítségével megoszthatja a fejlesztői tárhelyet a csapat többi tagja is. Minden fejlesztő dolgozhat a saját térben anélkül, hogy félne a törés mások. Emellett az egy helyen való közös munka lehetővé teszi a kód végpontok közötti tesztelését anélkül, hogy gúnyolódásokat kellene létrehoznia vagy függőségeket szimulálnia. További információt a [Csapatfejlesztési](../team-development-nodejs.md) útmutatóban talál.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Fejlesztői terület beállítása több fejlesztő számára

1. Hozzon létre egy fejlesztői területet az Azure-ban. Válassza a [.NET Core és a VS Code](../get-started-netcore.md), [.NET Core és Visual Studio](../get-started-netcore-visualstudio.md), vagy a [Node.js és a VS Code lehetőséget.](../get-started-nodejs.md) Tulajdonos- vagy közreműködői hozzáféréssel kell rendelkeznie a kiválasztott Azure-előfizetéshez.
1. Győződjön meg arról, hogy minden csapattag rendelkezik a [megfelelő engedélyekkel az Azure Dev Spaces vezérlő eléréséhez.](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis) Konfigurálhatja például az Azure Dev Space **erőforráscsoportját** úgy, hogy [közreműködői hozzáférést biztosítson](/azure/active-directory/role-based-access-control-configure) az egyes csapattagokszámára. A fejlesztői terület erőforráscsoportját a következő parancs futtatásával ellenőrizheti:`azds show-context`
1. Kérje meg a csapattagokat, hogy **válasszák ki a fejlesztői területet,** hogy fejlődhessenek benne.
   * **Parancssori vagy VS-kód**: A meglévő Azure `azds space list`dev-terek megtekintéséhez hozzáférése van: . Fejlesztői terület kiválasztása: `azds space select`.
   * **Visual Studio IDE**: Nyisson meg egy projektet a Visual Studióban, válassza az **Azure Dev Spaces** lehetőséget az indítási beállítások legördülő menüből. A megnyíló párbeszédpanelen jelöljön ki egy meglévő fürtöt.

     ![A Visual Studio indítási beállításai legördülő menü](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>További lépések

További információ [a Csapatfejlesztésről](../team-development-nodejs.md) című témakörben található.
