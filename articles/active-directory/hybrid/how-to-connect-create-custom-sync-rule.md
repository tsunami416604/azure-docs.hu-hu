---
title: Az Azure AD Connect szinkronizálási szabály testreszabása |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure AD Connect telepítésével kapcsolatos problémák elhárítása.
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
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172433"
---
# <a name="how-to-customize-a-synchronization-rule"></a>A szinkronizálási szabály testreszabása

## <a name="recommended-steps"></a>**Javasolt lépések**

A szinkronizálási Szabályszerkesztő segítségével szerkesztheti, vagy hozzon létre egy új szinkronizálási szabályt. Meg kell lennie a módosításokat a szinkronizálási szabályokon tapasztalt felhasználó. Megfelelő módosításokat a cél-címtár objektumait törlését eredményezheti. Kérjük, olvassa el [ajánlott dokumentumok](#recommended-documents) próbál a jeggyel szakértelmet szinkronizálási szabályait. Módosíthatja egy szinkronizálási szabály hajtania az alábbi lépéseket:

* Indítsa el a szinkronizálási szerkesztő, az alkalmazás menüből a desktop alább látható módon:

    ![Szinkronizálási Szabályszerkesztő menü](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Annak érdekében, hogy testre szabhatja a alapértelmezett szinkronizálási szabályt, klónozza a szinkronizálási Szabályszerkesztővel, amely másolatot készít a szabványos alapértelmezett szabályt, és tiltsa le a "Szerkesztés" gombra kattintva a meglévő szabályt. 100-nál kisebb prioritással a klónozott szabály mentéséhez.  Sorrend határozza meg, milyen szabály wins (numerikus érték kisebb) egy ütközésének feloldása egy attribútum folyamat ütközés esetén.

    ![Synchronization Rule Editor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Egy adott attribútum módosításakor ideális esetben érdemes csak megtartani a módosítása attribútum a klónozott szabályban.  Az alapértelmezett szabály engedélyezze, hogy a módosított attribútum származnak klónozott szabály és egyéb attribútumok útvonalról alapértelmezett standard szabályból. 

* Vegye figyelembe, hogy abban az esetben, ahol a számított érték a módosított attribútum a klónozott szabályban má hodnotu NULL, és nem NULL, a szabványos alapértelmezett szabály, majd a not NULL értékkel legyőzi, és lecseréli a NULL érték. Ha nem szeretné, hogy a rendszer NULL értéket cserélje le egy nem NULL értéket, majd a klónozott szabályban AuthoritativeNull hozzárendelése.

* Módosíthatja egy **kimenő** szabály, a szinkronizálási Szabályszerkesztő a szűrő módosítása.

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
