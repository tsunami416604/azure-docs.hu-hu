---
title: Felhasználói adatok kezelése az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: Ismerje meg, hogyan törölheti vagy exportálhatja a felhasználói adatokat az Azure AD B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e245b58449ab773914fc60be056082b82f05035a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184485"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Felhasználói adatok kezelése az Azure Active Directory B2C-ben

 Ez a cikk bemutatja, hogyan kezelheti a felhasználói adatokat az Azure Active Directory B2C (Azure AD B2C) a [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)által biztosított műveletek használatával. A felhasználói adatok kezelése magában foglalja a naplókból származó adatok törlését vagy exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Felhasználói adatok törlése

A felhasználói adatok az Azure AD B2C könyvtárban és a naplónaplókban tárolódnak. Az összes felhasználói naplózási adatok 7 napig megmaradnak az Azure AD B2C-ben. Ha a hét napos időszakon belül törölni szeretné a felhasználói adatokat, [használhatja](https://docs.microsoft.com/graph/api/user-delete) a Felhasználói művelet törlése műveletet. A DELETE művelet szükséges az azure AD B2C-bérlők, ahol az adatok előfordulhat, hogy található.

Az Azure AD B2C minden felhasználója objektumazonosítót kap. Az objektumazonosító egyértelmű azonosítót biztosít a felhasználói adatok törléséhez az Azure AD B2C-ben. Az architektúrától függően az objektumazonosító hasznos korrelációs azonosító lehet más szolgáltatások, például pénzügyi, marketing- és ügyfélkapcsolat-kezelési adatbázisok között.

A felhasználó objektumazonosítójának beszerzésének legpontosabb módja, ha az Azure AD B2C-vel egy hitelesítési út részeként beszerzi azt. Ha egy felhasználótól más módszerekkel érvényes adatkérést kap, kapcsolat nélküli folyamatra, például egy ügyfélszolgálati ügyfélszolgálati ügynök által végzett keresésre lehet szükség a felhasználó megkereséséhez és a társított objektumazonosító megjegyzéséhez.

A következő példa egy lehetséges adattörlési folyamatot mutat be:

1. A felhasználó bejelentkezik, és az Adataim törlése lehetőséget **választja.**
2. Az alkalmazás lehetőséget kínál az alkalmazás adminisztrációs szakaszában lévő adatok törlésére.
3. Az alkalmazás kényszeríti a hitelesítést az Azure AD B2C. Az Azure AD B2C egy jogkivonatot biztosít a felhasználó objektumazonosítójával az alkalmazáshoz.
4. A jogkivonatot az alkalmazás megkapja, és az objektumazonosító segítségével törli a felhasználói adatokat a Microsoft Graph API-hoz való híváson keresztül. A Microsoft Graph API törli a felhasználói adatokat, és 200 OK állapotkódot ad vissza.
5. Az alkalmazás az objektumazonosító vagy más azonosítók használatával vezényli a felhasználói adatok törlését más szervezeti rendszerekben.
6. Az alkalmazás megerősíti az adatok törlését, és a következő lépéseket biztosítja a felhasználó számára.

## <a name="export-customer-data"></a>Ügyféladatok exportálása

Az Ügyféladatok Exportálása az Azure AD B2C-ből hasonló a törlési folyamathoz.

Az Azure AD B2C felhasználói adatai a következőkre korlátozódnak:

- **Az Azure Active Directoryban tárolt adatok:** Az Azure AD B2C hitelesítésfelhasználói felhasználói út során adatokat kérhet le az objektumazonosító vagy bármely bejelentkezési név, például egy e-mail cím vagy felhasználónév használatával.
- **Felhasználóspecifikus naplózási események jelentése**: Az adatokat az objektumazonosító használatával indexelheti.

Az exportadatfolyam következő példájában az alkalmazás által végrehajtottként leírt lépések háttérfolyamattal vagy a címtárban rendszergazdai szerepkörrel rendelkező felhasználóval is elvégezhetők:

1. A felhasználó bejelentkezik az alkalmazásba. Az Azure AD B2C szükség esetén kényszeríti a hitelesítést az Azure többtényezős hitelesítésével.
2. Az alkalmazás a felhasználói hitelesítő adatok segítségével hívja meg a Microsoft Graph API-műveletet a felhasználói attribútumok lekéréséhez. A Microsoft Graph API JSON formátumban biztosítja az attribútumadatokat. Asémától függően beállíthatja, hogy az azonosító token tartalma tartalmazza a felhasználó összes személyes adatát.
3. Az alkalmazás lekéri a felhasználói naplózási tevékenységet. A Microsoft Graph API biztosítja az eseményadatokat az alkalmazás számára.
4. Az alkalmazás összesíti az adatokat, és elérhetővé teszi azokat a felhasználó számára.

## <a name="next-steps"></a>További lépések

Ha tudni szeretné, hogyan kezelheti, hogyan férhetnek hozzá a felhasználók az alkalmazáshoz, olvassa el [a Felhasználói hozzáférés kezelése témakört.](manage-user-access.md)
