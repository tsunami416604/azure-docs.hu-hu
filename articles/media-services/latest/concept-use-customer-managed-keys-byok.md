---
title: Saját kulcs használata (ügyfél által felügyelt kulcsok) a Media Services
description: Az ügyfél által felügyelt kulcs (azaz saját kulcs használata) Media Services használatával is használható.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a56922c972efeb21c188413522bd05f83b74ca12
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681822"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Saját kulcs használata (ügyfél által felügyelt kulcsok) a Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Bring Your Own Key (BYOK) egy Azure-alapú kezdeményezés, amely segít az ügyfeleknek a számítási feladatok felhőbe való áthelyezésében. Az ügyfél által felügyelt kulcsok lehetővé teszik az ügyfelek számára az iparági megfelelőségi előírások betartását, és javítják a szolgáltatások bérlői elkülönítését. A titkosítási kulcsok megadásának módja a szükségtelen hozzáférés és a Microsoft-szolgáltatások megbízhatóságának csökkentése.

## <a name="keys-and-key-management"></a>Kulcsok és kulcskezelő

Ha a Media Services 2020-05-01 API-t használja, használhatja a saját kulcsát Media Services. A rendszer minden olyan fiókhoz létrehoz egy alapértelmezett fiókot, amelyet Media Services által birtokolt rendszerkulcs titkosít. A saját kulcs használatakor a fiók kulcsa titkosítva van a kulccsal. A tartalmi kulcsokat a fiók kulcsa titkosítja. A JobInputHttp URL-címek és a szimmetrikus jogkivonat-ellenőrző kulcsok is titkosítva vannak.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Az ügyfél által felügyelt kulcs helyettesíti a rendszer által felügyelt kulcsot.":::

A Media Services a Media Services fiók felügyelt identitásával olvassa be a kulcsot egy Ön által birtokolt Key Vault. Media Services megköveteli, hogy a Key Vault ugyanabban a régióban legyen, mint a fiók, és hogy a rendszer a helyreállítható törlési és törlési védelmet engedélyezze.

A kulcs 2048, 3072 vagy 4096 RSA-kulcs lehet, és a HSM és a szoftver kulcsai is támogatottak.

> [!NOTE]
> Az EC-kulcsok nem támogatottak.

Megadhatja a kulcs nevét és a kulcs verziószámát, vagy csak a kulcs nevét. Ha csak a kulcs nevét használja, Media Services a legújabb verziót fogja használni. A rendszer automatikusan észleli a vásárlói kulcsok új verzióit, és a fiók kulcsát újra titkosítja.

> [!WARNING]
> Media Services figyeli az ügyfél kulcsának elérését. Ha az ügyfél kulcsa elérhetetlenné válik (például a kulcs törölve lett, vagy a Key Vault törölve lett, vagy a hozzáférési engedély el lett távolítva), Media Services átváltja a fiókot az ügyfél kulcsának elérhetetlenné válása állapotba (ami gyakorlatilag letiltja a fiókot). A fiók azonban törölhető ebben az állapotban. Az egyetlen támogatott művelet a fiók beolvasása, LISTÁZÁSa és törlése; az összes többi kérés (Encoding, streaming stb.) sikertelen lesz, amíg nem állítja vissza a fiók kulcsának elérését.

## <a name="tutorials"></a>Oktatóanyagok

- [Az ügyfél által felügyelt kulcsok vagy BYOK használata a Azure Portal használatával Media Services](tutorial-byok-portal.md)
- [Az ügyfél által felügyelt kulcsokat vagy BYOK Media Services REST API használhatja](tutorial-byok-postman.md).

## <a name="next-steps"></a>Következő lépések

[A tartalmak Media Services dinamikus titkosítással védhetők](content-protection-overview.md)
