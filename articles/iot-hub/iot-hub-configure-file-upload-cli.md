---
title: Az Azure CLI-vel az IoT Hub fájlfeltöltés konfigurálása |} A Microsoft Docs
description: Konfigurálása fájlt tölt fel az Azure IoT hubba a platformfüggetlen Azure parancssori felületével.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6cd0b657c8d0352c41e0da538396b166d633306a
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42057566"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Az IoT Hub Azure CLI-vel a fájlfeltöltések konfigurálása

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

A [fájlfeltöltés az eszközökről](iot-hub-devguide-file-upload.md), először társítania kell egy Azure Storage-fiókot az IoT hubbal. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.

* [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Az Azure IoT hubra. Ha nem rendelkezik egy IoT hubot, használhatja a [ `az iot hub create` parancs](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) hozhat létre egyet vagy [hozzon létre egy IoT hubot, a portál használatával](iot-hub-create-through-portal.md).

* Egy Azure Storage-fiók. Ha nem rendelkezik Azure Storage-fiókot, akkor használhatja a [Azure CLI - storage-fiókok kezelése](../storage/common/storage-azure-cli.md#manage-storage-accounts) hozzon létre egyet, vagy a portál használata [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókkal

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login paranccsal](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Használja a következő [paranccsal listát készíthet az Azure-fiókok](https://docs.microsoft.com/cli/azure/account) elérhető kell használni:

    ```azurecli
    az account list
    ```

    A következő paranccsal, amely az IoT hub létrehozása a parancsok futtatásához használni kívánt előfizetés kiválasztásához. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>A storage-fiók adatainak beolvasása

A következő lépések azt feltételezik, hogy a tárfiók tárfiókkulcsait létrehozott a **Resource Manager** -alapú üzemi modellben, és nem a **klasszikus** üzemi modellt.

Fájlfeltöltés az eszközökről származó konfigurálásához szükség van a kapcsolati karakterláncot egy Azure storage-fiókot. A storage-fiókot az IoT hub az azonos előfizetésben kell lennie. A storage-fiókban található blob-tárolóra nevét is szükséges. A következő parancsot használja a storage-fiók kulcsok lekéréséhez:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Jegyezze fel a **connectionString** értéket. A következő lépésekben van szükség.

Meglévő blob tároló használata a fájlfeltöltési, vagy hozzon létre egy újat:

* A meglévő, a tárfiókban található blob-tárolók listájában, használja a következő parancsot:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Hozzon létre egy blobtárolót a tárfiókban található, használja a következő parancsot:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fájl feltöltése

Mostantól konfigurálhatja az IoT hub lehetővé [fájlok feltöltése az IoT hub](iot-hub-devguide-file-upload.md) használatával a tárfiók részleteit.

A konfigurációs van szükség a következő értékeket:

* **A tároló**: az aktuális Azure-előfizetés társítása az IoT hub az Azure storage-fiókban lévő blobtárolóba. A szükséges tárolási fiók adatait az előző szakaszban lekért. Az IoT Hub automatikusan létrehozza az SAS URI-k eszközöket használja, ha azok a fájlok feltöltése a blob-tárolóba írási engedéllyel rendelkező.

* **A feltöltött fájlokkra vonatkozó értesítések fogadása**: fájl feltöltése értesítések engedélyezése vagy letiltása.

* **SAS-élettartam**: Ez a beállítás akkor a time-to-live, az eszközt az IoT Hub által visszaadott SAS URI-azonosítókat. Alapértelmezés szerint egy óra beállítva.

* **Értesítési beállítások alapértelmezett TTL fájl**: A time-to-live-fájl feltöltése értesítési, mielőtt lejár. Alapértelmezés szerint egy nap beállítva.

* **A fájl értesítési kézbesítések maximális száma**: hányszor az IoT Hub fájlt próbál meg feltölteni értesítés. Alapértelmezés szerint a 10-re állítva.

A következő Azure CLI-parancsok használatával adja meg a fájl feltöltése beállításait az IoT hubnak:

<!--Robinsh this is out of date, add cloud powershell -->

Bash-felületen használja:

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

A fájlok feltöltése konfigurálása az IoT hub a következő paranccsal tekintheti meg:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>További lépések

Az IoT Hub a fájl feltöltése képességeivel kapcsolatos további információkért lásd: [fájlfeltöltés az eszközökről](iot-hub-devguide-file-upload.md).

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Az IoT-megoldás az alapoktól biztonságos mentése](../iot-fundamentals/iot-security-ground-up.md)
