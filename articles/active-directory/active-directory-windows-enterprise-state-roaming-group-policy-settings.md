---
title: Házirend- és MDM-beállítások |} Microsoft Docs
description: Tájékoztatást ad azokról csoportházirend és a mobileszköz-felügyelet (MDM) beállításait a vállalat által birtokolt eszközök használni. Ezek a házirendek a felhasználó-eszköz teljes is vonatkozik.
services: active-directory
keywords: Mik azok a csoport házirend és a vállalati Állapothordozás, a vállalati Állapothordozás, a windows felhőalapú MDM beállításait
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 588084481ffc5cbbeed34e9527271179fa359ed5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614908"
---
# <a name="group-policy-and-mdm-settings"></a>A csoportházirend és a mobileszköz-kezelési beállítások
Használja a csoportházirend és a mobileszközök felügyeletének (MDM) beállításai csak a vállalat által birtokolt eszközök mivel ezek a házirendek a felhasználó-eszköz teljes vonatkoznak. Személyes beállítások szinkronizálásának letiltása az MDM-házirend alkalmazását, felhasználó által birtokolt eszköz lesz negatív hatással az adott eszköz használatát. Ezenfelül más felhasználói fiókokat az eszközön is befolyásolják a házirend.

Vállalatok számára, a személyes (nem felügyelt) eszközök központi kezelése szeretne az Azure portál segítségével engedélyezheti vagy tilthatja le a központi, nem pedig a csoportházirend vagy a mobileszköz-kezelést.
Az alábbi táblázatban láthatók a rendelkezésre álló házirend-beállításokat.

## <a name="mdm-settings"></a>MDM-beállítások
A mobileszköz-kezelési házirend-beállítások Windows 10 és Windows 10 Mobile egyaránt vonatkozik.  Windows 10 Mobile-támogatás csak Microsoft-fiók segítségével a felhasználó OneDrive-fiókja központi létezik.  Tekintse meg az "Eszközök és végpontok" című szakaszban talál információt az Azure AD-alapú szinkronizálásra támogatott eszközök.

| Név | Leírás |
| --- | --- |
| Microsoft-fiók csatlakozás engedélyezése |Lehetővé teszi, hogy a felhasználók számára a hitelesítést a Microsoft-fiókkal az eszközön |
| A beállítások szinkronizálásának engedélyezése |Lehetővé teszi a felhasználók számára a Windows-beállítások és alkalmazások adatainak; Ez a házirend letiltása letiltja a szinkronizálása, valamint a mobileszközök biztonsági mentések |

## <a name="group-policy-settings"></a>Csoportházirend-beállítások
A csoportházirend-beállítások egy Active Directory-tartományhoz csatlakoztatott Windows 10-eszközökre vonatkozik. A tábla is örökölt beállítások, amely a szinkronizálási beállítások kezelése jelent, azonban, hogy nem működik, a vállalati állapot barangolás a Windows 10, amely a "Nem használható" leírása leírásban ki vannak emelve.

| Név | Leírás |
| --- | --- |
| Fiókok: Blokk-Microsoft-fiókok |A házirend-beállítás megakadályozza, hogy a felhasználók új Microsoft-fiókokat adjanak hozzá ezen a számítógépen |
| Szinkronizálja a |Megakadályozza, hogy a felhasználók számára a Windows beállításai és adatai |
| Szinkronizálja a személyre szabás |Letiltja a témák csoport szinkronizálása |
| Szinkronizálja a böngésző beállításai |Letiltja az Internet Explorer csoport szinkronizálása |
| Szinkronizálja a jelszavakat |Letiltja a jelszavak csoport szinkronizálása |
| Szinkronizálja a más Windows-beállítások |Letiltja a Windows egyéb beállítások csoport szinkronizálása |
| Szinkronizálja a asztal személyre szabása |Ne használjon; nincs hatása |
| Szinkronizálja a forgalmi díjas kapcsolatok használata esetén |Letiltja a roaming díjas kapcsolatok, például a 3 G mobilhálózat |
| Szinkronizálja a alkalmazások |Ne használjon; nincs hatása |
| Szinkronizálja a Alkalmazásbeállítások |Alkalmazásadatok központi letiltása |
| Nem kezdő beállítások szinkronizálása |Ne használjon; nincs hatása |

## <a name="related-topics"></a>Kapcsolódó témakörök
* [A vállalati Állapothordozás áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Az Azure Active Directoryban a vállalati állapothordozás engedélyezése](active-directory-windows-enterprise-state-roaming-enable.md)
* [Beállítások és adatok hordozása – gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Windows 10 központi beállításainak ismertetése](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

