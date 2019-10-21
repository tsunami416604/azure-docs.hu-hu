---
layout: HubPage
hide_bc: false
title: Az Azure Monitor log-adatdokumentációja | Microsoft Docs
description: A Azure Monitor különböző forrásokat gyűjthet a naplózási adatok alapján, és a lekérdezési nyelv használatával tárolhatja a korrelációt és az elemzést.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: hub-page
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2019
ms.openlocfilehash: 407e18008c947f95204ea073db31b5f2279a5a4c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597320"
---
<div id="main" class="v2">
    <div class="container">
        <h1>Azure Monitor naplózási adatdokumentáció</h1>
        <p>A Azure Monitor különböző forrásokat gyűjthet a naplózási adatok alapján, és a lekérdezési nyelv használatával tárolhatja a korrelációt és az elemzést.</p>
        <p>Ismerkedjen meg a legnépszerűbb tartalommal a naplózási adatok tervezésével, gyűjtésével és elemzésével.</p>
        <hr style="margin: 30px 0;" />
        <ul class="pivots">
            <li>
                <a href="#products"></a>
                <ul id="products">
                    <li>
                        <a class="singlePanelNavItem selected" style="display: none" href="#indexA" data-linktype="self-bookmark"></a>
                        <ul class="panelContent singlePanelContent" id="indexA" style="border: medium; border-image: none; margin-top: 0px; display: flex; float: left;">
                            <li class="fullSpan">
                                <a href="#index1" data-linktype="self-bookmark"></a>
                                <ul class="cardsF cols cols4" id="index1" style="float: left; display: flex; width: 100%; border-bottom: 1px var(--grey-lighter) solid;">  
                                    <li>
                                        <ul class="cardsB panelContent" id="cardtypes-B" style="float: left; display: flex; width: 100%;">
                                            <li>
                                                <!-- <a href="">-->
                                                    <div class="cardSize">
                                                        <div class="cardPadding">
                                                            <div class="card">
                                                                <div class="cardImageOuter">
                                                                    <div class="cardImage">
                                                                        <img alt="" src="https://docs.microsoft.com/media/common/i_learn-about.svg" data-linktype="external">
                                                                    </div>
                                                                </div>
                                                                <div class="cardText" style="padding-left: 0px">
                                                                    <h3>A fundamentumok megismerése</h3> 
                                                                    <p> 
                                                                        <a href="/azure/azure-monitor/overview">Mi az Azure Monitor?</a><br/>
                                                                        <a href="/azure/azure-monitor/azure-monitor-rebrand">Védjegyezési változások</a><br/>
                                                                        <a href="/azure/azure-monitor/platform/data-sources">Adatforrások figyelése</a><br/>
                                                                        <a href="/azure/azure-monitor/platform/service-providers">A szolgáltatók kialakításával kapcsolatos szempontok</a><br/>
                                                                    </p>
                                                                </div>
                                                            </div>
                                                        </div>
                                                    </div>
                                                <!-- </a>-->
                                            </li>
                                            <li>
                                                <!-- <a href="">-->
                                                    <div class="cardSize">
                                                        <div class="cardPadding">
                                                            <div class="card">
                                                                <div class="cardImageOuter">
                                                                    <div class="cardImage">
                                                                        <img alt="" src="https://docs.microsoft.com/media/common/i_security-management.svg" data-linktype="external">
                                                                    </div>
                                                                </div>
                                                                <div class="cardText" style="padding-left: 0px">
                                                                    <h3>A biztonság ismertetése</h3> 
                                                                    <p>
                                                                        <a href="/azure/azure-monitor/platform/data-security">Naplózási adatgyűjtés</a><br/>
                                                                        <a href="/azure/azure-monitor/platform/personal-data-mgmt">Személyes naplózási adatkezelés</a><br/>
                                                                        <a href="/azure/azure-monitor/platform/roles-permissions-security">Szerepkör-engedélyek és biztonság</a><br/>
                                                                    </p>
                                                                    <br>
                                                                     <h3>Munkaterület kezelése</h3>
                                                                    <p>
                                                                    <a href="/azure/azure-monitor/learn/quick-create-workspace">Munkaterület létrehozása a Azure Portalban</a><br/>
                                                                    <a href="/azure/azure-monitor/learn/quick-create-workspace-cli">Munkaterület létrehozása az Azure CLI-vel</a><br/>
                                                                   <a href="/azure/azure-monitor/learn/quick-create-workspace-posh">Munkaterület létrehozása Azure PowerShell</a><br/>
                                                                  <a href="/azure/azure-monitor/platform/delete-workspace">Munkaterület törlése</a><br/>
                                                                 </p>
                                                                </div>
                                                            </div>
                                                        </div>
                                                    </div>
                                                <!-- </a>-->
                                            </li>
                                        </ul>
                                    </li>
                                    <li>
                                        <div class="cardSize">
                                            <div class="cardPadding">
                                                <div class="card">
                                                    <div class="cardText">
                                                    <h3>Monitoring</h3>
                                                        <p>
                                                            <a href="/azure/azure-monitor/platform/data-collection">Metrikák és naplók</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-ingestion-time">Adatfeldolgozási idő naplózása</a><br/>
                                                        </p>
                                                        <br>
                                                        <h3>Adatforrások</h3>
                                                        <p>
                                                            <a href="/azure/azure-monitor/platform/data-sources">Overview<br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-windows-events">Windows-események</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-performance-counters">Windows-és Linux-teljesítményszámlálók</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-linux-applications">Linux-alkalmazások teljesítménye</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-json">Egyéni JSON-adatok</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-collectd">Összegyűjtött teljesítményadatok</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix">Nagios- és Zabbix-riasztások</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-syslog">Syslog</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-iis-logs">IIS-naplók</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-sources-custom-logs">Egyéni naplók</a><br/>
                                                            <a href="/azure/azure-monitor/platform/custom-fields">Egyéni mezők</a><br/>
                                                            <a href="/azure/azure-monitor/platform/data-collector-api">Adatgyűjtő API</a><br/>
                                                        </p>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </li>
                                    <li>
                                        <div class="cardSize">
                                            <div class="cardPadding">
                                                <div class="card">
                                                    <div class="cardImageOuter">
                                                                    <div class="cardImage">
                                                                        <img alt="" src="https://docs.microsoft.com/media/common/i_search.svg" data-linktype="external">
                                                                    </div>
                                                                </div>
                                                    <div class="cardText">
                                                        <h3>Adatok elemzése</h3>
                                                        <p>
                                                            <a href="/azure/azure-monitor/log-query/get-started-queries">Ismerkedés a lekérdezésekkel</a><br/>
                                                            <a href="/azure/azure-monitor/log-query/search-queries">A naplózási lekérdezések ismertetése</a><br/>
                                                            <a href="/azure/azure-monitor/log-query/portals">Napló lekérdezési portálok</a><br/>
                                                            <a href="/azure/azure-monitor/log-query/query-language">A naplózási lekérdezés nyelvi referenciája</a><br/>
                                                            <a href="/azure/azure-monitor/log-query/cross-workspace-query">Erőforrások közötti lekérdezés</a><br/>
                                                        </p>
                                                        <br>
                                                        <h3>Incidensek kezelése</h3>
                                                        <p>
                                                            <a href="/azure/azure-monitor/platform/alerts-overview">Riasztások áttekintése</a><br/>
                                                            <a href="/azure/azure-monitor/platform/autoscale-overview">Automatikus méretezés</a><br/>
                                                            <a href="/azure/azure-monitor/platform/alerts-unified-log">Riasztások naplózása</a><br/>
                                                            <a href="/azure/azure-monitor/platform/alerts-activity-log">Műveletnapló riasztásai</a><br/>
                                                            <a href="/azure/azure-monitor/platform/action-groups">Műveletcsoportok</a><br/>
                                                        </p>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </li>
                                </ul>
                            </li>
                        </ul>
                    </li>
                </ul>
            </li>
        </ul>
    </div>
</div>
