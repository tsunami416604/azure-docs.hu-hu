---
title: "Az Azure Linux Virtual Machine dokumentációja – oktatóanyagok és API-referencia | Microsoft Docs"
description: "Ismerje meg a kívánt Linux-disztribúciót futtató virtuális gépek létrehozási lehetőségeit. A dokumentáció különböző módszereket mutat be a VM-sablonok létrehozására."
services: virtual-machines\linux
author: carolz
manager: carolz
layout: LandingPage
ms.service: virtual-machines\linux
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 2dd642627033d297bfa53b00aac0de418212ecb3
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017

---
<div class="content">
    <h1>Linux rendszerű virtuális gépek</h1>
    <div class="introHolder" style="justify-content: space-between;">
        <div class="intro" style="min-width: 200px">
            <p>Az Azure Linux virtuális gépek igény szerinti, nagy léptékben méretezhető, biztonságos virtualizált infrastruktúrát biztosítanak Red Hat, Ubuntu vagy bármely tetszőleges Linux disztribúció használatával. Ismerje meg, hogyan hozhat létre, konfigurálhat, felügyelhet és méretezhet Linux virtuális gépeket a gyors útmutatóink, oktatóanyagaink és példáink segítségével.</p>
        </div>
        <a href="https://azure.microsoft.com/en-us/resources/videos/create-linux-virtual-machine/">
            <div class="calloutHolder" style="max-width: 250px">
                <div>
                    <img src="media/index/create-linux-virtual-machine.png" style="width: 250px" />
                </div>
                <div>
                    <p style="margin-top: 0; color: #6e6e6e">Hozzon létre egy Linux rendszerű virtuális gépet. (4:11)</p>
                </div>
            </div>
        </a>
    </div>
<h2 style="margin-top: 0px; margin-bottom: 0px;">5 perces gyors útmutatók</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">Megtudhatja, hogyan lehet egy Ubuntut futtató virtuális gépet üzembe helyezni egy NGINX-webkiszolgálón belül:</p>
<div class="ico48Case">
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/cli.svg" alt="">
            <span>Azure CLI</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-portal?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
        <img src="media/index/portal.svg" alt="">
            <span>Azure Portal</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-powershell?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/logo_powershell.svg" alt="">
            <span>Azure PowerShell</span>
        </a>
    </div>
<div>

<h2 style="margin-top: 36px">Részletes útmutatók</h2>
<p>Megtudhatja, hogyan telepíthet, kezelhet és méretezhet biztonságos Linux rendszerű virtuális gépeket az Azure-ban</p>
<ol>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-vm">Linux rendszerű virtuális gépek létrehozása és felügyelete</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-disks">Virtuálisgép-lemezek létrehozása és kezelése</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-automate-vm-deployment">Virtuálisgép-konfiguráció létrehozása</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-custom-images">Egyéni virtuálisgép-rendszerképek létrehozása</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-availability-sets">Magas rendelkezésre állású virtuális gépek létrehozása</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-create-vmss">Virtuálisgép-méretezési csoport létrehozása</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-load-balancer">Virtuális gépek terheléselosztása</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-virtual-network">Virtuális gépek és virtuális hálózatok kezelése</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-backup-vms">Virtuális gépek biztonsági mentése</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-monitoring">Virtuális gépek figyelése</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-azure-security">Virtuális gépek biztonságának kezelése</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd">CI/CD-infrastruktúra létrehozása a Jenkins, a Docker és a GitHub használatával</a></li>
    
</ol>

<h2 style="margin-top: 36px">Ingyenes PluralSight videós képzés</h2>
<ul class="panelContent cardsW">
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-infrastructure.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>Infrastruktúra kezelése</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-vms.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>A Virtual Machines használatának első lépései</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-iaas-monitoring.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>Az IaaS megfigyelés használatának első lépései</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<h2>Példák</h2>
<p>Az első alkalmazás telepítése az Azure-ban.</p>
<ul class="spaced">
    <li><a href="/azure/virtual-machines/virtual-machines-linux-cli-samples">Azure CLI</a></li>
    <li><a href="/azure/virtual-machines/virtual-machines-linux-powershell-samples">Azure PowerShell</a></li>
</ul>

<h2 style="margin-top: 36px">Referencia</h2>
<ul class="panelContent cardsW">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Parancssor</h3>
                        <p><a href="/cli/azure/vm">Azure CLI</a></p>
                        <p><a href="/powershell/azureps-cmdlets-docs">Azure PowerShell</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Nyelvek</h3>
                        <p><a href="/dotnet/api/microsoft.azure.management.compute">.NET</a></p>
                        <p><a href="/java/api">Java</a></p>
                        <p><a href="https://azure.microsoft.com/develop/nodejs/#azure-sdk">Node.js</a></p>
                        <p><a href="https://azure.microsoft.com/develop/python/">Python</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>REST</h3>
                        <p><a href="/rest/api/compute">REST API-referencia</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>
</div>

