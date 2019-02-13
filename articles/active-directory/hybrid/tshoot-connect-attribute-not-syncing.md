---
title: Az Azure AD Connect nem szinkronizálja a attribútum hibaelhárítása |} A Microsoft Docs
description: Ez a témakör a hibaelhárítási feladat használatával attribútum-szinkronizálás hibáinak elhárítása a lépéseit ismerteti.
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
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180825"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Az Azure AD Connect nem szinkronizálja a attribútum hibaelhárítása

## <a name="recommended-steps"></a>**Javasolt lépések**

Mielőtt attribútum szinkronizálásával kapcsolatos problémák kivizsgálása, tekintsük át, a **az Azure AD Connect** szinkronizálása folyamatban:

  ![Az Azure AD Connect szinkronizálási folyamat](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminológia**

* **CS:** Összekötő-térben database egyik táblájába.
* **MV:** Metaverzum, database egyik táblájába.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Szinkronizálásának lépései**

* Importálás az AD-ből: Active Directory-objektumok az Active Directory Tanúsítványszolgáltatások való importálás.

* Importálás az aad-ből: Az Azure Active Directory-objektumokat hozza be AAD-CS.

* Szinkronizálás: **Bejövő szinkronizálási szabályok** és **kimenő szinkronizálási szabály** futnak sorrendjében prioritása kisebb, magasabb. Tekintse meg a szinkronizálási szabályokat, nyissa meg a **szinkronizálási Szabályszerkesztővel** az asztali alkalmazásokétól. A **bejövő szinkronizálási szabályok** adatok biztosítható a CS MV az. A **kimenő szinkronizálási szabály** helyez át adatokat MV CS.

* Exportálása az ad-hez: Után futtatnia kell a szinkronizálást, objektumok exportálása az AD CS **Active Directory**.

* Exportálás az aad-be: Az AAD-CS programból exportált objektumokat után futtatnia kell a szinkronizálást, **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Részletes vizsgálat**

* Megkezdjük a keresést, hogy a **Metaverzum** , és tekintse meg a attribútum leképezés a cél forrásból.

* Indítsa el a **Synchronization Service Managert** az asztali alkalmazásokétól alább látható módon:

  ![Indítsa el a Synchronization Service Managert](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Az a **Synchronization Service Managert**, jelölje be a **keresés a Metaverzumban**válassza **objektumtípus szerint hatókör**, jelölje ki az objektumot egy attribútum használatával, majd kattintson a **Keresési** gombra.

  ![Keresés a Metaverzumban](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Kattintson duplán a található az objektum a **Metaverzum** keresés az összes hozzá tartozó attribútumok megtekintéséhez. Kattintson a a **összekötők** fülre, és tekintse meg az összes kapcsolódó objektumot a **Összekötőterek**.

  ![Összekötő Metaverzum-objektum](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Kattintson duplán a **Active Directory-összekötő** megtekintéséhez a **Összekötőterében** attribútumok. Kattintson a a **előzetes** , a következő párbeszédpanelen kattintson a gombra a **készítése előzetes** gombra.

  ![Összekötő terület attribútumok](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Ezután kattintson a a **importálási Attribútumfolyam**, ez jelzi, hogy az attribútumok folyamat **Active Directory Összekötőterében** , a **Metaverzum**. **A szabály szinkronizálása** az oszlopban látható, amely **szinkronizálási szabály** járult hozzá ezt az attribútumot. **Az adatforrás** az oszlopban látható, hogy az attribútumok alapján a **Összekötőterében**. **Metaverzum-attribútum** az oszlopban látható, hogy az attribútumok a **Metaverzum**. Az attribútum nem szinkronizálja az itt megkeresheti. Ha nem találja itt, az attribútumot, akkor ez nincs hozzárendelve, és létre kell hoznia az új egyéni **szinkronizálási szabály** való leképezéséhez az attribútum.

  ![Összekötő terület attribútumok](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kattintson a a **Attribútumfolyam exportálása** az Attribútumfolyam a megtekintéséhez a bal oldali panelen **Metaverzum** vissza **Active Directory Összekötőterében** használatával  **Kimenő szinkronizálási szabály**.

  ![Összekötő terület attribútumok](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Ehhez hasonlóan megtekintheti a **Azure Active Directory Összekötőterében** objektumra, és hozhat létre a **előzetes** megtekintéséhez az Attribútumfolyam **Metaverzum** , a **Összekötőterében** és fordítva, ezzel a módszerrel segítségével megvizsgálhatja, hogy miért nem szinkronizálja a attribútum.

## <a name="recommended-documents"></a>**Ajánlott dokumentumok**
* [Az Azure AD Connect szinkronizálása: Műszaki fogalmak](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Az Azure AD Connect szinkronizálása: Az architektúra ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Az Azure AD Connect szinkronizálása: A deklaratív üzembe helyezés ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Az Azure AD Connect szinkronizálása: A deklaratív üzembehelyezési kifejezések ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Az Azure AD Connect szinkronizálása: Az alapértelmezett konfiguráció ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Az Azure AD Connect szinkronizálása: A felhasználók, csoportok és kapcsolatok ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Az Azure AD Connect szinkronizálása: Árnyékattribútumok](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>További lépések

- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md).
- [Mi a hibrid identitás? ](whatis-hybrid-identity.md).
