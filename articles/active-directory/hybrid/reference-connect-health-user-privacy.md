---
title: Azure AD Connect egészség és a felhasználók adatvédelme | Microsoft dokumentumok
description: Ez a dokumentum ismerteti a felhasználók adatvédelmét az Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58cddcde71e6e86b9abe07eb4200f13ad55ea659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253662"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>A felhasználók adatainak védelme és az Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk az Azure AD Connect Health szolgáltatással és a felhasználók adatainak védelmével foglalkozik.  Az Azure AD Connectről és a felhasználók adatairól az [itt](reference-connect-user-privacy.md)található cikkben olvashat.

## <a name="user-privacy-classification"></a>Felhasználói adatvédelmi besorolás
Az Azure AD Connect Health a GDPR-besorolás **adatfeldolgozó** kategóriájába tartozik. Adatfeldolgozó folyamatként a szolgáltatás adatfeldolgozási szolgáltatásokat nyújt a legfontosabb partnereknek és a végfelhasználóknak. Az Azure AD Connect Health nem hoz létre felhasználói adatokat, és nincs független szabályozása a személyes adatok gyűjtésének és felhasználásának felett. Az Azure AD Connect Health adatlekérése, összesítése, elemzése és jelentése a meglévő helyszíni adatokon alapul. 

## <a name="data-retention-policy"></a>Adatmegőrzési házirend
Az Azure AD Connect Health nem hoz létre jelentéseket, nem végez elemzéseket, és nem nyújt elemzéseket 30 napon túl. Ezért az Azure AD Connect Health nem tárol, nem dolgoz fel és nem tart meg adatokat 30 napon túl. Ez a kialakítás megfelel a GDPR-szabályozásoknak, a Microsoft adatvédelmi megfelelőségi előírásainak és az Azure AD adatmegőrzési szabályzatainak. 

Az aktív **állapotszolgáltatás-adatokkal** rendelkező kiszolgálók 30 egymást követő napon keresztül nem **naprakészhiba-riasztások,** arra utalnak, hogy ez alatt az időtartam alatt egyetlen adat sem érte el a Connect Health szolgáltatást. Ezek a kiszolgálók le lesznek tiltva, és nem jelennek meg a Connect Health portálon. A kiszolgálók újbóli engedélyezéséhez el kell távolítania és újra kell [telepítenie az állapotügynököt.](how-to-connect-health-agent-install.md) Kérjük, vegye figyelembe, hogy ez nem vonatkozik az azonos riasztási típusú **figyelmeztetésekre.** A figyelmeztetések azt jelzik, hogy részleges adatok hiányoznak akiszolgálóról, amelyről figyelmeztetést kap. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Az adatgyűjtés és -figyelés letiltása az Azure AD Connect Health szolgáltatásban
Az Azure AD Connect Health lehetővé teszi, hogy leállítsa az adatgyűjtést minden egyes figyelt kiszolgálóvagy egy figyelt szolgáltatás példánya számára. Például leállíthatja az adatgyűjtést az Egyes ADFS (Active Directory összevonási szolgáltatások) kiszolgálók, amelyek az Azure AD Connect Health figyelése. Az Azure AD Connect Health használatával figyelt teljes ADFS-példány adatgyűjtését is leállíthatja. Ha úgy dönt, hogy ezt teszi, a megfelelő kiszolgálók törlődnek az Azure AD Connect Health portálról, az adatgyűjtés leállítása után. 

>[!IMPORTANT]
> Az Azure AD globális rendszergazdai jogosultságainak vagy az RBAC közreműködői szerepkörének az Azure AD Connect Health figyelő kiszolgálóinak törléséhez szükséges.
>
> Egy kiszolgáló vagy szolgáltatáspéldány eltávolítása az Azure AD Connect Health nem visszafordítható művelet. 

### <a name="what-to-expect"></a>Mire számítsunk?
Ha leállítja az adatgyűjtést és a figyelést egy adott figyelt kiszolgálóvagy egy figyelt szolgáltatás példánya számára, vegye figyelembe a következőket:

