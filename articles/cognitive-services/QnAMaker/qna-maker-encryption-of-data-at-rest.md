---
title: QnA Maker inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: QnA Maker inaktív adatok titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 99c21ece202f8d9867045d506574dd7718bd455e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653662"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker inaktív adatok titkosítása

A QnA Maker automatikusan titkosítja az adatait, ha a felhőben marad, így segítve a szervezeti biztonsági és megfelelőségi célok teljesítését.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Lehetősége van arra is, hogy az előfizetését a saját kulcsaival kezelje. Az ügyfél által felügyelt kulcsok (CMK) nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

QnA Maker az Azure Search CMK-támogatását használja. A [Azure Key Vault használatával CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)kell létrehoznia Azure Searchban. Ezt az Azure-példányt hozzá kell rendelni QnA Maker szolgáltatáshoz, hogy a CMK engedélyezve legyen.

> [!IMPORTANT]
> A Azure Search Service-erőforrást a január 2019 után kell létrehozni, és nem lehet ingyenes (megosztott) szinten. Nem támogatott az ügyfél által felügyelt kulcsok konfigurálása a Azure Portalban.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

A QnA Maker szolgáltatás a CMK használja a Azure Search szolgáltatásból. A CMKs engedélyezéséhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure Search példányt, és engedélyezze az [Azure Cognitive Search ügyfél által felügyelt legfontosabb előfeltételeiben](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)említett előfeltételeket.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker-erőforrás létrehozásakor a rendszer automatikusan egy Azure Search-példánnyal társítja. Ez a CMK nem használható. A CMK használatához hozzá kell rendelnie Azure Search újonnan létrehozott példányát, amelyet az 1. lépésben hoztak létre. Pontosabban frissítenie kell a és a `AzureSearchAdminKey` `AzureSearchName` QnA Maker erőforrást.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Ezután hozzon létre egy új Alkalmazásbeállítás-beállítást:
   * **Név**: állítsa be ezt a`CustomerManagedEncryptionKeyUrl`
   * **Érték**: ez az az érték, amelyet az 1. lépésben kapott Azure Search példány létrehozásakor.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Ha elkészült, indítsa újra a futtatókörnyezetet. A QnA Maker szolgáltatás most már CMK-kompatibilis.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok minden Azure Search régióban elérhetők.

## <a name="encryption-of-data-in-transit"></a>Az adatforgalom titkosítása

QnA Maker portál a felhasználó böngészőjében fut. Minden művelet közvetlen hívást indít a megfelelő kognitív szolgáltatás API-hoz. Ezért a QnA Maker megfelel az átvitt adatforgalomnak.
Mivel azonban a QnA Maker portál szolgáltatás az USA nyugati régiójában található, még mindig nem ideális a nem USA-beli ügyfelek számára. 

## <a name="next-steps"></a>További lépések

* [Titkosítás Azure Search a CMKs használatával Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Inaktív adatok titkosítása](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
