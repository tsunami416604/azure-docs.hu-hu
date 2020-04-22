---
title: Az Azure Key Vault titkos titkai – Azure Key Vault
description: Az Azure Key Vault REST-felületének áttekintése és a titkos kulcsok fejlesztői részletei.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eabfa03aa70f54a967fe256f694ef59ad0fe7ebe
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685438"
---
# <a name="about-azure-key-vault-secrets"></a>Az Azure Key Vault titkai

A Key Vault biztonságos anamtikai titkos kulcsok, például jelszavak és adatbázis-kapcsolati karakterláncok biztonságos tárolását biztosítja.

A fejlesztő szemszögéből a Key Vault API-k titkos értékeket fogadnak el és adnak vissza karakterláncként. Belsőleg a Key Vault oktettsorozatként (8 bites bájtok) tárolja és kezeli a titkos kulcsokat, egyenként legfeljebb 25 k bájt méretű. A Key Vault szolgáltatás nem biztosít szemantikát a titkos kulcsokhoz. Csupán elfogadja az adatokat, titkosítja, tárolja, és egy titkos azonosítót ("id") ad vissza. Az azonosító segítségével később is beolvasható a titok.  

A szigorúan bizalmas adatokhoz az ügyfeleknek ajánlott további adatvédelmi rétegeket is használni. Ez lehet például az adatok külön védelmi kulccsal történő titkosítása a Key Vaultba helyezés előtt.  

Key Vault is támogatja a contentType mező titkos kulcsok. Az ügyfelek megadhatják egy titkos titok tartalomtípusát, amely segítséget nyújt a titkos adatok értelmezéséhez a beolvasáskor. A mező maximális hossza 255 karakter. Nincsenek előre definiált értékek. A javasolt használat a titkos adatok értelmezésére vonatkozó tipp. Például egy implementáció titkos jelszóként és tanúsítványként is tárolhatja a jelszavakat és a tanúsítványokat, majd ezt a mezőt használja a megkülönböztetéshez. Nincsenek előre definiált értékek.  

## <a name="secret-attributes"></a>Titkos attribútumok

A titkos adatokon kívül a következő attribútumok is megadhatók:  

