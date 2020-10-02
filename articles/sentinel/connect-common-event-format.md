---
title: CEF-adatkapcsolatok összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Csatlakoztasson egy olyan külső megoldást, amely Common Event Format (CEF) üzeneteket küld az Azure Sentinelnek egy Linux rendszerű géppel, proxyként.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: d63893ab219854a270652da38c474e3ccad83abc
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630508"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A külső megoldás összekötése a közös esemény formátumával

Ha olyan külső megoldást csatlakoztat, amely CEF-üzeneteket küld, három lépést kell megtennie az Azure Sentinel szolgáltatással való csatlakozáshoz:

1. lépés: [a CEF csatlakoztatása syslog/CEF-továbbító üzembe helyezésével](connect-cef-agent.md) 2. lépés: a [megoldásra vonatkozó lépések végrehajtása](connect-cef-solution-config.md) 3. lépés: a [kapcsolat ellenőrzése](connect-cef-verify.md)

Ez a cikk leírja, hogyan működik a kapcsolat, biztosítja az előfeltételeket, és ismerteti az ügynök üzembe helyezésének lépéseit olyan biztonsági megoldásokban, amelyek a syslog-ra épülő Common Event Format (CEF) üzeneteket küldenek. 

> [!NOTE] 
> Az adattárolást annak a munkaterületnek a földrajzi helye tárolja, amelyen az Azure Sentinel alkalmazást futtatja.

Ahhoz, hogy ez a kapcsolat elérhető legyen, telepítenie kell egy syslog-továbbító kiszolgálót a készülék és az Azure Sentinel közötti kommunikáció támogatásához.  A kiszolgáló egy dedikált linuxos gépről (VM vagy helyszíni) áll, amelyen telepítve van a Linux rendszerhez készült Log Analytics-ügynök. 

Az alábbi ábra az Azure-beli linuxos virtuális gépek esetén történő telepítést ismerteti:

 ![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Másik lehetőségként ez a beállítás akkor is előfordulhat, ha egy másik felhőben vagy egy helyszíni gépen található virtuális gépet használ: 

 ![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Biztonsági szempontok

Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. A következő útmutatást követve javíthatja a gép biztonsági konfigurációját:  [biztonságos virtuális gép az Azure-ban](../virtual-machines/security-policy.md), [ajánlott eljárások a hálózati biztonsághoz](../security/fundamentals/network-best-practices.md).

Ahhoz, hogy TLS-kommunikációt lehessen használni a syslog-forrás és a syslog-továbbító között, konfigurálnia kell a syslog démont (rsyslog vagy syslog-ng) a TLS-vel való kommunikációhoz: a [syslog-forgalom titkosítása a TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), a [naplózási üzenetek titkosítása a TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)használatával.
 
## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a proxyként használt Linux-gép az alábbi operációs rendszerek egyikét futtatja:

- 64 bites
  - CentOS 7 és alverziók, valamint magasabb (nem 6)
  - Amazon Linux 2017,09
  - Oracle Linux 6 és 7
  - Red Hat Enterprise Linux (RHEL) Server 7 és alverziók, valamint magasabb (nem 6)
  - Debian GNU/Linux 8 és 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bites
   - CentOS 7
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 7
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
    - Győződjön meg arról, hogy a számítógépen fut-e a Python (2,7 vagy újabb)

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

