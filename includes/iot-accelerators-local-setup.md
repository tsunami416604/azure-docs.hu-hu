---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: ec382217bfa32da19c0b98e656f3782739b26cc6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125058"
---
## <a name="download-the-source-code"></a>Letöltheti a forráskódot

A távoli figyelési forráskódtárházak forráskódját és a Docker konfigurációs fájlokat kell futtatnia a mikroszolgáltatások Docker-rendszerképeket tartalmazza.

Klónozza, és hozza létre a tárház helyi verzióját, a parancssori környezetet használatával keresse meg a megfelelő mappát a helyi gépen. Ezután futtassa a következő eljárások egyikét parancsokat vagy klónozásához a .NET-adattárban:

Töltse le a legújabb verzióját a .NET mikroszolgáltatás-megvalósítások, futtassa:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
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

     A parancsfájl is hozzáad egy előtaggal rendelkező környezeti változók készletét **számítógépek** a helyi gépen. Ha elindítja a Docker-tárolók vagy helyileg mikroszolgáltatás-projektek, a konfigurációs értékeket, olvassa az ezeket a környezeti változókat.

     > [!TIP]
     > Miután a parancsfájl futása befejeződött, azt is menti a környezeti változók nevű fájlba  **\<a kezdőmappa\>\\.pcs\\\<megoldásnevet\>.env** . A jövőbeli megoldás megoldásgyorsító központi telepítések használhatja őket. Vegye figyelembe, hogy a helyi gépen állítsa be a környezeti változók felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.

1. Lépjen ki a parancssori környezetből.

### <a name="use-existing-azure-resources"></a>A meglévő Azure-erőforrások

Ha már létrehozta a szükséges Azure-erőforrások, a helyi gépén hozzon létre a megfelelő környezeti változókat. Előfordulhat, hogy a menteni ezeket a  **\<a kezdőmappa\>\\.pcs\\\<megoldás neve\>.env** a központi telepítési fájlt. Vegye figyelembe, hogy a helyi gépen beállított környezeti változókat felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.