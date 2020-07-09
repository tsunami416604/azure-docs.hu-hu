---
title: 'Azure AD Connect: a telepítési hibák elhárítása a telepítés során | Microsoft Docs'
description: Ez a témakör azt ismerteti, hogyan lehet elhárítani a telepítési hibákat a forrás-horgonyban.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: cb82eb2d8e23daec0ddb8856b713c1aa051f25c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85355947"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>A forrás-és a telepítés során felmerülő hibák elhárítása
Ez a cikk ismerteti a különböző forrásokra vonatkozó, a telepítés során esetlegesen felmerülő problémákat, valamint a problémák megoldásának módszereit.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Érvénytelen a forrás horgonya Azure Active Directory

### <a name="custom-installation"></a>Egyéni telepítés

Az egyéni telepítés során Azure AD Connect beolvassa a forrás-rögzítési házirendet a Azure Active Directoryból. Ha a házirend létezik a Azure Active Directoryban, Azure AD Connect alkalmazza ugyanazt a házirendet, kivéve, ha az ügyfél felülbírálja. A varázsló tájékoztatja arról, hogy melyik attribútumot olvasta a program. Emellett a varázsló figyelmezteti, ha megpróbálja felülbírálni a forrás-rögzítési házirendet.

Az olvasási művelet során lehetséges, hogy a forrás-rögzítési házirend Azure Active Directory váratlan. Ebben az esetben a Azure AD Connect nem tudja, hogy mit kell használni a forrás-és a kézi felülbírálás.</br>
![váratlan](media/tshoot-connect-source-anchor/source1.png)

A probléma megoldásához a forrás-horgonyt manuálisan felülbírálhatja egy adott attribútum kiválasztásával. Folytassa ezt a beállítást, ha csak a kijelölni kívánt attribútumot kívánja kiválasztani. Ha nem biztos benne, forduljon a [Microsoft támogatási szolgálatához](https://support.microsoft.com/contactus/) útmutatásért. Ha megváltoztatja a forrás-rögzítési házirendet, akkor a helyszíni felhasználók és a hozzájuk tartozó Azure-erőforrások közötti társítást is megszüntetheti.</br>
![váratlan](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Expressz telepítés
Az expressz telepítés során Azure AD Connect beolvassa a forrás-rögzítési házirendet a Azure Active Directoryból. Ha a házirend létezik a Azure Active Directoryban, Azure AD Connect alkalmazza ugyanazt a házirendet. A manuális felülbírálás nem lehetséges.

Az olvasási művelet során lehetséges, hogy a forrás-rögzítési házirend Azure Active Directory váratlan. Ebben az esetben a Azure AD Connect nem tudja, mi a forrás horgonya.</br>
![váratlan](media/tshoot-connect-source-anchor/source3.png)

A probléma megoldásához újra kell telepítenie az egyéni üzemmódot, és manuálisan kell felülbírálnia a forrás-horgonyt egy adott attribútum kiválasztásával. Folytassa ezt a beállítást, ha csak a kijelölni kívánt attribútumot kívánja kiválasztani. Ha nem biztos benne, forduljon a [Microsoft támogatási szolgálatához](https://support.microsoft.com/contactus/) útmutatásért. Ha megváltoztatja a forrás-rögzítési házirendet, akkor a helyszíni felhasználók és a hozzájuk tartozó Azure-erőforrások közötti társítást is megszüntetheti.

### <a name="invalid-source-anchor-in-sync-engine"></a>Érvénytelen a forrás-horgony a Szinkronizáló motorban
A telepítés során lehetséges, hogy Azure AD Connect megkísérli a szinkronizálási motor konfigurálását egy érvénytelen forrás-horgony használatával. Ez a művelet valószínűleg egy termékkel kapcsolatos probléma, és a Azure AD Connect telepítése sikertelen lesz. Ha ezt a problémát futtatja, forduljon a [Microsoft támogatási szolgálatához](https://support.microsoft.com/contactus/) .</br>
![váratlan](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
