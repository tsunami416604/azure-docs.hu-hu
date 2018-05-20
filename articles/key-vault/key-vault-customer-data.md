---
title: Az Azure Key Vault felhasználói adatokkal kapcsolatos funkciókkal |} Microsoft Docs
description: További tudnivalók a Key Vault ügyféladatok
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/15/2018
ms.author: barclayn
ms.openlocfilehash: 12a176a6eba3b6dd83ef7fb97f89ecfe99083059
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="azure-key-vault-customer-data-features"></a>Az Azure Key Vault felhasználói adatokkal kapcsolatos funkciókkal

Az Azure Key Vault kap ügyféladatok létrehozása vagy a tárolók, a kulcsokat, a titkos kulcsokat, a tanúsítványok és a felügyelt storage-fiókok módosítása során. Az ügyféladatok látható közvetlenül az Azure portálon, és a REST API-n keresztül. Felhasználói adatok szerkeszthetők és törölte a frissítése vagy törlése az objektum, amely tartalmazza az adatokat.

Rendszer belépési naplók jön létre, amikor egy felhasználó vagy alkalmazás fér hozzá a Key Vault. Részletes belépési naplók Azure Insights felhasználója számára érhetők el.

## <a name="identifying-customer-data"></a>Felhasználói adatok azonosítása

A következő információkat azonosítja a felhasználói adatokat az Azure Key Vault belül:

- Hozzáférési házirendek az Azure Key Vault tartalmazza az objektumazonosító jelző, a felhasználók, csoportok és alkalmazások
- Tanúsítványtulajdonosok tartalmazhatják az e-mail címek vagy más felhasználó vagy a szervezeti azonosítók
- Tanúsítvány névjegyek tartalmazhat a felhasználó e-mail címét, nevek vagy telefonszámok
- Tanúsítványkiállítók tartalmazhatnak e-mail címek, a nevek, a telefonszámot, a fiók hitelesítő adatait és a szervezeti részletei
- Az Azure Key Vault objektumok tetszőleges címkéket alkalmazhatja. Ezek az objektumok tartalmazzák, tárolók, a kulcsok, a titkos kulcsokat, a tanúsítványokat és a storage-fiókok. A címkék használt személyes adatokat is tartalmazhat.
- Az Azure Key Vault belépési naplók tartalmaznak objektumazonosítók, [UPN-EK](../active-directory/connect/active-directory-aadconnect-userprincipalname.md), és minden REST API-hívások IP-címek
- Az Azure Key Vault diagnosztikai naplók az objektumazonosító és IP-címek REST API-hívásokban tartalmazhatnak.

## <a name="deleting-customer-data"></a>Felhasználói adatok törlése

Az azonos REST API-k, portállal kapcsolatban és SDK-k tárolók, a kulcsok, a titkos kulcsok, a tanúsítványokat és a felügyelt storage-fiókok létrehozásához használt frissítése, és törölje ezeket az objektumokat is alkalmasak.

Helyreállítható törlésre lehetővé teszi a törlés után 90 nappal a törölt adatokat helyreállítani. Helyreállítható törlésre használata esetén az adatok előfordulhat, hogy véglegesen törlődnek a megőrzési időtartam lejár, a kiürítési művelet elvégzésével 90 nap előtt. Ha a tárolóhoz vagy az előfizetés konfigurációja blokk kiürítése műveletek, nincs lehetőség véglegesen törli az adatokat mindaddig, amíg az ütemezett megőrzési időszak letelte.

## <a name="exporting-customer-data"></a>Felhasználói adatok exportálása

Az azonos REST API-k, a portál élmény, és a tárolók, a kulcsok, a titkos kulcsokat, a tanúsítványok létrehozásához használt SDK-k és a fiókok is vannak felügyelt tárolási engedélyezi, hogy a megtekintése, és ezek az objektumok exportálása.

Hozzáférés naplózása az opcionális szolgáltatása is be kell kapcsolni az Azure Key Vault minden REST API-hívások naplófájlokat hoznak létre. Ha az adatmegőrzési szabályt, amely a szervezet igényeinek megfelelő telepítését ezek a naplók adjuk át a tárfiók az előfizetésben.

Az Exportálás kérést a felhasználói adatvédelem portálon az Azure Key Vault diagnosztikai naplók személyes adatokat tartalmazó kérhetnek le. A kérelem a bérlői rendszergazdának kell kezdeményeznie.

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault naplózása](key-vault-logging.md)

- [Az Azure Key Vault soft-törlés áttekintése](key-vault-soft-delete-cli.md)

- [Tárolók](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Az Azure Key Vault kulcsú műveletek](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Az Azure Key Vault titkos üzemeltetése](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Az Azure Key Vault tanúsítványok és házirendek](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Tanúsítványkiállítók](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Az Azure Key Vault tárolási fiók műveletek](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
