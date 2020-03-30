---
title: Attribútumok szinkronizálása az Azure AD szolgáltatással a leképezéshez | Microsoft dokumentumok
description: Ismerje meg, hogyan szinkronizálhatja az attribútumokat a helyszíni Active Directory és az Azure AD között. Amikor konfigurálja a felhasználó kiépítését az SaaS-alkalmazásokba, használja a címtárbővítmény-szolgáltatást az alapértelmezés szerint nem szinkronizált forrásattribútumok hozzáadásához.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522271"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Attribútum szinkronizálása a helyszíni Active Directoryból az Azure AD-hez egy alkalmazásba való kiépítéshez

Amikor az attribútum-hozzárendelés-hozzárendeléseket a felhasználó kiépítéséhez szabja testre, előfordulhat, hogy a leképezni kívánt attribútum nem jelenik meg a **Forrás attribútumlistában.** Ez a cikk bemutatja, hogyan adja hozzá a hiányzó attribútumot a helyszíni Active Directoryból (AD) az Azure Active Directoryba (Azure AD) történő szinkronizálásával.

Az Azure AD-nek tartalmaznia kell a felhasználói profil létrehozásához szükséges összes adatot, amikor felhasználói fiókokat létesít az Azure AD-ből egy SaaS-alkalmazásba. Bizonyos esetekben az adatok elérhetővé teszi szükség lehet szinkronizálni attribútumok a helyszíni AD az Azure AD. Az Azure AD Connect automatikusan szinkronizálja bizonyos attribútumokat az Azure AD-vel, de nem minden attribútumot. Továbbá előfordulhat, hogy az alapértelmezés szerint szinkronizált attribútumok (például a SAMAccountName) nem érhetőel el a Microsoft Graph API használatával. Ezekben az esetekben az Azure AD Connect címtárbővítmény-funkcióval szinkronizálhatja az attribútumot az Azure AD-vel. Így az attribútum látható lesz a Microsoft Graph API és az Azure AD-kiépítési szolgáltatás.

Ha a kiépítéshez szükséges adatok az Active Directoryban vannak, de a fent leírt okok miatt nem érhetők el a kiépítéshez, kövesse az alábbi lépéseket.
 
## <a name="sync-an-attribute"></a>Attribútum szinkronizálása 

1. Nyissa meg az Azure AD Connect varázslót, válassza a Feladatok lehetőséget, és válassza **a Szinkronizálási beállítások testreszabása lehetőséget.**

   ![Az Azure Active Directory Csatlakozás varázsló További feladatok lap](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Jelentkezzen be Azure AD globális rendszergazdaként. 

3. A **Választható szolgáltatások** lapon válassza a **Címtárbővítmény attribútumszinkronizálása**lehetőséget.
 
   ![Az Azure Active Directory Csatlakozás varázsló Választható szolgáltatások lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Válassza ki az Azure AD-re kiterjeszteni kívánt attribútum(oka)t.
   > [!NOTE]
   > Az **Elérhető attribútumok csoportban** a kis- és nagybetűket megkülönböztető keresés.

   ![Az Azure Active Directory csatlakoztatása varázsló könyvtárbővítményeiválasztó lap](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Fejezze be az Azure AD Connect varázslót, és engedélyezze a teljes szinkronizálási ciklus futtatását. Amikor a ciklus befejeződött, a séma kivan bővítve, és az új értékek szinkronizálva vannak a helyszíni AD és az Azure AD között.
 
6. Az Azure Portalon a [felhasználói attribútum-hozzárendelések szerkesztése](customize-application-attributes.md)közben a **Forrás attribútumlista** mostantól `<attributename> (extension_<appID>_<attributename>)`a hozzáadott attribútumot fogja tartalmazni a formátumban. Válassza ki az attribútumot, és rendelje hozzá a célalkalmazás kiépítéshez.

   ![Az Azure Active Directory csatlakoztatása varázsló könyvtárbővítményeiválasztó lap](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A helyszíni AD-ből származó hivatkozási attribútumok , például **a managedby** vagy **a DN/DistinguishedName**szolgáltatás létesítésének lehetősége ma nem támogatott. Ezt a funkciót a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>További lépések

* [Annak meghatározása, hogy ki van a kiépítés hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
