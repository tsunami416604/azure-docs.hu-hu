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
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 9db0fa29f6af0053d45f9f0238b52ac34fdb464a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223261"
---
# <a name="group-policy-and-mdm-settings"></a>A csoportházirend és a mobileszköz-kezelési beállítások
Használja ezeket a csoportházirend és a mobileszköz-felügyelet (MDM) beállítások csak a vállalat által birtokolt eszközök, mert ezek a szabályzatok érvénybe lépnek a felhasználó-eszköz teljes. Letiltja a személyes beállítások szinkronizálása egy mobileszköz-kezelési házirend alkalmazása, felhasználó által birtokolt eszközök hátrányosan befolyásolhatja, hogy az eszköz használatát. Ezenkívül az eszközön más felhasználói fiókok is érinti a szabályzatot.

Olyan vállalatok, amelyek személyes (nem felügyelt) eszközök központi kezeléséhez használhatja az Azure Portalon engedélyezheti vagy tilthatja le a barangolás, nem pedig a csoportházirend vagy a mobileszköz-kezelést.
Az alábbi táblázatok ismertetik a rendelkezésre álló házirend-beállításokat.

## <a name="mdm-settings"></a>Mobileszköz-kezelési beállítások
A mobileszköz-kezelési házirend-beállítások Windows 10 és Windows 10 Mobile is vonatkozik.  Windows 10 Mobile-támogatás csak a Microsoft-fiók roaming a felhasználó OneDrive-fiók létezik.  Tekintse meg az "Eszközök és végpontok" című szakasz részletezi az Azure AD-alapú szinkronizálása a támogatott eszközök.

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

## <a name="related-topics"></a>Kapcsolódó témakörök
* [Vállalati Állapothordozás áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Az Azure Active Directory vállalati állapothordozás engedélyezése](active-directory-windows-enterprise-state-roaming-enable.md)
* [Beállítások és adatroaming GYIK](active-directory-windows-enterprise-state-roaming-faqs.md)
* [A Windows 10 roaming beállítások referenciája](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

