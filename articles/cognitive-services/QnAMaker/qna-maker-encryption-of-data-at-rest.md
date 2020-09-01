---
title: QnA Maker inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk a QnA Maker inaktív adatok titkosítását, valamint a CMK engedélyezését és felügyeletét ismerteti.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: e744423e00377ef763824f6e39865e6b3e8ee475
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073539"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker inaktív adatok titkosítása

A QnA Maker automatikusan titkosítja az adatait, ha a felhőben marad, így segítve a szervezeti biztonsági és megfelelőségi célok teljesítését.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Az előfizetést az ügyfél által felügyelt kulcsok (CMK) nevű saját kulcsokkal is kezelheti. A CMK nagyobb rugalmasságot biztosít a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Továbbá az adatok védelméhez használt titkosítási kulcsok naplózására is lehetősége van. Ha az előfizetéshez CMK van konfigurálva, a rendszer dupla titkosítást biztosít, amely második védelmi réteget kínál, miközben lehetővé teszi a titkosítási kulcs vezérlését a Azure Key Vaulton keresztül.

QnA Maker az Azure Search CMK-támogatását használja. A [Azure Key Vault használatával CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)kell létrehoznia Azure Searchban. Ezt az Azure-példányt hozzá kell rendelni QnA Maker szolgáltatáshoz, hogy a CMK engedélyezve legyen.

> [!IMPORTANT]
> A Azure Search Service-erőforrást a január 2019 után kell létrehozni, és nem lehet ingyenes (megosztott) szinten. Nem támogatott az ügyfél által felügyelt kulcsok konfigurálása a Azure Portalban.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

A QnA Maker szolgáltatás a CMK használja a Azure Search szolgáltatásból. A CMKs engedélyezéséhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure Search példányt, és engedélyezze az [Azure Cognitive Search ügyfél által felügyelt legfontosabb előfeltételeiben](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)említett előfeltételeket.

   ![1. titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker-erőforrás létrehozásakor a rendszer automatikusan egy Azure Search-példánnyal társítja. Ez a CMK nem használható. A CMK használatához hozzá kell rendelnie Azure Search újonnan létrehozott példányát, amelyet az 1. lépésben hoztak létre. Pontosabban frissítenie kell a és a `AzureSearchAdminKey` `AzureSearchName` QnA Maker erőforrást.

   ![2. titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Ezután hozzon létre egy új Alkalmazásbeállítás-beállítást:
   * **Név**: állítsa be ezt a `CustomerManagedEncryptionKeyUrl`
   * **Érték**: ez az az érték, amelyet az 1. lépésben kapott Azure Search példány létrehozásakor.

   ![3. titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Ha elkészült, indítsa újra a futtatókörnyezetet. A QnA Maker szolgáltatás most már CMK-kompatibilis.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok minden Azure Search régióban elérhetők.

## <a name="encryption-of-data-in-transit"></a>Az adatforgalom titkosítása

QnA Maker portál a felhasználó böngészőjében fut. Minden művelet közvetlen hívást indít a megfelelő kognitív szolgáltatás API-hoz. Ezért a QnA Maker megfelel az átvitt adatforgalomnak.
Mivel azonban a QnA Maker portál szolgáltatás az USA nyugati régiójában található, még mindig nem ideális a nem USA-beli ügyfelek számára. 

## <a name="next-steps"></a>Következő lépések

* [Titkosítás Azure Search a CMKs használatával Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Adat-titkosítás inaktív állapotban](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)