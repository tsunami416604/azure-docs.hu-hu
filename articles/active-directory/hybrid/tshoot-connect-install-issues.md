---
title: Az Azure AD Connect telepítési problémák elhárítása |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure AD Connect telepítésével kapcsolatos problémák elhárítása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876922"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Hibaelhárítás: Az Azure AD Connect telepítésével kapcsolatos problémák

## **<a name="recommended-steps"></a>Javasolt lépések**
. Ellenőrizze, hogy mely [az Azure AD Connect telepítési típusának](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) ideális választás az Ön számára. Ha megfelel a feltételeknek az expressz telepítési, majd erősen javasoljuk, hogy látogassa meg a gyorstelepítés. Az expressz telepítési a telepítés befejezéséhez szükséges minimális lehetőségeket kínál, így kevesebb valószínűségét esetleges problémákat. 

Azonban ha nincs a feltételeknek az Expressz telepítés és az egyéni telepítés kell tennie, majd az alábbiakban néhány gyakorlati tanácsot is követheti gyakori hibák elkerülése érdekében. Az egyszerűség kedvéért csak szelektív beállításokat Itt szerepelnek:

* Ellenőrizze, hogy a rendszergazda a számítógépen, amelyre telepíteni kívánja az AAD Connect. Jelentkezzen be a gép ugyanazt a rendszergazdai hitelesítő adataival.

* Legyen az alapértelmezett "Meglévő SQL Server használata", kivéve a következő oldalon, ha meglévő SQL Server használata a beállítások lehetővé teszik. Az alábbiakban [további részleteket](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) használatáról egyéni telepítési beállításait. 

    ![Meglévő SQL Server használata](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* A következő oldalon válassza ki az lehetőséget "Hozzon létre új AD-fiók", meglévő fiókkal problémák elkerülése érdekében bármilyen engedéllyel.

    ![AD-erdő fiók](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### **<a name="common-issues"></a>Gyakori problémák**

* [Kapcsolódási problémák, a helyszíni Active Directoryval](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Kapcsolódási problémák az online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Engedélyekkel kapcsolatos problémák a helyszíni Active Directoryval](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## **<a name="recommended-documents"></a>Ajánlott dokumentumok**
* [Az Azure AD Connect előfeltételei](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Használja az Azure AD Connect telepítési típusának kiválasztása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Az Azure AD Connect testreszabott telepítése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Egy előző verzióról a legújabbra frissítése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Mi az átmeneti kiszolgálót?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Mi az a ADConnectivityTool PowerShell-modult?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>További lépések
- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md).
- [Mi a hibrid identitás? ](whatis-hybrid-identity.md).





