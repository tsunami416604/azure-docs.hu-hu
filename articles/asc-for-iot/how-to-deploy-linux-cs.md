---
title: Útmutató a telepítése és központi telepítése Linux C# ügynök az Azure Security Center az IoT-előzetes verzió |} A Microsoft Docs
description: Útmutató az Azure Security Center az IoT-ügynök telepítése 32 és 64 bites Linux rendszeren.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: be4c663d3a1e99ef67cbbbc2f39b315f1080125c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758331"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Üzembe helyezése az Azure Security Center az IoT C#-alapú Linux-ügynök biztonsági

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az útmutató azt ismerteti, hogyan telepítése és központi telepítése az Azure Security Center (ASC) IoT C#-security-ügynök Linux-alapú.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Az ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformok és az ügynök változatban érhetők el: [válassza ki a megfelelő biztonsági ügynök](how-to-deploy-agent.md).

1. A biztonsági ügynök telepítése, a helyi rendszergazdai jogosultsággal a telepíteni kívánt számítógépen szükségesek. 

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök telepítése, tegye a következőket:

1. Töltse le a legújabb verzióra a gépre az [Github](https://aka.ms/iot-security-github-cs).

1. Bontsa ki a csomag tartalmát, és keresse meg a _/Install_ mappát.

1. Futó jogosultságot a **InstallSecurityAgent parancsfájl** futtatásával `chmod +x InstallSecurityAgent.sh` 

1. Ezután futtassa: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Lásd: [hitelesítés konfigurálásának](concept-security-agent-authentication-methods.md) hitelesítési paraméterekkel kapcsolatos további részletekért.

A parancsfájl a következő műveleteket hajtja végre:

- Előfeltételek telepítése.

- A szolgáltatás felhasználó hozzáadása (az interaktív bejelentkezés le van tiltva).

- Telepíti az ügynököt, mint egy **démon** – Ez azt feltételezi, hogy az eszköz **systemd** Szolgáltatáskezelési.

- Konfigurálja a **sudoers** az ügynök bizonyos feladatok végrehajtásához rendszergazdaként.

- Konfigurálja az ügynököt, a megadott hitelesítési paraméterekkel.


További segítségre van szüksége, futtassa a szkriptet a – help paraméter: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Az ügynök eltávolítása

Az ügynök eltávolításához futtassa a parancsfájlt a – u paraméterrel: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Távolítsa el nem távolítja el a hiányzó előfeltételeket, hogy megtörtént-e a telepítés során.

## <a name="troubleshooting"></a>Hibaelhárítás  

1. A központi telepítés állapotának ellenőrzéséhez futtassa:

    `systemctl status ASCIoTAgent.service`

2. Engedélyezze a naplózást.  
   Ha az ügynök nem indul el, kapcsolja be a naplózást, és részletes tájékoztatást.

   A naplózás által bekapcsolása:

   1. Nyissa meg a konfigurációs fájlt szerkesztésre minden olyan Linux-szerkesztőben:

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
       > Javasoljuk, hogy naplózás bekapcsolása **ki** hibaelhárítási befejeződése után. Naplózás és **a** növeli a fájl méretét és az adatok használatának naplózása.

   1. Indítsa újra az ügynök futtatásával:

       `systemctl restart ASCIoTAgent.service`

   1. A naplófájlban talál a hibával kapcsolatos további információkat.  

       Naplófájl helye a következő: `/var/ASCIoTAgent/IotAgentLog.log`

       A fájl elérési útjának a választott név szerint módosítsa a **logFilePath** 2. lépés. 

## <a name="next-steps"></a>További lépések

- Olvassa el az IoT-szolgáltatást az ASC [áttekintése](overview.md)
- További információ az ASC a IoT [architektúra](architecture.md)
- Engedélyezze a [szolgáltatás](quickstart-onboard-iot-hub.md)
- Olvassa el a [– gyakori kérdések](resources-frequently-asked-questions.md)
- Megismerheti [riasztások](concept-security-alerts.md)