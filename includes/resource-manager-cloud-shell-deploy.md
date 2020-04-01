---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74451528"
---
## <a name="deploy-template-from-cloud-shell"></a>Sablon üzembe helyezése a Cloud Shellből

A sablon üzembe helyezéséhez a [Cloud Shell](../articles/cloud-shell/overview.md) is használható. Külső sablon üzembe helyezéséhez adja meg a sablon URI-ját pontosan úgy, ahogy bármely külső központi telepítésesetén tenné. Helyi sablon üzembe helyezéséhez először be kell töltenie a sablont a Cloud Shell tárfiókjába. Ez a szakasz azt ismerteti, hogyan töltheti be a sablont a felhőbeli rendszerhéj-fiókba, és hogyan telepítheti helyi fájlként. Ha még nem használta a Cloud Shellt, olvassa [el az Azure Cloud Shell áttekintése](../articles/cloud-shell/overview.md) című témakört a beállításáról.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Válassza ki a Cloud Shell tárfiókját.

   ![Adattároló fiók kiválasztása](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Válassza **a Blobok lehetőséget.**

   ![Blobok kiválasztása](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Válassza a **+ Tároló** lehetőséget.

   ![Tároló hozzáadása](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Adjon nevet és hozzáférési szintet a tárolónak. A cikkben szereplő mintasablon nem tartalmaz bizalmas adatokat, ezért engedélyezze a névtelen olvasási hozzáférést. Válassza **az OK gombot.**

   ![Tárolóértékek biztosítása](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Válassza ki a létrehozott tárolót.

   ![Új tároló kiválasztása](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Válassza a **Feltöltés** lehetőséget.

   ![Blob feltöltése](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Keresse meg és töltse fel a sablont.

   ![Fájl feltöltése](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. A feltöltés után válassza ki a sablont.

   ![Új sablon kiválasztása](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Másolja az URL-címet.

   ![URL-cím másolása](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Nyissa meg a parancssort.

   ![Cloud Shell megnyitása](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
