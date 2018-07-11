---
title: 'Az Azure Active Directory tartományi szolgáltatások: Felfüggesztve tartományok |} A Microsoft Docs'
description: Felügyelt tartomány felfüggesztése és törlése
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: ergreenl
ms.openlocfilehash: 37524fbdf3cd521414a507f6fb67421708343ccd
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934247"
---
# <a name="suspended-domains"></a>Felfüggesztett tartományok
Ha az Azure AD Domain Services nem tudja teljesíteni a felügyelt tartomány egy hosszú ideig, felfüggesztett állapotba helyezni, a felügyelt tartományhoz. Ez a cikk elmagyarázza miért felügyelt tartományok fel vannak függesztve, a felfüggesztés hossza és a felfüggesztett tartomány javítása.


## <a name="overview-of-suspended-domains"></a>Felfüggesztett tartományok áttekintése

![Felfüggesztett tartomány ütemterv](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

A fenti ábra bemutatja, hogyan tartomány fel van függesztve, mennyi ideig lesz felfüggesztve, és végső soron a felügyelt tartomány törlése. Az alábbi szakaszok az okokat, miért lehet felfüggeszteni a tartományhoz, és hogyan újraaktiválására egy felügyelt tartományon.


## <a name="why-are-managed-domains-suspended"></a>Miért felfüggesztve a felügyelt tartományok?

A felügyelt tartományok fel vannak függesztve, amelyek olyan állapotban, amelyben az Azure AD Domain Services tudja felügyelni a tartományhoz. Ezt okozhatja egy hibás Azure AD tartományi szolgáltatásokat, vagy inaktív Azure-előfizetés szükséges erőforrásokat, hogy blokkolja a hozzáférést. Azure AD tartományi szolgáltatásokat, hogy nem tudja teljesíteni a felügyelt tartomány 15 nap után a felfüggeszti az a tartomány.

A pontos okokat, miért sikerült felfüggeszteni a tartomány az alábbiak:
* 15 egymást követő napon a tartományban lévő rendelkeznek legalább egy megtalálható a következő riasztásokat:
   * [AADDS104: Hálózati hiba](active-directory-ds-troubleshoot-nsg.md).
* Az Azure-előfizetése lejárt vagy inaktív


## <a name="what-happens-when-a-domain-is-suspended"></a>Mi történik, ha egy tartomány fel van függesztve?

A tartomány fel van függesztve, az Azure AD Domain Services leállítja a virtuális gépek, amelyek a szolgáltatás a felügyelt tartományra. Ez azt jelenti, hogy már nem készít biztonsági másolatokat, a felhasználók nem tudnak bejelentkezni a tartományba, és az Azure AD szinkronizálási már nem történik.

A tartomány maximum 15 napos felfüggesztési állapotba csak marad. A megfelelő időben történő helyreállítás biztosítása érdekében ajánlott a felfüggesztés minél hamarabb cím.

## <a name="how-do-i-know-if-my-domain-is-suspended"></a>Honnan tudhatom meg, ha a saját tartomány fel van függesztve?
A felügyelt tartományhoz fog kapni egy [riasztás](active-directory-ds-troubleshoot-alerts.md) a az Azure AD tartományi szolgáltatások Health-oldal az Azure Portalon, amely a tartomány felfüggesztve deklarálja. Emellett a tartomány állapota "Felfüggesztett" fogja ellátni.


## <a name="unsuspending-and-restoring-domains"></a>Tartományok unsuspending és visszaállítás

Újraaktiválására egy tartományhoz, a következő lépések szükségesek:

1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon
2. Kattintson a kívánt újraaktiválására tartományon
3. A bal oldali navigációs sávján kattintson **állapota**
4. Kattintson a felfüggesztés riasztás (a riasztás azonosítója lesz AADDS503 vagy AADDS504, felfüggesztése okától függően).
5. Kattintson a a feloldási hivatkozásra a riasztásban, és kövesse a lépéseket, a felfüggesztés feloldása.

A tartomány csak a legutóbbi biztonsági mentés napját állítható vissza. A dátum a legutóbbi biztonsági mentését a felügyelt tartomány állapotának lapján jelenik meg. A legutóbbi biztonsági mentés óta módosításokat unsuspension esetén nem állítható vissza. Emellett az Azure AD Domain Services csak tárolható biztonsági mentések legfeljebb 30 napig. Ha a legutóbbi biztonsági másolatra 30 napnál régebbi, törölni kell a biztonsági mentés, és az Azure AD Domain Services nem tudja egy biztonsági másolatból történő visszaállítását.

## <a name="deleting-domains"></a>Tartomány törlése

A tartomány fel van függesztve, több mint 15 napon keresztül, ha az Azure AD Domain Services törlése miatt tétlen és a tartományi szolgáltatás megakadályozhatják a felügyelt tartományhoz. Már nem díjköteles az Azure AD tartományi szolgáltatásokhoz. A felügyelt tartomány visszaállításához, szüksége lesz hozza létre újból.


## <a name="next-steps"></a>További lépések
- [Riasztások a felügyelt tartományon](active-directory-ds-troubleshoot-alerts.md)
- [További információ az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kapcsolat

Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
