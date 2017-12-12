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
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5875cf4cc0938607fa1ff6adf840d513d1fb85bd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>A Cloud App Discovery nem felügyelt felhőalapú alkalmazások keresése
## <a name="summary"></a>Összefoglalás

A cloud App Discovery egy olyan szolgáltatás, az Azure Active Directory Premium, amely lehetővé teszi, hogy a szervezet dolgozói által használt nem felügyelt felhőalkalmazások felderítése. A modern vállalatok informatikai részlegek ismerik gyakran nem a szervezet tagjai segítségével végezhesse a munkáját felhőalapú alkalmazásokhoz. Legyen könnyen látható, hogy miért a rendszergazdák jogosulatlan hozzáférés a vállalati adatokat, lehetséges adatszivárgás és egyéb biztonsági kockázatok kétségei vannak. Tájékoztatási hiánya teheti a felsorolt biztonsági kockázatok kezelésével tűnhet terv létrehozása.

> [!TIP] 
> Tekintse meg az Azure Active Directory (Azure AD), amely tovább növeli a Cloud App Discovery fejlesztések [integráció a Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

**A Cloud App Discovery a következőket teheti:**

* Keresse meg a felhőalapú alkalmazások használják, és mérheti a használatot felhasználók száma, a forgalom vagy a webkiszolgáló az alkalmazásnak küldött kérelmek száma.
* Azonosítsa az adott alkalmazást használó felhasználókat.
* Exportálhatja az adatokat kapcsolat nélküli elemzéshez.
* Ezek az alkalmazások informatikai vezérlése alatt állapotba, és egyszeri bejelentkezés a felhasználói felügyeletének engedélyezése.

## <a name="how-it-works"></a>A működési elv
1. Alkalmazás használati ügynök telepítve van a felhasználók számítógépeire.
2. Az alkalmazás használati adatait, az ügynökök által rögzített a cloud app discovery szolgáltatásba zajlik a biztonságos, titkosított csatornán.
3. A Cloud App Discovery szolgáltatásba kiértékeli az adatokat, és létrehozza a jelentéseket.

![Cloud App Discovery diagramja](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Következő lépések
* [Cloud App Discovery biztonsági és adatvédelmi megfontolások](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery beállításjegyzék-beállítások egyéni porttal proxykiszolgálók](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery-ügynök változásnaplója](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)

