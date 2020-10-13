---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b83951cd743afc7e249e3ee37182007bbe40cb5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971299"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrásának létrehozása

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portalt</a>.

A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget.

A keresőmező segítségével megkeresheti a **térbeli horgonyokat**.

![A térbeli horgonyok keresésének eredményét bemutató képernyőkép.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza a **térbeli horgonyok**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

A **térbeli horgonyok fiók** panelen tegye a következőket:

* Adjon meg egy egyedi nevet a normál alfanumerikus karakterek használatával.  
* Válassza ki azt az előfizetést, amelyhez az erőforrást csatolni kívánja.  
* Hozzon létre egy erőforráscsoportot az **új létrehozása**lehetőség kiválasztásával. Nevezze el **myResourceGroup**, majd kattintson **az OK gombra**.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* Válassza ki azt a helyet (régiót), ahová az erőforrást helyezni kívánja.  
* Válassza az **új** lehetőséget az erőforrás létrehozásának megkezdéséhez.

![Képernyőkép a térbeli horgonyok panelről az erőforrás létrehozásához.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után a Azure Portal mutatja, hogy a telepítés befejeződött. 
   
![Az erőforrás-telepítés befejezését bemutató képernyőkép.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Válassza az **Erőforrás megnyitása** lehetőséget. Ekkor megtekintheti az erőforrás tulajdonságait. 
   
Másolja az erőforrás **Account ID** értékét egy szövegszerkesztőbe későbbi használatra.

![Képernyőkép az erőforrás-tulajdonságok panelről.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Másolja át az erőforrás **fiók tartományának** értékét egy szövegszerkesztőbe későbbi használatra.

![Az erőforrás fiók tartományának értékét ábrázoló képernyőkép.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

A **Beállítások**területen válassza a **kulcs**elemet. Másolja az **elsődleges kulcs** értékét, a **fiók kulcsát**egy szövegszerkesztőbe későbbi használatra.

![Képernyőkép a fiókhoz tartozó kulcsok panelről.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
