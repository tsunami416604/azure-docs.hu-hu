---
title: A felhasználók listájának letöltése az Azure Active Directory portálon | Microsoft dokumentumok
description: Töltse le a felhasználói rekordokat tömegesen az Azure Active Directory Azure Felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532643"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Felhasználók listájának letöltése az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) támogatja a tömeges felhasználói importálási (létrehozási) műveleteket.

## <a name="required-permissions"></a>Szükséges engedélyek

A felhasználók listájának letöltéséhez az Azure AD felügyeleti központból be kell jelentkeznie egy vagy több szervezetszintű rendszergazdai szerepkörhöz rendelt felhasználóval az Azure AD-ben (a felhasználói rendszergazda a minimálisan szükséges szerepkör). A vendégmeghívó és az alkalmazásfejlesztő nem minősül rendszergazdai szerepkörnek.

## <a name="to-download-a-list-of-users"></a>Felhasználók listájának letöltése

1. [Jelentkezzen be az Azure AD-szervezetbe](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezetben.
2. Keresse meg az Azure Active Directory > felhasználók. Ezután válassza ki azokat a felhasználókat, akiket be szeretne vonni a letöltésbe, jelölje be az egyes felhasználók melletti bal oldali oszlopban lévő négyzetet. Megjegyzés: Jelenleg nincs mód az összes felhasználó kiválasztására az exportáláshoz. Mindegyiket egyenként kell kiválasztani.
3. Az Azure AD-ben válassza a **Felhasználók** > **felhasználók letöltése**lehetőséget.
4. A **Felhasználók letöltése** lapon válassza az **Indítás** lehetőséget, ha a felhasználói profil tulajdonságait tartalmazó CSV-fájlt szeretne kapni. Ha vannak hibák, letöltheti és megtekintheti az eredményfájlt a Tömeges művelet eredménylapján. A fájl tartalmazza az egyes hibák okát.

   ![Válassza ki, hogy hol szeretné a listát a letölteni kívánt felhasználók között](./media/users-bulk-download/bulk-download.png)

   A letöltési fájl a felhasználók szűrt listáját fogja tartalmazni.

   A következő felhasználói attribútumok szerepelnek:

   - userPrincipalName
   - displayName
   - surname
   - Levelezés
   - givenName
   - objectId
   - userType (felhasználótípusa)
   - jobTitle
   - Részleg
   - accountEnabled
   - usageLocation (használathelye)
   - utcacím
   - state
   - ország
   - physicalDeliveryOfficeName
   - city
   - postai kód
   - telefonszáma
   - mobil
   - hitelesítésPhoneNumber
   - hitelesítésAlternatívPhoneNumber
   - hitelesítésE-mail
   - alternateEmailAddress
   - korcsoport
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Állapot ellenőrzése

A függőben lévő tömeges kérelmek állapotát a **Tömeges művelet eredménylapján láthatja.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Tömeges letöltési szolgáltatás korlátai

Minden tömeges tevékenység a felhasználók listájának létrehozásához legfeljebb egy órán keresztül futtatható. Ez lehetővé teszi legalább 500 000 felhasználóból álló lista létrehozását és letöltését.

## <a name="next-steps"></a>További lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Tömeges visszaállítási felhasználók](users-bulk-restore.md)
