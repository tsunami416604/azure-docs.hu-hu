---
title: 'Azure AD Connect szinkronizálása: címtárbővítmények |} Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connectben a directory böngészőbővítmények funkciója.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: dda35e63c209951547a667c46639dc0f37c87b43
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593632"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect szinkronizálása: címtárbővítmények
Az Azure Active Directory (Azure AD) a séma kiterjesztése címtárbővítmények használható saját attribútumait a helyszíni Active Directoryból. Ez a funkció lehetővé teszi a LOB-alkalmazásokat hozhat létre attribútumokat, amelyek továbbra is kezelheti a helyszíni fel. Ezek az attribútumok felhasználhatók, keresztül [Azure AD Graph API címtárbővítmények](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) vagy [Microsoft Graph](https://graph.microsoft.io/). Megjelenik a rendelkezésre álló attribútumok használatával [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) és [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer), illetve.

Jelenleg nincsenek Office 365 munkaterhelés igényel, ezek az attribútumok.

Szeretné szinkronizálni a telepítési varázsló egyéni beállítások útvonalán mely további attribútumok konfigurálása

![Séma kiterjesztése varázsló](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

A telepítés a következő attribútumok, amelyek a deduplikációra kijelölt érvényes jeleníti meg:

* Felhasználók és csoportok objektumtípusok
* Egyértékű attribútumok: String, Boolean, egész, bináris
* Többértékű attribútumok: karakterlánc, a bináris


>[!NOTE]
> Az Azure AD Connect szinkronizálási többértékű Active Directory-attribútumok Azure ad szolgáltatásba többértékű címtárbővítmények támogatja. De nincs funkció jelenleg az Azure AD-ben támogatja a többértékű címtárbővítmények.

Az attribútumok listájában van gyorsítótárából olvasta be a séma, amely az Azure AD Connect telepítés során jön létre. Ha további attribútumok az Active Directory-séma már ki van bővítve, kell [a séma frissítése](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) előtt ezeket az új attribútumokat láthatók.

Az Azure AD-objektum lehet címtárbővítmények legfeljebb 100 attribútumait. A hossza legfeljebb 250 karakterből áll. Ha egy attribútum-érték hosszabb, a szinkronizálási motor csonkolja azt.

Az Azure AD Connect telepítése során az alkalmazás regisztrálva van, ahol elérhetők ezek az attribútumok. Ez az alkalmazás az Azure-portálon tekintheti meg.

![Séma kiterjesztése alkalmazás](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Az attribútumok fűzve előtagként a bővítmény \_{AppClientId}\_. AppClientId értéke az összes attribútum esetében az Azure AD-bérlőben.

Ezek az attribútumok elérhetők az Azure AD Graph API-n keresztül. Segítségével lekérdezhető [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Az Azure AD Graph Explorer](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Vagy a Microsoft Graph API-n keresztül, az attribútumok alkalmazásával lekérheti [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer#).

>[!NOTE]
> Az attribútumok vissza kell kérnie kell. Explicit módon az ilyen attribútumok kiválasztása: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division =. 
>
> További információkért lásd: [Microsoft Graph: lekérdezés paraméterekkel](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>További lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
