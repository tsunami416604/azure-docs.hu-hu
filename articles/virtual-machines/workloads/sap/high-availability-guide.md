---
title: Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára | Microsoft dokumentumok
description: Magas rendelkezésre állási útmutató az SAP NetWeaver számára az Azure virtuális gépeken
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa0810818bf7cfea21f925ee639b4b5a50dcb23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246122"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Az SAP NetWeaver magas rendelkezésre állása az Azure virtuális gépein

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md
[dbms-guide-2.1]:../../virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:../../virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:../../virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:../../virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:../../virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:../../virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:../../virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:../../virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:../../virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:../../virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:../../virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:../../virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:../../virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:../../virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:../../virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:../../virtual-machines-windows-sap-deployment-guide.md
[deployment-guide-2.2]:../../virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:../../virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:../../virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:../../virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:../../virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:../../virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:../../virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:../../virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:../../virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:../../virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:../../virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:../../virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:../../virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:../../virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:../../virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:../../virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:../../virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:../../virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:../../virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:../../virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:../../virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:../../virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:../../virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:../../virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:../../virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:../../virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:../../virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:../../virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állású konfiguráció)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-termék rendelkezésre állási mátrixa)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Az Azure Virtual Machines olyan megoldások, amelyeknek számítási, tárolási és hálózati erőforrásokra van szükségük, minimális idő alatt és hosszadalmas beszerzési ciklusok nélkül. Az Azure virtuális gépek segítségével klasszikus alkalmazásokat, például AZ SAP NetWeaver-alapú ABAP, Java és egy ABAP+Java verem üzembe helyezhet. Bővítse a megbízhatóságot és a rendelkezésre állást további helyszíni erőforrások nélkül. Az Azure virtual machines támogatja a létesítmények közötti kapcsolatot, így integrálhatja az Azure virtuális gépeket a szervezet helyszíni tartományaiba, a magánfelhőkbe és az SAP-rendszerkörnyezetbe.

Ebben a cikkben ismertetik azokat a lépéseket, amelyek segítségével telepítheti a magas rendelkezésre állású SAP-rendszerek az Azure-ban az Azure Resource Manager telepítési modell használatával. Végigvezetjük az alábbi főfeladatokon:

* Keresse meg a megfelelő SAP-jegyzeteket és telepítési útmutatókat, amelyek az [Erőforrások][sap-ha-guide-2] szakaszban találhatók. Ez a cikk kiegészíti az SAP telepítési dokumentációt és az SAP Notes-t, amelyek az elsődleges erőforrások, amelyek segítségével telepítheti és telepítheti az SAP-szoftvereket adott platformokon.
* Ismerje meg az Azure Resource Manager üzembe helyezési modellje és az Azure klasszikus telepítési modell közötti különbségeket.
* Ismerje meg a Windows Server feladatátvételi fürtözési kvórummód, így kiválaszthatja a modellt, amely megfelelő az Azure-telepítés.
* Információ a Windows Server feladatátvételi fürtözésmegosztott tárolásáról az Azure-szolgáltatásokban.
* Ismerje meg, hogyan védheti meg az olyan egypontos összetevőket, mint az Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) és az adatbázis-kezelő rendszerek (DBMS), valamint a redundáns összetevőket, például az SAP Application Servert az Azure-ban.
* Kövesse a részletes példát egy nagy rendelkezésre állású SAP-rendszer telepítésére és konfigurálására egy Windows Server feladatátvételi fürtfürtben az Azure-ban az Azure Resource Manager használatával.
* Ismerje meg a Windows Server feladatátvételi fürtözésének azure-beli használatához szükséges további lépéseket, amelyek azonban nem szükségesek a helyszíni telepítéshez.

Az üzembe helyezés és a konfiguráció egyszerűsítése érdekében ebben a cikkben az SAP háromrétegű, magas rendelkezésre állású Erőforrás-kezelő sablonokat használjuk. A sablonok automatizálják a teljes infrastruktúra üzembe helyezését, amely egy magas rendelkezésre állású SAP-rendszerhez szükséges. Az infrastruktúra is támogatja az SAP-alkalmazás teljesítményszabvány (SAPS) méretezése az SAP-rendszer.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy megfelel az alábbi szakaszokban ismertetett előfeltételeknek. Ügyeljen arra is, hogy ellenőrizze az [Erőforrások][sap-ha-guide-2] szakaszban felsorolt összes erőforrást.

Ebben a cikkben az Azure Resource Manager-sablonokat használjuk a [háromrétegű SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)hez. A sablonok hasznos áttekintését az [SAP Azure Resource Manager-sablonok című témakörben találja.](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Erőforrások
Ezek a cikkek az Azure-beli SAP-telepítéseket fedik le:

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver számára][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP NetWeaver számára][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése az SAP NetWeaver számára][dbms-guide]
* [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára (ez az útmutató)][sap-ha-guide]

> [!NOTE]
> Amikor csak lehetséges, adunk egy linket a hivatkozó SAP telepítési útmutató (lásd az [SAP telepítési útmutató ).][sap-installation-guides] Az előfeltételek és a telepítési folyamattal kapcsolatos információk, célszerű figyelmesen elolvasni az SAP NetWeaver telepítési útmutatókat. Ez a cikk csak az SAP NetWeaver-alapú rendszerek, amelyek azure virtuális gépek használható feladatokat.
>
>

Ezek az SAP-megjegyzések az Sap azure-beli témaköréhez kapcsolódnak:

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és méretezés |
| [2015553] |SAP a Microsoft Azure-on: Támogatási előfeltételek |
| [1999351] |Továbbfejlesztett Azure-figyelés az SAP-hoz |
| [2178632] |Az SAP legfontosabb figyelési mutatói a Microsoft Azure-ban |
| [1999351] |Virtualizáció Windowsrendszeren: Továbbfejlesztett figyelés |
| [2243692] |Az Azure Premium SSD storage használata az SAP DBMS-példányhoz |

További információ az [Azure-előfizetések korlátairól,][azure-resource-manager/management/azure-subscription-service-limits-subscription]beleértve az általános alapértelmezett korlátozásokat és a maximális korlátozásokat.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Magas rendelkezésre állású SAP az Azure Resource Managerrel és az Azure klasszikus telepítési modelljével
Az Azure Resource Manager és az Azure klasszikus üzembe helyezési modelljei a következő területeken különböznek:

