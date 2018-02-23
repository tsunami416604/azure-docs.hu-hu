---
title: "Az Azure AD Connect állapotát és általános Data Protection szabályozás |} Microsoft Docs"
description: "Ez a dokumentum ismerteti az beszerzése az Azure AD Connect GDPR előírásainak."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>GDPR megfelelőség és az Azure AD Connect Health 

[Általános Data Protection szabályozás (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) Európai Unió adatok védelmét és adatvédelmi törvény van. A GDPR támaszt a vállalatok új szabályokat, állami intézményekhez, nem nyereség és más szervezetek, termékek és szolgáltatások biztosítson munkatársai az EU, vagy ha az adatgyűjtés és -elemzés EU lakosai kötve. 

Microsoft-termékek és szolgáltatások elérhetők ma segítséget nyújtanak a GDPR követelményeknek. További információk a Microsoft Privacy Policy házirend [biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter).

Az Azure AD Connect Health figyeli a helyszíni identitás infrastruktúra és a szinkronizálási szolgáltatás. Azt is biztosít elemzések és a felületek riasztásokat. A Microsoft elkötelezte magát a GDPR-megfelelőségi felhőszolgáltatás, előfordulhat, hogy 2018 kényszerítési megkezdésekor, és adja meg a szerződéses kötelezettségeket a GDPR kapcsolatos biztosítékok. 

>[!NOTE] 
> Ez a cikk ismerteti az Azure AD Connect Health GDPR megfelelőségi. Az Azure AD Connectben GDPR megfelelőségi információkért lásd: [GDPR megfelelőség és az Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>GDPR besorolás
Az Azure AD Connect Health beleesik a **adatfeldolgozó** GDPR besorolási kategóriáját. A processzor feldolgozási folyamat, mint a szolgáltatás az adatfeldolgozás szolgáltatásokat nyújt kulcs partnerek és a záró fogyasztók. Az Azure AD Connect Health nem hozhat létre a felhasználói adatokat, és rendelkezik a független befolyással milyen személyes adatok gyűjtése, és hogyan használja fel azokat. Adatok beolvasása, összesítési, elemzés és a jelentéskészítés az Azure AD Connect Health meglévő helyszíni adatok alapján. 

## <a name="data-retention-policy"></a>Adatmegőrzési szabályzat
Az Azure AD Connect Health nem-jelentések készítéséhez, hajtsa végre az analytics vagy 30 napon túl áttekintést adnak. Ezért az Azure AD Connect Health nem tárolja, feldolgozni, és bármely 30 napon túl adatok megőrzése mellett. Ez a kialakítás megfelel a GDPR szabályzat, a Microsoft adatvédelmi megfelelőségi szabályzatok és az adatmegőrzési házirendek az Azure AD. 
 
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
Lásd: [kiszolgáló eltávolítása az Azure AD Connect Health](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Adatgyűjtés és a figyelt szolgáltatás egy példánya figyelésének letiltása
Lásd: [egy szolgáltatáspéldány eltávolítása az Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Engedélyezze újra az adatok gyűjtésével és az Azure AD Connect Health ellenőrzése
Egy korábban törölt figyelt szolgáltatás számára az Azure AD Connect Health ellenőrzése újbóli engedélyezéséhez el kell távolítania és [telepítse újra a health agent](active-directory-aadconnect-health-agent-install.md) a kiszolgálókon.


## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy házirendet, a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
* [Az Azure AD Connect és GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Az Azure AD Connect Health műveletei](active-directory-aadconnect-health-operations.md)
