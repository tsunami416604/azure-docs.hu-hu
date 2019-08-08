---
title: IIS-naplók a Azure Monitorban | Microsoft Docs
description: A Internet Information Services (IIS) a Azure Monitor által összegyűjthető naplófájlokban tárolja a felhasználói tevékenységeket.  Ez a cikk bemutatja, hogyan konfigurálhatja az IIS-naplók gyűjteményét és a Azure Monitorban létrehozott rekordok részleteit.
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
ms.openlocfilehash: cc0fcbb2005ce2aaa70c9e1d2a9993d341169209
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814220"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>IIS-naplók összegyűjtése Azure Monitor
A Internet Information Services (IIS) olyan naplófájlokban tárolja a felhasználói tevékenységeket, amelyeket Azure Monitor tárolhat, és [naplózó adatként](data-platform.md)tárolhatja őket.

![IIS-naplók](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS-naplók konfigurálása
A Azure Monitor az IIS által létrehozott naplófájlokból gyűjt bejegyzéseket, ezért konfigurálnia kell az IIS-t [a naplózáshoz](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor csak a W3C formátumban tárolt IIS-naplófájlokat támogatja, és nem támogatja az egyéni mezőket vagy az IIS speciális naplózását. Az NCSA vagy az IIS natív formátumában nem gyűjt naplókat.

Konfigurálja az IIS-naplókat Azure Monitor a [Speciális beállítások menüből](agent-data-sources.md#configuring-data-sources).  A **W3C-formátumú IIS**-naplófájlok gyűjtésének kiválasztásához nincs szükség konfigurációra.


## <a name="data-collection"></a>Adatgyűjtés
A Azure Monitor az egyes ügynököktől érkező IIS-naplókat gyűjti össze minden alkalommal, amikor megváltozik a napló időbélyege. A napló **5 percenként**olvasható. Ha bármilyen okból kifolyólag az IIS nem frissíti az időbélyeget az új fájl létrehozásakor, a rendszer az új fájl létrehozása után begyűjti a bejegyzéseket. Az új fájl létrehozásának gyakoriságát az IIS-hely **naplófájl** -átváltási ütemezési beállítása szabályozza, amely alapértelmezés szerint naponta egyszer történik. Ha a beállítás **óránként**történik, Azure monitor óránként gyűjti a naplót. Ha a beállítás **naponta**történik, Azure monitor 24 óránként gyűjti a naplót.


## <a name="iis-log-record-properties"></a>IIS-napló rekordjának tulajdonságai
Az IIS-naplóbejegyzések típusa **W3CIISLog** , és a következő táblázatban található tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| Számítógép |Annak a számítógépnek a neve, amelyre az eseményt gyűjtötték. |
| cIP |Az ügyfél IP-címe. |
| csMethod |A kérelem metódusa, például GET vagy POST. |
| csReferer |A hely, amelyet a felhasználó az aktuális helyre mutató hivatkozást követett. |
| csUserAgent |Az ügyfél böngésző-típusa. |
| csUserName |A kiszolgálót elérő hitelesített felhasználó neve. A névtelen felhasználókat kötőjel jelöli. |
| csUriStem |A kérelem célja, például weblap. |
| csUriQuery |Lekérdezés, ha van ilyen, amelyet az ügyfél megpróbált végrehajtani. |
| ManagementGroupName |Operations Manager ügynökök felügyeleti csoportjának neve.  Más ügynökök esetén ez AOI -\<munkaterület azonosítója\> |
| RemoteIPCountry |Az ügyfél IP-címének országa/régiója. |
| RemoteIPLatitude |Az ügyfél IP-címének földrajzi szélességét. |
| RemoteIPLongitude |Az ügyfél IP-címének hosszúsága. |
| scStatus |HTTP-állapotkód. |
| scSubStatus |Részállapot-hibakód. |
| scWin32Status |Windows-állapotkód. |
| sIP |A webkiszolgáló IP-címe. |
| SourceSystem |OpsMgr |
| sPort |Annak a kiszolgálónak a portja, amelyhez az ügyfél csatlakozik. |
| sSiteName |Az IIS-hely neve. |
| TimeGenerated |A bejegyzés naplózásának dátuma és időpontja. |
| Eltelt idő |A kérelem feldolgozásának időtartama ezredmásodpercben. |

## <a name="log-queries-with-iis-logs"></a>Lekérdezések naplózása IIS-naplókkal
Az alábbi táblázat különböző példákat tartalmaz az IIS-naplóbejegyzések lekérésére szolgáló naplókra.

| Lekérdezés | Leírás |
|:--- |:--- |
| W3CIISLog |Az összes IIS-naplózási rekord. |
| W3CIISLog &#124; , ahol scStatus = = 500 |Az összes IIS-naplózási rekord 500-as visszatérési állapottal. |
| W3CIISLog &#124; összesített száma () a cIP szerint |IIS-naplóbejegyzések száma az ügyfél IP-címe alapján. |
| W3CIISLog &#124; , ahol a csHost = =\."www &#124; contoso.com" összegzi a Count () függvényt csUriStem szerint |Az IIS-naplóbejegyzések száma a gazdagép www\.contoso.com URL-címe alapján. |
| W3CIISLog &#124; összegzése (csBytes) a számítógép &#124; által 500000 |AZ egyes IIS-számítógépek által fogadott bájtok száma. |

## <a name="next-steps"></a>További lépések
* A Azure Monitor konfigurálásával más [](agent-data-sources.md) adatforrásokat is gyűjthet az elemzéshez.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.
