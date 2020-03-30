---
title: SQL Server Azure Virtual Machines DBMS üzembe helyezése az SAP-munkaterheléshez | Microsoft dokumentumok
description: SQL Server rendszerű Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0fbed1f4dd62b2d75d39f475d2fe124c55a2b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645803"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines DBMS-telepítés az SAP NetWeaver számára

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Ez a dokumentum számos különböző területet fed le, amelyeket figyelembe kell venni az SQL Server for SAP-számítási feladatok azure IaaS-ben történő üzembe helyezésekor. A dokumentum előfeltételeként el kell olvasnia a [dokumentumot az Azure virtual machines DBMS üzembe helyezésével kapcsolatos szempontok sap-számítási feladatokhoz,](dbms_guide_general.md) valamint az Azure dokumentációjában az [SAP-munkaterhelés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)egyéb útmutatóit. 



> [!IMPORTANT]
> A dokumentum hatóköre az SQL Server Windows verziója. Az SAP nem támogatja az SQL Server Linux verzióját az SAP-szoftverek egyikével sem. A dokumentum nem tárgyalja a Microsoft Azure SQL Database, amely a Platform, mint a Microsoft Azure Platform szolgáltatási ajánlat. A jelen dokumentumban szereplő vita az SQL Server-termék futtatásáról szól, mivel az Azure virtuális gépeken a helyszíni telepítésekről ismert, kihasználva az Azure-infrastruktúra szolgáltatásként való kihasználását. A két ajánlat közötti adatbázis-képességek és -funkciók eltérőek, és nem szabad összekeverni őket egymással. Lásd még:<https://azure.microsoft.com/services/sql-database/>
> 
>

Általában érdemes a legújabb SQL Server-kiadások használatával az SAP-számítási feladatok futtatásához az Azure IaaS-ben. A legújabb SQL Server-kiadások jobb integrációt biztosítanak az Azure egyes szolgáltatásaiba és funkcióiba. Vagy olyan módosításokat, amelyek optimalizálják a műveleteket egy Azure IaaS-infrastruktúrában.

Javasoljuk, hogy [a][virtual-machines-sql-server-infrastructure-services] folytatás előtt tekintse át ezt a dokumentációt.

A következő szakaszokban a fenti link alatt található dokumentáció egyes részeit összesítik és megemlítik. Az SAP körüli sajátosságokat is megemlítik, és néhány fogalmat részletesebben ismertetünk. Az SQL Server-specifikus dokumentáció elolvasása előtt azonban erősen ajánlott a fenti dokumentáció takarása.

Van néhány SQL Server az IaaS-ben, amelyeket tudnia kell a folytatás előtt:

