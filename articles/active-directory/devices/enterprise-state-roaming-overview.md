---
title: Mi a vállalati állapothordozás az Azure Active Directoryban? | Microsoft Docs
description: Enterprise State Roaming beállítások a Windows-eszközök arról nyújt tájékoztatást. Enterprise State Roaming nyújt a felhasználók számára egységes felületet a Windows-eszközök között, és csökkenti az új eszköz konfigurálásához szükséges időt.
services: active-directory
keywords: Mi az Enterprise State Roaming, vállalati szinkronizálás, a windows a felhő
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3a2a81bd8aa3fc99d033564e8a8782c79261305
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521601"
---
# <a name="what-is-enterprise-state-roaming"></a>Mi az az Enterprise State Roaming?

A Windows 10-ben [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) felhasználók kapjanak biztonságosan szinkronizálhatók az felhasználói beállítások és az alkalmazás beállításainak adatokat a felhőbe. Enterprise State Roaming nyújt a felhasználók számára egységes felületet a Windows-eszközök között, és csökkenti az új eszköz konfigurálásához szükséges időt. Enterprise State Roaming működik hasonló a normál [fogyasztói szinkronizálását](https://go.microsoft.com/fwlink/?linkid=2015135) , amely a Windows 8 rendszerben bevezetett. Ezenkívül Enterprise State Roaming a következőket kínálja:

* **Jobban elkülöníti a vállalati és felhasználói adatok** –, amelyek adataik felett, és ott nem tartozik, tehát a vállalati adatokat a fogyasztói felhőalapú fiókban vagy egy vállalati felhő fiók fogyasztói adatokat.
* **Fokozott biztonsági** – adat automatikusan titkosítva lesznek mielőtt a felhasználó a Windows 10-es eszköz az Azure Rights Management (Azure RMS) használatával, és az adatok titkosítva, a felhőben inaktív marad. Minden tartalom titkosított a felhőben, kivéve a névtereket, például a beállítások és Windows-alkalmazás nevének inaktív marad.  
* **Felügyeleti és monitorozási jobb** – láthatóságát és vezérlést tesz lehetővé a keresztül, aki a szervezetben, és milyen eszközökön keresztül a portál az Azure AD-integrációs beállítások szinkronizálja. 

Enterprise State Roaming érhető el több Azure-régióban. Az elérhető régiók frissített listáját megtalálhatja a [Azure-szolgáltatások régiók szerint](https://azure.microsoft.com/regions/#services) az Azure Active Directory oldalon.

| Cikk | Leírás |
| --- | --- |
| [Vállalati Állapothordozás engedélyezése az Azure Active Directoryban](enterprise-state-roaming-enable.md) |Enterprise State Roaming a prémium szintű Azure Active Directory (Azure AD) előfizetéssel minden szervezet számára érhető el. Az Azure AD-előfizetés beszerzése a további részletekért tekintse meg a [Azure AD-termékre](https://azure.microsoft.com/services/active-directory) lapot. |
| [Beállítások és adatroaming GYIK](enterprise-state-roaming-faqs.md) |Ez a témakör egyes rendszergazdák lehet beállításai és alkalmazás data Sync szolgáltatással kapcsolatos kérdésekre ad választ. |
| [Csoport házirend és a mobileszköz-kezelési beállítások a beállítások szinkronizálása](enterprise-state-roaming-group-policy-settings.md) |Windows 10-es biztosít a csoportházirend és a mobileszköz-kezelés (MDM) házirend beállításait, és korlátozza a szinkronizálási beállításokat. |
| [A Windows 10 roaming beállítások referenciája](enterprise-state-roaming-windows-settings-reference.md) |A beállítások, amelyek fog kell forrásul és/vagy a biztonsági másolat a Windows 10 teljes listáját a következő: |
| [hibaelhárítással](enterprise-state-roaming-troubleshooting.md) |Ez a témakör halad végig hibaelhárítási néhány alapvető lépéseit, és az ismert problémák listáját tartalmazza. |

## <a name="next-steps"></a>További lépések

Vállalati állapothordozás engedélyezésével kapcsolatos információkért lásd: [vállalati állapothordozás engedélyezése](enterprise-state-roaming-enable.md).
