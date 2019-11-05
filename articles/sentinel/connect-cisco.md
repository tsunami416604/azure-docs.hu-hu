---
title: Cisco-adatkapcsolatok az Azure Sentinel-be | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódhat a Cisco-beli adatbázisokhoz az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 4985593c6fc86f7d80243990c1cce10ce25a2998
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498733"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>A Cisco ASA összekötése az Azure Sentinel-vel



Ez a cikk azt ismerteti, hogyan csatlakoztatható a Cisco ASA-berendezés az Azure Sentinelhez. A Cisco ASA adatösszekötővel könnyedén csatlakoztathatja a Cisco ASA-naplókat az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Cisco ASA használata az Azure Sentinelben további információkat biztosít a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 


## <a name="how-it-works"></a>Működési elv

Telepítenie kell egy ügynököt egy dedikált linuxos gépen (VM vagy helyszíni) a Cisco ASA és az Azure Sentinel közötti kommunikáció támogatásához. Az alábbi ábra az Azure-beli Linux rendszerű virtuális gépek esetén történő telepítést ismerteti.

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
 
 
1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, válassza a **Cisco ASA** elemet, majd **nyissa meg az összekötő lapot**. 

1. **A syslog-ügynök telepítése és konfigurálása**területen válassza ki a gép típusát, akár az Azure-t, akár a másik felhőt, akár a helyszínen. 
   > [!NOTE]
   > Mivel a következő lépésben a parancsfájl telepíti a Log Analytics ügynököt, és csatlakoztatja a gépet az Azure Sentinel-munkaterülethez, győződjön meg róla, hogy ez a számítógép nincs csatlakoztatva más munkaterülethez.
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`

1. Futtassa a következő szkriptet a proxy gépen.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. A szkript futtatása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.

 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>2\. lépés: a Cisco ASA-naplók továbbítása a syslog-ügynöknek

A Cisco ASA nem támogatja a CEF, ezért a rendszer syslog-ként küldi el a naplókat, és az Azure Sentinel-ügynök tudja, hogyan elemezheti őket úgy, mintha CEF naplókat. A Cisco ASA konfigurálása a syslog-üzenetek Azure-munkaterületre történő továbbításához a syslog-ügynök használatával:

Válassza a [syslog-üzenetek küldése egy külső syslog-kiszolgálónak lehetőséget](https://aka.ms/asi-syslog-cisco-forwarding), majd kövesse az utasításokat a kapcsolódás beállításához. Ha a rendszer kéri, használja ezeket a paramétereket:
- Állítsa a **portot** 514-re vagy az ügynökben beállított portra.
- Állítsa be az **syslog_ip** az ügynök IP-címére.

A Cisco-események Log Analytics vonatkozó sémájának használatához keresse meg a `CommonSecurityLog`kifejezést.

## <a name="step-3-validate-connectivity"></a>3\. lépés: a kapcsolat ellenőrzése

1. A Log Analytics megnyitásával győződjön meg arról, hogy a naplók a CommonSecurityLog séma használatával érkeznek.<br> Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

1. A szkript futtatása előtt javasoljuk, hogy küldjön üzeneteket a biztonsági megoldásból, hogy azok továbbítva legyenek a konfigurált syslog-proxy gépre. 
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`
1. Futtassa a következő szkriptet az ügynök, az Azure Sentinel és a biztonsági megoldás közötti kapcsolat vizsgálatához. Ellenőrzi, hogy a démon továbbítása megfelelően van-e konfigurálva, figyeli a megfelelő portokat, és hogy semmi sem blokkolja a démon és a Log Analytics ügynök közötti kommunikációt. A parancsfájl a "TestCommonEventFormat" nevű, a végpontok közötti kapcsolat vizsgálatához is elküldi a "" üzenetet. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`





## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a Cisco ASA-készülékekhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