- Ha töröl egy figyelt szolgáltatás egy példányát, a példány törlődik az Azure AD Connect állapotfigyelő szolgáltatás listájáról a portálon. 
- Ha töröl egy figyelt kiszolgálót vagy egy figyelt szolgáltatás egy példányát, az állapotfigyelő ügynök nem lesz eltávolítva vagy eltávolítva a kiszolgálókról. Az állapotügynök úgy van beállítva, hogy ne küldjön adatokat az Azure AD Connect Health. Manuálisan el kell távolítania az állapotügynököt a korábban figyelt kiszolgálókon.
- Ha a lépés végrehajtása előtt nem távolítja el az állapotügynököt, hibaesemények jelenhetnek meg az állapotügynökhöz kapcsolódó kiszolgáló(ka)n.
- A figyelt szolgáltatás példányához tartozó összes adat törlődik a Microsoft Azure adatmegőrzési szabályzatának szerint.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>A figyelt szolgáltatás egy példányának adatgyűjtésének és figyelésének letiltása
Tekintse [meg, hogyan távolíthat el egy szolgáltatáspéldányt az Azure AD Connect Health szolgáltatásból.](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Figyelt kiszolgáló adatgyűjtésének és figyelésének letiltása
Ebből [a témakörből megtudhatja, hogy miként távolíthat el egy kiszolgálót az Azure AD Connect Health szolgáltatásból.](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Az Azure AD Connect Health összes figyelt szolgáltatásának adatgyűjtésének és figyelésének letiltása
Az Azure AD Connect Health is lehetővé teszi, hogy állítsa le **az** adatgyűjtést a bérlőben regisztrált szolgáltatások. Javasoljuk, hogy a művelet megkezdése előtt alaposan fontolja meg és ismerje el az összes globális rendszergazdát. A folyamat megkezdése után a Connect Health service leállítja az összes szolgáltatás adatainak fogadását, feldolgozását és jelentését. A Connect Health szolgáltatás meglévő adatait legfeljebb 30 napig őrizzük meg.
Ha le szeretné állítani egy adott kiszolgáló adatgyűjtését, kövesse az adott kiszolgálók törlésének lépéseit. A bérlői szintű adatgyűjtés leállításához kövesse az alábbi lépéseket az adatgyűjtés leállításához és a bérlő összes szolgáltatásának törléséhez.

1. Kattintson az **Általános beállítások gombra** a fő panel konfigurációja alatt. 
2. Kattintson az **Adatgyűjtés leállítása** gombra a penge tetején. A bérlői konfigurációs beállítások egyéb beállításai a folyamat indításakor le lesznek tiltva.  
 
   ![Adatgyűjtés leállítása](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Biztosítsa az adatgyűjtés ek leállítása által érintett fedélzeti szolgáltatások listáját. 
4. Adja meg a pontos bérlői nevet a **Törlés** művelet gomb engedélyezéséhez
5. Kattintson a **Törlés** gombra az összes szolgáltatás törlésének elindításához. A Connect Health leállítja a fedélzeti szolgáltatásokból küldött adatok fogadását, feldolgozását és jelentését. Az egész folyamat akár 24 órát is igénybe vehet. Figyelje meg, hogy ez a lépés nem visszafordítható. 
6. A folyamat befejezése után többé nem fog látni regisztrált szolgáltatásokat a Connect Health szolgáltatásban. 

   ![Az adatgyűjtés leállítása után](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Az adatgyűjtés és -figyelés újbóli engedélyezése az Azure AD Connect Health szolgáltatásban
Ha újra engedélyezni szeretné a figyelést az Azure AD Connect Health szolgáltatásban egy korábban törölt figyelt szolgáltatáshoz, el kell távolítania, és újra kell [telepítenie az állapotfigyelő t](how-to-connect-health-agent-install.md) az összes kiszolgálón.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Az adatgyűjtés és -figyelés újbóli engedélyezése az összes figyelt szolgáltatás esetében

A bérlői szintű adatgyűjtés az Azure AD Connect Health szolgáltatásban folytatható. Javasoljuk, hogy a művelet megkezdése előtt alaposan fontolja meg és ismerje el az összes globális rendszergazdát.

>[!IMPORTANT]
> A következő lépések 24 órás letiltási művelet után lesznek elérhetők.
> Az adatgyűjtés engedélyezése után a connect health-ben bemutatott betekintési és figyelési adatok nem jelennek meg a korábban gyűjtött örökölt adatok. 

1. Kattintson az **Általános beállítások gombra** a fő panel konfigurációja alatt. 
2. Kattintson az **Adatgyűjtés engedélyezése** gombra a panel tetején. 
 
   ![Az adatgyűjtés engedélyezése](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Adja meg a pontos bérlőnevét az Engedélyezés gomb **aktiválásához.**
4. Kattintson az **Engedélyezés** gombra, ha engedélyt szeretne adni az adatgyűjtésre a Connect Health szolgáltatásban. A módosítás hamarosan érvénybe lép. 
5. Kövesse a [telepítési folyamatot](how-to-connect-health-agent-install.md) az ügynök újratelepítéséhez a figyelni szükséges kiszolgálókon, és a szolgáltatások jelen lesznek a portálon.  


## <a name="next-steps"></a>További lépések
* [A Microsoft adatvédelmi irányelveinek áttekintése az Adatvédelmi központban](https://www.microsoft.com/trustcenter)
* [Az Azure AD Connect és a felhasználók adatainak védelme](reference-connect-user-privacy.md)

