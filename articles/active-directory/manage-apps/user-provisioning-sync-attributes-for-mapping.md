---
title: A leképezés az Azure AD-attribútumok szinkronizálása |} A Microsoft Docs
description: Ismerje meg az Azure AD a helyszíni Active Directoryból származó attribútumok szinkronizálása. SaaS-alkalmazásokhoz történő felhasználókiépítés konfigurálása, ha a címtár kiterjesztése szolgáltatás használatával adja hozzá a forrás az attribútumokat, amelyek alapértelmezés szerint nincsenek szinkronizálva.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806116"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>A helyszíni Active Directoryból az Azure AD-alkalmazáshoz való üzembe helyezés attribútum szinkronizálása

Amikor a felhasználók átadásának attribútumleképezések testreszabása, találhatja, hogy hozzá kívánja rendelni az attribútum nem jelenik meg a **forrásattribútum** listája. Ez a cikk bemutatja, hogyan adhat hozzá a hiányzó attribútum szinkronizálása a helyszíni Active Directory (AD) az Azure Active Directory (Azure AD) által.

Az Azure AD felhasználói fiókok Azure AD-ből egy SaaS-alkalmazás üzembe helyezésekor a felhasználói profil létrehozásához szükséges összes adatot kell tartalmaznia. Bizonyos esetekben az adatok elérhetővé tétele, kell szinkronizálására attribútumait a helyszíni AD az Azure ad-hez. Az Azure AD Connect automatikusan szinkronizálja az Azure AD-attribútumok, de nem minden tulajdonságot. Továbbá néhány alapértelmezés szerint a szinkronizált attribútumok (például a SAMAccountName) előfordulhat, hogy nem lesz elérhető az Azure AD Graph API-n keresztül. Ezekben az esetekben az attribútum az Azure AD szinkronizálása az Azure AD Connect címtár kiterjesztése szolgáltatás használhatja. Ezzel a módszerrel az attribútum lesz látható az Azure AD Graph API-t és az Azure AD létesítési szolgáltatás.

Ha a telepítéshez szükséges adatokat az Active Directory, de nem érhető el a fenti okokból kiépítése, kövesse az alábbi lépéseket.
 
## <a name="sync-an-attribute"></a>Attribútum szinkronizálása 

1. Az Azure AD Connect varázsló megnyitásához, válassza ki a feladatokat, és válassza **szinkronizálási beállítások testreszabása**.

   ![Az Azure Active Directory Connect varázsló további feladatok lap](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Jelentkezzen be az Azure AD globális rendszergazdaként. 

3. Az a **választható funkciók** lapon jelölje be **címtárbővítmény-attribútumok szinkronizálása**.
 
   ![Az Azure Active Directory Connect varázsló választható szolgáltatások lapja](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Válassza ki a kívánja kiterjeszteni az Azure AD-attribútumokkal.
   > [!NOTE]
   > A keresés alapján **rendelkezésre álló attribútumok** megkülönbözteti a kis-és nagybetűket.

   ![Az Azure Active Directory Connect varázsló Directory bővítmények kiválasztására szolgáló lap](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Az Azure AD Connect varázsló Befejezés, és lehetővé teszi egy teljes szinkronizálási ciklust futtatásához. A ciklus befejezésekor a séma ki van bővítve, és az új értékekkel között szinkronizálja a helyszíni AD és az Azure ad-ben.
 
6. Az Azure Portalon, közben, netán [szerkesztési felhasználói attribútum-leképezéshez](customize-application-attributes.md), a **forrásattribútum** listában mostantól tartalmazza a hozzáadott attribútum a következő formátumban `<attributename> (extension_<appID>_<attributename>)`. Válassza ki az attribútumot, és készítsen leképezést a célalkalmazás kiépítéshez.

   ![Az Azure Active Directory Connect varázsló Directory bővítmények kiválasztására szolgáló lap](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A hivatkozási attribútumok kiépítésének lehetősége a helyszíni ad-ben, mint például **managedby** vagy **DN/DistinguishedName**, jelenleg nem támogatott. Ez a funkció kérheti a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>További lépések

* [Adja meg, akik a kiépítés hatókörébe](define-conditional-rules-for-provisioning-user-accounts.md)
