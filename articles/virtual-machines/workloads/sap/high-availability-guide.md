---
title: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver |} A Microsoft Docs
description: Magas rendelkezésre állású útmutató az SAP NetWeaver az Azure Virtual machines szolgáltatásban
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: goraco
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c6dddbdbc781869ef6a3c1a0a707eeb83941b92a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239321"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Magas rendelkezésre állás az SAP NetWeaver az Azure virtuális gépekhez

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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

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
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP több biztonsági AZONOSÍTÓVAL magas rendelkezésre állású konfiguráció)


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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Az SAP-termék rendelkezésre állási mátrix)
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/resource-group-overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager
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


Azure-beli virtuális gépek a megoldás olyan szervezeteknek, amelyek a szükséges számítási, tárolási és hálózati erőforrásokat, minimális és hosszadalmas beszerzési ciklusokat nélkül is. Használhatja az Azure Virtual Machines klasszikus alkalmazások, például ABAP SAP NetWeaver-alapú, a Java és a egy ABAP + Java-verem üzembe helyezése. Kiterjesztheti a megbízhatósággal és rendelkezésre állással, a helyszíni erőforrások nélkül. Azure-beli virtuális gépek közötti kapcsolatot, támogatja, így az Azure virtuális gépek integrálása a szervezet helyszíni tartományok, a magánfelhők és az SAP-rendszer rendszeren.

Ez a cikk ismerteti a lépéseket, amelyeket az Azure-ban magas rendelkezésre állású SAP-rendszerek telepítése az Azure Resource Manager-alapú üzemi modell használatával. Részletesen fő feladatok:

* Keresse meg a megfelelő SAP-megjegyzések és telepítési útmutatóit szerepel a [erőforrások] [ sap-ha-guide-2] szakaszban. Ez a cikk kiegészíti az SAP-dokumentáció, és SAP-megjegyzések, amelyek az elsődleges erőforrásokat, amelyek segítségével telepítse, és üzembe helyezése az SAP-szoftvereket az adott platformon.
* Ismerje meg az Azure Resource Manager-alapú üzemi modell és a klasszikus Azure üzemi modell közötti különbségekről.
* Ismerje meg a Windows Server feladatátvételi fürtszolgáltatási kvórummódok, ezért kiválaszthatja a megfelelő az Azure-alapú modell.
* További információ az Azure-szolgáltatások a Windows Server feladatátvételi fürtszolgáltatási megosztott tároló.
* Ismerje meg, hogyan védheti meg egyetlen – pont-az-hibát összetevői például fejlett üzleti alkalmazások fejlesztői (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) és az adatbázis-kezelő rendszerek (DBMS) és a redundáns összetevők, például az SAP-alkalmazások Kiszolgáló, az Azure-ban.
* Egy részletes példa-telepítés és konfiguráció egy magas rendelkezésre állású SAP-rendszerhez egy Windows Server feladatátvételi fürtszolgáltatási fürt az Azure-ban, kövesse az Azure Resource Manager használatával.
* További információ a Windows Server feladatátvételi fürtszolgáltatási amelyhez nem szükséges egy helyszíni környezetben, de az Azure használatához szükséges további lépéseket.

Központi telepítése és konfigurálása, ez a cikk egyszerűsítése érdekében használjuk az SAP háromrétegű magas rendelkezésre állású Resource Manager-sablonok. A sablonok egy magas rendelkezésre állású SAP-rendszerhez szükséges a teljes infrastruktúra üzembe helyezésének automatizálása. Az infrastruktúra támogatja a SAP alkalmazás teljesítményének Standard (SAP) méretezés az SAP-rendszer is.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Előfeltételek
A Kezdés előtt győződjön meg arról, hogy megfelel-e az előfeltételeknek, amely a következő szakaszok ismertetik. Emellett mindenképpen ellenőrizze a felsorolt összes erőforrást a [erőforrások] [ sap-ha-guide-2] szakaszban.

