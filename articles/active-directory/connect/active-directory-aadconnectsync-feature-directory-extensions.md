---
title: "Azure AD Connect szinkronizálása: címtárbővítmények |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure AD Connectben a directory böngészőbővítmények funkciója."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9abd035b13a0d51c534eb8cac50c045012399a69
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect szinkronizálása: címtárbővítmények
Címtárbővítmények lehetővé teszi a séma kiterjesztése a saját attribútumokkal rendelkező Azure AD-ben a helyszíni Active Directoryból. Ez a funkció lehetővé teszi ÜZLETÁGI alkalmazások továbbra is kezelheti a helyszíni attribútumok fel. Ezek az attribútumok felhasználhatók, keresztül [Azure AD Graph címtárbővítmények](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) vagy [Microsoft Graph](https://graph.microsoft.io/). Megjelenik az attribútumok elérhető használatával [Azure AD Graph explorer](https://graphexplorer.azurewebsites.net/) és [Microsoft Graph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) kulcsattribútumokkal.

Jelenleg nincsenek Office 365 munkaterhelés igényel, ezek az attribútumok.

Szeretné szinkronizálni a telepítési varázsló egyéni beállítások útvonalán mely további attribútumok konfigurálása
![Séma kiterjesztése varázsló](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
A telepítés a következő attribútumok, amelyek a deduplikációra kijelölt érvényes jeleníti meg:

* Felhasználók és csoportok objektumtípusok
* Egyértékű attribútumok: String, Boolean, egész, bináris
* Többértékű attribútumok: karakterlánc, a bináris


>[!NOTE]
> Míg az Azure AD Connect támogatja az Azure ad szolgáltatásba többértékű címtárbővítmények többértékű AD-attribútumok szinkronizálása, jelenleg nincs funkció az Azure ad-ben, amely támogatja a többértékű címtárbővítmények.

Az Azure AD Connect telepítése során létrehozott séma gyorsítótárból attribútumok listája olvasható. Ha további attribútumokkal, az Active Directory-séma ki van bővítve a [sémát frissíteni kell](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) előtt ezeket az új attribútumokat láthatók.

Az Azure AD-objektum lehet akár 100 directory bővítmények attribútuma. A maximális hossza 250 karakterből áll. Ha egy attribútum-érték hosszabb, majd a függvény egésszé csonkítja a szinkronizálási motor.

Az Azure AD Connect telepítése során az alkalmazás regisztrálva van, ahol elérhetők ezek az attribútumok. Ez az alkalmazás az Azure-portálon tekintheti meg.  
![Séma kiterjesztése alkalmazás](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Az attribútumok fűzve előtagként a bővítmény\_{AppClientId}\_. A AppClientId értéke az összes attribútum esetében az Azure AD-bérlőben.

Ezek az attribútumok elérhetők keresztül a **Azure AD Graph**:

Azt lekérdezheti az Azure AD Graph keresztül az Azure AD Graph explorer: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)

![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Vagy a **Microsoft Graph API**:

Azt tudja lekérdezni a Microsoft Graph API segítségével a Microsoft Graph explorer: [https://developer.microsoft.com/en-us/graph/graph-explorer#](https://developer.microsoft.com/en-us/graph/graph-explorer#)

>[!NOTE]
> Az attribútum a visszaadandó kifejezetten kérni kell. Az ilyen attribútumok explicit módon kiválasztásával ehhez: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division vonatkozó további információ a ellenőrizze [Microsoft Graph: lekérdezés-paraméterek használata](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter)

## <a name="next-steps"></a>További lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
