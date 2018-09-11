---
title: Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver |} A Microsoft Docs
description: Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b1f937a71a0ff5b8030c922073dc463af3c8430
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44349262"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver
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
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
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

A Microsoft Azure lehetővé teszi a vállalatok számára, hogy a számítási és tárolási erőforrások szerez minimális idő nélkül hosszadalmas beszerzési ciklusokat. Az Azure Virtual machines szolgáltatás lehetővé teszi a cégeknek üzembe helyezése a klasszikus alkalmazások, például az SAP NetWeaver-alapú alkalmazások az Azure-ba, és a megbízhatóságot és rendelkezésre állást kiterjesztése további erőforrás érhető el a helyszíni nélkül. Az Azure Virtual Machine Services támogatja a létesítmények közötti kapcsolatok, amely lehetővé teszi a vállalatok számára, hogy az Azure Virtual Machines aktívan integrálhat saját helyszíni tartományok, a Magánfelhők és az SAP-rendszer rendszeren is.
Ez a tanulmány alapjait mutatja be, a Microsoft Azure virtuális gép ismerteti, és az Azure-beli SAP NetWeaver-telepítéseket vonatkozó tervezési és megvalósítási szempontokat ismerteti, és ilyen kell lennie a tényleges megkezdése előtt olvassa el a dokumentumot az SAP NetWeaver az Azure-ban üzemelő példányok.
A tanulmány az SAP-telepítési dokumentációt és SAP-megjegyzések, amelyek tartalmazzák a telepítések és SAP-szoftverek központi telepítései az elsődleges erőforrásokat a megadott platformok egészíti ki.

## <a name="summary"></a>Összegzés
Felhő-számítástechnika széles körben használt fogalom, amely egyre több fontosságát az informatikai iparágban, belül van hozd a kisvállalatok nagy, nemzetközi vállalatok.

Microsoft Azure a Microsoft új lehetőségek széles skáláját nyújtó Felhőszolgáltatás-platformja. Most már ügyfelek képesek gyors kiépítés és megszüntetni hozzárendeléseket alkalmazásokat a felhőben, szolgáltatásként, akkor nem érvényesülnek a technikai vagy költségvetési korlátozások. Ahelyett idő és a költségvetés beruházó hardver-infrastruktúra, vállalatok koncentrálhat az alkalmazás, üzleti folyamatokat, és az ügyfeleknek és felhasználóknak.

