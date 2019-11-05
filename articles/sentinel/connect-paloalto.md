---
title: A Palo Alto Networks-adatkapcsolatok és az Azure Sentinel összekötése | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatók a Palo Alto-hálózatok az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 8b1331eb99fd3d061d231ae48c40a721911e74db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475856"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>A Palo Alto-hálózatok összekapcsolhatók az Azure Sentinel-vel



Ez a cikk azt ismerteti, hogyan csatlakoztatható a Palo Alto Networks-berendezés az Azure Sentinelhez. A Palo Alto Networks adatösszekötő segítségével könnyedén csatlakoztathatja a Palo Alto hálózati naplókat az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Palo Alto Networks használata az Azure Sentinelben további információkat nyújt a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 


## <a name="how-it-works"></a>Működési elv

A Palo Alto Networks és az Azure Sentinel közötti kommunikáció támogatásához telepítenie kell egy ügynököt egy dedikált linuxos gépen (VM vagy helyszíni). Az alábbi ábra az Azure-beli Linux rendszerű virtuális gépek esetén történő telepítést ismerteti.

 ![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Másik lehetőségként ez a beállítás akkor is fennáll, ha egy másik felhőben vagy egy helyszíni gépen használ virtuális gépet. 

 ![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Biztonsági szempontok

Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. A következő útmutatást követve javíthatja a gép biztonsági konfigurációját:  [biztonságos virtuális gép az Azure-ban](../virtual-machines/linux/security-policy.md), [ajánlott eljárások a hálózati biztonsághoz](../security/fundamentals/network-best-practices.md).

Ha TLS-kommunikációt szeretne használni a biztonsági megoldás és a syslog-gép között, konfigurálnia kell a syslog démont (rsyslog vagy syslog-ng) a TLS protokollon keresztüli kommunikációhoz: a [syslog-forgalom titkosítása TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), a [naplózási üzenetek titkosítása TLS használatával – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a proxyként használt Linux-gép az alábbi operációs rendszerek egyikét futtatja:

- 64 bites
  - CentOS 6 és 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 és 7
  - Red Hat Enterprise Linux 6. és 7. kiszolgáló
  - Debian GNU/Linux 8 és 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bites
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 és 9
   - Ubuntu Linux 14,04 LTS és 16,04 LTS
 
 - Daemon-verziók
   - Syslog-ng: 2,1 – 3.22.1
   - Rsyslog: V8
  
 - A syslog RFC-k támogatottak
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Győződjön meg arról, hogy a gép a következő követelményeknek is megfelel: 
- Engedélyek
    - Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. 
- Szoftverkövetelmények
    - Győződjön meg arról, hogy a Python fut a gépen
## <a name="step-1-deploy-the-agent"></a>1\. lépés: az ügynök üzembe helyezése

Ebben a lépésben ki kell választania azt a Linux-gépet, amely proxyként fog működni az Azure Sentinel és a biztonsági megoldás között. Futtatnia kell egy parancsfájlt a proxy gépen, amely a következőket teszi:
- Telepíti a Log Analytics ügynököt, és szükség szerint konfigurálja a syslog-üzeneteket a 514-as porton a TCP protokollon keresztül, és elküldi a CEF-üzeneteket az Azure Sentinel-munkaterületre.
- Konfigurálja a syslog démont, hogy továbbítsa a CEF üzeneteket a Log Analytics-ügynöknek az 25226-es port használatával.
- Beállítja a syslog-ügynököt, hogy összegyűjtse az adatokat, és biztonságosan küldje el Log Analyticsba, ahol a rendszer elemzi és gazdagítja azokat.
 
 
1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** lehetőségre, válassza a **Palo Alto Networks** elemet, majd **nyissa meg az összekötő lapot**. 

1. **A syslog-ügynök telepítése és konfigurálása**területen válassza ki a gép típusát, akár az Azure-t, akár a másik felhőt, akár a helyszínen. 
   > [!NOTE]
   > Mivel a következő lépésben a parancsfájl telepíti a Log Analytics ügynököt, és csatlakoztatja a gépet az Azure Sentinel-munkaterülethez, győződjön meg róla, hogy ez a számítógép nincs csatlakoztatva más munkaterülethez.
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`

1. Futtassa a következő szkriptet a proxy gépen.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. A szkript futtatása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>2\. lépés: a Palo Alto hálózati naplók továbbítása a syslog-ügynökhöz

A Palo Alto-hálózatok konfigurálása a syslog-üzenetek CEF formátumban való továbbításához az Azure-munkaterületen a syslog-ügynök használatával:
1.  Nyissa meg a [Common Event Format (CEF) konfigurációs útmutatóit](https://docs.paloaltonetworks.com/resources/cef) , és töltse le a készülék típusának PDF-fájlját. A CEF események gyűjtéséhez kövesse az útmutató utasításait a Palo Alto Networks-berendezés beállításához. 

1.  Lépjen a [syslog-figyelés konfigurálása](https://aka.ms/asi-syslog-paloalto-forwarding) elemre, és kövesse a 2. és a 3. lépést a Palo Alto Networks-készülékről az Azure sentinelre történő CEF-továbbítás konfigurálásához.

    1. Győződjön meg arról, hogy a **syslog-kiszolgáló formátumát** a **BSD**értékre állítja be.

       > [!NOTE]
       > Előfordulhat, hogy a PDF-fájl másolási/beillesztési műveletei megváltoztatják a szöveget, és véletlenszerű karaktereket szúrnak be. Ennek elkerüléséhez másolja a szöveget egy Szerkesztőbe, és távolítson el minden olyan karaktert, amely megtöri a napló formátumát a beillesztés előtt, ahogy az ebben a példában látható.
 
        ![CEF szöveges másolási probléma](./media/connect-cef/paloalto-text-prob1.png)

2. Ha a Log Analytics vonatkozó sémát szeretné használni a Palo Alto Networks-eseményekhez, keresse meg a **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>3\. lépés: a kapcsolat ellenőrzése

1. A Log Analytics megnyitásával győződjön meg arról, hogy a naplók a CommonSecurityLog séma használatával érkeznek.<br> Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

1. A szkript futtatása előtt javasoljuk, hogy küldjön üzeneteket a biztonsági megoldásból, hogy azok továbbítva legyenek a konfigurált syslog-proxy gépre. 
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`
1. Futtassa a következő szkriptet az ügynök, az Azure Sentinel és a biztonsági megoldás közötti kapcsolat vizsgálatához. Ellenőrzi, hogy a démon továbbítása megfelelően van-e konfigurálva, figyeli a megfelelő portokat, és hogy semmi sem blokkolja a démon és a Log Analytics ügynök közötti kommunikációt. A parancsfájl a "TestCommonEventFormat" nevű, a végpontok közötti kapcsolat vizsgálatához is elküldi a "" üzenetet. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`







## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Palo Alto hálózati berendezések az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

