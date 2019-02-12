---
title: Az Azure monitorban naplózza az IIS |} A Microsoft Docs
description: Az Internet Information Services (IIS) felhasználói tevékenység az Azure Monitor gyűjtött naplófájlokat tárolja.  Ez a cikk ismerteti az IIS-naplók gyűjtésének és hozhatnak létre az Azure monitorban a rekordok részleteit.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: ca951c813554ae253cbd572e03c53b8687499af9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000165"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Az Azure monitorban gyűjtése IIS-naplók
Az Internet Information Services (IIS) felhasználói tevékenység tárolja az Azure Monitor által összegyűjtött és tárolt és naplófájlok [adatok](data-collection.md).

![IIS-naplók](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Naplózza az IIS konfigurálása
Az Azure Monitor bejegyzések gyűjti össze a naplófájlokat, az IIS által létrehozott, ezért meg kell [konfigurálni az IIS naplózásának](https://technet.microsoft.com/library/hh831775.aspx).

Az Azure Monitor csak tárolt W3C formátumú IIS-naplófájlok támogatja, és nem támogatja az egyéni mezők és az IIS naplózás speciális. NCSA vagy az IIS natív formátumú naplókat nem gyűjt.

Az Azure monitorban az IIS-naplók konfigurálása a [speciális beállítások menü](agent-data-sources.md#configuring-data-sources).  Nincs semmilyen beállítást nem szükséges kiválasztásával **gyűjtése W3C formátumú IIS-naplófájljai**.


## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor gyűjti az IIS-naplóbejegyzések minden ügynök minden alkalommal, amikor a napló be van zárva, és a egy új jön létre. Ez a gyakoriság vezérli a **fájl váltása naplóütemezés** beállítása az IIS-webhely, amely alapértelmezés szerint naponta egyszer van. Például, ha a beállítások **óránkénti**, majd az Azure Monitor összegyűjti a napló óránként.  Ha a beállítás értéke **napi**, majd az Azure Monitor összegyűjti a napló 24 óránként.


## <a name="iis-log-record-properties"></a>Az IIS-napló rekord tulajdonságai
Az IIS-naplóbejegyzések rendelkezik olyan típusú **W3CIISLog** , és a tulajdonságok a következő táblázatban:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép nevét. |
| cIP |Az ügyfél IP-címe. |
| csMethod |A kérelem például GET vagy POST metódus. |
| csReferer |A hely, hogy a felhasználó követi-e egy hivatkozást a az aktuális helyen. |
| csUserAgent |Az ügyfél böngésző típusa. |
| csUserName |A kiszolgáló elérhető a hitelesített felhasználó nevét. Névtelen felhasználókat kötőjel jelzik. |
| csUriStem |Például egy weblap címe |
| csUriQuery |Lekérdezés, ha vannak, hogy az ügyfél által megkísérelt művelet. |
| ManagementGroupName |Az Operations Manager-ügynököket a felügyeleti csoport neve.  Más ügynökök esetén ez AOI -\<munkaterület azonosítója\> |
| RemoteIPCountry |Ország, az ügyfél IP-címét. |
| RemoteIPLatitude |Az ügyfél IP-cím szélességét. |
| RemoteIPLongitude |Az ügyfél IP-cím hosszúságát. |
| scStatus |HTTP-állapotkódot. |
| scSubStatus |A részállapot hibakód. |
| scWin32Status |Windows-állapotkódot. |
| sIP- |A webalkalmazás-kiszolgáló IP-címe. |
| SourceSystem |OpsMgr |
| sPort |Port a kiszolgálón az ügyfél csatlakozik. |
| sSiteName |Az IIS-webhely neve. |
| TimeGenerated |Dátum és idő, a bejegyzés a naplózott. |
| TimeTaken |Eltelt idő (MS) a kérelem feldolgozását. |

## <a name="log-queries-with-iis-logs"></a>IIS-naplók log lekérdezéseket.
Az alábbi táblázat az IIS-naplóbejegyzések lekérő lekérdezések log különböző példákat.

| Lekérdezés | Leírás |
|:--- |:--- |
| W3CIISLog |Az összes IIS-napló rögzíti. |
| W3CIISLog &#124; where scStatus==500 |Az összes IIS-napló rekordok 500 visszaadott állapottal. |
| W3CIISLog &#124; summarize count() by cIP |Száma az IIS naplóbejegyzéseket, ügyfél IP-cím alapján. |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |Száma az IIS-napló bejegyzései a gazdagép www.contoso.com URL-címe alapján. |
| W3CIISLog &#124; összegzése számítógép szerint sum(csBytes) &#124; 500 000 összeget igénybe |Egyes IIS-számítógépek által fogadott összes bájt. |

## <a name="next-steps"></a>További lépések
* Egyéb gyűjtésére az Azure Monitor konfigurálása [adatforrások](agent-data-sources.md) elemzés céljából.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.