---
title: A Azure AD Connect telepítési problémáinak elhárítása | Microsoft Docs "
description: Ez a témakör a Azure AD Connect telepítésével kapcsolatos hibák elhárításának lépéseit ismerteti.
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
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "70211845"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Hibaelhárítás: Azure AD Connect telepítési problémák

## <a name="recommended-steps"></a>**Ajánlott lépések**
Ellenőrizze, hogy a [Azure ad Connect telepítési típusa](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) megfelelő-e. Ha teljesíti az expressz telepítés feltételeit, javasoljuk, hogy az expressz telepítéssel lépjen tovább. Az expressz telepítés a telepítés befejezéséhez minimálisan szükséges beállításokat biztosít, ezért a problémák kevésbé valószínűek. 

Ha azonban nem felel meg az expressz telepítési feltételeknek, és el kell végeznie az Egyéni telepítést, akkor itt talál néhány ajánlott eljárást, amelyekkel elkerülheti a gyakori problémákat. Az egyszerűség kedvéért csak a szelektív beállítások szerepelnek itt:

* Győződjön meg arról, hogy Ön rendszergazda azon a számítógépen, amelyre a HRE-kapcsolatot telepíti. Jelentkezzen be a gépre ugyanazzal a rendszergazdai hitelesítő adatokkal.

* Ha meglévő SQL Server kíván használni, a következő lapon minden beállítás alapértelmezett értékre kerül, kivéve a "meglévő SQL Server használata" lehetőséget. [További](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) információ az egyéni telepítési beállítások használatáról. 

    ![Meglévő SQL Server használata](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* A következő lapon válassza az "új AD-fiók létrehozása" lehetőséget, hogy elkerülje a meglévő fiókkal kapcsolatos engedélyek megadását.

    ![AD Forest-fiók](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Gyakori problémák**

* [A helyszíni Active Directory kapcsolódási problémái](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Kapcsolódási problémák az online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [A helyszíni Active Directoryekkel kapcsolatos engedélyek](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Ajánlott dokumentumok**
* [Az Azure AD Connect előfeltételei](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Az Azure AD Connect telepítési típusának kiválasztása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Az Azure AD Connect testreszabott telepítése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: frissítés egy előző verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Mi az átmeneti kiszolgáló?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Mi az az ADConnectivityTool PowerShell-modul?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>További lépések
- [Azure ad Connect szinkronizálás](how-to-connect-sync-whatis.md).
- [Mi az a hibrid identitás?](whatis-hybrid-identity.md)





