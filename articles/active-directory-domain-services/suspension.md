---
title: Felfüggesztett tartományok az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Ismerje meg az Azure AD DS felügyelt tartományának különböző állapotait, és hogyan állíthatja vissza a felfüggesztett tartományt.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 0b6e28fc92361fe20fc34e92386bf17199b19617
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519028"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Az állapotok megismerése és a felfüggesztett tartományok feloldása az Azure Active Directory tartományi szolgáltatásokban

Ha az Azure Active Directory tartományi szolgáltatások (Azure AD DS) hosszú ideig nem tudja kiszolgálni a felügyelt tartományt, a felügyelt tartományt felfüggesztett állapotba helyezi. Ha egy felügyelt tartomány ezután felfüggesztett állapotban marad, a rendszer automatikusan törli. Az Azure AD DS által felügyelt tartomány kifogástalan állapotban tartása és a felfüggesztés elkerülése érdekében a lehető leggyorsabban oldja fel a riasztásokat.

Ez a cikk ismerteti a felügyelt tartományok felfüggesztésének és a felfüggesztett tartomány helyreállításának módját.

## <a name="overview-of-managed-domain-states"></a>Felügyelt tartományállapotok áttekintése

Az Azure AD DS felügyelt tartomány életciklusa során különböző állapotok jelzik az állapotát. Ha a felügyelt tartomány problémát jelent, gyorsan oldja meg a mögöttes okot, hogy az állapot ne romoljon tovább.

