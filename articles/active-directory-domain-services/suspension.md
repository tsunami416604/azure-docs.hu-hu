---
title: 'Azure Active Directory Domain Services: Felfüggesztett tartományok | Microsoft Docs'
description: Felügyelt tartomány felfüggesztése és törlése
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 781a81589032c290cef7342e7210ee36f388b22a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233984"
---
# <a name="understand-the-suspended-states-for-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services felügyelt tartomány felfüggesztett állapotának ismertetése

Ha Azure Active Directory Domain Services (Azure AD DS) nem tud hosszú ideig kiszolgálni egy felügyelt tartományt, a felügyelt tartományt felfüggesztett állapotba helyezi. Ez a cikk ismerteti a felügyelt tartományok felfüggesztésének okát, valamint a felfüggesztett tartomány szervizelését.


## <a name="states-your-managed-domain-can-be-in"></a>Kijelenti, hogy a felügyelt tartománya lehet

![Felfüggesztett tartomány idővonala](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Az előző ábra az Azure AD DS felügyelt tartományának lehetséges állapotait ismerteti.

### <a name="running-state"></a>"Running" állapot
A megfelelően konfigurált és rendszeresen működő felügyelt tartomány **futó** állapotban van.

**Mire számíthat**
* A Microsoft rendszeresen nyomon követheti a felügyelt tartomány állapotát.
* A felügyelt tartományhoz tartozó tartományvezérlők frissítése és frissítése rendszeresen megtörténik.
* A Azure Active Directory változásai rendszeresen szinkronizálhatók a felügyelt tartományba.
* A felügyelt tartományhoz rendszeres biztonsági mentés készül.


### <a name="needs-attention-state"></a>"Figyelmet igényel" állapot
A felügyelt tartományoknak beavatkozásra van szükségük, ha egy vagy több probléma szükséges a rendszergazdának a művelet elvégzéséhez. A felügyelt tartomány állapot lapja egy vagy több riasztást listáz ebben az állapotban.

Ha például konfigurált egy korlátozó NSG a virtuális hálózathoz, előfordulhat, hogy a Microsoft nem tudja frissíteni és figyelni a felügyelt tartományt. Ez az érvénytelen konfiguráció olyan riasztást indít, amely a felügyelt tartományt a "figyelmet igényel" állapotba helyezi.

Az egyes riasztások feloldási lépéseinek összessége. Bizonyos riasztások átmenetiek, és a szolgáltatás automatikusan feloldja a szolgáltatást. A riasztáshoz kapcsolódó megoldási lépések utasításait követve más riasztásokat is megoldhat. Egyes kritikus riasztások esetén fel kell vennie a kapcsolatot a Microsoft ügyfélszolgálatával.

További információ: a [riasztások hibakeresése felügyelt tartományon](troubleshoot-alerts.md).

**Mire számíthat**

Bizonyos esetekben (Ha például érvénytelen hálózati konfigurációval rendelkezik) a felügyelt tartományhoz tartozó tartományvezérlők nem érhetők el. Ha a felügyelt tartomány "figyelmet igényel" állapotban van, a Microsoft nem tudja garantálni, hogy rendszeresen figyelni, javítani, frissíteni vagy biztonsági mentést készít.

* A felügyelt tartomány sérült állapotban van, és a folyamatos állapot figyelése leállhat a riasztás feloldása előtt.
* A felügyelt tartományhoz tartozó tartományvezérlőket nem lehet kijavítani vagy frissíteni.
* Előfordulhat, hogy a Azure Active Directory módosításai nem szinkronizálhatók a felügyelt tartományba.
* Lehetséges, hogy a felügyelt tartomány biztonsági másolatait is el lehet végezni.
* Ha a felügyelt tartományt érintő riasztásokat oldja fel, lehetséges, hogy a "Running" állapotra állíthatja vissza.
* A kritikus riasztások olyan konfigurációs problémák esetén aktiválódnak, amikor a Microsoft nem tudja elérni a tartományvezérlőket. Ha az ilyen riasztások nem oldhatók fel 15 napon belül, a felügyelt tartomány "felfüggesztett" állapotba kerül.


### <a name="the-suspended-state"></a>A "felfüggesztett" állapot
A felügyelt tartomány a következő okok  miatt felfüggesztett állapotba kerül:

* Egy vagy több kritikus riasztás 15 nap múlva nem lett feloldva. A kritikus riasztásokat olyan helytelen konfiguráció okozhatja, amely blokkolja az Azure AD DS által igényelt erőforrásokhoz való hozzáférést.
    * Például a riasztás [AADDS104: A hálózati](alert-nsg.md) hiba több mint 15 napja megoldatlan a felügyelt tartományban.
* Az Azure-előfizetéssel kapcsolatos számlázási probléma, vagy az Azure-előfizetése lejárt.

A felügyelt tartományok fel lesznek függesztve, ha a Microsoft nem tudja folyamatosan kezelni, figyelni, javítani vagy biztonsági másolatot készíteni a tartományról.

**Mire számíthat**
* A felügyelt tartományhoz tartozó tartományvezérlők kiépítve, és nem érhetők el a virtuális hálózaton belül.
* Secure LDAP a felügyelt tartomány elérését az interneten keresztül (ha engedélyezve van), leáll.
* A felügyelt tartományba való hitelesítéssel, a tartományhoz csatlakoztatott virtuális gépekre való bejelentkezéssel, illetve az LDAP/LDAPs-kapcsolaton keresztüli csatlakozással kapcsolatos hibákat tapasztal.
* A felügyelt tartományhoz tartozó biztonsági másolatok már nem lettek elvégezve.
* Az Azure AD-vel való szinkronizálás leáll.

A riasztás feloldása után a felügyelt tartomány a "felfüggesztett" állapotba kerül. Ezután kapcsolatba kell lépnie a támogatási szolgálattal.
A támogatás visszaállíthatja a felügyelt tartományt, de csak akkor, ha a 30 napnál régebbi biztonsági másolat létezik.

A felügyelt tartomány 15 napig csak felfüggesztett állapotban marad. A felügyelt tartomány helyreállításához a Microsoft azt javasolja, hogy azonnal oldja fel a kritikus riasztásokat.


### <a name="deleted-state"></a>"Törölt" állapot
A "felfüggesztett" állapotú felügyelt tartomány 15 napig törölve.

**Mire számíthat**
* A rendszer törli a felügyelt tartomány összes erőforrását és biztonsági mentését.
* A felügyelt tartomány nem állítható vissza, és az Azure AD DS használatához új felügyelt tartományt kell létrehoznia.
* A törlése után a rendszer nem számláz a felügyelt tartományhoz.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Honnan tudhatja, hogy a felügyelt tartomány fel van-e függesztve?
A Azure Portal Azure AD DS Health (állapot) lapján megjelenik egy [riasztás](troubleshoot-alerts.md) , amely kijelenti, hogy a tartomány fel van függesztve. A tartomány állapota a "Felfüggesztve" állapotot is megjeleníti.


## <a name="restore-a-suspended-domain"></a>Felfüggesztett tartomány visszaállítása
A "felfüggesztett" állapotú tartomány visszaállításához hajtsa végre a következő lépéseket:

1. Nyissa meg a Azure Portal [Azure Active Directory Domain Services lapját](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) .
2. Válassza ki a felügyelt tartományt.
3. A bal oldali panelen válassza az **állapot**lehetőséget.
4. Válassza ki a riasztást. A riasztás azonosítója AADDS503 vagy AADDS504 lesz a felfüggesztés okának megfelelően.
5. Válassza ki a riasztásban megadott feloldási hivatkozást. Ezután kövesse a lépéseket a riasztás feloldásához.

A felügyelt tartomány csak a legutóbbi biztonsági mentés dátumára állítható vissza. A legutóbbi biztonsági mentés dátuma megjelenik a felügyelt tartomány állapot lapján. A legutóbbi biztonsági mentést követően történt módosítások nem lesznek visszaállítva. A felügyelt tartomány biztonsági másolatait akár 30 napig tárolja a rendszer. A 30 napnál régebbi biztonsági mentések törlődnek.


## <a name="next-steps"></a>További lépések
- [A felügyelt tartományhoz tartozó riasztások feloldása](troubleshoot-alerts.md)
- [További információ a Azure Active Directory Domain Servicesról](overview.md)
- [Kapcsolatfelvétel a termék csapatával](contact-us.md)

## <a name="contact-us"></a>Kapcsolat
A [visszajelzések megosztásához és](contact-us.md)a támogatáshoz forduljon a Azure Active Directory Domain Services termék csapatához.
