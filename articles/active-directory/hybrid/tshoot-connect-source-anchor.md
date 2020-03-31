---
title: 'Azure AD Connect: A forráshorgonyokkal kapcsolatos problémák elhárítása a telepítés során | Microsoft dokumentumok'
description: Ez a témakör a forráshorgonynal kapcsolatos problémák elhárításának lépéseit ismerteti a telepítés során.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114154"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>A forráshorgonyokkal kapcsolatos problémák elhárítása a telepítés során
Ez a cikk ismerteti a telepítés során előforduló különböző forráshorgonyokkal kapcsolatos problémákat, és lehetőséget kínál a problémák megoldására.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Érvénytelen forrásszerkesztő az Azure Active Directoryban

### <a name="custom-installation"></a>Egyéni telepítés

Az egyéni telepítés során az Azure AD Connect beolvassa a forráshorgony-szabályzatot az Azure Active Directoryból. Ha a szabályzat létezik az Azure Active Directoryban, az Azure AD Connect ugyanazt a szabályzatot alkalmazza, kivéve, ha az ügyfél felülbírálja. A varázsló tájékoztatja, hogy melyik attribútumot olvasták. Ezenkívül a varázsló figyelmeztet, ha megpróbálja felülírni a forráshorgony-házirendet.

Az olvasási művelet során lehetséges, hogy a forráshorgony-házirend az Azure Active Directoryban váratlan. Ebben az esetben az Azure AD Connect nem tudja, hogy a forráshorgonyt mi ként kell használnia, és manuális felülbírálást igényel.</br>
![Váratlan](media/tshoot-connect-source-anchor/source1.png)

A probléma megoldásához manuálisan felülbírálhatja a forráshorgonyt egy adott attribútum kiválasztásával. Akkor és csak akkor folytassa ezt a beállítást, ha biztos abban, hogy melyik attribútumot szeretné kiválasztani. Ha nem biztos benne, kérjen útmutatást a [Microsoft támogatási szolgálatától.](https://support.microsoft.com/contactus/) Ha módosítja a forráshorgony-szabályzatot, megszakíthatja a helyszíni felhasználók és a hozzájuk tartozó Azure-erőforrások közötti társítást.</br>
![Váratlan](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Expressz telepítés
Az expressz telepítés során az Azure AD Connect beolvassa a forráshorgony-házirendet az Azure Active Directoryból. Ha a szabályzat létezik az Azure Active Directoryban, az Azure AD Connect ugyanazt a szabályzatot alkalmazza. Nincs lehetőség a kézi felülbírálásra.

Az olvasási művelet során lehetséges, hogy a forráshorgony-házirend az Azure Active Directoryban váratlan. Ebben az esetben az Azure AD Connect nem tudja, mi legyen a forráshorgony.</br>
![Váratlan](media/tshoot-connect-source-anchor/source3.png)

A probléma megoldásához újra kell telepítenie az egyéni módhasználatával, és manuálisan felül kell bírnia a forráshorgonyt egy adott attribútum kiválasztásával. Akkor és csak akkor folytassa ezt a beállítást, ha biztos abban, hogy melyik attribútumot szeretné kiválasztani. Ha nem biztos benne, kérjen útmutatást a [Microsoft támogatási szolgálatától.](https://support.microsoft.com/contactus/) Ha módosítja a forráshorgony-szabályzatot, megszakíthatja a helyszíni felhasználók és a hozzájuk tartozó Azure-erőforrások közötti társítást.

### <a name="invalid-source-anchor-in-sync-engine"></a>Érvénytelen forrásszerkesztő a szinkronizálómotorban
A telepítés során lehetséges, hogy az Azure AD Connect megkísérli a szinkronizálási motor konfigurálását egy érvénytelen forráshorgony használatával. Ez a művelet valószínűleg egy termék probléma, és az Azure AD Connect telepítése sikertelen lesz. Ha befut a problémába, forduljon a [Microsoft támogatási szolgálatához.](https://support.microsoft.com/contactus/)</br>
![Váratlan](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).