---
title: "Mi az a csoport-alapú licencelése az Azure Active Directoryban? | Microsoft Docs"
description: "Azure Active Directory biztonságicsoport-alapú licencelési, hogyan működik és ajánlott eljárások leírása"
services: active-directory
keywords: "Az Azure AD-licencelés"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: d6c76af713a73b965e87a2f470125f2e65565975
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Csoportalapú licencelés alapjai az Azure Active Directoryban

A Microsoft felhőszolgáltatások, például az Office 365, nagyvállalati mobilitási + biztonsági, Dynamics CRM és egyéb hasonló termékek fizetős szükség van a licenceket. A licencek vannak rendelve ezekhez a szolgáltatásokhoz való hozzáférést igénylő összes felhasználónak. A rendszergazdák licencek kezeléséhez használja a felügyeleti portálokat (Office vagy Azure) és a PowerShell-parancsmagok egyikét. Azure Active Directory (Azure AD) az alkalmazás mögötti infrastruktúra, amely támogatja az Identitáskezelés minden Microsoft-szolgáltatásokhoz. Az Azure AD felhasználók hozzárendelés licencállapotukat kapcsolatos információkat tárolja.

Eddig licencek csak rendelhető, amely tesz, a felügyeleti teendők központjaként felügyelet nehéz egyedi felhasználói szinten. Például hozzáadása vagy eltávolítása a felhasználói licencek szervezeti módosítások szempontjából, például a felhasználók csatlakozni vagy a szervezet, illetve egy részleg alapján rendszergazda gyakran összetett PowerShell-parancsfájlt kell írnia. Ezt a parancsfájlt a felhőszolgáltatásba egyes hívások révén.

Ezek a kihívások megoldására mostantól az Azure AD tartalmazza a licencelési biztonságicsoport-alapú. Egy vagy több termék licenceket rendelhet egy csoportot. Az Azure AD gondoskodik arról, hogy a licenceket rendelt-e a csoport összes tagja számára. Új tagokat, akik csatlakozzon a csoporthoz rendeli hozzá a szükséges licencekkel. Ha elhagyják a csoport, ezeket a licencek eltávolítását. Ez szükségtelenné teszi a licenckezelést a PowerShell automatizálása a szervezet és a részlegszintű struktúra változásaihoz igazodva felhasználónkénti alapon.

## <a name="features"></a>Szolgáltatások

Az alábbiakban a fő funkcióinak Csoportalapú Licencelés:

- Az Azure AD biztonsági csoporthoz is hozzárendelhető licencek. Biztonsági csoportok lehetnek szinkronizálása a helyszíni, az Azure AD Connect használatával. Biztonsági csoportok közvetlenül az Azure ad-ben (más néven csak felhőalapú csoportok), vagy automatikusan keresztül az Azure AD dinamikus csoport funkciót is létrehozhat.

- Ha a termék licence van rendelve egy csoportot, a rendszergazda letilthatja a termékben lévő egy vagy több service-csomagokról. Általában ez történik, ha a szervezete még nem áll készen a termékben szolgáltatás elindítására. Például a rendszergazda előfordulhat, hogy rendelje hozzá az Office 365 részleghez, de ideiglenesen letilthatja a Yammer-szolgáltatást.

- Összes Microsoft-felhőszolgáltatás felhasználói szintű licencelési igénylő támogatottak. Ez magában foglalja az összes Office 365 termékek, nagyvállalati mobilitási + biztonsági és Dynamics CRM-hez.

- Csoportalapú licencelési érhető el jelenleg csak [az Azure-portálon](https://portal.azure.com). Ha a felhasználók és csoportok kezelése, például az Office 365 portálra, elsősorban az egyéb felügyeleti portálokat továbbra is megteheti. De csoportok szintjén licencek kezelése az Azure-portálon kell használnia.

- Az Azure AD automatikusan kezeli a csoporttagsági változások licenc a módosításokat. Licenc módosítások általában hatékony a tagság megváltoztatása percen belül.

- Egy felhasználó lehet a megadott licenc házirendek több csoport tagja. A felhasználó is néhány licencek közvetlenül hozzárendelt, kívül minden olyan csoportot. Az eredményül kapott felhasználói állapota hozzárendelt termék és a szolgáltatás licenceket.

- Bizonyos esetekben licencek nem lehet hozzárendelni egy felhasználói. Például lehet, hogy nincs elegendő elérhető licencek a bérlő vagy ütköző szolgáltatásokat lehet, hogy van rendelve egy időben. Rendszergazdák, amely az Azure AD nem tudta teljesen feldolgozni csoport licencek felhasználókról hozzáféréssel rendelkeznek. Majd érvénybe ezen információk alapján kiigazító intézkedéseket.

- Nyilvános előzetes egy próbaverziós vagy fizetős előfizetést az Azure AD alapszintű vagy a prémium szintű kiadásai a bérlő használatához szükséges licenc csoport-alapú felügyeleti.

## <a name="your-feedback-is-welcome"></a>Visszajelzése Üdvözöljük!

Ha visszajelzést vagy szolgáltatás kérelmeket, adjon való megosztásához használja [ezen a fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Következő lépések

Más esetekben a Licenckezelés Csoportalapú licenceléssel kapcsolatos további információkért lásd:

* [Ismerkedés az Azure Active Directory-licenc](active-directory-licensing-get-started-azure-portal.md)
* [Licencek hozzárendelése az Azure Active Directory csoport](active-directory-licensing-group-assignment-azure-portal.md)
* [Majd azonosítani és megoldani az Azure Active Directory csoport licenc problémák](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési egyedi licenccel rendelkező felhasználók áttelepítése](active-directory-licensing-group-migration-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési további helyzeteket is](active-directory-licensing-group-advanced.md)
