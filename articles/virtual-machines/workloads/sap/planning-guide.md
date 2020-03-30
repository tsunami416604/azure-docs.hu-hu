---
title: 'SAP az Azure-ban: Tervezési és megvalósítási útmutató'
description: Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver számára
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ddcc5165f5588ff9015d7fafbc2b822268ffea7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337165"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver számára

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
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

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

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
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
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



A Microsoft Azure lehetővé teszi a vállalatok számára, hogy minimális idő alatt, hosszadalmas beszerzési ciklusok nélkül szerezzenek be számítási és tárolási erőforrásokat. Az Azure Virtual Machine szolgáltatás lehetővé teszi a vállalatok számára, hogy klasszikus alkalmazásokat telepítsenek, például az SAP NetWeaver alapú alkalmazásokat az Azure-ba, és bővítsék megbízhatóságukat és elérhetőségüket anélkül, hogy a helyszínen további erőforrások állnának rendelkezésre. Az Azure Virtual Machine Services is támogatja a létesítmények közötti kapcsolatot, amely lehetővé teszi a vállalatok számára, hogy aktívan integrálja az Azure virtuális gépek a helyszíni tartományok, a privát felhők és az SAP-rendszer fekvő.
Ez a tanulmány ismerteti a Microsoft Azure virtuális gép alapjait, és bemutatja az SAP NetWeaver azure-beli telepítések tervezési és megvalósítási szempontjait, és mint ilyen, a tényleges indítás előtt elolvasandó dokumentumnak kell lennie. az SAP NetWeaver üzembe helyezését az Azure-ban.
A papír kiegészíti az SAP telepítési dokumentációt és az SAP-megjegyzéseket, amelyek az SAP-szoftverek telepítésének és központi telepítésének elsődleges erőforrásait képviselik az adott platformokon.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Összefoglalás
Cloud Computing egy széles körben használt kifejezés, amely egyre nagyobb jelentőséget az informatikai ágazat, a kisvállalkozások egészen a nagy-és multinacionális vállalatok.

A Microsoft Azure a Microsoft felhőszolgáltatási platformja, amely az új lehetőségek széles skáláját kínálja. Mostantól az ügyfelek gyorsan kiépíthetik és megszüntethetik az alkalmazásokat szolgáltatásként a felhőben, így azok nem korlátozódnak a technikai vagy költségvetési korlátozásokra. Ahelyett, hogy időt és költségvetést fektetnének a hardverinfrastruktúrába, a vállalatok az alkalmazásra, az üzleti folyamatokra és annak az ügyfelek és a felhasználók számára nyújtott előnyeire összpontosíthatnak.

