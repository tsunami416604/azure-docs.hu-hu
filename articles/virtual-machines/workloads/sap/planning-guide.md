---
title: Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver |} Microsoft Docs
description: Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6b01c2a0bfacb4e7e121cdb06ad8be4ab17d53a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver
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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

A Microsoft Azure lehetővé teszi a vállalatok szerezni a számítási és tárolási erőforrásokat minimális időben hosszadalmas beszerzési ciklusok nélkül. Az Azure virtuális gépek lehetővé teszi a vállalatok például SAP NetWeaver-alapú alkalmazások az Azure klasszikus alkalmazások, központi telepítéséhez, és a megbízhatóság és rendelkezésre állás kiterjesztése anélkül, hogy további erőforrás érhető el a helyszíni. Az Azure virtuális gép szolgáltatások közötti kapcsolatot nyújthassanak, amely lehetővé teszi a vállalatok Azure virtuális gépek aktívan integrálja a helyi tartomány, a Magánfelhők és az SAP rendszer fekvő is támogatja.
Ez a dokumentum ismerteti a Microsoft Azure virtuális gép alapjait és biztosít egy SAP NetWeaver telepítések az Azure-ban tervezési és megvalósítási szempontok segédlet és ilyen kell tényleges megkezdése előtt olvassa el a dokumentumot SAP NetWeaver Azure példányai.
A dokumentum kiegészíti az SAP-dokumentáció és az SAP, az elsődleges erőforrások telepítése és a SAP szoftver központi telepítését megfelelnek a megadott platformok.

## <a name="summary"></a>Összegzés
A felhőalapú informatika egy kifejezést gyakran használják, amely legfeljebb nemzetközi és nagy vállalatok kis vállalatok egyre több fontosságát az informatikai ágazatban van eszközöket.

Microsoft Azure a Microsoft, széles skálája új lehetőségeket kínál, amelyek Felhőplatform szolgáltatások. Most már az ügyfelek képesek gyors kiépítése és deaktiválás rendelkezés alkalmazások felhőalapú szolgáltatásként úgy, hogy azok nem technikai vagy költségvetési korlátozások korlátozódik. Helyett befektetés időt és a hardver infrastruktúra, a vállalatok összpontosíthatnak, az alkalmazás, az üzleti folyamatokat és az előnye az ügyfelek és felhasználók.

A Microsoft Azure Virtual Machine Services szolgáltatással a Microsoft átfogó szolgáltatott infrastruktúra (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). E tanulmány tervezésével és megvalósításával SAP NetWeaver-alapú alkalmazások a Microsoft Azure-ban választott platformként ismerteti.

Maga a dokumentum elsősorban két fő szempontjait:

* Az első részt az Azure két támogatott üzembe helyezési mintát SAP NetWeaver-alapú alkalmazások ismerteti. Azt is bemutatja az SAP üzemelő példányokkal figyelembe Azure általános kezelését.
* A második rész adatokat az első részében leírt két különböző forgatókönyv megvalósításához.

További források című fejezet [erőforrások] [ planning-guide-1.2] ebben a dokumentumban.

### <a name="definitions-upfront"></a>Definíciók előzetes megfizetése esetén
A dokumentumban a következő kifejezéseket használjuk:

* Infrastruktúra-szolgáltatási: Szolgáltatott infrastruktúra
* A PaaS: Platformok
* SaaS: Szolgáltatott szoftver
* ARM: Az Azure Resource Manager
* SAP összetevő: egy egyedi SAP alkalmazás, például ECC, a Sávszélesség, a megoldás Manager vagy a EP  SAP-összetevők a hagyományos ABAP vagy Java technológiák vagy egy nem NetWeaver alapú alkalmazás, például üzleti objektumok is alapulhat.
* SAP-környezetben: egy vagy több SAP összetevők logikailag egy csoportba egy üzleti funkció, például a fejlesztés, QAS, képzési, vész-Helyreállítási vagy éles.
* SAP fekvő: Ez vonatkozik az egész SAP eszközök az ügyfél informatikai fekvő. Az SAP fekvő minden üzemi és nem éles környezetben tartalmaz.
* SAP-verzió: az adatbázis-kezelő réteg és kombinációja alkalmazás réteget, például egy SAP ERP fejlesztőrendszer, SAP BW gazdagépes tesztrendszer, SAP CRM éles rendszer stb.. Azure-környezetekhez, a nem támogatott a két réteg között a helyszíni és az Azure felosztása. Ez azt jelenti, hogy egy SAP rendszer vagy a helyszínen telepített vagy telepítve van az Azure-ban. Azonban telepítheti a másik egy SAP fekvő Azure vagy a helyszíni rendszeren. Például sikerült telepíteni a SAP CRM-fejlesztői és tesztrendszerek Azure, de az SAP CRM éles rendszer helyszíni.
* Csak felhőalapú telepítési: egy központi telepítést, amikor az Azure-előfizetés nem csatlakozik a pont-pont vagy ExpressRoute-kapcsolat a helyszíni hálózati infrastruktúrára. Közös Azure dokumentációja az ilyen típusú központi telepítések egyaránt "Csak felhőalapú" telepítések leírása. Ezzel a módszerrel telepített virtuális gépek az interneten, és egy nyilvános IP-címet és/vagy egy nyilvános DNS-nevet a virtuális gépeket az Azure-ban rendelt keresztül érhetők el. A Microsoft Windows a helyszíni Active Directory (AD) és a DNS nem bővítette ki az Azure-ba, az ilyen típusú központi telepítések. Ezért a virtuális gépek nincsenek a helyszíni Active Directory részét. Is igaz Linux-használ, például OpenLDAP + Kerberos hitelesítés megvalósításához.

> [!NOTE]
> Ebben a dokumentumban csak felhőalapú központi telepítés meghatározott teljes SAP tájak kizárólag az Azure Active Directory kiterjesztés nélkül futnak / OpenLDAP vagy a nyilvános felhőbe helyszíni névfeloldás. Csak felhőalapú konfigurációk nem támogatottak a termelési SAP rendszerek vagy konfigurációk, ahol az SAP STM vagy más helyszíni erőforrásokat kell használható Azure és a helyszínen található erőforrások üzemeltetett SAP-rendszerek között.
>
>

* Létesítmények közötti: Bemutatja egy olyan forgatókönyvet, ahol a virtuális gépek Azure-előfizetéshez, amely rendelkezik pont-pont, többhelyes vagy a helyszíni datacenter(s) és az Azure között ExpressRoute kapcsolat van telepítve. Közös Azure dokumentációja, az ilyen típusú központi telepítések egyaránt létesítmények közötti forgatókönyv leírása. A kapcsolat oka, hogy a helyi tartomány, a helyszíni Active Directory/OpenLDAP és a helyi DNS kiterjeszti Azure. A helyszíni fekvő az időtartam, az előfizetéshez tartozó Azure eszközökre. Ha ezt a bővítményt, a virtuális gépek a helyi tartomány része lehet. A helyi tartomány tartományi felhasználók férhetnek hozzá a kiszolgálókat, és szolgáltatásokat futtathatja virtuális gépek (például adatbázis-kezelő szolgáltatás). Telepített virtuális gépek a helyszíni és az Azure telepített virtuális gépek közötti kommunikációt és a névfeloldás lehetőség. Ez a forgatókönyv a legtöbb SAP eszközök telepítendő várhatóan. További információkért lásd: [ez] [ vpn-gateway-cross-premises-options] cikk és [ez][vpn-gateway-site-to-site-create].

> [!NOTE]
> Létesítmények közötti telepítések SAP rendszert futtató Azure virtuális gépek esetén a helyszíni-tartománybeli tagsággal SAP rendszerek éles SAP rendszerek támogatottak. Létesítmények közötti konfigurációk támogatottak a kijelzők telepítése, vagy végezze el az SAP tájak az Azure. A teljes SAP fekvő az Azure-ban futó szükség van a helyszíni tartományi és ADS/OpenLDAP tartozó virtuális gépek. Hibrid-IT-forgatókönyvekkel kapcsolatos megtartásról korábbi verzióiban a dokumentációt, ha a kifejezés *hibrid* a tényt, hogy van-e a létesítmények közötti kapcsolat a helyszíni és az Azure közötti feltörték. Plusz a tényt, hogy a virtuális gépeket az Azure-ban a helyszíni Active Directory részét / OpenLDAP.
>
>

Néhány Microsoft dokumentációjában létesítmények közötti forgatókönyv különösen az adatbázis-kezelő magas rendelkezésre ÁLLÁSÚ konfiguráció egy kicsit másképp ismerteti. Az SAP-kapcsolódó dokumentumok esetében a létesítmények közötti forgatókönyv éppen forrni kezd, hogy a pont-pont vagy magán (ExpressRoute) kapcsolatot és azt a tényt, hogy a SAP fekvő elosztása a helyszíni és az Azure között.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Erőforrások
Az alábbi kiegészítő útmutatók érhetők el a témakör az SAP-telepítések az Azure-on:

* [Az Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver (Ez a dokumentum)][planning-guide]
* [SAP NetWeaver Azure virtuális gépek központi telepítését][deployment-guide]
* [Az SAP NetWeaver Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]

