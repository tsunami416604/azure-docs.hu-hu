---
title: Útmutató a Azure Security Center linuxos C# ügynökének telepítéséhez és telepítéséhez a IoT-hez | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a IoT-ügynök Azure Security Center a 32 bites és a 64-bites Linux rendszeren.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 0d77a1be2a3469282dabb646b02c43e350313ce5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596304"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>A Linux rendszerhez C# készült IoT-alapú biztonsági ügynök Azure Security Center üzembe helyezése


Ez az útmutató ismerteti, hogyan telepítheti és telepítheti a C#IoT-alapú biztonsági ügynök Azure Security Center a Linux rendszeren.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Az ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformokon és ügynöki Ízeknél tekintse meg [a megfelelő biztonsági ügynök kiválasztása](how-to-deploy-agent.md)című témakört.

1. A biztonsági ügynök telepítéséhez helyi rendszergazdai jogosultságok szükségesek azon a gépen, amelyre telepíteni kívánja a szolgáltatást. 

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Töltse le a legújabb verziót a gépére a [githubról](https://aka.ms/iot-security-github-cs).

1. Bontsa ki a csomag tartalmát, és navigáljon a _/install_ mappára.

1. Futó engedélyek hozzáadása a **InstallSecurityAgent parancsfájlhoz** futtatásával`chmod +x InstallSecurityAgent.sh` 

1. Ezután futtassa a következőt: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   További információ a hitelesítési paraméterekről: [a hitelesítés konfigurálása](concept-security-agent-authentication-methods.md).

Ez a szkript a következő műveleteket hajtja végre:

- Telepíti az előfeltételeket.

- Szolgáltatásbeli felhasználó (interaktív bejelentkezéssel letiltva) hozzáadásával.

- Az ügynököt démonként telepíti, feltételezi, hogy az eszköz a klasszikus üzemi modellhez tartozó **rendszert** használja.

- Úgy konfigurálja a sudoers-t, hogy az ügynök elvégezzen bizonyos feladatokat root-ként.

- A megadott hitelesítési paraméterekkel konfigurálja az ügynököt.


További segítségért futtassa a parancsfájlt a – Súgó paraméterrel:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Az ügynök eltávolítása

Az ügynök eltávolításához futtassa a parancsfájlt az – u paraméterrel: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Az eltávolítás nem távolítja el a telepítés során telepített hiányzó előfeltételeket.

## <a name="troubleshooting"></a>Hibaelhárítás  

1. A telepítés állapotának ellenőrzéséhez futtassa a következőket:

    `systemctl status ASCIoTAgent.service`

2. Naplózás engedélyezése.  
   Ha az ügynök nem indul el, a naplózás bekapcsolásával további információkat érhet el.

   A naplózás bekapcsolása:

   1. Nyissa meg a konfigurációs fájlt szerkesztésre bármely Linux-szerkesztőben:

        `vi /var/ASCIoTAgent/General.config`

   1. Szerkessze a következő értékeket: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       A **logFilePath** érték konfigurálható. 

       > [!NOTE]
       > A hibaelhárítás befejezése után javasoljuk a naplózás kikapcsolását. A naplózás bekapcsolásával megnő **a** naplófájl mérete és az adatfelhasználás.

   1. Indítsa újra az ügynököt a futtatásával:

       `systemctl restart ASCIoTAgent.service`

   1. A hibával kapcsolatos további információkért tekintse meg a naplófájlt.  

       A naplófájl helye:`/var/ASCIoTAgent/IotAgentLog.log`

       Módosítsa a fájl helyének elérési útját a 2. lépésben a **logFilePath** választott név szerint. 

## <a name="next-steps"></a>További lépések

- A IoT-szolgáltatás áttekintésének [](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- A [riasztások](concept-security-alerts.md) ismertetése