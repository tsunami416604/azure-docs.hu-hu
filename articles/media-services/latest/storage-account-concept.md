---
title: Azure Storage-fiókok Azure Media Services fiókkal | Microsoft Docs
description: A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot.
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
ms.openlocfilehash: 4bbadd7e10f0fd6896932dd79a5ca42d9906d2a2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393485"
---
# <a name="azure-storage-accounts"></a>Azure Storage-fiókok

Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. 

A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a Storage-fiókok használata a Media Services-fiókkal megegyező helyen, így elkerülhető a további késés és az adatforgalom költségei

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. <br/>A blobfiókok nem megengedettek **elsődleges** tárfiókként. 

Javasoljuk, hogy a GPv2 használja, így kihasználhatja a legújabb funkciókat és teljesítményt. További információ a Storage-fiókokról: az [Azure Storage-fiók áttekintése](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Csak a lassú elérési szint támogatott a Azure Media Serviceshoz való használathoz, bár a többi hozzáférési réteg is használható a tárolási költségek csökkentésére a nem aktívan használt tartalmak esetében.

Különböző SKU-ket is választhat a Storage-fiókhoz. További információ: Storage- [fiókok](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Ha a Storage-fiókokkal szeretne kísérletezni, használja a `--sku Standard_LRS`. Ha azonban éles üzemben lévő SKU-t vesz fel, érdemes megfontolnia `--sku Standard_RAGRS`, amely földrajzi replikációt biztosít az üzletmenet folytonossága érdekében. 

## <a name="assets-in-a-storage-account"></a>A Storage-fiókban lévő eszközök

A Media Services v3-as verziójában a Storage API-k segítségével tölthet fel fájlokat az eszközökre. További információ: [assets koncepció](assets-concept.md).

> [!Note]
> Ne próbálja meg módosítani a Media Services SDK által létrehozott blob-tárolók tartalmát Media Services API-k használata nélkül.
 
## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services v3:

|Titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|
|Media Services, tárolás titkosítása| AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Nem támogatott<sup>(1)</sup>|
|[Inaktív adatok Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|

<sup>1</sup> a Media Services v3-as verzióban a Storage encryption (AES-256 encryption) csak akkor támogatott a visszamenőleges kompatibilitás érdekében, ha az eszközök Media Services v2-mel lettek létrehozva. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="storage-account-errors"></a>Storage-fiókok hibái

A Media Services-fiók Leválasztott állapota azt jelzi, hogy a fiók már nem rendelkezik hozzáféréssel egy vagy több csatolt tárfiókhoz a tárelérési kulcsok változása miatt. A Media Servicesnek naprakész tárelérési kulcsokra van szüksége a fiókbeli feladatok nagy részének végrehajtásához.

Az alábbiak az elsődleges forgatókönyvek, amelyek a Media Services-fiók a csatolt tárfiókokhoz való hozzáférésének megszűnését eredményezhetik. 

|Probléma|Megoldás|
|---|---|
|A Media Services-fiók vagy a csatolt tárfiókok külön előfizetésekbe lettek migrálva. |Helyezze át a tárfiókokat vagy a Media Services-fiókot, hogy mind egy előfizetésben legyenek. |
|A Media Services-fiók egy másik előfizetésben lévő csatolt tárfiókot használ, mivel ez egy régi Media Services-fiók, amikor ez még támogatott volt. Minden régi Media Services-fiók át lett alakítva modern Azure Resource Manager- (ARM-) -alapú fiókká, és Leválasztott állapotba került. |Migrálja a tárfiókot vagy a Media Services-fiókot, hogy mind egy előfizetésben legyenek.|

## <a name="azure-storage-firewall"></a>Azure Storage-tűzfal

A Azure Media Services nem támogatja a Storage-fiókok használatát az Azure Storage-tűzfallal vagy a [magánhálózati végpontokkal](https://docs.microsoft.com/azure/storage/common/storage-network-security) .

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan csatolhat Storage-fiókot a Media Services-fiókjához, tekintse meg a [fiók létrehozása](create-account-cli-quickstart.md)című témakört.
