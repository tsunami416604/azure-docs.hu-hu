---
title: Az Azure Log Analytics Linux-ügynök hibaelhárítása |} A Microsoft Docs
description: A jelenség okok és a leggyakoribb problémák megoldási ismertetik a Log Analytics Linux-ügynökkel rendelkező.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: baa81a52d4b007cd690a2b01df642cd3775f7d6b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044136"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>A Linux-ügynök a Log Analytics-hibáinak elhárítása

Ez a cikk ismerteti a hibaelhárítást hibákat tapasztalhat a Log Analytics Linux-ügynökkel rendelkező, és azok megoldását lehetséges megoldásokat javasol.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Probléma: Nem lehet kapcsolódni a Log Analytics-proxy használatával

### <a name="probable-causes"></a>Lehetséges okok
* A proxy, az előkészítés során megadott helytelen volt.
* A Log Analytics és Azure Automation szolgáltatás végpontok nem érhetők szerepel az engedélyezési listán a helyi adatközpontban 

### <a name="resolutions"></a>Megoldások
1. Az OMS-ügynök Linux rendszeren a következő parancs használatával a lehetőséggel az a Log Analytics szolgáltatás Reonboard `-v` engedélyezve van. Ez lehetővé teszi, hogy az ügynök az OMS szolgáltatásnak a proxyn keresztül történő csatlakozás részletes kimenet. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a szakasz [proxy beállításainak frissítése](log-analytics-agent-manage.md#update-proxy-settings) ellenőrizze, hogy megfelelően konfigurálta az ügynök közötti kommunikációhoz egy proxykiszolgálón keresztül.    
* Ellenőrizze, hogy a következő Log Analytics-Szolgáltatásvégpontok-e az engedélyezési listán:

    |Ügynök erőforrása| Portok | Irány |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.oms.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.blob.core.windows.net | 443-as port| Bejövő és kimenő |  
    |*.azure-automation.net | 443-as port| Bejövő és kimenő | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: 403-as hibaüzenetet kap előkészítése tett kísérlet során

### <a name="probable-causes"></a>Lehetséges okok
* Dátum és idő nem megfelelő Linux-kiszolgálón 
* Munkaterület-Azonosítót és a használt munkaterület kulcsa nem megfelelőek

### <a name="resolution"></a>Megoldás:

1. Ellenőrizze az idő a Linux-kiszolgálón, a parancs dátummal. Ha az idő +/-aktuális időtől számítva 15 percig, majd előkészítés sikertelen lesz. Megfelelő ez frissítse a dátumot és/vagy a Linux-kiszolgáló időzónáját. 
2. Ellenőrizze a Linuxhoz készült OMS-ügynök legújabb verzióját telepítette.  A legújabb verzióra most már értesítést küld, ha időeltérési a bevezetési hibát okozó.
3. Reonboard megfelelő munkaterület-Azonosítót és a telepítési utasításokat a témakör korábbi szakaszában a következő munkaterület kulcsa.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: Egy 500 és 404-es hibaüzenetet látja a naplófájlban közvetlenül az előkészítés után
Ez az egy ismert probléma, amely akkor fordul elő, a Log Analytics-munkaterület az első Linux adatok feltöltése. Ez nem befolyásolja a adat rangsorát elküldött vagy service felhasználói élményt.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probléma: Nem jelennek meg adatok az Azure Portalon

### <a name="probable-causes"></a>Lehetséges okok

- A Log Analytics szolgáltatás üzembe helyezése nem sikerült
- A Log Analytics szolgáltatással létesített kapcsolat le van tiltva.
- OMS-ügynök Linux-adatok biztonsági mentése

### <a name="resolutions"></a>Megoldások
1. Ellenőrizze, hogy ha bevezetése a Log Analytics szolgáltatás sikeres volt-e a következő fájl létezik: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard használatával a `omsadmin.sh` parancssori utasításokat
3. Ha proxyt használ, tekintse meg a korábban megadott proxy megoldási lépések.
4. Bizonyos esetekben amikor a Linuxhoz készült OMS-ügynök nem tud kommunikálni a szolgáltatás adatai az ügynökön várólistára van állítva a teljes puffert méretét, amely 50 MB-ot. A Linuxhoz készült OMS-ügynök a következő parancs futtatásával újra kell indítani: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Az ügynök verziója 1.1.0-28 és újabb verzióiban ez a problémát megoldottuk.

