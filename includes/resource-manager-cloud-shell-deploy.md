---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: a2ee8705be3f34b6df113c68d88e375411f84bf2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440194"
---
## <a name="deploy-template-from-cloud-shell"></a>Sablon üzembe helyezése a Cloud Shellből

A sablon üzembe helyezéséhez a [Cloud Shell](../articles/cloud-shell/overview.md) is használható. Azonban, először be kell töltenie a sablont a storage-fiókra a Cloud Shell. Ha még nem használta a Cloud Shellt, a telepítésével kapcsolatban lásd [Az Azure Cloud Shell áttekintése](../articles/cloud-shell/overview.md) című cikket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Válassza ki a Cloud Shell tárfiókját.

   ![Adattároló fiók kiválasztása](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Válassza ki a **Blobok** lehetőséget.

   ![Blobok kiválasztása](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Válassza a **+ Tároló** lehetőséget.

   ![Tároló hozzáadása](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. A tároló adjon meg egy nevet és a egy hozzáférési szintet. Ez a cikk a minta-sablon nem tartalmaz bizalmas információt, így lehetővé teszi a névtelen olvasási hozzáférés. Kattintson az **OK** gombra.

   ![Adja meg a tároló értékek](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Válassza ki a létrehozott tárolóba.

   ![Új tároló választása](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Válassza a **Feltöltés** lehetőséget.

   ![Blob feltöltése](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Keresse meg és töltse fel a sablont.

   ![Fájl feltöltése](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Miután feltöltötte, válassza ki a sablont.

   ![Új sablon kiválasztása](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Másolja az URL-címet.

   ![URL-Címének másolása](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Nyissa meg a parancssort.

   ![Cloud Shell megnyitása](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
