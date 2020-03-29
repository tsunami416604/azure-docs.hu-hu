---
title: Az Azure AD Connectben nem szinkronizált attribútumok hibáinak elhárítása | Microsoft Dokumentumok"
description: Ez a témakör az attribútumok szinkronizálásával kapcsolatos problémák hibaelhárítási lépéseit ismerteti a hibaelhárítási feladattal.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455969"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Az Azure AD Connect ben nem szinkronizált attribútumok – problémamegoldás

## <a name="recommended-steps"></a>**Ajánlott lépések**

Az attribútumok szinkronizálásával kapcsolatos problémák vizsgálata előtt ismerjük meg az **Azure AD Connect** szinkronizálási folyamatát:

  ![Az Azure AD Connect szinkronizálási folyamata](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminológia**

* **CS:** Összekötő szóköz, egy tábla az adatbázisban.
* **MV:** Metaverse, egy tábla az adatbázisban.
* **Hirdetés:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Szinkronizálási lépések**

* Importálás AD-ből: Az Active Directory-objektumok az Active Directory active directory n-szolgáltatásokba kerülnek.

* Importálás AAD-ból: Az Azure Active Directory-objektumok az AAD CS-be kerülnek.

* Szinkronizálás: A **bejövő szinkronizálási szabályok** és **a kimenő szinkronizálási szabályok** a prioritási sorrendben futnak alacsonyabbtól magasabbig. A szinkronizálási szabályok megtekintéséhez nyissa meg a **Szinkronizálási szabályok szerkesztőjét** az asztali alkalmazásokból. A **bejövő szinkronizálási szabályok** adatokat hoznak a CS-ről az MV-re. A **kimenő szinkronizálási szabályok** áthelyezik az adatokat az MV-ről a CS-re.

* Exportálás AD szolgáltatásba: A szinkronizálás futtatása után az objektumok exportálása az Active Directory tartományi szolgáltatásokból az **Active Directoryba**történik.

* Exportálás AAD-ba: A szinkronizálás futtatása után az objektumok exportálása az AAD CS-ből az **Azure Active Directoryba**történik.

### <a name="step-by-step-investigation"></a>**Lépésről lépésre vizsgálat**

* Elkezdjük a keresést a **Metaverse-ből,** és megnézzük az attribútum leképezését a forrástól a célig.

* Indítsa el **a Szinkronizálási szolgáltatáskezelőt** az asztali alkalmazásokból, az alábbiak szerint:

  ![Szinkronizálási szolgáltatáskezelő indítása](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* A **Szinkronizálási szolgáltatáskezelőben**jelölje ki a **Metaverzum-keresést**, válassza a **Hatókör objektumtípus szerint**lehetőséget, jelölje ki az objektumot egy attribútum használatával, majd kattintson a **Keresés** gombra.

  ![Metaverzum keresése](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Kattintson duplán a **metaverzum-keresésben** található objektumra az összes attribútum megtekintéséhez. Az **Összekötők** fülre kattintva megnézheti a megfelelő objektumot az összes **összekötőtérben.**

  ![Metaverzum-objektum összekötők](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Kattintson duplán az **Active Directory-összekötőre** az **összekötőtér** jellemzőinek megtekintéséhez. Kattintson az **Előnézet** gombra, a következő párbeszédpanelen kattintson az **Előnézet létrehozása** gombra.

  ![Összekötő tér jellemzői](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Most kattintson az **Import Attribútum folyamat**, ez azt mutatja, áramlását attribútumok Active Directory Connector **Space** a **Metaverse**. **A Szinkronizálási szabály** oszlop azt mutatja, hogy melyik **szinkronizálási szabály** járult hozzá az attribútumhoz. **Az Adatforrás** oszlop az összekötőtér attribútumait jeleníti **meg.** **A Metaverse attribútum** oszlop a **Metaverzum attribútumait**jeleníti meg. Itt megkeresheti a nem szinkronizált attribútumot. Ha itt nem találja az attribútumot, akkor ez nincs leképezve, és új egyéni **szinkronizálási szabályt** kell létrehoznia az attribútum leképezéséhez.

  ![Összekötő tér jellemzői](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kattintson az **Attribútumfolyamat exportálása** elemre a bal oldali ablaktáblában a **Metaverzumból** az **Active Directory összekötő területére** a **kimenő szinkronizálási szabályok**használatával.

  ![Összekötő tér jellemzői](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Hasonlóképpen megtekintheti az **Azure Active Directory-összekötő tér** objektumot, és **létrehozhatja** az előnézetet a **Metaverzumból** az **összekötő térbe** irányuló attribútum-áramlás megtekintéséhez, és fordítva, így megvizsgálhatja, hogy egy attribútum miért nem szinkronizál.

## <a name="recommended-documents"></a>**Ajánlott dokumentumok**
* [Az Azure AD Connect szinkronizálása: technikai kulcsfogalmak](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect szinkronizálás: Az architektúra ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect szinkronizálás: A deklaratív kiépítés ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect szinkronizálás: A deklaratív létesítési kifejezések ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect szinkronizálás: A felhasználók, csoportok és kapcsolattartók ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect szinkronizálás: Árnyékattribútumok](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md).
- [Mi a hibrid identitás?](whatis-hybrid-identity.md)
