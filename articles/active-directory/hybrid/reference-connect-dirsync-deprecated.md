---
title: Frissítés a DirSync és az Azure AD Sync |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure AD Connect a DirSync és az Azure AD-Szinkronizáló frissítése.
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
ms.openlocfilehash: b95fa5b5336db62bdf64ebd0dc254160d1a9282d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490600"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Windows Azure Active Directory Sync és Azure Active Directory Sync frissítése
Az Azure AD Connect a legjobb megoldás, ha a helyszíni címtárat az Azure AD-hez és az Office 365-höz szeretné csatlakoztatni. Ez az ideje, mivel ezek elavultak, és 2017. április 13, már nem támogatott a Windows Azure Active Directory Sync (DirSync) vagy Azure AD Sync szolgáltatásról az Azure AD Connect frissítése.

Elavult két identitás szinkronizálási eszközöket is (DirSync) egyerdős ügyfelek számára érhető el, és több erdőhöz és más speciális ügyfelek (Azure AD Sync). Ezeket a régebbi eszközöket vette egy egyetlen megoldás, amely minden esetben érhető el: Azure AD Connect. Új funkciók, fejlesztések és új forgatókönyvek támogatása kínál. A továbbra is a helyszíni identitás adatok az Azure AD szinkronizálása és az Office 365-höz, javasoljuk, hogy az Azure AD Connect szolgáltatásra frissít. A Microsoft nem garantálja a 2017. December 31. után ezen régebbi verziói.

A DirSync utolsó kiadásában 2014. július jelent meg, és 2015 május jelent meg az Azure AD Sync utolsó kiadásában.

## <a name="what-is-azure-ad-connect"></a>Mi az az Azure AD Connect?
Az Azure AD Connect a DirSync és az Azure AD Sync utódja. Amely ötvözi az minden esetben ez a két támogatott. További információ a [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Elavulási ütemezésnek
| Dátum | Megjegyzés |
| --- | --- |
| 2016. április 13. |Windows Azure Active Directory Sync ("DirSync") és a Microsoft Azure Active Directory Sync ("Azure AD Sync") jelentette be, mert elavult. |
| 2017. április 13. |Támogatás véget. Ügyfelek már nem tudja megnyitni egy támogatási esetet először az Azure AD Connect frissítése nélkül. |
|2017. december 31-ig.|Azure ad-ben, hogy már nem fogad el a kommunikáció a Windows Azure Active Directory Sync ("DirSync") és a Microsoft Azure Active Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Az Azure AD Connect szolgáltatásra való áttéréssel
Ha a DirSync futtatja, kétféleképpen frissítheti: A helyben frissítés és a párhuzamos üzembe helyezés. Helyszíni frissítés a legtöbb ügyfél számára javasolt, és ha rendelkezik egy újabb operációs rendszer és a kisebb, mint 50 000 objektummal. Más esetekben ajánlott ehhez párhuzamos üzembe helyezést, ahol a DirSync-konfigurációt egy új, Azure AD Connectet futtató kiszolgálóra kerül.

| Megoldás | Forgatókönyv |
| --- | --- |
| [Frissítés a DirSync szolgáltatásról](how-to-dirsync-upgrade-get-started.md) |<li>Ha már fut a meglévő DirSync kiszolgáló.</li> |
| [Frissítés az Azure AD-Szinkronizálóról](how-to-upgrade-previous-version.md) |<li>Az Azure AD-Szinkronizálóról áthelyezéséhez.</li> |

Ha azt szeretné, hogyan hajtsa végre egy helyszíni frissítését az Azure AD Connect a Dirsync szolgáltatásról, tekintse meg a Channel 9-videók:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>GYIK
**K: E-mailben értesítést kaptam az Azure csapata és/vagy az Office 365 üzenetközpontjában üzenetét, de a Connect használok.**  
Az Azure AD Connect használatával a buildszám 1.0 ügyfelek is küldött az értesítést. \*.0 (kiadás előtti-1.1 használatával). Microsoft azt javasolja, hogy az ügyfelek számára az Azure AD Connect-kiadások és legyen naprakész. A [automatikus frissítés](how-to-connect-install-automatic-upgrade.md) 1.1 bevezetett szolgáltatás megkönnyíti mindig van telepítve az Azure AD Connect legújabb verzióját.

**K: A DirSync vagy az Azure AD Sync működése leáll a 2017. április 13?**  
A DirSync vagy az Azure AD Sync fog folytatja a munkát a 2017. április 13.  Azonban az Azure AD, hogy már nem fogad el a DirSync vagy az Azure AD-Szinkronizáló által kezdeményezett kommunikáció a 2017. December 31. után.

**K: A DirSync verzióinak frissíthetem a?**  
Bármely jelenleg használt DirSync-kiadás frissítése támogatott. 

**K: Mi a teendő az Azure AD-összekötő FIM vagy MIM?**  
Az Azure AD-összekötő az FIM vagy MIM **nem** már bejelentett elavult. Ugyanakkor **funkció lefagyását tapasztaló**; új funkció sem kerül, és nem kapja, hogy nincs hibajavításokat tartalmaz. A Microsoft javasolja ügyfelek tervezése áthelyezése az Azure AD Connect használatával. Erősen ajánlott, használja az új környezeteknek indul el. Ez az összekötő elavult később lesz bejelentve.

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
