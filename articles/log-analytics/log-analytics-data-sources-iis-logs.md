---
title: Az IIS-naplók az Azure Log Analyticsben |} A Microsoft Docs
description: Az Internet Information Services (IIS) naplófájlok a Log Analytics által gyűjtött felhasználói tevékenység tárolja.  Ez a cikk bemutatja, hogyan konfigurálhatja az IIS-naplók gyűjtésének és hozhatnak létre a Log Analytics-munkaterületen a rekordok részleteit.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: ''
ms.openlocfilehash: 7b44c0712c4d88ec0bbb7a94f574c2a12faf3550
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040676"
---
# <a name="iis-logs-in-log-analytics"></a>A Log Analytics naplózza az IIS
Az Internet Information Services (IIS) naplófájlok a Log Analytics által gyűjtött felhasználói tevékenység tárolja.  

![IIS-naplók](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Naplózza az IIS konfigurálása
Log Analytics bejegyzéseket gyűjt, meg kell, az IIS által létrehozott naplófájlok [konfigurálni az IIS naplózásának](https://technet.microsoft.com/library/hh831775.aspx).

Csak a log Analytics támogatja a tárolt W3C formátumú IIS-naplófájlok, és nem támogatja az egyéni mezők és az IIS naplózás speciális.  
A log Analytics nem gyűjt naplók NCSA vagy az IIS natív formátumban.

IIS-naplók konfigurálása a Log Analytics az [adatok Log Analytics-beállítások menüben](log-analytics-data-sources.md#configuring-data-sources).  Nincs semmilyen beállítást nem szükséges kiválasztásával **gyűjtése W3C formátumú IIS-naplófájljai**.


## <a name="data-collection"></a>Adatgyűjtés
A log Analytics minden ügynök jön létre minden alkalommal, amikor a napló be van zárva, és a egy új IIS-naplóbejegyzések gyűjti össze. Ez a gyakoriság vezérli a **fájl váltása naplóütemezés** beállítása az IIS-webhely, amely alapértelmezés szerint naponta egyszer van. Például, ha a beállítások **óránkénti**, akkor a Log Analytics gyűjti a napló óránként.  Ha a beállítás értéke **napi**, akkor a Log Analytics gyűjti a napló 24 óránként.


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
| SourceSystem |Az OpsMgr |
| sPort |Port a kiszolgálón az ügyfél csatlakozik. |
| sSiteName |Az IIS-webhely neve. |
| TimeGenerated |Dátum és idő, a bejegyzés a naplózott. |
| timeTaken |Eltelt idő (MS) a kérelem feldolgozását. |

## <a name="log-searches-with-iis-logs"></a>Naplókeresések az IIS-naplókkal
Az alábbi táblázat az IIS-naplóbejegyzések lekérő lekérdezések log különböző példákat.

| Lekérdezés | Leírás |
|:--- |:--- |
| W3CIISLog |Az összes IIS-napló rögzíti. |
| W3CIISLog &#124; ahol scStatus == 500 |Az összes IIS-napló rekordok 500 visszaadott állapottal. |
| W3CIISLog &#124; által cIP count() összegzése |Száma az IIS naplóbejegyzéseket, ügyfél IP-cím alapján. |
| W3CIISLog &#124; ahol csHost == "www.contoso.com" &#124; által csUriStem count() összegzése |Száma az IIS-napló bejegyzései a gazdagép www.contoso.com URL-címe alapján. |
| W3CIISLog &#124; összegzése számítógép szerint sum(csBytes) &#124; 500 000 összeget igénybe |Egyes IIS-számítógépek által fogadott összes bájt. |

## <a name="next-steps"></a>További lépések
* Konfigurálja a gyűjtendő más Log Analytics [adatforrások](log-analytics-data-sources.md) elemzés céljából.
* Ismerje meg [naplókereséseket](log-analytics-log-searches.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.
* Riasztások konfigurálása a Log Analytics segítségével proaktívan kaphat értesítést az IIS-naplókban található fontos feltételekről.