- Erőforráscsoportok
- Az Azure belső terheléselosztófüggősége az Azure erőforráscsoporttól
- Az SAP több SID-forgatókönyvek támogatása

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Erőforráscsoportok
Az Azure Resource Managerben erőforráscsoportok használatával kezelheti az Azure-előfizetésében lévő összes alkalmazás-erőforrást. Az erőforráscsoportban az integrált megközelítés életciklusa megegyezik. Például az összes erőforrás egyszerre jön létre, és egyszerre törlődnek. További információk az [erőforráscsoportokról](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Az Azure belső terheléselosztófüggősége az Azure erőforráscsoporttól

A klasszikus Azure-telepítési modellben függőség van az Azure belső terheléselosztó (Azure Load Balancer szolgáltatás) és a felhőszolgáltatás-csoport között. Minden belső terheléselosztónak egy felhőszolgáltatási csoportra van szüksége.

Az Azure Resource Managerben nincs szükség Azure-erőforráscsoportra az Azure Load Balancer használatához. A környezet egyszerűbb és rugalmasabb.

### <a name="support-for-sap-multi-sid-scenarios"></a>Az SAP több SID-forgatókönyvek támogatása

Az Azure Resource Managerben több SAP-rendszerazonosítót (SID) ASCS/SCS-példányt telepíthet egy fürtbe. Több-SID-példányok azért lehetséges, mert a támogatás több IP-címek minden Azure belső terheléselosztó.

Az Azure klasszikus telepítési modelljének használatához kövesse az SAP NetWeaver az [Azure-ban: Az SAP ASCS/SCS-példányok fürtözése az Azure-beli Windows Server feladatátvételi fürtözés használatával a SIOS DataKeeper használatával leírt eljárásokat.](https://go.microsoft.com/fwlink/?LinkId=613056)

> [!IMPORTANT]
> Azt javasoljuk, hogy az Azure Resource Manager üzembe helyezési modelljét használja az SAP-telepítésekhez. Számos olyan előnyt kínál, amelyek nem érhetők el a klasszikus üzembe helyezési modellben. További információ az Azure [üzembe helyezési modelljeiről.][virtual-machines-azure-resource-manager-architecture-benefits-arm]   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server feladatátvevő fürtözés
A Windows Server feladatátvételi fürtözése a Windows magas rendelkezésre állású SAP ASCS/SCS telepítésének és ADATBÁZIS-rendszerének alapja.

A feladatátvevő fürt 1+n független kiszolgálók (csomópontok) csoportja, amelyek együttműködnek az alkalmazások és szolgáltatások elérhetőségének növelése érdekében. Csomóponthiba esetén a Windows Server feladatátvevő fürtözéskiszámítja, hogy hány hiba fordulhat elő az alkalmazások és szolgáltatások biztosításához a kifogástalan állapotú fürt karbantartása közben. A feladatátvevő fürtözés eléréséhez különböző kvórummódok közül választhat.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Kvórum módok
A Windows Server feladatátvevő fürtözésének használatakor négy kvórummód közül választhat:

* **Csomópont többség**. A fürt minden csomópontja szavazhat. A klaszter csak a szavazatok többségével működik, azaz a szavazatok több mint felével. Ezt a beállítást olyan fürtök esetén javasoljuk, amelyek egyenlőtlen számú csomóponttal rendelkeznek. Egy hétcsomópontos fürt három csomópontja például meghibásodhat, és a fürt állóképek többséget ér el, és továbbra is futnak.  
* **Csomópont és lemez többség**. A fürttárolóban lévő minden csomópont és egy kijelölt lemez (egy tanúsító lemez) szavazhat, ha rendelkezésre állnak és kommunikációban vannak. A klaszter csak a szavazatok többségével működik, azaz a szavazatok több mint felével. Ennek a módnak van értelme egy páros számú csomópontos fürtkörnyezetben. Ha a csomópontok és a lemez fele online állapotban van, a fürt kifogástalan állapotban marad.
* **Csomópont- és fájlmegosztási többség**. Minden csomópont, valamint egy kijelölt fájlmegosztás (egy tanúsító fájlmegosztás), amelyet a rendszergazda létrehoz, szavazhat, függetlenül attól, hogy a csomópontok és a fájlmegosztás rendelkezésre állnak-e és a kommunikációban. A klaszter csak a szavazatok többségével működik, azaz a szavazatok több mint felével. Ennek a módnak van értelme egy páros számú csomópontos fürtkörnyezetben. Hasonló a Csomópont és a Lemez többség módhoz, de tanúsító lemez helyett tanúsító fájlmegosztást használ. Ez a mód könnyen megvalósítható, de ha maga a fájlmegosztás nem magas rendelkezésre állású, akkor egyetlen hibaponttá válhat.
* **Nincs többség: Csak lemez**. A fürt kvórummal rendelkezik, ha egy csomópont elérhető, és a fürttárolóban egy adott lemezzel kommunikál. Csak azok a csomópontok csatlakozhatnak a fürthöz, amelyek szintén kommunikálnak a lemezzel. Azt javasoljuk, hogy ne használja ezt a módot.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Windows Server feladatátvevő fürtözés a helyszínen
Az 1. Ha a csomópontok közötti hálózati kapcsolat meghibásodik, és mindkét csomópont működik, a kvórumlemez vagy a fájlmegosztás határozza meg, hogy melyik csomópont fogja továbbra is biztosítani a fürt alkalmazásait és szolgáltatásait. A kvórumlemezhez vagy fájlmegosztáshoz hozzáféréssel rendelkező csomópont az a csomópont, amely biztosítja a szolgáltatások folytatását.

Mivel ez a példa kétcsomópontos fürtöt használ, a Csomópont- és fájlmegosztási többség kvórummódot használjuk. A csomópont és a lemez többség is érvényes lehetőség. Éles környezetben azt javasoljuk, hogy használjon kvórumlemezt. A hálózati és tárolási rendszer technológiájával magas rendelkezésre állásúvá teheti azt.

![1. ábra: Példa egy Windows Server feladatátvételi fürtözési konfigurációra az SAP ASCS/SCS-hez az Azure-ban][sap-ha-guide-figure-1000]

_**1. ábra:** Példa az SAP ASCS/SCS Windows Server feladatátvételi fürtkonfigurációjára az Azure-ban_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Megosztott tároló
Az 1. A helyszíni megosztott tárolófürtben a fürt összes csomópontja észleli a megosztott tárolót. A zárolási mechanizmus megvédi az adatokat a sérüléstől. Minden csomópont észleli, ha egy másik csomópont meghibásodik. Ha egy csomópont meghibásodik, a fennmaradó csomópont a tárolási erőforrások tulajdonjogát veszi át, és biztosítja a szolgáltatások rendelkezésre állását.

> [!NOTE]
> Néhány DBMS-alkalmazás, például az SQL Server esetében nem szükséges megosztott lemezekre van szükség a magas rendelkezésre álláshoz. Az SQL Server Always On replikálja az ADATBÁZIS-adatokat és a naplófájlokat az egyik fürtcsomópont helyi lemezéről egy másik fürtcsomópont helyi lemezére. Ebben az esetben a Windows fürtkonfigurációnak nincs szüksége megosztott lemezre.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Hálózatkezelés és névfeloldás
Az ügyfélszámítógépek a DNS-kiszolgáló által biztosított virtuális IP-címen és virtuális állomásnévvel érik el a fürtöt. A helyszíni csomópontok és a DNS-kiszolgáló több IP-címet is képes kezelni.

Egy tipikus beállításnál két vagy több hálózati kapcsolatot használ:

* Külön csatlakozás a tárolóhoz
* Fürt-belső hálózati kapcsolat a szívveréshez
* Nyilvános hálózat, amelyet az ügyfelek a fürthöz való csatlakozáshoz használnak

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Windows Server feladatátvételi fürtözés az Azure-ban
A csupasz fémes vagy magánfelhő-telepítésekhez képest az Azure virtuális gépek további lépéseket igényelnek a Windows Server feladatátvételi fürtözésének konfigurálásához. Megosztott fürtlemez létrehozásakor több IP-címet és virtuális állomásnevet kell beállítania az SAP ASCS/SCS-példányhoz.

Ebben a cikkben bemutatjuk a legfontosabb fogalmakat és a további lépéseket hozhat létre egy SAP magas rendelkezésre állású központi szolgáltatási fürt az Azure-ban. Bemutatjuk, hogyan állíthatja be a harmadik féltől származó SIOS DataKeeper eszközt, és hogyan konfigurálhatja az Azure belső terheléselosztóját. Ezekkel az eszközökkel hozhat létre egy Windows feladatátvételi fürt egy fájlmegosztás tanúsító az Azure-ban.

![2. ábra: Windows Server feladatátvevő fürtözési konfiguráció az Azure-ban megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**2. ábra:** Windows Server feladatátvevő fürtözési konfiguráció az Azure-ban megosztott lemez nélkül_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Megosztott lemez az Azure-ban a SIOS DataKeeper-rel
Fürt megosztott tároló egy magas rendelkezésre állású SAP ASCS/SCS-példány. 2016 szeptemberétől az Azure nem kínál megosztott tárhelyet, amely segítségével megosztott tárolófürtöt hozhat létre. A harmadik féltől származó SIOS DataKeeper Cluster Edition szoftverrel létrehozhat egy tükrözött tárolót, amely szimulálja a fürt megosztott tárolását. A SIOS megoldás valós idejű szinkron adatreplikációt biztosít. Így hozhat létre megosztott lemezerőforrást egy fürthöz:

1. Csatlakoztasson egy további Azure virtuális merevlemezt (VHD) a Windows-fürtkonfigurációban lévő virtuális gépekhez (VM-ekhez).
2. Futtassa a SIOS DataKeeper Cluster Edition-t mindkét virtuálisgép-csomóponton.
3. Állítsa be a SIOS DataKeeper Cluster Edition-t úgy, hogy az tükrözze a forrás virtuális gépről a további virtuális merevlemezhez csatlakoztatott kötet tartalmát a cél virtuális gép további virtuális merevlemezhez csatlakoztatott kötetére. A SIOS DataKeeper kivonta a forrást és a helyi köteteket, majd egyetlen megosztott lemezként mutatja be azokat a Windows Server feladatátvételi fürtözésének.

További információ a [SIOS DataKeeper-](https://us.sios.com/products/datakeeper-cluster/)ről.

![3. ábra: Windows Server feladatátvételi fürtözési konfiguráció az Azure-ban a SIOS DataKeeper segítségével][sap-ha-guide-figure-1002]

_**3. ábra:** Windows Server feladatátvételi fürtözési konfiguráció az Azure-ban a SIOS DataKeeper segítségével_

> [!NOTE]
> Egyes DBMS-termékek, például az SQL Server esetében nem szükséges megosztott lemezekre van szükség a magas rendelkezésre álláshoz. Az SQL Server Always On replikálja az ADATBÁZIS-adatokat és a naplófájlokat az egyik fürtcsomópont helyi lemezéről egy másik fürtcsomópont helyi lemezére. Ebben az esetben a Windows fürtkonfigurációnak nincs szüksége megosztott lemezre.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Névfeloldás az Azure-ban
Az Azure felhőplatform nem kínál lehetőséget a virtuális IP-címek, például a lebegő IP-címek konfigurálására. Egy alternatív megoldásra van szüksége egy virtuális IP-cím beállításához a fürterőforrás felhőbeli eléréséhez.
Az Azure rendelkezik egy belső terheléselosztóval az Azure Load Balancer szolgáltatásban. A belső terheléselosztóval az ügyfelek a fürt virtuális IP-címén keresztül érik el a fürtöt.
A fürtcsomópontokat tartalmazó erőforráscsoportban telepítenie kell a belső terheléselosztót. Ezután konfigurálja az összes szükséges porttovábbítási szabályt a belső terheléselosztó mintavételi portjaival.
Az ügyfelek a virtuális állomás névvel csatlakozhatnak. A DNS-kiszolgáló feloldja a fürt IP-címét, és a belső terheléselosztó kezeli a porttovábbítást a fürt aktív csomópontjára.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>SAP NetWeaver magas rendelkezésre állás az Azure Infrastructure-as-a-Service (IaaS)
Az SAP-alkalmazások magas rendelkezésre állásának eléréséhez, például az SAP szoftverösszetevőihez, a következő összetevőket kell védenie:

* SAP Alkalmazáskiszolgáló példánya
* SAP ASCS/SCS-példány
* ADATBÁZIS-KEZELŐ-kiszolgáló

Az SAP-összetevők magas rendelkezésre állású forgatókönyvekben való védelméről az [Azure Virtual Machines tervezése és megvalósítása az SAP NetWeaver számára](planning-guide.md)című témakörben talál további információt.

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Magas rendelkezésre állású SAP-alkalmazáskiszolgáló
Általában nincs szükség egy adott magas rendelkezésre állású megoldásra az SAP Application Server és a párbeszédpéldányok. Redundanciával magas rendelkezésre állást érhet el, és az Azure virtuális gépek különböző példányaiban több párbeszédpéldányt is konfigurálhat. Legalább két SAP-alkalmazáspéldánynak kell telepítve lennie az Azure virtuális gépek két példányában.

![4. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**4. ábra:** Magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

Az SAP Application Server-példányokat tároló összes virtuális gépet ugyanabban az Azure-rendelkezésre állási csoportban kell elhelyeznie. Az Azure rendelkezésre állási készlete biztosítja a következőket:

* Minden virtuális gép ugyanannak a frissítési tartománynak a része. Egy frissítési tartomány, például győződjön meg arról, hogy a virtuális gépek nem frissülnek egy időben a tervezett karbantartási állásidő alatt.
* Minden virtuális gép ugyanannak a tartalék tartománynak a része. A tartalék tartomány, például gondoskodik arról, hogy a virtuális gépek telepítése, hogy egyetlen hibapont nem befolyásolja az összes virtuális gép rendelkezésre állását.

További információ [a virtuális gépek rendelkezésre állásának kezeléséről.][virtual-machines-manage-availability]

Mivel az Azure storage-fiók egy potenciális hibapont, fontos, hogy legalább két Azure storage-fiók, amelyben legalább két virtuális gép van elosztva. Ideális beállítás esetén az SAP-párbeszédpanelt futtató virtuális gépek lemezei egy másik tárfiókban lesznek telepítve.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Magas rendelkezésre állású SAP ASCS/SCS-példány
5. ábra egy példa egy magas rendelkezésre állású SAP ASCS/SCS-példány.

![5. ábra: Magas rendelkezésre állású SAP ASCS/SCS-példány][sap-ha-guide-figure-2001]

_**5. ábra:** Magas rendelkezésre állású SAP ASCS/SCS-példány_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>Az SAP ASCS/SCS-példány magas rendelkezésre állása a Windows Server feladatátvételi fürtözésével az Azure-ban
A csupasz fémes vagy magánfelhő-telepítésekhez képest az Azure virtuális gépek további lépéseket igényelnek a Windows Server feladatátvételi fürtözésének konfigurálásához. Windows feladatátvételi fürt létrehozásához szüksége van egy megosztott fürtlemezre, több IP-címre, több virtuális állomásnévre és egy Azure belső terheléselosztóra egy SAP ASCS/SCS-példány fürtözéséhez. Ezt részletesebben megbeszéljük a cikk későbbi részében.

![6. ábra: Windows Server feladatátvételi fürtözés egy SAP ASCS/SCS-konfigurációhoz az Azure-ban a SIOS DataKeeper használatával][sap-ha-guide-figure-1002]

_**6. ábra:** Windows Server feladatátvételi fürtözés az Azure-beli SAP ASCS/SCS-konfigurációhoz a SIOS DataKeeper segítségével_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Magas rendelkezésre állású DBMS-példány
A DBMS is egy kapcsolattartó pont egy SAP-rendszerben. Meg kell védeni egy magas rendelkezésre állású megoldás használatával. 7. ábra egy SQL Server Mindig a magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtözés és az Azure belső terheléselosztó. Az SQL Server Always On replikálja az ADATBÁZIS-adatokat és a naplófájlokat a saját DBMS-replikációjával. Ebben az esetben nincs szükség fürtmegosztott lemezekre, ami leegyszerűsíti a teljes telepítést.

![7. ábra: Példa egy magas rendelkezésre állású SAP DBMS-re, amelyen az SQL Server mindig be van kapcsolva][sap-ha-guide-figure-2003]

_**7. ábra:** Példa egy magas rendelkezésre állású SAP DBMS-re, amelyen az SQL Server mindig be van kapcsolva_

Az SQL Server Azure-beli fürtözéséről az Azure Resource Manager telepítési modelljének használatával az alábbi cikkekben talál további információt:

* [A Mindig rendelkezésre állási csoport konfigurálása az Azure virtuális gépeken manuálisan az Erőforrás-kezelő használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Azure belső terheléselosztó konfigurálása az Azure mindig elérhető állási csoportjához][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Teljes körű, magas rendelkezésre állású telepítési forgatókönyvek

### <a name="deployment-scenario-using-architectural-template-1"></a>Telepítési forgatókönyv az 1.

ábra egy példa egy SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **egy** SAP-rendszer. Ez a forgatókönyv a következőképpen van beállítva:

- Egy dedikált fürt az SAP ASCS/SCS-példányhoz használatos.
- Egy dedikált fürt használatos a DBMS-példányhoz.
- Az SAP Application Server-példányok saját dedikált virtuális gépeken vannak telepítve.

![8. ábra: SAP magas rendelkezésre állású architekturális sablon 1, az ASCS/SCS és a DBMS dedikált fürtjével][sap-ha-guide-figure-2004]

_**8. ábra:** SAP magas rendelkezésre állású architekturális sablon 1, dedikált fürtök ASCS/SCS és DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Telepítési forgatókönyv a 2.

9. ábra egy példa egy SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **egy** SAP-rendszer. Ez a forgatókönyv a következőképpen van beállítva:

- Egy dedikált fürt **both** az SAP ASCS/SCS-példányhoz és a DBMS-hez egyaránt használatos.
- Az SAP Application Server-példányok saját dedikált virtuális gépeken vannak telepítve.

![9. ábra: SAP magas rendelkezésre állású architekturális sablon 2, egy dedikált klaszter ESCS /SCS és egy dedikált fürt dbms][sap-ha-guide-figure-2005]

_**9. ábra:** SAP magas rendelkezésre állású architekturális sablon 2, egy dedikált fürt az ASCS/SCS és egy dedikált fürt a DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Telepítési forgatókönyv a 3.

10. ábra egy példa egy SAP NetWeaver magas rendelkezésre állású&gt; architektúra az Azure-ban **két** SAP-rendszerek, &lt;a SID1 és &lt;SID2.&gt; Ez a forgatókönyv a következőképpen van beállítva:

- Egy dedikált fürt az SAP ASCS/SCS SID1 *példányhoz és* az SAP ASCS/SCS SID2 **példányhoz** (egy fürthöz) is használatos.
- Az egyik dedikált fürt a DBMS SID1, egy másik dedikált fürt pedig a DBMS SID2 (két fürt) esetében használatos.
- Az SAP-rendszer SID1 SAP alkalmazáskiszolgálói példányai saját dedikált virtuális gépekkel rendelkeznek.
- Az SAP-rendszer SID2-jének SAP Alkalmazáskiszolgáló-példányai saját dedikált virtuális gépekkel rendelkeznek.

![10. ábra: SAP magas rendelkezésre állású 3-as architekturális sablon, különböző ASCS/SCS-példányok dedikált fürtjével][sap-ha-guide-figure-6003]

_**10. ábra:** SAP magas rendelkezésre állású 3-as architekturális sablon, dedikált fürttel a különböző ASCS/SCS-példányok számára_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Az infrastruktúra előkészítése

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az 1.
Az Sap-hoz készült Azure Resource Manager-sablonok megkönnyítik a szükséges erőforrások üzembe helyezését.

Az Azure Resource Manager háromrétegű sablonjai is támogatják a magas rendelkezésre állású forgatókönyveket, például az 1-es architekturális sablonban, amely két fürttel rendelkezik. Minden fürt egy SAP egyetlen meghibásodási pont az SAP ASCS/SCS és a DBMS.

Itt szerezheti be az Azure Resource Manager-sablonokat a cikkben leírt példaforgatókönyvhöz:

* [Azure Piactér lemezképe](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Egyéni kép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Az 1.

- Az Azure Portalon a **Paraméterek** panelen, a **SYSTEMAVAILABILITY** mezőben válassza a **HA**lehetőséget.

  ![11. ábra: Sap magas rendelkezésre állású Azure Resource Manager-paraméterek beállítása][sap-ha-guide-figure-3000]

_**11. ábra:** Sap magas rendelkezésre állású Azure Resource Manager-paramétereinek beállítása_


  A sablonok a következőket hozzák létre:

  * **Virtuális gépek**:
    * SAP Application Server virtuális gépek: <*SAPSystemSID*>-di-<*szám*>
    * ASCS/SCS fürt virtuális gépei: <*SAPSystemSID*>-ascs-<*szám*>
    * DBMS-fürt: *SAPSystemSID*>-db-<*szám* <>

  * **Hálózati kártyák az összes virtuális géphez, ip-címekkel:**
    * <*SAPSystemSID*>-nic-di-<*szám*>
    * <*SAPSystemSID*>-nic-ascs-<*szám*>
    * <*SAPSystemSID*>-nic-db-<*szám*>

  * **Azure Storage-fiókok**

  * **Elérhetőségi csoportok:**
    * SAP Application Server virtuális gépek: <*SAPSystemSID*>-avset-di
    * SAP ASCS/SCS fürt virtuális gépei: <*SAPSystemSID*>-avset-ascs
    * DBMS fürt virtuális gépei: <*SAPSystemSID*>-avset-db

  * **Az Azure belső terheléselosztója:**
    * Az ASCS/SCS példány összes portjával és az *SAPSystemSID*>-lb-ascs-<IP-címmel
    * Az SQL Server DBMS és IP-cím összes portjával <*SAPSystemSID*>-lb-db

  * **Hálózati biztonsági csoport**: <*SAPSystemSID*>-nsg-ascs-0  
    * Az *SAPSystemSID*>-ascs-0 virtuális gép <lévő távoli távoli asztali protokoll (RDP) portjával

> [!NOTE]
> A hálózati kártyák és az Azure belső terheléselosztók összes IP-címe alapértelmezés szerint **dinamikus.** Módosítsa őket **statikus** IP-címekre. Ezt a cikk későbbi részében ismertetjük.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Vállalati hálózati kapcsolattal (telephelyek közötti) rendelkező virtuális gépek üzembe helyezése éles környezetben való használatra
Éles SAP-rendszerek esetén telepítse na a vállalati hálózati kapcsolattal rendelkező Azure virtuális gépeket az Azure helyek közötti VPN vagy az Azure ExpressRoute használatával.

> [!NOTE]
> Használhatja az Azure virtuális hálózati példányt. A virtuális hálózat és az alhálózat már létrejött és előkészített.
>
>

1. Az Azure Portalon a **Paraméterek** panelEN a **NEWOREXISTINGSUBNET** mezőben válassza a **meglévőt.**
2. A **SUBNETID** mezőben adja hozzá az előkészített Azure-hálózat SubnetID teljes karakterláncát, ahol az Azure virtuális gépeit szeretné telepíteni.
3. Az Azure hálózati alhálózatainak listájának lefelvételéhez futtassa ezt a PowerShell-parancsot:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Az **Azonosító** mezőben a **SUBNETID látható.**
4. Az összes **SUBNETID** érték listájának lefelvételéhez futtassa ezt a PowerShell-parancsot:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   A **SUBNETID** így néz ki:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Csak felhőalapú SAP-példányok üzembe helyezése teszteléshez és bemutatóhoz
A magas rendelkezésre állású SAP-rendszert csak felhőalapú üzembe helyezési modellben telepítheti. Ez a fajta központi telepítés elsősorban a bemutató és a tesztelési használati esetek. Nem alkalmas gyártási használati esetekre.

- Az Azure Portalon a **Paraméterek** panelEN a **NEWOREXISTINGSUBNET** mezőben válassza az **Új**lehetőséget. Hagyja üresen a **SUBNETID mezőt.**

  Az SAP Azure Resource Manager sablon automatikusan létrehozza az Azure virtuális hálózatot és alhálózatot.

> [!NOTE]
> Emellett legalább egy dedikált virtuális gépet kell telepítenie az Active Directoryhoz és a DNS-hez ugyanabban az Azure virtuális hálózati példányban. A sablon nem hozza létre ezeket a virtuális gépeket.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Az infrastruktúra előkészítése a 2.

Ezt az Azure Resource Manager-sablont az SAP-hoz használhatja a szükséges infrastruktúra-erőforrások üzembe helyezésének egyszerűsítéséhez az SAP 2-es architekturális sablonhoz.

Itt szerezheti be az Azure Resource Manager-sablonokat ehhez a telepítési forgatókönyvhöz:

* [Azure Piactér lemezképe](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Egyéni kép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Az infrastruktúra előkészítése a 3- as építészeti sablonhoz

Előkészítheti az infrastruktúrát, és konfigurálhatja az **SAP-t a több-SID.You**can prepare the infrastructure and configure SAP for multi-SID . Például hozzáadhat egy további SAP ASCS/SCS-példányt egy *meglévő* fürtkonfigurációhoz. További információ: [Egy további SAP ASCS/SCS-példány konfigurálása egy meglévő fürtkonfigurációba SAP többSID-konfiguráció létrehozásához az Azure Resource Managerben.][sap-ha-multi-sid-guide]

Ha új, több SID-fürtöt szeretne létrehozni, használhatja a [githubon a több SID-s gyorsindítási sablonokat.](https://github.com/Azure/azure-quickstart-templates)
Új, több SID-fürt létrehozásához a következő három sablont kell telepítenie:

* [ASCS/SCS sablon](#ASCS-SCS-template)
* [Adatbázissablon](#database-template)
* [Alkalmazáskiszolgálók sablonja](#application-servers-template)

A következő szakaszok további részleteket tartalmaznak a sablonokról és a sablonokban megadandó paraméterekről.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS sablon

Az ASCS/SCS sablon két virtuális gépet telepít, amelyek segítségével több ASCS/SCS-példányt tartalmazó Windows Server feladatátvevő fürtet hozhat létre.

Az ASCS/SCS multi-SID sablon beállításához az [ASCS/SCS multi-SID sablonban][sap-templates-3-tier-multisid-xscs-marketplace-image]adja meg a következő paraméterek értékeit:

  - **Erőforrás-előtag**.  Állítsa be az erőforrás-előtagot, amely a központi telepítés során létrehozott összes erőforrás előtagra szolgál. Mivel az erőforrások nem csak egy SAP-rendszerhez tartoznak, az erőforrás előtagja nem egy SAP-rendszer sid-je.  Az előtagnak **három és hat karakter**között kell lennie .
  - **Halom típusa**. Válassza ki az SAP-rendszer veremtípusát. A verem típusától függően az Azure Load Balancer rendelkezik egy (Csak ABAP vagy Java) vagy két (ABAP+Java) privát IP-címSAP-rendszerenként.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerét.
  -  **SAP-rendszerszáma**. Válassza ki a fürtben telepíteni kívánt SAP-rendszerek számát.
  -  **A rendszer elérhetősége**. Válassza a **HA**lehetőséget.
  -  **Rendszergazdai felhasználónév és rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépre.
  -  **Új vagy meglévő alhálózat**. Állítsa be, hogy új virtuális hálózatot és alhálózatot kell-e létrehozni, vagy egy meglévő alhálózatot kell használni. Ha már rendelkezik olyan virtuális hálózattal, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő**lehetőséget.
  -  **Alhálózati azonosító**. Ha azt szeretné, hogy a virtuális gép egy meglévő virtuális hálózat, ahol a virtuális gép definiált alhálózat a virtuális gép kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában így néz ki: /subscriptions/<*subscription id*>/resourceGroups/<*resource group name*>/providers/Microsoft.Network/virtualNetworks/<virtual network *name*>/subnets/<*subnet name*>

A sablon egyetlen Azure Load Balancer-példányt telepít, amely több SAP-rendszert támogat.

- Az ASCS-példányok a 00, 10, 20...
- Az SCS-példányok a 01, 11, 21...
- Az ASCS enqueue replikációs kiszolgáló (ERS) (csak Linux) példányai a 02, 12, 22 számú példányra vannak konfigurálva...
- Az SCS ERS (csak Linux) példányok a 03, 13, 23...

A terheléselosztó 1 (2 For Linux) VIP(s), 1x VIP ASCS/SCS és 1x VIP for ERS (csak Linux) tartalmazza.

Az alábbi lista tartalmazza az összes terheléselosztási szabályt (ahol x az SAP-rendszer száma, például 1, 2, 3...):
- Windows-specifikus portok minden SAP rendszerhez: 445, 5985
- ASCS-portok (példányszám x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portok (példányszám x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS portok Linuxon (példányszám x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS portok Linuxon (példányszám x3): 33x3, 5x313, 5x314, 5x316

A terheléselosztó a következő mintavételi portok használatára van konfigurálva (ahol x az SAP-rendszer száma, például 1, 2, 3...):
- ASCS/SCS belső terheléselosztó szondaport: 620x0
- ERS belső terheléselosztó szondaport (csak Linux esetén): 621x2

#### <a name="database-template"></a><a name="database-template"></a>Adatbázissablon

Az adatbázissablon egy vagy két virtuális gépet telepít, amelyek segítségével telepítheti a relációs adatbázis-kezelő rendszert (RDBMS) egy SAP-rendszerhez. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor ezt a sablont ötször kell telepítenie.

Az adatbázis több-SID sablonjának beállításához az [adatbázis több-SID sablonjában][sap-templates-3-tier-multisid-db-marketplace-image]adja meg a következő paraméterek értékeit:

- **Sap rendszer azonosító**. Adja meg a telepíteni kívánt SAP-rendszer azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használva.
- **Os típus**. Válassza ki a virtuális gépek operációs rendszerét.
- **Adatbázisgép típusa**. Válassza ki a fürtre telepíteni kívánt adatbázis típusát. Válassza az **SQL** lehetőséget, ha telepíteni szeretné a Microsoft SQL Server kiszolgálót. Válassza ki **a HANA,** ha azt tervezi, hogy telepítse az SAP HANA a virtuális gépeken. Győződjön meg arról, hogy válassza ki a megfelelő operációs rendszer típusát: válassza ki a **Windows** SQL,és válasszon egy Linux disztribúció hana. A virtuális gépekhez csatlakoztatott Azure load balancer a kiválasztott adatbázistípus támogatására lesz konfigurálva:
  * **SQL**. A terheléselosztó teherelosztási port1433. Győződjön meg arról, hogy ezt a portot használja az SQL Server Always On beállításához.
  * **Hana**. A terheléselosztó a 35015-ös és a 35017-es portokat fogja kiegyensúlyozni. Győződjön meg arról, hogy az SAP HANA-t **az 50-es**példányszámmal telepíti.
  A terheléselosztó a 62550-es szondaportot fogja használni.
- **Sap rendszer mérete**. Állítsa be az új rendszer által biztosított SAPS számát. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
- **A rendszer elérhetősége**. Válassza a **HA**lehetőséget.
- **Rendszergazdai felhasználónév és rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépre.
- **Alhálózati azonosító**. Adja meg az ASCS/SCS sablon telepítése során használt alhálózat azonosítóját, vagy az ASCS/SCS sablon központi telepítésének részeként létrehozott alhálózat azonosítóját.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a>Alkalmazáskiszolgálók sablonja

Az alkalmazáskiszolgálók sablon két vagy több virtuális gépet telepít, amelyek SAP Application Server-példányként használhatók egy SAP-rendszerhez. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor ezt a sablont ötször kell telepítenie.

Az alkalmazáskiszolgálók több SID-sablonjának beállításához az [alkalmazáskiszolgálók több SID-sablonjában][sap-templates-3-tier-multisid-apps-marketplace-image]adja meg a következő paraméterek értékeit:

  -  **Sap rendszer azonosító**. Adja meg a telepíteni kívánt SAP-rendszer azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használva.
  -  **Os típus**. Válassza ki a virtuális gépek operációs rendszerét.
  -  **Sap rendszer mérete**. Az új rendszer által biztosított SAPS-ek száma. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
  -  **A rendszer elérhetősége**. Válassza a **HA**lehetőséget.
  -  **Rendszergazdai felhasználónév és rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépre.
  -  **Alhálózati azonosító**. Adja meg az ASCS/SCS sablon telepítése során használt alhálózat azonosítóját, vagy az ASCS/SCS sablon központi telepítésének részeként létrehozott alhálózat azonosítóját.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure virtuális hálózat
A példában az Azure virtuális hálózat címtere 10.0.0.0/16. Van egy Alhálózat nevű **alhálózat,** amelynek címtartománya 10.0.0.0/24. Az összes virtuális gép és belső terheléselosztó telepítve van ebben a virtuális hálózatban.

> [!IMPORTANT]
> Ne módosítsa a hálózati beállításokat a vendég operációs rendszeren belül. Ide tartoznak az IP-címek, a DNS-kiszolgálók és az alhálózatok. Konfigurálja az összes hálózati beállítást az Azure-ban. A DHCP (Dynamic Host Configuration Protocol) szolgáltatás propagálja a beállításokat.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-címek

A szükséges DNS-IP-címek beállításához tegye a következő lépéseket.

1. Az Azure PortalON a **DNS-kiszolgálók** panelen győződjön meg arról, hogy a virtuális hálózati **DNS-kiszolgálók** beállítása **Egyéni DNS**.
2. Válassza ki a beállításokat a hálózat típusa alapján. További információkért lásd a következőket:
   * Adja hozzá a helyszíni DNS-kiszolgálók IP-címét.  
   A helyszíni DNS-kiszolgálókat kiterjesztheti az Azure-ban futó virtuális gépekre. Ebben a forgatókönyvben hozzáadhatja azoknak az Azure virtuális gépeknek az IP-címeit, amelyeken a DNS-szolgáltatást futtatja.
   * Az Azure-ban elkülönített központi telepítések esetén: Telepítsen egy további virtuális gépet ugyanabban a virtuális hálózati példányban, amely DNS-kiszolgálóként szolgál. Adja hozzá a DNS-szolgáltatás futtatásához beállított Azure virtuális gépek IP-címét.

   ![12. ábra: DNS-kiszolgálók konfigurálása az Azure virtuális hálózathoz][sap-ha-guide-figure-3001]

   _**12. ábra:** DNS-kiszolgálók konfigurálása az Azure virtuális hálózathoz_

   > [!NOTE]
   > Ha módosítja a DNS-kiszolgálók IP-címét, újra kell indítania az Azure virtuális gépeket a módosítás alkalmazásához és az új DNS-kiszolgálók terjesztéséhez.
   >
   >

A példánkban a DNS-szolgáltatás telepítve és konfigurálva van ezeken a Windows virtuális gépeken:

| Virtuális gép szerepkör | Virtuálisgép-állomás neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Állomásnevek és statikus IP-címek az SAP ASCS/SCS fürtözött példányhoz és az DBMS fürtözött példányhoz

A helyszíni telepítéshez ezekre a fenntartott állomásnevekre és IP-címekre van szükség:

| Virtuális állomásnév szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS első fürtvirtuális állomásneve (fürtkezeléshez) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS-példány virtuális állomásneve |pr1-ascs-nedv |10.0.0.43 |
| SAP DBMS második fürt virtuális állomásneve (fürtkezelés) |pr1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor hozza létre a virtuális állomás **neveket pr1-ascs-vir** és **pr1-dbms-vir** és a kapcsolódó IP-címeket, amelyek kezelik a fürt maga. Ennek módjáról a [Fürtcsomópontok gyűjtése fürtkonfigurációban][sap-ha-guide-8.12.1]című témakörben talál további információt.

A másik két virtuális állomásnevet, a **pr1-ascs-sap** és **pr1-dbms-sap**és a kapcsolódó IP-címeket manuálisan is létrehozhatja a DNS-kiszolgálón. A fürtözött SAP ASCS/SCS-példány és a fürtözött DBMS-példány ezeket az erőforrásokat használja. Ennek módjáról a [Fürtözött SAP ASCS/SCS-példány virtuális állomásnevének létrehozása][sap-ha-guide-9.1.1]című témakörben talál.

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Statikus IP-címek beállítása az SAP virtuális gépekhez
Miután üzembe helyezte a fürtben használt virtuális gépeket, statikus IP-címeket kell beállítania az összes virtuális géphez. Ehhez az Azure virtuális hálózat konfiguráció, és nem a vendég operációs rendszer.

1. Az Azure portalon válassza az **Erőforráscsoport** >  >  > **hálózatikártya-beállítások****IP-címét**lehetőséget.**Settings**
2. Az **IP-címek** panel hozzárendelés **e**területén válassza a **Statikus**lehetőséget. Az **IP-cím** mezőbe írja be a használni kívánt IP-címet.

   > [!NOTE]
   > Ha módosítja a hálózati kártya IP-címét, újra kell indítania az Azure virtuális gépeket a módosítás alkalmazásához.  
   >
   >

   ![13. ábra: Statikus IP-címek beállítása az egyes virtuális gépek hálózati kártyáihoz][sap-ha-guide-figure-3002]

   _**13. ábra:** Statikus IP-címek beállítása az egyes virtuális gépek hálózati kártyáihoz_

   Ismételje meg ezt a lépést az összes hálózati csatoló esetében, azaz az összes virtuális gépen, beleértve az Active Directory/DNS szolgáltatáshoz használni kívánt virtuális gépeket is.

A példánkban ezek a virtuális gépek és statikus IP-címek:

| Virtuális gép szerepkör | Virtuálisgép-állomás neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP-alkalmazáskiszolgáló-példány |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Második SAP-alkalmazáskiszolgáló példány |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP-alkalmazáskiszolgáló-példány |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Az ASCS/SCS-példány első fürtcsomópontja |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Második fürtcsomópont AZ ASCS/SCS-példányhoz |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| A DBMS-példány első fürtcsomópontja |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| A DBMS-példány második fürtcsomópontja |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Statikus IP-cím beállítása az Azure belső terheléselosztójának

Az SAP Azure Resource Manager-sablon létrehoz egy Azure belső terheléselosztót, amely az SAP ASCS/SCS-példányfürthöz és a DBMS-fürthöz használatos.

> [!IMPORTANT]
> Az SAP ASCS/SCS virtuális állomásnevének IP-címe megegyezik az SAP ASCS/SCS belső terheléselosztó: **pr1-lb-ascs**IP-címével.
> A DBMS virtuális nevének IP-címe megegyezik a DBMS belső terheléselosztójának **IP-címével: pr1-lb-dbms**.
>
>

Statikus IP-cím beállítása az Azure belső terheléselosztóhoz:

1. A kezdeti központi telepítés a belső terheléselosztó IP-címét **dinamikusra állítja.** Az Azure Portalon az **IP-címek** panelen a **Hozzárendelés**csoportban válassza a **Statikus**lehetőséget.
2. Állítsa be a belső terheléselosztó **pr1-lb-ascs** IP-címét az SAP ASCS/SCS-példány virtuális állomásnevének IP-címére.
3. Állítsa be a belső terheléselosztó **pr1-lb-dbms** IP-címét a DBMS-példány virtuális állomásnevének IP-címére.

   ![14. ábra: Statikus IP-címek beállítása az SAP ASCS/SCS-példány belső terheléselosztójának][sap-ha-guide-figure-3003]

   _**14. ábra:** Statikus IP-címek beállítása az SAP ASCS/SCS-példány belső terheléselosztójának_

A példában két Azure belső terheléselosztóval rendelkezik, amelyek rendelkeznek ezekkel a statikus IP-címekkel:

| Az Azure belső terheléselosztó szerepköre | Az Azure belső terheléselosztójának neve | Statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS-példány belső terheléselosztója |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS belső terheléselosztó |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz

Az SAP Azure Resource Manager sablon létrehozza a szükséges portokat:
* ABAP ASCS-példány, **00** alapértelmezett példányszámmal
* Java SCS-példány, **01-es** alapértelmezett példányszámmal

Az SAP ASCS/SCS-példány telepítésekor az ABAP ASCS-példány **00-as** számú példányát és a Java SCS-példány **01-es** számú példányát kell használnia.

Ezután hozza létre a szükséges belső terheléselosztási végpontokat az SAP NetWeaver portokhoz.

A szükséges belső terheléselosztási végpontok létrehozásához először hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver ABAP ASCS portokhoz:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (ASCS-példány 00-s példányszámmal) (ERS 10-el) |
| --- | --- | --- |
| Várólistára helyezett kiszolgáló / *lbrule3200* |32<*példányszáma*> |3200 |
| ABAP üzenetkiszolgáló / *lbrule3600* |36<*példányszáma*> |3600 |
| Belső ABAP üzenet / *lbrule3900* |39<*példányszáma*> |3900 |
| Üzenetkiszolgáló HTTP / *Lbrule8100* |81<*példányszáma*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5<*példányszáma*>13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5<*példányszáma*>14 |50014 |
| Várólistára helyezett replikáció / *Lbrule50016* |5<*példányszáma*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*példányszáma*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*példányszáma*>14 |51014 |
| Győzelem *RM Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**1.** Az SAP NetWeaver ABAP ASCS-példányok portszámai_

Ezután hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver Java SCS portokhoz:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Betonportok (SCS-példány 01-es példányszámmal) (ERS 11-es sel) |
| --- | --- | --- |
| Várólistára helyezett kiszolgáló / *lbrule3201* |32<*példányszáma*> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33<*példányszáma*> |3301 |
| Java üzenetkiszolgáló / *lbrule3900* |39<*példányszáma*> |3901 |
| Üzenetkiszolgáló HTTP / *Lbrule8101* |81<*példányszáma*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5<*példányszáma*>13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5<*példányszáma*>14 |50114 |
| Várólistára helyezett replikáció / *Lbrule50116* |5<*példányszáma*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*példányszáma*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*példányszáma*>14 |51114 |
| Győzelem *RM Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**2.** Az SAP NetWeaver Java SCS-példányok portszámai_

![15. ábra: Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz][sap-ha-guide-figure-3004]

_**15. ábra:** Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz_

Állítsa be a **pr1-lb-dbms** terheléselosztó IP-címét a DBMS-példány virtuális állomásnevének IP-címére.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztójára vonatkozóan

Ha az SAP ASCS- vagy SCS-példányokhoz különböző számokat szeretne használni, módosítania kell a portok nevét és értékeit az alapértelmezett értékekről.

1. Az Azure Portalon válassza >  ** <a *SID*>-lb-ascs terheléselosztási****szabályok.**
2. Az SAP ASCS- vagy SCS-példányhoz tartozó összes terheléselosztási szabály esetében módosítsa ezeket az értékeket:

   * Név
   * Port
   * Háttérport

   Ha például az alapértelmezett ASCS-példányszámot 00-ról 31-re szeretné módosítani, az 1.

   Íme egy példa az *lbrule3200*port frissítésére.

   ![16. ábra: Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztójára vonatkozóan][sap-ha-guide-figure-3005]

   _**16. ábra:** Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztójára vonatkozóan_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows virtuális gépek hozzáadása a tartományhoz

Miután statikus IP-címet rendelt a virtuális gépekhez, adja hozzá a virtuális gépeket a tartományhoz.

![17. ábra: Virtuális gép hozzáadása tartományhoz][sap-ha-guide-figure-3006]

_**17. ábra:** Virtuális gép hozzáadása tartományhoz_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Rendszerleíró bejegyzések hozzáadása az SAP ASCS/SCS-példány mindkét fürtcsomópontján

Az Azure Load Balancer rendelkezik egy belső terheléselosztóval, amely bezárja a kapcsolatokat, ha a kapcsolatok egy meghatározott ideig (egy tétlen időtúltöltés) tétlenek. A párbeszédpéldányokban lévő SAP-munkafolyamatok megnyitják az SAP-várólistás folyamattal létesített kapcsolatokat, amint az első enqueue/dequeue kérelmet el kell küldeni. Ezek a kapcsolatok általában a munkafolyamat vagy a várólistán lévő folyamat újraindításáig maradnak létre. Azonban ha a kapcsolat egy meghatározott ideig tétlen, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mert az SAP munkafolyamat újra létrehozza a kapcsolatot a várólistára folyamat, ha már nem létezik. Ezeket a tevékenységeket az SAP-folyamatok fejlesztői nyomai dokumentálják, de nagy mennyiségű extra tartalmat hoznak létre ezekben a nyomkövetésekben. Célszerű módosítani a TCP/IP protokollt `KeepAliveTime` `KeepAliveInterval` és mindkét fürtcsomóponton. Kombinálja ezeket a változásokat a TCP/IP paraméterek et az SAP profil paraméterekkel, amelyeket a cikk későbbi részében ismertetett.

Az SAP ASCS/SCS-példány mindkét fürtcsomópontjának rendszerleíró bejegyzéseinek hozzáadásához először adja hozzá ezeket a Windows rendszerleíró bejegyzéseket az SAP ASCS/SCS mindkét Windows-fürtcsomópontján:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Paraméterek |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**3.** Az első TCP/IP paraméter módosítása_

Ezután adja hozzá ezt a Windows rendszerleíró bejegyzéseket az SAP ASCS/SCS mindkét Windows fürtcsomópontján:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Paraméterek |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**4.** A második TCP/IP paraméter módosítása_

**A módosítások alkalmazásához indítsa újra mindkét fürtcsomópontot.**

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Windows Server feladatátvevő fürtfürt beállítása SAP ASCS/SCS-példányhoz

Windows Server feladatátvevő fürt fürt beállítása SAP ASCS/SCS-példányhoz a következő feladatokat foglalja magában:

- A fürtcsomópontok gyűjtése fürtkonfigurációban
- Fürtfájlmegosztás tanúsítójának konfigurálása

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>A fürtcsomópontok gyűjtése fürtkonfigurációban

1. A Szerepkör és szolgáltatások hozzáadása varázslóban adja hozzá a feladatátvevő fürtözést mindkét fürtcsomóponthoz.
2. Állítsa be a feladatátvevő fürtöt a Feladatátvevő fürtkezelővel. A Feladatátvevőfürt-kezelőben válassza **a Fürt létrehozása**lehetőséget, majd csak az első fürt, az A csomópont nevét adja hozzá. Még ne adja hozzá a második csomópontot; egy későbbi lépésben adja hozzá a második csomópontot.

   ![18. ábra: Adja hozzá az első fürtcsomópont kiszolgáló- vagy virtuálisgép-nevét][sap-ha-guide-figure-3007]

   _**18. ábra:** Az első fürtcsomópont kiszolgáló- vagy virtuálisgép-nevének hozzáadása_

3. Adja meg a fürt hálózati nevét (virtuális állomásnevét).

   ![19. ábra: Adja meg a fürt nevét][sap-ha-guide-figure-3008]

   _**19. ábra:** Adja meg a fürt nevét_

4. A fürt létrehozása után futtasson fürtérvényesítési tesztet.

   ![20. ábra: A fürtérvényesítési ellenőrzés futtatása][sap-ha-guide-figure-3009]

   _**20. ábra:** A fürtérvényesítési ellenőrzés futtatása_

   A folyamat ezen pontján figyelmen kívül hagyhatja a lemezekkel kapcsolatos figyelmeztetéseket. Később hozzá kell adnia egy fájlmegosztási tanúsítót és a SIOS megosztott lemezeket. Ebben a szakaszban nem kell aggódnia a kvórum miatt.

   ![21. ábra: Nem található kvórumlemez][sap-ha-guide-figure-3010]

   _**21. ábra:** Nem található kvórumlemez_

   ![22. ábra: A központi fürterőforrásnak új IP-címre van szüksége][sap-ha-guide-figure-3011]

   _**22. ábra:** A központi fürterőforrásnak új IP-címre van szüksége_

5. Módosítsa a központi fürtszolgáltatás IP-címét. A fürt nem indítható el, amíg nem módosítja a központi fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címe a virtuális gép egyik csomópontjára mutat. Ehhez a fő fürtszolgáltatás IP-erőforrásának **Tulajdonságok** lapján.

   Például hozzá kell rendelnünk egy IP-címet (példában **10.0.0.42)** a fürt virtuális állomásnevéhez **pr1-ascs-vir**.

   ![23. ábra: A Tulajdonságok párbeszédpanelen módosítsa az IP-címet][sap-ha-guide-figure-3012]

   _**23. ábra:** A **Tulajdonságok** párbeszédpanelen módosítsa az IP-címet_

   ![24. ábra: A fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

   _**24. ábra:** A fürt számára fenntartott IP-cím hozzárendelése_

6. A fürt virtuális állomásnevének online állapotba hozása.

   ![25. ábra: A fürt alapszolgáltatása működik, és a megfelelő IP-címmel működik][sap-ha-guide-figure-3014]

   _**25. ábra:** A fürt alapszolgáltatása működik, és a megfelelő IP-címmel működik_

7. Adja hozzá a második fürtcsomópontot.

   Most, hogy a fő fürtszolgáltatás működik, hozzáadhatja a második fürtcsomópontot.

   ![26. ábra: A második fürtcsomópont hozzáadása][sap-ha-guide-figure-3015]

   _**26. ábra:** A második fürtcsomópont hozzáadása_

8. Adja meg a második fürtcsomópont-állomás nevét.

   ![27. ábra: Adja meg a második fürtcsomópont állomásnevét][sap-ha-guide-figure-3016]

   _**27. ábra:** Adja meg a második fürtcsomópont állomásnevét_

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az **Összes jogosult tároló hozzáadása a fürthöz** jelölőnégyzet **NINCS** bejelölve.  
   >
   >

   ![28. ábra: Ne jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

   _**28. ábra:** **Ne** jelölje be a jelölőnégyzetet_

   Figyelmen kívül hagyhatja a kvórumra és a lemezekre vonatkozó figyelmeztetéseket. A kvórumot később fogja beállítani, és a lemezt később fogja megosztani, az [SAP ASCS/SCS fürtmegosztási lemez SIOS DataKeeper Cluster Edition telepítése című][sap-ha-guide-8.12.3]témakörben leírtak szerint.

   ![29. ábra: A lemezkvórumra vonatkozó figyelmeztetések figyelmen kívül hagyása][sap-ha-guide-figure-3018]

   _**29. ábra:** A lemezkvórumra vonatkozó figyelmeztetések figyelmen kívül hagyása_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Fürtfájlmegosztás tanúsítójának konfigurálása

A fürtfájlmegosztás tanúsítójának konfigurálása a következő feladatokat foglalja magában:

- Fájlmegosztás létrehozása
- A fájlmegosztási tanúsító kvórumának beállítása a feladatátvevői fürtkezelőben

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Fájlmegosztás létrehozása

1. Kvórumlemez helyett jelöljön ki egy tanúsító fájlmegosztást. A SIOS DataKeeper támogatja ezt a lehetőséget.

   Ebben a cikkben a példákban a fájlmegosztás tanúsító az Azure-ban futó Active Directory/DNS-kiszolgálón található. Az aktamegosztási tanú neve **domcontr-0.** Mivel vpn-kapcsolatot konfigurált volna az Azure-ral (a helyek közötti VPN-en vagy az Azure ExpressRoute-on keresztül), az Active Directory/DNS-szolgáltatás helyszíni, és nem alkalmas fájlmegosztási tanúsító futtatására.

   > [!NOTE]
   > Ha az Active Directory/DNS szolgáltatás csak a helyszínen fut, ne konfigurálja a fájlmegosztás tanúsítóját a helyszíni Active Directory/DNS Windows operációs rendszeren. Az Azure-ban futó fürtcsomópontok és a helyszíni Active Directory/DNS közötti hálózati késés túl nagy lehet, és kapcsolódási problémákat okozhat. Ügyeljen arra, hogy konfigurálja a fájlmegosztás tanúsító egy Azure virtuális gépen, amely a fürtcsomópont közelében fut.  
   >
   >

   A kvórummeghajtónak legalább 1024 MB szabad helyre van szüksége. Javasoljuk, hogy 2048 MB szabad terület a kvórummeghajtó.

2. Adja hozzá a fürtnév-objektumot.

   ![30. ábra: A fürtnév-objektum megosztására vonatkozó engedélyek hozzárendelése][sap-ha-guide-figure-3019]

   _**30. ábra:** A fürtnév-objektum megosztására vonatkozó engedélyek hozzárendelése_

   Győződjön meg arról, hogy az engedélyek tartalmazzák a fürtnév-objektum megosztásának adatait (példánkban **pr1-ascs-vir$**).

3. Ha hozzá szeretné adni a fürtnév-objektumot a listához, válassza a **Hozzáadás gombot.** Módosítsa a szűrőt a számítógép-objektumok ellenőrzésére a 31.

   ![31. ábra: Az objektumtípusok módosítása a számítógépekre][sap-ha-guide-figure-3020]

   _**31. ábra:** Az objektumtípusok módosítása számítógépekre_

   ![32. ábra: Jelölje be a Számítógépek jelölőnégyzetet.][sap-ha-guide-figure-3021]

   _**32. ábra:** Jelölje be a **Számítógépek** jelölőnégyzetet._

4. Írja be a fürtnév-objektumot a 31. Mivel a rekord már létrejött, módosíthatja az engedélyeket, ahogy az a 30.

5. Válassza a megosztás **Biztonság** lapját, majd adja meg a fürtnév-objektum részletesebb engedélyeit.

   ![33. ábra: A fürtnév-objektum biztonsági attribútumainak beállítása a fájlmegosztáskvórumban][sap-ha-guide-figure-3022]

   _**33. ábra:** A fürtnév-objektum biztonsági attribútumainak beállítása a fájlmegosztáskvórumban_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>A fájlmegosztási tanúsító kvórumának beállítása a Feladatátvevői fürtkezelőben

1. Nyissa meg a Kvórum beállítása varázslót.

   ![34. ábra: A Fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

   _**34. ábra:** A Fürtkvórum beállítása varázsló indítása_

2. A **Kvórumkonfiguráció kiválasztása** lapon válassza **a Kvórumtanúsító kiválasztása**lehetőséget.

   ![35. ábra: A kvórum konfigurációi közül választhat][sap-ha-guide-figure-3024]

   _**35. ábra:** Kvórum konfigurációk közül választhat_

3. A **Kvórum tanúsító kiválasztása** lapon válassza **a Fájlmegosztási tanúsító konfigurálása**lehetőséget.

   ![36. ábra: Válassza ki a tanúsító fájlmegosztást][sap-ha-guide-figure-3025]

   _**36. ábra:** A tanúsító fájlmegosztás kiválasztása_

4. Adja meg a fájlmegosztás UNC elérési \\útját (példánkban: domcontr-0\FSW). A módosítások listájának megtekintéséhez válassza a **Tovább gombot.**

   ![37. ábra: A tanúsító megosztás fájlmegosztási helyének meghatározása][sap-ha-guide-figure-3026]

   _**37. ábra:** A tanúsító megosztás fájlmegosztási helyének meghatározása_

5. Jelölje ki a kívánt módosításokat, majd kattintson a **Tovább gombra.** Sikeresen újra kell konfigurálnia a fürtkonfigurációt a 38.  

   ![38. ábra: Megerősítés a fürt újrakonfigurálása][sap-ha-guide-figure-3027]

   _**38. ábra:** A fürt újrakonfigurálásának megerősítése_

A Windows feladatátvevő fürt sikeres telepítése után bizonyos küszöbértékek módosításait az Azure-beli körülményekhez igazítandó feladatátvételi észleléshez kell igazítani. A paramétereket meg kell változtatni dokumentálni [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)ebben a blogban: . Feltételezve, hogy az ASCS/SCS Windows fürtkonfigurációját létrehozó két virtuális gép ugyanabban az alhálózatban van, a következő paramétereket kell módosítani ezekre az értékekre:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Ezeket a beállításokat tesztelték az ügyfelekkel, és jó kompromisszumot biztosítottak ahhoz, hogy egyoldalon elég rugalmasak legyenek. Másrészt ezek a beállítások elegendő gyors feladatátvételt biztosítottak az SAP-szoftverek vagy csomópont/virtuális gép meghibásodása esetén. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SIOS DataKeeper Cluster Edition telepítése az SAP ASCS/SCS fürtmegosztási lemezre

Most már rendelkezik egy működő Windows Server feladatátvételi fürtözési konfigurációval az Azure-ban. De egy SAP ASCS/SCS-példány telepítéséhez egy megosztott lemezerőforrásra van szükség. Az Azure-ban nem hozhat létre a szükséges megosztott lemezerőforrásokat. A SIOS DataKeeper Cluster Edition egy külső gyártótól származó megoldás, amelynek segítségével megosztott lemezerőforrásokat hozhat létre.

A SIOS DataKeeper Cluster Edition telepítése az SAP ASCS/SCS fürtmegosztási lemezhez a következő feladatokat foglalja magában:

- A .NET Framework 3.5 hozzáadása
- A SIOS DataKeeper telepítése
- A SIOS DataKeeper beállítása

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>A .
A Microsoft . Mivel a SIOS DataKeeper megköveteli, hogy a .

A .

- Használja a Szerepkörök és szolgáltatások hozzáadása varázslót a Windows rendszerben a 39.

  ![39. ábra: A .NET Framework 3.5 telepítése a Szerepkörök és szolgáltatások hozzáadása varázslóval][sap-ha-guide-figure-3028]

  _**39. ábra:** A .NET Framework 3.5 telepítése a Szerepkörök és szolgáltatások hozzáadása varázslóval_

  ![40. ábra: A .][sap-ha-guide-figure-3029]

  _**40. ábra:** A ._

- Használja a dism.exe parancssori eszközt. Az ilyen típusú telepítéshez hozzá kell férnie a Windows telepítési adathordozójának SxS könyvtárához. A rendszergazda jogú parancssorba írja be a következőt:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper telepítése

Telepítse a SIOS DataKeeper Cluster Edition-t a fürt minden csomópontjára. Virtuális megosztott tároló létrehozásához a SIOS DataKeeper segítségével hozzon létre egy szinkronizált tükröt, majd szimulálja a fürt megosztott tárolóját.

A SIOS szoftver telepítése előtt hozza létre a **DataKeeperSvc tartományi felhasználót.**

> [!NOTE]
> Adja hozzá a **DataKeeperSvc** felhasználót a **helyi rendszergazda** csoporthoz mindkét fürtcsomóponton.
>
>

A SIOS DataKeeper telepítése:

1. Telepítse a SIOS-szoftvert mindkét fürtcsomópontra.

   ![SIOS telepítő][sap-ha-guide-figure-3030]

   ![41. ábra: A SIOS DataKeeper telepítésének első oldala][sap-ha-guide-figure-3031]

   _**41. ábra:** A SIOS DataKeeper telepítésének első oldala_

2. A **42.**

   ![42. ábra: A DataKeeper tájékoztatja, hogy egy szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

   _**42. ábra:** A DataKeeper tájékoztatja, hogy egy szolgáltatás le lesz tiltva_

3. A **43.**

   ![43. ábra: A SIOS DataKeeper felhasználói kiválasztása][sap-ha-guide-figure-3033]

   _**43. ábra:** A SIOS DataKeeper felhasználóválasztása_

4. Adja meg a SIOS DataKeeper számára létrehozott tartományi fiók felhasználónevét és jelszavait.

   ![44. ábra: Adja meg a SIOS DataKeeper telepítéséhez megadott tartomány-felhasználónevet és -jelszót][sap-ha-guide-figure-3034]

   _**44. ábra:** Adja meg a SIOS DataKeeper telepítéséhez megadott tartományfelhasználónevet és jelszót_

5. Telepítse a SIOS DataKeeper példány licenckulcsát a 45.

   ![45. ábra: Adja meg a SIOS DataKeeper licenckulcsát][sap-ha-guide-figure-3035]

   _**45. ábra:** Adja meg a SIOS DataKeeper licenckulcsát_

6. Amikor a rendszer kéri, indítsa újra a virtuális gépet.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>A SIOS DataKeeper beállítása

Miután mindkét csomópontra telepítette a SIOS DataKeeper-t, el kell indítania a konfigurációt. A konfiguráció célja, hogy az egyes virtuális gépekhez csatlakoztatott további virtuális adatközpontok közötti szinkron adatreplikáció legyen.

1. Indítsa el a DataKeeper Management and Configuration eszközt, majd válassza **a Kiszolgáló csatlakoztatása**lehetőséget. (A 46. ábrán ez a beállítás pirossal van bekarikázva.)

   ![46. ábra: A SIOS DataKeeper felügyeleti és konfigurációs eszköze][sap-ha-guide-figure-3036]

   _**46. ábra:** SIOS DataKeeper felügyeleti és konfigurációs eszköz_

2. Adja meg annak az első csomópontnak a nevét vagy TCP/IP-címét, amelyhez a Felügyeleti és konfiguráció eszköznek csatlakoznia kell, és egy második lépésben a második csomóponthoz.

   ![47. ábra: Adja meg annak az első csomópontnak a nevét vagy TCP/IP-címét, amelyhez a Felügyeleti és konfiguráció staféta a második csomóponthoz, a második csomóponthoz pedig a második][sap-ha-guide-figure-3037]

   _**47. ábra:** A Felügyeleti és konfigurációs eszköz első csomópontjának nevét vagy TCP/IP-címét szúrja be, és egy második lépésben a második csomópontot._

3. Hozza létre a replikációs feladatot a két csomópont között.

   ![48. ábra: Replikációs feladat létrehozása][sap-ha-guide-figure-3038]

   _**48. ábra:** Replikációs feladat létrehozása_

   A varázsló végigvezeti a replikációs feladat létrehozásának folyamatán.
4. Adja meg a forráscsomópont nevét, TCP/IP-címét és lemezkötetét.

   ![49. ábra: A replikációs feladat nevének meghatározása][sap-ha-guide-figure-3039]

   _**49. ábra:** A replikációs feladat nevének megadása_

   ![50. ábra: Adja meg a csomópont alapadatait, amely nek az aktuális forráscsomópontnak kell lennie.][sap-ha-guide-figure-3040]

   _**50. ábra:** Adja meg a csomópont alapadatait, amely nek az aktuális forráscsomópontnak kell lennie_

5. Adja meg a célcsomópont nevét, TCP/IP-címét és lemezkötetét.

   ![51. ábra: A csomópont alapadatainak meghatározása, amely nek az aktuális célcsomópontnak kell lennie][sap-ha-guide-figure-3041]

   _**51. ábra:** Adja meg a csomópont alapadatait, amely nek az aktuális célcsomópontnak kell lennie_

6. Adja meg a tömörítési algoritmusokat. A példában azt javasoljuk, hogy tömörítse a replikációs adatfolyamot. Különösen az újraszinkronizálási helyzetekben a replikációs adatfolyam tömörítése jelentősen csökkenti az újraszinkronizálási időt. Vegye figyelembe, hogy a tömörítés a virtuális gép PROCESSZOR- és RAM-erőforrásait használja. A tömörítési sebesség növekedésével a felhasznált CPU-erőforrások mennyisége is növekszik. Ezt a beállítást később is módosíthatja.

7. Egy másik beállítás, amelyet ellenőriznie kell, hogy a replikáció aszinkron vagy szinkron módon történik-e. *Az SAP ASCS/SCS-konfigurációk védelme kor szinkron replikációt kell használnia.*  

   ![52. ábra: Replikációs adatok meghatározása][sap-ha-guide-figure-3042]

   _**52. ábra:** Replikációs adatok megadása_

8. Adja meg, hogy a replikációs feladat által replikált kötetet megosztott lemezként kell-e ábrázolni a Windows Server feladatátvevő fürtkonfigurációjában. Az SAP ASCS/SCS konfiguráció esetében válassza az **Igen** lehetőséget, hogy a Windows-fürt a replikált kötetet megosztott lemezként lássa, amelyet fürtkötetként használhat.

   ![53. ábra: Válassza az Igen lehetőséget a replikált kötet fürtkötetként való beállításához][sap-ha-guide-figure-3043]

   _**53. ábra:** Válassza az **Igen** lehetőséget a replikált kötet fürtkötetként való beállításához_

   A kötet létrehozása után a DataKeeper-kezelés és konfiguráció eszköz azt mutatja, hogy a replikációs feladat aktív.

   ![54. ábra: A DataKeeper szinkron tükrözése az SAP ASCS/SCS megosztási lemezhez aktív][sap-ha-guide-figure-3044]

   _**54. ábra:** Az SAP ASCS/SCS megosztási lemez DataKeeper szinkron tükrözése aktív_

   A feladatátvevőfürt-kezelő mostantól datakeeper lemezként jeleníti meg a lemezt, ahogy az az 55.

   ![55. ábra: A feladatátvevőfürt-kezelő a DataKeeper által replikált lemezt jeleníti meg][sap-ha-guide-figure-3045]

   _**55. ábra:** A feladatátvevőfürt-kezelő a DataKeeper által replikált lemezt jeleníti meg_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Az SAP NetWeaver rendszer telepítése

Nem fogjuk leírni a DBMS beállítását, mert a beállítások a használt DBMS-rendszertől függően változnak. Azonban feltételezzük, hogy a magas rendelkezésre állású aggodalmak a DBMS foglalkozik a funkciókat a különböző DBMS-szállítók támogatja az Azure-ban. Például az SQL Server mindig bekapcsolva vagy adatbázis-tükrözése, valamint oracle-adatbázisokhoz készült Oracle Data Guard. Az ebben a cikkben használt forgatókönyvben nem adtunk hozzá további védelmet a DBMS-hez.

Nincsenek különleges szempontok, ha a különböző DBMS-szolgáltatások az azure-beli fürtözött SAP ASCS/SCS-konfigurációval kommunikálnak.

> [!NOTE]
> Az SAP NetWeaver ABAP rendszerek, Java rendszerek és ABAP+Java rendszerek telepítési eljárásai majdnem azonosak. A legjelentősebb különbség az, hogy egy SAP ABAP rendszer rendelkezik egy ASCS-példány. Az SAP Java rendszer egy SCS-példányt rendelkezik. Az SAP ABAP+Java rendszer egy ASCS-és egy SCS-példányt futtat ugyanabban a Microsoft feladatátvevő fürtcsoportban. Az egyes SAP NetWeaver telepítési veremtelepítési különbségek et kifejezetten megemlíti. Feltételezheti, hogy minden más rész ugyanaz.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Telepítse az SAP-t magas rendelkezésre állású ASCS/SCS-példányokkal

> [!IMPORTANT]
> Ügyeljen arra, hogy ne helyezze el a lapfájlt a DataKeeper tükrözött köteteken. A DataKeeper nem támogatja a tükrözött köteteket. A lapozófájlt az Azure virtuális gép D ideiglenes meghajtóján hagyhatja, amely az alapértelmezett. Ha még nincs ott, helyezze át a Windows-lapfájlt az Azure virtuális gép D-meghajtójára.
>
>

Az SAP telepítése magas rendelkezésre állású ASCS/SCS-példányokkal a következő feladatokat foglalja magában:

- Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányszámára
- Az SAP első fürtcsomópontjának telepítése
- Az ASCS/SCS-példány SAP-profiljának módosítása
- Mintavételi port hozzáadása
- A Windows tűzfal mintavételi portjának megnyitása

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz

1. A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést az ASCS/SCS-példány virtuális állomásnevéhez.

   > [!IMPORTANT]
   > Az ASCS/SCS-példány virtuális állomásnevéhez rendelt IP-címnek meg kell egyeznie az Azure Load Balancerhez rendelt IP-címmel (**<*SID*>-lb-ascs).**  
   >
   >

   A virtuális SAP ASCS/SCS állomásnév (**pr1-ascs-sap**) IP-címe megegyezik az Azure Load Balancer (**pr1-lb-ascs**) IP-címével.

   ![56. ábra: Az SAP ASCS/SCS fürt virtuális nevéhez és TCP/IP-címéhez tartozó DNS-bejegyzés megadása][sap-ha-guide-figure-3046]

   _**56. ábra:** Az SAP ASCS/SCS fürt virtuális nevének és A TCP/IP-címnek a DNS-bejegyzésének megadása_

2. A virtuális állomásnévhez rendelt IP-cím meghatározásához válassza a **DNS-kezelői** > **tartomány**lehetőséget.

   ![57. ábra: Új virtuális név és TCP/IP-cím az SAP ASCS/SCS fürtkonfigurációhoz][sap-ha-guide-figure-3047]

   _**57. ábra:** Új virtuális név és TCP/IP-cím az SAP ASCS/SCS fürtkonfigurációhoz_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Az SAP első fürtcsomópontjának telepítése

1. Az első fürtcsomópont-beállítás végrehajtása az A fürtcsomóponton. Például a **pr1-ascs-0** gazdagépen.
2. Az Azure belső terheléselosztó alapértelmezett portjainak megtartásához válassza a következőket:

   * **ABAP rendszer**: **00-as SZÁMÚ ASCS-példány** **00**
   * **Java rendszer**: **SCS** példány száma **01**
   * **ABAP+Java rendszer**: **00-as SZÁMÚ ASCS-példány** és **01-es** **00** **SCS-példányszám**

   Ha 00-tól eltérő példányszámokat szeretne használni az ABAP ASCS-példányhoz és a 01-hez a Java SCS-példányhoz, először módosítania kell az Azure belső terheléselosztó alapértelmezett terheléselosztási szabályait, amelyet [az AsCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztóhoz][sap-ha-guide-8.9]című részben ismertetett.

A következő néhány feladat nem ismertetésa a szabványos SAP telepítési dokumentációban.

> [!NOTE]
> Az SAP telepítési dokumentációja leírja, hogyan kell telepíteni az első ASCS/SCS fürtcsomópontot.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Az ASCS/SCS-példány SAP-profiljának módosítása

Új profilparamétert kell hozzáadnia. A profilparaméter megakadályozza az SAP munkafolyamatok és a várólistára helyezett kiszolgáló közötti kapcsolatok bezárását, ha túl sokáig üresjáratban vannak. Említettük a problémát az [SAP ASCS/SCS-példány mindkét fürtcsomópontján a rendszerleíró bejegyzések hozzáadása című részben.][sap-ha-guide-8.11] Ebben a szakaszban két módosítást is bevezettünk néhány alapvető TCP/IP kapcsolati paraméterben. A második lépésben be kell állítania a `keep_alive` várólistára helyezett kiszolgálót, hogy jelet küldjön, hogy a kapcsolatok ne érjék el az Azure belső terheléselosztó tétlen küszöbértékét.

Az ASCS/SCS-példány SAP-profiljának módosítása:

1. Adja hozzá ezt a profilparamétert az SAP ASCS/SCS-példányprofilhoz:

   ```
   enque/encni/set_so_keepalive = true
   ```
   A mi példánkban az út a következő:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Például az SAP SCS-példány profil és a megfelelő elérési út:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. A módosítások alkalmazásához indítsa újra az SAP ASCS /SCS-példányt.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Mintavételi port hozzáadása

A belső terheléselosztó mintavételi funkciójával a teljes fürtkonfiguráció együttműködik az Azure Load Balancer használatával. Az Azure belső terheléselosztó általában egyenlően osztja el a bejövő számítási feladatokat a részt vevő virtuális gépek között. Ez azonban egyes fürtkonfigurációkban nem működik, mert csak egy példány aktív. A másik példány passzív, és nem tudja elfogadni a számítási feladatok egyikét sem. A mintavételi funkció segít, ha az Azure belső terheléselosztó csak egy aktív példányhoz rendeli hozzá a munkát. A mintavételi funkcióval a belső terheléselosztó észleli, hogy mely példányok aktívak, és csak a számítási feladattal rendelkező példányt célozza meg.

Mintavételi port hozzáadása:

1. Ellenőrizze az aktuális **ProbePort-beállítást** a következő PowerShell-parancs futtatásával. A fürtkonfigurációegyik virtuális gépéről hajtsa végre.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Mintavételi port definiálása. A mintavételi port alapértelmezett száma **0**. Példánkban a **62000**szondaportot használjuk.

   ![58. ábra: A fürtkonfiguráció mintavételi portja alapértelmezés szerint 0.][sap-ha-guide-figure-3048]

   _**58. ábra:** A fürt konfigurációs mintavételi portjának alapértelmezett portja 0_

   A portszám az SAP Azure Resource Manager-sablonokban van definiálva. A portszámát a PowerShellben rendelheti hozzá.

   Új ProbePort-érték beállításához az **SAP <*SID*> IP-fürterőforráshoz,** futtassa a következő PowerShell-parancsfájlt. Frissítse a PowerShell-változókat a környezetben. A parancsfájl futtatása után a rendszer kéri, hogy indítsa újra az SAP-fürtcsoportot a módosítások aktiválásához.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Miután online állapotba hozta az **SAP <*SID-fürtszerepkört,* > ** ellenőrizze, hogy a **ProbePort** értéke az új értékre van-e beállítva.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![59. ábra: A fürtport vizsgálata az új érték beállítása után][sap-ha-guide-figure-3049]

   _**59. ábra:** A fürtport vizsgálata az új érték beállítása után_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>A Windows tűzfal mintavételi portjának megnyitása

Mindkét fürtcsomóponton meg kell nyitnia egy Windows tűzfal mintavételi portját. A következő parancsfájl segítségével nyissa meg a Windows tűzfal mintavételi portját. Frissítse a PowerShell-változókat a környezetben.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** **62000-re**van állítva. Most már más állomásokról, például **ascsha-dbas-ból**is elérheti a ** \\fájlmegosztást \ascsha-clsap\sapmnt.**

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Az adatbázispéldány telepítése

Az adatbázispéldány telepítéséhez kövesse az SAP telepítési dokumentációjában ismertetett eljárást.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>A második fürtcsomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatójának lépéseit.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Az SAP ERS Windows szolgáltatáspéldány indítási típusának módosítása

Módosítsa az SAP ERS Windows-szolgáltatás indítási típusát **automatikusra (késleltetett indítás)** mindkét fürtcsomóponton.

![60. ábra: Módosítsa az SAP ERS-példány szolgáltatástípusát késleltetett automatikusra][sap-ha-guide-figure-3050]

_**60. ábra:** Módosítsa az SAP ERS-példány szolgáltatástípusát késleltetett automatikusra_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Az SAP elsődleges alkalmazáskiszolgáló telepítése

Telepítse az elsődleges alkalmazáskiszolgáló (PAS) példányát <*SID*>-di-0 azonosítót azon a virtuális gépen, amelyet a PAS üzemeltetésére kijelölt. Nincsenek függőségek az Azure-tól vagy a DataKeeper-specifikus beállításoktól.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Az SAP additional Application Server telepítése

Telepítsen egy SAP additional Application Server (AAS) rendszert az összes olyan virtuális gépen, amelyet egy SAP Application Server-példány üzemeltetésére kijelölt. Például <*SID* SID->-di-1-en <*SID* &lt;>-di-n&gt;.

> [!NOTE]
> Ezzel befejezi a telepítést a magas rendelkezésre állású SAP NetWeaver rendszer. Ezután folytassa a feladatátvételi tesztelést.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Az SAP ASCS/SCS-példány feladatátvételének és a SIOS-replikációnak a tesztelése
Az SAP ASCS/SCS-példány feladatátvétele és a SIOS-lemezreplikáció tesztelése és figyelése a Feladatátvevő fürtkezelő és a SIOS DataKeeper Management and Configuration eszközzel könnyen ellenőrizhető és figyelhető.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Az SAP ASCS/SCS-példány az A fürtcsomóponton fut

Az **SAP PR1** fürtcsoport az A fürtcsomóponton fut. Például a **pr1-ascs-0.** Rendelje hozzá az **SAP PR1** fürtcsoport részét képező s-meghajtót az A fürtcsomóponthoz.

![61. ábra: Feladatátvevőfürt-kezelő: Az SAP <SID> fürtcsoport az A fürtcsomóponton fut][sap-ha-guide-figure-5000]

_**61. ábra:** Feladatátvevőfürt-kezelő: Az SAP <*SID*> fürtcsoport az A fürtcsomóponton fut_

A SIOS DataKeeper Management and Configuration eszközben láthatja, hogy a megosztott lemezadatok szinkron módon replikálódnak az A fürtsen lévő S forráskötet-meghajtóról a B fürtcsomópont S célkötet-meghajtójára. Például **a pr1-ascs-0 -ról [10.0.0.40]** **pr1-ascs-1 [10.0.0.41]** replikálódik.

![62. ábra: A SIOS DataKeeper alkalmazásban replikálja a helyi kötetet az A fürtcsomópontról a B fürtcsomópontra][sap-ha-guide-figure-5001]

_**62. ábra:** A SIOS DataKeeper alkalmazásban replikálja a helyi kötetet az A fürtcsomópontról a B fürtcsomópontra_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Feladatátvétel az A csomópontról a B csomópontba

1. Az sap-<*SID*> fürtcsoport feladatátvételének kezdeményezéséhez válasszon az Alábbi lehetőségek közül a B fürtcsomópontról:
   - Feladatátvevőfürt-kezelő használata  
   - Feladatátvevő fürt powershell használata

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Indítsa újra az A fürtcsomópontot a Windows vendég operációs rendszeren belül (ez elindítja az SAP <*SID*> fürtcsoport automatikus feladatátvételét az A csomópontról a B csomópontba).  
3. Indítsa újra az A fürtcsomópontot az Azure Portalról (ez elindítja az SAP <*SID*> fürtcsoport automatikus feladatátvételét az A csomópontról a B csomópontba).  
4. Indítsa újra az A fürtcsomópontot az Azure PowerShell használatával (ez elindítja az SAP <*SID*> fürtcsoport automatikus feladatátvételét az A csomópontról a B csomópontba).

   Feladatátvétel után az SAP <*SID*> fürtcsoport a B fürtcsomóponton fut. Például, ez fut **pr1-ascs-1**.

   ![63. ábra: A feladatátvevői fürtkezelőben az SAP <SID> fürtcsoport a B fürtcsomóponton fut][sap-ha-guide-figure-5002]

   _**63. ábra:** A feladatátvevőfürt-kezelőben az SAP <*SID*> fürtcsoport a B fürtcsomóponton fut_

   A megosztott lemez most már csatlakoztatva van a B fürtcsomópontra. Például **a pr1-ascs-1 [10.0.0.41]** **pr1-ascs-0 [10.0.0.40]** értékre replikál.

   ![64. ábra: A SIOS DataKeeper replikálja a helyi kötetet a B fürtcsomópontról az A fürtcsomópontra][sap-ha-guide-figure-5003]

   _**64. ábra:** A SIOS DataKeeper replikálja a helyi kötetet a B fürtcsomópontról az A fürtcsomópontra_