![Az Azure AD DS által felügyelt tartomány felfüggesztése felé irányuló állapotok előrehaladása](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Az Azure AD DS felügyelt tartománya a következő állapotok egyikében lehet:

* [Fut](#running-state)
* [Figyelmet igényel](#needs-attention-state)
* [Felfüggesztve](#suspended-state)
* [Törölve](#deleted-state)

## <a name="running-state"></a>Futó állapot

Az Azure AD DS által felügyelt tartomány, amely megfelelően van konfigurálva, és problémamentesen fut a *Futó* állapotban van. Ez a felügyelt tartomány kívánt állapota.

### <a name="what-to-expect"></a>Mire számíthat

* Az Azure platform rendszeresen figyelheti a felügyelt tartomány állapotát.
* A felügyelt tartomány tartományvezérlőit rendszeresen javítják és frissítik.
* Az Azure Active Directory módosításait rendszeresen szinkronizálja a felügyelt tartománnyal.
* A felügyelt tartomány rendszeres biztonsági másolatai készülnek.

## <a name="needs-attention-state"></a>Szüksége figyelem állapota

Az Azure AD DS által felügyelt tartomány egy vagy több kijavítandó problémák a *szükséges figyelmet* állapotban van. A felügyelt tartomány állapotlapja felsorolja a riasztásokat, és jelzi, hogy hol van a probléma. Egyes riasztások átmeneti, és az Azure platform automatikusan feloldódik. Egyéb riasztások esetén a problémát a megadott megoldási lépések végrehajtásával oldhatja meg. Kritikus riasztást kap, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségre.

A riasztás egyik példája, ha van egy korlátozó hálózati biztonsági csoport. Ebben a konfigurációban előfordulhat, hogy az Azure platform nem tudja frissíteni és figyelni a felügyelt tartományt. Riasztás jön létre, és az állapot *a Figyelem kell.*

További információ: [Az Azure AD DS által felügyelt tartományra vonatkozó riasztások hibaelhárítása][resolve-alerts]című témakörben talál.

### <a name="what-to-expect"></a>Mire számíthat

Ha egy Azure AD DS felügyelt tartomány a *needs attention* állapotban van, az Azure platform nem lehet rendszeresen figyelni, folt, frissítés vagy biztonsági másolatot rendszeresen. Bizonyos esetekben, például egy érvénytelen hálózati konfiguráció esetén, előfordulhat, hogy a felügyelt tartomány tartományvezérlői nem érhetőek el.

* A felügyelt tartomány sérült állapotban van, és a folyamatos állapotfigyelés leállhat, amíg a riasztás fel nem oldódik.
* A felügyelt tartomány tartományvezérlői nem javíthatók és nem frissíthetők.
* Előfordulhat, hogy az Azure Active Directory ból származó módosítások nincsenek szinkronizálva a felügyelt tartománnyal.
* Előfordulhat, hogy a felügyelt tartománybiztonsági mentések nem készülnek el.
* Ha feloldja a nem kritikus riasztásokat, amelyek hatással vannak a felügyelt tartományra, az állapotnak vissza kell *térnie* a Futó állapotba.
* Kritikus riasztások aktiválódnak a konfigurációs problémák, ahol az Azure platform nem tudja elérni a tartományvezérlőket. Ha ezek a kritikus riasztások nem oldódnak fel 15 napon belül, a felügyelt tartomány *felfüggesztett* állapotba kerül.

## <a name="suspended-state"></a>Felfüggesztett állapot

Az Azure AD DS által felügyelt tartomány a következő okok valamelyike miatt lép **be felfüggesztett** állapotba:

* Egy vagy több kritikus riasztás 15 napja nem oldódott fel.
    * A kritikus riasztásokat okozhatja egy helytelen konfiguráció, amely blokkolja az Azure AD DS által szükséges erőforrásokhoz való hozzáférést. Például a riasztás [AADDS104: Hálózati hiba][alert-nsg] már több mint 15 napig feloldatlan a felügyelt tartományban.
* Számlázási probléma merül fel az Azure-előfizetéssel kapcsolatban, vagy az Azure-előfizetés lejárt.

A felügyelt tartományok felfüggesztésre kerülnek, ha az Azure platform nem tudja kezelni, monitorozni, foltozni vagy biztonsági másolatot a tartományról. A felügyelt tartomány 15 napig *felfüggesztett* állapotban marad. A felügyelt tartományhoz való hozzáférés fenntartásához azonnal oldja fel a kritikus riasztásokat.

### <a name="what-to-expect"></a>Mire számíthat

A következő viselkedés tapasztalható, ha egy Azure AD DS felügyelt tartomány *felfüggesztett* állapotban van:

* A felügyelt tartomány tartományvezérlői kivannak építve, és nem érhetőek el a virtuális hálózaton belül.
* A felügyelt tartományhoz való biztonságos LDAP-hozzáférés az interneten keresztül, ha engedélyezve van, leáll.
* Hibák vannak a felügyelt tartomány hitelesítésében, a tartományhoz csatlakozó virtuális gépekre való bejelentkezésben vagy az LDAP/LDAPS-en keresztüli csatlakozásban.
* A felügyelt tartomány biztonsági mentései már nem kerülnek mentésre.
* Az Azure AD-vel való szinkronizálás leáll.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Honnan tudja, hogy a felügyelt tartomány fel van függesztve?

Megjelenik egy [riasztás][resolve-alerts] az Azure AD DS Health lapon az Azure Portalon, amely megjegyzi, hogy a tartomány fel van függesztve. A tartomány állapota a *Felfüggesztett*állapot ot is mutatja.

### <a name="restore-a-suspended-domain"></a>Felfüggesztett tartomány visszaállítása

Az Azure AD DS által felügyelt tartomány felfüggesztett állapotú állapotának visszaállításához *hajtsa* végre az alábbi lépéseket:

1. Az Azure Portalon keressen és válasszon **domainszolgáltatásokat.**
1. Válassza ki az Azure AD DS felügyelt tartományát a listából, például *aaddscontoso.com*, majd válassza **az Állapot**lehetőséget.
1. Válassza ki a riasztást, például *AADDS503* vagy *AADDS504*, a felfüggesztés okától függően.
1. Válassza ki a riasztásban megadott felbontási hivatkozást, és kövesse a probléma megoldásához szükséges lépéseket.

Felügyelt tartomány csak az utolsó biztonsági mentés dátumáig állítható vissza. Az utolsó biztonsági mentés dátuma megjelenik a felügyelt tartomány **Egészség** lapján. Az utolsó biztonsági mentés után bekövetkezett módosítások nem lesznek visszaállítva. A felügyelt tartomány biztonsági másolatai legfeljebb 30 napig tárolódnak. A 30 napnál régebbi biztonsági mentések törlődnek.

Miután feloldotta a riasztásokat, amikor a felügyelt tartomány *felfüggesztett* állapotban van, [nyisson meg egy Azure-támogatási kérelmet][azure-support] a kifogástalan állapotba való visszatéréshez. Ha 30 napnál rövidebb a biztonsági mentés, az Azure-támogatás visszaállíthatja a felügyelt tartományt.

## <a name="deleted-state"></a>Törölt állapot

Ha egy Azure AD DS felügyelt tartomány 15 napig *felfüggesztett* állapotban marad, törlődik. Ez a folyamat helyreállíthatatlan.

### <a name="what-to-expect"></a>Mire számíthat

Amikor egy Azure AD DS felügyelt tartomány *a Törölt* állapotba lép, a következő viselkedés látható:

* A felügyelt tartomány összes erőforrása és biztonsági másolata törlődik.
* A felügyelt tartomány nem állítható vissza, és létre kell hoznia egy helyettesítő felügyelt tartományt az Azure AD DS újbóli használatához.
* A törlés után a felügyelt tartományért nem kell fizetnie.

## <a name="next-steps"></a>További lépések

Az Azure AD DS által felügyelt tartomány kifogástalan állapotának megőrzéséhez és a felfüggesztés kockázatának minimalizálásához olvassa el a [felügyelt tartományriasztásainak feloldását.][resolve-alerts]

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
