---
title: Adatok küldése a Windows Azure diagnosztikai bővítményéből az Azure Event Hubs-ba
description: Konfigurálja a diagnosztikai bővítményt az Azure Monitorban, hogy adatokat küldjön az Azure Event Hubnak, így továbbíthatja azokat az Azure-on kívüli helyekre.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672531"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Adatok küldése a Windows Azure diagnosztikai bővítményéből az Azure Event Hubs-ba
Az Azure diagnostics bővítmény az Azure Monitor egy ügynöke, amely a vendég operációs rendszerből és az Azure virtuális gépek és más számítási erőforrások munkaterheléseiből gyűjtfigyelési adatokat. Ez a cikk bemutatja, hogyan küldhet adatokat a Windows Azure diagnosztikai bővítményből (WAD) az Azure Event Hubs-ba, hogy továbbíthassa az [Azure-on](https://azure.microsoft.com/services/event-hubs/) kívüli helyekre.

## <a name="supported-data"></a>Támogatott adatok

A vendég operációs rendszerből gyűjtött adatok, amelyek az Event Hubs-ba küldhetők, a következőket tartalmazzák. A WAD által gyűjtött egyéb adatforrások, például az IIS-naplók és az összeomlási memóriaképek nem küldhetők az Eseményközpontokba.

* A Windows esemény-nyomkövetés (ETW) eseményei
* Teljesítményszámlálók
* Windows eseménynaplók, beleértve az alkalmazásnaplókat a Windows eseménynaplójában
* Azure Diagnostics-infrastruktúranaplók

## <a name="prerequisites"></a>Előfeltételek

* A Windows 1.6-os vagy újabb verzióinak diagnosztikai bővítménye. Tekintse meg [az Azure Diagnostics bővítmény konfigurációs séma verziók és előzmények](diagnostics-extension-versions.md) egy verzióelőzmények és az Azure [Diagnostics bővítmény áttekintése](diagnostics-extension-overview.md) a támogatott erőforrások.
* Az Event Hubs névteret mindig ki kell építeni. További részletek az [Event Hubs – Első lépések.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>Konfigurációs séma
A konfigurációs séma referenciaként a Diagnosztikai bővítmény és az [Azure Diagnosztika konfigurációs séma](diagnostics-extension-schema-windows.md) engedélyezésére és konfigurálására szolgáló különböző lehetőségeket a [Windows Azure diagnosztikai bővítmény (WAD)](diagnostics-extension-windows-install.md) telepítése és konfigurálása című témakörben található. A cikk további cikk ismerteti, hogyan használhatja ezt a konfigurációt adatok küldése egy eseményközpontba. 

Az Azure Diagnostics mindig naplókat és metrikákat küld egy Azure Storage-fiókba. Konfigurálhat egy vagy több *adatgyűjtőt,* amelyek további helyekre küldenek adatokat. Minden fogadó a [sinksconfig eleme](diagnostics-extension-schema-windows.md#sinksconfig-element) a nyilvános konfiguráció bizalmas információkat a privát konfigurációban. Az eseményközpontok ezen konfigurációja az alábbi táblázatértékeit használja.

| Tulajdonság | Leírás |
|:---|:---|
| Név | A mosogató leíró neve. A konfigurációban azt használja, hogy mely adatforrásokat küldje el a fogadóba. |
| URL-cím  | Az eseményközpont url-címe \<az eseményközpont\>-névtér\<.servicebus.windows.net/ esemény-hub-neve\>formájában.          |
| SharedAccessKeyName (SharedAccessKeyName) | Legalább **küldési** jogosultsággal rendelkező eseményközpont megosztott hozzáférési házirendjének neve. |
| SharedAccessKey     | Elsődleges vagy másodlagos kulcs az eseményközpont megosztott hozzáférési szabályzatából. |

Példa nyilvános és privát konfigurációk alább láthatók. Ez egy minimális konfiguráció egyetlen teljesítményszámlálóval és eseménynaplóval, amely bemutatja az eseményközpont-adatgyűjtő konfigurálását és használatát. Tekintse meg [az Azure Diagnostics konfigurációs séma](diagnostics-extension-schema-windows.md) egy összetettebb példa.

### <a name="public-configuration"></a>Nyilvános konfiguráció

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Privát konfiguráció

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Beállítási lehetőségek
Ha adatokat szeretne küldeni egy adatgyűjtőbe, megadhatja az adatforrás csomópontjának **fogadóattribútumát.** A **sinks** attribútum helye határozza meg a hozzárendelés hatókörét. A következő példában a **sinks** attribútum a **PerformanceCounters** csomóponthoz van definiálva, amely az összes gyermekteljesítmény-számlálót az eseményközpontba küldi.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


A következő példában a **sinks** attribútum közvetlenül három számlálóra lesz alkalmazva, amelyek csak azokat a teljesítményszámlálókat küldik az eseményközpontba. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>A konfiguráció ellenőrzése
Számos módszerrel ellenőrizheti, hogy az adatok küldése az eseményközpontba történik.You can use a variety of methods to validate that data is sent to the event hub. Ne egyszerű módszer az Event Hubs rögzítése az [Azure Blob Storage vagy az Azure Data Lake Storage Azure Event Hubs-on keresztüli rögzítési eseményekben leírtak szerint.](../../event-hubs/event-hubs-capture-overview.md) 


## <a name="troubleshoot-event-hubs-sinks"></a>Az Eseményközpontok fogadóinak hibaelhárítása

- Tekintse meg az Azure Storage-tábla **WADDiagnosticInfrastructureLogsTable,** amely tartalmazza a naplókat és hibákat az Azure Diagnostics is. Az egyik lehetőség az, hogy egy eszköz, például az [Azure Storage Explorer](https://www.storageexplorer.com) használatával csatlakozik ehhez a tárfiókhoz, tekintse meg ezt a táblát, és adjon hozzá egy lekérdezést a TimeStamp az elmúlt 24 órában. Az eszközzel .csv fájlt exportálhat, és megnyithatja egy alkalmazásban, például a Microsoft Excelprogramban. Az Excel megkönnyíti a hívókártya-karakterláncok, például **az EventHubs**keresését, hogy lássa, milyen hiba történt.  

- Ellenőrizze, hogy az eseményközpont sikeresen ki van-e építve. A konfiguráció **PrivateConfig** szakaszában található összes kapcsolatinformációnak meg kell egyeznie az erőforrás nak a portálon látható értékeivel. Győződjön meg arról, hogy van egy SAS-házirend definiálva (*SendRule* a példában) a portálon, és hogy *a Küldés* engedély meg van adva.  

## <a name="next-steps"></a>További lépések

* [Eseményközpontok – áttekintés](../../event-hubs/event-hubs-about.md)
* [Eseményközpont létrehozása](../../event-hubs/event-hubs-create.md)
* [Event Hubs – gyakori kérdések](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



