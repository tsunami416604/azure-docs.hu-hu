---
title: "Az Azure virtuális gépek magas rendelkezésre állás a SAP NetWeaver |} Microsoft Docs"
description: "Magas rendelkezésre állású útmutatója SAP NetWeaver Azure virtuális gépeken"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: goraco
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae90fe1d6d9e91bffa3fd4c6a7d79d069ab604a2
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Magas rendelkezésre állás a SAP NetWeaver Azure virtuális gépeken

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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


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
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
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
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
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


Az Azure virtuális gépek egy a megoldás olyan szervezeteknek, amelyek szükség van a számítási, tárolási és hálózati erőforrásokat, minimális időben, valamint hosszú beszerzési ciklusok nélkül. Azure virtuális gépek például SAP NetWeaver alapú ABAP, Java és egy ABAP + Java verem klasszikus alkalmazások központi telepítéséhez használhatja. Megbízhatóság és rendelkezésre állás további helyszíni erőforrások nélkül kiterjesztése. Az Azure virtuális gépek közötti kapcsolatot nyújthassanak, támogatja, így a Azure Virtual Machines integrálása a szervezete helyszíni tartományok, magánfelhőket és SAP rendszer fekvő.

Ebben a cikkben azt a lépéseket, amelyek tudja telepíteni a magas rendelkezésre állású SAP rendszerek az Azure-ban Azure Resource Manager telepítési modellel fedik le. A Microsoft végigvezetik Önt a nagyobb feladatok:

* Keresse meg a megfelelő SAP megjegyzések és a telepítési útmutatók, szerepel a [erőforrások] [ sap-ha-guide-2] szakasz. Ez a cikk kiegészíti az SAP-dokumentáció és az SAP megjegyzések, amelyek az elsődleges erőforrások, amelyek segítségével telepítse, és az adott platformon SAP szoftver központi telepítése.
* További tudnivalók az Azure Resource Manager telepítési modell és az Azure klasszikus üzembe helyezési modellel közötti különbségeket.
* További információk a Windows Server feladatátvételi fürtszolgáltatási kvórummódok, így kiválaszthatja a modellt az Azure-telepítés számára megfelelő.
* További információk a Windows Server feladatátvételi fürtszolgáltatási megosztott tár az Azure-szolgáltatásokhoz.
* Ismerje meg, hogyan védheti az egyetlen ponton-az-hibát jelentő összetevők például a fejlett üzleti alkalmazások fejlesztői (ABAP) SAP központi szolgáltatások (ASC) / SAP központi szolgáltatások (SCS) és az adatbázis-kezelő rendszerek (DBMS) és a redundáns összetevők, például SAP-alkalmazás Kiszolgáló, az Azure-ban.
* Kövesse a részletes példa egy telepítési és a konfiguráció egy magas rendelkezésre állású SAP rendszer Windows Server feladatátvételi fürtszolgáltatási fürtben az Azure-ban Azure Resource Manager használatával.
* Ismerje meg a Windows Server feladatátvételi fürtszolgáltatási Azure, de amelyek nem szükségesek helyszíni telepítés használatához szükséges további lépéseket.

Egyszerűbbé teheti a üzembe helyezését és konfigurálását, ebben a cikkben az SAP háromrétegű magas rendelkezésre állású Resource Manager-sablonok használjuk. A sablonok automatizálni, amelyekre szüksége van a magas rendelkezésre állású SAP rendszert a teljes infrastruktúra telepítését. Az infrastruktúra SAP alkalmazás teljesítmény szabványos (SAP) méretezése a SAP rendszert is támogatja.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy teljesülnek-e az előfeltételeket, amelyeket a következő szakaszok ismertetik. Emellett ügyeljen arra, hogy a felsorolt összes erőforrást ellenőrizze a [erőforrások] [ sap-ha-guide-2] szakasz.

