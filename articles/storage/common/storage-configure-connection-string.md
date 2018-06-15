---
title: Az Azure Storage kapcsolati karakterláncának konfigurálása |} Microsoft Docs
description: Az Azure-tárfiók kapcsolati karakterláncának konfigurálása. A kapcsolati karakterlánc hitelesíti a hozzáférést a tárfiókhoz futásidőben az alkalmazásból szükséges információkat tartalmazza.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: dee0fd68ccaf80ff6e44ee10fa24bb19e220d0d5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240829"
---
# <a name="configure-azure-storage-connection-strings"></a>Configure Azure Storage connection strings (Az Azure Storage kapcsolati karakterláncok konfigurálása)

A kapcsolati karakterláncok futásidőben Azure Storage-fiók hozzáférési adatainak az alkalmazáshoz szükséges hitelesítési adatokat tartalmaz. Konfigurálhatja a kapcsolati karakterláncokkal:

* Az Azure storage emulator csatlakozni.
* Az Azure-ban a tárfiók eléréséhez.
* A közös hozzáférésű jogosultságkód (SAS) keresztül az Azure-ban megadott erőforrások eléréséhez.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>A kapcsolati karakterlánc tárolásának
Az alkalmazás hozzá kell férniük az Azure Storage intézett kérelmek hitelesítéséhez szükséges futásidőben a kapcsolati karakterláncot. A kapcsolati karakterlánc tárolására több lehetőség közül választhat:

* Egy alkalmazás fut, az asztalon vagy egy eszközön tárolhatja a kapcsolati karakterlánc egy **app.config** vagy **web.config** fájlt. A kapcsolati karakterláncot adja hozzá a **AppSettings** szakasz ezeket a fájlokat.
* Egy Azure-felhőszolgáltatásban futó valamelyik alkalmazás a kapcsolati karakterlánc tárolhatja a [Azure szolgáltatás konfigurációs (.cscfg) fájl](https://msdn.microsoft.com/library/ee758710.aspx). A kapcsolati karakterláncot adja hozzá a **ConfigurationSettings** a szolgáltatás konfigurációs fájl részét.
* A kapcsolati karakterláncot a kódban közvetlenül is használhatja. Azt javasoljuk azonban, hogy a legtöbb esetben egy konfigurációs fájlban tárolhatja a kapcsolati karakterlánc.

A kapcsolati karakterlánc egy konfigurációs fájlban tárolja megkönnyíti, hogy a kapcsolati karakterláncot Váltás a storage emulator és a felhőben Azure storage-fiók frissítéséhez. Csak akkor módosíthatók, hogy a célkörnyezet mutasson a kapcsolati karakterláncot.

Használhatja a [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) a kapcsolati karakterláncot, függetlenül attól, amelyen fut az alkalmazás futásidőben eléréséhez.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>A storage Emulator kapcsolati karakterlánc létrehozása
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

A storage emulator kapcsolatos további információkért lásd: [fejlesztéshez és teszteléshez használja az Azure storage emulator](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Hozzon létre egy Azure-tárfiók kapcsolati karakterlánca
Az Azure-tárfiók kapcsolati karakterláncot, használja a következő formátumban. Azt jelzi, hogy csatlakozni a tárfiók keresztül (ajánlott) HTTPS vagy HTTP, cserélje le `myAccountName` nevű, a tárfiók, és cserélje ki `myAccountKey` rendelkező a hívóbetűre:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Például a kapcsolati karakterlánc hasonlóan néznének ki:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Bár az Azure Storage támogatja a HTTP és HTTPS kapcsolat-karakterláncban *HTTPS erősen ajánlott*.

> [!TIP]
> A tárfiók kapcsolati karakterláncokat megtalálhatja a [Azure-portálon](https://portal.azure.com). Navigáljon a **beállítások** > **hívóbetűk** a tárfiók menü panelen kapcsolati karakterláncainak mindkét elsődleges és másodlagos hívóbetűk megjelenítéséhez.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>A kapcsolati karakterlánc használatával a közös hozzáférésű jogosultságkód létrehozása
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Explicit tárolási a végpont-kapcsolati karakterlánc létrehozása
Explicit Szolgáltatásvégpontok adhat meg a kapcsolati karakterlánc alapértelmezett végpontok használata helyett. Hozzon létre egy kapcsolati karakterláncot, amely meghatározza egy explicit végpontot, adja meg a teljes szolgáltatási végpont az egyes szolgáltatásokhoz, beleértve a protokollt (HTTPS (ajánlott) vagy HTTP), a következő formátumban:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

A Blob storage végponthoz átnevezte egy forgatókönyvet, ahol előfordulhat, hogy szeretne megadni egy explicit végpont esetén egy [egyéni tartomány](../blobs/storage-custom-domain-name.md). Ebben az esetben a kapcsolódási karakterláncban a Blob Storage-egyéni végpontot is megadhat. Opcionálisan megadhat alapértelmezett végpontok a többi szolgáltatást, ha az alkalmazás azokat.

Íme egy példa egy kapcsolati karakterláncot, amely meghatározza a Blob szolgáltatás explicit végpont:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Ebben a példában az összes szolgáltatáshoz, beleértve a Blob szolgáltatás az egyéni tartománynév explicit végpontok határozza meg:

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

A végpont értékek a kapcsolati karakterláncban a kérelem URI-azonosítók a tárolási szolgáltatásokhoz, és bármely URI, amely a kódot a rendszer visszairányítja formájában előírják szolgálnak.

Ha egy tárolási végpont már leképezve az egyéni tartománynév, és hagyja ki ezt a kapcsolati karakterláncot az adott végpontra, majd csak akkor használhatják a kapcsolati karakterlánc az, hogy a szolgáltatás a hozzáférési adatok a felhasználói kódból.

> [!IMPORTANT]
> Lehet, hogy a szolgáltatási végpont értékeit a kapcsolati karakterláncok megfelelően formázott URI-k, beleértve a `https://` (ajánlott) vagy `http://`. Azure Storage egyelőre nem támogatják a HTTPS az egyéni tartományok, mert Ön *kell* meg `http://` a tetszőleges végpontot URI mutató egyéni tartományhoz.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Hozzon létre egy kapcsolati karakterláncot egy végpont utótag
A régiókban, vagy másik végpont utótagok példányok társzolgáltatás kapcsolati karakterláncot, mint az Azure China vagy az Azure Governmentnek használja a következő kapcsolati karakterlánc-formátum. Azt jelzi, hogy csatlakozni a tárfiók keresztül (ajánlott) HTTPS vagy HTTP, cserélje le `myAccountName` cserélje le a tárfiók nevével, `myAccountKey` a hívóbetűre, és cserélje ki a `mySuffix` URI utótaggal rendelkező:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Íme egy példa kapcsolati karakterláncot az Azure Kínában tárolási szolgáltatások:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>A kapcsolódási karakterlánc elemzésekor
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>További lépések
* [Az Azure storage emulator használata a fejlesztéshez és teszteléshez](storage-use-emulator.md)
* [Az Azure Storage-kezelők](storage-explorers.md)
* [Közös hozzáférésű Jogosultságkód (SAS) használatával](storage-dotnet-shared-access-signature-part-1.md)

