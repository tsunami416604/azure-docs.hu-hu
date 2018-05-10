---
title: Felhasználói adatokat az Azure AD B2C |} Microsoft Docs
description: Megtudhatja, hogyan törölhető vagy felhasználói adatok az Azure AD B2C exportálása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.openlocfilehash: 414221c3e4942801b5792835d520ec936c8c4bbb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Felhasználói adatokat az Azure AD B2C

 Ez a cikk ismerteti hogyan kezelheti a felhasználói adatokat az Azure Active Directory (AD) B2C használatával művelet, amelyet a [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). Felhasználói adatok kezelése lehetővé teszi a naplók adatok exportálása vagy törli az adatokat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Felhasználói adatok törlése

Felhasználói adatokat az Azure AD B2C-címtár és a vizsgálati naplók tárolja. Minden felhasználó naplózási adatok 30 nap az adatmegőrzés az Azure AD B2C megmarad. Ha a felhasználói adatokat törölni, hogy 30 napon belül kívánja, használhatja a [felhasználó törlése](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) műveletet. A törlési művelet szükség az Azure AD B2C-bérlő minden egyes adatokat tároló lehet. 

Minden felhasználó az Azure AD B2C hozzá van rendelve egy objektumot. Az Objektumazonosító biztosít ahhoz, hogy törli a felhasználói adatokat az Azure AD B2C egyértelműen azonosítót.  Az architektúra Objektumazonosító függően egy hasznos korrelációs azonosító keresztül más szolgáltatásokon, például pénzügyi, marketing, és a felhasználói kapcsolat felügyeleti adatbázisok.  

A legpontosabb objektum lekérése a felhasználó módja szerezheti be, az Azure AD B2C hitelesítési út részeként.  Az adatok egy érvényes kérelem érkezett a felhasználó más módszerekkel, offline művelet, ha egy ügyfélszolgálati támogatás szerinti keresés például ügynök, akkor lehet szükség keresse meg azt a felhasználót, és jegyezze fel a társított objektum azonosítóját. 

A következő példa bemutatja egy lehetséges adatfolyam törlése:

1. A felhasználó jelentkezik be és kijelöli **az adatok törlése**.
2. Az alkalmazás kínálja fel az adatok egy felügyeleti szakaszban az alkalmazás törléséhez.
3. Az alkalmazás arra kényszeríti az Azure AD B2C-hitelesítéshez. Az Azure AD B2C jogkivonatot biztosít az alkalmazásnak a felhasználó objektum azonosítója. 
4. A token érkezik, az alkalmazás és az objektum Azonosítót használja az Azure AD Graph API hívása felhasználói adatok törlése. Az Azure AD Graph API törli a felhasználói adatokat, és egy állapotkódot 200 OK adja vissza.
5. Az alkalmazás használatával az Objektumazonosító vagy egyéb szükség szerint koordinálja egyéb szervezeti rendszerekben felhasználói adatok törlését.
6. Az alkalmazás megerősíti, hogy az adatok törlését, és a felhasználó következő lépéseit.

## <a name="export-customer-data"></a>Felhasználói adatok exportálása

A felhasználói adatok exportálása az Azure AD B2C folyamat hasonlít a törlési folyamat.

Az Azure AD B2C felhasználói adatok korlátozva:

- **Az Azure Active Directoryban tárolt adatok** -is lehet adatokat beolvasni az Azure AD B2C-hitelesítés felhasználói út Objektumazonosító vagy bármely bejelentkezési nevével, például e-mail vagy felhasználónév a.  
- **Felhasználó által megadott naplózási események jelentés** -adatok indexelése az objektum azonosítójával.

A következő példa egy exportálási adatfolyama, az alkalmazás által végzett leírt lépéseket is végrehajtható egy háttér folyamat vagy egy felhasználó egy rendszergazdai szerepkörrel rendelkező könyvtárban:

1. A felhasználó bejelentkezik az alkalmazást. Az Azure AD B2C-hitelesítés és többtényezős hitelesítési az érvényesíti, szükség esetén.
2. Az alkalmazás a felhasználói hitelesítő adatok használatával felhasználói attribútumok beolvasása az Azure AD Graph API művelet hívására. Az Azure AD Graph API-t adja meg az attribútum adatait JSON formátumban. Attól függően, hogy a séma azonosító token tartalma előfordulhat, hogy úgy, hogy a felhasználó személyes adatait tartalmazza.
3. Az alkalmazás végfelhasználói naplózási tevékenység kéri le. Az Azure AD Graph API biztosít az alkalmazás az eseményadatok.
4. az alkalmazás összesíti az adatokat, és lehetővé teszi a felhasználó számára.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kezelheti, hogy a felhasználók miként férhetnek hozzá az alkalmazás [felügyelheti a felhasználók hozzáférését](manage-user-access.md)




