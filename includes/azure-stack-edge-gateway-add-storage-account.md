---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 30ca4d330d9b16214396ac81e5ab5722ca0e7569
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254294"
---
1. A [Azure Portal](https://portal.azure.com/)válassza ki a Azure stack Edge-erőforrást, majd lépjen az **Áttekintés**elemre. Az eszköznek online állapotban kell lennie.

2. Válassza ki a **+ Storage-fiók hozzáadása** lehetőséget az eszköz parancssáv-sávján. 

   ![Storage-fiók hozzáadása](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Az **Edge Storage-fiók hozzáadása** panelen adja meg a következő beállításokat:

    a. Az eszköz peremhálózati tárolási fiókjának egyedi neve. A Storage-fiókok nevei csak kisbetűket és betűket tartalmazhatnak. A speciális karakterek használata nem engedélyezett. A Storage-fiók nevének egyedinek kell lennie az eszközön belül (az eszközökön kívül).

    b. A Storage-fiókban tárolt adatokra vonatkozó információk opcionális leírása.  
    
    c. Alapértelmezés szerint a Edge Storage-fiók a felhőben egy Azure Storage-fiókra van leképezve, és a Storage-fiókból származó adatok automatikusan a felhőbe kerülnek. Adja meg azt az Azure Storage-fiókot, amelyhez a peremhálózati tárolóeszköz hozzá van rendelve.  

    d. Ezután hozzon létre egy új tárolót, vagy válasszon egy meglévő tárolóból az Azure Storage-fiókban. A rendszer automatikusan feltölti az eszközről az Edge Storage-fiókba írt összes olyan adatforrást, amely a hozzárendelt Azure Storage-fiókban található kiválasztott tárolóba kerül.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. Miután megadta a Storage-fiók összes beállítását, kattintson a **Hozzáadás** gombra a peremhálózati Storage-fiók létrehozásához. A rendszer értesítést küld, ha a peremhálózati Storage-fiók létrehozása sikeres volt. Ekkor megjelenik az új Edge Storage-fiók a Azure Portal a Storage-fiókok listájában. 

    
4. Ha ezt az új Storage-fiókot választja, és a **hozzáférési kulcsok**lehetőségre kattint, megkeresheti a blob Service-végpontot és a hozzá tartozó Storage-fiók nevét. Másolja ezeket az adatokat, mivel ezek az értékek és a hozzáférési kulcsok is segítenek csatlakozni az Edge Storage-fiókhoz.

    ![Storage-fiók hozzáadása](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    A hozzáférési kulcsokat úgy érheti el, ha [a Azure Resource Manager használatával csatlakozik az eszköz helyi API-](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)khoz. 
