---
title: Az Azure AD Connect telepítési problémáinak elhárítása | Microsoft Dokumentumok"
description: Ez a témakör az Azure AD Connect telepítésével kapcsolatos problémák elhárításának lépéseit ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70211845"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Hibaelhárítás: Az Azure AD Connect telepítési problémái

## <a name="recommended-steps"></a>**Ajánlott lépések**
Ellenőrizze, hogy melyik [Azure AD Connect telepítési típus](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) alkalmas az Ön számára. Ha megfelel az expressz telepítés kritériumainak, akkor javasoljuk, hogy menjen az expressz telepítéshez. Az expressz telepítés minimális lehetőségeket biztosít a telepítés befejezéséhez, így kisebb a valószínűsége a problémáknak. 

Ha azonban nem felel meg az expressz telepítési feltételeknek, és meg kell tennie az egyéni telepítést, akkor az alábbiakat néhány ajánlott eljárás követheti a gyakori problémák elkerülése érdekében. Az egyszerűség kedvéért csak a szelektív lehetőségek et említik itt:

* Győződjön meg arról, hogy rendszergazda azon a számítógépen, amelyen az AAD Connect et telepíti. Jelentkezzen be a számítógépre ugyanazzal a rendszergazdai hitelesítő adatokkal.

* Hagyja, hogy az összes beállítás legyen az alapértelmezett a következő lapon, kivéve a "Meglévő SQL Server használata" lehetőséget, ha meglévő SQL Server t szeretné használni. Az alábbiakban [további részleteket olvashat](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) az egyéni telepítési beállítások használatáról. 

    ![Meglévő SQL Server használata](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* A következő lapon válassza az "Új AD-fiók létrehozása" lehetőséget, hogy elkerülje a meglévő fiókkal kapcsolatos engedélyproblémákat.

    ![AD erdőfiók](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Gyakori problémák**

* [Kapcsolódási problémák a helyszíni Active Directoryval.](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools)

* [Kapcsolódási problémák az online Azure Active Directoryval kapcsolati problémákkal kapcsolatban.](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity)

* [A helyszíni Active Directoryval kapcsolatos engedélyproblémák.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account)

## <a name="recommended-documents"></a>**Ajánlott dokumentumok**
* [Az Azure AD Connect előfeltételei](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Az Azure AD Connect telepítési típusának kiválasztása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Az Azure AD Connect testreszabott telepítése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: frissítés egy előző verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Mi az átmeneti kiszolgáló?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Mi az az ADConnectivityTool PowerShell-modul?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>További lépések
- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md).
- [Mi az a hibrid identitás?](whatis-hybrid-identity.md)