A Microsoft Azure Virtual Machine Services szolgáltatással a Microsoft átfogó szolgáltatott infrastruktúra (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Ez a tanulmány bemutatja, hogyan tervezhet és valósíthat meg SAP NetWeaver alapú alkalmazásokat a Microsoft Azure-on belül, mint a választott platform.

Maga a dokumentum két fő szempontra összpontosít:

* Az első rész két támogatott üzembe helyezési mintát ír le az SAP NetWeaver alapú alkalmazások hoz az Azure-ban. Azt is ismerteti, az Azure általános kezelése az SAP-telepítések szem előtt tartva.
* A második rész részletezi az első részben leírt különböző forgatókönyvek megvalósítását.

További erőforrásokért olvassa el a dokumentum [Erőforrások][planning-guide-1.2] című fejezetét.

### <a name="definitions-upfront"></a>Fogalommeghatározások előre
A dokumentumban a következő kifejezéseket használjuk:

* IaaS: Infrastruktúra mint szolgáltatás
* PaaS: Platform mint szolgáltatás
* SaaS: Szoftver mint szolgáltatás
* SAP-összetevő: egy egyedi SAP-alkalmazás, például az ECC, a BW, a Solution Manager vagy az S/4HANA.  Az SAP-összetevők hagyományos ABAP vagy Java technológiákon vagy nem NetWeaver alapú alkalmazásokon, például üzleti objektumokon alapulhatnak.
* SAP-környezet: egy vagy több SAP-összetevő logikailag csoportosítva egy üzleti funkció, például fejlesztés, QAS, képzés, DR vagy éles.
* SAP fekvő: Ez a kifejezés az ügyfél informatikai környezetében lévő teljes SAP-eszközökre vonatkozik. Az SAP-környezet tartalmazza az összes éles és nem éles környezetben.
* SAP rendszer: A DBMS-réteg és az alkalmazásréteg kombinációja, például egy SAP ERP fejlesztési rendszer, SAP BW tesztrendszer, SAP CRM termelési rendszer stb. Az Azure-telepítések nem támogatott a két réteg felosztása a helyszíni és az Azure között. Azt jelenti, hogy az SAP-rendszer vagy a helyszínen van telepítve, vagy az Azure-ban van telepítve. Azonban üzembe helyezheti az SAP-környezet különböző rendszereit az Azure-ba vagy a helyszíni környezetbe. Például üzembe helyezheti az SAP CRM fejlesztési és tesztelési rendszereket az Azure-ban, de az SAP CRM éles rendszer a helyszínen.
* Telephelyek közötti vagy hibrid: Egy olyan forgatókönyvet ismertet, amelyben a virtuális gépek egy olyan Azure-előfizetésre vannak telepítve, amely a helyszíni adatközpont(ok) és az Azure között helyek közötti, többtelephelyes, többtelephelyes vagy ExpressRoute-kapcsolattal rendelkezik. A közös Azure-dokumentációban az ilyen típusú központi telepítések is le, mint a telephelyek közötti vagy hibrid forgatókönyvek. A kapcsolat oka a helyszíni tartományok, a helyszíni Active Directory/OpenLDAP és a helyszíni DNS Azure-ra való kiterjesztése. A helyszíni környezet az előfizetés Azure-eszközeire is kiterjed. Ezzel a bővítményekkel a virtuális gépek a helyszíni tartomány részét lehet tenni. A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és futtathatnak szolgáltatásokat ezeken a virtuális gépeken (például a DBMS-szolgáltatásokon). A helyszínen üzembe helyezett virtuális gépek és az Azure által telepített virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a leggyakoribb és szinte kizárólagos eset az SAP-eszközök Azure-ba való üzembe helyezése. További információt [ebben a cikkben][vpn-gateway-cross-premises-options] és [ebben a cikkben][vpn-gateway-site-to-site-create]talál.
* Azure monitoring bővítmény, bővített figyelése és az Azure Extension az SAP:Írja le egy és ugyanazt az elemet. Ismerteti a virtuális gép bővítmény, amelyet telepítenie kell az Ön számára, hogy néhány alapvető adatot az Azure-infrastruktúráról az SAP gazdaügynök. Az SAP az SAP-jegyzetekben hivatkozhat rá figyelőbővítményként vagy továbbfejlesztett figyelésként. Az Azure-ban az **SAP-hoz való Azure-bővítményként**hivatkozunk rá.

> [!NOTE]
> Az SAP-rendszerek telephelyközi vagy hibrid üzembe helyezései, ahol az SAP-rendszereket futtató Azure virtuális gépek egy helyszíni tartomány tagjai, éles SAP-rendszerek számára támogatottak. A létesítmények közötti vagy hibrid konfigurációk használata támogatja az alkatrészek vagy a teljes SAP-környezetek azure-ba való üzembe helyezését. Még a teljes SAP-környezet futtatásához az Azure-ban igényel, amelyek a virtuális gépek részét képezik a helyszíni tartomány és az ADS/OpenLDAP. 
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Erőforrások
Az Azure dokumentációjában az SAP-munkaterhelés belépési pontja [itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)található. Kezdve ezzel a belépési pont talál sok cikket, amelyek lefedik a témák:

- SAP NetWeaver és Business One az Azure-ban
- SAP DBMS útmutatók különböző DBMS-rendszerekhez az Azure-ban
- Magas rendelkezésre állás és vészhelyreállítás az SAP-terheléshez az Azure-ban
- Az SAP HANA Azure-on való futtatásához szükséges konkrét útmutatás
- Az SAP HANA ADATBÁZIS-RENDSZERÉhez tartozó, az Azure HANA nagy példányainak útmutatója 


> [!IMPORTANT]
> Ahol csak lehetséges, a hivatkozó SAP telepítési útmutatókra vagy más SAP-dokumentációra <http://service.sap.com/instguides>mutató hivatkozás takarásban (Hivatkozás: InstGuide-01, lásd). Az SAP dokumentációját és útmutatóit mindig figyelmesen kell elolvasni, ha az előfeltételekről, a telepítési folyamatról vagy az egyes SAP-funkciók részleteiről van szó, mivel a Microsoft-dokumentumok csak az SAP-szoftverekhez telepített és üzemeltetett speciális feladatokat fedik le. Microsoft Azure virtuális gép.
>
>

A következő SAP-megjegyzések az Azure-beli SAP témaköréhez kapcsolódnak:

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és méretezés |
| [2015553] |SAP a Microsoft Azure-on: Támogatási előfeltételek |
| [1999351] |Továbbfejlesztett Azure-figyelés immárSAP-figyelési hibák elhárítása |
| [2178632] |Az SAP legfontosabb figyelési mutatói a Microsoft Azure-ban |
| [1409604] |Virtualizáció Windowsrendszeren: Továbbfejlesztett figyelés |
| [2191498] |SAP Linuxon az Azure-ral: Továbbfejlesztett figyelés |
| [2243692] |Linux a Microsoft Azure (IaaS) virtuális gépen: SAP licencproblémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7.x: Telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítés és frissítés |
| [1597355] |Swap-space ajánlás Linux |

Olvassa el az [SCN Wiki-t](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) is, amely tartalmazza az összes SAP Notes for Linux-ot.

Az Azure-előfizetések általános alapértelmezett korlátozásai és maximális korlátozásai ebben a [cikkben][azure-resource-manager/management/azure-subscription-service-limits-subscription]találhatók.

## <a name="possible-scenarios"></a>Lehetséges forgatókönyvek
Az SAP-t gyakran tekintik a vállalatokon belüli egyik legkritikusabb alkalmazásnak. Ezeknek az alkalmazásoknak az architektúrája és működése többnyire összetett, és fontos, hogy megfeleljen a rendelkezésre állásra és a teljesítményre vonatkozó követelményeknek.

Ezért a vállalatoknak alaposan át kell gondolniuk, hogy melyik felhőszolgáltatót válasszák az ilyen üzleti legkritikusabb üzleti folyamatok futtatásához. Az Azure ideális nyilvános felhőplatform az üzleti legkritikusabb SAP-alkalmazások és üzleti folyamatok számára. Tekintettel az Azure-infrastruktúra széles választékára, szinte az összes meglévő SAP NetWeaver és S/4HANA rendszer már ma üzemeltethető az Azure-ban. Az Azure számos Terabájt memóriát és több mint 200 processzort biztosít a virtuális gépek számára. Azon túl, hogy az Azure kínál [HANA nagy példányok,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)amelyek lehetővé teszik a felskálázási HANA-telepítések akár 24 TB-os és SAP HANA horizontális felskálázási telepítések akár 120 TB-os. Ma már kilehet mondani, hogy szinte az összes helyszíni SAP-forgatókönyv is futtatható az Azure-ban. 

A forgatókönyvek és néhány nem támogatott forgatókönyvek durva leírását lásd a dokumentum [SAP számítási feladatok az Azure virtuális gépen támogatott forgatókönyvek.](./sap-planning-supported-configurations.md)

Tekintse meg ezeket a forgatókönyveket és néhány olyan feltételt, amelyek et a hivatkozott dokumentáció nem támogatottnak nevezett az Azure-ba üzembe helyezni kívánt architektúra tervezése és fejlesztése során.

Összességében a leggyakoribb üzembe helyezési minta egy telephelyközi forgatókönyv, mint a megjelenített

![VPN helyek közötti kapcsolattal (telephelyek közötti)][planning-guide-figure-300]

Számos ügyfél számára a telephelyek közötti üzembe helyezési minta alkalmazása az a tény, hogy az összes alkalmazás számára a legátláthatóbb, ha az Azure ExpressRoute használatával kiterjeszti a helyszíni létesítményeket az Azure-ba, és az Azure-t virtuális adatközpontként kezeli. Ahogy egyre több eszköz kerül át az Azure-ba, az Azure által telepített infrastruktúra és hálózati infrastruktúra növekedni fog, és a helyszíni eszközök ennek megfelelően csökkenni fog. Minden átlátható a felhasználók és az alkalmazások számára.

Annak érdekében, hogy az SAP-rendszerek sikeresüzembe helyezése az Azure IaaS vagy az IaaS általában, fontos megérteni a jelentős különbségeket a hagyományos outsourcers vagy hosters és IaaS-ajánlatok között. Míg a hagyományos gazdagép vagy outsourcer az infrastruktúrát (hálózat, tároló és kiszolgáló típus) az ügyfél által üzemeltetni kívánt munkaterheléshez igazítja, ehelyett az ügyfél vagy a partner felelőssége a munkaterhelés jellemzése és a megfelelő Azure kiválasztása a virtuális gépek, a tároló és a hálózat összetevői az IaaS-telepítésekhez.

Az Azure-ba való üzembe helyezés megtervezéséhez szükséges adatok összegyűjtéséhez fontos a következőket:

- Kiértékelheti, hogy milyen SAP-termékek támogatottak az Azure virtuális gépeken való futtatáshoz
- Kiértékelheti, hogy az SAP-termékekhez tartozó adott Azure-virtuális gépek milyen adott operációsrendszer-kiadásokat támogatnak
- Kiértékelheti, hogy az SAP-termékek mely DBMS-kiadások támogatottak adott Azure-beli virtuális gépekkel
- Annak kiértékelése, hogy a szükséges OS/DBMS kiadások némelyike szükséges-e az SAP-kiadások, a támogatási csomag és a kernelfrissítések végrehajtásához a támogatott konfigurációhoz
- Kiértékelheti, hogy az Azure-ban való üzembe helyezéshez át kell-e helyeznie a különböző operációs rendszerekre.

Az Azure-ban támogatott SAP-összetevők, a támogatott Azure-infrastruktúra-egységek, valamint a kapcsolódó operációsrendszer-kiadások és a DBMS-kiadások részleteit a [Milyen SAP-szoftverek támogatják az Azure-telepítésekhez.](./sap-supported-product-on-azure.md) Az érvényes SAP-kiadások, az operációs rendszer és a DBMS-kiadások kiértékeléséből nyert eredmények nagy hatással vannak az SAP-rendszerek Azure-ba való áthelyezésére irányuló erőfeszítésekre. Az értékelés eredményei határozzák meg, hogy jelentős előkészítési erőfeszítésekre lehetnek-e szükség azokban az esetekben, amikor sap-kiadási frissítésekre vagy operációs rendszerek módosítására van szükség.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-régiók
A Microsoft Azure-szolgáltatásait az Azure-régiókban gyűjtik. Az Azure-régió egy vagy egy gyűjtemény az adatközpontok, amelyek tartalmazzák a hardver és az infrastruktúra, amely fut, és a különböző Azure-szolgáltatások üzemelteti. Ez az infrastruktúra számos olyan csomópontot tartalmaz, amelyek számítási csomópontként vagy tárolócsomópontként működnek, vagy hálózati funkciókat futtatnak. 

A különböző Azure-régiók listáját az [Azure földrajzi területein olvashat.](https://azure.microsoft.com/global-infrastructure/geographies/) Nem minden Azure-régió kínálja ugyanazokat a szolgáltatásokat. A futtatni kívánt SAP-terméktől, valamint az ahhoz kapcsolódó operációs rendszertől és adatbázis-kezelőrendszertől függően olyan helyzetbe kerülhet, amelyet egy bizonyos régió nem kínál a szükséges virtuálisgép-típusoknak. Ez különösen igaz az SAP HANA futtatásához, ahol általában az M/Mv2 virtuális gép sorozat virtuális gépeinek szükséges. Ezek a virtuálisgép-családok csak a régiók egy részhalmazában vannak telepítve. Megtudhatja, hogy milyen pontos virtuális gép, típusok, Azure-tárhelytípusok vagy más Azure-szolgáltatások érhetők el, amelyek a régiók segítségével a webhely [termékek elérhető régiónként.](https://azure.microsoft.com/global-infrastructure/services/) A tervezés megkezdésekor, és bizonyos régiókat elsődleges régióként és végül másodlagos régióként szem előtt tart, először meg kell vizsgálnia, hogy a szükséges szolgáltatások elérhetők-e ezekben a régiókban. 

### <a name="availability-zones"></a>Rendelkezésre állási zónák
Az Azure-régiók közül több is megvalósított egy rendelkezésre állási zónák nevű koncepciót. A rendelkezésre állási zónák fizikailag különálló helyek egy Azure-régióban. Az egyes rendelkezésre állási zónák egy vagy több, önálló áramellátással, hűtéssel, és hálózattal rendelkező adatközpontból állnak. Például két virtuális gép üzembe helyezése az Azure két rendelkezésre állási zónájában, és az SAP DBMS-rendszer vagy az SAP központi szolgáltatások magas rendelkezésre állású keretrendszerének megvalósítása a legjobb SLA-t biztosítja az Azure-ban. Ehhez az adott virtuálisgép-SLA-hoz az Azure-ban tekintse meg a [virtuálisgép-SLA-k](https://azure.microsoft.com/support/legal/sla/virtual-machines/)legújabb verzióját. Mivel az Azure-régiók gyorsan fejlődtek és bővültek az elmúlt években, az Azure-régiók topológiája, a fizikai adatközpontok száma, az adatközpontok közötti távolság és az Azure rendelkezésre állási zónák közötti távolság eltérő lehet. És ezzel a hálózati késés.

A rendelkezésre állási zónák elve nem vonatkozik a [HANA nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)HANA-specifikus szolgáltatására. A HANA nagy példányok szolgáltatásiszint-szerződései az [SAP HANA-hoz az Azure nagy példányais SLA-ban](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) található 


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Tartalék tartományok
A tartalék tartományok az adatközpontokban található fizikai infrastruktúrához szorosan kapcsolódó fizikai meghibásodási egységet jelentenek, és bár egy fizikai panel vagy rack tartalék tartománynak tekinthető, a kettő között nincs közvetlen egy az egyhez hozzárendelés.

Ha egy SAP-rendszer részeként több virtuális gépet telepít a Microsoft Azure virtuálisgép-szolgáltatásokban, befolyásolhatja az Azure Fabric Controllert, hogy az alkalmazást különböző tartalék tartományokba telepítse, így nagyobb rendelkezésre állási SLA-k. Azonban a tartalék tartományok egy Azure Scale-egység (gyűjtemény több száz számítási csomópontok vagy tárolási csomópontok és a hálózat) vagy a virtuális gépek hozzárendelése egy adott tartalék tartomány van valami, amely felett nem rendelkezik közvetlen vezérléssel. Annak érdekében, hogy az Azure fabric-vezérlő a virtuális gépek egy készletét különböző tartalék tartományokra való üzembe helyezéséhez irányítsa, üzembe helyezéskor hozzá kell rendelnie egy Azure rendelkezésre állási készletet a virtuális gépekhez. Az Azure rendelkezésre állási csoportjairól az [Azure rendelkezésre állási készletei][planning-guide-3.2.3] című fejezetben olvashat ebben a dokumentumban.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Tartományok frissítése
A frissítési tartományok egy logikai egységet képviselnek, amely segít meghatározni, hogy egy SAP-rendszeren belüli virtuális gép, amely több virtuális gépen futó SAP-példányokból áll, hogyan frissül. Frissítés esetén a Microsoft Azure egyenként frissíti ezeket a frissítési tartományokat. A virtuális gépek terjesztése a központi telepítés idején a különböző frissítési tartományok, megvédheti az SAP-rendszer részben a lehetséges állásidő. Annak érdekében, hogy az Azure-t egy SAP-rendszer virtuális gépeinek különböző frissítési tartományokra elosztva üzembe helyezésére kényszerítse, be kell állítania egy adott attribútumot az egyes virtuális gépek üzembe helyezésekor. A tartalék tartományokhoz hasonlóan az Azure Scale-egység is több frissítési tartományra van osztva. Annak érdekében, hogy az Azure fabric-vezérlő a virtuális gépek egy készletét különböző frissítési tartományokra való üzembe helyezéséhez irányítsa, üzembe helyezéskor hozzá kell rendelnie egy Azure rendelkezésre állási készletet a virtuális gépekhez. Az Azure rendelkezésre állási csoportjairól az alábbi fejezetben olvashat az [Azure rendelkezésre állási készleteiről.][planning-guide-3.2.3]


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Az Azure rendelkezésre állási készletei
Az Azure virtuális gépek egy Azure rendelkezésre állási készleten belül az Azure Fabric Controller különböző hiba- és frissítési tartományokon keresztül forgalmazza. A különböző tartalék és frissítési tartományokon keresztüli terjesztés célja, hogy megakadályozza az SAP-rendszer összes virtuális gépének leállítását infrastruktúra karbantartása vagy egy tartalék tartományon belüli hiba esetén. Alapértelmezés szerint a virtuális gépek nem részei egy rendelkezésre állási csoport. A virtuális gép részvételét egy rendelkezésre állási csoportban az üzembe helyezés időpontjában vagy később egy virtuális gép újrakonfigurálása és újratelepítése határozza meg.

Az Azure rendelkezésre állási csoportjainak koncepciójának, valamint a tartalék és frissítési tartományokhoz kapcsolódó rendelkezésre állási csoportok megértéséhez olvassa el [ezt a cikket.][virtual-machines-manage-availability] 

A rendelkezésre állási csoportok meghatározásakor, és próbálja meg keverni a különböző virtuálisgép-családok különböző virtuális gépeit egy rendelkezésre állási csoporton belül, problémák merülhetnek fel, amelyek megakadályozzák, hogy egy adott virtuálisgép-típust egy ilyen rendelkezésre állási csoportba vegyen fel. Ennek az az oka, hogy a rendelkezésre állási csoport egy bizonyos típusú számítási gazdagépet tartalmazó egység méretezéséhez van kötve. És egy bizonyos típusú számítási gazdagép csak bizonyos típusú virtuálisgép-családok futtatásához. Ha például létrehoz egy rendelkezésre állási csoportot, és telepíti az első virtuális gépet a rendelkezésre állási csoportba, és kiválaszt egy virtuális gép típusát az Esv3 család, és majd megpróbálja üzembe helyezni a második virtuális gép az M család virtuális gép, a rendszer elutasítja a második foglalás. Ennek oka az, hogy az Esv3 családi virtuális gépek nem futnak ugyanazon a gazdagépen hardveren, mint az M család virtuális gépei. Ugyanez a probléma akkor fordulhat elő, amikor megpróbálja átméretezni a virtuális gépeket, és megpróbál áthelyezni egy virtuális gépet az Esv3 családból az M család virtuálisgép-típusába. Abban az esetben, ha egy virtuálisgép-család, amely nem üzemeltethető ugyanazon a gazdagépen hardver, le kell állítania az összes virtuális gép a rendelkezésre állási csoportban, és átméretezni őket, hogy képes legyen futtatni a másik gazdagép-típus. A rendelkezésre állási csoporton belül üzembe helyezett virtuális gépek SLOS-jai esetében tekintse meg a [Virtuálisgép SL-ek című cikket.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 

A rendelkezésre állási készlet és a kapcsolódó frissítési és tartalék tartomány elve nem vonatkozik a [HANA nagy példányok HANA-specifikus szolgáltatására.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) A hana nagy példányok szolgáltatásiszint-szerződései az [SAP HANA sla-ja az Azure nagy példányokon](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)című cikkben találhatók. 

> [!IMPORTANT]
> Az Azure rendelkezésre állási zónák és az Azure rendelkezésre állási készletek fogalmai kölcsönösen kizárják egymást. Ez azt jelenti, hogy egy pár vagy több virtuális gép üzembe helyezhetegy adott rendelkezésre állási zónába vagy egy Azure rendelkezésre állási csoportba. De nem mindkettőt.


## <a name="azure-virtual-machine-services"></a>Az Azure virtuálisgép-szolgáltatásai
Az Azure a virtuális gépek széles választékát kínálja, amelyeket üzembe helyezhet. Nincs szükség előzetes technológiai és infrastrukturális beszerzésre. Az azure virtuálisgép-szolgáltatás leegyszerűsíti az alkalmazások karbantartását és üzemeltetését azáltal, hogy igény szerinti számítási és tárolási szolgáltatást biztosít a webalkalmazások és a csatlakoztatott alkalmazások üzemeltetéséhez, méretezéséhez és kezeléséhez. Az infrastruktúra-kezelés automatizált egy olyan platformmal, amely et magas rendelkezésre állásra és dinamikus skálázásra terveztek, hogy megfeleljen a használati igényeknek és számos különböző díjszabási modell nek.

![A Microsoft Azure virtuálisgép-szolgáltatások elhelyezése][planning-guide-figure-400]

Az Azure virtuális gépekkel a Microsoft lehetővé teszi, hogy egyéni kiszolgálói lemezképeket telepítsen az Azure-ba IaaS-példányként. Vagy az Azure-képgalériából származó fogyóeszközök operációsrendszer-lemezképeinek széles választékából választhat.

Működési szempontból az Azure virtuális gép szolgáltatás hasonló élményt kínál, mint a helyszínen üzembe helyezett virtuális gépek. Ön felelős a felügyeletért, a műveletekért és az adott operációs rendszer javításáért, amely egy Azure virtuális gépben és annak alkalmazásában fut az adott virtuális gépben. A Microsoft nem nyújt több szolgáltatást azon túl, hogy a virtuális gép az Azure-infrastruktúrán (Infrastruktúra szolgáltatásként - IaaS) üzemelteti. Az SAP-munkaterhelés, amely az ügyfél üzembe helyezése, a Microsoft nem rendelkezik az IaaS-ajánlatokon túl. 

A Microsoft Azure platform egy több-bérlős platform. Ennek eredményeképpen az Azure-beli virtuális gépeket tároló, hálózati és számítási erőforrások – néhány kivételtől eltekintve – megosztottak a bérlők között. Az intelligens szabályozás és a kvótalogika segítségével megakadályozhatja, hogy egy bérlő drasztikus módon befolyásolja egy másik bérlő (zajos szomszéd) teljesítményét. Különösen az SAP HANA Azure-platform hitelesítéséhez a Microsoftnak bizonyítania kell az erőforrások elkülönítését olyan esetekben, amikor több virtuális gép futtatható ugyanazon a gazdagépen rendszeresen az SAP-nak. Bár az Azure logikája igyekszik megtartani a sávszélesség-beli eltéréseket, a nagy megosztott platformok általában nagyobb eltéréseket vezetnek be az erőforrások és a sávszélesség rendelkezésre állásában, mint amit az ügyfelek a helyszíni telepítésekben tapasztalhatnak. Figyelembe kell venni annak valószínűségét, hogy az Azure-beli SAP-rendszer nagyobb eltéréseket tapasztalhat, mint egy helyszíni rendszerben.

### <a name="azure-virtual-machines-for-sap-workload"></a>Azure virtuális gépek az SAP-munkaterheléshez

Az SAP-munkaterhelés, szűkítettük a kiválasztás tágítható a különböző virtuálisgép-családok, amelyek alkalmasak az SAP számítási feladatok és az SAP HANA számítási feladatok pontosabban. A megfelelő virtuális géptípus és az SAP-munkaterhelésen való munkaképességének megkeresésének módját a [Dokumentum ismerteti, hogy milyen SAP-szoftver támogatott az Azure-telepítésekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 

> [!NOTE]
> A virtuális gép típusai, amelyek sap-munkaterhelés tanúsítvánnyal rendelkeznek, nincs túl kiépítése a CPU és a memória-erőforrások.

A tisztán támogatott virtuális gépek kiválasztásán túl azt is ellenőriznie kell, hogy ezek elérhetők-e egy adott régióban a [régiónként elérhető Termékek](https://azure.microsoft.com/global-infrastructure/services/)webhely alapján. De ami még fontosabb, meg kell értékelni, hogy:

- Különböző virtuálisgép-típusok processzor- és memóriaerőforrásai 
- Különböző virtuálisgép-típusok IOPS-sávszélessége
- Különböző virtuálisgép-típusok hálózati képességei
- A csatolható lemezek száma
- Bizonyos Azure-tárhelytípusok kihasználásának lehetősége

megfelel az Ön igényéhez. A legtöbb, hogy az adatok itt [található (Linux)][virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows] egy adott virtuális gép típus.

Árképzési modellként számos különböző árképzési lehetőség közül választhat, amelyek például:

- Használatalapú fizetés
- Egy év fenntartva
- Három év fenntartva
- Azonnali árképzés

Az árak az egyes különböző ajánlatok különböző szolgáltatási ajánlatok körül operációs rendszerek és a különböző régiók ban érhető el az oldalon [Linux virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és a [Windows virtuális gépek díjszabása.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Az egy éves és három éves fenntartott példányok részletes és rugalmas ságáról az alábbi cikkekben talál:

- [Mik azok az Azure-foglalások?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
- [A Reserved VM Instances virtuális gépeinek méretrugalmassága](https://docs.microsoft.com/azure/virtual-machines/windows/reserved-vm-instance-size-flexibility)
- [Az Azure-foglalási kedvezmény alkalmazása virtuális gépekre](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-vm-reservation-charges) 

A direktszintű díjszabásról az Azure Spot virtuális gépek című cikkben olvashat [bővebben.](https://azure.microsoft.com/pricing/spot/) Az azonos virtuálisgép-típus díjszabása is eltérő lehet a különböző Azure-régiók között. Egyes ügyfelek számára érdemes volt egy olcsóbb Azure-régióban üzembe helyezni.

Emellett az Azure egy dedikált gazdagép fogalmait is kínálja. A dedikált gazdagép-koncepció segítségével még jobban szabályozhatja az Azure által végzett javítási ciklusokat. A javítást a saját időbeosztása szerint is időzítheti. Ez az ajánlat kifejezetten olyan számítási feladattal rendelkező ügyfeleket céloz meg, amelyek nem feltétlenül követik a szokásos munkaterhelési ciklust. Az Azure dedikált gazdagépajánlatainak fogalmait az [Azure dedikált gazdagépe](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)iszt. Az ajánlat használata az SAP számítási feladatok, és több SAP-ügyfelek, akik szeretnék, hogy több ellenőrzést az infrastruktúra javítása és a Microsoft esetleges karbantartási tervek. Ha többet szeretne tudni arról, hogy a Microsoft hogyan karbantartja és foltezi a virtuális gépeket kiszolgáló Azure-infrastruktúrát, olvassa el a Virtuális gépek karbantartása az [Azure-ban című cikket.](https://docs.microsoft.com/azure/virtual-machines/maintenance-and-updates)

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Generációs 1 és 2 generációs virtuális gépek
A Microsoft hipervizorképes kezelni két különböző generációs virtuális gépek. Ezek a formátumok az úgynevezett **Generation 1** és **Generation 2**. **A 2.** Az Azure az 1. Az Azure virtuális gépek üzembe helyezésekor az alapértelmezett beállítás továbbra is a Generation 1 formátum használata. Eközben telepítheti generation 2 VM formátumokat is. Az [Azure-beli 2-es generációs virtuális gépek támogatása](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) című cikk felsorolja a 2. Ez a cikk a 2. Ennél is fontosabb ez a cikk az 1. 

> [!NOTE]
> Az Azure-ban futó 1. Olvassa el a [2. generációs virtuális gépek támogatása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) című cikket a különbségek listájának megtekintéséhez.  
 
Egy meglévő virtuális gép áthelyezése az egyik generációból a másik generációba nem lehetséges. A virtuális gép létrehozásának módosításához telepítenie kell egy új virtuális gépet a kívánt generációból, és újra kell telepítenie a generációs virtuális gépet, és újra kell telepítenie a generációs virtuális gépben futó szoftvert. Ez csak a virtuális gép alap Virtuális merevlemez-lemezképét érinti, és nincs hatással az adatlemezekre vagy a csatolt NFS- vagy SMB-megosztásokra. Adatlemezek, NFS- vagy SMB-megosztások, amelyeket eredetileg egy 1.

Ebben a pillanatban, akkor találkozik ezzel a problémával, különösen az Azure M sorozatú virtuális gépek és az Mv2 sorozatú virtuális gépek között. Az 1. generációs virtuálisgép-formátum korlátai miatt az Mv2 család nagy virtuális gépeit nem lehetett 1. A másik oldalon az M-series virtuálisgép-család még nincs engedélyezve a 2. Ennek eredményeképpen az M-sorozat és az Mv2 sorozatú virtuális gépek közötti újraméretezés hez a szoftver újratelepítését kell igényelni egy olyan virtuális gépen, amelyet a másik virtuálisgép-családban céloz meg. A Microsoft azon dolgozik, hogy lehetővé tegye az M sorozatú virtuális gépek üzembe helyezését a 2. Az M sorozatú virtuális gépek üzembe helyezése 2. generációs virtuális gépként a jövőben lehetővé teszi az M sorozatú és az Mv2 sorozatú virtuális gépek közötti, látszólag kevésbé átméretezést. Mindkét irányban, akár az M sorozatról a nagyobb Mv2 sorozatú virtuális gépekre, akár a nagyobb Mv2 sorozatú virtuális gépekről a kisebb M-sorozatú virtuális gépekre történő méretezésre. A dokumentáció továbbfejlesztett lesz, amint az M sorozatú virtuális gépek üzembe helyezhetők 2.    

 

### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Tárolás: Microsoft Azure Storage és Data Disks
A Microsoft Azure virtuális gépek különböző tárolótípusokat használnak. Az SAP Azure virtuálisgép-szolgáltatásokon való megvalósításakor fontos megérteni a két fő tárolási típus közötti különbségeket:

* Nem perzisztens, felejtő tároló.
* Állandó tároló.

Az Azure virtuális gépek nem állandó lemezeket kínálnak a virtuális gép üzembe helyezése után. Virtuális gép újraindítása esetén a meghajtókon lévő összes tartalom törlődik. Ezért ez egy adott, hogy az adatfájlok és a log / redo fájlok adatbázisok semmilyen körülmények között nem található az ilyen nem megőrzött meghajtókon. Előfordulhat, hogy egyes adatbázisok kivételeket, ahol ezek a nem megőrzött meghajtók lehet nek hőmérsékletés temptablespaces. Azonban ne használja ezeket a meghajtókat az A-sorozatú virtuális gépekhez, mivel ezek a nem megőrzött meghajtók átviteli sebességű, hogy a virtuálisgép-család. További részletekért olvassa el [az Ideiglenes meghajtó ismertetése windowsos virtuális gépeken az Azure-ban című cikket.](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> D meghajtó:\ az Azure virtuális gép egy nem védett meghajtó, amely az Azure számítási csomópont néhány helyi lemezei által támogatott. Mivel nem tartós, ez azt jelenti, hogy a D:\ meghajtó elvész, amikor a virtuális gép újraindul. A "változások", mint a tárolt fájlok, könyvtárak létre, telepített alkalmazások, stb
> 
> ![Linux][Logo_Linux] Linux
> 
> A Linux Azure virtuális gépek automatikusan csatlakoztatnak egy meghajtót a /mnt/resource, amely egy nem védett meghajtó által támogatott helyi lemezek az Azure számítási csomóponton. Mivel nem védett, ez azt jelenti, hogy a /mnt/resource tartalom módosításai elvesznek a virtuális gép újraindításakor. Bármilyen változás, mint a tárolt fájlok, könyvtárak létre, telepített alkalmazások, stb
> 
> 

---

A Microsoft Azure Storage a megőrzött tárhelyet, valamint a SAN-tárolókon látható tipikus védelmi és redundanciaszinteket biztosítja. Az Azure Storage-on alapuló lemezek virtuális merevlemez (VHD) az Azure Storage Services-ben található. A helyi operációsrendszer-lemez\, (Windows C: Linux /dev/sda1) az Azure Storage tárolja, és a virtuális géphez csatlakoztatott további kötetek/lemezek is tárolják.

Lehetőség van egy meglévő virtuális merevlemez feltöltésére a helyszíni, vagy hozzon létre üres eket az Azure-on belül, és csatolja ezeket a virtuális gépeküzembe helyezett virtuális gépekhez.

Virtuális merevlemez létrehozása vagy feltöltése után az Azure Storage-ba, lehetőség van csatlakoztatni, és csatolja azokat egy meglévő virtuális géphez, és másolja a meglévő (nem csatlakoztatott) virtuális merevlemez.

Mivel ezek a virtuális gépek megőrződnek, a virtuális gép példányainak újraindítása és újbóli létrehozása során a virtuális gép példányain belüli adatok és módosítások biztonságosak. Még akkor is, ha egy példány törlődik, ezek a virtuális merevlemezek biztonságban maradnak, és újratelepíthetők, vagy nem operációs rendszerlemezek más virtuális gépekhez csatlakoztathatók.

Az Azure Storage szolgáltatásról további információt itt talál:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Az Azure Standard storage az Azure IaaS megjelenésekor elérhető tárterület típusa volt. Egyetlen lemezen érvényesítve iOPS-kvóták voltak kényszerítve. A tapasztalt késés nem volt ugyanabban az osztályban, mint a SAN/NAS eszközök általában telepített csúcskategóriás SAP-rendszerek üzemeltetett helyszíni. Mindazonáltal az Azure Standard Storage elegendőnek bizonyult több száz SAP-rendszer számára, amelyeket eközben telepítettek az Azure-ban.

Az Azure Standard Storage-fiókokban tárolt lemezek után a ténylegesen tárolt adatok, a tárolási tranzakciók mennyisége, a kimenő adatátvitel és a redundancia beállítás választott alapján számítunk fel díjat. Sok lemez hozható létre a maximális 1 TB méretű, de amíg azok üresek maradnak, nincs díj. Ha ezután kitölt egy virtuális merevlemezt 100 GB-os mindegyikel, akkor a 100 GB-os tárolásért kell fizetnie, és nem a virtuális merevlemez által létrehozott névleges méretért.

#### <a name="azure-premium-storage"></a><a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure prémium szintű tárhely
Az Azure Premium Storage azzal a céllal került bevezetésre, hogy:

* Jobb I/O késleltetés.
* Jobb átmenő.
* Kisebb változékonyság az I/O késleltetésben.

E célból számos változtatást vezettek be, amelyek közül a két legjelentősebb a következő:

* SSD-lemezek használata az Azure Storage-csomópontokban
* Egy azure-számítási csomópont helyi SSD-je által támogatott új olvasási gyorsítótár

A standard szintű tárterülettel ellentétben, ahol a képességek nem változtak a lemez méretétől (vagy a virtuális merevlemeztől), a Prémium szintű storage jelenleg három különböző lemezkategóriával rendelkezik, amelyek ebben a cikkben láthatók:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Láthatja, hogy az IOPS/lemez és a lemez átviteli/lemezmérete a lemezek méretkategóriájától függ

A prémium szintű tárolás esetében a költségalap nem az ilyen lemezeken tárolt tényleges adatkötet, hanem az ilyen lemez méretkategóriája, függetlenül a lemezen tárolt adatok mennyiségétől.

Olyan lemezeket is létrehozhat a prémium szintű storage-ban, amelyek nem közvetlenül vannak leképezve a megjelenített méretkategóriákba. Ez a helyzet akkor fordulhat elő, ha lemezeket másol a standard szintű tárból a prémium szintű tárba. Ilyen esetekben a következő legnagyobb prémium szintű storage lemezbeállítás leképezése történik.

Az SAP-val hitelesített Azure virtuálisgép-családok többsége képes együttműködni a Prémium szintű storage-mal és az Azure standard és a Prémium szintű storage keverékével.

Ha ebben a [cikkben (Linux)][virtual-machines-sizes-linux] és ebben a [cikkben (Windows)][virtual-machines-sizes-windows]a DS-sorozatú virtuális gépek részét veszi ki, akkor rájössz, hogy a virtuális gép szintjén a prémium szintű storage-lemezek adatkötet-korlátozások vannak érvényben. A különböző DS-sorozatú vagy GS-sorozatú virtuális gépek is eltérő korlátozásokat az adatlemezek, amelyek csatlakoztathatók száma. Ezeket a korlátokat a fent említett cikk is dokumentálja. De lényegében ez azt jelenti, hogy ha például 32 x P30 lemezeket csatlakoztat egyetlen DS14 virtuális géphez, akkor nem kaphat 32 x maximális átviteli értéket egy P30 lemez. Ehelyett a virtuális gép szintjén a cikkben dokumentált maximális átviteli teljesítmény korlátozza az adatátviteli forgalmat.

További információ a Prémium Storage-ról itt található:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure storage-fiókok

Szolgáltatások vagy virtuális gépek azure-beli üzembe helyezésekor a VHD-k és a virtuálisgép-lemezképek üzembe helyezése azure storage-fiókok nak nevezett egységekben is rendszerezhető. Az Azure-telepítés tervezésekor alaposan meg kell fontolnia az Azure-korlátozásokat. Az egyik oldalon van egy korlátozott számú tárfiókok Azure-előfizetésenként. Bár minden Egyes Azure Storage-fiók nagy számú virtuális merevlemez-fájlt tárolhat, a storage-fiókonkénti teljes IOPS-csomag rögzített korláttal rendelkezik. Ha több száz SAP virtuális gépet telepít jelentős I/O-hívásokat létrehozó DBMS-rendszerekkel, ajánlott magas IOPS-rendszerű DBMS-virtuális gépeket terjeszteni több Azure Storage-fiók között. Ügyelni kell arra, hogy ne lépje túl az Azure Storage-fiókok előfizetésenkénti jelenlegi korlátját. Mivel a tárolás az SAP-rendszer adatbázis-központi telepítésének létfontosságú része, ezt a fogalmat a már hivatkozott [DBMS-telepítési útmutató][dbms-guide]ismerteti részletesebben.

Az Azure Storage-fiókokról további információt a [normál tárfiókok méretezhetőségi céljai](../../../storage/common/scalability-targets-standard-account.md) és [a prémium szintű lapblobstorage-fiókok méretezhetőségi céljai](../../../storage/blobs/scalability-targets-premium-page-blobs.md)tartalmaznak. Ezek a cikkek olvasása, rájössz, hogy vannak különbségek az Azure standard szintű storage-fiókok és a prémium szintű storage-fiókok közötti korlátozások. Jelentős különbségek vannak az ilyen tárfiókban tárolható adatok mennyisége. A standard storage-ban a kötet nagyságrenddel nagyobb, mint a prémium szintű storage. A másik oldalon a standard tárfiók szigorúan korlátozott az IOPS-ban (lásd a **Teljes kérelemdíj oszlopot),** míg az Azure Premium Storage-fiók nem rendelkezik ilyen korlátozással. Ezeket a különbségeket az SAP-rendszerek, különösen a DBMS-kiszolgálók központi telepítéseinek megvitatása során tárgyaljuk.

Egy tárfiókon belül lehetősége van különböző tárolók létrehozására a különböző Virtuális gépek szervezése és kategorizálása céljából. Ezek a tárolók, például a különböző virtuális gépek különálló Virtuálisszámítógép-azonosítók külön. Nincsenek teljesítménybeli következmények egyetlen Azure Storage-fiók alatt csak egy tároló vagy több tároló használatával.

Az Azure-on belül a VHD-név a következő elnevezési kapcsolatot követi, amelynek egyedi nevet kell megadnia a virtuális merevlemeznek az Azure-on belül:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

A fenti karakterlánc nak egyedileg azonosítania kell az Azure Storage-ban tárolt virtuális merevlemezt.

#### <a name="managed-disks"></a><a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Felügyelt lemezek

A felügyelt lemezek egy új erőforrástípus az Azure Resource Managerben, amely az Azure Storage-fiókokban tárolt virtuális merevlemezek helyett használható. A felügyelt lemezek automatikusan igazodnak a virtuális gép rendelkezésre állási készletéhez, amelyhez kapcsolódnak, és ezáltal növelik a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állását. További információkért olvassa el az [áttekintő cikket.](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)

Javasoljuk, hogy felügyelt lemezt használjon, mert ezek leegyszerűsítik a virtuális gépek üzembe helyezését és felügyeletét.
Az SAP jelenleg csak a prémium szintű felügyelt lemezeket támogatja. További információ: SAP Note [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>A Microsoft Azure Storage rugalmassága

A Microsoft Azure Storage tárolja az alap virtuális merevlemez (operációs rendszerrel) és a csatlakoztatott lemezek vagy BLOB-k legalább három külön tárolócsomópontokon tárolja. Ezt a tényt helyi redundáns tárolásnak (LRS) nevezik. Az LRS az Azure-beli összes tárterület alapértelmezett beállítása. 

Számos további redundancia-módszer létezik, amelyek mindegyike az [Azure Storage replikációs](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)cikkében ismertet.

> [!NOTE]
>Az Azure Premium Storage, amely az adatbázis- és napló-/redo fájlokat tároló lemezek és lemezek ajánlott tárolási típusa, az egyetlen elérhető módszer az LRS. Ennek eredményeképpen konfigurálnia kell az adatbázis-módszereket, például az SQL Server Always On, az Oracle Data Guard vagy a HANA system replication adatbázis-replikációt egy másik Azure-régióba vagy egy másik Azure rendelkezésre állási zónába.


> [!NOTE]
> A DBMS-telepítések esetében a georedundáns tárolás azure standard szintű storage-ban elérhető használata nem ajánlott, mivel súlyos teljesítménybeli hatással jár, és nem tartja tiszteletben az írási sorrendet a virtuális géphez csatlakoztatott különböző virtuális gépek között. Az a tény, hogy nem tartja tiszteletben az írási sorrendet a különböző virtuális gépek között nagy potenciállal rendelkezik, hogy a replikációs céloldalon inkonzisztens adatbázisokban végződjön, ha az adatbázis és a napló/ismétlés i. fájlok több Virtuálisgép-számítógépen (főként ebben az esetben) vannak elosztva.


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure hálózatkezelés

A Microsoft Azure egy hálózati infrastruktúrát biztosít, amely lehetővé teszi az összes forgatókönyv leképezését, amelyet az SAP szoftverekkel szeretnénk megvalósítani. A képességek a következők:

* Hozzáférés kívülről, közvetlenül a virtuális gépekhez a Windows terminálszolgáltatásokon vagy az ssh/VNC-n keresztül
* Hozzáférés a virtuális gépeken belüli alkalmazások által használt szolgáltatásokhoz és portokhoz
* Belső kommunikáció és névfeloldás az Azure virtuális gépekként üzembe helyezett virtuális gépek csoportja között
* Az ügyfél helyszíni hálózata és az Azure-hálózat közötti, telephelyen átnyúló kapcsolat
* Az Azure-régió vagy az adatközpontok közötti kapcsolat az Azure-webhelyek között

További információt itt találhat: <https://azure.microsoft.com/documentation/services/virtual-network/>

Az Azure-ban számos különböző lehetőség van a név- és IP-felbontás konfigurálására. Van egy Azure DNS-szolgáltatás is, amely a saját DNS-kiszolgáló beállítása helyett használható. További információ található [ebben][virtual-networks-manage-dns-in-vnet] a cikkben, és ezen az [oldalon](https://azure.microsoft.com/services/dns/).

A létesítmények közötti vagy hibrid forgatókönyvek, támaszkodunk arra a tényre, hogy a helyszíni AD/OpenLDAP/DNS-t vpn-en vagy privát kapcsolaton keresztül az Azure-hoz. Bizonyos forgatókönyvek itt dokumentált, szükség lehet egy AD/OpenLDAP replika telepítve van az Azure-ban.

Mivel a hálózatkezelés és a névfeloldás az SAP-rendszer adatbázis-telepítésének létfontosságú része, ezt a fogalmat a DBMS telepítési útmutató részletesebben [tárgyalja.][dbms-guide]

##### <a name="azure-virtual-networks"></a>Azure virtuális hálózatok

Az Azure virtuális hálózat kiépítésével megadhatja az Azure DHCP-funkciók által lefoglalt privát IP-címek címtartományát. A létesítmények közötti forgatókönyvek, a megadott IP-címtartomány továbbra is levan foglalva az Azure DHCP használatával. A tartománynév feloldása azonban a helyszínen történik (feltételezve, hogy a virtuális gépek egy helyszíni tartomány részét képezik), és így a különböző Azure-felhőszolgáltatásokon kívüli címeket is feloldhatja.

Az Azure-ban minden virtuális gépnek csatlakoznia kell egy virtuális hálózathoz.

További részletek találhatók ebben a [cikkben][resource-groups-networking] és [ezen az oldalon](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Alapértelmezés szerint a virtuális gép telepítése után nem módosíthatja a virtuális hálózat konfigurációját. A TCP/IP-beállításokat az Azure DHCP-kiszolgálóra kell hagyni. Az alapértelmezett viselkedés a dinamikus IP-hozzárendelés.
>
>

A virtuális hálózati kártya MAC-címe változhat, például az átméretezés után, és a Windows vagy Linux vendég operációs rendszer felveszi az új hálózati kártyát, és ebben az esetben automatikusan DHCP-t használ az IP- és DNS-címek hozzárendeléséhez.

##### <a name="static-ip-assignment"></a>Statikus IP-hozzárendelés
Az Azure virtuális hálózaton belül rögzített vagy fenntartott IP-címek hozzárendelése lehetséges. A virtuális gépek futtatása egy Azure virtuális hálózaton megnyílik egy nagyszerű lehetőség, hogy kihasználja ezt a funkciót, ha szükséges, vagy szükséges bizonyos forgatókönyvek. Az IP-hozzárendelés a virtuális gép teljes időtartama alatt érvényes marad, függetlenül attól, hogy a virtuális gép fut-e vagy leáll.The IP assignment remains valid throughout the existence of the VM, függetlenül attól, hogy a virtuális gép fut vagy leáll. Ennek eredményeképpen figyelembe kell venni a virtuális gépek (futó és leállított virtuális gépek) teljes számát a virtuális hálózat IP-címtartományának meghatározásakor. Az IP-cím a virtuális gép és a hálózati adapter törléséig vagy az IP-cím újra kinem nem kerül hozzárendeléséig. További információkért olvassa el [ezt a cikket][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Statikus IP-címeket kell hozzárendelni e-n keresztül az Azure-eszközök az egyes virtuális adapterek. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy virtuális hálózati adapterhez. Egyes Azure-szolgáltatások, például az Azure backup szolgáltatás támaszkodnak arra a tényre, hogy legalább az elsődleges virtuális adapter van beállítva, hogy DHCP, és nem statikus IP-címek. Lásd még a dokumentum [hibaelhárítása Azure virtuális gép biztonsági mentés .](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)
>
>

##### <a name="multiple-nics-per-vm"></a>Virtuális számítógépenként több hálózati adapter

Egy Azure virtuális géphez több virtuális hálózati csatolókártyát (vNIC) is definiálhat. Azzal a képességgel, hogy több vNIC-k megkezdheti a hálózati forgalom elkülönítését, ahol például az ügyfélforgalom egy virtuális adapteren keresztül, és a háttérforgalom egy második vNIC-n keresztül történik. A virtuális gép típusától függően a virtuális gép által hozzárendelhető virtuális gépek száma különböző korlátozásokat rendelhet. A pontos részletek, a funkciók és a korlátozások a következő cikkekben találhatók:

* [Több hálózati adapterrel rendelkező Windows virtuális gép létrehozása][virtual-networks-multiple-nics-windows]
* [Linuxos virtuális gép létrehozása több hálózati adapterrel][virtual-networks-multiple-nics-linux]
* [Több hálózati adapterek telepítése sablon használatával][virtual-network-deploy-multinic-arm-template]
* [Több hálózati adapterek telepítése a PowerShell használatával][virtual-network-deploy-multinic-arm-ps]
* [Több hálózati szolgáltatású virtuális gépek üzembe helyezése az Azure CLI használatával][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Helyek közötti kapcsolat

A telephelyek közötti azure-beli virtuális gépek és a helyszíni, átlátható és állandó VPN-kapcsolattal összekapcsolt helyszíni gépek. Várhatóan az Azure-ban a leggyakoribb SAP-telepítési minta lesz. A feltételezés az, hogy az Azure-beli SAP-példányokkal végzett működési eljárásoknak és folyamatoknak átláthatóan kell működniük. Ez azt jelenti, hogy képesnek kell lennie arra, hogy nyomtassa ki ezeket a rendszereket, valamint az SAP Transport Management System (TMS) a változások átvitele az Azure-ban egy tesztrendszer, amely a helyszínen üzembe helyezett. További dokumentáció körül site-to-site megtalálható ebben a [cikkben][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-alagút eszköz

Ahhoz, hogy helyközi kapcsolatot (helyszíni adatközpontot az Azure-adatközponttal) hozzon létre, be kell szereznie és konfigurálnia kell egy VPN-eszközt, vagy használnia kell az Útválasztás és távelérés szolgáltatást (RRAS), amelyet a Windows Server 2012-vel szoftverösszetevőként vezettek be.

* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a PowerShell használatával][vpn-gateway-create-site-to-site-rm-powershell]
* [Információk a helyek közötti VPN Gateway-kapcsolatok VPN-eszközeiről][vpn-gateway-about-vpn-devices]
* [VPN Gateway – gyakori kérdések][vpn-gateway-vpn-faq]

![Helyek közötti kapcsolat a helyszíni és az Azure között][planning-guide-figure-600]

A fenti ábra két Azure-előfizetés ip-cím altartományt mutat az Azure-beli virtuális hálózatokban való használatra fenntartva. A helyszíni hálózat és az Azure közötti kapcsolat VPN-en keresztül jön létre.

#### <a name="point-to-site-vpn"></a>Pont–hely VPN

A pont-hely VPN használatához minden ügyfélgépnek csatlakoznia kell a saját VPN-jéhez az Azure-ba. Az SAP-forgatókönyvek, azt vizsgáljuk, pont-hely kapcsolat nem praktikus. Ezért a pont-hely VPN-kapcsolatra nem hivatkoznak további hivatkozások.

További információt itt találhat
* [Pont-hely kapcsolat konfigurálása virtuális hálózattal az Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Többhelyes VPN

Az Azure napjainkban is lehetőséget kínál többhelyes VPN-kapcsolat létrehozására egyetlen Azure-előfizetéshez. Korábban egyetlen előfizetés egyetlen helyről helyekre szóló VPN-kapcsolatra korlátozódott. Ez a korlátozás egyetlen előfizetés többhelyű VPN-kapcsolatokkal ment el. Ez lehetővé teszi, hogy egynél több Azure-régió egy adott előfizetés terepen konfigurációk on keresztül.

További dokumentációebben ebben [a cikkben][vpn-gateway-create-site-to-site-rm-powershell] olvashat.

#### <a name="vnet-to-vnet-connection"></a>Virtuális hálózat és virtuális hálózat kapcsolata

Többhelyes VPN használatával minden régióban külön Azure virtuális hálózatot kell konfigurálnia. Azonban gyakran van az a követelmény, hogy a különböző régiókban lévő szoftverösszetevők kommunikáljanak egymással. Ideális esetben ezt a kommunikációt nem kell átirányítani az egyik Azure-régióból a helyszíni és onnan a másik Azure-régióba. A parancsikon, az Azure lehetőséget kínál a kapcsolat konfigurálása az egyik régióban az egyik régióban egy másik Azure virtuális hálózat üzemeltetett egy másik régióban. Ezt a funkciót virtuális hálózat és virtuális hálózat kapcsolatának nevezik. További részletek erről a funkcióról <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>itt található: .

#### <a name="private-connection-to-azure-expressroute"></a>Privát kapcsolat az Azure ExpressRoute-hoz

A Microsoft Azure ExpressRoute lehetővé teszi az Azure-adatközpontok és az ügyfél helyszíni infrastruktúrája vagy egy helymegosztási környezet közötti privát kapcsolatok létrehozását. Az ExpressRoute-ot különböző MPLS (csomagkapcsolt) VPN-szolgáltatók vagy más hálózati szolgáltatók kínálják. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Az ExpressRoute-kapcsolatok nagyobb biztonságot, nagyobb megbízhatóságot és nagyobb megbízhatóságot kínálnak több párhuzamos kapcsolaton keresztül, gyorsabb sebességet és kisebb késést biztosítanak, mint az interneten keresztül isznak.

Az Azure ExpressRoute-ról és az ajánlatokról itt olvashat bővebben:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Az Express Route több Azure-előfizetést is lehetővé tesz egyetlen ExpressRoute-kapcsolaton keresztül, ahogy az itt dokumentálva van.

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Kényszeralagút telephelyek közötti
A helyszíni tartományokat a helyek közötti, a pont-hely vagy az ExpressRoute között csatlakozó virtuális gépek esetében meg kell győződnie arról, hogy az internetes proxybeállítások a virtuális gépek összes felhasználója számára is üzembe kerülnek. Alapértelmezés szerint a virtuális gépeken futó szoftverek vagy a böngészőt használó felhasználók nem megy keresztül a vállalati proxy, de közvetlenül az Azure-on keresztül csatlakozik az internethez. De egyenletes a helyettes elintézés van nem egy 100% oldat -hoz közvetlen a forgalom átmenő a társaság helyettes óta ez felelősség -ból szoftver és szolgáltatás -hoz ellenőriz részére a helyettes. Ha a virtuális gépen futó szoftver nem teszi ezt meg, vagy a rendszergazda manipulálja a beállításokat, az internetre irányuló forgalom ismét közvetlenül az Azure-on keresztül az internetre kerülendő.

Az ilyen közvetlen internetkapcsolat elkerülése érdekében konfigurálhatja a kényszerített bújtatást a helyszíni és az Azure közötti helyek közötti kapcsolattal. A kényszeralagút szolgáltatás részletes leírása itt jelenik meg.<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

A kényszerített bújtatás expressroute-i kapcsolatfelvételre az ExpressRoute BGP-társviszony-létesítési munkameneteken keresztül alapértelmezett útvonalat hirdető ügyfelek számára engedélyezett.

#### <a name="summary-of-azure-networking"></a>Az Azure Networking összefoglalása

Ez a fejezet számos fontos pontot tartalmazott az Azure Networking ről. Itt van egy összefoglaló a főbb pontok:

* Az Azure virtuális hálózatok lehetővé teszik, hogy egy hálózati struktúra az Azure-telepítés. Virtuális hálózatok egymástól elkülöníthetők, vagy a virtuális hálózatok közötti hálózati biztonsági csoportok közötti forgalom vezérelhető.
* Az Azure virtuális hálózatok kihasználva ip-címtartományokat rendelhetnek a virtuális gépekhez, vagy rögzített IP-címeket rendelhetnek a virtuális gépekhez
* A helyek közötti vagy a helyek közötti kapcsolat létrehozásához először létre kell hoznia egy Azure virtuális hálózatot
* A virtuális gép üzembe helyezése után már nem lehet módosítani a virtuális géphez rendelt virtuális hálózatot.

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvóták az Azure virtuálisgép-szolgáltatásokban
Tisztában kell lennünk azzal a ténnyel, hogy a tárolási és hálózati infrastruktúra meg van osztva az Azure-infrastruktúrában különböző szolgáltatásokat futtató virtuális gépek között. És csakúgy, mint az ügyfél saját adatközpontok, túlzott kiépítése egyes infrastrukturális erőforrások nem kerül sor bizonyos mértékig. A Microsoft Azure platform lemez-, processzor-, hálózati és egyéb kvótákat használ az erőforrás-felhasználás korlátozására, valamint a konzisztens és determinisztikus teljesítmény megőrzésére.  A különböző virtuálisgép-típusok (A5, A6 stb.) különböző kvótákkal rendelkeznek a lemezek, a PROCESSZOR, a RAM és a hálózat számára.

> [!NOTE]
> Az SAP által támogatott virtuálisgép-típusok processzor- és memória-erőforrásai előre le vannak foglalva a gazdacsomópontokon. Ez azt jelenti, hogy a virtuális gép üzembe helyezése után a gazdagép erőforrásai a virtuális gép típusa által meghatározott érhetők el.
>
>

Az SAP tervezésekor és méretezésekor az Azure-megoldások, a kvóták minden virtuális gép mérete figyelembe kell venni. A virtuális gép kvóták [itt (Linux)][virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A leírt kvóták az elméleti maximális értékeket jelentik.  A lemezenkénti IOPS-korlát kis IOs (8kb) esetén érhető el, de nagy IOs (1Mb) esetén azonban valószínűleg nem érhető el.  Az IOPS-korlát egyetlen lemez részletességén érvényesítve van.

Egy durva döntési fa annak eldöntéséhez, hogy egy SAP-rendszer illeszkedik-e az Azure virtuálisgép-szolgáltatásokba és annak képességeibe, vagy hogy egy meglévő rendszert másképp kell-e konfigurálni a rendszer Azure-on történő üzembe helyezéséhez, az alábbi döntési fa használható:

![Döntési fa az SAP Azure-beli üzembe helyezésének lehetőségének eldöntéséhez][planning-guide-figure-700]

**1. lépés:** A legfontosabb információ kezdeni az SAPS követelmény egy adott SAP-rendszer. Az SAPS-követelményeket el kell különíteni a DBMS-rész és az SAP-alkalmazás rész, még akkor is, ha az SAP-rendszer már telepítve van a helyszínen egy kétrétegű konfigurációban. A meglévő rendszerek esetében a használt hardverhez kapcsolódó SAPS gyakran meghatározható vagy megbecsülhető a meglévő SAP-referenciaértékek alapján. Az eredmények itt találhatók: <https://sap.com/about/benchmark.html>.
Az újonnan üzembe helyezett SAP-rendszerek, át kellett volna mennie egy méretezési gyakorlat, amely nek meg kell határoznia a rendszer SAPS-követelményeit.
Tekintse meg ezt a blogot és a csatolt dokumentumot az Azure-beli SAP-méretezéshez:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2. lépés:** Meglévő rendszerek esetén meg kell mérni az I/O-kötetet és az I/O-műveleteket másodpercenként a DBMS-kiszolgálón. Az újonnan tervezett rendszerek esetében az új rendszer méretezési gyakorlatának durva ötleteket kell adnia az I/O követelményekről a DBMS oldalon. Ha nem biztos benne, akkor végül kell, hogy végezzen a Proof of Concept.

**3. lépés:** Hasonlítsa össze az SAPS-követelmény a DBMS-kiszolgáló az SAPS a különböző virtuálisgép-típusok az Azure nyújthat. A különböző Azure-virtuálisgép-típusok SAPS-adatai az SAP [Note 1928533-ban]vannak dokumentálva. A hangsúly kell a DBMS virtuális gép először, mivel az adatbázis-réteg a réteg egy SAP NetWeaver rendszer, amely nem horizontális felskálázás a legtöbb központi telepítések. Ezzel szemben az SAP alkalmazásréteg kicsinyített. Ha az SAP által támogatott Azure virtuálisgép-típusok egyike sem tudja biztosítani a szükséges SAPS-t, a tervezett SAP-rendszer munkaterhelése nem futtatható az Azure-on. Vagy telepítenie kell a rendszert a helyszínen, vagy módosítania kell a rendszer számítási feladatának mennyiségét.

**4. lépés:** A dokumentált [itt (Linux)][virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows]az Azure kényszeríti az IOPS-kvóta lemezenként független, hogy a Standard Storage vagy a Prémium szintű storage használata. A virtuális gép típusától függően az adatlemezek száma, amely csatlakoztatható, változó. Ennek eredményeképpen kiszámíthatja a maximális IOPS-számot, amely a különböző virtuálisgép-típusok mindegyikével elérhető. Az adatbázisfájl elrendezésétől függően a lemezsávok csíkozhatók, hogy a vendég operációs rendszerben egy kötetlegyen. Azonban ha az üzembe helyezett SAP-rendszer aktuális IOPS-kötete meghaladja a legnagyobb virtuálisgép-típusú Azure számított korlátait, és ha nincs esély a több memóriával való kompenzálására, az SAP-rendszer munkaterhelése súlyosan hatással lehet. Ilyen esetekben megnyomhatja azt a pontot, ahol nem kell telepítenie a rendszert az Azure-ban.

**5. lépés:** Különösen az SAP-rendszerek, amelyek a helyszínen üzembe helyezett kétrétegű konfigurációk, az esélye, hogy a rendszer lehet konfigurálni az Azure-ban egy 3 rétegű konfigurációban. Ebben a lépésben ellenőriznie kell, hogy van-e egy összetevő az SAP-alkalmazásrétegben, amely nem lehet horizontálisan kiskálázni, és amely nem fér bele a különböző Azure-virtuálisgép-típusok által kínált PROCESSZOR- és memória-erőforrásokba. Ha valóban van ilyen összetevő, az SAP-rendszer és a számítási feladatok nem telepíthetők az Azure-ba. De ha az SAP-alkalmazás-összetevőket több Azure-beli virtuális gépre is kitudja bővíteni, a rendszer telepíthető az Azure-ba.

**6. lépés:** Ha a DBMS és az SAP alkalmazásréteg-összetevők futtathatók az Azure virtuális gépeken, a konfigurációt a következők tekintetében kell meghatározni:

* Az Azure virtuális gépek száma
* Virtuálisgép-típusok az egyes összetevőkhöz
* A virtuális gépek száma a DBMS virtuális gépben, hogy elegendő IOPS-t biztosítson

## <a name="managing-azure-assets"></a>Azure-eszközök kezelése

### <a name="azure-portal"></a>Azure portál

Az Azure Portal az Azure virtuális gép központi telepítéseikezelésének három felületének egyike. Az alapvető felügyeleti feladatok, például a virtuális gépek telepítése a rendszerképekről, az Azure Portalon keresztül elvégezhetők. Emellett a storage-fiókok, a virtuális hálózatok és más Azure-összetevők létrehozása is olyan feladatok, amelyeket az Azure Portal jól tud kezelni. Azonban a funkciók, mint a virtuális merevlemezek feltöltése a helyszíni Azure-ba, vagy egy virtuális merevlemez másolása az Azure-on belül olyan feladatok, amelyek megkövetelik a külső eszközök vagy felügyelet a PowerShell vagy a CLI.

![Microsoft Azure portal – virtuális gép – áttekintés][planning-guide-figure-800]


Felügyeleti és konfigurációs feladatok a virtuális gép példány a virtual machine-portálon belül lehetséges.

A virtuális gép újraindítása és leállítása mellett a virtuálisgép-példány hoz létre adatlemezeket is csatolhat, leválaszthat és létrehozhat a lemezkép-előkészítéshez, és konfigurálhatja a virtuálisgép-példány méretét.

Az Azure Portal alapvető funkciókat biztosít a virtuális gépek és számos más Azure-szolgáltatás üzembe helyezéséhez és konfigurálásához. Azonban nem minden elérhető funkció az Azure Portal hatálya alá tartozik. Az Azure Portalon nem lehet olyan feladatokat végrehajtani, mint például:

* Virtuális gépek feltöltése az Azure-ba
* Virtuális gépek másolása


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Kezelés a Microsoft Azure PowerShell-parancsmagokon keresztül

A Windows PowerShell egy hatékony és bővíthető keretrendszer, amelyet az Azure-ban nagyobb számú rendszert üzembe helyező ügyfelek széles körben alkalmaznak. A PowerShell-parancsmagok asztali, hordozható vagy dedikált felügyeleti állomásra történő telepítése után a PowerShell-parancsmagok távolról is futtathatók.

Ebben a [cikkben][powershell-install-configure]ismertetjük azt a folyamatot, amely lehetővé teszi a helyi asztali és hordozható számítógépek használatát az Azure PowerShell-parancsmagok használatához, és hogy miként konfigurálhatja azokat az Azure-előfizetés(ek)kel való használatra.

Az Azure PowerShell-parancsmagok telepítésének, frissítésének és konfigurálásának részletesebb lépései [a Telepítési útmutató ebben a fejezetében][deployment-guide-4.1]találhatók.

Az ügyfélélmény eddig az volt, hogy a PowerShell (PS) minden bizonnyal a hatékonyabb eszköz a virtuális gépek üzembe helyezéséhez és egyéni lépések létrehozásához a virtuális gépek üzembe helyezéséhez. Az Azure-ban SAP-példányokat futtató összes ügyfél PS-parancsmagokkal egészíti ki az Azure Portalon végzett felügyeleti feladatokat, vagy akár csak ps-parancsmagokat is használ az Azure-beli telepítésük kezelésére. Mivel az Azure-specifikus parancsmagok ugyanazt az elnevezési konvenciót használják, mint a több mint 2000 Windows-vonatkozású parancsmag, a Windows-rendszergazdák számára egyszerű feladat, hogy kihasználják ezeket a parancsmagokat.

Lásd a példát itt:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Az Azure Extension for SAP üzembe helyezése (lásd az [Azure Extension for SAP][planning-guide-9.1] ebben a dokumentumban) csak a PowerShell vagy a CLI használatával lehetséges. Ezért kötelező beállítani és konfigurálni a PowerShell vagy a CLI telepítésekor vagy felügyelete az SAP NetWeaver rendszer az Azure-ban.  

Mivel az Azure több funkciót biztosít, új PS-parancsmagok kerülnek hozzáadásra, amely a parancsmagok frissítését igényli. Ezért célszerű, hogy ellenőrizze az Azure letöltési <https://azure.microsoft.com/downloads/> hely legalább havonta egyszer a parancsmagok új verzióját. Az új verzió a régebbi verzió tetejére van telepítve.

Az Azure-ral kapcsolatos PowerShell-parancsok általános <https://docs.microsoft.com/powershell/azure/overview>listáját itt ellenőrizheti: .

### <a name="management-via-microsoft-azure-cli-commands"></a>Kezelés a Microsoft Azure CLI parancsain keresztül

A Linuxot használó és az Azure-erőforrásokat kezelni kívánó ügyfelek számára előfordulhat, hogy a PowerShell nem választható. A Microsoft alternatívaként kínálja az Azure CLI-t.
Az Azure CLI nyílt forráskódú, platformfüggetlen parancsokat biztosít az Azure Platformmal való együttműködéshez. Az Azure CLI az Azure Portalon található funkciók nagy részét biztosítja.

A telepítésről, a konfigurációról és a CLI-parancsok Azure-feladatok elvégzéséhez való használatáról a

* [Az Azure klasszikus parancssori felületének telepítése][xplat-cli]
* [Virtuális gépek telepítése és kezelése az Azure Resource Manager-sablonok és az Azure CLI használatával] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Az Azure klasszikus CLI használata Mac, Linux és Windows rendszerhez az Azure Resource Manager rel][xplat-cli-azure-resource-manager]

Olvassa el az [Azure CLI linuxos virtuális gépekhez][deployment-guide-4.5.2] című fejezetet is a [telepítési útmutatóban arról,][planning-guide] hogyan használhatja az Azure CLI-t az Azure-bővítmény SAP-hoz való üzembe helyezéséhez.


## <a name="first-steps-planning-a-deployment"></a>Telepítés tervezésének első lépései
Az első lépés a központi telepítés tervezése nem ellenőrzi a virtuális gépek sap futtatásához elérhető. Az első lépés lehet az egyik, hogy időigényes, de a legfontosabb, hogy működjön együtt a megfelelőségi és biztonsági csapatok a vállalatnál, hogy milyen a határfeltételek üzembe helyezéséhez, hogy milyen típusú SAP számítási feladatok vagy üzleti folyamat nyilvános felhőbe. Ha vállalata korábban más szoftvereket telepített az Azure-ba, a folyamat egyszerű lehet. Ha a vállalat több az utazás elején, előfordulhat, hogy nagyobb megbeszélésekre van szükség annak érdekében, hogy kitaláljuk, a határfeltételek, biztonsági feltételek, amelyek lehetővé teszik bizonyos SAP-adatok és az SAP üzleti folyamatok üzemeltetése nyilvános felhőben.

Hasznos segítségként a [Microsoft megfelelőségi ajánlataira](https://docs.microsoft.com/microsoft-365/compliance/offering-home) mutathat a Microsoft által nyújtott megfelelőségi ajánlatok listájához. 

Az [azure-beli titkosításáttekintés](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview)e területen más, az adatok titkosítása az inaktív adatokhoz vagy más titkosításhoz.

Ne becsülje alá a projekt nek ezt a fázisát a tervezés során. Csak akkor, ha a szerződés és a szabályok körül ebben a témakörben, meg kell tolnia a következő lépés, amely a hálózati architektúra, amely az Azure-ban üzembe helyezett terv.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Az SAP-hoz való virtuális gépek üzembe helyezésének különböző módjai az Azure-ban

Ebben a fejezetben megtudhatja, hogy milyen különböző módon telepítheti a virtuális gépeket az Azure-ban. További előkészítési eljárások, valamint a virtuális gépek és virtuális gépek azure-ban kezeli az ebben a fejezetben.

### <a name="deployment-of-vms-for-sap"></a>Virtuális gépek telepítése az SAP-hoz

A Microsoft Azure számos lehetőséget kínál a virtuális gépek és a kapcsolódó lemezek központi telepítésére. Ezért fontos megérteni a különbségeket, mivel a virtuális gépek előkészületei a központi telepítés módjától függően eltérőek lehetnek. Általánosságban a következő forgatókönyveket tekintjük meg:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Virtuális gép áthelyezése a helyszíni környezetből az Azure-ba egy nem általános lemezzel

Azt tervezi, hogy egy adott SAP-rendszer áthelyezése a helyszíni Azure-ba. Ez az operációs rendszert, az SAP bináris fájlokat és a DBMS bináris fájlokat, valamint a Virtuális merevlemezeket tartalmazó virtuális merevlemezek feltöltésével teheti meg az Azure-ba. Az [alábbi forgatókönyv #2vel][planning-guide-5.1.2]ellentétben az állomásnév, az SAP-sid és az SAP felhasználói fiókok at az Azure virtuális gép, a helyszíni környezetben konfigurálva. Ezért a kép általánosítása nem szükséges. Lásd: [A virtuális gépek helyszíni és Azure-ból azure-ba való áthelyezésének előkészítése][planning-guide-5.2.1] a dokumentum nem általános lemezével című fejezeteket a helyszíni előkészítési lépésekhez és a nem általános katalógusú virtuális gépek vagy virtuális merevlemezek Azure-ba való feltöltéséhez című fejezeteket. Olvassa el [a 3.][deployment-guide-3.4] [Deployment Guide][deployment-guide]

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Virtuális gép üzembe helyezése ügyfélspecifikus rendszerképpel

Az operációs rendszer vagy a DBMS-verzió speciális javítási követelményei miatt előfordulhat, hogy az Azure Marketplace-en biztosított lemezképek nem felelnek meg az igényeinek. Ezért előfordulhat, hogy létre kell hoznia egy virtuális gépet a saját privát OPERÁCIÓSrendszer-/DBMS virtuálisgép-lemezkép használatával, amely utólag többször is telepíthető. Az ilyen privát kép párhuzamosságra való előkészítéséhez a következő elemeket kell figyelembe venni:

---
> ![Windows][Logo_Windows] Windows
>
> További részleteket <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> itt talál: A Windows-beállításokat (például a Windows BIZTONSÁGI azonosítót és a állomásnevet) a sysprep parancs on keresztül kell absztrakt/általánosítottsá tenni a helyszíni virtuális gépen.
>
>
> ![Linux][Logo_Linux] Linux
>
> Kövesse az alábbi cikkekben leírt lépéseket a [SUSE,][virtual-machines-linux-create-upload-vhd-suse] [red hat][virtual-machines-linux-redhat-create-upload-vhd], vagy [oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], az Azure-ba feltöltendő virtuális merevlemez előkészítéséhez.
>
>

---
Ha már telepítette az SAP-tartalmat a helyszíni virtuális gépben (különösen a kétrétegű rendszerekesetében), módosíthatja az SAP rendszerbeállításait az Azure VM üzembe helyezése után az SAP szoftverkiépítési kezelő (SAP Note [1619720)]által támogatott példányátnevezési eljáráson keresztül. Lásd: A virtuális [gép üzembe helyezésének előkészítése egy ügyfél-specifikus lemezkép az SAP és][planning-guide-5.2.2] a virtuális merevlemez [feltöltése a helyszíni Azure-ba][planning-guide-5.3.2] ebben a dokumentumban a helyszíni előkészítési lépéseket, és töltse fel egy általános virtuális gép az Azure-ba. Olvassa el [a 2.][deployment-guide-3.3] [Deployment Guide][deployment-guide]

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Virtuális gép üzembe helyezése az Azure Piactérről

Egy Microsoft vagy külső által biztosított virtuálisgép-lemezképet szeretne használni az Azure Piactérről a virtuális gép üzembe helyezéséhez. Miután üzembe helyezte a virtuális gépet az Azure-ban, ugyanazokat az irányelveket és eszközöket kell követnie az SAP-szoftver és/vagy a DBMS telepítéséhez a virtuális gépen belül, mint egy helyszíni környezetben. További részletes üzembe helyezési leírást, lásd a [fejezet 1.][deployment-guide-3.2] [Deployment Guide][deployment-guide]

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Virtuális gépek előkészítése az Sap-val az Azure-hoz

Virtuális gépek feltöltése az Azure-ba, győződjön meg arról, hogy a virtuális gépek és a virtuális gépek megfelelnek bizonyos követelményeknek. A használt telepítési módszertől függően kisebb különbségek vannak.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Felkészülés a virtuális gépek helyszíni és azure-beli, nem általános lemezes áthelyezésére

Egy gyakori üzembe helyezési módszer egy meglévő virtuális gép áthelyezése, amely egy SAP-rendszert futtat a helyszíni Azure-ba. A virtuális gép és az SAP-rendszer a virtuális gép csak az Azure-ban kell futtatni az azonos állomásnév és valószínűleg ugyanazt az SAP-sid használatával. Ebben az esetben a virtuális gép vendég operációs rendszer nem általánosítható több központi telepítésesetén. Ha a helyszíni hálózat lett kiterjesztve az Azure-ba, majd még ugyanazokat a tartományi fiókokat lehet használni a virtuális gépen belül, mint a helyszíni használat előtt használt.

A saját Azure VM-lemez előkészítésekor a következő követelmények a következők:

* Eredetileg az operációs rendszert tartalmazó virtuális merevlemez maximális mérete csak 127 GB lehet. Ezt a korlátozást 2015 márciusának végén megszüntették. Most az operációs rendszert tartalmazó virtuális merevlemez akár 1 TB méretű is lehet, mint bármely más Azure Storage üzemeltetett virtuális merevlemez.
* Rögzített VHD formátumban kell lennie. Dinamikus Virtuális gépek vagy VHD-k VHDx formátumban még nem támogatottak az Azure-ban. A dinamikus Virtuális merevlemezek statikus virtuális merevlemezekké alakulnak át, amikor powershell-parancsokkal vagy CLI-vel tölti fel a virtuális merevlemezt
* Virtuális merevlemezek, amelyek a virtuális géphez vannak csatlakoztatva, és újra csatlakoztatni kell az Azure-ban a virtuális gép hez kell egy rögzített virtuális merevlemez-formátumban is. Olvassa el [ezt a cikket (Linux)](../../linux/managed-disks-overview.md) és [ezt a cikket (Windows)](../../windows/managed-disks-overview.md)az adatlemezek méretkorlátairól. A dinamikus Virtuális merevlemezek statikus virtuális merevlemezekké alakulnak át, amikor powershell-parancsokkal vagy CLI-vel tölti fel a virtuális merevlemezt
* Adjon hozzá egy másik helyi fiókot rendszergazdai jogosultságokkal, amelyeket a Microsoft-támogatás használhat, vagy amely a virtuális gép üzembe helyezéséig futtatandó szolgáltatások és alkalmazások környezeteként rendelhető, és megfelelőbb felhasználók at használhat.
* Adjon hozzá más helyi fiókokat, mivel ezek az adott telepítési forgatókönyvhöz szükségesek lehetnek.

---
> ![Windows][Logo_Windows] Windows
>
> Ebben az esetben nincs általánosítása (sysprep) a virtuális gép feltöltése és üzembe helyezése az Azure-ban.
> Győződjön meg arról, hogy a meghajtó D:\ nem használják.
> Állítsa be a csatlakoztatott lemezek automatikus csatlakoztatásának beállítását a [dokumentumban csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3] című fejezetben leírtak szerint.
>
> ![Linux][Logo_Linux] Linux
>
> Ebben a forgatókönyvben nincs általánosítása (waagent -deprovision) a virtuális gép feltöltése és üzembe helyezése az Azure-ban.
> Győződjön meg arról, hogy az /mnt/resource nincs használatban, és hogy az ÖSSZES lemez az uuid segítségével van csatlakoztatva. Az operációs rendszer lemeze esetén győződjön meg arról, hogy a rendszertöltő-bejegyzés az uuid-alapú csatlakoztatót is tükrözi.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Felkészülés egy virtuális gép üzembe helyezéséhez egy ügyfél-specifikus lemezképpel az SAP-hoz

Az általános operációs rendszert tartalmazó vhd-fájlok at az Azure Storage-fiókok tárolóiban vagy felügyelt lemezlemez-lemezképekként tárolják. Egy új virtuális gép üzembe helyezhető egy ilyen lemezképből, ha a virtuális merevlemezre vagy a felügyelt lemezlemez-lemezképre hivatkozik forrásként [Deployment Guide][deployment-guide]a központi telepítési sablonfájlokban a [2.][deployment-guide-3.3]

A saját Azure Virtuálisgép-lemezkép előkészítésekor a következő követelmények a következők:

* Eredetileg az operációs rendszert tartalmazó virtuális merevlemez maximális mérete csak 127 GB lehet. Ezt a korlátozást 2015 márciusának végén megszüntették. Most az operációs rendszert tartalmazó virtuális merevlemez akár 1 TB méretű is lehet, mint bármely más Azure Storage üzemeltetett virtuális merevlemez.
* Rögzített VHD formátumban kell lennie. Dinamikus Virtuális gépek vagy VHD-k VHDx formátumban még nem támogatottak az Azure-ban. A dinamikus Virtuális merevlemezek statikus virtuális merevlemezekké alakulnak át, amikor powershell-parancsokkal vagy CLI-vel tölti fel a virtuális merevlemezt
* Virtuális merevlemezek, amelyek a virtuális géphez vannak csatlakoztatva, és újra csatlakoztatni kell az Azure-ban a virtuális gép hez kell egy rögzített virtuális merevlemez-formátumban is. Kérjük, olvassa el [ezt a cikket (Linux)](../../windows/managed-disks-overview.md) és ezt a [cikket (Windows)](../../linux/managed-disks-overview.md) az adatlemezek méretkorlátairól. A dinamikus Virtuális merevlemezek statikus virtuális merevlemezekké alakulnak át, amikor powershell-parancsokkal vagy CLI-vel tölti fel a virtuális merevlemezt
* Adjon hozzá más helyi fiókokat, mivel ezek az adott telepítési forgatókönyvhöz szükségesek lehetnek.
* Ha a lemezkép tartalmazza az SAP NetWeaver telepítését, és valószínűleg az Azure-telepítés pontján az eredeti névről átnevezi az állomásnevet, ajánlott az SAP Software Provisioning Manager DVD legújabb verzióit a sablonba másolni. Ez lehetővé teszi, hogy könnyen használja az SAP által biztosított átnevezési funkciót a módosított állomásnév adaptálásához és/vagy módosíthatja az SAP-rendszer SID-jét az üzembe helyezett virtuálisgép-lemezképen belül, amint egy új példány elindul.

---
> ![Windows][Logo_Windows] Windows
>
> Győződjön meg arról, hogy a meghajtó D:\ nem használt Lemez automatikus csatlakoztatása a csatlakoztatott lemezekhez a jelen dokumentumban [csatolt lemezek automatikus csatlakoztatásának beállítása című][planning-guide-5.5.3] fejezetben leírtak szerint.
>
> ![Linux][Logo_Linux] Linux
>
> Győződjön meg arról, hogy az /mnt/resource nincs használatban, és hogy az ÖSSZES lemez az uuid segítségével van csatlakoztatva. Az operációs rendszer lemeze esetén győződjön meg arról, hogy a rendszertöltő-bejegyzés az uuid-alapú csatlakoztatót is tükrözi.
>
>

---
* Az SAP GUI (felügyeleti és telepítési célokra) előre telepíthető egy ilyen sablonba.
* A virtuális gépek telephelyek közötti forgatókönyvekben való sikeres futtatásához szükséges egyéb szoftverek is telepíthetők, amennyiben ez a szoftver együttműködik a virtuális gép átnevezésével.

Ha a virtuális gép készen áll ahhoz, hogy általános és végül független a fiókok/felhasználók nem érhető el a célzott Azure-telepítési forgatókönyv, az utolsó előkészítési lépés az ilyen lemezkép általánosítása történik.

##### <a name="generalizing-a-vm"></a>Virtuális gép általánosítása
---
> ![Windows][Logo_Windows] Windows
>
> Az utolsó lépés az, hogy jelentkezzen be egy virtuális gép egy rendszergazdai fiókkal. Nyisson meg egy Windows parancsablakot *rendszergazdaként*. Nyissa meg a %windir%\windows\system32\sysprep mappát, és hajtsa végre a sysprep.exe programot.
> Egy kis ablak jelenik meg. Fontos, hogy ellenőrizze a **Generalize opciót** (az alapértelmezett nincs bejelölve), és módosítsa a Leállítási opciót az alapértelmezett "Újraindítás" értékről "leállításra". Ez az eljárás feltételezi, hogy a sysprep folyamat végrehajtása a helyszínen a virtuális gép vendég operációs rendszerében.
> Ha azt szeretné, hogy az eljárást egy virtuális gép már fut az Azure-ban, kövesse a [cikkben](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)leírt lépéseket.
>
> ![Linux][Logo_Linux] Linux
>
> [Linuxos virtuális gép rögzítése Resource Manager-sablonként][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Virtuális gépek és virtuális gépek átvitele a helyszíni adatok között az Azure-ba
Mivel a virtuális géplemezképek és lemezek azure-ba való feltöltése nem lehetséges az Azure Portalon keresztül, Azure PowerShell-parancsmagokat vagy CLI-t kell használnia. Egy másik lehetőség az "AzCopy" eszköz használata. Az eszköz virtuális gépek másolása a helyszíni és az Azure között (mindkét irányban). Azt is másolhatja a virtuális gépek az Azure-régiók között. Az AzCopy letöltéséhez és használatához tekintse meg ezt a [dokumentációt.][storage-use-azcopy]

A harmadik alternatíva az lenne, hogy különböző külső GUI-orientált eszközök. Azonban győződjön meg arról, hogy ezek az eszközök támogatják az Azure-lapblobok. Céljainkhoz az Azure Page Blob áruházat kell használnunk <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>(a különbségeket itt ismertetjük: ). Az Azure által biztosított eszközök is hatékonyak a virtuális gépek és a virtuális gépek tömörítésében, amelyeket fel kell tölteni. Ez azért fontos, mert ez a hatékonyság a tömörítés csökkenti a feltöltési idő (amely egyébként függ a feltöltési linket az internetre a helyszíni létesítmény és az Azure üzembe helyezési régió célzott). Helyes feltételezés, hogy egy virtuális gép vagy virtuális merevlemez európai helyről az Egyesült Államokban az Egyesült Államokban található Azure-adatközpontokba való feltöltése hosszabb időt vesz igénybe, mint az azonos virtuális gépek/virtuális gépek feltöltése az európai Azure-adatközpontokba.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Virtuális merevlemez feltöltése a helyszíni azure-ból
Ha egy meglévő virtuális gép vagy virtuális merevlemez a helyszíni hálózatról, így egy virtuális gép vagy virtuális merevlemez kell megfelelnie a követelményeknek a fejezetben felsorolt [előkészítés egy virtuális gép áthelyezése a helyszíni Azure-ba egy nem általánosított lemez][planning-guide-5.2.1] ebben a dokumentumban.

Az ilyen virtuális gép nem kell általánossá, és lehet feltölteni az állam és a forma azt leállítás után a helyszíni oldalon. Ugyanez igaz a további Virtuális gépekre is, amelyek nem tartalmaznak operációs rendszert.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Virtuális merevlemez feltöltése és Azure-lemezvé tétele
Ebben az esetben szeretnénk feltölteni egy virtuális merevlemezt, akár operációs rendszerrel, akár anélkül, és csatlakoztatni egy virtuális géphez adatlemezként, vagy operációs rendszer lemezként használni. Ez egy többlépéses folyamat

**Powershell**

* Jelentkezzen be az előfizetésbe a *Connect-AzAccount segítségével*
* A környezet előfizetésének beállítása *set-azContext* és a paraméter-SubscriptionId vagy SubscriptionName segítségével - lásd:<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Töltse fel a virtuális merevlemezt *az Add-AzVhd segítségével* egy Azure Storage-fiókba – lásd:<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Nem kötelező) Felügyelt lemez létrehozása a virtuális merevlemezről a *New-AzDisk segítségével* - lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Állítsa be egy új virtuális gép konfigurációjának operációs rendszerlemezét a virtuális merevlemezre vagy a Managed Disk *set-AzVMOSDisk-re* – lásd:<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Hozzon létre egy új virtuális gép a virtuális gép config *a New-AzVM* - lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Adatlemez hozzáadása új virtuális géphez *az Add-AzVMDataDisk segítségével* – lásd:<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* Jelentkezzen be az előfizetésbe az *bejelentkezéssel*
* Válassza ki az előfizetését *az-fiókbeállítással --subscription `<subscription name or id` *
* Töltse fel a virtuális merevlemezt az *az storage blob feltöltésével* – [lásd: Az Azure CLI használata az Azure Storage-szal][storage-azure-cli]
* (Nem kötelező) Felügyelt lemez létrehozása a virtuális merevlemezről az *az lemez létrehozása segítségével* - lásd:https://docs.microsoft.com/cli/azure/disk
* Hozzon létre egy új virtuális gép, amely a feltöltött virtuális merevlemezt vagy a felügyelt lemezt operációs rendszer lemezeként adja meg *az vm létrehozási* és paraméter *--attach-os-disk paraméterrel*
* Adatlemez hozzáadása egy új virtuális géphez *az vm lemez csatolásával* és paraméterrel *--új*

**Sablon**

* Töltse fel a virtuális merevlemezt a PowerShell vagy az Azure CLI segítségével
* (Nem kötelező) Felügyelt lemez létrehozása a virtuális merevlemezről a PowerShell, az Azure CLI vagy az Azure Portal segítségével
* Telepítse a virtuális gép egy JSON sablon hivatkozva a Virtuális merevlemez, ahogy az ebben a [példában a JSON sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) vagy a felügyelt lemezek használata, ahogy az ebben a [példában a JSON sablon.](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)

#### <a name="deployment-of-a-vm-image"></a>Virtuálisgép-lemezkép központi telepítése
Egy meglévő virtuális gép vagy virtuális merevlemez feltöltése a helyszíni hálózatról, annak érdekében, hogy egy Azure virtuális gép rendszerképként, például egy virtuális gép vagy virtuális merevlemez kell, hogy megfeleljen a fejezetben [felsorolt követelményeknek előkészítése egy virtuális gép üzembe helyezése egy ügyfél-specifikus lemezkép az SAP][planning-guide-5.2.2] ebben a dokumentumban.

* A *sysprep* használata Windows rendszeren vagy *waagent -deprovision* linuxos a virtuális gép általánosításához - lásd: [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx) for Windows vagy How to capture a Linux virtuális gép [linuxos erőforrás-kezelősablonként való használata][capture-image-linux-step-2-create-vm-image]
* Jelentkezzen be az előfizetésbe a *Connect-AzAccount segítségével*
* A környezet előfizetésének beállítása *set-azContext* és a paraméter-SubscriptionId vagy SubscriptionName segítségével - lásd:<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Töltse fel a virtuális merevlemezt *az Add-AzVhd segítségével* egy Azure Storage-fiókba – lásd:<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Nem kötelező) Felügyelt lemezlemez-lemezkép létrehozása a Virtuális merevlemezről a *New-AzImage segítségével* - lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Állítsa be az operációs rendszer lemeze egy új virtuális gép konfigurációa a
  * VHD *a Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* - lásd:<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Felügyelt lemezkép *set-AzVMSourceImage* - lásd:<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Hozzon létre egy új virtuális gép a virtuális gép config *a New-AzVM* - lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* A *sysprep* használata Windows rendszeren vagy *waagent -deprovision* linuxos a virtuális gép általánosításához - lásd: [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx) for Windows vagy How to capture a Linux virtuális gép [linuxos erőforrás-kezelősablonként való használata][capture-image-linux-step-2-create-vm-image]
* Jelentkezzen be az előfizetésbe az *bejelentkezéssel*
* Válassza ki az előfizetését *az-fiókbeállítással --subscription `<subscription name or id` *
* Töltse fel a virtuális merevlemezt az *az storage blob feltöltésével* – [lásd: Az Azure CLI használata az Azure Storage-szal][storage-azure-cli]
* (Nem kötelező) Felügyelt lemezkép létrehozása a virtuális merevlemezről az *az-lemez létrehozásával* – lásd:https://docs.microsoft.com/cli/azure/image
* Hozzon létre egy új virtuális gép, amely a feltöltött virtuális merevlemezt vagy a felügyelt lemezlemezt operációs rendszerként adja meg *az vm létrehozási* és *paraméter---image*

**Sablon**

* A *sysprep* használata Windows rendszeren vagy *waagent -deprovision* linuxos a virtuális gép általánosításához - lásd: [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx) for Windows vagy How to capture a Linux virtuális gép [linuxos erőforrás-kezelősablonként való használata][capture-image-linux-step-2-create-vm-image]
* Töltse fel a virtuális merevlemezt a PowerShell vagy az Azure CLI segítségével
* (Nem kötelező) Felügyelt lemezlemez-lemezlemez-lemezkép létrehozása a virtuális merevlemezről a PowerShell, az Azure CLI vagy az Azure Portal segítségével
* Telepítse a virtuális gép egy JSON sablon hivatkozva a kép Virtuális merevlemez, ahogy az ebben a [példában a JSON sablon,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) vagy használja a felügyelt lemezlemez lemezkép, ahogy az ebben a [példában a JSON sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Virtuális merevlemezek vagy felügyelt lemezek letöltése a helyszíni
Az Azure Infrastructure as a Service nem egyirányú utca, hogy csak a virtuális gépek és az SAP-rendszerek feltöltése. Az Azure-ból származó SAP-rendszereket is áthelyezheti a helyszíni világba.

A letöltés ideje alatt a Virtuális merevlemezek vagy a felügyelt lemezek nem lehetnek aktívak. Még a virtuális gépekhez csatlakoztatott lemezek letöltésekének esetén is le kell állítani a virtuális gépet, és fel kell osztani. Ha csak az adatbázis tartalmát szeretné letölteni, amelyet akkor egy új rendszer helyszíni beállításához kell használni, és elfogadható, hogy a letöltés és az új rendszer beállítása során a rendszer továbbra is működőképes lehet , elkerülheti a hosszú állásidőt, ha egy tömörített adatbázis biztonsági mentést hajt végre egy lemezre, és csak töltse le a lemezt, ahelyett, hogy letöltené az operációs rendszer alap virtuális gépét.

#### <a name="powershell"></a>PowerShell

* Felügyelt lemez letöltése  
  Először is hozzá kell férnem a felügyelt lemez alapjául szolgáló blobhoz. Ezután átmásolhatja az alapul szolgáló blobot egy új tárfiókba, és letöltheti a blobot erről a tárfiókból.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Virtuális merevlemez letöltése  
  Miután az SAP-rendszer leállt, és a virtuális gép leáll, használhatja a PowerShell-parancsmag Save-AzVhd a helyszíni cél a VHD-lemezek letöltéséhez vissza a helyszíni világ. Ehhez szüksége van a virtuális merevlemez URL-címét, amely megtalálható az Azure Portal "tárolási szakaszában" (meg kell navigálnia a tárfiókhoz és a tárolótárolóhoz, ahol a virtuális merevlemez t hozták létre), és tudnia kell, hogy a virtuális merevlemezhova kell másolni.

  Ezután kihasználhatja a parancsot a SourceUri paraméter taszthatja a letöltő virtuális merevlemez URL-címeként, és a LocalFilePath-ot a virtuális merevlemez fizikai helyeként (beleértve a nevét is). A parancs így nézhet ki:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  A Save-AzVhd parancsmagról itt olvashat <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>bővebben.

#### <a name="azure-cli"></a>Azure CLI
* Felügyelt lemez letöltése  
  Először is hozzá kell férnem a felügyelt lemez alapjául szolgáló blobhoz. Ezután átmásolhatja az alapul szolgáló blobot egy új tárfiókba, és letöltheti a blobot erről a tárfiókból.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Virtuális merevlemez letöltése   
  Miután az SAP-rendszer leállt, és a virtuális gép leáll, használhatja az Azure CLI parancs _azure storage blob letöltésa_ a helyszíni cél a VHD-lemezek letöltéséhez a helyszíni világ. Ehhez szüksége van a nevére és a virtuális merevlemez tárolójára, amely megtalálható az Azure Portal "Tárolási szakaszában" (meg kell keresnie a tárfiókot és a tárolótárolót, ahol a virtuális merevlemez t létrehozták), és tudnia kell, hogy a virtuális merevlemezt hol kell másolni a.

  Ezután használhatja a parancsot a letölthető virtuális merevlemez blobés tárolójának, valamint a virtuális merevlemez fizikai célhelyeként (beleértve a nevét is) a paraméterek meghatározásával. A parancs így nézhet ki:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Virtuális gépek és lemezek átvitele az Azure-on belül

#### <a name="copying-sap-systems-within-azure"></a>SAP-rendszerek másolása az Azure-ban

Egy SAP-rendszer, vagy akár egy dedikált DBMS-kiszolgáló, amely támogatja az SAP alkalmazásréteget, valószínűleg több lemezből áll, amelyek vagy az operációs rendszert tartalmazzák a bináris fájlokkal, vagy az SAP-adatbázis adatait és naplófájljait. Sem a lemezek másolásának Azure-funkciói, sem a lemezek helyi lemezre mentésének Azure-funkciója nem rendelkezik szinkronizálási mechanizmussal, amely több lemezt konzisztens módon pillanatkakészít. Ezért a másolt vagy mentett lemezek állapota akkor is, ha azok ugyanahhoz a virtuális géphez vannak csatlakoztatva, eltérő lenne. Ez azt jelenti, hogy a különböző lemezeken található különböző adatok és naplófájlok esetében az adatbázis végül inkonzisztens lenne.

**Következtetés: Lemezek másolásához vagy mentéséhez, amelyek egy SAP-rendszer konfigurációjának részét képezik, le kell állítania az SAP-rendszert, és le kell állítania az üzembe helyezett virtuális gép leállítását is. Csak ezután másolhatja vagy töltheti le a lemezek készletét, hogy hozzon létre egy másolatot az SAP-rendszerről az Azure-ban vagy a helyszínen.**

Adatlemezek tárolhatók VHD-fájlok egy Azure Storage-fiókban, és közvetlenül csatlakoztatható egy virtuális géphez, vagy lemezképként használható. Ebben az esetben a virtuális merevlemez másolva egy másik helyre, mielőtt csatlakozik a virtuális géphez. A vHD-fájl teljes nevének az Azure-ban egyedinek kell lennie az Azure-on belül. Mint korábban már említettük, a név egyfajta három részből álló név, amely így néz ki:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Az adatlemezek felügyelt lemezek is lehetnek. Ebben az esetben a felügyelt lemez segítségével hozzon létre egy új felügyelt lemezt, mielőtt csatlakozik a virtuális géphez. A kezelt lemez nevének egyedinek kell lennie egy erőforráscsoporton belül.

##### <a name="powershell"></a>PowerShell

Az Azure PowerShell-parancsmagokkal a vhd másolásához a [jelen cikkben][storage-powershell-guide-full-copy-vhd]látható módon. Új felügyelt lemez létrehozásához használja a New-AzDiskConfig és a New-AzDisk a következő példában látható módon.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

Az Azure CLI segítségével egy virtuális merevlemez másolása. Új felügyelt lemez létrehozásához használja az *az lemezlétrehozást* az alábbi példában látható módon.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage-eszközök

* <https://storageexplorer.com/>

Az Azure Storage Explorers professzionális kiadásai itt találhatók:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

A virtuális merevlemez másolata egy tárfiókon belül egy folyamat, amely csak néhány másodpercet vesz igénybe (hasonlóan a SAN hardver létrehozása pillanatképek lusta másolás és másolás írásra). Miután rendelkezik a virtuális merevlemez-fájl másolatával, csatolhatja azt egy virtuális géphez, vagy lemezképként használhatja a virtuális merevlemez másolatainak csatolásához a virtuális merevlemezekhez.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Azure CLI

```azurecli

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

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Lemezek másolása az Azure Storage-fiókok között
Ez a feladat nem hajtható végre az Azure Portalon. Használhatja az Azure PowerShell-parancsmagokat, az Azure CLI-t vagy egy külső tárolóböngészőt. A PowerShell-parancsmagok vagy CLI-parancsok blobokat hozhatnak létre és kezelhetnek, amelyek magukban foglalják a blobok astorage-fiókok és az Azure-előfizetés régiói közötti aszinkron másolását.

##### <a name="powershell"></a>PowerShell
VhD-k is másolhatók az előfizetések között. További információkért olvassa el [ezt a cikket][storage-powershell-guide-full-copy-vhd].

A PS-parancsmag logikájának alapfolyamata így néz ki:

* Tárfiók környezetének létrehozása **source** a Forrástárfiókhoz a *New-AzStorageContext* környezettel - lásd:<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Tárfiók környezetének létrehozása a *new-azstoragecontext-vel* a **céltárfiókhoz** - lásd:<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* A másolás indítása

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Ellenőrizze a másolat állapotát egy hurokban

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Csatlakoztassa az új virtuális merevlemezt egy virtuális géphez a fent leírtak szerint.

Példákat [lásd ebben a cikkben][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure CLI
* A másolás indítása

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Ellenőrizze az állapotot, ha a másolat még mindig hurokban van

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Csatlakoztassa az új virtuális merevlemezt egy virtuális géphez a fent leírtak szerint.

### <a name="disk-handling"></a>Lemezkezelés

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Virtuális gép/lemez struktúra az SAP-telepítésekhez

Ideális esetben a virtuális gép és a kapcsolódó lemezek szerkezetének kezelése egyszerű nek kell lennie. A helyszíni telepítéseksorán az ügyfelek számos módot fejlesztettek ki a kiszolgálótelepítésének strukturálására.

* Egy alaplemez, amely tartalmazza az operációs rendszert és a DBMS és/vagy AZ SAP összes bináris fájlját. 2015 márciusa óta ez a lemez legfeljebb 1 TB méretű lehet a korábbi korlátozások helyett, amelyek 127 GB-ra korlátozták.
* Egy vagy több lemez, amelyek tartalmazzák az SAP-adatbázis DBMS naplófájlját és a DBMS ideiglenes tárolóterület naplófájlját (ha a DBMS ezt támogatja). Ha az adatbázisnapló IOPS-követelményei magasak, több lemezt kell csíkoznia ahhoz, hogy elérje a szükséges IOPS-kötetet.
* Az SAP-adatbázis és a DBMS ideiglenes adatfájljainak egy vagy két adatbázisfájlját tartalmazó lemezek száma (ha a DBMS ezt támogatja).

![Az Azure IaaS virtuális gép referenciakonfigurációja az SAP-hoz][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Sok ügyfélnél olyan konfigurációkat láttunk, ahol például az SAP és a DBMS bináris fájlok nem voltak telepítve a c:\ meghajtón, ahol az operációs rendszer telepítve volt. Ennek különböző okai voltak, de amikor visszamentünk a gyökérhez, általában az volt, hogy a meghajtók kicsiek voltak, és az operációs rendszer frissítése további helyet szükséges 10-15 évvel ezelőtt. Mindkét feltétel nem vonatkozik ezekben a napokban túl gyakran már. Ma a c:\ meghajtó nagy kötetlemezeken vagy virtuális gépeken is leképezhető. Annak érdekében, hogy a központi telepítések egyszerűek legyenek a struktúrájukban, ajánlott az SAP NetWeaver-rendszerek következő telepítési mintáját követni az Azure-ban
>
> A Windows operációs rendszer lapozófájljának a D: meghajtón (nem állandó lemez) kell lennie
>
> ![Linux][Logo_Linux] Linux
>
> Helyezze a Linux swapfile-t a /mnt /mnt/resource on Linux alá a [cikkben][virtual-machines-linux-agent-user-guide]leírtak szerint . A lapozófájl konfigurálható a Linux-ügynök /etc/waagent.conf konfigurációs fájljában. Adja hozzá vagy módosítsa a következő beállításokat:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

A módosítások aktiválásához újra kell indítania a Linux-ügynököt

```console
sudo service waagent restart
```

Kérjük, olvassa el az SAP Note [1597355-öt] az ajánlott lapozófájl méretével kapcsolatos további részletekért

---
A DBMS-adatfájlokhoz használt lemezek számát és a lemezek üzemeltetett Azure Storage típusát az IOPS-követelmények és a szükséges késés határozza meg. A pontos kvótákat ebben a [cikkben (Linux)][virtual-machines-sizes-linux] és [ebben a cikkben (Windows) ismertetjük.][virtual-machines-sizes-windows]

Az SAP telepítések tapasztalatai az elmúlt két évben néhány leckét tanítottak nekünk, amelyek a következőképpen foglalhatók össze:

* IOPS-forgalom különböző adatfájlok nem mindig ugyanaz, mivel a meglévő ügyfélrendszerek eltérő méretű adatfájlokat képviselő SAP-adatbázis(ok). Ennek eredményeként kiderült, hogy jobban használja a RAID konfiguráció több lemezen elhelyezni az adatfájlok LUNs faragott ki azokat. Voltak olyan helyzetek, különösen az Azure Standard Storage, ahol az IOPS-díj elérte a kvótát egy lemez a DBMS tranzakciós napló. Ilyen esetekben a prémium szintű storage használata ajánlott, vagy több szabványos tárolólemez egy szoftvercsíkkal összesítése.

---
> ![Windows][Logo_Windows] Windows
>
> * [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Szoftveres RAID konfigurálása Linuxon][virtual-machines-linux-configure-raid]
> * [LVM konfigurálása Linux os virtuális gépen az Azure-ban][virtual-machines-linux-configure-lvm]
> * [Az Azure Storage titkai és linuxos I/O-optimalizálása](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* A prémium szintű storage jelentős jobb teljesítményt mutat, különösen a kritikus tranzakciós naplóírások esetében. Sap-forgatókönyvek, amelyek várhatóan éles, mint a teljesítmény, erősen ajánlott a Virtuálisgép-sorozat, amely kihasználhatja az Azure Premium Storage.

Ne feledje, hogy a lemez, amely tartalmazza az operációs rendszer, és azt javasoljuk, az SAP bináris fájlok at és az adatbázis (alap Virtuális gép) is, már nem korlátozódik 127GB. Most már lehet akár 1TB méretű. Ennek elegendő helyet kell biztosítania az összes szükséges fájl megtartásához, beleértve például az SAP kötegelt feldolgozási naplókat is.

További javaslatokért és további részletekért, különösen a DBMS virtuális gépekhez, olvassa el a [DBMS telepítési útmutatóját][dbms-guide]

#### <a name="disk-handling"></a>Lemezkezelés

A legtöbb esetben további lemezeket kell létrehoznia az SAP-adatbázis virtuális gépre való üzembe helyezéséhez. Beszéltünk a szempontok at lemezek száma a fejezetben [VM/lemez szerkezete az SAP telepítések][planning-guide-5.5.1] ebben a dokumentumban. Az Azure Portal lehetővé teszi a lemezek csatolása és leválasztása, ha egy alap virtuális gép üzembe helyezése. A lemezek csatlakoztathatók/leválaszthatók, ha a virtuális gép működik, valamint leáll. Lemez csatlakoztatásakor az Azure Portal felajánlja egy üres lemez vagy egy meglévő lemez csatolását, amely ebben az időpontban nem csatlakozik egy másik virtuális géphez.

**Megjegyzés:** A lemezek csak egy virtuális géphez csatlakoztathatók egy adott időpontban.

![Lemezek csatlakoztatása és leválasztása az Azure Standard Storage szolgáltatással][planning-guide-figure-1400]

Egy új virtuális gép üzembe helyezése során eldöntheti, hogy felügyelt lemezeket kíván-e használni, vagy a lemezeket az Azure Storage-fiókokra kívánja-e helyezni. Ha prémium szintű storage-ot szeretne használni, javasoljuk a Felügyelt lemezek használatát.

Ezután el kell döntenie, hogy új és üres lemezt szeretne-e létrehozni, vagy egy korábban feltöltött meglévő lemezt szeretne-e kiválasztani, és most csatolni kell a virtuális géphez.

**FONTOS:** **Nem** szeretné használni a gazdagép-gyorsítótárazást az Azure Standard Storage szolgáltatással. A gazdagyorsítótár-beállítást a NINCS alapértelmezett értéken kell hagyni. Az Azure Premium Storage, engedélyeznie kell a gyorsítótárazás olvasása, ha az I/O-jellemző többnyire olvasni, mint a tipikus I/O-forgalom adatbázis-adatfájlok ellen. Adatbázis-tranzakciós naplófájl esetén nem ajánlott gyorsítótárazás.

---
> ![Windows][Logo_Windows] Windows
>
> [Adatlemez csatolása az Azure Portalon][virtual-machines-linux-attach-disk-portal]
>
> Ha lemezek vannak csatlakoztatva, be kell jelentkeznie a virtuális gépre a Windows Lemezkezelő megnyitásához. Ha az automatikus csatlakoztatás nincs engedélyezve a [csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3]című fejezetben ajánlott módon, az újonnan csatlakoztatott kötetet online állapotba kell hozni, és inicializálni kell.
>
> ![Linux][Logo_Linux] Linux
>
> Ha lemezek vannak csatlakoztatva, be kell jelentkeznie a virtuális gépbe, és inicializálnia kell a lemezeket a [jelen cikkben][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] leírtak szerint.
>
>

---
Ha az új lemez üres lemez, akkor a lemezt is formáznia kell. A formázás, különösen a DBMS-adatok és a naplófájlok esetében ugyanazok a javaslatok érvényesek, mint a dbms operációs rendszer nélküli telepítésekesetében.

Az Azure Storage-fiók nem biztosít végtelen erőforrásokat az I/O-kötet, az IOPS és az adatmennyiség tekintetében. Általában a DBMS virtuális gépeket érinti leginkább ez. Előfordulhat, hogy a legjobb, ha egy külön tárfiókot minden virtuális gép, ha kevés magas I/O-kötet virtuális gépek üzembe helyezéséhez annak érdekében, hogy az Azure Storage-fiók kötete belül maradjon. Ellenkező esetben meg kell néznie, hogyan lehet kiegyenlíteni ezeket a virtuális gépeket a különböző storage-fiókok között anélkül, hogy elérne az egyes tárfiókok korlátját. További részleteket a [DBMS telepítési útmutatótartalmaz.][dbms-guide] Ezeket a korlátozásokat is szem előtt kell tartania a tiszta SAP-alkalmazáskiszolgáló virtuális gépek vagy más virtuális gépek esetében, amelyek végül további virtuális gépeket igényelhetnek. Ezek a korlátozások nem érvényesek a Felügyelt lemez használata esetén. Ha a Prémium szintű storage-t szeretné használni, javasoljuk a Felügyelt lemez használatát.

Egy másik témakör, amely fontos a tárfiókok, hogy a virtuális virtuális gépek egy tárfiókban kap nak georeplikált. A georeplikáció engedélyezve van vagy le van tiltva a tárfiók szintjén, és nem a virtuális gép szintjén. Ha a georeplikáció engedélyezve van, a virtuális központi erőforrások a tárfiókban replikálódik egy másik Azure-adatközpontugyanabban a régióban. Mielőtt döntést hozna erről, át kell gondolnia a következő korlátozást:

Az Azure georeplikáció helyileg működik a virtuális gép minden egyes virtuális merevlemezén, és nem replikálja az IOs-t időrendi sorrendben több virtuális gép több virtuális gépközött. Ezért a virtuális merevlemez, amely az alap virtuális gép, valamint a virtuális géphez kapcsolódó további Virtuális merevlemezek replikálódik egymástól függetlenül. Ez azt jelenti, hogy nincs szinkronizálás a különböző virtuális gépek módosításai között. Az a tény, hogy az IOs-ek replikálása az írási sorrendtől függetlenül történik, azt jelenti, hogy a földrajzi replikáció nem értékes azon adatbázis-kiszolgálók esetében, amelyek adatbázisait több Virtuális gépre terjesztik. A DBMS mellett előfordulhatnak más alkalmazások is, ahol a folyamatok különböző Virtuális gépekben írnak vagy kezelnek adatokat, és ahol fontos a változások sorrendjének megtartása. Ha ez követelmény, az Azure-ban a georeplikációt nem szabad engedélyezni. Attól függően, hogy szüksége van-e vagy szeretne-e georeplikációt egy másik készlethez, de nem egy másik készlethez, már kategorizálhatja a virtuális gépeket és a kapcsolódó virtuális gépeket különböző tárfiókokba, amelyek en engedélyezve vagy le tiltva rendelkeznek.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Csatlakoztatott lemezek automatikus csatlakoztatásának beállítása
---
> ![Windows][Logo_Windows] Windows
>
> A saját lemezképekből vagy lemezekről létrehozott virtuális gépek esetében ellenőrizni kell és esetleg be kell állítani az automatikus csatlakoztatási paramétert. Ez a paraméter beállításával a virtuális gép újraindítás vagy az Azure-ban való újratelepítés után automatikusan csatlakoztatja a csatlakoztatott/csatlakoztatott meghajtókat.
> A paraméter a Microsoft által az Azure Marketplace-en biztosított lemezképekhez van beállítva.
>
> Az automatikus csatlakoztatás beállításához itt ellenőrizze a parancssori végrehajtható diskpart.exe dokumentációját:
>
> * [A DiskPart parancssori kapcsolói](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatikus csatlakoztatás](https://technet.microsoft.com/library/cc753703.aspx)
>
> A Windows parancssori ablakát rendszergazdaként kell megnyitni.
>
> Ha lemezek vannak csatlakoztatva, be kell jelentkeznie a virtuális gépre a Windows Lemezkezelő megnyitásához. Ha az automatikus csatlakoztatás nincs engedélyezve a [csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3]című fejezetben ajánlott módon, az újonnan csatlakoztatott kötetet >online állapotba kell hozni, és inicializálni kell.
>
> ![Linux][Logo_Linux] Linux
>
> Inicializálnia kell egy újonnan csatlakoztatott üres lemezt a cikkben leírtak [szerint.][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
> Új lemezeket is hozzá kell adnia az /etc/fstab könyvtárhoz.
>
>

---
### <a name="final-deployment"></a>Végső telepítés

A végső üzembe helyezésés és a pontos lépések, különösen az Azure-bővítmény üzembe helyezését illetően az SAP-hoz, olvassa el a [telepítési útmutatót.][deployment-guide]

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Az Azure virtuális gépeken futó SAP-rendszerek elérése

Olyan esetekben, ahol szeretne csatlakozni az SAP-rendszerek hez a nyilvános interneten keresztül az SAP GUI használatával, a következő eljárásokat kell alkalmazni.

A dokumentum későbbi részében megvitatjuk a másik fő forgatókönyv, az SAP-rendszerekhez való csatlakozás a létesítmények közötti központi telepítések, amelyek a helyek közötti kapcsolat (VPN-alagút) vagy az Azure ExpressRoute-kapcsolat a helyszíni rendszerek és az Azure-rendszerek között.

### <a name="remote-access-to-sap-systems"></a>Távoli hozzáférés az SAP-rendszerekhez

Az Azure Resource Manager, nincsenek alapértelmezett végpontok már, mint a korábbi klasszikus modell. Az Azure Resource Manager virtuális gép minden portja meg van nyitva, amíg:

1. Nincs megadva hálózati biztonsági csoport az alhálózathoz vagy a hálózati adapterhez. Az Azure virtuális gépekhez irányuló hálózati forgalom az úgynevezett "hálózati biztonsági csoportokon" keresztül biztosítható. További információ: [What is a Network Security Group (NSG)?][virtual-networks-nsg] (Mi az a hálózati biztonsági csoport?).
2. Nincs megadva Azure load balancer a hálózati adapterhez   

Tekintse meg a klasszikus modell és az ARM architektúrájának különbségét a cikkben leírtak [szerint.][virtual-machines-azure-resource-manager-architecture]

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Az SAP-rendszer és az SAP GUI-kapcsolat konfigurálása az interneten keresztül

Kérjük, olvassa el ezt a cikket, amely leírja a részleteket ebben a témában:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Tűzfalbeállítások módosítása a virtuális gépen belül

Szükség lehet a tűzfal konfigurálására a virtuális gépeken, hogy az SAP-rendszer bejövő forgalmat.

---
> ![Windows][Logo_Windows] Windows
>
> Alapértelmezés szerint a Windows tűzfal egy Azure telepített virtuális gép be van kapcsolva. Most engedélyeznie kell az SAP port megnyitását, ellenkező esetben az SAP GUI nem tud csatlakozni.
> Ehhez tegye a következőket:
>
> * Nyissa meg a Vezérlőpult\Rendszer és biztonság\Windows tűzfal **speciális beállításokat.**
> * Most kattintson a jobb gombbal a bejövő szabályokra, és válassza az **Új szabályt.**
> * A következő varázsló új **portszabályt** választott.
> * A varázsló következő lépésében hagyja meg a beállítást TCP-ben, és írja be a megnyitni kívánt portszámot. Mivel az SAP-példányazonosító00, 3200-at vettünk. Ha a példány példánya eltérő példányszámmal rendelkezik, meg kell nyitni a korábban a példányszáma alapján definiált portot.
> * A varázsló következő részében hagyja bejelölve a **Kapcsolat engedélyezése** elemet.
> * A varázsló következő lépésében meg kell határoznia, hogy a szabály vonatkozik-e a tartományi, a magán- és a nyilvános hálózatra. Kérjük, szükség esetén állítsa be az Ön igényeihez. Azonban az SAP GUI-val a nyilvános hálózaton keresztül, a nyilvános hálózaton keresztül csatlakozik, a szabályt alkalmazni kell a nyilvános hálózatra.
> * A varázsló utolsó lépésében nevezze el a szabályt, és a Befejezés gombra gombnyomásra nyomja le a **vágógombot.**
>
> A szabály azonnal hatályba lép.
>
> ![Portszabály definíciója][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Az Azure Piactéren a Linux-rendszerképek alapértelmezés szerint nem engedélyezik az iptables tűzfalat, és az SAP-rendszerrel való kapcsolatnak működnie kell. Ha engedélyezte az iptables vagy egy másik tűzfal, olvassa el a dokumentációt iptables vagy a használt tűzfal, hogy a bejövő tcp forgalom port 32xx (ahol xx a rendszer számát az SAP rendszer).
>
>

---
#### <a name="security-recommendations"></a>Biztonsági javaslatok

Az SAP GUI nem csatlakozik azonnal az SAP-példányokhoz (32xx port), amelyek futnak, de először az SAP üzenetkiszolgáló folyamatához megnyitott porton keresztül csatlakozik (port 36xx). A múltban ugyanazt a portot használta az üzenetkiszolgáló az alkalmazáspéldányokkal való belső kommunikációhoz. Annak megakadályozása érdekében, hogy a helyszíni alkalmazáskiszolgálók véletlenül kommunikáljanak az Azure-beli üzenetkiszolgálóval, a belső kommunikációs portok módosíthatók. Erősen ajánlott az SAP-üzenetkiszolgáló és az alkalmazáspéldányok közötti belső kommunikáció módosítása egy másik portszámra a helyszíni rendszerekről klónozott rendszereken, például a fejlesztés klónja projektteszteléshez stb. Ez az alapértelmezett profilparaméterrel végezhető el:

> rdisp/msserv_internal
>
>

az SAP [Message Server biztonsági beállításai](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm) című dokumentumban


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Egyetlen virtuális gép az SAP NetWeaver bemutató/betanítási forgatókönyvvel

![Egyetlen virtuálisgép-SAP-bemutatórendszerek futtatása azonos virtuálisgép-nevekkel, az Azure Cloud Servicesben elkülönítve][planning-guide-figure-1700]

Ebben a forgatókönyvben egy tipikus betanítási/bemutató rendszerforgatókönyvet valósítunk meg, amelyben a teljes betanítási/bemutató forgatókönyv egyetlen virtuális gépben található. Feltételezzük, hogy a központi telepítés virtuális gép lemezképsablonjain keresztül történik. Azt is feltételezzük, hogy ezek a bemutató/oktató: virtuális gépek több kell telepíteni a virtuális gépek, amelyek azonos nevű. A teljes képzési rendszerek nem rendelkeznek a helyszíni eszközök, és egy ellentétes a hibrid üzembe helyezés.

A feltételezés az, hogy létrehozott egy virtuális gép lemezképet a fejezet egyes szakaszaiban a virtuális gépek előkészítése az [SAP az Azure-hoz][planning-guide-5.2] ebben a dokumentumban.

A forgatókönyv megvalósításához szükséges események sorrendje a következőképpen néz ki:

##### <a name="powershell"></a>PowerShell

* Új erőforráscsoport létrehozása minden betanítási/bemutatói környezethez

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Új tárfiók létrehozása, ha nem szeretné használni a felügyelt lemezeket

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Hozzon létre egy új virtuális hálózatot minden betanítási/bemutató környezetben, hogy lehetővé tegye az azonos állomásnév és IP-címek használatát. A virtuális hálózatot egy hálózati biztonsági csoport védi, amely csak a 3389-es portra irányuló forgalmat engedélyezi a távoli asztali hozzáférés és az SSH 22-es portjának engedélyezéséhez.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Új nyilvános IP-cím létrehozása, amellyel a virtuális gép az internetről érhető el

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Új hálózati illesztő létrehozása a virtuális gépszámára

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Virtuális gépet hoz létre. Ebben a forgatókönyvben minden virtuális gép ugyanazt a nevet. Az SAP NetWeaver-példányok SAP-sid-je is megegyezik a virtuális gépekben. Az Azure Resource Groupon belül a virtuális gép nevének egyedinek kell lennie, de a különböző Azure-erőforráscsoportokban futtathatja a virtuális gépeket ugyanazzal a névvel. A Windows alapértelmezett "rendszergazdai" fiókja vagy a Linux "root" fiókja érvénytelen. Ezért egy új rendszergazdai felhasználónevet jelszóval együtt kell definiálni. A virtuális gép méretét is meg kell határozni.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Szükség esetén további lemezeket is hozzáadhat, és visszaállíthatja a szükséges tartalmat. Minden blobnévnek (a blobok URL-címének) egyedinek kell lennie az Azure-on belül.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>parancssori felület

A következő példakód Linuxon is használható. Windows rendszerben használja a PowerShellt a fent leírtak szerint, vagy igazítsa a példát a %rgName% $rgName helyett, és állítsa be a környezeti változót a Windows *parancskészlettel.*

* Új erőforráscsoport létrehozása minden betanítási/bemutatói környezethez

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Új tárfiók létrehozása

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Hozzon létre egy új virtuális hálózatot minden betanítási/bemutató környezetben, hogy lehetővé tegye az azonos állomásnév és IP-címek használatát. A virtuális hálózatot egy hálózati biztonsági csoport védi, amely csak a 3389-es portra irányuló forgalmat engedélyezi a távoli asztali hozzáférés és az SSH 22-es portjának engedélyezéséhez.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Új nyilvános IP-cím létrehozása, amellyel a virtuális gép az internetről érhető el

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Új hálózati illesztő létrehozása a virtuális gépszámára

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Virtuális gépet hoz létre. Ebben a forgatókönyvben minden virtuális gép ugyanazt a nevet. Az SAP NetWeaver-példányok SAP-sid-je is megegyezik a virtuális gépekben. Az Azure Resource Groupon belül a virtuális gép nevének egyedinek kell lennie, de a különböző Azure-erőforráscsoportokban futtathatja a virtuális gépeket ugyanazzal a névvel. A Windows alapértelmezett "rendszergazdai" fiókja vagy a Linux "root" fiókja érvénytelen. Ezért egy új rendszergazdai felhasználónevet jelszóval együtt kell definiálni. A virtuális gép méretét is meg kell határozni.

```azurecli
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

```azurecli
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

* Szükség esetén további lemezeket is hozzáadhat, és visszaállíthatja a szükséges tartalmat. Minden blobnévnek (a blobok URL-címének) egyedinek kell lennie az Azure-on belül.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Sablon

Használhatja a mintasablonokat az azure-quickstart-templates tárházban a GitHubon.

* [Egyszerű Linux virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Egyszerű Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuális gép a képből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Az Azure-on belül kommunikáló virtuális gépek egy készletének megvalósítása

Ez a nem hibrid forgatókönyv egy tipikus forgatókönyv betanítási és bemutató célokra, ahol a bemutató/betanítási forgatókönyvet képviselő szoftver több virtuális gép között van elosztva. A különböző virtuális gépekbe telepített különböző összetevőknek kommunikálniuk kell egymással. Ebben az esetben is nincs szükség helyszíni hálózati kommunikációra vagy több telephelyközötti forgatókönyvre.

Ez a forgatókönyv a telepítés kiterjesztése fejezetben leírt [egyetlen virtuális gép az SAP NetWeaver bemutató/betanítási forgatókönyv][planning-guide-7.1] ebben a dokumentumban. Ebben az esetben több virtuális gép kerül egy meglévő erőforráscsoportba. A következő példában a betanítási környezet egy SAP ASCS/SCS vM, egy DBMS-t futtató virtuális gép ből és egy SAP Application Server-példány virtuális gépéből áll.

A forgatókönyv létrehozása előtt meg kell gondolnia az alapvető beállításokat, ahogy azt már a forgatókönyvben már gyakorolta.

#### <a name="resource-group-and-virtual-machine-naming"></a>Erőforráscsoport és virtuálisgép-elnevezés

Minden erőforráscsoport-névnek egyedinek kell lennie. Saját elnevezési sémát fejleszthet ki az erőforrásokról, például `<rg-name`> utótagról.

A virtuális gép nevének egyedinek kell lennie az erőforráscsoporton belül.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Hálózat beállítása a különböző virtuális gépek közötti kommunikációhoz

![Virtuális gépek készlete egy Azure virtuális hálózaton belül][planning-guide-figure-1900]

Az azonos betanítási/demótáj-alapú klónokkal való elnevezési ütközések megelőzése érdekében minden környezethez létre kell hoznia egy Azure virtuális hálózatot. A DNS-névfeloldást az Azure biztosítja, vagy beállíthatja saját DNS-kiszolgálóját az Azure-on kívül (itt nem kell továbbian megvitatni). Ebben az esetben nem konfiguráljuk a saját DNS-ünket. Egy Azure virtuális hálózaton belüli összes virtuális gép esetén engedélyezve lesz az állomásneveken keresztüli kommunikáció.

A képzési vagy bemutatótájak virtuális hálózatokon való elkülönítésének okai lehetnek, és nem csak az erőforráscsoportok:

* A beállított SAP-környezetnek saját AD/OpenLDAP-ra van szüksége, és a tartományi kiszolgálónak minden tájkép részét kell, hogy legyen.  
* Az SAP-környezet beállítása olyan összetevőket tartalmaz, amelyekrögzített IP-címekkel kell működniük.

Az Azure virtuális hálózatokról és azok meghatározásáról ebben a cikkben olvashat [bővebben.][virtual-networks-create-vnet-arm-pportal]

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>SAP virtuális gépek telepítése vállalati hálózati kapcsolattal (több helyiség)

Sap-környezetet futtat, és szeretné megosztani a központi telepítést a csúcskategóriás DBMS-kiszolgálók, az alkalmazásrétegek helyszíni virtualizált környezetei, valamint a kisebb, kétrétegű konfigurált SAP-rendszerek és az Azure IaaS között. Az alap feltételezés az, hogy az SAP-rendszerek egy SAP-környezetben kell kommunikálni egymással, és sok más szoftver-összetevők a vállalatnál telepített, függetlenül a központi telepítési űrlap. Az SAP GUI-hoz vagy más felületekhez csatlakozó végfelhasználó számára a központi telepítési űrlap által bevezetett különbségek nek sem lehetnek különbségei. Ezek a feltételek csak akkor teljesíthetők, ha a helyszíni Active Directory/OpenLDAP és DNS-szolgáltatások a helyek közötti/többwebhelyközötti kapcsolaton vagy az Azure ExpressRoute-on keresztül az Azure-rendszerekre is kiterjedtek.



### <a name="scenario-of-an-sap-landscape"></a>SAP-környezet forgatókönyve

A telephelyek közötti vagy hibrid forgatókönyv nagyjából leírható az alábbi ábrán:

![Helyek közötti kapcsolat a helyszíni és az Azure-eszközök között][planning-guide-figure-2100]

A minimális követelmény a biztonságos kommunikációs protokollok, például az SSL/TLS használata a böngésző hozzáféréshez vagy vpn-alapú kapcsolatok az Azure-szolgáltatások hoz való rendszerhozzáféréshez. A feltételezés az, hogy a vállalatok eltérően kezelik a VPN-kapcsolatot a vállalati hálózat és az Azure között. Előfordulhat, hogy egyes vállalatok üresen megnyitják az összes portot. Néhány más vállalat is szeretné, hogy pontos, amely portok kell nyitni, stb

Az alábbi táblázatban tipikus SAP kommunikációs portok vannak felsorolva. Alapvetően elegendő az SAP átjáróport megnyitása.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Szolgáltatás | Port neve | Példa `<nn`> = 01 | Alapértelmezett tartomány (min-max) | Megjegyzés |
| --- | --- | --- | --- | --- |
| Diszpécser |sapdp`<nn>` lásd * |3201 |3200 - 3299 |SAP Dispatcher, az SAP GUI által használt Windows és Java |
| Üzenetkiszolgáló |sapms`<sid`> lásd ** |3600 |ingyenes sapms`<anySID`> |sid = SAP-System-ID |
| Átjáró |sapgw`<nn`> lásd * |3301 |Ingyenes |SAP átjáró, CPIC és RFC kommunikációhoz használva |
| SAP-útválasztó |sapdp99 |3299 |Ingyenes |Csak ci (központi példány) szolgáltatásnevek et lehet újra hozzárendelni az /etc/services kapcsolóhoz a telepítés után tetszőleges értékhez. |

*) nn = SAP-példány száma

**) sid = SAP-System-ID

A különböző SAP-termékekhez vagy -szolgáltatásokhoz az SAP-termékekhez szükséges portokról itt talál részletesebb <https://scn.sap.com/docs/DOC-17124>információt.
Ezzel a dokumentummal meg kell tudnia nyitni a dedikált portokat a VPN-eszközben, amely az adott SAP-termékekhez és forgatókönyvekhez szükséges.

Más biztonsági intézkedések telepítésekor virtuális gépek ilyen forgatókönyv lehet, hogy hozzon létre egy [hálózati biztonsági csoport][virtual-networks-nsg] hozzáférési szabályok meghatározásához.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Nyomtatás helyi hálózati nyomtatón az Azure-beli SAP-példányból

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Nyomtatás TCP/IP protokollon keresztül több helyiségen keresztül

A helyszíni TCP/IP-alapú hálózati nyomtatók beállítása egy Azure virtuális gépen összességében megegyezik a vállalati hálózattal, feltéve, hogy vpn-hely közötti alagúttal vagy ExpressRoute-kapcsolattal rendelkezik.

---
> ![Windows][Logo_Windows] Windows
>
> Ehhez tegye a következőket:
>
> * Egyes hálózati nyomtatók hoz egy konfigurációs varázsló, amely megkönnyíti a nyomtató beállítása egy Azure virtuális gép. Ha a nyomtatóval nem osztottak varázslószoftvert, a nyomtató beállításának manuális módja egy új TCP/IP nyomtatóport létrehozása.
> * A Vezérlőpult -> eszközök és nyomtatók -> Nyomtató hozzáadása
> * Válassza a Nyomtató hozzáadása TCP/IP-címmel vagy állomásnévvel lehetőséget
> * Írja be a nyomtató IP-címét
> * Nyomtatóport szabvány 9100
> * Szükség esetén telepítse manuálisan a megfelelő nyomtató-illesztőprogramot.
>
> ![Linux][Logo_Linux] Linux
>
> * mint a Windows csak kövesse a szokásos eljárást telepíteni a hálózati nyomtató
> * csak kövesse a nyilvános Linux útmutatókat a [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) vagy a [Red Hat és](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) az Oracle Linux számára a nyomtató hozzáadásához.
>
>

---
![Hálózati nyomtatás][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Gazdanyomtató SMB-n (megosztott nyomtatón) keresztül, telephelyközi környezetben

Az állomásalapú nyomtatók nem rendelkeznek hálózatkompatibilisszámítógépekkel. Az állomásalapú nyomtató azonban megosztható a hálózat számítógépei között, feltéve, hogy a nyomtató egy bekapcsolt számítógéphez csatlakozik. Csatlakoztassa a vállalati hálózatot a helyek közötti vagy az ExpressRoute-hoz, és ossza meg a helyi nyomtatót. Az SMB protokoll a DNS helyett a NetBIOS protokollt használja névszolgáltatásként. A NetBIOS-állomásnév eltérhet a DNS-állomásnévtől. A szabványos eset az, hogy a NetBIOS-állomásnév és a DNS-állomásnév azonos. A DNS-tartománynak nincs értelme a NetBIOS névtérben. Ennek megfelelően a DNS-állomásnévből és DNS-tartományból álló teljesen minősített DNS-állomásnév nem használható a NetBIOS-névtérben.

A nyomtatómegosztást a hálózat ban egy egyedi név azonosítja:

* Az SMB-állomás állomásneve (mindig szükséges).
* A megosztás neve (mindig szükséges).
* A tartomány neve, ha a nyomtatómegosztás nem ugyanabban a tartományban van, mint az SAP-rendszer.
* Ezenkívül a nyomtatómegosztás eléréséhez felhasználónévre és jelszóra is szükség lehet.

Útmutató:

---
> ![Windows][Logo_Windows] Windows
>
> Ossza meg a helyi nyomtatót.
> Az Azure virtuális gép, nyissa meg a Windows Intéző, és írja be a megosztás nevét a nyomtató.
> A nyomtatótelepítő varázsló végigvezeti a telepítési folyamaton.
>
> ![Linux][Logo_Linux] Linux
>
> Íme néhány példa a hálózati nyomtatók Linuxon történő konfigurálásáról szóló dokumentációra, vagy a Linux-nyomtatásra vonatkozó fejezetre. Ugyanúgy fog működni az Azure Linux virtuális gépeken, ha a virtuális gép egy VPN része:
>
> * SLES<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL vagy Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB-nyomtató (nyomtató továbbítása)

Az Azure-ban a távoli asztali szolgáltatások azon képessége, hogy a felhasználók számára a hozzáférést a helyi nyomtatóeszközök egy távoli munkamenetben nem érhető el.

---
> ![Windows][Logo_Windows] Windows
>
> További részletek a nyomtatás a Windows <https://technet.microsoft.com/library/jj590748.aspx>megtalálható itt: .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Az SAP Azure-rendszerek integrálása a helyesbítő és szállítórendszerbe (TMS) a telephelyek közötti környezetben

Az SAP változási és szállítási rendszer (TMS) kell konfigurálni, hogy exportálja és importálja a szállítási kérelmet a rendszerek között a táj. Feltételezzük, hogy az SAP-rendszer (DEV) fejlesztési példányai az Azure-ban találhatók, míg a minőségbiztosítás (QA) és a produktív rendszerek (PRD) a helyszíni. Továbbá feltételezzük, hogy van egy központi átviteli könyvtár.

##### <a name="configuring-the-transport-domain"></a>Az átviteli tartomány konfigurálása

Konfigurálja az átviteli tartományt azon a rendszeren, amelyet átviteli tartományvezérlőként jelölt meg [az átviteli tartományvezérlő konfigurálása](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)című témakörben leírtak szerint. Létrejön egy rendszerhasználó, a TMSADM, és létrejön a szükséges RFC-cél. Ezeket az RFC-kapcsolatokat az SM59 tranzakció val ellenőrizheti. A gazdanév felbontását engedélyezni kell az átviteli tartományban.

Útmutató:

* A mi forgatókönyvünkben úgy döntöttünk, hogy a helyszíni QAS rendszer lesz a CTS tartományvezérlő. Hívástranzakció STMS. Megjelenik a TMS párbeszédpanel. Megjelenik az Átviteli tartomány konfigurálása párbeszédpanel. (Ez a párbeszédpanel csak akkor jelenik meg, ha még nem konfigurált átviteli tartományt.)
* Győződjön meg arról, hogy az automatikusan létrehozott TMSADM felhasználó jogosult TMSADM@E61.DOMAIN_E61 (SM59 -> ABAP connection -> -> Details -> Utilities(M) -> engedélyezési teszt). A tranzakciós STMS kezdeti képernyőjének meg kell mutatnia, hogy ez az SAP rendszer most már a szállítási tartomány vezérlőjeként működik, ahogy az itt látható:

![A tartományvezérlőn a tranzakciós STMS kezdeti képernyője][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Beleértve az SAP-rendszereket a szállítási tartományban

Az SAP-rendszer átviteli tartományba való belefoglalásának sorrendje a következőképpen néz ki:

* Az Azure dev rendszerében nyissa meg az átviteli rendszert (Client 000) és hívja meg az STMS tranzakciót. Válassza az Egyéb konfiguráció lehetőséget a párbeszédpanelen, és folytassa a Rendszer felvétele a tartományba lehetőséggel. Adja meg a tartományvezérlőt célállomásként ([beleértve az SAP-rendszereket is az átviteli tartományban](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). A rendszer most arra vár, hogy bekerülj a szállítási tartományba.
* Biztonsági okokból ezután vissza kell mennie a tartományvezérlőhöz a kérés megerősítéséhez. Válassza a Rendszer áttekintése és a várakozási rendszer jóváhagyása lehetőséget. Ezután erősítse meg a kérdést, és a konfiguráció ellesz osztva.

Ez az SAP-rendszer most már tartalmazza a szükséges információkat az összes többi SAP-rendszer ről a szállítási tartományban. Ugyanakkor az új SAP-rendszer címadatait elküldi az összes többi SAP-rendszernek, és az SAP-rendszert bejegyezi a szállítási vezérlőprogram átviteli profiljába. Ellenőrizze, hogy az RFC-k és a tartomány átviteli könyvtárához való hozzáférés működik-e.

Folytassa a szállítási rendszer beállítását a szokásos módon, ahogy azt a Módosítás i és szállítási rendszer című dokumentáció [ismerteti.](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)

Útmutató:

* Győződjön meg arról, hogy a helyszíni STMS megfelelően van konfigurálva.
* Győződjön meg arról, hogy a transport domain controller állomásnevét az Azure-on és a vice-vízumon lévő virtuális gép feloldhatja.
* Hívástranzakció STMS -> Egyéb konfiguráció -> A rendszer felvétele a tartományba.
* Erősítse meg a kapcsolatot a helyszíni TMS rendszerben.
* Az átviteli útvonalak, csoportok és rétegek konfigurálása a szokásos módon.

A helyek közötti összekapcsolt létesítmények közötti forgatókönyvek, a helyszíni és az Azure közötti késés továbbra is jelentős lehet. Ha követjük a tárgyak szállítási sorrendjét a fejlesztési és vizsgálati rendszereken keresztül a termelésbe, vagy elgondolkodunk a szállítások vagy támogatási csomagok különböző rendszerekre történő alkalmazásán, rájössz, hogy a központi szállítási könyvtár helyétől függően, egyes rendszerek nagy késleltetésű olvasási vagy adatírási adatokat fognak észlelni a központi átviteli címtárban. A helyzet hasonló az SAP fekvő konfigurációk, ahol a különböző rendszerek között elosztva a különböző adatközpontok jelentős távolság az adatközpontok között.

Az ilyen késés megkerülése és annak érdekében, hogy a rendszerek gyorsan működjenek olvasás vagy írás során, és az átviteli címtárból két STMS átviteli tartományt állíthat be (egyet a helyszíni, egyet pedig az Azure-beli rendszerekkel, és összekapcsolhatja az átviteli tartományokat. Kérjük, ellenőrizze ezt a dokumentációt, amely elmagyarázza <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>a koncepció mögött megmaradtak elveket az SAP TMS-ben: .

Útmutató:

* Átviteli tartomány beállítása minden helyen (helyszíni és Az Azure-ban) tranzakciós STMS használatával<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Kapcsolja össze a tartományokat egy tartományhivatkozással, és erősítse meg a kapcsolatot a két tartomány között.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* A konfiguráció elosztása a csatolt rendszerre.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-forgalom az Azure-ban és a helyszíni (telephelyek közötti) SAP-példányok között

RFC-forgalom rendszerek között, amelyek a helyszíni és az Azure-ban kell működnie. Az SM59-es csatlakozási hívástranzakció beállítása olyan forrásrendszerben, ahol rfc-kapcsolatot kell definiálni a célrendszerrel. A konfiguráció hasonló az RFC-kapcsolat szabványos beállításához.

Feltételezzük, hogy a létesítmények közötti forgatókönyvben a virtuális gépek, amelyek az egymással kommunikáló SAP-rendszereket futtatnak, ugyanabban a tartományban vannak. Ezért az SAP-rendszerek közötti RFC-kapcsolat beállítása nem tér el a helyszíni forgatókönyvek beállítási lépéseitől és bemeneteitől.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Helyi fájlmegosztások elérése az Azure-ban található SAP-példányokból vagy fordítva

Az Azure-ban található SAP-példányok nak hozzá kell férniük a vállalati telephelyen belüli fájlmegosztásokhoz. Emellett a helyszíni SAP-példányok kell elérni fájlmegosztások, amelyek az Azure-ban található. A fájlmegosztások engedélyezéséhez konfigurálnia kell az engedélyeket és a megosztási beállításokat a helyi rendszeren. Győződjön meg arról, hogy megnyitja a portokat a VPN vagy ExpressRoute kapcsolat az Azure és az adatközpont között.

## <a name="supportability"></a>Támogatási lehetőségek

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure-bővítmény az SAP-hoz

Annak érdekében, hogy az Azure-infrastruktúra adatainak egy részét a kritikus fontosságú SAP-rendszerek az SAP host agent példányok, virtuális gépeken telepített, egy Azure (VM) bővítmény sap kell telepíteni az üzembe helyezett virtuális gépek. Mivel az SAP igényei az SAP-alkalmazásokra vonatkoztak, a Microsoft úgy döntött, hogy általánosan nem valósítja meg a szükséges funkciókat az Azure-ban, de hagyja, hogy az ügyfelek telepítsék a szükséges virtuálisgép-bővítményt és konfigurációkat a futó virtuális gépekre az Azure-ban. Az Azure VM-bővítmény sap-hoz való üzembe helyezését és életciklus-kezelését azonban többnyire az Azure automatikusan fogja eltekinteni.

#### <a name="solution-design"></a>Megoldásterv

Az SAP Host Agent szükséges információk beszerzése érdekében kifejlesztett megoldás az Azure VM-ügynök és az Extension framework architektúráján alapul. Az Azure VM-ügynök és -bővítmény keretrendszer ötlete az, hogy lehetővé tegye az Azure VM Extension-gyűjteményben egy virtuális gépen elérhető szoftveralkalmazások telepítését. A koncepció alapelve, hogy lehetővé tegye (például az Azure Extension for SAP), a speciális funkciók üzembe helyezése egy virtuális gép, és az ilyen szoftver konfigurációját a központi telepítés idején.

Az "Azure VM-ügynök", amely lehetővé teszi az adott Azure virtuálisgép-bővítmények kezelését a virtuális gépen belül alapértelmezés szerint a virtuális gépek létrehozása az Azure Portalon. SUSE, Red Hat vagy Oracle Linux esetén a virtuálisgép-ügynök már része az Azure Marketplace-rendszerkép. Abban az esetben, ha egy linuxos virtuális gép a helyszíni Azure-ba a virtuális gép ügynök manuálisan kell telepíteni.

Az Azure-infrastruktúra-információk azure-beli azure-beli ügynöknek az Azure-beli adatok szolgáltatására vonatkozó alapvető építőelemei a következőképpen néznek ki:

![Microsoft Azure Extension-összetevők][planning-guide-figure-2400]

Amint azt a fenti blokkdiagram, a megoldás egy része az Azure Virtuálisgép-lemezkép és az Azure Extension Gallery, amely egy globálisan replikált tárház, amely az Azure Operations által felügyelt. A közös SAP/MS csapat feladata, amely az SAP Azure-megvalósításán dolgozik, hogy az Azure Operations-szel együttműködve közzétegye az Azure Extension for SAP új verzióit.

Új Windows virtuális gép telepítésekor az Azure virtuálisgép-ügynök automatikusan hozzáadódik a virtuális géphez. Ez az ügynök feladata, hogy koordinálja a virtuális gépek Azure-bővítményeinek betöltését és konfigurálását. Linuxos virtuális gépek esetén az Azure virtuálisgép-ügynök már része az Azure Marketplace operációs rendszerrendszer-rendszer.

Van azonban egy lépés, amelyet még végre kell hajtania az ügyfélnek. Ez a teljesítménygyűjtemény engedélyezése és konfigurálása. A konfigurációhoz kapcsolódó folyamat ot PowerShell-parancsfájl vagy CLI-parancs automatikusan automatizálja. A PowerShell-parancsfájl letölthető a Microsoft Azure Script Center [betöltendő a telepítési útmutatóban leírtak szerint.][deployment-guide]

Az SAP-bővítmény általános architektúrája így néz ki:

![Azure-bővítmény az SAP-hoz ][planning-guide-figure-2500]

**A powershell-parancsmagok vagy cli-parancsok központi telepítések során történő használatának pontos útmutatóját és részletes lépéseit a [központi telepítési útmutatóutasításait][deployment-guide]kell követnie.**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Az Azure-ban található SAP-példány integrációja a SAProuter beépítésébe

Az Azure-ban futó SAP-példányokat a SAProuter-ről is elérhetőnek kell lenniük.

![SAP-router hálózati kapcsolat][planning-guide-figure-2600]

A SAProuter lehetővé teszi a TCP/IP kommunikációt a részt vevő rendszerek között, ha nincs közvetlen IP-kapcsolat. Ez azt az előnyt biztosítja, hogy hálózati szinten nincs szükség végpontok közötti kapcsolatra a kommunikációs partnerek között. A SAProuter alapértelmezés szerint a 3299-es porton figyel.
Sap-példányok saprouteren keresztül történő csatlakoztatásához meg kell adnia a SAProuter karakterláncot és az állomásnevet a kapcsolódási kísérletekkel.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Eddig a hangsúly a dokumentum már SAP NetWeaver általában vagy az SAP NetWeaver ABAP verem. Ebben a kis szakaszban az SAP Java verem speciális szempontjai vannak felsorolva. Az egyik legfontosabb SAP NetWeaver Java kizárólag alapú alkalmazások az SAP Enterprise Portal. Más SAP NetWeaver alapú alkalmazások, például az SAP PI és az SAP Solution Manager az SAP NetWeaver ABAP és a Java halmokat egyaránt használják. Ezért minden bizonnyal szükség van, hogy fontolja meg a konkrét szempontokat kapcsolatos SAP NetWeaver Java verem is.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

Az SAP-portál beállítása egy Azure virtuális gép nem különbözik a helyszíni telepítés, ha a létesítmények közötti forgatókönyvekben üzembe helyezése. Mivel a DNS-t a helyszíni, az egyes példányok portbeállításai tetszhet a konfigurált helyszíni beállításokat. A jelen dokumentumban leírt javaslatok és korlátozások eddig egy olyan alkalmazásra vonatkoznak, mint az SAP Enterprise Portal vagy általában az SAP NetWeaver Java verem.

![Elérhetővé tett SAP-portál][planning-guide-figure-2700]

Egyes ügyfelek speciális központi telepítési forgatókönyve az SAP Enterprise Portal közvetlen internetes kitettsége, miközben a virtuálisgép-gazdagép a telephelyek közötti VPN-alagúton vagy expressroute-on keresztül csatlakozik a vállalati hálózathoz. Ilyen esetben meg kell győződnie arról, hogy bizonyos portok meg vannak nyitva, és nem blokkolja a tűzfal vagy a hálózati biztonsági csoport. 

A kezdeti portál URI http(s):`<Portalserver`>:5XX00/irj, ahol a port <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>az SAP által dokumentált formában van létrehozva.

![Végpont konfigurációja][planning-guide-figure-2800]

Ha testre szeretné szabni az SAP Enterprise Portal URL-címét és/vagy portjait, olvassa el ezt a dokumentációt:

* [Portál URL-címének módosítása](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alapértelmezett portszámok módosítása, portálportszámok](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Magas rendelkezésre állás (HA) és vész-helyreállítási (DR) az Azure virtuális gépeken futó SAP NetWeaver számára

### <a name="definition-of-terminologies"></a>A terminológiák meghatározása

A **magas rendelkezésre állás (HA)** kifejezés általában olyan technológiák készletéhez kapcsolódik, amelyek minimálisra csökkentik az informatikai zavarokat azáltal, hogy redundáns, hibatűrő vagy feladatátvételi védett összetevőkön keresztül biztosítják az informatikai szolgáltatások üzletmenet-folytonosságát **ugyanazon** adatközponton belül. A mi esetünkben egy Azure-régión belül.

**Vész-helyreállítási (DR)** is célozza minimalizálja az informatikai szolgáltatások zavar, és azok helyreállítási, de **a különböző** adatközpontok, amelyek általában több száz kilométerre található. A mi esetünkben általában a különböző Azure-régiók között ugyanazon a geopolitikai régióban, vagy az Ön által létrehozott, mint egy ügyfél.

### <a name="overview-of-high-availability"></a>A magas rendelkezésre állás áttekintése

Az Azure-ban az SAP magas rendelkezésre állásáról szóló vitát két részre bonthatjuk:

* **Az Azure-infrastruktúra magas rendelkezésre állású,** például ha a számítási (VM), hálózat, tárolás stb.
* **SAP alkalmazás magas rendelkezésre állású**, például HA SAP szoftver összetevők:
  * SAP alkalmazáskiszolgálók
  * SAP ASCS/SCS-példány
  * DB-kiszolgáló

és hogyan kombinálható az Azure-infrastruktúra HA.

Az Azure-ban az SAP magas rendelkezésre állása van néhány különbség, mint az SAP magas rendelkezésre állása a helyszíni fizikai vagy virtuális környezetben. Az SAP következő dokumentuma a Windows virtualizált környezetében lévő <https://scn.sap.com/docs/DOC-44415>SAP high availability standard konfigurációit ismerteti: . Nincs sapinst integrált SAP-HA konfiguráció Linuxhoz, mint a Windows. Ami az SAP HA-t illeti, a <https://scn.sap.com/docs/DOC-8541>Linux helyszíni területén további információkat talál itt: .

### <a name="azure-infrastructure-high-availability"></a>Az Azure infrastruktúra magas rendelkezésre állása

Jelenleg egy vm SLA 99,9%-os. Ha egy ötlet, hogy egy virtuális gép rendelkezésre állása hogyan nézhet ki, létrehozhatja <https://azure.microsoft.com/support/legal/sla/>a különböző rendelkezésre álló Azure SLA-k termékét: .

A számítás alapja havi 30 nap, vagy 43200 perc. Ezért a 0,05%-os állásidő 21,6 percnek felel meg. Mint mindig, a rendelkezésre álló különböző szolgáltatások megsokszorozódik a következő módon:

(Elérhetőségi szolgáltatás #1/100) * (elérhetőségi szolgáltatás #2/100) * (elérhetőségi szolgáltatás #3/100) 

Mint:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 vagy 99,75%-os teljes rendelkezésre állás.

#### <a name="virtual-machine-vm-high-availability"></a>Virtuális gép (VM) magas rendelkezésre állása

Az Azure platformeseményeknek két típusa van, amelyek befolyásolhatják a virtuális gépek rendelkezésre állását: a tervezett karbantartás és a nem tervezett karbantartás.

* A tervezett karbantartási események a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések a virtuális gépeket futtató platforminfrastruktúra általános megbízhatóságának, teljesítményének és biztonságának növelése érdekében.
* A nem tervezett karbantartási események akkor következnek be, ha a virtuális gép mögöttes hardveres vagy fizikai infrastruktúrája valamiképp meghibásodik. Ez lehet helyi hálózati hiba, a helyi lemezek meghibásodása, vagy egyéb állványszintű meghibásodások. Ilyen hiba észlelésekor az Azure platform automatikusan áttelepíti a virtuális gépet a virtuális gépet üzemeltető, nem megfelelő fizikai kiszolgálóról egy kifogástalan állapotú fizikai kiszolgálóra. Ilyen esetek ritkán lépnek fel, de ezek is okozhatják a virtuális gép újraindítását.

További részletek ebben a dokumentációban találhatók:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure storage-redundancia

A Microsoft Azure Storage-fiókban lévő adatok mindig replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében, és az Azure Storage SLA-nak még átmeneti hardverhibák esetén is megfelelnek.

Mivel az Azure Storage alapértelmezés szerint három rendszerképet tárol az adatokról, a RAID5 vagy a RAID1 több Azure-lemezen nem szükséges.

További részletek találhatók ebben a cikkben:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Az Azure Infrastructure virtuális gép újraindításának kihasználása az SAP-alkalmazások magasabb rendelkezésre állásának elérése érdekében

Ha úgy dönt, hogy nem használ olyan funkciókat, mint a Windows Server feladatátvételi fürtözése (WSFC) vagy a Pacemaker Linuxon (jelenleg csak az SLES 12-es és újabb verzióiban támogatott), az Azure VM Restart az SAP-rendszer védelmét használja az Azure tervezett és nem tervezett állásidejével szemben fizikai kiszolgálói infrastruktúra és az alapul szolgáló Azure-platform.

> [!NOTE]
> Fontos megemlíteni, hogy az Azure VM Restart elsősorban a virtuális gépek és a NOT-alkalmazások védelme. A virtuális gép újraindítása nem kínál magas rendelkezésre állást az SAP-alkalmazások számára, de az infrastruktúra bizonyos szintű rendelkezésre állását, és így közvetetten az SAP-rendszerek magasabb rendelkezésre állását. Nincs is SLA a virtuális gép újraindításához szükséges idő egy tervezett vagy nem tervezett gazdagép kimaradás után. Ezért ez a magas rendelkezésre állási módszer nem alkalmas az SAP-rendszer kritikus összetevőihez, például az (A)SCS vagy DBMS-hez.
>
>

A magas rendelkezésre állás másik fontos infrastrukturális eleme a tárolás. Például az Azure Storage SLA 99,9%-os rendelkezésre állása. Ha az egyik telepíti az összes virtuális gép a lemezekkel egyetlen Azure Storage-fiók, a potenciális Azure Storage elérhetetlenség éhezés az adott Azure Storage-fiókban elhelyezett összes virtuális gép, valamint az összes SAP-összetevők futó belül a virtuális gépek.  

Ahelyett, hogy az összes virtuális gépet egyetlen Azure Storage-fiókba helyezné, minden virtuális géphez dedikált tárfiókokat is használhat, és ily módon növelheti a virtuális gép és az SAP-alkalmazások teljes rendelkezésre állását több független Azure Storage-fiók használatával.

Az Azure felügyelt lemezek automatikusan elhelyezve a tartalék tartománya a virtuális gép, amelyhez kapcsolódnak. Ha két virtuális gépet helyez el egy rendelkezésre állási csoportban, és felügyelt lemezeket használ, a platform gondoskodik a felügyelt lemezek különböző tartalék tartományokba történő elosztásáról is. Ha a Prémium szintű storage-t szeretné használni, javasoljuk a Lemezek kezelése is használatát.

Az Azure Infrastructure HA-t és storage-fiókokat használó SAP NetWeaver rendszer mintaarchitektúrája a következőkre néz ki:

![Az Azure-infrastruktúra HA-jának kihasználása az SAP-alkalmazások magasabb rendelkezésre állásának eléréséhez][planning-guide-figure-2900]

Az Azure-infrastruktúrát HA és felügyelt lemezeket használó SAP NetWeaver rendszer mintaarchitektúrája a következőkre néz ki:

![Az Azure-infrastruktúra HA-jának kihasználása az SAP-alkalmazások magasabb rendelkezésre állásának eléréséhez][planning-guide-figure-2901]

A kritikus SAP-összetevők esetében eddig a következőket értük el:

* Az SAP alkalmazáskiszolgálók (AS) magas rendelkezésre állása

  Az SAP alkalmazáskiszolgáló-példányok redundáns összetevők. Minden SAP AS-példány saját virtuális gépén van telepítve, amely egy másik Azure-tartalék és frissítési tartományban fut (lásd: [tartalék tartományok][planning-guide-3.2.1] és [frissítési tartományok fejezetek).][planning-guide-3.2.2] Ezt az Azure rendelkezésre állási készletei biztosítják (lásd az [Azure rendelkezésre állási csoportjainak fejezetét).][planning-guide-3.2.3] Az Azure-hiba vagy frissítési tartomány potenciálisan tervezett vagy nem tervezett elérhetetlensége korlátozott számú virtuális gép nem lesz elérhető az SAP AS-példányokkal.

  Minden EGYES SAP AS-példány a saját Azure Storage-fiókjába kerül– egy Azure Storage-fiók potenciális elérhetetlensége csak egy virtuális gép nem érhető el az SAP AS-példánysal. Azonban ne feledje, hogy egy Azure Storage-fiókok egy Azure-előfizetésen belül korlátozva van. Az (A)SCS-példány automatikus indításának biztosításához a virtuális gép újraindítása után győződjön meg arról, hogy az Autostart paramétert (A)SCS-példány indítási profilját az [SAP-példányok autostart használata][planning-guide-11.5]című fejezetben ismertetett szakaszban kell beállítani.
  További részletekért olvassa el [az SAP alkalmazáskiszolgálók magas rendelkezésre állású fejezetét][planning-guide-11.4.1] is.

  Még akkor is, ha felügyelt lemezeket használ, ezek a lemezek is egy Azure Storage-fiókban tárolódnak, és nem érhetők el egy tárolási kimaradás esetén.

* *Magasabb* Az SAP (A)SCS-példány elérhetősége

  Itt az Azure VM Restart használatával védjük a virtuális gép telepített SAP (A)SCS-példányt. Az Azure-megszakítások tervezett vagy nem tervezett állásidejének esetén a virtuális gépek újraindulnak egy másik elérhető kiszolgálón. Mint korábban említettük, az Azure VM újraindítása elsősorban a virtuális gépek és a NOT-alkalmazások, ebben az esetben az (A)SCS-példány. A virtuális gép újraindítása, akkor közvetetten magasabb rendelkezésre állású SAP (A)SCS-példány. Az (A)SCS-példány automatikus indításának biztosításához a virtuális gép újraindítása után győződjön meg arról, hogy az Autostart paramétert az (A)SCS-példány indítási profiljában az [SAP-példányok automatikus indítása][planning-guide-11.5]című fejezetben ismertetett szakaszban kell beállítani. Ez azt jelenti, hogy az (A)SCS-példány egyetlen meghibásodási pontként (SPOF) egyetlen virtuális gépként futó lesz a meghatározó tényező a teljes SAP-környezet rendelkezésre állása.

* *Magasabb* A DBMS-kiszolgáló elérhetősége

  Itt az SAP (A)SCS-példány használati esetéhez hasonlóan az Azure VM Restart szolgáltatást használjuk a virtuális gép telepítésével ellátott DBMS-szoftverrel való védelméhez, és a virtuális gép újraindítása révén a DBMS-szoftverek nagyobb rendelkezésre állását érjük el.
  Az egyetlen virtuális gépben futó ADATBÁZIS-kezelő is egy SPOF, és ez a meghatározó tényező a teljes SAP-környezet rendelkezésre állása.

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP-alkalmazás magas rendelkezésre állás az Azure IaaS-on

A teljes SAP-rendszer magas rendelkezésre állásának elérése érdekében meg kell védenünk az összes kritikus SAP-rendszerösszetevőt, például a redundáns SAP-alkalmazáskiszolgálókat és az egyedi összetevőket (például az egyhibapontú) például az SAP (A)SCS-példányt és a DBMS-t.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Az SAP alkalmazáskiszolgálók magas rendelkezésre állása

Az SAP-alkalmazáskiszolgálók/párbeszédpéldányok esetében nem szükséges egy adott magas rendelkezésre állású megoldásra gondolni. A magas rendelkezésre állás redundanciával érhető el, és ezáltal elegendő a különböző virtuális gépeken. Az összes kell helyezni ugyanabban az Azure-ban rendelkezésre állási készlet elkerülése érdekében, hogy a virtuális gépek lehet frissíteni egy időben a tervezett karbantartási állásidő alatt. Az Azure Scale-egységen belüli különböző frissítési és tartalék tartományokra épülő alapvető funkciók már be [kerültek][planning-guide-3.2.2]a Tartományok frissítése című fejezetben. Az Azure rendelkezésre állási készletei ebben a dokumentumban az [Azure rendelkezésre állási készletei][planning-guide-3.2.3] című fejezetben kerültek bemutatásra.

Nincs végtelen számú tartalék és frissítési tartományok, amelyek et egy Azure-ban rendelkezésre állási készlet egy Azure Scale Egységen belül használható. Ez azt jelenti, hogy egy virtuális gép egy rendelkezésre állási csoportba helyezése, előbb vagy utóbb egynél több virtuális gép ugyanabban a tartalék vagy frissítési tartományban kerül sor.

Néhány SAP-alkalmazáskiszolgáló-példány telepítése a dedikált virtuális gépekben, és feltételezve, hogy öt frissítési tartományt kaptunk, a következő kép jelenik meg a végén. A hiba- és frissítési tartományok tényleges maximális száma egy rendelkezésre állási csoporton belül a jövőben változhat:

![AZ SAP alkalmazáskiszolgálók HA-ja az Azure-ban][planning-guide-figure-3000]

További részletek ebben a dokumentációban találhatók:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Az SAP központi szolgáltatásainak magas rendelkezésre állása az Azure-ban

Az SAP Központi szolgáltatások azure-beli magas rendelkezésre állású architektúrája az [Azure-ban a magas rendelkezésre állású architektúra és az SAP NetWeaver forgatókönyvei](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) bejegyzési információként című cikkben olvashat. A cikk az adott operációs rendszerek részletesebb leírására mutat rá.

#### <a name="high-availability-for-the-sap-database-instance"></a>Az SAP-adatbázispéldány magas rendelkezésre állása

A tipikus SAP DBMS HA telepítő két DBMS virtuális gépen alapul, ahol a DBMS magas rendelkezésre állású funkciók az aktív DBMS-példányból a második virtuális gépre egy passzív DBMS-példányba történő replikálására szolgálnak.

A dbms magas rendelkezésre állási és vész-helyreállítási funkcióit általában, valamint az egyes adatbázis-tudnivalókat a [DBMS telepítési útmutató][dbms-guide]ismerteti.

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>A teljes SAP-rendszer teljes körű magas rendelkezésre állása

Íme két példa a teljes SAP NetWeaver HA architektúrára az Azure-ban – egy Windows és egy Linux ra.

Csak nem felügyelt lemezek: Az alábbiakban ismertetett fogalmak előfordulhat, hogy egy kicsit veszélybe, ha sok SAP-rendszerek telepítésekor, és a telepített virtuális gépek száma meghaladja a storage-fiókok előfizetésenként maximális korlátját. Ilyen esetekben a virtuális gépek virtuális gépek virtuális gépek virtuális gépek virtuális gépek virtuális gépek virtuális gépek virtuális gépek virtuális gépek egyetlen tárfiókon belül kell kombinálni. Általában ezt a különböző SAP-rendszerek SAP-alkalmazásréteg-virtuális gépeinek Virtuális számítógépeinek virtuális számítógépeinek kombinálásával tenné meg.  A különböző SAP-rendszerek különböző DBMS virtuális gépeinek különböző Virtuális gépeit is kombináltuk egy Azure Storage-fiókban. Ezáltal az Azure Storage-fiókok IOPS-korlátait szem előtt tartva (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA windowson

![SAP NetWeaver application HA architektúra az SQL Server rel az Azure IaaS-ban][planning-guide-figure-3200]

Az SAP NetWeaver rendszerhez a következő Azure-konstrukciókat használják az infrastruktúra-problémák és a gazdagép-javítás hatásainak minimalizálása érdekében:

* A teljes rendszer telepítve van az Azure-ban (kötelező – DBMS-réteg, (A)SCS-példány és a teljes alkalmazásréteg ugyanazon a helyen kell futnia).
* A teljes rendszer egy Azure-előfizetésen belül fut (kötelező).
* A teljes rendszer egy Azure virtuális hálózaton belül fut (kötelező).
* Egy SAP-rendszer virtuális gépeinek három rendelkezésre állási csoportra történő elkülönítése még az azonos virtuális hálózathoz tartozó összes virtuális gép esetén is lehetséges.
* Minden rétegnek (például DBMS, ASCS, Application Servers) dedikált rendelkezésre állási csoportot kell használnia.
* Egy SAP-rendszer ADATBÁZIS-rendszerpéldányait futtató összes virtuális gép egy rendelkezésre állási csoportban van. Feltételezzük, hogy rendszerenként egynél több virtuális gép futtatja az ADATBÁZIS-példányokat, mivel natív DBMS magas rendelkezésre állású szolgáltatásokat használnak, például az SQL Server AlwaysOn vagy az Oracle Data Guard.
* Az ADATBÁZIS-példányokat futtató összes virtuális gép saját tárfiókot használ. A DBMS-adatok és a naplófájlok replikálása az egyik tárfiókból egy másik tárfiókba történik az adatokat szinkronizáló, magas rendelkezésre állású adatbázis-funkciók használatával. Egy tárfiók elérhetetlensége egy SQL Windows fürtcsomópont elérhetetlenségét okozza, de a teljes SQL Server-szolgáltatást nem.
* Egy SAP-rendszer (A)SCS-példányát futtató összes virtuális gép egy rendelkezésre állási csoportban van. A Windows Server feladatátvevő fürt (WSFC) van konfigurálva belül a virtuális gépek, hogy megvédje az (A)SCS-példány.
* Az (A)SCS-példányok futó összes virtuális gép saját tárfiókot használ. A) a) Az SCS-példányfájlok és az SAP globális mappája replikálódik az egyik tárfiókból egy másik tárfiókba a SIOS DataKeeper replikáció használatával. Egy tárfiók elérhetetlensége egy (A)SCS Windows fürtcsomópont elérhetetlenségét okozza, de nem a teljes (A)SCS-szolgáltatást.
* Az SAP-alkalmazáskiszolgálói réteget képviselő összes virtuális gép egy harmadik rendelkezésre állási csoportban található.
* Az SAP-alkalmazáskiszolgálókat futtató összes virtuális gép saját tárfiókot használ. Egy tárfiók elérhetetlensége egy SAP-alkalmazáskiszolgáló elérhetetlenségét okozza, ahol a többi SAP-alkalmazáskiszolgáló továbbra is fut.

Az alábbi ábra ugyanazt a fekvő tájképet szemléltette a Felügyelt lemezek használatával.

![SAP NetWeaver application HA architektúra az SQL Server rel az Azure IaaS-ban][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA Linuxon

Az SAP HA architektúrája az Azure-on alapvetően ugyanaz, mint a Windows esetében, afent leírtak szerint. A támogatott magas rendelkezésre állású megoldások listáját az SAP [Note 1928533] című részében találja.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Az Automatikus indítás használata SAP-példányokhoz

Az SAP felajánlotta a funkciót az SAP-példányok indításához közvetlenül az operációs rendszer indítása után a virtuális gépen belül. A pontos lépéseket az SAP Tudásbázis [1909114.] Az SAP azonban nem javasolja a beállítás használatát, mert nincs vezérlő a példány újraindításának sorrendjében, feltételezve, hogy egynél több virtuális gép érintett, vagy több példány fut virtuális gépenként. Feltételezve, hogy egy tipikus Azure-forgatókönyv egy SAP-alkalmazáskiszolgáló-példány egy virtuális gép, és az esetben, ha egy virtuális gép végül újraindul, az automatikus indítás nem kritikus, és engedélyezhető a paraméter hozzáadásával:

    Autostart = 1

Az SAP ABAP és/vagy Java-példány kezdő profiljába.

> [!NOTE]
> Az Autostart paraméternek is lehetnek downfalls-ai. Részletesebben a paraméter elindítja egy SAP ABAP vagy Java példány indítását, amikor a példány kapcsolódó Windows/Linux szolgáltatása elindul. Ez minden bizonnyal a helyzet, amikor az operációs rendszer elindul. Azonban az SAP-szolgáltatások újraindítása is gyakori dolog az SAP szoftver életciklus-kezelési funkciók, például a SUM vagy más frissítések vagy frissítések. Ezek a funkciók egyáltalán nem számítanak arra, hogy egy példány automatikusan újraindul. Ezért az automatikus indítás paramétert le kell tiltani az ilyen feladatok futtatása előtt. Az Automatikus indítás paraméter t sem szabad használni a fürtözött SAP-példányokhoz, például az ASCS/SCS/CI-hez.
>
>

Az SAP-példányok automatikus indításával kapcsolatos további információkért itt olvashat:

* [Az SAP indítása/leállítása a Unix Server start/stop szolgáltatással együtt](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Az SAP NetWeaver felügyeleti megbízottai indítása és leállítása](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [A HANA-adatbázis automatikus indításának engedélyezése](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Nagyobb, 3 rétegű SAP-rendszerek
A 3 rétegű SAP-konfigurációk magas rendelkezésre állású szempontjai már a korábbi szakaszokban is megvitatásra kerültek. De mi a helyzet a rendszerekkel, ahol a DBMS-kiszolgáló követelményei túl nagyok ahhoz, hogy az Azure-ban található, de az SAP alkalmazásréteg telepíthető az Azure-ba?

#### <a name="location-of-3-tier-sap-configurations"></a>Háromrétegű SAP-konfigurációk helye
Nem támogatott az alkalmazásréteg vagy az alkalmazás és a DBMS szint felosztása a helyszíni és az Azure között. Az SAP-rendszer vagy teljesen üzembe helyezett a helyszínen vagy az Azure-ban. Az is nem támogatott, hogy az alkalmazáskiszolgálók némelyike a helyszínen, mások pedig az Azure-ban fussanak. Ez a vita kiindulópontja. Azt is nem támogatja, hogy a DBMS-összetevők egy SAP-rendszer és az SAP alkalmazáskiszolgáló-réteg két különböző Azure-régiókban üzembe helyezett. Például a DBMS az USA nyugati részén és az SAP alkalmazásréteg az USA középső részén. Az ilyen konfigurációk támogatásának oka az SAP NetWeaver architektúra késésérzékenysége.

A tavalyi év során azonban az adatközponti partnerek közös helyeket alakítottak ki az Azure Regions számára. Ezek a közös helyek gyakran közel vannak a fizikai Azure-adatközpontok egy Azure-régión belül. A rövid távolság és az eszközök kapcsolata a helymegosztáson keresztül az ExpressRoute-on keresztül az Azure-ba 2 ms-nál kisebb késést eredményezhet. Ilyen esetekben a DBMS-réteg (beleértve a tároló SAN/NAS) megkereséséhez egy ilyen hely megosztásán, és az SAP alkalmazásréteg az Azure-ban lehetséges. [HANA nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Az SAP-rendszerek offline biztonsági mentése
Az SAP-konfiguráció választott (2-tier vagy 3-tier) szükség lehet biztonsági másolatot. A virtuális gép tartalma, valamint az adatbázis biztonsági mentése. A DBMS-sel kapcsolatos biztonsági mentések várhatóan adatbázis-módszerekkel készülnek. A különböző adatbázisok részletes leírása a [DBMS Guide című részben][dbms-guide]található. Másrészt az SAP-adatok offline módon (beleértve az adatbázis tartalmát is) biztonsági másolatot lehet kapni, ahogy azt ebben a szakaszban vagy online leírtak szerint a következő szakaszban leírtak szerint.

Az offline biztonsági mentés alapvetően a virtuális gép leállítását az Azure Portalon keresztül, és az alap virtuálisgép-lemez és az összes csatlakoztatott lemez a virtuális géphez való leállítását. Ez megőrizné a virtuális gép és a hozzá tartozó lemez időképét. Javasoljuk, hogy másolja a biztonsági másolatokat egy másik Azure Storage-fiókba. Ezért a fejezetben leírt lemezek [másolása az Azure Storage-fiókok között][planning-guide-5.4.2] ebben a dokumentumban lenne alkalmazni.
Az Azure Portal használatával történő leállítás mellett a Powershellen vagy a CLI-n keresztül is megteheti az itt leírtak szerint:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Ennek az állapotnak a visszaállítása az alap virtuális gép, valamint az alap virtuális gép és a csatlakoztatott lemezek eredeti lemezeinek törléséből, a mentett lemezek nek az eredeti tárfiókba vagy erőforráscsoportba a felügyelt lemezekre való másolásából, majd a rendszer újratelepítéséből állna.
Ez a cikk egy példa, hogyan parancsfájl ezt a folyamatot a PowerShell:<http://www.westerndevs.com/azure-snapshots/>

Győződjön meg róla, hogy új SAP-licencet telepít, mivel a virtuális gép biztonsági másolatának visszaállítása a fent leírtak szerint új hardverkulcsot hoz létre.

### <a name="online-backup-of-an-sap-system"></a>Az SAP-rendszer online biztonsági mentése

A DBMS biztonsági mentése a DBMS-útmutatóban [DBMS Guide][dbms-guide]leírt DBMS-specifikus módszerekkel történik.

Az SAP-rendszeren belüli egyéb virtuális gépekről az Azure Virtual Machine Backup funkcióval lehet biztonsági másolatot készíteni. Az Azure Virtual Machine Backup egy szabványos módszer egy teljes virtuális gép biztonsági mentéséhez az Azure-ban. Az Azure Backup tárolja a biztonsági mentéseket az Azure-ban, és lehetővé teszi a virtuális gép visszaállítását.

> [!NOTE]
> 2015 decembere óta a virtuális gép biztonsági mentése nem tartja meg az sap-licenceléshez használt egyedi virtuálisgép-azonosítót. Ez azt jelenti, hogy egy virtuális gép biztonsági mentéséből történő visszaállítás egy új SAP licenckulcs telepítését igényli, mivel a visszaállított virtuális gép új virtuális gépnek minősül, és nem a korábbi, amely et mentett.
>
> ![Windows][Logo_Windows] Windows
>
> Elméletileg az adatbázisokat futtató virtuális gépekről konzisztens módon is készíthető biztonsági másolat, ha <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>a DBMS-rendszer támogatja a Windows VSS (Volume Shadow Copy Service) szolgáltatást, ahogy például az SQL Server is.
> Azonban ne feledje, hogy az Azure VM biztonsági mentések alapján az adatbázisok időben visszaállítani nem lehetséges. Ezért a javaslat az, hogy az Adatbázisok adatbázis-biztonsági mentések adatbázis-kezelő funkcióval, ahelyett, hogy az Azure VM Biztonsági mentés támaszkodva.
>
> Ha meg szeretné ismerni az Azure <https://docs.microsoft.com/azure/backup/backup-azure-vms>virtuális gép biztonsági mentését, itt kezdődhet: .
>
> További lehetőségek az Azure Virtuálisgépbe telepített Microsoft Data Protection Manager és az Azure Backup használatával az adatbázisok biztonsági mentéséhez/visszaállításához használhatók. További információ itt található: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> A Linux rendszerben a Windows VSS-nek nincs megfelelője. Ezért csak fájlkonzisztens biztonsági mentések lehetségesek, de az alkalmazáskonzisztens biztonsági mentések nem. Az SAP DBMS biztonsági mentést a DBMS funkció használatával kell elvégezni. Az SAP-val kapcsolatos adatokat tartalmazó fájlrendszer például az itt leírt kátrány használatával menthető:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Az Azure dr-helyként az éles SAP-tájakhoz

2014 közepe óta a Hyper-V, a System Center és az Azure különböző összetevőinek bővítményei lehetővé teszik az Azure DR-helyként való használatát a Hyper-V-alapú helyszíni virtuális gépekhez.

A blog részletezi, hogyan kell telepíteni ezt <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>a megoldást itt dokumentált: .

## <a name="summary"></a>Összefoglalás

Az SAP-rendszerek magas rendelkezésre állásának legfontosabb pontjai az Azure-ban a következők:

* Jelenleg az SAP egyetlen meghibásodási pont nem biztosítható pontosan ugyanúgy, mint a helyszíni telepítések. Ennek az az oka, hogy a megosztott lemezfürtök még nem építhetők be az Azure-ban harmadik féltől származó szoftverek használata nélkül.
* Az ADATBÁZIS-RENDSZER réteghez olyan DBMS-funkciókat kell használnia, amelyek nem támaszkodnak a megosztott lemezfürt technológiájára. A részleteket a [DBMS-útmutató dokumentálja.][dbms-guide]
* Az Azure-infrastruktúrában vagy a gazdagép karbantartásában a tartalék tartományokon belüli problémák hatásának minimalizálása érdekében az Azure rendelkezésre állási csoportjait kell használnia:
  * Ajánlott egy rendelkezésre állási készlet az SAP-alkalmazásréteghez.
  * Javasoljuk, hogy az SAP DBMS-réteghez külön rendelkezésre állási készletet hozzon létre.
  * NEM ajánlott ugyanazt a rendelkezésre állási készletet alkalmazni a különböző SAP-rendszerek virtuális gépeihez.
  * Prémium szintű felügyelt lemezek használata ajánlott.
* Az SAP DBMS-réteg biztonsági mentési céljából ellenőrizze a [DBMS-útmutatót.][dbms-guide]
* Az SAP Dialog példányainak biztonsági mentése nem sok értelme van, mivel általában gyorsabb az egyszerű párbeszédpéldányok újratelepítése.
* A virtuális gép biztonsági mentése, amely tartalmazza az SAP-rendszer globális könyvtárát, és ezzel együtt a különböző példányok összes profilját, van értelme, és a Windows biztonsági másolat vagy például a Linux kátrányral kell végrehajtani. Mivel különbségek vannak a Windows Server 2008 (R2) és a Windows Server 2012 (R2) között, ami megkönnyíti a biztonsági másolatot a Windows Server újabb kiadásaival, javasoljuk, hogy a Windows Server 2012 (R2) rendszert windowsos vendég operációs rendszerként futassza.

## <a name="next-steps"></a>További lépések
Olvassa el a cikkeket:

- [Az Azure virtuális gépek üzembe helyezése az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Az Azure Virtual Machines DBMS üzembe helyezésének szempontjai az SAP-munkaterheléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA infrastruktúra konfigurációk és műveletek az Azure-ban] (https://docs.microsoft.com/
- az azúr/virtuális gépek/számítási feladatok/sap/hana-vm-műveletek)