A Microsoft Azure Virtual Machine Services szolgáltatással a Microsoft átfogó szolgáltatott infrastruktúra (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Ez a tanulmány megtervezheti és megvalósíthatja az SAP NetWeaver-alapú alkalmazások a Microsoft Azure-ban, a választott platformnak módját ismerteti.

A maga koncentrál két fő szempontjait:

* Az első rész az Azure-on SAP NetWeaver-alapú alkalmazások esetén a két támogatott üzembe helyezési minta ismerteti. Az SAP-környezetekhez szem előtt az Azure általános kezelési is ismerteti.
* A második rész részleteit az első részében leírt két különböző forgatókönyvek megvalósítása.

További forrásokért tekintse meg a fejezet [erőforrások] [ planning-guide-1.2] ebben a dokumentumban.

### <a name="definitions-upfront"></a>Előre definíciók
A dokumentumban a következő kifejezéseket használjuk:

* IaaS: Infrastruktúra-szolgáltatás
* PaaS: Szolgáltatásként nyújtott platformon
* SaaS: Szolgáltatott szoftver
* Az SAP-összetevőt: egyedi SAP alkalmazások például az ECC, a BW, a megoldás Manager vagy a EP  Az SAP-összetevők hagyományos ABAP és Java-technológiák vagy egy nem NetWeaver-alapú alkalmazás, például az üzleti objektumok alapulhat.
* Az SAP-környezet: egy vagy több SAP összetevő logikusan például fejlesztési, QAS, képzés, DR vagy éles üzleti függvény végrehajtásához.
* SAP-rendszeren: A kifejezés a teljes SAP-eszközök az ügyfél informatikai környezetét. Az SAP-rendszeren, tartalmazza az összes éles környezetben, és nem éles környezetekben.
* SAP-rendszerhez: Adatbázis-kezelő réteg és kombinációja alkalmazásréteg, például egy fejlesztőrendszerrel SAP ERP, SAP BW tesztgépen, SAP CRM-előállítási rendszerek stb. Az Azure-környezetek nem támogatott ezen két réteg között a helyszíni és az Azure osztani. Az SAP-rendszer vagy az azt jelenti, hogy üzembe helyezte a helyszínen, vagy azt az Azure-ban üzemel. Telepíthet azonban egy az Azure-ban vagy a helyszíni SAP-rendszeren, a különböző rendszerek. Például sikerült telepíteni az SAP CRM fejlesztési és rendszerek tesztelése az Azure azonban az SAP CRM rendszert a helyi környezetben.
* Kizárólag felhőalapú üzembe helyezés: egy központi telepítést, ahol az Azure-előfizetés nem kapcsolódik site-to-site vagy a helyszíni hálózati infrastruktúrát ExpressRoute-kapcsolaton keresztül. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések akkor is, a "csak Felhőbeli" című szakasz ismertet. Ezzel a módszerrel telepített virtuális gépek az interneten és a egy nyilvános IP-címet és/vagy az Azure-ban a virtuális gépekhez rendelt nyilvános DNS-név használatával érhetők el. A Microsoft Windows, a helyszíni Active Directory (AD) és a DNS nem lett kiterjesztve, az Azure-telepítések az ilyen típusú. Ezért a virtuális gépek nem részei a helyszíni Active Directoryban. Ugyanez vonatkozik a Linux-megvalósítások, például OpenLDAP + Kerberos használatával.

> [!NOTE]
> Ebben a dokumentumban csak felhőalapú üzembe helyezés van definiálva, ezeket kizárólag az Azure Active Directory kiterjesztés nélküli teljes SAP-környezetünk futnak / OpenLDAP vagy a nyilvános felhőbe helyszíni névfeloldás. Kizárólag felhőalapú konfigurációk nem támogatottak az éles SAP-rendszereit vagy konfigurációk, ahol az SAP STM vagy más helyszíni erőforrásokat kell használható üzemeltetett Azure-ra és a hozzá tartozó a helyszíni erőforrások az SAP-rendszerek között.
>
>

* Létesítmények közötti: Egy forgatókönyvet, ahol a virtuális gépek Azure-előfizetéssel, amely rendelkezik a site-to-site, többhelyes vagy az ExpressRoute-kapcsolat a helyszíni kommunikálhassanak és az Azure közötti üzembe ismerteti. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések is rendelkezésre állnak, létesítmények közötti forgatókönyvek leírtak szerint. A kapcsolat az az oka, hogy kiterjesztése a helyszíni tartományokra, a helyszíni Active Directory/OpenLDAP és a helyszíni DNS az Azure-bA. Az Azure-előfizetés objektumok kiterjed a helyszíni környezet változásaihoz. Ez a bővítmény kapcsolatban, a virtuális gépek a helyszíni tartomány része lehet. Tartományi felhasználókat a helyszíni tartomány férhet hozzá a kiszolgálókat, és futtathatja a szolgáltatások a virtuális gépeken (például adatbázis-kezelő szolgáltatások). Lehetőség a helyszíni üzembe helyezett virtuális gépek és az Azure üzembe helyezett virtuális gépek közötti kommunikációt és a névfeloldás. Ez a helyzet leggyakoribb és szinte kizárólagos üzembe helyezéséhez SAP-eszközök az Azure-bA. További információkért lásd: [ez] [ vpn-gateway-cross-premises-options] cikk és [ez][vpn-gateway-site-to-site-create].

> [!NOTE]
> Létesítmények közötti SAP-rendszereit, amelyeknél a helyszíni tartomány tagjai az Azure Virtual machines gépeken futó SAP-rendszerek központi telepítései éles SAP-rendszerek támogatottak. Létesítmények közötti konfigurációkat üzembe helyezésének részek támogatottak, vagy hajtsa végre az SAP-környezetünk az Azure-bA. Még az Azure-ban futó a teljes SAP-rendszeren, ezeket a virtuális gépek is tagja, a helyszíni tartomány és a HIRDETÉSEK/OpenLDAP igényel. A-dokumentáció korábbi verziói a beszéltünk hibrid-informatikai alkalmazási helyzetek, ahol az előfizetési időszak *hibrid* rootolva van, az a tény, hogy nincs-e a létesítmények közötti kapcsolatok a helyszíni és az Azure között. Emellett a tény, hogy a virtuális gépek az Azure-ban a helyszíni Active Directory részei / OpenLDAP.
>
>

Bizonyos Microsoft-dokumentációt létesítmények közötti forgatókönyv különösen az adatbázis-kezelő magas rendelkezésre ÁLLÁS konfigurációk esetén egy kicsit másképp ismerteti. Az SAP-kapcsolatos dokumentumok esetében a létesítmények közötti forgatókönyv csak forrni kezd, hogy a site-to-site vagy privát (ExpressRoute) kapcsolat és a tény, hogy az SAP-rendszeren oszlik el a helyszíni és az Azure között.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Erőforrások
A következő kiegészítő útmutatók érhetők el a témakör az SAP-környezetét az Azure-ban:

* [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver (Ez a dokumentum)][planning-guide]
* [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]

> [!IMPORTANT]
> Bárhol is lehetséges a hivatkozó SAP telepítési útmutatóra használatos (referencia InstGuide-01, lásd: <http://service.sap.com/instguides>). Esetén, az előfeltételeket és a telepítési folyamat, az SAP NetWeaver telepítési útmutatóit mindig olvassa el körültekintően, mivel ez a dokumentum csak SAP NetWeaver-rendszerek telepítése a Microsoft Azure virtuális gép adott feladatokat mutatja be.
>
>

A témakör az Azure-beli SAP kapcsolódó az alábbi SAP-megjegyzések:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-on: támogatott termékek és méretezése |
| [2015553] |A Microsoft Azure-beli SAP: támogatás előfeltételei |
| [1999351] |Továbbfejlesztett Azure Monitoring for SAP hibaelhárítási |
| [2178632] |Metrikák figyelése a Microsoft Azure-beli SAP-kulcs |
| [1409604] |A Windows virtualizálási: Enhanced Monitoring |
| [2191498] |SAP használata Linux az Azure-ral: Enhanced Monitoring |
| [2243692] |A Microsoft Azure (IaaS) virtuális gép Linux: SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési jegyzetek |
| [2002167] |Red Hat Enterprise Linux 7.x: telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítése és frissítése |
| [1597355] |Linux-lapozóterület javaslat |

Emellett olvassa el a [Állapotváltozás Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden SAP-megjegyzések, amely tartalmazza a Linuxhoz készült.

Általános alapértelmezett korlátozások és az Azure-előfizetések maximális korlátozások található [Ez a cikk][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Lehetséges forgatókönyvek
SAP gyakran látható egy vállalatok legfontosabb üzleti szempontból alapvető fontosságú alkalmazáshoz. Az architektúra és a műveletek ezeket az alkalmazásokat csak a bonyolult, és fontos, hogy teljesülnek-követelményeket a rendelkezésre állást és teljesítményt biztosítva.

Így a vállalatok rendelkeznek úgy gondolja, hogy alaposan arról, hogy mely alkalmazások futtathatók a nyilvános felhőalapú környezetbe, a kiválasztott felhőszolgáltató független.

Lehetséges rendszertípusok üzembe helyezéséhez SAP NetWeaver-alapú alkalmazások belül nyilvános felhőbeli környezeteket az alábbiak:

1. Közepes méretű éles rendszerek
2. Fejlesztési rendszerek
3. Tesztelési rendszerek
4. Prototípus-rendszerek
5. Tanulás / bemutató rendszerek

Annak érdekében, hogy sikeresen üzembe SAP-rendszereit az Azure IaaS vagy IaaS általánosságban, fontos a hagyományos outsourcers vagy gazdagépek az ajánlatok és az IaaS-ajánlatok közötti lényeges különbségeket. A hagyományos szolgáltató vagy a outsourcer alkalmazkodik a számítási feladatok futtatására szeretne infrastruktúra (hálózati, tárolási és kiszolgáló típusú), mivel feladata, ehelyett az ügyfél, válassza ki a megfelelő számítási feladatot az IaaS központi telepítéséhez.

Első lépésként szükséges ellenőrizze az alábbiakat:

* Az SAP támogatott az Azure virtuális gépek típusai
* Az SAP Azure-on támogatott termékek és kiadások
* A támogatott operációs rendszer és az adatbázis-kezelő kiadja az adott SAP kiadásokban az Azure-ban
* Különböző Azure-termékváltozatok által nyújtott SAP átviteli sebesség

Az ezekre a kérdésekre adott válaszokat az SAP-Jegyzetnek olvasható [1928533].

A második lépésben Azure erőforrás- és sávszélesség-korlátozások kell a helyszíni rendszerek tényleges erőforrás-felhasználásának össze kell hasonlítani. Emiatt szükséges az Azure, az SAP területén támogatott típusok a különböző képességekkel ismernie kell:

* Virtuális gép különböző típusú a CPU és memória-erőforrások és a
* Virtuális gép különböző típusú IOPS sávszélesség és
* Virtuális gép különböző típusú hálózati képességeit.

Az adatokat a legtöbb található [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Ne feledje, hogy a korlátok a fenti hivatkozás szerepel az egyes felső korlátok. Ez nem jelenti azt, amely a korlátok a az erőforrások, például iops-érték adható meg minden körülmények között. A kivétel, ha a Processzor és memória-erőforrások egy kiválasztott VM-típusú. Az SAP által támogatott virtuális gép esetében a CPU és memória-erőforrások találhatók a virtuális Gépen belüli használat ideje fenntartott, ezért bármely pontján.

A Microsoft Azure platformon, más IaaS platformokon, például az egy több-bérlős platform. Ennek eredményeképpen tárolási, hálózati és egyéb erőforrások bérlők között vannak megosztva. Intelligens szabályozás és a kvótaházirendek logikai megakadályozza, hogy egy bérlő egy másik bérlőben (zajos szomszédok) teljesítményét érintő drasztikus módon szolgál. Bár az Azure-beli logikai próbál eltérésekre ne észlelt sávszélesség kis méretű, magas megosztott platformok általában nagyobb eltérések az erőforrás/sávszélesség rendelkezésre állása, mint a számos ügyfél a saját helyszíni üzemelő bevezetni. Ennek eredményeképpen Ön is szembesülhet kapcsolatban (a kötet, valamint késés) hálózati vagy tárolási I/O sávszélesség különböző szintjei perc perc. Annak a valószínűsége, hogy egy Azure-beli SAP-rendszerrel tapasztalhat, mint nagyobb eltérésekre egy a helyszíni rendszerben kell figyelembe venni.

Egy utolsó lépéseként kiértékelni a rendelkezésre állási követelmények vonatkoznak. Ez akkor fordulhat elő, hogy az alapul szolgáló Azure-infrastruktúra első frissíteni, és megköveteli a gazdagépeken futó virtuális gépeket újra kell indítani. Ezekben az esetekben azokon a gazdagépeken futó virtuális gépek kellene lennie leállításra és újraindításra is. Az ilyen karbantartási időzítése munkaidejében kiegészítő egy adott régióban történik, de viszonylag nagy a potenciális ablak, amely során néhány óra egy újraindítás. Számos különböző technológiákat az Azure platform, amely beállítható úgy, hogy néhány vagy összes ilyen frissítések hatásainak csökkentése érdekében. Jövőbeli fejlesztések az Azure platformon, az adatbázis-kezelő és SAP alkalmazás tervezték, hogy az ilyen újraindítást hatásának minimalizálása érdekében.

Annak érdekében, hogy körét áthelyezzük az Azure az SAP-rendszer sikeres telepítéséhez, a helyszíni SAP rendszer(ek) operációs rendszer, adatbázis, és a SAP-alkalmazások meg kell jelennie az SAP az Azure-támogatási mátrixa fér belül az erőforrásokat az Azure infrastruktúrát biztosíthat és is dolgozhat, amely a rendelkezésre állási SLA-k a Microsoft Azure-ajánlatokkal. Ezek a rendszerek meghatározott kell döntse el, egy, a következő két üzembe helyezési forgatókönyveket.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Kizárólag felhőalapú – virtuális gépek telepítéséhez az Azure-bA a helyszíni ügyfél hálózaton függőségek nélkül
![Az SAP bemutató vagy az Azure-ban üzembe helyezett képzési forgatókönyv egyetlen virtuális gép][planning-guide-figure-100]

Ebben a forgatókönyvben a jellemző betanítások vagy bemutató rendszerek esetében, amelyen telepítve vannak az SAP és az SAP szoftverek összes összetevő egyetlen virtuális Gépen belül. Éles SAP-rendszerek nem támogatottak ebben a telepítési forgatókönyvben. Ebben a forgatókönyvben általában megfelel az alábbi követelményeknek:

* A virtuális gépekből a nyilvános hálózaton keresztül érhetők el. A helyszíni hálózathoz az bemutatók vagy betanítások tartalmat vagy az ügyfél a tulajdonos vagy a vállalat a virtuális gépeken futtatott alkalmazások közvetlen hálózati kapcsolat már nem szükséges.
* Esetén a betanítások vagy bemutató forgatókönyvet képviselő több virtuális gép hálózati kommunikáció és a névfeloldás kell a virtuális gépek között működik. De a csoporton közötti kommunikáció kell elkülöníteni, hogy több virtuálisgép-csoportokon egymás mellett beavatkozás nélkül telepíthetők.  
* Internetkapcsolat szükség a felhasználó a távoli jelentkezzen be az Azure-ban üzemeltetett virtuális gépekhez. Attól függően, a vendég operációs rendszer, terminál szolgáltatások/RDS vagy VNC ssh segítségével eléri a virtuális Gépet, vagy a betanítási feladatok teljesítése, vagy hajtsa végre a bemutatókat. Ha SAP 3200, 3300 & 3600 is például portok is elérhető az SAP alkalmazáspéldány bármely internethez csatlakoztatott asztali elérhető lesz.
* Az SAP rendszer(ek) (és a VM(s)) jelölik az Azure-ban, amely csak a végfelhasználói hozzáférés a nyilvános internetkapcsolatra van szükség, és nem szükséges más Azure-beli virtuális gépek csatlakozni egy önálló forgatókönyvet.
* SAPGUI és a egy böngésző telepítése és futtatása közvetlenül a virtuális gépen.
* Gyors visszaállítását egy virtuális Gépet az eredeti állapotra kell és új központi telepítését az eredeti állapotban ismét szükség.
* Bemutató és képzési forgatókönyvek esetében amely is felismerte, hogy több virtuális gépre, az Active Directory OpenLDAP és/vagy a DNS szolgáltatás van szükség az egyes virtuális gépek.

![Virtuális gép egy bemutatót vagy képzési forgatókönyv az Azure-Felhőszolgáltatásban képviselő csoport][planning-guide-figure-200]

Fontos vegye figyelembe, hogy a készlet minden egyes virtuális gép kell üzembe helyezni, párhuzamos, ahol a virtuális gép nevét, a készlet minden egyes azonosak.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Létesítmények közötti – üzembe egyetlen vagy több SAP virtuális gép követelmény alatt teljes körűen integrálva a helyszíni hálózat az Azure-bA
![Hely – hely kapcsolattal (létesítmények közötti) VPN][planning-guide-figure-300]

Ebben a forgatókönyvben számos lehetséges üzembe helyezési minták a létesítmények közötti forgatókönyv. Azt is le kell írni egyes részei az SAP futtatása helyszíni fekvő és egyéb részei az SAP az Azure-ban fekvő. Lehet, hogy a tény, hogy az SAP-összetevők egy részét futtatja az Azure-ban minden aspektusát átlátható a végfelhasználók számára. Ezért a SAP átviteli javítás System (STM), RFC kommunikációs, nyomtatás, (például egyszeri bejelentkezés) biztonsági és az egyéb zökkenőmentesen működik az Azure-on futó SAP-rendszereinket. De a létesítmények közötti forgatókönyv azt is leírja egy forgatókönyvet, ahol a teljes SAP-rendszeren fut, az Azure-ban az ügyfél tartományi és DNS extended az Azure-bA.

> [!NOTE]
> Ez az a környezetben, amely hatékony SAP-rendszerek támogatott.
>
>

Olvasási [Ez a cikk] [ vpn-gateway-create-site-to-site-rm-powershell] bővebben a helyszíni hálózat csatlakoztatása a Microsoft Azure

> [!IMPORTANT]
> Azure-ban és a helyszíni ügyfél üzemelő példányok közötti létesítmények közötti forgatókönyvekkel kapcsolatos beszélünk, amikor azt láthatjuk egész SAP-rendszereinket részletességét. Forgatókönyvek, melyek *nem támogatott* létesítmények közötti forgatókönyvek is:
>
> * Különböző központi telepítési módszer különböző rétegeket, SAP-alkalmazások futnak. A DBMS réteg a helyszínen, de az SAP alkalmazásrétegre például az Azure virtuális gépként, vagy fordítva üzembe helyezett virtuális gépek futnak.
> * Az Azure-ban és a egy helyszíni SAP réteg néhány összetevőt. Például felosztása a SAP alkalmazás réteg között az Azure-beli és helyszíni példányát.
> * Több Azure-régióban keresztül SAP-példányok egy rendszer futó virtuális gépek elosztása nem támogatott.
>
> Ez a korlátozás oka belüli egy SAP-rendszerhez, különösen az alkalmazáspéldányok és a DBMS réteg az SAP-rendszer között közel valós idejű, nagy teljesítményű hálózat vonatkozó követelmény.
>
>

### <a name="supported-os-and-database-releases"></a>Támogatott operációs rendszer és adatbázis-kiadások
* A Microsoft szerverszoftver, Azure Virtual Machine Services ebben a cikkben szerepel a támogatott: <http://support.microsoft.com/kb/2721672>.
* Támogatott operációs rendszer kiadásai, adatbázis rendszer kiadásokban támogatott az Azure Virtual Machine Services SAP software együtt vannak dokumentálva az SAP-Jegyzetnek [1928533].
* SAP-alkalmazások és az Azure Virtual Machine Services támogatott kiadások vannak dokumentálva az SAP-Jegyzetnek [1928533].
* Csak 64 bites lemezképek fiókként való futtatásra Vendég virtuális gépeket az Azure-beli SAP-forgatókönyvek esetén támogatottak. Emiatt csak a 64 bites SAP-alkalmazások és adatbázisok támogatottak.

## <a name="microsoft-azure-virtual-machine-services"></a>Virtuális gép Microsoft Azure-szolgáltatások
A Microsoft Azure platform egy internetes léptékű felhőalapú szolgáltatásokat biztosító platform üzemeltetett és a Microsoft-adatközpontokban üzemeltetett. A platform a Microsoft Azure Virtual Machine Services (infrastruktúra-szolgáltatás, vagy IaaS) és gazdag funkcionalitású platformon, a Platformszolgáltatás (PaaS) képességek egy készletét tartalmazza.

Az Azure platform csökkenti a kezdeti technológia szükségességét, és megvásárolja az infrastruktúra. Egyszerűbbé téve a karbantartása és működő alkalmazások igény szerinti számítási és tárolási üzemeltetésére, méretezhető és webes alkalmazások és csatlakoztatott alkalmazások felügyelete megadásával. Infrastruktúra-felügyelettel az automatizált platformokhoz készült, magas rendelkezésre állás és a dinamikus méretezés a használati igényekhez beállítással, használatalapú díjszabási modellt.

![Microsoft Azure-szolgáltatások a virtuális gép elhelyezése][planning-guide-figure-400]

Az Azure Virtual Machine Services Microsoft van, hogy lehetővé teszi egyéni server-rendszerképeket üzembe az Azure IaaS-példány (lásd a 4. ábra). A virtuális gépek az Azure-ban Hyper-V virtuális merevlemezek (VHD) alapulnak, és képesek különböző operációs rendszert futtató, a vendég operációs rendszer.

Működési szempontból az Azure Virtual machines szolgáltatás hasonló funkciókat kínál a helyszínen üzembe helyezett virtuális gépek. Azonban jelentős előny, amely nem kell be kell szereznie, felügyelheti és kezelheti az infrastruktúra rendelkezik. Fejlesztők és rendszergazdák jogköre teljes. az operációs rendszer lemezképének a virtual machines szolgáltatáson belül. A rendszergazdák jelentkezhetnek be távolról ezeket a virtuális gépeket, karbantartási és hibaelhárítási feladatokat, valamint a szoftverek központi telepítési feladatok végrehajtásához. Üzembe helyezés tekintetében a csak korlátozások vonatkoznak, a méretek és Azure virtuális gépek képességeit. Ezek a méretek nem lehet például finom részletes konfigurációban, megteheti a helyszínen. Nincs kombinációját képviselő Virtuálisgép-típusok közül választhat:

* Vcpu-k száma
* Memory (Memória)
* Csatolt VHD-k száma
* Hálózati és tárolási sávszélesség

A méret és a különböző virtuális gépek különböző fürtméretekkel járó korlátozásokat érhető el, láthatja a tábla [ebben a cikkben (Linux)] [ virtual-machines-sizes-linux] és [ebben a cikkben (Windows)] [virtual-machines-sizes-windows].

Nem minden más Virtuálisgép-sorozatok a előfordulhat, hogy az egyes egy Azure-régiót (az Azure-régiók lásd a következő fejezet) érhető el. Ügyeljen arra is, hogy nem minden virtuális gépeket vagy Virtuálisgép-sorozat SAP-minősítés.

> [!IMPORTANT]
> SAP NetWeaver-alapú alkalmazások használatát, csak a Virtuálisgép-típusok és konfigurációk része az SAP-Jegyzetnek felsorolt [1928533] támogatottak.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Az Azure-régiók
Virtuális gépek vannak telepítve az úgynevezett *Azure-régiók*. Egy Azure-régió lehet egy vagy több közelében található adatközpontokban. A geopolitikai régiók, a világ legtöbb a Microsoft rendelkezik, legalább két Azure-régiót. Például Európában van, egy Azure-régió *Észak-Európa* és az egyik *Nyugat-Európa*. Az ilyen két Azure-régiót a geopolitikai régión belül elég jelentős távolság választják el, hogy természetes vagy technikai katasztrófák nincsenek hatással az ugyanazon geopolitikai régióban mindkét Azure régióban. A Microsoft folyamatosan globálisan készíti el az új Azure-régiót a geopolitikai régiókon, mivel a 2015. december 20 Azure-régiók a már bejelentett további régiók száma elérte, és ezek a régiók száma folyamatosan nő. SAP-rendszerek üzembe, a Microsoft a összes ezekben a régiókban, beleértve a két Azure-régiót Kínában. Az Azure-régiók aktuális naprakész információkat ezen a webhelyen talál: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>A Microsoft Azure virtuális gép fogalma
A Microsoft Azure biztosít az infrastruktúra szolgáltatást (IaaS) hasonló funkciókkal rendelkező virtuális gépek gazdagépre egy helyszíni virtualizálási megoldás. Ön létre tudja hozni a virtuális gépeket az Azure Portal, PowerShell vagy parancssori felület, amely üzembe helyezési és felügyeleti képességeket is kínál.

Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra telepíteni az alkalmazás életciklusának minden fázisában az alkalmazás használhatja.

Resource Manager-sablonok használatával kapcsolatos további információk itt található:

* [Telepítését és kezelését a virtuális gépek Azure Resource Manager-sablonok és az Azure parancssori felület használatával][../../linux/create-ssh-secured-vm-from-template.md]
* [Virtuális gépek kezelése az Azure Resource Managerrel és Powershellel][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Egy másik érdekes szolgáltatása lehetővé teszi képek létrehozása a virtuális gépekről, ami lehetővé teszi, hogy készítse elő az egyes adattárak, amelyről is tudja gyors üzembe helyezése virtuálisgép-példányok, amelyek megfelelnek az elvárásainak.

További információ a virtuális gépekről származó lemezképek létrehozása található [ebben a cikkben (Linux)] [ virtual-machines-linux-capture-image-resource-manager] és [ebben a cikkben (Windows)] [ virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Tartalék tartományok
Tartalék tartományok felel meg a hiba, a fizikai infrastruktúrán lévő adatközpontok szorosan kapcsolódó fizikai egysége, és a egy fizikai panel vagy az állványoknál is figyelembe kell venni egy tartalék tartományt, amíg nincs közvetlen egy az egyhez típusú hozzárendelés a kettő között nem létezik.

Több virtuális gép egy SAP-rendszerrel a Microsoft Azure virtuális gép szolgáltatás részeként történő telepítésekor az Azure Fabric Controller különböző tartalék tartományok, így megfelelnek a követelményeinek való az alkalmazás központi telepítése befolyásolhatja a A Microsoft Azure SLA-t. Azonban a tartalék tartományok az Azure skálázási egység (számítási csomópontok vagy a tárolási csomópontok és a hálózat több száz gyűjteménye) a terjesztési vagy a virtuális gépek az adott tartalék tartomány-hozzárendelés valami, amelyeken nem kell irányítani. Az Azure fabric controller központi telepítése a virtuális gépek különböző tartalék tartomány keresztül irányítani, kell hozzárendelése egy Azure rendelkezésre állási csoportot a virtuális gépek üzembe helyezéskor. Az Azure rendelkezésre állási csoportokról további információért lásd: fejezet [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] ebben a dokumentumban.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Frissítési tartományok
Frissítési tartományok egy logikai egységet, amely segít meghatározni, hogy hogyan frissül, az SAP rendszere áll, több virtuális gépeken futó SAP-példányok, belüli virtuális gép képviseli. Frissítés esetén a Microsoft Azure végighalad a frissítési tartományok egyenként frissítésének folyamatát. Osztja szét a virtuális gépek üzembe helyezéskor keresztül különböző frissítési tartományokban, részben a lehetséges állásidő védheti meg az SAP-rendszerhez. A különböző frissítési tartományokban eloszlatva SAP a rendszer a virtuális gépek üzembe helyezése az Azure kényszeríti, egy adott attribútum beállítása az egyes virtuális gépek az üzembe helyezéskor kell. Tartalék tartományok hasonlóan, az Azure skálázási egység van osztva több frissítési tartományt. Az Azure fabric controller központi telepítése a virtuális gépek különböző frissítési tartományokban keresztül irányítani, kell hozzárendelése egy Azure rendelkezésre állási csoportot a virtuális gépek üzembe helyezéskor. Az Azure rendelkezésre állási csoportokról további információért lásd: fejezet [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] alatt.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Az Azure rendelkezésre állási csoportok
Azure virtuális gépeit egy Azure rendelkezésre állási csoport különböző hibatűrési és frissítési tartományok szerint az Azure Fabric Controller vannak elosztva. A különböző hibatűrési és frissítési tartományok eloszlása a célja, hogy megakadályozza, hogy az összes virtuális gépet, az SAP-rendszer éppen leállítás alatt az infrastruktúra-karbantartási vagy egy tartalék tartományon belüli hiba esetén. Alapértelmezés szerint virtuális gépek nem részei rendelkezésre állási csoportban. Egy virtuális Gépet egy rendelkezésre állási csoportban való részvétel üzembe helyezés során vagy később újrakonfigurálása és újbóli üzembe helyezés egy virtuális gép van meghatározva.

Megismerheti az Azure rendelkezésre állási készletek és a rendelkezésre állási csoportok tartalék és frissítési tartományok kapcsolódnak módon fogalmát, olvassa el [Ez a cikk][virtual-machines-manage-availability]

Adja meg a rendelkezésre állási csoportok az Azure Resource Manager json-sablon használatával, lásd: [a rest-api specifikáció](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) , és keresse meg az "availability".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Storage: A Microsoft Azure Storage és az adatlemezek
A Microsoft Azure virtuális gépek különböző tárolótípusokra használatára. Az SAP az Azure Virtual Machine Services megvalósításakor fontos tárolási két fő típusai közötti különbségeket:

* Nem állandó, ideiglenes tárolást.
* Tartós tároláshoz.

Azure virtuális gépek nem állandó lemezt is kínálnak, egy virtuális gép üzembe helyezése után. Esetén a virtuális gép újraindítását az adott meghajtókat minden tartalom törlődik. Ennélfogva a tekintve, hogy az adatfájlok és az adatbázisok napló vagy visszaállíthatja a fájlokat semmilyen körülmények között találhatók, ezek nem megőrzött meghajtókon. Van néhány adatbázist, a kivételek, ezek a meghajtók nem megőrzött tempdb és ideiglenes táblahelyeket alkalmas lehet. Azonban ne az adott meghajtókat használja A-sorozatú virtuális gépek, mivel ezek nem megőrzött meghajtók korlátozott, hogy a Virtuálisgép-család átviteli. További részletekért olvassa el a cikket [a Windows Azure virtuális gépeken futó ideiglenes meghajtó ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
> 
> Egy Azure virtuális Gépen a D:\ meghajtón egy nem megőrzött meghajtót, az Azure-beli számítási csomópont által az egyes helyi lemezek biztonsági mentése. Mivel a szolgáltatás nem megőrzött, akkor ez azt jelenti, hogy a tartalom a D:\ meghajtóra végzett módosítások elvész, amikor a virtuális gép újraindul. "Végrehajtott módosítások", például a tárolt fájlok, hozta létre, könyvtárak, telepített alkalmazások stb.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux rendszerű Azure virtuális gépek automatikusan, amely az Azure számítási csomópont helyi lemezek által támogatott nem megőrzött meghajtó /mnt/resource meghajtót csatlakoztatni. Mivel a szolgáltatás nem megőrzött, akkor ez azt jelenti, hogy /mnt/resource tartalom végzett módosítások elvesznek, ha a virtuális gép újraindul. Végrehajtott módosítások, például a tárolt fájlok, hozta létre, könyvtárak, telepített alkalmazások stb.
> 
> 

- - -

A Microsoft Azure Storage megőrzött tárolás és a tipikus szintű védelmének és redundanciájának SAN-tároló látható itt. Az Azure Storage-alapú lemezek a virtuális merevlemez (VHD-k) az Azure Storage szolgáltatásainak található. A helyi operációsrendszer-lemez (Windows C:\, Linux/dev/sda1) az Azure Storage tárolja, és további köteteket vagy lemezeket a virtuális géphez csatlakoztatott első tárolja, túl.

Töltse fel egy meglévő VHD-t a helyszíni vagy hozzon létre üres azokat az Azure-ban, és ezeket az üzembe helyezett virtuális gépek csatlakoztatása lehetőség.

Miután létrehozott vagy virtuális merevlemez feltöltése az Azure Storage-ba, akkor lehet csatlakoztatásához, és csatolja azokat egy meglévő virtuális gépet, és másolja a meglévő (fürtköteteiről) virtuális Merevlemezt.

Ezeket a virtuális merevlemezek megmaradnak, mivel adatok és a módosítások azokkal biztonságos, amikor újraindul, és újra létre kellene hoznia a virtuálisgép-példányt is. Akkor is, ha egy példány törlik, ezek a virtuális merevlemezek biztonságban és újratelepítése is, vagy operációsrendszer-lemezek esetén is csatlakoztathatók más virtuális gépekhez.

Az Azure Storage-ról további információt itt található:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Az Azure standard szintű Storage
Az Azure standard szintű tárolást jelent meg az Azure IaaS elérhető tárolási típus történt. Kényszerített egyetlen lemezenkénti IOPS kvóták volt. Késések tapasztalhatók nem volt ugyanahhoz az osztályhoz, TÁROLÓHÁLÓZATI és NAS-eszközökön általában a csúcskategóriás SAP-rendszerek üzembe helyezett helyszíni központigyorsítótár. Mindazonáltal, az Azure standard szintű tárolást bizonyult több száz elegendő csapattagok pedig nyugodtabban aludhatnak üzembe helyezett Azure-beli SAP-rendszereinket.

A standard szintű Azure Storage-fiókok tárolt lemezek a tényleges tárolt adatokat, a kötet tárolási tranzakciók, a kimenő forgalmat és a választott adatredundáns tárolási mód alapján lesznek kiszámlázva. A legfeljebb 1TB méretű, sok lemezzel hozható létre, de üres, azok továbbra is nem számítunk fel díjat. Ha, majd töltse ki az egyik virtuális merevlemez 100 GB, 100 GB-os tárolására, nem pedig a VHD-t a létrehozott névleges mérete díjkötelesek.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Az Azure Premium Storage
Az Azure Premium Storage a célja, hogy biztosítson bevezetett van:

* Jobb i/o várakozási ideje.
* Jobb átviteli sebességet.
* Biztosítja az i/o várakozási ideje kisebb változékonyságát.

Erre a célra a sok módosítást, a legjelentősebb két vannak jelentek meg:

* SSD-lemezeket az Azure Storage-csomópontok használata
* Egy új olvasási gyorsítótár, amely használatával egy Azure-beli számítási csomópont helyi SSD

Leváló Standard tárolási képességek nem változott, a lemez (vagy VHD), méretétől függ a Premium Storage jelenleg rendelkezik másik lemezt három kategóriába, amely ebben a cikkben látható: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Láthatja, hogy a lemez és az IOPS/lemez átviteli sebesség/lemez függenek a lemezek mérete kategóriáját

Költség alapja a Premium Storage esetén nem áll az ilyen lemezeket, de ilyen egy lemezt, független a a lemezen tárolt adatok mennyisége, mérete kategóriáját tárolva tényleges adatmennyiséget.

Lemezek a prémium szintű Storage, amely nem közvetlen leképezés a mérete kategóriákba látható hozhat létre. Ez a helyzet, lehet, különösen akkor, ha a lemezek másolásának Standard Storage prémium szintű Storage-bA. Ebben az esetben a következő legnagyobb prémium szintű tárlemezméretre kerekítünk hozzárendelésével történik.

Az SAP-minősítéssel rendelkező Azure Virtuálisgép-családok többsége képesek együttműködni a és a Premium Storage, vagy az Azure standard és prémium szintű Storage közötti vegyesen.

Ha ki a részét a DS sorozatú virtuális gépek ellenőrzése [ebben a cikkben (Linux)] [ virtual-machines-sizes-linux] és [ebben a cikkben (Windows)][virtual-machines-sizes-windows], vegye figyelembe, hogy nincsenek adatok mennyiségi korlátozásai a prémium szintű tárolólemezeket a granularitási a virtuális gép szint. Különböző DS vagy GS sorozatú virtuális gépek is korlátozásokat a biztonságra vonatkozó is csatlakoztatott adatlemezek száma. Ezeket a korlátokat, valamint a fent említett cikkben vannak dokumentálva. De lényegében azt jelenti, hogy ha, például csatlakoztassa 32 x P30 lemezek DS14 egyetlen virtuális gép nem kaphat 32 x maximális P30 lemez átviteli sebességet. Ehelyett a maximális átviteli sebesség, a Virtuálisgép-szintű a cikkben leírtak szerint korlátozza az adatátvitelt.

További információ a Premium Storage itt található: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>


#### <a name="azure-storage-accounts"></a>Azure Storage-tárfiókok
Szolgáltatások vagy az Azure-beli virtuális gépek telepítésekor VHD-k és a Virtuálisgép-rendszerképek üzembe helyezése, Azure Storage-fiókok nevű egységek rendszerezheti. Az Azure-telepítés tervezésekor alaposan gondolja át, a korlátozások az Azure kell. Az egyik oldalán nincs a Storage-fiókok csak korlátozott számú Azure-előfizetésenként. Bár minden Azure Storage-fiók képes tárolni a VHD-fájlok nagy számú, rögzített korlátozva van a teljes iops-értékkel / Storage-fiókot. Száz SAP virtuális gép létrehozása jelentős i/o-hívásokat az adatbázis-kezelő rendszerekkel való telepítésekor ajánlott magas iops-t az adatbázis-kezelő virtuális több Azure Storage-fiókok közötti elosztására. Ügyelni kell, nem haladhatja meg az Azure Storage-fiókok aktuális korlát előfizetésenként. Mivel a tárolás fontos részei az SAP-rendszer adatbázis üzembe, a fogalom részletesebben hivatkozott már tárgyalt [DBMS üzembe helyezési útmutató][dbms-guide].

További információ az Azure Storage-fiókok található [Ez a cikk][storage-scalability-targets]. A cikk elolvasása, vegye figyelembe, hogy nincsenek-e a korlátozások az Azure standard szintű Storage-fiókok és a Premium Storage-fiókok közötti különbségek. Jelentősebb eltérések ezen a Tárfiókon belül tárolt adatok mennyisége. Standard szintű tárolóban lemez észrevehetően nagyobb, mint a prémium szintű tárolást használjon. A másik oldalon, a standard szintű Tárfiók jelentősen korlátozott IOPS (oszlop **teljes kérelmek gyakorisága**), míg az Azure Premium Storage-fiók rendelkezik ilyen korlátozás nélkül. Részletek és eredményeinek megtekintéséhez a különbségeket az SAP-rendszereit, különösen az adatbázis-kezelő kiszolgálók központi telepítései ismertetésekor ismertetik.

A Tárfiókon belül lehetősége van különböző tárolók rendszerezése és a kategorizálásához különböző VHD-k létrehozásához. Ezek a tárolók szolgálnak, például különálló virtuális merevlemezeket különböző virtuális gépek. Nincsenek nem teljesítményre gyakorolt hatása, csak egy tároló vagy több tároló alatt egy Azure Storage-fiók használatával.

Azure-ban a virtuális merevlemez neve követi az alábbi elnevezési kapcsolatot igénylő Azure-ban a virtuális merevlemez egyedi nevét adja meg:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

A fenti karakterláncot kell az Azure Storage tárolt virtuális merevlemez egyedi azonosításához.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>A felügyelt lemezek
A felügyelt lemezek az Azure Resource Managerben, amely használható az Azure Storage-fiókokban tárolt VHD-k helyett új erőforrástípust. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva, és ezért a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állásának növelése. További információkért olvassa el a [áttekintő cikkben](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Azt javasoljuk, hogy felügyelt lemez, használja, mivel azok egyszerűsítheti az üzembe helyezés és a virtuális gépek felügyeletét.
SAP jelenleg csak támogatja prémium szintű Managed Disks. További információkért olvassa el az SAP-Jegyzetnek [1928533].


#### <a name="microsoft-azure-storage-resiliency"></a>A Microsoft Azure Storage rugalmasságot
A Microsoft Azure Storage tárolja az alap virtuális merevlemez (az operációs rendszer) és a csatlakoztatott lemezek vagy a Blobok legalább három külön tárolócsomópontok. Ez a tény helyi redundáns tárolást (LRS) nevezzük. LRS a alapértelmezett minden típusú tároló az Azure-ban. 

Többféleképpen is további redundancia, olyan minden cikk [Azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Kezdődően az Azure Premium Storage, amely az adatbázis-kezelő virtuális gépek és lemezek, adatbázis és a napló vagy visszaállíthatja a fájlokat tároló tárolási ajánlott típus, az egyetlen elérhető módszer esetén az LRS. Ennek eredményeképpen kell konfigurálása az adatbázis módszerek, például az SQL Server Always On, Oracle Data Guard vagy HANA Rendszerreplikáció adatbázis replikálása egy másik Azure-régióban, vagy egy másik Azure rendelkezésre állási zónában engedélyezéséhez.


> [!NOTE]
> A DBMS üzembe helyezési elérhető az Azure standard szintű Storage, Georedundáns tárolás használata nem ajánlott, mert teljesítményt, és nem fogadja el az írási sorrend között különböző virtuális merevlemezek, virtuális géphez csatolt. A tény nem teljesítésére az írási sorrend között különböző virtuális merevlemezek, a magas lehetséges, ha az adatbázis és a napló vagy visszaállíthatja a fájlokat a forrás virtuális gép oldalán (adott esetben többnyire) több virtuális merevlemezek vannak elosztva a replikációs cél oldalon inkonzisztens adatbázisok végül viseli.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>A Microsoft Azure-hálózatkezelés
A Microsoft Azure egy hálózati infrastruktúra, amely lehetővé teszi, hogy minden esetben, szeretnénk valósíthat meg az SAP-szoftverek leképezése biztosít. Az funkciók a következők:

* A hozzáférést kívülről, közvetlenül a Windows Terminálszolgáltatások vagy ssh/VNC virtuális gépekhez
* Szolgáltatások és alkalmazások belül a virtuális gépek által használt adott portokhoz való hozzáférést
* Belső kommunikációs és az Azure virtuális gépként üzembe helyezett virtuális gépek közötti névfeloldás
* Létesítmények közötti kapcsolat egy ügyfél a helyszíni hálózat és az Azure-hálózat között
* Több Azure-régió vagy az adatközpontok az Azure webhelyek közötti kapcsolat

További információt itt találhat: <https://azure.microsoft.com/documentation/services/virtual-network/>

Nincsenek neve és IP-feloldás konfigurálása az Azure-ban számos különböző lehetőségeit. Ebben a dokumentumban csak felhőalapú forgatókönyveket az Azure DNS-sel (szakembereket meghatározása egy saját DNS-szolgáltatás) alapértelmezett támaszkodnak. Emellett van egy új Azure DNS-szolgáltatás, amely helyett a saját DNS-kiszolgáló beállításához használható. További információ található [Ez a cikk] [ virtual-networks-manage-dns-in-vnet] és a [ezt oldal](https://azure.microsoft.com/services/dns/).

Létesítmények közötti forgatókönyvek esetén, hogy hagyatkoznia a tény, hogy a helyszíni AD/OpenLDAP/DNS ki van terjesztve VPN vagy privát kapcsolaton keresztül az Azure-bA. Az egyes forgatókönyvek itt dokumentált, lehet szükséges, hogy telepítve van az Azure AD/OpenLDAP replikájának.

Mivel a hálózat és a névfeloldás alapvető fontosságú SAP-rendszerhez az adatbázis központi telepítésének, részletesen tárgyalja a fogalom a [DBMS üzembe helyezési útmutató][dbms-guide].

##### <a name="azure-virtual-networks"></a>Azure virtuális hálózatok
Egy Azure virtuális hálózat létrehozása létrehozásával meghatározhatja a címtartományt a magánhálózati IP-címek foglalva az Azure DHCP-funkcióit. A létesítmények közötti forgatókönyvek esetén a megadott IP-címtartomány még hozzá van rendelve DHCP használatával az Azure-ban. Azonban a tartománynév feloldása történik a helyszíni (feltéve, hogy, hogy a virtuális gépek egy része egy a helyszíni tartomány), és így fel tudja oldani címek mellett a különböző Azure Cloud Services.

Az Azure-ban minden virtuális gép csatlakoztatva kell lennie egy virtuális hálózathoz.

További részletek találhatók [Ez a cikk] [ resource-groups-networking] és a [ezt oldal](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Alapértelmezés szerint a virtuális gép üzembe helyezése után nem módosítható a virtuális hálózati konfiguráció. A TCP/IP-beállítások az Azure DHCP-kiszolgálóhoz kell hagyni. Alapértelmezett viselkedés a dinamikus IP-hozzárendelés.
>
>

A virtuális hálózati adapter MAC-cím változhat, például átméretezése és a Windows vagy Linux-alapú vendég operációs rendszer szerzi be az új hálózati kártya, és automatikusan a DHCP segítségével ebben az esetben a IP-cím és DNS-címek kiosztása után.

##### <a name="static-ip-assignment"></a>Statikus IP-cím-hozzárendelés
Rögzített vagy fenntartott IP-címek hozzárendelése egy Azure virtuális hálózaton belüli virtuális gépek lehetőség. A virtuális gép futtatása az Azure Virtual Networkhöz megnyílik egy nagyszerű lehetőséget kihasználhatja ezt a funkciót, ha szükséges, vagy bizonyos esetekben szükséges. Az IP-hozzárendelés létezik-e a virtuális gép, független, hogy a virtuális gép futó vagy -leállítás során érvényes marad. Ennek eredményeképpen kell tennie a virtuális gépek (fut, és a leállított virtuális gépek) teljes száma figyelembe az IP-címtartományt a virtuális hálózat meghatározásakor. Az IP-cím hozzárendelve maradjon, amíg nem törli a virtuális gép és a hálózati adaptert, vagy addig, amíg az IP-cím megszüntetése ismét lekérdezi hozzárendelt. További információkért olvassa el [Ez a cikk][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Segítségével Azure azt jelenti, hogy statikus IP-címeket kell rendel az egyes Vnic. A vendég operációs rendszeren belül a statikus IP-címek nem egy virtuális hálózati adaptert kell hozzárendelése. Egyes Azure-szolgáltatásokhoz hasonlóan az Azure Backup szolgáltatás támaszkodhat, hogy legalább az elsődleges virtuális hálózati adapter van beállítva, a DHCP és a statikus IP-címeket. Lásd még a dokumentum [elhárítása Azure virtuális gépek biztonsági mentésének](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Ha több statikus IP-cím hozzárendelése virtuális Géphez van szüksége, több virtuális hálózati adapter hozzárendelése virtuális Géphez szeretné.
>
>

##### <a name="multiple-nics-per-vm"></a>Több hálózati adapter virtuális gépenként
Meghatározhat több virtuális hálózati adapterrel (vNIC) egy Azure virtuális gépekhez. Lehetővé teszi több virtuális hálózati adapter beállítása a hálózati forgalom megkezdése rendelkezik, például ügyfél forgalmát áthalad egy virtuális hálózati adapter és a háttérkiszolgáló forgalom elkülönítése áthalad egy második virtuális hálózati adaptert. Függő a típusú virtuális gépek vannak a Vnic száma biztonságra vonatkozó korlátozásokat. Ezekben a cikkekben található pontos részleteit, funkciókat és korlátozásokat:

* [Windows virtuális gép több hálózati adapter létrehozása][virtual-networks-multiple-nics-windows]
* [Több hálózati adapterrel rendelkező Linux virtuális gép létrehozása][virtual-networks-multiple-nics-linux]
* [Egy sablon használatával több hálózati Adaptert virtuális gépek üzembe helyezése][virtual-network-deploy-multinic-arm-template]
* [Több hálózati Adaptert virtuális gépek PowerShell-lel üzembe helyezése][virtual-network-deploy-multinic-arm-ps]
* [Az Azure CLI használatával több hálózati Adaptert virtuális gépek üzembe helyezése][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Helyek közötti kapcsolat
Létesítmények közötti Azure-beli és helyszíni átlátható és állandó VPN-kapcsolattal társított. Válhat a leggyakrabban használt SAP telepítési minta az Azure-ban várható. Feltételezzük, hogy operatív eljárások és folyamatok SAP-példányok az Azure-ban transzparens módon működik. Ez azt jelenti, hogy ezek a rendszerek kívül a nyomtatáshoz kell, valamint egy tesztgépen, amely az Azure-ban fejlesztési rendszerből változik az SAP átviteli felügyeleti rendszert (TMS) átviteléhez használható üzembe helyezte a helyszínen. További dokumentáció körül site-to-site található [Ez a cikk][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-alagút eszköz
Hozzon létre egy helyek közötti kapcsolat (a helyi adatközpontok Azure-adatközpontban), kell beszerzése és a egy VPN-eszköz konfigurálásához, vagy a szoftverfrissítési összetevő a Windows Server 2012 Útválasztás és távelérés szolgáltatás (RRAS) bevezetett használja.

* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal PowerShell-lel][vpn-gateway-create-site-to-site-rm-powershell]
* [Tudnivalók a Site-to-Site VPN Gateway-kapcsolatok VPN-eszközeiről][vpn-gateway-about-vpn-devices]
* [VPN Gateway – gyakori kérdések][vpn-gateway-vpn-faq]

![A helyszíni és az Azure között helyek közötti kapcsolat][planning-guide-figure-600]

A fenti ábrán látható két Azure-előfizetés használatra fenntartva az IP-cím résztartományok rendelkezik az Azure-beli virtuális hálózatokkal. A kapcsolat a helyszíni hálózat és az Azure VPN-en keresztül jön létre.

#### <a name="point-to-site-vpn"></a>Pont – hely VPN
Pont – hely VPN igényel minden ügyfélszámítógépen, a saját VPN kapcsolatot az Azure-bA. Az SAP-forgatókönyveket hogy megnézzük, pont – hely kapcsolat nem célszerű. Ezért semmilyen további hivatkozások megadott pont – hely VPN-kapcsolat.

További információt itt találhat
* [Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Többhelyes VPN
Az Azure ma is kínál a többhelyes VPN-kapcsolatot egy Azure-előfizetés létrehozása lehetőséget. Egyetlen előfizetéshez korábban csak egy helyek közötti VPN-kapcsolat. Ez a korlátozás tűnnek el a többhelyes VPN-kapcsolatok egyetlen előfizetéssel. Ez lehetővé teszi egy adott előfizetés keretében létesítmények közötti konfigurációkat a több Azure-régió használhatja.

További dokumentációjáért lásd: [Ez a cikk][vpn-gateway-create-site-to-site-rm-powershell]



#### <a name="vnet-to-vnet-connection"></a>Virtuális hálózatok közötti kapcsolat
Többhelyes VPN használatával kell külön Azure virtuális hálózat konfigurálása az egyes régiókban. Gyakran azonban rendelkezik, amely a különböző régiókban lévő szoftverösszetevőket kell egymással kommunikálni a követelménynek. Ideális esetben ez a kommunikáció nem lesznek irányítva, egy Azure-régióban a helyszíni és egyéb Azure-régió onnan. Helyi az Azure kínál arra, hogy egy Azure virtuális hálózati kapcsolat konfigurálása egy adott régióban egy másik Azure virtuális hálózat egy másik régióban üzemeltet. Ez a funkció neve VNet – VNet kapcsolat. Ez a funkció további információt itt találhat: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Az Azure ExpressRoute magánhálózati kapcsolatot
A Microsoft Azure ExpressRoute lehetővé teszi, hogy az Azure adatközpontok és az ügyfél helyszíni infrastruktúra között vagy a bérelt kiszolgálói környezetben magánkapcsolatok létrehozását. ExpressRoute kínál különféle MPLS VPN-szolgáltatók (csomagkapcsolt) vagy más hálózati szolgáltatók. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Az ExpressRoute-kapcsolatok az interneten keresztül nagyobb biztonságot, keresztül több párhuzamos kapcsolatok megbízhatóbbak, gyorsabbak, és kevesebb késéssel jár, mint a szokásos internetkapcsolatoknál megbízhatóbb kínálnak.

További részleteket az Azure ExpressRoute- és ajánlatokat itt talál:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route használatával egy ExpressRoute-kapcsolatcsoport több Azure-előfizetés lehetővé teszi a itt leírtak szerint

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Kényszerített bújtatás esetén a létesítmények közötti
A virtuális gépek site-to-site, pont – hely vagy az ExpressRoute használatával a helyszíni tartományokhoz való csatlakozás esetén győződjön meg arról, hogy az internetes proxybeállítások első telepítésére ezeken a virtuális gépeken, valamint az összes felhasználót kell. Alapértelmezés szerint az ezeket a virtuális gépek vagy az internet eléréséhez egy böngészőben használó felhasználók szoftvert nem sikerült a vállalat proxyn keresztül, de közvetlenül az Azure-on az interneten keresztül csatlakoznak. De még a proxybeállítást nem 100 %-os megoldást a vállalati proxyn keresztül a forgalom közvetlen óta feladata, a szoftverek és szolgáltatások a proxy kereséséhez. Ha a virtuális Gépen futó szoftver, amely nem végez, illetve a rendszergazda kezeli a beállításokat, az internetre irányuló forgalom is kell újra detoured közvetlenül az Azure-bA az interneten keresztül.

Annak érdekében, hogy az ilyen egy közvetlen internetkapcsolattal, konfigurálhatja a kényszerített bújtatásról helyek közötti kapcsolattal a helyszíni és az Azure között. A kényszerített bújtatás funkció részletes leírása itt van közzétéve. <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Az expressroute-tal a kényszerített bújtatás engedélyezve van a vevők az ExpressRoute BGP-társviszony-létesítési munkamenetek keresztül egy alapértelmezett útvonalat hirdet által.

#### <a name="summary-of-azure-networking"></a>Az Azure-hálózatok összegzése
Ez a fejezet kapcsolatos Azure-hálózatot is számos fontos pontjait tartalmazza. A következő főbb pontjai összefoglalása:

* Azure virtuális hálózatok lehetővé teszik a hálózati struktúra üzembe az Azure-alapú. Virtuális hálózatok egymással szembeni elkülönített vagy virtuális hálózatok közötti forgalom hálózati biztonsági csoportok segítségével lehet irányítani.
* Azure virtuális hálózatok is javítható, ha az IP-címtartományok hozzárendelése a virtuális gépek vagy fix IP-címek hozzárendelése a virtuális gépek
* Egy helyek közötti és pont – hely kapcsolat beállításához először hozzon létre egy Azure virtuális hálózaton kell
* Virtuális gép üzembe helyezése után már nem módosítható a virtuális hálózat a virtuális géphez hozzárendelt

### <a name="quotas-in-azure-virtual-machine-services"></a>Az Azure-beli virtuálisgép-szolgáltatásokban kvóták
Az a tény, hogy a tárolási és hálózati infrastruktúra különböző szolgáltatások, az Azure-infrastruktúra-ban futó virtuális gépek között megosztott tisztában lenni kell. És az ügyfél saját adatközpontok hasonlóan túlzott kiosztása az infrastruktúra-erőforrások részének mértékben. A Microsoft Azure Platform az erőforrás-használat korlátozása és a konzisztens és determinisztikus teljesítmény megőrzése lemezen, Processzor, hálózati és egyéb kvóták használ.  A virtuális gépek különböző típusairól (A5, A6, stb.) más kvóták, a lemezek, CPU, RAM, számát, és a hálózati.

> [!NOTE]
> Az SAP által támogatott virtuális gép típusok a CPU és memória-erőforrások előre lefoglalt a csomópontokra. Ez azt jelenti, hogy a virtuális gép üzembe helyezése után a gazdagépen az erőforrások elérhetők a virtuális gép típusa szerint.
>
>

Amikor SAP méretezése az Azure-megoldások tervezése, az egyes virtuálisgép-méret kvótái figyelembe kell venni. A virtuális gép kvóták leírása [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A leírt kvóták elméleti maximális értékeket jelölik.  A korlát iops lemezenként kis IOS (8 KB-os) érhető el, de valószínűleg nincs elérhető nagy IOS (1Mb).  Az IOPS-korlátját egyetlen lemez részletességét van kényszerítve.

A nyers döntési fa eldönteni, hogy az SAP-rendszer illeszkedik a Azure Virtual Machine Services és annak képességeit vagy hogy egy meglévő system kell eltérően kell konfigurálni ahhoz, hogy a rendszer az Azure-ban üzembe, mint az alábbi döntési fa használhatók:

![Döntési fa dönthet arról, hogy lehetővé teszi az Azure-ban az SAP üzembe helyezése][planning-guide-figure-700]

**1. lépés**: A legfontosabb információt a kezdéshez egy adott SAP-rendszer a SAP követelménye. Az SAP-követelményeknek kell bontható az adatbázis-kezelő és a SAP alkalmazás részét, még akkor is, ha az SAP-rendszer már üzembe helyezett helyszíni 2 szintű konfigurációban. Meglévő rendszerekkel az SAP, gyakran használt hardver kapcsolatos határozza meg vagy meglévő SAP-referenciaalapok alapján. Az eredmények itt található: <https://sap.com/about/benchmark.html>.
Az újonnan üzembe helyezett SAP-rendszereit érdemes elvégezte a méretezési gyakorlatban kell meghatározni, hogy a rendszer a SAP követelményeinek.
Lásd még: erre a blogra és a csatolt dokumentumot SAP méretezéshez az Azure-ban: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2. lépés**: A meglévő rendszerek esetében az i/o-kötet és i/o-műveletek száma másodpercenként a DBMS-kiszolgálón kell mérni. Újonnan tervezett rendszerek esetén a méretezés a gyakorlatban az új rendszer is kell biztosítania az i/o-követelményeket a DBMS oldalon hozzávetőleges ötleteit. Ha nem tudja biztosan, végül a koncepció igazolása magatartási kell.

**3. lépés**: hasonlítsa össze az Azure virtuális gép különböző típusú tud biztosítani az SAP DBMS-kiszolgáló a SAP követelménye. Az SAP az Azure virtuális gép különböző típusú információkat leírása itt található SAP-Jegyzetnek [1928533]. A fókusz kell lennie, mint az adatbázis-kezelő virtuális gép első óta az Adatbázisréteg, amely a terjessze ki a központi telepítések többsége az SAP NetWeaver rendszerben. Ezzel szemben a SAP alkalmazás réteg kiterjeszthető. Ha egy az SAP használatát sem támogatja az Azure-beli Virtuálisgép-típusok is szükséges a SAP, az a számítási feladat a tervezett SAP-rendszer nem futtatható az Azure-ban. Vagy kell telepítenie a rendszer a helyszíni vagy a rendszer a számítási feladatok kötet módosítani kell.

**4. lépés**: dokumentált [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows], Azure kényszerít egy lemezenkénti IOPS-kvóta Standard szintű Storage vagy a prémium szintű Storage akár független. Függ a virtuális gép, csatlakoztathatók adatlemezek száma eltérő. Ennek eredményeképpen, kiszámíthatja, hogy elérhető-e az egyes virtuális gépek különböző típusairól a maximális iops-érték szám. Az adatbázis-fájl elrendezése függ, is stripe-lemezek egy kötetet a vendég operációs rendszerben az válik. Azonban ha egy üzembe helyezett SAP-rendszerhez a jelenlegi IOPS mennyisége meghaladja az Azure-ban, és hogy van-e nincs lehetősége arra, hogy a meghiúsult lépések kompenzációjához, több memória legnagyobb virtuális gép típusát számított keretein, a számítási feladatok, az SAP-rendszer befolyásolja súlyosan. Ilyen esetben nyomja le a pont, ahol ne telepítsen a rendszer az Azure-ban.

**5. lépés**: kifejezetten az SAP-rendszereit, amelyek üzembe helyezett helyszíni 2 szintű konfigurációban található, az valószínűleg, hogy a rendszer előfordulhat, hogy kell konfigurálni az Azure-on 3 szintű konfigurációban. Ebben a lépésben ellenőrzi, hogy van egy összetevő, amely nem terjeszthető ki, és amely lenne nem illik a CPU és memória-erőforrások a különböző Azure-beli Virtuálisgép-ajánlat az SAP alkalmazásréteg kell. Ha valójában nincs ilyen összetevő, az SAP-rendszerhez, és a munkaterhelés nem telepíthető az Azure-bA. De ha több Azure virtuális gépre történő horizontális felskálázása a SAP alkalmazás-összetevők, a rendszer is üzembe helyezhetők az Azure-bA.

**6. lépés**: az adatbázis-kezelő és az SAP alkalmazás réteg-összetevők az Azure-beli virtuális gépeken is futtatható, ha a konfiguráció kell definiálni, tekintettel a következőkre:

* Az Azure virtuális gépek száma
* Az egyes összetevők a virtuális gépek típusai
* Az adatbázis-kezelő virtuális gép elegendő iops-t biztosít a virtuális merevlemezek száma

## <a name="managing-azure-assets"></a>Azure-objektumok kezelése
### <a name="azure-portal"></a>Azure Portal
Az Azure Portalon az Azure virtuális gép központi telepítések felügyeletéhez szükséges három felületek egyik. Az alapszintű felügyeleti feladatokat, mint a rendszerképek, virtuális gépek üzembe helyezéséhez az Azure Portalon teheti meg. Ezenkívül Tárfiókok, virtuális hálózatok és egyéb Azure-összetevők létrehozása is rendelkezésre állnak az Azure Portalon is kezelhetik a feladatok. Azonban a funkciókat, például virtuális merevlemezek feltöltését a helyszíni az Azure-bA vagy az Azure-on belüli virtuális Merevlemezét másolja olyan feladatokat, melyekhez a harmadik felektől származó eszközök vagy a PowerShell vagy parancssori felület segítségével felügyeleti.

![A Microsoft Azure portal – a virtuális gépek – áttekintés][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Virtuálisgép-példány a felügyeleti és konfigurálási feladatokon is lehetséges a az Azure Portalon.

Újraindítás és a egy virtuális gép leállítása mellett is csatolása, leválasztani, és adatlemezeket a virtuálisgép-példány rendszerkép előkészítésekor példányt létrehozni és konfigurálni a virtuálisgép-példány mérete.

Az Azure Portalon üzembe helyezése és konfigurálása a virtuális gépek és sok más Azure-szolgáltatások alapszintű funkciókat biztosít. Azonban nem minden elérhető funkciók az Azure Portalon alá tartozik. Az Azure Portalon már nem hasonló feladatok elvégzéséhez:

* VHD feltöltése az Azure-bA
* Virtuális gép másolása

[comment]: <> (MShermannd TODO Mi a teendő az automation szolgáltatás SAP-beli virtuális gépek? )
[comment]: <> (MSSedusch több perbe telik, mire lehetővé virtuális gépek operációs rendszer telepítése)
[comment]: <> (MSSedusch is bármely Automation vonatkozó központi telepítési típus nem lehetséges az Azure portal használatával. Feladatokat, köztük a parancsfájlokkal történő üzembe helyezését több virtuális gép nem áll az Azure Portalon keresztül lehetséges.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Felügyelet az Azure PowerShell-parancsmagok
Windows PowerShell az ügyfelek központi telepítése a rendszer az Azure-ban számtalan széles körben elfogadott nagy teljesítményű és bővíthető keretrendszer. A desktop, a laptop vagy a dedikált felügyeleti állomás a PowerShell-parancsmagok a telepítés után a PowerShell-parancsmagok is lehet távolról futtatni.

A folyamat engedélyezéséhez az Azure PowerShell-parancsmagok és konfigurálása, azok esetében a használatot a Azure-előfizetés(ek) ismertetett használati helyi asztali vagy hordozható [Ez a cikk][powershell-install-configure].

Részletesebb útmutatást telepítése, frissítése és konfigurálása az Azure PowerShell parancsmagok is található [Ez a fejezet az üzembe helyezési útmutató][deployment-guide-4.1].

Felhasználói élmény eddig lett, hogy PowerShell (PS) természetesen-e a virtuális gépek üzembe helyezése és egyéni lépéseihez létrehozása a virtuális gépek központi hatékony eszköz. PS parancsmagok lehetőséget a felügyeleti feladatokat az Azure Portalon vagy a PS-parancsmagok is használ kizárólag az Azure-ban az üzembe helyezett megoldások kezelése az SAP-példányok Azure-ban futó ügyfelek használja. Mivel az Azure-ra vonatkozó parancsmagok oszt meg, a több mint 2000 Windows kapcsolatos parancsmagok ugyanazt az elnevezési konvenciót, célszerű ezeket a parancsmagokat a Windows-rendszergazdák egyszerű feladat.

Példa itt talál: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO új CLI-paranccsal tesztelésekor leírása )
Az SAP az Azure Monitoring bővítmény telepítését (című [Azure Monitoring megoldás az SAP] [ planning-guide-9.1] ebben a dokumentumban) csak akkor lehetséges, PowerShell vagy parancssori felület használatával. Ezért azt kötelező való telepítése és konfigurálása a PowerShell vagy parancssori felület üzembe helyezésekor és a egy Azure-beli SAP NetWeaver rendszer felügyelete.  

Az Azure további funkciókat biztosít, új PS-parancsmagok fog hozzáadni, amely megköveteli, hogy a parancsmagok frissítése. Ezért logikus ellenőrizze az Azure letöltési hely a hónap során legalább egyszer <https://azure.microsoft.com/downloads/> parancsmag egy új verzióért. Az új verzió telepítve van a régebbi verziót felett.

A PowerShell Azure-hoz kapcsolódó általános listáját parancsok keresse fel itt: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>A Microsoft Azure CLI-parancsok felügyelet
Az ügyfelek, akiknek Linux használata és kezelése az Azure Powershell erőforrások nem egy lehetőséget. A Microsoft Azure parancssori felület alternatívájaként kínálja.
Az Azure parancssori felülete nyílt forráskódú, platformfüggetlen parancsokat biztosít az Azure Platform használatához. Az Azure CLI ugyanezeket a funkciókat az Azure Portalon található több tartalmazza.

További információ a telepítéshez, konfiguráláshoz és parancssori felület használata parancsok Azure feladatok elvégzéséhez:

* [Az Azure CLI telepítése][xplat-cli]
* [Telepítését és kezelését a virtuális gépek Azure Resource Manager-sablonok és az Azure parancssori felület használatával][../../linux/create-ssh-secured-vm-from-template.md]
* [Az Azure CLI használata Mac, Linux és Windows Azure Resource Managerrel][xplat-cli-azure-resource-manager]

Is olvasható fejezet [Linux virtuális gépek Azure CLI][deployment-guide-4.5.2] a a [telepítési útmutató][planning-guide] központi telepítése az Azure-figyelés az Azure parancssori felület használatával Az SAP-kiterjesztés.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Az SAP az Azure-beli virtuális gépek üzembe helyezése különböző módjai
Ebben a fejezetben elsajátíthatja a virtuális gép üzembe helyezése az Azure-ban különböző módjait. További előkészítést eljárásokat, valamint a virtuális merevlemezek és az Azure-beli virtuális gépek kezelése a fejezet terjed ki.

### <a name="deployment-of-vms-for-sap"></a>Az SAP virtuális gépek telepítése
A Microsoft Azure virtuális gépek és a kapcsolódó lemezek üzembe helyezése több lehetőséget is kínál. Ezért fontos különbségek megértéséhez, mivel a virtuális gépek előkészített a központi telepítési módszertől függően eltérőek lehetnek. Általánosságban elmondható hogy tekintse meg a következő esetekben:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel
Azt tervezi, hogy egy adott SAP-rendszer áthelyezése a helyszínről az Azure-bA. Ezt megteheti az adathoz és naplófájlhoz az adatbázis-kezelő Azure-ba, az a virtuális Merevlemezt, amely tartalmazza az operációs rendszer, az SAP bináris fájljait, és az adatbázis-kezelő bináris fájljainak és a VHD feltöltésével. Ellentétben [alábbi #2. forgatókönyv][planning-guide-5.1.2], az állomásnév, SAP SID, maradjon, és SAP-felhasználó, a helyszíni környezetben konfigurált fiókok az Azure-beli virtuális gépen. Ezért a kép általánosítása már nem szükséges. Fejezeteiben talál [előkészítése virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel] [ planning-guide-5.2.1] jelen dokumentum előkészítő lépések helyszíni és a feltöltése nem általánosított virtuális gépeket és a VHD-k az Azure-bA. Fejezet elolvasása [3. forgatókönyv: virtuális gép áthelyezése a helyszínről az Azure nem általánosított virtuális merevlemez használata SAP] [ deployment-guide-3.4] a a [üzembe helyezési útmutató] [ deployment-guide] számára egy kép, az Azure-ban történő telepítésének részletes lépésein.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Egy ügyfél-specifikus lemezképet használó virtuális gép üzembe helyezése
Adott javítás követelmények miatt az operációs rendszer vagy az adatbázis-kezelő rendszer verzió a a megadott lemezképek az Azure Marketplace-en előfordulhat, hogy nem az igényeinek. Emiatt előfordulhat, hogy kell hozzon létre egy virtuális Gépet, a saját privát operációs rendszer/adatbázis-kezelő virtuális gép rendszerkép használatával, amely ezután többször is telepíthető. Privát rendszerkép előkészítése a másolásra, a következő elemek kell figyelembe venni:

- - -
> ![Windows][Logo_Windows] Windows
>
> Itt további részletek: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> a Windows-beállítások (például a Windows biztonsági AZONOSÍTÓK és állomásnév) kell lennie a helyszíni virtuális gépen keresztül a sysprep parancsot emeli ki/általánosítva.
>
>
> ![Linux][Logo_Linux] Linux
>
> Az ezekben a cikkekben ismertetett lépéseket követve [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], vagy [Oracle Linux] [ virtual-machines-linux-create-upload-vhd-oracle], fel kell tölteni az Azure-ban virtuális merevlemez előkészítése.
>
>

- - -
Ha már telepítette az SAP-tartalom a helyszíni virtuális gépen (különösen a 2 szintű rendszerekhez), központi telepítését az Azure VM-példány az eljárás szerint a SAP Software kiépítés Manager (SAP támogatott átnevezése után módosíthatja úgy a SAP-rendszer beállításai Vegye figyelembe [1619720]). Fejezeteiben talál [specifikus lemezképet használó virtuális gép telepítése az SAP-előkészítése] [ planning-guide-5.2.2] és [feltöltése az Azure-bA a helyszíni virtuális merevlemez] [ planning-guide-5.3.2]jelen dokumentum előkészítő lépések helyszíni és a egy általánosított virtuális gép az Azure-bA feltöltése. Fejezet elolvasása [2. forgatókönyv: egyéni rendszerkép rendelkező virtuális gép telepítése az SAP] [ deployment-guide-3.3] a a [üzembe helyezési útmutató] [ deployment-guide] üzembe helyezésének részletes leírását az ilyen kép az Azure-ban.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Az Azure Marketplace-en kívül egy virtuális gép üzembe helyezése
Szeretné használni a Microsoft, illetve a megadott külső Virtuálisgép-rendszerképet a virtuális gép üzembe helyezéséhez az Azure piactérről. Az Azure-ban a virtuális gép központi telepítése után, kövesse az irányelvek és az eszközök telepítése a SAP-szoftverek és/vagy az adatbázis-kezelő a virtuális Gépen található, mint a helyszíni környezetben. Részletes üzembe helyezés leírása, tekintse meg a fejezet [1. eset: egy virtuális géphez, az SAP az Azure piactéren kívül] [ deployment-guide-3.2] a a [üzembe helyezési útmutató] [ deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Az Azure az SAP virtuális gépek előkészítése
Mielőtt feltöltené a virtuális gépek az Azure-ba, ellenőrizze, hogy a virtuális gépek és virtuális merevlemezek megfelel bizonyos követelményeknek kell. A használt központi telepítési módszertől függően kisebb különbségek vannak.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Felkészülés a virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel
Gyakori üzembe helyezési módszer, hogy egy meglévő virtuális Gépet, amelyek egy SAP-rendszerrel futtatja a helyszínről az Azure-bA. A virtuális Gépeket és a virtuális gép az SAP-rendszerrel ugyanúgy kell futtatnia az Azure-ban az azonos állomásnévvel és valószínűleg SAP SID AZONOSÍTÓVAL. Ebben az esetben a vendég operációs rendszer a virtuális Gépen kell nem általánosítható több központi telepítést. Ha a helyszíni hálózat lett terjesztve, az Azure (lásd: fejezet [létesítmények közötti – üzembe egyetlen vagy több SAP virtuális gép az Azure-bA az a követelménye alatt teljes körűen integrálva a helyszíni hálózat] [ planning-guide-2.2] a jelen dokumentum), majd még az ugyanazon tartományi fiókok is használható a virtuális Gépet, azokat a helyszíni előtt használt.

A saját Azure Virtuálisgép-lemez előkészítésekor követelmények a következők:

* Eredetileg az operációs rendszert tartalmazó virtuális merevlemez mérete 127GB csak rendelkezhet. Ez a korlátozás van kiküszöbölhetők a 2015. március végén. Most az operációs rendszert tartalmazó virtuális merevlemez lehet bármely más Azure Storage, valamint tárolt virtuális merevlemez mérete legfeljebb 1TB.
* Kell lennie a rögzített méretű VHD formátumban. Dinamikus VHD vagy VHDx formátumú virtuális merevlemezeket még nem támogatottak az Azure-ban. Dinamikus VHD-k statikus virtuális merevlemezek alakul, amikor feltölti a VHD-t a PowerShell-parancsmagok vagy a parancssori felület
* Virtuális merevlemezek, amelyek csatlakoztatva vannak a virtuális géphez, és kell csatlakoztatnia kell újra az Azure-ban, valamint egy rögzített VHD formátumú virtuális gép szükséges. Olvasási [ebben a cikkben (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) és [ebben a cikkben (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) méretbeli korlátokat, adatlemez esetében. Dinamikus VHD-k statikus virtuális merevlemezek alakul, amikor feltölti a VHD-t a PowerShell-parancsmagok vagy a parancssori felület
* Adjon hozzá egy másik helyi fiók rendszergazdai jogokkal, amely a Microsoft támogatási vagy helyi szolgáltatások és alkalmazások futtatásához, amíg a virtuális gép üzemel, amely lehet hozzárendelni, és jobban megfelelő felhasználók által használható használható.
* A kis-és a egy kizárólag felhőalapú üzembe helyezési forgatókönyv használatával (című [csak felhőalapú - függőségek a helyszíni ügyfél hálózat nélkül az Azure-bA a virtuális gépi környezetekben] [ planning-guide-2.1] ebben a dokumentumban) a Ez a telepítési módszer kombinálva, tartományi fiókok nem működnek az Azure-ban az Azure-lemezek üzembe helyezése után. Ez különösen igaz a fiókok, amelyek segítségével a szolgáltatások, például az adatbázis-kezelő vagy SAP alkalmazások futtatása. Ezért kell cserélje le az ilyen tartományi fiókok virtuális gép helyi felhasználói fiókok és a helyszíni tartományi fiókokat a virtuális gép törlése. A Virtuálisgép-lemezkép tartja a helyszíni tartományi felhasználók esetén nem egy problémát a virtuális gép van üzembe helyezve a létesítmények közötti forgatókönyv fejezetben leírtak szerint [létesítmények közötti – üzembe egyetlen vagy több SAP virtuális gép az Azure-ba, hogy teljes mértékben a követelményeknek integrálva a helyszíni hálózat] [ planning-guide-2.2] ebben a dokumentumban.
* Ha tartományi fiókokat használt DBMS-bejelentkezések és felhasználók, amikor a rendszer a helyi és virtuális gépeken futó kellene telepíteni szeretné a kizárólag felhőalapú forgatókönyveket, a tartományi felhasználók törölni kell. Győződjön meg arról, hogy a helyi rendszergazda és a egy másik virtuális gép helyi felhasználót adnak hozzá egy bejelentkezési/felhasználóként a DBMS-rendszergazdák kell.
* Adja hozzá a többi helyi fiókok azokat, akkor lehet szükség, az adott központi telepítési forgatókönyv szerint.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ebben a forgatókönyvben nem általánossá (sysprep) a virtuális gép és az Azure-ban a virtuális gép telepítése szükséges.
> Győződjön meg arról, hogy nem használatos a D:\ meghajtóra.
> Állítsa be a csatlakoztatott lemezek lemez automount fejezetben leírtak szerint [beállítás automount a csatlakoztatott lemezeket] [ planning-guide-5.5.3] ebben a dokumentumban.
>
> ![Linux][Logo_Linux] Linux
>
> Ebben a forgatókönyvben nem általánossá (waagent-deprovision) a virtuális gép van szükség fel-és az Azure-beli virtuális gép üzembe helyezése.
> Győződjön meg arról, hogy nem használja a/mnt/erőforrás és, hogy az összes lemez uuid-n keresztül vannak csatlakoztatva. Az operációsrendszer-lemez győződjön meg arról, hogy a rendszerbetöltőt bejegyzés is tükrözi az uuid-alapú csatlakoztatási.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Egy ügyfél-specifikus rendszerképpel rendelkező virtuális gép telepítése az SAP-előkészítése
VHD-fájlokat, amelyek tartalmaznak egy általánosított operációs rendszer tárolók az Azure Storage-fiókok vagy felügyelt lemezképeket vannak tárolva. Telepíthet egy új virtuális Gépet egy rendszerképből fejezetben leírtak szerint a VHD- vagy Managed Disk kép lére a központi telepítési sablon fájlokban forrásként [2. forgatókönyv: egyéni rendszerkép rendelkező virtuális gép telepítése az SAP] [ deployment-guide-3.3], a [üzembe helyezési útmutató][deployment-guide].

A saját Azure Virtuálisgép-rendszerkép előkészítésekor követelmények a következők:

* Eredetileg az operációs rendszert tartalmazó virtuális merevlemez mérete 127GB csak rendelkezhet. Ez a korlátozás van kiküszöbölhetők a 2015. március végén. Most az operációs rendszert tartalmazó virtuális merevlemez lehet bármely más Azure Storage, valamint tárolt virtuális merevlemez mérete legfeljebb 1TB.
* Kell lennie a rögzített méretű VHD formátumban. Dinamikus VHD vagy VHDx formátumú virtuális merevlemezeket még nem támogatottak az Azure-ban. Dinamikus VHD-k statikus virtuális merevlemezek alakul, amikor feltölti a VHD-t a PowerShell-parancsmagok vagy a parancssori felület
* Virtuális merevlemezek, amelyek csatlakoztatva vannak a virtuális géphez, és kell csatlakoztatnia kell újra az Azure-ban, valamint egy rögzített VHD formátumú virtuális gép szükséges. Kérjük, olvassa el [ebben a cikkben (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) és [ebben a cikkben (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) méretbeli korlátokat, adatlemez esetében. Dinamikus VHD-k statikus virtuális merevlemezek alakul, amikor feltölti a VHD-t a PowerShell-parancsmagok vagy a parancssori felület
* Mivel a tartományi felhasználók regisztrálni, mert a felhasználók a virtuális gép nem létezik a csak felhőalapú forgatókönyvekben (című [csak felhőalapú - virtuális gépi környezetekben az Azure-bA a helyszíni ügyfél hálózaton függőségek nélkül] [ planning-guide-2.1] Ez a dokumentum), az ilyen fiókok nem működik az Azure-ban a lemezkép üzembe helyezése után tartományi szolgáltatásokat. Ez különösen igaz a fiókok, amelyek segítségével a szolgáltatások, például az adatbázis-kezelő vagy SAP alkalmazások futtatása. Ezért kell cserélje le az ilyen tartományi fiókok virtuális gép helyi felhasználói fiókok és a helyszíni tartományi fiókokat a virtuális gép törlése. A helyszíni tartományi felhasználók tartja a Virtuálisgép-lemezkép nem feltétlenül egy problémát, ha a virtuális gép van üzembe helyezve a létesítmények közötti forgatókönyv fejezetben leírtak szerint [létesítmények közötti – üzembe egyetlen vagy több SAP virtuális gép az Azure-ba, hogy a követelményeknek teljes körűen integrálva a helyszíni hálózat] [ planning-guide-2.2] ebben a dokumentumban.
* Adjon hozzá egy másik helyi fiók rendszergazdai jogokkal, amely probléma vizsgálatok vagy a helyi szolgáltatások és alkalmazások futtatásához, amíg a virtuális gép üzemel, amely lehet hozzárendelni a Microsoft támogatási és jobban megfelelő felhasználók által használható használható.
* A csak Felhőbeli, és ahol tartományi fiókok használt DBMS-bejelentkezések és felhasználók, amikor a rendszer a helyszínen futó a tartományi felhasználók törölni kell. Győződjön meg arról, hogy a helyi rendszergazda és a egy másik virtuális gép helyi felhasználót adnak a DBMS-rendszergazdák a bejelentkezési/felhasználóként kell.
* Adja hozzá a többi helyi fiókok azokat, akkor lehet szükség, az adott központi telepítési forgatókönyv szerint.
* Ha a rendszerkép tartalmazza, telepíteni kell az SAP NetWeaver és az állomás nevét az Azure üzembehelyezési ponton kell megadni az eredeti nevén átnevezése valószínű, javasoljuk, hogy a legfrissebb verzióit a SAP Software kiépítés Manager DVD másolja a sablont. Ez lehetővé teszi, hogy egyszerűen a megadott SAP átnevezése funkciójának használata a módosított állomásnév alkalmazkodnak és/vagy módosítsa a biztonsági AZONOSÍTÓT az SAP-rendszer belül az üzembe helyezett Virtuálisgép-rendszerképet, amint az új példány indítása.

- - -
> ![Windows][Logo_Windows] Windows
>
> Győződjön meg arról, hogy nem a D:\ meghajtóra használt Set lemez automount csatlakoztatott lemezek fejezetben leírtak szerint [beállítás automount a csatlakoztatott lemezeket] [ planning-guide-5.5.3] ebben a dokumentumban.
>
> ![Linux][Logo_Linux] Linux
>
> Győződjön meg arról, hogy nem használja a/mnt/erőforrás és, hogy az összes lemez uuid-n keresztül vannak csatlakoztatva. Az operációsrendszer-lemez ügyeljen a rendszerbetöltőt bejegyzés is tükrözi az uuid-alapú csatlakoztatási.
>
>

- - -
* SAP grafikus felhasználói felület (a felügyeleti és beállítása céljából) egy sablonban előre telepített.
* A virtuális gépek a létesítmények közötti forgatókönyvek sikeres futtatásához szükséges más szoftverek is telepíthető, mindaddig, amíg ez a szoftver képes együttműködni az Átnevezés, a virtuális gép.

A virtuális gép készen áll elég általános, és végül független a fiókok és felhasználói számára a célzott Azure üzembe helyezési forgatókönyv nem érhető el, ha az utolsó előkészítési lépés ilyen kép általánosítása végez.

##### <a name="generalizing-a-vm"></a>Virtuális gép általánosítása
- - -
> ![Windows][Logo_Windows] Windows
>
> Az utolsó lépés, hogy jelentkezzen be rendszergazdai fiókkal rendelkező virtuális Gépet. Nyisson meg egy Windows parancssori ablakban, *rendszergazda*. Ugrás a %windir%\windows\system32\sysprep, és hajtsa végre a sysprep.exe.
> Egy kis ablakban jelenik meg. Fontos, hogy ellenőrizze a **Generalize** (alapértelmezés szerint nincs bejelölve) lehetőséget, és módosítsa a leállítási lehetőséget a "shutdown" az alapértelmezett "Újraindítás". Ez az eljárás feltételezi, hogy a sysprep folyamat végrehajtott helyszíni virtuális gépek vendég operációs rendszer.
> Ha azt szeretné, hajtsa végre az Azure-ban már futó virtuális gépen, az ismertetett lépéseket követve [Ez a cikk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [A Resource Manager-sablonként használni egy Linuxos virtuális gép rögzítése][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Virtuális gépek és virtuális merevlemezek átvitele az Azure-bA helyszíni között
Mivel a Virtuálisgép-lemezképek és lemezek feltöltése az Azure-ban ez nem lehetséges az Azure Portalon keresztül, kell használnia az Azure PowerShell-parancsmagok vagy a parancssori felület. Egy másik lehetőség, az eszköz 'AzCopy' használatát. Az eszköz másolhat VHD-k a helyszíni és az Azure között (kétirányú). Azt is másolhatja VHD-k az Azure-régiók között. Tekintse meg [ebben a dokumentációban] [ storage-use-azcopy] letöltése és az AzCopy használatát.

Harmadik lehetőségként a különböző külső GUI-orientált eszközeivel lenne. Azonban győződjön meg arról, hogy ezek az eszközök Azure-Lapblobokkal támogatnak. A jelen Azure Lapblob áruház használata kell (a különbségek ebben a témakörben találhatók: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Az Azure által biztosított eszközök is hatékony az tömöríti a virtuális gépek és virtuális merevlemezek, amelyeket fel kell tölteni. Ez azért fontos, mert a hatékonyságot a tömörítés csökkenti a kísérlet ideje (amely függvényében ennek ellenére a feltöltési kapcsolaton az internetre a helyszíni létesítmény és a megcélzott Azure-beli régióban). Egy valós feltételezve, hogy a virtuális gép vagy az adatközpontok hosszabb időt vesz igénybe, mint az azonos virtuális gépek VHD feltöltése az Európai Azure-ban az Azure az Egyesült államokbeli adatok európai helyről VHD feltöltése az adatközpontok.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Az Azure-bA a helyi VHD feltöltése
Egy meglévő virtuális Gépet vagy virtuális merevlemez feltöltéséhez a helyszíni hálózatról egy virtuális Gépet vagy virtuális Merevlemezt kell a követelmények teljesítéséhez fejezetben felsorolt [előkészítése virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel] [ planning-guide-5.2.1]ebben a dokumentumban.

Egy virtuális Gépet nem kell általánosítani, és az állapot és rendelkezik a helyszíni oldalon leállást követően alakzat fel lehet tölteni. Ugyanez igaz további virtuális merevlemezek, amelyek nem tartalmaznak minden operációs rendszeren.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>VHD feltöltése és így az Azure-lemez
Ebben az esetben azt szeretné, akár anélkül, az operációs rendszer VHD feltöltése és csatlakoztassa a virtuális gép adatlemezként vagy operációsrendszer-lemez szeretné használni. Egy több lépésből álló folyamat

**PowerShell**

* Jelentkezzen be az előfizetéshez *Connect-AzureRmAccount*
* Az előfizetés az a környezet beállításához *Set-AzureRmContext* és előfizetés-azonosító paramétert vagy a SubscriptionName - lásd: <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Töltse fel a virtuális Merevlemezt *Add-AzureRmVhd* egy Azure Storage-fiók – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Nem kötelező) Hozzon létre egy felügyelt lemezt a virtuális Merevlemezt a *New-AzureRmDisk* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Egy új virtuális gép konfigurációs operációsrendszer-lemezének beállítani a virtuális merevlemez vagy a felügyelt lemezről az *Set-AzureRmVMOSDisk* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Egy új virtuális gép létrehozása a virtuális gép konfigurációt *New-AzureRmVM* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Adatlemez hozzáadása egy új virtuális Gépet *Add-AzureRmVMDataDisk* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Azure CLI 2.0**

* Jelentkezzen be az előfizetéshez *az login*
* Válassza ki az előfizetését *az fiók beállítása – előfizetés `<subscription name or id`>*
* Töltse fel a virtuális Merevlemezt *az storage blob feltöltése* – lásd: [az Azure Storage az Azure CLI használatával][storage-azure-cli]
* (Nem kötelező) Hozzon létre egy felügyelt lemezt a virtuális Merevlemezt a *az lemez létrehozása* – lásd: https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Hozzon létre egy új virtuális Gépet a feltöltött virtuális merevlemez vagy a felügyelt lemez-t adja meg az operációsrendszer-lemez *az virtuális gép létrehozása* és paraméter *--csatolása operációsrendszer-lemez*
* Adatlemez hozzáadása egy új virtuális Gépet *az vm disk attach* és paraméter *– új*

**Sablon**

* Töltse fel a VHD-t a Powershell vagy az Azure CLI-vel
* (Nem kötelező) Felügyelt lemez létrehozása a Powershell, Azure CLI-vel vagy az Azure Portalon a virtuális merevlemezből
* Telepítse a virtuális Gépet egy JSON-sablon hivatkozik a VHD-t, ahogyan az a [Ez a példa JSON-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) vagy a felügyelt lemezek használatával, ahogyan [Ez a példa JSON-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Virtuálisgép-rendszerkép telepítése
Egy meglévő virtuális Gépet vagy virtuális merevlemez feltöltéséhez a helyszíni hálózatból Azure-beli Virtuálisgép-lemezképként használatához egy virtuális Gépet vagy virtuális Merevlemezhez meg kell felelniük a fejezetben felsorolt követelményeknek [specifikus lemezképet használó virtuális gép telepítése az SAP-előkészítése] [ planning-guide-5.2.2] ebben a dokumentumban.

* Használja *sysprep* a Windows vagy *waagent-deprovision* Linux általánosítani a virtuális gép – lásd: [technikai útmutató a Sysprep](https://technet.microsoft.com/library/cc766049.aspx) a Windows vagy [rögzítése egy Linuxos virtuális gép használja, mint egy Resource Manager-sablon] [ capture-image-linux-step-2-create-vm-image] linuxhoz
* Jelentkezzen be az előfizetéshez *Connect-AzureRmAccount*
* Az előfizetés az a környezet beállításához *Set-AzureRmContext* és előfizetés-azonosító paramétert vagy a SubscriptionName - lásd: <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Töltse fel a virtuális Merevlemezt *Add-AzureRmVhd* egy Azure Storage-fiók – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Nem kötelező) Felügyelt lemez rendszerképének létrehozása a VHD-vel rendelkező *New-AzureRmImage* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Egy új virtuális gép konfigurációs az operációsrendszer-lemezének beállítani a
  * A virtuális merevlemez *Set-AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Felügyelt lemez rendszerképének *Set-AzureRmVMSourceImage* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Egy új virtuális gép létrehozása a virtuális gép konfigurációt *New-AzureRmVM* – lásd: <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Azure CLI 2.0**

* Használja *sysprep* a Windows vagy *waagent-deprovision* Linux általánosítani a virtuális gép – lásd: [technikai útmutató a Sysprep](https://technet.microsoft.com/library/cc766049.aspx) a Windows vagy [rögzítése egy Linuxos virtuális gép használja, mint egy Resource Manager-sablon] [ capture-image-linux-step-2-create-vm-image] linuxhoz
* Jelentkezzen be az előfizetéshez *az login*
* Válassza ki az előfizetését *az fiók beállítása – előfizetés `<subscription name or id`>*
* Töltse fel a virtuális Merevlemezt *az storage blob feltöltése* – lásd: [az Azure Storage az Azure CLI használatával][storage-azure-cli]
* (Nem kötelező) Felügyelt lemez rendszerképének létrehozása a VHD-vel rendelkező *az lemezkép létrehozása* – lásd: https://docs.microsoft.com/cli/azure/image#az_image_create
* Hozzon létre egy új virtuális Gépet a feltöltött virtuális merevlemez vagy a felügyelt lemez rendszerképének-t adja meg az operációsrendszer-lemez *az virtuális gép létrehozása* és paraméter *– kép*

**Sablon**

* Használja *sysprep* a Windows vagy *waagent-deprovision* Linux általánosítani a virtuális gép – lásd: [technikai útmutató a Sysprep](https://technet.microsoft.com/library/cc766049.aspx) a Windows vagy [rögzítése egy Linuxos virtuális gép használja, mint egy Resource Manager-sablon] [ capture-image-linux-step-2-create-vm-image] linuxhoz
* Töltse fel a VHD-t a Powershell vagy az Azure CLI-vel
* (Nem kötelező) Felügyelt lemez rendszerképének létrehozása a Powershell-lel, Azure CLI-vel, vagy az Azure Portalon a virtuális merevlemezből
* Telepítse a virtuális Gépet egy JSON-sablon hivatkozik a kép VHD-t, ahogyan az a [Ez a példa JSON-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) vagy a felügyelt lemez rendszerkép használatával, ahogyan az [Ez a példa JSON-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>A helyszíni virtuális merevlemezek vagy a felügyelt lemezek letöltése
Az Azure infrastruktúra-szolgáltatás nem egy egyirányú utcában csak tudnak töltse fel a VHD-k és az SAP-rendszerek. Továbbléphet az SAP az Azure-ból rendszerek vissza világszerte, valamint a helyszíni szolgáltatásba.

A letöltés ideje alatt a VHD-k vagy a felügyelt lemezeket nem lehet aktív. Akkor is, ha a letöltés lemezek, amelyek csatlakoztatva vannak a virtuális gépek, a virtuális gép leállítása és felszabadítása van szüksége. Ha csak szeretné letölteni a használandó, majd állítsa be az adatbázis tartalmat egy új rendszer a helyszínen, ha elfogadható, amelyek a letöltés idején és az új rendszerre, hogy a rendszer az Azure-ban is beállítása során kell működési , sikerült hosszú kialakításának lemezzé tömörített adatbázis biztonsági mentésének végrehajtásával, és csak töltse le a lemezt, hanem is az operációs rendszer alap virtuális Gépet.

#### <a name="powershell"></a>PowerShell

  * Felügyelt lemez letöltése  
  Először az alapul szolgáló blob a felügyelt lemez eléréséhez. Ezután az alapul szolgáló blob átmásolása egy új tárfiókot, és a blob letöltése a storage-fiókból.

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

  * VHD letöltése  
  Az SAP-rendszer le van állítva, és a virtuális gép leállt, használhatja a PowerShell-parancsmag Save-AzureRmVhd a helyszíni cél töltheti le a VHD lemezek vissza a helyszíni tételéhez. A virtuális Merevlemezt, amely találhatja meg a "storage szakasz" URL-CÍMÉT kell megtenni, az Azure Portalon (Nyissa meg a Tárfiók és a storage-tárolót, ahol a virtuális merevlemez létrehozásának szükség), és ismernie kell, a virtuális Merevlemezt másolja.

  A parancs kihasználhatja majd definiálásával SourceUri paraméter az URL-címet a virtuális merevlemez letöltésére és a LocalFilePath, mint a fizikai helye a VHD-t (mint például a neve). A parancs nézhet:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  A Save-AzureRmVhd parancsmag további részletekért ellenőrizze az itt <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Felügyelt lemez letöltése  
  Először az alapul szolgáló blob a felügyelt lemez eléréséhez. Ezután az alapul szolgáló blob átmásolása egy új tárfiókot, és a blob letöltése a storage-fiókból.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * VHD letöltése   
  Az SAP-rendszer le van állítva, és a virtuális gép leállítása után is használhatja az Azure CLI-paranccsal _az azure storage blob download_ töltheti le a VHD-t a helyszíni célként megadott lemezek biztonsági helyszíni tételéhez. A nevet kell megtenni, és a virtuális Merevlemezt, amely az Azure Portalon (Nyissa meg a Tárfiók és a storage-tárolót, ahol a virtuális merevlemez létrehozásának szükség), a tárolás részében találhatja, a tárolót, és át kell tudnia, hol kell lennie má a másolat.

  A parancs kihasználhatja majd a paramétereket a blob és a VHD-t tartalmazó tároló definiálásával letöltése és a cél fizikai célhelyként a virtuális merevlemez (mint például a neve). A parancs nézhet:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Átadó virtuális gépeket és lemezeket az Azure-ban
#### <a name="copying-sap-systems-within-azure"></a>Azure-beli SAP-rendszereinket másolása
Egy SAP-rendszerrel, vagy akár egy SAP alkalmazásréteg támogató dedikált adatbázis-kezelő kiszolgáló valószínűleg több lemezt, vagy az operációs rendszer és a bináris fájlok vagy az SAP-adatbázist az adat- és naplófájlok fájl(ok) tartalmaznak olyan állnak. Sem a lemezek másolása az Azure funkcióit sem nem lemezek egy helyi lemezre mentése az Azure működésének rendelkezik egy szinkronizálási mechanizmust, mely pillanatképeket több lemez egységes módon. Ezért a másolt vagy mentett lemez állapota akkor is, ha azok csatlakoztatva vannak az azonos virtuális gépek elleni eltérő lenne. Ez azt jelenti, hogy, hogy a különböző adatokat és az eltérő lemezeken található logfile(s) a konkrét esetben az adatbázis a végén lenne inkonzisztens.

**Részletes ismertetők: Másolja, vagy mentse a lemezeket, az SAP-rendszer konfiguráció részét képező kell leállítása az SAP-rendszerhez, és állítsa le az üzembe helyezett virtuális gép is kell. Csak ezután másolja, vagy töltse le az SAP-rendszer másolatot létrehozni Azure-ban vagy a helyi lemezeken.**

Adatlemezek is tárolhatók az Azure Storage-fiókban VHD-fájlok formájában, és egy virtuális géphez közvetlenül csatlakoztatott vagy képként használni. Ebben az esetben a virtuális Merevlemezt másolja egy másik helyre előtt a virtuális géphez csatolni. A teljes nevét a VHD-fájlt az Azure-beli Azure-on belül egyedinek kell lennie. Ahogy korábban már említettük, a név típusú a következőhöz hasonló háromrészes név:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Az adatlemezeket is Managed Disks. Ebben az esetben a felügyelt lemezt használja egy új felügyelt lemez létrehozása előtt a virtuális géphez csatolni. A felügyelt lemez neve erőforráscsoporton belül egyedinek kell lennie.

##### <a name="powershell"></a>PowerShell
Azure PowerShell-parancsmagok segítségével másolja egy VHD-t, ahogyan az [Ez a cikk][storage-powershell-guide-full-copy-vhd]. Egy új felügyelt lemez létrehozásához használja a New-azurermdiskconfig parancsmaghoz és New-AzureRmDisk lehetőséget, az alábbi példában látható módon.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Azure CLI használatával másolja egy VHD-t, ahogyan az [Ez a cikk][storage-azure-cli-copy-blobs]. Az új felügyelt lemez létrehozásához használja *az lemez létrehozása* az alábbi példában látható módon.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Az Azure Storage-eszközökkel
* <http://storageexplorer.com/>

Az Azure Storage-tallózók Professional kiadása esetén itt található:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

A virtuális merevlemez másolatának készítése magát a tárfiókon belül egy olyan folyamat, amely (SAN-hardverre pillanatképeket létrehozni a Lusta másolása és másolási írás hasonló) csak néhány másodpercet vesz igénybe. Miután a VHD-fájl egy példányát, csatlakoztat egy virtuális géphez, vagy másolja a virtuális merevlemez csatlakoztatása a virtuális gépek képként használni.

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

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Az Azure Storage-fiókok közötti lemezek másolása
Ez a feladat nem hajtható végre az Azure Portalon. Azure PowerShell parancsmagok, Azure CLI-vel vagy egy külső tároló böngészőben is használhatja. A PowerShell-parancsmagokkal vagy a parancssori felület parancsai hozzon létre és kezelheti a blobokat, amelyek között megtalálható a blobok aszinkron módon másolása Tárfiókok között, és az Azure-előfizetésen belül-régiók között.

##### <a name="powershell"></a>PowerShell
Virtuális merevlemezek előfizetések között is másolhatja. További információkért olvassa el [Ez a cikk][storage-powershell-guide-full-copy-vhd].

A PS-parancsmag logika használt alapvető folyamat a következőhöz hasonló:

* Hozzon létre egy a tárfiók környezetét a **forrás** tárfiók *New-azurestoragecontext parancsmaggal kapcsolatos* – lásd: <https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext>
* Hozzon létre egy a tárfiók környezetét a **cél** tárfiók *New-azurestoragecontext parancsmaggal kapcsolatos* – lásd: <https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext>
* Indítsa el a másolatot

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Egy hurokba, és a másolat állapotának ellenőrzéséhez

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Az új virtuális merevlemez egy virtuális géphez csatolni a fent leírt módon.

További példák: [Ez a cikk][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Indítsa el a másolatot

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Ellenőrizze az állapotát, ha a példány továbbra is szerepel egy kör felépítése

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Az új virtuális merevlemez egy virtuális géphez csatolni a fent leírt módon.

További példák: [Ez a cikk][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Lemez kezelése
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Virtuálisgép-lemez szerkezet SAP-környezetekhez
A virtuális gép és a kapcsolódó lemezek szerkezete kezelésének ideális egyszerű kell lenniük. A helyszíni telepítésekre, az ügyfelek fejlesztett, strukturálja a kiszolgáló telepítése számos módon.

* Egy alap lemez, amelyre az operációs rendszer és az adatbázis-kezelő és/vagy az SAP minden bináris fájl tartalmaz. 2015. március óta a lemez lehet akár 1TB-nál, legfeljebb 127 GB korábbi korlátozások helyett.
* Egy vagy több lemez, amely tartalmazza a DBMS naplófájl, az SAP adatbázis és az adatbázis-kezelő ideiglenes tárterület naplófájl (ha az adatbázis-kezelő támogatja ezt). Ha az adatbázis naplózási IOPS követelményeknek magas, szüksége több lemez stripe-elérése érdekében a szükséges IOPS-kötetet.
* Az SAP adatbázis egy vagy két adatbázis-fájlokat és az adatbázis-kezelő ideiglenes adatokat fájlokat is tartalmazó, (ha az adatbázis-kezelő támogatja ezt) lemezek száma.

![Az SAP az Azure IaaS virtuális gép konfigurációjának referencia][planning-guide-figure-1300]


- - -
> ![Windows][Logo_Windows] Windows
>
> A számos ügyfél látott konfigurációk, például SAP- és adatbázis-kezelő bináris fájljai nem telepítették a a c:\ meghajtó, ahol az operációs rendszer telepítve van. Ennek számos oka van, de azt vissza a legfelső szintű történt, amikor általában volt, hogy a meghajtó sem volt kis és az operációs rendszer verziófrissítései szükség további terület 10 – 15 éve. Mindkét feltétel napjainkban túl gyakran, többé nem érvényesek. Ma a c:\ meghajtóra is le lehet képezni nagy lemezek vagy virtuális gépeken. Annak érdekében, hogy a központi telepítések a struktúra egyszerű maradjon, javasoljuk, hogy az Azure-beli SAP NetWeaver-rendszerekhez a következő központi telepítési mintának
>
> A Windows operációs rendszer lapozófájl a D: meghajtón (a nem állandó lemez) kell lennie.
>
> ![Linux][Logo_Linux] Linux
>
> Helyezze el a Linuxos swapfile alatt /mnt/mnt/erőforrás linuxon leírtak szerint [Ez a cikk][virtual-machines-linux-agent-user-guide]. A lapozófájl a konfigurációs fájlban, a Linux-ügynök /etc/waagent.conf konfigurálható. Adja hozzá, vagy módosítsa a következő beállításokat:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Az aktiválás a módosításokat, majd újra kell indítania a Linux-ügynök

```
sudo service waagent restart
```

Olvassa el az SAP-Jegyzetnek [1597355] kapcsolatos további részletekért a javasolt lapozófájl-kapacitás mérete

- - -
Az adatbázis-kezelő adatfájlok és az Azure Storage ezeket a lemezeket tárolnak a típusú használt lemezek száma az IOPS-követelmények és a késés szükséges kell meghatározni. Pontos kvóták ismertetett [ebben a cikkben (Linux)] [ virtual-machines-sizes-linux] és [ebben a cikkben (Windows)][virtual-machines-sizes-windows].

Az SAP üzemelő példányok az előző két éven át élmény színesített velünk a kapcsolatot néhány leckék szerint lehet összegezni:

* IOPS-forgalom különböző adatfájlok értéke nem mindig ugyanaz, mivel a meglévő ügyfél rendszerek előfordulhat, hogy rendelkezik eltérően méretű adatfájlokat jelölő, az SAP adatbázis (oka). Ennek eredményeképpen, kiderült jobban használatához egy RAID-konfigurációt több lemez fölé helyezi el a logikai egységek faragottnak ki azokat az adatfájlokat. Nincs olyan helyzetekben, különösen az Azure standard szintű Storage, az IOPS ráta nyomja le az adatbázis-kezelő tranzakciónapló szemben egyetlen lemez kvótáját. Ilyen esetekben a prémium szintű Storage használata javasolt, vagy másik lehetőségként összesíti több standard szintű Storage stripe-lemezeket a szoftverrel.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Teljesítmény az Azure Virtual machines gépeken az SQL Server ajánlott eljárásai][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Szoftveres RAID linuxon konfigurálása][virtual-machines-linux-configure-raid]
> * [LVM konfigurálása az Azure-beli Linuxos virtuális gépre][virtual-machines-linux-configure-lvm]
> * [Az Azure Storage titkos kulcsok és a Linux-i/o-optimalizálást](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* A Premium Storage jelentős jobb teljesítményt, különösen a kritikus fontosságú tranzakciós napló írási műveletek láthatók. Az SAP-forgatókönyvek, amelyek várhatóan éleshez hasonlító teljesítményt nyújthat mindenképpen ajánlatos Virtuálisgép-sorozat, amelyek kihasználhatják az Azure Premium Storage használatához.

Vegye figyelembe, hogy a lemez, amely tartalmazza az operációs rendszer, és azt javasoljuk, az SAP bináris fájljait és az adatbázis (alap VM), már nem legfeljebb 127GB. Most már rendelkezhet legfeljebb 1TB-nál. Ez többek között például az SAP feladat naplók a batchben az összes szükséges fájl megtartandó elegendő hely legyen.

Kapcsolatos további javaslatok és a kifejezetten az adatbázis-kezelő virtuális gépeket, a további részletekért tekintse meg a [DBMS üzembe helyezési útmutató][dbms-guide]

#### <a name="disk-handling"></a>Lemez kezelése
A legtöbb esetben szüksége további lemezeket annak érdekében, hogy az SAP adatbázis helyezze üzembe a virtuális gép létrehozásához. A fejezet lemezek számát a beszéltünk a szempontok [Virtuálisgép-lemez szerkezet SAP-környezetekhez] [ planning-guide-5.5.1] ebben a dokumentumban. Az Azure portal lehetővé teszi, hogy csatolása és leválasztása lemezek alap virtuális gép üzembe helyezése után. A lemezeket lehet csatlakoztatott vagy leválasztott fel van és fut, valamint ha le van állítva. Ha egy lemez csatolása, az Azure Portalon üres lemez vagy egy meglévő lemez, amely ezen a ponton a időben történő nincs csatolva egy másik virtuális Géphez csatolni kínál.

**Megjegyzés:**: lemezek csak lehet rendelni egy virtuális gép egy adott időpontban.

![Csatolása / leválasztása az Azure standard szintű Storage-lemezek][planning-guide-figure-1400]

Új virtuális gép telepítése közben eldöntheti, hogy szeretné-e felügyelt lemezek használata, vagy helyezze a lemezeket az Azure Storage-fiókok. Ha azt szeretné, a Premium Storage használatához, felügyelt lemezek használatát javasoljuk.

Ezután meg kell döntse el, hogy szeretné-e az új és üres lemez létrehozása, vagy hogy jelöljön ki egy meglévő lemezt, amely korábban feltöltött, és most kell csatlakoztatni a virtuális Gépen szeretné.

**FONTOS**: Ön **nem** szeretné használni az állomás-gyorsítótárazás az Azure standard szintű tárolást. A gazdagép gyorsítótáras szabályozó hagyja az alapértelmezett, none. Az Azure Premium Storage engedélyeznie kell a olvasási gyorsítótárazást, ha az i/o-jellemző többnyire olvasható adatbázis adatfájlok elleni ugyanúgy átlagos i/o. Adatbázis-tranzakciós naplófájl, esetén nincs gyorsítótárazás használata javasolt.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Bemutatja, hogyan csatlakoztathat adatlemezt az Azure Portalon][virtual-machines-linux-attach-disk-portal]
>
> Lemezek vannak csatlakoztatva, szüksége jelentkezzen be a virtuális géphez a Windows logikailemez-kezelő megnyitásához. Ha automount nincs engedélyezve a fejezet javasolt módon [beállítás automount a csatlakoztatott lemezeket][planning-guide-5.5.3], az újonnan csatolt kötetet kell online állapotban és inicializálva kell venni.
>
> ![Linux][Logo_Linux] Linux
>
> Lemezek vannak csatlakoztatva, jelentkezzen be a virtuális gép és a lemezek inicializálása leírtak szerint kell [Ez a cikk][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Üres lemez az új lemez esetén szüksége, valamint a lemez formázásához. A formázást, különösen az adatbázis-kezelő adathoz és naplófájlhoz az azonos ajánlásokat, mint az operációs rendszer nélküli központi telepítését az adatbázis-kezelő vonatkoznak.

Említettek szerint a fejezet [a Microsoft Azure virtuális gép fogalom][planning-guide-3.2], egy Azure Storage-fiók nem biztosít az i/o kötet, IOPS és adatmennyiség végtelen erőforrások. Általában az adatbázis-kezelő virtuális gépek legtöbb ez érinti. Érdemes lehet egy külön Tárfiók használata az egyes virtuális Gépekhez, ha néhány nagy i/o kötet virtuális gépek üzembe helyezése annak érdekében, hogy az Azure Storage-fiók mennyiségi korlátot belül ajánlott. Ellenkező esetben szeretne látni, hogyan tudja osztani a ezek a virtuális gépek anélkül, hogy szerezze meg a korlátot, minden egyes egyetlen tárfiók különböző Storage-fiókok között. További részleteket ismertetik a [DBMS üzembe helyezési útmutató][dbms-guide]. Ezek a korlátozások is kell tartania, tiszta SAP alkalmazás serveres virtuális gépekre vagy más virtuális gépeket, amelyek végül szükség lehet további virtuális merevlemezeket figyelembe. Ezek a korlátozások nem vonatkoznak a felügyelt lemezek használatakor. Ha azt tervezi, a Premium Storage használatához, felügyelt lemezek használatát javasoljuk.

Egy másik témakör, amely kapcsolódik a Storage-fiókok, hogy a virtuális merevlemezeket, a Storage-fiókban vannak első georeplikált. A georeplikáció engedélyezve van-e a Tárfiók szintjén nem pedig a virtuális gép szintet. Ha engedélyezve van a georeplikáció, a virtuális merevlemezek a Tárfiókban található lenne replikál egy másik Azure-adatközpontban ugyanazon a régión belül. Mielőtt eldönti, ez, gondolja a következő korlátozás:

Georeplikáció az Azure virtuális gép egyes VHD-n helyben működik, és nem replikálja az IOS-es időrendi sorrendben egy virtuális gépen több VHD-k között. Ezért a virtuális Merevlemezt, amely a kiindulási virtuális gépet, valamint a virtuális Géphez csatolt további VHD-k egymástól független lesznek replikálva. Ez azt jelenti, hogy nincs szinkronizálás között a különböző virtuális merevlemezek változásait. Az a tény, hogy az IOS-es replikálva vannak-e a sorrendet, amelyben azt jelenti, hogy megírásának függetlenül, hogy a földrajzi replikáció nem érték az adatbázis-kiszolgálókon, amelyek csak egy virtuális merevlemezt elosztva adatbázisaikat áll. Az adatbázis-kezelő mellett is lehet más alkalmazásokat, ahol folyamatok írási adatok módosítására vagy másik VHD-k, és ezért fontos, hogy a változások sorrendje. Ha ez a követelmény, georeplikáció az Azure-ban nem szabad engedélyezni. Függ, hogy van szüksége, vagy szeretné a georeplikáció, virtuális gépek, de nem egy másik, már rendezheti az virtuális gépek és azok kapcsolódó virtuális merevlemezek, különböző Storage-fiókok, amelyek rendelkeznek a georeplikáció engedélyezve van, vagy le van tiltva.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>A csatlakoztatott lemezek automount beállítása
- - -
> ![Windows][Logo_Windows] Windows
>
> A virtuális gépek esetében a saját rendszerképek vagy lemezek jönnek létre, amely szükség, és lehetőség szerint állítsa be a automount paramétert. A paraméter lehetővé teszi a virtuális gép újraindítása vagy újbóli üzembe helyezés az Azure-ban a csatolt és csatlakoztatott meghajtók automatikusan újra csatlakoztatni után.
> A paraméter az Azure piactér a Microsoft által biztosított rendszerképek van beállítva.
>
> Annak érdekében, hogy a automount beállítása, a dokumentációban, itt a parancssori végrehajtható diskpart.exe:
>
> * [A DiskPart parancssori kapcsolók](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](http://technet.microsoft.com/library/cc753703.aspx)
>
> A Windows-parancssori ablakot rendszergazdaként kell megnyitni.
>
> Lemezek vannak csatlakoztatva, szüksége jelentkezzen be a virtuális géphez a Windows logikailemez-kezelő megnyitásához. Ha automount nincs engedélyezve a fejezet javasolt módon [beállítás automount a csatlakoztatott lemezeket][planning-guide-5.5.3], az újonnan csatlakoztatott kötet > online állapotban és inicializálva állapotba kell helyezni.
>
> ![Linux][Logo_Linux] Linux
>
> Egy újonnan csatolt üres lemez inicializálása leírtak szerint kell [Ez a cikk][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Meg kell új lemezeket ad hozzá a /etc/fstab is.
>
>

- - -
### <a name="final-deployment"></a>Utolsó telepítési
Lásd a végleges üzembe helyezés és a pontos lépések, különösen a központi telepítés az SAP kiterjesztett figyelést, érdemes a [üzembe helyezési útmutató][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Azure virtuális gépeken belül futó SAP-rendszereinket elérése
A csak felhőalapú esetben érdemes rendszerhez való csatlakozáshoz ezeket az SAP az SAP grafikus felhasználói felülettel nyilvános interneten keresztül. Ezekben az esetekben a következő eljárásokat kell alkalmazni.

A dokumentum későbbi szakaszában ismertetjük az egyéb fő forgatókönyv, létesítmények közötti telepítések esetén, amelyek rendelkeznek a helyek közötti kapcsolat (VPN-alagút) vagy Azure ExpressRoute-kapcsolat a helyszíni és Azure rendszerek közötti SAP-rendszerekhez való kapcsolódáshoz.

### <a name="remote-access-to-sap-systems"></a>Távoli elérés az SAP-rendszerek
Az Azure Resource Manager végpontleképzőben nincs alapértelmezett többé például a korábbi klasszikus modellben. Az Azure Resource Manager virtuális gép összes portok nyitva, amennyiben:

1. Az alhálózatra vagy a hálózati adapterhez nincs hálózati biztonsági csoport van definiálva. Azure-beli virtuális hálózati forgalom úgynevezett "hálózati biztonsági csoportok" keresztül védve legyenek. További információkért lásd: [Mi az a hálózati biztonsági csoport (NSG)?][virtual-networks-nsg]
2. Nem Azure Load Balancer a hálózati adapter van definiálva.   

Tekintse meg a klasszikus modellen és ARM architektúra különbség leírtak szerint [Ez a cikk][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>A csak felhőalapú forgatókönyvhöz az SAP-rendszerhez, és az SAP grafikus felhasználói Felülettel kapcsolat konfigurációja
Tekintse meg ebben a cikkben a részleteit, ez a témakör ismerteti: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Virtuális gépen a tűzfal beállításainak módosítása
A tűzfal konfigurálása a virtuális gépek, az SAP-rendszer a bejövő forgalom engedélyezésére szükség lehet.

- - -
> ![Windows][Logo_Windows] Windows
>
> Az Azure üzembe helyezett virtuális Gépen belül a Windows tűzfal alapértelmezés szerint be van kapcsolva. Most már engedélyeznie kell a SAP-portot kell megnyitni, ellenkező esetben az SAP grafikus felhasználói Felülettel nem lesz képes csatlakozni.
> Ehhez tegye a következőket:
>
> * Nyissa meg a vezérlőpult\rendszer és biztonság\windows tűzfal **speciális beállítások**.
> * Most kattintson a jobb gombbal a bejövő szabályok és a választott **új szabály**.
> * A következő varázslóban úgy döntött, hozzon létre egy új **Port** szabály.
> * A varázsló a következő lépésben hagyja a TCP, és írja be a beállítást a megnyitni kívánt port számát. Mivel az SAP-példány azonosítója 00, hogy 3200 vett igénybe. Ha a példány másik példányt, a korábban a példányok száma alapján meghatározott portot kell megnyitni.
> * A varázsló következő részében, az elem kell **engedélyezése kapcsolat** be van jelölve.
> * A varázsló a következő lépésben adja meg, hogy a szabály vonatkozzon-e tartományi, privát és nyilvános hálózathoz kell. Módosítsa, ha szükséges, az igényeinek megfelelően. Azonban a nyilvános hálózaton kívülről kapcsolódik az SAP grafikus felhasználói Felülettel rendelkező, szüksége lesz a alkalmazni a nyilvános hálózathoz szabályt.
> * A varázsló a legutóbbi lépésben, adjon nevet a szabálynak, és billentyűkombináció lenyomásával mentse **Befejezés**.
>
> A szabály azonnal érvénybe lép.
>
> ![Port definice pravidla][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> A Linux-rendszerképeket az Azure Marketplace-en nem engedélyezi a engedélyezze az iptables tűzfal alapértelmezés szerint, és az SAP-rendszerhez kapcsolódni működnie kell. Ha engedélyezte, hogy engedélyezze az iptables vagy más tűzfal, tekintse meg a dokumentációt, engedélyezze az iptables vagy a használt tűzfalán, hogy a bejövő tcp-forgalmat a port 32xx (ahol xx az SAP-rendszerünk a rendszer száma).
>
>

- - -
#### <a name="security-recommendations"></a>Biztonsági javaslatok
Az SAP grafikus felhasználói Felülettel nem csatlakozik közvetlenül a SAP példányra (port 32xx), amely futnak, de először létesít kapcsolatot létesíteni az SAP üzenet kiszolgálói folyamat (port 36xx) porton keresztül. Múltbeli időpont ugyanazt a portot használta a kiszolgáló a belső kommunikáció az alkalmazás-példányokhoz. Megakadályozni a helyszíni alkalmazáskiszolgálók véletlenül a az Azure-ban, egy üzenet kiszolgálóval való kommunikációhoz, a belső kommunikációs portok is módosítható. Ajánlott a belső kommunikáció az SAP üzenetkiszolgáló és az alkalmazáspéldányok közötti váltson egy másik portszámot, amely a helyszíni rendszerekből, például a fejlesztést, így a projekt tesztelési stb klónja lett klónja rendszereken. Ez az alapértelmezett profil paraméterrel teheti meg:

> rdisp/msserv_internal
>
>

dokumentált módon [biztonsági beállításait az SAP Üzenetkiszolgáló ](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Az SAP-példányok csak felhőalapú üzembe helyezés fogalmak
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>SAP netweaver bemutató/képzési forgatókönyv egyetlen virtuális gép
![Az Azure Cloud Servicesben egyetlen SAP virtuális gép bemutató rendszert futtató virtuális gép ugyanazzal a névvel, elszigetelt][planning-guide-figure-1700]

Ebben a forgatókönyvben (című [csak felhőalapú] [ planning-guide-2.1] ebben a dokumentumban) azt fontolgatja, hogy hol található a teljes képzési/bemutató forgatókönyvet tipikus képzési/bemutató rendszer forgatókönyv egyetlen virtuális gépen. Feltételezzük, hogy az üzemelő példány Virtuálisgép-lemezkép sablonok segítségével történik-e. Is feltételezzük, hogy ezek bemutató betanítások többszöröse ugyanazzal a névvel rendelkező virtuális gépek az üzembe helyezni a virtuális gépekhez szükség van.

Feltételezzük, létrehozott egy Virtuálisgép-lemezkép fejezet egyes szakaszokban leírtak szerint [virtuális gépek előkészítése az Azure SAP] [ planning-guide-5.2] ebben a dokumentumban.

A forgatókönyv megvalósításához a eseménysorozat így néz ki:

##### <a name="powershell"></a>PowerShell
* Hozzon létre egy új erőforráscsoportot, minden képzési/bemutató fekvő

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Hozzon létre egy új tárfiókot, ha nem szeretné felügyelt lemezek használata

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Hozzon létre egy új virtuális hálózat minden képzési/bemutató fekvő ahhoz, hogy az azonos állomásnévvel és IP-címek használatát. A virtuális hálózat, amely csak engedélyezi a forgalmat a 3389-es, a távoli asztali hozzáférés engedélyezéséhez és a 22-es port az SSH számára a hálózati biztonsági csoport védi.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Hozzon létre egy új nyilvános IP-címet, amely hozzáfér a virtuális géphez az internetről segítségével

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Hozzon létre egy új hálózati adaptert a virtuális gép

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Virtuális gépet hoz létre. A csak felhőalapú a forgatókönyvben minden virtuális gép rendelkezik ugyanazzal a névvel. Az SAP SID ezeken a virtuális gépeken SAP NetWeaver-példánya azonos lesz is. Az Azure-erőforráscsoporton belül egyedinek kell lennie kell a virtuális gép nevét, de különböző Azure-erőforráscsoportok, virtuális gépeket futtatni képes ugyanazzal a névvel. A Windows vagy Linux rendszeren a "root" alapértelmezett "Administrator" fiók nem érvényesek. Ezért egy új rendszergazdai felhasználónevet kell definiálni és jelszóval. A virtuális gép méretét is kell definiálni.

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

* Igény szerint adjon hozzá további lemezeket, és állítsa vissza a szükséges tartalom. Minden blob nevének (URL-címek használatával a blobok) Azure-on belül egyedinek kell lennie.

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
Az alábbi példakód Linux rendszeren használható. A Windows, a PowerShell használata a fent leírt vagy alkalmazkodnak a példa a(z) % rgName % használhatja $rgName helyett, és a környezeti változót a Windows paranccsal *beállítása*.

* Hozzon létre egy új erőforráscsoportot, minden képzési/bemutató fekvő

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Új tárfiók létrehozása

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Hozzon létre egy új virtuális hálózat minden képzési/bemutató fekvő ahhoz, hogy az azonos állomásnévvel és IP-címek használatát. A virtuális hálózat, amely csak engedélyezi a forgalmat a 3389-es, a távoli asztali hozzáférés engedélyezéséhez és a 22-es port az SSH számára a hálózati biztonsági csoport védi.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Hozzon létre egy új nyilvános IP-címet, amely hozzáfér a virtuális géphez az internetről segítségével

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Hozzon létre egy új hálózati adaptert a virtuális gép

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Virtuális gépet hoz létre. A csak felhőalapú a forgatókönyvben minden virtuális gép rendelkezik ugyanazzal a névvel. Az SAP SID ezeken a virtuális gépeken SAP NetWeaver-példánya azonos lesz is. Az Azure-erőforráscsoporton belül egyedinek kell lennie kell a virtuális gép nevét, de különböző Azure-erőforráscsoportok, virtuális gépeket futtatni képes ugyanazzal a névvel. A Windows vagy Linux rendszeren a "root" alapértelmezett "Administrator" fiók nem érvényesek. Ezért egy új rendszergazdai felhasználónevet kell definiálni és jelszóval. A virtuális gép méretét is kell definiálni.

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

* Igény szerint adjon hozzá további lemezeket, és állítsa vissza a szükséges tartalom. Minden blob nevének (URL-címek használatával a blobok) Azure-on belül egyedinek kell lennie.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Sablon
A mintasablonokat használhatja az azure-gyorssablonok tárházban a githubon.

* [Egyszerű Linux rendszerű virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Egyszerű Windows virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuálisgép-rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Virtuális gépek Azure-ban kommunikáló megvalósítása
A csak felhőalapú forgatókönyv a képzés és bemutató szokás célra használja a helyét a szoftver a bemutató képzés jelölő forgatókönyv több virtuális gép oszlik. A különböző összetevőket, a különböző virtuális gépek telepítve kell egymással kommunikálni. Újra ebben a forgatókönyvben nem a helyszíni hálózati kommunikáció vagy létesítmények közötti forgatókönyv van szükség.

Ebben a forgatókönyvben az kiterjesztése, a telepítés fejezetben leírt [SAP netweaver bemutató/képzési forgatókönyv egyetlen virtuális gép] [ planning-guide-7.1] ebben a dokumentumban. Ebben az esetben a virtuális gépek fog lehet hozzáadni egy meglévő erőforráscsoportot. A következő példában egy SAP ASCS/SCS virtuális gép, egy adatbázis-kezelő és a egy SAP-alkalmazáskiszolgáló-példányok virtuális gép futó virtuális gép a betanítási fekvő áll.

Ebben a forgatókönyvben hoz létre, mielőtt kell alapbeállítások szerint már végeztek a forgatókönyvben előtt gondolja.

#### <a name="resource-group-and-virtual-machine-naming"></a>Erőforráscsoport és a virtuális gép elnevezése
Minden erőforráscsoport-nevekkel egyedinek kell lennie. A saját elnevezési sémája az erőforrásokat, például a fejlesztés `<rg-name`>-utótagot.

A virtuális gép neve nem lehet egyedi az erőforráscsoporton belül.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>A különböző virtuális gépek közötti kommunikáció a hálózat beállítása
![Egy Azure virtuális hálózaton belüli virtuális gépek][planning-guide-figure-1900]

Hogy elnevezési ütközések a klónok, az azonos képzési/bemutató környezetünk, hozzon létre egy Azure virtuális hálózat minden fekvő kell. Az Azure által megadott DNS-névfeloldás, vagy beállíthatja a saját DNS-kiszolgáló (hogy nem kell további ismertetünk) Azure-on kívülről. Ebben a forgatókönyvben azt ne konfigurálja a saját DNS. Egy Azure virtuális hálózaton belül minden virtuális gép állomásnevek kommunikációra engedélyezve lesz.

A virtuális hálózatok, és nem csupán az erőforráscsoportok képzési vagy bemutató környezetünk külön okok miatt lehet:

* Az SAP-rendszeren, állítsa be a saját AD OpenLDAP kell, és a tartomány-kiszolgálónak kell lennie a környezetünk mindegyike része.  
* Az SAP-rendszeren, állítsa be a helyösszetevőket fix IP-címét kell használnia.

További információt az Azure Virtual Networkökhöz és hogyan adhat meg hozzájuk található [Ez a cikk][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>SAP virtuális gépek üzembe helyezéséhez a vállalati hálózati kapcsolattal (létesítmények közötti)
Egy olyan SAP-rendszeren futtatja, és szeretné osztani az operációs rendszer nélküli csúcskategóriás DBMS-kiszolgálók, a helyszíni virtualizált környezetekben az alkalmazásrétegek közötti üzembe helyezés, és kisebb 2 szintű SAP-rendszerek, valamint Azure IaaS vannak beállítva. A kiinduló feltételezése, hogy egy SAP-rendszeren belüli SAP-rendszerek kommunikáljanak egymással és számos egyéb szoftverösszetevők, a vállalaton belül, az üzembe helyezés űrlapja független üzembe kell. Van még ne legyenek különbségek bevezetett SAP grafikus felhasználói Felülettel vagy egyéb felületek csatlakoztatása a végfelhasználók számára a központi telepítési képernyőn. Ezek a feltételek csak akkor kell teljesíteni, ha a helyszíni Active Directory/OpenLDAP el, DNS-szolgáltatások az Azure webhely-az-hely vagy több-site kapcsolat vagy a privát kapcsolatok, például az Azure ExpressRoute-rendszerekhez terjeszteni.

Annak érdekében, hogy további háttér lekérése az Azure-beli SAP implementálásának részletei, javasoljuk, hogy olvassa el a fejezet [fogalmak, Cloud-Only telepítési SAP-példányok] [ planning-guide-7] ebben a dokumentumban ismerteti azokat az az alapvető beállítások szerkezeteket, az Azure-ban, és hogyan ezeket kell használni az SAP-alkalmazások az Azure-ban.

### <a name="scenario-of-an-sap-landscape"></a>Az SAP-rendszeren, a forgatókönyv
A létesítmények közötti forgatókönyv nagyjából leírható például az alábbi ábrák:

![Hely – hely kapcsolat a helyszíni és Azure-objektumok között][planning-guide-figure-2100]

A fenti forgatókönyvben egy forgatókönyvet, a helyszíni AD/OpenLDAP és DNS bővítve lettek az Azure-bA. A helyszíni oldalon a Azure-előfizetésenként egy adott IP-címtartomány van fenntartva. Az IP-címtartományt rendel egy Azure virtuális hálózat az Azure részéről.

#### <a name="security-considerations"></a>Biztonsági szempontok
A minimális követelmény, a biztonságos kommunikációs protokollok, mint például az SSL/TLS használatát a böngésző-hozzáférés vagy VPN-alapú kapcsolatot az Azure-szolgáltatások rendszer-hozzáférést. Feltételezzük, hogy vállalatok másképp kezeli-e a VPN-kapcsolat a vállalati hálózat és az Azure között. Egyes vállalatok blankly előfordulhat, hogy minden a portok megnyitásához. Néhány más vállalatok érdemes lehet pontos milyen portokat kell megnyitni, stb.

A tipikus SAP az alábbi táblázatban a kommunikációs portok vannak felsorolva. Alapvetően azt is használhatók a SAP-átjáró port megnyitásához.

| Szolgáltatás | Port neve | Példa `<nn`> = 01 | Alapértelmezett tartomány (min-max) | Megjegyzés |
| --- | --- | --- | --- | --- |
| Diszpécser |sapdp`<nn>` lásd: * |3201 |3200 - 3299 |SAP Dispatcher, az SAP grafikus felhasználói felület a Windows- és Java által használt |
| Üzenetkiszolgáló |sapms`<sid`> lásd: ** |3600 |ingyenes sapms`<anySID`> |biztonsági azonosító SAP-rendszer-ID = |
| Átjáró |sapgw`<nn`> lásd: * |3301 |ingyenes |SAP-átjáróhoz, CPIC és RFC-kommunikációhoz használt |
| Az SAP-útválasztó |sapdp99 |3299 |ingyenes |Csak CI (központi példány) szolgáltatásnevek máshoz lehessen rendelni ezeket /etc/services egy tetszőleges értéket a telepítés után. |

*) Neurális hálózat = SAP-példányok száma

*) sid SAP-rendszer-ID =

További részletes információ a különböző SAP-termékekhez szükséges portokat vagy a szolgáltatások SAP-termékek szerint itt található <http://scn.sap.com/docs/DOC-17124>.
Ebben a dokumentumban a dedikált portok megnyithatja az szükséges, adott SAP-termékekhez és-forgatókönyvek a VPN-eszköznek kell lennie.

Egyéb biztonsági intézkedések, ha ilyen esetben a virtuális gépek üzembe helyezéséhez lehet létrehozni egy [hálózati biztonsági csoport] [ virtual-networks-nsg] hozzáférési szabályok meghatározásához.

### <a name="dealing-with-different-virtual-machine-series"></a>Különböző virtuálisgép-sorozat többé vesződnie a sérült
A Microsoft számos további virtuális gépek típusai, amelyet eltérő számú Vcpu, memória vagy hozzáadva, vagy több fontos hardveren futó. Nem minden virtuális gépeken támogatottak az SAP (lásd a támogatott virtuális gépek típusai az SAP-Jegyzetnek [1928533]). Másik gazdagépet hardvergenerációk egyes virtuális gépeken futtathatja. A gazdagép hardvergenerációk a részletesség, egy Azure-Bővítőegysége első üzemelnek. Esetek fordulhatnak elő, választott a virtuális gépek különböző típusairól a azonos skálázási egység nem lehet futtatni. Rendelkezésre állási csoport csak korlátozott lehetővé teszi több skálázási egység különböző hardver alapján.  Például futtatja az SAP DBMS réteg-E64s_v3 együtt a virtuális gép egy rendelkezésre állási csoportban lévő virtuális gépen a másodlagos adatbázis-kezelő-példányt futtató egy magas rendelkezésre ÁLLÁSÚ konfigurációban, ha nem egyszerűen leállítása és újraindítása a másodlagos virtuális gép M sorozatú virtuális Gépet, mert előfordulhat, hogy szeretné upg a virtuális gép rade. Oka, hogy M-sorozat virtuális gépei és Ev3-sorozat virtuális gépei futnak másik hardverre, és az adott különböző skálázási egységben. Hozzon létre egy új rendelkezésre állási csoport, törölje a másodlagos Ev3-sorozat virtuális Gépet, a tároló törlése nélkül, és újbóli üzembe helyezése a virtuális gép M-sorozat virtuális gépként be az új rendelkezésre állási csoportot kell.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Az Azure-beli SAP-példány a helyi hálózati nyomtatót a nyomtatás
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Nyomtatás TCP/IP felett a létesítmények közötti forgatókönyv
A helyszíni TCP/IP-alapú hálózati nyomtatók egy Azure-beli virtuális gépen, beállítania a teljes ugyanaz, mint a vállalati hálózathoz, feltételezve, hogy a VPN-helyek alagúton vagy ExpressRoute-kapcsolat létrejött.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ehhez tegye a következőket:
>
> * Egyes hálózati nyomtatók kapható a konfigurációs varázsló, ami lehetővé teszi az egyszerűen beállítható a nyomtató-Azure-beli virtuális gépen. Ha a varázsló szoftvert még nem volt terjesztve a nyomtatóval, a manuális állítsa be a nyomtató módja hozzon létre egy új TCP/IP nyomtatóportot.
> * Nyissa meg a Vezérlőpult -> eszközök és nyomtatók > Nyomtató hozzáadása
> * A Hozzáadás gombra a számítógéphez egy TCP/IP-címét vagy állomásnevét használata
> * Írja be a nyomtató IP-címe
> * Standard 9100-as nyomtatóportot
> * Szükség esetén telepítse manuálisan a megfelelő illesztőprogramot.
>
> ![Linux][Logo_Linux] Linux
>
> * Windows csak azon, mint a szokásos eljárás hálózati nyomtató telepítésére
> * csak kövesse a nyilvános Linux útmutatók [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) vagy [Red Hat és Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) nyomtató hozzáadásával.
>
>

- - -
![Hálózati nyomtatás][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Gazdagép-alapú nyomtató (megosztott nyomtató) SMB-n keresztül a létesítmények közötti forgatókönyv
Gazdagép-alapú nyomtatók nem kompatibilisek hálózati-tervező által. De a gazdagép-alapú nyomtató a hálózaton lévő számítógépek megosztható, mindaddig, amíg a nyomtató bekapcsolt számítógéphez csatlakozik. Csatlakozás a vállalati hálózati helyek közötti vagy az ExpressRoute és a helyi nyomtató megosztása. Az SMB protokoll helyett DNS NetBIOS név szolgáltatásként használja. Lehet, hogy a DNS-állomásnév eltér a gazdagép NetBIOS-nevét. A szokványos esettel, hogy a gazdagép NetBIOS-nevét és a DNS-állomásnév azonosak. A DNS-tartomány nem értelmezhető a NetBIOS-neve területen. Ennek megfelelően a DNS-állomásnevet és a DNS-tartomány teljesen minősített DNS-állomásnév nem használható a NetBIOS-neve területen.

A nyomtató megosztási azonosít egy egyedi nevet a hálózat:

* Az SMB-állomás (mindig szükség) állomásneve.
* (Minden esetben szükséges) a megosztás neve.
* A tartomány nevét, ha a nyomtató megosztása nem szerepel az SAP-rendszer azonos tartományban.
* Ezenkívül a felhasználónév és jelszó fiókdíjat a nyomtató-megosztás eléréséhez.

Útmutató:

- - -
> ![Windows][Logo_Windows] Windows
>
> Helyi nyomtató megosztása.
> Az Azure-beli virtuális gépen nyissa meg a Windows Explorert és írja be a megosztás neve, a nyomtató.
> A nyomtató telepítése varázsló végigvezeti Önt a telepítési folyamat.
>
> ![Linux][Logo_Linux] Linux
>
> Az alábbiakban néhány példa a hálózati nyomtatók konfigurálása a Linux vagy egy fejezet, mint például a dokumentációval kapcsolatos nyomtatás Linux rendszeren. Működik ugyanúgy, mint az Azure Linux virtuális gép mindaddig, amíg a virtuális gép része egy VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL- vagy Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>USB-nyomtató (nyomtató továbbítás)
Az Azure-ban a távoli asztali szolgáltatások azon képessége, hogy a távoli kapcsolat a helyi nyomtató eszközeiket a hozzáférést biztosíthat a felhasználók nem áll rendelkezésre.

- - -
> ![Windows][Logo_Windows] Windows
>
> További részletek a Windows-nyomtatás itt található: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integráció az SAP javítása és átviteli System (TMS) a létesítmények közötti Azure rendszerek
Az SAP változás- és átviteli System (TMS) kell állítani, exportálásáról és importálásáról az átviteli kérelem rendszerekből a környezet változásaihoz. Feltételezzük, hogy az SAP-rendszerek (fejlesztői) fejlesztési példányai találhatók az Azure-ban, mivel a minőségbiztosítás (Tesztelés) és a hatékony rendszerek (PRD) helyszíni. Emellett feltételezzük, hogy nincs-e a központi átviteli könyvtár.

##### <a name="configuring-the-transport-domain"></a>A szállítási tartomány konfigurálása
Az átviteli tartomány konfigurálása a rendszer az átviteli tartományvezérlő leírtak szerint jelölt [a szállítási tartományvezérlő beállítása](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Egy rendszer felhasználói TMSADM jön létre, és a szükséges RFC cél jön létre. Ha bejelöli a tranzakció SM59 segítségével RFC kapcsolatok. Állomásnév feloldása engedélyezni kell a a szállítás tartományon keresztül.

Útmutató:

* Ebben az esetben döntöttünk, a helyszíni QAS rendszer CTS tartományvezérlőre. Tranzakció STM hívjuk. A TMS párbeszédpanel jelenik meg. Egy átviteli tartomány konfigurálása párbeszédpanel jelenik meg. (Ez a párbeszédpanel csak akkor jelenik meg, ha még nem konfigurálta az átviteli tartomány.)
* Győződjön meg arról, hogy jogosult-e az automatikusan létrehozott felhasználó TMSADM (SM59 kapcsolat ABAP -> -> TMSADM@E61.DOMAIN_E61 -> Részletek -> Utilities(M) engedélyezési Test ->). A kezdeti képernyő tranzakció STM jelenítsen meg, hogy az SAP-rendszer már működik a tartományvezérlőnek a szállítási tartomány Itt látható módon:

![A tartományvezérlő STM tranzakció kezdeti képernyő][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Többek között az SAP-Rendszereinket átviteli a tartományban
A feladatütemezés, beleértve az SAP-rendszer átviteli tartományban a következőképpen néz ki:

* A fejlesztői rendszeren az Azure-ban nyissa meg az (000 ügyfél) rendszer, és a tranzakciós STM hívja. Válasszon más konfigurációt párbeszédpanelen, és folytassa a rendszer közé tartozik a tartományban. Adja meg a tartományvezérlő a cél gazdagépre ([SAP-Rendszereinket többek között a szállítási tartomány](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). A rendszer most már szerepelni a a szállítás tartomány vár.
* Biztonsági okokból majd akkor térjen vissza a tartományvezérlő ellenőrizze a kérelmet. Válassza ki a rendszer – áttekintés és jóváhagyás az várakozási rendszer. Erősítse meg a parancssort, a konfiguráció terjesztése.

Az SAP-rendszer már tartalmazza a szükséges információkat az összes többi SAP rendszer átviteli a tartományban. Egy időben a címet az új SAP-rendszer adatküldést az összes többi SAP rendszeren, és az SAP-rendszerhez is meg kell adni a szállítási profiljában az átviteli vezérlő programhoz. Ellenőrzi, hogy RFC-ket és a tartomány átviteli könyvtárának hozzáférés működni.

A rendszer a konfiguráció a szokásos módon a dokumentációjában leírtak szerint folytassa [változás- és rendszer](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Útmutató:

* Ellenőrizze, hogy a helyszíni STM megfelelően van konfigurálva.
* Ellenőrizze, hogy a virtuális gép az Azure-ban, és fordítva visa megoldhatók a szállítási tartományvezérlő állomásnevét.
* Hívás STM tranzakció -> Other konfiguráció -> rendszer közé tartozik a tartományban.
* Erősítse meg a kapcsolat a helyszíni TMS a rendszer.
* Állítsa be a szokásos módon átviteli útvonalakat, csoportok és rétegek.

A site-to-site csatlakoztatott létesítmények közötti forgatókönyvek, a helyszíni és Azure közötti késés továbbra is lehet jelentős mértékben használják. Ha a feladatütemezési objektum fejlesztési és tesztelési rendszerek az éles környezetben keresztül szállítása követjük, vagy gondoljunk átvitelek vagy támogatási csomagok alkalmaz a különböző rendszerek, akkor vegye figyelembe, hogy, függő helye határozza meg a központi adatátviteli könyvtár nagy a késés, adatok központi átviteli a címtárban írása és olvasása a rendszerek egyes fog történni. A helyzet hasonlít SAP-rendszeren, konfigurációk, ahol a különféle rendszereken futó különböző adatközpontokban és az adatközpontok közötti lényeges távolság keresztül.

Annak érdekében, hogy kerülő ilyen késés és a rendszer gyors írása és olvasása, vagy az átviteli könyvtárból dolgozhat, beállíthat két STM átviteli tartomány (egy a helyszíni és a egy, az Azure-ban a rendszerek és az átviteli tartományok hivatkozás. Ellenőrizze a dokumentációját, amely ismerteti a fogalom a az SAP TMS mögött elvek: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Útmutató:

* Minden egyes helye (helyszíni és Azure) átviteli tartomány beállítása tranzakcióval STM <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Hivatkozás a tartományok tartomány hivatkozást, és ellenőrizze a kapcsolatot a két tartomány között.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* A csatolt rendszer a konfiguráció terjesztése.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC forgalom Azure-beli és helyszíni (létesítmények közötti) található SAP-példányok között
RFC-forgalmat a helyszíni rendszerek között, és az Azure-ban kell működjön. Beállítása egy kapcsolat webszolgáltatáshívás-tranzakció SM59 a forrásrendszerben ahol meg kell határoznia egy RFC-kapcsolat felé a célrendszeren. A konfiguráció hasonlít a szabványos telepítés RFC-kapcsolat.

Feltételezzük, hogy a létesítmények közötti a forgatókönyvben a virtuális gépek, amelyek ugyanabban a tartományban kell kommunikálniuk egymással futtassa SAP-rendszereinket. Ezért egy SAP-rendszerek közötti RFC-kapcsolat beállítása nem különbözik a beállítási lépéseket és a bemeneti adatokat az olyan telephelyi szituációk esetén.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Az SAP-példányok az Azure-ban, vagy fordítva található elérésére helyi fileshares
SAP-példányok az Azure-ban található kell a vállalati helyi belül fájlmegosztások eléréséhez. Emellett a helyszíni SAP-példányok kell elérni a fájlmegosztásokat, az Azure-ban található. Engedélyezi a fájlmegosztások, konfigurálnia kell az engedélyeket és a helyi rendszer megosztási beállítások. Ellenőrizze, hogy a portok Azure és az Adatközpont közötti VPN vagy ExpressRoute-kapcsolat megnyitásához.

## <a name="supportability"></a>Támogatási lehetőségek
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure Monitorozási megoldás az SAP
Ahhoz, hogy ki egy Azure Monitoring bővítményt az SAP-n keresztül az Azure Virtual machines szolgáltatás gazdagép-adatokat a figyelést az üzletmenet szempontjából kritikus fontosságú SAP-rendszereit az Azure-ban a figyelési eszközök SAPOSCOL vagy a gazdagép-ügynök az SAP az SAP kérhet. Mivel az igényeinek, az SAP az SAP-alkalmazások adott volt, a Microsoft úgy döntött, nem az általános megvalósítása az Azure-bA a szükséges funkciót, de hagyja, az ügyfelek számára a szükséges figyelési összetevők és konfigurációk üzembe helyezése a virtuális Az Azure-ban futó gépek. Azonban a figyelési összetevők a üzembe helyezési és életciklus-kezelés többnyire automatizált lesz az Azure-ban.

#### <a name="solution-design"></a>Megoldásterv
A megoldás célja az SAP-figyelés engedélyezése az Azure Virtuálisgép-ügynök és a bővítmény keretrendszer architektúrája alapján történik. Az Azure Virtuálisgép-ügynök és a bővítmény keretrendszer lényege, hogy a virtuális Gépen belül a Virtuálisgép-bővítmény az Azure katalógusban elérhető szoftverek alkalmazás(ok) telepítése. A működési elv a fogalom, hogy engedélyezi (ilyen esetekben az SAP az Azure Monitoring bővítmény), központi telepítését a virtuális gépre, speciális funkciók és ezeknek a szoftvereknek a konfiguráció üzembe helyezéskor.

Az "Azure Virtuálisgép-ügynök", amely lehetővé teszi a meghatározott Azure Virtuálisgép-bővítmények belül a virtuális gép kezelését elhelyezte Windows virtuális gépek alapértelmezés szerint a virtuális gép létrehozása az Azure Portalon. SUSE, a Red Hat vagy az Oracle Linuxot a Virtuálisgép-ügynök már a Azure Marketplace-ről lemezképben. Abban az esetben egy Linux virtuális gép a helyszínről az Azure-bA volna feltölteni a Virtuálisgép-ügynököt manuálisan telepítve van.

A figyelés megoldás az Azure-beli SAP keresi az alapvető alkotóelemeket ehhez hasonló:

![A Microsoft Azure-bővítmény-összetevők][planning-guide-figure-2400]

A blokk a fenti ábrán látható, az SAP-monitorozási megoldás egyik részét üzemel az Azure Virtuálisgép-rendszerképet és az Azure-bővítmény katalógus, amely a globálisan replikált műveletek az Azure által felügyelt. A feladata a közös SAP/MS munkacsoport új verziói az SAP az Azure Monitoring bővítmény közzétételéhez az Azure Operations dolgozhat az SAP az Azure végrehajtásának dolgozik.

Amikor telepít egy új Windows virtuális Gépet, a rendszer automatikusan hozzáadja az Azure Virtuálisgép-ügynököt a virtuális géppel. A függvény az ügynök koordinálása a be- és az SAP NetWeaver rendszerek figyelése az Azure-bővítmények konfigurációs. Linux rendszerű virtuális gépekhez az Azure Virtuálisgép-ügynök már része az Azure Marketplace-en operációsrendszer-képet.

Van azonban a lépést, amely továbbra is van szüksége az ügyfélnek kell végrehajtani. Ez az a lehetővé tétele és a teljesítménygyűjtés konfigurációját. A folyamat konfigurációjával kapcsolatos automatizált egy PowerShell-szkript vagy a CLI-parancsot. A PowerShell-parancsprogram letölthető a Microsoft Azure Script Centerben leírtak szerint a [üzembe helyezési útmutató][deployment-guide].

Az általános architektúrát, az SAP az Azure figyelési megoldás hasonlóan néz ki:

![Azure monitorozási megoldás az SAP NetWeaver számára][planning-guide-figure-2500]

**A pontos útmutató és részletes leírását az alábbi PowerShell-parancsmagokkal vagy a CLI-parancs segítségével központi telepítések során, kövesse az utasításokat, adott a [üzembe helyezési útmutató][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Az Azure integrációs SAProuter be SAP-példány található
Az Azure-ban futó SAP-példányok elérhetőnek kell lenniük a SAProuter is.

![Az SAP-útválasztó hálózati kapcsolat][planning-guide-figure-2600]

Egy SAProuter lehetővé teszi, hogy a TCP/IP kommunikációt, ha ez nem közvetlen IP részt vevő rendszerek között. Ez biztosítja azt az előnyt, a kommunikáció partnerek között nincs végpontok közötti kapcsolat nem szükséges a hálózati szinten. A SAProuter alapértelmezés szerint a 3299 port figyel.
SAP-példányok egy SAProuter-en keresztül csatlakozni szeretne az való csatlakozásra tett bármilyen kísérletet SAProuter karakterlánc és a gazdagépcsoport nevét meg kell adni.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Eddig a lépéseknek az ismertetése, a dokumentum az SAP NetWeaver általában vagy az SAP NetWeaver ABAP zásobníku. Kis ebben a szakaszban az SAP-Java-verem bizonyos szempontokat sorolja fel. A legfontosabb kizárólag alapú SAP NetWeaver Java-alkalmazások egyik, a SAP vállalati portálon. Egyéb SAP NetWeaver-alapú alkalmazások például az SAP-PI és az SAP-megoldás Manager használatához az SAP NetWeaver ABAP és Java-implementációk. Ezért természetesen szükség van, valamint az SAP NetWeaver Java stack kapcsolódó, meghatározott mérlegelnie.

### <a name="sap-enterprise-portal"></a>SAP vállalati portál
A telepítő egy SAP-portál egy Azure virtuális gép nem különböznek a helyi telepítés helyez üzembe a létesítmények közötti forgatókönyvek. A DNS-ben a helyszínen történik, mivel az egyes példányok portbeállítások konfigurált helyszíni teheti meg. A javaslatok és, amennyiben a jelen dokumentumban ismertetett korlátozások vonatkoznak az olyan alkalmazások, mint az SAP az Enterprise Portal vagy az SAP NetWeaver Java stack általában.

![Közzétett SAP-portál][planning-guide-figure-2700]

Egy speciális üzembe helyezési forgatókönyv szerint egyes ügyfelek, az SAP Enterprise Portal közvetlen elérését az internethez, viszont a virtuálisgép-gazdán site-to-site VPN-alagúton vagy ExpressRoute-n keresztül a vállalati hálózathoz csatlakozik. Ilyen esetben győződjön meg arról, hogy adott portok nyitva és nem blokkolja tűzfal vagy a hálózati biztonsági csoport rendelkezik. Az azonos mechanics kell alkalmazni, ha szeretne csatlakozni egy kizárólag felhőalapú forgatókönyvben helyszíni SAP-Java-példány.

A kezdeti portál URI-t nem HTTP (s):`<Portalserver`>: 5XX00/irj, a port képződik 50000 plusz (Systemnumber?? 100). Az alapértelmezett portál URI-t az SAP rendszer 00 `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. További részletekért tekintse meg rendelkezik <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Végpont-konfiguráció][planning-guide-figure-2800]

Ha azt szeretné, az URL-címet és/vagy a portok az SAP vállalati portál testreszabása, tekintse meg ezt a dokumentációt:

* [Portál URL-Címének módosítása](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alapértelmezett portszámok, Portal portszámok módosítása](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Magas rendelkezésre állású (HA) és a katasztrófa utáni helyreállítás (DR) az Azure virtuális gépeken futó SAP NetWeaver számára
### <a name="definition-of-terminologies"></a>Terminológiát meghatározása
Az előfizetési időszak **magas rendelkezésre ÁLLÁS** általában kapcsolódik, amely minimálisra csökkenti az informatikai fennakadások azáltal, hogy IT-szolgáltatások révén üzletmenet technológiák csoportja, redundáns, hibatűrő, vagy a feladatátvételi védett összetevők belül a **ugyanazon** adatközpontban. A mi esetünkben egy Azure-régión belül.

**A vészhelyreállítás (DR)** van is célzó informatikai szolgáltatások megszakítás minimalizálása és a helyreállítási de keresztben **különböző** adatközpontok, amely általában több száz kilométer azonnal található. Az ebben az esetben általában egy geopolitikai régión belül, vagy Ön által létrehozott Microsoft különböző Azure-régiók között.

### <a name="overview-of-high-availability"></a>A magas rendelkezésre állás – Áttekintés
Hogy elkülönítheti az SAP magas rendelkezésre állás az Azure-ban két részre szóló vita:

* **Azure-infrastruktúra magas rendelkezésre állású**, például magas rendelkezésre ÁLLÁS (VM) számítási, hálózati, tárolási stb. és a SAP rendelkezésre állásának növelése az általa nyújtott előnyöket.
* **Az SAP alkalmazások magas rendelkezésre állású**, például a magas rendelkezésre ÁLLÁS az SAP szoftverösszetevőket:
  * SAP-alkalmazáskiszolgálókhoz
  * Az SAP ASCS/SCS példányhoz
  * Adatbázis-kiszolgálót

és hogyan, kombinálható az Azure-infrastruktúra magas rendelkezésre ÁLLÁS.

Az SAP az Azure-ban magas rendelkezésre állású SAP magas rendelkezésre állású helyszíni fizikai vagy virtuális környezetben képest néhány eltérés van. Az alábbi SAP-tanulmány azt ismerteti, standard SAP magas rendelkezésre állású konfigurációkhoz a Windows virtualizált környezetekben: <http://scn.sap.com/docs/DOC-44415>. Nem tartozik sapinst integrált SAP-magas rendelkezésre ÁLLÁS konfiguráció Linux, Windows létezik. Kapcsolatos SAP magas rendelkezésre ÁLLÁS a helyszíni linuxos további információkat itt találja: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra magas rendelkezésre állás
Jelenleg egy egyetlen virtuális garantált 99,9 %. Hogyan egyetlen virtuális gép rendelkezésre állását a következőhöz hasonló lehet képet kapjon a termék különböző elérhető az Azure SLA-k is létrehozható: <https://azure.microsoft.com/support/legal/sla/>.

A számítás alapját pedig 30 nap havonta vagy 43200 perc. Ezért 21,6 perc állásidő 0,05 % felel meg. A szokásos módon a különböző szolgáltatások rendelkezésre állásának szorzása lesz a következő módon:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) 

Például:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 vagy 99.75 %-os általános rendelkezésre állási.

#### <a name="virtual-machine-vm-high-availability"></a>Virtuális gép (VM) magas rendelkezésre állás
Az Azure-platform események, amelyek hatással lehetnek a virtuális gépek rendelkezésre állásának két típusa van: tervezett karbantartás és a nem tervezett karbantartás.

* A tervezett karbantartási események olyan végzett időszakos frissítések a Microsoft által a mögöttes Azure platform általános megbízhatóságának, teljesítményének és a virtuális gépeket futtató platforminfrastruktúra biztonsági javítása.
* Nem tervezett karbantartási események fordulhat elő, ha a hardver- vagy a virtuális gép mögöttes fizikai infrastruktúrája valamiképp valamilyen módon. Ez lehet helyi hálózati hiba, a helyi lemezek meghibásodása, vagy egyéb állványszintű meghibásodások. Ha ilyen hibát észlel, az Azure platform automatikusan át a virtuális gép a nem megfelelő állapotú, egy megfelelő állapotú fizikai kiszolgálóra a virtuális gépet üzemeltető fizikai kiszolgáló. Ilyen esetek ritkán lépnek fel, de ezek is okozhatják a virtuális gép újraindítását.

További részleteket ebben a dokumentációban található: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Az Azure-tárhely-redundancia
A Microsoft Azure Storage-fiókban az adatok mindig replikálódik a tartósság és magas rendelkezésre állás érdekében az Azure Storage SLA átmeneti hardverhibák esetén is teljesíti.

Mivel az Azure Storage az adatok három rendszerkép viselkedéssel alapértelmezés szerint, RAID5 vagy az Azure több lemezre kiterjedő RAID1 nem szükségesek.

További részleteket ebben a cikkben található: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Magas rendelkezésre állás az SAP-alkalmazások eléréséhez kezelésénél Azure-infrastruktúra virtuális gép újraindítása
Ha úgy dönt, hogy nem használja a funkciókat, mint a Windows Server feladatátvételi fürtszolgáltatási (WSFC) vagy támasztja Linux rendszeren (jelenleg csak a támogatott a SLES 12 rendszert vagy újabb), az Azure virtuális gép indítsa újra a védelmét egy SAP-rendszerrel szemben az Azure tervezett és nem tervezett leállások tételnél jelennek fizikai kiszolgáló infrastruktúra és a mögöttes Azure platform általános.

> [!NOTE]
> Fontos megemlíteni, hogy az Azure virtuális gép újraindítása elsősorban védi a virtuális gépek és alkalmazások nem. Indítsa újra a virtuális gép nem biztosít magas rendelkezésre állás az SAP-alkalmazások, de kínál a bizonyos szintű infrastruktúra elérhetőségét, és ezért közvetve magasabb rendelkezésre állás, az SAP-rendszereinket. A ideig tart egy gazdagép tervezett vagy nem tervezett leállás után indítsa újra a virtuális gép még nem nem biztosítunk szolgáltatói szerződést. Ezért ez a módszer a magas rendelkezésre állás nem alkalmas az SAP-rendszerhez, például (A) SCS vagy az adatbázis-kezelő kritikus összetevői.
>
>

A magas rendelkezésre állás érdekében egy másik fontos infrastruktúra elem a tárolási. Például az Azure Storage SLA 99,9 %-os elérhetőségét. Ha egy üzembe helyezi a lemezek minden virtuális gép egyetlen Azure Storage-fiókra, elérhetetlensége vannak elhelyezve, hogy az Azure Storage-fiókot minden virtuális gép, is minden SAP összetevőiről és ezeken a virtuális gépeken futtatott elérhetetlensége miatt lehetséges Azure Storage.  

Minden virtuális gép üzembe helyezése egy egyetlen Azure Storage-fiókot, helyett használhatja dedikált tárolási fiókok minden virtuális géphez, és így növelheti a virtuális gép és az SAP alkalmazások rendelkezésre állás több független az Azure Storage-fiók használatával.

Az Azure Managed Disks automatikusan kerülnek a tartalék tartomány a virtuális gép vannak csatolva. Ha a két virtuális gépet egy rendelkezésre állási beállítása és használhat felügyelt lemezeket, a platform gondoskodik a különböző tartalék tartományok, valamint azokat a Managed Disks terjesztése. Ha azt tervezi, a prémium szintű tárolást, erősen ajánlott lemezek kezelése, valamint használatával.

SAP NetWeaver a rendszer által használt Azure-infrastruktúra magas rendelkezésre ÁLLÁS és a storage-fiókok egy mintaarchitektúra nézhet ki:

![Magas rendelkezésre ÁLLÁSÚ SAP alkalmazások magas rendelkezésre állás eléréséhez Azure-infrastruktúra használatával][planning-guide-figure-2900]

Egy SAP NetWeaver a rendszer által használt Azure-infrastruktúra magas rendelkezésre ÁLLÁS és a Managed Disks – mintaarchitektúra nézhet ki:

![Magas rendelkezésre ÁLLÁSÚ SAP alkalmazások magas rendelkezésre állás eléréséhez Azure-infrastruktúra használatával][planning-guide-figure-2901]

A kritikus fontosságú SAP-összetevők mi érhető el a következő eddig:

* Magas rendelkezésre állás az SAP-alkalmazáskiszolgálókhoz (AS)

  SAP alkalmazáskiszolgáló-példányok olyan redundáns összetevők. Minden SAP, a példány van üzembe helyezve, a saját virtuális gépen, amely futtatja a egy másik Azure tartalék és frissítési tartomány (fejezeteiben talál [tartalék tartományok] [ planning-guide-3.2.1] és [frissítési tartományok] [ planning-guide-3.2.2]). Ez biztosított Azure rendelkezésre állási készletek használatával (lásd: fejezet [Azure rendelkezésre állási készletek][planning-guide-3.2.3]). Lehetséges tervezett vagy nem tervezett elérhetetlensége Azure tartalék és frissítési tartomány virtuális gépek, korlátozott számú elérhetetlensége okoz a saját SAP-AS példányok.

  Minden SAP, a példány kerül a saját Azure Storage-fiók – lehetséges elérhetetlensége egy Azure Storage-fiók csak egy virtuális gép elérhetetlensége okoz a saját SAP-AS példány. Ugyanakkor vegye figyelembe, hogy nincs-e az Azure Storage-fiókok legfeljebb egy Azure-előfizetésen belül. Annak érdekében, (A) SCS példányhoz automatikus elindítását követően a virtuális gép újraindítása, ellenőrizze, hogy az automatikus indítási paraméter beállítása (A) SCS példányhoz indítsa el a fejezetben leírt profil [SAP-példányok használatával Autostart][planning-guide-11.5].
  Olvassa el is fejezet [magas rendelkezésre állás az SAP-alkalmazáskiszolgálókhoz] [ planning-guide-11.4.1] további részletekért.

  Akkor is, ha a Managed Disks szolgáltatást, ezeket a lemezeket is vannak tárolva egy Azure Storage-fiókot és egy eseményt, a tároló leállása miatt nem érhető el.

* *Magasabb* SAP rendelkezésre állását (A) SCS példányhoz

  Itt azt használni, az Azure virtuális gép újraindítása a telepített SAP (A) SCS példányhoz a virtuális gép védelméhez. Az Azure tervezett vagy nem tervezett leállás esetén adatbázisai, virtuális gépeket egy másik elérhető kiszolgáló újraindul. Ahogy korábban említettük, az Azure virtuális gép újraindítása elsősorban védi a virtuális gépek és a nem alkalmazások, az ebben az esetben a (A) SCS példányhoz. A virtuális gép újraindításához keresztül közvetve magasabb rendelkezésre állás az SAP (A) SCS példányhoz lesz eljut. (A) SCS példányhoz automatikus elindítását biztosításához a virtuális gép újraindítása után, ügyeljen arra, hogy Autostart paraméter értéke (A) SCS példányhoz kezdő profilban fejezetben leírt [SAP-példányok használatával Autostart][planning-guide-11.5]. Ez azt jelenti, hogy a (A) SCS-példány egyetlen pont, hiba (SPOF) az egyetlen virtuális gép fut, a rendelkezésre állást nyújtva az egész SAP-rendszeren, a meghatározó tényező lesz.

* *Magasabb* adatbázis-kezelő kiszolgáló rendelkezésre állása

  Itt hasonlóan az SAP (A) SCS példányhoz használati eset, hogy igénybevételéhez Azure virtuális gép újraindítása védelme érdekében a virtuális Gépet a telepített adatbázis-kezelő szoftver, és azt magas rendelkezésre állás az adatbázis-kezelő szoftver segítségével indítsa újra a virtuális gép eléréséhez.
  Az adatbázis-kezelő futtatása egyetlen virtuális gép is egy SPOF, és a rendelkezésre állást nyújtva az egész SAP-rendszeren, a meghatározó tényező.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Az SAP alkalmazások magas rendelkezésre állás az Azure IaaS-on
Teljes SAP rendszer magas rendelkezésre állás, az összes kritikus fontosságú SAP rendszer összetevőit, például redundáns SAP-alkalmazáskiszolgálókhoz, és egyedi összetevők (például egyetlen pont, hiba), például SAP (A) SCS példányhoz, és az adatbázis-kezelő védeni kell.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Magas rendelkezésre állás az SAP-alkalmazáskiszolgálókhoz
A SAP alkalmazás kiszolgálók/párbeszédpanel példányok nem elengedhetetlen, gondolja át egy adott magas rendelkezésre állású megoldás. Magas rendelkezésre állás érhető el, a redundancia, és ezáltal fel őket elég a különböző virtuális gépek. Ezek az összes jelvényt ugyanabban az Azure rendelkezésre állási csoportban elkerülése érdekében, hogy a virtuális gépek tervezett karbantartás leállások során egy időben előfordulhat, hogy lehet frissíteni. Az alapszintű funkcióval rendelkezik, amely a különböző frissítési és tartalék tartományok az Azure skálázási egység épül már jelent meg a fejezet [frissítési tartományok][planning-guide-3.2.2]. Az Azure rendelkezésre állási csoportok számára a fejezet [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] ebben a dokumentumban.

Nincs végtelen számú tartalék és frissítési tartományok, amelyek segítségével az Azure rendelkezésre állási csoport egy Azure skálázási egység belül van. Ez azt jelenti, hogy a virtuális gépek számát és a egy rendelkezésre állási csoportban, előbb vagy később egynél több virtuális gép ugyanabban a tartalék vagy frissítési tartományt is említi kerülnek.

Néhány SAP alkalmazáskiszolgáló-példányok a saját dedikált virtuális gépek üzembe helyezése és feltételezve, hogy öt frissítési tartományok fájt, a végén jelenik meg a következő képen látható. Rendelkezésre állási csoporton belül a tartalék és frissítési tartományok tényleges maximális számát. a későbbiekben változhatnak:

![Magas rendelkezésre ÁLLÁS az SAP-alkalmazáskiszolgálókhoz, az Azure-ban][planning-guide-figure-3000]

További részleteket ebben a dokumentációban található: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Magas rendelkezésre állás az SAP Central Services az Azure-ban
A magas rendelkezésre állású architektúra az Azure-beli SAP Central Services esetében ellenőrizze a cikk [magas rendelkezésre állású architektúra és forgatókönyvek esetében az SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) bejegyzés információk. A cikk részletes leírását az adott operációs rendszer mutat.

#### <a name="high-availability-for-the-sap-database-instance"></a>Magas rendelkezésre állás az SAP adatbázis-példányt
A szokásos SAP DBMS magas rendelkezésre ÁLLÁS telepítése a két adatbázis-kezelő virtuális gépet, ahol az adatbázis-kezelő magas rendelkezésre állású funkció használható replikálja az adatokat az aktív DBMS-példányból a második virtuális Gépet, a passzív adatbázis-kezelő példány alapul.

Az adatbázis-kezelő magas rendelkezésre állású és vész helyreállítási funkciókat általában is adott DBMS ismertetett a [DBMS üzembe helyezési útmutató][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Végpontok közötti magas rendelkezésre állás a teljes SAP-rendszerhez
Az alábbiakban egy teljes SAP NetWeaver magas rendelkezésre ÁLLÁS architektúra az Azure-ban – két példa egy Windows, egy, a Linux rendszerre.

Nem felügyelt lemezek csak: Előfordulhat, hogy egy kicsit sérülhet, amikor sok SAP-rendszerek telepítése és üzembe helyezett virtuális gépek száma előfizetésenként Storage-fiókok maximális számát meghaladják kell a fogalmak, kövesse az alábbi utasításokat. Ezekben az esetekben a virtuális merevlemezek, virtuális gépek kell egyesítve jön létre egy Storage-fiókon belül. Általában tennénk alkalmazásréteg VHD-k az SAP az SAP-rendszereit különböző virtuális gépek kombinálásával.  Azt is együtt egy Azure Storage-fiókban lévő másik SAP-rendszereit különböző adatbázis-kezelő virtuális gépeinek különböző virtuális merevlemezeket. Az Azure Storage-fiókok IOPS-korlátok ezáltal szem (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Magas rendelkezésre ÁLLÁS a Windows
![SAP NetWeaver alkalmazás magas rendelkezésre ÁLLÁSÚ architektúra az Azure iaas-beli SQL Serverrel][planning-guide-figure-3200]

A következő Azure szerkezetek az SAP NetWeaver rendszerhez által infrastrukturális problémára gyakorolt hatás minimalizálása és a javítás üzemeltetésére szolgálnak:

* A teljes rendszer helyezünk üzembe az Azure-ban (szükséges – DBMS réteg, (A) SCS példányhoz, és a teljes alkalmazás réteg ugyanazon a helyen futtatnia kell).
* A teljes rendszer fut (kötelező) egy Azure-előfizetésen belül.
* A teljes rendszer fut, egy Azure virtuális hálózaton belül (kötelező).
* Az azonos virtuális hálózatba tartozó virtuális gépek mellett is lehetőség a virtuális gépek egy SAP-rendszer elválasztása három rendelkezésre állási csoportokba.
* Minden egyes réteg (például adatbázis-kezelő, ascs rendszerbe fut be, az alkalmazáskiszolgálók) egy dedikált rendelkezésre állási csoportot kell használnia.
* Egy SAP-rendszer DBMS-példányok futó összes virtuális gép egy rendelkezésre állási csoportban vannak. Feltételezzük, hogy nincs-e egynél több virtuális gép DBMS példányok esetében a rendszer natív adatbázis-kezelő rendelkezésre állással használják a szolgáltatásokat, például az SQL Server AlwaysOn- vagy Oracle Data Guard óta.
* Az adatbázis-kezelő rendszert futtató összes virtuális gép saját tárfiókot használni. Adathoz és naplófájlhoz az adatbázis-kezelő a rendszer szinkronizálja az adatokat az adatbázis-kezelő magas rendelkezésre állású funkciókat használja egy másik tárfiókba replikálja egyik tárfiókból. Egy tárfiók elérhetetlensége egy SQL Windows fürtcsomópont, de nem a teljes SQL Server szolgáltatás elérhetetlensége miatt.
* (A) SCS példányhoz egy SAP-rendszer futó összes virtuális gép egy rendelkezésre állási csoportban vannak. A Windows Server feladatátvételi fürt (WSFC) van konfigurálva, belüli virtuális gépek védelme érdekében a (A) SCS példányhoz.
* (A) SCS-példányok futó összes virtuális gép saját tárfiókot használni. (A) SCS példányhoz fájlok és az SAP globális mappa a rendszer replikálja egy storage-fiókból az SIOS DataKeeper replikációt használ egy másik tárfiókba. Egy tárfiók elérhetetlensége miatt elérhetetlensége egy (A) SCS Windows fürtcsomópont, de nem teljes (A) SCS-szolgáltatás.
* A SAP alkalmazás server réteget jelző összes virtuális gép van egy harmadik rendelkezésre állási csoportban.
* SAP-alkalmazáskiszolgálókhoz futó összes virtuális gép saját tárfiókot használni. Egy tárfiók elérhetetlensége miatt hiányában egy SAP-alkalmazáskiszolgáló, ahol más SAP-alkalmazáskiszolgálókhoz továbbra is futni.

Az alábbi ábra szemlélteti a felügyelt lemezek használatával azonos fekvő.

![SAP NetWeaver alkalmazás magas rendelkezésre ÁLLÁSÚ architektúra az Azure iaas-beli SQL Serverrel][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Magas rendelkezésre ÁLLÁS Linux rendszeren
Az architektúra az SAP magas rendelkezésre ÁLLÁS az Azure-on Linux alapvetően ugyanaz, mint a Windows a fent leírt módon. Tekintse meg az SAP-Jegyzetnek [1928533] támogatott magas rendelkezésre állású megoldások listáját.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Az SAP-példányok automatikus indítása használatával
SAP belül a virtuális gép operációs rendszerének megkezdése után azonnal elindítani az SAP-példányokat a funkció érhető el. A pontos lépések a rendszer részletes ismertetését lásd: SAP Tudásbázis [1909114]. Azonban az SAP nem javasolja a beállítás használatához többé, mert nincs sorrendjében példány újraindítása nem szabályozza, feltéve, hogy több virtuális gép van hatással, vagy több példány virtuális gépenként futott. Feltéve, hogy az Azure szokás egy SAP alkalmazás server-példány a virtuális gép és a kis-és idővel első újraindítása egyetlen virtuális Gépet, az Autostart nem kritikus, és ez a paraméter hozzáadásával engedélyezheti:

    Autostart = 1

A kezdő profilba SAP ABAP és/vagy a Java-példány.

> [!NOTE]
> Az automatikus indítási paraméter néhány downfalls is rendelkezhet. Részletesen a paraméter egy SAP ABAP és Java-példány kezdetét aktivál, ha a példány a kapcsolódó Windows/Linux-szolgáltatás elindult. Természetesen ez a helyzet, amikor elindul az operációs rendszer. Azonban az SAP-szolgáltatások újraindítása is egy közös lépésként SAP Software életciklus-felügyelet funkciók, például a SUM vagy más frissíti vagy frissíti. Ezek a Funkciók, amelyek nem számítanak a példány esetén az összes automatikusan újra kell indítani. Ezért az Autostart paraméter le kell tiltani az ilyen feladatok futtatása előtt. Az automatikus indítási paraméter is nem használandó SAP-példányok fürtözöttek, például a CI/ASCS/SCS esetén.
>
>

Tekintse meg az SAP autostart vonatkozó további információk itt példányok:

* [SAP és a UNIX rendszerű kiszolgáló indítása és leállítása indítása és leállítása](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Indítása és leállítása SAP NetWeaver felügyeleti ügynökök](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Indítsa el a HANA-adatbázis automatikus engedélyezése](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Nagyobb 3 szintű SAP-rendszerek
Magas rendelkezésre állású aspektusa 3 szintű SAP-konfigurációkat van tárgyalja a korábbi szakaszokban már. De mi a helyzet a rendszerek, ahol az adatbázis-kezelő kiszolgáló követelményei túl nagyok, hogy az Azure, azonban az SAP ügyfélalkalmazási rétegben található is telepíthető, az Azure-bA?

#### <a name="location-of-3-tier-sap-configurations"></a>SAP-konfigurációkat 3 szintű helye
Nem támogatott az alkalmazásrétegek felosztása saját maga, vagy az alkalmazás és az adatbázis-kezelő réteg a helyszíni és az Azure között. Az SAP-rendszer teljesen üzembe helyezett helyszíni, vagy az Azure-ban. Még nem támogatott az alkalmazáskiszolgálók, a helyszínen és más Azure-beli futtatását rendelkeznek. Ez a hozzászólás a kiindulási pont. Azt is nem támogatják az SAP-rendszerhez, és két különböző Azure-régióban üzembe helyezett SAP-kiszolgáló alkalmazásréteg DBMS összetevői rendelkeznie. Ha például az adatbázis-kezelő középső RÉGIÓJA az USA nyugati RÉGIÓJA és az SAP alkalmazásréteg. Nem támogatja az ilyen konfigurációk oka az SAP NetWeaver architektúra késés érzékenységét.

Azonban fennállásakor tavaly data center fejlesztett partnerek közös helyek Azure-régióban. Közös helyek gyakran történik a hálózatbővítési a fizikai az Azure data centers egy Azure-régión belül. A rövid távolság és az eszközök az Azure-bA expressroute-on keresztül az együttes elhelyezés a kapcsolat a késés, amely kisebb, mint 2ms eredményezhet. Ezekben az esetekben az adatbázis-kezelő réteg (beleértve a tárolási TÁROLÓHÁLÓZATI és NAS) található ilyen egy bérelt és az SAP az Azure-ban alkalmazásréteg lehetőség. [Nagyméretű HANA-példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Offline biztonsági mentés az SAP-rendszerek
Függ az SAP-konfiguráció (2 szintű vagy 3 szintű) van kiválasztva a biztonsági mentéséhez szükséges lehet. A tartalom a virtuális gép plusz maga az adatbázis biztonsági mentésére. Az adatbázis-kezelő kapcsolatos biztonsági mentések várhatóan adatbázis-módszerekkel együtt kell végrehajtani. A különböző adatbázisokat, részletes leírását található [DBMS útmutató][dbms-guide]. Másrészről az SAP-adatok biztonsági másolat készíthető egy offline módon (beleértve az adatbázisok tartalmát is) ebben a szakaszban leírtak szerint, vagy online a következő szakaszban leírtak szerint.

Az offline biztonsági mentés alapvetően megköveteli a az Azure Portalon a virtuális gép leállítása és a egy példányát az alap Virtuálisgép-lemez és az összes csatlakoztatott lemezek a virtuális géphez. Ezzel a virtuális gép és a kapcsolódó lemezt idő képen egy pont megőrzik. Javasoljuk, hogy a biztonsági másolatok másolása másik Azure Storage-fiókra. Ezért a fejezetben leírt eljárás [lemezek között az Azure Storage-fiókok másolása] [ planning-guide-5.4.2] ebben a dokumentumban a alkalmazni.
A leállás mellett az Azure Portallal egy is megteheti Powershell vagy parancssori felület a itt leírtak szerint: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Visszaállítás, az állapotban, amelyek a kiindulási virtuális Gépet, valamint az eredeti lemezek alap virtuális gép törlése, és a csatlakoztatott lemezeket, a mentett lemezek másolása az eredeti Storage-fiókot vagy resource csoport felügyelt lemezek és a rendszer majd újratelepítésével.
Ez a cikk mutatja be egy példa a folyamat a Powershell parancsfájllal történő: <http://www.westerndevs.com/azure-snapshots/>

Ellenőrizze, hogy telepítése egy új SAP-licenccel, mivel visszaállítása egy virtuális gép biztonsági mentése a fent leírt módon létrehoz egy új hardver kulcsot.

### <a name="online-backup-of-an-sap-system"></a>Az SAP-rendszer online biztonsági mentés
Az adatbázis-kezelő a biztonsági mentés DBMS-specifikus módszerekkel leírtak szerint a [DBMS útmutató][dbms-guide].

Más virtuális gépein az SAP-rendszer Azure virtuális gépek biztonsági mentésének funkciójával is készíthető. Az Azure virtuális gépek biztonsági mentése a szabványos módszer biztonsági mentése egy teljes virtuális gép az Azure-ban. Az Azure Backup tárolja a biztonsági másolatokat az Azure-ban, és lehetővé teszi a virtuális gépek visszaállítását újra.

> [!NOTE]
> 2015. december használatával a virtuális gép biztonsági mentése nem őrzi az egyedi virtuális gép azonosítója az SAP használt licencelése. Ez azt jelenti, hogy egy virtuális gép biztonsági másolat visszaállítása egy új SAP licenckulcs telepíteni kell, ahogy a visszaállított virtuális Gépet egy új virtuális Gépet, és nem helyettesíti az elsőt megtörtént amelyhez kell tekinteni.
>
> ![Windows][Logo_Windows] Windows
>
> Elméletileg, virtuális gépeket, amelyek futtatási adatbázisok biztonsági mentésre alkalmas, valamint olyan egységes módon ha az adatbázis-kezelő rendszer támogatja a Windows VSS (kötet árnyékmásolata szolgáltatás <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>), mint például az SQL kiszolgáló nem.
> Vegye figyelembe, amelyek az Azure virtuális gép biztonsági mentéseinek-időponthoz állítja vissza az adatbázisok nem engedélyezettek. Ezért ajánlott biztonsági mentéshez az adatbázisok adatbázis-kezelő funkciókkal, így nem kell hagyatkoznia a Azure VM Backup esetében.
>
> Megismerkedhet az Azure virtuális gépek biztonsági mentésének kezdő itt: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Egyéb lehetőségek a Microsoft Data Protection Manager telepítése egy Azure-beli Virtuálisgép-és Azure Backup biztonsági mentési és visszaállítási adatbázis együttes használata. További információt itt találhat: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Nincs a Linux Windows VSS nem megfelelő. Így csak fájlkonzisztens biztonsági mentés lehetőség, de nem alkalmazásfüggő biztonsági másolatok. Az SAP DBMS biztonsági mentést kell végezni az adatbázis-kezelő szolgáltatással. A fájlrendszer, amely tartalmazza a SAP-kapcsolatos adatokat is mentve, például használatával tar leírtak szerint itt: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Az Azure az SAP-környezetünk éles Vészhelyreállítási helyként
Mid 2014 óta különféle összetevőket Hyper-V, a System Center és az Azure-bővítmények engedélyezése az Azure-használatát Vészhelyreállítási helyként futó helyszíni Hyper-v-alapú virtuális gépeket.

Ez a megoldás üzembe helyezése a részletező blog itt dokumentált: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Összegzés
A magas rendelkezésre állású legfontosabb pontjait az Azure-beli SAP-rendszerek a következők:

* Ezen a ponton az időben a SAP rendszerkritikus meghibásodási pontot nem védhető ugyanúgy módon teheti meg a helyszíni üzemelő példányok. A hiba oka, hogy a megosztott lemez fürtök nem még kell építeni az Azure-ban, a 3. fél szoftverek nélkül.
* Az adatbázis-kezelő réteg kell használnia, nem szükséges megosztott lemez fürttechnológia adatbázis-kezelő funkcióit. Részletek szerepelnek a [DBMS útmutató][dbms-guide].
* A tartalék tartományok belüli problémák az Azure infrastruktúra- vagy gazdagép karbantartási hatás minimalizálása érdekében az Azure rendelkezésre állási készletek kell használnia:
  * Azt javasoljuk, hogy egy rendelkezésre állási csoportot az SAP alkalmazásrétegre.
  * Azt javasoljuk, hogy egy külön rendelkezésre állási csoportot az SAP DBMS réteg.
  * NEM ajánlott a alkalmazni az azonos rendelkezésre állási csoportjának a virtuális gépek különböző SAP-rendszerek.
  * Prémium szintű Managed Disks használata ajánlott.
* Az SAP DBMS réteg biztonsági mentés céljából, ellenőrizze a [DBMS útmutató][dbms-guide].
* SAP párbeszédpanel példányok biztonsági másolatának értelme kevés, mert általában gyorsabb egyszerű párbeszédpanel példányok újratelepíteni.
* Biztonsági mentése a virtuális gép, amely tartalmazza a globális címjegyzékben, az SAP-rendszerhez, és ennek a különböző példányok, a profilok jelentéssel bírnak és a Windows biztonsági másolat kell végrehajtani, vagy például tar Linux rendszeren. Mivel ebben az esetben a Windows Server 2008 (R2) és a Windows Server 2012 (R2) közötti különbségeket, amely könnyebben biztonsági mentéséhez használja a a legújabb Windows Server-kiadások, javasoljuk, hogy a Windows Server 2012 (R2) futó Windows vendég operációs rendszerként.

##<a name="next-steps"></a>További lépések
A cikkek elolvasásával:

- [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA-infrastruktúra konfigurációi és műveletek az Azure-ban] (https://docs.microsoft.com/
- Azure/virtuális-gépek/számítási feladatok/sap/hana-virtuálisgép-műveletek)
