---
title: 'Az Azure AD Connect szinkronizálása: Címtárbővítmények |} A Microsoft Docs'
description: Ez a témakör ismerteti a directory böngészőbővítmények funkciója az Azure AD Connectben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 0d058dc1875c69fe3c582c4d68da81c09ab92fcb
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079701"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Az Azure AD Connect szinkronizálása: Címtárbővítmények
Címtárbővítmények segítségével az Azure Active Directoryban (Azure AD) a séma kiterjesztése a saját helyszíni Active Directoryból származó attribútumok. Ez a funkció lehetővé teszi felhasználása az attribútumokat, amelyek továbbra is kezelheti a helyszíni LOB-alkalmazások készítését. Ezek az attribútumok felhasználhatók keresztül [Azure AD Graph API címtárbővítmények](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) vagy [Microsoft Graph](https://developer.microsoft.com/graph/). Megjelenik a rendelkezésre álló attribútumok használatával [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) és [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer), illetve.

Jelenleg nincs Office 365 számítási feladat használ fel ezeket az attribútumokat.

Milyen további attribútumok szeretné szinkronizálni az egyéni beállítások elérési út a telepítési varázslóban konfigurálja.

>[!NOTE]
>A rendelkezésre álló attribútumok mezőben a kis-és nagybetűket.

![Séma kiterjesztése varázsló](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A telepítés a következő attribútumokat, amelyek érvényes jelöltek látható:

* Felhasználók és csoportok objektumtípusok
* Egyértékű attribútumokkal: Bináris logikai érték, egész szám, karakterlánc
* Többértékű attribútumok: Karakterlánc, a bináris


>[!NOTE]
> Bár a többértékű az Active Directory szinkronizálása az Azure AD Connect támogatja az Azure AD szolgáltatásba címtárbővítmények többértékű attribútumok, jelenleg nincs mód a többértékű directory bővítményattribútumok feltöltött adatok olvashatók be és felhasználásához.

A séma gyorsítótárból, az Azure AD Connect telepítése során létrehozott attribútumok listája olvasható. Ha további attribútumok az Active Directory-séma ki van bővítve, meg kell [a sémát](how-to-connect-installation-wizard.md#refresh-directory-schema) előtt ezeket az új attribútumokat láthatók.

Az Azure AD-objektum címtárkiterjesztések attribútumait legfeljebb 100 lehet. A hossza legfeljebb 250 karakter lehet. Ha egy attribútumérték hosszabb, a szinkronizálási motor csonkolja azt.

Az Azure AD Connect telepítése során az alkalmazás regisztrálva van, ahol ezek az attribútumok elérhetők. Ez az alkalmazás az Azure Portalon látható.

![Séma kiterjesztése alkalmazás](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Az attribútumok van fűzve előtagként a bővítmény \_{AppClientId}\_. AppClientId ugyanazt az értéket az összes attribútum esetében az Azure AD-bérlővel rendelkezik.

Ezek az attribútumok elérhetők az Azure AD Graph API-n keresztül. Használatával is lekérdezhetők [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Az Azure AD Graph Explorer](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Vagy a Microsoft Graph API-val, az attribútumok használatával lekérdezheti [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Az attribútumok vissza kell kérnie kell. Explicit módon az ilyen attribútumok kiválasztása: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division =. 
>
> További információkért lásd: [Microsoft Graph: Lekérdezési paraméterek](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
