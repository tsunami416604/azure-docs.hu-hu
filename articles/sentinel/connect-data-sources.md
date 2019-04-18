---
title: Csatlakozás Azure-on Előzetesben Sentinel-adatforrások? |} A Microsoft Docs
description: Ismerje meg, hogyan Azure Sentinel-adatforrásokhoz csatlakozhat.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: ba0f584e8026fe3828ec79c4b6c0ff5a0bb89f5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492362"
---
# <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



A fedélzeti Azure Sentinel először az adatforrásokhoz való kapcsolódáshoz. Az Azure-Sentinel együttműködik a Microsoft-megoldások, a box és a valós idejű integráció, beleértve a Microsoft veszélyforrások elleni védelmi megoldások és a Microsoft 365 forrásból, például az Office 365, Azure AD-ben az Azure ATP, azonnal összekötőket és A Microsoft Cloud App Security, és többet. Emellett számos beépített összekötőkről, a szélesebb körű biztonsági ökoszisztéma nem Microsoft-megoldások. Az adatforrások csatlakoztatása az Azure-Sentinel, valamint a Syslog vagy a REST-API formátum. esemény közös is használhatja.  

![Adatgyűjtők](./media/collect-data/collect-data-page.png)

## <a name="data-connection-methods"></a>Kapcsolat metody dat

Az alábbi adatok kapcsolat módszerek az Azure-Sentinel támogatja:

- **Microsoft-szolgáltatások**:<br> Microsoft-szolgáltatásokhoz kapcsolódó natív módon, kihasználva ki a szolgáltatással az Azure alapja, néhány kattintással csatlakozhat a következő megoldásokat:
    - [Office 365](connect-office-365.md)
    - [Az Azure AD naplózási naplók és a bejelentkezések](connect-azure-active-directory.md)
    - [Azure-tevékenység](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Az Azure komplex veszélyforrások elleni védelem](connect-azure-atp.md)
    - [A cloud App Security](connect-cloud-app-security.md)
    - [Windows biztonsági események](connect-windows-security-events.md) 
    - [Windows tűzfal](connect-windows-firewall.md)

- **API-n keresztül külső megoldások**: Egyes adatforrásokhoz csatlakoznak, a csatlakoztatott adatforrás által biztosított API-k használatával. Általában a legtöbb biztonsági technológiákat biztosítanak egy API-k, amelyen keresztül az eseménynaplókat lehet beolvasni. Az API-k Azure Sentinel-csatlakozhat, és megadott adattípusokat gyűjt, és küldhet nekik az Azure Log Analytics. API-n keresztül csatlakoztatott készülékeket a következők:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Külső megoldások ügynökön keresztül**: Minden más adatforrás által végrehajtható műveleteket, valós idejű naplóstreamelés egy ügynökön keresztül, a Syslog protokollt használó Azure Sentinel csatlakoztatható legyen. <br>Legtöbb készülékek a Syslog protokollt használják a naplót tartalmazó eseményt üzenetek küldéséhez, saját maga és a naplózási adatait. A naplók formátuma érték, de a legtöbb berendezések támogatja a Common Event Format (CEF). <br>Az Azure Sentinel-ügynök, amely az OMS-ügynök alapul, konvertálja CEF naplók formázni a olyan formátumra, amely a Log Analytics által olvasódnak. A berendezés típusától függően az ügynök telepítve van a készüléken közvetlenül vagy egy dedikált Linux-kiszolgálón. A Linuxhoz készült ügynök fogad eseményeket a Syslog démon UDP-n keresztül, de azokban az esetekben, ahol nagy mennyiségű, a Syslog-események gyűjtése a Linuxos gépek várható, küldés TCP-n keresztül az ügynöknek a Syslog démonból, és ott a Log Analytics szolgáltatásba.
    - Tűzfalak, proxyk és végpontok:
        - [F5](connect-f5.md)
        - [A Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Rendszert futtató Palo Alto](connect-paloalto.md)
        - [Egyéb CEF-készülékek](connect-common-event-format.md)
        - [Egyéb Syslog készülékek](connect-syslog.md)
    - DLP-megoldásokhoz
    - [Fenyegetésfelderítési szolgáltatóktól vásárolja](connect-threat-intelligence.md)
    - [DNS-gépek](connect-dns.md) – közvetlenül a DNS-gépre telepített ügynök
    - Linux-kiszolgálók
    - Egyéb felhők
    
## Az ügynök csatlakozási lehetőségek<a name="agent-options"></a>

A külső berendezés csatlakozni az Azure-Sentinel, az ügynököt telepíteni kell egy dedikált gépen (VM vagy a helyszínen) a készüléket és a Sentinel-Azure közötti kommunikáció támogatásához. Az ügynök automatikusan vagy manuálisan telepítheti. Automatikus központi telepítési csak akkor használható, ha dedikált számítógépe egy új virtuális Gépet hoz létre az Azure-ban. 


![Az Azure-ban CEF](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti telepítheti az ügynököt manuálisan a meglévő Azure virtuális gép, egy virtuális gépen egy másik felhőben vagy a helyszíni gépen.

![A helyszíni CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>További lépések

- Ismerkedés az Azure Sentinel-, Microsoft Azure-előfizetéssel kell. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [előkészíteni az adatok Azure Sentinel-](quickstart-onboard.md), és [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
