---
title: Csoportházirend és MDM-beállítások az ESR-hez – Azure Active Directory
description: A vállalati állami barangolás kezelési beállításai
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672371"
---
# <a name="group-policy-and-mdm-settings"></a>Csoportházirend és MDM-beállítások

Ezeket a csoportházirend-és mobileszköz-kezelési (MDM- beállításokat) csak vállalati tulajdonú eszközökön használja, mert ezek a házirendek a felhasználó teljes eszközére vonatkoznak. Ha egy MDM-házirendet alkalmaz a beállítások szinkronizálásának letiltására egy személyes, felhasználó által birtokolt eszközön, az negatívhatással lesz az eszköz használatára. Emellett az eszközön lévő egyéb felhasználói fiókokat is érinti a házirend.

A személyes (nem felügyelt) eszközök roamingját kezelni kívánó vállalatok a csoportházirend vagy az MDM használata helyett az Azure Portalon engedélyezhetik vagy letilthatják a barangolást.
Az alábbi táblázatok az elérhető házirend-beállításokat ismertetik.

> [!NOTE]
> Ez a cikk a 2015 júliusában Windows 10 rendszerrel indított Microsoft Edge Legacy HTML-alapú böngészőre vonatkozik. A cikk nem vonatkozik az új Microsoft Edge Chromium-alapú böngésző re január 15-én, 2020. Az új Microsoft Edge szinkronizálási viselkedéséről további információt a Microsoft Edge Sync című cikkben [talál.](/deployedge/microsoft-edge-enterprise-sync)

## <a name="mdm-settings"></a>MDM-beállítások

Az MDM-házirend-beállítások a Windows 10 és a Windows 10 Mobile rendszerre egyaránt vonatkoznak.  A Windows 10 Mobile-támogatás csak a Microsoft-fiókalapú barangoláshoz létezik a felhasználó OneDrive-fiókján keresztül. Tekintse meg [az eszközök és a végpontok](enterprise-state-roaming-windows-settings-reference.md) a részleteket, hogy milyen eszközök támogatják az Azure AD-alapú szinkronizálás.

| Név | Leírás |
| --- | --- |
| Microsoft-fiókkapcsolat engedélyezése |Lehetővé teszi a felhasználók számára a hitelesítést egy Microsoft-fiók használatával az eszközön |
| Saját beállítások szinkronizálásának engedélyezése |Lehetővé teszi a felhasználók számára, hogy barangoljanak a Windows beállításaiban és az alkalmazásadatokban; A házirend letiltása letiltja a szinkronizálást, valamint a biztonsági mentéseket a mobileszközökön |

## <a name="group-policy-settings"></a>Csoportházirend-beállítások

A csoportházirend-beállítások az Active Directory-tartományhoz csatlakozó Windows 10-eszközökre vonatkoznak. A táblázat olyan örökölt beállításokat is tartalmaz, amelyek látszólag kezelik a szinkronizálási beállításokat, de amelyek nem működnek a Windows 10 enterprise state roaming esetében, amelyek a leírásban a "Ne használja" megjegyzéssel rendelkeznek.

Ezek a beállítások a következő helyen találhatók:`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Név | Leírás |
| --- | --- |
| Fiókok: Microsoft-fiókok letiltása |Ez a házirend-beállítás megakadályozza, hogy a felhasználók új Microsoft-fiókokat adjanak hozzá a számítógépen |
| Nincs szinkronizálás |Megakadályozza, hogy a felhasználók barangoljanak a Windows beállításaiban és az alkalmazásadatokban |
| A személyre szabás szinkronizálásának mellőzése |Letiltja a Témák csoport szinkronizálását |
| A böngésző beállításainak szinkronizálásának mellőzése |Letiltja az Internet Explorer csoport szinkronizálását |
| Jelszavak szinkronizálásának mellőzése |Letiltja a Jelszavak csoport szinkronizálását |
| Más Windows-beállítások szinkronizálásának mellőzése |Letiltja az Egyéb Windows-beállítások csoport szinkronizálását |
| Az asztal személyre szabásának mellőzése |Ne használja; nincs hatása |
| Forgalmi díjas kapcsolatokon nincs szinkronizálás |Letiltja a barangolást a forgalmi díjas kapcsolatokon, például a mobil 3G-n |
| Alkalmazások szinkronizálásának mellőzése |Ne használja; nincs hatása |
| Az alkalmazásbeállítások szinkronizálásának mellőzése |Letiltja az alkalmazásadatok barangolását |
| Ne szinkronizálja az indítási beállításokat |Ne használja; nincs hatása |

## <a name="next-steps"></a>További lépések

Áttekintést a [vállalati állapotroaming áttekintése című témakörben talál.](enterprise-state-roaming-overview.md)
