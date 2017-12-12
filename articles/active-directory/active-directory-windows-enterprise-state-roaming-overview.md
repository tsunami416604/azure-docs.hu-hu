---
title: "A vállalati Állapothordozás áttekintése |} Microsoft Docs"
description: "A Windows-eszközökön a vállalati Állapothordozás beállításokkal kapcsolatos információkat nyújt. A vállalati Állapothordozás nyújt a felhasználók számára egy egységes élmény a Windows-eszközön, és csökkenti az új eszköz konfigurálásához szükséges időt."
services: active-directory
keywords: "Mi az a vállalati Állapothordozás, vállalati sync, a windows felhő"
documentationcenter: 
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 2e1ebf5a9bfc4a1f0f92ff85f9406ecc7d538819
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="enterprise-state-roaming-overview"></a>Vállalati állapothordozás áttekintése
A Windows 10 [Azure Active Directory (Azure AD)](active-directory-whatis.md) a felhasználók biztonságosan szinkronizálhatók a felhasználói beállítások és a beállítások adatainak a felhőbe kapnak. A vállalati Állapothordozás nyújt a felhasználók számára egy egységes élmény a Windows-eszközön, és csökkenti az új eszköz konfigurálásához szükséges időt. A vállalati Állapothordozás működik, a standard hasonló [fogyasztói szinkronizálása](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) , amely először a Windows 8 rendszerben bevezetett. Emellett a vállalati Állapothordozás kínálja:

* **A vállalati szét és fogyasztói adatoktól** – szervezetek irányítják az adatokat, és nincs tartozik, tehát a vállalati adatokat a fogyasztói felhő fiók vagy a vállalati felhő fiók fogyasztói adatoktól.
* **Fokozott biztonsági** – automatikusan adattitkosítás mielőtt elhagynák a Windows 10-eszközön a felhasználó Azure Rights Management (Azure RMS) segítségével, és adatokat a felhő aktívan titkosított marad. Minden tartalom átvitelig titkosítva, kivéve a névteret, például a beállítások és a Windows alkalmazás nevét a felhő aktívan marad.  
* **Kezelési és figyelési jobb** – vezérlő és a láthatóság érdekében biztosítanak a szervezetében, és milyen eszközökön keresztül a portál az Azure AD-integrációs beállítások szinkronizálásának ki. 

A vállalati Állapothordozás nem áll rendelkezésre több Azure-régiók. Elérhető régiók frissített listáját megtalálhatja a [Azure szolgáltatásokat régiók szerint](https://azure.microsoft.com/regions/#services) az Azure Active Directory lap.

| Cikk | Leírás |
| --- | --- |
| [Vállalati Állapothordozás engedélyezése az Azure Active Directoryban](active-directory-windows-enterprise-state-roaming-enable.md) |A vállalati Állapothordozás bármely szervezet a prémium szintű Azure Active Directory (Azure AD) előfizetést érhető el. Az Azure AD-előfizetés. További részletekért tekintse meg a [az Azure AD termékkel](https://azure.microsoft.com/services/active-directory) lap. |
| [Beállítások és adatok hordozása – gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md) |Ez a témakör néhány rendszergazdák rendelkezhet beállításait, valamint az alkalmazás adatszinkronizálás kérdésekre ad választ. |
| [Csoport házirend és a mobileszköz-kezelési beállítások beállítások szinkronizálási szolgáltatás](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 beállításokat tartalmazza, Csoportházirend és a mobileszköz-felügyelet (MDM) házirend korlátozza a szinkronizálási beállításokat. |
| [Windows 10 központi beállításainak ismertetése](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Lesz kell forrásul és/vagy a biztonsági mentésben Windows 10-beállítások teljes listáját a következő: |
| [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Ez a témakör néhány alapvető lépések a hibaelhárításhoz, és az ismert problémák listáját tartalmazza. |

