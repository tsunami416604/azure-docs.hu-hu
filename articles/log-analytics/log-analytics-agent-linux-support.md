---
title: "Azure Naplóelemzés Analytics Linux-ügynök hibáinak elhárítása |} Microsoft Docs"
description: "A jelenség okok és a leggyakoribb problémák megoldási napló Analytics Linux-ügynökkel rendelkező leírása"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: magoedte
ms.openlocfilehash: 4f9eaa330ba90052080fc18def974d5dec43c1e0
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>A Naplóelemzési Linux-ügynökkel rendelkező kapcsolatos problémák elhárítása

Ez a cikk ismerteti a Naplóelemzési Linux-ügynökkel rendelkező tapasztalhat, és azok megoldását lehetséges megoldások javasolja hibák elhárításához.

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Probléma: Nem lehet csatlakozni a szolgáltatáshoz proxyn keresztül

#### <a name="probable-causes"></a>Lehetséges okok
* A bevezetési során megadott proxy helytelen volt.
* A Naplóelemzési és Azure Automation szolgáltatás végpontok nincsenek-e az Adatközpont szerepel az engedélyezési listán 

#### <a name="resolutions"></a>Megoldások
1. A Linux-a kapcsolóval használja a következő parancsot az OMS-ügynököt a Log Analytics szolgáltatás Reonboard `-v` engedélyezve van. Ez lehetővé teszi, hogy az ügynök az OMS szolgáltatáshoz a proxyn keresztül történő kapcsolódás részletes kimenet. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a szakasz [az ügynök használatra konfigurálja a proxykiszolgáló vagy az OMS-átjáró](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) megfelelően konfigurált proxykiszolgálón keresztül kommunikálnak az ügynököt ellenőrzéséhez.    
* Ellenőrizze, hogy a következő Naplóelemzés szolgáltatás végpontjait szerepel az engedélyezési listán:

    |Ügynök erőforrása| Portok |  
    |------|---------|  
    |*.ods.opinsights.azure.com | 443-as port|   
    |*.oms.opinsights.azure.com | 443-as port|   
    |ods.systemcenteradvisor.com | 443-as port|   
    |*.BLOB.Core.Windows.NET/ | 443-as port|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: 403-as hibaüzenetet kap közben előkészítésére

#### <a name="probable-causes"></a>Lehetséges okok
* Dátum és idő nem megfelelő Linux-kiszolgálón 
* Munkaterületének Azonosítóját és kulcsát használt nem megfelelőek

#### <a name="resolution"></a>Megoldás:

1. Ellenőrizze az időt a parancs dátuma a Linux-kiszolgálóra. Ha a idő 15 perc, az aktuális időponthoz képest +/-, akkor bevezetési sikertelen lesz. Megfelelő ez frissítse a dátumot és/vagy a Linux-kiszolgáló időzónáját. 
2. Ellenőrizze, hogy a legújabb verzióját az OMS-ügynököt telepítette Linux.  A legújabb verziója most értesíti, ha időeltérési a bevezetési hibát okozó.
3. A Reonboard használja a megfelelő munkaterület Azonosítóját és kulcsát a következő témakör korábbi szakaszában a telepítési utasításokat.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: Egy 500 és 404-es hiba látható a naplófájlban bevezetése után
Ez az egy ismert hiba jelentkezik be a Naplóelemzési munkaterület Linux adatok első feltöltéskor. Ez nem befolyásolja az elküldött és a szolgáltatás élmény adatok.

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probléma: Nem Ön az Azure-portálon megadott adattal sem

#### <a name="probable-causes"></a>Lehetséges okok

- Sikertelen a Naplóelemzési szolgáltatás bevezetése
- A Log Analytics szolgáltatás kapcsolat le van tiltva.
- OMS-ügynököt a Linux-adatok biztonsági mentése

#### <a name="resolutions"></a>Megoldások
1. Ellenőrizze, hogy ha bevezetése a Naplóelemzés szolgáltatás sikeres volt a következő fájl létezésének ellenőrzésével:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard használatával a `omsadmin.sh` parancssori utasításokat
3. Ha proxyt használ, tekintse meg a korábban megadott proxy megoldási lépések.
4. Egyes esetekben amikor Linux az OMS-ügynök nem tud kommunikálni a szolgáltatás adatai az ügynökön várólistára van állítva 50 MB teljes puffer mérete. A Linux OMS-ügynököt újra kell indítani a következő parancs futtatásával: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ez a probléma fennáll ügynök verziója 1.1.0-28 és újabb verziók.

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>Probléma: OMSAgent hoz létre felhasználói folyamat túl sok számítógép, és soha nem megszakítja azokat
Ha engedélyezi a felügyeleti megoldás, amely támogatja a Linux virtuális gépek felügyeletét, a Linux-ügynök folyamatok számos indít. De a folyamat befejeződik, mielőtt egy másik folyamat elindul egy ismert probléma miatt. 

#### <a name="resolutions"></a>Megoldások
Ha módosítani szeretné a OMSAgent létrehozó felhasználó folyamatok száma, omsadmin.sh használatával ügynök konfigurálása.  Alapértelmezés szerint generált folyamatok száma 75, és módosítsa az értéket, mielőtt először futtassa a következő parancsot, hogy hány OMSAgent folyamatok jelenleg futnak: `ps aux | grep -E '^omsagent' | wc -l`.  
Mi a jelenlegi érték a következő parancs futtatásával ellenőrizheti:`cat /etc/security/limits.conf | grep -E '^omsagent'`

Használja a következő parancsokat, konfigurálhat egy egyéni feldolgozási korlátja, vagy vissza az alapértelmezett érték a folyamat korlát beállítása.

1. A feldolgozási korlátja OMSAgent beállítása: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`.<br>Vegye figyelembe, hogy a minimális korlát beállítható az 5.  

2. A feldolgozási korlátja OMSAgent beállítása az alapértelmezett értékre:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`

Győződjön meg arról, hogy az új beállítás alkalmazva van a következő parancs futtatásával: `cat /etc/security/limits.conf | grep -E '^omsagent'`.  Ha nem látja az új konfigurációt alkalmazza, akkor valószínűleg korlátot állít be a folyamat túl alacsony.  