---
title: A Microsoft Threat Modeling Tool 4/9/2019 kiadása
titleSuffix: Azure
description: A fenyegetésmodellező eszköz kiadási megjegyzésekének dokumentálása
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269726"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>A Threat Modeling Tool 7.1.60408.1 - 4/9/2019 frissítés

A Microsoft Threat Modeling Tool (TMT) 7.1.60408.1-es verziója 2019.

- Új rajzsablonok az Azure Key Vaulthoz és az Azure Traffic Managerhez
- A TMT verziószáma mostantól megjelenik a kezdőképernyőn
- A támogatási hivatkozások frissültek
- Hibajavítások

## <a name="feature-changes"></a>Funkcióváltozások

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Új rajzsablonok az Azure Key Vaulthoz és az Azure Traffic Managerhez

![Azure Key Vault-rajzsablon](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Az Azure Key Vault és az Azure Traffic Manager új rajzsablonjai és fenyegetései lettek hozzáadva az Azure stencilkészletéhez. Az Azure stencil-készleten alapuló modellek megnyitásakor a rendszer kéri a felhasználókat a modellhez társított sablon frissítésére. Az Azure stencil-készleten alapuló modell frissítése manuálisan is kezdeményezhető a "Fájl" menü "Sablon alkalmazása" parancsával, és a legújabb Azure Cloud Services.tb7 fájl újbóli alkalmazásával.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>A TMT verziószáma mostantól megjelenik a kezdőképernyőn

A fenyegetésmodellező eszköz ügyfélverziója mostantól megjelenik a könnyebb hozzáférés érdekében az alkalmazás kezdőképernyőjén.

![Azure Key Vault-rajzsablon](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>A támogatási hivatkozások frissültek

Az eszköz összes támogatási linkje frissült, [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) hogy a felhasználókat az MSDN-fórum helyett irányítsa.

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET verzió szükséges
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A fenyegetésmodellező eszköz dokumentációja [a docs.microsoft.com](threat-modeling-tool.md)található, és információkat tartalmaz [az eszköz használatával kapcsolatban.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
