---
title: Hibrid identitás Tervező - directory sync követelmények Azure |} Microsoft Docs
description: Milyen követelmények szükségesek szinkronizálásához között a felhasználók azonosítása az = a helyszíni és a vállalati felhő.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: d6749960806e858909f42c6ecccd445ba8d5ec00
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801561"
---
# <a name="determine-directory-synchronization-requirements"></a>Címtár-szinkronizálás követelmények meghatározása
Szinkronizálás legyen biztosítható a felhasználók a felhőben, a helyszíni identitás alapján identitást. -E a hitelesítés és az összevont hitelesítés szinkronizált fiókkal fogja használják, a felhasználók továbbra is kell rendelkeznie az identitás a felhőben.  Ezzel az identitással kell tartani és rendszeresen frissülnek.  A frissítések számos formája, a jelszó-változtatásának cím módosításai.  

Indítsa el a szervezet helyszíni identitás megoldás és a felhasználó szükséges követelmények értékelésekor. Ez a kiértékelés fontos, hogy milyen felhasználói identitások létrejön és adatait a felhőben a műszaki követelményeinek meghatározását.  A szervezetek többsége, az Active Directory a helyszíni és a helyszíni címtár, hogy a felhasználók által rendszer szinkronizálja a, azonban néhány esetben ez nem lesz az eset.  

Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Rendelkezik egy AD-erdő, több vagy nincs?
  
  * Hány Azure AD-címtártól fog meg kell szinkronizálja a névjegyadatokat?
    
    1. Használ szűrés?
    2. Van több Azure AD Connect-kiszolgálók tervezett?
* Jelenleg rendelkezik a szinkronizálás a helyszíni eszköze?
  
  * Ha igen, ha a felhasználók egy virtuális könyvtárat és-integráció identitások használ a felhasználók?
* Van bármilyen más címtár a helyszínen (pl. LDAP-címtárhoz, személyzeti adatbázis stb) szinkronizálni kívánt?
  * Lesz, aki a GALSync?
  * Mi az a szervezet UPN-EK aktuális állapotát? 
  * Van egy másik címtárat, hogy a felhasználók hitelesítése?
  * A vállalat használ a Microsoft Exchange?
    * Ezek fogja, hogy az exchange hibrid telepítés?

Most, hogy segítse az szinkronizálási követelményekről, meg kell határoznia, melyik eszközt, a megfelelő egy ezek a követelmények teljesítéséhez.  Microsoft címtár-integráció és a szinkronizálás elvégzéséhez eszközöket biztosít.  Tekintse meg a [hibrid Identity directory integration eszközök összehasonlító táblázatában](active-directory-hybrid-identity-design-considerations-tools-comparison.md) további információt. 

Most, hogy a szinkronizálási követelmények és az eszközt, amely a rendszer ehhez a vállalat, fel kell mérnie az alkalmazásokat, amelyek a címtárszolgáltatások. Ez a kiértékelés fontos, hogy ezeket az alkalmazásokat a felhőben való integrálásához műszaki követelményeinek meghatározása. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Ezek az alkalmazások kerül a felhőben és a címtár?
* Vannak-e különleges attribútumok, amelyek a felhőbe szinkronizálni kell, így ezek az alkalmazások használni tudják őket sikeresen?
* Felhőalapú hitelesítés előnyeinek kihasználása érdekében újra írandó kell ezeket az alkalmazásokat?
* Ezek az alkalmazások továbbra is a helyszíni élő, amíg a felhasználók férhetnek hozzá, azokat a felhőalapú identitás?

Meg kell határoznia a biztonsági követelmények és korlátozások címtár-szinkronizálás is. Ez a kiértékelés fontos, hogy lesz szükség ahhoz, hogy létrehozása és karbantartása a felhő a felhasználói identitások követelmények listáját. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Ha a szinkronizálási kiszolgáló kerülnek?
* Lesz tartományhoz csatlakoztatott?
* A kiszolgáló helyezkednek el a korlátozott jogú hálózatban, például egy Szegélyhálózaton tűzfal mögött található?
  * Meg fogja tudni a szinkronizálás támogatásához szükséges portok megnyitása?
* A vész-helyreállítási terv a szinkronizálási kiszolgáló van?
* Rendelkezik egy fiókot az összes erdőben megfelelő engedélyekkel a synch szeretné?
  * Ha a vállalat nem tudja, hogy a válasz a kérdésre, tekintse át a "Jelszó-szinkronizálás engedélyeinek" című cikk [telepítse az Azure Active Directory szinkronizáló szolgáltatás](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) és határozza meg, ha már rendelkezik ezekkel a jogosultságokkal rendelkező fiók, vagy ha kell létrehoznia egyet.
* Ha rendelkezik a mutli-erdő szinkronizálási kérhető le a mindkét erdőben a szinkronizálási kiszolgálót?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról jegyzeteket, és megismerheti a válaszok indokait. [Incidensválasz-követelmények meghatározása](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) az elérhető lehetőségeket ismerteti. Ezen kérdések mely leginkább megfelelő lehetőséget az üzleti megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
[A multi-factor authentication követelmények meghatározása](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)

