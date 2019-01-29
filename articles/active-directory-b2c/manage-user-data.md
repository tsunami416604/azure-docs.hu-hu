---
title: Felhasználói adatokat az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan törölheti, vagy az Azure AD B2C felhasználói adatok exportálása.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b03e02a783245459fa28126b73eac2459f6aad67
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182846"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C felhasználói adatok kezelése

 Ez a cikk ismerteti, hogyan kezelheti a felhasználói adatokat az Azure Active Directory (Azure AD) B2C által biztosított műveletek segítségével a [Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog). Felhasználói adatok kezelésére is tartalmaz, törlése vagy exportálása az auditnaplók.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Felhasználói adatok törlése

Felhasználói adatok tárolása az Azure AD B2C-címtár és a naplók. Az összes felhasználói naplózási adatok az Azure AD B2C-t 30 napig őrződnek meg. Ha szeretne felhasználói adatok törlése a 30 napos időtartamon belül, akkor használhatja a [felhasználót törölni](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) műveletet. Egy törlési művelet szükség az Azure AD B2C-bérlők mindegyike adatokat tároló lehet. 

Az Azure AD B2C-ben minden felhasználó hozzá van rendelve egy objektumot. Az Objektumazonosító biztosít, hogy törli a felhasználói adatokat az Azure AD B2C-egyértelmű azonosítója. Az architektúra függően Objektumazonosítóját egy hasznos korrelációs azonosítót lehet más szolgáltatások, például a pénzügyi, marketing, és az ügyfél kapcsolat felügyeleti adatbázisok. 

A legpontosabb felhasználó Objektumazonosítójának lekérése módja beszerzése az Azure AD B2C-hitelesítési út részeként. Ha kérést kap, egy érvényes adatok a felhasználó más módszerekkel, egy offline folyamattal, egy olyan ügyfélszolgálati támogatás szerinti keresés például az ügynök, szükség lehet keresse meg a felhasználót, és jegyezze fel a társított objektum azonosítóját. 

Az alábbi példa bemutatja egy lehetséges Adattörlés folyamatot:

1. A felhasználó bejelentkezik, és kiválasztja **adatok törlése**.
2. Az alkalmazás felajánlja az alkalmazás-felügyeleti szakaszokon belül az adatok törléséhez.
3. Az alkalmazás arra kényszeríti az Azure AD B2C-vel való hitelesítéshez. Az Azure AD B2C jogkivonat biztosít az alkalmazásnak a felhasználó Objektumazonosítóját. 
4. A jogkivonat érkezett az alkalmazás és az objektum azonosítója segítségével törölje a felhasználói adatokat az Azure AD Graph API-hívás használatával. Az Azure AD Graph API törli a felhasználói adatokat, és OK 200-as állapotkódot ad vissza.
5. Az alkalmazás az Objektumazonosítót és egyéb azonosítókhoz kötött használatával szükség szerint más szervezeti rendszerekben hangolja össze a felhasználói adatok törlése.
6. Az alkalmazás megerősíti, hogy az adatok törlését, és biztosítja a következő lépések a felhasználó számára.

## <a name="export-customer-data"></a>Vásárlói adatok exportálása

A vásárlói adatok exportálása az Azure AD B2C-ből a folyamat hasonlít a törlési folyamat.

Az Azure AD B2C felhasználói adatok korlátozva:

- **Az Azure Active Directoryban tárolt adatok**: Az Azure AD B2C-hitelesítés felhasználói interakciósorozatban szereplő adatok Objektumazonosítóját vagy tetszőleges bejelentkezési nevet, például e-mail-cím vagy felhasználónév használatával kérheti le. 
- **Felhasználó-specifikus naplózási események jelentés**: Adatok indexelésére használhatja, az az objektum azonosítója.

A következő példában egy exportálási adatfolyama leírt lépéseket, az alkalmazás által végzett is végezhet el egy háttérbeli folyamatok vagy a címtárban rendszergazdai szerepkörrel rendelkező felhasználó:

1. A felhasználó bejelentkezik az alkalmazásba. Az Azure AD B2C az Azure multi-factor Authentication hitelesítés kikényszeríti, ha szükséges.
2. Az alkalmazás használ a felhasználói hitelesítő adatokat lekérni a felhasználói attribútumok az Azure AD Graph API-művelet meghívásához. Az Azure AD Graph API-t biztosít a attribútum adatok JSON formátumban. A séma függően beállíthatja azonosító jogkivonat tartalma a felhasználó személyes adatait tartalmazza.
3. Az alkalmazás lekéri a felhasználó naplózási tevékenység. Az Azure AD Graph API-t biztosít az alkalmazásnak az eseményadatokat.
4. Az alkalmazás összesíti az adatokat, és elérhetővé teszi a felhasználó.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a felhasználók hogyan érhetik el az alkalmazást, lásd: [felhasználói hozzáférés felügyelete](manage-user-access.md).




