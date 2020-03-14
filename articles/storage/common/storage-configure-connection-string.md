---
title: A kapcsolatok karakterláncának konfigurálása
titleSuffix: Azure Storage
description: Konfiguráljon egy Azure Storage-fiókhoz tartozó kapcsolatok karakterláncot. A kapcsolati karakterlánc tartalmazza azokat az információkat, amelyek szükségesek ahhoz, hogy a megosztott kulcsos hitelesítés használatával engedélyezze a Storage-fiókhoz való hozzáférést az alkalmazásból.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268352"
---
# <a name="configure-azure-storage-connection-strings"></a>Configure Azure Storage connection strings (Az Azure Storage kapcsolati sztringek konfigurálása)

A kapcsolati karakterlánc tartalmazza azokat az engedélyezési adatokat, amelyek szükségesek ahhoz, hogy az alkalmazás hozzáférjen az Azure Storage-fiókban lévő adatokhoz a megosztott kulcs engedélyezésekor. A kapcsolódási karakterláncokat a következőre állíthatja be:

* Kapcsolódjon az Azure Storage emulatorhoz.
* Hozzáférés egy Storage-fiókhoz az Azure-ban.
* Hozzáférés a megadott erőforrásokhoz az Azure-ban egy közös hozzáférési aláírás (SAS) használatával.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>A kapcsolatok karakterláncának megtekintése és másolása

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>A kapcsolatok karakterláncának tárolása

Az alkalmazásnak a futásidejű kapcsolati sztringhez kell hozzáférnie, hogy engedélyezze az Azure Storage-ba irányuló kéréseket. A következő több lehetőség áll rendelkezésre a kapcsolatok karakterláncának tárolásához:

* A kapcsolatok karakterláncát egy környezeti változóban tárolhatja.
* Az asztalon vagy eszközön futó alkalmazások tárolhatják a kapcsolati karakterláncot egy **app. config** vagy **web. config** fájlban. Adja hozzá a kapcsolódási karakterláncot a **appSettings** szakaszhoz ezekben a fájlokban.
* Az Azure Cloud Service-ben futó alkalmazások tárolhatják a kapcsolatok karakterláncát az [Azure szolgáltatás konfigurációs sémájának (. cscfg) fájljában](https://msdn.microsoft.com/library/ee758710.aspx). Adja hozzá a kapcsolódási karakterláncot a szolgáltatás konfigurációs fájljának **ConfigurationSettings** szakaszához.

A kapcsolati karakterlánc egy konfigurációs fájlban való tárolása megkönnyíti a kapcsolati sztring frissítését a Storage-emulátor és a felhőben található Azure Storage-fiók közötti váltáshoz. Csak úgy kell szerkesztenie a kapcsolódási karakterláncot, hogy az a célként megadott környezetre mutasson.

A [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) használatával a kapcsolati karakterláncot futásidőben érheti el, függetlenül attól, hogy az alkalmazás hol fut.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>A Storage emulatorhoz tartozó kapcsolatok karakterláncának konfigurálása

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

További információ a Storage emulatorról: [a fejlesztéshez és teszteléshez használja az Azure Storage emulatort](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Egy Azure Storage-fiókhoz tartozó kapcsolatok karakterláncának konfigurálása

Az Azure Storage-fiókhoz tartozó kapcsolódási karakterlánc létrehozásához használja a következő formátumot. Jelezze, hogy HTTPS-kapcsolaton (ajánlott) vagy HTTP-n keresztül szeretne-e csatlakozni a Storage-fiókhoz, cserélje le a `myAccountName` a Storage-fiók nevére, és cserélje le a `myAccountKey`t a fiók hozzáférési kulcsára:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

A kapcsolódási karakterlánc például a következőhöz hasonló lehet:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Bár az Azure Storage a HTTP-t és a HTTPS-t is támogatja egy kapcsolati sztringben, a *https használata kifejezetten ajánlott*.

> [!TIP]
> A Storage-fiókhoz tartozó kapcsolatok karakterláncait a [Azure Portal](https://portal.azure.com)találja. Navigáljon a **beállítások** > **hozzáférési kulcsok** elemre a Storage-fiók menüjében, és tekintse meg az elsődleges és másodlagos hozzáférési kulcsok kapcsolati karakterláncait.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Kapcsolati karakterlánc létrehozása közös hozzáférési aláírás használatával

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Egy explicit tárolási végponthoz tartozó kapcsolatok karakterláncának létrehozása

Az alapértelmezett végpontok használata helyett explicit szolgáltatási végpontokat is megadhat a kapcsolatok karakterláncában. Explicit végpontot megadó kapcsolati sztring létrehozásához adja meg az egyes szolgáltatások teljes szolgáltatási végpontját, beleértve a protokoll specifikációját (HTTPS (ajánlott) vagy HTTP), a következő formátumban:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Ha a blob Storage-végpontot [Egyéni tartományra](../blobs/storage-custom-domain-name.md)rendelte, az egyik esetben érdemes explicit végpontot megadni. Ebben az esetben megadhatja az egyéni végpontot a blob Storage-hoz a kapcsolatok karakterláncában. Igény szerint megadhatja az alapértelmezett végpontokat a többi szolgáltatáshoz, ha az alkalmazás ezeket használja.

Íme egy példa a Blob service explicit végpontját megadó kapcsolatok sztringre:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Ez a példa az összes szolgáltatás explicit végpontját határozza meg, beleértve a Blob service egyéni tartományát is:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

A kapcsolódási karakterláncban található végponti értékek a kérés URI-azonosítóinak a tárolási szolgáltatásokhoz való összeállítására szolgálnak, és a kód számára visszaadott URI azonosítók megadását írják elő.

Ha hozzárendelt egy tárolási végpontot egy egyéni tartományhoz, és kihagyja a végpontot egy kapcsolati karakterláncból, akkor nem fogja tudni használni a kapcsolati sztringet, hogy az adott szolgáltatásban lévő adatokhoz hozzáférjen a kódból.

> [!IMPORTANT]
> A kapcsolatok karakterláncában a szolgáltatás végpontjának értékeinek megfelelően formázott URI-azonosítóknak kell lenniük, beleértve a `https://` (ajánlott) vagy a `http://`. Mivel az Azure Storage még nem támogatja a HTTPS-t az egyéni tartományokhoz, meg *kell* adnia `http://` minden olyan végpont URI-azonosítóhoz, amely egyéni tartományra mutat.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Végponti utótaggal rendelkező kapcsolatok karakterlánc létrehozása

Ha különböző végponti utótaggal rendelkező régiókban vagy példányokban szeretné létrehozni a tárolási szolgáltatáshoz tartozó kapcsolódási karakterláncot, például az Azure China 21Vianet vagy a Azure Governmenthoz, használja a következő kapcsolódási karakterlánc-formátumot. Jelezze, hogy HTTPS-kapcsolaton (ajánlott) vagy HTTP-n keresztül szeretne-e csatlakozni a Storage-fiókhoz, cserélje le a `myAccountName`t a Storage-fiók nevére, cserélje le a `myAccountKey`t a fiók hozzáférési kulcsára, és cserélje le a `mySuffix` értéket az URI utótagra:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Íme egy példa a tárolási szolgáltatásokhoz az Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>A kapcsolatok karakterláncának elemzése

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>További lépések

* [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez](storage-use-emulator.md)
* [Azure Storage-tallózók](storage-explorers.md)
* [Közös hozzáférésű aláírások (SAS) használata](storage-sas-overview.md)
