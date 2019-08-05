---
title: Adatforrások összekötése az Azure Sentinel előzetes verziójával? | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az adatforrások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 4928657aa9052b50faf1f326cc09797c5aaf69bb
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780517"
---
# <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Az Azure Sentinelhez először csatlakoznia kell az adatforrásokhoz. Az Azure Sentinel számos, a Microsoft-megoldások számára elérhető összekötővel rendelkezik, és valós idejű integrációt biztosít, beleértve a Microsoft veszélyforrások elleni védelmi megoldásait és Microsoft 365 forrásait, beleértve az Office 365, az Azure AD, az Azure ATP és a Microsoft Cloud App Security és így tovább. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. Az adatforrások az Azure Sentinel szolgáltatással való összekapcsolásához használhatja a Common Event Format, a syslog vagy a REST-API-t is.  

1. A menüben válassza az adatösszekötők lehetőséget. Ezen a lapon megtekintheti az Azure Sentinel által biztosított összekötők teljes listáját, valamint azok állapotát. Válassza ki a csatlakoztatni kívánt összekötőt, és válassza az **összekötő lap megnyitása**lehetőséget. 

   ![Adatgyűjtők](./media/collect-data/collect-data-page.png)

1. Az adott összekötő lapon győződjön meg arról, hogy teljesítette az összes előfeltételt, és kövesse az utasításokat az adat Azure Sentinelhez való csatlakozásához. Eltarthat egy ideig, amíg a naplók megkezdik a szinkronizálást az Azure Sentinel használatával. A csatlakozást követően megjelenik a **kapott** adatmennyiség és az adattípusok kapcsolati állapota.

   ![Gyűjtők összekapcsolása](./media/collect-data/opened-connector-page.png)
  
1. A **következő lépések** lapon lekérheti, hogy az Azure Sentinel milyen adattípust biztosít a beépített tartalomhoz.

   ![Adatgyűjtők](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Adatkapcsolatok módszerei

Az Azure Sentinel a következő adatkapcsolási módszereket támogatja:

- **Microsoft-szolgáltatások**:<br> A Microsoft-szolgáltatások natív módon csatlakoznak, és az Azure Foundation kihasználása a Box-integrációhoz, néhány kattintással csatlakozhat a következő megoldásokhoz:
    - [Office 365](connect-office-365.md)
    - [Azure AD-naplók és-bejelentkezések](connect-azure-active-directory.md)
    - [Azure-tevékenység](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure komplex veszélyforrások elleni védelem](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Windows biztonsági események](connect-windows-security-events.md) 
    - [Windows tűzfal](connect-windows-firewall.md)

- **Külső megoldások API-n keresztül**: Egyes adatforrások a csatlakoztatott adatforrás által biztosított API-kkal vannak összekapcsolva. A legtöbb biztonsági technológia jellemzően olyan API-kat biztosít, amelyeken keresztül az eseménynaplók kérhetők le. Az API-k az Azure Sentinelhez csatlakoznak, és konkrét adattípusokat gyűjtenek, és elküldhetik azokat az Azure Log Analyticsba. Az API-n keresztül csatlakoztatott berendezések a következők:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Külső megoldások ügynökön keresztül**: Az Azure Sentinel minden más olyan adatforráshoz csatlakoztatható, amely valós idejű naplózást végez a syslog protokoll használatával egy ügynökön keresztül. <br>A legtöbb készülék a syslog protokollt használja az olyan események küldésére, amelyek magukban foglalják a naplót és a naplóval kapcsolatos információkat. A naplók formátuma változó, de a legtöbb készülék támogatja a Common Event Format (CEF) szabványt. <br>A Microsoft monitoring Agenten alapuló Azure Sentinel-ügynök átalakítja a CEF formázott naplókat olyan formátumba, amelyet a Log Analytics betölt. A készülék típusától függően az ügynököt közvetlenül a készülékre vagy egy dedikált Linux-kiszolgálóra telepíti. A Linux-ügynök a syslog démontól érkező eseményeket fogad UDP-n keresztül, de ha egy Linux rendszerű gépen nagy mennyiségű syslog-eseményt kell gyűjteni, a rendszer a syslog démonból az ügynököt és onnan Log Analytics.
    - Tűzfalak, proxyk és végpontok:
        - [F5 BILLENTYŰT](connect-f5.md)
        - [Ellenőrzési pont](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Egyéb CEF készülékek](connect-common-event-format.md)
        - [Egyéb syslog-készülékek](connect-syslog.md)
    - DLP-megoldások
    - [Veszélyforrások elleni intelligencia szolgáltatók](connect-threat-intelligence.md)
    - [DNS-gépek](connect-dns.md) – közvetlenül a DNS-gépen telepített ügynök
    - Linux-kiszolgálók
    - Egyéb felhők
    
## Ügynök-csatlakoztatási beállítások<a name="agent-options"></a>

Ahhoz, hogy a külső berendezést az Azure Sentinelhez lehessen kapcsolni, az ügynököt egy dedikált gépen (virtuális gépen vagy helyszínen) kell telepíteni a készülék és az Azure Sentinel közötti kommunikáció támogatásához. Az ügynököt automatikusan vagy manuálisan is üzembe helyezheti. Az automatikus központi telepítés csak akkor érhető el, ha a dedikált számítógép egy új, az Azure-ban létrehozott virtuális gép. 


![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti, hogy manuálisan telepítheti az ügynököt egy meglévő Azure-beli virtuális gépre, egy másik felhőben lévő virtuális gépre vagy egy helyszíni gépre.

![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>További lépések

- Az Azure Sentinel megkezdéséhez szüksége lesz egy előfizetésre Microsoft Azure. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan hozhatja be [adatait az Azure sentinelbe](quickstart-onboard.md), és hogyan tekintheti [meg az adatait és a lehetséges fenyegetéseket](quickstart-get-visibility.md).
