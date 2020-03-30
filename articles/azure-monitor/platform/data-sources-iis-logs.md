---
title: IIS-naplók az Azure Monitorban | Microsoft dokumentumok
description: Az Internet Information Services (IIS) az Azure Monitor által gyűjthető naplófájlokban tárolja a felhasználói tevékenységet.  Ez a cikk ismerteti, hogyan konfigurálhatja az IIS-naplók gyűjteményét és az Azure Monitorban létrehozott rekordok részleteit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670576"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>IIS-naplók gyűjtése az Azure Monitorban
Az Internet Information Services (IIS) olyan naplófájlokban tárolja a felhasználói tevékenységet, amelyeket az Azure Monitor gyűjthet és [naplóadatként](data-platform.md)tárol.

![IIS-naplók](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Az IIS-naplók konfigurálása
Az Azure Monitor az IIS által létrehozott naplófájlokból gyűjtbejegyzéseket, ezért [konfigurálnia kell az IIS-t a naplózáshoz.](https://technet.microsoft.com/library/hh831775.aspx)

Az Azure Monitor csak a W3C formátumban tárolt IIS-naplófájlokat támogatja, és nem támogatja az egyéni mezőket vagy az IIS speciális naplózását. Nem gyűjt naplókat NCSA vagy IIS natív formátumban.

Konfigurálja az IIS-naplókat az Azure Monitorban a [Speciális beállítások menüből.](agent-data-sources.md#configuring-data-sources)  Nincs szükség más konfigurációra, mint a **W3C formátumú IIS naplófájlok gyűjtése**.


## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor minden alkalommal gyűjti az IIS-naplóbejegyzéseket minden ügynöktől, amikor a napló időbélyege megváltozik. A napló **5 percenként beolvasásra kerül.** Ha az IIS bármilyen okból nem frissíti az időbélyeget az új fájl létrehozásakor i dicsőítő idő előtt, a rendszer az új fájl létrehozását követően gyűjti a bejegyzéseket. Az új fájl létrehozásának gyakoriságát az IIS-hely **naplófájl-átütemezési ütemezési** beállítása határozza meg, amely alapértelmezés szerint naponta egyszer történik. Ha a beállítás **óránkénti,** az Azure Monitor óránként gyűjti a naplót. Ha a beállítás **napi,** az Azure Monitor 24 óránként gyűjti a naplót.


## <a name="iis-log-record-properties"></a>IIS-naplórekord tulajdonságai
Az IIS naplórekordjai **w3CIISLog** típusúak, és az alábbi táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Annak a számítógépnek a neve, amelyről az eseményt gyűjtötték. |
| Cip |Az ügyfél IP-címe. |
| csMódszer |A kérelem módja, mint a GET vagy a POST. |
| csReferer |Az a webhely, amelyről a felhasználó az aktuális webhelyre mutató hivatkozást követett. |
| csUserAgent |Az ügyfél böngészőtípusa. |
| csUserName |A kiszolgálót elérő hitelesített felhasználó neve. A névtelen felhasználókat kötőjel jelöli. |
| csUriStem |A kérelem, például egy weboldal célzása. |
| csUriQuery |Az ügyfél által végrehajtani kívánt lekérdezés( ha van ilyen). |
| ManagementGroupName |Az Operations Manager-ügynökök felügyeleti csoportjának neve.  Más ügynökök esetében ez az\<AOI-munkaterület-azonosító\> |
| RemoteIPCountry |Az ügyfél IP-címének országa/régiója. |
| RemoteIPLatitude |Az ügyfél IP-címének szélessége. |
| RemoteIPLongitude |Az ügyfél IP-címének hosszúsága. |
| scStatus |HTTP állapotkód. |
| scSubStatus |Részállapot-hibakód. |
| scWin32Status |Windows állapotkód. |
| Sip |A webkiszolgáló IP-címe. |
| SourceSystem |OpsMgr között |
| sPort |Port azon a kiszolgálón, amelyhez az ügyfél csatlakozott. |
| sSiteName |Az IIS-hely neve. |
| TimeGenerated |A bejegyzés naplózásának dátuma és időpontja. |
| Időtaken |A kérelem feldolgozásának időtartama ezredmásodpercben. |

## <a name="log-queries-with-iis-logs"></a>Lekérdezések naplózása IIS-naplókkal
Az alábbi táblázat különböző példákat tartalmaz az IIS-naplórekordokat lekérdező naplólekérdezésekre.

| Lekérdezés | Leírás |
|:--- |:--- |
| W3CIISLog |Az összes IIS-naplórekord. |
| W3CIISLog &#124; ahol scStatus==500 |Az összes 500-as visszatérési állapotú IIS-naplórekord. |
| A W3CIISLog &#124; összefoglalja a count() programot cIP szerint |Az IIS naplóbejegyzéseinek száma ügyfél IP-címe szerint. |
| W3CIISLog &#124; ahol csHost\.=="www contoso.com" &#124; összefoglalni count() által csUriStem |Az IIS naplóbejegyzéseinek száma URL-cím szerint a www\.contoso.com. |
| W3CIISLog &#124; összefoglalni sum (csBytes) a számítógép &#124; hogy 500000 |Az egyes IIS-számítógépek által fogadott bájtok száma. |

## <a name="next-steps"></a>További lépések
* Konfigurálja az Azure Monitort más [adatforrások](agent-data-sources.md) elemzéshez való gyűjtésére.
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.
