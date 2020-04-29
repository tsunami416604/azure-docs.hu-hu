---
title: Mi az Azure AD Connect Cloud kiépítés. | Microsoft Docs
description: Ismerteti Azure AD Connect felhő üzembe helyezését.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80050603"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?
Azure AD Connect a felhő-kiépítés egy új Microsoft-ügynök, amely a hibrid identitási célok teljesítésére és a felhasználók, csoportok és névjegyek Azure AD-be való szinkronizálására szolgál.  Azure AD Connect szinkronizálás mellett is használható, és a következő előnyöket biztosítja:
    
- Azure AD-bérlők többerdős kapcsolattal való szinkronizálásának támogatása Active Directory Forest-környezetből: a gyakori forgatókönyvek közé tartozik az egyesítés & a beszerzés, ahol a beszerzett vállalat AD-erdők el vannak különítve a szülő vállalat AD-erdőkből és olyan vállalatoktól, amelyeknek történelmileg több AD-erdője van.
- Egyszerűsített telepítés kis teljesítményű kiépítési ügynökökkel: az ügynökök az AD-ből az Azure AD-ből származó hidakként működnek, és a felhőben kezelt összes szinkronizálási konfigurációval rendelkeznek. 
- Több kiépítési ügynök használható a magas rendelkezésre állású üzemelő példányok leegyszerűsítéséhez, különösen kritikus fontosságú az AD és az Azure AD közötti jelszó-kivonatolási szinkronizálást használó szervezetek számára.


![Mi az az Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Miben különbözik Azure AD Connect felhőalapú kiépítés Azure AD Connect szinkronizálástól?
Azure AD Connect felhőalapú kiépítés révén az AD-ből az Azure AD-be való kiépítés a Microsoft Online Services szolgáltatásban történik. Egy szervezetnek csak a helyszíni és a IaaS üzemeltetett környezetében kell üzembe helyeznie egy egyszerű ügynököt, amely az Azure AD és az AD közötti hidat képez. A létesítési konfiguráció az Azure AD-ben tárolódik, és a szolgáltatás részeként van kezelve.

Az alábbi táblázat összehasonlítja a Azure AD Connect és a Azure AD Connect Cloud kiépítés között:

| Szolgáltatás | Azure Active Directory Connect szinkronizálása| Felhőalapú kiépítés Azure Active Directory Connect |
|:--- |:---:|:---:|
|Csatlakozás egyetlen helyszíni AD-erdőhöz|● |● |
| Csatlakozás több helyszíni AD-erdőhöz |● |● |
| Kapcsolódás több leválasztott helyszíni AD-erdőhöz | |● |
| Egyszerűsített ügynök telepítési modellje | |● |
| Több aktív ügynök a magas rendelkezésre állás érdekében | |● |
| Kapcsolódás LDAP-címtárakhoz|●| | 
| Felhasználói objektumok támogatása |● |● |
| Csoport objektumainak támogatása |● |● |
| Kapcsolattartási objektumok támogatása |● |● |
| Eszközök objektumainak támogatása |● | |
| Alapvető Testreszabás engedélyezése az attribútumok folyamataihoz |● |● |
| Sychronize Exchange Online-attribútumok |● |● |
| A bővítmény attribútumainak szinkronizálása 1-15 |● |● |
| Ügyfél által definiált AD-attribútumok (címtárszolgáltatás-bővítmények) szinkronizálása |● | |
| Jelszó-kivonat szinkronizálásának támogatása |●|●|
| Az átmenő hitelesítés támogatása |●||
| Összevonás támogatása |●|●|
| Közvetlen egyszeri bejelentkezés|● |●|
| Támogatja a tartományvezérlőre történő telepítést |● |● |
| A Windows Server 2012 és a Windows Server 2012 R2 támogatása |● |● |
| Szűrés tartományokon/szervezeti egységeken/csoportokon |● |● |
| Objektumok szűrése attribútum értékeit |● | |
| A minimális attribútumkészletek szinkronizálásának engedélyezése (MinSync) |● |● |
| A folyamból az attribútumok eltávolításának engedélyezése az AD-ből az Azure AD-ba |● |● |
| Az attribútumfolyamok speciális testreszabásának engedélyezése |● | |
| Visszaírási-támogatás (jelszavak, eszközök, csoportok) |● | |
| Azure AD Domain Services támogatás|● | |
| [Hibrid Exchange-visszaírási](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Több mint 50 000 objektum támogatása AD-tartományban |● | |

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [A felhő kiépítés telepítése](how-to-install.md)