Ebben a cikkben az Azure Resource Manager sablonok használjuk [háromrétegű SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Sablonok hasznos áttekintéséért lásd: [SAP Azure Resource Manager-sablonok](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Erőforrások
Ezek a cikkek SAP üzemelő példányok Azure terjed ki:

* [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]
* [SAP NetWeaver Azure virtuális gépek központi telepítését][deployment-guide]
* [Az SAP NetWeaver Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]
* [Az Azure virtuális gépek magas rendelkezésre állás a SAP NetWeaver (Ez az útmutató)][sap-ha-guide]

> [!NOTE]
> Amikor csak lehetséges – ad a hivatkozás a hivatkozó SAP telepítési útmutatójában (lásd a [SAP telepítési útmutatók][sap-installation-guides]). Az Előfeltételek és a telepítési folyamatra vonatkozó információ célszerű gondosan olvassa el az SAP NetWeaver telepítési útmutatók. Ez a cikk ismerteti a SAP NetWeaver-alapú rendszereken használható az Azure virtuális gépek csak meghatározott feladatok.
>
>

Ezek a megjegyzések SAP SAP, az Azure-ban témakör kapcsolódnak:

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |Az Azure-on SAP-alkalmazásokból: támogatott termékek és méretezése |
| [2015553] |A Microsoft Azure SAP: Előfeltételek támogatja |
| [1999351] |SAP Azure figyelésének továbbfejlesztett |
| [2178632] |Kulcs figyelési metrikákat az SAP, a Microsoft Azure |
| [1999351] |A Windows virtualizálási: fokozott figyelése |
| [2243692] |A prémium szintű Azure SSD-tárolón SAP DBMS példány használata |

További információ a [korlátozások az Azure-előfizetések][azure-subscription-service-limits-subscription], többek között az általános alapértelmezett korlátozások és a maximális korlátozások.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Magas rendelkezésre állású SAP az Azure Resource Manager és az Azure klasszikus telepítési modell
Az Azure Resource Manager és az Azure klasszikus üzembe helyezési modellek szerepelnek eltérő a következő területeken:

- Erőforráscsoportok
- Az Azure erőforráscsoport Azure belső terheléselosztási terheléselosztó függőség
- SAP multi-SID forgatókönyvek támogatása

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Erőforráscsoportok
Az Azure Resource Manager erőforráscsoportokat használhatja az alkalmazás-erőforrásokat az Azure-előfizetése kezeléséhez. Integrált megközelítést, egy erőforráscsoportban található összes erőforrást rendelkezik az azonos életciklusát. Például minden erőforrás létrehozása egy időben, és egyszerre kell, akkor azok törlődnek. További információk az [erőforráscsoportokról](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Az Azure erőforráscsoport Azure belső terheléselosztási terheléselosztó függőség

Az Azure klasszikus üzembe helyezési modellel az Azure belső terheléselosztó (Azure terheléselosztó szolgáltatás) és a felhőalapú szolgáltatás csoportja közötti függőség van. Minden belső terheléselosztónak egy felhőalapú szolgáltatás csoportot kell.

Az Azure erőforrás-kezelőben, nem kell használni az Azure Load Balancer egy Azure erőforráscsoport. A környezet nem egyszerűbb és rugalmasabb.

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP multi-SID forgatókönyvek támogatása

Az Azure erőforrás-kezelőben, akkor több példányt is telepíthet SAP rendszer azonosítója (SID) ASC/SCS egy fürt. Minden Azure belső terheléselosztóhoz több IP-cím támogatásának köszönhetően multi-SID-példányok is előfordulhatnak.

Az Azure klasszikus üzembe helyezési modellel használatához kövesse az ismertetett eljárások [SAP NetWeaver az Azure-ban: az Azure-ban SIOS DataKeeper segítségével a Windows Server feladatátvételi fürtszolgáltatási SAP ASC/SCS fürtszolgáltatási példányok](http://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Javasoljuk, hogy az SAP-telepítések az Azure Resource Manager központi telepítési modellt használja. Nem érhetők el a klasszikus üzembe helyezési modellel számos előnyt kínál. További tudnivalók az Azure [üzembe helyezési modellel][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server feladatátvételi fürtszolgáltatás
Windows Server feladatátvételi fürtszolgáltatási az alapja az egy magas rendelkezésre állású SAP ASC/SCS telepítése és a Windows DBMS.

A feladatátvevő fürt egy 1 + n különálló kiszolgálók csoportja (csomópontok), amelyek együttműködése az alkalmazások és szolgáltatások rendelkezésre állásának javítása. Ha egy csomópont meghibásodik, Windows Server feladatátvételi fürtszolgáltatási számítja ki a biztosításához az alkalmazások és szolgáltatások kifogástalan fürt megőrzésével előforduló hibák száma. Választhat a különböző kvórummódok feladatátvételi fürtszolgáltatás eléréséhez.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Kvórummódok
Windows Server feladatátvételi fürtszolgáltatási használatakor négy kvórummódok közül választhat:

* **Csomóponttöbbség**. A fürt mindegyik csomópontján szavazati is. A fürt működik csak a szavazatot, többségét Ez azt jelenti, hogy a több mint fele a szavazatot. Azt javasoljuk, hogy a csomópontok páratlan számú fürtöknél ezt a beállítást. Például egy hét csomópontos fürtben három csomópont sikertelen lehet, és a fürt stills többsége éri el, és továbbra is fut.  
* **Csomópont- és lemeztöbbség**. Minden csomópont- és a fürttároló egy kijelölt lemezt (a tanúsító lemez) is szavazásra, mikor érhetők el, és a kommunikáció. A fürt működik csak a többsége a szavazatot, ez azt jelenti, hogy a több mint fele a szavazatot. Ez a mód környezetben fürt páros számú csomópont teljesen logikus. Ha a csomópontok fele és a lemez online állapotban, a fürt megfelelő állapotban marad.
* **Csomópont- és fájlmegosztás többsége**. Minden csomópont és a kijelölt fájlmegosztást (a tanúsító fájlmegosztás), amelyet a rendszergazda létrehoz is szavazásra, függetlenül a csomópontok és a fájlmegosztás elérhetők-e, és a kommunikáció. A fürt működik csak a többsége a szavazatot, ez azt jelenti, hogy a több mint fele a szavazatot. Ez a mód környezetben fürt páros számú csomópont teljesen logikus. A csomópont- és lemeztöbbség mód hasonló, de helyett a tanúsító lemez a tanúsító fájlmegosztás használja. Ebben a módban könnyű, de ha a fájlmegosztás maga nem magas rendelkezésre állású, azt válnak a hibaérzékeny pontok kialakulását.
* **Nincs többség: Csak lemez**. A fürt rendelkezik egy kvórum, ha egy csomópont elérhető, és a fürttároló egy meghatározott lemezével kommunikál. Csak a csomópontok is, hogy a lemez kommunikál csatlakozhat a fürthöz. Azt javasoljuk, hogy nem használja ezt a módot.

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Windows Server feladatátvételi fürtszolgáltatás a helyszíni
1. ábra mutatja a két csomópontból álló fürtben. Ha a hálózati kapcsolatot a csomópontok sikertelen, és mind fel csomópontok marad és futtatását, egy kvórumlemez vagy a fájl határozza meg, melyik csomópontján továbbra is a fürt alkalmazások és szolgáltatások biztosításához. A csomópont a kvórum lemez vagy fájlmegosztás eléréséhez használt a csomópont, amely biztosítja, hogy a szolgáltatások továbbra is.

Ez a példa egy két csomópontot tartalmazó fürtben, mert a csomópont- és fájlmegosztástöbbség kvórummód használjuk. A csomópont- és lemeztöbbség is beállítás érvényes. Éles környezetben azt javasoljuk, hogy egy kvórumlemez használjon. Hálózati és tárolási rendszer technológia segítségével magas rendelkezésre állásúvá tenni.

![1. ábra: Példa egy Windows Server feladatátvételi fürtszolgáltatás konfigurációs az SAP ASC/SCS az Azure-ban][sap-ha-guide-figure-1000]

_**1. ábra:** egy Windows Server feladatátvételi fürtszolgáltatás konfigurációs az SAP ASC/SCS az Azure-ban – példa_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Megosztott tároló
1. ábra is mutatja egy két csomópontos megosztott tárolófürt. Egy helyi megosztott tárolási fürt a fürt összes csomópontjának észleli a megosztott tároló. Zárolási mechanizmus sérülése védi az adatokat. Minden csomópont észleli, ha egy másik csomópont meghibásodik. Ha egy csomópont meghibásodik, a megmaradó csomópontra megkapja a tulajdonjogot, a tárolási erőforrások és szolgáltatások rendelkezésre állását biztosítja.

> [!NOTE]
> Például a magas rendelkezésre állás az egyes adatbázis-kezelő alkalmazások, egy megosztott lemez nem kell az SQL Server. SQL Server Always On DBMS adatainak és naplókönyvtárainak fájlokat replikálja a helyi lemez egy fürtcsomópont a helyi lemez egy másik csomópont. A Windows-fürt konfigurációs ebben az esetben egy megosztott lemez nem szükséges.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Hálózati és a névfeloldás
Ügyfélszámítógépek számára a fürt egy virtuális IP-címet és egy virtuális nevet a DNS-kiszolgáló által biztosított keresztül érhető el. A helyszíni csomópontok és a DNS-kiszolgáló képes kezelni a több IP-címet.

A hagyományos telepítés, a két vagy több hálózati kapcsolatot használja:

* A tárolási dedikált kapcsolat
* A szívverés egy fürt belső hálózati kapcsolat
* Az ügyfelek kapcsolódik a fürthöz használt nyilvános hálózaton

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Windows Server feladatátvételi fürtszolgáltatás az Azure-ban
Operációs rendszer nélküli vagy saját felhőben történő alkalmazáshoz képest, Azure virtuális gépek konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. A megosztott fürtlemez összeállításakor több IP-címek és az SAP ASC/SCS példány virtuális állomásnevek be kell.

Ez a cikk arról lesz szó főbb fogalmakat és az Azure-ban az SAP központi szolgáltatások magas rendelkezésre állású fürt létrehozásához szükséges további lépéseket. Megmutatjuk, hogyan állíthat be a külső eszköz SIOS DataKeeper és konfigurálása az Azure belső terheléselosztót. Ezek az eszközök segítségével hozzon létre egy Windows feladatátvevő fürt egy tanúsító fájlmegosztást az Azure-ban.

![2. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**2. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>SIOS DataKeeper megosztott lemezt az Azure-ban
A magas rendelkezésre állású SAP ASC/SCS példányához megosztott tárolót kell fürtbe. 2016 szeptemberétől Azure segítségével hozzon létre egy megosztott tárolófürt megosztott tárhelyet nem kínál. Harmadik féltől származó szoftverek SIOS DataKeeper Cluster Edition hozhat létre tükrözött tárolási funkciókat biztosító, amely a fürt megosztott tárolási szimulálja. A SIOS megoldást biztosít a valós idejű szinkron replikálása. Ez az, hogy hogyan hozhat létre a fürt egy megosztott lemez erőforrás:

1. Egy másik Azure virtuális merevlemezt (VHD) csatolni az egyes virtuális gépek (VM) Windows fürtkonfiguráció.
2. Futtassa a SIOS DataKeeper Cluster Edition mindkét virtuális gép csomóponton.
3. Konfigurálása SIOS DataKeeper Cluster Edition, hogy azt a tartalmat a további csatolt virtuális merevlemez kötetének a forrás virtuális gép a cél virtuális gép további csatolt virtuális merevlemez méretével tükrözi. SIOS DataKeeper kivonatolja a forrás- és helyi köteteken, és majd megadja azokat a Windows Server feladatátvételi fürtszolgáltatási mint egy megosztott lemez.

További információk [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![3. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban SIOS DataKeeper][sap-ha-guide-figure-1002]

_**3. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban SIOS DataKeeper_

> [!NOTE]
> Nincs szükség a megosztott lemez néhány DBMS termékekkel, például az SQL Server magas rendelkezésre állásra. SQL Server Always On DBMS adatainak és naplókönyvtárainak fájlokat replikálja a helyi lemez egy fürtcsomópont a helyi lemez egy másik csomópont. A Windows-fürt konfigurációs ebben az esetben egy megosztott lemez nem szükséges.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>A névfeloldás az Azure-ban
Az Azure felhőalapú platform nem teszi lehetővé a virtuális IP-címek, például csak fix IP-címek konfigurálásához. Hozzon létre egy virtuális IP-címet a felhőben fürterőforrás elérni egy másik megoldásra van szüksége.
Azure az Azure terheléselosztó szolgáltatás belső terheléselosztót tartalmaz. A belső terheléselosztó rendelkező ügyfelek elérni a fürt keresztül a virtuális IP-címet.
Kell telepítenie a belső terheléselosztó, amely tartalmazza a fürt csomópontjai az erőforráscsoportban. Ezt követően konfigurálja a belső terheléselosztó portok továbbítási szabályok a mintavételi minden szükséges portot.
Az ügyfelek kapcsolódhatnak az virtuális állomás név. A DNS-kiszolgáló oldja fel a fürt IP-címét, és a belső terheléselosztó leírók port továbbítja a fürt aktív csomópontja.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>SAP NetWeaver magas rendelkezésre állás érdekében az Azure infrastruktúra,--szolgáltatás (IaaS)
Eléréséhez SAP alkalmazás magas rendelkezésre állású, többek között az SAP szoftverösszetevőket, a következő összetevők védelmére kell:

* SAP Application Server-példány
* SAP ASC/SCS példány
* Adatbázis-kezelő kiszolgáló

Tudnivalók az SAP-összetevők a magas rendelkezésre állású forgatókönyvek védelméről további információkért lásd: [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver](planning-guide.md).

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Magas rendelkezésre állású SAP-alkalmazáskiszolgáló
Általában nem szükséges egy adott magas rendelkezésre állású megoldás az SAP-kiszolgáló és a párbeszédpanel-példányok. Magas rendelkezésre állású redundancia által érhetők el, és másik példányát az Azure virtuális gépek több párbeszédpanel példánya konfigurálhatja. Két példányban Azure virtuális gépek telepítése legalább két SAP alkalmazáspéldányok kell rendelkeznie.

![4. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**4. ábra:** magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

Az azonos Azure rendelkezésre állási állomás SAP Application Server-példány beállítása minden virtuális gépnek kell elhelyezni. Az Azure rendelkezésre állási csoportok biztosítja, hogy:

* Összes virtuális gépet az azonos frissítési tartomány részét képezik. Frissítési tartományokhoz, például gondoskodik arról, hogy a virtuális gépek tervezett karbantartás leállások során egy időben nem frissíti.
* Összes virtuális gépet az azonos tartalék tartományban részét képezik. A tartalék tartomány például gondoskodik arról, hogy telepít virtuális gépeket, hogy nincs hibaérzékeny pontok kialakulását hatással van az összes virtuális gép rendelkezésre állását.

További tudnivalók a [virtuális gépek rendelkezésre állásának kezelése][virtual-machines-manage-availability].

Mivel az Azure storage-fiók egy potenciális hibaérzékeny pontok kialakulását, fontos, hogy legalább két az Azure storage-fiókot, legalább két virtuális gép szétosztva. Az ideális beállítás a lemezeket, az egyes virtuális gépek egy SAP párbeszédpanel példányát futtató volna helyezhető üzembe egy másik tárolási fiókot.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Magas rendelkezésre állású SAP ASC/SCS példány
5. ábra egy példát a magas rendelkezésre állású SAP ASC/SCS példánya.

![5. ábra: Magas rendelkezésre állású SAP ASC/SCS példány][sap-ha-guide-figure-2001]

_**5. ábra:** magas rendelkezésre állású SAP ASC/SCS példány_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>SAP ASC/SCS példány magas rendelkezésre állását a Windows Server feladatátvételi fürtszolgáltatási az Azure-ban
Operációs rendszer nélküli vagy saját felhőben történő alkalmazáshoz képest, Azure virtuális gépek konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. A Windows feladatátvevő fürtöt hozza létre, szüksége megosztott fürtlemezre mutat, több IP-címek, több virtuális állomásnevek és az Azure belső terheléselosztót a fürtszolgáltatás egy SAP ASC/SCS példányát. Ez a cikk későbbi részében részletesebben azt tárgyalják.

![6. ábra: A Windows Server feladatátvételi fürtszolgáltatási SIOS DataKeeper használatával az Azure-ban SAP ASC/SCS konfigurációhoz][sap-ha-guide-figure-1002]

_**6. ábra:** Windows Server feladatátvételi fürtszolgáltatási az Azure-ban SIOS DataKeeper SAP ASC/SCS konfigurációhoz_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Magas rendelkezésre állású DBMS példány
Az adatbázis-kezelő a rendszer egyetlen pont, forduljon egy SAP rendszerben. Azt a magas rendelkezésre állású megoldás használatával védeni kell. 7. ábra mutatja egy SQL Server Always On magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatási és az Azure belső terheléselosztó. SQL Server Always On DBMS adatainak és naplókönyvtárainak fájlokat replikálja a saját adatbázis-kezelő replikáció használatával. Ebben az esetben, nem kell a fürt megosztott lemezt, amely egyszerűbbé teszi az összes beállítást.

![7. ábra: Példa egy magas rendelkezésre állású SAP DBMS, az SQL Server Always On][sap-ha-guide-figure-2003]

_**7. ábra:** egy magas rendelkezésre állású SAP DBMS, az SQL Server Always On – példa_

SQL Server az Azure-ban az Azure Resource Manager telepítési modell segítségével fürtszolgáltatással kapcsolatos további információkért lásd: ezek a cikkek:

* [Always On rendelkezésre állási csoport konfigurálásához Azure virtuális gépek manuálisan erőforrás-kezelő használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Egy Azure belső terheléselosztót egy Always On rendelkezésre állási csoport konfigurálása az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Végpontok közötti magas rendelkezésre állású környezetekben

### <a name="deployment-scenario-using-architectural-template-1"></a>Üzembe helyezési forgatókönyv segítségével 1. sablon architekturális.

8. ábrán egy SAP NetWeaver magas rendelkezésre állású architektúra példáját mutatja be az Azure-ban **egy** SAP rendszer. Ebben a forgatókönyvben be van állítva az alábbiak szerint:

- Az SAP ASC/SCS-példány egy kijelölt fürt használható.
- Az adatbázis-kezelő példány egy kijelölt fürt használható.
- SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépek vannak telepítve.

![8. ábra: SAP magas rendelkezésre állású architekturális sablon 1, a kijelölt fürt ASC/SCS és adatbázis-kezelő][sap-ha-guide-figure-2004]

_**8. ábra:** SAP architekturális sablon 1 magas rendelkezésre állású, dedikált fürtök ASC/SCS és adatbázis-kezelő_

### <a name="deployment-scenario-using-architectural-template-2"></a>Üzembe helyezési forgatókönyv architekturális sablon 2 használatával

9. ábra egy SAP NetWeaver magas rendelkezésre állású architektúra példáját mutatja be az Azure-ban **egy** SAP rendszer. Ebben a forgatókönyvben be van állítva az alábbiak szerint:

- Egy kijelölt fürt használt **mindkét** az SAP ASC/SCS példány és az adatbázis-kezelő.
- SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépek vannak telepítve.

![9. ábra: SAP magas rendelkezésre állású architekturális sablon 2, a dedikált fürtökben ASC/SCS és egy dedikált adatbázis-kezelő fürt][sap-ha-guide-figure-2005]

_**9. ábra:** SAP magas rendelkezésre állású architekturális sablon 2, a dedikált fürtökben ASC/SCS és egy dedikált adatbázis-kezelő fürt_

### <a name="deployment-scenario-using-architectural-template-3"></a>Üzembe helyezési forgatókönyv a 3. sablon architekturális.

10. ábrán látható egy példa egy SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **két** rendszerek, a SAP &lt;SID1&gt; és &lt;SID2&gt;. Ebben a forgatókönyvben be van állítva az alábbiak szerint:

- Egy kijelölt fürt használt **mindkét** az SAP ASC/SCS SID1 példány *és* az SAP ASC/SCS SID2 példány (egy fürt).
- Egy kijelölt fürt DBMS SID1 szolgál, és egy másik kijelölt fürt használt adatbázis-kezelő SID2 (fürtök).
- Az SAP rendszer SID1 SAP alkalmazáskiszolgáló példányainak rendelkezik saját dedikált virtuális gépek.
- Az SAP rendszer SID2 SAP alkalmazáskiszolgáló példányainak rendelkezik saját dedikált virtuális gépek.

![10. ábra: SAP magas rendelkezésre állású architekturális sablon 3, egy dedikált fürttel különböző ASC/SCS-példányok][sap-ha-guide-figure-6003]

_**10. ábra:** SAP magas rendelkezésre állású architekturális sablon 3, egy dedikált fürttel különböző ASC/SCS-példányok_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Az infrastruktúra előkészítése

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése architekturális sablon 1
Az SAP Azure Resource Manager-sablonok segítségével egyszerűsítheti a szükséges erőforrások.

A háromrétegű sablonok az Azure Resource Manager is támogatja a magas rendelkezésre állású forgatókönyvek, például architekturális sablon az 1., amely két fürttel rendelkezik. Minden fürthöz egy SAP hibaérzékeny pontot SAP ASC/SCS és adatbázis-kezelő.

Itt található, ahol kaphat a példaforgatókönyv azt ismertetik, ez a cikk az Azure Resource Manager-sablonok:

* [Kép: Azure piactér](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Kép: egyéni](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Az infrastruktúra előkészítése architekturális sablon 1:

- Az Azure portálon a a **paraméterek** panelen, a a **SYSTEMAVAILABILITY** mezőben válassza **magas rendelkezésre ÁLLÁSÚ**.

  ![11. ábra: Beállítása SAP magas rendelkezésre állású Azure Resource Manager-paraméterek][sap-ha-guide-figure-3000]

_**11. ábra:** beállítása SAP magas rendelkezésre állású Azure Resource Manager-paraméterek_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP alkalmazáskiszolgáló virtuális gépek: <*SAPSystemSID*> - di - <*száma*>
    * A fürt virtuális gépek ASC/SCS: <*SAPSystemSID*> - ASC - <*száma*>
    * Adatbázis-kezelő fürt: <*SAPSystemSID*> - db - <*száma*>

  * **Hálózati kártya tartozó összes virtuális géphez társított IP-címekkel rendelkező**:
    * <*SAPSystemSID*> - nic - di - <*száma*>
    * <*SAPSystemSID*> - nic - ASC - <*száma*>
    * <*SAPSystemSID*> - nic - db - <*száma*>

  * **Az Azure storage-fiókok**

  * **Rendelkezésre állási csoportok** esetében:
    * SAP alkalmazáskiszolgáló virtuális gépek: <*SAPSystemSID*> - avset - di
    * SAP ASC/SCS cluster virtuális gépek: <*SAPSystemSID*> - avset - ASC
    * Adatbázis-kezelő fürt virtuális gépek: <*SAPSystemSID*> - avset - adatbázis

  * **Az Azure belső terheléselosztó**:
    * Az összes port a ASC/SCS példányhoz, és az IP-cím <*SAPSystemSID*> - lb - ASC
    * Az összes port az SQL Server adatbázis-kezelő és az IP-cím <*SAPSystemSID*> - lb - adatbázis

  * **Hálózati biztonsági csoport**: <*SAPSystemSID*> - nsg - ASC-0  
    * Egy nyitott külső Remote Desktop Protocol (RDP) port az a <*SAPSystemSID*> virtuálisgép - ASC - 0

> [!NOTE]
> Az összes IP-címek a hálózati kártyák és az Azure belső terheléselosztók **dinamikus** alapértelmezés szerint. Módosítani őket **statikus** IP-címeket. Azt ismerteti, hogyan ehhez a cikk későbbi részében.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>A vállalati hálózati kapcsolat hibája (létesítmények közötti) éles környezetben használandó virtuális gépek telepítése
Éles rendszerek esetén SAP, az Azure virtuális gépek telepítése a [vállalati hálózati kapcsolat (létesítmények közötti)] [ planning-guide-2.2] Azure hely-hely típusú VPN vagy Azure ExpressRoute segítségével.

> [!NOTE]
> Az Azure Virtual Network-példányt is használhat. A virtuális hálózat és alhálózat már létrehozott és előkészítése.
>
>

1.  Az Azure portálon a a **paraméterek** panelen, a a **NEWOREXISTINGSUBNET** mezőben válassza **meglévő**.
2.  Az a **SUBNETID** mezőben adja meg az elkészített Azure hálózati SubnetID, ha azt tervezi, hogy az Azure virtuális gépek telepítése a teljes karakterlánc.
3.  Ahhoz, hogy az összes Azure-hálózat alhálózatok listája, a PowerShell parancsot:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  A **azonosító** mezőben látható a **SUBNETID**.
4. Az összes listájának **SUBNETID** értékek, futtassa a PowerShell-parancsot:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  A **SUBNETID** dolgozunk:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>A tesztelési és bemutató csak felhőalapú SAP-példányok telepítése
Egy kizárólag felhőalapú üzembe helyezési modellben a magas rendelkezésre állású SAP rendszereket telepítheti központilag. Ez a központi telepítési típus elsősorban akkor hasznos, bemutató és tesztelési használatából adódó. Nem alkalmas a termelési használati eseteket.

- Az Azure portálon a a **paraméterek** panelen, a a **NEWOREXISTINGSUBNET** mezőben válassza **új**. Hagyja a **SUBNETID** mező üres.

  Az SAP Azure Resource Manager sablon automatikusan létrehozza az Azure-beli virtuális hálózat és az alhálózatot.

> [!NOTE]
> Szükség legalább egy dedikált virtuális gép üzembe helyezéséhez az Active Directory és a DNS az Azure Virtual Network ugyanezen példányában. A sablon nem hoz létre a virtuális gépek.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Az infrastruktúra előkészítése a 2. sablon architekturális.

A SAP Azure Resource Manager sablon segítségével leegyszerűsíti az SAP architekturális sablon 2 álló szükséges infrastruktúra központi telepítését.

Itt található, ahol kaphat Azure Resource Manager-sablonok a központi telepítési forgatókönyv:

* [Kép: Azure piactér](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Kép: egyéni](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Az infrastruktúra előkészítése a 3. sablon architekturális.

Készítse elő az infrastruktúrát, és konfigurálja az SAP **multi-SID**. Például hozzáadhat egy SAP ASC/SCS másodpéldányt be egy *meglévő* fürtkonfiguráció. További információkért lásd: [konfigurálása egy SAP ASC/SCS másodpéldányt be egy meglévő fürt konfigurációját, és hozzon létre egy SAP multi-SID-konfigurációját az Azure Resource Manager][sap-ha-multi-sid-guide].

Ha szeretne létrehozni egy új multi-SID-fürtöt, használhatja a multi-SID [gyorsindítási sablonok a Githubon](https://github.com/Azure/azure-quickstart-templates).
Hozzon létre egy új multi-SID-fürtöt, üzembe helyezheti a következő három sablonokat kell:

* [Asc/SCS sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Alkalmazássablon-kiszolgálók](#application-servers-template)

A következő szakaszok további információkat a sablonok és a paraméterek meg kell adnia a sablonokban rendelkezik.

#### <a name="ASCS-SCS-template"></a>Asc/SCS sablon

A ASC/SCS sablon segítségével több ASC/SCS-példányt futtató Windows Server feladatátvevő fürt létrehozása két virtuális gépek telepíti.

A ASC/SCS multi-SID-sablon beállítása az a [ASC/SCS multi-SID sablon][sap-templates-3-tier-multisid-xscs-marketplace-image], adja meg a következő paraméterekkel:

  - **Erőforrás-előtag**.  Állítsa be az erőforrás előtagja, amelynek segítségével a telepítés során létrehozott összes erőforrás előtag. Mivel az erőforrások csak egy SAP-rendszer nem tartoznak, az előtag, az erőforrás nincs egy SAP rendszer biztonsági azonosítója.  Az előtag között kell lennie **3-6 karakter**.
  - **A verem típus**. Válassza ki a verem az SAP rendszer típusa. A verem típusától függően a Azure Load Balancer (ABAP vagy csak a Java) egy vagy két (ABAP + Java) magánhálózati IP-címek SAP rendszerenként rendelkezik.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **SAP rendszer száma**. Válassza ki a fürt telepítendő SAP rendszerek számát.
  -  **Rendelkezésre állására**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazda felhasználónevét és a rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépet.
  -  **Új vagy meglévő alhálózati**. Állítsa be, hogy egy új virtuális hálózat és alhálózat kell létrehozni, vagy használjon egy létező alhálózatot. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válassza ki a **meglévő**.
  -  **Alhálózati azonosító**. Állítsa az azonosító az alhálózat, amelyhez a virtuális gépek kell csatlakoztatni. Jelölje ki az alhálózatot, a virtuális magánhálózati (VPN) vagy ExpressRoute virtuális hálózat a virtuális gép és a helyszíni hálózathoz csatlakozni. Az azonosító általában néz ki:

   /Subscriptions/ <*előfizetés-azonosító*> /resourceGroups/ <*erőforráscsoport-név*> /providers/Microsoft.Network/virtualNetworks/ <*virtuálishálózat-név*> /subnets/ <*alhálózat neve*>

A sablon egy Azure Load Balancer példány, amely támogatja a több SAP rendszert telepíti.

- A ASC példányok példányszámának 00, 10, 20 beállítást...
- A SCS példányok példányszámának 01, 11, 21 beállítást...
- A ASC sorba helyezni replikációs Server (SSZON) (csak Linux) példányok példányszámának 02, 12, 22 beállítást...
- A SCS SSZON (csak Linux) példányok példányszámának 03., 13, 23 beállítást...

A load balancer tartalmaz 1 (Linux 2) VIP(s), a ASC/SCS 1 x-VIP és a SSZON (csak Linux esetén) 1 x-VIP.

Az alábbi lista tartalmazza az összes terheléselosztási szabályok (ahol x egy SAP rendszer, például 1, 2, 3... száma):
- Minden SAP rendszerhez a Windows-specifikus portokat: 445-ös, 5985
- Asc portok (x0 száma): 32 x 0, 36 x 0, 39 x 0, 81-es x 0, 5 x 013, 5 x 014, 5 x 016
- SCS portok (x1 száma): 32 x 1, 33 x 1, 39 x 1, 81-es x 1, 5 x 113, 5 x 114, 5 x 116
- Linux (példányszámának x2) portok ASC SSZON: 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Linux (példányszámának x3) portok SCS SSZON: 33 x 3, 5 x 313, 5 x 314, 5 x 316

A terheléselosztó a következő (Ha x az a szám az SAP-rendszer, például 1, 2, 3...) mintavételi portok használatára van konfigurálva:
- Asc/SCS belső terheléselosztó mintavételi portot betölteni: 620 x 0
- SSZON belső terheléselosztó mintavételi portot (csak Linux) betöltése: 621 x 2

#### <a name="database-template"></a>Adatbázis-sablon

Az adatbázis-sablon telepít egy vagy két virtuális gépek, amelyek egy SAP rendszer relációs adatbázis-kezelő rendszerének (RDBMS) telepítéséhez. Például ha telepít egy ASC/SCS sablon öt SAP rendszerekhez, akkor kell telepíteni a sablon ötször.

Az adatbázis-multi-SID sablon beállítása a [adatbázis multi-SID sablon][sap-templates-3-tier-multisid-db-marketplace-image], adja meg a következő paraméterekkel:

  -  **SAP rendszerazonosító**. Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használható.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **DbType**. Válassza ki az adatbázist, a fürtön telepíteni kívánt típusát. Válassza ki **SQL** Ha telepíti a Microsoft SQL Server. Válassza ki **HANA** Ha azt tervezi, hogy SAP HANA telepítse a virtuális gépeken. Ügyeljen arra, hogy válassza ki a megfelelő operációs rendszer típusa: válassza ki **Windows** SQL, és válasszon egy Linux terjesztési Hana. A kijelölt adatbázis támogatja az Azure terheléselosztó, amely kapcsolódik a virtuális gépek lesz konfigurálva:
    * **SQL**. A load balancer fog terheléselosztásához 1433-as port. Győződjön meg arról, hogy ezen a porton az SQL Server Always On beállítás.
    * **HANA**. A load balancer fog terheléselosztásához 35015 és 35017 portot. Ügyeljen arra, hogy telepítse SAP HANA példányszámának **50**.
    A load balancer 62550 mintavételi portot fogja használni.
  -  **SAP mérete**. Adjon meg az új rendszer nyújtják SAP. Ha nem tudja, a rendszer hány SAP, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  -  **Rendelkezésre állására**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazda felhasználónevét és a rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépet.
  -  **Alhálózati azonosító**. Adja meg az alhálózat ASC/SCS-sablon a telepítés során használt azonosító, vagy az alhálózat létrehozott azonosítója a ASC/SCS sablon központi telepítésének részeként.

#### <a name="application-servers-template"></a>Alkalmazássablon-kiszolgálók

A kiszolgálók alkalmazássablon SAP alkalmazáskiszolgáló-példányok egy SAP-rendszerhez használható két vagy több virtuális gépek telepíti. Például ha telepít egy ASC/SCS sablon öt SAP rendszerekhez, akkor kell telepíteni a sablon ötször.

Az alkalmazás kiszolgálók multi-SID sablon beállítása a [kiszolgálók multi-SID alkalmazássablon][sap-templates-3-tier-multisid-apps-marketplace-image], adja meg a következő paraméterekkel:

  -  **SAP rendszerazonosító**. Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használható.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **SAP mérete**. Az új rendszer nyújtják SAP száma. Ha nem tudja, a rendszer hány SAP, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  -  **Rendelkezésre állására**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazda felhasználónevét és a rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépet.
  -  **Alhálózati azonosító**. Adja meg az alhálózat ASC/SCS-sablon a telepítés során használt azonosító, vagy az alhálózat létrehozott azonosítója a ASC/SCS sablon központi telepítésének részeként.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure-beli virtuális hálózat
A fenti példában az Azure virtuális hálózat címtartománya 10.0.0.0/16. Egy alhálózat neve van **alhálózati**, a 10.0.0.0/24 címtartományt. A virtuális hálózaton található virtuális gépek és a belső terheléselosztók vannak telepítve.

> [!IMPORTANT]
> Nem módosítja a vendég operációs rendszerében a hálózati beállításokat. Ez magában foglalja az IP-címek, a DNS-kiszolgálók és az alhálózatot. A hálózati beállítások konfigurálása az Azure-ban. A Dynamic Host Configuration Protocol (DHCP) szolgáltatás tölti ki a beállításokat.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-címek

Állítsa be a szükséges DNS-IP-címek, tegye a következőket.

1.  Az Azure portálon a a **DNS-kiszolgálók** panelen ellenőrizze, hogy a virtuális hálózat **DNS-kiszolgálók** beállítás **egyéni DNS**.
2.  Válassza ki a beállítások alapján a hálózati rendelkezik. További információkért lásd a következőket:
    * [Vállalati hálózati kapcsolat (létesítmények közötti)][planning-guide-2.2]: hozzáadása a helyi DNS-kiszolgálók IP-címét.  
    Kiterjesztheti a helyi DNS-kiszolgálók az Azure-ban futó virtuális gépek számára. A forgatókönyv adhat hozzá a DNS szolgáltatást futtató Azure virtuális gépek IP-címét.
    * [Csak felhőalapú telepítési][planning-guide-2.1]: helyezze üzembe a virtuális hálózati példányt a DNS-kiszolgáló látja, hogy egy további virtuális gépet. Adja hozzá a DNS-szolgáltatás futtatásához beállítása az Azure virtuális gépek IP-címét.

    ![12. ábra: DNS-kiszolgálók konfigurálása az Azure-beli virtuális hálózathoz][sap-ha-guide-figure-3001]

    _**12. ábra:** DNS konfigurálása az Azure Virtual Network kiszolgálók_

  > [!NOTE]
  > Ha módosítja a DNS-kiszolgálók IP-címét, a módosítás alkalmazásához, és az új DNS-kiszolgálók propagálása Azure virtuális gépek újraindítására szeretné.
  >
  >

A fenti példában a DNS-szolgáltatás telepítve és konfigurálva van a Windows virtuális gépek:

| Virtuálisgép-szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Állomásnév és a statikus IP-címet a SAP ASC/SCS fürtözött példány és az adatbázis-kezelő fürtözött példány

A helyszíni telepítéshez szüksége ezek fenntartott állomásneve és IP-címek:

| Virtuális állomás neve szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| SAP ASC/SCS első fürt virtuális host name (kezelő) |PR1-ASC-vir |10.0.0.42 |
| SAP ASC/SCS példány virtuális állomás neve |PR1-ASC-sap |10.0.0.43 |
| SAP DBMS második fürt virtuális állomásnevet (kezelő) |PR1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor hozzon létre a virtuális állomásnevek **pr1-ASC-vir** és **pr1-dbms-vir** és a társított IP-címek, amely egyrészt a fürt kezeléséhez. Ezzel kapcsolatos további információkért lásd: [fürtcsomópontok fürtkonfiguráció gyűjtése][sap-ha-guide-8.12.1].

A másik két virtuális állomásnevek, manuálisan is létrehozhat **pr1-ASC-sap** és **pr1-adatbázis-kezelő – sap**, és a társított IP-címek, a DNS-kiszolgálón. A fürtözött SAP ASC/SCS-példány és a fürtözött adatbázis-kezelő példány használja ezeket az erőforrásokat. Ezzel kapcsolatos további információkért lásd: [hozzon létre egy virtuális nevet egy fürtözött SAP ASC/SCS példány][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Állítsa be a statikus IP-címeket az SAP virtuális gépekhez
Miután telepítette a virtuális gépeket a fürtben használni, az összes virtuális gép statikus IP-címek be kell. Ehhez az Azure virtuális hálózat konfigurálása, és nem a vendég operációs rendszer.

1.  Válassza ki az Azure-portálon **erőforráscsoport** > **hálózati kártya** > **beállítások** > **IP-cím**.
2.  Az a **IP-címek** panel alatt **hozzárendelés**, jelölje be **statikus**. Az a **IP-cím** mezőbe írja be a használni kívánt IP-cím.

  > [!NOTE]
  > Ha módosítja a hálózati kártya IP-címét, indítsa újra az Azure virtuális gépeken, a módosítás alkalmazására van szükség.  
  >
  >

  ![13. ábra: Állítsa be a statikus IP-címek a hálózati kártyát. az egyes virtuális gépek][sap-ha-guide-figure-3002]

  _**13. ábra:** statikus IP-címek a hálózati kártyát. az egyes virtuális gépek beállítása_

  Ismételje meg ezt a lépést minden hálózati interfészen, hogy van, az összes virtuális gép, beleértve az Active Directory és a DNS szolgáltatás használni kívánt virtuális gépek.

A jelen példában vezetünk be a virtuális gépek és a statikus IP-címek:

| Virtuálisgép-szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP Application Server-példány |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Második SAP Application Server-példány |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP Application Server-példány |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| Első fürtcsomópontra ASC/SCS-példány |PR1-ASC-0 |PR1-nic-ASC-0 |10.0.0.40 |
| Második fürtcsomópont ASC/SCS-példány |PR1-ASC-1 |PR1-nic-ASC-1 |10.0.0.41 |
| Adatbázis-kezelő példány első fürtcsomópontra |PR1-db-0 |PR1-nic-db-0 |10.0.0.30 |
| Adatbázis-kezelő példány második fürtcsomópont |PR1-db-1 |PR1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Egy statikus IP-cím beállítása az Azure belső terheléselosztóhoz

Az SAP Azure Resource Manager sablonnal hoz létre Azure belső terheléselosztót a SAP ASC/SCS példány és az adatbázis-kezelő fürtön használt.

> [!IMPORTANT]
> A virtuális gazdagép neve a SAP ASC/SCS IP-címe megegyezik a SAP ASC/SCS belső terheléselosztó IP-címe: **pr1-lb-ASC**.
> A DBMS neve virtuális IP-címe megegyezik az IP-cím, az adatbázis-kezelő belső terheléselosztó: **pr1-lb-dbms**.
>
>

Egy statikus IP-cím beállítása az Azure belső terheléselosztóhoz:

1.  A kezdeti telepítés beállítja a belső terheléselosztó IP-cím **dinamikus**. Az Azure portálon a a **IP-címek** panel alatt **hozzárendelés**, jelölje be **statikus**.
2.  Állítsa be az IP-cím, a belső terheléselosztó **pr1-lb-ASC** virtuális állomásnevének az SAP ASC/SCS példány IP-címre.
3.  Állítsa be az IP-cím, a belső terheléselosztó **pr1-lb-dbms** virtuális állomásnevének az adatbázis-kezelő példány IP-címre.

  ![14. ábra: Az SAP ASC/SCS példány belső terheléselosztót beállítani statikus IP-címek][sap-ha-guide-figure-3003]

  _**14. ábra:** SAP ASC/SCS-példány a belső terheléselosztó statikus IP-címek beállítása_

Ebben a példában két Azure belső terheléselosztók a statikus IP-címmel rendelkező vezetünk be:

| Az Azure belső terheléselosztási szerepköréhez | Az Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| SAP ASC/SCS példány belső terheléselosztót |PR1-lb-ASC |10.0.0.43 |
| SAP DBMS belső terheléselosztó |PR1-lb-adatbázis-kezelő |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Alapértelmezett ASC/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz

A SAP Azure Resource Manager-sablon létrehozza a portok:
* Az alapértelmezett példányszámának, ABAP ASC példány **00**
* A Java SCS példány, az alapértelmezett példányszámának **01**

Ha a SAP ASC/SCS példányát telepíti, az alapértelmezett példányszámának kell használnia **00** ABAP ASC-példány és az alapértelmezett példányszámának **01** a Java SCS-példány.

Ezután hozzon létre a szükséges belső terheléselosztási végpontok a SAP NetWeaver portok.

Szükséges belső terheléselosztási végpontok, először hozzon létre a terheléselosztást a SAP NetWeaver ABAP ASC portok végpontok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (példányszámának 00 példány ASC) (SSZON 10) |
| --- | --- | --- |
| Sorba helyezni Server / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| ABAP üzenet Server / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Belső ABAP üzenet / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| A kiszolgáló HTTP-üzenet / *Lbrule8100* |81-es <*InstanceNumber*> |8100 |
| SAP Start ASC a HTTP / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP Start szolgáltatás ASC HTTPS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Sorba helyezni replikációs / *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| SAP Start SSZON HTTP-kérelmekre *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| SAP Start SSZON HTTP-kérelmekre *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| Erőforrás-kezelő Win *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**1. táblázat:** portszámokat SAP NetWeaver ABAP ASC példánya_

Ezután hozzon létre a terheléselosztást a SAP NetWeaver Java SCS portok végpontok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (SCS példány példányszámának 01) (SSZON 11) |
| --- | --- | --- |
| Sorba helyezni Server / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Java-üzenet Server / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| A kiszolgáló HTTP-üzenet / *Lbrule8101* |81-es <*InstanceNumber*> |8101 |
| SAP Start SCS a HTTP / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| SAP Start szolgáltatás SCS HTTPS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Sorba helyezni replikációs / *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| SAP Start SSZON HTTP-kérelmekre *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| SAP Start SSZON HTTP-kérelmekre *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| Erőforrás-kezelő Win *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**2. táblázat:** portszámot az SAP NetWeaver Java SCS-példányok_

![15. ábra: Az alapértelmezett ASC/SCS betöltése az Azure belső terheléselosztóhoz tartozó terheléselosztási szabályok][sap-ha-guide-figure-3004]

_**15. ábra:** alapértelmezett ASC/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz_

A terheléselosztó IP-cím beállítása **pr1-lb-dbms** virtuális állomásnevének az adatbázis-kezelő példány IP-címre.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Módosítsa a ASC/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztóhoz

Ha a SAP ASC vagy SCS példányok eltérő számú használni kívánt, módosítania kell a nevek és értékek a portok alapértelmezett értékekhez.

1.  Válassza ki az Azure-portálon  **<* SID*> - lb - ASC betöltése terheléselosztó ** > **terheléselosztási szabályok betöltése**.
2.  Minden terheléselosztási szabályok, amelyek az SAP ASC vagy SCS példányhoz tartozik ezek az értékek módosítása:

  * Név
  * Port
  * Háttér-port

  Például ha szeretné módosítani az alapértelmezett ASC példányszámának a 00 és 31, szeretné hajtsa végre a módosításokat minden porthoz az 1.

  Példa port frissítési *lbrule3200*.

  ![16. ábra: Módosítsa a ASC/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztóhoz][sap-ha-guide-figure-3005]

  _**16. ábra:** ASC/SCS alapértelmezett terheléselosztási az Azure belső terheléselosztóhoz tartozó szabályok módosítása_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows virtuális gépek felvételét a tartományba

Miután egy statikus IP-címet rendel a virtuális gépek, a virtuális gépek felvételét a tartományba.

![17. ábra: A virtuális gépek hozzáadása a tartományhoz][sap-ha-guide-figure-3006]

_**17. ábra:** felvesz egy virtuális gépet egy tartományhoz_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adja hozzá a beállításjegyzék-bejegyzések az SAP ASC/SCS példány mindkét fürtcsomóponton

Az Azure terheléselosztó a belső terheléselosztók, hogy a kapcsolatok bezárása, amikor a kapcsolat üresjáratban a megadott ideig (üresjárati időkorlátot) időben rendelkezik. SAP munkafolyamatok párbeszédpanel példányok nyitott kapcsolatok az SAP sorba, amint az első sorba helyezni/created küldje kérelem küldésének kell feldolgozni. Ezek a kapcsolatok általában marad a meghatározott a munkahelyi folyamat, vagy a sorba helyezni folyamat újraindítja. Azonban ha a kapcsolat üresjáratban a beállított időn belül, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mert a SAP munkahelyi folyamat újra létrehozza a kapcsolatot, a várólistára helyezés folyamatban, ha már nem létezik. Ezek a tevékenységek SAP folyamatok fejlesztői nyomait vannak dokumentálva, de ezeket a nyomkövetéseket a felesleges tartalmat nagy mennyiségű hoznak létre. Jó ötlet módosítása a TCP/IP `KeepAliveTime` és `KeepAliveInterval` mindkét fürtcsomóponton. Ezek a változások, a TCP/IP-paraméterek SAP profil paraméterekkel, a cikk későbbi részében leírt össze.

Mindkét fürtcsomópont az SAP ASC/SCS példány beállításjegyzék-bejegyzések hozzáadásához először adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASC/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció csatolása |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

_**3. táblázat:** módosítsa az első TCP/IP-paraméter_

Ezt követően adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASC/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció csatolása |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

_**4. táblázat:** módosítása a második TCP/IP-paraméter_

**A módosítások alkalmazásához, indítsa újra a mindkét fürtcsomópontot**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Egy SAP ASC/SCS példánya számára a Windows Server feladatátvételi fürtszolgáltatási fürt beállítása

A Windows Server feladatátvételi fürtszolgáltatási fürt egy SAP ASC/SCS-példány beállítása magában foglalja a ezeket a feladatokat:

- Fürtözött konfigurációban a fürt csomópontjai gyűjtése
- A fürt tanúsító fájlmegosztás beállítása

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Fürtözött konfigurációban a fürt csomópontjai gyűjtése

1.  A szerepkör hozzáadása és szolgáltatások varázsló vegye fel a Feladatátvételi fürtszolgáltatást mindkét fürtcsomóponton.
2.  A feladatátvevő fürt beállítása a Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelőben válasszon **fürt létrehozása**, és adja meg csak az első fürt, a csomópont A. neve A második csomópont ne adjon hozzá még; a második csomópont egy későbbi lépésben fogja hozzáadni.

  ![18. ábra: A kiszolgáló vagy a virtuális gép nevét, az első fürtcsomópont hozzáadása][sap-ha-guide-figure-3007]

  _**18. ábra:** adja hozzá az első fürtcsomópontra a kiszolgáló vagy a virtuális gép neve_

3.  Adja meg a fürt hálózati neve (virtuális állomás neve).

  ![19. ábra: Adja meg a fürt neve][sap-ha-guide-figure-3008]

  _**19. ábra:** adja meg a fürt neve_

4.  Miután létrehozta a fürthöz, futtassa a fürtellenőrzési tesztet.

  ![20. ábra: A fürt érvényesítése-ellenőrzés futtatása][sap-ha-guide-figure-3009]

  _**20. ábra:** a fürt érvényesítése-ellenőrzés futtatása_

  Ezen a ponton a folyamat lemezek kapcsolatos figyelmeztetéseket figyelmen kívül hagyhatja. Egy tanúsító fájlmegosztást és a SIOS megosztott lemezek később fogja hozzáadni. Ezen a ponton nem kell foglalkoznia a kvórum.

  ![21. ábra: Kvórumlemez nem található][sap-ha-guide-figure-3010]

  _**21. ábra:** kvórumlemez nem található_

  ![22. ábra: Core fürterőforrás kell egy új IP-cím][sap-ha-guide-figure-3011]

  _**22. ábra:** Core fürterőforrás kell egy új IP-cím_

5.  A core fürtszolgáltatás az IP-címének módosítása. A fürt nem indítható el, amíg nem módosítja az IP-cím a core fürtszolgáltatás, mert a kiszolgáló IP-címét a virtuális gép csomópontok egyikére. Az ehhez a **tulajdonságok** a core fürtszolgáltatás IP-erőforrás oldalán.

  Például IP-címet kell (a példánkban **10.0.0.42**) a fürt virtuális állomás neve **pr1-ASC-vir**.

  ![23. ábra: A Tulajdonságok párbeszédpanelen az IP-címének módosítása][sap-ha-guide-figure-3012]

  _**23. ábra:** a a **tulajdonságok** párbeszédpanel változtassa meg az IP-cím_

  ![24. ábra: Az a fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

  _**24. ábra:** a fürt számára fenntartott IP-cím hozzárendelése_

6.  A fürt virtuális állomásnevet online állapotba.

  ![25. ábra: Core a fürtszolgáltatás működik-e és fut, és a megfelelő IP-cím][sap-ha-guide-figure-3014]

  _**25. ábra:** core a fürtszolgáltatás működik-e és fut, és a megfelelő IP-cím_

7.  Adja hozzá a második fürtcsomópontokat.

  Most, hogy a core fürtszolgáltatás működik és elérhető, a második fürtcsomópont is hozzáadhat.

  ![26. ábra: A második csomópont hozzáadása][sap-ha-guide-figure-3015]

  _**26. ábra:** második csomópont hozzáadása_

8.  Adjon meg egy nevet, a második csomópont gazda esetében.

  ![27. ábra: Adja meg a második fürt csomópont állomásnév][sap-ha-guide-figure-3016]

  _**27. ábra:** adja meg a második fürt csomópont állomásnév_

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a **minden megfelelő tároló felvétele a fürt** jelölőnégyzet **nem** kijelölt.  
  >
  >

  ![28. ábra: Jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

  _**28. ábra:** tegye **nem** jelölje be a jelölőnégyzetet_

  Kvórum és lemezek kapcsolatos figyelmeztetések figyelmen kívül hagyhatja. Akkor lesz a kvórum és megosztja a lemez később, a [telepítése SIOS DataKeeper Cluster Edition SAP ASC/SCS megosztás olyan fürtlemez esetében][sap-ha-guide-8.12.3].

  ![29. ábra: A lemez kvórumával kapcsolatos figyelmeztetések mellőzése][sap-ha-guide-figure-3018]

  _**29. ábra:** a lemez kvórumával kapcsolatos figyelmeztetések mellőzése_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>A fürt tanúsító fájlmegosztás beállítása

Ezeket a feladatokat a fürt tanúsító fájlmegosztás beállítása foglal magában:

- Fájlmegosztás létrehozása
- A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelőben

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Fájlmegosztás létrehozása

1.  Válassza ki a tanúsító fájlmegosztást egy kvórumlemez helyett. SIOS DataKeeper támogatja ezt a lehetőséget.

  A cikkben szereplő példákban a tanúsító fájlmegosztás az Azure-ban futó Active Directory és a DNS-kiszolgálón. A tanúsító fájlmegosztás neve **domcontr-0**. Volna konfigurálta az Azure-ba (telephelyek közötti VPN vagy Azure ExpressRoute) keresztül VPN-kapcsolat, mert az Active Directory és a DNS szolgáltatás a helyi, és nem megfelelő futtatni egy tanúsító ossza meg.

  > [!NOTE]
  > Ha az Active Directory és a DNS szolgáltatás csak a helyszíni fut, ne állítson be a tanúsító fájlmegosztás fut-e a helyszíni Active Directory és a DNS a Windows operációs rendszeren. Előfordulhat, hogy fut az Azure Active Directory és a DNS a helyszíni és a fürtcsomópontok közötti hálózati késés túl nagy, és a csatlakozási problémák miatt. Ne felejtse el a tanúsító fájlmegosztás beállítása megközelíti a fürtcsomópont futtató Azure virtuális géphez.  
  >
  >

  A kvórum meghajtó legalább 1024 MB szabad területre van szüksége. Azt javasoljuk, hogy a 2048 MB szabad lemezterületet a kvórum meghajtót.

2.  Adja hozzá a fürtnévobjektum.

  ![30. ábra: A megosztást a fürtnévobjektum vonatkozó engedélyek hozzárendelése][sap-ha-guide-figure-3019]

  _**30. ábra:** a megosztást a fürtnévobjektum vonatkozó engedélyek hozzárendelése_

  Ne feledje, hogy az engedélyek tartalmazza-e az adatok módosítása a megosztást a fürtnévobjektum-kezelő szolgáltatóként (a példánkban **pr1-ASC-vir$**).

3.  A fürtnévobjektum felvenni a listára, válassza ki **Hozzáadás**. Módosítsa a szűrőt, hogy ellenőrizze a számítógép-objektumok kívül 31. ábrán látható.

  ![31. ábra: Változás az Objektumtípusok számítógép felvétele][sap-ha-guide-figure-3020]

  _**31. ábra:** számítógépek felvenni objektumtípusok módosítása_

  ![32. ábra: Jelölje be a számítógépek][sap-ha-guide-figure-3021]

  _**32. ábra:** válassza ki a **számítógépek** jelölőnégyzetet_

4.  Adja meg a fürtnévobjektum, ahogy az ábra 31. A bejegyzést már létrejött, mert az engedélyek módosíthatja, ahogy az ábra 30.

5.  Válassza ki a **biztonsági** a megosztást, és majd lapján részletesebb a fürtnévobjektum engedélyeit.

  ![33. ábra: A fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása][sap-ha-guide-figure-3022]

  _**33. ábra:** a a fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Állítsa be a fájl megosztási tanúsító kvórum a Feladatátvevőfürt-kezelőben

1.  Nyissa meg a fürtkvórum beállítása varázsló konfigurálja.

  ![34. ábra: A konfigurálás fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

  _**34. ábra:** a konfigurálás fürtkvórum beállítása varázsló indítása_

2.  Az a **kvórumkonfiguráció kiválasztása** lapon, hogy melyik **a kvórum tanúsítójának kijelölése**.

  ![35. ábra: Választhat kvórumkonfigurációinak][sap-ha-guide-figure-3024]

  _**35. ábra:** választhat kvórumkonfigurációinak_

3.  Az a **kvórum Tanúsítójának kijelölése** lapon, hogy melyik **konfigurálása egy tanúsító fájlmegosztást**.

  ![36. ábra: Válassza ki a tanúsító fájlmegosztás][sap-ha-guide-figure-3025]

  _**36. ábra:** válassza ki a tanúsító fájlmegosztás_

4.  Adja meg az UNC elérési útnak a fájlmegosztásra (a példánkban \\domcontr-0\FSW). A módosításokat végezhet listájának megtekintéséhez válasszon **következő**.

  ![37. ábra: A fájlmegosztási helyet a tanúsító fájlmegosztás meghatározása][sap-ha-guide-figure-3026]

  _**37. ábra:** határozza meg a fájlmegosztás helyét a tanúsító fájlmegosztás_

5.  Válassza ki a megfelelő módosításokat, majd válassza ki **következő**. Sikeresen konfigurálja újra a fürtkonfiguráció ahogy az ábra 38 kell.  

  ![38. ábra: Megerősítése, hogy a fürt már újra konfigurálni][sap-ha-guide-figure-3027]

  _**38. ábra:** , hogy a fürt már újra konfigurálni megerősítése_

A Windows feladatátvevő fürt sikeres telepítését követően módosítások szükség lehet néhány küszöbértékek való igazításának lehetősége feladatátvételi észlelési feltételeket az Azure-ban. Módosítani kell a paraméterei dokumentálva vannak ebben a blogban: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/. Feltételezve, hogy a két virtuális gépekre, amelyek a Windows-fürt konfigurációs építése ASC/SCS ugyanazon az alhálózaton találhatók, a következő paramétereket kell módosítani ezeket az értékeket:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Ezeket a beállításokat felhasználók tesztelése és megadott kell lennie, elég rugalmas az egyik oldalon a helyes biztonsági sérülése. Másfelől ezeket a beállításokat volt biztosítása gyors elég feladatátvételi valós hibaüzenet feltételekben az SAP-szoftver vagy a csomópont vagy Virtuálisgép-hiba. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Az SAP ASC/SCS fürtlemez-megosztás SIOS DataKeeper Cluster Edition telepítése

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban. De egy SAP ASC/SCS példányát telepítenie kell egy megosztott lemez erőforrás. Nem hozható létre a szükséges megosztott lemez erőforrások az Azure-ban. SIOS DataKeeper Cluster Edition egy olyan külső megoldás, megosztott lemez erőforrások létrehozására használhatja.

Ezeket a feladatokat az SAP ASC/SCS megosztás fürtlemezt SIOS DataKeeper Cluster Edition telepítését foglalja magában:

- A .NET-keretrendszer 3.5-ös hozzáadása
- SIOS DataKeeper telepítése
- SIOS DataKeeper beállítása

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adja hozzá a .NET-keretrendszer 3.5-ös verzióját
A Microsoft .NET-keretrendszer 3.5-ös verzióját nem automatikusan aktiválva, vagy Windows Server 2012 R2 rendszeren telepítve. Mivel SIOS DataKeeper szüksége van a .NET-keretrendszer telepíthető DataKeeper minden csomóponton, telepítenie kell a .NET-keretrendszer 3.5 az összes virtuális gépet a fürt a vendég operációs rendszeren.

A .NET-keretrendszer 3.5 hozzáadandó két módja van:

- A szerepkörök hozzáadása és szolgáltatások varázsló használata a Windows ahogy az ábra 39.

  ![39. ábra: A .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és szolgáltatások varázsló segítségével][sap-ha-guide-figure-3028]

  _**39. ábra:** a .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és szolgáltatások varázsló_

  ![40. ábra: Telepítés folyamatjelző, amikor telepíti a .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével][sap-ha-guide-figure-3029]

  _**40. ábra:** telepítés folyamatjelző, amikor telepíti a .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével_

- A parancssori eszköz a dism.exe használata szükséges. Az ilyen típusú telepítést kell a Windows-telepítési adathordozón lévő SxS könyvtár eléréséhez. Egy rendszergazda jogú parancssort írja be:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper telepítése

A fürt minden csomópontja SIOS DataKeeper Cluster Edition telepítését. Hozzon létre virtuális megosztott tárolási SIOS DataKeeper hozzon létre egy szinkronizált tükrözött, és ezután szimulálni a fürt megosztott tárhelye.

A SIOS szoftver telepítése előtt hozza létre a tartományi felhasználót **DataKeeperSvc**.

> [!NOTE]
> Adja hozzá a **DataKeeperSvc** felhasználót, hogy a **helyi rendszergazdai** csoport mindkét fürtcsomóponton.
>
>

SIOS DataKeeper telepítése:

1.  Telepíti a SIOS mindkét fürtcsomóponton.

  ![SIOS telepítő][sap-ha-guide-figure-3030]

  ![. Ábra 41: A SIOS DataKeeper telepítés első oldalán][sap-ha-guide-figure-3031]

  _**41. ábra:** SIOS DataKeeper telepítésének első oldalára_

2.  A megjelenő ábra 42 párbeszédpanelen válassza ki **Igen**.

  ![42. ábra: DataKeeper arról tájékoztatja, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

  _**42. ábra:** DataKeeper arról tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3.  A párbeszédpanelen megjelenő ábra 43, azt javasoljuk, hogy kiválassza **tartomány vagy a kiszolgáló fiók**.

  ![43. ábra: A SIOS DataKeeper felhasználó kiválasztása][sap-ha-guide-figure-3033]

  _**43. ábra:** SIOS DataKeeper a felhasználó kiválasztása_

4.  Adja meg a tartományi fiók felhasználói nevét és a jelszavak SIOS DataKeeper létrehozott.

  ![44. ábra: Adja meg a tartományi felhasználónevet és jelszót a SIOS DataKeeper telepítés][sap-ha-guide-figure-3034]

  _**44. ábra:** adja meg a tartományi felhasználónevet és jelszót a SIOS DataKeeper telepítés_

5.  Telepítse a SIOS DataKeeper példány licenckulcs, ahogy az ábra 45.

  ![45. ábra: Adja meg a SIOS DataKeeper licenckulcs][sap-ha-guide-figure-3035]

  _**45. ábra:** adja meg a SIOS DataKeeper licenckulcs_

6.  Amikor a rendszer kéri, indítsa újra a virtuális gép.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS DataKeeper beállítása

Miután mindkét csomópont SIOS DataKeeper telepítette, akkor indítsa el a konfigurációs. A konfigurációs célja van csatolva a virtuális gépek mindegyikének további virtuális merevlemezeknek között szinkron replikálása.

1.  Indítsa el a DataKeeper kezelési és konfigurációs eszközt, és válassza **Connect kiszolgáló**. (Az ábrán 46, ez a beállítás van körben piros.)

  ![46. ábra: SIOS DataKeeper kezelési és konfigurációs eszköz][sap-ha-guide-figure-3036]

  _**46. ábra:** SIOS DataKeeper felügyelete és konfigurálása eszköz_

2.  Adja meg a nevét vagy IP-címét az első csomópontot a felügyeleti és konfigurációs eszköz számára, és egy második lépésben, a második csomópont kapcsolódnia kell.

  ![47. ábra: Helyezze be a nevet, vagy a TCP/IP-cím, az első csomópontot a felügyeleti és a konfigurációs eszközt kell csatlakoztatja, és egy második lépésben, a második csomópont][sap-ha-guide-figure-3037]

  _**47. ábra:** helyezze be a nevet vagy az első csomópontot a felügyeleti és a konfigurációs eszközt kell csatlakoztatja, és egy második lépésben, a második csomópont TCP/IP-címe_

3.  Hozzon létre a replikációs feladatot, a két csomópont között.

  ![48. ábra: A replikáció feladat létrehozása][sap-ha-guide-figure-3038]

  _**48. ábra:** replikációs feladat létrehozása_

  A varázsló végigvezeti egy fájlreplikálási feladat létrehozásának folyamatán.
4.  Adja meg a nevét, az TCP/IP-cím és a a forráscsomópont lemezkötetet.

  ![49. ábra: Adja meg a nevét, a replikációs feladat][sap-ha-guide-figure-3039]

  _**49. ábra:** adja meg a nevét, a replikációs feladat_

  ![50. ábra: A csomópont, amely az aktuális adatforrás-csomópontnak kell lennie az alap adatok meghatározásához][sap-ha-guide-figure-3040]

  _**50. ábra:** a csomóponthoz, amely az aktuális adatforrás-csomópontnak kell lennie az alap adatok meghatározásához_

5.  Adja meg a nevét, az TCP/IP-cím és a célcsomóponton lemezkötetének.

  ![51. ábra: A csomópont, hogy az aktuális célcsomóponttal alkalmazandó alap adatok meghatározásához][sap-ha-guide-figure-3041]

  _**51. ábra:** határozza meg a kell lennie az aktuális célcsomóponttal csomópont adatait_

6.  A tömörítési algoritmust határozza meg. Ebben a példában azt javasoljuk, hogy a tömörítés a replikálási adatfolyamból. Különösen abban az esetben az újraszinkronizálás a tömörítés a replikációs adatfolyam jelentősen csökkenti az újraszinkronizálás idő. Vegye figyelembe, hogy a tömörítés a Processzor és memória szempontjából erőforrásokat egy virtuális gép használja. A tömörítési arány növekszik, ezért nem használt CPU-erőforrások mennyiségét. Is módosíthatja ezt a beállítást később.

7.  Egy másik beállítást kell ellenőrizni, hogy a replikáció aszinkron vagy szinkron módon történik-e. *Ha a SAP ASC/SCS konfigurációk, használnia kell a szinkron replikáció*.  

  ![52. ábra: A replikáció adatainak megadása][sap-ha-guide-figure-3042]

  _**52. ábra:** replikációs adatainak megadása_

8.  Határozza meg, hogy a kötet a replikációs feladat által replikált kell magát egy Windows Server feladatátvételi fürtszolgáltatási fürtkonfiguráció, mint egy megosztott lemez számára. Az SAP ASC/SCS konfigurációs kiválasztása **Igen** , hogy a Windows fürtszolgáltatás látja a replikált kötet egy megosztott lemezt, amely egy fürt kötetként használhat.

  ![53. ábra: Válassza az Igen, a replikált kötet beállítása a fürt kötetként][sap-ha-guide-figure-3043]

  _**53. ábra:** válasszon **Igen** a replikált kötet beállítása a fürt kötetként_

  A kötet létrehozása után a DataKeeper felügyelete és konfigurálása eszköz mutatja, hogy a replikációs feladat aktív.

  ![Ábra 54: DataKeeper szinkron tükrözés az SAP ASC/SCS megosztás lemez jelenleg aktív][sap-ha-guide-figure-3044]

  _**Ábra 54:** DataKeeper szinkron tükrözés az SAP ASC/SCS a lemez megosztása a jelenleg aktív_

  Feladatátvevőfürt-kezelő most már a lemez DataKeeper lemezként jeleníti meg, ahogy az ábra 55.

  ![55. ábra: A Feladatátvevőfürt-kezelő a lemez DataKeeper replikált jeleníti meg][sap-ha-guide-figure-3045]

  _**Ábra 55:** Feladatátvevőfürt-kezelő jeleníti meg a lemezt, a replikált DataKeeper_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Az SAP NetWeaver rendszer telepítése

Az adatbázis-kezelő a telepítő azt nem ismertetik, mert beállítások eltérők lehetnek, attól függően, hogy az adatbázis-kezelő rendszert használ. Azonban feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket a különböző DBMS forgalmazójával támogatja az Azure-funkciókkal rendelkező tárgyalja. Például mindig bekapcsolva vagy adatbázis-tükrözést az SQL Server és Oracle Data Guard az Oracle-adatbázisok. A forgatókönyvben a cikkben használjuk, azt nem további védelem bekapcsolása a az adatbázis-kezelő.

Nincsenek semmilyen külön odafigyelést különböző adatbázis-kezelő szolgáltatásokat fürtözött SAP ASC/SCS konfigurálása az Azure-ban az ilyen kommunikál.

> [!NOTE]
> A telepítési eljárásokat az SAP NetWeaver ABAP rendszerek, Java, és a ABAP + Java rendszerek csaknem azonosak. A legfontosabb különbség, hogy rendelkezik-e az SAP ABAP rendszer ASC egy példánya. Az SAP Java rendszer egy SCS példány van. Az SAP ABAP + Java rendszer rendelkezik egy ASC példány és egy SCS példány fut a Microsoft feladatátvevő fürt csoporton belül. Összes telepítési különbséget minden SAP NetWeaver telepítési verem explicit módon szerepelnek. Feltételezzük, hogy minden egyéb részeinek megegyeznek.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Telepítse a SAP, ha a magas rendelkezésre állású ASC/SCS példánya

> [!IMPORTANT]
> Győződjön meg arról, hogy nem helyezhető el a lapozófájl DataKeeper tükrözött köteteken. DataKeeper nem támogatja a tükrözött kötetek. A lapozófájl az ideiglenes meghajtón D egy Azure virtuális gép, az alapértelmezett hagyhatja. Ha még nincs hiba, a Windows lapozófájlja áthelyezése az Azure virtuális gép D meghajtó.
>
>

Ezeket a feladatokat, ha a magas rendelkezésre állású ASC/SCS példánya SAP telepítése foglal magában:

- Egy virtuális nevet az SAP ASC/SCS fürtözött példány létrehozása
- Az SAP első fürtcsomópontra telepítése
- Az SAP-profilnak ASC/SCS-példány módosítása
- A mintavétel a port hozzáadása
- A Windows tűzfal mintavételi port megnyitása

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Hozzon létre egy virtuális nevet az SAP ASC/SCS fürtözött példány

1.  A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést a ASC/SCS példányának virtuális állomás neve.

  > [!IMPORTANT]
  > Az IP-cím, amikor hozzárendeli a ASC/SCS példányának virtuális állomás neve lehet ugyanaz, mint az Azure Load Balancer rendelt IP-cím (**<*SID*> - lb - ASC **).  
  >
  >

  Az SAP ASC/SCS állomásnév virtuális IP-címe (**pr1-ASC-sap**) ugyanaz, mint az IP-cím az Azure Load Balancer (**pr1-lb-ASC**).

  ![56. ábra: A DNS-bejegyzést a SAP ASC/SCS fürt virtuális nevét és a TCP/IP-cím megadása][sap-ha-guide-figure-3046]

  _**Ábra 56:** a DNS-bejegyzést a SAP ASC/SCS fürt virtuális nevét és a TCP/IP-cím megadása_

2.  A virtuális állomásneve rendelt IP-cím megadásához válassza ki a **DNS-kezelő** > **tartomány**.

  ![57. ábra: Új virtuális nevét és TCP/IP-cím SAP ASC/SCS fürtnek megfelelő konfiguráció][sap-ha-guide-figure-3047]

  _**57. ábra:** új virtuális nevét és a TCP/IP-címtartományok SAP ASC/SCS fürtnek megfelelő konfiguráció_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Az SAP első fürtcsomópontra telepítése

1.  Az első fürt csomópont lehetőség fürtcsomóponton A. végrehajtása Ha például a **pr1-ASC-0** állomás.
2.  Az alapértelmezett portok az Azure belső terheléselosztóhoz, jelölje be:

  * **ABAP rendszer**: **ASC** szám példány **00**
  * **Java-rendszer**: **SCS** szám példány **01**
  * **ABAP + Java rendszer**: **ASC** szám példány **00** és **SCS** szám példány **01**

  A ABAP ASC példányhoz, és a Java SCS példány 01 példány 00 eltérő számú portok használatához először módosítani szeretné az Azure belső alapértelmezett terheléselosztási szabályok, leírt [ASC/SCS alapértelmezett terheléselosztási szabályok módosítása az Azure belső terheléselosztó][sap-ha-guide-8.9].

A következő néhány feladatot a szabványos SAP-dokumentáció nem ismerteti.

> [!NOTE]
> Az SAP-dokumentáció ASC/SCS fürt első csomópontjára telepítését ismerteti.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Az SAP-profil ASC/SCS-példány módosítása

Akkor hozzon létre egy új profil paraméter. A profil paraméter megakadályozza, hogy az SAP-munkafolyamatok és a sorba helyezni kiszolgáló közötti kapcsolat bezárása, ha túl sokáig üresjáratban. Jelenleg a probléma esetén említett [beállításjegyzék-bejegyzések hozzáadása az SAP ASC/SCS példány mindkét fürtcsomóponton][sap-ha-guide-8.11]. A szakaszt azt is vezette be két módosítások néhány alapvető TCP/IP-kapcsolat paraméterekhez. Egy második lépésben be kell állítani a sorba helyezni kiszolgáló küld egy `keep_alive` jelezze, hogy a kapcsolatok nem elérte az Azure belső elosztott terhelésű üresjárati küszöbértéket.

Az SAP-profil ASC/SCS-példány módosítása:

1.  Ez a profil paraméter hozzáadása az SAP ASC/SCS példány profilhoz:

  ```
  enque/encni/set_so_keepalive = true
  ```
  A fenti példában az elérési út:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Például, hogy a SAP SCS példány profil és a megfelelő elérési út:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  A módosítások alkalmazásához, indítsa újra az SAP ASC /SCS példányt.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adjon hozzá egy mintavételi portot

A belső terheléselosztó mintavételi funkció segítségével a teljes fürt konfigurációs Azure terheléselosztó dolgozni. Az Azure belső terheléselosztó általában a bejövő terhelés részt vevő virtuális gépek között egyenlően osztja el. Azonban ez nem fog működni az egyes fürtkonfigurációk mert csak egy példány aktív. A többi példány passzív, és a munkaterhelés nem tudja elfogadni. A mintavételi funkció segít, amikor az Azure belső terheléselosztó munkahelyi csak egy aktív példány számára. A mintavétel-szolgáltatásával a belső terheléselosztó előfordulások aktív, és ezután célpéldányának csak a munkaterheléssel képes észlelni.

A mintavételi portot hozzáadása:

1.  Ellenőrizze az aktuális **ProbePort** beállítása a következő PowerShell-parancs futtatásával. A fürtkonfiguráció le a virtuális gépek egyik végrehajtást.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  A mintavételi portot határozza meg. Az alapértelmezett mintavételi portszám **0**. A jelen példában használjuk a mintavételi portot **62000**.

  ![58. ábra: A fürt konfigurációs mintavételi portot pedig 0 alapértelmezés szerint][sap-ha-guide-figure-3048]

  _**58. ábra:** fürt konfigurációs mintavételi törölve: 0_

  A portszám SAP Azure Resource Manager-sablonok van meghatározva. A port számát a PowerShellben rendelhet hozzá.

  Egy új ProbePort értéket az a  **SAP <*SID*> IP ** fürterőforrás, futtassa a következő PowerShell-parancsfájlt. A környezet PowerShell változói frissítése. A parancsfájl futtatása után a rendszer kérni fogja újraindítja a SAP fürtcsoport a változások életbe lépjenek.

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

  Miután kapcsolása a  **SAP <*SID*> ** fürtön a szerepkör hálózatra, ellenőrizze, hogy **ProbePort** az új értékre van beállítva.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![59. ábra: Miután beállította az új érték mintavétel-a fürt port][sap-ha-guide-figure-3049]

  _**59. ábra:** az új érték beállítása után, mintavételi modulja a fürt port_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Nyissa meg a Windows tűzfal mintavételi portot

Nyissa meg a Windows tűzfal mintavételi portot mindkét fürtcsomóponton kell. A következő parancsfájl segítségével nyissa meg a Windows tűzfal mintavételi portot. A környezet PowerShell változói frissítése.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** értéke **62000**. Most már hozzáférhet a fájlmegosztáshoz  **\\\ascsha-clsap\sapmnt** a többi, például mert a **ascsha-dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Az adatbázis-példány telepítése

Az adatbázispéldány fölött telepítéséhez kövesse a SAP dokumentáció ismertetett folyamatot.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>A második fürt csomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatóban.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Módosítsa a SAP SSZON Windows szolgáltatáspéldány indítási típusa

A SAP SSZON Windows szolgáltatás indítási típusának módosítása **automatikus (Késleltetett indítás)** mindkét fürtcsomóponton.

![60. ábra: Az SAP SSZON példány szolgáltatás típusának módosítása késleltetett automatikusra][sap-ha-guide-figure-3050]

_**60. ábra:** az SAP SSZON példányt késleltetett automatikus módosíthatja a szolgáltatás típusa_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Az SAP elsődleges alkalmazáskiszolgáló telepítése

Az elsődleges alkalmazás kiszolgáló (szolgáltatói)-példány telepítése <*SID*> - di - 0-fiókjához kijelölt üzemeltetéséhez a szolgáltatói CÍMEK a virtuális gépen. Nincsenek függőségek a Azure vagy DataKeeper-specifikus beállításokat.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Az SAP további alkalmazáskiszolgáló telepítése

Telepítse az SAP további Application Server (AAS) üzemeltetésére SAP Application Server-példány már a kijelölt virtuális gépek. Például a <*SID*> - di - 1 a <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver rendszer telepítését. A következő folytatásához feladatátvételi tesztelése.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Az SAP ASC/SCS példány feladatátvétel és SIOS replikációs tesztelése
Akkor is könnyen a tesztelése egy SAP ASC/SCS-példány feladatátvevő és SIOS lemez replikációs Feladatátvevőfürt-kezelő és a SIOS DataKeeper kezelési és konfigurációs eszköz használatával.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>A fürtcsomóponton SAP ASC/SCS-példány fut.

A **SAP PR1** fürtcsoport fürtcsomóponton A. fut. Például a **pr1-ASC-0**. Rendelje hozzá a megosztott lemezmeghajtó S, amely része a a **SAP PR1** fürtcsoportot, és az ASC/SCS-példány használja, a fürt csomópont azonosítójához.

![61. ábra: Feladatátvevőfürt-kezelő: < SID > a SAP fürtcsoport A csomóponton fut.][sap-ha-guide-figure-5000]

_**61. ábra:** Feladatátvevőfürt-kezelő: az SAP <*SID*> fürtcsoport A csomóponton fut._

A SIOS DataKeeper felügyelete és konfigurálása eszköz megtekintheti, hogy a megosztott lemez adatainak rendszer szinkron módon replikálja a csomóponton A forrás-kötet meghajtó S a kötet célmeghajtó S fürtcsomópontra a b kiszolgálóra. Például, hogy a rendszer replikálja a **pr1-ASC-0 [10.0.0.40]** való **pr1-ASC-1 [10.0.0.41]**.

![62. ábra: SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról A B-fürt csomópontjának][sap-ha-guide-figure-5001]

_**62. ábra:** SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról A B-fürt csomópontjának_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>B csomópont-csomópont A feladatátvétel

1.  Válasszon egyet ezek közül a SAP a feladatátvétel kezdeményezése <*SID*> fürtcsoport fürtcsomópontról A b-fürt csomópontjának
  - Feladatátvevőfürt-kezelővel  
  - Feladatátvevő fürt PowerShell használata

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Indítsa újra a fürt csomópont A a Windows vendég operációs rendszerben (a SAP, az automatikus feladatátvételt kezdeményez <*SID*> fürtcsoport csomópontból A csomópont B).  
3.  Indítsa újra a fürt csomópont A Azure-portálról (egy, a SAP automatikus feladatátvételt kezdeményez <*SID*> fürtcsoport csomópontból A csomópont B).  
4.  Indítsa újra a fürt csomópont A Azure PowerShell használatával (ez elindít egy automatikus feladatátvétel, a SAP <*SID*> fürtcsoport csomópontból A csomópont B).

  Feladatátvétel után a SAP <*SID*> fürtcsoport fürtcsomóponton b fut. Futó például **pr1-ASC-1**.

  ![63. ábra: A Feladatátvevőfürt-kezelő, a SAP < SID > fürtcsoport van fürtcsomóponton futó B][sap-ha-guide-figure-5002]

  _**Ábra 63**: A Feladatátvevőfürt-kezelő, a SAP <*SID*> fürtcsoport fürtcsomóponton B fut._

  A megosztott lemez már csatlakoztatva van a fürt csomópont b SIOS DataKeeper replikálódik adatok forrás kötet meghajtóról S fürtcsomóponton B célmeghajtó kötet, S fürtcsomóponton A. A replikáló például **pr1-ASC-1 [10.0.0.41]** való **pr1-ASC-0 [10.0.0.40]**.

  ![Ábra 64: SIOS DataKeeper replikálja a helyi kötet fürtcsomópontból B csomópont A fürt][sap-ha-guide-figure-5003]

  _**Ábra 64:** SIOS DataKeeper replikálja a helyi kötet fürtcsomópontból B csomópont A fürt_
