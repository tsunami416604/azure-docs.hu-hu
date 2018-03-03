---
title: "Azure verem identitás-architektúra |} Microsoft Docs"
description: "Ismerje meg az identitás-architektúra Azure verem használható."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/28/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 7f2ec78da38f3c97fde810fb8fc965cfbb6fda08
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Azure verem identitás-architektúra
Az identitásszolgáltató Azure verem használata mellett dönt, mielőtt megértése fontos különbség a beállításokat az Azure Active Directory (Azure AD) és Active Directory összevont szolgáltatások (AD FS) között. 

## <a name="capabilities-and-limitations"></a>Képességei és korlátozásai 
Az identitásszolgáltató úgy dönt, korlátozhatja a beállításokat, beleértve a több-bérlős támogatását. 

  

|Funkció vagy forgatókönyv        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Az internethez csatlakozó     |Igen       |Optional|
|Több vállalat kiszolgálása támogatása     |Igen       |Nem      |
|Piactér szindikálási       |Igen       |Igen – használatát igényli a [offline piactér szindikálási](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) eszköz.|
|Az Active Directory Authentication Library (ADAL) támogatása |Igen |Igen|
|Például az Azure parancssori felület (CLI), a Visual Studio (VS) és a PowerShell támogatása  |Igen |Igen|
|A portálon keresztül szolgáltatásnevekről létrehozása     |Igen |Nem|
|Hozzon létre szolgáltatásnevekről tanúsítványok      |Igen |Igen|
|Hozzon létre szolgáltatásnevekről titkok (kulcsok)    |Igen |Nem|
|Alkalmazások használhatják a Graph-szolgáltatás           |Igen |Nem|
|Alkalmazások identitásszolgáltató használhatják a bejelentkezéshez |Igen |Igen – igényel az alkalmazások összevonni a helyszíni Active Directory összevonási szolgáltatások. |

## <a name="topologies"></a>Topológiák
Az alábbi szakaszok ismertetik a identitás topológiákat használhatja.

### <a name="azure-ad--single-tenant"></a>Az Azure AD – egyetlen-bérlő 
Alapértelmezés szerint telepítse az Azure-vermet, és használhatja az Azure Active Directory, Azure verem használja a single-bérlő topológia. 

A single-bérlő topológia akkor hasznos, ha:
- Minden felhasználó ugyanannak a bérlőnek a részét képezik.
- A szolgáltató egy szervezet Azure verem példánya üzemelteti.  

![Az Azure AD egy egybérlős topológia használatával Azure verem topológia](media/azure-stack-identity-architecture/single-tenant.png)

Ez a topológia:
- Azure verem regisztrálja összes alkalmazást, és ugyanazt az Azure AD-szolgáltatások bérlői könyvtár. 
- Az Azure verem csak a felhasználók és az alkalmazások a könyvtárhoz, beleértve a jogkivonatok hitelesíti. 
- Rendszergazdák (a felhő üzemeltetői) és a bérlői felhasználók identitásainak directory ugyanannak a bérlőnek szerepelnek. 
- Ahhoz, hogy egy felhasználó egy másik címtárból az Azure-verem környezet elérésére, kell [hívhat meg vendégként felhasználói](azure-stack-identity-overview.md#guest-users) számára a bérlő címtárát.  

### <a name="azure-ad--multi-tenant"></a>Az Azure AD – több-bérlős
A felhő üzemeltetői konfigurálhatja az Azure veremben egy vagy több szervezet bérlők alkalmazások hozzáférést. Felhasználók érnek el alkalmazásokat a felhasználói portálon keresztül. Ebben a konfigurációban a felügyeleti portál (a felhő üzemeltetője által használt) korlátozva a felhasználók számára olyan egyetlen könyvtárból. 

A több-bérlős topológia akkor hasznos, ha:
- A szolgáltató szeretné engedélyezni az Azure verem eléréséhez több szervezet felhasználóit.

![Az Azure AD egy több-bérlős topológia használatával Azure verem topológia](media/azure-stack-identity-architecture/multi-tenant.png)

Ez a topológia:
- Erőforrásokhoz való hozzáférés szervezet szinten kell lennie. 
- Az egyik szervezetből felhasználók nem lehet tudni a felhasználók számára a szervezetén kívüli erőforrásokhoz való hozzáférést.  
- A rendszergazdák (a felhő üzemeltetői) identitások egy külön directory-bérlőt, mint a felhasználók identitásainak lehet. Ez az elkülönítés az identity provider szintjén fiók elkülönítést is biztosít. 
 
### <a name="ad-fs"></a>AD FS  
Az AD FS topológia megadása kötelező, ha a következő feltételek valamelyike teljesül:
- Az Azure verem nem csatlakozik az internethez.
- Az Azure verem csatlakozni tud-e az internethez, de az identitásszolgáltató az Active Directory összevonási szolgáltatások használatát választja.
  
![AD FS segítségével Azure verem topológia](media/azure-stack-identity-architecture/adfs.png)

Ez a topológia:
- Támogatása, éles környezetben a beépített Azure verem AD FS-példányt kell integrálható egy meglévő AD FS-példányt, biztonsági által az Active Directory (AD), egy összevonási megbízhatósági kapcsolat keresztül. 
- A Graph szolgáltatás Azure verem integrálható a meglévő AD.  Használhatja az OData-alapú API-konzisztensek az Azure AD Graph API-t támogató Graph API-szolgáltatás.  

  Az AD kommunikál, a Graph API felhasználói hitelesítő adatok szükségesek az AD-ből, amely rendelkezik olvasási engedéllyel az AD. 
  - A beépített Active Directory összevonási szolgáltatások Server 2016 alapul. 
  - Az AD FS és az AD-szabványra kell épülniük Server 2012 vagy újabb.  
  
  Az AD és a beépített AD FS kapcsolati nem korlátozza az OpenID Connect, valamint bármely kölcsönösen támogatott protokoll használható.  
  - Felhasználói fiókok létrehozása és kezelése a helyszíni AD.
  - A beépített AD szolgáltatásnevekről és a regisztrációk alkalmazások felügyelete.



## <a name="next-steps"></a>További lépések
- [Identitás áttekintése](azure-stack-identity-overview.md)   
- [Adatközpont-integráció - identitás](azure-stack-integrate-identity.md)