---
title: Házirend- és MDM-beállítások |} A Microsoft Docs
description: Információ a csoportházirend és a mobileszköz-felügyelet (MDM) beállítások telepíthetők, amelyek kell a vállalat által birtokolt eszközöket biztosít. Ezek a szabályzatok érvényesek a felhasználó-eszköz teljes.
services: active-directory
keywords: Mik azok a csoport házirend és a mobileszköz-kezelési beállítások Enterprise State Roaming, Enterprise State Roaming, a windows-felhő
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.openlocfilehash: a08fbb90d1148396dbcf3960536568d61e5f04b0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50098015"
---
# <a name="group-policy-and-mdm-settings"></a>A csoportházirend és a mobileszköz-kezelési beállítások
Használja ezeket a csoportházirend és a mobileszköz-felügyelet (MDM) beállítások csak a vállalat által birtokolt eszközök, mert ezek a szabályzatok érvénybe lépnek a felhasználó-eszköz teljes. Letiltja a személyes beállítások szinkronizálása egy mobileszköz-kezelési házirend alkalmazása, felhasználó által birtokolt eszközök hátrányosan befolyásolhatja, hogy az eszköz használatát. Ezenkívül az eszközön más felhasználói fiókok is érinti a szabályzatot.

Olyan vállalatok, amelyek személyes (nem felügyelt) eszközök központi kezeléséhez használhatja az Azure Portalon engedélyezheti vagy tilthatja le a barangolás, nem pedig a csoportházirend vagy a mobileszköz-kezelést.
Az alábbi táblázatok ismertetik a rendelkezésre álló házirend-beállításokat.

## <a name="mdm-settings"></a>Mobileszköz-kezelési beállítások
A mobileszköz-kezelési házirend-beállítások Windows 10 és Windows 10 Mobile is vonatkozik.  Windows 10 Mobile-támogatás csak a Microsoft-fiók roaming a felhasználó OneDrive-fiók létezik.  Tekintse meg [eszközök és a végpontok](enterprise-state-roaming-windows-settings-reference.md) milyen eszközökön használható az Azure AD-alapú szinkronizálása részleteiért.

| Name (Név) | Leírás |
| --- | --- |
| Microsoft-fiók-kapcsolat engedélyezése |Lehetővé teszi a felhasználóknak a hitelesítést a Microsoft-fiókkal az eszközön |
| Beállítások szinkronizálásának engedélyezése |Lehetővé teszi a felhasználók számára a Windows-beállításokat és alkalmazásadatokat; Ez a szabályzat letiltását letiltja a szinkronizálása, valamint a biztonsági mentések a mobileszközökön |

## <a name="group-policy-settings"></a>Csoportházirend-beállítások
A csoportházirend-beállítások Active Directory-tartományhoz csatlakoztatott Windows 10-es eszközökre vonatkoznak. A táblázat az örökölt beállítások, amely a következőképpen fog szinkronizálási beállítások kezelését, de, amely nem működnek a vállalati állapota a központi Windows 10-es, amelyek jeleztük, a "Ne használjon" leírásában is tartalmaz.

| Name (Név) | Leírás |
| --- | --- |
| Fiókok: Microsoft-fiókok blokkolása |A házirend-beállítás megakadályozza, hogy a felhasználók új Microsoft-fiókok felvételének ezen a számítógépen |
| Szinkronizálja a |Megakadályozza, hogy a felhasználók számára a Windows-beállításokat és alkalmazásadatokat |
| Szinkronizálja a személyre szabása |Letiltja a témák csoport szinkronizálása |
| Szinkronizálja a böngésző beállításai |Letiltja az Internet Explorer csoport szinkronizálása |
| Szinkronizálja a jelszavakat |Letiltja a jelszavakat csoport szinkronizálása |
| Szinkronizálja a többi Windows-beállítások |Egyéb Windows-beállítások csoport szinkronizálása letiltása |
| Asztal személyre szabása nem szinkronizálhatók |Ne használjon; nem lesz hatása |
| Szinkronizálja a forgalmi díjas kapcsolatok használata esetén |Letiltja a roaming díjköteles, például a mobiltelefonos 3 G-kapcsolatok |
| Nem alkalmazások szinkronizálása |Ne használjon; nem lesz hatása |
| Szinkronizálja a beállításai |Az alkalmazásadatok barangolás letiltása |
| Szinkronizálja a kezdő beállítások |Ne használjon; nem lesz hatása |

## <a name="next-steps"></a>További lépések

Áttekintéséhez lásd: [vállalati Állapothordozás áttekintése](enterprise-state-roaming-overview.md).


