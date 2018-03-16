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
ms.openlocfilehash: 899e0fc0c1eb93d68c79c92c9cc042462ebc2fef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Azure verem identitás-architektúra
Mielőtt az identitásszolgáltató Azure verem használata mellett dönt, a beállításokat az Azure Active Directory (Azure AD) és Active Directory összevonási szolgáltatások (AD FS) közötti fontos különbségek megismeréséhez. 

## <a name="capabilities-and-limitations"></a>Képességei és korlátozásai 
Az identitásszolgáltató az Ön által korlátozhatja a beállításokat, beleértve a több-bérlős támogatását. 

  

|Funkció vagy forgatókönyv        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Az internethez csatlakozó     |Igen       |Optional|
|Több vállalat kiszolgálása támogatása     |Igen       |Nem      |
|Piactér szindikálási       |Igen       |Igen. Használatát igényli a [offline piactér szindikálási](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) eszköz.|
|Az Active Directory Authentication Library (ADAL) támogatása |Igen |Igen|
|Eszközök, például az Azure parancssori felület, a Visual Studio és a PowerShell támogatása  |Igen |Igen|
|Az Azure portálon keresztül szolgáltatásnevekről létrehozása     |Igen |Nem|
|Hozzon létre szolgáltatásnevekről tanúsítványok      |Igen |Igen|
|Hozzon létre szolgáltatásnevekről titkok (kulcsok)    |Igen |Nem|
|Alkalmazások használhatják a Graph-szolgáltatás           |Igen |Nem|
|Alkalmazások identitásszolgáltató használhatják a bejelentkezéshez |Igen |Igen. Szükséges alkalmazások összevonni a helyszíni AD FS-példányt. |

## <a name="topologies"></a>Topológiák
A következő szakaszok discus a különböző identitás topológiák használható.

### <a name="azure-ad-single-tenant-topology"></a>Az Azure AD: single-bérlő topológia 
Alapértelmezés szerint telepítse az Azure-vermet, és használhatja az Azure Active Directory, Azure verem használja a single-bérlő topológia. 

A single-bérlő topológia akkor hasznos, ha:
- Minden felhasználó ugyanannak a bérlőnek a részét képezik.
- A szolgáltató egy szervezet Azure verem példánya üzemelteti. 

![Az Azure ad-val Azure verem single-bérlő topológia](media/azure-stack-identity-architecture/single-tenant.png)

Ez a topológia szolgáltatások a következő jellemzőkkel:
- Azure verem regisztrálja összes alkalmazást, és ugyanazt az Azure AD-szolgáltatások bérlői könyvtár. 
- Az Azure verem csak a felhasználók és az alkalmazások a könyvtárhoz, beleértve a jogkivonatok hitelesíti. 
- Rendszergazdák (a felhő üzemeltetői) és a bérlői felhasználók identitásainak directory ugyanannak a bérlőnek szerepelnek. 
- Ahhoz, hogy egy felhasználó egy másik címtárból az Azure-verem környezet elérésére, kell [hívhat meg vendégként felhasználói](azure-stack-identity-overview.md#guest-users) számára a bérlő címtárát. 

### <a name="azure-ad-multi-tenant-topology"></a>Az Azure AD: több-bérlős topológia
A felhő üzemeltetői konfigurálhatja az Azure veremben egy vagy több szervezet bérlők alkalmazások hozzáférést. Felhasználók érnek el alkalmazásokat a felhasználói portálon keresztül. Ebben a konfigurációban a felügyeleti portál (a felhő üzemeltetője által használt) korlátozva a felhasználók számára olyan egyetlen könyvtárból. 

A több-bérlős topológia akkor hasznos, ha:
- A szolgáltató szeretné engedélyezni az Azure verem eléréséhez több szervezet felhasználóit.

![Az Azure ad-val Azure verem több-bérlős topológia](media/azure-stack-identity-architecture/multi-tenant.png)

Ez a topológia szolgáltatások a következő jellemzőkkel:
- Erőforrásokhoz való hozzáférés szervezet szinten kell lennie. 
- Az egyik szervezetből felhasználók erőforrásokhoz való hozzáférés biztosítása a szervezeten kívüli felhasználók nem kell lennie. 
- A felhasználók identitásainak egy külön directory bérlő rendszergazdák (a felhő üzemeltetői) identitásainak lehet. Ez az elkülönítés az identity provider szintjén fiók elkülönítést is biztosít. 
 
### <a name="ad-fs"></a>AD FS  
Az AD FS topológia megadása kötelező, ha a következő feltételek valamelyike teljesül:
- Az Azure verem nem csatlakozik az internethez.
- Az Azure verem csatlakozni tud-e az internethez, de az identitásszolgáltató az Active Directory összevonási szolgáltatások használatát választja.
  
![AD FS segítségével Azure verem topológia](media/azure-stack-identity-architecture/adfs.png)

Ez a topológia szolgáltatások a következő jellemzőkkel:
- Éles környezetben ez a topológia használatának támogatásához a beépített Azure verem AD FS-példányt kell integrálható egy meglévő AD FS-példányt, amely Active Directory összevonási megbízhatósági kapcsolat keresztül alapját. 
- A Graph szolgáltatás Azure verem integrálható a meglévő Active Directory-példányban. A Graph API OData-alapú szolgáltatás, amely támogatja az API-konzisztensek az Azure AD Graph API-t is használhatja. 

  Az Active Directory-példányban kommunikál, a Graph API felhasználói hitelesítő adatok szükségesek az Active Directory-példányból, amely olvasási engedélyekkel rendelkezik. 
  - A beépített AD FS-példányt a Windows Server 2016 alapul. 
  - Az AD FS és az Active Directory-példányokat a Windows Server 2012 vagy újabb kell alapulniuk. 
  
  Az Active Directory-példányt és a beépített AD FS-példányt, közötti kapcsolati nem OpenID Connect korlátozni, és bármely kölcsönösen támogatott protokollt használhatnak. 
  - Felhasználói fiókok létrehozása és kezelése a helyszíni Active Directory-példányban.
  - A beépített Active Directory-példányban szolgáltatásnevekről és a regisztrációk alkalmazások felügyelete.



## <a name="next-steps"></a>További lépések
- [Identitás áttekintése](azure-stack-identity-overview.md)   
- [Adatközpont-integráció - identitás](azure-stack-integrate-identity.md)
