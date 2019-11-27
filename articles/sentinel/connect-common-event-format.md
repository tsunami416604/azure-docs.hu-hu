---
title: CEF-adatkapcsolatok összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a CEF-adatbázisokhoz az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0fbdba5c3fbfdfab5267407ccec9c611d74a5e02
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463981"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A külső megoldás összekötése a közös esemény formátumával



Ez a cikk azt ismerteti, hogyan kapcsolódhat az Azure Sentinel szolgáltatáshoz olyan külső biztonsági megoldásokkal, amelyek Common Event Format-(CEF-) üzeneteket küldenek a syslog-ben. 

> [!NOTE] 
> Az adattárolást annak a munkaterületnek a földrajzi helye tárolja, amelyen az Azure Sentinel alkalmazást futtatja.

## <a name="how-it-works"></a>Működés

Telepítenie kell egy ügynököt egy dedikált linuxos gépen (VM vagy helyszíni) a készülék és az Azure Sentinel közötti kommunikáció támogatásához. Az alábbi ábra az Azure-beli Linux rendszerű virtuális gépek esetén történő telepítést ismerteti.

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
- Telepíti a Log Analytics ügynököt, és szükség szerint konfigurálja a syslog-üzenetek figyeléséhez.
- Úgy konfigurálja a syslog démont, hogy a 514-es TCP-porton keresztül figyelje a syslog-üzeneteket, majd csak a CEF üzeneteket továbbítja a Log Analytics ügynöknek a 25226-es TCP-porton keresztül.
- Beállítja a syslog-ügynököt az adatok gyűjtésére és biztonságos küldésére az Azure Sentinel számára, ahol a rendszer elemzi és gazdagítja az adatokat.
 
 
1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre, és válassza a **Common Event Format (CEF)** lehetőséget, majd **nyissa meg az összekötő lapot**. 

1. **A syslog-ügynök telepítése és konfigurálása**területen válassza ki a gép típusát, akár az Azure-t, akár a másik felhőt, akár a helyszínen. 
   > [!NOTE]
   > Mivel a következő lépésben a parancsfájl telepíti a Log Analytics ügynököt, és csatlakoztatja a gépet az Azure Sentinel-munkaterülethez, győződjön meg róla, hogy ez a számítógép nincs csatlakoztatva más munkaterülethez.
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`

1. Futtassa a következő szkriptet a proxy gépen.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. A szkript futtatása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.


## <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2\. lépés: a biztonsági megoldás konfigurálása CEF-üzenetek küldésére

1. A készüléken ezeket az értékeket úgy kell beállítania, hogy a berendezés a szükséges formátumú naplókat a szükséges formátumban küldje el az Azure Sentinel syslog-ügynöknek a Log Analytics ügynök alapján. Ezeket a paramétereket módosíthatja a berendezésben, ha az Azure Sentinel-ügynök syslog démonán is módosítja őket.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-cím – ügyeljen arra, hogy a CEF üzeneteket az erre a célra kijelölt virtuális gép IP-címére küldje el.

   > [!NOTE]
   > Ez a megoldás a syslog RFC 3164 vagy az RFC 5424 szolgáltatást támogatja.


1. A CEF-események Log Analytics vonatkozó sémájának használatához keresse meg a `CommonSecurityLog`kifejezést.

## <a name="step-3-validate-connectivity"></a>3\. lépés: a kapcsolat ellenőrzése

1. A Log Analytics megnyitásával győződjön meg arról, hogy a naplók a CommonSecurityLog séma használatával érkeznek.<br> Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

1. A szkript futtatása előtt javasoljuk, hogy küldjön üzeneteket a biztonsági megoldásból, hogy azok továbbítva legyenek a konfigurált syslog-proxy gépre. 
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`
1. Futtassa a következő szkriptet az ügynök, az Azure Sentinel és a biztonsági megoldás közötti kapcsolat vizsgálatához. Ellenőrzi, hogy a démon továbbítása megfelelően van-e konfigurálva, figyeli a megfelelő portokat, és hogy semmi sem blokkolja a démon és a Log Analytics ügynök közötti kommunikációt. A parancsfájl a "TestCommonEventFormat" nevű, a végpontok közötti kapcsolat vizsgálatához is elküldi a "" üzenetet. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

