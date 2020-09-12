---
title: A Windows Defender-tűzfalszabályok összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Engedélyezze a Windows tűzfal-összekötőt az Azure Sentinelben, így egyszerűen továbbíthatja a tűzfalak eseményeit olyan Windows-gépekről, amelyeken telepítve van Log Analytics ügynök.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 5804dcc840eb666c1d43ea7d7ed7640b8f7ff371
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657447"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>A fokozott biztonságú Windows Defender-tűzfal és az Azure Sentinel összekapcsolása

A [fokozott biztonságú Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) -összekötő lehetővé teszi, hogy az Azure Sentinel egyszerűen betöltse a Windows Defender-tűzfalat a speciális biztonsági naplókból a munkaterület bármely Windows-gépről. Ez a kapcsolat lehetővé teszi a Windows tűzfal eseményeinek megtekintését és elemzését a munkafüzetekben, hogy azok az egyéni riasztások létrehozásával, valamint a biztonsági vizsgálatokban való beépítésével legyenek felhasználva, így jobban betekintést nyerhet a szervezet hálózatára, és javíthatja a biztonsági működési képességeit. 

A megoldás Windows tűzfalbeli eseményeket gyűjt azokról a Windows-gépekről, amelyeken Log Analytics ügynök van telepítve. 

> [!NOTE]
> - Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.
>
> - Ha az Azure Sentinel és az Azure Defender (korábban Azure Security Center) összegyűjtése ugyanarra a munkaterületre történik, nem szükséges engedélyezni a Windows tűzfal megoldását ezen az összekötőn keresztül. Ha ezt az állapotot engedélyezte, akkor a duplikált adatértékeket nem okoz. 

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie azon a munkaterületen, amelyhez a figyelni kívánt gépek csatlakoznak.

- Az **Azure Sentinel** szerepkörein kívül hozzá kell rendelnie a **log Analytics közreműködő** szerepkört a SecurityInsights-megoldáshoz az adott munkaterületen. [További információ](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Az összekötő engedélyezése 

1. Az Azure Sentinel portálon válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Válassza az összekötők katalógusában a **Windows tűzfal** lehetőséget, majd kattintson az **összekötő lap megnyitása**lehetőségre.

### <a name="instructions-tab"></a>Utasítások lap

Hajtsa végre a következő lépéseket az **utasítások** lapon.

- **Ha a Windows rendszerű gépek az Azure-ban találhatók, hajtsa végre a következő lépéseket:**

   1. Válassza **az ügynök telepítése az Azure Windows rendszerű virtuális gépen**lehetőséget.
   
   1. Kattintson a **letöltés & telepítse az ügynököt az Azure Windows rendszerű virtuális gépekhez >** a megjelenő hivatkozásra.
   
   1. A **virtuális gépek** listájában válassza ki azt a Windows-gépet, amelyet az Azure sentinelbe szeretne továbbítani. (Az operációs rendszer oszlopának szűrőben a **Windows** lehetőség kiválasztásával biztosíthatja, hogy csak a Windows rendszerű virtuális gépek jelenjenek meg).
   
   1. A virtuális gép megnyíló ablakában kattintson a **kapcsolat**elemre.
   
   1. Térjen vissza a **Virtual Machines** panelre, és ismételje meg az előző két lépést minden olyan virtuális gép esetében, amelyhez csatlakozni szeretne. Ha elkészült, térjen vissza a **Windows tűzfal** ablaktáblára.

- **Ha a Windows rendszerű számítógép nem Azure-beli virtuális gép, hajtsa végre a következő lépéseket:**
   
   1. Válassza **az ügynök telepítése nem Azure-beli Windows-gépen**lehetőséget.
   
   1. Kattintson a **letöltés & telepítse az ügynököt nem Azure-beli Windows-gépekhez >** a megjelenő hivatkozásra.
   
   1. Az **ügynökök kezelése** panelen válassza a Windows- **ügynök letöltése (64 bit)** vagy a **Windows-ügynök (32 bites)** letöltését igény szerint.
   
   1. Másolja a **munkaterület-azonosítót**, az **elsődleges kulcsot**és a **másodlagos kulcs** karakterláncait egy szövegfájlba. Másolja a fájlt és a letöltött telepítőfájlt a Windows rendszerű gépre. Futtassa a telepítőfájlt, és amikor a rendszer kéri, adja meg az azonosító és a kulcs karakterláncot a szövegfájlban a telepítés során.
   
   1. Térjen vissza a **Windows tűzfal** ablaktáblára.

Miután elvégezte a lépéseket az **utasítások** lapon, kattintson a **megoldás telepítése**elemre.

### <a name="next-steps-tab"></a>Következő lépések lap

- A Windows tűzfal naplófájljainak betekintéséhez tekintse meg a rendelkezésre álló javasolt munkafüzeteket és a **Windows tűzfal** adatösszekötőjét tartalmazó lekérdezési mintákat.

- A Windows tűzfal **naplókban**tárolt adatlekérdezéséhez írja be a **WindowsFirewall** értéket a lekérdezési ablakban.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
 
Mivel a rendszer csak akkor küldi el a Windows tűzfal naplóit az Azure Sentinelnek, ha a helyi naplófájl eléri a kapacitást, a napló az alapértelmezett 4096 KB-os méretnél nagyobb valószínűséggel fog eredményezni a gyűjtemény késését. A naplófájl méretének csökkentésével csökkentheti a késést. Tekintse meg a [Windows tűzfal naplójának konfigurálására](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)vonatkozó utasításokat. Vegye figyelembe, hogy a lehető legkevesebb naplózási méret (1 KB) meghatározásakor a rendszer gyakorlatilag kizárja a gyűjtési késést, és negatív hatással lehet a helyi gép teljesítményére is. 

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Windows tűzfal az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

