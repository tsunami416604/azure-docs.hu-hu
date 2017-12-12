---
title: "Azure AD Connect szinkronizálása: címtárbővítmények |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure AD Connectben a directory böngészőbővítmények funkciója."
services: active-directory
documentationcenter: 
author: AndKjell
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
ms.openlocfilehash: 3ab8b02ad30315de23e5d8e7370cc385a53ecf3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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

Ezek az attribútumok elérhetők Graph keresztül:  
![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Az attribútumok fűzve előtagként a bővítmény\_{AppClientId}\_. A AppClientId értéke az összes attribútum esetében az Azure AD-bérlőben.

## <a name="next-steps"></a>Következő lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
