---
title: A felhasználók listájának letöltése (előzetes verzió) a Azure Active Directory portálon | Microsoft Docs
description: A felhasználói rekordok tömeges letöltése a Azure Active Directory Azure felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063832"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>A felhasználók listájának letöltése (előzetes verzió) a Azure Active Directory portálon

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói importálás (létrehozás) műveleteit.

## <a name="required-permissions"></a>Szükséges engedélyek

Az Azure AD felügyeleti központban lévő felhasználók listájának letöltéséhez be kell jelentkeznie egy, az Azure AD-ben egy vagy több szervezeti szintű rendszergazdai szerepkörhöz hozzárendelt felhasználóval. A vendég meghívója és az alkalmazás fejlesztője nem tekintendő rendszergazdai szerepkörnek.

## <a name="to-download-a-list-of-users"></a>A felhasználók listájának letöltése

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezeten belül.
2. Navigáljon Azure Active Directory > felhasználóhoz. Ezután válassza ki a letöltéshez használni kívánt felhasználókat, ha a bal oldali oszlopban lévő jelölőnégyzetet bejelöli az egyes felhasználók mellett. Megjegyzés: jelenleg nincs lehetőség az összes felhasználó kiválasztására az exportáláshoz. Mindegyiket külön kell kiválasztani.
3. Az Azure AD-ben válassza a **felhasználók** > **felhasználók letöltése**lehetőséget.
4. A **felhasználók letöltése** lapon kattintson a **Start** gombra a felhasználói profil tulajdonságait tartalmazó CSV-fájl fogadásához. Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a tömeges művelet eredményei lapon. A fájl az egyes hibák okát tartalmazza.

   ![Válassza ki, hova szeretné listázni a letölteni kívánt felhasználókat](./media/users-bulk-download/bulk-download.png)

   A letöltési fájl a felhasználók szűrt listáját fogja tartalmazni.

   A következő felhasználói attribútumok szerepelnek:

   - userPrincipalName
   - displayName
   - Vezetéknév
   - mail
   - givenName
   - objectId
   - userType
   - Beosztás
   - Szervezeti egység
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - ország
   - physicalDeliveryOfficeName
   - city
   - Irányítószám
   - telephoneNumber
   - mobil
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - Beszerzésimennyiség
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Állapot ellenőrzése

A függőben lévő tömeges kérelmek állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![Feltöltés állapotának keresése a tömeges műveletek eredményei lapon](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A felhasználók listájának létrehozásához minden tömeges tevékenység akár egy óráig is futhat. Ez lehetővé teszi, hogy legalább 500 000 felhasználóból álló listát hozzanak létre és töltsenek le.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
