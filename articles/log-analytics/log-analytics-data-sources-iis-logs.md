---
title: Az Azure Naplóelemzés naplózza az IIS |} Microsoft Docs
description: Internet Information Services (IIS) felhasználói tevékenység Naplóelemzési által gyűjtendő naplófájlokat tárolja.  A cikkből megtudhatja, hogyan lehet beállítani az IIS-naplók gyűjtésének és hoznak létre a Naplóelemzési munkaterület rekord részletei.
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
ms.comopnent: na
ms.openlocfilehash: 65320e7d3cc97a3d53fd1a00fbbeab5559c02fce
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132954"
---
# <a name="iis-logs-in-log-analytics"></a>A Naplóelemzési naplózza az IIS
Internet Information Services (IIS) felhasználói tevékenység Naplóelemzési által gyűjtendő naplófájlokat tárolja.  

![IIS-naplók](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Naplózza az IIS konfigurálása
A Naplóelemzési bejegyzéseket gyűjt, meg kell, az IIS által létrehozott naplófájlok [az IIS naplózás konfigurálása](https://technet.microsoft.com/library/hh831775.aspx).

A Naplóelemzési csak támogatja az IIS W3C formátumban tárolja, és nem támogatja az egyéni mezők és az IIS naplózás speciális.  
A Naplóelemzési nem naplógyűjtéshez NCSA vagy az IIS natív formátumban.

A Naplóelemzési konfigurálása IIS-napló a [Naplóelemzés beállításai adatok menüben](log-analytics-data-sources.md#configuring-data-sources).  Nincs a konfiguráció nem szükséges másik kiválasztásával **gyűjtése W3C-formátum az IIS-naplófájljai**.


## <a name="data-collection"></a>Adatgyűjtés
Naplóelemzési minden jön létre minden alkalommal, amikor a napló be van zárva, és egy új ügynök gyűjti össze az IIS-napló bejegyzései. A gyakoriságát a **napló fájl helyettesítő ütemezés** az IIS-webhely alapértelmezés szerint naponta egyszer egyben beállítása. Például, ha a beállítások **óránkénti**, majd Naplóelemzési összegyűjti a napló minden órában.  Ha a beállítás értéke **napi**, majd Naplóelemzési összegyűjti a napló 24 óránként.


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
| W3CIISLog &#124; ahol scStatus == 500 |Az összes IIS napló rekordot 500 visszaadott állapotát. |
| W3CIISLog &#124; count() összesíteni cIP |Száma az IIS naplóbejegyzéseket, ügyfél IP-cím alapján. |
| W3CIISLog &#124; ahol csHost == "www.contoso.com" &#124; count() összesíteni csUriStem |Száma az IIS a naplóbejegyzéseket, URL-cím által a gazdagép a www.contoso.com. |
| W3CIISLog &#124; összesítse a számítógép által sum(csBytes) &#124; 500 000 összeget igénybe |Minden egyes IIS-számítógép által fogadott összes bájt. |

## <a name="next-steps"></a>További lépések
* Naplóelemzési más gyűjtéséhez konfigurálja [adatforrások](log-analytics-data-sources.md) elemzés céljából.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.
* Riasztások konfigurálása a Log Analyticshez proaktív értesítik a fontos feltételek található az IIS-naplókba.
