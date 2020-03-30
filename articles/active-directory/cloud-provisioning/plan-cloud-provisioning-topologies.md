---
title: Az Azure AD Connect felhőalapú kiépítése támogatott topológiák és forgatókönyvek
description: Ez a témakör ismerteti az előfeltételeket és a hardverkövetelmények felhőkiépítés.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620871"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Az Azure AD Connect felhőalapú kiépítése támogatott topológiák és forgatókönyvek
Ez a cikk ismerteti a különböző helyszíni és az Azure Active Directory (Azure AD) topológiák, amelyek az Azure AD Connect felhőkiépítés. Ez a cikk csak a támogatott konfigurációkat és forgatókönyveket tartalmazza.

> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect felhőalapú kiépítésének módosítását vagy üzemeltetését a hivatalosan dokumentált konfigurációkon vagy műveleteken kívül. Ezen konfigurációk vagy műveletek bármelyike az Azure AD Connect felhőkiépítés inkonzisztens vagy nem támogatott állapotát eredményezheti. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Emlékezetes dolgok az összes forgatókönyvről és topológiáról
Az alábbi lista a megoldás kiválasztásakor szem előtt tartandó információkat tartalmazza.

- A felhasználókat és csoportokat egyedileg kell azonosítani az összes erdőben
- Az erdők közötti egyeztetés nem történik meg a felhőalapú kiépítéssel
- Egy felhasználót vagy csoportot csak egyszer kell képviselni az összes erdőben.
- A program automatikusan kiválasztja az objektumok forráshorgonyát.  Ez használ ms-DS-ConsistencyGuid ha jelen van, ellenkező esetben ObjectGUID van használva.
- A forráshorgonyhoz használt attribútum nem módosítható.

## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő és egyetlen bérlő topológiája](media/plan-cloud-provisioning-topologies/single-forest.png)

A legegyszerűbb topológia egy helyszíni erdő, egy vagy több tartománnyal, és egy Azure AD-bérlővel.  Ebben a forgatókönyvben például az [oktatóanyag: Egyetlen erdő egyetlen Azure AD-bérlővel](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Többerdős, egyetlen Azure AD-bérlő
![Topológia több erdőés egyetlen bérlő számára](media/plan-cloud-provisioning-topologies/multi-forest.png)

A közös topológia egy több AD-erdők, egy vagy több tartománnyal, és egy Azure AD-bérlő.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Meglévő erdő az Azure AD Connecttel, új erdő felhőkiépítéssel
![Egyetlen erdő és egyetlen bérlő topológiája](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Ez a forgatókönyv a topológia hasonló a többerdős forgatókönyvhöz, azonban ez egy meglévő Azure AD Connect-környezetet foglal magában, majd egy új erdőt hoz létre az Azure AD Connect felhőalapú kiépítéshasználatával.  Ebben a forgatókönyvben például az [oktatóanyag: Egy meglévő erdő egyetlen Azure AD-bérlővel](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Az Azure AD Connect felhőkiépítés kísérleti projektje egy meglévő hibrid AD-erdőben
![Egy erdő és egy bérlő](media/plan-cloud-provisioning-topologies/migrate.png) topológiája A próba-forgatókönyv magában foglalja az Azure AD Connect és az Azure AD Connect felhőkiépítés létezését ugyanabban az erdőben, és ennek megfelelően a felhasználók és csoportok hatókörét. MEGJEGYZÉS: Egy objektum nak csak az egyik eszközben kell hatókörrel rendelkeznie. 

Ebben a forgatókönyvben például az [oktatóanyag: Kísérleti Azure AD Connect felhőkiépítés egy meglévő szinkronizált AD erdőben](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

