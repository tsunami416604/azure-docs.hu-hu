---
title: "Az Azure Active Directory felügyeleti egység felügyeleti előzetes verzió"
description: "Adminisztratív egységei használ az Azure Active Directory engedélyeit részletesebb delegálása"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8464cd6b-1d1a-470d-a4fb-ee29b8eab4c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d657eda25f3b26cb793a7ba1a4546f98c08b7e65
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Az adminisztrációs egységek felügyelete az Azure AD - nyilvános előzetes verzió
Ez a cikk ismerteti adminisztratív egységei – egy új Azure Active Directory-tárolót az erőforrásokat, amelyek rendszergazdai engedélyek delegálása felhasználók és a felhasználók egy részének fog alkalmazni házirendeket részhalmaza keresztül is használható. Az Azure Active Directoryban adminisztratív egységei engedélyezése központi rendszergazdák regionális rendszergazdák vonatkozó engedélyek delegálása vagy alapszinten házirend beállítása.

Ez akkor hasznos, a független részlegek, például a nagy egyetemi sok autonóm iskola (üzleti iskolai, műszaki osztály iskolai, és így tovább) egymástól független végzett rendelkező szervezetek számára. Az ilyen osztályok rendelkezik saját informatikai rendszergazdáknak, akik hozzáférést, a felhasználók kezelése és a kifejezetten a felosztás vonatkozó házirendek beállítása. Központi rendszergazdák tudják szeretne biztosítani a részlegszintű rendszergazdák engedélyek keresztül az adott részlegek számára. Pontosabban Ez a példa, egy központi felügyeleti segítségével, például egy adott iskolai (üzleti iskolai) a felügyeleti egység létrehozása és feltöltése csak az üzleti felhasználók iskolai. Egy központi felügyeleti hatókörrel rendelkező szerepkörhöz, adhat hozzá az üzleti iskola informatikai munkatársak Ez azt jelenti, adja meg az informatikai munkatársak üzleti iskolai felügyeleti engedélyeket csak az iskolai felügyeleti részleg keresztül.

> [!IMPORTANT]
> Adminisztratív egységei használatára van szükség a felügyeleti egység hatókörű rendszergazdáját, hogy rendelkezik egy Azure Active Directory Premium licenc, és az Azure Active Directory alapvető licencek az összes felhasználó számára a felügyeleti egység. További információkért lásd: [Ismerkedés az Azure AD Premium](active-directory-get-started-premium.md).
>


A központi felügyeleti szempontból egy felügyeleti egység hozható létre és töltődik erőforrások címtárobjektum. **Ebben az előzetes kiadásban ezeket az erőforrásokat lehet felhasználókra.** Miután létre és töltődik fel, a felügyeleti egység segítségével hatóköreként a rendelt engedélyek korlátozása csak a felügyeleti egység található erőforrások keresztül.

## <a name="managing-administrative-units"></a>Felügyeleti egységek kezelése
Ebben az előzetes kiadásban létrehozhat és kezelése az Azure Active Directory modul a Windows PowerShell-parancsmagok használatával adminisztratív egységei. További információt szeretne, olvassa el, hogyan [adminisztratív egységei használata](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

További információ a szoftver-követelményeket és az Azure AD-modul telepítése, és információk az Azure AD-modullal parancsmagok felügyeleti egységek, beleértve a szintaxist, a paraméterek leírásait és a példákat, kezelése [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory-kiadások](active-directory-editions.md)
