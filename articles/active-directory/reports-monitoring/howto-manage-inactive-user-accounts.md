---
title: Inaktív felhasználói fiókok kezelése az Azure AD-ben | Microsoft dokumentumok
description: További információ az elavulttá vált Azure AD-ben található felhasználói fiókok észleléséről és kezeléséről
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886041"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Útmutató: Inaktív felhasználói fiókok kezelése az Azure AD-ben

Nagy környezetekben a felhasználói fiókok nem mindig törlődnek, amikor az alkalmazottak elhagyják a szervezetet. Informatikai rendszergazdaként fel szeretné deríteni és kezelni ezeket az elavult felhasználói fiókokat, mert azok biztonsági kockázatot jelentenek.

Ez a cikk ismerteti az azure-beli AD elavult felhasználói fiókok kezelésére. 

## <a name="what-are-inactive-user-accounts"></a>Mik azok az inaktív felhasználói fiókok?

Az inaktív fiókok olyan felhasználói fiókok, amelyekre a szervezet tagjai már nem igényelnek az erőforrásokhoz való hozzáféréshez. Az inaktív fiókok egyik kulcsazonosítója, hogy *egy ideje* nem használták őket a környezetébe való bejelentkezéshez. Mivel az inaktív fiókok a bejelentkezési tevékenységhez vannak kötve, használhatja az utolsó bejelentkezés időbélyegét, amely sikeres volt észlelésükhöz. 

A kihívás ennek a módszernek az, hogy meghatározza, mit *jelent egy ideig* abban az esetben, a környezet. Előfordulhat például, hogy a felhasználók egy ideig nem jelentkeznek be egy *környezetbe,* mert szabadságon vannak. Az inaktív felhasználói fiókok különbözetének meghatározásakor figyelembe kell vennie az összes jogos okot, amiért nem jelentkezik be a környezetbe. Számos szervezetben az inaktív felhasználói fiókok különbözete 90 és 180 nap között van. 

Az utolsó sikeres bejelentkezés potenciális betekintést nyújt a felhasználó továbbra is szükség van az erőforrásokhoz való hozzáférés.  Segíthet annak meghatározásában, hogy szükség van-e még csoporttagságra vagy alkalmazás-hozzáférésre, vagy eltávolítható-e. A külső felhasználó kezelése, megértheti, ha egy külső felhasználó még mindig aktív a bérlőn belül, vagy meg kell tisztítani. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Az inaktív felhasználói fiókok észlelése

Az inaktív fiókokat a **Microsoft Graph** API **signInActivity** erőforrástípusa által elérhetővé tett **lastSignInDateTime** tulajdonság kiértékelésével észlelheti. Ezzel a tulajdonsággal a következő esetekben valósíthat meg megoldást:

- **Felhasználók név szerint:** Ebben az esetben egy adott felhasználót név szerint keres, amely lehetővé teszi a lastSignInDate kiértékelését:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Felhasználók dátum szerint**: Ebben az esetben a megadott dátum előtt kéri a lastSignInDateTime lastSignInDateTime-mal rendelkező felhasználók listáját:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Amit még tudnia kell

Ez a szakasz felsorolja, mit kell tudni a lastSignInDateTime tulajdonságról.

### <a name="how-can-i-access-this-property"></a>Hogyan érhetem el ezt a tulajdonságot?

A **lastSignInDateTime** tulajdonságot a Microsoft Graph REST [API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph) [signInActivity erőforrástípusa](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) teszi elérhetővé.   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>A LastSignInDateTime tulajdonság elérhető a Get-AzureAdUser parancsmagon keresztül?

Nem.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Az Azure AD melyik kiadására van szükségem a tulajdonság eléréséhez?

Ezt a tulajdonságot az Azure AD összes kiadásában elérheti.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Milyen engedélyre van szükségem a tulajdonság elolvasásához?

A tulajdonság olvasásához a következő jogokat kell megadnia: 

- AuditLogs.Read.All
- Szervezet.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Mikor frissíti az Azure AD a tulajdonságot?

Minden interaktív bejelentkezés, amely sikeres volt, az alapul szolgáló adattár frissítését eredményezi. A sikeres bejelentkezések általában 10 percen belül megjelennek a kapcsolódó bejelentkezési jelentésben.
 

### <a name="what-does-a-blank-property-value-mean"></a>Mit jelent az üres tulajdonságérték?

A lastSignInDateTime időbélyeg létrehozásához sikeres bejelentkezésre van szükség. Mivel a lastSignInDateTime tulajdonság egy új funkció, a lastSignInDateTime tulajdonság értéke üres lehet, ha:

- A felhasználó utolsó sikeres bejelentkezése a funkció megjelenése előtt történt (2019. december 1.).
- Az érintett felhasználói fiók soha nem volt használva a sikeres bejelentkezéshez.

## <a name="next-steps"></a>További lépések

* [Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Bejelentkezési tevékenység jelentés API-jának hivatkozása](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
