---
title: CEF-adatkapcsolatok összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a CEF-adatbázisokhoz az Azure Sentinel szolgáltatáshoz.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588348"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A külső megoldás összekötése a közös esemény formátumával


Ha olyan külső megoldást csatlakoztat, amely CEF-üzeneteket küld, három lépést kell megtennie az Azure Sentinel szolgáltatással való csatlakozáshoz:

1\. lépés: [a CEF csatlakoztatása az ügynök üzembe helyezésével](connect-cef-agent.md) 2. lépés: a [megoldásra vonatkozó lépések végrehajtása](connect-cef-solution-config.md) 3. lépés: a [kapcsolat ellenőrzése](connect-cef-verify.md)

Ez a cikk leírja, hogyan működik a kapcsolat, biztosítja az előfeltételeket, és megadja az ügynök üzembe helyezésének lépéseit olyan biztonsági megoldásokban, amelyek a syslog-alapú Common Event Format (CEF) üzeneteket küldenek. 

> [!NOTE] 
> Az adattárolást annak a munkaterületnek a földrajzi helye tárolja, amelyen az Azure Sentinel alkalmazást futtatja.

Ennek a kapcsolatnak a létrehozásához telepítenie kell egy ügynököt egy dedikált linuxos gépen (VM vagy helyszíni) a készülék és az Azure Sentinel közötti kommunikáció támogatása érdekében. Az alábbi ábra az Azure-beli Linux rendszerű virtuális gépek esetén történő telepítést ismerteti.

 ![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Másik lehetőségként ez a beállítás akkor is fennáll, ha egy másik felhőben vagy egy helyszíni gépen használ virtuális gépet. 

 ![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Biztonsági szempontok

Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. A következő útmutatást követve javíthatja a gép biztonsági konfigurációját:  [biztonságos virtuális gép az Azure-ban](../virtual-machines/linux/security-policy.md), [ajánlott eljárások a hálózati biztonsághoz](../security/fundamentals/network-best-practices.md).

Ahhoz, hogy TLS-kommunikációt lehessen használni a biztonsági megoldás és a syslog-gép között, konfigurálnia kell a syslog démont (rsyslog vagy syslog-ng) a TLS-vel való kommunikációhoz: a [syslog-forgalom titkosítása TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), a [naplózási üzenetek titkosítása a TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)használatával.

 
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



## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

