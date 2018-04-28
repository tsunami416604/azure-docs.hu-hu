---
title: Elemzési szolgáltatások keresse meg a szolgáltatók |} Microsoft Docs
description: Naplóelemzési segítségével felügyelt szolgáltató (MSPs), a nagyobb vállalatok független szoftver szállítói (ISV-k) és az üzemeltetési szolgáltatók kezelni és megfigyelni a kiszolgálók az ügyfél helyszíni vagy felhőalapú infrastruktúrában.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 6934e92df562099122eaede39fd26cf51cf1ee44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="log-analytics-features-for-service-providers"></a>Napló Analytics szolgáltatások szolgáltatók
A Naplóelemzési segítségével felügyelt szolgáltató (MSPs), a nagyobb vállalatok, független szoftvergyártók (ISV-k) és üzemeltetési szolgáltatók kezelni és megfigyelni a kiszolgálók az ügyfél helyszíni vagy felhőalapú infrastruktúrában. 

A nagyobb vállalatok megosztása sok a hasonlóság szolgáltatók, különösen, ha egy központi informatikai csoportját, amelyek felügyeletéért felelős informatikai számos különböző üzleti egységek számára. Az egyszerűség, a dokumentum kifejezést használja *szolgáltató* , de ugyanezeket a funkciókat is vállalatok és más ügyfelek számára érhető el.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider program
A partnerek és szolgáltatók, amelyek a a [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, Log Analyticshez az elérhető Azure-szolgáltatások egyik [Azure CSP előfizetés](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

A Naplóelemzési, az alábbi képességeket engedélyezve van az *Cloud Solution Provider* előfizetések.

Mint egy *Cloud Solution Provider* is:

* A Naplóelemzési munkaterület létrehozása a bérlő (ügyfél) az előfizetéshez.
* Hozzáférés a bérlők által létrehozott munkaterületek. 
* Adja hozzá, és távolítsa el a felhasználói hozzáférés az Azure felhasználói felügyeleti munkaterületet. Amikor a bérlő munkaterületén az OMS-portálon a felhasználók kezelése lapon az beállítások nem érhető el
  * A Naplóelemzési nem támogatja a szerepköralapú hozzáférés még - ad egy felhasználónak `reader` az Azure-portálon az engedély lehetővé teszi, hogy konfigurációs módosításokat az OMS-portálon

Jelentkezzen be egy bérlői előfizetéshez, hogy meg kell adnia a bérlő azonosítója. A bérlői azonosító gyakran a bejelentkezéshez használt e-mail cím utolsó része.

* Az OMS-portálon hozzáadása `?tenant=contoso.com` a portál URL-címét. Például: `mms.microsoft.com/?tenant=contoso.com`
* A PowerShellben használja a `-Tenant contoso.com` paraméter használatakor `Connect-AzureRmAccount` parancsmag
* A bérlői azonosító automatikusan fel lesz véve használatakor a `OMS portal` hivatkozás megnyitásához, és jelentkezzen be a kijelölt munkaterülethez OMS-portálon az Azure portálról

Mint egy *ügyfél* , a Cloud Solution Provider is:

* Napló analytics munkaterületek egy CSP-előfizetés létrehozása
* A kriptográfiai Szolgáltató által létrehozott Access munkaterületek
  * Használja a `OMS portal` hivatkozás megnyitásához, és jelentkezzen be a kijelölt munkaterülethez OMS-portálon az Azure portálról
* Megtekintése és használata a felhasználófelügyeleti lapra lépéshez a beállítások az OMS-portálon

> [!NOTE]
> Naplóelemzési belefoglalt biztonsági mentési és hely helyreállítási megoldások nem tud csatlakozni a Recovery Services-tároló, és nem állítható be egy CSP-előfizetés az. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>A Naplóelemzési több felhasználója kezelése
Javasoljuk, hogy hozzon létre egy Naplóelemzési munkaterület minden felügyelt ügyfél esetében. A Naplóelemzési munkaterület biztosítja:

* A földrajzi hely, az adatok tárolását. 
* A számlázáshoz szükséges részletes adatokat 
* Az adatok elkülönítését 
* Egyedi konfigurációja

Hozzon létre egy ügyfél egy munkaterület, is minden egyes felhasználói adatok elkülönítése és is nyomon követheti az egyes ügyfelek használatát.

Mikor és miért több munkaterületek létrehozására vonatkozó részletes információért ismertetett [analytics jelentkezzen való hozzáférés kezelése](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Létrehozása és ügyfél munkaterületek konfigurációjának automatizálható használatával [PowerShell](log-analytics-powershell-workspace-configuration.md), [Resource Manager-sablonok](log-analytics-template-workspace-configuration.md), vagy használja a [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Munkaterület konfigurációs Resource Manager-sablonok használata lehetővé teszi, amelyek segítségével hozza létre és konfigurálja a munkaterületek fő konfigurációval rendelkezik. Biztos lehet abban, hogy amikor munkaterületek hoz létre az ügyfelek automatikusan konfigurálásuk az igényeinek megfelelően. Ha frissíti a követelmények, a sablon frissül, és ezután újra alkalmazza a meglévő munkaterületek. Ez a folyamat biztosítja, hogy még meglévő munkaterületek megfelelnek-e az új szabványok.    

Több Naplóelemzési munkaterület kezelése esetén ajánlott minden egyes munkaterület integrálása a meglévő hibajegyalapú rendszert / operatív konzol használatával a [riasztások](log-analytics-alerts.md) funkciót. A meglévő rendszerekkel integrálásával támogató személyzete számára továbbra is a megszokott eljárások követésével. A Naplóelemzési rendszeresen ellenőrzi az egyes munkaterületeken ellen, adja meg a riasztás feltételeit, és riasztást küld, ha nincs szükség műveletre.

Az adatok személyre szabott nézeteket, használja a [irányítópult](../azure-portal/azure-portal-dashboards.md) funkció az Azure portálon.  

A végrehajtó szintű jelentések, amelyek adatainak összefoglalója munkaterületek között is használhatja a Naplóelemzési közötti integrációt és [Power bi](log-analytics-powerbi.md). Ha egy másik jelentéskészítő rendszer integrálása van szüksége, használhatja a keresési API-t (PowerShell vagy [REST](log-analytics-log-search-api.md)) lekérdezéseinek futtatásához, és a keresési eredmények exportálása.

## <a name="next-steps"></a>További lépések
* Létrehozása és konfigurálása munkaterületek használatával automatizálhatja [Resource Manager-sablonok](log-analytics-template-workspace-configuration.md)
* Használatával munkaterületek létrehozásának automatizálása [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Használjon [riasztások](log-analytics-alerts.md) integrálható a meglévő rendszerek
* Összegző jelentéseket használatával [Power BI](log-analytics-powerbi.md)

