---
title: Az Azure Key Vault vásárlói adatok funkciókat – Azure Key Vault |} A Microsoft Docs
description: További tudnivalók a Key Vaultban a vásárlói adatok
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: reference
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 12492fad419fdc703ddabcf2288be282f2fd47be
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111781"
---
# <a name="azure-key-vault-customer-data-features"></a>Az Azure Key Vault vásárlói funkciók

Az Azure Key Vault létrehozás vagy frissítés, tárolók, kulcsok, titkos kódok, tanúsítványok és a felügyelt tárfiókok alatt kap a vásárlói adatokat. Az ügyféladatok jelenik meg közvetlenül az Azure Portalon, és a REST API-n keresztül. Vásárlói adatok szerkeszthetők és törölte a frissítése vagy törlése az objektum, amely tartalmazza az adatokat.

Amikor egy felhasználó vagy alkalmazás fér hozzá a Key Vault hozzáférés rendszernaplók jönnek létre. Részletes hozzáférési naplók Azure Insights használó ügyfelek számára érhetők el.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Vásárlói adatok azonosítása

A következő információkat azonosítja a vásárlói adatok Azure Key Vault belül:

- Hozzáférési szabályzatok az Azure Key Vault-objektumazonosítók tartalmazhat jelölő a felhasználókat, csoportokat vagy alkalmazásokat
- Tanúsítványtulajdonosok tartalmazhatják az e-mail-címeket vagy más felhasználó vagy a szervezeti azonosító
- Tanúsítványpartnerek felhasználói e-mail-címeket, nevek vagy számokat tartalmazhat.
- Tanúsítványkiállítók tartalmazhatja az e-mail-címeket, neveket, telefonszámokat, fiók hitelesítő adatait és szervezeti részletei
- Tetszőleges címkék alkalmazhatók az Azure Key Vault-objektumokhoz. Ezek az objektumok belefoglalása, tárolók, kulcsok, titkos kódok, tanúsítványok és storage-fiókok. A címkéket személyes adatokat is tartalmazhat.
- Az Azure Key Vault hozzáférési naplók tartalmaznak objektumazonosítók, [UPN-EK](../active-directory/hybrid/plan-connect-userprincipalname.md), és a egy REST API-hívás IP-címek
- Az Azure Key Vault-diagnosztikai naplók objektum-azonosítók és a REST API-hívások IP-címet tartalmazhat.

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése

Az azonos REST API-k, portál felhasználói élményt és tárolók, kulcsok, titkos kódok, tanúsítványok és a felügyelt tárfiókok, létrehozásához használt SDK-k frissítése és törlése, ezek az objektumok is alkalmasak.

Helyreállítható törlés lehetővé teszi, hogy a törölt adatok helyreállítása a törlés után 90 napig. Helyreállítható törlés használata esetén az adatok előfordulhat, hogy véglegesen törlődik a megőrzési időtartam lejár egy kiürítési művelet végrehajtásával 90 nap előtt. Ha a tároló vagy előfizetést van konfigurálva blokk kiürítése operations, nem lehetséges véglegesen törli az adatokat mindaddig, amíg az ütemezett megőrzési időszak letelte.

## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása

A REST API-kkal, portálja és SDK-k hozhatók létre, tárolók, kulcsok, titkos kódok, tanúsítványok, és a storage-fiókok is felügyelt lehetővé teszi, hogy ezek az objektumok exportálása és.

Az Azure Key Vault hozzáférés naplózásának ez nem kötelező, hogy be tudja kapcsolni egyes REST API-hívások naplófájlokat hoznak létre. Ezek a naplók adjuk át egy tárfiókot az előfizetésében, a alkalmazni, ha az adatmegőrzési szabályt, amely megfelel a szervezet követelményeinek.

Személyes adatait tartalmazó Azure Key Vault diagnosztikai naplók lekérhetők-exportálási kérelem beállításjegyzékén végrehajtja a felhasználói portálon. Ezt a kérelmet kell végezni a bérlői rendszergazda.

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault naplózása](key-vault-logging.md)

- [Az Azure Key Vault helyreállítható törlés áttekintése](key-vault-soft-delete-cli.md)

- [Az Azure Key Vault-kulcs műveletek](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Az Azure Key Vault titkos műveletek](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Az Azure Key Vault-tanúsítványok és szabályzatok](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Az Azure Key Vault storage-fiók műveletek](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
