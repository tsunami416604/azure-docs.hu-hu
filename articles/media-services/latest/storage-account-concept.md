---
title: Feltöltés és tárolás az Azure Media Services felhőbeli |} A Microsoft Docs
description: Ez a cikk felhőalapú fel- és fájltárolóval kapcsolatos fogalmak.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/28/2019
ms.author: juliako
ms.openlocfilehash: 96c3a3eb5e4c07ad9cad8ea5060a27c0c33eec5f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621298"
---
# <a name="cloud-upload-and-storage"></a>Felhőbe történő feltöltés és tárolás

Indítsa el a kezelése, titkosítására, kódolás, elemzése és médiafolyam az Azure-ban, szüksége a Media Services-fiók létrehozása. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. 

A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a storage-fiókok használatához a Media Services-fiók ugyanazon a helyen a további késleltetés és az adatok kimenő adatforgalmi költségek elkerülése érdekében

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. 

>[!NOTE]
> A blobfiókok nem megengedettek **elsődleges** tárfiókként. 

Javasoljuk a GPv2, használja, így kihasználhatja, gyakori elérésű közötti választáshoz, és lassú elérésű szinteket annak. Storage-fiókokkal kapcsolatos további tudnivalókért lásd: [Azure Storage-fiók áttekintése](../../storage/common/storage-account-overview.md). 

Számos különböző SKU-k, kiválaszthatja a tárfiók számára. További információkért lásd: [tárfiókok](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Ha azt szeretné, kísérletezhet a storage-fiókok, `--sku Standard_LRS`. Azonban éles környezetben a Termékváltozat kiválasztásakor érdemes, `--sku Standard_RAGRS`, amely biztosítja az üzletmenet folytonosságának földrajzi replikáció. 

## <a name="assets-in-a-storage-account"></a>Storage-fiókban lévő eszközök

A Media Services v3-as a Storage API-k segítségével tölthetnek fel fájlokat az eszközöket. További információkért lásd: [eszközök fogalom](assets-concept.md).

> [!Note]
> Meg nem próbálja meg módosítani a blob-tárolók, a Media Services SDK által előállított, a Media Services API-k használata nélkül tartalmát.
 
## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services v3:

|Titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|
|Media Services, tárolás titkosítása| AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Nem támogatott<sup>(1)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|

<sup>1</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="next-steps"></a>További lépések

Tárfiók csatolása a Media Services-fiókhoz való kezelésével kapcsolatos információkért lásd: [hozzon létre egy fiókot](create-account-cli-quickstart.md).
