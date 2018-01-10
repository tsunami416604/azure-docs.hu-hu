---
title: "Az Azure Naplóelemzés naplózza az IIS |} Microsoft Docs"
description: "Internet Information Services (IIS) felhasználói tevékenység Naplóelemzési által gyűjtendő naplófájlokat tárolja.  A cikkből megtudhatja, hogyan lehet beállítani az IIS-naplók gyűjtésének és hoznak létre a Naplóelemzési munkaterület rekord részletei."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: b8ce4e6fe6e12aa3edb81abad1589924e3e121e4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="iis-logs-in-log-analytics"></a>A Naplóelemzési naplózza az IIS
Internet Information Services (IIS) felhasználói tevékenység Naplóelemzési által gyűjtendő naplófájlokat tárolja.  

![IIS-naplók](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Naplózza az IIS konfigurálása
A Naplóelemzési bejegyzéseket gyűjt, meg kell, az IIS által létrehozott naplófájlok [az IIS naplózás konfigurálása](https://technet.microsoft.com/library/hh831775.aspx).

A Naplóelemzési csak támogatja az IIS W3C formátumban tárolja, és nem támogatja az egyéni mezők és az IIS naplózás speciális.  
A Naplóelemzési nem naplógyűjtéshez NCSA vagy az IIS natív formátumban.

A Naplóelemzési konfigurálása IIS-napló a [Naplóelemzés beállításai adatok menüben](log-analytics-data-sources.md#configuring-data-sources).  Nincs a konfiguráció nem szükséges másik kiválasztásával **gyűjtése W3C-formátum az IIS-naplófájljai**.

Azt javasoljuk, hogy ha engedélyezi az IIS naplógyűjtést, konfigurálnia kell az IIS napló átfordulási beállítás az egyes kiszolgálókon.

## <a name="data-collection"></a>Adatgyűjtés
A Naplóelemzési gyűjti össze az IIS-napló bejegyzései minden csatlakoztatott forrás körülbelül 15 percenként.  Az ügynök a helyére azt gyűjti össze az egyes eseménynapló rögzíti.  Ha az ügynök offline állapotba kerül, majd Naplóelemzési gyűjti események ahol utolsó abbamaradtak, akkor is, ha olyan eseményeket hozta létre, miközben az ügynök offline állapotban volt.

## <a name="iis-log-record-properties"></a>IIS-napló rekord tulajdonságai
Naplórekordok az IIS típusa lehet **W3CIISLog** és az alábbi táblázatban a jellemzőkkel rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény összegyűjtött a számítógép nevét. |
| cIP |Az ügyfél IP-címe. |
| csMethod |Például a GET vagy POST kérelem metódusában. |
| csReferer |A hely, hogy a felhasználó és egy hivatkozást a jelenlegi helyre. |
| csUserAgent |Az ügyfél böngésző típusa. |
| csUserName |A hitelesített felhasználó, érhetők el a kiszolgáló neve. A névtelen felhasználókat kötőjel jelzi. |
| csUriStem |Például egy weblap célját. |
| csUriQuery |Lekérdezni, ha van ilyen, hogy az ügyfél által végrehajtani próbált. |
| ManagementGroupName |Az Operations Manager-ügynököket a felügyeleti csoport neve.  Más ügynökök, ez pedig AOI -\<munkaterület azonosítója\> |
| RemoteIPCountry |Az IP-cím, az ügyfél országa. |
| RemoteIPLatitude |A földrajzi hosszúság az ügyfél IP-cím. |
| RemoteIPLongitude |A földrajzi hosszúság értéke az ügyfél IP-cím. |
| scStatus |HTTP-állapotkódot. |
| scSubStatus |A részállapot hibakód. |
| scWin32Status |Windows állapotkódját. |
| a sIP |A webalkalmazás-kiszolgáló IP-címe. |
| SourceSystem |Az OpsMgr |
| sPort |Port a kiszolgálón az ügyfél csatlakozik. |
| sSiteName |Az IIS-webhely neve. |
| TimeGenerated |Dátum és idő, a bejegyzés naplózásának. |
| TimeTaken |Eltelt idő, ezredmásodpercben megadva a kérelem feldolgozása. |

## <a name="log-searches-with-iis-logs"></a>Napló keresések az IIS-napló
Az alábbi táblázat példákat különböző napló lekérdezések IIS naplórekordokat beolvasása.

| Lekérdezés | Leírás |
|:--- |:--- |
| W3CIISLog |Az összes IIS-napló rögzíti. |
| W3CIISLog &#124; Ha scStatus == 500 |Az összes IIS napló rekordot 500 visszaadott állapotát. |
| W3CIISLog &#124; count() összesíteni cIP |Száma az IIS naplóbejegyzéseket, ügyfél IP-cím alapján. |
| W3CIISLog &#124; Ha csHost == "www.contoso.com" &#124; count() összesíteni csUriStem |Száma az IIS a naplóbejegyzéseket, URL-cím által a gazdagép a www.contoso.com. |
| W3CIISLog &#124; Számítógép &#124; sum(csBytes) összefoglalója 500 000 összeget igénybe |Minden egyes IIS-számítógép által fogadott összes bájt. |

## <a name="next-steps"></a>További lépések
* Naplóelemzési más gyűjtéséhez konfigurálja [adatforrások](log-analytics-data-sources.md) elemzés céljából.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.
* Riasztások konfigurálása a Log Analyticshez proaktív értesítik a fontos feltételek található az IIS-naplókba.
