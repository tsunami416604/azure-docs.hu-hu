---
title: Az Azure Blob Storage használata modellátalakításhoz
description: A blobstorage modellkonverzióhoz való beállításának és használatának gyakori lépéseit ismerteti.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681648"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Az Azure Blob Storage használata modellátalakításhoz

A [modellkonverziós](model-conversion.md) szolgáltatás hozzáférést igényel az Azure blob storage- hoz, hogy letudja olvasni a bemeneti adatokat, és tárolhassa a kimeneti adatokat. Ez a cikk a leggyakoribb lépések végrehajtásának módját ismerteti.

## <a name="prepare-azure-storage-accounts"></a>Azure Storage-fiókok előkészítése

- Tárfiók létrehozása (StorageV2)
- Hozzon létre egy bemeneti blobtárolót a tárfiókban (például "arrinput" nevű)
- Hozzon létre egy kimeneti blobtárolót a tárfiókban (például "arroutput" nevű)

> [!TIP]
> A tárfiók beállításának lépésenkénti útmutatóját tekintse meg [a Rövid útmutató: Modell konvertálása renderelésre](../../quickstarts/convert-model.md)

A tárfiók és a blobtárolók létrehozása az alábbi eszközök egyikével végezhető el:

- [Azure Portal](https://portal.azure.com)
- [az parancssor](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- SDK-k (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Annak biztosítása, hogy az Azure távoli renderelés e-tárolós elérése korosodhat a tárfiókban

Az Azure Remote-rendezésnek le kell kérnie a modelladatokat a tárfiókból, és adatokat kell visszaírnia.

Az Azure távoli renderelési hozzáférést a tárfiókhoz a következő két módon biztosíthatja:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Az Azure Storage-fiók csatlakoztatása az Azure távoli renderelési fiókjához

Kövesse a Fiók [létrehozása](../create-an-account.md#link-storage-accounts) szakaszban leírt lépéseket.

### <a name="retrieve-sas-for-the-storage-containers"></a>SAS beolvasása a tárolótárolókhoz

A tárolt hozzáférési aláírások (SAS) olvasási hozzáférést biztosítanak a bemenethez, és írási hozzáférést a kimenethez. Azt javasoljuk, hogy új URI-k minden alkalommal, amikor egy modell átalakítása. Mivel az URI-k egy idő után lejárnak, hosszabb ideig való megőrzésük esetén előfordulhat, hogy váratlanul megszakítja az alkalmazást.

A SAS-ről a [SAS dokumentációjában talál részleteket.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

A SAS URI a következők egyikével generálható:

- az PowerShell modul
  - tekintse meg a [példa PowerShell-parancsfájlokat](../../samples/powershell-example-scripts.md)
- [az parancssor](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - jobb gombbal a tároló "Get Shared Access Signature" (olvasás, lista hozzáférés bemeneti tároló, írási hozzáférés kimeneti tároló)
- SDK-k (C#, Python ... )

A megosztott hozzáférésű aláírások eszközkonverzióban való használatára példa a [Powershell példaparancsfájljainak](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1 fájlja látható.

## <a name="upload-an-input-model"></a>Bemeneti modell feltöltése

A modell konvertálásának megkezdéséhez fel kell töltenie azt az alábbi lehetőségek egyikével:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) – kényelmes felhasználói felület fájlok feltöltéséhez/letöltéséhez/kezeléséhez az azure blob storage-ban
- [Az Azure parancssora](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - tekintse meg a [Példa PowerShell-parancsfájlokat](../../samples/powershell-example-scripts.md)
- [Tároló használata SDK (Python, C# ... )](https://docs.microsoft.com/azure/storage/)
- [Az Azure Storage REST API-k használata](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Az adatok konverziós feltöltésének például a [Powershell példaparancsfájljainak](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1 fájlja.

## <a name="get-a-sas-uri-for-the-converted-model"></a>SAS URI beszereznie az átalakított modellhez

Ez a lépés hasonló [a tárolótárolók SAS-ának beolvasásához.](#retrieve-sas-for-the-storage-containers) Ezúttal azonban be kell olvasnia egy SAS URI-t a modellfájlhoz, amely a kimeneti tárolóba lett írva.

Ha például az Azure Storage [Explorer](https://azure.microsoft.com/features/storage-explorer/)en keresztül szeretne SAS-URI-t beolvasni, kattintson a jobb gombbal a modellfájlra, és válassza a "Megosztott hozzáférésű jogosultságjegyaláírása" parancsot.

A modellek betöltéséhez megosztott hozzáférésű aláírásra (SAS) van szükség, ha még nem kapcsolta össze a tárfiókot az Azure távoli leképezési fiókjával. A fiók összekapcsolásáról a Fiók létrehozása című részben [olvashat.](../create-an-account.md#link-storage-accounts)

## <a name="next-steps"></a>További lépések

- [A modellkonvertálás konfigurálása](configure-model-conversion.md)
- [A modellkonverziós REST API](conversion-rest-api.md)
