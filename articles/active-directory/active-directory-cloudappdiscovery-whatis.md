---
title: "A Cloud App Discovery a nem felügyelt felhőalapú alkalmazásokhoz az Azure Active Directoryban található |} Microsoft Docs"
description: "Információk keresése és alkalmazások kezelése az a Cloud App Discovery, milyen előnyökkel és annak működéséről."
services: active-directory
keywords: "a cloud app discovery alkalmazások kezelése"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>A Cloud App Discovery nem felügyelt felhőalapú alkalmazások keresése
## <a name="summary"></a>Összegzés

A cloud App Discovery az Azure Active Directoryban most a Microsoft Cloud App Security technológiával továbbfejlesztett ügynök nélküli felderítési élményt nyújt. A Cloud App Discovery használatához csak jelentkezzen be Azure AD Premium P1 hitelesítő adataival. A frissítés érhető el az összes Azure AD Premium P1 ügyfél számára további költségek nélkül. Ismerkedjen meg a cikk [az Azure AD Cloud App Discovery beállítása](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started), majd próbálja ki [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> A jelenlegi Azure AD Cloud App Discovery ügynök-alapú feltárásához kipróbálhatja az, hogy ki kell kapcsolni a 2018. március 5., amely után az ügynökök lesz tiltva, és adatok törlődnek. Hajtsa végre műveletet, és kérjen működik, és az új felület a szolgáltatás megszűnésének megelőzése érdekében 5. március előtt.  
 
**A Cloud App Discovery a következőket teheti:**

* Keresse meg a felhőalapú alkalmazások használják, és mérheti a használatot felhasználók száma, a forgalom vagy a webkiszolgáló az alkalmazásnak küldött kérelmek száma.
* Azonosítsa az adott alkalmazást használó felhasználókat.
* Exportálhatja az adatokat kapcsolat nélküli elemzéshez.
* Ezek az alkalmazások informatikai vezérlése alatt állapotba, és egyszeri bejelentkezés a felhasználói felügyeletének engedélyezése.

## <a name="how-it-works"></a>Működés
1. Alkalmazás használati ügynök telepítve van a felhasználók számítógépeire.
2. Az alkalmazás használati adatait, az ügynökök által rögzített a cloud app discovery szolgáltatásba zajlik a biztonságos, titkosított csatornán.
3. A Cloud App Discovery szolgáltatásba kiértékeli az adatokat, és létrehozza a jelentéseket.

![Cloud App Discovery diagramja](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>További lépések
* [Cloud App Discovery biztonsági és adatvédelmi megfontolások](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery beállításjegyzék-beállítások egyéni porttal proxykiszolgálók](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery-ügynök változásnaplója](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)

