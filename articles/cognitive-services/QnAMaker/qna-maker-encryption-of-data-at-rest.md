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
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372094"
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

2. QnA Maker-erőforrás létrehozásakor a rendszer automatikusan egy Azure Search-példánnyal társítja. Ez a CMK nem használható. A CMK használatához hozzá kell rendelnie Azure Search újonnan létrehozott példányát, amelyet az 1. lépésben hoztak létre. Pontosabban frissítenie kell a `AzureSearchAdminKey` és `AzureSearchName` a QnA Maker erőforrásban.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Ezután hozzon létre egy új Alkalmazásbeállítás-beállítást:
   * **Név**: állítsa be `CustomerManagedEncryptionKeyUrl`
   * **Érték**: ez az az érték, amelyet az 1. lépésben kapott Azure Search példány létrehozásakor.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Ha elkészült, indítsa újra a futtatókörnyezetet. A QnA Maker szolgáltatás most már CMK-kompatibilis.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok minden Azure Search régióban elérhetők.

## <a name="next-steps"></a>Következő lépések

* [Titkosítás Azure Search a CMKs használatával Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Adattitkosítás inaktív állapotban](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
