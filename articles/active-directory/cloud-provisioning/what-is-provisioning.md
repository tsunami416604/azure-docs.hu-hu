---
title: Mi az az identitás kiépítés az Azure AD-vel? | Microsoft Docs
description: Az identitás kiépítés áttekintését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acde7f30649e03d44fd891c959b53113b0e46f51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793513"
---
# <a name="what-is-identity-provisioning"></a>Mi az identitás kiépítés?

Napjainkban a vállalatok és a vállalatok egyre többen használják a helyszíni és a felhőalapú alkalmazásokat.  A felhasználóknak a helyszíni és a Felhőbeli alkalmazásokhoz is hozzá kell férniük. A különböző alkalmazásokban (a helyszínen és a felhőben) egyetlen identitással kell rendelkeznie.

A kiépítés során a rendszer bizonyos feltételek alapján hozza létre az objektumot, megtartja az objektum előállítási dátumát és törli az objektumot, amikor a feltételek már nem teljesülnek. Ha például egy új felhasználó csatlakozik a szervezethez, a felhasználó bekerül a HR rendszerbe.  Ezen a ponton a kiépítés létrehozhat egy megfelelő felhasználói fiókot a felhőben, Active Directoryban és különböző alkalmazásokban, amelyekhez a felhasználónak hozzá kell férnie.  Ez lehetővé teszi a felhasználó számára, hogy megkezdje a munkát, és hozzáférhessen az első napon szükséges alkalmazásokhoz és rendszerekhez. 

![Felhőbeli kiépítés](media/what-is-provisioning/cloud1.png)

Azure Active Directory tekintetében a kiépítés a következő főbb forgatókönyvek szerint bontható.  

- **[HR-vezérelt kiépítés](#hr-driven-provisioning)**  
- **[Alkalmazás kiépítés](#app-provisioning)**  
- **[Címtár kiépítés](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-vezérelt kiépítés

![Felhőbeli kiépítés](media/what-is-provisioning/cloud2.png)

A HR-ből a felhőbe való kiépítés magában foglalja az adott HR-rendszeren található információk alapján objektumok (felhasználók, szerepkörök, csoportok stb.) létrehozását.  

A leggyakoribb forgatókönyv a következő: amikor egy új alkalmazott csatlakozik a vállalathoz, azok bekerülnek a HR-rendszerbe.  Ha ez bekövetkezik, a rendszer kiépíti a felhőbe.  Ebben az esetben az Azure AD-t.  A HR-ből való kiépítés a következő helyzetekben lehetséges. 

- **Új alkalmazottak felvétele** – ha új alkalmazottat ad hozzá a Cloud HR-hez, a rendszer automatikusan létrehoz egy felhasználói fiókot Active Directory, Azure Active Directory és opcionálisan az Office 365-ben és az Azure ad által támogatott egyéb SaaS-alkalmazásokban, az e-mail-cím a Cloud HR-re való visszaírásával.
- **Alkalmazotti attribútumok és a profil frissítései** – ha egy alkalmazotti rekord frissül a Felhőbeli HR-ben (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan frissül Active Directory, Azure Active Directory és opcionálisan az Office 365-ben és az Azure ad által támogatott egyéb SaaS-alkalmazásokban is.
- **Alkalmazotti megszakítások** – ha egy alkalmazott leáll a Cloud HR-ben, a felhasználói fiókja automatikusan le van tiltva Active Directory, Azure Active Directory és opcionálisan az Office 365 és az Azure ad által támogatott egyéb SaaS-alkalmazások esetében.
- **Alkalmazottak** újratelepítése – ha egy alkalmazottat a Felhőbeli HR-ben helyeztek üzembe, a régi fiók automatikusan újraaktiválható vagy újraépíthető (a beállítástól függően), hogy Active Directory, Azure Active Directory, és opcionálisan az Office 365 és az Azure ad által támogatott egyéb SaaS-alkalmazásokat.


## <a name="app-provisioning"></a>Alkalmazás kiépítés

![Felhőbeli kiépítés](media/what-is-provisioning/cloud3.png)

Az alkalmazások kiosztása magában foglalja a felhasználók és szerepkörök kiosztását azon alkalmazásokban, amelyekhez a felhasználónak hozzáférésre van szüksége.  

A leggyakoribb forgatókönyv a következő: Ha az Azure AD-ben lévő felhasználó O365 vagy Salesforce van kiépítve.

## <a name="directory-provisioning"></a>Címtár kiépítés

![Felhőbeli kiépítés](media/what-is-provisioning/cloud4.png)

A helyszíni kiépítés magában foglalja a helyszíni forrásokból (például Active Directory) az Azure AD-be történő kiépítést.  

A leggyakoribb forgatókönyv a következő: Ha Active Directory (AD) felhasználója az Azure AD-ben van kiépítve.

Ezt a Azure AD Connect Sync, Azure AD Connect a felhőalapú kiépítés és a Microsoft Identity Manager használatával hajtották végre. 
 
## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
