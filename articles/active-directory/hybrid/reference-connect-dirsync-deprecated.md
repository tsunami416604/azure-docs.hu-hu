---
title: Frissítés a DirSync és az Azure AD Sync szolgáltatásról | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként frissíthet a DirSync és az Azure AD Sync szolgáltatásról az Azure AD Connect szolgáltatásra.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803fcc0161f2a092006e60db5a98f5bf18dce1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381178"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>A Microsoft Azure Active Directory Sync és az Azure Active Directory Sync frissítése
Az Azure AD Connect a legjobb megoldás, ha a helyszíni címtárat az Azure AD-hez és az Office 365-höz szeretné csatlakoztatni. Ez egy nagyszerű alkalom az Azure AD Connect re való frissítéshez a Windows Azure Active Directory Sync (DirSync) vagy az Azure AD Sync szolgáltatásból, mivel ezek az eszközök már elavultak, és 2017.

Az elavult két identitás-szinkronizálási eszköz egyetlen erdőbeli ügyfél (DirSync) és többerdős és más speciális ügyfelek (Azure AD Sync) számára került felkínálva. Ezeket a régebbi eszközöket egyetlen megoldás váltotta fel, amely minden forgatókönyvben elérhető: az Azure AD Connect. Új funkciókat, funkciófejlesztéseket és új forgatókönyvek támogatását kínálja. A helyszíni identitásadatok Azure AD-vel és az Office 365-tel való további szinkronizálásához javasoljuk, hogy frissítsen az Azure AD Connectre. A Microsoft nem garantálja, hogy ezek a régebbi verziók 2017.

A DirSync utolsó kiadása 2014 júliusában jelent meg, az Azure AD Sync utolsó kiadása pedig 2015 májusában jelent meg.

## <a name="what-is-azure-ad-connect"></a>Mi az az Azure AD Connect?
Az Azure AD Connect a DirSync és az Azure AD Sync utódja. Egyesíti az összes forgatókönyvet a két támogatott. Erről bővebben [a helyszíni identitások integrálása az Azure Active Directoryval.](whatis-hybrid-identity.md)

## <a name="deprecation-schedule"></a>Eprekation ütemezése
| Dátum | Megjegyzés |
| --- | --- |
| 2016. április 13. |A Windows Azure Active Directory-szinkronizálás ("DirSync") és a Microsoft Azure Active Directory-szinkronizálás ("Azure AD Sync") elavultként van bejelentve. |
| 2017. április 13.April 13, 2017 |A támogatás véget ér. Az ügyfelek a továbbiakban nem nyithatnak meg támogatási esetet anélkül, hogy először az Azure AD Connectre kellene frissíteniük. |
|2017. december 31., sidlai|Előfordulhat, hogy az Azure AD a továbbiakban nem fogad el kommunikációt a Windows Azure Active Directory Sync ("DirSync") és a Microsoft Azure Active Directory Sync ("Azure AD Sync") rendszerből.

## <a name="how-to-transition-to-azure-ad-connect"></a>Váltás az Azure AD Connectre való áttérés
Ha a DirSync-et futtatja, kétféleképpen frissíthet: helybeni frissítés és párhuzamos telepítés. A legtöbb ügyfél számára ajánlott a helyben történő frissítés, és ha friss operációs rendszerrel és 50 000 objektumnál kisebb objektumokkal rendelkezik. Más esetekben ajánlott egy párhuzamos központi telepítést, ahol a DirSync-konfiguráció egy Azure AD Connect et futtató új kiszolgálóra kerül.

| Megoldás | Forgatókönyv |
| --- | --- |
| [Frissítés a DirSync szolgáltatásról](how-to-dirsync-upgrade-get-started.md) |<li>Ha már fut egy dirSync-kiszolgáló.</li> |
| [Frissítés az Azure AD Sync-ről](how-to-upgrade-previous-version.md) |<li>Ha az Azure AD Sync-ről költözik.</li> |

Ha szeretné látni, hogyan lehet egy helyben i upgrade-et végezni a DirSync-ről az Azure AD Connectre, akkor tekintse meg ezt a Channel 9 videót:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>GYIK
**K: E-mailértesítést kaptam az Azure Teamtől és/vagy üzenetet az Office 365 üzenetközpontjából, de a Connectet használom.**  
Az értesítést az Azure AD Connect használatával is elküldték az 1.0-s számú buildszámmal. \*0-val (az 1.1 előtti kiadással). A Microsoft azt javasolja az ügyfeleknek, hogy maradjanak naprakészek az Azure AD Connect-kiadásokkal. Az 1.1-ben bevezetett [automatikus frissítési](how-to-connect-install-automatic-upgrade.md) funkció megkönnyíti az Azure AD Connect legújabb verziójának telepítését.

**K: A DirSync/Azure AD Sync 2017.**  
2017. április 13-án a DirSync/Azure AD Sync továbbra is működni fog.  2017. december 31-e után azonban előfordulhat, hogy az Azure AD már nem fogadja el a DirSync/Azure AD Sync kommunikációját.

**K: Mely DirSync verziókról frissíthetek?**  
A jelenleg használt DirSync-kiadásokról való frissítés támogatott. 

**K: Mi a helyzet az Azure AD-összekötő FIM/MIM?**  
Az Azure AD-összekötő fim/MIM-hez **nem** lett elavultként bejelentve. Ez a **funkció befagyasztása;** nincs új funkció hozzáadva, és nem kap hibajavításokat. A Microsoft azt javasolja az ügyfeleknek, hogy azt használják az Azure AD Connect-re való áttérés megtervezéséhez. Erősen ajánlott, hogy ne indítson el új központi telepítéseket. Ez a csatlakozó a jövőben elavultlesz.

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
