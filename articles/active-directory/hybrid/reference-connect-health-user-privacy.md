---
title: Az Azure AD Connect Health és a felhasználói adatok védelme |} A Microsoft Docs
description: Ez a dokumentum ismerteti a felhasználói adatok védelme az Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0a7dc5152bb1ede5f7311e74815a6d58782d32a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199323"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Felhasználói adatok védelme és az Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk az Azure AD Connect Health és a felhasználói adatvédelem foglalkozik.  Az Azure AD Connect és a felhasználó adatvédelmi információkat lásd: a cikk [Itt](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Felhasználói adatvédelem besorolás
Az Azure AD Connect Health lép át a **adatfeldolgozó** GDPR besorolási kategóriája. Egy adatfeldolgozó folyamatot, mint a szolgáltatás adatokat feldolgozó szolgáltatásokat nyújt a kulcs partnerek és a végfelhasználók a fogyasztók. Az Azure AD Connect Health nem hozhat létre a felhasználói adatokat, és nincs független vezérléssel rendelkezik, milyen személyes adatokat gyűjteni, és azok felhasználási módjáról. Adatok beolvasása, aggregáció, elemzés és jelentéskészítés az Azure AD Connect Health meglévő helyszíni adatok alapján. 

## <a name="data-retention-policy"></a>Adatmegőrzési szabályzat
Az Azure AD Connect Health nem jelentések készítése, analitikai vagy tárhat fel összefüggéseket a 30 napos időszak letelte után. Ezért az Azure AD Connect Health nem tárolja, dolgoz fel, vagy megőrizni adatokat 30 napos időszak letelte után. Ez a kialakítás az általános adatvédelmi rendelet előírásainak, a Microsoft adatvédelmi előírásoknak és az adatmegőrzési házirendek az Azure AD megfelelő. 

Az aktív kiszolgáló **az állapotfigyelő szolgáltatás adatai nem naprakészek** **hiba** több mint 30 egymást követő napon javasoljuk, hogy nincsenek adatok adott időtartományon elérte a Connect Health vonatkozó riasztásokat. Ezek a kiszolgálók letiltásra kerül, és nem jelenik meg a Connect Health portálon. A kiszolgálók ismételt engedélyezéséhez el kell távolítania és [telepítse újra a health agent](how-to-connect-health-agent-install.md). Vegye figyelembe, hogy ez nem vonatkozik a **figyelmeztetések** az ugyanolyan típusú riasztást. Figyelmeztetés azt jelzi, hogy részleges adatokat a kiszolgálóról, amelyekről riasztást kap a hiányzó. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Tiltsa le az adatgyűjtést és a monitorozás az Azure AD Connect Health
Az Azure AD Connect Health lehetővé teszi, hogy az egyes figyelt kiszolgálókhoz vagy a figyelt szolgáltatás egy példányának adatgyűjtés leállítása. Például állítsa le az adatgyűjtést az Azure AD Connect Health használatával figyelt egyedi ADFS (Active Directory összevonási szolgáltatások)-kiszolgálókhoz. Adatok gyűjtése a teljes megfigyelés alatt áll az Azure AD Connect Health AD FS-példány is leállíthatja. Ha ezt választja, a hozzájuk tartozó kiszolgálók adatgyűjtés leállítása után az Azure AD Connect Health portálon lesznek törölve. 

>[!IMPORTANT]
> Szüksége vagy az Azure AD globális rendszergazdai jogosultsággal, vagy a közreműködő szerepkört az RBAC-ben delete figyelt kiszolgálók az Azure AD Connect Health.
>
> Az Azure AD Connect Health kiszolgáló vagy szolgáltatáspéldány eltávolítása nem vonható vissza a művelet. 

### <a name="what-to-expect"></a>Mi várható?
Ha leállítja az adatgyűjtést és a figyelt a különálló kiszolgáló vagy egy felügyelt szolgáltatás egy példányának figyelését, vegye figyelembe a következőket:

