---
title: 'Azure AD Connect szinkronizálás: Directory-bővítmények | Microsoft Docs'
description: Ez a témakör a Azure AD Connect Directory Extensions szolgáltatását ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fdfce58bdd8e13637e77d01d4b6c0ab21f696a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607652"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect szinkronizálás: Címtárkiterjesztések
A (z) Azure Active Directory (Azure AD) sémájának kiterjesztése a helyi Active Directory saját attribútumaival is elvégezhető. Ez a funkció lehetővé teszi LOB-alkalmazások készítését olyan attribútumok fogyasztásával, amelyeket továbbra is a helyszínen kezelhet. Ezek az attribútumok felhasználhatók az [Azure AD Graph API Directory Extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) vagy [Microsoft Graph](https://developer.microsoft.com/graph/)használatával. Az elérhető attribútumok az [Azure ad Graph Explorer](https://graphexplorer.azurewebsites.net/) és a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használatával tekinthetők meg.

Jelenleg az Office 365-es munkaterhelések nem használják fel ezeket az attribútumokat.

Konfigurálja, hogy mely további attribútumokat kívánja szinkronizálni a telepítővarázsló egyéni beállítások elérési útján.

>[!NOTE]
>Az elérhető attribútumok mező megkülönbözteti a kis-és nagybetűket.

![Séma-kiterjesztés varázsló](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A telepítés a következő attribútumokat jeleníti meg, amelyek érvényes jelöltek:

* Felhasználói és csoport típusú objektumok típusai
* Egyértékű attribútumok: Karakterlánc, logikai, egész szám, bináris
* Többértékű attribútumok: Karakterlánc, bináris


>[!NOTE]
> Bár a Azure AD Connect támogatja a többértékű Active Directory attribútumok többértékű címtár-bővítményként történő szinkronizálását, jelenleg nincs mód a többértékű címtár-bővítmény attribútumaiban feltöltött adatlekérdezések lekérésére/felhasználására.

Az attribútumok listáját a rendszer a Azure AD Connect telepítésekor létrehozott séma-gyorsítótárból olvassa be. Ha további attribútumokkal bővítette a Active Directory sémát, frissítenie kell [a sémát](how-to-connect-installation-wizard.md#refresh-directory-schema) , mielőtt láthatóvá válnak az új attribútumok.

Az Azure AD-beli objektumok legfeljebb 100 attribútumot tartalmazhatnak a címtár-kiterjesztésekhez. A maximális hossz 250 karakter. Ha egy attribútum értéke továbbra is fennáll, a szinkronizálási motor csonkolja azt.

Azure AD Connect telepítésekor az alkalmazás regisztrálva lesz, ahol ezek az attribútumok elérhetők. Ezt az alkalmazást a Azure Portal tekintheti meg.

![Séma-bővítmény alkalmazás](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Az attribútumok {AppClientId} \_\_kiterjesztésű előtaggal vannak ellátva. A AppClientId ugyanazt az értéket adja meg az Azure AD-bérlő összes attribútuma számára.

Ezek az attribútumok mostantól elérhetők az Azure AD Graph APIon keresztül. A lekérdezéseket az [Azure ad Graph Explorer](https://graphexplorer.azurewebsites.net/)használatával kérdezheti le.

![Azure AD Graph Explorer](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Az attribútumokat a Microsoft Graph API-n keresztül is lekérdezheti a [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Meg kell kérnie a visszaadott attribútumok értékét. Explicit módon válassza ki a következő attribútumokat:\:HTTPS//graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $Select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> További információkért lásd [: Microsoft Graph: Lekérdezési paraméterek](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)használata

## <a name="next-steps"></a>További lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
