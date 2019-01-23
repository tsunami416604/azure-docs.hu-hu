---
title: Hibrid identitás Tervező – Azure címtár-szinkronizálás követelményei |} A Microsoft Docs
description: Azonosíthatja a használati követelményekről szükséges ahhoz, hogy között a felhasználók szinkronizálása az helyi és felhőbeli vállalati =.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
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
ms.openlocfilehash: ad14e443f7176a0acb2da3529348fba452f76dee
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477957"
---
# <a name="determine-directory-synchronization-requirements"></a>Határozza meg a címtár-szinkronizálás követelményei
Szinkronizálási szól az identitás a felhőben, a helyszíni identitás alapján biztosítanak a felhasználók számára. E szinkronizált fiókot fogja használni, a hitelesítés és az összevont hitelesítés, a felhasználók továbbra is kell rendelkeznie az identitás a felhőben.  Ezt az identitást kell karbantartani, és rendszeresen frissülnek.  A frissítések számos formája, a cím módosításainak a jelszó módosítására.  

Indítsa el a szervezetek számára a helyszíni identitáskezelési megoldás és a felhasználói követelmények kiértékelésekor. Ezt a próbaidőszakot fontos, hogy milyen felhasználói identitások létrejön és a felhőben fenntartott vonatkozó technikai követelmények meghatározása.  A szervezetek többsége az Active Directory a helyszíni és a helyszíni címtár, amely felhasználó által szinkronizálja a rendszer a, azonban bizonyos esetekben ez nem az eset áll fenn.  

Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Rendelkezik egy AD-erdő, több vagy nincs?
  
  * Hány Azure AD-címtár fogja azt kell szinkronizálja a?
    
    1. Használ szűrés?
    2. Több Azure AD Connect-kiszolgálók tervezett van?
* Jelenleg rendelkezik a szinkronizálás a helyszíni eszköz?
  
  * Ha igen, támogatja a felhasználók számára, ha a felhasználó rendelkezik egy virtuális könyvtárat vagy integrációs identitások?
* Van bármilyen más címtár a helyszínen (pl. LDAP-címtárhoz, HR-adatbázist, és így tovább) szinkronizálni kívánt?
  * Lesz a minden GALSync foglalják?
  * Mi az UPN-EK aktuális állapotát a szervezetben? 
  * Van egy másik címtárban, hogy a felhasználók hitelesítése?
  * A vállalat a Microsoft Exchange használ?
    * Ezek tervezi, hogy az exchange hibrid telepítés?

Most, hogy van egy ötletem a szinkronizálás követelményeiről, meg kell határoznia, melyik eszközt a követelmények teljesítéséhez a megfelelőt.  A Microsoft címtár-integráció és a szinkronizálás végrehajtásához számos eszközt nyújt.  Tekintse meg a [Hybrid Identity directory integrációs eszközök összehasonlító táblázatot](plan-hybrid-identity-design-considerations-tools-comparison.md) további információt. 

Most, hogy a szinkronizálás követelményei és az eszközt, amely érheti el a vállalat, kell kiértékelni az alkalmazásokat, amelyek a címtárszolgáltatások. Ezt a próbaidőszakot fontos, hogy ezeket az alkalmazásokat a felhőbe való integrálásához technikai követelmények meghatározása. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Ezek az alkalmazások kerül a felhőbe, és könyvtárat?
* Vannak-e speciális attribútum használatát, amely a felhőbe kell szinkronizálni, így ezeket az alkalmazásokat a segítségükkel sikeresen?
* Ezeket az alkalmazásokat kell újra írható felhőalapú hitelesítés előnyeinek kihasználása érdekében?
* Ezek az alkalmazások továbbra is a helyszíni élő, miközben a felhasználók férhetnek hozzá őket a felhőbeli identitás használatával?

Meg kell határoznia a biztonsági követelmények és korlátozások a címtár-szinkronizálás is. Ezt a próbaidőszakot fontos lesz szükség ahhoz, hogy létre és tarthatja karban a felhasználói identitások a felhőben követelmények listáját. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Ha a szinkronizálási kiszolgáló kerülnek?
* Lesz, azt a tartományhoz csatlakoztatott?
* A kiszolgáló legyen, mint például a DMZ-t, tűzfal mögött egy korlátozott hálózati?
  * Akkor fogja tudni nyissa meg a szükséges tűzfalportok szinkronizálás támogatásához?
* Rendelkezik egy vész-helyreállítási terv a szinkronizálási kiszolgáló?
* Rendelkezik egy fiókot a megfelelő engedélyekkel az minden olyan erdőben, a synch szeretne?
  * Ha a cég nem tudja a választ a kérdésre, tekintse át a "Jelszó-szinkronizáláshoz engedélyek" részt a cikk [az Azure Active Directory Sync Service telepítése](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) és döntse el, ha már rendelkezik fiókkal az ezeket az engedélyeket, vagy ha szeretne létrehozni egyet.
* Ha az megtalálhatjuk-erdő szinkronizálási tud minden olyan erdőben, a szinkronizálási kiszolgálót?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról feljegyzéseket, és megismerheti a válaszok indokait. [Incidensválasz-követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md) halad keresztül a rendelkezésre álló lehetőségeket. A fenti melyik leginkább megfelelő lehetőséget az üzleti kiválaszthatja kérdések megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
[A multi-factor authentication-követelmények meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

