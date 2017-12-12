---
title: "Kezelheti a hozzáférést és engedélyeket az RBAC - Azure RBAC |} Microsoft Docs"
description: "Ismerkedés a hozzáférés-kezelés az Azure szerepköralapú hozzáférés-vezérlés az Azure portálon. Szerepkör-hozzárendelésekkel adhat meg engedélyeket a címtárában."
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 7df1ebcc65998a2078f5f215277eef351e48ca1b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései
A vállalat biztonsági célú van szükségük a pontos engedélyeket ad az alkalmazottak kell összpontosítania. Túl sok engedélyeket is elérhetővé teheti a támadásokkal fiókkal. Túl kevés engedélyek, az azt jelenti, hogy az alkalmazottak nem munkavégzéséhez hatékony. Azure szerepköralapú hozzáférés-vezérlés (RBAC) segít a részletes hozzáféréskezelést az Azure felajánlásával oldja meg a problémát.

Az RBAC használata, feladatokat elkülönítse a munkacsoporton belül, és csak olyan mértékű hozzáférést biztosítania a felhasználóknak a feladataik elvégzéséhez szükséges. Jogosultságot ad a Mindenki helyett korlátozás engedélyek az Ön Azure-előfizetés vagy erőforrásokhoz, engedélyezheti a csak bizonyos műveleteket. Például, hogy egy előfizetésben található virtuális gépek kezeléséhez, miközben egy másik kezelheti az SQL-adatbázisok egyazon előfizetésen belül egy alkalmazott RBAC használatát.

## <a name="basics-of-access-management-in-azure"></a>Hozzáférés-kezelés az Azure-ban alapjai
Minden Azure-előfizetés tartozik egy Azure Active Directory (AD) címtárban. Felhasználók, csoportok és alkalmazások tartalmazza a kezelhetik az erőforrásokat az Azure-előfizetésben. Rendelje hozzá a következő hozzáférési jogokat az Azure portál, az Azure parancssori eszközök és az Azure szolgáltatásfelügyeleti API használatával.

Adjon hozzáférést a megfelelő RBAC szerepkört rendel a felhasználók, csoportok és alkalmazások egy adott hatókörben. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást. A szülő hatókörben szerepkörrel is benne a gyermekek számára engedélyezi a hozzáférést. Például egy felhasználó, aki hozzáfér az erőforráscsoporthoz az összes erőforrást tartalmaz, például webhelyekhez, virtuális gépek és alhálózatok kezelheti.

![Kapcsolat közötti elemek Azure Active Directory - ábra](./media/role-based-access-control-what-is/rbac_aad.png)

Az RBAC szerepkörhöz hozzárendelt határozzák meg, hogy a felhasználó, csoport vagy alkalmazás felügyelheti, hogy a hatókörön belüli erőforrásokat.

## <a name="built-in-roles"></a>Beépített szerepkörök
Az Azure RBAC rendelkezik, amelyek érvényesek az összes erőforrástípus három alapvető szerepkörök:

* **Tulajdonos** minden erőforrást, beleértve a jogot, hogy mások számára delegálása teljes hozzáféréssel rendelkezik.
* **A közreműködői** is létrehozása és kezelése az Azure-erőforrások minden típusú, de nem tud hozzáférést biztosítani, mások számára.
* **Olvasó** megtekintheti a meglévő Azure-erőforrások.

A többi Azure RBAC szerepkört az adott Azure-erőforrások felügyelet lehetővé tételéhez. Például a virtuális gép közreműködői szerepkör lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozására és kezelésére. Azt nem betekintést biztosít a virtuális hálózat vagy az alhálózatot, amely a virtuális géphez csatlakozik. 

[Beépített RBAC-szerepkörök](role-based-access-built-in-roles.md) felsorolja azokat a szerepköröket, az Azure-ban érhető el. Azt adja meg az operatív és hatókörhöz, amely minden beépített szerepkörök a felhasználók számára. Ha még nagyobb mértékben vezérelheti a saját szerepköröket definiál, lásd: hogyan hozhat létre [egyéni szerepkörök az Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Erőforrás-hierarchiát és a hozzáférés öröklési
* Minden egyes **előfizetés** csak egy címtárban az Azure-ban tartozik. (De minden directory rendelkezhet egynél több előfizetésnek.)
* Minden egyes **erőforráscsoport** csak egy előfizetés tartozik.
* Minden egyes **erőforrás** csak egy erőforráscsoporthoz tartozik.

A gyermekhatókör örökölt, hogy a szülő hatókörök számára biztosítson hozzáférést. Példa:

* Az olvasó szerepkört rendelni az előfizetési hatókört, az Azure Active Directory-csoportnak. A csoport tagjai megtekintheti minden erőforrás csoport- és az előfizetés.
* A közreműködői szerepkör hozzárendelése az erőforrás-csoport hatóköre az alkalmazást. Az erőforráscsoport, de nem egyéb erőforráscsoportok az előfizetés bármilyen típusú erőforrások kezelésére alkalmas.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Az Azure RBAC és a hagyományos előfizetés rendszergazdái
Hagyományos előfizetés rendszergazdák és a társadminisztrátoroknak az Azure-előfizetés teljes hozzáféréssel rendelkezzen. -Erőforrások kezelésére a [Azure-portálon](https://portal.azure.com) Azure Resource Manager API-khoz, vagy a [a klasszikus Azure portálon](https://manage.windowsazure.com) és az Azure klasszikus üzembe helyezési modellben. Az RBAC-modellben a hagyományos adminisztrátorait a tulajdonosi szerepkört az előfizetés hatókörben vannak társítva.

Csak az Azure portál és az új Azure Resource Manager API-kat támogatja az Azure RBAC. Felhasználók és az alkalmazások rendelt RBAC-szerepkörök nem használható a klasszikus felügyeleti portál és az Azure klasszikus üzembe helyezési modellben.

## <a name="authorization-for-management-vs-data-operations"></a>Engedélyezési és adatok az operations Management
Az Azure RBAC az Azure-erőforrások felügyeleti műveletek csak támogatja az Azure-portál és az Azure Resource Manager API-k. Nem lehet engedélyezni, az összes adat szintű műveletet Azure-erőforrások. Például engedélyezheti valaki Storage-fiókok kezeléséhez, de nem a blobok vagy a táblák egy Tárfiókon belül. Hasonlóképpen SQL-adatbázis kezelése, de nem szereplő táblák.

## <a name="next-steps"></a>Következő lépések
* Ismerkedés a [szerepköralapú hozzáférés-vezérlés az Azure portálon](role-based-access-control-configure.md).
* Lásd: [Beépített RBAC-szerepkörök](role-based-access-built-in-roles.md)
* Saját [egyéni szerepkörök az Azure RBAC-ben](role-based-access-control-custom-roles.md)
