---
title: Az Azure AD Connect Health és a felhasználói adatvédelem |} Microsoft Docs
description: Ez a dokumentum ismerteti az Azure AD Connect Health felhasználói adatok.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bf26e91308cfec0dc8ede20e683919b5764a4868
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Felhasználói adatok és az Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk az Azure AD Connect Health és a felhasználói adatvédelem foglalkozik.  Az Azure AD Connect és felhasználói adatvédelmi információkat lásd: a cikk [Itt](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="user-privacy-classification"></a>Felhasználói adatvédelmi besorolás
Az Azure AD Connect Health beleesik a **adatfeldolgozó** GDPR besorolási kategóriáját. A processzor feldolgozási folyamat, mint a szolgáltatás az adatfeldolgozás szolgáltatásokat nyújt kulcs partnerek és a záró fogyasztók. Az Azure AD Connect Health nem hozhat létre a felhasználói adatokat, és rendelkezik a független befolyással milyen személyes adatok gyűjtése, és hogyan használja fel azokat. Adatok beolvasása, összesítési, elemzés és a jelentéskészítés az Azure AD Connect Health meglévő helyszíni adatok alapján. 

## <a name="data-retention-policy"></a>Adatmegőrzési szabályzat
Az Azure AD Connect Health nem-jelentések készítéséhez, hajtsa végre az analytics vagy 30 napon túl áttekintést adnak. Ezért az Azure AD Connect Health nem tárolja, feldolgozni, és bármely 30 napon túl adatok megőrzése mellett. Ez a kialakítás megfelel a GDPR szabályzat, a Microsoft adatvédelmi megfelelőségi szabályzatok és az adatmegőrzési házirendek az Azure AD. 

Az aktív kiszolgáló **Állapotfigyelő szolgáltatás adatokat nem folyamatosan naprakész adatokat tartalmazzon** **hiba** riasztásai több mint 30 egymást követő nap javasoljuk, hogy a nem adatok elérte-e a Connect Health adott időtartam során. Ezek a kiszolgálók letiltásra kerül, és nem jelenik meg a Connect Health portálon. A kiszolgálók engedélyezéséhez el kell távolítania és [telepítse újra a health agent](active-directory-aadconnect-health-agent-install.md). Vegye figyelembe, hogy ez nem vonatkozik **figyelmeztetések** rendelkező ugyanolyan típusú riasztást. Figyelmeztetés azt jelzi, hogy részleges adat hiányzik a kiszolgálóról, figyelmeztetést kap. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Tiltsa le az adatok gyűjtésével és az Azure AD Connect Health ellenőrzése
Az Azure AD Connect Health lehetővé teszi, hogy állítsa le az adatgyűjtést az egyes figyelt kiszolgálókhoz vagy a figyelt szolgáltatás egy példánya. Például egyéni ADFS (Active Directory összevonási szolgáltatások) kiszolgálókat, amelyek figyelése az Azure AD Connect Health-gyűjtésének állíthatók le. Adatgyűjtés a teljes az AD FS-példányhoz az Azure AD Connect Health figyelt is leállíthatja. Ha Ön úgy dönt, hogy engedélyezi, a hozzájuk tartozó kiszolgálók törlődnek az Azure AD Connect Health portálon adatgyűjtés leállítása után. 

>[!IMPORTANT]
> Kell vagy az Azure AD globális rendszergazdai jogosultságokkal, vagy a közreműködő szerepkört az RBAC delete figyelt kiszolgálók az Azure AD Connect Health.
>
> Egy kiszolgáló vagy a szolgáltatáspéldány eltávolítása az Azure AD Connect Health nincs visszavonható művelet. 

### <a name="what-to-expect"></a>Mi várható?
Ha kikapcsolja az adatok gyűjtésével és a figyelt a különálló kiszolgáló vagy a figyelt szolgáltatás példányának figyelését, vegye figyelembe a következőket:

- Ha törli a figyelt szolgáltatás egy példánya, a példány eltávolítják a listából az Azure AD Connect Health figyelési szolgáltatás a portálon. 
- Ha törli a figyelt kiszolgáló vagy a figyelt szolgáltatás egy példánya, a rendszerállapot-ügynöke nincs eltávolítva vagy eltávolítva a kiszolgálóról. A rendszerállapot-ügynöke szeretnék adatokat küldeni az Azure AD Connect Health van konfigurálva. Manuálisan távolítsa el a Health Agent ügynököt a korábban megfigyelt kiszolgálókon kell.
- Ha nem távolította el a Health Agent ügynököt a lépés végrehajtása előtt, a a kiszolgálókra a rendszerállapot-ügynöke kapcsolatos hibaesemények jelenhetnek.
- A figyelt szolgáltatás példányához tartozó minden adat törlődik a Microsoft Azure adatmegőrzési szabályzatának megfelelően.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Tiltsa le az adatgyűjtést és a figyelt kiszolgáló figyelése
Lásd: [kiszolgáló eltávolítása az Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Adatgyűjtés és a figyelt szolgáltatás egy példánya figyelésének letiltása
Lásd: [egy szolgáltatáspéldány eltávolítása az Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Engedélyezze újra az adatok gyűjtésével és az Azure AD Connect Health ellenőrzése
Egy korábban törölt figyelt szolgáltatás számára az Azure AD Connect Health ellenőrzése újbóli engedélyezéséhez el kell távolítania és [telepítse újra a health agent](active-directory-aadconnect-health-agent-install.md) a kiszolgálókon.


## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy házirendet, a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
* [Az Azure AD Connect és a felhasználói adatok](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

