---
title: Szinkronizálási szabály testreszabása az Azure AD Connectben | Microsoft Dokumentumok"
description: Ez a témakör az Azure AD Connect telepítésével kapcsolatos problémák elhárításának lépéseit ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351068"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Szinkronizálási szabály testre szabása

## <a name="recommended-steps"></a>**Ajánlott lépések**

A szinkronizálási szabály szerkesztőjével szerkesztheti vagy létrehozhat egy új szinkronizálási szabályt. A szinkronizálási szabályok módosításához tapasztalt felhasználónak kell lennie. Bármilyen helytelen módosítás az objektumok törlését eredményezheti a célkönyvtárból. Kérjük, olvassa el [az Ajánlott dokumentumok elolvasását,](#recommended-documents) hogy szakértelmet szerezzen a szinkronizálási szabályokterén. A szinkronizálási szabályok módosításához hajtsa végre a következő lépéseket:

* Indítsa el a szinkronizálási szerkesztőt az asztali alkalmazás menüből az alábbi módon:

    ![Szinkronizálási szabályszerkesztő menü](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Az alapértelmezett szinkronizálási szabály testreszabásához klónozza a meglévő szabályt a Szinkronizálási szabályok szerkesztőjének "Szerkesztés" gombjára kattintva, amely létrehozza a szabványos alapértelmezett szabály másolatát, és letiltja azt. Mentse a klónozott szabályt 100-nál kisebb prioritással.  A prioritás határozza meg, hogy melyik szabály nyer (alacsonyabb numerikus érték) ütközésfeloldást, ha attribútumfolyamat-ütközés van.

    ![Szinkronizálási szabály szerkesztője](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Egy adott attribútum módosításakor ideális esetben csak a klónozott szabályban tartsa meg a módosító attribútumot.  Ezután engedélyezze az alapértelmezett szabályt, hogy a módosított attribútum klónozott szabályból származzon, és más attribútumokat az alapértelmezett normál szabályból vegye ki. 

* Kérjük, vegye figyelembe, hogy abban az esetben, ha a módosított attribútum számított értéke NULL a klónozott szabályban, és nem NULL az alapértelmezett általános szabályban, akkor a nem NULL érték nyer, és felülírja a NULL értéket. Ha nem szeretné, hogy a NULL értéket nem NULL értékkel helyettesítse, akkor rendelje hozzá a MérvativeNull értéket a klónozott szabályban.

* **Kimenő** szabály módosításához módosítsa a szűrőt a szinkronizálási szabály szerkesztőjéből.

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
