---
title: Hibrid identitástervezés – címtárszinkronizálási követelmények Azure | Microsoft dokumentumok
description: Határozza meg, milyen követelmények szükségesek az összes felhasználó szinkronizálásához az on=premises és a felhő között a vállalat számára.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381161"
---
# <a name="determine-directory-synchronization-requirements"></a>Címtár-szinkronizálási követelmények meghatározása
A szinkronizálás arról szól, hogy a felhasználók a helyszíni identitásuk alapján identitást biztosítsanak a felhőben. Függetlenül attól, hogy szinkronizált fiókot fognak-e használni a hitelesítéshez vagy az összevont hitelesítéshez, a felhasználóknak továbbra is rendelkezniük kell identitással a felhőben.  Ezt az identitást rendszeresen meg kell őrizni és frissíteni kell.  A frissítések számos formát ölthetnek, a címmódosításoktól a jelszómódosításokig.  

Első ként értékelje ki a szervezetek helyszíni identitáskezelési megoldás és a felhasználói követelmények. Ez a kiértékelés fontos a felhasználói identitások létrehozásának és karbantartásának műszaki követelményeinek meghatározásához.  A szervezetek többsége számára az Active Directory a helyszínen található, és a helyszíni könyvtár lesz, amelyből a felhasználók szinkronizálva lesznek, de bizonyos esetekben ez nem így lesz.  

Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Van egy AD erdő, több, vagy nincs?
  
  * Hány Azure AD-könyvtárat fog szinkronizálni?
    
    1. Szűrést használ?
    2. Több Azure AD Connect-kiszolgálót tervez?
* Jelenleg rendelkezik a helyszíni szinkronizálási eszközzel?
  
  * Ha igen, akkor a felhasználók rendelkeznek az identitások virtuális könyvtárával/integrációjával?
* Van más helyszíni könyvtára, amelyet szinkronizálni szeretne (pl. LDAP Könyvtár, HR-adatbázis stb.)?
  * Fogsz csinálni valami GALSync-et?
  * Milyen állapotban vannak az UPN-ek a szervezetben? 
  * Van egy másik könyvtár, amely a felhasználók hitelesítése ellen?
  * A vállalat a Microsoft Exchange programot használja?
    * Tervezik-e, hogy hibrid cseretelepítést végeznek?

Most, hogy van egy ötlete a szinkronizálási követelményekről, meg kell határoznia, hogy melyik eszköz felel meg ezeknek a követelményeknek.  A Microsoft számos eszközt biztosít a címtár-integráció és -szinkronizálás megvalósításához.  További információkért tekintse meg a [hibrid identitáskönyvtár-integrációs eszközök összehasonlító tábláját.](plan-hybrid-identity-design-considerations-tools-comparison.md) 

Most, hogy rendelkezik a szinkronizálási követelmények és az eszköz, amely ezt a vállalat számára, ki kell értékelnie az alkalmazásokat, amelyek ezeket a címtárszolgáltatásokat. Ez a kiértékelés fontos, hogy meghatározza a műszaki követelmények integrálása ezeket az alkalmazásokat a felhőbe. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Ezek az alkalmazások átkerülnek a felhőbe, és a könyvtárat használják?
* Vannak-e olyan speciális attribútumok, amelyeket szinkronizálni kell a felhővel, hogy ezek az alkalmazások sikeresen használhassák őket?
* Át kell írni ezeket az alkalmazásokat, hogy kihasználhassák a felhőhitelesítés előnyeit?
* Ezek az alkalmazások továbbra is a helyszínen maradnak, miközben a felhasználók a felhőalapú identitás használatával érik el őket?

Meg kell határoznia a biztonsági követelményeket és a korlátozásokat a címtár-szinkronizálást is. Ez a kiértékelés fontos, hogy egy listát a követelmények, amelyek szükségesek lesznek a felhasználói identitások létrehozásához és karbantartásához a felhőben. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol található a szinkronizálási kiszolgáló?
* Domain-csatlakozás lesz?
* A kiszolgáló egy tűzfal mögötti korlátozott hálózaton, például DMZ-n lesz elhelyezve?
  * Meg tudja nyitni a szinkronizálás támogatásához szükséges tűzfalportokat?
* Rendelkezik vész-helyreállítási tervvel a szinkronizálási kiszolgálóhoz?
* Rendelkezik a megfelelő engedélyekkel rendelkező fiókkal minden olyan erdőhöz, amellyel szinkronizálni szeretne?
  * Ha a vállalat nem tudja a választ erre a kérdésre, tekintse át a "Jelszó-szinkronizálás engedélyei" című témakörben [az Azure Active Directory-szinkronizálási szolgáltatás telepítése](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) című témakörben található szakaszt, és állapítsa meg, hogy rendelkezik-e már ilyen engedélyekkel rendelkező fiókkal, vagy létre kell hoznia egyet.
* Ha van mutli-erdő szinkronizálás a szinkronizálási kiszolgáló képes eljutni az egyes erdők?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. [Határozza meg az incidensválasz követelményeit,](plan-hybrid-identity-design-considerations-incident-response-requirements.md) amelyek átmennek a rendelkezésre álló lehetőségeken. A kérdések megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg legjobban üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
[Többtényezős hitelesítési követelmények meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)

