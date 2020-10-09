---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67179552"
---
## <a name="download-the-source-code"></a>Forráskód letöltése

A távfelügyelet forráskódja tartalmazza a forráskódot és a Docker konfigurációs fájljait, amelyekre szüksége van a Service Docker-rendszerképek futtatásához.

A tárház helyi verziójának klónozásához és létrehozásához a parancssori környezet segítségével navigáljon a helyi gépen található megfelelő mappára. Ezután futtassa a következő parancsok egyikét a .NET-tárház klónozásához:

A .NET-es webszolgáltatás-implementációk legújabb verziójának letöltéséhez futtassa a következőt:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Ezek a parancsok az összes szolgáltatás forráskódját letöltik a helyi webszolgáltatás futtatásához használt parancsfájlokon kívül. Bár a forráskód nem szükséges a Docker-ben való futtatásához, a forráskód akkor hasznos, ha később a megoldás-gyorsító módosítását tervezi, és helyileg teszteli a módosításokat.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan futtathatja helyileg a Service-t, a felhőben futó Azure-szolgáltatásoktól függenek. Az Azure-szolgáltatások üzembe helyezéséhez használja az alábbi szkriptet. A következő parancsfájl-példák feltételezik, hogy a .NET-tárházat használja egy Windows rendszerű gépen. Ha más környezetben dolgozik, állítsa be megfelelően az elérési utakat, a fájlkiterjesztések és az elérési út elválasztóit.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozta létre a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben navigáljon a tárház klónozott példányának **\services\scripts\local\launch** mappájához.

1. Futtassa a következő parancsokat a **számítógépek** CLI-eszköz telepítéséhez, és jelentkezzen be az Azure-fiókjába:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **Start. cmd** parancsfájlt. A parancsfájl a következő információk megadását kéri:
   * Egy megoldás neve.
   * A használandó előfizetés.
   * A használni kívánt Azure-Adatközpont helye.

     A szkript létrehoz egy erőforráscsoportot az Azure-ban a megoldás nevével. Ez az erőforráscsoport tartalmazza a megoldás-gyorsító által használt Azure-erőforrásokat. Akkor törölheti ezt az erőforráscsoportot, ha már nincs szüksége a megfelelő erőforrásokra.

     A parancsfájl környezeti változókat is hozzáad a helyi számítógéphez előtaggal rendelkező **számítógépekhez** . Ezek a környezeti változók adják meg a távoli Figyelés részleteit, hogy el tudják olvasni egy Azure Key Vault erőforrásból. Ez a Key Vault erőforrás, ahol a távoli figyelés beolvassa a konfigurációs értékeit a rendszerből.

     > [!TIP]
     > A parancsfájl befejeződése után a környezeti változókat egy ** \<your home folder\> \\ . PCs \\ \<solution name\> . env**nevű fájlba menti. Használhatja őket a jövőbeli megoldás-gyorsító üzembe helyezésekhez. Vegye figyelembe, hogy a helyi gépen beállított környezeti változók a **Docker-összeállítás**futtatásakor felülbírálják a **szolgáltatások \\ \\ helyi \\ . env** fájljának értékeit.

1. Kilépés a parancssori környezetből.

### <a name="use-existing-azure-resources"></a>Meglévő Azure-erőforrások használata

Ha már létrehozta a szükséges Azure-erőforrásokat, hozza létre a megfelelő környezeti változókat a helyi gépen.
Állítsa be a környezeti változókat a következőkhöz:
* **PCS_KEYVAULT_NAME** – a Azure Key Vault erőforrás neve
* **PCS_AAD_APPID** – a HRE alkalmazás azonosítója
* **PCS_AAD_APPSECRET** – a HRE alkalmazás titka

A konfigurációs értékek beolvasása ebből a Azure Key Vault erőforrásból történik. Ezek a környezeti változók menthetők a ** \<your home folder\> \\ . PC \\ \<solution name\> . env** fájlba a telepítésből. Vegye figyelembe, hogy a helyi gépen beállított környezeti változók a **Docker-összeállítás**futtatásakor felülbírálják a **szolgáltatások \\ \\ helyi \\ . env** fájljának értékeit.

A webszolgáltatás által igényelt néhány konfiguráció a kezdeti telepítéskor létrehozott **Key Vault** egy példányában van tárolva. A kulcstartó megfelelő változóit szükség szerint módosítani kell.