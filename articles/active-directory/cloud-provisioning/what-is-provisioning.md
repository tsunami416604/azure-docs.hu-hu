---
title: Mi az az identitás kiépítés az Azure AD-vel? | Microsoft Docs
description: Az identitás kiépítés áttekintését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28513c57101af67695d10056b3dc8e6537dcddb2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712551"
---
# <a name="what-is-identity-provisioning"></a>Mi az az identitáslétesítés?

Napjainkban a vállalatok és a vállalatok egyre többen használják a helyszíni és a felhőalapú alkalmazásokat.  A felhasználóknak a helyszíni és a Felhőbeli alkalmazásokhoz is hozzá kell férniük. A különböző alkalmazásokban (a helyszínen és a felhőben) egyetlen identitással kell rendelkeznie.

A kiépítés során a rendszer bizonyos feltételek alapján hozza létre az objektumot, és az objektumot naprakészen tartja, és törli az objektumot, amikor a feltételek már nem teljesülnek. Ha például egy új felhasználó csatlakozik a szervezethez, a felhasználó bekerül a HR rendszerbe.  Ezen a ponton a kiépítés létrehozhat egy megfelelő felhasználói fiókot a felhőben, Active Directoryban és különböző alkalmazásokban, amelyekhez a felhasználónak hozzá kell férnie.  Ez lehetővé teszi a felhasználó számára, hogy megkezdje a munkát, és hozzáférhessen az első napon szükséges alkalmazásokhoz és rendszerekhez. 

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

Azure Active Directory (Azure AD) esetében az **[alkalmazás üzembe](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** helyezésének kifejezése arra a felhasználói identitások és szerepkörök automatikus létrehozására utal, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés a felhasználói identitások karbantartását és eltávolítását is magában foglalja az állapot vagy a szerepkörök módosításakor. Gyakori forgatókönyvek például az Azure AD-felhasználók üzembe helyezése olyan alkalmazásokban, mint a [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), a [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), a [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)és még sok más.

## <a name="directory-provisioning"></a>Címtár kiépítés

![Felhőbeli kiépítés](media/what-is-provisioning/cloud4.png)

A helyszíni kiépítés magában foglalja a helyszíni forrásokból (például Active Directory) az Azure AD-be történő kiépítést.  

A leggyakoribb forgatókönyv a következő: Ha Active Directory (AD) felhasználója az Azure AD-ben van kiépítve.

Ezt a Azure AD Connect Sync, Azure AD Connect a felhőalapú kiépítés és a Microsoft Identity Manager használatával hajtották végre. 
 
## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
- [A felhő kiépítés telepítése](how-to-install.md)
