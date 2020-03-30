---
title: Kapcsolati karakterlánc konfigurálása
titleSuffix: Azure Storage
description: Konfiguráljon egy kapcsolati karakterláncot egy Azure-tárfiókhoz. A kapcsolati karakterlánc tartalmazza azokat az információkat, amelyek az alkalmazásból származó tárfiókhoz való hozzáférés engedélyezéséhez szükségesek futásidőben a megosztott kulcs engedélyezése használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268352"
---
# <a name="configure-azure-storage-connection-strings"></a>Configure Azure Storage connection strings (Az Azure Storage kapcsolati sztringek konfigurálása)

A kapcsolati karakterlánc tartalmazza az alkalmazás számára az Azure Storage-fiók ban a megosztott kulcs engedélyezése használatával futásidőben az adatok eléréséhez szükséges engedélyezési adatokat. A kapcsolati karakterláncokat a következőkre állíthatja be:

* Csatlakozzon az Azure storage-emulátorhoz.
* Az Azure-beli tárfiók elérése.
* Hozzáférés a megadott erőforrások az Azure-ban egy megosztott hozzáférésű aláírás (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Kapcsolati karakterlánc megtekintése és másolása

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Kapcsolati karakterlánc tárolása

Az alkalmazásnak futásidőben hozzá kell férnie a kapcsolati karakterlánchoz az Azure Storage-nak küldött kérelmek engedélyezéséhez. A kapcsolati karakterlánc tárolására több lehetőség is kínálhatja:

* A kapcsolati karakterláncot egy környezeti változóban tárolhatja.
* Az asztalon vagy eszközön futó alkalmazások a kapcsolati karakterláncot **egy app.config** vagy **web.config** fájlban tárolhatják. Adja hozzá a kapcsolati karakterláncot a fájlok **AppSettings** szakaszához.
* Az Azure-felhőszolgáltatásban futó alkalmazások a kapcsolati karakterláncot az [Azure szolgáltatáskonfigurációs séma (.cscfg) fájlban](https://msdn.microsoft.com/library/ee758710.aspx)tárolhatják. Adja hozzá a kapcsolati karakterláncot a szolgáltatáskonfigurációs fájl **ConfigurationSettings** szakaszához.

A kapcsolati karakterlánc konfigurációs fájlban való tárolása megkönnyíti a kapcsolati karakterlánc frissítését a storage-emulátor és egy Azure storage-fiók közötti váltáshoz a felhőben. Csak a kapcsolati karakterláncot kell szerkeszteni, hogy a célkörnyezetre mutasson.

A Microsoft [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) segítségével futásidőben érheti el a kapcsolati karakterláncot, függetlenül attól, hogy az alkalmazás hol fut.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>A tárolóemulátor kapcsolati karakterláncának konfigurálása

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

A storage-emulátorról további információt [a Fejlesztéshez és teszteléshez az Azure-tároló emulátorának használata című témakörben talál.](storage-use-emulator.md)

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Kapcsolati karakterlánc konfigurálása Azure-tárfiókhoz

Ha hozzon létre egy kapcsolati karakterláncot az Azure storage-fiókjához, használja a következő formátumot. Adja meg, hogy HTTPS -en (ajánlott) vagy HTTP-n keresztül kíván-e csatlakozni a tárfiókhoz, cserélje le `myAccountName` a tárfiók nevét, és cserélje le `myAccountKey` a fiók hozzáférési kulcsát:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

A kapcsolati karakterlánc például a következőhöz hasonló lehet:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Bár az Azure Storage támogatja a HTTP és https protokollt is egy kapcsolati karakterláncban, *a HTTPS használata erősen ajánlott.*

> [!TIP]
> A tárfiók kapcsolati karakterláncait az [Azure Portalon](https://portal.azure.com)találhatja meg. A **beállítások** > hoz a tárfiók menüpaneljének**hozzáférési kulcsai** között az elsődleges és a másodlagos hozzáférési kulcsok kapcsolati karakterláncainak megtekintéséhez.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Kapcsolati karakterlánc létrehozása megosztott hozzáférésű aláírással

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Kapcsolati karakterlánc létrehozása explicit tárolóvégponthoz

Az alapértelmezett végpontok használata helyett explicit szolgáltatásvégpontokat is megadhat a kapcsolati karakterláncban. Explicit végpontot megadó kapcsolati karakterlánc létrehozásához adja meg az egyes szolgáltatások teljes szolgáltatásvégpontját, beleértve a protokollspecifikációt (HTTPS (ajánlott) vagy HTTP), a következő formátumban:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Az egyik forgatókönyv, ahol explicit végpontot kíván megadni, az, amikor a Blob storage-végpontot [egy egyéni tartományhoz](../blobs/storage-custom-domain-name.md)rendeli. Ebben az esetben megadhatja az egyéni végpont blob storage a kapcsolati karakterláncban. Ha az alkalmazás használja őket, megadhatja a többi szolgáltatás alapértelmezett végpontjait.

Íme egy példa egy kapcsolati karakterláncra, amely a Blob szolgáltatás explicit végpontját adja meg:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Ez a példa explicit végpontokat határoz meg az összes szolgáltatáshoz, beleértve a Blob szolgáltatás egyéni tartományát is:

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

A végpont értékek egy kapcsolati karakterlánc létrehozásához használt kérelem URI-k a tárolási szolgáltatások, és diktálja a kódot visszaadott URI-k formáját.

Ha leképezett egy tárolóvégpontot egy egyéni tartományhoz, és kihagyja a végpontot egy kapcsolati karakterláncból, akkor nem fogja tudni használni ezt a kapcsolati karakterláncot az adott szolgáltatás ban lévő adatok eléréséhez a kódból.

> [!IMPORTANT]
> A kapcsolati karakterláncokban lévő végpontértékeknek jól formázott `https://` URI-knak kell lenniük, beleértve (ajánlott) vagy `http://`. Mivel az Azure Storage még nem támogatja *must* a `http://` HTTPS egyéni tartományok, meg kell adnia minden végpont URI, amely egy egyéni tartományra mutat.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Végpontutótaggal rendelkező kapcsolati karakterlánc létrehozása

Ha hozzon létre egy kapcsolati karakterláncot egy tárolási szolgáltatás régiókban vagy példányokban különböző végpont-utótagok, például az Azure China 21Vianet vagy az Azure Government, használja a következő kapcsolati karakterlánc formátumban. Adja meg, hogy HTTPS -en (ajánlott) vagy HTTP-n keresztül kíván-e csatlakozni a tárfiókhoz, cserélje le `myAccountName` a tárfiók nevét, cserélje le `myAccountKey` a fiók hozzáférési kulcsát, és cserélje le `mySuffix` az URI-utótagot:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Íme egy példa az Azure China 21Vianet tárolási szolgáltatásainak kapcsolati karakterláncára:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Kapcsolati karakterlánc elemzése

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>További lépések

* [Az Azure storage-emulátor használata fejlesztéshez és teszteléshez](storage-use-emulator.md)
* [Az Azure Storage-kezelők](storage-explorers.md)
* [Megosztott hozzáférésű aláírások (SAS) használata](storage-sas-overview.md)
