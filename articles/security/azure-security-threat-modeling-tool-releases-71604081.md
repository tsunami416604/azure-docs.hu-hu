---
title: A Threat Modeling Tool kiadásai – Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
description: Dokumentálja a threat modeling tool kibocsátási megjegyzései
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501903"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool frissítés kiadása 7.1.60408.1 – 4/9/2019

Verzió 7.1.60408.1, a Microsoft Threat Modeling eszköz (TMT) fel lett oldva. április 9-es verzióját 2019, és tartalmazza a következő módosításokat:

- Az Azure Key Vault és az Azure Traffic Manager új rajzsablonokhoz
- A kezdőképernyőn mostantól látható a TMT verziószáma
- Frissítve lett-e a támogatási oldalak hivatkozásai
- Hibajavítások

## <a name="feature-changes"></a>A szolgáltatás módosítások

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Az Azure Key Vault és az Azure Traffic Manager új rajzsablonokhoz

![Az Azure Key Vault rajzsablonon](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Új rajzsablonokhoz és a fenyegetések ellen az Azure Key Vault és az Azure Traffic Manager az Azure rajzsablonon csoporthoz lettek hozzáadva. Az Azure rajzsablonon alapján modelleket megnyitásakor kéri a felhasználótól a modell társított sablon frissítéséhez. Az Azure rajzsablonon alapján modellek is is manuálisan kell kezdeményeznie a "File" menü "Sablon alkalmazása" paranccsal, és a legújabb Azure-felhő Services.tb7 fájl újbóli.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>A kezdőképernyőn mostantól látható a TMT verziószáma

A Threat Modeling Tool ügyfél verziója mostantól a könnyű kezelés alkalmazása a kezdőképernyő jelenik meg.

![Az Azure Key Vault rajzsablonon](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Frissítve lett-e a támogatási oldalak hivatkozásai

Az összes támogatási oldalak hivatkozásai belül az eszköz frissítve lett-e közvetlen felhasználók [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) ahelyett, hogy egy MSDN-fórum.

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - [A Microsoft Windows 10 Évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió megadása kötelező
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Internetkapcsolat szükséges az eszköz, valamint a sablonok a frissítések fogadásához.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációjában található [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), és tartalmaz adatokat [az eszközzel kapcsolatos](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>További lépések

Töltse le a legújabb verzióját a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).