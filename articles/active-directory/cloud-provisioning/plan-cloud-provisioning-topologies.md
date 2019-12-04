---
title: Azure AD Connect felhőalapú üzembe helyezés támogatott topológiák és forgatókönyvek
description: Ez a témakör ismerteti az előfeltételeket és a hardverkövetelmények Felhőbeli üzembe helyezését.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793996"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect felhőalapú üzembe helyezés támogatott topológiák és forgatókönyvek
Ez a cikk a Felhőbeli kiépítést Azure AD Connect használó különböző helyszíni és Azure Active Directory (Azure AD) topológiákat ismerteti. Ez a cikk csak a támogatott konfigurációkat és forgatókönyveket tartalmazza.

> [!IMPORTANT]
> A Microsoft nem támogatja a Azure AD Connect felhőalapú kiépítésének módosítását és működését a hivatalosan dokumentált konfigurációkon vagy műveleteken kívül. Ezen konfigurációk vagy műveletek bármelyike inkonzisztens vagy nem támogatott állapotba ütközhet Azure AD Connect felhőalapú kiépítés során. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Az összes forgatókönyvről és topológiáról megjegyezhető dolgok
A következő lista azokat az információkat tartalmazza, amelyeket érdemes figyelembe venni a megoldás kiválasztásakor.

- A felhasználóknak és a csoportoknak egyedileg azonosíthatóknak kell lenniük az összes erdőben
- A Felhőbeli kiépítés nem történik meg az erdők közötti egyeztetés során
- Egy felhasználót vagy csoportot csak egyszer kell megjeleníteni az összes erdőben
- Az objektumok forrás-horgonya automatikusan kiválasztva.  Ha van, MS-DS-ConsistencyGuid-t használ, máskülönben ObjectGUID van használatban.
- A forrás-horgonyhoz használt attribútum nem módosítható.



## <a name="multi-forest-single-azure-ad-tenant"></a>Több erdős, egyetlen Azure AD-bérlő
![Több erdő és egyetlen bérlő topológiája](media/plan-cloud-provisioning-topologies/multi-forest.png)

A leggyakoribb topológia több AD-erdő, egy vagy több tartománnyal, valamint egyetlen Azure AD-Bérlővel.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Meglévő erdő Azure AD Connect, új erdővel a Felhőbeli kiépítés során
![Egyetlen erdő és egyetlen bérlő topológiája](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Ez a forgatókönyv a többerdős forgatókönyvhöz hasonló, azonban egy meglévő Azure AD Connect-környezettel rendelkezik, majd egy új erdőt hoz létre Azure AD Connect felhőalapú kiépítés használatával.  Példa erre a forgatókönyvre [: oktatóanyag: egy meglévő erdő egyetlen Azure ad-Bérlővel](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Azure AD Connect felhőalapú kiépítés kipróbálása egy meglévő hibrid AD-erdőben
![topológiát egyetlen erdőhöz és egyetlen bérlőhöz](media/plan-cloud-provisioning-topologies/migrate.png) a kísérleti forgatókönyv magában foglalja mind a Azure AD Connect, mind a Azure AD Connect Felhőbeli kiépítés létezését ugyanabban az erdőben, és ennek megfelelően a felhasználók és csoportok hatókörét. Megjegyzés: egy objektumnak csak az egyik eszköz hatókörében kell lennie. 

Példa erre a forgatókönyvre [: oktatóanyag: kísérleti Azure ad Connect felhőalapú kiépítés egy meglévő szinkronizált ad-erdőben](tutorial-pilot-aadc-aadccp.md)

## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő és egyetlen bérlő topológiája](media/plan-cloud-provisioning-topologies/single-forest.png)

A legegyszerűbb topológia egyetlen helyszíni erdő, amely egy vagy több tartománnyal és egyetlen Azure AD-Bérlővel rendelkezik.  Példa erre az esetre [: oktatóanyag: egyetlen erdő egyetlen Azure ad-Bérlővel](tutorial-single-forest.md)

## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)

