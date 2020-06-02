---
title: Az Office 365-naplók összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Office 365-es adatszolgáltatások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: bcd00247486faeea47ef4a4a43fa1df5420321e6
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248940"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Az Office 365-naplók összekapcsolhatók az Azure Sentinel-vel

Az [Office 365](https://docs.microsoft.com/office/) log Connector az **Exchange** -ben és a **SharePointban** folytatott folyamatos felhasználói és rendszergazdai tevékenységekről nyújt Azure Sentinel-információkat (beleértve a **OneDrive**is). Ez az információ részletesen ismerteti az olyan műveleteket, mint a fájlok letöltése, a hozzáférési kérelmek elküldése, a csoport eseményeinek módosítása és a postaláda műveletei, valamint a műveleteket végrehajtó felhasználó adatai. Ha az Office 365-naplókat az Azure Sentinelhez csatlakoztatja, lehetővé teszi az adatok megtekintését és elemzését a munkafüzetekben, lekérdezheti az egyéni riasztásokat, és beépítheti azt a vizsgálati folyamat javítására, így jobban betekintést nyerhet az Office 365-biztonságba.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- A bérlőn globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal kell rendelkeznie.

- Az Office 365 üzemelő példányának az Azure Sentinel-munkaterülettel azonos bérlőn kell lennie.

> [!IMPORTANT]
> - Ahhoz, hogy az összekötő hozzáférhessen az adataihoz az Office 365 felügyeleti tevékenység API-n keresztül, az Office 365-telepítésben engedélyezve kell lennie az **egyesített naplózási naplózásnak** . Az Office 365/Microsoft 365 licenc típusától függően előfordulhat, hogy alapértelmezés szerint nem engedélyezhető. Az [Office 365 biztonsági és megfelelőségi központban](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) ellenőrizze az egyesített naplózási naplózás állapotát a licenc típusa alapján.
> - Manuálisan is engedélyezheti, letilthatja és megtekintheti az Office 365 egyesített naplózási naplózásának aktuális állapotát. Útmutatásért lásd: [az Office 365 naplózási naplójának keresése be-és kikapcsolása](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - További információt az [Office 365 felügyeleti tevékenység API-referenciája](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)című témakörben talál.


   > [!NOTE]
   > Ahogy az a fentiekben is látható, és amint az **adattípusok**alatt megjelenik az összekötő lapon, az Azure Sentinel Office 365-összekötő jelenleg csak a Microsoft Exchange és a SharePoint (beleértve a OneDrive) naplóinak betöltését támogatja. Vannak azonban olyan külső megoldások, amelyek érdeklik az [adatok csapatokból](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) vagy [más Office-adatokból](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) való bevezetését az Azure Sentinel szolgáltatásba. 

## <a name="enable-the-office-365-log-connector"></a>Az Office 365 log Connector engedélyezése

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők**lehetőséget.

1. Az **adatösszekötők** listájában kattintson az **Office 365**elemre, majd a jobb alsó sarokban található **összekötő megnyitása lap** gombra.

1. A **konfiguráció**feliratú szakaszban jelölje be azon Office 365-tevékenységek naplófájljainak jelölőnégyzetét, amelyekhez csatlakozni szeretne az Azure sentinelhez, majd kattintson a **módosítások alkalmazása**lehetőségre. 

   > [!NOTE]
   > Ha korábban több bérlőt csatlakoztatott az Azure Sentinelhez, az Office 365-összekötő egy régebbi verziójával, amely ezt támogatta, megtekintheti és módosíthatja az egyes bérlők által gyűjtött naplókat. Nem adhat hozzá további bérlőket, de a korábban hozzáadott bérlőket is eltávolíthatja.

1. Ha Log Analytics szeretné lekérdezni az Office 365-naplóját, írja be a `OfficeActivity` következőt a lekérdezési ablak első sorába:.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Office 365 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats-built-in.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.

