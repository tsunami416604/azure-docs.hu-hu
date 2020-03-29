---
title: A nyugalmi adatok QnA Maker titkosítása
titleSuffix: Azure Cognitive Services
description: QnA Maker titkosítása adatok inaktív .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372094"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>A nyugalmi adatok QnA Maker titkosítása

A QnA Maker automatikusan titkosítja az adatokat, ha azok megmaradnak a felhőben, ezzel segítve a szervezeti biztonsági és megfelelőségi célok elérését.

## <a name="about-encryption-key-management"></a>Titkosítási kulcs kezelése –

Az előfizetés alapértelmezés szerint Microsoft által felügyelt titkosítási kulcsokat használ. Lehetőség van arra is, hogy az előfizetést saját kulcsokkal kezelje. Az ügyfél által felügyelt kulcsok (CMK) nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlők létrehozásához, elforgatásához, letiltásához és visszavonásához. Az adatok védelmére használt titkosítási kulcsokat is naplózhatja.

A QnA Maker az Azure Search CMK-támogatását használja. CmK-t kell [létrehoznia az Azure Search szolgáltatásban az Azure Key Vault használatával.](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys) Ezt az Azure-példányt a QnA Maker szolgáltatással kell társozni, hogy a CMK engedélyezve legyen.

> [!IMPORTANT]
> Az Azure Search szolgáltatáserőforrást 2019 januárja után kell létrehozni, és nem lehet az ingyenes (megosztott) szinten. Nincs támogatás az ügyfél által felügyelt kulcsok konfigurálásához az Azure Portalon.

## <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése

A QnA Maker szolgáltatás cmk-t használ az Azure Search szolgáltatásból. A CMK-k engedélyezéséhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure Search-példányt, és engedélyezze az [Azure Cognitive Search ügyfél által felügyelt kulcselőfeltételeiben említett előfeltételeket.](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Amikor létrehoz egy QnA Maker-erőforrást, az automatikusan egy Azure Search-példányhoz lesz társítva. Ez nem használható cmk.This cannot be used with CMK. A CMK használatához társítania kell az Azure Search újonnan létrehozott példányát, amelyet az 1. Pontosabban, frissítenie kell a `AzureSearchAdminKey` `AzureSearchName` és a QnA Maker erőforrás.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Ezután hozzon létre egy új alkalmazásbeállítást:
   * **Név**: Állítsa ezt a`CustomerManagedEncryptionKeyUrl`
   * **Érték:** Ez az az érték, amelyet az Azure Search-példány létrehozásakor az 1.

   ![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Ha végzett, indítsa újra a futási időt. Most a QnA Maker szolgáltatás CMK-kompatibilis.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfelek által felügyelt kulcsok az Azure Search összes régiójában elérhetők.

## <a name="next-steps"></a>További lépések

* [Titkosítás az Azure Search ben CMK-k használatával az Azure Key Vaultban](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Adattitkosítás inaktív](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
