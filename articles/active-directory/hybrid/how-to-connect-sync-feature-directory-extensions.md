---
title: 'Azure AD Connect szinkronizálása: Címtárbővítmények | Microsoft dokumentumok'
description: Ez a témakör ismerteti a címtárbővítmények szolgáltatás az Azure AD Connect.
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
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917913"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect szinkronizálás: Címtárbővítmények
A címtárbővítmények segítségével kiterjesztheti a sémát az Azure Active Directoryban (Azure AD) a helyszíni Active Directory saját attribútumaival. Ez a funkció lehetővé teszi, hogy a helyszíni funkciókat továbbra is kezelő attribútumok alkalmazásával karbantartsa a LOB-alkalmazásokat. Ezek az [attribútumok a bővítményeken](https://docs.microsoft.com/graph/extensibility-overview
)keresztül használhatók fel. Az elérhető attribútumokat a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)segítségével láthatja. Ezzel a funkcióval dinamikus csoportokat is létrehozhat az Azure AD-ben.

Jelenleg egyetlen Office 365-ös számítási feladat sem használja fel ezeket az attribútumokat.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Az Azure AD-vel szinkronizálandó attribútumok testreszabása

A telepítővarázsló egyéni beállítási útvonalán beállíthatja, hogy mely további attribútumokat kívánja szinkronizálni.

>[!NOTE]
>Az Elérhető attribútumok mezőben a kis- és nagybetűk et is figyelembe kell.

![Sémabővítmény varázsló](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A telepítés a következő attribútumokat mutatja, amelyek érvényes jelöltek:

* Felhasználó és csoport objektumtípusok
* Egyértékű attribútumok: Karakterlánc, Logikai, Egész, Bináris
* Többértékű attribútumok: Karakterlánc, Bináris


>[!NOTE]
> Bár az Azure AD Connect támogatja a többértékű Active Directory-attribútumok szinkronizálását az Azure AD-hez többértékű címtár-bővítményekként, jelenleg nincs mód a többértékű könyvtárbővítmény-attribútumokban feltöltött adatok lekérésére/felhasználására.

Az attribútumok listáját az Azure AD Connect telepítése során létrehozott séma-gyorsítótárból olvassa be a rendszer. Ha további attribútumokkal bővítette az Active Directory-sémát, frissítenie kell [a sémát,](how-to-connect-installation-wizard.md#refresh-directory-schema) mielőtt ezek az új attribútumok láthatóvá válnak.

Az Azure AD-ben egy objektum legfeljebb 100 attribútumok címtárbővítmények. A maximális hossz 250 karakter. Ha egy attribútumértéke hosszabb, a szinkronizálási motor csonkolja azt.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>A varázsló által végrehajtott konfigurációs módosítások az Azure AD-ben

Az Azure AD Connect telepítése során egy alkalmazás regisztrálva van, ahol ezek az attribútumok érhetők el. Ez az alkalmazás az Azure Portalon. A neve mindig **bérlői séma-bővítmény alkalmazás.**

![Sémabővítmény-alkalmazás](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Győződjön meg arról, hogy az alkalmazás megtekintéséhez válassza a **Minden alkalmazás** lehetőséget.

Az attribútumok előtaggal vannak ellátva **a(z) {ApplicationId} \_\_kiterjesztéssel.** ApplicationId ugyanazt az értéket az Azure AD-bérlő összes attribútumai. Szüksége lesz erre az értékre az összes többi forgatókönyv ebben a témakörben.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Attribútumok megtekintése a Microsoft Graph API használatával

Ezek az attribútumok a Microsoft Graph [Explorer](https://developer.microsoft.com/graph/graph-explorer#)segítségével már elérhetők a Microsoft Graph API-n keresztül.

>[!NOTE]
> A Microsoft Graph API-ban meg kell kérnie az attribútumok visszaadását. Explicit módon válassza ki `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`az attribútumokat, mint ez: .
>
> További információt a [Microsoft Graph: Lekérdezési paraméterek használata](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)című témakörben talál.

## <a name="use-the-attributes-in-dynamic-groups"></a>Az attribútumok használata dinamikus csoportokban

Az egyik leghasznosabb forgatókönyv, ha ezeket az attribútumokat dinamikus biztonságban vagy Office 365-csoportokban használja.

1. Hozzon létre egy új csoportot az Azure AD-ben. Adjon neki egy jó nevet, és győződjön meg **róla,** hogy a tagság típusa **dinamikus felhasználó**.

   ![Képernyőkép új csoporttal](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Jelölje be a **Dinamikus lekérdezés hozzáadása**lehetőséget. Ha megnézi a tulajdonságokat, akkor nem fogja látni ezeket a kiterjesztett attribútumokat. Először hozzá kell adnia őket. Kattintson **az Egyéni bővítmény tulajdonságainak beszereznie**gombra, adja meg az Alkalmazásazonosítót, majd kattintson a **Tulajdonságok frissítése**gombra.

   ![Képernyőkép, amely szerint könyvtárbővítmények lettek hozzáadva](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Nyissa meg a tulajdonság legördülő menüt, és vegye figyelembe, hogy a hozzáadott attribútumok most már láthatók.

   ![Képernyőkép a felhasználói felületen megjelenő új attribútumokkal](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Töltse ki a kifejezést, hogy megfeleljen az Ön igényeinek. A példánkban a szabály **beállítása (user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Értékesítés és marketing")**.

4. A csoport létrehozása után adjon egy kis időt az Azure AD-nek a tagok feltöltésére, majd tekintse át a tagokat.

   ![Képernyőkép a dinamikus csoport tagjaival](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>További lépések
További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
