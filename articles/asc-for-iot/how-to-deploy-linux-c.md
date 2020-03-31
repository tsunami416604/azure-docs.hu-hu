---
title: Útmutató az Azure Security Center for IoT-ügynök Linux C-ügynökének telepítéséhez és üzembe helyezéséhez| Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure Security Center for IoT-ügynök 32 bites és 64 bites Linuxra is.
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
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68812727"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>C-alapú Azure Security Center for IoT biztonsági ügynök üzembe helyezése Linuxon

Ez az útmutató bemutatja, hogyan telepítheti és telepítheti az Azure Security Center for IoT C-alapú biztonsági ügynök Linuxon.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformok és ügynök ízek, [lásd: Válassza ki a megfelelő biztonsági ügynök](how-to-deploy-agent.md).

1. A biztonsági ügynök üzembe helyezéséhez helyi rendszergazdai jogok szükségesek azon a gépen, amelyen telepíteni szeretné (sudo).

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök telepítéséhez és telepítéséhez használja a következő munkafolyamatot:


1. Töltse le a legújabb verziót a gépére a [GitHubról.](https://aka.ms/iot-security-github-c)

1. Bontsa ki a csomag tartalmát, és keresse meg a _/src/installation_ mappát.

1. Adjon hozzá futó engedélyeket az **InstallSecurityAgent parancsfájlhoz** a következő parancs futtatásával:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ezután fusson: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   [A hitelesítési](concept-security-agent-authentication-methods.md) paraméterekkel kapcsolatos további információkért olvassa el a Hitelesítés konfigurálása című témakört.

Ez a parancsfájl a következő funkciót hajtja végre:

1. Telepíti az előfeltételeket.

2. Hozzáad egy szolgáltatásfelhasználót (az interaktív bejelentkezés le van tiltva).

3. Az ügynököt **démonként telepíti** – feltételezi, hogy az eszköz a szolgáltatáskezeléshez **rendszerezve** működik.

4. Az ügynököt a megadott hitelesítési paraméterekkel konfigurálja. 

További segítségért futtassa a parancsfájlt a –help paraméterrel: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolításához futtassa a parancsfájlt a –-uninstall paraméterrel:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Hibaelhárítás
Ellenőrizze a központi telepítés állapotát a következő futtatásával:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>További lépések
- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el a [GYIK-et](resources-frequently-asked-questions.md)
- A [biztonsági riasztások ismertetése](concept-security-alerts.md)
