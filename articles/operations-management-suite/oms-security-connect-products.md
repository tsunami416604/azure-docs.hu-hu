---
title: "Biztonsági termékek csatlakoztatása az Operations Management Suite (OMS) biztonsági és auditálási megoldáshoz | Microsoft Docs"
description: "A jelen dokumentum ahhoz nyújt segítséget, ha biztonsági termékeket szeretne csatlakoztatni az Operations Management Suite biztonsági és auditálási megoldáshoz a Common Event Formattal."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 46eee484-e078-4bad-8c89-c88a3508f6aa
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.openlocfilehash: f95ee94d1b86b6409fc5ecce14ed3e068a635b3d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="connecting-your-security-products-to-the-operations-management-suite-oms-security-and-audit-solution"></a>Biztonsági termékek csatlakoztatása az Operations Management Suite (OMS) biztonsági és auditálási megoldáshoz 
A jelen dokumentum ahhoz nyújt segítséget, ha biztonsági termékeket szeretne csatlakoztatni az OMS biztonsági és auditálási megoldáshoz. Az alábbi források támogatottak:

- Common Event Format- (CEF-) események;
- Cisco ASA-események.


## <a name="what-is-cef"></a>Mi az a CEF?
A Common Event Format (CEF) egy iparági szabványnak számító, a Syslog-üzenetekre épülő formátum, amelyet számos, biztonsági megoldásokkal foglalkozó forgalmazó használ az események együttműködésének biztosításához a különböző platformok között. Az OMS biztonsági és auditálási megoldás a CEF használatával támogatja az adatfeldolgozást, ami lehetővé teszi a biztonsági termékek csatlakoztatását az OMS biztonsági megoldáshoz. 

Az adatforrás OMS-csatlakoztatásával a platform részét képező alábbi képességek előnyeit használhatja ki:

- Keresés és korreláció
- Naplózás
- Riasztás
- Fenyegetések felderítése
- Jelentős problémák

## <a name="collection-of-security-solution-logs"></a>Biztonsági megoldások naplóinak gyűjtése

Az OMS biztonsági megoldás a Syslog-alapú CEF-naplók és [Cisco ASA](https://blogs.technet.microsoft.com/msoms/2016/08/25/add-your-cisco-asa-logs-to-oms-security/)-naplók használatával támogatja a naplók gyűjtését. Ebben a példában a forrás (a naplókat létrehozó számítógép) egy syslog-ng démont futtató Linux-számítógép, a cél pedig az OMS-biztonság. A Linux-számítógép előkészítéséhez az alábbi feladatokat kell végrehajtania:

- Töltse le a Linuxhoz készült OMS-ügynök 1.2.0-25-ös vagy újabb verzióját.
- Kövessen [ezen cikk](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux) **gyors telepítési útmutatóját** az ügynök telepítéséhez és bevezetéséhez a munkaterületen.

Az ügynök általában nem a naplókat létrehozó számítógépre települ. A naplók ügynökgépre történő továbbításához általában az alábbi lépésekre van szükség:

- A naplózást végző termék/gép konfigurálása a szükséges események továbbításához az ügynökgépen futó syslog démonnak (rsyslog vagy syslog-ng).
- A távoli rendszerről érkező üzenetek fogadásának engedélyezése az ügynökgépen futó syslog démon számára.

Az ügynökgépen a helyi 25226-os UDP-portra kell küldeni az eseményeket a syslog démonból. Az ügynök ezen a porton figyeli a bejövő eseményeket. Az alábbi példakonfigurációban a helyi rendszer minden eseményt az ügynöknek küld el (a konfigurációt a helyi beállításoknak megfelelően módosíthatja):

1. Nyissa meg a terminálablakot, és lépjen az */etc/syslog-ng/* könyvtárba. 
2. Hozza létre az új *security-config-omsagent.conf* fájlt, és adja hozzá a következő tartalmat: OMS_facility = local4
    
    filter f_local4_oms { facility(local4); };

    destination security_oms { tcp("127.0.0.1" port(25226)); };

    log { source(src); filter(f_local4_oms); destination(security_oms); };
    
3. Töltse le a *security_events.conf* fájlt, és helyezze az */etc/opt/microsoft/omsagent/conf/omsagent.d/* mappába az OMS-ügynök számítógépén.
4. Írja be az alábbi parancsot a syslog démon újraindításához: *A syslog-ng futtatása esetén:*
    
    ```
    sudo service rsyslog restart
    ```

    *Az rsyslog futtatása esetén:*
    
    ```
    /etc/init.d/syslog-ng restart
    ```
5. Írja be az alábbi parancsot az OMS-ügynök újraindításához:

    *A syslog-ng futtatása esetén:*
    
    ```
    sudo service omsagent restart
    ```

    *Az rsyslog futtatása esetén:*
    
    ```
    systemctl restart omsagent
    ```
6. Írja be az alábbi parancsot, és az eredményeket áttekintve ellenőrizze, hogy találhatók-e hibák az OMS-ügynök naplójában:

    ``` 
    tail /var/opt/microsoft/omsagent/log/omsagent.log
    ```

## <a name="reviewing-collected-security-events"></a>Az összegyűjtött biztonsági események áttekintése

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

A konfigurálás befejezését követően az OMS biztonsági megoldás elkezdi feldolgozni a biztonsági eseményeket. Az események megjelenítéséhez nyissa meg a Naplók keresése eszközt, írja be a *Type=CommonSecurityLog* parancsot a keresőmezőbe, majd nyomja le az ENTER billentyűt. Az alábbi példában a parancs eredménye látható. Vegye figyelembe, hogy ebben az esetben az OMS biztonsági megoldás már feldolgozta a több forgalmazótól származó biztonsági naplókat:
   
![Az OMS biztonsági és auditálási alapkonfiguráció értékelése](./media/oms-security-connect-products/oms-security-connect-products-fig1.png)

A keresést egyetlen forgalmazóra szűkítheti. Az online Cisco-naplók megjelenítéséhez például írja be a következő parancsot: *Type=CommonSecurityLog DeviceVendor=Cisco*. A „CommonSecurityLog” előre meghatározott mezőkkel rendelkezik minden CEF-fejlécre vonatkozóan, beleértve az alapszintű bővítményeket is, minden más bővítmény pedig, függetlenül attól, hogy egyéni bővítmény-e vagy sem, az „AdditionalExtensions” mezőben látható. Az Egyéni mezők funkcióval kérheti le a dedikált mezőket. 

### <a name="accessing-computers-missing-baseline-assessment"></a>Az alapkonfiguráció értékeléséből kimaradt számítógépek elérése
Az OMS a tartományi tag alapkonfigurációjának profilját a Windows Server 2008 R2 verziótól a Windows Server 2012 R2 verzióig támogatja. A Windows Server 2016 alapkonfigurációja még nem végleges, és közzététele után azonnal megtörténik a hozzáadása. Minden más operációs rendszer, amelyet az OMS biztonság és audit alapkonfiguráció értékelése megvizsgált, **Az alapkonfiguráció értékeléséből kimaradt számítógépek** szakaszban fog megjelenni.

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatja a CEF-megoldást az OMS-hez. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)

