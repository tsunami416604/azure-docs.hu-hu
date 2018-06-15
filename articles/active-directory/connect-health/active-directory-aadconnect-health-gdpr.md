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
ms.openlocfilehash: 5fedbac439636b56da217e7babd30820bce7b342
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931758"
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

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Adatgyűjtés és a figyelt szolgáltatás egy példánya figyelésének letiltása
Lásd: [egy szolgáltatáspéldány eltávolítása az Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Tiltsa le az adatgyűjtést és a figyelt kiszolgáló figyelése
Lásd: [kiszolgáló eltávolítása az Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Adatok gyűjtése és az Azure AD Connect Health összes figyelt szolgáltatások figyelésének letiltása
Az Azure AD Connect Health is lehetővé teszi az adatgyűjtés leállítása **összes** szolgáltatások a bérlő regisztrálva. Javasoljuk, hogy alapos megfontolás és az összes globális rendszergazda teljes nyugtázása a műveletek előtt. A folyamat megkezdése után Connect Health szolgáltatás leáll, fogadására, feldolgozása és a szolgáltatások adatot reporting. A Connect Health szolgáltatásban meglévő adatok legfeljebb 30 napig lesznek megőrizve.
Ha szeretné leállítani az adatgyűjtést a megadott kiszolgáló, kövesse a lépéseket a megadott kiszolgáló törlése. Tenant-wise adatgyűjtés leállításához hajtsa végre az alábbi lépések végrehajtásával állítsa le az adatgyűjtést, és törölje a bérlő összes szolgáltatás.

1.  Kattintson a **általános beállítások** konfigurációja a fő paneljén. 
2.  Kattintson a **adatgyűjtés leállítása** gomb a panel tetején. A többi beállítást a bérlő konfigurációs beállítások letiltásra kerül, amennyiben az a folyamat elindul.  
 
 ![Adatgyűjtés leállítása](./media/active-directory-aadconnect-health-gdpr/gdpr4.png)
  
3.  Adatok gyűjtemények leállítása által érintett előkészítve a szolgáltatások biztosítása. 
4.  Adja meg a pontos bérlő nevét az engedélyezése a **törlése** Akciógomb
5.  Kattintson a **törlése** a törlés, az összes olyan szolgáltatás elindítása. Csatlakozás állapotának fogadásakor, feldolgozás, a előkészítve szolgáltatások által küldött adatokat reporting leáll. A teljes folyamat akár 24 órába is telhet. Láthatja, hogy ez a lépés nem vonható vissza. 
6.  A folyamat befejezése után többé nem jelenik a Connect Health regisztrált szolgáltatások. 

 ![Adatgyűjtés leállítása után](./media/active-directory-aadconnect-health-gdpr/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Engedélyezze újra az adatok gyűjtésével és az Azure AD Connect Health ellenőrzése
Egy korábban törölt figyelt szolgáltatás számára az Azure AD Connect Health ellenőrzése újbóli engedélyezéséhez el kell távolítania és [telepítse újra a health agent](active-directory-aadconnect-health-agent-install.md) a kiszolgálókon.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Engedélyezze újra az adatok gyűjtésével és az összes figyelt szolgáltatás figyelése

Tenant-Wise adatgyűjtést az Azure AD Connect Health folytathatók. Javasoljuk, hogy alapos megfontolás és az összes globális rendszergazda teljes nyugtázása a műveletek előtt.

>[!IMPORTANT]
> Az alábbi lépéseket az 24 órányi művelet letiltása után lesz elérhető.
> Miután engedélyezte az adatgyűjtés, a bemutatott betekintést és a figyelési adatok a Connect Health fog megjelenni, mielőtt gyűjtött régebbi adatokat. 

1.  Kattintson a **általános beállítások** konfigurációja a fő paneljén. 
2.  Kattintson a **adatgyűjtés engedélyezése** gomb a panel tetején. 
 
 ![Az adatgyűjtés engedélyezése](./media/active-directory-aadconnect-health-gdpr/gdpr6.png)
 
3.  Adja meg a pontos bérlő nevét az aktiválja a **engedélyezése** gombra.
4.  Kattintson a **engedélyezése** gombra kattintva adja meg az engedélyt az adatgyűjtés Connect Health szolgáltatásban. A módosítás hamarosan lépnek érvénybe. 
5.  Kövesse a [telepítési folyamat](active-directory-aadconnect-health-agent-install.md) kattintva telepítse újra az ügynököt a kiszolgálók figyelni és a szolgáltatások jelen lesznek a portálon.  


## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy házirendet, a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
* [Az Azure AD Connect és a felhasználói adatok](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

