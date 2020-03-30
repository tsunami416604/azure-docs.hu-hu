---
title: CEF-adatok csatlakoztatása az Azure Sentinel Előzetes verzióhoz| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a CEF-adatokat az Azure Sentinelhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588348"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A külső megoldás csatlakoztatása a Közös eseményformátum használatával


Ha cef-üzeneteket küldő külső megoldást csatlakoztat, három lépésből áll az Azure Sentinelhez való csatlakozás:

1. LÉPÉS: [A CEF csatlakoztatása az ügynök 2.](connect-cef-agent.md) [Perform solution-specific steps](connect-cef-solution-config.md) [Verify connectivity](connect-cef-verify.md)

Ez a cikk ismerteti, hogyan működik a kapcsolat, előfeltételeket biztosít, és megadja a lépéseket az ügynök központi telepítéséhez a biztonsági megoldások, amelyek a Közös eseményformátum (CEF) üzeneteket küld a Syslog. 

> [!NOTE] 
> Az adatok annak a munkaterületnek a földrajzi helyén tárolódnak, amelyen az Azure Sentinelt futtatja.

Annak érdekében, hogy ezt a kapcsolatot, egy dedikált Linux-gépen (virtuális gépen vagy a helyszínen) egy ügynök üzembe helyezéséhez kell telepítenie a készülék és az Azure Sentinel közötti kommunikáció támogatása érdekében. Az alábbi ábra ismerteti a beállítás esetén egy Linux virtuális gép az Azure-ban.

 ![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Másik lehetőségként ez a beállítás akkor is létezik, ha egy virtuális gépet használ egy másik felhőben, vagy egy helyszíni gép. 

 ![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Biztonsági szempontok

Győződjön meg arról, hogy a számítógép biztonságát a szervezet biztonsági házirendjének megfelelően konfigurálja. Beállíthatja például, hogy a hálózat igazodjon a vállalati hálózati biztonsági házirendhez, és módosítsa a démon portjait és protokolljait, hogy megfeleljenek az ön igényeinek. A következő utasításokat használhatja a gép biztonsági konfigurációjának javításához:  [Biztonságos virtuális gép az Azure-ban](../virtual-machines/linux/security-policy.md), Gyakorlati tanácsok a hálózat [biztonságához.](../security/fundamentals/network-best-practices.md)

A biztonsági megoldás és a Syslog gép közötti TLS-kommunikáció használatához a Syslog démont (rsyslog vagy syslog-ng) kell konfigurálnia a TLS-ben való kommunikációhoz: [A Syslog Traffic titkosítása TLS -rsyslog,](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) [Log üzenetek titkosítása TLS -syslog-ng segítségével.](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

 
## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a proxyként használt Linux-gép az alábbi operációs rendszerek egyikét futtatja:

- 64 bites
  - CentOS 6 és 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 és 7
  - Red Hat Enterprise Linux Server 6 és 7
  - Debian GNU/Linux 8 és 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS és 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bites
   - CentOS, 6.
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 és 9
   - Ubuntu Linux 14.04 LTS és 16.04 LTS
 
 - Démon verziók
   - Syslog-ng: 2,1 - 3,22,1
   - Rsyslog: v8
  
 - Syslog RFC-k támogatott
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Győződjön meg arról, hogy a gép megfelel az alábbi követelményeknek is: 
- Engedélyek
    - Emelt szintű engedélyekkel (sudo) kell rendelkeznie a számítógépen. 
- Szoftverkövetelmények
    - Győződjön meg róla, hogy a Python fut a gépen



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a CEF-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)

