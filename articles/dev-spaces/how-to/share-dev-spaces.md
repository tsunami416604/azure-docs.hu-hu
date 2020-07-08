---
title: Az Azure dev Spaces megosztása
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Ismerje meg, hogy az Azure dev Spaces hogyan oszthat meg fejlesztési helyet az Azure Kubernetes szolgáltatásban másokkal a csapatában
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79474407"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces megosztása

Az Azure dev Spaces használatával megoszthatja a fejlesztői tárhelyét másokkal a csapatával. Minden fejlesztő dolgozhat a saját tárhelyén anélkül, hogy másokat kellene megszakítani. Emellett az egyetlen térben való együttműködés lehetővé teszi a kód végpontok közötti tesztelését anélkül, hogy létre kellene hoznia a modelleket, vagy szimulálnia kell a függőségeket. További információért lásd a Team Development-útmutatójának [megismerése](../team-development-nodejs.md) című témakört.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Fejlesztői terület beállítása több fejlesztő számára

1. Fejlesztői terület létrehozása az Azure-ban. Válassza a [.net Core és a vs Code](../get-started-netcore.md), a [.net Core és a Visual Studio](../get-started-netcore-visualstudio.md), vagy a [Node.js és a vs Code](../get-started-nodejs.md)elemet. A kiválasztott Azure-előfizetéshez tulajdonosi vagy közreműködői hozzáféréssel kell rendelkeznie.
1. Győződjön meg arról, hogy minden csapattag rendelkezik a [megfelelő engedélyekkel az Azure dev Spaces-vezérlő eléréséhez](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Beállíthatja például, hogy az Azure dev Space **erőforráscsoport** [közreműködői hozzáférést](/azure/active-directory/role-based-access-control-configure) biztosítson az egyes csapattagok számára. A következő parancs futtatásával tekintheti meg a fejlesztői terület erőforrás-csoportját:`azds show-context`
1. Kérje meg a csapattagokat, hogy a fejlesztéshez **válassza ki a fejlesztői helyet** .
   * A **parancssor vagy a vs Code**: megtekintheti a meglévő Azure dev-helyeket, amelyekhez hozzáférése van: `azds space list` . Fejlesztői terület kiválasztása: `azds space select` .
   * **Visual Studio ide**: nyisson meg egy projektet a Visual Studióban, válassza az **Azure dev Spaces** elemet a rendszerindítási beállítások legördülő menüből. A megnyíló párbeszédpanelen válasszon ki egy meglévő fürtöt.

     ![A Visual Studio indítási beállításai legördülő menü](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>További lépések

További információért lásd a [Team Development](../team-development-nodejs.md) megismerését ismertető témakört.
