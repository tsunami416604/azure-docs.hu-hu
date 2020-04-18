---
title: Az Azure Key Vault ügyféladat-szolgáltatásai – Azure Key Vault | Microsoft dokumentumok
description: Tudnivalók az ügyféladatokról a Key Vaultban
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: bceea53e6b177940305a2dc77f2a6ecfa37f277a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617891"
---
# <a name="azure-key-vault-customer-data-features"></a>Az Azure Key Vault ügyféladat-szolgáltatásai

Az Azure Key Vault ügyféladatokat kap a tárolók, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozása vagy frissítése során. Ezek az ügyféladatok közvetlenül láthatók az Azure Portalon és a REST API-n keresztül. Az ügyféladatok szerkeszthetők vagy törölhetők az adatokat tartalmazó objektum frissítésével vagy törlésével.

A rendszerhozzáférési naplók akkor jönnek létre, amikor egy felhasználó vagy alkalmazás hozzáfér a Key Vaulthoz. Részletes hozzáférési naplók érhetők el az Azure Insights használatával az ügyfelek számára.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Az ügyféladatok azonosítása

A következő adatok azonosítják az ügyféladatokat az Azure Key Vaultban:

- Az Azure Key Vault hozzáférési szabályzatai felhasználókat, csoportokat vagy alkalmazásokat képviselő objektumazonosítókat tartalmaznak
- A tanúsítvány tulajdonosai tartalmazhatnak e-mail címeket vagy más felhasználói vagy szervezeti azonosítókat
- A tanúsítvány-partnerek felhasználói e-mail címeket, neveket vagy telefonszámokat tartalmazhatnak
- A tanúsítvány kibocsátói tartalmazhatnak e-mail címeket, neveket, telefonszámokat, fiókhitelesítő adatokat és szervezeti adatokat.
- Tetszőleges címkéket lehet alkalmazni az objektumok az Azure Key Vaultban. Ezek az objektumok közé tartoznak a tárolók, kulcsok, titkos kulcsok, tanúsítványok és tárfiókok. A használt címkék személyes adatokat tartalmazhatnak
- Az Azure Key Vault hozzáférési naplói objektumazonosítókat, [UPN-eket](../../active-directory/hybrid/plan-connect-userprincipalname.md)és IP-címeket tartalmaznak minden REST API-híváshoz
- Az Azure Key Vault diagnosztikai naplói objektumazonosítókat és IP-címeket tartalmazhatnak a REST API-hívásokhoz

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

Ugyanazok a REST API-k, portálélmény és SDK-k, amelyek et tárolók, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozásához használnak, szintén frissíthetik és törölhetik ezeket az objektumokat.

A helyreállítható törlés lehetővé teszi a törölt adatok helyreállítását a törlésután 90 napig. A helyreállítható törlés használata esetén az adatok véglegesen törölhetők a 90 napos megőrzési időszak lejárta előtt egy kiürítési művelet végrehajtásával. Ha a tároló vagy az előfizetés úgy van konfigurálva, hogy blokkolja a kiürítési műveleteket, nem lehet véglegesen törölni az adatokat, amíg az ütemezett megőrzési időszak le nem telt.

## <a name="exporting-customer-data"></a>Vevői adatok exportálása

Ugyanazok a REST API-k, portálélmény és SDK-k, amelyek tárolók, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozásához használatosak, lehetővé teszik ezen objektumok megtekintését és exportálását is.

Az Azure Key Vault hozzáférés naplózása egy opcionális funkció, amely be kapcsolható naplók létrehozásához minden REST API-hívás. Ezek a naplók átkerülnek egy tárfiókba az előfizetésben, ahol alkalmazza a szervezet követelményeinek megfelelő adatmegőrzési szabályzatot.

A személyes adatokat tartalmazó Azure Key Vault diagnosztikai naplók a Felhasználói adatvédelem portálon történő exportálási kérelemmel lekérdezhetők. Ezt a kérelmet a bérlői rendszergazdának kell benyújtania.

## <a name="next-steps"></a>További lépések

- [Azure key vault naplózása](logging.md))

- [Azure Key Vault: a helyreállítható törlés áttekintése](soft-delete-cli.md)

- [Az Azure Key Vault kulcsműveletei](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Az Azure Key Vault titkos műveletei](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault-tanúsítványok és -szabályzatok](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Az Azure Key Vault tárfiók-műveletei](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
