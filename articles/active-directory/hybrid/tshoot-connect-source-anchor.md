---
title: 'Azure AD Connect: A telepítés során forrás Forráshorgony hibáinak elhárítása |} A Microsoft Docs'
description: Ez a témakör a forráshorgony hibáinak elhárítása a telepítés során szükséges lépéseket.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114154"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>A telepítés során forrás Forráshorgony kapcsolatos hibák elhárítása
Ez a cikk ismerteti a különböző forráshorgony kapcsolatos problémák megoldásához a telepítési és ajánlatok módon során esetlegesen felmerülő problémákat.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Az Azure Active Directoryban érvénytelen Forráshorgony

### <a name="custom-installation"></a>Egyéni telepítés

Egyéni telepítés során az Azure AD Connect beolvassa a forráshorgony forrásházirend az Azure Active Directoryból. Ha a szabályzat megtalálható az Azure Active Directory, az Azure AD Connect ugyanaz a szabályzat vonatkozik, az ügyfél bírálni. A varázsló értesíti arról, hogy melyik attribútum elolvasták. Ezenkívül a varázsló figyelmezteti, ha azelőtt próbál felülbírálhatja a forrás kapcsolati alappal házirendet.

Az olvasási művelet során is lehet, hogy a forrás kapcsolati alappal szabályzatot az Azure Active Directoryban nem a várt. Ebben az esetben az Azure AD Connect nem tudja, milyen a forráshorgony használatával, és Kézi felülbírálás kell.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

A probléma megoldásához manuálisan felülbírálhatja a forráshorgony egy adott attribútum kiválasztásával. Ez a beállítás folytatásához, csak ha biztos benne, válassza ki, melyik attribútum. Ha nem bizonyos, lépjen kapcsolatba [a Microsoft támogatási](https://support.microsoft.com/contactus/) útmutatást. Ha a forrás kapcsolati alappal szabályzatot módosítja, a helyi felhasználók és a kapcsolódó Azure-erőforrások közötti társítás meghibásodásához vezethet.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Az Expressz telepítés
Expressz telepítés során az Azure AD Connect beolvassa a forráshorgony forrásházirend az Azure Active Directoryból. Ha a szabályzat megtalálható az Azure Active Directory, az Azure AD Connect ugyanaz a szabályzat vonatkozik. Kézi felülbírálás nincs lehetőség van.

Az olvasási művelet során is lehet, hogy a forrás kapcsolati alappal szabályzatot az Azure Active Directoryban nem a várt. Ebben az esetben az Azure AD Connect nem tudja, milyen a forráshorgony kell.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

A probléma megoldásához kell telepítse újra az egyéni módban, és kézzel felülbírálják a forráshorgony egy adott attribútum kiválasztásával. Ez a beállítás folytatásához, csak ha biztos benne, válassza ki, melyik attribútum. Ha nem bizonyos, lépjen kapcsolatba [a Microsoft támogatási](https://support.microsoft.com/contactus/) útmutatást. Ha a forrás kapcsolati alappal szabályzatot módosítja, a helyi felhasználók és a kapcsolódó Azure-erőforrások közötti társítás meghibásodásához vezethet.

### <a name="invalid-source-anchor-in-sync-engine"></a>A szinkronizálási motor érvénytelen Forráshorgony
A telepítés során a rendszer lehetséges az Azure AD Connect kísérletek a szinkronizálási motor használatával egy érvénytelen forráshorgony konfigurálása. Ez a művelet nagy valószínűséggel termékkel kapcsolatos probléma, és az Azure AD Connect telepítése sikertelen lesz. Kapcsolattartó [a Microsoft támogatási](https://support.microsoft.com/contactus/) Ha fut, a probléma.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).