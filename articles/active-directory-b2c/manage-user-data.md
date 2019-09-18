---
title: Felhasználói adatkezelés Azure Active Directory B2Cban | Microsoft Docs
description: Megtudhatja, hogyan törölhet vagy exportálhatunk felhasználói információkat Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 78726620db119abf617be8a30cf03697b04e382b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064086"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Felhasználói adatkezelés Azure Active Directory B2C

 Ez a cikk azt ismerteti, hogyan kezelheti a felhasználói adatok Azure Active Directory B2Cban (Azure AD B2C) az [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog)által biztosított műveletek használatával. A felhasználói adatok kezelése magában foglalja az adatok törlését és a naplókból való exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Felhasználói adatértékek törlése

A felhasználói adatszolgáltatások tárolása a Azure AD B2C könyvtárban és a naplókban történik. Az összes felhasználói naplózási adat megmarad a Azure AD B2C 7 napig. Ha az adott 7 napos időszakon belül szeretné törölni a felhasználói adatbázisokat, használhatja a [felhasználó törlése](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) műveletet. TÖRLÉSi műveletre van szükség az egyes Azure AD B2C bérlők esetében, ahol az adattárolók találhatók.

Azure AD B2C minden felhasználója hozzá van rendelve egy objektum-AZONOSÍTÓhoz. Az objektumazonosító egyértelmű azonosítót biztosít, amellyel törölheti Azure AD B2C felhasználói adatait. Az architektúrától függően az objektumazonosító hasznos korrelációs azonosító lehet más szolgáltatások, például pénzügyi, marketing és ügyfélkapcsolat-kezelési adatbázisok között.

A felhasználó objektum-AZONOSÍTÓjának lekéréséhez a legpontosabb módszer, ha a hitelesítési út részeként beszerezze Azure AD B2C. Ha más módszerekkel is érvényes adatkérést kap a felhasználótól, az offline folyamat, például az ügyfélszolgálati támogatási ügynök általi keresés szükséges lehet a felhasználó megtalálásához, és jegyezze fel a társított objektumazonosítót.

Az alábbi példa az adattörlés lehetséges folyamatát mutatja be:

1. A felhasználó bejelentkezik, éskiválasztja az adattörlés lehetőséget.
2. Az alkalmazás lehetőséget kínál az alkalmazások törlésére az alkalmazás adminisztrációs részén belül.
3. Az alkalmazás Azure AD B2C hitelesítését kényszeríti. Azure AD B2C biztosít egy jogkivonatot, amely a felhasználó objektumazonosítóát adja vissza az alkalmazásnak.
4. Az alkalmazás megkapja a jogkivonatot, és az objektumazonosító segítségével törli a felhasználói adatelemet az Azure AD Graph API hívásával. Az Azure AD Graph API törli a felhasználói adatok számát, és visszaadja az 200-es állapotkódot.
5. Az alkalmazás az objektumazonosító vagy más azonosítók használatával összehangolja a felhasználói adat más szervezeti rendszerekben való törlését.
6. Az alkalmazás megerősíti az adattörlést, és a következő lépéseket biztosítja a felhasználó számára.

## <a name="export-customer-data"></a>Ügyféladatok exportálása

A vásárlói adatok Azure AD B2Cból való exportálásának folyamata hasonló a törlési folyamathoz.

Azure AD B2C felhasználói adatmennyiség a következőre korlátozódik:

- **A Azure Active Directory tárolt adatértékek**: Az Azure AD B2C hitelesítés felhasználói úton az objektum AZONOSÍTÓjának vagy a bejelentkezési név (például e-mail-cím vagy Felhasználónév) használatával kérhet le az adatgyűjtést.
- **Felhasználó-specifikus naplózási események jelentés**: Az adatindexelést az objektumazonosító használatával végezheti el.

Az exportálási folyamat következő példájában az alkalmazás által elvégezhető lépéseket egy háttérrendszer vagy egy rendszergazdai szerepkörrel rendelkező felhasználó is végrehajthatja a címtárban:

1. A felhasználó bejelentkezik az alkalmazásba. Azure AD B2C szükség esetén kikényszeríti az Azure Multi-Factor Authentication hitelesítését.
2. Az alkalmazás a felhasználói hitelesítő adatok használatával hívja meg az Azure AD Graph API műveletet a felhasználói attribútumok lekéréséhez. Az Azure AD Graph API JSON formátumú attribútumot biztosít. A sémától függően beállíthatja az azonosító token tartalmát úgy, hogy az tartalmazza a felhasználóra vonatkozó összes személyes információt.
3. Az alkalmazás lekéri a felhasználói naplózási tevékenységet. Az Azure AD-Graph API az adott alkalmazáshoz szolgáltatja az eseményre vonatkozó adatgyűjtést.
4. Az alkalmazás összesíti az adatokat, és elérhetővé teszi a felhasználó számára.

## <a name="next-steps"></a>További lépések

- A felhasználók alkalmazáshoz való hozzáférésének kezelésével kapcsolatos információkért lásd: [felhasználói hozzáférés kezelése](manage-user-access.md).




