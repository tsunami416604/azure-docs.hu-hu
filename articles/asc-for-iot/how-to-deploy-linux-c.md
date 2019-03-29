---
title: Útmutató telepítésére és üzembe helyezésére az ASC-ügynök Linux C IoT ügynök előzetes verzió |} A Microsoft Docs
description: Ismerje meg az ASC IoT-ügynök telepítése 32 és 64 bites Linux rendszeren.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 2b18a09e5b79e7b3d3ea837e937397ac92491f9f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619849"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>ASC biztonsági IoT C-alapú Linux-ügynök telepítése

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az útmutató ismerteti, hogyan telepítésére és üzembe helyezésére az ASC a IoT C-alapú biztonsági ügynök Linux rendszeren.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Az ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformok és az ügynök változatban érhetők el: [válassza ki a megfelelő biztonsági ügynök](how-to-deploy-agent.md).

1. A biztonsági ügynök telepítése, a helyi rendszergazdai jogosultságokkal a számítógépen (sudo) a telepíteni kívánt szükségesek.

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

Telepítse és a biztonsági ügynök telepítése, tegye a következőket:


1. Töltse le a legújabb verzióra a gépre az [Github](https://aka.ms/iot-security-github-c).

1. Bontsa ki a csomag tartalmát, és keresse meg a _/Install_ mappát.

1. Futó jogosultságot a **InstallSecurityAgent parancsfájl** a következő futtatásával:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ezután futtassa: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Lásd: [hitelesítés konfigurálásának](concept-security-agent-authentication-methods.md) hitelesítési paraméterekkel kapcsolatos további részletekért.

A parancsfájl a következő műveleteket hajtja végre:

1. Előfeltételek telepítése.

2. A szolgáltatás felhasználó hozzáadása (az interaktív bejelentkezés le van tiltva).

3. Telepíti az ügynököt, mint egy **démon** -feltételezi, hogy az eszköz **systemd** Szolgáltatáskezelési.

4. Konfigurálja az ügynököt, a megadott hitelesítési paraméterekkel. 

További segítségre van szüksége, futtassa a szkriptet a – help paraméter: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Az ügynök eltávolítása

Az ügynök eltávolításához futtassa a parancsfájlt a –-paraméter eltávolítása:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Hibaelhárítás
A központi telepítés állapotának ellenőrzéséhez futtassa:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>További lépések
- Olvassa el az IoT-szolgáltatást az ASC [áttekintése](overview.md)
- További információ az ASC a IoT [architektúra](architecture.md)
- Engedélyezze a [szolgáltatás](quickstart-onboard-iot-hub.md)
- Olvassa el a [– gyakori kérdések](resources-frequently-asked-questions.md)
- Megismerheti [biztonsági riasztások](concept-security-alerts.md)