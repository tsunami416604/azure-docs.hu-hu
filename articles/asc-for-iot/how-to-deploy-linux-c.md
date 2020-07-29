---
title: A Linux C Agent telepítése & üzembe helyezése
description: Ismerje meg, hogyan telepítheti a IoT-ügynök Azure Security Center a 32 bites és a 64-bites Linux rendszeren.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311195"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>C-alapú Azure Security Center for IoT biztonsági ügynök üzembe helyezése Linuxon

Ez az útmutató ismerteti, hogyan telepítheti és telepítheti a IoT C-alapú biztonsági ügynökének Azure Security Center a Linux rendszeren.

Ebből az útmutatóból a következőket tanulhatja meg:

> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Ügynök eltávolítása
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Más platformokon és ügynöki Ízeknél tekintse meg [a megfelelő biztonsági ügynök kiválasztása](how-to-deploy-agent.md)című témakört.

1. A biztonsági ügynök telepítéséhez helyi rendszergazdai jogosultságok szükségesek azon a gépen, amelyet telepíteni kíván (sudo).

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés

A biztonsági ügynök telepítéséhez és telepítéséhez használja a következő munkafolyamatot:

1. Töltse le a legújabb verziót a gépére a [githubról](https://aka.ms/iot-security-github-c).

1. Bontsa ki a csomag tartalmát, és navigáljon a _/src/Installation_ mappára.

1. A következő parancs futtatásával adja hozzá a futó engedélyeket a **InstallSecurityAgent parancsfájlhoz** :

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ezután futtassa a következőt:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   A hitelesítési paraméterekkel kapcsolatos további információkért tekintse [meg a hitelesítés konfigurálása](concept-security-agent-authentication-methods.md) című témakört.

Ez a szkript a következő függvényt hajtja végre:

1. Telepíti az előfeltételeket.

1. Szolgáltatásbeli felhasználó (interaktív bejelentkezéssel letiltva) hozzáadásával.

1. Az ügynököt **démonként** telepíti, feltételezi, hogy az eszköz a szolgáltatás felügyeletéhez használt **rendszert** használja.

1. Konfigurálja az ügynököt a megadott hitelesítési paraméterekkel.

További segítségért futtassa a parancsfájlt a – Súgó paraméterrel:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolításához futtassa a szkriptet a – uninstall paraméterrel:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Hibaelhárítás

A telepítés állapotának ellenőrzéséhez futtassa a következőket:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>További lépések

- A IoT-szolgáltatás [áttekintésének](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- [Biztonsági riasztások](concept-security-alerts.md) ismertetése
