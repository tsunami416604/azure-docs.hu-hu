---
title: Kulcstároló-verziók
description: Az Azure Key Vault-verziók
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012102"
---
# <a name="key-vault-versions"></a>Kulcstároló-verziók

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - felügyelt Tárfiókkulcsok

Nyári 2017 - Tárfiókkulcsok funkció hozzáadása az Azure Storage egyszerűbb integrálását. Az áttekintési témakörben további információkért lásd: [Tárfiókkulcsok felügyelete – áttekintés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01-soft-törlés

Nyári 2017 - soft-törlés funkció hozzáadása a kulcstárolót, és a kulcstároló továbbfejlesztett adatainak védelme objektumok. Az áttekintési témakörben további információkért lásd: [Soft-törlés – áttekintés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - tanúsítványkezelés

A tanúsítványkezelés kerül szolgáltatásként GA verziójával 2015-06-01 2016 szeptemberétől 26.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - általánosan rendelkezésre álló

Általánosan rendelkezésre álló verzió 2015-06-01 szóló bejelentések a 2015. június 24.

A következő módosítások történtek, ebben a kiadásban:

- A kulcs törlése, – "használja a" mező eltávolítása.
- Az információk egy kulcs - információ "használata" mező eltávolítása.
- Kulcs importálása egy tárolóba – "használata" mező eltávolítása.
- Vissza a kulcsot egy – a "használata" mező eltávolítása.
- Módosított "RSA_OAEP" a "RSA-OAEP típusú" az RSA algoritmust. Lásd: [kulcsokat, a titkos kulcsok és a tanúsítványok](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01. dátumú előnézeti 

Második előzetes verzió 2015-02-01. dátumú előnézeti, szóló bejelentések a 2015. áprilisi 20. További információkért lásd: [REST API frissítés](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) blogbejegyzést.

Frissítve a következő feladatokat:

- Egy tároló - művelet hozzáadott tördelési támogatási kulcsainak listázása.
- A verziók listában egy kulcs - hozzáadott művelet egy kulcs verzióinak listázása.
- Egy tároló - hozzáadott tördelési támogatási titkos kulcsainak listázása.
- Titkos kulcs verzióinak listázása - hozzáadási művelet a titkos kulcs verzióinak listázása.
- Összes művelet - attribútumok létrehozása/frissítése időbélyegeket hozzá.
- Content-Type - titkos kulcs létrehozása hozzá a titkos kulcsok.
- Hozzon létre egy kulcs - címkék fel, mert választható adatokat.
- Hozzon létre a titkos kulcs - címkék fel, mert választható adatokat.
- A kulcs - címkék fel, mert választható adatokat.
- Frissítse a titkos kulcs - címkék fel, mert választható adatokat.
- Módosult titkos kulcsok legfeljebb 10 KB-os 25 kilobájt. Megtekintéséhez [kulcsokat, a titkos kulcsok és a tanúsítványok](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-minta

Első előzetes verzió 2014-12-08-előzetes szóló bejelentések a 2015. januári 8.

## <a name="see-also"></a>Lásd még
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
