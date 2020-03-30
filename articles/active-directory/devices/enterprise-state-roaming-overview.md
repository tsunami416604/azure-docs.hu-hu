---
title: Mi a vállalati állapotroaming az Azure Active Directoryban?
description: Az Enterprise State Roaming egységes felhasználói élményt nyújt a felhasználóknak Windows-eszközeiken
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77194279"
---
# <a name="what-is-enterprise-state-roaming"></a>Mi az az Enterprise State Roaming?

A Windows 10-ben az [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) felhasználói biztonságosan szinkronizálhatja a felhasználói beállításokat és az alkalmazásbeállítások adatait a felhőbe. Az Enterprise State Roaming egységes felhasználói élményt nyújt a felhasználóknak windowsos eszközeiken, és csökkenti az új eszközök konfigurálásához szükséges időt. Az Enterprise State Roaming hasonló a Windows 8 rendszerben először bevezetett szabványos [fogyasztói beállításszinkronizáláshoz.](https://go.microsoft.com/fwlink/?linkid=2015135) Ezenkívül az Enterprise State Roaming a következőket kínálja:

* **A vállalati és fogyasztói adatok elkülönítése** – A szervezetek irányítják az adataikat, és a vállalati adatok nem keverednek a fogyasztói felhőfiókban vagy a fogyasztói adatok egy vállalati felhőfiókban.
* **Fokozott biztonság** – Az adatok automatikusan titkosítva vannak, mielőtt az Azure Rights Management (Azure RMS) használatával elhagynák a felhasználó Windows 10-es eszközét, és az adatok a felhőben maradnak titkosítva. Minden tartalom titkosítva marad a felhőben, kivéve a névtereket, például a beállítások nevét és a Windows-alkalmazások nevét.  
* **Jobb felügyelet és figyelés** – Szabályozza és láthatja, hogy ki szinkronizálja a beállításokat a szervezetben, és mely eszközökön az Azure AD-portál integrációja révén. 

A vállalati állapotroaming több Azure-régióban érhető el. Az [azure-szolgáltatások régiók szerint](https://azure.microsoft.com/regions/#services) lapon található elérhető régiók frissített listáját az Azure Active Directory ban találja.

| Cikk | Leírás |
| --- | --- |
| [A vállalati állapothordozás engedélyezése az Azure Active Directoryban](enterprise-state-roaming-enable.md) |Az Enterprise State Roaming minden prémium szintű Azure Active Directory (Azure AD) előfizetéssel rendelkező szervezet számára elérhető. Az Azure AD-előfizetés betöltésével kapcsolatos további információkért tekintse meg az [Azure AD termékoldalát.](https://azure.microsoft.com/services/active-directory) |
| [Beállítások és adatroaming GYIK](enterprise-state-roaming-faqs.md) |Ez a cikk választ ad a rendszergazdák által a beállításokkal és az alkalmazásadatok szinkronizálásával kapcsolatos kérdésekre. |
| [Csoportházirend és MDM-beállítások a beállítások szinkronizálásához](enterprise-state-roaming-group-policy-settings.md) |A Windows 10 csoportházirend- és mobileszköz-kezelési (MDM) házirend-beállításokat biztosít a beállítások szinkronizálásának korlátozására. |
| [Windows 10 roaming beállítások referenciája](enterprise-state-roaming-windows-settings-reference.md) |A Windows 10-ben barangolandó és/vagy biztonsági másolatot tartalmazó beállítások listája. |
| [hibaelhárítással](enterprise-state-roaming-troubleshooting.md) |Ez a cikk néhány alapvető hibaelhárítási lépésen megy keresztül, és tartalmazza az ismert problémák listáját. |

## <a name="next-steps"></a>További lépések

A vállalati állami barangolás engedélyezéséről a [Vállalati állami barangolás engedélyezése](enterprise-state-roaming-enable.md)című témakörben talál további információt.