Ez a cikk az Azure Resource Manager-sablonokkal használjuk [háromrétegű SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Sablonok hasznos áttekintéséért lásd: [SAP az Azure Resource Manager-sablonok](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Erőforrások
Ezek a cikkek az Azure-beli SAP-környezetekhez terjed ki:

* [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver][planning-guide]
* [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver (Ez az útmutató)][sap-ha-guide]

> [!NOTE]
> Amikor csak lehetséges, is biztosítunk egy hivatkozást a hivatkozó SAP telepítési útmutatójában (lásd a [SAP telepítési útmutatóinak][sap-installation-guides]). Az előfeltételeket és a telepítési folyamatra vonatkozó információkat célszerű figyelmesen olvassa el az SAP NetWeaver telepítési útmutatóit. Ez a cikk ismerteti, amelyet használhat az Azure Virtual Machines, az SAP NetWeaver-alapú rendszerek csak meghatározott feladatok.
>
>

A témakör az Azure-beli SAP kapcsolatos alábbi SAP-megjegyzések:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-on: támogatott termékek és méretezése |
| [2015553] |A Microsoft Azure-beli SAP: támogatás előfeltételei |
| [1999351] |Továbbfejlesztett SAP az Azure Monitoring |
| [2178632] |Metrikák figyelése a Microsoft Azure-beli SAP-kulcs |
| [1999351] |A Windows virtualizálási: Enhanced Monitoring |
| [2243692] |SAP DBMS-példány használatát az Azure prémium szintű SSD-tárolóval |

Tudjon meg többet a [korlátozások az Azure-előfizetések][azure-subscription-service-limits-subscription], beleértve az általános alapértelmezett korlátozások és a maximális korlátozások.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Magas rendelkezésre állás az SAP az Azure Resource Managerrel és a klasszikus Azure üzemi modell
Az Azure Resource Manager és klasszikus Azure üzemi modell a következő területek különböznek meg:

- Erőforráscsoportok
- Azure belső load balancer függőség az Azure-erőforráscsoportot
- SAP több biztonsági AZONOSÍTÓVAL forgatókönyvek támogatása

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Erőforráscsoportok
Az Azure Resource Manager erőforráscsoportokat használhatja az alkalmazás-erőforrások kezelése az Azure-előfizetésében. Az integrált módszert egy erőforráscsoportba tartozó összes erőforrásnak rendelkeznie kell az azonos életciklus. Például egyszerre létrehozott összes erőforrást, és a egy időben törlés. További információk az [erőforráscsoportokról](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure belső load balancer függőség az Azure-erőforráscsoportot

A klasszikus Azure üzemi modellben az Azure belső terheléselosztó (az Azure Load Balancer szolgáltatás) és a felhőalapú szolgáltatás csoportja közötti függőség van. Minden egyes belső load balancer egy felhőalapú szolgáltatás csoportot igényel.

Az Azure Resource Managerben, nem kell egy Azure-erőforráscsoportot az Azure Load Balancer használatát. A környezet nem egyszerűbb és rugalmasabb.

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP több biztonsági AZONOSÍTÓVAL forgatókönyvek támogatása

Az Azure Resource Managerben, több példányt is telepíthet SAP rendszer azonosítója (SID) ASCS/SCS egy fürtben. Több SID-vel példányok miatt egyes Azure belső terheléselosztó több IP-címek támogatása is lehetséges.

A klasszikus Azure üzemi modellt használja, kövesse az ismertetett eljárások [SAP NetWeaver az Azure-ban: a Windows Server feladatátvételi fürtszolgáltatás használatával az Azure-ban az SIOS DataKeeper SAP ASCS/SCS fürtszolgáltatási példányok](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Javasoljuk, hogy az SAP-telepítések az Azure Resource Manager üzemi modell használata. Ez lehetővé teszi számos előnnyel jár, amelyek nem érhetők el a klasszikus üzemi modellben. További tudnivalók az Azure [üzembe helyezési modellek][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatási az alapja egy magas rendelkezésre állású SAP ASCS/SCS telepítés és a Windows DBMS.

Egy feladatátvevő fürt egy 1 + n különálló kiszolgálók csoportja, (csomópontok), amelyek együttműködése magasabb az alkalmazások és szolgáltatások rendelkezésre állását. Egy csomópont meghibásodása esetén a Windows Server feladatátvételi fürtszolgáltatási számítja ki, amely akkor fordulhat elő, miközben fenntartja az alkalmazások és szolgáltatások megfelelő fürt hibák száma. A Feladatátvételi fürtszolgáltatás eléréséhez különböző kvórummódok közül választhat.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Kvórummódok
A Windows Server feladatátvételi fürtszolgáltatás használata esetén négy kvórummódok közül választhat:

* **Csomóponttöbbség**. A fürt minden csomópontján is szavazhat. A fürt működik csak a szavazatot, többségét, a több mint fele a szavazatokat. Azt javasoljuk, hogy ezt a lehetőséget olyan fürtök, amelyek rendelkeznek a csomópontok páratlan számú. Például egy hét csomópontos fürtben három csomópont visszaadhatja a feladatokat, és a fürt stills éri el a nagyobb részét, és továbbra is fusson.  
* **Csomópont- és lemeztöbbség**. Minden egyes csomópont és a egy kijelölt lemez a fürttároló, (a tanúsító lemez) mikor érhetők el, és a kommunikáció is szavazhat. A fürt működik a szavazatok többsége csak az azt jelenti, a több mint fele a szavazatokat. Ebben a módban van értelme páros szám a csomópontok fürtözött környezetben. Ha a csomópontok fele és a lemez online állapotban, a fürt kifogástalan állapotban marad.
* **Csomópont- és fájlmegosztás legtöbb**. Minden egyes csomópontra és a egy kijelölt fájlmegosztás (tanúsító fájlmegosztás) a rendszergazda által létrehozott értékelheti a hozzászólást, függetlenül attól, hogy a csomópontok és a fájlmegosztás érhetők el, és a kommunikáció. A fürt működik a szavazatok többsége csak az azt jelenti, a több mint fele a szavazatokat. Ebben a módban van értelme páros szám a csomópontok fürtözött környezetben. Hasonló, a csomópont- és lemeztöbbség módot, de a tanúsító fájlmegosztás helyett egy tanúsító lemezt használ. Ebben a módban könnyen megvalósítható, de ha a fájlmegosztás maga nem magas rendelkezésre állású, válhat a hibaérzékeny pont.
* **Nincs többség: Csak lemez**. A fürt kvóruma rendelkezik, ha egy csomópont elérhető, és a egy adott lemez a fürttároló folytatott kommunikáció. Csak a csomópontok is, hogy a lemez folytatott kommunikáció csatlakozhassanak a fürthöz. Azt javasoljuk, hogy nem használja ezt a módot.

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server feladatátvételi fürtszolgáltatás a helyszíni
1. ábra mutatja a két csomópontból álló fürtben. Ha a csomópontok meghiúsul, és egyaránt csomópontok vesznek fel, és fut, egy kvórumlemez, vagy a fájl közötti hálózati kapcsolat meghatározza, hogy melyik csomópont továbbra is biztosítja a fürt alkalmazásokat és szolgáltatásokat. A csomópont, amely a kvórum lemez vagy fájlmegosztás hozzáféréssel rendelkezik az a csomópont, biztosítja, hogy a szolgáltatások továbbra is.

Mivel ez a példa egy két csomópontos fürt használja, a csomópont- és fájlmegosztástöbbség kvórummód használjuk. A csomópont- és lemeztöbbség is érvényes. Éles környezetben azt javasoljuk, hogy egy kvórumlemez használja. Hálózati és tárolási rendszer technológia segítségével magas rendelkezésre állásúvá tenni.

![1. ábra: Példa egy Windows Server feladatátvételi fürtszolgáltatás konfigurációs az SAP ASCS/SCS az Azure-ban][sap-ha-guide-figure-1000]

_**1. ábra:** egy Windows Server feladatátvételi fürtszolgáltatás konfigurációs az SAP ASCS/SCS az Azure-ban – példa_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Megosztott tároló
1. ábra is mutatja egy két csomópontos megosztott tárolófürt. Egy helyi megosztott tároló fürtben a fürt összes csomópontja észleli a megosztott tároló. Zárolási mechanizmus sérülés elleni védi az adatokat. Minden csomópont képes észlelni, ha egy másik csomópont meghibásodik. Ha egy csomópont meghibásodik, a megmaradó csomópontra tulajdonjogot a tárolási erőforrások és szolgáltatások rendelkezésre állását biztosítja.

> [!NOTE]
> Az SQL Server nincs szükség megosztott lemezeinek magas rendelkezésre állás az egyes adatbázis-kezelő alkalmazások, például. SQL Server Always On replikálja a helyi lemez egy másik fürtcsomópontra, a helyi lemez egy fürtcsomópont adathoz és naplófájlhoz az adatbázis-kezelő. Ebben az esetben a Windows-fürt konfigurációját nem kell egy megosztott lemez.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Hálózat és a névfeloldás
Ügyfélszámítógépek a fürt virtuális IP-címet és a egy virtuális állomás neve, a DNS-kiszolgáló által biztosított keresztül érhető el. A helyszíni csomópontok és a DNS-kiszolgáló képes kezelni a több IP-címet.

A hagyományos telepítés két vagy több hálózati kapcsolatot használhatja:

* A storage, dedikált kapcsolat
* A szívverés egy fürt belső hálózati kapcsolatot
* Egy nyilvános hálózatot használó ügyfelek számára a fürthöz való csatlakozáshoz

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> A Windows Server feladatátvevő fürt az Azure-ban
Operációs rendszer nélküli vagy magánfelhő-telepítések képest, Azure Virtual Machines konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. Ha egy megosztott fürtlemezre hoz létre, több IP-címek és virtuális állomásnevek az SAP ASCS/SCS példányhoz be kell.

Ebben a cikkben bemutatjuk, főbb fogalmakat és a egy SAP central services magas rendelkezésre állású fürt az Azure-ban létrehozásához szükséges további lépéseket. Bemutatjuk, hogyan állítható be a külső eszköz az SIOS DataKeeper, és az Azure belső terheléselosztó konfigurálása. Ezek az eszközök segítségével a Windows feladatátvevő fürt létrehozása a tanúsító fájlmegosztás az Azure-ban.

![2. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**2. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Az SIOS DataKeeper megosztott lemezt az Azure-ban
A magas rendelkezésre állású SAP ASCS/SCS példányhoz megosztott tárolót kell fürt. 2016 szeptemberétől Azure nem biztosít, amelyek segítségével hozzon létre egy megosztott tárolófürt megosztott tárolót. Harmadik féltől származó szoftverek az SIOS DataKeeper Cluster Edition használatával hozzon létre egy tükrözött tárfiókot, amely szimulálja a fürt megosztott tárolójába. Az SIOS megoldás biztosítja a valós idejű szinkron adatreplikációt. Ez a fürt megosztott erőforrás létrehozásának módját:

1. Egy másik Azure virtuális merevlemezt (VHD) csatolása a virtuális gépek (VM) minden egyes Windows fürtözött konfigurációban.
2. Futtassa az SIOS DataKeeper Cluster Edition mindkét virtuális gép csomópontját.
3. Állítsa az SIOS DataKeeper Cluster Edition, hogy azt tükrözi, hogy a tartalom a további csatlakoztatott virtuális merevlemez kötet a forrás virtuális gép a cél virtuális gép további csatlakoztatott virtuális merevlemez mennyisége. Az SIOS DataKeeper kivonatolja a forrás- és helyi köteteken, és ezután jeleníti meg őket a Windows Server feladatátvételi fürtszolgáltatás egy megosztott lemez.

További információk [az SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![3. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban az SIOS DataKeeper][sap-ha-guide-figure-1002]

_**3. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban az SIOS DataKeeper_

> [!NOTE]
> Magas rendelkezésre állás az egyes DBMS-termékek, például az SQL Server már nincs szüksége megosztott lemezeket. SQL Server Always On replikálja a helyi lemez egy másik fürtcsomópontra, a helyi lemez egy fürtcsomópont adathoz és naplófájlhoz az adatbázis-kezelő. Ebben az esetben a Windows-fürt konfigurációját nem kell egy megosztott lemez.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> A névfeloldás az Azure-ban
Az Azure felhőalapú platformján nem teszi lehetővé a virtuális IP-címek, például a nem fix IP-címek konfigurálásához. Alternatív megoldásként elérni a fürt erőforrásai a felhőben egy virtuális IP-cím beállításához szüksége lesz.
Az Azure belső terheléselosztó az Azure Load Balancer szolgáltatás rendelkezik. A belső terheléselosztó az ügyfelek a fürt a fürt virtuális IP-címen keresztül érhető el.
Az erőforráscsoport, amely tartalmazza a fürtcsomópontok belső terheléselosztóinak kell. Ezt követően konfigurálja a portokat a belső terheléselosztó továbbítási szabályok a mintavétel az összes szükséges port.
Az ügyfelek csatlakozhatnak-n keresztül a virtuális gazdagép nevét. A DNS-kiszolgáló szünteti meg a fürt IP-címét, és a belső load balancer kezeli port továbbítja a fürt aktív csomópontja.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver az Azure infrastruktúra--szolgáltatásként (IaaS) a magas rendelkezésre állás
SAP alkalmazás magas rendelkezésre állás, mint például a SAP software-összetevők a következő összetevők védelmére kell:

* SAP alkalmazáskiszolgáló-példány
* Az SAP ASCS/SCS példányhoz
* Az adatbázis-kezelő kiszolgáló

Az SAP-összetevők magas rendelkezésre állású forgatókönyvekben védelméről további információ: [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára](planning-guide.md).

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Magas rendelkezésre állású SAP-alkalmazáskiszolgáló
Általában nincs szükség egy adott magas rendelkezésre állású megoldás az SAP-alkalmazáskiszolgáló és a párbeszédpanel-példányokhoz. Magas rendelkezésre állás, a redundancia, és a különböző példányok az Azure Virtual Machines alkalmazáspéldányok párbeszédpanelen konfigurálja. Legalább két SAP alkalmazáspéldányok két példányt az Azure Virtual Machines telepítve kell lennie.

![4. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**4. ábra:** magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

SAP-alkalmazáskiszolgáló példányait üzemeltetni az azonos Azure rendelkezésre állási beállítása minden virtuális gépnek kell elhelyeznie. Az Azure rendelkezésre állási csoport biztosítja, hogy:

* Az összes virtuális gép az azonos frissítési tartomány részét képezik. A frissítési tartomány, például gondoskodik arról, hogy a virtuális gépek tervezett karbantartás leállások során egy időben nem frissítenek.
* Az összes virtuális gép az azonos tartalék tartomány részét képezik. Tartalék tartomány, például gondoskodik arról, hogy a virtuális gépek, hogy nem rendszerkritikus meghibásodási pontot hatással van az összes virtuális gépek rendelkezésre állásának vannak-e telepítve.

Ismerje meg, hogyan [virtuális gépek rendelkezésre állásának kezelése][virtual-machines-manage-availability].

Mivel az Azure storage-fiók egy potenciálisan hibaérzékeny pont, fontos legalább két Azure storage-fiókok, amelyben oszlanak meg legalább két virtuális gép rendelkezik. Egy ideális beállítás minden SAP párbeszédpanel példányát futtató virtuális gép lemezei a egy másik tárfiók üzembe kívánja helyezni.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Magas rendelkezésre állású SAP ASCS/SCS példányhoz
5. ábra egy példát egy magas rendelkezésre állású SAP ASCS/SCS példányhoz.

![5. ábra: Magas rendelkezésre állású SAP ASCS/SCS példányhoz][sap-ha-guide-figure-2001]

_**5. ábra:** magas rendelkezésre állású SAP ASCS/SCS példányhoz_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Az SAP ASCS/SCS példányhoz magas rendelkezésre állást biztosít a Windows Server feladatátvételi fürtszolgáltatási az Azure-ban
Operációs rendszer nélküli vagy magánfelhő-telepítések képest, Azure Virtual Machines konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. Egy Windows feladatátvevő fürtöt létrehozni, szüksége egy megosztott fürtlemezre, több IP-címek, több virtuális állomásnevek és az Azure belső terheléselosztó a fürtszolgáltatás egy SAP ASCS/SCS példányhoz. Bemutatjuk, ez a cikk későbbi részében részletesebben.

![6. ábra: A Windows Server feladatátvételi fürtszolgáltatási az SIOS DataKeeper használatával az Azure-beli SAP ASCS/SCS konfigurációhoz][sap-ha-guide-figure-1002]

_**6. ábra:** Windows Server feladatátvételi fürtszolgáltatási az Azure-ban az SIOS DataKeeper SAP ASCS/SCS konfigurációhoz_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Magas rendelkezésre állású DBMS-példány
Az adatbázis-kezelő egy SAP-rendszerrel is szerepel egy egyetlen kapcsolattartási pontja. Kell egy magas rendelkezésre állású megoldás megvédheti azokat. 7. ábra bemutatja egy SQL Server Always On magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatási és az Azure belső terheléselosztó. SQL Server Always On replikálja a saját adatbázis-kezelő replikációs adathoz és naplófájlhoz az adatbázis-kezelő. Ebben az esetben, nem kell a fürt megosztott lemezzel, ami leegyszerűsíti az összes beállítást.

![7. ábra: Az SQL Server Always On a magas rendelkezésre állású SAP DBMS – példa][sap-ha-guide-figure-2003]

_**7. ábra:** az SQL Server Always On a magas rendelkezésre állású SAP DBMS – példa_

Az Azure Resource Manager-alapú üzemi modell használatával az Azure-beli SQL Server fürtszolgáltatással kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Always On rendelkezésre állási csoport konfigurálása Azure Virtual Machines szolgáltatásban manuálisan Resource Manager használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Always On rendelkezésre állási csoporthoz az Azure belső terheléselosztó konfigurálása az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Végpontok közötti magas rendelkezésre állású központi telepítési forgatókönyvei

### <a name="deployment-scenario-using-architectural-template-1"></a>Üzembe helyezési forgatókönyv architekturális sablon 1 használata

8. ábra szemlélteti, az SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **egy** SAP-rendszerhez. Ebben a forgatókönyvben be van állítva a következő:

- Az SAP ASCS/SCS példányhoz egy dedikált fürt használható.
- A DBMS-példány egy dedikált fürt használható.
- SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépek vannak üzembe helyezve.

![8. ábra: SAP magas rendelkezésre állású architektúra sablon 1, dedikált fürttel ASCS/SCS-és adatbázis-kezelő][sap-ha-guide-figure-2004]

_**8. ábra:** architekturális sablon 1 magas rendelkezésre állású, SAP ASCS/SCS-és adatbázis-kezelő dedikált fürtök_

### <a name="deployment-scenario-using-architectural-template-2"></a>Üzembe helyezési forgatókönyv architekturális sablon 2 használatával

9. ábra szemlélteti, az SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **egy** SAP-rendszerhez. Ebben a forgatókönyvben be van állítva a következő:

- Egy dedikált fürt használt **mindkét** az SAP ASCS/SCS-példány és az adatbázis-kezelő.
- SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépek vannak üzembe helyezve.

![9. ábra: A magas rendelkezésre állású architektúra sablon 2, egy dedikált ASCS/SCS-fürtöt és a egy dedikált adatbázis-kezelő fürt SAP][sap-ha-guide-figure-2005]

_**9. ábra:** SAP magas rendelkezésre állású architektúra sablon 2, egy dedikált ASCS/SCS-fürtöt és a egy dedikált adatbázis-kezelő fürt_

### <a name="deployment-scenario-using-architectural-template-3"></a>Üzembe helyezési forgatókönyv a 3. a sablon architekturális.

10. ábrán látható egy példa az SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **két** rendszerek, SAP &lt;SID1&gt; és &lt;SID2&gt;. Ebben a forgatókönyvben be van állítva a következő:

- Egy dedikált fürt használt **mindkét** az SAP ASCS/SCS SID1 példány *és* az SAP ASCS/SCS SID2-példány (egy fürtben).
- Egy dedikált fürt DBMS SID1 szolgál, és egy másik dedikált fürtöt szolgál az adatbázis-kezelő SID2 (két fürtben).
- SAP alkalmazáskiszolgáló-példányok az SAP-rendszer SID1 rendelkezik a saját dedikált virtuális gépeken.
- SAP alkalmazáskiszolgáló-példányok az SAP-rendszer SID2 rendelkezik a saját dedikált virtuális gépeken.

![10. ábra: SAP magas rendelkezésre állású architektúra sablon 3, egy másik ASCS/SCS-példányok dedikált fürttel][sap-ha-guide-figure-6003]

_**10. ábra:** SAP magas rendelkezésre állású architektúra sablon 3, egy másik ASCS/SCS-példányok dedikált fürttel_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Az infrastruktúra előkészítése

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az 1. a sablon architekturális.
Az SAP az Azure Resource Manager-sablonok segítségével egyszerűsítheti az üzembe helyezést, a szükséges erőforrásokat.

A háromrétegű sablonok az Azure Resource Manager is támogatja a magas rendelkezésre állású forgatókönyvek, például architekturális sablon 1, amely két fürttel rendelkezik. Minden egyes fürt egy SAP rendszerkritikus meghibásodási pontot SAP ASCS/SCS-és adatbázis-kezelő rendszer.

Itt látható, ahol is igénybe az Azure Resource Manager-sablonok az ebben a cikkben ismertetünk. példa:

* [Az Azure Marketplace-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Az infrastruktúra előkészítése architekturális sablon 1:

- Az Azure Portalon az a **paraméterek** panelen, a a **SYSTEMAVAILABILITY** jelölje ki **magas rendelkezésre ÁLLÁSÚ**.

  ![11. ábra: Állítsa be SAP magas rendelkezésre állású Azure Resource Manager-paraméterek][sap-ha-guide-figure-3000]

_**11. ábra:** beállítása SAP magas rendelkezésre állású Azure Resource Manager-paraméterek_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP alkalmazáskiszolgáló virtuális gépek: <*SAPSystemSID*> - di - <*száma*>
    * A fürt virtuális gépek ASCS/SCS: <*SAPSystemSID*> – ascs - <*száma*>
    * Az adatbázis-kezelő fürt: <*SAPSystemSID*> - db - <*száma*>

  * **Hálózati kártya minden virtuális gép társított IP-címekkel rendelkező**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Az Azure storage-fiókok**

  * **Rendelkezésre állási csoportok** számára:
    * SAP alkalmazáskiszolgáló virtuális gépek: <*SAPSystemSID*> - avset - di
    * Az SAP ASCS/SCS virtuális gépek fürtözése: <*SAPSystemSID*> - avset – ascs
    * Az adatbázis-kezelő fürt virtuális gépek: <*SAPSystemSID*> - avset - adatbázis

  * **Az Azure belső terheléselosztó**:
    * Az ASCS/SCS példányhoz és IP-cím minden porttal <*SAPSystemSID*> - lb - ascs
    * Az SQL Server adatbázis-kezelő és az IP-cím minden porttal <*SAPSystemSID*> - lb - adatbázis

  * **Hálózati biztonsági csoport**: <*SAPSystemSID*> - nsg - ascs-0  
    * Egy megnyitott külső távoli asztal protokoll (RDP) port, a <*SAPSystemSID*> virtuális gép – ascs - 0

> [!NOTE]
> A hálózati kártyák és az Azure belső terheléselosztók összes IP-címek **dinamikus** alapértelmezés szerint. Módosítsa őket **statikus** IP-címeket. A cikk későbbi részében ehhez ismertetünk.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> A vállalati hálózati kapcsolattal (létesítmények közötti) az éles környezetben használt virtuális gépek üzembe helyezése
Az SAP-rendszereit, üzembe helyezése az Azure-beli virtuális gépek [vállalati hálózati kapcsolat (létesítmények közötti)] [ planning-guide-2.2] az Azure Site-to-Site VPN- vagy Azure ExpressRoute használatával.

> [!NOTE]
> Az Azure Virtual Network-példányt is használhat. A virtuális hálózat és alhálózat már létrehozott és előkészített.
>
>

1.  Az Azure Portalon az a **paraméterek** panelen, a a **NEWOREXISTINGSUBNET** jelölje ki **meglévő**.
2.  Az a **SUBNETID** adjon hozzá a teljes karakterláncot, az előkészített Azure-hálózatok SubnetID, ha azt tervezi, hogy az Azure-beli virtuális gépek üzembe helyezése.
3.  Az összes Azure-beli hálózati alhálózatok listájának lekéréséhez futtassa a következő PowerShell-paranccsal:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  A **azonosító** mezőt jeleníti meg a **SUBNETID**.
4. Az összes listába **SUBNETID** értékeket, futtassa a következő PowerShell-parancsot:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  A **SUBNETID** kell kinéznie:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> A tesztelési és bemutató csak felhőalapú SAP-példányok üzembe helyezése
A csak felhőalapú üzembe helyezési modell magas rendelkezésre állású SAP-rendszerrel is telepítheti. Az ilyen típusú központi telepítés elsősorban hasznos bemutató és tesztelés a használati esetek. Éles környezeti alkalmazási helyzetek esetén nem használhatók.

- Az Azure Portalon az a **paraméterek** panelen, a a **NEWOREXISTINGSUBNET** jelölje ki **új**. Hagyja a **SUBNETID** mezője üres.

  Az SAP az Azure Resource Manager-sablon automatikusan létrehozza az Azure virtuális hálózatot és alhálózatot.

> [!NOTE]
> Akkor is szükség van legalább egy dedikált virtuális gép Active Directory és DNS Azure Virtual Network ugyanezen példányában. A sablon nem hoz létre ezekre a virtuális gépekre.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>2. a sablon architekturális. az infrastruktúra előkészítése

Az SAP az Azure Resource Manager-sablon segítségével egyszerűsítheti az üzembe helyezést a szükséges infrastruktúra-erőforrások SAP architekturális sablon 2.

Itt látható, ahol megkapja a az Azure Resource Manager-sablonok a központi telepítési forgatókönyv:

* [Az Azure Marketplace-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>3. a sablon architekturális. az infrastruktúra előkészítése

Az infrastruktúra előkészítése, és konfigurálja az SAP **több biztonsági AZONOSÍTÓVAL**. Például hozzáadhat egy további, az SAP ASCS/SCS-példányt egy *meglévő* fürtkonfiguráció. További információkért lásd: [konfigurálása egy további SAP ASCS/SCS példányhoz be egy meglévő fürtkonfigurációhoz egy SAP több biztonsági AZONOSÍTÓVAL konfiguráció létrehozása az Azure Resource Manager][sap-ha-multi-sid-guide].

Ha több biztonsági AZONOSÍTÓVAL fürtöt létrehozni, használhatja a több biztonsági AZONOSÍTÓVAL [gyorsindítási sablonok github](https://github.com/Azure/azure-quickstart-templates).
Hozzon létre egy új több biztonsági AZONOSÍTÓVAL fürtöt, meg kell a következő három sablonok üzembe helyezése:

* [ASCS/SCS-sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Alkalmazássablon-kiszolgálók](#application-servers-template)

Az alábbi szakaszok a sablonok és a paraméterek, meg kell adnia a sablonok részletesebb információkat tartalmaz.

#### <a name="ASCS-SCS-template"></a> ASCS/SCS-sablon

Az ASCS/SCS-sablon üzembe helyezi a két virtuális gépet, amelyek segítségével több ASCS/SCS-példányt üzemeltető Windows Server feladatátvevő fürt létrehozása.

Az ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon, a beállításához a [ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon][sap-templates-3-tier-multisid-xscs-marketplace-image], adja meg az értékeket a következő paramétereket:

  - **Erőforrás-előtag**.  Állítsa be az erőforrás-előtagja, amelynek az üzembe helyezés során létrehozott összes erőforrást előtagot használja. Az erőforrások csak egy SAP-rendszerhez tartozik, mert az előtag, az erőforrás nem áll a SID-egy SAP-rendszerhez.  Az előtag közé kell esnie **3-6 karakter**.
  - **Írja be a verem**. Válassza ki a verem az SAP-rendszerhez. A stack típusától függően az Azure Load Balancerhez tartozik (ABAP és Java csak) egy vagy két (ABAP + Java) magánhálózati IP-címek száma SAP-rendszerhez.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **Az SAP-rendszer száma**. Válassza ki a fürt telepíteni szeretné az SAP-rendszerek számát.
  -  **Rendszer rendelkezésre állását**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**. Hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Új vagy meglévő alhálózaton**. Állítsa be, hogy egy új virtuális hálózatot és alhálózatot kell létrehozni, vagy egy létező alhálózatot kell használnia. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
  -  **Alhálózati azonosító**. Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/ <*előfizetés-azonosító*> /resourceGroups/ <*erőforráscsoport-név*> /providers/Microsoft.Network/virtualNetworks/ < *virtuális hálózat neve*> /subnets/ <*alhálózat neve*>

A sablon üzembe helyez egy Azure Load Balancer példányt, amely támogatja a több SAP-rendszereinket.

- Az ASCS példányok úgy vannak konfigurálva, a példány számát 00, 10, 20...
- Az SCS-példányok száma 01, 11., 21 konfigurált...
- Az ASCS sorba replikációs kiszolgáló (SSZON) (csak Linux) példányok úgy vannak konfigurálva, a példány a 02, 12, 22-es szám...
- Az SCS SSZON (csak Linux) példányok úgy vannak konfigurálva, a példányok száma 03., 13., 23...

A load balancer tartalmaz (2. Linux esetén) 1 VIP(s), ASCS/SCS virtuális 1 x IP és 1 x VIP SSZON (csak Linux) számára.

Az alábbi lista tartalmazza az összes terheléselosztási szabályok (ahol x a az SAP-rendszerhez, például 1, 2, 3... száma):
- Minden SAP rendszere Windows-specifikus portokat: 445-ös, 5985
- ASCS portok (x0 száma): 32 x 0, 36 x 0, 39 x 0, 81-es x 0, 5 x 013, 5 x 014, 5 x 016
- SCS portok (x1 száma): 32 x 1, 33 x 1, 39 x 1, 81-es x 1, 5 x 113, 5 x 114, 5 x 116
- Linux (példányszámának x2) portok ASCS SSZON: 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Linux (példányszámának x3) portok SCS SSZON: 33 x 3, 5 x 313, 5 x 314, 5 x 316

A terheléselosztó a következő mintavételi portokon (Ha x az a szám az SAP-rendszer, például 1, 2, 3...) használatára van konfigurálva:
- ASCS/SCS belső load balancer mintavételi port: 620 x 0
- SSZON belső load balancer mintavételi portot (csak Linux): 621 x 2

#### <a name="database-template"></a> Adatbázis-sablon

Az adatbázis-sablon üzembe helyez egy vagy két virtuális gépek, amelyek egy SAP-rendszer relációsadatbázis-kezelő rendszerének (RDBMS) telepítéséhez használhatja. Például ha telepít egy ASCS/SCS-sablon az öt SAP-rendszereit, szüksége ötször a sablon üzembe helyezésére.

A beállítása az adatbázis több biztonsági AZONOSÍTÓVAL sablon, a [adatbázis több biztonsági AZONOSÍTÓVAL sablon][sap-templates-3-tier-multisid-db-marketplace-image], adja meg az értékeket az alábbi paraméterekkel:

  -  **Rendszer-azonosító SAP**. Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások használható előtagjaként.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **DbType**. Válassza ki az adatbázist a fürtön telepíteni kívánt típusát. Válassza ki **SQL** Ha Microsoft SQL Server telepítéséhez. Válassza ki **HANA** Ha azt tervezi, hogy az SAP HANA telepítése a virtuális gépeken. Ügyeljen arra, hogy válassza ki a megfelelő operációs rendszer típusa: válassza ki **Windows** SQL és a Hana Linux-disztribúciók válassza. Az Azure Load Balancer virtuális gépekhez csatlakoztatott lesz konfigurálva a kijelölt adatbázis típusát támogatja:
    * **SQL**. A load balancer terheléselosztás 1433-as porton lesz. Ellenőrizze, hogy ezt a portot használja az SQL Server Always On beállítás.
    * **HANA**. A load balancer fog terheléselosztás 35015 és 35017 portot. Mindenképpen telepítse az SAP HANA-példányok számát **50**.
    A load balancer mintavételi port 62550 fogja használni.
  -  **SAP-rendszer mérete**. Az új rendszer biztosít SAP számának megadása. Ha nem arra van szükség a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  -  **Rendszer rendelkezésre állását**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**. Hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Alhálózati azonosító**. Adja meg az alhálózatot, amelyet a ASCS/SCS-sablon üzembe helyezése során használt azonosítója vagy az alhálózatot, amelyet létrejött azonosítója a ASCS/SCS sablon központi telepítésének részeként.

#### <a name="application-servers-template"></a> Alkalmazássablon-kiszolgálók

Az alkalmazássablon kiszolgálók SAP-alkalmazáskiszolgáló-példányok egy SAP-rendszerhez használható két vagy több virtuális gépeket helyez üzembe. Például ha telepít egy ASCS/SCS-sablon az öt SAP-rendszereit, szüksége ötször a sablon üzembe helyezésére.

A beállítása az kiszolgálók több biztonsági AZONOSÍTÓVAL sablon, a [alkalmazássablon kiszolgálók több biztonsági AZONOSÍTÓVAL][sap-templates-3-tier-multisid-apps-marketplace-image], adja meg az értékeket a következő paramétereket:

  -  **Rendszer-azonosító SAP**. Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások használható előtagjaként.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **SAP-rendszer mérete**. Az új rendszer biztosít SAP száma. Ha nem arra van szükség a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  -  **Rendszer rendelkezésre állását**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**. Hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Alhálózati azonosító**. Adja meg az alhálózatot, amelyet a ASCS/SCS-sablon üzembe helyezése során használt azonosítója vagy az alhálózatot, amelyet létrejött azonosítója a ASCS/SCS sablon központi telepítésének részeként.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Az Azure virtual network
Ebben a példában az Azure virtuális hálózat címtartománya 10.0.0.0/16. Van egy nevű alhálózat **alhálózati**, a 10.0.0.0/24 címtartományt. A virtuális hálózatban lévő virtuális gépek és a belső terheléselosztók vannak telepítve.

> [!IMPORTANT]
> A vendég operációs rendszeren belül a hálózati beállításokat nem kell semmit módosítania. Ez magában foglalja az IP-címek, DNS-kiszolgálók és alhálózat. A hálózati beállítások konfigurálása az Azure-ban. A Dynamic Host Configuration Protocol (DHCP) szolgáltatás tölti ki a beállításokat.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP-címek

Állítsa be a szükséges DNS-IP-címek, hajtsa végre a következő lépéseket.

1.  Az Azure Portalon az a **DNS-kiszolgálók** panelen ellenőrizze, hogy a virtuális hálózat **DNS-kiszolgálók** beállítás **egyéni DNS**.
2.  Válassza ki a beállításokat, rendelkezik hálózat típusa alapján. További információkért lásd a következőket:
    * [Vállalati hálózati kapcsolat (létesítmények közötti)][planning-guide-2.2]: Adja hozzá a helyi DNS-kiszolgálók IP-címét.  
    Kiterjesztheti a helyszíni DNS-kiszolgálók Azure-ban futó virtuális gépekhez. Ebben az esetben a DNS-szolgáltatást futtató Azure virtuális gépek IP-címek is hozzáadhat.
    * [Kizárólag felhőalapú üzembe helyezés][planning-guide-2.1]: a virtuális hálózat példányt, amely egy DNS-kiszolgálót egy további virtuális gép üzembe helyezése. Adja hozzá az Azure virtuális gépeket, amelyek a DNS-szolgáltatás futtatásához beállította az IP-címét.

    ![12. ábra: Azure virtuális hálózat DNS-kiszolgálók konfigurálása][sap-ha-guide-figure-3001]

    _**12. ábra:** DNS konfigurálása Azure Virtual Network kiszolgálók_

  > [!NOTE]
  > Ha módosítja a DNS-kiszolgálók IP-címek, meg kell indítania az Azure virtuális gépeket, a módosítás alkalmazása és az új DNS-kiszolgálók propagálása.
  >
  >

Ebben a példában a DNS-szolgáltatás telepítve és konfigurálva van a Windows virtuális gépeken:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Állomásnév és az SAP ASCS/SCS fürtözött példányát és az adatbázis-kezelő fürtözött példány statikus IP-címek

A helyszíni telepítéshez szüksége ezen fenntartott neveket és IP-címek:

| Virtuális állomás neve szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| Az SAP ASCS/SCS első fürt virtuális host name (kezelő) |pr1-ascs-vir |10.0.0.42 |
| Az SAP ASCS/SCS példányhoz virtuális állomás neve |PR1 – ascs-sap |10.0.0.43 |
| Az SAP DBMS második fürt virtuális állomás neve (kezelő) |pr1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor meg létrehozni a virtuális állomásnevek **pr1 – ascs-vir** és **pr1 – dbms-vir** és kezelheti a fürthöz társított IP-címeket. Ennek módjáról további információkért lásd: [fürtcsomópontok fürtkonfiguráció gyűjtése][sap-ha-guide-8.12.1].

A másik két virtuális állomásnevek, manuálisan is létrehozhat **pr1 – ascs-sap** és **pr1 – dbms-sap**, és a társított IP-címek, a DNS-kiszolgálón. A fürtözött SAP ASCS/SCS-példány és a fürtözött adatbázis-kezelő példány használja ezeket az erőforrásokat. Ennek módjáról további információkért lásd: [hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Állítsa be a statikus IP-címeket, a SAP virtual Machines
A virtuális gépek használata a fürt üzembe helyezése, után minden virtuális gép statikus IP-címeket be kell. Ehhez az Azure Virtual Network konfigurációban, nem pedig a vendég operációs rendszer.

1.  Az Azure Portalon válassza ki a **erőforráscsoport** > **hálózati kártya** > **beállítások** > **IP-cím**.
2.  Az a **IP-címek** panel alatt **hozzárendelés**válassza **statikus**. Az a **IP-cím** mezőbe írja be a használni kívánt IP-címet.

  > [!NOTE]
  > Ha módosítja a hálózati kártya IP-címét, indítsa újra az Azure virtuális gépeket, a módosítás alkalmazására van szükség.  
  >
  >

  ![13. ábra: A statikus IP-címeket a hálózati kártya minden virtuális gép beállítása][sap-ha-guide-figure-3002]

  _**13. ábra:** statikus IP-címeket a hálózati kártya minden virtuális gép beállítása_

  Ismételje meg ezt a lépést, amely van, minden virtuális gép, beleértve a virtuális gépeket, amelyek az Active Directory és DNS szolgáltatás használni kívánt összes hálózati adapter.

Ebben a példában rendelkezünk ezen virtuális gépek és a statikus IP-címek:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP-alkalmazáskiszolgáló-példány |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Második SAP-alkalmazáskiszolgáló-példány |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP-alkalmazáskiszolgáló-példány |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Első fürtcsomópontra ASCS/SCS példányhoz |PR1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Második fürtcsomópontra ASCS/SCS példányhoz |PR1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS-példányra az első fürtcsomópontra |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Második fürtcsomópontra DBMS-példány |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Az Azure belső terheléselosztó statikus IP-cím beállítása

Az SAP az Azure Resource Manager-sablont hoz létre az Azure belső terheléselosztó az SAP ASCS/SCS példányhoz és az adatbázis-kezelő fürtön használt.

> [!IMPORTANT]
> Az IP-címe az SAP ASCS/SCS virtuális állomás neve megegyezik az SAP ASCS/SCS belső terheléselosztó IP-címe: **pr1-lb-ascs**.
> A DBMS neve virtuális IP-címe megegyezik a DBMS belső terheléselosztó IP-címe: **pr1-lb-dbms**.
>
>

Az Azure belső terheléselosztó statikus IP-címet beállítani:

1.  Az első üzembe helyezés beállítja a belső terheléselosztó IP-címe **dinamikus**. Az Azure Portalon az a **IP-címek** panel alatt **hozzárendelés**, jelölje be **statikus**.
2.  A belső terheléselosztó IP-cím beállítva **pr1-lb-ascs** az SAP ASCS/SCS-példány állomásneve, IP-címet.
3.  A belső terheléselosztó IP-cím beállítva **pr1-lb-dbms** virtuális állomás nevét a DBMS-példány IP-címet.

  ![14. ábra: Állítsa be a statikus IP-címeket, a belső terheléselosztó az SAP ASCS/SCS példányhoz][sap-ha-guide-figure-3003]

  _**14. ábra:** statikus IP-címek beállítása a belső terheléselosztó az SAP ASCS/SCS példányhoz_

Ebben a példában van két Azure belső terheléselosztó, amely a statikus IP-címet:

| Az Azure belső terheléselosztói szerepkör | Az Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| Az SAP ASCS/SCS példány belső terheléselosztót |PR1-lb-ascs |10.0.0.43 |
| Az SAP DBMS belső load balancer |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó

Az SAP az Azure Resource Manager-sablont hoz létre a szükséges portokat:
* ABAP ASCS példányok, az alapértelmezett példányszámának **00**
* A Java SCS példányhoz, az alapértelmezett példány számmal **01**

Az SAP ASCS/SCS példányhoz telepítésekor kell használnia az alapértelmezett példányszámának **00** az ABAP ASCS-példány és az alapértelmezett példányszámának **01** a Java SCS példányhoz.

Ezután hozza létre a szükséges belső terheléselosztási végpont esetében az SAP NetWeaver-portokat.

Szükséges belső terheléselosztási végpont létrehozásához először hozza létre a terheléselosztási végpontok a SAP NetWeaver ABAP ASCS portok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (ASCS példány példányszámának 00) (10 SSZON) |
| --- | --- | --- |
| Sorba Server / *lbrule3200* |32 <*példányszám*> |3200 |
| ABAP Üzenetkiszolgáló / *lbrule3600* |36 <*példányszám*> |3600 |
| Belső ABAP üzenet / *lbrule3900* |39 <*példányszám*> |3900 |
| A kiszolgáló HTTP-üzenet / *Lbrule8100* |81-es <*példányszám*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5 <*példányszám*> 13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5 <*példányszám*> 14 |50014 |
| Sorba replikációs / *Lbrule50016* |5 <*példányszám*> 16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5 <*példányszám*> 13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5 <*példányszám*> 14 |51014 |
| Erőforrás-kezelő Win *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**1. táblázat:** portszámot az SAP NetWeaver ABAP ASCS példányok_

Ezután hozzon létre a terheléselosztási végpontok a SAP NetWeaver Java SCS portok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (SCS példányhoz az példányszámának 01) (SSZON-11) |
| --- | --- | --- |
| Sorba Server / *lbrule3201* |32 <*példányszám*> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33 <*példányszám*> |3301 |
| Java Message kiszolgáló / *lbrule3900* |39 <*példányszám*> |3901 |
| Message Server HTTP / *Lbrule8101* |81-es <*példányszám*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5 <*példányszám*> 13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5 <*példányszám*> 14 |50114 |
| Sorba replikációs / *Lbrule50116* |5 <*példányszám*> 16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5 <*példányszám*> 13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5 <*példányszám*> 14 |51114 |
| Erőforrás-kezelő Win *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**2. táblázat:** portszámot az SAP NetWeaver Java SCS-példányok_

![15. ábra: Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó][sap-ha-guide-figure-3004]

_**15. ábra:** alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó_

A terheléselosztó IP-cím beállítva **pr1-lb-dbms** virtuális állomás nevét a DBMS-példány IP-címet.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Az ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása

Ha azt szeretné, az SAP ASCS vagy SCS példányokhoz használandó eltérő számú, módosítania kell neveket és értékeket a portok alapértelmezett értékek közül.

1.  Az Azure Portalon válassza ki a  **< *SID*> - lb - ascs terheléselosztó** > **terheléselosztási szabályok betöltése**.
2.  Az összes terheléselosztási szabályok, amelyek az SAP ASCS vagy SCS példányhoz tartozik módosítsa ezeket az értékeket:

  * Name (Név)
  * Port
  * Háttér-port

  Például ha szeretné módosítani az alapértelmezett ASCS példányszámának 00 31-ig, meg kell hajtsa végre a módosításokat az összes port az 1.

  Íme egy példa egy frissítést, a port *lbrule3200*.

  ![16. ábra: Módosítsa a ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó][sap-ha-guide-figure-3005]

  _**16. ábra:** ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows virtuális gépek hozzáadása a tartományhoz

Miután statikus IP-címet rendel a virtuális gépek, virtuális gépeket adni a tartományhoz.

![17. ábra: Virtuális gép hozzáadása egy tartományhoz][sap-ha-guide-figure-3006]

_**17. ábra:** hozzáadása egy virtuális gépet egy tartományhoz_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adja hozzá a beállításjegyzék-bejegyzések az SAP ASCS/SCS-példányának mindkét fürtcsomóponton

Az Azure Load Balancer belső terheléselosztót, hogy bezárul kapcsolatok, ha a megadott kapcsolatok üresjárati idő (üresjárati időkorlátot) rendelkezik. A párbeszédpanelen példányok nyitott kapcsolatok az SAP sorbaállítása SAP munkafolyamatok feldolgozni, amint az első sorba/eltávolítása a sorból kérelem kell küldeni. Ezek a kapcsolatok általában marad megalapozott a work folyamat, vagy sorba folyamata újraindul. Azonban ha a kapcsolat egy meghatározott ideig tétlen, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mivel az SAP rendellenesnek akkor újra létrehozza a kapcsolatot a sorba folyamatot, ha már nem létezik. Ezeket a tevékenységeket a fejlesztői nyomkövetések SAP folyamatok vannak dokumentálva, de ezeket a nyomkövetéseket a felesleges tartalmat nagy mennyiségű hoznak létre. Módosíthatja a TCP/IP-érdemes `KeepAliveTime` és `KeepAliveInterval` mindkét fürtcsomóponton. Ezeket a módosításokat a TCP/IP-paraméterek SAP profil paraméterek, a cikk későbbi részében leírt össze.

Az SAP ASCS/SCS-példányának mindkét fürtcsomóponton beállításjegyzék-bejegyzések hozzáadásához először adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASCS/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| A változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció mutató hivatkozás |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**3. táblázat:** módosítsa az első TCP/IP-paraméter_

Ezután adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASCS/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| A változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció mutató hivatkozás |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**4. táblázat:** módosítsa a második TCP/IP-paraméter_

**A módosítások életbe léptetéséhez indítsa újra a mindkét fürtcsomópont**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Fürt létrehozása a Windows Server feladatátvételi fürtszolgáltatási egy SAP ASCS/SCS példányhoz

Az SAP ASCS/SCS példányhoz egy Windows Server feladatátvételi fürtszolgáltatási fürt beállítása magában foglalja ezeket a feladatokat:

- Fürtözött konfigurációban a fürt csomópontjai gyűjtése
- A fürt tanúsító fájlmegosztás beállítása

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Fürtözött konfigurációban a fürt csomópontjai gyűjtése

1.  A szerepkör hozzáadása és szolgáltatások varázsló adja hozzá a Feladatátvételi fürtszolgáltatást mindkét fürtcsomóponton.
2.  Állítsa be a feladatátvevő fürt Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelőben válassza **-fürt létrehozása**, majd adja hozzá a csak az első fürt,. a csomópont neve A második csomópont ne adjon hozzá még; a második csomópont egy későbbi lépésben fogja hozzáadni.

  ![18. ábra: A kiszolgáló vagy virtuális gép nevére az első fürtcsomópont hozzáadása][sap-ha-guide-figure-3007]

  _**18. ábra:** adja hozzá az első fürtcsomópontra a kiszolgáló vagy virtuális gép neve_

3.  Adja meg a fürt hálózati neve (virtuális állomás neve).

  ![19. ábra: Adja meg a fürt neve][sap-ha-guide-figure-3008]

  _**19. ábra:** adja meg a fürt neve_

4.  Miután létrehozta a fürthöz, futtassa a fürtellenőrzési tesztet.

  ![20. ábra: A fürt-ellenőrzés futtatása][sap-ha-guide-figure-3009]

  _**20. ábra:** a fürt-ellenőrzés futtatása_

  A folyamat ezen a ponton lemezekkel kapcsolatos figyelmeztetéseket figyelmen kívül hagyhatja. Egy tanúsító fájlmegosztást és az SIOS megosztott lemezeket később fogja hozzáadni. Ebben a szakaszban nem kell foglalkoznia a kvóruma.

  ![21. ábra: Nincs kvórum lemez található][sap-ha-guide-figure-3010]

  _**21. ábra:** nincs kvórum lemez található_

  ![22. ábra: Alapvető fürterőforrás kell új IP-cím][sap-ha-guide-figure-3011]

  _**22. ábra:** Core fürterőforrás kell új IP-cím_

5.  Módosítsa a core fürtszolgáltatás IP-címét. A fürt nem indítható el, amíg nem módosítja a core fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címét a virtuálisgép-csomópontok egyikét mutat. Ehhez a **tulajdonságok** lapján a core fürtszolgáltatás IP-erőforrást.

  Például kell hozzárendelni az IP-cím (ebben a példában **10.0.0.42**) a fürt virtuális állomás neve **pr1 – ascs-vir**.

  ![23. ábra: A Tulajdonságok párbeszédpanelen módosítsa az IP-cím][sap-ha-guide-figure-3012]

  _**23. ábra:** a a **tulajdonságok** párbeszédpanelen módosítsa az IP-cím_

  ![24. ábra: A fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

  _**24. ábra:** a fürt számára fenntartott IP-cím hozzárendelése_

6.  A fürt virtuális állomás neve online állapotba.

  ![25. ábra: Fürt core szolgáltatás működik és fut, és a megfelelő IP-cím][sap-ha-guide-figure-3014]

  _**25. ábra:** core szolgáltatás működik és fut, és a megfelelő IP-cím_

7.  Adja hozzá a második fürtcsomópontra.

  Most, hogy a core fürtszolgáltatás helyezheti üzembe, a második fürtcsomópontra is hozzáadhat.

  ![26. ábra: A második fürtcsomópont hozzáadása][sap-ha-guide-figure-3015]

  _**26. ábra:** adja hozzá a második fürtcsomópontra_

8.  Adja meg a második fürt csomópont állomás nevét.

  ![27. ábra: Adja meg a második fürt állomásneve][sap-ha-guide-figure-3016]

  _**27. ábra:** adja meg a második fürt állomásneve_

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a **minden megfelelő tároló felvétele a fürtbe** jelölőnégyzet **nem** kiválasztott.  
  >
  >

  ![28. ábra: Nem jelöli be a jelölőnégyzetet][sap-ha-guide-figure-3017]

  _**28. ábra:** tegye **nem** jelölje be a jelölőnégyzetet_

  Kvórum és a lemezek kapcsolatos figyelmeztetést figyelmen kívül hagyhatja. Fog a kvórum beállítása és a lemezt később, megoszthatja a leírtak szerint [telepítése az SIOS DataKeeper Cluster Edition a SAP ASCS/SCS fürtlemez-megosztás][sap-ha-guide-8.12.3].

  ![29. ábra: A lemez kvórumával kapcsolatos figyelmeztetések mellőzése][sap-ha-guide-figure-3018]

  _**29. ábra:** a lemez kvórumával kapcsolatos figyelmeztetések mellőzése_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> A fürt tanúsító fájlmegosztás beállítása

Ezeket a feladatokat a fürt tanúsító fájlmegosztás konfigurálása foglalja magában:

- Fájlmegosztás létrehozása
- A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelő

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Fájlmegosztás létrehozása

1.  Válassza ki a tanúsító fájlmegosztás helyett egy kvórumlemez. Az SIOS DataKeeper támogatja ezt a lehetőséget.

  A cikkben szereplő példák a tanúsító fájlmegosztás van, az Azure-ban fut-e az Active Directory és DNS-kiszolgálón. Tanúsító fájlmegosztás neve **domcontr-0**. Mivel lenne konfigurálva az Azure (Site-to-Site VPN- vagy Azure ExpressRoute) VPN-kapcsolat, az Active Directory és DNS szolgáltatás a helyszíni, és nem megfelelő futtatni egy tanúsító ossza meg.

  > [!NOTE]
  > Ha az Active Directory és DNS-szolgáltatás csak a helyszínen fut, az Active Directory és DNS Windows operációs rendszeren, amelyen fut a helyi ne konfigurálja a tanúsító fájlmegosztás. Előfordulhat, hogy az Azure és az Active Directory és a DNS a helyszínen futó fürtcsomópontok közötti hálózati késés túl nagy, és a kapcsolódási problémák miatt. Győződjön meg arról, tanúsító fájlmegosztás beállítása egy Azure virtuális gépen, amelyen fut a fürtcsomópont közelében.  
  >
  >

  A kvórum meghajtón legalább 1024 MB szabad terület van szüksége. Azt javasoljuk, hogy a 2048 MB-nyi szabad terület a kvórum.

2.  Adja hozzá a fürtnévobjektum.

  ![30. ábra: Rendelje hozzá a megosztást a fürtnévobjektum engedélyei][sap-ha-guide-figure-3019]

  _**30. ábra:** engedélyei a megosztást a fürtnévobjektum hozzárendelése_

  Győződjön meg, hogy az engedélyek tartalmazzák-e a szolgáltató módosítása a fürtobjektum nevének a megosztás adataihoz (a példánkban a **pr1 – ascs-vir$**).

3.  Válassza ki a fürtnévobjektum hozzáadása a listához, **Hozzáadás**. Módosítsa a szűrőt, hogy ellenőrizze a számítógép-objektumok 31. ábra szereplő termékektől mellett.

  ![31. ábra: Változás az Objektumtípusok közé tartoznak a számítógépek][sap-ha-guide-figure-3020]

  _**31. ábra:** közé tartoznak a számítógépek az objektumtípusok módosításához_

  ![32. ábra: Válassza ki a számítógép négyzet jelölését.][sap-ha-guide-figure-3021]

  _**32. ábra:** válassza ki a **számítógépek** jelölőnégyzetet_

4.  Írja be a fürt nevét, ahogyan a 31. ábra. A bejegyzést már létre van hozva, mert módosíthatja az engedélyeket, 30. ábrán látható módon.

5.  Válassza ki a **biztonsági** lapján a megosztást, és állítsunk be részletesebb a fürtnévobjektum engedélyeit.

  ![33. ábra: A fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása][sap-ha-guide-figure-3022]

  _**33. ábra:** a fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelő

1.  Nyissa meg a fürtkvórum beállítása varázsló konfigurálja.

  ![34. ábra: A konfigurálás fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

  _**34. ábra:** a konfigurálás fürtkvórum beállítása varázsló indítása_

2.  Az a **kvórumkonfiguráció kiválasztása** lapra, jelölje be **a kvórum tanúsítójának kijelölése**.

  ![35. ábra: Kvórumkonfigurációkkal közül választhat][sap-ha-guide-figure-3024]

  _**35. ábra:** kvórumkonfigurációkkal közül választhat_

3.  Az a **kvórum Tanúsítójának kijelölése** lapra, jelölje be **konfigurálja egy tanúsító fájlmegosztást**.

  ![36. ábra: Válassza ki a tanúsító fájlmegosztás][sap-ha-guide-figure-3025]

  _**36. ábra:** tanúsító fájlmegosztás kiválasztása_

4.  Adja meg a fájlmegosztás UNC elérési útját (a példánkban a \\domcontr-0\FSW). A módosításokat végezhet listájának megtekintéséhez válasszon **tovább**.

  ![37. ábra: A tanúsító fájlmegosztás a fájlmegosztás helyét határozza meg.][sap-ha-guide-figure-3026]

  _**37. ábra:** határozza meg a fájlmegosztás helyét a tanúsító fájlmegosztás_

5.  Válassza ki a módosításokat, majd válassza ki **tovább**. Sikeresen konfigurálja újra a fürt konfigurációját, 38. ábrán látható módon kell.  

  ![38. ábra: Jóváhagyás, hogy a fürt már újra konfigurálni][sap-ha-guide-figure-3027]

  _**38. ábra:** , hogy a fürt már újra konfigurálni megerősítése_

A Windows feladatátvevő fürt sikeres telepítés után módosításokat kell tenni bizonyos küszöbértékek igazíthatja a feladatátvétel az észlelési feltételek az Azure-ban. Módosítani a paraméterei dokumentálva vannak ebben a blogbejegyzésben: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Feltételezve, hogy a két virtuális gépet hozhat létre a Windows-fürt konfigurációját ASCS/SCS ugyanazon az alhálózaton találhatók, a következő paramétereket kell módosítani ezeket az értékeket:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Ezek a beállítások az ügyfelekkel való tesztelését és egy jó biztonsági sérülés elég rugalmas az egyik oldalon megadott. Másrészről ezeket a beállításokat is biztosít gyors elegendő valós hibaállapotok-feladatátvétel az SAP-szoftver vagy csomópontot vagy Virtuálisgép-hiba. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Telepítse az SIOS DataKeeper Cluster Edition esetében az SAP ASCS/SCS fürtlemez-megosztás

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban. De, egy SAP ASCS/SCS példányhoz telepítéséhez, egy megosztott lemez erőforrást kell. Nem hozható létre a szükséges megosztott erőforrásokat az Azure-ban. Az SIOS DataKeeper Cluster Edition egy olyan külső megoldás, megosztott erőforrások létrehozására használhatja.

Ezek a feladatok telepítése az SIOS DataKeeper Cluster Edition a SAP ASCS/SCS fürtlemez-megosztás foglalja magában:

- A .NET-keretrendszer 3.5-ös hozzáadása
- Az SIOS DataKeeper telepítése
- Az SIOS DataKeeper beállítása

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adja hozzá a .NET-keretrendszer 3.5.
A Microsoft .NET-keretrendszer 3.5-ös verzióját nem automatikusan aktiválva, vagy a Windows Server 2012 R2 rendszeren telepített. Az SIOS DataKeeper használatához a .NET-keretrendszer kell az összes csomóponton, amelyet telepíteni DataKeeper, telepítenie kell a .NET-keretrendszer 3.5 a fürt összes virtuális gép vendég operációs rendszeren.

A .NET-keretrendszer 3.5 hozzáadandó két módja van:

- Használja a szerepkörök hozzáadása és a szolgáltatások varázsló Windows 39. ábrán látható módon.

  ![39. ábra: A .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és a szolgáltatások varázsló használatával][sap-ha-guide-figure-3028]

  _**39. ábra:** a .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és a szolgáltatások varázsló használatával_

  ![40. ábra: Telepítési folyamatjelző, amikor telepíti a .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével][sap-ha-guide-figure-3029]

  _**40. ábra:** telepítési folyamatjelző, amikor telepíti a .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével_

- A parancssori eszköz a dism.exe használata szükséges. Az ilyen típusú telepítés kell a a Windows-telepítési adathordozón lévő SxS könyvtár eléréséhez. Egy rendszergazda jogú parancssort írja be:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Telepítse az SIOS DataKeeper

Telepítse az SIOS DataKeeper Cluster Edition a fürt egyes csomópontjaihoz. Az SIOS DataKeeper virtuális megosztott tároló létrehozásához, hozzon létre egy szinkronizált tükrözött, és ezután szimulálja a fürt megosztott tárolójába.

Mielőtt a SIOS szoftver telepítése, létre kell hoznia a tartományi felhasználó **DataKeeperSvc**.

> [!NOTE]
> Adja hozzá a **DataKeeperSvc** felhasználót, hogy a **helyi rendszergazdai** csoport mindkét fürtcsomóponton.
>
>

Az SIOS DataKeeper telepítése:

1.  Telepítse az SIOS szoftvert mindkét fürtcsomóponton.

  ![Az SIOS telepítő][sap-ha-guide-figure-3030]

  ![41. ábra: Az SIOS DataKeeper telepítési első oldalán][sap-ha-guide-figure-3031]

  _**41. ábra:** az SIOS DataKeeper telepítés első oldal_

2.  A 42. ábra megjelenő párbeszédpanelen jelölje ki a **Igen**.

  ![42. ábra: DataKeeper figyelmeztet, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

  _**42. ábra:** DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3.  A párbeszédpanelen látható 43. ábra, azt javasoljuk, hogy bejelölte **tartomány vagy a kiszolgáló fiók**.

  ![43. ábra: Az SIOS DataKeeper felhasználó kiválasztása][sap-ha-guide-figure-3033]

  _**43. ábra:** az SIOS DataKeeper felhasználó kiválasztása_

4.  Adja meg a tartományi fiók felhasználói nevét és az SIOS DataKeeper létrehozott jelszavakat.

  ![44. ábra: Adja meg a tartomány felhasználónév és jelszó az SIOS DataKeeper telepítéséhez][sap-ha-guide-figure-3034]

  _**44. ábra:** az SIOS DataKeeper telepítését adja meg a tartomány felhasználónév és jelszó_

5.  Telepítse az SIOS DataKeeper példány a licenckulcs, 45. ábrán látható módon.

  ![45. ábra: Adja meg az SIOS DataKeeper licenckulcs][sap-ha-guide-figure-3035]

  _**45. ábra:** adja meg az SIOS DataKeeper licenckulcs_

6.  Amikor a rendszer kéri, indítsa újra a virtuális gépet.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Az SIOS DataKeeper beállítása

Miután telepítette az SIOS DataKeeper mindkét csomóponton, indítsa el a konfigurációs kell. A konfiguráció az a célja, hogy van csatolva a virtuális gépek mindegyike további virtuális merevlemezeknek közötti szinkron adatreplikációt.

1.  Indítsa el a DataKeeper felügyeleti és a konfigurációs eszközt, és válassza ki **Kapcsolódás kiszolgálóhoz**. (A 46. ábra ezt a beállítást a pirossal bekarikázva.)

  ![46. ábra: Az SIOS DataKeeper felügyeleti és a konfigurációs eszköz][sap-ha-guide-figure-3036]

  _**46. ábra:** az SIOS DataKeeper felügyeleti és a konfigurációs eszköz_

2.  Adja meg a nevét vagy a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakozni az első fürtcsomópont TCP/IP-címét.

  ![47. ábra: Helyezze be a nevét, vagy az első felügyeleti csomópont TCP/IP-címét és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakoznia][sap-ha-guide-figure-3037]

  _**47. ábra:** helyezze be a nevét vagy a TCP/IP-címét a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakozni az első fürtcsomópont_

3.  Hozzon létre a a két csomópont közötti replikálás feladatot.

  ![48. ábra: Replikáció feladat létrehozása][sap-ha-guide-figure-3038]

  _**48. ábra:** replikációs feladat létrehozása_

  A varázsló végigvezeti egy replikációs feladat létrehozásának folyamatán.
4.  Adja meg a nevét, a TCP/IP-cím és a forráscsomóponton lemezek mennyisége.

  ![49. ábra: Határozza meg a replikációs feladat nevét][sap-ha-guide-figure-3039]

  _**49. ábra:** határozza meg a replikációs feladat nevét_

  ![50. ábra: Az alapszintű adatok kell lennie az aktuális forráscsomópont csomópont meghatározása][sap-ha-guide-figure-3040]

  _**50. ábra:** a csomópont, amely lehet a jelenlegi forráscsomópont alapadatok megadása_

5.  Adja meg a nevét, a TCP/IP-cím és a cél uzlu lemezkötetet.

  ![51. ábra: A csomópont, amely lehet a jelenlegi célcsomópont alap adatok meghatározásához][sap-ha-guide-figure-3041]

  _**51. ábra:** a csomópont, amely lehet a jelenlegi célcsomópont alapadatok megadása_

6.  A tömörítési algoritmust határozza meg. Ebben a példában azt javasoljuk, hogy a tömörítés a replikációs adatfolyam. Különösen abban az esetben az újraszinkronizálás a tömörítés a replikációs Stream jelentősen csökkenti az újraszinkronizálás idő. Vegye figyelembe, hogy a tömörítés a Processzor és memória erőforrások a virtuális gép használja. A tömörítési aránya nő, mivel így nem használt Processzor-erőforrások mennyiségét. Emellett módosíthatja ezt a beállítást később.

7.  Egy másik beállítást kell ellenőrizni az e a replikáció aszinkron vagy szinkron módon történik-e. *Ha az SAP ASCS/SCS-konfigurációk, a szinkron replikáció kell használnia*.  

  ![52. ábra: Replikáció adatainak megadása][sap-ha-guide-figure-3042]

  _**52. ábra:** replikálás részletei_

8.  Adja meg e kell-e a kötetet, amelyet a rendszer replikálja a replikálási feladat által jelölt a Windows Server feladatátvételi fürtszolgáltatási fürtkonfiguráció megosztott lemez. Válassza ki az SAP ASCS/SCS konfiguráció **Igen** úgy, hogy a Windows-fürt látja a replikált kötet megosztott fürtkötet, ezáltal az lemez.

  ![53. ábra: A replikált kötetet állítja be a fürt kötet az Igen lehetőséget.][sap-ha-guide-figure-3043]

  _**53. ábra:** kiválasztása **Igen** a replikált kötet beállítása a fürt kötetként_

  A kötet létrehozása után a DataKeeper felügyeleti és a konfigurációs eszköz azt mutatja, hogy a replikációs feladat aktív.

  ![54. ábra: DataKeeper szinkron tükrözés az SAP ASCS/SCS-megosztás lemez jelenleg aktív][sap-ha-guide-figure-3044]

  _**54. ábra:** DataKeeper szinkron tükrözés az SAP ASCS/SCS a lemez megosztása a jelenleg aktív_

  Ahogy az 55. ábra a Feladatátvevőfürt-kezelő most DataKeeper lemezként, a lemez jeleníti meg.

  ![55. ábra: A Feladatátvevőfürt-kezelővel a lemez DataKeeper replikált jeleníti meg][sap-ha-guide-figure-3045]

  _**55. ábra:** Feladatátvevőfürt-kezelőben jeleníti meg a lemez a replikált DataKeeper_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Az SAP NetWeaver rendszer telepítése

Az adatbázis-kezelő a telepítő nem ismertetünk, mert mátrixok változhat az adatbázis-kezelő rendszert használja. Azonban feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket az a Funkciók, a különböző DBMS-szállítók támogatja az Azure-ig. Például mindig bekapcsolva vagy adatbázis-tükrözést az SQL Server és Oracle Data Guard az Oracle-adatbázisokat. Az ebben a cikkben használjuk esetben nagyobb védelmet, az adatbázis-kezelő nem adtuk hozzá.

Nincsenek speciális megfontolásokat különböző DBMS-szolgáltatásokkal interakcióba ilyen konfigurációtípusok fürtözött SAP ASCS/SCS az Azure-ban.

> [!NOTE]
> A telepítési eljárások az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek majdnem azonosak. A legfontosabb különbség, hogy rendelkezik-e egy SAP ABAP-rendszer egyik ASCS-példány. Az SAP-Java-rendszer egyik SCS példányhoz rendelkezik. Az SAP ABAP + Java rendszer rendelkezik egy ASCS-példány és a egy, a feladatátvételi fürt ugyanazt a Microsoft csoportban futó SCS példányhoz. SAP NetWeaver-telepítés rengetegféle telepítési eltérések explicit módon szerepelnek. Akkor feltételezheti, hogy minden más részek azonosak.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Telepítse az SAP egy magas rendelkezésre állású ASCS/SCS példányhoz

> [!IMPORTANT]
> Győződjön meg arról, hogy ne helyezze el a lapozófájl DataKeeper tükrözött kötetek. DataKeeper nem támogatja a tükrözött kötetek. Az Azure virtuális gép ideiglenes D meghajtó a lapozófájl az alapértelmezett hagyhatja. Ha még nem szerepel ott, a Windows lapon fájl áthelyezéséhez az Azure virtuális gép D meghajtó.
>
>

Ezeket a feladatokat egy magas rendelkezésre állású ASCS/SCS-példánnyal rendelkező SAP telepítése foglalja magában:

- Virtuális állomásnevet a fürtözött SAP ASCS/SCS-példány létrehozása
- Az SAP első fürtcsomópontra telepítése
- Az SAP-profil ASCS/SCS-példány módosítása
- A mintavételi port hozzáadása
- A Windows tűzfal mintavételi port megnyitása

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz

1.  Hozzon létre egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomás nevét a Windows DNS-kezelőben.

  > [!IMPORTANT]
  > Az ASCS/SCS-példány virtuális állomás nevét a hozzárendelt IP-cím ugyanaz, mint az Azure Load Balancer rendelt IP-cím kell lennie (**<*SID*> - lb - ascs**).  
  >
  >

  Az SAP ASCS/SCS virtuális állomásnév IP-címét (**pr1 – ascs-sap**) ugyanaz, mint az Azure Load Balancer IP-címét (**pr1-lb-ascs**).

  ![56. ábra: A DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális nevét és a TCP/IP-cím megadása][sap-ha-guide-figure-3046]

  _**56. ábra:** a DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális nevét és a TCP/IP-cím megadása_

2.  A virtuális gazdagép neve rendelt IP-cím meghatározásához válassza **DNS-kezelő** > **tartomány**.

  ![57. ábra: Virtuális nevére és az SAP ASCS/SCS-fürtkonfiguráció TCP/IP-cím][sap-ha-guide-figure-3047]

  _**57. ábra:** új virtuális nevét és a TCP/IP-cím az SAP ASCS/SCS-fürtkonfiguráció_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Telepítse az SAP első fürtcsomópontra

1.  Hajtsa végre az első fürt csomópont lehetőség fürtcsomóponton rögzíti. Például a **pr1-ascs-0** gazdagép.
2.  Tartsa meg az alapértelmezett portok az Azure belső load balancer-hez, válassza ki:

  * **ABAP rendszer**: **ASCS** példányok száma **00**
  * **Java-rendszer**: **SCS** példányok száma **01**
  * **ABAP + Java rendszer**: **ASCS** példányok száma **00** és **SCS** példányok száma **01**

  Az ABAP ASCS-példány és a Java SCS példányhoz 01 példány számok eltérő 00 használatához először módosítani szeretné az Azure belső load balancer alapértelmezett terheléselosztási szabályokat és ismertetett [az ASC/SCS alapértelmezett vonatkozó terheléselosztási szabályok módosítása az Azure belső terheléselosztó][sap-ha-guide-8.9].

A standard szintű SAP telepítési dokumentációjának a következő néhány feladatot nem ismerteti.

> [!NOTE]
> Az SAP telepítési dokumentáció ismerteti, hogyan lehet ASCS/SCS fürt első csomópontjára telepítse.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Az SAP ASCS/SCS-példány módosításához

Adjon hozzá egy új profil paramétert kell. A profil paraméter megakadályozza, hogy az SAP-munkafolyamatok és a sorba kiszolgáló közötti kapcsolat bezárása, amelyek hosszabb ideig inaktív. A probléma esetén említettük [beállításjegyzék-bejegyzések hozzáadásához az SAP ASCS/SCS-példányának mindkét fürtcsomóponton][sap-ha-guide-8.11]. A szakasz emellett bevezettük két változást néhány alapszintű TCP/IP-kapcsolat paramétereit a. A második lépésben kell állítania a sorba kiszolgáló küld egy `keep_alive` jelezze, hogy a kapcsolatok nem nyomja le az Azure belső load balancer üresjárati küszöbértéket.

A módosításához az SAP ASCS/SCS-példány:

1.  Ez a profil paraméter hozzáadása az SAP ASCS/SCS példányhoz profil:

  ```
  enque/encni/set_so_keepalive = true
  ```
  Ebben a példában az elérési út:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Például, hogy az SAP SCS példányhoz profil és a megfelelő elérési út:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  A módosítások életbe léptetéséhez indítsa újra az SAP ASCS /SCS példányt.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adja hozzá a mintavételi port

A belső load balancer mintavételi funkciót használja, hogy az Azure Load Balancerrel együttműködve a teljes fürtkonfiguráció. Az Azure belső terheléselosztó általában a bejövő munkaterhelés részt vevő virtuális gépek között egyenlően osztja el. Azonban ez nem fog működni az egyes fürtkonfigurációk mert csak egy példány aktív. A másik példány passzív, és a munkaterhelés nem tud fogadni. Egy mintavételi funkció segítségével, amikor az Azure belső terheléselosztó munkahelyi csak egy aktív példány számára. A mintavétel funkciókkal a belső terheléselosztó képes észlelni, mely példányok aktív, és csak a számítási feladatok példány majd célként.

A mintavételi port hozzáadása:

1.  Ellenőrizze a jelenlegi **ProbePort** beállítása a következő PowerShell-parancs futtatásával. Hajtsa végre a virtuális gépek egyikében, az a fürt konfigurációját.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  A mintavételi port megadása. Az alapértelmezett mintavételi portszám **0**. Ebben a példában a mintavételi portot használjuk **62000**.

  ![58. ábra: A fürt konfigurációt a mintavételi portot kötelező alapértelmezés szerint 0][sap-ha-guide-figure-3048]

  _**58. ábra:** az alapértelmezett fürt konfigurációt a mintavételi portot kötelező 0_

  A port számát az SAP az Azure Resource Manager-sablonokkal van meghatározva. A port számát, a PowerShell rendelhet hozzá.

  Új ProbePort értéket beállítani a **SAP <*SID*> IP** fürterőforrás, futtassa a következő PowerShell-parancsfájlt. Frissítés a PowerShell változók környezete számára. A szkript futtatása után a rendszer kérni fogja, indítsa újra a SAP fürtcsoport a változások életbe lépjenek.

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

  Miután tenné a **SAP <*SID*>** a fürt hálózatra, ellenőrizze, hogy **ProbePort** az új értékre van állítva.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![59. ábra: A fürt port mintavételi, miután beállította az új érték][sap-ha-guide-figure-3049]

  _**59. ábra:** mintavételi a fürt port, miután beállította az új érték_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Nyissa meg a Windows tűzfal mintavételi port

Meg kell nyitnia a Windows tűzfal a mintavételi port mindkét fürtcsomóponton. Windows tűzfal a mintavételi port megnyitásához használja a következő szkriptet. Frissítés a PowerShell változók környezete számára.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** értékre van állítva **62000**. Most akkor is hozzáférhet a fájlmegosztáshoz  **\\\ascsha-clsap\sapmnt** más gazdagépekről, például, mint a **ascsha-adatbázisok**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Az adatbázis-példány telepítése

Az adatbázis-példány telepítéséhez kövesse a SAP telepítési dokumentációban ismertetett folyamatot.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Telepítse a második fürtcsomópontra

A második fürt telepítéséhez kövesse a lépéseket az SAP telepítési útmutatóban.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Módosíthatja a az SAP on felhasználók Windows-példányok

A SAP SSZON Windows szolgáltatás indítási típusának módosítása **automatikus (Késleltetett indítás)** mindkét fürtcsomóponton.

![60. ábra: A szolgáltatás típusa az SAP SSZON példány módosíthatja a késleltetett automatikus][sap-ha-guide-figure-3050]

_**60. ábra:** módosíthatja a szolgáltatás típusa az SAP SSZON példányt késleltetett automatikus_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Az elsődleges alkalmazás SAP-kiszolgáló telepítése

Telepítse az elsődleges alkalmazás Server ATTRIBÚTUMKÉSZLETHEZ példányt <*SID*> - di - 0 ÜGYFÉLCÍMEK üzemeltetésére kijelölt már a virtuális gépen. Nincsenek függőségek az Azure-ban vagy DataKeeper-specifikus beállítások.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> További SAP alkalmazáskiszolgáló telepítése

Telepítse az SAP további Application Server (AAS), amely már egy SAP-alkalmazáskiszolgáló-példány futtatására kijelölt összes virtuális gépet. Ha például a <*SID*> - di - 1, <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver-rendszer telepítését. Ezután folytassa feladatátvétel tesztelése.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Az SAP ASCS/SCS-példány feladatátvételt és SIOS replikáció tesztelése
Akkor is könnyen teszteléséhez és a egy SAP ASCS/SCS-példány feladatátvételt és SIOS lemez replikációs figyelemmel követhető a Feladatátvevőfürt-kezelő és az SIOS DataKeeper felügyeleti és a konfigurációs eszközt.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Az SAP ASCS/SCS példányhoz egy fürtcsomóponton fut.

A **SAP PR1** fürtcsoport fut, a fürtcsomópont-t. Ha például a **pr1-ascs-0**. Rendelje hozzá a megosztott lemezmeghajtó S, amely részét képezi, a **SAP PR1** fürtcsoport, és az ASC/SCS példányhoz használja, a fürt csomópont-t.

![61. ábra: A Feladatátvevőfürt-kezelő: < SID > az SAP fürtcsoport fut egy fürtcsomóponton][sap-ha-guide-figure-5000]

_**61. ábra:** Feladatátvevőfürt-kezelő: az SAP <*SID*> fürtcsoport fut egy csomóponton_

Az SIOS DataKeeper felügyeleti és a konfigurációs eszközt láthatja, hogy a megosztott adatok a rendszer szinkron módon replikálja a fürtcsomópontra A forrás-kötet meghajtó S a céloldali kötet meghajtóról S fürtcsomópont b Például, hogy a rendszer replikálja a **pr1-ascs-0 [10.0.0.40]** való **pr1-ascs-1 [10.0.0.41]**.

![62. ábra: Az SIOS DataKeeper, replikálja a helyi kötet fürtcsomópontról egy fürtcsomóponton B][sap-ha-guide-figure-5001]

_**62. ábra:** az SIOS DataKeeper, replikálja a helyi kötet fürtcsomópontról egy fürtcsomóponton B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Feladatátvételt egy csomópontról csomópontra B

1.  Válasszon egyet az alábbi lehetőségek közül, az SAP feladatátvétel <*SID*> fürtcsoport fürtcsomópontról A b fürtcsomópontra
  - Használja a Feladatátvevőfürt-kezelőben  
  - A feladatátvevő fürt PowerShell-lel

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Indítsa újra a fürt csomópont A Windows vendég operációs rendszerből (Ez elindítja az SAP az automatikus feladatátvételt <*SID*> fürtcsoport egy csomópontról csomópontra B).  
3.  Indítsa újra a fürt csomópont A az Azure Portal (Ez elindítja az SAP az automatikus feladatátvételt <*SID*> fürtcsoport egy csomópontról csomópontra B).  
4.  Indítsa újra a csomópont A fürthöz az Azure PowerShell használatával (Ez elindítja az SAP az automatikus feladatátvételt <*SID*> fürtcsoport egy csomópontról csomópontra B).

  Az SAP a feladatátvételt követően <*SID*> fürtcsoport fut, a fürtcsomópont B. Ha például jelenleg is fut a **pr1-ascs-1**.

  ![63. ábra: A Feladatátvevőfürt-kezelő, a SAP < SID > fürtcsoport futtató fürtcsomóponton B][sap-ha-guide-figure-5002]

  _**63. ábra**: A Feladatátvevőfürt-kezelő, a SAP <*SID*> fürtcsoport B csomóponton fut._

  A megosztott lemez már csatlakoztatva van a fürt csomópont b az SIOS DataKeeper van adatok meghajtóról forrás kötet S B fürtcsomóponton való replikálásához célmeghajtó kötet, S fürtcsomóponton A. A replikáló például **pr1-ascs-1 [10.0.0.41]** való **pr1-ascs-0 [10.0.0.40]**.

  ![64. ábra: Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról B, a csomópont A fürthöz][sap-ha-guide-figure-5003]

  _**64. ábra:** az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról B, a csomópont A fürthöz_
