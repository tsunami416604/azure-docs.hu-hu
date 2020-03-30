---
title: Mi az identitás-kiépítés az Azure AD-vel? | Microsoft Docs
description: Az identitáskiépítés áttekintése.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76712551"
---
# <a name="what-is-identity-provisioning"></a>Mi az az identitáslétesítés?

Napjainkban a vállalkozások és a vállalatok egyre inkább a helyszíni és a felhőalapú alkalmazások keverékévé válnak.  A felhasználóknak mind a helyszíni, mind a felhőbeli alkalmazásokhoz kell hozzáférnie. Szükség van egy identitás a különböző alkalmazások (helyszíni és felhőalapú) egyetlen identitását.

A kiépítés az a folyamat, amelynek során bizonyos feltételek alapján létrehoznak egy objektumot, naprakészen tartják az objektumot, és akkor is törlésre kerülnek, ha a feltételek már nem teljesülnek. Ha például egy új felhasználó csatlakozik a szervezethez, akkor az adott felhasználó bekerül a HR-rendszerbe.  Ezen a ponton kiépítése hozhat létre egy megfelelő felhasználói fiókot a felhőben, az Active Directoryban, és a különböző alkalmazások, amelyek a felhasználónak szüksége van a hozzáférésre.  Ez lehetővé teszi a felhasználó számára, hogy elkezdje a munkát, és hozzáférjen a szükséges alkalmazásokhoz és rendszerekhez az első napon. 

![felhőkiépítés](media/what-is-provisioning/cloud1.png)

Az Azure Active Directory tekintetében a kiépítés a következő kulcsfontosságú forgatókönyvek rebontható.  

- **[HR-alapú kiépítés](#hr-driven-provisioning)**  
- **[Alkalmazáskiépítés](#app-provisioning)**  
- **[Címtár kiépítése](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-alapú kiépítés

![felhőkiépítés](media/what-is-provisioning/cloud2.png)

A HR-ről a felhőbe való kiépítés magában foglalja az objektumok (felhasználók, szerepkörök, csoportok stb.) létrehozását a HR-rendszerben lévő információk alapján.  

A leggyakoribb forgatókönyv az lenne, ha egy új alkalmazott csatlakozik a vállalathoz, akkor bekerül a HR-rendszerbe.  Ha ez megtörténik, ki vannak építve a felhőbe.  Ebben az esetben az Azure AD.  A HR-ből való kiépítés a következő forgatókönyveket fedheti le. 

- **Új alkalmazottak alkalmazása** – Amikor új alkalmazottat ad hozzá a felhőhr-hez, a rendszer automatikusan létrehoz egy felhasználói fiókot az Active Directoryban, az Azure Active Directoryban, és adott esetben az Office 365-ben és az Azure AD által támogatott egyéb SaaS-alkalmazásokban, az e-mail cím felhőbeli HR-be való visszaírásával.
- **Alkalmazotti attribútum- és profilfrissítések** – Amikor egy alkalmazotti rekord frissül a felhőhr-ben (például a nevükben, a címükben vagy a kezelőjükben), a felhasználói fiókjuk automatikusan frissül az Active Directoryban, az Azure Active Directoryban, és adott esetben az Office 365-ben és az Azure AD által támogatott egyéb SaaS-alkalmazásokban.
- **Az alkalmazottak megszűnése** – Ha egy alkalmazott megszűnik a felhőHR-ben, a felhasználói fiók automatikusan le van tiltva az Active Directoryban, az Azure Active Directoryban, és opcionálisan az Office 365-ben és az Azure AD által támogatott más SaaS-alkalmazásokban.
- **Alkalmazotti újrahires** – Ha egy alkalmazott at újra felvett a felhő HR, a régi fiók automatikusan újra aktiválható, vagy újra kiépített (attól függően, hogy a preferencia) az Active Directory, az Azure Active Directory, és szükség esetén az Office 365 és más SaaS-alkalmazások által támogatott Azure AD.


## <a name="app-provisioning"></a>Alkalmazáskiépítés

![felhőkiépítés](media/what-is-provisioning/cloud3.png)

Az Azure Active Directoryban (Azure AD) az **[alkalmazáskiépítés](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** kifejezés a felhasználói identitások és szerepkörök automatikus létrehozására utal a felhőalapú alkalmazásokban, amelyekhez a felhasználóknak hozzáférésre van szükségük. A felhasználói identitások létrehozása mellett az automatikus kiépítés magában foglalja a felhasználói identitások karbantartását és eltávolítását az állapot vagy a szerepkörök változásakor. Gyakori forgatókönyvek közé tartozik egy Azure AD-felhasználó kiépítése olyan alkalmazásokba, mint a [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) [a Salesforce,](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial) [a ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)és egyebek.

## <a name="directory-provisioning"></a>Címtár kiépítése

![felhőkiépítés](media/what-is-provisioning/cloud4.png)

A helyszíni kiépítés magában foglalja a helyszíni forrásokból (például az Active Directoryból) az Azure AD-be való kiépítést.  

A leggyakoribb forgatókönyv az lenne, ha egy felhasználó az Active Directoryban (AD) ki van építve az Azure AD.The common scenario would be, when a user in Active Directory (AD) is provisioned into Azure AD.

Ezt az Azure AD Connect szinkronizálás, az Azure AD Connect felhőalapú kiépítés és a Microsoft Identity Manager valósította meg. 
 
## <a name="next-steps"></a>További lépések 

- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
- [Felhőkiépítés telepítése](how-to-install.md)
