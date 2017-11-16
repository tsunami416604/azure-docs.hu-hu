---
title: "Frissítés a DirSync és Azure AD Sync |} Microsoft Docs"
description: "Ismerteti, hogyan lehet az Azure AD Connect a DirSync és az Azure AD Sync rendszerről."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 104c2f087a38b3fe006ede4c4d6d283336c3511c
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Frissítse a Windows Azure Active Directory-szinkronizálás és az Azure Active Directory-szinkronizálás
Az Azure AD Connect a legjobb megoldás, ha a helyszíni címtárat az Azure AD-hez és az Office 365-höz szeretné csatlakoztatni. Ez az ideje, mivel ezek az eszközök most már elavultak, és már nem támogatott 2017. április 13-től a Windows Azure Active Directory-szinkronizálás (DirSync) vagy az Azure AD Sync az Azure AD Connect frissítése.

A két azonosító szinkronizálási eszközt, amely elavult érhető el a egyerdős ügyfelek (DirSync) és Többerdős és egyéb speciális ügyfelek (Azure AD Sync). Ezek az eszközök régebbi helyett, amelyet a összes forgatókönyv egyetlen megoldás: az Azure AD Connect. Új funkciókat, a továbbfejlesztett szolgáltatásokat és az új forgatókönyvek támogatása kínál. Tudjanak továbbra is szinkronizálja a helyszíni identitási adatokat az Azure AD és az Office 365, Határozottan javasoljuk, hogy az Azure AD Connect frissítése. A Microsoft nem garantálja 2017. December 31. után működéséhez régebbi verzióit.

2014. július jelent meg a legutóbbi kiadása a DirSync és 2015. május jelent meg az Azure AD Sync legutóbbi kiadása.

## <a name="what-is-azure-ad-connect"></a>Mi az az Azure AD Connect?
Az Azure AD Connect a DirSync és Azure AD Sync utódja. Ez a két támogatott egyesít minden forgatókönyvben. További információk a [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Érvénytelenítése ütemezése
| Dátum | Megjegyzés |
| --- | --- |
| 2016. április 13. |Windows Azure Active Directory Sync ("DirSync") és a Microsoft Azure Active Directory Sync ("Azure AD Sync") jelentette be, történő használata elavult. |
| 2017. április 13. |Támogatja a végződik. Az ügyfelek már nem lesz megnyitható támogatási esetet az Azure AD Connect először frissítés nélkül. |
|2017. december 31.|Az Azure AD nem fogadja el a Windows Azure Active Directory Sync ("DirSync") és a Microsoft Azure Active Directory Sync ("Azure AD Sync") érkező kommunikációt.

## <a name="how-to-transition-to-azure-ad-connect"></a>Az Azure AD Connect áttérési útmutató
Ha futtatja a DirSync, két módon frissítheti: helybeni frissítést, és párhuzamos központi telepítés. A legtöbb felhasználó helyben frissítés ajánlott, és ha van egy nemrég végrehajtott operációs rendszer és a kisebb, mint 50 000 objektummal. Más esetekben ajánlott ehhez párhuzamos üzembe helyezést, ahol a DirSync-konfiguráció áthelyezése az Azure AD Connect futtató új kiszolgálóra.

Azure AD Sync használja, ha egy frissítés ajánlott. Ha szeretné, akkor lehet telepíteni egy új Azure AD Connect-kiszolgáló párhuzamosan, és az Azure AD Sync kiszolgálóról az Azure AD Connect mozgó áttelepítés.

| Megoldás | Forgatókönyv |
| --- | --- |
| [Frissítés a DirSync szolgáltatásról](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Ha egy meglévő DirSync-kiszolgálóval.</li> |
| [Frissítés Azure AD-szinkronizálóról](active-directory-aadconnect-upgrade-previous-version.md) |<li>Ha az Azure AD-Szinkronizálóról.</li> |

Ha meg szeretné tekinteni a helyben frissítés menete a Dirsync szolgáltatásról az Azure AD Connect, majd tekintse meg ezt a Channel 9 videót:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>GYIK
**K: kaptam e-mailben értesítést az Azure csapata és/vagy az Office 365 üzenetközpontot üzenetét, de használom a csatlakozás.**  
Az értesítési is elküldte az Azure AD Connect használatával a buildszámot 1.0-ügyfél számára. \*.0 (előtti-1.1 kiadási használatával). A Microsoft azt javasolja, hogy az ügyfelek számára az Azure AD Connect kiadásainak mindig naprakész. A [automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) 1.1 bevezetett szolgáltatás segítségével egyszerűen mindig a telepítve az Azure AD Connect legújabb verziójával rendelkezik.

**K: fog DirSync vagy az Azure AD Sync működni azon 2017. április 13?**  
A DirSync vagy az Azure AD Sync továbbra is működnek majd a 13 2017. április.  Azonban az Azure AD nem fogadja el a DirSync vagy az Azure AD Sync folytatott kommunikáció a December 31-edik 2017.

**K: DirSync verzióinak frissíthető?**  
A jelenleg használt DirSync kiadás verzióról támogatott.

**K: Mi a helyzet az Azure AD-összekötő FIM vagy MIM?**  
Az Azure AD-összekötő FIM vagy MIM rendelkezik **nem** lett bejelentette történő használata elavult. Ez ugyanis azonos **szolgáltatás rögzíteni**; új funkció sem kerül, és nem hibajavítások kap. A Microsoft azt javasolja, hogy az ügyfelek segítségével tervezze meg az Azure AD Connect áthelyezése. Erősen ajánlott szeretné elindítani minden új üzemelő használja azt. Ez az összekötő bejelentések a jövőben elavult.

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