- Ha töröl egy figyelt szolgáltatás egy példányának, a példányt eltávolítjuk az Azure AD Connect Health figyelési szolgáltatás listájához a portálon. 
- Ha törli a figyelt kiszolgáló vagy egy felügyelt szolgáltatás egy példányának, a Health-ügynök nem eltávolításra vagy eltávolítja a kiszolgálókról. A Health-ügynök nem az, hogy adatokat küldjön az Azure AD Connect Health van konfigurálva. Manuálisan távolítsa el a Health Agent a korábban megfigyelt kiszolgálókon kell.
- Ez a lépés végrehajtása előtt nem távolította el a Health-ügynököt, hibaesemények a Health Agent kapcsolatos ko jelenhet meg.
- A figyelt szolgáltatás példányához tartozó összes adat törlődik a Microsoft Azure adatmegőrzési szabályzatának megfelelően.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Tiltsa le az adatgyűjtést és a egy figyelt szolgáltatás egy példányának figyelését
Lásd: [szolgáltatáspéldány eltávolítása az Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Tiltsa le az adatgyűjtést és a figyelt kiszolgálók figyelése
Lásd: [kiszolgáló eltávolítása az Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Tiltsa le az adatgyűjtés és az Azure AD Connect Health összes figyelt szolgáltatás figyelése
Az Azure AD Connect Health is lehetővé teszi, hogy állítsa le az adatgyűjtést, **összes** szolgáltatások regisztrálva a bérlővel. Javasoljuk, hogy alapos megfontolás, és minden globális rendszergazda teljes nyugtázása a műveletek előtt. A folyamat megkezdése után a Connect Health szolgáltatás leáll, fogadását, feldolgozását és jelentéskészítési bármilyen típusú és minden szolgáltatás. Connect Health service-ben meglévő adatok legfeljebb 30 napig lesznek megőrizve.
Ha szeretné leállítani az adatgyűjtést az adott kiszolgáló, kövesse a lépéseket az egyes kiszolgálók törlését. Tenant-wise adatgyűjtés leállítása, kövesse az alábbi lépések végrehajtásával állítsa le az adatgyűjtést, és törölje a bérlő összes szolgáltatást.

1.  Kattintson a **általános beállítások** konfigurációja a fő panelen. 
2.  Kattintson a **adatgyűjtés leállítása** gombra a panel felső részén. A többi beállítást a bérlő konfigurációs beállításainak letiltásra kerül, amint a folyamat elindul.  
 
 ![Adatgyűjtés leállítása](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3.  Győződjön meg arról leállítása az adatok gyűjtemények által érintett előkészítve a szolgáltatások listájába. 
4.  Adja meg a pontos bérlő nevét ahhoz, hogy a **törlése** műveletgombként
5.  Kattintson a **törlése** az összes szolgáltatást a törlés aktiválásához. Connect Health fogadása, feldolgozás, az előkészítést szolgáltatások által küldött adatokat reporting le fog állni. A teljes folyamatot, akár 24 órát is igénybe vehet. Figyelje meg, hogy ez a lépés nem vonható vissza. 
6.  A folyamat befejezése után többé nem látják regisztrált a Connect Health szolgáltatások. 

 ![Miután leállította az adatgyűjtést](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Engedélyezze újra az adatgyűjtés és -monitorozás az Azure AD Connect Health
Monitorozás az Azure AD Connect Health for egy korábban törölt figyelt szolgáltatás újbóli engedélyezéséhez el kell távolítania és [telepítse újra a health agent](how-to-connect-health-agent-install.md) az összes kiszolgálón.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Engedélyezze újra az adatgyűjtés és az összes figyelt szolgáltatás figyelése

Az Azure AD Connect Health tenant-Wise adatgyűjtés tudja folytatni. Javasoljuk, hogy alapos megfontolás, és minden globális rendszergazda teljes nyugtázása a műveletek előtt.

>[!IMPORTANT]
> Az alábbi lépéseket az 24 órányi művelet letiltása után lesz elérhető.
> Miután engedélyezte az adatgyűjtést, az itt bemutatott insight és a figyelési adatokat a Connect Health nem jelenik meg előtt gyűjtött régi adatokat. 

1.  Kattintson a **általános beállítások** konfigurációja a fő panelen. 
2.  Kattintson a **engedélyezni az adatgyűjtést** gombra a panel felső részén. 
 
 ![Az adatgyűjtés engedélyezése](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3.  Adja meg a pontos bérlőneve aktiválása a **engedélyezése** gombra.
4.  Kattintson a **engedélyezése** gombra kattintva adjon engedélyt az adatgyűjtés a Connect Health szolgáltatásban. Hamarosan a módosítás lépnek érvénybe. 
5.  Kövesse a [telepítési folyamat](how-to-connect-health-agent-install.md) újratelepíteni az ügynököt a figyelni a kiszolgálókon és a szolgáltatások lesznek jelen a portálon.  


## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy szabályzat a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
* [Az Azure AD Connect és a felhasználói adatok védelme](reference-connect-user-privacy.md)

