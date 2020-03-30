---
title: Fájlfeltöltés konfigurálása az IoT Hubba az Azure CLI használatával | Microsoft dokumentumok
description: Hogyan konfigurálhatja a fájlfeltöltések az Azure IoT Hub a többplatformos Azure CLI használatával.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302525"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>IoT Hub-fájlfeltöltések konfigurálása az Azure CLI használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Fájlok [feltöltéséhez egy eszközről](iot-hub-devguide-file-upload.md)először hozzá kell rendelnie egy Azure Storage-fiókot az IoT hubhoz. Használhatja a meglévő tárfiókot, vagy hozzon létre egy újat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Egy Azure IoT-központ. Ha nem rendelkezik IoT-központtal, a [ `az iot hub create` paranccsal](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) létrehozhat egyet, vagy [létrehozhat egy IoT-központot a portálon keresztül.](iot-hub-create-through-portal.md)

* Egy Azure Storage-fiók. Ha nem rendelkezik Azure Storage-fiókkal, az Azure CLI használatával hozhat létre egyet. További információ: [Tárfiók létrehozása](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be és állítsa be Azure-fiókját

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login parancsot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat](https://docs.microsoft.com/cli/azure/account):

    ```azurecli
    az account list
    ```

    A következő paranccsal válassza ki az ioT hub létrehozásához a parancsok futtatásához használni kívánt előfizetést. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>A tárfiók adatainak lekérése

A következő lépések feltételezik, hogy a tárfiókot az **Erőforrás-kezelő** központi telepítési modell, és nem a **klasszikus** központi telepítési modell használatával hozta létre.

A fájlok feltöltésének konfigurálásához az eszközökről egy Azure-tárfiók csatlakozási karakterláncára van szükség. A tárfióknak ugyanabban az előfizetésben kell lennie, mint az IoT hubnak. Szüksége van egy blobtároló nevére is a tárfiókban. A tárfiók kulcsainak lekéréséhez használja a következő parancsot:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Jegyezze fel a **connectionString** értékét. Szüksége van rá a következő lépésekben.

Használhat egy meglévő blobtárolót a fájlfeltöltéshez, vagy létrehozhat egy újat:

* A meglévő blobtárolók listázásához használja a következő parancsot:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Blob-tároló létrehozásához a tárfiókban a következő parancsot használja:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fájl feltöltése

Most már konfigurálhatja az IoT hub, hogy lehetővé tegye a [fájlok feltöltését az IoT hub](iot-hub-devguide-file-upload.md) a tárfiók adatait.

A konfigurációhoz a következő értékek szükségesek:

* **Storage container:** Egy blob tároló egy Azure-tárfiókban a jelenlegi Azure-előfizetés az IoT hub társítására. Az előző szakaszban lekérte a szükséges tárfiók-adatokat. Az IoT Hub automatikusan létrehozza a SAS URI-k írási engedélyekkel ezt a blob tárolót az eszközök számára, amelyeket a fájlok feltöltésekor használni.

* **Értesítések fogadása a feltöltött fájlokról**: Fájlfeltöltési értesítések engedélyezése vagy letiltása.

* **SAS TTL**: Ez a beállítás az IoT Hub által az eszközre visszaadott SAS URI-k élő ideje. Alapértelmezés szerint egy órára van beállítva.

* **Fájl értesítési beállítások alapértelmezett TTL**: A fájlfeltöltési értesítés lejárta előtti ideje. Alapértelmezés szerint egy napra van beállítva.

* **A fájlok kézbesítésének maximális kézbesítési száma:** Az IoT Hub hányszor kísérel meg fájlfeltöltési értesítést kézbesíteni. Alapértelmezés szerint 10-re van állítva.

A következő Azure CLI-parancsok segítségével konfigurálhatja a fájlfeltöltési beállításokat az IoT-központban:

<!--Robinsh this is out of date, add cloud powershell -->

A bash shell, használja:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Az IoT-központ fájlfeltöltési konfigurációját a következő paranccsal tekintheti meg:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>További lépések

Az IoT Hub fájlfeltöltési lehetőségeiről további információt a [Fájlok feltöltése eszközről](iot-hub-devguide-file-upload.md)című témakörben talál.

Az Azure IoT Hub kezeléséről az alábbi hivatkozásokra kattintva olvashat bővebben:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Biztosítsa IoT-megoldását az alapoktól kezdve](../iot-fundamentals/iot-security-ground-up.md)
