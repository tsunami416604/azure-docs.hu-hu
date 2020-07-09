---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74451528"
---
## <a name="deploy-template-from-cloud-shell"></a>Sablon üzembe helyezése a Cloud Shellből

A sablon üzembe helyezéséhez a [Cloud Shell](../articles/cloud-shell/overview.md) is használható. Külső sablon üzembe helyezéséhez pontosan úgy adja meg a sablon URI-JÁT, mint bármely külső központi telepítésnél. Helyi sablon üzembe helyezéséhez először be kell töltenie a sablont a Cloud Shell Storage-fiókjába. Ez a szakasz azt ismerteti, hogyan tölthető be a sablon a Cloud Shell-fiókjába, és hogyan telepíthető helyi fájlként. Ha még nem használta a Cloud Shellt, tekintse meg a [Azure Cloud Shell áttekintése](../articles/cloud-shell/overview.md) című témakört, amely a beállításával kapcsolatos információkat tartalmazza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Válassza ki a Cloud Shell tárfiókját.

   ![Adattároló fiók kiválasztása](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Válassza a **Blobok**lehetőséget.

   ![Blobok kiválasztása](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Válassza a **+ Tároló** lehetőséget.

   ![Tároló hozzáadása](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Adja meg a tároló nevét és hozzáférési szintjét. A cikkben szereplő sablon nem tartalmaz bizalmas adatokat, ezért engedélyezze a névtelen olvasási hozzáférést. Válassza az **OK** lehetőséget.

   ![Tároló értékeinek megadása](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Válassza ki a létrehozott tárolót.

   ![Új tároló kiválasztása](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Válassza a **Feltöltés** lehetőséget.

   ![BLOB feltöltése](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Keresse meg és töltse fel a sablont.

   ![Fájl feltöltése](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. A feltöltést követően válassza ki a sablont.

   ![Új sablon kiválasztása](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Másolja az URL-címet.

   ![URL-cím másolása](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Nyissa meg a parancssort.

   ![Cloud Shell megnyitása](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
