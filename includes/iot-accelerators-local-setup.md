---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 10/29/2018
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 900d75f826830ea7336044a892506d3bec546e30
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283912"
---
## <a name="download-the-source-code"></a>Letöltheti a forráskódot

A távoli figyelési forráskódtárházak kell futtatnia a mikroszolgáltatások Docker-rendszerképek Docker konfigurációs fájljait tartalmazza.

Klónozza, és hozza létre a tárház helyi verzióját, a parancssori környezetet használatával keresse meg a megfelelő mappát a helyi gépen. Ezután futtassa a következő eljárások egyikét parancsokat vagy klónozásához a .NET vagy Java-tárház:

Töltse le a legújabb verzióját a .NET mikroszolgáltatás-megvalósítások, futtassa:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

Töltse le a legújabb verzióját a Java-mikroszolgáltatások megvalósításokhoz, futtassa:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, a mikroszolgáltatások helyi futtatásához használja a parancsfájlok mellett a mikroszolgáltatások. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később kívánja módosítani a megoldásgyorsító és módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan helyileg történő futtatása a mikroszolgáltatás-alapú, Azure-szolgáltatások a felhőben futó függenek. Használja a következő szkriptet az Azure-szolgáltatások üzembe helyezéséhez. A következő parancsfájl példák feltételezik, hogy egy Windows-gépen használja a .NET-adattárban. Ha egy másik környezetben dolgozik, állítsa be az elérési utak, fájlkiterjesztések, és elérési út elválasztók megfelelően.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozott a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben, keresse meg a **\services\scripts\local\launch** mappájához klónozott másolatának a tárházban.

1. Futtassa az alábbi parancsokat a **számítógépek** parancssori eszközt, és jelentkezzen be az Azure-fiókjával:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **start.cmd** parancsfájlt. A parancsfájl kérni fogja, hogy a következő információkat:
    * A megoldás nevét.
    * A használandó előfizetés.
    * Használata az Azure-adatközpont helyét.

    A parancsfájl hoz létre, a megoldás nevű erőforráscsoportot az Azure-ban. Ez az erőforráscsoport tartalmazza az Azure-erőforrások a megoldásgyorsító használja. Ez az erőforráscsoport már nincs szüksége a megfelelő erőforrások után törölheti.

    A parancsfájl is hozzáad egy előtaggal rendelkező környezeti változók készletét **számítógépek** a helyi gépen. Ha elindítja a Docker-tárolók helyileg, a konfigurációs értékeket, olvassa az ezeket a környezeti változókat.

> [!TIP]
> Miután a parancsfájl futása befejeződött, a környezeti változók listáját jeleníti meg. Ha ezekre az értékekre, menti a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájlt is használhatja őket jövőbeli megoldás megoldásgyorsító központi telepítésekhez. Vegye figyelembe, hogy a helyi gépen állítsa be a környezeti változók felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.

### <a name="use-existing-azure-resources"></a>A meglévő Azure-erőforrások

Ha már létrehozta a szükséges Azure-erőforrások, a helyi gépén hozzon létre a megfelelő környezeti változókat. Előfordulhat, hogy mentette-e ezeket az értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájlt a legutóbbi üzembe helyezés részeként. Vegye figyelembe, hogy a helyi gépen beállított környezeti változókat felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.