- *exp*: IntDate, nem kötelező, az alapértelmezett **örökre**. Az *exp* (lejárati idő) attribútum azonosítja a lejárati időt, vagy amely után a titkos adatokat NEM szabad letölteni, kivéve az [adott helyzetekben](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű, mivel tájékoztatja a felhasználókat a key vault szolgáltatás, hogy egy adott titkos kulcs nem használható. Értéke intdate értéket tartalmazó szám kell, hogy legyen.   
- *nbf*: IntDate, nem kötelező, az alapértelmezett **most**. Az *nbf* (nem korábban) attribútum azonosítja azt az időt, amely előtt a titkos adatokat NEM szabad beolvasni, kivéve [bizonyos eseteket](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű. Értéke intdate értéket tartalmazó szám kell, hogy legyen. 
- *engedélyezve*: logikai, nem kötelező, az alapértelmezett **érték igaz**. Ez az attribútum határozza meg, hogy a titkos adatok beolvashatók-e. Az engedélyezett attribútum az *nbf* és *exp* együttesen használatos, ha az *nbf* és *exp*közötti művelet csak akkor engedélyezett, ha az engedélyezve van **true**értékre. Az *nbf* és *exp* ablakon kívüli műveletek automatikusan nem engedélyezettek, kivéve [bizonyos eseteket.](#date-time-controlled-operations)  

Vannak további írásvédett attribútumok, amelyek szerepelnek minden válasz, amely tartalmazza a titkos attribútumok:  

- *létrehozva*: IntDate, nem kötelező. A létrehozott attribútum jelzi, hogy a titkos titok ezen verziója mikor jött létre. Ez az érték null az attribútum hozzáadása előtt létrehozott titkos kulcsok esetében. Az értéknek IntDate értéket tartalmazó számnak kell lennie.  
- *frissítve*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy a titkos titok ezen verziója mikor lett frissítve. Ez az érték null az attribútum hozzáadása előtt utoljára frissített titkos kulcsok esetében. Az értéknek IntDate értéket tartalmazó számnak kell lennie.

### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A titkos **get** művelet fog működni a még nem érvényes és lejárt titkok, kívül *nbf* / *exp* ablak. Egy titkos **lehívási** művelet hívása, egy még nem érvényes titok esetén, használható tesztelési célokra. Beolvasása **(get**ting) egy lejárt titkos, helyreállítási műveletekhez használható.

## <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

Hozzáférés-vezérlés a Key Vaultban kezelt titkos kulcsok, a kulcstartó szintjén, amely tartalmazza ezeket a titkos kulcsokat. A titkos kulcsok hozzáférés-vezérlési házirendje különbözik az ugyanabban a Key Vaultban lévő kulcsok hozzáférés-vezérlési házirendjétől. A felhasználók létrehozhatnak egy vagy több tárolót a titkos kulcsok tárolására, és a forgatókönyv megfelelő szegmentálása és a titkos kulcsok kezelése karbantartásához szükségesek.   

A következő engedélyek használhatók, egy fő alapon, a titkos kulcshozzáférés-vezérlési bejegyzés egy tárolóban, és szorosan tükrözi a műveletek engedélyezett egy titkos objektum:  

- Titkos kezelési műveletek engedélyei
  - *get*: Olvassa el a titkos  
  - *lista*: A Key Vaultban tárolt titkos fájlok titkos kulcsainak vagy verzióinak felsorolása  
  - *set*: Titkos kapcsolat létrehozása  
  - *törlés*: Titkos fájl törlése  
  - *helyreállítás*: Törölt titkos titok helyreállítása
  - *biztonsági mentés*: Titkos biztonsági másolat a kulcstárolóban
  - *visszaállítás*: Biztonsági másolatot kell adni egy titkos kulcstárolóba

- Jogosultsággal rendelkező műveletek engedélyei
  - *kiürítés*: Törölt titok törlése (végleges törlése)

A titkos kulcsok ról további információt [a Key Vault REST API-hivatkozástitkos műveletei című témakörben talál.](/rest/api/keyvault) Az engedélyek létrehozásáról további információt a [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és [tárolók – hozzáférési szabályzat című témakörben talál.](/rest/api/keyvault/vaults/updateaccesspolicy) 

## <a name="secret-tags"></a>Titkos címkék  
További alkalmazásspecifikus metaadatokat adhat meg címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakternévvel és 256 karakterértékkel rendelkezhet.  

>[!Note]
>A címkéket a hívó akkor tudja elolvasni, ha rendelkezik a *listával* vagy engedélyt *kap.*

## <a name="azure-storage-account-key-management"></a>Az Azure Storage-fiók kulcsának kezelése

A Key Vault képes kezelni az Azure storage-fiók kulcsait:

- Belsőleg key vault listázhatja (szinkronizálja) kulcsok egy Azure-tárfiókkal. 
- A Key Vault rendszeres időközönként újragenerálja (elforgatja) a kulcsokat.
- A főértékek et soha nem adja vissza a hívónak adott válaszként.
- A Key Vault kezeli a tárfiókok és a klasszikus tárfiókok kulcsait.

További információ: [Azure Key Vault Storage-fiókkulcsok](../secrets/overview-storage-keys.md))

## <a name="storage-account-access-control"></a>Tárfiók-hozzáférés-vezérlés

A következő engedélyek használhatók, ha egy felhasználó vagy az egyszerű felhasználó számára engedélyezi a felügyelt tárfiókon végzett műveleteket:  

- A felügyelt tárfiók és az SaS-definíciós műveletek engedélyei
  - *bekés:* Információ beszerez egy tárfiókról 
  - *list:* A Key Vault által kezelt tárfiókok listázása
  - *frissítés*: Tárfiók frissítése
  - *törlés*: Tárfiók törlése  
  - *helyreállítás*: Törölt tárfiók helyreállítása
  - *biztonsági mentés*: Tárfiók biztonsági mentése
  - *visszaállítás*: Biztonsági másolatot álló tárfiók visszaállítása Key Vaultba
  - *set*: Tárfiók létrehozása vagy frissítése
  - *regeneratekey*: Egy tárfiók megadott kulcsértékének újragenerálása
  - *getsas:* Információ beszerez egy SAS-definícióegy tárfiók
  - *listsas*: Storage SAS-definíciók listázása tárfiókhoz
  - *deletesas*: SAS-definíció törlése tárfiókból
  - *setsas*: Új SAS-definíció/attribútumok létrehozása vagy frissítése tárfiókhoz

- Jogosultsággal rendelkező műveletek engedélyei
  - *kiürítés*: Felügyelt tárfiók törlése (végleges törlése)

További információ: [A Storage-fiók műveletei a Key Vault REST API-referencia.](/rest/api/keyvault) Az engedélyek létrehozásáról további információt a [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és [tárolók – hozzáférési szabályzat című témakörben talál.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="next-steps"></a>További lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [Kulcsok, titkos kulcsok és tanúsítványok –](../general/about-keys-secrets-certificates.md)
- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
