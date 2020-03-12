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
ms.date: 03/06/2020
ms.author: memildin
ms.openlocfilehash: 183b81134b2fe72a539cc6460a05d828342aafbb
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086486"
---
# <a name="monitor-identity-and-access"></a>Identitás és hozzáférés monitorozása

> [!TIP]
> Március 2020-én a Azure Security Center identitás-és hozzáférési javaslatai szerepelnek az ingyenes díjszabási szinten található összes előfizetésben. Ha van előfizetése az ingyenes szinten, a rendszer a biztonságos pontszámot fogja érinteni, mivel azokat korábban nem értékelték a személyazonosságuk és a hozzáférésük biztonsága szempontjából. 

Ha Security Center észleli a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

A biztonsági szegély egy hálózati kerületből lett kialakulóban egy identitási peremhálózaton. A biztonság kevesebb információt nyújt a hálózat védelméről és az adatai védelméről, valamint az alkalmazások és a felhasználók biztonságának kezeléséről. Napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lép a szegélyhálózatok helyére.

Az identitástevékenységek figyelésével proaktív módon tud cselekedni, mielőtt egy incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Példák a Azure Security Center az **identitás és hozzáférés erőforrás-** biztonság szakaszában esetlegesen megjelenő javaslatokra:

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az elavult fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Az itt megjelenő javaslatok teljes listáját az [identitás-és hozzáférési javaslatok](recommendations-reference.md#recs-identity)című részben találja.

> [!NOTE]
> Ha az előfizetése több mint 600-fiókkal rendelkezik, Security Center nem tudja futtatni az identitással kapcsolatos ajánlásokat az előfizetésében. A nem futtatott javaslatok az alábbi "nem elérhető értékelések" alatt találhatók.
A Security Center nem tudja futtatni az identitásra vonatkozó javaslatokat a Cloud Solution Provider (CSP) partner rendszergazdai ügynökeit.
>


Az összes identitási és hozzáférési javaslat a **javaslatok** oldalon két biztonsági vezérlőn belül érhető el:

- Hozzáférés és engedélyek kezelése 
- MFA engedélyezése

![A két biztonsági ellenőrzés az identitással és hozzáféréssel kapcsolatos javaslatokkal](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA) engedélyezése

Az MFA engedélyezéséhez [Azure Active Directory (ad) bérlői engedélyek](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)szükségesek. 

- Ha prémium szintű AD-kiadással rendelkezik, engedélyezze az MFA használatát [feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)használatával.

- Az AD Free Edition felhasználói az [ad-dokumentációban](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) leírtak szerint engedélyezhetik a Azure Active Directory **biztonsági alapértelmezéseit** , de az MFA engedélyezésére vonatkozó Security Center-javaslat továbbra is megjelenik.


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikkeket:

- [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
- [Az Azure SQL-szolgáltatás és-adatok védelme Azure Security Center](security-center-sql-service-recommendations.md)