* **SQL-verzió támogatás:** Sap-ügyfelek számára az SQL Server 2008 R2 és újabb verziók támogatottak a Microsoft Azure virtuális gépen. A korábbi kiadások nem támogatottak. További részletekért tekintse át ezt az általános [támogatási nyilatkozatot.](https://support.microsoft.com/kb/956893) Általánosságban elmondható, hogy az SQL Server 2008-at a Microsoft is támogatja. Az SQL Server 2008 R2 rendszerrel bevezetett SAP jelentős funkcionalitása miatt azonban az SQL Server 2008 R2 az SAP minimális kiadása. Általában érdemes a legújabb SQL Server-kiadások használatával az SAP-számítási feladatok futtatásához az Azure IaaS-ben. A legújabb SQL Server-kiadások jobb integrációt biztosítanak az Azure egyes szolgáltatásaiba és funkcióiba. Vagy olyan módosításokat, amelyek optimalizálják a műveleteket egy Azure IaaS-infrastruktúrában. Ezért a papír az SQL Server 2016-ra és az SQL Server 2017-re korlátozódik.
* **SQL-teljesítmény: A**Microsoft Azure üzemeltetett virtuális gépei jól teljesítenek a többi nyilvános felhőbeli virtualizációs ajánlathoz képest, de az egyes eredmények eltérőek lehetnek. Tekintse meg a [teljesítmény gyakorlati tanácsok az SQL Server az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Az Azure Marketplace-ről származó lemezképek használata:** Az új Microsoft Azure virtuális gép üzembe helyezésének leggyorsabb módja az Azure Piactérről származó lemezkép használata. Vannak olyan képek az Azure Marketplace-en, amelyek tartalmazzák a legújabb SQL Server-kiadások. Azok a lemezképek, amelyeken az SQL Server már telepítve van, nem használhatók azonnal az SAP NetWeaver-alkalmazásokhoz. Ennek az az oka, hogy az alapértelmezett SQL Server-rendezés telepítve van ezekben a lemezképekben, és nem az SAP NetWeaver rendszerek által igényelt rendezés. Az ilyen lemezképek használatához ellenőrizze a fejezetben ismertetett [lépéseket: Az SQL Server-lemezkép használata a Microsoft Azure Piactérről.][dbms-guide-5.6] 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Javaslatok a virtuális gép/VHD struktúra SAP-hoz kapcsolódó SQL Server-telepítések
Az általános leírásnak megfelelően az SQL Server végrehajtható fájljait a virtuális gép operációsrendszer-lemezének\)rendszermeghajtójára kell telepíteni vagy telepíteni (C meghajtó: .  Az SQL Server rendszeradatbázisainak többségét általában nem használja magas szinten az SAP NetWeaver munkaterhelése. Ennek eredményeképpen az SQL Server rendszeradatbázisai (főkiszolgáló, msdb és modell) a C:\ meghajtón is. Kivételt kell tempdb, amely az SAP-számítási feladatok esetén szükség lehet nagyobb adatmennyiség vagy I/O-műveletek kötet. I/O-számítási feladatok, amely nem alkalmazható az operációs rendszer virtuális merevlemezére. Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani:


* Az összes SAP-tanúsítvánnyal rendelkező virtuálisgép-típus (lásd az SAP Note [1928533]megjegyzést), kivéve az A sorozatú virtuális gépeket, a tempdb-adatokat és a naplófájlokat a nem megőrzött D:\ Meghajtó. 
* Mindazonáltal ajánlott több tempdb adatfájlt használni. Legyen tudatában D:\ a meghajtókötetek a virtuális gép típusától függően eltérőek. A D:\ pontos méreteihez meghajtót a különböző virtuális gépek, ellenőrizze a cikk [méretei windowsos virtuális gépek az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)

Ezek a konfigurációk lehetővé teszik, hogy a tempdb több helyet foglaljon el, mint amennyit a rendszermeghajtó képes biztosítani. A nem állandó D:\ meghajtó is kínál jobb I/O késleltetés és átviteli (kivéve az A-sorozatú virtuális gépek). A megfelelő tempdb méret meghatározásához ellenőrizheti a tempdb méreteit a meglévő rendszereken. 

>[!NOTE]
> abban az esetben ön hely tempdb adat fájlokat és fatörzs reszelő -ba egy dosszié -ra D:\ meghajtót, amely et létrehozott, meg kell győződnie arról, hogy a mappa nem létezik a virtuális gép újraindítása után. Mivel a D:\ a meghajtó frissen inicializálva van a virtuális gép újraindítása után, az összes fájl- és könyvtárstruktúra törlődik. Lehetőség a d:\ végleges könyvtárstruktúrák újbóli létrehozásához a [cikk](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)ben az SQL Server szolgáltatás megkezdése előtt.

Virtuális gép konfigurációja, amely az SQL Server alkalmazást SAP-adatbázissal futtatja, és ahol a tempdb adatok és a tempdb naplófájl a D:\ meghajtó így nézne ki:

![Egyszerű virtuálisgép-lemezkonfiguráció diagramja az SQL Server kiszolgálóhoz](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

A fenti ábra egy egyszerű esetet jelenít meg. Ahogy a cikkben [az Azure virtuális gépek DBMS üzembe helyezésének megfontolása az SAP-munkaterheléshez,](dbms_guide_general.md)a prémium szintű storage-lemezek száma és mérete különböző tényezőktől függ. De általában azt javasoljuk:

- Tárolóhelyek használata az SQL Server adatfájljait tartalmazó kötetek egy vagy kis számának létrehozásához. Ennek a konfigurációnak az az oka, hogy a valós életben számos SAP-adatbázis különböző méretű adatbázisfájlokkal, különböző I/O munkaterheléssel van elhabaltával.
- Tárolóhelyek használatával elegendő IOPS-t és az SQL Server tranzakciós naplófájlját. A potenciális IOPS-munkaterhelés gyakran a tranzakciónapló-kötet méretezésének vezérlővonala, nem pedig az SQL Server tranzakciós kötetének potenciális kötete.
- Használja a D:\meghajtót tempdb mindaddig, amíg a teljesítmény elég jó. Ha a teljes munkaterhelés teljesítményében korlátozott, a Tmepdb a D:\ előfordulhat, hogy érdemes áthelyeznie a tempdb-t a prémium szintű tárolólemezek rekesszésére [a cikkben](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)ajánlott módon.


### <a name="special-for-m-series-vms"></a>Különleges M-sorozatú virtuális gépekhez
Az Azure M sorozatú virtuális gép esetében a tranzakciós naplóba történő késésírás az Azure Premium Storage teljesítményéhez képest az Azure Premium Storage teljesítményéhez képest csökkenthető. Ezért az Azure Write Accelerator-t az SQL Server tranzakciós naplókötetét alkotó virtuális merevlemez(ek)hez kell telepítenie. Részletek olvashatók a [dokumentumban Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>A lemezek formázása
AZ SQL Server esetében az SQL Server-adatokat és naplófájlokat tartalmazó lemezek NTFS-blokkmérete 64 KB.For SQL Server, the NTFS block size for disks containing SQL Server data and log files should be 64KB. Nincs szükség a D:\ formázására Meghajtó. Ez a meghajtó jön előre formázott.

Annak érdekében, hogy az adatbázisok visszaállítása vagy létrehozása ne inicializálja az adatfájlokat a fájlok tartalmának nullázásával, győződjön meg arról, hogy az SQL Server szolgáltatás által futtatott felhasználói környezet rendelkezik bizonyos engedéllyel. A Windows rendszergazda csoport felhasználói általában rendelkeznek ezekkel az engedélyekkel. Ha az SQL Server szolgáltatás nem Windows rendszergazdai felhasználók felhasználói környezetében fut, hozzá kell rendelnie a felhasználóhoz a **kötetkarbantartási feladatok végrehajtása**felhasználói jog.  A Microsoft Tudásbázis ezen cikkének részleteit a következőkben találja:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Az adatbázis-tömörítés hatása
Olyan konfigurációkban, ahol az I/O-sávszélesség korlátozó tényezővé válhat, minden olyan mérték, amely csökkenti az IOPS-t, segíthet a munkaterhelés megnyújtásában, amely egy IaaS-forgatókönyvben, például az Azure-ban futtatható. Ezért ha még nem történt meg, az SQL Server PAGE-tömörítés alkalmazását az SAP és a Microsoft is ajánlott, mielőtt egy meglévő SAP-adatbázist feltöltene az Azure-ba.

Az Adatbázis-tömörítés Azure-ba való feltöltés előtti végrehajtására vonatkozó javaslat nak két oka van:

* A feltöltendő adatok mennyisége alacsonyabb.
* A tömörítés végrehajtásának időtartama rövidebb, feltételezve, hogy erősebb hardvert használhat, amelynek nagyobb processzora vagy nagyobb I/O-sávszélessége vagy kisebb I/O-késleltetése van a helyszínen.
* A kisebb adatbázisméretek a lemezfoglalás költségeinek csökkentéséhez vezethetnek

Az adatbázis-tömörítés ugyanúgy működik az Azure virtuális gépeken, mint a helyszíni. A meglévő SAP NetWeaver SQL Server adatbázisok tömörítésével kapcsolatos további részletekért tekintse meg a [Javított SAP tömörítő eszköz MSSCOMPRESS című cikket.](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/) 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 és újabb – Adatbázisfájlok tárolása közvetlenül az Azure Blob Storage szolgáltatásban
Az SQL Server 2014-es és újabb verziói megnyitják az adatbázisfájlok közvetlenül az Azure Blob Store-ban való tárolásának lehetőségét anélkül, hogy a virtuális merevlemez "burkolója" lenne körülöttük. Különösen a Standard Azure Storage vagy kisebb virtuálisgép-típusok használatával az ilyen típusú üzembe helyezés lehetővé teszi, hogy olyan forgatókönyvek, ahol leküzdheti az IOPS korlátait, amelyek korlátozott számú lemez, amely csatlakoztatható néhány kisebb virtuálisgép-típusok. Ez a telepítési mód a felhasználói adatbázisok esetében működik, de az SQL Server rendszeradatbázisai esetében nem. Az SQL Server adat- és naplófájljaihoz is működik. Ha egy SAP SQL Server-adatbázist szeretne telepíteni, ahelyett, hogy "csomagolná" azt virtuális gépekbe, ne feledje:

* A használt tárfióknak ugyanabban az Azure-régióban kell lennie, mint amely a Virtuálisgép SQL Server üzembe helyezéséhez használt.
* A virtuális gépek különböző Azure Storage-fiókok közötti elosztásával kapcsolatban korábban felsorolt szempontok is érvényesek az üzembe helyezési módszerre. Azt jelenti, hogy az I/O-műveletek az Azure Storage-fiók korlátaihoz számítanak.
* Ahelyett, hogy a virtuális gép tárolási I/O-kvótával szembeni számlázás, az SQL Server-adatokat és naplófájlokat képviselő tárblobok közötti forgalom az adott virtuálisgép-típus virtuális gép hálózati sávszélességébe lesz figyelembevéve. Egy adott virtuálisgép-típus hálózati és tárolási sávszélességéről az [Azure-beli Windows-alapú virtuális gépek méretei című cikkben olvashat.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
* A fájl I/O-lenyomása a hálózati kvótán keresztül, akkor a tárolási kvóta többnyire, és hogy használja a virtuális gép teljes sávszélességét csak részben.
* Az IOPS és az I/O átviteli teljesítmény célokat, hogy az Azure Premium Storage a különböző lemezméretek már nem érvényesek. Még akkor is, ha a létrehozott blobok találhatók az Azure Premium Storage.Even if the blobs you created are located on Azure Premium Storage. A célok dokumentálva vannak a [cikk nagy teljesítményű prémium szintű tárolás és felügyelt lemezek virtuális gépek.](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) Az SQL Server-adatfájlok és a naplófájlok közvetlenül az Azure Premium Storage-ban tárolt blobokon való elhelyezése miatt a teljesítményjellemzők eltérhetnek az Azure Premium Storage-ban lévő virtuális gépekhez képest.
* Az Azure Premium Storage-lemezekhez elérhető gazdagépalapú gyorsítótárazás nem érhető el, ha az SQL Server-adatfájlokat közvetlenül az Azure-blobokba helyezi.
* M sorozatú virtuális gépeken az Azure Write Accelerator nem használható az SQL Server tranzakciós naplófájlján kívüli alezstávolságra történő írások támogatására. 

Ennek a funkciónak a részletei a [Microsoft Azure SQL Server adatfájljairól](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017) szóló cikkben találhatók.

Az éles rendszerek rejleménye, hogy elkerüljék ezt a konfigurációt, és inkább válassza ki az SQL Server-adatok és naplófájlok elhelyezését az Azure Premium Storage virtuális gépekben, nem pedig közvetlenül az Azure blobokon.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 pufferkészlet-bővítmény
Az SQL Server 2014 bevezetett egy új szolgáltatást, amelyet [pufferkészlet-bővítménynek neveznek.](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017) Ez a funkció kiterjeszti az SQL Server pufferkészletét, amely et egy kiszolgáló vagy virtuális gép helyi SSD-i által támogatott második szintű gyorsítótárral tárolanak a memóriában. A pufferkészlet-bővítmény lehetővé teszi, hogy nagyobb munkaadatkészletet tartson "a memóriában". Az Azure Standard Storage eléréséhez képest a pufferkészlet bővítményéhez való hozzáférés, amely egy Azure virtuális gép helyi SSD-in tárolódik, számos tényező gyorsabb. A pufferkészlet-bővítmény és az Azure Premium Storage olvasási gyorsítótárának összehasonlítása, az SQL Server adatfájlokhoz ajánlottan nem várható jelentős előny a pufferkészlet-bővítmények számára. Ennek oka, hogy mindkét gyorsítótár (SQL Server bufferkészlet-bővítmény és prémium szintű tárolási olvasási gyorsítótár) az Azure számítási csomópont helyi lemezeit használja.

Az SQL Server bufferpool-bővítmény SAP-munkaterheléssel való használatával szerzett tapasztalatok vegyesek, és továbbra sem teszik lehetővé az egyértelmű javaslatokat arra vonatkozóan, hogy minden esetben használják-e. Az ideális eset az, hogy az SAP alkalmazás munkakészlete illeszkedik a fő memóriába. Az Azure eközben kínál virtuális gépek, hogy jön-val akár 4 TB-os memória, elérhetőnek kell lennie, hogy a munkakészlet a memóriában. Ezért a bufferkészlet-bővítmény használata néhány ritka esetre korlátozódik, és nem lehet általános eset.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Az SQL Server biztonsági mentési/helyreállítási szempontjai
Az SQL Server Azure-ba való telepítésekor a biztonsági mentési módszertant felül kell vizsgálni. Még akkor is, ha a rendszer nem éles rendszer, az SQL Server által üzemeltetett SAP-adatbázisról rendszeres időközönként biztonsági másolatot kell kapnia. Mivel az Azure Storage három lemezképet tárol, a biztonsági mentés most kevésbé fontos a tárolási összeomlás kompenzálása tekintetében. A megfelelő biztonsági mentési és helyreállítási terv fenntartásának elsődleges oka több, hogy kompenzálhatja a logikai/manuális hibákat a pont-helyreállítási képességek biztosításával. Így a cél az, hogy vagy a biztonsági mentések segítségével visszaállítsa az adatbázist egy bizonyos időpontra, vagy használja a biztonsági mentések az Azure-ban egy másik rendszer a meglévő adatbázis másolásával. 

Az SQL Server biztonsági mentési lehetőségeinek az Azure-ban való megtekintéséhez olvassa el az SQL Server biztonsági mentése és visszaállítása az Azure virtuális gépekben című [cikket.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery) A cikk több különböző lehetőséget ölel fel.

### <a name="manual-backups"></a>Kézi biztonsági mentések
Számos lehetősége van a "manuális" biztonsági mentések végrehajtására:

1. Hagyományos SQL Server biztonsági mentések végrehajtása közvetlen csatlakoztatott Azure-lemezekre. Ez a módszer azzal az előnnyel jár, hogy a biztonsági mentések gyorsan elérhető a rendszer frissítése és az új rendszerek kiépítése a meglévő SAP rendszerek másolataiként
2.  Az SQL Server 2012 CU4 és újabb verziói az azure-tárhely URL-címére biztonsági másolatot tudnak létrehozni az adatbázisokról.
3.  Az Azure Blob Storage adatbázisfájljainak fájlpillanatkép-biztonsági mentései. Ez a módszer csak akkor működik, ha az SQL Server-adatok és a naplófájlok az Azure blob storage-ban találhatók

Az első módszer jól ismert és alkalmazott sok esetben a helyszíni világban is. Mindazonáltal, ez hagy ön -val a feladat -hoz megfejt a hosszabb időszak hát elhelyezés. Mivel 30 vagy több napig nem szeretné megtartani a biztonsági mentéseket a helyileg csatlakoztatott Azure Storage-ban, vagy egy másik külső biztonsági mentési/helyreállítási eszközt kell használnia, amely hozzáférést és adatmegőrzési kezelést tartalmaz a biztonsági mentések számára. Vagy egy nagy fájlkiszolgálót hoz létre az Azure-ban a Windows tárolóhelyhasználatával.

A második módszer az [SQL Server Biztonsági másolat url-címhez](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)című cikkében ismertetjük. Az SQL Server különböző kiadásai nak van néhány változata ebben a funkcióban. Ezért tekintse meg az adott SQL Server kiadási ellenőrzés dokumentációját. Fontos megjegyezni, hogy ez a cikk sok korlátozást sorol fel. Vagy lehetősége van arra, hogy a biztonsági mentésellen:

- Egyetlen Azure-lapblob, amely ezután a biztonsági mentés méretét 1000 GB-ra korlátozza. Ez is korlátozza az átviteli lehet elérni.
- Több (legfeljebb 64) Azure blokk blobok, amelyek lehetővé teszik az elméleti biztonsági mentési mérete 12 TB.Multiple (up to 64) Azure block blobs, which enable a theoretical backup size of 12 TB. Az ügyfél-adatbázisokkal végzett tesztek azonban azt mutatták, hogy a biztonsági mentés maximális mérete kisebb lehet, mint az elméleti korlát. Ebben az esetben Ön felelős a biztonsági mentések megőrzéséért és a biztonsági mentések eléréséért is.


### <a name="automated-backup-for-sql-server"></a>Automatikus biztonsági mentés az SQL Serverhez
Az Automatikus biztonsági mentés automatikus biztonsági mentési szolgáltatást biztosít az Azure-beli Windows virtuális gépeken futó SQL Server Standard és Enterprise kiadásokhoz. Ezt a szolgáltatást az [SQL Server IaaS Agent Extension](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)biztosítja, amely automatikusan telepítve van az SQL Server Windows virtuálisgép-lemezképeire az Azure Portalon. Ha telepíti a saját operációs rendszerlemezt az SQL Server telepítve, a virtuálisgép-bővítményeket külön kell telepítenie. A szükséges lépéseket ebben a [cikkben](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)dokumentálja .

A módszer képességeiről további részletek találhatók az alábbi cikkekben:

- SQL Server 2014: [Automatikus biztonsági mentés az SQL Server 2014 virtuális gépekhez (Erőforrás-kezelő)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatikus biztonsági mentés i 2.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

A dokumentációt vizsgálva láthatja, hogy a legújabb SQL Server-kiadások funkciója javult. Az SQL Server automatikus biztonsági másolatairól az SQL Server felügyelt biztonsági mentése a [Microsoft Azure-nak](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017)című cikkben talál további részleteket. Az elméleti biztonsági mentési mérethatár 12 TB.  Az automatikus biztonsági mentések jó módszer lehet a 12 TB-os biztonsági mentési méretekhez. Mivel több blobok vannak írva párhuzamosan, 100 MB/s-nál nagyobb átviteli sebességre számíthat. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Biztonsági másolat SQL Server virtuális gépekhez
Az SQL Server biztonsági mentésének ezen új módszerét 2018 júniusátantól az Azure Backup-szolgáltatások nyilvános előzetes verzióként kínálják. Az SQL Server biztonsági mentésének módja megegyezik a más külső gyártótól származó eszközökkel, nevezetesen az SQL Server VSS/VDI felületével a biztonsági mentések célhelyre történő streameléséhez. Ebben az esetben a célhely az Azure Recovery Service-tároló.

A biztonsági mentési módszer részletesebb leírása, amely számos előnyt biztosít a központi biztonsági mentési konfigurációk, a figyelés és a felügyelet számára, [itt](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)érhető el. 


### <a name="third-party-backup-solutions"></a>Külső biztonsági mentési megoldások
Elég sok SAP-ügyfél számára nem volt lehetőség az újrakezdésre és a teljes új biztonsági mentési megoldások bevezetésére az Azure-on futó SAP-környezetük höz. Ennek eredményeképpen a meglévő biztonsági mentési megoldásokat kell használni, és az Azure-ba kell terjeszteni. A meglévő biztonsági mentési megoldások Azure-ra való kiterjesztése általában jól működött a legtöbb fő szállítóval ezen a helyen. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>SQL Server-lemezkép használata a Microsoft Azure Piactérről
A Microsoft virtuális gépeket kínál az Azure Marketplace-en, amelyek már tartalmazzák az SQL Server verzióit. Az SQL Server és a Windows licenceket igénylő SAP-ügyfelek számára ezek a lemezképek használata lehetőséget adhat arra, hogy a virtuális gépek már telepített SQL Server rel való felpörgetésével fedezze a licencek szükségességét. Annak érdekében, hogy ezeket a képeket az SAP-hoz használhassa, a következő szempontokat kell figyelembe venni:

* Az SQL Server nem kiértékelési verziói magasabb költségeket kapnak, mint az Azure Marketplace-en telepített "Csak Windows rendszerű" virtuális gépek. Az árak összehasonlításához <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> tekintse <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>meg ezeket a cikkeket: és . 
* Csak az SAP által támogatott SQL Server-kiadások használhatók.
* Az SQL Server-példány, amely telepítve van az Azure Marketplace-en kínált virtuális gépeken, nem az SAP NetWeaver által futtatott rendezési SAP NetWeaver futtatásához szükséges. A rendezést a következő szakaszban található utasításokkal módosíthatja.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Microsoft Windows/SQL Server virtuális gép SQL Server-illesztésének módosítása
Mivel az SQL Server-rendszerképek az Azure Marketplace-en nincsenek beállítva a rendezés, amely az SAP NetWeaver-alkalmazások által megkövetelt, azonnal módosítani kell a központi telepítés után. Az SQL Server esetében ez a rendezési módosítás a következő lépésekkel végezhető el, amint a virtuális gép telepítve van, és a rendszergazda be tud jelentkezni az üzembe helyezett virtuális gépbe:

* Nyisson meg egy Windows parancsablakot rendszergazdaként.
* Módosítsa a könyvtárat C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012 értékre.
* A következő parancs végrehajtása: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER`<local_admin_account_name` /SQLSYSADMINACCOUNTS=> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> az a fiók, amely rendszergazdai fiókként lett definiálva a virtuális gép első üzembe helyezésekor a katalóguson keresztül.

A folyamat csak néhány percet vesz igénybe. Annak érdekében, hogy a lépés a megfelelő eredménnyel zárult-e, hajtsa végre a következő lépéseket:

* Nyissa meg az SQL Server Management Studiót.
* Nyisson meg egy lekérdezési ablakot.
* A parancs végrehajtása sp_helpsort az SQL Server főadatbázisában.

A kívánt eredménynek így kell kinéznie:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Ha az eredmény eltérő, a STOP üzembe helyezése SAP, és vizsgálja meg, hogy miért a telepítő parancs nem működött a várt módon. Az SAP NetWeaver-alkalmazások telepítése a fent említetttől eltérő SQL Server kódlapokkal rendelkező SQL Server-példányra **NEM** támogatott.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Az SAP magas rendelkezésre állású SQL Server szolgáltatása az Azure-ban
Az SQL Server használatával az Azure IaaS-telepítések az SAP,számos különböző lehetőségeket hozzá a DBMS-réteg magas rendelkezésre állású üzembe helyezéséhez. Az Azure [virtuális gépek ADATBÁZIS-kezelési üzembe helyezésének megfontolásai](dbms_guide_general.md) már tárgyalt módon az Azure különböző up-time SLA-kat biztosít egyetlen virtuális géphez és egy Azure rendelkezésre állási csoportban üzembe helyezett virtuális gépekhez. Feltételezés, hogy az éles környezetek, amely az Azure rendelkezésre állási készletek üzembe helyezéséhez szükséges, a fel-idő SLA felé halad. Ebben az esetben legalább két virtuális gépet kell üzembe helyeznie egy ilyen rendelkezésre állási csoportban. Egy virtuális gép fogja futtatni az aktív SQL Server-példányt. A másik virtuális gép a passzív példányt futtatja

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>SQL Server fürtözés a Windows kibővített fájlkiszolgálóhasználatával
A Windows Server 2016 rendszerrel a Microsoft bemutatta [a Direct tárolóhelyeket.](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) A közvetlen telepítés en alapuló SQL Server FCI fürtözés támogatott. A részletek az [SQL Server feladatátvételi fürtpéldány konfigurálása az Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)című cikkben találhatók. A megoldás hoz egy Azure-terheléselosztóis, valamint a fürt erőforrások virtuális IP-címét. Az SQL Server adatbázisfájlok tárolóhelyeken tárolóhelyeken tárolóhelyek. Ezért adott, hogy az Azure Premium Storage alapján létre kell adnia a Windows storage-tárolókat. Mivel ez a megoldás még nem túl hosszú ideig támogatott, nincsenek ismert SAP-ügyfelek, akik ezt a megoldást használják az SAP éles forgatókönyvekben.  

### <a name="sql-server-log-shipping"></a>SQL Server naplószállítás
A magas rendelkezésre állás (HA) egyik módszere az SQL Server log Shipping. Ha a HA-konfigurációban részt vevő virtuális gépek rendelkeznek a működő névfeloldással, nincs probléma, és az Azure-beli beállítás nem különbözik a helyszíni telepítéstől. Ami a Log Shipping beállítását és a Log Shipping-re vonatkozó elveket illeti. Az SQL Server naplószállítás részletei a [Naplószállításról (SQL Server) című](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017)cikkben találhatók.

Az SQL Server naplószállítási funkcióit alig használták az Azure-ban egy Azure-régión belüli magas rendelkezésre állás eléréséhez. A következő esetekben azonban az SAP-ügyfelek az Azure-ral együtt sikeres naplószállítást használtak:

- Vész-helyreállítási forgatókönyvek az egyik Azure-régióból egy másik Azure-régióba
- Vész-helyreállítási konfiguráció a helyszíni azure-régióból egy Azure-régióba
- Cut-over forgatókönyvek a helyszíni Azure-ba. Ezekben az esetekben a naplószállítás az azure-beli új ADATBÁZIS-üzembe helyezés és a helyszíni éles rendszer szinkronizálására szolgál. A levágáskor az éles környezet leáll, és gondoskodik arról, hogy az utolsó és a legújabb tranzakciónapló biztonsági mentései átkerüljenek az Azure DBMS-üzembe helyezésre. Ezután az Azure DBMS üzembe helyezése megnyílik éles környezetben.  



### <a name="database-mirroring"></a>Adatbázis tükrözése
Adatbázis-tükrözés az SAP által támogatott (lásd SAP Note [965908]) támaszkodik a feladatátvevő partner meghatározása az SAP-kapcsolati karakterláncban. A telephelyek közötti esetekben feltételezzük, hogy a két virtuális gép ugyanabban a tartományban van, és hogy a felhasználói környezetben a két SQL Server-példány fut egy tartományi felhasználó alatt is, és megfelelő jogosultságokkal rendelkezik a két SQL Server-példányérintett. Ezért az Azure-beli adatbázis-tükrözés beállítása nem különbözik a szokásos helyszíni beállítás/konfiguráció között.

A csak felhőalapú telepítések esetében a legegyszerűbb módszer egy másik tartomány beállítás az Azure-ban, hogy ezek a DBMS virtuális gépek (és ideális esetben dedikált SAP virtuális gépek) egy tartományon belül.

Ha egy tartomány nem lehetséges, az adatbázis hoz az itt leírt végpontokat tükröző tanúsítványokat is használhatja:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Az Azure-beli adatbázis-tükrözés beállításához az oktatóanyag itt található:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Mivel mindig be van támogatva az SAP helyszíni (lásd: SAP Note [1772688),]az Azure-ban az SAP-val együtt támogatott. Az SQL Server rendelkezésre állási csoportfigyelőjének üzembe helyezésével kapcsolatban van néhány speciális szempont (nem tévesztendő össze az Azure rendelkezésre állási készletével), mivel az Azure jelenleg nem teszi lehetővé Az AD/DNS-objektum létrehozását, mivel az a helyszíni megoldás. Ezért néhány különböző telepítési lépések szükségesek az Azure adott viselkedésének leküzdéséhez.

A rendelkezésre állási csoportfigyelő használatával kapcsolatos néhány szempont a következő:

* Az elérhetőségi csoportfigyelő használata csak windows Server 2012 vagy újabb operációs rendszer rel lehetséges a virtuális gép vendég operációs rendszerében. Windows Server 2012 esetén meg kell győződnie arról, hogy a javítás tana van:<https://support.microsoft.com/kb/2854082> 
* Windows Server 2008 R2 esetén ez a javítás nem létezik, és az Always On programot ugyanúgy kell használni, mint az Adatbázis-tükrözést, ha a kapcsolati karakterláncban megad egy feladatátvevő partnert (az SAP default.pfl paraméteren keresztül dbs/mss/server - lásd SAP Note [965908]).
* Rendelkezésre állási csoportfigyelő használata esetén az adatbázis virtuális gépek kell csatlakoztatni egy dedikált terheléselosztó. Annak elkerülése érdekében, hogy az Azure új IP-címeket rendeljen olyan esetekben, amikor mindkét virtuális gép mellékesen le van állítva, statikus IP-címeket kell rendelnie a virtuális gépek hálózati felületeihez az Always On konfigurációban [(a][virtual-networks-reserved-private-ip] statikus IP-cím definiálása ebben a cikkben található)
* Speciális lépések szükségesek a WSFC-fürtkonfiguráció létrehozásakor, ahol a fürthöz speciális IP-cím van hozzárendelve, mivel az Azure jelenlegi funkcióival a fürt nevét ugyanazt az IP-címet rendelné hozzá, mint a fürt létrehozásához. Ez azt jelenti, hogy manuális lépést kell végrehajtani egy másik IP-cím fürthöz rendeléséhez.
* Az akezőcsoport figyelő jön létre az Azure-ban tcp/IP-végpontok, amelyek a rendelkezésre állási csoport elsődleges és másodlagos replikáit futtató virtuális gépek.
* Előfordulhat, hogy ezeket a végpontokat a cl-kkel kell biztonságossá tenni.

Részletes dokumentáció az SQL Server használatával az Azure virtuális gépek listáiban történő üzembe helyezéshez, például:

- [Bemutatjuk az SQL Server Always On rendelkezésre állási csoportokat az Azure virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
- [Konfiguráljon egy mindig rendelkezésre állási csoportot a különböző régiókban lévő Azure virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
- [Konfiguráljon egy terheléselosztót egy mindig elérhető helyen lévő csoporthoz az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)

>[!NOTE]
> Ha az Azure load balancer az elérhetőségi csoport figyelő virtuális IP-címéhez konfigurálja, győződjön meg arról, hogy a DirectServerReturn konfigurálva van. a beállítás konfigurálása csökkenti a hálózati oda-vissza késést az SAP alkalmazásréteg és a DBMS-réteg között. 

Az SQL Server Always On a leggyakrabban használt magas rendelkezésre állású és vész-helyreállítási funkció az Azure-ban az SAP számítási feladatok üzembe helyezéséhez. A legtöbb ügyfél használja mindig be a magas rendelkezésre állás egyetlen Azure-régión belül. Ha a központi telepítés csak két csomópontra korlátozódik, két lehetőség közül választhat a kapcsolathoz:

- A rendelkezésre állási csoport figyelője. A rendelkezésre állási csoport figyelője, egy Azure-terheléselosztó üzembe helyezéséhez szükséges. Ez általában az alapértelmezett telepítési módszer. Az SAP-alkalmazások úgy vannak konfigurálva, hogy az elérhetőségi csoport figyelője ellen csatlakozzanak, és ne egyetlen csomópontellen
- Az SQL Server adatbázis-tükrözés kapcsolódási paramétereinek használata. Ebben az esetben az SAP-alkalmazások kapcsolatát úgy kell konfigurálnia, hogy mindkét csomópontnév neve meg van nevezve. Az ILYEN SAP oldali konfiguráció pontos részleteit az SAP Note [#965908 dokumentálja.](https://launchpad.support.sap.com/#/notes/965908) Ezzel a beállítással nem kell konfigurálnia egy rendelkezésre állási csoport figyelő. És ezzel nincs Azure load balancer az SQL Server magas rendelkezésre állású. Ennek eredményeképpen az SAP-alkalmazásréteg és a DBMS-réteg közötti hálózati késés alacsonyabb, mivel az SQL Server-példány bejövő forgalma nem kerül átirányítva az Azure terheléselosztón keresztül. De visszahívás, ez a beállítás csak akkor működik, ha korlátozza a rendelkezésre állási csoport két példányra kiterjedően. 

Jó néhány ügyfél kihasználja az SQL Server Always On funkciót az Azure-régiók közötti további vész-helyreállítási funkciókhoz. Több ügyfél is használja a másodlagos replika biztonsági mentéseit. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server transzparens adattitkosítás
Számos olyan ügyfél van, akik az SQL Server [transzparens adattitkosítást (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) használják az SAP SQL Server-adatbázisok Azure-beli telepítésekor. Az SQL Server TDE funkciót teljes mértékben támogatja az SAP (lásd: SAP [#1380493).](https://launchpad.support.sap.com/#/notes/1380493) 

### <a name="applying-sql-server-tde"></a>SQL Server TDE alkalmazása
Azokban az esetekben, amikor egy másik, a helyszínen futó adatbázisból heterogén áttelepítést hajt végre az Azure-ban futó Windows/SQL Server rendszerre, az üres céladatbázist az SQL Serverben kell előre létrehoznia. Következő lépésként az SQL Server TDE funkciót alkalmazza. Amíg az éles környezetben továbbra is fut a helyszíni. Oka szeretne végrehajtani ebben a sorrendben, hogy a folyamat az üres adatbázis titkosítása eltarthat egy ideig. Az SAP importálási folyamatok ezután importálja az adatokat a titkosított adatbázisba az állásidő fázisban. A titkosított adatbázisba való importálás többletterhelése sokkal kisebb időhatással bír, mint az adatbázis titkosítása az exportálási fázis utáni titkosítása a leállási időszakban. Negatív tapasztalatok, ahol a TDE alkalmazása során az SAP számítási feladat fut az adatbázis tetején. Ezért a javaslat a TDE üzembe helyezését olyan tevékenységként kezeli, amelyet sap-munkaterhelés nélkül kell elvégezni az adott adatbázisban.

Azokban az esetekben, ahol sap SQL Server-adatbázisok áthelyezése a helyszíni Azure-ba, javasoljuk, hogy tesztelje, hogy melyik infrastruktúra a leggyorsabban alkalmazható titkosítást kaphatja be. Ehhez tartsa szem előtt ezeket a tényeket:

- Nem határozható meg, hogy hány szálat használ az adatbázisadat-titkosítás alkalmazása. A szálak száma nagymértékben függ az SQL Server-adatok és naplófájlok által elosztott lemezkötetek számától. Azt jelenti, hogy minél több különböző kötet (meghajtóbetűs) lesz, annál több szál lesz párhuzamosan a titkosítás végrehajtásához. Egy ilyen konfiguráció ellentmond egy kicsit a korábbi lemezkonfigurációs javaslat létrehozása egy vagy kisebb számú tárolóhelyek az SQL Server adatbázisfájlok at Azure virtuális gépek. A kis számú kötettel rendelkező konfiguráció a titkosítást kis számú szál végrehajtásához vezetne. Egyetlen szál titkosítása olvasása 64KB terjedelem, titkosítja, majd írjon egy rekordot a tranzakciónapló-fájlba, mondván, hogy a mértéke van titkosítva. Ennek eredményeképpen a tranzakciós napló betöltése mérsékelt.
- A régebbi SQL Server kiadásokban a biztonsági mentés tömörítése már nem volt hatékony, amikor titkosította az SQL Server adatbázist. Ez a viselkedés akkor alakulhat ki, ha a terv az volt, hogy titkosítja az SQL Server-adatbázist a helyszínen, majd másolja a biztonsági másolatot az Azure-ba az azure-beli adatbázis visszaállításához. Az SQL Server biztonsági mentési tömörítése általában 4-es számú tömörítési arányt eredményez.
- Az SQL Server 2016-tal az SQL Server új funkciókat vezetett be, amelyek lehetővé teszik a titkosított adatbázisok hatékony tömörítését is. Lásd ezt a [blogokat](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) néhány részletet.
 
A TDE-titkosítás csak kis SAP-munkaterheléssel való kezelése, tesztelje az adott konfigurációban, hogy jobb-e a TDE alkalmazása az SAP-adatbázisra a helyszínen, vagy az Azure-ban. Az Azure-ban minden bizonnyal nagyobb rugalmasságot biztosít a túl-kiépítési infrastruktúra és az infrastruktúra csökkentése a TDE alkalmazása után.

### <a name="using-azure-key-vault"></a>Az Azure Key Vault használata
Az Azure egy [Key Vault](https://azure.microsoft.com/services/key-vault/) szolgáltatás átszolgálását kínálja a titkosítási kulcsok tárolására. A másik oldalon az SQL Server egy összekötőt kínál az Azure Key Vault tripkeditanúsítványok tárolójaként való kihasználásához.

További részletek az Azure Key Vault SQL Server TDE-listákhoz való használatához, mint például:

- [Bővíthető kulcskezelés az Azure Key Vault (SQL Server) használatával.](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017)
- [SQL Server TDE bővíthető kulcskezelés az Azure Key Vault – Telepítési lépések használatával.](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017)
- [SQL Server Connector Maintenance & hibaelhárítási &.](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017)
- [További kérdések az ügyfelektől az SQL Server transzparens adattitkosításával kapcsolatban – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Az SQL Server TDE használatával, különösen az Azure Key Vault használatával ajánlott az SQL Server 2014, az SQL Server 2016 és az SQL Server 2017 legújabb javításait használni. Ennek oka, hogy az ügyfelek visszajelzései, optimalizálásai és javításai alapján alkalmazták a kódot. Például ellenőrizze [a KBA #4058175.](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm)
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Általános SQL Server for SAP az Azure összegzésén
Ebben az útmutatóban számos javaslat található, és azt javasoljuk, hogy az Azure-telepítés megtervezése előtt többször is elolvassa. Általában azonban ügyeljen arra, hogy kövesse a legfelső általános DBMS az Azure-specifikus javaslatok:

1. Használja a legújabb DBMS-kiadást, például az SQL Server 2017-et, amely az Azure-ban a legtöbb előnnyel rendelkezik. 
2. Gondosan tervezze meg az SAP-rendszer környezetét az Azure-ban az adatfájlok elrendezésének és az Azure-korlátozásoknak az egyensúlyának kiegyenlítése érdekében:
   * Nincs túl sok lemez, de elegendő ahhoz, hogy a szükséges IOPS eléréséhez.
   * Ha nem használja a felügyelt lemezeket, ne feledje, hogy az IOPS is korlátozott azure storage-fiókonként, és hogy a storage-fiókok korlátozottak az egyes Azure-előfizetések[(további részletek).][azure-resource-manager/management/azure-subscription-service-limits] 
   * Csak csíkozott lemezek között, ha nagyobb átviteli igényt kell elérnie.
3. Soha ne telepítsen szoftvert, és ne helyezzen olyan fájlokat, amelyek megmaradást igényelnek a D:\ meghajtót, mert nem állandó, és bármi ezen a meghajtón elvész a Windows újraindítás.
4. Ne használja a lemezgyorsítótárazást az Azure Standard Storage szolgáltatáshoz.
5. Ne használja az Azure georeplikált Azure standard tárfiókok.  Használja a helyileg redundáns a DBMS számítási feladatokhoz.
6. A DBMS-szállító HA/DR megoldásával replikálhatja az adatbázisadatokat.
7. Mindig használja a Névfeloldást, ne hagyatkozzon IP-címekre.
8. Az SQL Server TDE használatával alkalmazza a legújabb SQL Server-javításokat.
9. A lehető legmagasabb adatbázis-tömörítést használja. Ami az SQL Server oldaltömörítése.
10. Legyen óvatos az Azure Marketplace-ről származó SQL Server-lemezképek használatával. Ha az SQL Server one-t használja, módosítania kell a példányillesztést, mielőtt bármilyen SAP NetWeaver rendszert telepítene rá.
11. Telepítse és konfigurálja az SAP Host Monitoring for Azure-t a [Telepítési útmutatóban][deployment-guide]leírtak szerint.
