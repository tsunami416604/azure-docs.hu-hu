---
title: A felhasználók listájának letöltése a Azure Active Directory portálon | Microsoft Docs
description: A felhasználói rekordok tömeges letöltése a Azure Active Directory Azure felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00dfac7c3036c4fd457c5d3a6bb4eb7bd4e56998
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84731324"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Felhasználók listájának letöltése Azure Active Directory portálon

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói importálás (létrehozás) műveleteit.

## <a name="required-permissions"></a>Szükséges engedélyek

A felhasználók listájának az Azure AD felügyeleti központból való letöltéséhez be kell jelentkeznie egy, az Azure AD-ben egy vagy több szervezeti szintű rendszergazdai szerepkörhöz hozzárendelt felhasználóval (a felhasználó rendszergazdája a minimálisan szükséges szerepkör). A vendég meghívója és az alkalmazás fejlesztője nem tekintendő rendszergazdai szerepkörnek.

## <a name="to-download-a-list-of-users"></a>A felhasználók listájának letöltése

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezeten belül.
2. Navigáljon Azure Active Directory > felhasználóhoz. Ezután válassza ki a letöltéshez használni kívánt felhasználókat, ha a bal oldali oszlopban lévő jelölőnégyzetet bejelöli az egyes felhasználók mellett. Megjegyzés: jelenleg nincs lehetőség az összes felhasználó kiválasztására az exportáláshoz. Mindegyiket külön kell kiválasztani.
3. Az Azure ad-ben válassza a **felhasználók**  >  **Letöltés felhasználók**lehetőséget.
4. A **felhasználók letöltése** lapon kattintson a **Start** gombra a felhasználói profil tulajdonságait tartalmazó CSV-fájl fogadásához. Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a tömeges művelet eredményei lapon. A fájl az egyes hibák okát tartalmazza.

   ![Válassza ki, hova szeretné listázni a letölteni kívánt felhasználókat](./media/users-bulk-download/bulk-download.png)

   A letöltési fájl a felhasználók szűrt listáját fogja tartalmazni.

   A következő felhasználói attribútumok szerepelnek:

   - userPrincipalName
   - displayName
   - surname
   - Levelezés
   - givenName
   - objectId
   - userType
   - Beosztás
   - Részleg
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
   - korcsoport
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Állapot ellenőrzése

A függőben lévő tömeges kérelmek állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A felhasználók listájának létrehozásához minden tömeges tevékenység akár egy óráig is futhat. Ez lehetővé teszi, hogy legalább 500 000 felhasználóból álló listát hozzanak létre és töltsenek le.

## <a name="next-steps"></a>További lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
