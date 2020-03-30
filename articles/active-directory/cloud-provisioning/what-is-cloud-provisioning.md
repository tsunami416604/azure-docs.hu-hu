---
title: Mi az Azure AD Connect felhőkiépítés. | Microsoft Docs
description: Az Azure AD Connect felhőalapú kiépítést ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050603"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?
Az Azure AD Connect felhőalapú kiépítése egy új Microsoft-ügynök, amelynek célja a felhasználók, csoportok és kapcsolattartók Azure AD-vel való szinkronizálása érdekében a hibrid identitásra vonatkozó célok elérése és megvalósítása.  Az Azure AD Connect szinkronizálása mellett használható, és a következő előnyöket biztosítja:
    
- Az Azure AD-bérlőkkel való szinkronizálás támogatása egy többerdős leválasztott Active Directory-erdőkörnyezetből: A gyakori forgatókönyvek közé tartozik az egyesülés i & felvásárlás, ahol a beszerzett vállalat AD-erdői el vannak különítve az anyavállalat AD-erdőitől és a korábban több AD-erdővel rendelkező vállalatoktól.
- Egyszerűsített telepítés a könnyű kiépítési ügynökök: Az ügynökök egy hídként az AD az Azure AD, az összes szinkronizálási konfiguráció a felhőben kezelt. 
- Több kiépítési ügynökök segítségével egyszerűsítheti a magas rendelkezésre állású központi telepítések, különösen fontos a szervezetek támaszkodó jelszókivonat-szinkronizálás az AD az Azure AD.


![Mi az az Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Miben különbözik az Azure AD Connect felhőalapú kiépítése az Azure AD Connect szinkronizálásától?
Az Azure AD Connect felhőalapú kiépítésével az AD-ből az Azure AD-be történő kiépítés a Microsoft Online Services-ben van vezénylve. Egy szervezetnek csak a helyszíni és az IaaS-üzemeltetésű környezetben kell telepítenie egy könnyű ügynök, amely hídként működik az Azure AD és az AD között. A létesítési konfiguráció az Azure AD-ben tárolódik, és a szolgáltatás részeként kezeli.

Az alábbi táblázat az Azure AD Connect és az Azure AD Connect felhőalapú kiépítésének összehasonlítását tartalmazza:

| Szolgáltatás | Az Azure Active Directory Connect szinkronizálása| Az Azure Active Directory Connect felhőalapú kiépítése |
|:--- |:---:|:---:|
|Csatlakozás egyetlen helyszíni AD-erdőhöz|● |● |
| Csatlakozás több helyszíni AD-erdőhöz |● |● |
| Csatlakozás több leválasztott helyszíni AD-erdőhöz | |● |
| Könnyű ügynök telepítési modell | |● |
| Több aktív ügynök a magas rendelkezésre állás érdekében | |● |
| Csatlakozás LDAP-könyvtárakhoz|●| | 
| Felhasználói objektumok támogatása |● |● |
| Csoportobjektumok támogatása |● |● |
| Kapcsolatobjektumok támogatása |● |● |
| Eszközobjektumok támogatása |● | |
| Attribútumfolyamatok alapvető testreszabásának engedélyezése |● |● |
| Az Exchange online attribútumai |● |● |
| Kiterjesztési attribútumok szinkronizálása 1-15 |● |● |
| Ügyfél által definiált AD-attribútumok szinkronizálása (címtárbővítmények) |● | |
| A jelszókivonat-szinkronizálás támogatása |●|●|
| Átmenő hitelesítés támogatása |●||
| Összevonás támogatása |●|●|
| Közvetlen egyszeri bejelentkezés|● |●|
| Támogatja a tartományvezérlőre történő telepítést |● |● |
| A Windows Server 2012 és a Windows Server 2012 R2 támogatása |● |● |
| Szűrés tartományokon/ous-okon/csoportokon |● |● |
| Objektumok attribútumértékeinek szűrése |● | |
| A minimális attribútumkészletek szinkronizálásának engedélyezése (MinSync) |● |● |
| A folyamból az attribútumok eltávolításának engedélyezése az AD-ből az Azure AD-ba |● |● |
| Az attribútumfolyamok speciális testreszabásának engedélyezése |● | |
| A visszaírás támogatása (jelszavak, eszközök, csoportok) |● | |
| Az Azure AD tartományi szolgáltatások támogatása|● | |
| [Az Exchange hibrid visszaírása](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Több mint 50 000 objektum támogatása AD-tartományonként |● | |

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Felhőkiépítés telepítése](how-to-install.md)
