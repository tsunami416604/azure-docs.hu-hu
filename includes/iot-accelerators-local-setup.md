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
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179552"
---
## <a name="download-the-source-code"></a>A forráskód letöltése

A távoli figyelés forráskód tárházak tartalmazzák a forráskódot és a Docker konfigurációs fájlokat a Docker-rendszerképek futtatásához.

Klónozni és a tárház helyi verziójának létrehozásához használja a parancssori környezetet a helyi számítógép megfelelő mappájához való navigáláshoz. Ezután futtassa az alábbi parancskészletek egyikét a .NET tárház klónozásához:

A .NET mikroszolgáltatás-implementációk legújabb verziójának letöltéséhez futtassa a következőt:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Ezek a parancsok a mikroszolgáltatások helyi futtatásához használt parancsfájlok mellett az összes mikroszolgáltatás forráskódját is letöltik. Bár a Dockerben nincs szükség a forráskódra, a forráskód akkor hasznos, ha később a megoldásgyorsító módosítását és a módosítások helyi tesztelését tervezi.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan futtathatja a mikroszolgáltatások helyileg, ezek függenek a felhőben futó Azure-szolgáltatások. Használja a következő parancsfájlt az Azure-szolgáltatások üzembe helyezéséhez. A következő parancsfájlpéldák feltételezik, hogy a .NET tárházat használja egy Windows-gépen. Ha más környezetben dolgozik, megfelelően módosítsa a görbéket, a fájlkiterjesztéseket és az útvonalelválasztókat.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozta létre a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben keresse meg a tárház klónozott példányának **\services\scripts\local\launch** mappáját.

1. Futtassa a következő parancsokat a **PCCLI** eszköz telepítéséhez és az Azure-fiókba való bejelentkezéshez:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **start.cmd** parancsfájlt. A parancsfájl a következő információkat kéri:
   * A megoldás neve.
   * A használandó előfizetés.
   * A használandó Azure-adatközpont helye.

     A parancsfájl erőforráscsoportot hoz létre az Azure-ban a megoldás nevével. Ez az erőforráscsoport tartalmazza a megoldásgyorsító által használt Azure-erőforrásokat. Törölheti ezt az erőforráscsoportot, ha már nincs szüksége a megfelelő erőforrásokra.

     A parancsfájl a **pcs** előtaggal rendelkező környezeti változók készletét is hozzáadja a helyi számítógéphez. Ezek a környezeti változók a távoli figyelés részleteit az Azure Key Vault-erőforrásból való olvasáshoz. Ez a Key Vault-erőforrás az a hely, ahonnan a távoli figyelés beolvassa a konfigurációs értékeket.

     > [!TIP]
     > Amikor a parancsfájl befejeződik, a környezeti változókat a ** \<kezdőmappa\>\\.pcs\\\<megoldásneve\>.env**fájlba is menti. Használhatja őket a jövőbeli megoldásgyorsító központi telepítések. Vegye figyelembe, hogy a helyi számítógépen beállított környezeti változók felülírják a **szolgáltatási\\parancsfájlok helyi\\\\.env** fájljának értékeit a **docker-compose futtatásakor.**

1. Lépjen ki a parancssori környezetből.

### <a name="use-existing-azure-resources"></a>Meglévő Azure-erőforrások használata

Ha már létrehozta a szükséges Azure-erőforrásokat, hozza létre a megfelelő környezeti változókat a helyi gépen.
Állítsa be a környezeti változókat a következő:
* **PCS_KEYVAULT_NAME** – az Azure Key Vault-erőforrás neve
* **PCS_AAD_APPID** - Az AAD alkalmazás azonosítója
* **PCS_AAD_APPSECRET** - Az AAD alkalmazás titkos

A konfigurációs értékek et ebből az Azure Key Vault-erőforrásból olvassa be a rendszer. Előfordulhat, hogy ezek a környezeti változók a ** \<saját mappába\>\\kerülnek .pcs\\\<megoldás név .env\>** fájlba a központi telepítésből. Vegye figyelembe, hogy a helyi számítógépen beállított környezeti változók felülbírálják a **szolgáltatási\\parancsfájlok helyi\\\\.env** fájljának értékeit a **docker-compose futtatásakor.**

A mikroszolgáltatás által szükséges konfiguráció egy része a **Key Vault** egy példányában van tárolva, amely a kezdeti üzembe helyezéskor jött létre. A megfelelő változók at keyvault szükség szerint módosítani kell.