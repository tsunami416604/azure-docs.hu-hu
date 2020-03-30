---
title: Azure Storage-fiókok
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan hozhat létre azure-tárfiókot az Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499839"
---
# <a name="azure-storage-accounts"></a>Azure Storage-fiókok

A médiatartalmak azure-beli kezelésének, titkosításának, elemzésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot.

A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókok használata ugyanazon a helyen, mint a Media Services-fiók további késés és az adatok kimenő forgalom költségek elkerülése érdekében.

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. Csak a blobfiókok nem engedélyezettek **elsődlegesként.**

Javasoljuk, hogy használja a GPv2-t, hogy kihasználhassa a legújabb funkciókat és teljesítményt. A tárfiókokról az [Azure Storage-fiók áttekintése című témakörben olvashat bővebben.](../../storage/common/storage-account-overview.md)

> [!NOTE]
> Csak a gyakori elérésű hozzáférési szint támogatott az Azure Media Services használatával, bár a többi hozzáférési szint használható a tárolási költségek csökkentésére olyan tartalomon, amelyet nem aktívan használnak.

A tárfiókhoz különböző sk-eket választhat. További információt a [tárfiókok című témakörben talál.](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) Ha tárfiókokkal szeretne kísérletezni, használja a használatát. `--sku Standard_LRS` Azonban, ha kiválaszt egy termékváltozat `--sku Standard_RAGRS`a termeléshez, vegye figyelembe, amely földrajzi replikációt biztosít az üzletmenet folytonosságát.

## <a name="assets-in-a-storage-account"></a>Fedezetek egy tárfiókban

A Media Services v3-as, a storage API-k segítségével fájlokat töltenek fel az eszközökbe. További információ: [Assets in Azure Media Services v3](assets-concept.md).

> [!Note]
> Ne próbálja módosítani a Media Services SDK által a Media Services API-k használata nélkül létrehozott blobtárolók tartalmát.

## <a name="storage-side-encryption"></a>Tárolási oldali titkosítás

Az eszközök védelme érdekében inaktív, az eszközök titkosítást kell a tárolási oldali titkosítást. Az alábbi táblázat bemutatja, hogyan működik a tárolási oldali titkosítás a Media Services 3-as v3-as részén:

|Titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|
|Media Services-tároló titkosítása| AES-256 titkosítás, a Media Services által kezelt kulcs. |Nem támogatott. <sup>1.)</sup>|
|[Tárolószolgáltatás titkosítása az inaktív adatokhoz](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, az Azure vagy az ügyfél által kezelt kulcs.|Támogatott.|
|[Tároló ügyféloldali titkosítása](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage által kínált ügyféloldali titkosítás, amelykulcst az ügyfél a Key Vaultban kezeli.|Nem támogatott.|

<sup>1</sup> A Media Services v3-as rendszerben a tárolótitkosítás (AES-256 titkosítás) csak akkor támogatott visszamenőleges kompatibilitás esetén, ha az eszközöket a Media Services v2-vel hozták létre, ami azt jelenti, hogy a v3 meglévő, titkosított tárterületekkel működik, de nem teszi lehetővé újak létrehozását.

## <a name="storage-account-errors"></a>Tárfiók hibái

A Media Services-fiók Leválasztott állapota azt jelzi, hogy a fiók már nem rendelkezik hozzáféréssel egy vagy több csatolt tárfiókhoz a tárelérési kulcsok változása miatt. A Media Servicesnek naprakész tárelérési kulcsokra van szüksége a fiókbeli feladatok nagy részének végrehajtásához.

Az alábbiak az elsődleges forgatókönyvek, amelyek a Media Services-fiók a csatolt tárfiókokhoz való hozzáférésének megszűnését eredményezhetik.

|Probléma|Megoldás|
|---|---|
|A Media Services-fiók vagy a csatolt tárfiókok külön előfizetésekbe lettek migrálva. |Telepítse át a tárfiók(ok) vagy a Media Services-fiók, hogy azok mind ugyanabban az előfizetésben. |
|A Media Services-fiók egy másik előfizetésben lévő csatolt tárfiókot használ, mivel ez egy régi Media Services-fiók, amikor ez még támogatott volt. Az összes korai Media Services-fiók modern Azure Resources Manager-alapú fiókokká lett konvertálva, és leválasztott állapotú lesz. |Telepítse át a tárfiókot vagy a Media Services-fiókot, hogy azok mind ugyanabban az előfizetésben.|

## <a name="azure-storage-firewall"></a>Az Azure Storage tűzfala

Az Azure Media Services nem támogatja az Azure Storage tűzfallal vagy privát végpontokkal rendelkező [tárfiókokat.](https://docs.microsoft.com/azure/storage/common/storage-network-security)

## <a name="next-steps"></a>További lépések

Ha tudni szeretné, hogyan csatolhat tárfiókot a Media Services-fiókhoz, olvassa el a [Fiók létrehozása című témakört.](create-account-cli-quickstart.md)
