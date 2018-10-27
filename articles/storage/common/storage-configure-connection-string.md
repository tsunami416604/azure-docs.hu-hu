---
title: Az Azure Storage kapcsolati sztringjének konfigurálása |} A Microsoft Docs
description: Az Azure storage-fiókhoz tartozó kapcsolati karakterlánc konfigurálása. Egy kapcsolati karakterláncot tartalmazza az információk szükségesek ahhoz, hogy engedélyezze a hozzáférést a tárfiókhoz futásidőben az alkalmazásból.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/12/2017
ms.author: tamram
ms.component: common
ms.openlocfilehash: 75f8e5605b84b21abf92cd77a71793dbceb4a62b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139042"
---
# <a name="configure-azure-storage-connection-strings"></a>Configure Azure Storage connection strings (Az Azure Storage kapcsolati sztringek konfigurálása)

A kapcsolati karakterlánca tartalmazza a szükséges, hogy az alkalmazás hozzáférési adatokat a futási időben Azure Storage-fiók hitelesítési adatait. Kapcsolati karakterláncok konfigurálhatja:

* Csatlakozás az Azure storage emulator.
* Az Azure storage-fiók eléréséhez.
* Közös hozzáférésű jogosultságkód (SAS) keresztül az Azure-ban megadott erőforrások eléréséhez.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>A kapcsolati karakterlánc tárolására
Az alkalmazás az Azure Storage-kérelmek hitelesítéséhez futásidőben a kapcsolati karakterlánc hozzáféréssel kell rendelkeznie. A kapcsolati karakterlánc tárolására szolgáló számos lehetősége van:

* Egy alkalmazás fut, az asztalon vagy egy eszközön a kapcsolati karakterláncot tárolhatja egy **app.config** vagy **web.config** fájlt. Adja hozzá a kapcsolati karakterláncot a **AppSettings** szakasz ezeket a fájlokat.
* Az Azure cloud service-ben futó alkalmazás a kapcsolati karakterláncot tárolhatja a [Azure szolgáltatás konfigurációs sémáját (.cscfg) fájljában](https://msdn.microsoft.com/library/ee758710.aspx). Adja hozzá a kapcsolati karakterláncot a **ConfigurationSettings** szakaszában a szolgáltatás konfigurációs fájlja.
* A kapcsolati karakterlánc használhatja közvetlenül a kódba. Azt javasoljuk azonban, hogy a kapcsolati karakterláncot a legtöbb esetben a konfigurációs fájlban tárolni.

A kapcsolati karakterlánc egy konfigurációs fájl tárolása megkönnyíti a storage emulator és a felhőben egy Azure storage-fiók közötti váltáshoz a kapcsolati karakterlánc frissítése. Csak akkor módosíthatók a kapcsolati karakterláncot, hogy a célkörnyezetben mutasson.

Használhatja a [a Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) eléréséhez a kapcsolati karakterláncot, függetlenül attól, ahol az alkalmazás fut-e futásidőben.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>A storage Emulator kapcsolati karakterlánc létrehozása
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

A storage emulator kapcsolatos további információkért lásd: [az Azure storage emulator használata a fejlesztési és tesztelési](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Az Azure storage-fiókhoz tartozó kapcsolati karakterlánc létrehozása
Egy kapcsolati karakterláncot az Azure storage-fiók létrehozásához használja a következő formátumban. Azt jelzi, hogy kívánja-e a tárfiók (ajánlott) HTTPS-en keresztül csatlakozni vagy a HTTP, cserélje le `myAccountName` a storage-fiókot, és cserélje le a nevű `myAccountKey` értékeket a fiók hozzáférési kulcsára:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

A kapcsolati karakterlánc például hasonlóan néznének ki:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Bár az Azure Storage támogatja a HTTP és HTTPS egy kapcsolati karakterláncot *erősen ajánlott a HTTPS*.

> [!TIP]
> A tárfiók kapcsolati karakterláncokat a annak a [az Azure portal](https://portal.azure.com). Navigáljon a **beállítások** > **hozzáférési kulcsok** a tárfiók menüjében panelen mindkét elsődleges és másodlagos hozzáférési kulcs kapcsolati karakterláncok megtekintéséhez.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Hozzon létre egy kapcsolati karakterláncot egy közös hozzáférésű jogosultságkód használatával
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont
Explicit Szolgáltatásvégpontok megadhatja a kapcsolati karakterláncban az alapértelmezett végpontok használata helyett. Hozzon létre egy kapcsolati karakterláncot, amely egy explicit végpontot határozza meg, hogy adja meg a teljes szolgáltatás-végpont az egyes szolgáltatások, többek között a protokoll-meghatározása (HTTPS (javasolt) vagy HTTP), a következő formátumban:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Egy forgatókönyv, ahol előfordulhat, hogy explicit végpontokat szeretne akkor, ha a Blob storage-végponthoz rendeltünk egy [egyéni tartomány](../blobs/storage-custom-domain-name.md). Ebben az esetben megadhat egyéni végpontra a Blob Storage a kapcsolati karakterláncban. A többi szolgáltatás tartozó alapértelmezett végpontok igény szerint adja meg, ha az alkalmazás használja őket.

Íme egy példa egy kapcsolati karakterláncot, amely meghatározza a Blob szolgáltatás explicit végpont:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Ebben a példában adja meg az összes szolgáltatás, így a Blob szolgáltatás egy egyéni tartományt explicit végpontok:

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

A végpont értékeket a kapcsolati karakterlánc segítségével a kérelem URI-k a tárolási szolgáltatásokhoz hozhatnak létre, és ettől eltérő konfigurációt tesznek a programkód visszaadott bármely URI-k formájában.

Ha már hozzárendelt egy storage-végpont egyéni tartományt, és hagyja ki, hogy a végpont egy kapcsolati karakterláncból, majd nem tudja használni a kapcsolati karakterlánc hozzáférési adatokat a szolgáltatás a kódból.

> [!IMPORTANT]
> Lehet, hogy a szolgáltatási végpont értékeket a kapcsolati karakterláncokban lévő megfelelően formázott URI-k, beleértve a `https://` (ajánlott) vagy `http://`. Mivel az Azure Storage egyelőre nem támogatják a HTTPS az egyéni tartományokhoz, *kell* adja meg `http://` bármely végpont URI-t, amely egy egyéni tartomány mutat.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Hozzon létre egy kapcsolati karakterláncot egy végpont utótagja
Hozzon létre egy kapcsolati karakterláncot a régiókban, vagy a példányok különböző végpont utótagok, a storage szolgáltatás például az Azure China vagy az Azure Government, használja a következő kapcsolati karakterlánc formátuma. Azt jelzi, hogy kívánja-e a tárfiók (ajánlott) HTTPS-en keresztül csatlakozni vagy a HTTP, cserélje le `myAccountName` cserélje a tárfiókja nevére, `myAccountKey` a tárfiók hozzáférési kulcsát, és cserélje le a `mySuffix` az URI utótaggal:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Íme egy példa tárolási szolgáltatások Azure China a kapcsolati karakterláncot:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Kapcsolati karakterlánc elemzéséhez
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>További lépések
* [Az Azure storage emulator használata a fejlesztési és tesztelési célra](storage-use-emulator.md)
* [Azure Storage-tallózók](storage-explorers.md)
* [Közös hozzáférésű Jogosultságkódok (SAS) használata](storage-dotnet-shared-access-signature-part-1.md)

