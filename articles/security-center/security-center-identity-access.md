---
title: Identitás és hozzáférés figyelése az Azure Security Centerben | Microsoft Docs
description: Itt megtudhatja, hogyan használható az Azure Security Center identitási és hozzáférési funkciója a felhasználók hozzáférési tevékenységeinek és identitással kapcsolatos problémáinak figyelésére.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481959"
---
# <a name="monitor-identity-and-access"></a>Identitás és hozzáférés monitorozása

> [!TIP]
> 2020 márciusától az Azure Security Center identitás- és hozzáférési javaslatai az ingyenes tarifacsomag összes előfizetésében megtalálhatók. Ha előfizetései vannak az ingyenes szinten, a rendszer érinti a biztonságos pontszámukat, mivel korábban nem értékelték őket identitásuk és hozzáférésük biztonsága miatt. 

Amikor a Security Center potenciális biztonsági réseket azonosít, javaslatokat készít, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán az erőforrások megkeményedése és védelme érdekében.

A biztonsági határ hálózatpereméről identitáshatárra változott. A biztonság kevésbé szól a hálózat védelméről, hanem az adatok védelméről, valamint az alkalmazások és felhasználók biztonságának kezeléséről. Napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lép a szegélyhálózatok helyére.

Az identitáskezelési tevékenységek figyelésével proaktív műveleteket tehet az incidens előtt, vagy reaktív műveleteket tehet a támadási kísérlet leállításához. Például a Security Center megjelölheti elavult fiókok (fiókok, amelyek már nincs szükség, és blokkolja a bejelentkezést az Azure Active Directory) az eltávolítás. 

Példák az Azure Security Center **identitás- és** hozzáférés-erőforrás-biztonsági szakaszában látható javaslatokra:

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az elavult fiókokat el kell távolítani az előfizetésből

Ezekről a javaslatokról, valamint az itt látható javaslatok teljes listájáról az [Identitás- és hozzáférési javaslatok című témakörben olvashat](recommendations-reference.md#recs-identity)bővebben.

> [!NOTE]
> Ha az előfizetés több mint 600 fiókkal rendelkezik, a Security Center nem tudja futtatni az identitásjavaslatokat az előfizetésen. A nem futtatott javaslatok az alábbi "nem elérhető értékelések" alatt találhatók.
A Security Center nem tudja futtatni az identitásjavaslatokat egy felhőszolgáltató (CSP) partner felügyeleti ügynökei ellen.
>


Az identitás- és hozzáférési javaslatok mindegyike a **Javaslatok** lap két biztonsági vezérlőjében érhető el:

- Hozzáférés és engedélyek kezelése 
- MFA engedélyezése

![A két biztonsági ellenőrzés az identitásra és a hozzáférésre vonatkozó ajánlásokkal](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Többtényezős hitelesítés engedélyezése (MFA)

Az MFA engedélyezéséhez [az Azure Active Directory (AD) bérlői engedélyei](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)re van szükség. 

- Ha az AD prémium kiadása van, engedélyezze az MFA-t [feltételes hozzáféréssel.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- Az AD ingyenes kiadás felhasználói engedélyezhetik a **biztonsági alapértelmezett beállításokat** az Azure Active Directoryban az [AD dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) leírtak szerint, de a Security Center mfa engedélyezésére vonatkozó javaslat továbbra is megjelenik.


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a más Azure-erőforrástípusokra vonatkozó javaslatokról, olvassa el az alábbi cikkeket:

- [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
- [Az Azure SQL-szolgáltatás és az adatok védelme az Azure Security Centerben](security-center-sql-service-recommendations.md)