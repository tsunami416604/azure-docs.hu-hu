---
title: Key Vault-verziók
description: Az Azure Key Vault-verziók
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b7e3aca133e2e9614ab83be83c20a4dbc2ae5fe2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542385"
---
# <a name="key-vault-versions"></a>Key Vault-verziók

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - felügyelt Tárfiókkulcsok

Nyári 2017 - Tárfiókkulcsok funkció hozzáadása az Azure Storage egyszerűbb integráció. További információ az áttekintése című témakörben talál [felügyelt Tárfiókkulcsok áttekintése](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01-helyreállítható törlés

Nyáron rendezzük 2017 - helyreállítható törlési funkció hozzáadva a kulcstartó és a key vault továbbfejlesztett adatvédelmi objektumokat. További információ az áttekintése című témakörben talál [helyreállítható törlés áttekintése](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - tanúsítványok kezelése

Tanúsítványkezelés hozzáadódik szolgáltatásként az általánosan elérhető verziót 2015-06-01-2016. szeptember 26.

## <a name="2015-06-01---general-availability"></a>2015-06-01 – általános elérhetőség

Általánosan elérhető verziója 2015-06-01, 2015. június 24-én bejelentettük.

A következő változtak, ebben a kiadásban:

- Kulcs törlése – "használata" mező eltávolítása.
- Egy kulcs - információ "használata" mező eltávolítása adatai olvashatók be.
- Kulcs importálása egy tárolóba – "használata" mező eltávolítása.
- Kulcs helyreállítása – "használata" mező eltávolítása.
- Módosított "RSA_OAEP", "RSA-OAEP" RSA algoritmusok. Lásd: [kapcsolatos kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Második előzetes verziója 2015-02-01-preview, 2015. április 20 jelentettük be. További információkért lásd: [REST API frissítése](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) blogbejegyzést.

Frissítve a következő feladatokat:

- Egy tároló - műveletet a hozzáadott tördelés támogatási kulcsainak listázása.
- Verziók listázása egy kulcs - kulcs verzióinak listázása művelet hozzáadva.
- Egy tároló - hozzáadott tördelés támogatása titkos kulcsainak listázása.
- Titkos kulcs verzióinak listázása – a titkos kulcs verzióinak listázása művelet hozzáadása.
- Minden művelet - attribútumok létrehozott vagy módosított időbélyegeket hozzá.
- Titkos kódok Content-Type - titkos kulcs létrehozása hozzá.
- Hozzon létre egy kulcsot - címkék hozzáadva opcionális információk.
- Hozzon létre egy titkos kulcsot - címkék hozzáadva opcionális információk.
- Egy kulcs - címkék hozzáadva opcionális információk.
- Frissítse a titkos kulcs - címkék hozzáadva opcionális információk.
- Módosult a titkos kulcsok maximális méret 10 ezer 25 kilobájt. Látható, [kapcsolatos kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014. 12-08-előzetes verzió

Első előzetes verziója 2014-12-08-preview, 2015. január 8 jelentettük be.

## <a name="see-also"></a>Lásd még
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