> [!IMPORTANT]
> Ahol csak lehetséges egy hivatkozást a hivatkozó SAP telepítési útmutató használatos (hivatkozás InstGuide-01, lásd: <http://service.sap.com/instguides>). Az Előfeltételek és a telepítési folyamat ismét, az SAP NetWeaver telepítési útmutatók mindig kell olvassa el, mivel ez a dokumentum csak hozzá van rendelve egy Microsoft Azure virtuális gépen telepített SAP NetWeaver rendszerek az egyes feladatok.
>
>

Az alábbi SAP megjegyzések Azure SAP témakör kapcsolódnak:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |Az Azure-on SAP-alkalmazásokból: támogatott termékek és méretezése |
| [2015553] |A Microsoft Azure SAP: Előfeltételek támogatja |
| [1999351] |Hibaelhárítási továbbfejlesztett Azure megfigyelése az SAP |
| [2178632] |Kulcs figyelési metrikákat az SAP, a Microsoft Azure |
| [1409604] |A Windows virtualizálási: fokozott figyelése |
| [2191498] |Az Azure Linux SAP: fokozott figyelése |
| [2243692] |A Microsoft Azure (IaaS) virtuális gép Linux: SAP licenc problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7.x: telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítése és frissítése |
| [1597355] |A Linux rendszerhez használt lapozóterület javaslat |

Emellett olvassa el a [Állapotváltozás Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) Linux tartalmazó összes SAP-jegyzetet.

Általános alapértelmezett korlátozások és az Azure-előfizetések maximális korlátai található [Ez a cikk][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>A következő eljárások
SAP gyakran előforduló, a legtöbb létfontosságú alkalmazások belül vállalatok számára rendelkezésre álló. Az architektúra és a műveletek az alkalmazások többnyire nagyon bonyolult, és győződjön meg arról, hogy megfelel a követelményeknek, a rendelkezésre állás és teljesítmény fontos.

Így a vállalat rendelkezik gondolja, hogy alaposan arról, hogy mely alkalmazások futtathatja a kiválasztott felhő szolgáltató független nyilvános felhő környezetben.

SAP NetWeaver telepítéséhez lehetséges rendszertípusok-alapú alkalmazások nyilvános felhőben környezetekben alábbiakban találhatók:

1. Közepes méretű éles rendszerek esetén.
2. Fejlesztési rendszerek
3. Tesztelési rendszerek
4. Prototípus rendszerek
5. Tanulási / bemutató rendszerek

Sikeresen üzembe helyezésének SAP rendszerek Azure IaaS vagy infrastruktúra-szolgáltatási általában, fontos a hagyományos outsourcers vagy a szolgáltatók által kínált és infrastruktúra-szolgáltatási ajánlatok között jelentős különbségek megismeréséhez. A hagyományos szolgáltató vagy outsourcer alkalmazkodik (hálózati, tárolási és a kiszolgáló típusa) infrastruktúra a munkaterhelés egy ügyfél szeretne futtatni, mivel feladata, ehelyett az ügyfél kiválaszthatja a megfelelő munkaterhelés IaaS-telepítésekhez.

Első lépésként szükséges ellenőrizze az alábbiakat:

* Az SAP támogatott Azure VM típusai
* Az SAP termékek/verziókban támogatott Azure-on
* A támogatott operációs rendszer és adatbázis-kezelő feloldja az adott SAP kiadásokban az Azure-ban
* Különböző Azure-SKU által biztosított SAP átviteli sebesség

Ezekre a kérdésekre adott válaszokat az SAP megjegyzés olvasható [1928533].

A második lépésben az Azure erőforrás- és sávszélesség korlátozások kell a helyszíni rendszerekben tényleges erőforrás-felhasználásának össze. Ezért szükséges lehet az Azure SAP területén a támogatott típusok különböző lehetőségeinek megismerése:

* Virtuális gép különböző típusú a CPU és memória-erőforrásokat és
* Virtuális gép különböző típusú IOPS sávszélesség és
* Virtuális gép különböző típusú hálózati képességeit.

Az adatok legnagyobb található [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Ne feledje, hogy a fenti hivatkozás szerepel a korlátozások felső határa. Azt nem jelenti azt, hogy a korlátok az összes olyan erőforrást, például IOPS biztosítható, hogy minden esetben. A kivételek, ha a CPU és memória-erőforrások a kiválasztott Virtuálisgép-típussá áll. A SAP által támogatott virtuális gép esetében a CPU és memória-erőforrások fogyasztás belül a virtuális gép számára fenntartott és használja, így bármikor elérhető.

A Microsoft Azure platformon IaaS más platformokon, például a egy több-bérlős platformja. Ez azt jelenti, hogy a tárolási, hálózati és egyéb erőforrások megosztott bérlők között. Intelligens sávszélesség-szabályozás és a kvóta logika szerint egy bérlői megakadályozza más bérlőket (zajos szomszédos) teljesítményét érintő drasztikus módon. Bár az Azure logic megpróbálja eltérésekre vegye észlelt sávszélesség alacsony, magas megosztott platformokhoz általában nagyobb eltérések az erőforrás vagy a sávszélesség rendelkezésre állása, mint a helyszíni központi telepítések segítségével számos ügyfél telepítéséhez. Ennek köszönhetően Ön is szembesülhet kapcsolatban (a kötet, valamint késés) hálózati vagy tárolási I/O sávszélesség különböző szintjei perc perc. A valószínűsége annak, hogy az SAP rendszer Azure szabályos hibába eltérésekre nagyobb, mint a helyszíni rendszer kell figyelembe kell venni.

Utolsó lépés a rendelkezésre állási követelmények kiértékeléséhez. Előfordulhat, hogy az alapul szolgáló Azure-infrastruktúra frissített, valamint újra kell indítani a virtuális gépeket futtató gazdagépek szükséges. Ezekben az esetekben meg azokon a gazdagépeken futó virtuális gépeket szeretné kell leállításra és újraindításra is. Ilyen karbantartás ütemezése munkaidejében kiegészítő egy adott régióban történik, de viszonylag nagy a potenciális ablak néhány óra során, ami egy újraindul. Nincsenek belül vagy azok egy részét a frissítések hatásának mérsékléséhez konfigurálható az Azure platformon különböző technológiákat. Jövőbeli fejlesztések a Azure platformon, az adatbázis-kezelő és az SAP alkalmazás tervezték, hogy az ilyen újraindítást gyakorolt hatásának minimalizálása érdekében.

Ahhoz, hogy egy SAP Azure rendszer sikeres telepítéséhez a helyszíni SAP rendszer(ek) operációs rendszer, adatbázis, és SAP-alkalmazásokból szerepelnie kell a SAP Azure támogatási mátrix a fér belül az erőforrásokat az Azure infrastruktúra biztosíthat és amely is a rendelkezésre állási SLA-k a Microsoft Azure ajánlatokat. Ezekhez a rendszerekhez meghatározott kell döntenie, hogy a következő két üzembe helyezési forgatókönyveket egyik.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Csak felhőalapú - függőségek a helyszíni ügyfél hálózaton nélkül az Azure virtuális gépek telepítéséhez
![Az SAP bemutató vagy Azure szolgáltatásba telepített képzési forgatókönyv egyetlen VM][planning-guide-figure-100]

Ez a forgatókönyv része jellemzően oktatási anyag vagy bemutató rendszerek, ahol telepítve vannak a SAP és nem SAP szoftverek összes összetevői belül egy virtuális. Éles SAP rendszerek nem támogatottak ebben a telepítési forgatókönyvben. Ebben a forgatókönyvben általában megfelel az alábbi követelményeknek:

* A virtuális gépek magukat a nyilvános hálózaton keresztül érhetők el. A bemutatók vagy oktatási anyag tartalmát, vagy az ügyfél a tulajdonos vagy a vállalat a helyszíni hálózatra a virtuális gépeken futtatott alkalmazások közvetlen kapcsolattal nincs szükség.
* Esetén a oktatási anyag vagy bemutató forgatókönyvet képviselő több virtuális gép hálózati kommunikációt és a névfeloldás kell a virtuális gépek is működnek. De a beállított virtuális gépek közötti kommunikáció kell lehessen, hogy a virtuális gépek több készleteinek egymás mellett beavatkozás nélkül telepíthetők.  
* Internetkapcsolat szükség a végfelhasználó számára az Azure-ban üzemeltetett virtuális gépek való távoli bejelentkezés. Attól függően, hogy a vendég operációs rendszer, Terminal Services/távoli asztali szolgáltatások vagy VNC ssh eléréséhez használt a virtuális Gépet, a képzési feladatok teljesítése, vagy hajtsa végre a különböző demók. Ha az SAP 3200, 3300 & 3600 részleg például portok is elérhetővé tehető az SAP alkalmazáspéldány elérhető bármely internethez csatlakoztatott asztalról.
* Az SAP rendszer(ek) (és a VM(s)) jelentik a önálló Azure, amely csak a végfelhasználói hozzáférése nyilvános internetkapcsolatra van szükség, és nem szükséges más virtuális gépek Azure-ban való kapcsolat esetén.
* SAPGUI és a böngésző telepítve és fut, közvetlenül a virtuális Gépen.
* A gyors alaphelyzetbe állítani a virtuális gépek az eredeti állapotra és új központi telepítését az eredeti állapotban ismét szükség.
* Bemutató és képzési forgatókönyvek esetében több virtuális gép, egy Active Directory amely vannak megvalósítani / OpenLDAP és/vagy a DNS szolgáltatásra szükség az egyes virtuális gépek.

![A virtuális gép egy bemutató vagy egy Azure-Felhőszolgáltatásban képzési forgatókönyv képviselő csoport][planning-guide-figure-200]

Fontos vegye figyelembe, hogy a virtuális gép van, a készlet minden egyes kell párhuzamosan, ahol a virtuális gép nevét, a készlet minden egyes azonosak telepíthető.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Létesítmények közötti - telepítését egy vagy több SAP-virtuális gép az Azure-e a követelményeknek, alatt teljesen integrálva a helyszíni hálózat
![A pont-pont kapcsolatban (létesítmények közötti) VPN][planning-guide-figure-300]

Ebben a forgatókönyvben a létesítmények közötti forgatókönyv számos lehetséges telepítési mintákat. Egyszerűen fut az egyes részei a SAP fekvő a helyszíni és az SAP többi részével fekvő Azure leírható. Az SAP-összetevők része az Azure-on futnak tény minden szempontját kell lennie átlátszó, a végfelhasználók számára. Ezért a SAP átviteli javítási rendszer (STM) RFC kommunikációs, nyomtatási, (például egyszeri bejelentkezés) biztonsági, stb zökkenőmentesen működjön az Azure-on futó SAP rendszerekhez. De a létesítmények közötti forgatókönyv is egy olyan forgatókönyvet, ahol a teljes SAP fekvő futtatja az Azure-ban a felhasználói tartomány és a DNS kiterjeszthetőek az Azure ismerteti.

> [!NOTE]
> Ez az a környezetben, amely hatékony SAP rendszereket futtató támogatott.
>
>

Olvasási [Ez a cikk] [ vpn-gateway-create-site-to-site-rm-powershell] bővebben a helyszíni hálózat csatlakoztatása a Microsoft Azure

> [!IMPORTANT]
> Azt is van szó létesítmények közötti forgatókönyv Azure és a helyszíni felhasználói telepítés között, azt keresi, az egész SAP rendszerek részletességű összegzése. Forgatókönyvek, amelyek *nem támogatott* létesítmények közötti forgatókönyv esetén:
>
> * Különböző központi telepítési módszer különböző rétegeinek SAP-alkalmazásokból fut. A DBMS réteg a helyszínen, de az SAP alkalmazásréteg például fordítva vagy Azure virtuális gépeken telepített virtuális gépek futnak.
> * Azure-ban és egy helyszíni SAP réteg néhány összetevőt. Például a felosztás az SAP alkalmazásréteg-hez és az Azure virtuális gépek közötti példányai.
> * SAP példányát egy olyan rendszert több Azure-régiókat felett futó virtuális gépeket eloszlásáról nem támogatott.
>
> Ezek a korlátozások oka egy SAP rendszerben, különösen az alkalmazáspéldányok és az adatbázis-kezelő réteg az SAP rendszer között nagy teljesítményű nagyon alacsony késleltetésű hálózathoz vonatkozó követelmény.
>
>

### <a name="supported-os-and-database-releases"></a>Támogatott operációs rendszer és adatbázis-kiadások
* Microsoft server szoftver esetében az Azure virtuális gép szolgáltatásokat, ez a cikk szerepel a támogatott: <http://support.microsoft.com/kb/2721672>.
* Támogatott operációs rendszer kiadások, adatbázis rendszer verziókban támogatott Azure virtuális gép szolgáltatások SAP szoftver együtt vannak dokumentálva SAP Megjegyzés [1928533].
* SAP-alkalmazásokból és -verziókat támogatja a virtuális gép Azure-szolgáltatásokra ismertetett SAP Megjegyzés [1928533].
* Csak 64 bites bináris fiókként szándékozik futtatni az Azure-ban a Vendég virtuális gépek SAP forgatókönyvek esetén támogatottak. Ez azt is jelenti, hogy csak a 64 bites SAP-alkalmazásokból és az adatbázisok támogatottak.

## <a name="microsoft-azure-virtual-machine-services"></a>Virtuális gép Microsoft Azure-szolgáltatások
A Microsoft Azure platform az egy internet-skálázási szolgáltatások felhőplatform birtokolt, és a Microsoft azon adatközpontjainak üzemeltetni. A platform a Microsoft Azure virtuális gép szolgáltatások (infrastruktúra-szolgáltatás, vagy IaaS) és a gazdag platformok egy Platformszolgáltatási képességek egy készletét tartalmazza.

Az Azure platformon csökkenti a kezdeti technológia szükségességét, és infrastruktúra a későbbi szoftvervásárlások. Egyszerűbbé teszi az karbantartásáért, valamint működő alkalmazások igény szerinti számítási és tárolási, méretezhető, és a webes alkalmazás és a csatlakoztatott alkalmazások kezelése megadásával. Infrastruktúra-kezelés a automatizált egy platform, amely a magas rendelkezésre állás és a dinamikus méretezés beállítással, a használatalapú árképzési modellt használati igényeinek megfelelően.

![A Microsoft Azure virtuális gép szolgáltatások elhelyezéséhez][planning-guide-figure-400]

Az Azure virtuális gép Services Microsoft van így lehetővé teszi egyéni kiszolgálói lemezképek központi telepítése az Azure-ba (lásd a 4. ábra) IaaS-példányként. A virtuális gépek Azure lehet a vendég operációs rendszer különböző operációs rendszert futtató, és a Hyper-V virtuális merevlemezek (VHD) alapulnak.

Működési szempontból az Azure virtuális gép szolgáltatás kínál hasonló feladatait, a helyszínen telepített virtuális gépek. Azonban az jelentős előnyt, akkor be kell szereznie, felügyelhetik és kezelhetik az infrastruktúrát nincs szükségük van. A fejlesztők és rendszergazdák jogköre teljes. az operációs rendszer lemezképének a virtuális gépekről. Rendszergazdák jelentkezhetnek be távolról karbantartási és hibaelhárítási feladatok, valamint a szoftverfrissítések telepítési feladatok végrehajtásához azon virtuális gépek számára. Fürttelepítésben tekintetében a csak korlátozások vonatkoznak, a méretek és az Azure virtuális gépek képességeit. Ezek nem lehet a finom konfigurációban részletes, mivel ezt megteheti a helyszínen. A Virtuálisgép-típusokon kombinációját jelentik választási lehetőség van:

* Vcpu, száma
* Memória
* Csatolt, VHD-k száma
* Hálózati és tárolási sávszélesség.

A méret és a különböző virtuális gépek különböző méretű korlátozások érhető el a táblázatban látható [Ez a cikk (Linux)] [ virtual-machines-sizes-linux] és [Ez a cikk (Windows)] [virtual-machines-sizes-windows].

Vegye figyelembe, hogy számos különböző családok vagy azokat a virtuális gépek. A következő virtuális gépek családok képes megkülönböztetni:

* VM a0-A7 csomag típusok: csak az SAP hitelesít. Első Virtuálisgép-sorozat Azure IaaS bevezetett kapott.
* VM a8-A11 csomag típusok: nagy teljesítményű számítástechnikai példányok. Különböző jobb végrehajtása futtatásának gazdagépek, mint más A-sorozatú virtuális gépek számítási.
* D/Dv2-sorozat Virtuálisgép-típusokon: A0-A7-nál nagyobb végrehajtása. A virtuális gép közül nem mindegyik az SAP hitelesít.
* DS/DSv2-méretek Virtuálisgép-típusokon: hasonló D/Dv2-sorozat, de biztosan csatlakozni tudjanak a prémium szintű Azure Storage (című [prémium szintű Azure Storage] [ planning-guide-3.3.2] a jelen dokumentum). Újra nem minden virtuális gép esetében az SAP hitelesít.
* G-sorozat Virtuálisgép-típusokon: felső memóriaterület Virtuálisgép-típusokon.
* GS sorozatnak Virtuálisgép-típusokon: G-sorozat hasonló, de többek között a prémium szintű Azure Storage lehetőséget (című [prémium szintű Azure Storage] [ planning-guide-3.3.2] a jelen dokumentum). Adatbázis-kiszolgálók GS sorozatnak virtuális gépeket használ, esetén kötelező a prémium szintű Storage DB adatok és a tranzakciós naplófájlok

Ugyanaz a CPU és memória-konfigurációk másik Virtuálisgép-sorozat tapasztalhatja. Mindazonáltal a másik adatsorozathoz kívüli virtuális gépeken átviteli teljesítményt keressük előfordulhat, hogy térnek el egymástól jelentősen. Annak ellenére, hogy ugyanazt a CPU és memória konfigurációt. Oka az, hogy a mögöttes állomás kiszolgáló hardver, a virtuális gép különböző bevezetése volt-e a különböző átviteli jellemzőit.  Általában a különbség a látható teljesítménye szempontjából is megjelenik a különböző virtuális gépek árát.

Nem minden más Virtuálisgép-sorozat előfordulhat, hogy lehet érhető el az Azure-régiókat, (az Azure-régiókat lásd a következő fejezet) mindegyiknél. Ügyeljen arra is, hogy nem minden virtuális gép vagy Virtuálisgép-sorozat hitelesített SAP.

> [!IMPORTANT]
> SAP NetWeaver alapuló alkalmazások használatára, csak a Virtuálisgép-típusokon és konfigurációk részét felsorolt SAP Megjegyzés [1928533] támogatottak.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-régiók
A Microsoft lehetővé teszi, hogy a virtuális gépek telepítéséhez úgynevezett *Azure-régiókat*. Egy Azure-régiót lehet egy vagy több olyan adatközpontokban közel található. A legtöbb a a világ geopolitikai régiók Microsoft van legalább két Azure-régió. Például a Európa esetében az Azure területére *Észak-Európa* és egy *Nyugat-Európában*. Ilyen két Azure-régiókat geopolitikai régión belül vannak elválasztva elég jelentős távolság, így természetes vagy technikai katasztrófák nem befolyásolják a két Azure-régió geopolitikai ugyanabban a régióban. Microsoft folyamatosan globálisan fejleszt kimenő geopolitikai különböző régiókban új Azure-régiókat, mivel a Dec 2015-től érhető el további régiókban már bejelentette 20 Azure régiók száma, és ezek a területek száma folyamatosan növekvő. Ügyfélként SAP rendszerek telepíthetők az összes e régiók, beleértve a két Azure-régiókat Kínában. Azure-régiók aktuális naprakész információkat ezen a webhelyen talál: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>A Microsoft Azure virtuális gép koncepció
Microsoft Azure kínál az infrastruktúra szolgáltatási (IaaS) megoldásként hasonló funkciókkal rendelkező virtuális gépek gazdagépen történő helyszíni virtualizálási megoldásaként. Le is tudja létrehozhatnak virtuális gépeket az Azure portálon, PowerShell vagy a parancssori felület, amely is kínál a központi telepítési és felügyeleti képességek.

Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Egy sablon használatával ismételten telepítheti az alkalmazás, minden szakasza során az alkalmazás-életciklus.

Resource Manager-sablonok használatával kapcsolatos további információkat talál itt:

* [Telepítését és kezelését a virtuális gépek Azure Resource Manager-sablonok és az Azure parancssori felület használatával][../../linux/create-ssh-secured-vm-from-template.md]
* [Virtuális gépeket Azure Resource Manager és a PowerShell használatával][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Egy másik érdekes szolgáltatás azt a képességet képek létrehozása a virtuális gépekről, amely lehetővé teszi bizonyos tárházak találhatók, amelyből le is tudja gyorsan üzembe helyezhet virtuálisgép-példányok, amelyek megfelelnek az elvárásainak előkészítéséhez.

A virtuális gépekről lemezképek létrehozásával kapcsolatos további információért megtalálhatók [Ez a cikk (Linux)] [ virtual-machines-linux-capture-image-resource-manager] és [Ez a cikk (Windows)] [ virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Tartalék tartományok
Tartalék tartományok képviselik fizikai egység, a hiba, nagyon szorosan kapcsolódó a fizikai infrastruktúra adatközpontok, és amíg a fizikai panel vagy állvány lehet tekinteni a tartalék tartomány található, a kettő között nincs közvetlen egy az egyhez típusú megfeleltetés.

Több virtuális gép egy SAP rendszer a Microsoft Azure virtuális gép szolgáltatás részeként történő telepítésekor befolyásolhatja az Azure Fabric Controller különböző tartalék tartományok, ezáltal a követelményeinek megfelelő az alkalmazás központi telepítése a A Microsoft Azure SLA-t. A tartalék tartományok egy Azure méretezési egységhez (akár több százszor is a számítási csomópontok vagy a tárolási csomópontokat és a hálózatkezelés gyűjteményét) elosztása vagy a hozzárendelés az adott tartalék tartomány virtuális gépek azonban valami, amelyben nincs közvetlen ellenőrzése. Ahhoz, hogy az Azure fabric controller központi telepítése a virtuális gépek halmaza különböző tartalék tartományok keresztül irányítani, akkor hozzá kell rendelni Azure rendelkezésre állási csoport a virtuális gépek központi telepítéskor. Azure rendelkezésre állási csoportokra vonatkozó további információkért lásd: fejezet [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] ebben a dokumentumban.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Frissítési tartományok
Frissítési tartományok határoz meg egy logikai egységet, meghatározhatja, hogy hogyan egy SAP rendszerben, alkotó SAP-példány a több virtuális gép fut, a virtuális gépek frissül. Frissítés esetén a Microsoft Azure végig kell vinnie a frissítési tartományok egyenként frissítését. Virtuális gépek központi telepítéskor a különböző frissítési tartományok keresztül terjednek, megvédheti a SAP rendszer részben a várható állásidő. Ahhoz, hogy a különböző frissítési tartományok eloszlatva SAP a rendszer a virtuális gépek telepítése Azure kényszeríti, be kell egy adott attribútum az egyes virtuális gépek központi telepítéskor. Tartalék tartományok hasonlóan egy Azure méretezési egység van osztva több frissítési tartományt. Ahhoz, hogy az Azure-hálót tartományvezérlő központi telepítése a virtuális gépek halmaza különböző frissítési tartományok keresztül irányítani, akkor hozzá kell rendelni Azure rendelkezésre állási csoport a virtuális gépek központi telepítéskor. Azure rendelkezésre állási csoportokra vonatkozó további információkért lásd: fejezet [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] alatt.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Az Azure rendelkezésre állási csoportok
Azure virtuális gépeken belül egy Azure rendelkezésre állási csoport az Azure Fabric Controller különböző tartalék és frissítési tartományok terjeszti. Különböző tartalék és tartományok frissítése a telepítési célja, megakadályozhatja, hogy a minden virtuális gép egy SAP rendszer éppen leállítás alatt infrastruktúra karbantartása vagy egy tartalék tartomány belül hiba esetén a. Alapértelmezés szerint virtuális gépek részei egy rendelkezésre állási csoportban. A tulajdonság részvételét a virtuális gépek rendelkezésre állási csoport a központi telepítéskor vagy későbbi egy újrakonfigurálása és a virtuális gépek ismételt telepítése által definiált.

Szeretné megtudni, Azure rendelkezésre állási készletek és a rendelkezésre állási készletek kapcsolódó hiba és a frissítési tartományok módon fogalmát, olvassa el a [Ez a cikk][virtual-machines-manage-availability]

Rendelkezésre állási készletet ARM egy json-sablon használatával lásd: Adja meg [a rest-api specifikációk](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) , és keressen az "availability".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Tárolás: A Microsoft Azure Storage és adatlemezek
Microsoft Azure virtuális gépek hasznosítani különböző típusú tárolókat. Azure virtuális gép szolgáltatások SAP végrehajtásakor fontos tárolási két fő típusai közötti különbségek megismeréséhez:

* Nem állandó, ideiglenes tárolási.
* Az állandó tároló.

A nem állandó közvetlenül csatlakozik a futó virtuális gépek és a számítási csomópontokat, a helyi Egypéldányos tárolás (ideiglenes tároló) található. A méret az üzemelő példány indításakor kiválasztott virtuális gép méretétől függ. A tárolási típus: "volatile", és ezért a lemez inicializálva van-e a virtuálisgép-példány újraindítását követően. Általában a ideiglenes lemezen a lapozófájl méretét, az operációs rendszer található.

- - -
> ![Windows][Logo_Windows] Windows
>
> Az ideiglenes meghajtón a Windows virtuális gépeken csatlakoztatva van a telepített virtuális gépen D:\ meghajtóként.
>
> ![Linux][Logo_Linux] Linux
>
> A Linux virtuális gépeken /mnt/resource vagy /mnt van csatlakoztatva. További részletek itt:
>
> * [Hogyan lehet adatlemezt csatolni egy Linux virtuális gép][virtual-machines-linux-how-to-attach-disk]
> * <https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux#temporary-disk>
>
>

- - -
A tényleges meghajtó nem felejtő, mert első magán a gazdagép-kiszolgálón. Ha a virtuális gép áthelyezését egy újbóli üzembe helyezése (például mert a gazdagép vagy állítsa le, majd indítsa újra a karbantartás) a meghajtó a tartalom elvész. Ezért, nincs lehetőség ezen a meghajtón a fontos adatok tárolására. Ez a tároló típusa szerinti használt adathordozó-típust nagyon különböző teljesítményt nyújt, amely 2015 júniusában frissítésétől tűnik a másik Virtuálisgép-sorozat eltérnek:

* A5-A7: Erősen korlátozott teljesítményét. Nem ajánlott a semmit túl az oldal fájlja
* A8-A11: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség.
* D sorozatú: Majd ezer IOPS nagyon jó teljesítmény jellemzőit és > 1GB/s átviteli sebesség.
* DS-méretek: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség.
* G-sorozat: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség.
* GS sorozatnak: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség.

A fenti utasítások alkalmazzák, a virtuális gép típusú, amelynek az SAP hitelesít. A Virtuálisgép-sorozat kiváló IOPS és átviteli használja ki az egyes adatbázis-kezelő szolgáltatásai által jogosultak. További információkért lásd: a [adatbázis-kezelő telepítési útmutatója][dbms-guide].

Microsoft Azure Storage megőrzött tárolás és a jellemző szintű védelmet és San-alapú tárolón látható redundanciát biztosít. Azure Storage alapján lemezek a virtuális merevlemez (VHD) található, az Azure Storage szolgáltatásainak. A helyi operációsrendszer-lemez (Windows C:\, Linux/dev/sda1) az Azure Storage tárolja, és további köteteket vagy lemezeket a virtuális géphez csatlakoztatott első tárolja, túl.

Akkor lehet feltölteni egy meglévő VHD-t a helyszíni vagy hozzon létre üres néhányat a meglévők közül az Azure-ban, és csatolja azokat a központilag telepített virtuális gépekhez.

Miután létrehozott vagy a virtuális merevlemez feltöltése az Azure Storage csatlakoztatásához, és csatolja azokat egy meglévő virtuális gépet, és másolja a meglévő (nem) virtuális Merevlemezek lehetőség.

Ezen a virtuális merevlemezek megmaradnak, adatokat és változásokkal azokat a rendszer biztonságos, amikor újraindul, és újra létrehozni a virtuálisgép-példányt. Akkor is, ha törölnek egy példányát, a virtuális merevlemezek biztonságban és is újra kell telepíteni, vagy nem operációsrendszer lemezek esetén csatlakoztathatók más virtuális gépek.

Az Azure Storage a hálózaton belül különböző redundancia szintek konfigurálható:

* Választható minimális szintje *helyi redundanciát*, amely egy Azure-régiót azonos adatközpontba belüli adatok három replikája (című [Azure-régiókat] [ planning-guide-3.1]).
* Zóna redundáns tárolás, amelyek között osztja el a három lemezképek különböző adatok erőforrások az Azure-régión belül.
* Alapértelmezett redundanciájának szintje földrajzi redundancia céljából, ami aszinkron módon replikál egy másik három lemezképei az adatokat egy másik Azure azon régióját, amely geopolitikai ugyanabban a régióban található, be a tartalmat.

Ez a cikk a különböző redundancia vonatkozó fölött a táblázatban is látható: <https://azure.microsoft.com/pricing/details/storage/>

Azure Storage-ról további információt itt található:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Az Azure standard szintű tárolót
Azure standard szintű tárolást volt a tároló típusa szerinti elérhető Azure IaaS kiadásakor. Nem voltak IOPS kvóta egyetlen lemezenként. Tapasztalt késést nem volt ugyanahhoz az osztályhoz tartozik, például a helyben tárolt csúcskategóriás SAP rendszerek általában telepített SAN/NAS-eszközökön. Ettől függetlenül az Azure standard szintű tárolást bizonyult elegendő-e több száz SAP rendszerek időközben telepítve az Azure-ban.

A szabványos Azure Storage-fiókok tárolt lemezek van szó, a tényleges tárolt adatokat, a storage-tranzakció, a kimenő adatátviteli és a redundancia beállítást a kiválasztott kötet alapján. Sok, a maximális 1TB méretű is létrehozható, de mindaddig, amíg azok továbbra is üres használata díjmentes. Ezután töltse ki egy virtuális Merevlemezt 100GB, ha van szó, 100GB tárolására, nem pedig a VHD-t a készült névleges méretét.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Prémium szintű Azure Storage
A 2015. áprilisi Microsoft bevezette a prémium szintű Azure Storage. Prémium szintű Storage a célja, hogy adja meg a kapott a következő bevezetett:

* Jobb késleltetéséről.
* Nagyobb átviteli sebesség.
* A i/o-késés kevesebb variancia.

Erre a célra sok módosításoknak az azon a két legfontosabb:

* Az Azure Storage csomópontok SSD-lemezek használata
* Egy új olvassa el a helyi SSD egy Azure számítási csomópont által támogatott gyorsítótár

Standard szintű storage, ha nem változtatta képességek leváló függő mérete a lemez (vagy VHD), prémium szintű Storage jelenleg rendelkezik három különböző lemez kategóriák, ez a cikk látható: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Láthatja, hogy IOPS/lemez és a lemez átviteli/lemez függ a lemez mérete kategória

Prémium szintű Storage esetében költség alapja nincs ilyen lemezek, de ilyen lemez belül a lemezen tárolt adatok mennyisége független mérete kategóriáját tárolt tényleges adatmennyiség.

Lemez a prémium szintű Storage, amely nem közvetlen leképezése látható mérete kategóriákba hozható létre. Ez a helyzet, lehet, különösen akkor, ha a lemezek másolásának szabványos tárolásból a prémium szintű tároló. Ebben az esetben a következő legnagyobb prémium szintű Storage lemezes beállítás hozzárendelésével történik.

Vegye figyelembe, hogy csak bizonyos Virtuálisgép-sorozat a prémium szintű Azure Storage előnyei. Től Dec 2015-öt ezek azok a DS - és GS-méretek. A DS sorozatnak megegyezik alapvetően azonos D sorozatú azzal a kivétellel, hogy DS sorozatnak képes a csatlakoztatási prémium szintű Storage alapuló virtuális gépek továbbá tárolt lemezekre Azure standard szintű tárolást. G-sorozat GS sorozatnak képest lehet ugyanaz.

Ha ellenőrzése a részét a DS sorozatnak virtuális gépek kimenő [Ez a cikk (Linux)] [ virtual-machines-sizes-linux] és [Ez a cikk (Windows)][virtual-machines-sizes-windows], vegye figyelembe, hogy nincsenek adatok mennyiségi korlátozásai a granularitási lemezekhez prémium szintű Storage a virtuális gép szintjének. Különböző DS-méretek és GS sorozatnak virtuális gépeken is különböző korlátozásokkal rendelkezik, amely lehet csatlakoztatni az adatlemezek számának tekintetében. Ezek a korlátozások vannak dokumentálva, valamint a fenti cikk. De lényegében azt jelenti, hogy, például csatlakoztatása egy DS14 virtuális lemezek 32 x P30 nem kaphat 32 x P30 lemez maximális átviteli sebességet. A maximális átviteli sebesség a virtuális gép szintje a cikkben ismertetett inkább adatátvitelt korlátozza.

Prémium szintű Storage további információk itt találhatók: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Felügyelt lemezek
Felügyelt lemezek olyan új erőforrást az Azure Resource Manager az Azure Storage-fiókok tárolt VHD-k helyett használható. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva, és ezért a a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állásának javítása. További információkért olvassa el a [a cikk áttekintése](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Azt javasoljuk, hogy felügyelt lemezt használja, mivel azok egyszerűsítése érdekében a központi telepítés és a virtuális gépek felügyeletét.
SAP jelenleg csak prémium felügyelt lemezeit támogatja. További információkért olvassa el az SAP Megjegyzés [1928533].

#### <a name="azure-storage-accounts"></a>Azure Storage-tárfiókok
Szolgáltatások és az Azure virtuális gépeken való telepítése virtuális merevlemezek és a Virtuálisgép-lemezképek központi telepítése Azure Storage-fiókok nevezett egységekben rendszerezheti. Egy Azure-telepítés megtervezésekor kell alaposan gondolja át, az Azure korlátozásait. Az egyik oldalon nincs Tárfiókok korlátozott számú Azure előfizetésenként. Bár minden Azure Storage-fiók nagyszámú VHD-fájlokat képes tárolni, nincs rögzített korlát a Tárfiók teljes iops-értéket. SAP virtuális gépek több száz jelentős IO hívások létrehozása DBMS rendszerekkel való telepítésekor ajánlott magas IOPS DBMS VMs között több Azure Storage-fiókok terjesztését. Ügyelni kell a következő korlátozásokkal előfizetésenként az Azure Storage-fiókok a jelenlegi korlátozását. Tárolót, mert az adatbázis egy SAP rendszer központi telepítésének fontos része, a fogalom részletes hivatkozott már tárgyalt [adatbázis-kezelő telepítési útmutatója][dbms-guide].

Azure Storage-fiókokról további információ található a [Ez a cikk][storage-scalability-targets]. A cikk elolvasása, vegye figyelembe, hogy a korlátozások Azure standard szintű Storage-fiókok és a prémium szintű Storage-fiókok közötti különbségek vannak. Fő különbségek ilyen egy Tárfiókon belül tárolt adatok mennyiségét. Standard szintű tárolót a lemez egy nagyobb, mint a prémium szintű Storage nagyságrenddel. A másik oldalon, a standard szintű Tárfiók súlyosan korlátozott IOPS (lásd: az oszlop **teljes kérelmek gyakorisága**), mivel a prémium szintű Azure Storage-fiókjához nincs ilyen korlátozás van érvényben. A központi telepítések SAP rendszerek, különösen az adatbázis-kezelő kiszolgálók ismertetésekor ismertetjük részletek és eltérések eredmények.

Különböző tárolók céljából történő szervezése és kategorizálásához különböző VHD-k létrehozásához lehetősége van a Tárfiókon belül. Ezek a tárolók általában szolgálnak, például különálló virtuális merevlemezeket különböző virtuális gépek. Nincsenek nem teljesítményre gyakorolt hatása, csak egy tárolót vagy több tároló alatt egy Azure Storage-fiók használatával.

Az Azure virtuális merevlemez nevét, amelyet az Azure virtuális merevlemez egyedi nevét adja meg az alábbi elnevezési kapcsolatot követi:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Ahogy azt korábban említettük, a fenti karakterláncot kell az Azure Storage a tárolt virtuális merevlemez egyedi azonosításához.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>A Microsoft Azure hálózatkezelés
A Microsoft Azure biztosít a hálózati infrastruktúra, amely lehetővé teszi az összes forgatókönyveket, amelyek azt szeretnénk, SAP szoftverrel megvalósításához leképezése. A funkciók a következők:

* A hozzáférést kívülről, közvetlenül a virtuális gépeket, a Windows Terminálszolgáltatások vagy ssh/VNC keresztül
* Hozzáférését szolgáltatásokhoz és az adott alkalmazást a virtuális gépek által használt portok
* Belső kommunikációs és a névfeloldás között egy központilag telepített Azure virtuális gépek csoportján
* Az ügyfél helyszíni hálózat és az Azure-hálózat között létesítmények közötti kapcsolat
* Több Azure-régióban vagy az adatközpontok Azure helyek közötti kapcsolat

További információk itt találhatók: <https://azure.microsoft.com/documentation/services/virtual-network/>

Nincsenek nevének és IP-feloldás konfigurálása az Azure-ban való számos különböző lehetőségeit. Ebben a dokumentumban csak felhőalapú forgatókönyvek az Azure DNS-sel (ellentétben meghatározása egy saját DNS-szolgáltatás) alapértelmezett támaszkodnak. Egy új Azure DNS-szolgáltatás, amely helyett a saját DNS-kiszolgáló beállításához használható is van. További információ található [Ez a cikk] [ virtual-networks-manage-dns-in-vnet] és a [ezen a lapon](https://azure.microsoft.com/services/dns/).

Létesítmények közötti forgatókönyvek esetén azt is hagyatkoznia azt a tényt, hogy a helyszíni AD/OpenLDAP/DNS-megtörtént-e keresztül VPN- vagy titkos kapcsolat az Azure-bA. Az egyes forgatókönyvek szerint itt dokumentált lehetőségektől, szükség lehet a egy telepített Azure AD/OpenLDAP replikát.

Mivel a hálózat és névfeloldás az adatbázis egy SAP rendszer központi telepítésének fontos része, a fogalom tárgyalt részletesen a [adatbázis-kezelő telepítési útmutatója][dbms-guide].

##### <a name="azure-virtual-networks"></a>Azure virtuális hálózatok
Épület egy Azure virtuális hálózat, megadhatja a címtartomány a magánhálózati IP-címek Azure DHCP-funkciók le van foglalva. A létesítmények közötti forgatókönyv a megadott IP-címtartomány még hozzá van rendelve a DHCP az Azure-ban. Tartomány névfeloldás azonban helyszíni (feltéve, hogy, hogy a virtuális gépeket egy helyszíni tartomány része) történik, és ezért oldhatja címek különböző Azure-Felhőszolgáltatások túl.

Minden virtuális gép az Azure-ban kell kapcsolódnia kell egy virtuális hálózathoz.

További részletek találhatók [Ez a cikk] [ resource-groups-networking] és a [ezen a lapon](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO található egy cikket, amely tartalmazza a OpenLDAP témakör + ARM; )
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> Alapértelmezés szerint a virtuális gépek telepítése után nem módosíthatja a virtuális hálózati konfigurációját. A TCP/IP-beállítások Azure DHCP-kiszolgálóhoz kell hagyni. Alapértelmezett viselkedés a dinamikus IP-hozzárendelés.
>
>

A virtuális hálózati adapter MAC-címet módosíthatja, például átméretezési és a Windows vagy Linux vendég operációs rendszer szerzi be az új hálózati kártya, és automatikusan a DHCP segítségével ebben az esetben az IP-cím és DNS címek hozzárendelése után.

##### <a name="static-ip-assignment"></a>Statikus IP-hozzárendelés
Rögzített vagy fenntartott IP-címek hozzárendelése az Azure virtuális hálózaton belüli virtuális gépek lehetőség. A virtuális gépeket futtató Azure virtuális hálózatban megnyitja kihasználhatják ezt a funkciót, ha szükséges, vagy bizonyos esetekben szükséges nagyszerű lehetőséget. Az IP-hozzárendelés marad érvényes létezik-e a virtuális gép, független, hogy a virtuális gép fut vagy -leállítás során. Ennek eredményeképpen kell a virtuális gépek (fut, és a leállított virtuális Gépeken) teljes számát figyelembe venni, ha az az IP-címek megadásával a virtuális hálózat. Az IP-cím megmarad, amíg nem törli a virtuális gép és a hálózati adapter, vagy amíg az IP-cím hozzárendelése újra deszerializálni lekérdezi. További információkért olvassa el a [Ez a cikk][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Több hálózati adapter virtuális gépenként
Egy Azure virtuális gépen a több virtuális hálózati adapterrel (vNIC) adhat meg. Megkezdheti az beállítása a hálózati forgalom több vNICs van lehetősége, például ügyfélforgalmat keresztül történik egy virtuális hálózati és a háttérkiszolgáló forgalom elkülönítése a második virtuális hálózati keresztül történik. Függő a típust a virtuális gépet a különböző korlátozások is vNICs száma szerint. Ezek a cikkek megtalálhatók pontos részleteit, funkcióit és korlátozások:

* [Több hálózati adapterrel rendelkező Windows virtuális gép létrehozása][virtual-networks-multiple-nics-windows]
* [Több hálózati adapterrel rendelkező Linux virtuális gép létrehozása][virtual-networks-multiple-nics-linux]
* [Sablon használatával több hálózati adapter virtuális gépek telepítése][virtual-network-deploy-multinic-arm-template]
* [PowerShell-lel több hálózati adapter virtuális gépek telepítése][virtual-network-deploy-multinic-arm-ps]
* [Az Azure parancssori felület használatával több hálózati adapter virtuális gépek telepítése][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Hely-hely kapcsolatot
Létesítmények közötti Azure virtuális gépek és a helyszíni átlátható és állandó VPN-kapcsolat csatolva. Várható legyen, a leggyakrabban használt SAP telepítési minta az Azure-ban. A rendszer azt feltételezi, hogy a műveleti eljárások és a folyamatok SAP osztályt az Azure-ban transzparens módon kell működnie. Ez azt jelenti, hogy sikerül nyomtatni, ezek a rendszerek kívül kell, valamint a teszt rendszerhez, amely az Azure-ban fejlesztési rendszerről módosítja az SAP átviteli felügyeleti rendszer (TMS) szállítási használata telepített helyszíni. Pont-pont körül további dokumentációjában található [Ez a cikk][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-alagút eszköz
(A helyszíni adatközpont Azure adatközpontba) helyek kapcsolat létrehozásához kell az beszerzése, és konfigurálja a VPN-eszköz, vagy használja az Útválasztás és távelérés szolgáltatás (RRAS) operációs rendszerben megjelent a Windows Server 2012 szoftver összetevőjeként.

* [Virtuális hálózat létrehozása a PowerShell használatával pont-pont VPN-kapcsolattal][vpn-gateway-create-site-to-site-rm-powershell]
* [VPN-eszközökről a webhelyek közötti VPN átjáró kapcsolatok][vpn-gateway-about-vpn-devices]
* [VPN Gateway – gyakori kérdések][vpn-gateway-vpn-faq]

![A helyszíni és az Azure közötti pont-pont kapcsolat][planning-guide-figure-600]

A fenti ábrán látható, két Azure-előfizetések használatra fenntartva az IP-cím résztartomány rendelkezik az Azure virtuális hálózatairól. A kapcsolat a helyszíni hálózat az Azure VPN-en keresztül jön létre.

#### <a name="point-to-site-vpn"></a>Pont – hely típusú VPN
Pont – hely típusú VPN minden ügyfélszámítógép csatlakozni az Azure, a saját virtuális Magánhálózatokkal igényel. Az SAP helyzetekben azt láthatjuk pont-hely kapcsolatot nincs gyakorlati. Ezért semmilyen további hivatkozások kapják pont-pont VPN-kapcsolatot.

További információk itt találhatók
* [Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Többhelyes VPN
Az Azure ma is kínál arra, hogy hozzon létre egy Azure-előfizetés többhelyes VPN-kapcsolatot. Egy-egy előfizetéshez korábban csak egy pont-pont VPN-kapcsolatot. Ez a korlátozás tűnnek el egyetlen előfizetéssel többhelyes VPN-kapcsolatokkal. Ez lehetővé teszi egy adott előfizetéseket a létesítmények közötti konfigurációk egynél több Azure-régióban ki.

További dokumentációjában talál [Ez a cikk][vpn-gateway-create-site-to-site-rm-powershell]

[comment]: <> (MShermannd TODO ARM dok csatolás nem található)

#### <a name="vnet-to-vnet-connection"></a>Virtuális hálózat virtuális hálózatot kapcsolathoz
Többhelyes VPN-kapcsolattal, kell külön Azure virtuális hálózat konfigurálása a régió. Azonban nagyon gyakran rendelkezik a követelmény, hogy a különböző régiókban szoftverösszetevőket kell kommunikálnak egymással. Ideális esetben ez a kommunikáció nem lesznek irányítva, a helyszíni egy Azure-régióban, illetve onnan az egyéb Azure-régióban. Parancsikonjára Azure kínál arra, hogy konfiguráljon egy Azure virtuális hálózati kapcsolatot egy másik Azure-beli virtuális hálózathoz egy régióban üzemeltetett egy másik régióban. Ez a funkció neve VNet – VNet-kapcsolatot. Ez a funkció a további részletek itt találhatók: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Az Azure expressroute magánhálózati kapcsolat
Microsoft Azure ExpressRoute lehetővé teszi, hogy az Azure-adatközpont és az ügyfél helyszíni infrastruktúra között vagy a közös elhelyezés környezetben magánhálózati kapcsolatok létrehozásához. ExpressRoute felajánlott különböző MPLS (csomagkapcsolt) VPN-szolgáltatókkal vagy az egyéb hálózati szolgáltatók. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. ExpressRoute kapcsolatok ajánlatot magasabb biztonsági szint, több megbízhatóság több párhuzamos kapcsolatok, a gyorsabb sebesség és a kisebb késések fordulnak elő, mint a szokásos kapcsolatok az interneten keresztül.

További részleteket a Azure ExpressRoute- és ajánlatok itt található:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Expressroute több Azure-előfizetések egy ExpressRoute-kapcsolatcsoportot keresztül lehetővé teszi a itt leírtak szerint

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>A kényszerített bújtatás esetén a létesítmények közötti
A virtuális gépekhez való csatlakozás helyszíni tartományok, webhelyek, pont-hely vagy ExpressRoute győződjön meg arról, hogy az internetes proxybeállításokat a virtuális gépek, valamint a felhasználók első telepített kell. Alapértelmezés szerint a szoftverek futtatásának ezeket a virtuális gépek vagy az internet eléréséhez használt böngésző felhasználók a vállalati proxy nem sikerült, de rögtön Azure az interneten keresztül csatlakozni. De még a proxybeállítást a 100 %-os megoldás át tudja irányítani a forgalmat a vállalati proxy, mivel felelőssége a szoftverek és -szolgáltatások kereséséhez a proxy. Ha a virtuális gépen futó szoftver, amely nem végez, illetve a rendszergazda kezeli a beállításokat, az internetre irányuló forgalom is kell újra detoured közvetlenül az internethez Azure szolgáltatáson keresztül.

Ennek elkerülése érdekében beállíthatja a kényszerített bújtatás a helyszíni és az Azure közötti pont-pont kapcsolatban. A kényszerített bújtatás szolgáltatás részletes leírása itt közzé van-e <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Kényszerített bújtatás az ExpressRoute engedélyezve van az ügyfelek egy alapértelmezett útvonalat hirdet a ExpressRoute BGP társviszony-létesítési munkameneteket keresztül.

#### <a name="summary-of-azure-networking"></a>Az Azure hálózatkezelés összegzése
Ez a fejezet Azure hálózati számos fontos pontok tartalmazott. Itt a következő fő összefoglalása:

* Az Azure virtuális hálózatok lehetővé teszi, hogy a saját igényeinek megfelelően a hálózat beállítása
* Egy Azure virtuális hálózatot is javítható az IP-címtartományok hozzárendelése a virtuális gépek vagy rögzített IP-címek hozzárendelése a virtuális gépek
* A pont-pont vagy a pont – hely kapcsolat beállításához először hozzon létre egy Azure virtuális hálózatra kell
* Ha egy virtuális gép van telepítve, már nem lehet módosítani a virtuális hálózat a virtuális Géphez rendelt

### <a name="quotas-in-azure-virtual-machine-services"></a>A virtuális gép az Azure-szolgáltatások kvóták
Igazolnia kell a tényt, hogy a tárolási és hálózati infrastruktúra az Azure-infrastruktúra a szolgáltatások különböző futtató virtuális gépek között megosztott tisztában lenni. És az ügyfél saját adatközpontok hasonlóan az infrastruktúrához kapcsolódó erőforrások részének túlzott kiosztása mértékben. A Microsoft Azure Platform használja a lemez, a Processzor, a hálózati és az egyéb kvóták korlátozni az erőforrás-felhasználás és következetes és determinisztikus teljesítmény megőrzése érdekében.  A virtuális gép különböző (A5, a6 méretű, stb.) a száma, CPU, RAM, eltérő kvóták, és a hálózati.

> [!NOTE]
> A virtuális gép típusú SAP által támogatott a CPU és memória-erőforrások előre lefoglalt az állomáscsomópontokon. Ez azt jelenti, hogy ha a virtuális gép van telepítve, az erőforrások a gazdagép elérhetők a határozzák meg a Virtuálisgép-típussá.
>
>

Tervezési és méretezéssel SAP Azure megoldásokról minden virtuális gép méretét kvótái kell tekinteni. A virtuális gép kvóták ismertetjük [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A leírt kvóták elméleti maximális értékeket jelölik.  Az iops-érték lemezenként korlátját kis IOS (8 KB-os) érhető el, de valószínűleg nem lehet elérni a nagy IOs (1Mb).  Az IOPS-korláttal megvalósul a lépésköz legyen egy lemezt.

A nyers döntési fa eldöntheti, hogy egy SAP rendszer Azure virtuális gép szolgáltatások és platformképességei vagy hogy egy meglévő rendszer kell megadni eltérően a rendszer az Azure telepítéséhez illeszkedik, mint a döntési fa alábbi használhatók:

![Döntse el, hogy lehetővé teszi az Azure-on SAP telepítését döntési fája][planning-guide-figure-700]

**1. lépés**: A legfontosabb adatokat kezdődnie esetében az SAP követelmény egy adott SAP rendszerhez. Az SAP-követelmények kell bontható az adatbázis-kezelő és az SAP alkalmazás részét, még akkor is, ha az SAP rendszer már telepített helyszíni 2 szintű konfiguráció. Meglévő rendszerek a SAP, gyakran kapcsolódik a a hardver meghatározott vagy meglévő SAP-referenciaalapok alapján. Az eredmények itt található: <http://global.sap.com/campaigns/benchmark/index.epx>.
Az újonnan telepített SAP rendszerek kell megtettünk mindent keresztül egy méretezési gyakorlatban kell meghatározni, hogy a rendszer a SAP követelményeinek.
Lásd még: Ebben a blogban és az SAP méretezési csatolt dokumentumot az Azure-on: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2. lépés**: meglévő rendszerek esetén az i/o-kötet és az adatbázis-kezelő kiszolgáló másodpercenkénti i/o-művelet kell értékelni. Újonnan tervezett rendszerek esetén a méretezési a gyakorlatban az új rendszer is biztosítani az i/o-követelményeinek nyers ötleteket DBMS oldalán. Ha nem ismeri, végül olyan szeretne egy a koncepció igazolása.

**3. lépés**: hasonlítsa össze a SAP követelmény az SAP, Azure virtuális gép különböző típusú biztosíthat az adatbázis-kezelő kiszolgáló. A SAP adatait az Azure virtuális gép különböző típusú SAP Megjegyzés ismertetett [1928533]. A fókusz kell az adatbázis-kezelő virtuális gépen először mert, amely a réteg az adatbázisrétegben egy terjessze ki a legtöbb környezetben módosítások nélkül az SAP NetWeaver rendszer. Ezzel szemben az SAP alkalmazásréteg kiterjeszthető. Ha az SAP egyik támogatott Azure Virtuálisgép-típusokon biztosíthat a szükséges SAP, a munkaterhelés a tervezett SAP-rendszer nem futtatható, az Azure-on. Vagy újra kell telepíteni, a rendszer a helyszíni vagy módosítania kell a munkaterhelés kötetet, a rendszer.

**4. lépés**: dokumentált [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows], Azure érvénybe lépteti az IOPS kvóta lemezenként Standard szintű tárolást vagy a prémium szintű Storage akár független. A Virtuálisgép-típussá függ, lehet csatlakoztatni adatlemezek száma függ. Ennek eredményeképpen kiszámításához a maximális iops-érték, amely elérhető az összes, a virtuális gép különböző típusú. Az adatbázis-fájlelrendezés függ, lemezek legyen, a vendég operációs rendszer egy kötet képes paritásos. Azonban ha egy telepített SAP rendszer aktuális IOPS mennyisége meghaladja a legnagyobb virtuális gép típusát és az Azure-e több memória kiegyensúlyozása lehetősége nélkül számított határain, a munkaterhelés, a SAP rendszer befolyásolhatja, súlyosan. Ebben az esetben kattintson az a pont, ahol nem kell telepíteni a rendszer az Azure-on.

**5. lépés**: SAP rendszerek, amelyek telepített helyszíni 2 szintű konfigurációk, különösen a, hogy a rendszer előfordulhat, hogy be kell állítani az Azure-on 3 szintű konfiguráció. Ebben a lépésben kell ellenőrizni, hogy van-e egy összetevő az SAP alkalmazásréteg, amely nem terjeszthető ki, és amely volna nem felelnek meg a CPU és memória-erőforrásokat kínálnak a különböző Azure virtuális Gépen. Ha valóban ilyen összetevőt, a SAP rendszer és a munkaterhelés nem telepíthető az Azure. De ha ki lehet terjeszteni a SAP alkalmazás-összetevők a több Azure virtuális gépeken, a rendszer az Azure is telepíthető.

**6. lépés**: az adatbázis-kezelő és az SAP alkalmazás réteg összetevőinek az Azure virtuális gépeken is futtatható, ha a konfigurációs kell tekintettel definiálni:

* Az Azure virtuális gépek száma
* Az egyes összetevők Virtuálisgép-típusokon
* Adatbázis-kezelő virtuális gépen elég iops értéket biztosítanak, virtuális merevlemezek száma

## <a name="managing-azure-assets"></a>Az Azure-eszközök kezelése
### <a name="azure-portal"></a>Azure Portal
Az Azure-portálon az Azure virtuális gép központi telepítések felügyeletéhez szükséges három felületek egyik. Az alapvető felügyeleti feladatokat, például a lemezképeket, a virtuális gépek telepítése az Azure portálon keresztül végezhető. Emellett a Storage-fiókok, a virtuális hálózatok és az egyéb Azure összetevők létrehozása egyaránt az Azure-portálon jól kezelhető feladatokat. Azonban a funkciók például a virtuális merevlemezek feltöltését a helyszíni Azure vagy az Azure virtuális merevlemez másolása feladatokat, melyekhez szükséges külső gyártású eszközöknek vagy a PowerShell vagy a parancssori felületen keresztül felügyeleti is.

![A Microsoft Azure portál – a virtuális gépek – áttekintés][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

A virtuálisgép-példány felügyeleti és konfigurációs műveleteket is előfordulhatnak, az az Azure portálon.

Újraindítása és leállítása a virtuális gép mellett is csatolása programot, válassza le, és a virtuálisgép-példány, rögzítheti a lemezkép előkészítése példányát, és a virtuálisgép-példányt méretének beállítása adatok létrehozásához.

Az Azure-portálon telepítheti és konfigurálhatja a virtuális gépek és sok más Azure-szolgáltatások alapszintű funkciókat biztosítja. Az Azure-portálon azonban nem az összes rendelkezésre álló funkciók vonatkozik. Az Azure portálon nincs lehetőség hasonló feladatok elvégzéséhez:

* VHD feltöltése az Azure-bA
* Virtuális gépek másolása

[comment]: <> (MShermannd TODO Mi a helyzet automation szolgáltatás SAP virtuális gépekhez? )
[comment]: <> (Több virtuális gépek, operációs rendszer időközben lehetséges MSSedusch telepítése)
[comment]: <> (MSSedusch is nincs lehetőség az Azure portálon az automatizálás vonatkozó központi telepítési típussal. Feladatokat, mint a több virtuális gépek parancsfájlalapú telepítés nincs lehetőség az Azure-portálon.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>A Microsoft Azure PowerShell-parancsmagok segítségével kezelése
A Windows PowerShell telepítése az Azure-ban rendszerek nagyobb mennyiségű ügyfél széles körben elfogadott sokoldalú és bővíthető keretrendszer. A PowerShell-parancsmagok egy asztali, a laptop vagy a dedikált felügyeleti állomás a telepítés után a PowerShell-parancsmagok távolról futtatható.

A folyamat ahhoz, hogy az Azure PowerShell-parancsmagok és a konfigurálása az Azure szóló használati ismertetett azok használatát a helyi asztali vagy hordozható [Ez a cikk][powershell-install-configure].

További részletes lépéseit telepítése, frissítése és konfigurálása az Azure PowerShell parancsmagok is megtalálhatók [a telepítési útmutató ezen fejezete][deployment-guide-4.1].

Felhasználói élmény eddig lett, hogy PowerShell (Elképzelhető), hogy biztosan a virtuális gépek telepítéséhez, és hozzon létre egyéni lépéseket a virtuális gépek központi hatékonyabb eszköz. SAP példányok Azure-ban futó ügyfelek használnak a PS-parancsmagok kiegészíti a felügyeleti feladatokat az Azure portálon teheti meg, vagy akár az PS parancsmagok kizárólag kezeli a telepítések az Azure-ban. Mivel az Azure-specifikus parancsmagok az azonos elnevezési a 2000-nél több Windows-kapcsolatos parancsmagok megosztásához célszerű a Windows rendszergazdák számára, hogy ezek a parancsmagok egyszerű feladat.

Példa itt talál: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd Teendőlista új CLI parancs tesztelésekor írják le. )
Az Azure Figyelőbővítmény az SAP telepítését (című [Azure figyelésére szolgáló megoldás az SAP] [ planning-guide-9.1] ebben a dokumentumban) csak akkor PowerShell vagy a parancssori felületen keresztül lehetséges. Ezért fontos kötelező telepítése és konfigurálása a PowerShell vagy a parancssori felület üzembe helyezésekor és felügyelete az SAP NetWeaver rendszer az Azure-ban.  

Azure több funkciót biztosít, új PS-parancsmagok is lehet hozzáadni, amelyhez a parancsmagok frissítése. Ezért érdemes ellenőrizni a Azure letöltési hely legalább egyszer a hónap <https://azure.microsoft.com/downloads/> a parancsmagok egy új verziója. Az új verzió telepítve van a korábbi verzióra.

Az Azure-hoz kapcsolódó PowerShell listáját általános parancsok be a négyzetet: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>A Microsoft Azure parancssori felület parancsait keresztül kezelése
Azure kezelni kívánt Linux felhasználók erőforrások Powershell beállítást nem lehet. A Microsoft Azure CLI alternatívájaként kínál.
Az Azure parancssori felület számos nyílt forráskódú, platformok közötti parancsok végzett munka az Azure platformra. Az Azure parancssori felület több ugyanazokat a funkció az Azure portálon található tartalmazza.

További információ a telepítéshez, konfiguráláshoz és parancssori felület használatával parancsok Azure feladatok elvégzését:

* [Az Azure parancssori felület telepítése][xplat-cli]
* [Telepítését és kezelését a virtuális gépek Azure Resource Manager-sablonok és az Azure parancssori felület használatával][../../linux/create-ssh-secured-vm-from-template.md]
* [Használja az Azure parancssori felület Mac, Linux és a Windows az Azure Resource Manager eszközzel][xplat-cli-azure-resource-manager]

Is olvasható fejezet [Linux virtuális gépek Azure CLI][deployment-guide-4.5.2] a a [telepítési útmutató][planning-guide] központi telepítése az Azure-figyelés az Azure parancssori felület használatával Az SAP-kiterjesztés.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Az SAP, az Azure virtuális gépek telepítése különböző módjai
Ebben a fejezetben ismerje meg a különböző módon telepíthet egy Azure-ban. Ez a fejezet további előkészítést eljárásokat, valamint a virtuális merevlemezek és az Azure virtuális gépek kezelésének ismertetnek.

### <a name="deployment-of-vms-for-sap"></a>Az SAP virtuális gépek telepítése
A Microsoft Azure virtuális gépek és a kapcsolódó lemezek telepítendő több lehetőséget is kínál. Így nagyon fontos különbségek megismeréséhez, mivel a virtuális gépek előkészített központi telepítési módszertől függően eltérőek lehetnek. Általában azt tekintse meg a következő esetekben:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Áthelyezését a virtuális gépek helyszíni Azure olyan nem általánosított tartalmazó
Kívánja áthelyezni a megadott számú SAP-rendszer a helyszíni Azure-bA. Ezt úgy teheti a virtuális Merevlemezt, amely tartalmazza az operációs rendszer, az SAP bináris fájljait, és az adatbázis-kezelő bináris fájljait és a virtuális merevlemezek feltöltését a DBMS Azure adatainak és naplókönyvtárainak fájlokkal. Kal ellentétben a [az alábbi #2. forgatókönyv][planning-guide-5.1.2], mindig az állomásnevet, SAP SID és SAP felhasználói fiókot az Azure virtuális gépen, mivel azok a helyszíni környezetben volt konfigurálva. A kép normalizálása ezért nem szükséges. Lásd: fejezeteknek: [áthelyezését a virtuális gépek helyszíni Azure olyan nem általánosított tartalmazó előkészítése] [ planning-guide-5.2.1] a jelen dokumentum előkészítő lépések helyszíni és a feltöltése nem általánosított virtuális gépek és VHD az Azure-bA. Fejezet elolvasása [forgatókönyv 3: a helyszíni SAP Azure nem általánosított virtuális Merevlemezt használ a virtuális gépek áthelyezése] [ deployment-guide-3.4] a a [telepítési útmutató] [ deployment-guide] a egy lemezképet, az Azure-ban történő telepítésének részletes lépéseket.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Virtuális gép és egy ügyfél-specifikus lemezkép központi telepítése
Adott javítás szükségletek az operációs rendszer vagy az adatbázis-kezelő verzió a megadott lemezképek az Azure piactéren előfordulhat, hogy nem felelnek meg az igényeinek. Ezért szükség lehet a saját személyes OS/adatbázis-kezelő Virtuálisgép-lemezkép, amelyet ezután többször telepítve virtuális gép létrehozása. Az ilyen titkos lemezkép előkészítése duplikálva lettek-e, a következő elemeket kell tekinteni:

- - -
> ![Windows][Logo_Windows] Windows
>
> További információt itt olvashat: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> a Windows-beállítások (például a Windows biztonsági AZONOSÍTÓK és állomásnév) kell lennie a helyszíni virtuális Gépre a sysprep parancsot keresztül azért/általánosítva.
>
>
> ![Linux][Logo_Linux] Linux
>
> Ezek a cikkekben ismertetett lépéseket követve [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], vagy [Oracle Linux] [ virtual-machines-linux-create-upload-vhd-oracle], az Azure-bA feltölteni kívánt virtuális merevlemez előkészítése.
>
>

- - -
Ha már telepítette a helyszíni virtuális gépre (különösen a 2-réteg rendszerek) SAP tartalom, módosíthatja az SAP rendszerbeállítások után az Azure virtuális Gépen keresztül a példány központi telepítését, nevezze át a SAP szoftver kiépítés Manager által támogatott eljárás (SAP Megjegyzés [1619720]). Lásd: fejezetek [specifikus képének a virtuális gépek telepítése az SAP-előkészítése] [ planning-guide-5.2.2] és [feltöltése az Azure-bA a helyi virtuális merevlemez] [ planning-guide-5.3.2]a jelen dokumentum előkészítő lépések helyszíni és a feltöltése a általánosított virtuális gépek Azure-bA. Fejezet elolvasása [2. forgatókönyv: központi telepítése a virtuális gép és egy egyéni lemezképet az SAP] [ deployment-guide-3.3] a a [telepítési útmutató] [ deployment-guide] történő telepítésének részletes lépései ilyen lemezkép az Azure-ban.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Az Azure piactéren kívüli virtuális gép telepítése
Szeretné használni a Microsoft vagy harmadik fél megadott Virtuálisgép-lemezkép központi telepítése a virtuális Gépet az Azure piactérről. Miután telepítette a virtuális Gépet az Azure-ban, az azonos irányelvek és eszközök telepítése, a SAP szoftver és/vagy az adatbázis-kezelő a virtuális Gépen belül, mint a helyszíni környezetben hajtsa végre. Részletes leírás a telepítés, ellenőrizze a fejezet [1. forgatókönyv: központi telepítése egy virtuális Gépet az Azure piactéren az SAP kívül] [ deployment-guide-3.2] a a [telepítési útmutató] [deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Az Azure SAP rendelkező virtuális gépek előkészítése
Azure meg kell győződnie, hogy a virtuális gépek feltöltés előtt a virtuális gépek és VHD-k bizonyos követelmények teljesítéséhez. A használt telepítési módszertől függően kisebb különbségek vannak.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Áthelyezését a virtuális gépek helyszíni Azure olyan nem általánosított tartalmazó előkészítése
Gyakori telepítési módja, hogy egy meglévő virtuális Gépre, amely egy SAP rendszer fut a helyszíni Azure. Ezt a virtuális Gépet és az SAP-rendszer a virtuális gép csak fusson az Azure-ban az azonos állomásnévvel és nagyon valószínű SAP SID AZONOSÍTÓVAL. Ebben az esetben a vendég operációs rendszer a virtuális Gépen kell nem általánosítva több-telepítéshez. Ha a helyszíni hálózat lett kiterjesztve, az Azure (című [létesítmények közötti - telepítését egy vagy több SAP-virtuális gép az Azure-e a követelményeknek, teljesen integrálva a helyszíni hálózatra] [ planning-guide-2.2] ebben a dokumentumban), akkor még a azonos tartományi fiókokat is használhat a virtuális Gépen belül azok helyszíni előtt használt.

A saját Azure VM lemez előkészítésekor követelmények a következők:

* Eredetileg az operációs rendszert tartalmazó virtuális merevlemez maximális mérete 127GB csak rendelkezhetnek. Ez a korlátozás 2015. márciusi végén kapott szüntetni. Most a virtuális Merevlemezt, amely tartalmazza az operációs rendszer lehet akár 1TB méretű egyéb Azure Storage tárolt VHD-t is.
* Kell lennie a rögzített méretű VHD formátumban. Dinamikus VHD vagy VHDx formátumú virtuális merevlemezeket még nem támogatottak az Azure-on. Dinamikus virtuális merevlemezek konvertálja statikus virtuális merevlemezek a virtuális Merevlemezt a PowerShell-parancsmagjaival vagy a CLI feltöltésekor
* Virtuális merevlemezek, amely a virtuális géphez csatlakoztatott, és legyen csatlakoztatva újra az Azure-ban, valamint egy rögzített méretű VHD formátumú virtuális gép szükséges. Kérjük, olvassa el [Ez a cikk (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) és [Ez a cikk (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) méretkorlátait adatlemezek számára. Dinamikus virtuális merevlemezek konvertálja statikus virtuális merevlemezek a virtuális Merevlemezt a PowerShell-parancsmagjaival vagy a CLI feltöltésekor
* Adjon hozzá egy másik helyi fiók, amely használható a Microsoft támogatási szolgálatához vagy a szolgáltatások és alkalmazások futtatásához, amíg a rendszer a virtuális gép környezeteként rendelhető és a megfelelő felhasználók rendszergazdai jogosultságokkal is használható.
* A kis-és egy kizárólag felhőalapú környezet-forgatókönyv használatával (című [csak felhőalapú - függőségek a helyszíni ügyfél hálózaton nélkül az Azure virtuális gépek telepítéséhez] [ planning-guide-2.1] a jelen dokumentum) a Ez a telepítési módszer együtt, tartományi fiókokat, hogy nem működik után az Azure lemez a rendszer az Azure-ban. Ez különösen igaz, ha a szolgáltatásokat, mint az adatbázis-kezelő vagy SAP alkalmazások futtatásához használt fióknak. Ezért kell ilyen tartományi fiókok cserélje le a virtuális gép helyi fiókokhoz és a helyszíni tartományi fiókokat a virtuális gép törlése. A helyszíni tartományi felhasználók megőrzi a Virtuálisgép-lemezkép található darabolása nem okoz problémát telepítésekor a virtuális Gépet a létesítmények közötti forgatókönyv fejezetben leírtak [létesítmények közötti - telepítését egy vagy több SAP-virtuális gép az Azure-e a követelményeknek, hogy az teljesen integrálva a helyszíni hálózat] [ planning-guide-2.2] ebben a dokumentumban.
* Ha tartományi fiókokat volt az adatbázis-kezelő bejelentkezést, vagy a felhasználók fut, a rendszer a helyi és virtuális gépek elvárt befejezési csak felhőalapú forgatókönyveket telepíteni, a tartományi felhasználók törölni kell. Győződjön meg arról, hogy a helyi rendszergazda, valamint egy másik virtuális gép helyi felhasználói meg van adva a bejelentkezési/felhasználó az adatbázis-kezelő, a rendszergazdáknak be kell.
* Más helyi fiókok hozzáadása, azok szükség lehet az adott környezet-forgatókönyv szerint.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ebben a forgatókönyvben nem általánosítása (sysprep) a virtuális gép frissítése és az Azure virtuális gép telepítése szükséges.
> Győződjön meg arról, hogy nem használatos a D:\ meghajtóra.
> Állítsa be a csatlakoztatott lemezek lemez automount fejezetben leírtak [beállítás automount a csatlakoztatott lemezekkel] [ planning-guide-5.5.3] ebben a dokumentumban.
>
> ![Linux][Logo_Linux] Linux
>
> Ebben a forgatókönyvben nem általánosítása (waagent-deprovision) a virtuális gép szükséges feltöltése és telepítése az Azure virtuális gép.
> Győződjön meg arról, hogy az összes lemez csatlakoztatva van-e keresztül uuid és, hogy nem használja a/mnt és az erőforrások. Az operációsrendszer-lemezképet győződjön meg arról, hogy a rendszertöltő bejegyzés is tükrözi az uuid-alapú csatlakoztatási.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Virtuális gép és egy ügyfél-specifikus kép telepítése az SAP-előkészítése
Egy általánosított OS tartalmazó VHD-fájlokat tároló Azure Storage-fiókok vagy kezelt lemezképek tárolódnak. A virtuális merevlemez vagy felügyelt lemezképről hivatkozik a központi telepítési sablon fájlokban forrásaként, fejezetben leírtak segítségével telepíthet egy új virtuális Gépet egy lemezképből [2. forgatókönyv: központi telepítése a virtuális gép és egy egyéni lemezképet az SAP] [ deployment-guide-3.3], a [telepítési útmutató][deployment-guide].

Ha a saját Azure Virtuálisgép-lemezkép előkészítése követelményei vannak:

* Eredetileg az operációs rendszert tartalmazó virtuális merevlemez maximális mérete 127GB csak rendelkezhetnek. Ez a korlátozás 2015. márciusi végén kapott szüntetni. Most a virtuális Merevlemezt, amely tartalmazza az operációs rendszer lehet akár 1TB méretű egyéb Azure Storage tárolt VHD-t is.
* Kell lennie a rögzített méretű VHD formátumban. Dinamikus VHD vagy VHDx formátumú virtuális merevlemezeket még nem támogatottak az Azure-on. Dinamikus virtuális merevlemezek konvertálja statikus virtuális merevlemezek a virtuális Merevlemezt a PowerShell-parancsmagjaival vagy a CLI feltöltésekor
* Virtuális merevlemezek, amely a virtuális géphez csatlakoztatott, és legyen csatlakoztatva újra az Azure-ban, valamint egy rögzített méretű VHD formátumú virtuális gép szükséges. Kérjük, olvassa el [Ez a cikk (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) és [Ez a cikk (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) méretkorlátait adatlemezek számára. Dinamikus virtuális merevlemezek konvertálja statikus virtuális merevlemezek a virtuális Merevlemezt a PowerShell-parancsmagjaival vagy a CLI feltöltésekor
* Mivel a tartományi felhasználók regisztrált felhasználók a virtuális gép nem szerepel egy kizárólag felhőalapú forgatókönyv (című [csak felhőalapú - függőségek a helyszíni ügyfél hálózaton nélkül az Azure virtuális gépek telepítéséhez] [ planning-guide-2.1] a jelen dokumentum), a szolgáltatások ilyen fiókok nem működik, ha a lemezkép központi telepítésekor az Azure-tartomány segítségével. Ez különösen igaz szolgáltatások például az adatbázis-kezelő vagy SAP alkalmazások futtatásához használt fiók. Ezért kell ilyen tartományi fiókok cserélje le a virtuális gép helyi fiókokhoz és a helyszíni tartományi fiókokat a virtuális gép törlése. Megőrzi a helyszíni tartományi felhasználók található a Virtuálisgép-lemezkép nem lehet probléma telepítésekor a virtuális Gépet a létesítmények közötti forgatókönyv fejezetben leírtak [létesítmények közötti - telepítését egy vagy több SAP-virtuális gép az Azure-e a követelményeknek, hogy az teljesen integrálva a helyszíni hálózat] [ planning-guide-2.2] ebben a dokumentumban.
* Adjon hozzá egy másik helyi fiók rendszergazdai jogosultságokkal, amely probléma vizsgálatok vagy a szolgáltatások és alkalmazások futtatásához, amíg a rendszer a virtuális gép környezeteként is hozzárendelhető a Microsoft támogatási szolgálatához, és jobban megfelelő felhasználók által használható használható.
* A csak felhőalapú telepítések és ahol tartományi fiókok volt az adatbázis-kezelő bejelentkezések vagy felhasználók futtatásakor használt a rendszer a helyi a tartományi felhasználók törölni kell. Győződjön meg arról, hogy a helyi rendszergazda, valamint egy másik virtuális gép helyi felhasználói meg van adva a rendszergazdáknak az adatbázis-kezelő a bejelentkezési/felhasználójának kell.
* Más helyi fiókok hozzáadása, azok szükség lehet az adott környezet-forgatókönyv szerint.
* Ha a rendszerkép tartalmazza az SAP NetWeaver egy telepítése és az eredeti neve az Azure-telepítés helyén állomásnevének átnevezése valószínű, javasoljuk, hogy a legújabb verzióját a SAP szoftver kiépítés Manager DVD átmásolja a sablont. Ez lehetővé teszi a könnyen az SAP megadott Átnevezés funkció segítségével igazítja a módosított állomásnév és/vagy módosítsa a SAP rendszer belül a telepített Virtuálisgép-lemezkép biztonsági azonosítója, amint egy új példányt elindult.

- - -
> ![Windows][Logo_Windows] Windows
>
> Győződjön meg róla, hogy nincs D:\ meghajtót használja Set lemez automount csatlakoztatott lemezek fejezetben leírtak [beállítás automount a csatlakoztatott lemezekkel] [ planning-guide-5.5.3] ebben a dokumentumban.
>
> ![Linux][Logo_Linux] Linux
>
> Győződjön meg arról, hogy az összes lemez csatlakoztatva van-e keresztül uuid és, hogy nem használja a/mnt és az erőforrások. Az operációsrendszer-lemezképet győződjön meg arról, a rendszertöltő bejegyzés is tükrözi az uuid-alapú csatlakoztatási.
>
>

- - -
* SAP grafikus felhasználói felület (a felügyeleti és beállítására céljából) a sablon előre telepítve.
* Egyéb szoftverek szükségesek, hogy a virtuális gépek futnak a létesítmények közötti forgatókönyv telepíthető, amíg ez a szoftver együttműködik a nevezze át a virtuális gép.

Ha a virtuális gép megfelelően előkészített általános, és végül független a fiókok/felhasználók számára nem érhető el a célként megadott Azure környezetben, az utolsó előkészítési lépés ilyen lemezkép normalizálása végzik.

##### <a name="generalizing-a-vm"></a>A virtuális gépek normalizálása
- - -
> ![Windows][Logo_Windows] Windows
>
> Az utolsó lépése, hogy jelentkezzen be rendszergazdaként egy virtuális Gépet. Nyisson meg egy Windows-parancsablakot, mint a *rendszergazda*. Ugrás a %windir%\windows\system32\sysprep, majd hajtsa végre a sysprep.exe.
> Egy kis ablakban jelenik meg. Fontos, hogy ellenőrizze a **Generalize** (az alapértelmezett érték a nem ellenőrzött) lehetőséget, és módosítsa a leállítási lehetőséget az "Újraindítás" alapértelmezett "Leállítás". Ez az eljárás azt feltételezi, hogy a sysprep folyamat végrehajtott helyszíni a virtuális gépek vendég operációs rendszerben.
> Ha azt szeretné, hogy hajtsa végre az Azure-ban már futó virtuális gépen, az ismertetett lépéseket követve [Ez a cikk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [A Resource Manager sablonként használni egy Linux virtuális gép rögzítése][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Virtuális gépek és virtuális merevlemezek átvitele az Azure-bA helyszíni között
Mivel a Virtuálisgép-lemezképet és lemezt feltöltése az Azure-ba nem lehetséges az Azure-portálon, kell használnia az Azure PowerShell-parancsmagjaival vagy a parancssori felület. Egy másik lehetőség, az eszköz "AzCopy" használatát. Az eszköz másolható VHD-k a helyszíni és az Azure közötti (kétirányú). Azt is másolhatja Azure-régiók közötti virtuális merevlemezeket. További részleteket [ebben a dokumentációban] [ storage-use-azcopy] a letöltésről és az AzCopy használata.

Harmadik lehetőségként különböző külső grafikus felhasználói Felülettel alapú eszközök lenne. Azonban győződjön meg arról, hogy ezek az eszközök támogatják a Lapblobokat Azure. A célra a Azure oldalakra vonatkozó Blob tároló használatára kell (a különbségek a dokumentum ismerteti: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Az Azure által biztosított eszközök is nagyon hatékonyak a tömörítés a virtuális gépek és VHD, amelyek fel kell tölteni. Ez azért fontos, mert ez a hatékonyság a tömörítés csökkenti a kísérlet ideje (ami függ ennek ellenére a feltöltési hivatkozás az internetre a helyszíni létesítményt a személyes és a megcélzott Azure-telepítés régió). A valós feltételezve, hogy a virtuális gép vagy a VHD-Európai helyről az adatközpontok hosszabb időt vesz igénybe, mint az azonos virtuális gépek vagy virtuális merevlemezek feltöltését a Európai Azure Azure az Egyesült államokbeli adatokat feltölteni az adatközpontok.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Az Azure-bA a helyi virtuális merevlemez feltöltése
A helyi hálózatról egy meglévő virtuális vagy virtuális merevlemez feltöltéséhez ilyen virtuális Merevlemezt vagy virtuális gép kell megfeleljenek a fejezetben felsorolt [áthelyezését a virtuális gépek helyszíni Azure olyan nem általánosított tartalmazó előkészítése] [ planning-guide-5.2.1]ebben a dokumentumban.

Egy virtuális Gépet nem kell általánosítva, és az állapot és rendelkezik a helyszíni oldalon leállást követően alakzat fel kell tölteni. Ugyanez érvényes további VHD, amelyek nem tartalmaznak minden operációs rendszer.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Virtuális merevlemez feltöltése, majd elérhetővé tétele az Azure-lemez
Ebben az esetben szeretnénk töltse fel a virtuális Merevlemezt, vagy anélkül, hogy az operációs és egy virtuális géphez, amely adatok lemezként csatlakoztatni vagy használni az operációsrendszer-lemez. Ez az folyamat

**PowerShell**

* Jelentkezzen be előfizetés *Connect-AzureRmAccount*
* Az előfizetés és a környezet beállításához *Set-AzureRmContext* és előfizetés-azonosító paramétert vagy SubscriptionName - lásd: <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* A virtuális merevlemez feltöltése *Add-AzureRmVhd* egy Azure Storage-fiók – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Választható) Hozhat létre egy felügyelt lemezt a virtuális Merevlemezt *New-AzureRmDisk* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Az operációsrendszer-lemezképet, az új Virtuálisgép-konfiguráció beállítása a virtuális merevlemez vagy a felügyelt lemezzel *Set-AzureRmVMOSDisk* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Hozzon létre egy új virtuális Gépet a a Virtuálisgép-konfiguráció a *New-AzureRmVM* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Adatlemez hozzáadása új virtuális gép és *Add-AzureRmVMDataDisk* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Azure CLI 2.0**

* Jelentkezzen be előfizetés *az bejelentkezés*
* Válassza ki az előfizetés *az fiók beállítása – előfizetés `<subscription name or id`>*
* A virtuális merevlemez feltöltése *az tárolási blob feltöltése* -lásd [az Azure Storage az Azure parancssori felület használatával][storage-azure-cli]
* (Választható) Hozhat létre egy felügyelt lemezt a virtuális Merevlemezt *az lemez létrehozása* -lásd: https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Hozzon létre egy új virtuális Gépet, adja meg a feltöltött virtuális merevlemez vagy kezelt lemez az operációs rendszer lemezeként *az virtuális gép létrehozása* és paraméter *--csatolása operációsrendszer-lemez*
* Adatlemez hozzáadása új virtuális gép és *az méretű lemez csatolása* és paraméter *– új*

**Sablon**

* A Powershell vagy Azure CLI feltöltéséhez
* (Választható) A virtuális merevlemezről végzett Powershell, az Azure parancssori felület és az Azure-portálon kezelt lemez létrehozása
* Telepítse a virtuális Gépet egy JSON-sablon hivatkozik a VHD-t, ahogy az a [példa JSON sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json) vagy felügyelt lemezt használ, ahogy az [példa JSON sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-disk-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>A Virtuálisgép-lemezkép központi telepítése
Egy meglévő virtuális vagy virtuális merevlemez feltöltése a helyi hálózatról történő használatához azt egy Azure Virtuálisgép-lemezkép egy virtuális gép vagy VHD-t kell fejezetben felsorolt követelményeknek [specifikus képének a virtuális gépek telepítése az SAP-előkészítése] [ planning-guide-5.2.2] ebben a dokumentumban.

* Használja *sysprep* Windows vagy *waagent-deprovision* tekintse meg a VM - általánosítja Linux [Sysprep műszaki útmutatója](https://technet.microsoft.com/library/cc766049.aspx) Windows vagy [rögzítése egy Linux virtuális gép használata erőforrás-kezelő sablonként] [ capture-image-linux-step-2-create-vm-image] Linux
* Jelentkezzen be előfizetés *Connect-AzureRmAccount*
* Az előfizetés és a környezet beállításához *Set-AzureRmContext* és előfizetés-azonosító paramétert vagy SubscriptionName - lásd: <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* A virtuális merevlemez feltöltése *Add-AzureRmVhd* egy Azure Storage-fiók – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Választható) Hozzon létre egy felügyelt lemezképet a virtuális merevlemez *New-AzureRmImage* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Az operációsrendszer-lemezképet a számára egy új virtuális gép konfigurációs beállítása a
  * Virtuális merevlemez *Set-AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Felügyelt lemezképet *Set-AzureRmVMSourceImage* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Hozzon létre egy új virtuális Gépet a a Virtuálisgép-konfiguráció a *New-AzureRmVM* -lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Azure CLI 2.0**

* Használja *sysprep* Windows vagy *waagent-deprovision* tekintse meg a VM - általánosítja Linux [Sysprep műszaki útmutatója](https://technet.microsoft.com/library/cc766049.aspx) Windows vagy [rögzítése egy Linux virtuális gép használata erőforrás-kezelő sablonként] [ capture-image-linux-step-2-create-vm-image] Linux
* Jelentkezzen be előfizetés *az bejelentkezés*
* Válassza ki az előfizetés *az fiók beállítása – előfizetés `<subscription name or id`>*
* A virtuális merevlemez feltöltése *az tárolási blob feltöltése* -lásd [az Azure Storage az Azure parancssori felület használatával][storage-azure-cli]
* (Választható) Hozzon létre egy felügyelt lemezképet a virtuális merevlemez *az lemezkép létrehozása* -lásd: https://docs.microsoft.com/cli/azure/image#az_image_create
* Hozzon létre egy új virtuális Gépet a feltöltött virtuális Merevlemezt vagy lemezképet felügyelt megadása az operációs rendszer lemezeként *az virtuális gép létrehozása* és paraméter *– kép*

**Sablon**

* Használja *sysprep* Windows vagy *waagent-deprovision* tekintse meg a VM - általánosítja Linux [Sysprep műszaki útmutatója](https://technet.microsoft.com/library/cc766049.aspx) Windows vagy [rögzítése egy Linux virtuális gép használata erőforrás-kezelő sablonként] [ capture-image-linux-step-2-create-vm-image] Linux
* A Powershell vagy Azure CLI feltöltéséhez
* (Választható) Hozzon létre egy felügyelt lemezképet a virtuális merevlemezről végzett Powershell, az Azure parancssori felület és az Azure-portálon
* Telepítse a virtuális Gépet egy JSON-sablon hivatkozik a kép VHD, ahogy az a [példa JSON sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-image/azuredeploy.json) vagy a felügyelt lemezképe használatával, ahogy az [példa JSON sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>A helyszíni virtuális merevlemezek vagy kezelt lemezek letöltése
Azure-infrastruktúra szolgáltatásként nincs csak a virtuális merevlemezek és az SAP tölthet fel éppen egy egyirányú utcaneve rendszerek. SAP áthelyezheti az Azure-ból rendszerek vissza az helyszíni világon is.

A letöltés ideje alatt a virtuális merevlemezek vagy kezelt lemezek nem lehet aktív. Akkor is, ha a virtuális gépekhez csatlakoztatott lemezzel letöltését a virtuális gép leállítása és felszabadítása. lehetséges kell. Ha szeretné letölteni a tartalmat, amely majd használandó új rendszer beállítása a helyszíni és elfogadható, ha a letöltési ideje és az új rendszer, hogy a rendszer az Azure-ban a telepítés során, működésbe adatbázis , nem sikerült egy hosszú állásidő elkerülése érdekében végezzen egy tömörített adatbázis biztonsági mentése lemezre, és csak töltse le, hogy a lemez az operációs rendszer alap virtuális gép is letöltésük helyett.

#### <a name="powershell"></a>PowerShell

  * Felügyelt lemezes letöltése  
  Először is elérheti az alapul szolgáló a blob, a kezelt lemez. Ezután másolja az alapul szolgáló blob egy új tárfiókot, és ez a tárfiók a blob letöltését.

  ```powershell
  $access = Grant-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzureRmStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzureStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzureStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzureStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzureRmVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

  * Virtuális merevlemez letöltése  
  Miután az SAP rendszer le van állítva, és a virtuális gép le van állítva, segítségével a PowerShell-parancsmag mentés-AzureRmVhd a helyszíni célszámítógépen töltse le a VHD lemezek vissza a helyszíni tétele. Ehhez a virtuális Merevlemezt, amely a "tárolóban szakasz" URL-CÍMÉT kell az Azure portálon (Nyissa meg a Tárfiók és a tároló, ahol a virtuális merevlemez létrehozásának szükség), és meg kell tudni, ahol a virtuális Merevlemezt kell átmásolni.

  Majd kihasználhatják a parancs által egyszerűen a paramétert definiáló SourceUri töltse le a VHD-és a LocalFilePath az URL-címet a virtuális merevlemez (beleértve a neve) fizikai helye. A parancs nézhet:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  A Save-AzureRmVhd parancsmag további részletekért ellenőrizze Itt <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Felügyelt lemezes letöltése  
  Először is elérheti az alapul szolgáló a blob, a kezelt lemez. Ezután másolja az alapul szolgáló blob egy új tárfiókot, és ez a tárfiók a blob letöltését.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Virtuális merevlemez letöltése   
  Miután az SAP rendszer le van állítva, és a virtuális gép le van állítva, az Azure CLI parancs használata _az azure storage-blob letöltési_ töltheti le a VHD-t a helyszíni célszámítógépen lemezek biztonsági másolatot a helyszíni világába. A "tárolási szakaszának" az Azure portálon (Nyissa meg a Tárfiók és a tároló, ahol a virtuális merevlemez létrehozásának szükség), és ahol a virtuális merevlemez fel kell tudnia kell keresés copi művelet végrehajtásához, hogy kell-e a neve és a VHD-fájl, melyet a tároló a kiadás alatt.

  A parancs kihasználhatják majd meghatározásával egyszerűen a paraméterek blob és a tároló a VHD-fájl letöltése és a cél fizikai célhelyként a VHD-fájl (beleértve a nevét). A parancs nézhet:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Virtuális gépek és az Azure lemezek átvitele
#### <a name="copying-sap-systems-within-azure"></a>Az Azure SAP rendszerek másolása
Az SAP rendszer vagy akár dedikált adatbázis-kezelő kiszolgáló egy SAP alkalmazásréteg valószínűleg áll, több olyan lemezeket, amelyeket az operációs rendszer, a bináris fájljait, vagy az adatok és a naplófájl (oka) t az SAP-adatbázis. A lemezek másolásának Azure funkció sem a lemezek helyi lemezre mentése Azure funkcióinak rendelkezik egy szinkronizálási mechanizmus, amely pillanatkép több lemez szinkron módon történik. Ezért a másolt vagy mentett lemezt az állapot akkor is, ha azokat, szemben az azonos virtuális gép csatlakoztatott eltérő lenne. Ez azt jelenti, hogy, hogy a különböző adatok és a különböző lemezeken található logfile(s) a konkrét esetben a végén adatbázis lenne inkonzisztens.

**Megkötését: Másolja vagy mentse a lemezek, amelyek részei egy SAP rendszerkonfiguráció van szüksége az SAP rendszer leállítása, és állítsa le a telepített virtuális gép is kell. Csak ezután másolja, vagy töltse le a lemezek létrehozásához vagy az SAP rendszer egy példányát az Azure vagy a helyszíni készlete.**

Adatlemezek egy Azure Storage-fiókot a VHD-fájlokat képes tárolni, és a virtuális gépekhez közvetlenül csatlakoztatott vagy képként használni. Ebben az esetben a virtuális merevlemez másolódik egy másik helyre a virtuális géphez csatolni kívánt előtt. A teljes nevet, a VHD-fájl az Azure-ban Azure belül egyedinek kell lennie. Amint azt korábban már említettük, az értéke milyen a következőhöz hasonló háromrészes név:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Az adatlemezek kezelt lemezeken is lehet. Ebben az esetben a kezelt lemez előtt a virtuális géphez csatolni kívánt új kezelt lemez létrehozásához használt. A kezelt lemez neve erőforráscsoporton belül egyedinek kell lennie.

##### <a name="powershell"></a>PowerShell
Azure PowerShell-parancsmagok segítségével másolja a virtuális merevlemez, ahogy az [Ez a cikk][storage-powershell-guide-full-copy-vhd]. Segítségével hozhat létre egy új kezelt lemezt New-AzureRmDiskConfig és a New-AzureRmDisk a következő példában látható módon.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Azure CLI segítségével másolja a virtuális merevlemez, ahogy az [Ez a cikk][storage-azure-cli-copy-blobs]. Hozhat létre egy új kezelt lemezt, *az lemez létrehozása* a következő példában látható módon.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Az Azure Storage-eszközök
* <http://storageexplorer.com/>

Azure Tártallózók Professional kiadása itt található:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

A virtuális merevlemez magát egy tárfiókon belül példány egy folyamatot, amely (SAN-hardverre pillanatképeinek Lusta és az írás hasonlóan) csupán néhány másodpercet vesz igénybe. Miután egy másolatot a VHD-fájl csatlakoztatása egy virtuális gép vagy másolja a VHD-fájl csatolása a virtuális gépek képként használni.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzureRmVM

# attach a copy of the managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzureRmDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzureRmDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzureRmVM
```
##### <a name="cli-20"></a>CLI 2.0
```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Azure Storage-fiókok között a lemezek másolása
Ez a feladat nem hajtható végre, az Azure portálon. Használhatja az Azure PowerShell-parancsmagok, Azure CLI és egy külső tároló böngészőt. A PowerShell-parancsmagjaival vagy a parancssori felület parancsait hozhat létre és kezelheti a blobok, amelyek közé tartozik a másolandó aszinkron módon BLOB Storage-fiókok és az Azure-előfizetés belül régiók között.

##### <a name="powershell"></a>PowerShell
Virtuális merevlemezek előfizetések között is másolhatja. További információk [Ez a cikk][storage-powershell-guide-full-copy-vhd].

A PS-parancsmag logika alapvető áramló így néz ki:

* Hozzon létre egy a tárfiók környezetét a **forrás** tárfiók *New-AzureStorageContext* -lásd: <https://msdn.microsoft.com/library/dn806380.aspx>
* Hozzon létre egy a tárfiók környezetét a **cél** tárfiók *New-AzureStorageContext* -lásd: <https://msdn.microsoft.com/library/dn806380.aspx>
* Indítsa el a másolatot

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* A hurok-ben található példány állapotának ellenőrzése

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Az új virtuális merevlemez csatlakoztatása egy virtuális gép fent leírt módon.

További példák: [Ez a cikk][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Indítsa el a másolatot

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Ellenőrizze az állapotát, ha az ismétlődő másolása

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Az új virtuális merevlemez csatlakoztatása egy virtuális gép fent leírt módon.

További példák: [Ez a cikk][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Lemez kezelése
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Virtuális gép/lemez-szerkezet SAP központi telepítések
Ideális esetben a virtuális gépek és a kapcsolódó lemezek szerkezete kezelésének kell nagyon egyszerű. A helyszíni telepítésekre az ügyfelek szerkezetének kialakítása a kiszolgáló telepítése az sokféleképpen ki.

* Egy alap lemez, amely tartalmazza az operációs rendszer és az adatbázis-kezelő és/vagy az SAP bináris fájlok. 2015. márciusi, mert a lemez lehet akár 1TB-nál korábbi korlátozások korlátozott a 127 GB helyett.
* Egy vagy több lemezt, amely tartalmazza az adatbázis-kezelő naplófájl az SAP-adatbázist, és a naplófájl az adatbázis-kezelő az ideiglenes tárterület (ha az adatbázis-kezelő támogatja ezt). Ha az adatbázis napló IOPS követelményei magas, szüksége több lemez paritásos az IOPS-kötet szükséges elérése érdekében.
* Lemezt tartalmazó egy vagy két adatbázis az SAP-adatbázis és az adatbázis-kezelő ideiglenes adatfájlok is (ha az adatbázis-kezelő támogatja ezt) száma.

![Az Azure infrastruktúra-szolgáltatási virtuális gép SAP referencia-konfiguráció][planning-guide-figure-1300]

[comment]: <> (MShermannd TODO Linux struktúra írják le.  )

- - -
> ![Windows][Logo_Windows] Windows
>
> Sok ügyfél az imént látott konfigurációkat, például SAP- és adatbázis-kezelő bináris fájlok nincsenek telepítve a c:\ meghajtóra, amelyre az operációs rendszer telepítve lett. Ennek számos oka volt, de azt vissza a legfelső szintű frissülő, általában, hogy a meghajtók kicsi volt, és az operációs rendszer frissítései szükséges további terület éve 10 – 15. Mindkét feltétel nem vonatkoznak ezek a napok túl gyakran többé. Ma a c:\ meghajtó nagy méretű lemezek vagy a virtuális gépek rendelhetők. Ahhoz, hogy a központi telepítések a struktúrában egyszerű tartani, javasoljuk, hogy kövesse a következő telepítési minta SAP NetWeaver rendszerekhez az Azure-ban
>
> A Windows operációs rendszer lapozófájl méretét a d meghajtón (nem állandó lemez) kell lennie.
>
> ![Linux][Logo_Linux] Linux
>
> Helyezze a Linux swapfile /mnt/mnt és az erőforrások a Linux leírtak [Ez a cikk][virtual-machines-linux-agent-user-guide]. A lapozófájl konfigurálható a Linux-ügynök /etc/waagent.conf konfigurációs fájljában. Adja hozzá, vagy módosítsa a következő beállításokat:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Aktiválja a változtatásokat, újra kell indítania a Linux-ügynök

```
sudo service waagent restart
```

Olvassa el az SAP Megjegyzés [1597355] kapcsolatban további részleteket a javasolt lapozófájl méretét a

- - -
Az adatbázis-kezelő az adatfájlok és a tároló típusa szerinti Azure ezeknek a lemezeknek üzemeltetett használt lemezek számát kell határozza meg az IOPS-követelmények és a késés szükséges. Ismerteti a pontos kvóták [Ez a cikk (Linux)] [ virtual-machines-sizes-linux] és [Ez a cikk (Windows)][virtual-machines-sizes-windows].

SAP-telepítések az utolsó 2 évek élménye tanított velünk során néhány tapasztalatokat, amely mint összegezhető:

* Különböző adatfájlok IOPS-forgalom nincs mindig azonos óta a meglévő ügyfél-rendszereken előfordulhat, hogy rendelkezik másképp méretű képviselő az SAP adatbázisának vagy adatbázisainak adatfájljait. Ennek eredményeképpen az kapcsolni lehet jobban RAID konfiguráció over használatával több lemezt a logikai egységek faragottnak kívül azokat adatok fájlok. Nem voltak olyan helyzetekben, különösen a Azure standard szintű tárolást, ahol az IOPS-ráta elérte az adatbázis-kezelő tranzakciónapló szemben egyetlen lemezt a beállított kvótát. Ilyen esetekben a prémium szintű Storage használata ajánlott, vagy egy szoftverek lemezek RAID azt is megteheti összesítése több standard szintű tárolót.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [SQL Server Azure virtuális gépek teljesítménye ajánlott eljárásai][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Szoftveres RAID Linux konfigurálása][virtual-machines-linux-configure-raid]
> * [A Linux virtuális gép az Azure-ban LVM konfigurálása][virtual-machines-linux-configure-lvm]
> * [Az Azure Storage titkos kulcsok és a Linux i/o-optimalizálás](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Prémium szintű Storage jelentős jobb teljesítményt, különösen a kritikus tranzakciós napló írása láthatók. Képes biztosítani az éleshez hasonlító teljesítmény várt SAP-forgatókönyvek esetén ajánlott használni, amelyek kihasználhatják a prémium szintű Azure Storage Virtuálisgép-sorozat.

A következőket kell figyelembe venni, hogy a lemez, amely tartalmazza az operációs rendszer, és azt javasoljuk, a SAP bináris fájljait és az adatbázis (alap virtuális gép), valamint nem többé legfeljebb 127GB. Most rendelkezhet akár 1TB-nál. Ez legyen elég hely a szükséges fájlok, például SAP kötegelt feladat naplófájlokkal együtt tartása.

További javaslatokat és adatbázis-kezelő virtuális gépeken, kifejezetten a további részletekért tekintse meg a [adatbázis-kezelő telepítési útmutatója][dbms-guide]

#### <a name="disk-handling"></a>Lemez kezelése
A legtöbb esetben szüksége ahhoz, hogy az SAP-adatbázisokhoz üzembe helyezés a virtuális gép további lemezek létrehozásához. Fejezetben lemezeinek száma miatt a kapcsolatos a megtartásról [virtuális gép/lemez-szerkezet SAP központi telepítések] [ planning-guide-5.5.1] ebben a dokumentumban. Az Azure-portál lehetővé teszi, hogy a csatolási és leválasztási lemezek, amennyiben egy alap virtuális Gépre van telepítve. A lemezek csak csatlakoztatott vagy leválasztott amikor a virtuális gép működik-e és fut, valamint ha le van állítva. Lemez csatlakoztatása, az Azure-portálon kínál, üres lemez vagy egy meglévő lemez, amely ezen a ponton a időben nem egy másik virtuális Géphez van csatolva.

**Megjegyzés:**: lemezek csak lehet rendelni egy virtuális gép egy adott időpontban.

![Csatlakoztassa / lemezeken Azure standard szintű tárolóval leválasztása][planning-guide-figure-1400]

Új virtuális gép központi telepítése során eldöntheti, hogy szeretné-e használni a felügyelt lemezek, vagy helyezze a lemezek Azure Storage-fiókok. Ha azt szeretné, a prémium szintű Storage, kezelt lemezek használatát javasoljuk.

Ezt követően kell döntse el, hogy új és üres lemez létrehozásához vagy, hogy kívánja-e jelöljön ki egy meglévő lemezt, amely korábban feltöltött, és most kell csatlakoztatni a virtuális Gépet.

**FONTOS**: akkor **nem** állomás gyorsítótárazását használja Azure standard szintű tárolást. A gazdagép gyorsítótár beállítások hagyja meg az alapértelmezés szerinti nincs. Prémium szintű Azure Storage kell engedélyeznie, olvasási gyorsítótárazás Ha az i/o-jellemző többnyire olvasható például tipikus i/o-forgalom adatbázis adatok fájlokra vonatkozóan. Adatbázis-tranzakciós naplófájl esetén gyorsítótárazása használata ajánlott.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Hogyan adatlemezzel az Azure-portálon][virtual-machines-linux-attach-disk-portal]
>
> Ha a lemezek vannak csatolva hozzá, jelentkezzen be a virtuális Gépen nyissa meg a Windows-kezelő szeretné. Ha automount nincs engedélyezve a fejezet ajánlott [beállítás automount a csatlakoztatott lemezekkel][planning-guide-5.5.3], az újonnan csatolt kötet online állapotban és inicializálva kell fordítani kell.
>
> ![Linux][Logo_Linux] Linux
>
> Lemezek vannak csatolva hozzá, ha szeretné-e a Virtuálisgép jelentkezni, és inicializálja a lemezeket, lásd: [Ez a cikk][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Ha az új lemez üres lemez, meg kell formázza a lemezt is. Formázásához, különösen az adatbázis-kezelő adatok és a napló az azonos javaslatok az adatbázis-kezelő az operációs rendszer nélküli telepítések esetében érvényesek.

Említettek szerint fejezetben [a Microsoft Azure virtuális gép koncepció][planning-guide-3.2], egy Azure Storage-fiók nem rendelkezik végtelen erőforrásokkal tekintetében i/o-kötet, iops-érték és az adatok kötet. Adatbázis-kezelő virtuális gépek általában leginkább által érintett. A legjobb, ha az egyes virtuális gépek egy külön Tárfiókot használja, ha néhány nagy i/o adatmennyiség ahhoz, hogy az Azure Storage-fiók kötet belül maradnak telepítendő virtuális gépek lehet. Ellenkező esetben szeretne látni, hogyan anélkül, hogy elérte-e minden egyetlen Tárfiók legfeljebb különböző Storage-fiókok közötti virtuális gépeken is el tudnak osztani. További részleteket ismertetik a [adatbázis-kezelő telepítési útmutatója][dbms-guide]. Ezek a korlátozások is tiszta SAP alkalmazás kiszolgáló virtuális gépek vagy más virtuális gépek, amelyek idővel előfordulhat, hogy további virtuális merevlemezek figyelembe kell venni. Ezen korlátozás alól kivételt képez felügyelt lemezt használni. Ha azt tervezi, a prémium szintű Storage, azt javasoljuk, kezelt lemezre.

Egy másik témakör, amely a Storage-fiókok szükséges, hogy a virtuális merevlemezek tárfiókokban georeplikált kap. A georeplikáció engedélyezve van-e a Tárfiók szintjén, és nem a virtuális gép szintről. A georeplikáció engedélyezve van, ha a virtuális merevlemezeket a Tárfiókon belül a régión belül egy másik Azure adatközpontba történő volna replikálható. Mielőtt eldönti, ez, gondolja a következő korlátozást:

Azure georeplikáció helyileg működik-e az egyes virtuális merevlemez virtuális gépen, és nem replikálja az IOs időrendben között csak egy virtuális merevlemezt a virtuális gép. Ezért a virtuális Merevlemezt, amely jelöli az alap virtuális gép, valamint minden további virtuális merevlemezek a virtuális Géphez csatlakozik, a rendszer replikálja egymástól független. Ez azt jelenti, hogy nincs szinkronizálás között a különböző virtuális merevlemezek változásait. Azt a tényt, hogy az IOs replikálva vannak-e a sorrendet, amelyben azt jelenti, hogy megírásának módjától függetlenül, hogy a georeplikáció nincs a hozzájuk tartozó adatbázisok elosztva a több virtuális merevlemezzel rendelkező adatbázis-kiszolgálók számára. A DBMS mellett is előfordulhat, más alkalmazások, ahol írási figyelmen kívül hagyott folyamatok kezelhetők adatok különböző virtuális merevlemezeken, és ezért fontos változások sorrendjének tartani. Ha ez a követelmény, az Azure-ban a georeplikáció nem engedélyezni kell. E szükséges, vagy szeretné georeplikáció állítja be a virtuális gépek, de nem egy másik készlet függ, már rendezheti az virtuális gépek és azok kapcsolódó virtuális merevlemezek be különböző Storage-fiókok, amelyek rendelkeznek a georeplikáció engedélyezve vagy letiltva.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>A csatlakoztatott lemezek automount beállítása
- - -
> ![Windows][Logo_Windows] Windows
>
> Virtuális gépek, amelyek a saját lemezképek vagy lemezek jönnek létre fontos ellenőrizni, és esetleg a automount paraméter. A paraméter lehetővé teszi a virtuális gép újraindítása vagy újbóli üzembe helyezése az Azure automatikusan újra csatlakoztatni a csatolt/csatlakoztatott meghajtók után.
> A paraméter értéke a lemezképek az Azure piactér a Microsoft biztosítja.
>
> Ahhoz, hogy állítsa be a automount, ellenőrizze a dokumentációban megtalálja Itt a parancssori végrehajtható diskpart.exe listáját:
>
> * [A DiskPart parancssori kapcsolók](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](http://technet.microsoft.com/library/cc753703.aspx)
>
> A Windows parancssori ablakot rendszergazdaként kell megnyitni.
>
> Ha a lemezek vannak csatolva hozzá, jelentkezzen be a virtuális Gépen nyissa meg a Windows-kezelő szeretné. Ha automount fejezetben szereplő ajánlás szerint nem engedélyezett [beállítás automount a csatlakoztatott lemezekkel][planning-guide-5.5.3], az újonnan csatolt kötet > online állapotban és inicializálva kell állítani.
>
> ![Linux][Logo_Linux] Linux
>
> Egy újonnan csatolt üres lemez inicializálása leírtak szerint kell [Ez a cikk][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Szükség új lemezek hozzáadása a /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Végső telepítés
A végső központi telepítés és szövegéről, különösen a központi telepítés SAP kiterjesztett figyelést, jelenítik tekintse meg a [telepítési útmutató][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Azure virtuális gépeken futó SAP rendszerek elérése
Csak felhőalapú forgatókönyvek esetén érdemes SAP grafikus felhasználói felülettel a nyilvános interneten keresztül csatlakozni az SAP rendszereket. Ezekben az esetekben a következő eljárásokat kell alkalmazni.

A dokumentum későbbi szakaszában ismertetjük a más fő forgatókönyv, létesítmények közötti telepítések esetén, amelyek a pont-pont (VPN-alagút) vagy Azure ExpressRoute-kapcsolatot a helyszíni és az Azure rendszerek közötti SAP rendszerek csatlakozik.

### <a name="remote-access-to-sap-systems"></a>Távelérés SAP rendszerekhez
Az Azure Resource Manager nincsenek alapértelmezett végpontok többé például a korábbi klasszikus modellt. Egy Azure ARM VM összes portjai nyitva, amíg:

1. Nincs hálózati biztonsági csoport az alhálózatra vagy a hálózati adapter van definiálva. Azure virtuális gépek hálózati forgalmának úgynevezett "hálózati biztonsági csoportok" via védve legyenek. További információ: [Mi az a hálózati biztonsági csoport (NSG)?][virtual-networks-nsg]
2. Nincsenek Azure Load Balancer a hálózati adapter van definiálva.   

Tekintse meg a klasszikus modellt és ARM architektúra különbségének a [Ez a cikk][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Csak felhőalapú forgatókönyvhöz SAP rendszer és az SAP grafikus felhasználói Felülettel kapcsolat konfigurációja
Lásd: Ez a cikk, amely részleteket a témakör ismerteti: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Virtuális Gépen belül a tűzfal beállításainak módosítása
A tűzfal konfigurálása a virtuális gépeken, a SAP rendszerhez bejövő adatforgalom engedélyezésére szükség lehet.

- - -
> ![Windows][Logo_Windows] Windows
>
> Egy Azure telepített virtuális Gépen belül a Windows tűzfal alapértelmezés szerint be van kapcsolva. Most engedélyeznie kell az SAP-portot kell megnyitni, ellenkező esetben a SAP grafikus felhasználói felület nem tudnak kapcsolódni.
> Ehhez tegye a következőket:
>
> * Nyissa meg a vezérlőpult\rendszer és biztonság\windows tűzfal **speciális beállítások**.
> * Most kattintson a jobb gombbal a bejövő szabályok, és úgy döntene **új szabály**.
> * A következő varázslóban úgy döntene, hogy hozzon létre egy új **Port** szabály.
> * A varázsló a következő lépésben hagyja meg az értéket, az TCP, és írja be a megnyitni kívánt portszámot. Mivel az SAP-példány azonosítója 00, azt 3200 vett igénybe. Ha a példány egy másik példány a szám, a korábbi példányok száma alapján meghatározott portot kell megnyitni.
> * A varázsló következő részében, az elem kell **engedélyezése kapcsolat** be van jelölve.
> * A varázsló a következő lépésben kell határozza meg, hogy a szabály vonatkozik-e tartományi, privát és nyilvános hálózat. Állítsa be, ha szükséges, az igényeinek megfelelően. Azonban összekötésével SAP grafikus felhasználói Felülettel rendelkező nyilvános hálózaton kívülről kell, hogy a szabály vonatkozik. a nyilvános hálózat.
> * A varázsló utolsó lépése, adja meg a szabály nevét, és mentse billentyűkombináció lenyomásával **Befejezés**.
>
> A szabály azonnal érvénybe lép.
>
> ![Port szabályát leíró definíció beolvasása][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> A Linux-lemezképek, az Azure piactéren alapértelmezés szerint nem engedélyezi a iptables tűzfal és a kapcsolatot a SAP rendszerrel működnek. Ha iptables vagy más tűzfal engedélyezve, tekintse meg a iptables vagy a használt tűzfalat, hogy engedélyezze a bejövő TCP-forgalom port 32xx (ahol a xx azt a SAP rendszer rendszer száma) a dokumentációban.
>
>

- - -
#### <a name="security-recommendations"></a>Biztonsági javaslatok
Az SAP grafikus felhasználói felület nem csatlakozik közvetlenül bármelyik az SAP-példányok (port 32xx) fut, de először csatlakozik az SAP üzenet kiszolgáló folyamat (port 36xx) megnyitott porton keresztül. A múltban nagyon ugyanazt a portot lett megadva az üzenet-kiszolgáló által az alkalmazás-példányokban kívánja a belső kommunikáció. Megelőzése érdekében a helyszíni akaratlanul is módosíthatja a belső kommunikációs portok Azure üzenet folytatott kommunikáció az alkalmazás-kiszolgálókat. Ajánlott a SAP üzenet kiszolgáló és az alkalmazás-példányok közötti belső kommunikációs váltson egy másik portszámot a rendelkezik a helyszíni rendszerekre, például egy projekt tesztelési stb fejlesztésére klónja lett klónozása rendszereken. Ezt megteheti az alapértelmezett profil paraméterrel:

> rdisp/msserv_internal
>
>

Ahogy [a SAP üzenet kiszolgáló biztonsági beállításait ](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>SAP-példányok csak felhőalapú telepítés fogalmak
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Az SAP NetWeaver bemutató/képzési forgatókönyv egyetlen VM
![Egyetlen VM SAP bemutató rendszereket futtató virtuális gép ugyanazzal a névvel, egymástól el vannak különítve az Azure Cloud Services csomag][planning-guide-figure-1700]

Ebben a forgatókönyvben (lásd a fejezet [csak felhőalapú] [ planning-guide-2.1] a jelen dokumentum) végrehajtási azt egy tipikus képzési/bemutató módszer, ahol a teljes képzési/bemutató forgatókönyvet tartalmazza egyetlen virtuális gépen. Feltételezzük, hogy a központi telepítés Virtuálisgép-lemezkép sablonok keresztül történik. Is feltételezzük, hogy bemutató/oktatási anyag többszöröse is be kell állítani a virtuális gépek azonos nevű virtuális gépek kell.

A feltételezése létrehozott Virtuálisgép-lemezkép fejezet egyes szakaszokban ismertetett módon [előkészítése virtuális gépek Azure-beli SAP] [ planning-guide-5.2] ebben a dokumentumban.

A forgatókönyv végrehajtásához események sorozatát így néz ki:

##### <a name="powershell"></a>PowerShell
* Hozzon létre egy új erőforráscsoportot minden képzési/bemutató fekvő

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Hozzon létre egy új tárfiókot, ha nem szeretné használni a felügyelt lemezek

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Hozzon létre egy új virtuális hálózat minden képzési/bemutató fekvő ahhoz, hogy az azonos állomásnévvel és IP-címek kihasználtsága. A virtuális hálózat, amely csak az SSH lehetővé teszi, hogy a távoli asztal hozzáférés engedélyezéséhez a 3389-es port és a 22-es portot felé irányuló forgalmat hálózati biztonsági csoport védi.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Hozzon létre egy új nyilvános IP-címet, a virtuális gép az internetről való eléréséhez használható

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Hozzon létre egy új hálózati illesztő a virtuális géphez

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Virtuális gépet hoz létre. A csak felhőalapú alkalmazás esetében minden virtuális gép lesz a néven. A virtuális gépek SAP NetWeaver példánya SAP SID ugyanaz lesz is. Az Azure erőforráscsoporton belül a virtuális gép nevének egyedinek kell lennie, de különböző Azure erőforráscsoportok futtatható virtuális gépek ugyanazzal a névvel. A Windows vagy Linux rendszeren a "Gyökér" alapértelmezett "Rendszergazda" fiók nem érvényesek. Ezért egy másik rendszergazda felhasználónevet kell definiálni és jelszóval. A virtuális gép méretétől is kell definiálni.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Lehetősége van további lemezek hozzáadása, és állítsa vissza a szükséges tartalom. Vegye figyelembe, hogy az összes blob nevének (kívánt URL-címeket a bináris objektumok) Azure belül egyedieknek kell lenniük.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM

# Optional: Attach additional Managed Disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzureRmVM
```

##### <a name="cli"></a>parancssori felület
Az alábbi példakód Linux rendszeren használható. Windows, vagy használjon PowerShell fent leírt módon, vagy % rgName %$rgName helyett használja, és a környezeti változót a Windows-paranccsal például igazítja *beállítása*.

* Hozzon létre egy új erőforráscsoportot minden képzési/bemutató fekvő

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Új tárfiók létrehozása

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Hozzon létre egy új virtuális hálózat minden képzési/bemutató fekvő ahhoz, hogy az azonos állomásnévvel és IP-címek kihasználtsága. A virtuális hálózat, amely csak az SSH lehetővé teszi, hogy a távoli asztal hozzáférés engedélyezéséhez a 3389-es port és a 22-es portot felé irányuló forgalmat hálózati biztonsági csoport védi.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Hozzon létre egy új nyilvános IP-címet, a virtuális gép az internetről való eléréséhez használható

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Hozzon létre egy új hálózati illesztő a virtuális géphez

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Virtuális gépet hoz létre. A csak felhőalapú alkalmazás esetében minden virtuális gép lesz a néven. A virtuális gépek SAP NetWeaver példánya SAP SID ugyanaz lesz is. Az Azure erőforráscsoporton belül a virtuális gép nevének egyedinek kell lennie, de különböző Azure erőforráscsoportok futtatható virtuális gépek ugyanazzal a névvel. A Windows vagy Linux rendszeren a "Gyökér" alapértelmezett "Rendszergazda" fiók nem érvényesek. Ezért egy másik rendszergazda felhasználónevet kell definiálni és jelszóval. A virtuális gép méretétől is kell definiálni.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Lehetősége van további lemezek hozzáadása, és állítsa vissza a szükséges tartalom. Vegye figyelembe, hogy az összes blob nevének (kívánt URL-címeket a bináris objektumok) Azure belül egyedieknek kell lenniük.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Sablon
A minta sablonokat használhat a githubon az azure-gyors üzembe helyezés-sablonok tárházba.

* [Egyszerű Linux virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Egyszerű Windows virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [A kép VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Virtuális gépek, amelyeknek az Azure készletének megvalósítását
Ez csak felhőalapú forgatókönyv, a jellemző forgatókönyv, képzés és bemutató jellegű helyét a szoftver a bemutató/képzési képviselő forgatókönyv nyúlik át több virtuális gép. A különböző virtuális gépek telepítve a különböző összetevőket kell kommunikálnak egymással. Ebben az esetben ebben a forgatókönyvben nem a helyi hálózati kommunikáció, vagy a létesítmények közötti forgatókönyv van szükség.

Ebben a forgatókönyvben a fejezetben ismertetett telepítési kiterjesztése [rendelkező SAP NetWeaver bemutató/képzési forgatókönyv egyetlen virtuális] [ planning-guide-7.1] ebben a dokumentumban. Több virtuális gép ekkor bekerül egy meglévő erőforráscsoportot. Az alábbi példában a képzés fekvő egy SAP ASC/SCS virtuális Gépre, a virtuális gép fut egy adatbázis-kezelő és az SAP Application Server-példány VM áll.

Mielőtt Ez a forgatókönyv kell gondolniuk az alapbeállítások szerint gyakorolt előtt forgatókönyvben.

#### <a name="resource-group-and-virtual-machine-naming"></a>Erőforráscsoport és a virtuális gép elnevezése
Az összes erőforráscsoport-nevek egyedinek kell lennie. A saját elnevezési sémát az erőforrások, például a fejlesztés `<rg-name`>-utótagot.

A virtuális gép neve nem lehet egyedi az erőforráscsoporton belül.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>A különböző virtuális gépek közötti kommunikáció a hálózat beállítása
![Egy Azure virtuális hálózaton belüli virtuális gépek halmaza][planning-guide-figure-1900]

Megelőzése érdekében ütközésének a klónozásával jár, a képzési/bemutató azonos tájak, hozzon létre egy Azure virtuális hálózatot minden fekvő kell. Azure DNS-név feloldása biztosítja, vagy beállíthatja, hogy a saját DNS-kiszolgáló (hogy nem kell további Microsofttól) Azure kívül. Ebben a forgatókönyvben azt a saját DNS konfigurálása nem. Az összes virtuális gép egy Azure virtuális hálózaton belül kommunikációs segítségével hostnames engedélyezve lesz.

Virtuális hálózatok, és nem csak erőforráscsoportok képzési vagy bemutató tájak külön lehetnek:

* Az SAP fekvő beállított kell a saját AD OpenLDAP, és a tartomány kiszolgálónak kell lennie a tájak mindegyikének részét.  
* Az SAP fekvő beállított kell működnie a fix IP-címek összetevőket tartalmaz.

További információt az Azure virtuális hálózatok és hogyan adhat meg hozzájuk található [Ez a cikk][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>SAP virtuális gépek telepítése a vállalati hálózati kapcsolat hibája (létesítmények közötti)
Egy SAP fekvő futtatja, és szeretné osztani az operációs rendszer nélküli csúcskategóriás DBMS kiszolgálók között a központi telepítés, helyszíni virtualizált környezetekben alkalmazás rétegek és kisebb 2-réteg SAP rendszerek és az Azure infrastruktúra-szolgáltatási konfigurálva. A kiinduló feltételezése, SAP rendszerek egy SAP fekvő belül kell kommunikálnak egymással, és számos egyéb szoftverösszetevők, a vállalaton belül, a telepítési képernyőn független telepített. Is lehetnek a végfelhasználó SAP grafikus felhasználói felületének vagy egyéb felületek összekapcsolása a telepítési képernyőn által bevezetett nincs különbség. Ezek a feltételek csak akkor kell teljesíteni, ha a helyszíni Active Directory/OpenLDAP és DNS-szolgáltatásokat a webhely-a-site/több-site kapcsolatot vagy Azure ExpressRoute például személyes kapcsolatok Azure rendszerekhez terjeszteni kell.

Ahhoz, hogy kérjen további háttér a SAP Azure megvalósítási részleteit, javasoljuk, hogy olvassa el a fejezet [fogalmak a Cloud-Only telepítési SAP-példányok] [ planning-guide-7] ebben a dokumentumban, amelyből megtudhatja, néhány a alapvető szerkezet Azure, és hogyan ezeket kell használni az SAP-alkalmazások az Azure-ban.

### <a name="scenario-of-an-sap-landscape"></a>Egy SAP fekvő forgatókönyvet
A létesítmények közötti forgatókönyv nagyjából jelentheti például az alábbi grafikus:

![A helyszíni és az Azure eszközök közötti pont-pont kapcsolat][planning-guide-figure-2100]

Egy olyan forgatókönyvet ismertet a fent látható helyzetet, ahol a helyszíni AD/OpenLDAP és DNS bővítve lettek az Azure-bA. A helyszíni oldalon egy bizonyos IP-címtartomány egy Azure-előfizetés van fenntartva. Az IP-címtartomány társítható egy Azure virtuális hálózatot az Azure oldalán.

#### <a name="security-considerations"></a>Biztonsági szempontok
A minimális követelmény például az SSL/TLS biztonságos kommunikációs protokollok használatának böngészőalapú hozzáférés vagy VPN-alapú kapcsolatot az Azure-szolgáltatások rendszer elérésére. A rendszer azt feltételezi, hogy a vállalatok nagyon másként kezeli-e a VPN-kapcsolat a vállalati hálózat és az Azure között. Egyes vállalatok blankly sérülékennyé portjai. Néhány más vállalatokkal, előfordulhat, hogy szeretne nagyon pontos mely portokat kell megnyitni, stb.

A tipikus SAP az alábbi táblázatban a kommunikációs portok vannak felsorolva. Alapvetően elegendő az SAP-átjáró port megnyitásához.

| Szolgáltatás | Port neve | Példa `<nn`> = 01 | Alapértelmezett tartomány (min, max) | Megjegyzés |
| --- | --- | --- | --- | --- |
| A kézbesítő |sapdp`<nn>` lásd: * |3201 |3200 - 3299 |SAP kézbesítő SAP grafikus felhasználói felület a Windows és a Java által használt |
| Üzenet kiszolgáló |sapms`<sid`> tekintse meg ** |3600 |szabad sapms`<anySID`> |SID SAP-rendszer-ID = |
| Átjáró |sapgw`<nn`> lásd: * |3301 |ingyenes |SAP-átjáróhoz, CPIC és RFC-kommunikációhoz használt |
| SAP-útválasztó |sapdp99 |3299 |ingyenes |Csak CI (központi példány) szolgáltatásnevek telepítése után elvégezhető a /etc/services tetszőleges értékre. |

*) nn = SAP-példányok száma

*) sid SAP-rendszer-ID =

További információk a különböző SAP termékek szükséges portok vagy szolgáltatásokat SAP termékek szerint itt található <http://scn.sap.com/docs/DOC-17124>.
Ez a dokumentum a dedikált portok megnyitása a VPN-eszköz adott SAP termékeket és forgatókönyvek szükséges tudni kell lennie.

Egyéb biztonsági intézkedések, amikor a virtuális gépek telepítését ilyen esetben lehet létrehozni egy [hálózati biztonsági csoport] [ virtual-networks-nsg] hozzáférési szabályok meghatározásához.

### <a name="dealing-with-different-virtual-machine-series"></a>A virtuális gép másik Adatsorozathoz foglalkozó
Elmúlt 12 hónap során a Microsoft számos további Virtuálisgép-típusokon, amelyek eltérnek vagy Vcpu, memória számú hozzáadott vagy fontosabb hardveren futó. Nem minden virtuális gépek támogatottak SAP (lásd a támogatott Virtuálisgép-típusokon SAP feljegyzés [1928533]). Néhány virtuális gépek futtasson másik gazdagépet hardver generációt. A gazdagép hardver generációja az időtartományt, egy Azure-Bővítőegysége első telepített. Azt jelenti, hogy olyan esetek is felmerülhetnek, ha úgy döntött, hogy más Virtuálisgép-méretek nem futtatható a azonos skálázási egység. Rendelkezésre állási csoport méretezési egységek különböző hardver alapján span lehetősége van korlátozva.  Például szeretné futtatni az adatbázis-kezelő A5-A11 virtuális gépek és az SAP alkalmazásréteg G sorozatú virtuális gépeken, ha meg szeretné kényszeríthető, egyetlen SAP rendszer vagy a másik rendelkezésre állási készletek belül különböző SAP rendszerek központi telepítéséhez.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Nyomtatás a helyi hálózati SAP példányból az Azure-ban
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>A létesítmények közötti forgatókönyv TCP/IP felett nyomtatás
A helyszíni TCP/IP-alapú hálózati nyomtatók egy Azure virtuális gép telepítése megegyezik a teljes a vállalati hálózathoz, feltéve, hogy Ön rendelkezik egy VPN-helyek alagút vagy ExpressRoute-kapcsolat létrejött.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ehhez tegye a következőket:
>
> * A konfiguráció varázsló, amely segítségével egyszerűen állítsa be a nyomtató egy Azure virtuális gép egyes hálózati nyomtatók rendelkeznek. Ha nincs varázsló szoftver a nyomtató került, a manuális állítsa be a nyomtató módja hozzon létre egy új TCP/IP nyomtatóportot.
> * Nyissa meg a Vezérlőpult -> eszközök és nyomtatók -> Nyomtató hozzáadása
> * A Hozzáadás gombra a számítógéphez egy TCP/IP-címét vagy állomásnevét használata
> * Az IP-címet, a nyomtató
> * Standard 9100 nyomtatóportot
> * Szükség esetén telepítse a megfelelő illesztőprogramot manuálisan.
>
> ![Linux][Logo_Linux] Linux
>
> * a Windows csak kövesse, például a hálózati nyomtató telepítésére a szokásos eljárás
> * Kövesse a nyilvános Linux útmutatói [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) vagy [Red Hat és Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) nyomtató felvételéhez.
>
>

- - -
![Hálózati nyomtatás][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Gazdagép-alapú nyomtató (megosztott nyomtató) az SMB protokollt a létesítmények közötti forgatókönyv
A nyomtatók állomásalapú nincsenek hálózati-kompatibilis úgy lett kialakítva. De állomásalapú nyomtató a hálózaton található számítógépek között megosztható, mindaddig, amíg a nyomtató csatlakozik-e kapcsolva a számítógép. Csatlakozás a vállalati hálózati helyek vagy ExpressRoute és helyi nyomtató megosztása. Az SMB protokoll helyett DNS NetBIOS név szolgáltatásként használja. Lehet, hogy a DNS-állomás neve eltér a gazdagép NetBIOS-név. A normál esetben, a NetBIOS-állomásnevet és a DNS-állomásnevet azonosak. A DNS-tartomány nem célszerű a NetBIOS-név területen. Ennek megfelelően a DNS-állomásnevet és a DNS-tartomány teljesen minősített DNS-állomásnevet kell nem használható a NetBIOS-névteret.

A nyomtató megosztási azonosít egy egyedi nevet a hálózat:

* Az SMB-állomás (mindig szükséges) állomásneve.
* A megosztás (mindig szükséges) neve.
* Annak a tartománynak a nevét, ha a nyomtató megosztása nincs SAP rendszer ugyanabban a tartományban.
* Emellett a felhasználónév és jelszó lehet kell a nyomtató megosztás eléréséhez.

Útmutató:

- - -
> ![Windows][Logo_Windows] Windows
>
> Helyi nyomtató megosztása.
> Nyissa meg a Windows Explorer és a nyomtató megosztási név írja be az Azure virtuális Géphez.
> A Nyomtatóáttelepítési varázsló végigvezeti a telepítési folyamat.
>
> ![Linux][Logo_Linux] Linux
>
> Néhány példa a hálózati nyomtatók konfigurálása a Linux vagy többek között a fejezet kapcsolatos dokumentáció a Linux nyomtatásra vonatkozó. Működik ugyanúgy egy Azure Linux virtuális gép mindaddig, amíg a virtuális gép VPN része:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL vagy Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>USB-nyomtató (nyomtató továbbítás)
Az Azure-ban egy távoli munkamenet a helyi nyomtató eszközökhöz való hozzáférést biztosít a felhasználók a távoli asztali szolgáltatások képességét nem érhető el.

- - -
> ![Windows][Logo_Windows] Windows
>
> Nyomtatás a Windows további információkat itt talál: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Az SAP integrálását javítása és a létesítmények közötti (TMS) rendszer Azure rendszerek
Az SAP-változás- és átviteli rendszer (TMS) kell megadni exportálásához és importálásához az átviteli kérelmet a fekvő rendszerek között. Feltételezzük, hogy a fejlesztési példányok az SAP rendszer (fejlesztés) találhatók az Azure-ban, mivel a minőségi megbízhatósági (QA) és a hatékony rendszerek (PRD) helyszíni. Továbbá feltételezzük, hogy van-e a központi átviteli könyvtár.

##### <a name="configuring-the-transport-domain"></a>A szállítási tartomány konfigurálása
Konfigurálja a átviteli tartományt a rendszeren, lásd: az átviteli tartományvezérlőként kijelölt [az átviteli tartományvezérlő beállítása](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). A rendszer felhasználó TMSADM jön létre és a szükséges RFC cél jön létre. Ezek a tranzakció SM59 segítségével RFC kapcsolatok ellenőrizheti. Állomásnév-feloldási engedélyezni kell a átviteli tartományon keresztül.

Útmutató:

* A mi esetünkben döntöttünk, a helyszíni QAS rendszer lesz a CTS tartományvezérlő. Tranzakció STM hívja. A TMS párbeszédpanel jelenik meg. A szállítási tartomány konfigurálása párbeszédpanel. (Ez a párbeszédpanel csak akkor jelenik meg, ha még nincs konfigurálva az átviteli tartomány.)
* Győződjön meg arról, hogy engedélyezve van-e az automatikusan létrehozott felhasználó TMSADM (SM59 -> ABAP kapcsolat -> TMSADM@E61.DOMAIN_E61 -> Részletek -> Utilities(M) engedélyezési teszt ->). A tranzakció a kezdeti képernyő STM jelenítsen meg, hogy az SAP rendszer most már működik a átviteli tartomány tartományvezérlőjeként itt látható módon:

![A tartományvezérlőn STM tranzakció indítási képernyő][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>SAP rendszerek például az átviteli tartományban
Az SAP-rendszerek például átviteli tartományban sorrendjét a következőképpen néz ki:

* A fejlesztői rendszeren az Azure-ban nyissa meg a szállítási rendszer (000 ügyfél), és tranzakció STM hívja. Egyéb konfigurációs lehetőségek közül választhat a párbeszédpanelt, és folytassa a rendszer közé tartozik a tartományban. Adja meg a tartományvezérlő a cél gazdagépre ([SAP rendszerek például az átviteli tartomány](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). A rendszer most vár az átviteli tartomány szerepelni.
* Biztonsági okokból majd akkor lépjen vissza a tartományvezérlő meg a kérést. Válassza ki a rendszer áttekintése és jóváhagyása a várakozási rendszer. Erősítse meg a parancssor és a konfigurációs lesznek kiosztva.

Az SAP rendszer mostantól a szükséges rendszerekről tartalmaz információt minden a más SAP átviteli a tartományban. Egy időben a címet az új SAP rendszer adatküldést a más SAP rendszerekre, és az SAP rendszer is meg kell adni a átviteli vezérlő program átviteli profiljában. Ellenőrzi, hogy RFC-dokumentumokat és a tartomány átviteli könyvtár elérésének működni.

A rendszer a konfiguráció a szokásos módon a dokumentációjában leírt folytassa [változás- és a rendszer](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Útmutató:

* Ellenőrizze, hogy a helyszíni STM megfelelően van konfigurálva.
* Győződjön meg arról, hogy az átviteli tartományvezérlő állomásneve megoldhatók a virtuális gépet, az Azure és fordítva visa.
* Hívás, tranzakciót STM -> egyéb konfigurációs -> rendszer közé tartozik a tartományban.
* Erősítse meg a kapcsolat a helyszíni TMS a rendszerben.
* Átviteli útvonalon, csoportok és rétegek konfigurálása a szokásos módon.

A pont-pont csatlakoztatott létesítmények közötti forgatókönyv, a helyszíni és az Azure közötti késés továbbra is lehet jelentős. Ha azt hajtsa végre az objektumoknak az üzemi környezetben fejlesztési és tesztelési rendszerek szállítására sorozatát vagy gondoljon átvitelek vagy támogatási csomag alkalmazása a különböző rendszerek kapcsolatos, akkor vegye figyelembe, hogy a függő központi átviteli könyvtár helye nagy késleltetésű olvasása vagy adatok írása a központi átviteli könyvtárban rendszereivel fog történni. A helyzet hasonlít SAP fekvő konfigurációk ahol a különböző rendszerek keresztül rendelkező jelentős távolságát az adatközpontok különböző adatközpontokban van elosztva.

Kerülő ilyen késés és a gyors olvasása vagy a írna vagy a transport könyvtárból működni rendszert, hogy beállíthat két STM átviteli tartományok (egy a helyszíni és egyet a rendszer az Azure-ban és a szállítási tartományok hivatkozás. Ellenőrizze az ebben a dokumentációban, amelyből megtudhatja, a fogalom a SAP TMS mögött ezeket az alapelveket: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Útmutató:

* (A helyszíni és az Azure) helyekre átviteli tartomány beállítása STM tranzakció használatával <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* A tartományok hivatkozásra tartomány hivatkozást, és erősítse meg a hivatkozást a két tartomány között.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* A csatolt rendszer a konfiguráció terjesztése.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC forgalom Azure-ban és a helyszíni (létesítmények közötti) található SAP példányai között
RFC forgalom közötti rendszerek, amelyek a helyszíni és az Azure-ban működéséhez szükséges. Beállítása egy kapcsolat tranzakció SM59 a forrásrendszerben ahol meg kell határoznia az RFC-kapcsolat a célrendszeren felé. A konfigurációs hasonlít az RFC-kapcsolat szokásos beállításait.

Feltételezzük, hogy a létesítmények közötti forgatókönyv esetén a virtuális gépek, amelyek beépített SAP rendszerek futnak, igénylő kommunikálnak egymással megegyező tartományban található. Ezért egy SAP rendszerek közötti RFC-kapcsolat beállítása nem különböznek a beállítási lépéseket és a bemeneti adatokat a helyszíni forgatókönyvekben.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Az Azure-ban, vagy fordítva található SAP példányokból elérése során helyi fileshares
Az Azure-ban található SAP-példányok tartoznak a vállalat helyszíni fájlmegosztások eléréséhez szükséges. Emellett a helyszíni SAP példányok kell hozzáfér a fájlmegosztásokhoz, amelyek az Azure-ban találhatók. A fájlmegosztások, konfigurálnia kell az engedélyeket és a helyi rendszer megosztási beállítások engedélyezéséhez. Győződjön meg arról, hogy a portok a VPN- vagy ExpressRoute kapcsolattal Azure és az Adatközpont között.

## <a name="supportability"></a>Támogatási lehetőségek
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Az Azure felügyeleti megoldás az SAP
Ahhoz, hogy a figyelő kritikus kritikus SAP rendszerek Azure a figyelési eszközök SAPOSCOL vagy az Azure virtuális gép szolgáltatásgazda egy Azure Figyelőbővítmény keresztül adatokat lekérni az SAP SAP Állomásügynök SAP. Óta az SAP igényeket erősen korlátozott számú SAP-alkalmazások, Microsoft lezárását nem általános implementálja a kért funkciót az Azure, de hagyja az ügyfelek számára a szükséges figyelési összetevőkkel és konfigurációk telepítése a virtuális Az Azure-ban futó gépek. Azonban a figyelési összetevők telepítési és életciklus kezelése főként automatizált lesz az Azure-ban.

#### <a name="solution-design"></a>Megoldásterv
A megoldás használatával engedélyezi a SAP fejlesztett Azure Virtuálisgép-ügynök és a bővítmény keretrendszer architektúrájának alapul. Az Azure Virtuálisgép-ügynök és a bővítmény keretrendszer lényege, engedélyezi az Azure Virtuálisgép-bővítmény katalógusában egy virtuális Gépen belül elérhető szoftverek alkalmazás(ok) telepítését. Az elve mögött a fogalom lényege engedélyezi (ilyen esetekben az Azure Figyelőbővítmény az SAP), speciális funkciókat a virtuális gépek telepítése és a szoftver központi telepítéskor.

Az "Azure Virtuálisgép-ügynök", amely lehetővé teszi az adott Azure Virtuálisgép-bővítmények a virtuális Gépen belül kezelésének van be a nézetmodellbe Windows virtuális gépek alapértelmezés szerint a virtuális gép létrehozása az Azure portálon. SUSE, Red Hat vagy Oracle Linux a Virtuálisgép-ügynök már része az Azure Piactéri lemezképhez. Abban az esetben, ha egy Linux virtuális gép a helyszíni Azure volna feltöltése a Virtuálisgép-ügynök manuális telepítve van.

Az alapvető építőelemeket, amelyekből az SAP keresi a figyelés megoldást az Azure-ban, ez például:

![A Microsoft Azure bővítmény összetevők][planning-guide-figure-2400]

Ahogy a fenti blokk ábrán is látható, az SAP figyelési megoldás egyik részét a Azure Virtuálisgép-lemezkép és Azure bővítmény gyűjteménye, amely Azure műveletei által kezelt globálisan replikált tára van tárolt. A feladata a közös SAP/MS munkacsoport SAP Azure műveletek közzététele az Azure Figyelőbővítmény az SAP új verzióit használható Azure végrehajtásáról működik.

Amikor telepít egy új Windows virtuális Gépet, az Azure Virtuálisgép-ügynök automatikusan szerepel-e a virtuális Gépet. Ez az ügynök függvény, koordinálhatja a be- és konfigurációs az Azure-bővítmények SAP NetWeaver rendszerek figyeléséhez. Linux virtuális gépek az Azure Virtuálisgép-ügynök már része az Azure piactér operációsrendszer-lemezképek.

Van azonban továbbra is szükséges az ügyfél által végrehajtandó minden lépést. Ez az a engedélyezése és a teljesítménynaplózás konfigurációját. A folyamat konfigurációjával kapcsolatos egy PowerShell-parancsfájl vagy a CLI parancs alapján automatikus. A PowerShell parancsfájl letölthető a Microsoft Azure Script Center leírtak szerint a [telepítési útmutató][deployment-guide].

Az Azure felügyeleti megoldás az SAP általános architektúrája néz ki:

![Az Azure felügyeleti megoldás az SAP NetWeaver][planning-guide-figure-2500]

**A pontos útmutató és részletes lépéseit a PowerShell-parancsmagjaival vagy a CLI-parancs segítségével központi telepítések során, kövesse az utasításokat, a megadott a [telepítési útmutató][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integráció az Azure található SAP példányának SAProuter
Azure-beli SAP példányok kell lennie, valamint SAProuter érhető el.

![SAP-útválasztó hálózati kapcsolat][planning-guide-figure-2600]

Egy SAProuter lehetővé teszi, hogy a TCP/IP-kommunikáció, ha nincs közvetlen IP-kapcsolat részt vevő rendszerek között. Ez lehetővé teszi az az előnye, hogy a végpont a kommunikációs partnerek között nincs kapcsolat szükséges hálózati szintű. A SAProuter alapértelmezés szerint 3299 portot figyel.
SAP példányok csatlakozhatnak egy SAProuter keresztül kell a kapcsolódási kísérlet a SAProuter karakterláncot és a gazdagép nevét meg kell adni.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Amennyiben a fókusz a dokumentum SAP NetWeaver általában vagy az SAP NetWeaver ABAP a verem. Ez kis területen figyelembe kell venni bizonyos az SAP Java verem találhatók. A legfontosabb kizárólag alapú SAP NetWeaver Java-alkalmazások egyike az SAP vállalati portálon. Más SAP NetWeaver alapú alkalmazások, például SAP-PI és SAP megoldás kezelő használata az SAP NetWeaver ABAP és a Java-verem. Ezért alapértékekkel szükség van, valamint a SAP NetWeaver Java verem kapcsolatos különleges szempontok figyelembe venni.

### <a name="sap-enterprise-portal"></a>SAP vállalati portál
A telepítő egy SAP portál egy Azure virtuális gép nem eltér a helyi telepítés a központi telepítése a létesítmények közötti forgatókönyv. A DNS-ben a helyszínen történik, mert a port beállítása az egyes példányok szerint konfigurált helyszíni végezhető. A javaslatok és korlátozások, amennyiben a jelen dokumentumban ismertetett érvényes olyan alkalmazások, mint az SAP vállalati portál vagy a SAP NetWeaver Java verem általában.

![Elérhetőségi SAP-portál][planning-guide-figure-2700]

Egy speciális környezet-forgatókönyv szerint egyes ügyfelek a közvetlen módon a SAP vállalati portál kapcsolódik az internethez, míg a virtuális gép gazdagép csatlakozik a vállalati hálózati helyek a VPN-alagúton vagy ExpressRoute keresztül. Ilyen esetben meg kell győződjön meg arról, hogy adott portok nyitva és nem blokkolja tűzfal vagy a hálózati biztonsági csoport. Azonos beállítás alkalmazható, ha azt szeretné, csak felhőalapú esetén helyszíni SAP Java-példány az csatlakozni kell.

A kezdeti portál URI nem HTTP (s):`<Portalserver`>: Ha a port 50000 plusz (Systemnumber?? alkotta 5XX00/irj 100). Az alapértelmezett portál URI az SAP rendszer 00 `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. További részletekért rá egy pillantást <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Végpont-konfiguráció][planning-guide-figure-2800]

Ha szeretné az URL-címet és/vagy a portok az SAP vállalati portál testreszabása, ellenőrizze a jelen dokumentáció:

* [Módosítsa a portál URL-címe](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alapértelmezett portszámok, Portal portszámok módosítása](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Magas rendelkezésre állású (HA) és a katasztrófa utáni helyreállítás (DR) az Azure virtuális gépeken futó SAP NetWeaver
### <a name="definition-of-terminologies"></a>Terminológiát meghatározása
A kifejezés **magas rendelkezésre ÁLLÁS** általában kapcsolódik, amely minimálisra csökkenti az informatikai szolgáltatások keresztül IT szolgáltatások üzleti folytonosság megadásával technológiák redundáns, hibatűrő vagy feladatátvételi védett összetevők belül a **azonos** adatközpont. Ebben az esetben egy Azure-régión belül.

**Vészhelyreállítás (DR)** is célzott IT szolgáltatások megszűnésének minimalizálja a és a helyreállítás azonban keresztben **különböző** adatközpontokban, amelyek általában több száz kilométer távolságra található. A mi esetünkben általában különböző Azure-régiókat geopolitikai régión belül, vagy Ön által létrehozott, az ügyfél között.

### <a name="overview-of-high-availability"></a>A magas rendelkezésre állás – Áttekintés
Azt is külön SAP magas rendelkezésre állás az Azure-ban két részre vonatkozó vitafórum:

* **Azure-infrastruktúra magas rendelkezésre állású**, például magas rendelkezésre ÁLLÁSÚ (VM) számítási, hálózati, tárolási stb és az SAP rendelkezésre állásának növelése előnyeit.
* **SAP alkalmazás magas rendelkezésre állású**, például a magas rendelkezésre ÁLLÁSÚ az SAP szoftverösszetevőket:
  * SAP alkalmazáskiszolgálók
  * SAP ASC/SCS példány
  * Adatbázis-kiszolgálót

és hogyan azt kombinálható az Azure-infrastruktúra magas rendelkezésre ÁLLÁSÚ.

SAP a magas rendelkezésre állás, az Azure-ban van néhány különbség az SAP magas rendelkezésre állás a helyi fizikai vagy virtuális környezetben képest. Elérhető a következő dokumentum ismerteti a szabványos SAP magas rendelkezésre állású konfiguráció Windows rendszeren virtualizált környezetben: <http://scn.sap.com/docs/DOC-44415>. Nincs sapinst integrált SAP-magas rendelkezésre ÁLLÁSÚ konfiguráció Linux például Windows létezik. Vonatkozó SAP magas rendelkezésre ÁLLÁSÚ Linux helyszíni további információkat itt találja: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra magas rendelkezésre állás
Jelenleg egy 99,9 %-os single-VM szolgáltatásiszint-szerződés. Ha meg szeretne ismerkedni hogyan lehet, hogy meg egy virtuális rendelkezésre állását, például egyszerűen hozhat létre a különböző elérhető Azure SLA-k szorzatát: <https://azure.microsoft.com/support/legal/sla/>.

A számítás alapja havonta vagy 43200 perc 30 nap. Ezért 21,6 perc 0,05 % állásidő felel meg. A szokásos módon a különböző szolgáltatások rendelkezésre állásának szorzási fogja az alábbi módon:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) 

Például:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 vagy egy 99.75 % rendelkezésre állását.

#### <a name="virtual-machine-vm-high-availability"></a>A virtuális gép (VM) magas rendelkezésre állás
Az Azure platform események, amelyek hatással lehetnek a virtuális gépek rendelkezésre állásának két típusa van: a tervezett karbantartást és a nem tervezett karbantartás.

* Tervezett karbantartási események a Microsoft által készített az alapul szolgáló Azure platform átfogó megbízhatóságát, teljesítményét és a platform infrastruktúra, amely a virtuális gépek futnak a biztonsági javítása érdekében rendszeres frissítések érhetők el.
* Nem tervezett karbantartási események fordulhat elő, ha a hardver- vagy a virtuális gép alapul szolgáló fizikai infrastruktúra valamilyen módon hibát jelzett. Ez lehet helyi hálózati hiba, a helyi lemezek meghibásodása, vagy egyéb állványszintű meghibásodások. Ilyen hiba lép fel, ha az Azure platformon automatikusan áttelepíti a virtuális gép a virtuális gépet egy megfelelő fizikai kiszolgálóhoz szolgáltató a nem megfelelő fizikai kiszolgálóról. Ilyen esetek ritkán lépnek fel, de ezek is okozhatják a virtuális gép újraindítását.

Ebben a dokumentációban talál további részleteket: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Az Azure Storage redundancia
A Microsoft Azure Storage-fiók adatainak mindig replikálódik a tartósság és magas rendelkezésre állás, az Azure Storage SLA még átmeneti hardverhibák esetén állásuk értekezlet biztosításához.

Mivel az Azure Storage alapértelmezés szerint megakadályozza az adatok három képek, RAID5 vagy több Azure lemezre RAID1 nem szükségesek.

Ebben a cikkben talál további részleteket: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Azure-infrastruktúra virtuális gép újraindítása az SAP-alkalmazásokból magasabb rendelkezésre állás biztosítása érdekében használata
Ha úgy dönt, hogy nem használja a Funkciók, például a Windows Server feladatátvételi fürtszolgáltatási (WSFC) vagy támasztja Linux (jelenleg csak a SLES 12 rendszert és az annál magasabb támogatott), az SAP rendszer az Azure-beli tervezett és nem tervezett leállások elleni védelméhez első Azure virtuális gép újraindítása fizikai kiszolgálói infrastruktúra és a teljes alapul szolgáló Azure platformon.

> [!NOTE]
> Fontos említse meg, hogy Azure virtuális gép újraindítása elsősorban védi a virtuális gépek és alkalmazásokat nem. Indítsa újra a virtuális gép nem biztosít magas rendelkezésre állás a SAP-alkalmazásokból, de az infrastruktúra rendelkezésre állását és ezért közvetve magasabb rendszerek elérhetőségének megszűnését SAP bizonyos szintű kínálnak. A ideig tart egy gazdagép tervezett vagy nem tervezett leállás után indítsa újra a virtuális gép is van nem az SLA-t. Ezért ez a módszer a magas rendelkezésre állás nem alkalmas A SCS vagy az adatbázis-kezelő például SAP a rendszer kritikus összetevői.
>
>

A magas rendelkezésre állás fontos infrastruktúra elem az tároló. Például az Azure Storage szolgáltatásiszint-szerződés 99,9 % rendelkezésre állási. Ha egy telepíti a lemezzel rendelkező összes virtuális gép egy egyetlen Azure Storage-fiók esetén lehetséges Azure Storage elérhetetlensége, akkor kerülnek, Azure Storage-fiókhoz tartozó összes virtuális gépet, is minden SAP összetevőiről és virtuális gépek belül futtatott elérhetetlensége be.  

Ahelyett, hogy minden virtuális gép üzembe egy egyetlen Azure Storage-fiókot, használhatja a kijelölt tárhelycsomópont fiókokat az egyes virtuális gépek, és így általános virtuális gép és az SAP rendelkezésre állásának növeléséhez több független Azure Storage-fiókok használatával.

Azure-lemezeket felügyelt automatikusan kerülnek a tartalék tartomány a virtuális gép vannak csatolva. Ha két virtuális gép rendelkezésre állási és felügyelt lemezt használ, a platform kezeli a különböző tartalék tartományok, valamint a kezelt lemezek terjesztése. Ha azt tervezi, a prémium szintű Storage, erősen ajánlott lemezek kezelése, valamint használatával.

Az SAP NetWeaver rendszer által használt Azure-infrastruktúra magas rendelkezésre ÁLLÁSÚ és a storage-fiókok egy mintaarchitektúrája nézhet ki:

![Magas rendelkezésre ÁLLÁSÚ SAP magasabb rendelkezésre állásának eléréséhez Azure-infrastruktúra használatával][planning-guide-figure-2900]

Egy Azure-infrastruktúra magas rendelkezésre ÁLLÁSÚ és felügyelt lemezeket használó SAP NetWeaver a rendszer mintaarchitektúrája nézhet ki:

![Magas rendelkezésre ÁLLÁSÚ SAP magasabb rendelkezésre állásának eléréséhez Azure-infrastruktúra használatával][planning-guide-figure-2901]

SAP-összetevők kritikus azt érhető el a következő eddig:

* Magas rendelkezésre állású SAP Alkalmazáskiszolgáló (AS)

  SAP alkalmazáskiszolgáló-példányok a redundáns összetevők. Minden egyes SAP, példány telepítve van a saját virtuális Gépet, amely futtatja a egy másik Azure hiba és a tartomány frissítése (lásd: fejezetek [tartalék tartományok] [ planning-guide-3.2.1] és [frissítési tartományok] [ planning-guide-3.2.2]). Ez biztosítja Azure rendelkezésre állási csoportokkal (című [Azure rendelkezésre állási készletek][planning-guide-3.2.3]). Egy Azure hiba vagy frissítéséhez tartományi lehetséges tervezett vagy nem tervezett elérhetetlensége fog okozhat a virtuális gépek a korlátozott számú hiányában az SAP-AS példányt.

  Minden egyes SAP, mivel a példány kerül a saját Azure Storage-fiók – lehetséges hiányában egy Azure Storage-fiók hiányában csak egy virtuális gép, akkor az az SAP-AS példányt. Azonban vegye figyelembe, hogy nincs maximális száma Azure Storage-fiókok egy Azure-előfizetéssel belül. (A) SCS-példány automatikus indítás biztosításához a virtuális gép újraindítása után, az automatikus indítási paraméter (A) SCS-példányban állítsa be start fejezetben ismertetett profil [használatával Autostart SAP-példányok][planning-guide-11.5].
  Olvassa el is fejezet [magas rendelkezésre állású SAP alkalmazáskiszolgálók] [ planning-guide-11.4.1] további részleteket.

  Akkor is, ha a lemez felügyelt használata esetén a lemezek tárolódnak az Azure-Tárfiók, és egy tárolási leállás az esemény nem érhető el.

* *Magasabb* SAP rendelkezésre állását (A) SCS példány

  Itt azt hasznosítani Azure VM indítsa újra a virtuális gép védelmét, SAP (A) SCS telepített példánya. Az Azure tervezett vagy nem tervezett leállás esetén kiszolgálója, virtuális gépek újraindul egy másik elérhető kiszolgálón. Amint azt korábban említettük, Azure virtuális gép újraindítása elsősorban védi a virtuális gépek és alkalmazásokat nem, ez esetben a (A) SCS-példányban. A virtuális gép újraindítása keresztül jelenleg lesz érhető el SAP (A) SCS példány közvetve magasabb rendelkezésre állását. (A) SCS-példány automatikus indítás biztosításához a virtuális gép újraindítása után, feltétlenül állítson be automatikus indítását paraméter (A) SCS példány start profilban fejezetben ismertetett [használatával Autostart SAP-példányok][planning-guide-11.5]. Ez azt jelenti, hogy a (A) SCS hibaként egyetlen pont, (SPOF) egy virtuális gépen futó példány lesz a meghatározó tényező az egész SAP fekvő rendelkezésre állását.

* *Magasabb* rendelkezésre állási adatbázis-kezelő kiszolgáló

  Itt a SAP (A) SCS példány használati eset hasonló, azt használata Azure VM indítsa újra a virtuális Géphez a telepített adatbázis-kezelő szoftver védelmére, és azt az adatbázis-kezelő szoftver használatával indítsa újra a virtuális gép magas rendelkezésre állás érdekében elérése.
  Egyetlen virtuális gépen futó DBMS egyben a SPOF, és a meghatározó tényező az egész SAP fekvő rendelkezésre állását.

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP alkalmazás magas rendelkezésre állásra Azure IaaS
Teljes SAP rendszer magas rendelkezésre állás biztosítása érdekében, minden kritikus SAP rendszer összetevők, például redundáns SAP alkalmazáskiszolgálókat, és egyedi összetevők (például egyetlen pont, hiba) például SAP (A) SCS-példány és az adatbázis-kezelő védeni kell.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Az SAP alkalmazáskiszolgálók magas rendelkezésre állás
Az SAP-alkalmazás kiszolgálók/párbeszédpanel példányok nincs szükség egy adott magas rendelkezésre állású megoldás gondolniuk. Magas rendelkezésre állású egyszerűen valósul meg a redundancia és ezáltal rendelkező elegendő azokat a különböző virtuális gépek. Azok az összes kell helyezni az azonos Azure rendelkezésre állási csoport elkerülése érdekében, hogy a virtuális gépek tervezett karbantartás leállások során egy időben előfordulhat, hogy frissíthetők. Az alapvető funkciókat épít, különböző frissítési és tartalék tartományok belül egy Azure méretezési egység, amely már jelent fejezetben [frissítési tartományok][planning-guide-3.2.2]. Az Azure rendelkezésre állási készletek fejezetben bemutatott [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] ebben a dokumentumban.

Nincs hiba, és a frissítési tartományok, amelyek segítségével Azure rendelkezésre állási csoport egy Azure méretezési egység belül korlátlan számú van. Ez azt jelenti, hogy a virtuális gépek száma üzembe egy rendelkezésre állási csoportban, előbb vagy később egynél több virtuális gép ugyanazon hiba vagy frissítéséhez tartományi fejeződik be.

Néhány SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépeken telepíti, és feltéve, hogy azt a kapott a következő öt frissítési tartományok, a következő kép jelenik meg a végén. Rendelkezésre állási csoportok hiba és a frissítési tartományok maximális száma a jövőben esetleg módosító:

![Magas rendelkezésre ÁLLÁSÚ SAP alkalmazáskiszolgáló az Azure-ban][planning-guide-figure-3000]

Ebben a dokumentációban talál további részleteket: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Az SAP (A) SCS példányához, a Windows a magas rendelkezésre állás
Windows Server feladatátvételi fürt (WSFC) egy olyan gyakran használt megoldás az SAP (A) SCS példány védelmét. Azt is integrálva van egy "magas rendelkezésre ÁLLÁSÚ telepítés" formában sapinst. Ezen a ponton a időben történő nincs beállításához a szükséges Windows Server feladatátvételi fürtszolgáltatás ugyanúgy fordult elő a helyi funkciókat nyújt az Azure-infrastruktúra.

2016. január az Azure felhőalapú platform, a Windows operációs rendszer nem biztosít a két Azure virtuális gépek között megosztott lemezt a fürt megosztott kötetei használatának lehetőségét.

Egy érvényes megoldás, ha a 3. fél szoftver, amely megosztott kötet biztosítja, mivel a szinkron és átlátható lemez replikációt, amely integrálható a WSFC használatát. Ez a megközelítés azt jelenti, hogy csak az aktív fürtcsomóponton érhessék el az egyik lemez példány egy időben. Január 2016 a magas rendelkezésre ÁLLÁSÚ konfiguráció támogatott windowsos vendég operációs rendszer Azure virtuális gépeken, valamint a 3. fél szoftver SIOS DataKeeper SAP (A) SCS-példányhoz védelme érdekében.

A SIOS DataKeeper megoldás a azzal, hogy biztosítja a Windows feladatátvételi fürtöket egy megosztott lemez fürterőforrását:

* Egy további Azure virtuális merevlemez csatolva a virtuális gépek (VM), amelyek a Windows-fürt konfigurációjába mindegyikének
* Mindkét virtuális gép csomópontokon futó SIOS DataKeeper Cluster Edition
* SIOS DataKeeper Cluster Edition szinkron módon tükrözi a további virtuális merevlemez tartalmát úgy, hogy a forrás virtuális gépeknek további virtuális merevlemezre csatlakoztatott kötet csatlakoztatott kötet a cél virtuális gép.
* SIOS DataKeeper a forrás- és helyi kötetek absztrakt, és azok Windows feladatátvevő fürt, amely egyetlen megosztott lemezként bemutatásának.

Windows rendszerű feladatátvevő fürtök telepítésével SIOS DataKeeper és az SAP található összes részletes a [fürtszolgáltatás SAP ASC példányhoz használatával Windows Server feladatátvevő fürt SIOS DataKeeper rendelkező Azure] [ ha-guide-classic] találhatók meg.

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Az SAP (A) SCS példány Linux magas rendelkezésre állás
Től Dec 2015 nincs is egyenértékű megosztott lemezre WSFC Azure Linux virtuális gépekhez. 3. fél szoftverrel SIOS a Windows-alternatív megoldásokat a rendszer nem érvényesíti még az SAP futó Azure Linux.

#### <a name="high-availability-for-the-sap-database-instance"></a>Az SAP-adatbázispéldány magas rendelkezésre állás
A szokásos SAP adatbázis-kezelő magas rendelkezésre ÁLLÁSÚ telepítése a két adatbázis-kezelő virtuális gépeken, ahol adatbázis-kezelő magas rendelkezésre állású funkció használható replikálja az adatokat az aktív DBMS példányból a második virtuális gép be a passzív adatbázis-kezelő példánya alapul.

Az adatbázis-kezelő magas rendelkezésre állású és vész helyreállítási funkciók általában, valamint a meghatározott adatbázis-kezelő ismerteti a [adatbázis-kezelő telepítési útmutatója][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Végpontok közötti magas rendelkezésre állás a teljes SAP rendszerhez
Teljes SAP NetWeaver magas rendelkezésre ÁLLÁSÚ architektúrájának az Azure - két példa egy a Windows és Linux egyet.

Nem felügyelt csak lemezek: A fogalmakat, ahogy az alábbi leírásban esetleg kicsit megsértik, amikor sok SAP-rendszerek telepítése és a telepített virtuális gépek száma túllépte a maximális száma a tárfiókok előfizetésenként. Ebben az esetben virtuális gépek virtuális merevlemezeket kell egy Tárfiókon belül használható együtt. Általában tennénk VHD-k az SAP alkalmazásréteg virtuális gépek különböző SAP rendszerek kombinálásával.  Azt is együtt a különböző VHD-k különböző adatbázis-kezelő virtuális gépek különböző SAP rendszerek egy Azure Storage-fiókot. Azure Storage-fiókok IOPS-korlátok vonatkoznak ezáltal tartva (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Magas rendelkezésre ÁLLÁSÚ Windows rendszeren
![SAP NetWeaver alkalmazás magas rendelkezésre ÁLLÁSÚ architektúra Azure IaaS SQL-kiszolgálót][planning-guide-figure-3200]

A következő Azure szerkezet által infrastrukturális problémára gyakorolt hatásának minimalizálása érdekében és javítását üzemeltetéséhez használt az SAP NetWeaver rendszerhez:

* A teljes rendszer Azure (kötelező – ugyanazon a helyen futtatnia kell az adatbázis-kezelő réteg, (A) SCS példány és a teljes alkalmazás réteg) van telepítve.
* A teljes rendszer fut (kötelező) egy Azure-előfizetéssel belül.
* A teljes rendszert futtat egy Azure virtuális hálózaton belül (kötelező).
* A három rendelkezésre állási be a virtuális gépeket egy SAP rendszer való ugyanahhoz a virtuális hálózathoz tartozó virtuális gépek mellett is lehetőség.
* Adatbázis-kezelő példányok egy SAP rendszer futó összes virtuális gép van egy rendelkezésre állási csoportban. Feltételezzük, hogy van-e az adatbázis-kezelő példányok esetében a rendszer futtató óta natív adatbázis-kezelő magas rendelkezésre funkcióit használják, mint például az SQL Server AlwaysOn vagy Oracle Data Guard egynél több virtuális.
* Adatbázis-kezelő példányok futó összes virtuális gép saját tárfiókot használni. Adatbázis-kezelő adatainak és naplókönyvtárainak fájlok lesznek replikálva az egy tárfiókot szinkronizálja az adatokat az adatbázis-kezelő magas rendelkezésre állású funkciókat használja egy másik tárfiókhoz. Egy tárfiókot elérhetetlensége miatt hiányában egy SQL-Windows-fürt csomópontja, de nem a teljes SQL Server szolgáltatás.
* (A) SCS példányát egy SAP rendszert futtató összes virtuális gép van egy rendelkezésre állási csoportban. A Windows Server feladatátvételi fürt (WSFC) úgy van konfigurálva, virtuális gépek védelméhez (A) belül SCS példány.
* (A) SCS példányok futó összes virtuális gép saját tárfiókot használni. (A) SCS példány fájlok és a SAP globális mappa lesznek replikálva az egy tárfiókot SIOS DataKeeper replikációt használ egy másik tárfiókhoz. Hiányában egy tárfiókot, akkor az egyik elérhetetlensége (A) SCS Windows fürtcsomópontra, de nem teljes (A) SCS szolgáltatás.
* Képviselő az SAP alkalmazásréteg-kiszolgáló virtuális gépek vannak egy harmadik rendelkezésre állási csoport.
* SAP alkalmazáskiszolgálók futó összes virtuális gép saját tárfiókot használni. Egy tárfiókot elérhetetlensége miatt hiányában egy SAP alkalmazáskiszolgáló, ha más SAP-AS továbbra is futni.

Az alábbi ábra mutatja a azonos fekvő felügyelt lemezekkel.

![SAP NetWeaver alkalmazás magas rendelkezésre ÁLLÁSÚ architektúra Azure IaaS SQL-kiszolgálót][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Magas rendelkezésre ÁLLÁSÚ Linux rendszeren
Az SAP magas rendelkezésre ÁLLÁSÚ Azure Linux architektúra tulajdonképpen ugyanaz, mint a Windows fent leírt módon. Tekintse meg a SAP Megjegyzés [1928533] támogatott magas rendelkezésre állású megoldások listáját.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Automatikus indítás SAP-példányok használata
SAP SAP példányok elindítani a virtuális Gépen belül az operációs rendszer elindítása után azonnal funkciót kínál. A pontos lépések az SAP Tudásbázis volt dokumentált [1909114]. Azonban SAP nem javasolja a beállítás használata többé nincs vezérlő sorrendjében példány újraindul, mert egynél több virtuális gép feltételezve van hatással, vagy több példány futtatott virtuális gépenként. Feltéve, hogy egy SAP application server-példány a virtuális gépek és a kis-és egy virtuális végül első újraindítása a jellemző Azure forgatókönyv, az automatikus indítási nincs igazán fontos, és ez a paraméter hozzáadásával engedélyezhető:

    Autostart = 1

A start profilba SAP ABAP és/vagy a Java-példány.

> [!NOTE]
> Az automatikus indítási paraméter néhány downfalls is rendelkezhet. Részletesen, a paraméter egy SAP ABAP vagy Java példány elindítása a példány kapcsolódó Windows/Linux-szolgáltatás elindítása váltja. Hogy biztosan van így, amikor az operációs rendszer elindul. Azonban az SAP-szolgáltatások újraindítása is egy közös lépésként SAP szoftver életciklus-kezelési funkciók, például a SUM vagy más frissítéseket, vagy frissíti. Ezek a funkciók nem vár egy példányát az összes automatikusan újraindul. Ezért a Autostart paraméter le kell tiltani az ilyen feladatok futtatása előtt. Az automatikus indítási paraméternek is nem használandó vannak foglalva, ASC/SCS/CI például SAP-példányok.
>
>

Tekintse meg a további információhoz autostart az SAP-példányok itt:

* [SAP, valamint a Unix kiszolgáló indítása/leállítása indítása/leállítása](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Indítása és leállítása SAP NetWeaver felügyeleti ügynökök](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Indítsa el a HANA-adatbázisból automatikus engedélyezése](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Nagyobb 3 szintű SAP rendszerek
Magas rendelkezésre állású aspektusainak 3 szintű SAP konfigurációk kapott tárgyalt korábbi szakaszokban már. De mi a helyzet a rendszerek, ahol az adatbázis-kezelő kiszolgáló követelményei túl nagy ahhoz, azok található, Azure, de az SAP alkalmazásréteg is telepíthető, az Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>3 rétegű SAP konfigurációk helye
Az alkalmazás réteg leíró nem támogatja a helyszíni és az Azure közötti réteg saját magát vagy az alkalmazás és az adatbázis-kezelő. Az SAP-rendszer teljesen telepített helyi vagy az Azure-ban. Nem is támogatott rendelkeznek az alkalmazáskiszolgálókat, futtassa a helyszíni és más Azure-ban. Ez az ismertető kiindulópontjaként. Azt is nem támogatják a kell rendelkeznie az adatbázis-kezelő összetevők egy SAP rendszer és az SAP server alkalmazásréteg két különböző Azure-régiókban telepítve. USA nyugati régiója és SAP alkalmazás rétegben Államok középső például DBMS. Nem támogatja az ilyen konfigurációt oka, hogy a késés érzékenységi a SAP NetWeaver architektúra.

Azonban során az elmúlt évben center fejlesztett partnerek közös helyének az Azure-régiókat. Ezeket a helyeket közös gyakran történik a nagyon közel a fizikai az Azure data központok belül egy Azure-régiót. A rövid távolság és a kapcsolat az Azure ExpressRoute segítségével a közös elhelyezés eszközök eredményezhet, amely kisebb, mint 2ms késleltetése. Ilyen esetben az adatbázis-kezelő réteg (beleértve a tárolási SAN/NAS) található ilyen a közös elhelyezés és az SAP alkalmazásréteg az Azure-ban lehetőség. Től Dec 2015 jelenleg nem áll a telepítések hasonlóan. Azonban nem SAP alkalmazástelepítések rendelkező, különböző ügyfelektől már ilyen megközelítések használ.

### <a name="offline-backup-of-sap-systems"></a>Offline biztonsági másolat az SAP-rendszerek
Függ az SAP-konfiguráció (rétegének 2 vagy 3 szintű) van kiválasztva a biztonsági mentéséhez szükséges lehet. A tartalom a virtuális gép plusz maga az adatbázis biztonsági mentésére. A DBMS kapcsolatos biztonsági mentések várható adatbázis metódusával kell elvégezni. A különböző adatbázisokhoz tartozó részletes leírása megtalálható [DBMS útmutató][dbms-guide]. Másrészről a SAP adatok készíthető (beleértve az adatbázis tartalmát is) offline módon ebben a szakaszban leírtak szerint online és a következő szakaszban leírtak szerint.

Az offline biztonsági másolat a virtuális Gépet az Azure portálon keresztül leállítására és az alap virtuális lemez és az összes csatlakoztatott lemez a virtuális gép másolatát alapvetően szükséges. Ezzel a virtuális gép és a kapcsolódó lemezt idő kép ponttá megőrzik. Javasoljuk, hogy a biztonsági mentések átmásolja egy másik Azure Storage-fiókot. Ezért eljárása a fejezet [Azure Storage-fiókok között a lemezek másolásának] [ planning-guide-5.4.2] ebben a dokumentumban csak akkor vonatkozik.
A leállás mellett egy Azure-portál használatával is megteheti Powershell vagy a CLI itt leírtak szerint: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Visszaállítás, az állapotban, amelyek az alap virtuális gép, valamint az alap virtuális gép eredeti lemezek törlése, és a csatlakoztatott lemezek, a mentett lemezek másolása az eredeti Tárfiók vagy erőforrás csoport felügyelt lemezek és a rendszer majd újratelepítésével.
Ez a cikk ezt a folyamatot, a Powershell parancsfájl hogyan példáját mutatja be: <http://www.westerndevs.com/azure-snapshots/>

Győződjön meg arról, hogy egy új SAP licenc telepítése, mert egy virtuális gép biztonsági mentését fent leírt módon állítja vissza létrehoz egy új hardver kulcsot.

### <a name="online-backup-of-an-sap-system"></a>Az SAP rendszer online biztonsági mentés
Az adatbázis-kezelő a biztonsági mentés DBMS-specifikus metódusával leírtak szerint a [DBMS útmutató][dbms-guide].

Más virtuális gépek, a SAP rendszerben készíthető funkciójával Azure virtuális gépek biztonsági mentéséhez. Az Azure virtuális gép biztonsági mentése korai 2015 kapott bevezetett és eközben szabványos módszer biztonsági mentése a teljes Azure-ban. Azure biztonsági mentés Azure-ban tárolja a biztonsági mentések, és lehetővé teszi, hogy a virtuális gépek visszaállítási újra.

> [!NOTE]
> Től Dec 2015 VM biztonsági mentéssel megőrzése nélkül SAP használt egyedi virtuális gép azonosítója licencelési. Ez azt jelenti, hogy a virtuális gép biztonsági másolat visszaállítása egy SAP új licenckulcs telepítése szükséges, mivel a visszaállított virtuális Gépet egy új virtuális Gépet, és nem helyettesíti a korábbi egy mentett kell tekinteni.
>
> ![Windows][Logo_Windows] Windows
>
> Elméletileg, virtuális gépek, amelyek futtatási adatbázisok biztonsági másolat készíthető következetesen, valamint ha az adatbázis-kezelő rendszer támogatja a Windows VSS (kötet árnyékmásolata szolgáltatás <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>), mint például az SQL létezik.
> Azonban figyelembe időpontban adatbázisok visszaállítása Azure virtuális gép biztonsági másolatok alapján, amelyek nem lehetséges. A javaslat ezért biztonsági mentéshez az adatbázisok adatbázis-kezelő funkciójú ahelyett, hogy az Azure virtuális gép biztonsági mentése.
>
> Ismerkedjen meg az Azure virtuális gépek biztonsági mentéséhez indítsa el itt: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Egyéb lehetőségek a Microsoft Data Protection Manager telepítése egy Azure virtuális gép és egy Azure Backup szolgáltatás a biztonsági mentés/visszaállítás adatbázisokhoz kombinációjának használatára. További információk itt találhatók: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Nincs a Windows VSS lévő Linux megfelelője. Ezért csak a fájlkonzisztens biztonsági mentések is lehetséges, de nem alkalmazáskonzisztens biztonsági mentés. Az SAP DBMS biztonsági mentést kell végezni az adatbázis-kezelő szolgáltatással. A fájlrendszer, amely tartalmazza az SAP-kapcsolatos adatokat is menthető, például használatával bont lásd itt: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Az éles SAP tájak vész-Helyreállítási helyként Azure
Mid 2014 óta különböző összetevőket Hyper-V, a System Center és az Azure-bővítmények engedélyezése az Azure használatát vész-Helyreállítási helyként helyszíni Hyper-v rendszerű virtuális gépek.

Egy blog, és részletesen leírja a megoldás központi telepítéséről itt dokumentált: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Összegzés
A legfontosabb, a magas rendelkezésre állás SAP rendszerekhez az Azure-ban a következők:

* Ezen a ponton az időben a SAP hibaérzékeny pontok kialakulását nem védhető ugyanúgy lehet végezni helyszíni telepítések esetén. A hiba oka, hogy a megosztott lemez fürtök nem még hozható létre az Azure-ban a 3. fél szoftverek használata nélkül.
* A DBMS réteg kell használnia, amely nem megosztott lemez fürt technológia szükséges adatbázis-kezelő funkcióit. Részletek ismertetett a [DBMS útmutató][dbms-guide].
* Az Azure infrastruktúra vagy a gazdagép karbantartási tartalék tartományok belül előforduló problémákról a hatás minimalizálása érdekében ajánlott Azure rendelkezésre állási csoportok:
  * Javasoljuk, hogy egy rendelkezésre állási készlet az SAP alkalmazásréteg rendelkezik.
  * Javasoljuk, hogy egy külön rendelkezésre állási csoport a SAP DBMS réteg rendelkezik.
  * NEM ajánlott, az azonos rendelkezésre állási készletét, különböző SAP rendszert virtuális gépeinek alkalmazandó.
  * Prémium szintű felügyelt lemez használata ajánlott.
* Az SAP DBMS réteg alkalmazásában biztonsági mentést, ellenőrizze a [DBMS útmutató][dbms-guide].
* SAP párbeszédpanel példányok biztonsági másolatának szabálykészletében kevés mert általában gyorsabb egyszerű párbeszédpanel példányok újratelepíteni.
* Biztonsági mentés a virtuális Gépet, amely tartalmazza a globális könyvtár az SAP rendszer, és azt a különböző példányok, a profilok értelme és kell végezhető el a Windows biztonsági másolat vagy, például a Linux bont. Mivel a Windows Server 2008 (R2) és a Windows Server 2012 (R2) közötti különbségeket, amelyek révén könnyebben készítsen biztonsági másolatot a korábbi Windows Server használatával feloldja, javasoljuk, Windows Server 2012 (R2) Windows vendég operációs rendszerként való futtatásra.
