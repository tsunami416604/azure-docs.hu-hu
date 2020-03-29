---
title: Oracle Azure Virtual Machines DBMS üzembe helyezése az SAP-munkaterheléshez | Microsoft dokumentumok
description: Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
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
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a23fb981e24f6152d99b76bd72115f8159f5d60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645844"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Az Azure Virtual Machines DBMS üzembe helyezése az SAP-munkaterheléshez

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


Ez a dokumentum számos különböző területet fed le, amelyeket figyelembe kell venni, amikor az Oracle Database for SAP számítási feladatok üzembe helyezését az Azure IaaS-ben telepíti. A dokumentum elolvasása előtt javasoljuk, hogy olvassa el [az Azure virtual machines DBMS üzembe helyezésével kapcsolatos szempontokat az SAP-munkaterheléshez.](dbms_guide_general.md) Azt is javasoljuk, hogy olvassa el az [Sap-számítási feladatok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)az Azure dokumentációjában más útmutatókat is. 

Az SAP Note [2039619]megjegyzésében információkat talál az Oracle verzióiról és a megfelelő operációs rendszerverziókról, amelyek et az Sap azure-beli Oracle-en való futtatásához támogatják.

Az SAP Business Suite Oracle-en való futtatásáról az [SAP on Oracle](https://www.sap.com/community/topic/oracle.html)oldalon található általános információk találhatók.
Az Oracle szoftverét az Oracle támogatja a Microsoft Azure-on való futtatáshoz. A Windows Hyper-V és az Azure általános támogatásáról az Oracle és a [Microsoft Azure gyakori kérdések című témakörben talál további információt.](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Az Oracle, az SAP és az Azure szempontjából releváns SAP-megjegyzések 

A következő SAP-megjegyzések az Azure-beli SAP-hoz kapcsolódnak.

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és Az Azure virtuálisgép-típusok |
| [2015553] |SAP a Microsoft Azure-ban: Támogatási előfeltételek |
| [1999351] |Továbbfejlesztett Azure-figyelés az SAP-hoz – hibaelhárítás |
| [2178632] |Az SAP legfontosabb figyelési mutatói a Microsoft Azure-ban |
| [2191498] |SAP Linuxon az Azure-ral: Továbbfejlesztett figyelés |
| [2039619] |SAP-alkalmazások a Microsoft Azure-ban az Oracle adatbázis használatával: Támogatott termékek és verziók |
| [2243692] |Linux a Microsoft Azure (IaaS) virtuális gépen: SAP licencproblémák |
| [2069760] |Oracle Linux 7.x SAP telepítés és frissítés |
| [1597355] |Swap-space ajánlás Linux |
| [2171857] |Oracle Database 12c - fájlrendszer támogatása Linuxon |
| [1114181] |Oracle Database 11g - fájlrendszer támogatása Linuxon |

Az Oracle és az SAP által az Azure-ban támogatott konfigurációkat és funkciókat az SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619)dokumentálja.

A Windows és az Oracle Linux az egyetlen olyan operációs rendszer, amelyet az Oracle és az SAP támogat az Azure-ban. A széles körben használt SLES és RHEL Linux disztribúciók nem támogatottak az Oracle-összetevők Azure-beli üzembe helyezéséhez. Az Oracle összetevői közé tartozik az Oracle Database ügyfél, amelyet az SAP-alkalmazások az Oracle DBMS-hez való csatlakozásra használnak. 

Kivételek szerint az SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619), az SAP-összetevők, amelyek nem használják az Oracle Database ügyfél. Az ilyen SAP-összetevők az SAP önálló várólistája, az üzenetkiszolgáló, a várólistára helyezett replikációs szolgáltatások, a WebDispatcher és az SAP Gateway.  

Még akkor is futtathatja az Oracle DBMS és SAP alkalmazáspéldányokat Oracle Linuxon, akkor is futtathatja az SAP Központi szolgáltatásokat SLES-en vagy RHEL-en, és használhatja azt egy Pacemaker-alapú fürttel. Pacemaker, mint egy magas rendelkezésre állású keretrendszer nem támogatott oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>A Windows Oracle-adatbázisának sajátosságai

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Az Oracle konfigurációs irányelvei az SAP-telepítésekhez a Windows Azure-beli virtuális gépeken

Az SAP telepítési kézikönyvének megfelelően az Oracle-fájlokat nem szabad telepíteni vagy elhelyezni a virtuális gép operációsrendszer-lemezének rendszerillesztőjében (c:meghajtó). A különböző méretű virtuális gépek különböző számú csatlakoztatott lemezt támogathatnak. A kisebb virtuálisgép-típusok kisebb számú csatlakoztatott lemezt is támogathatnak. 

Ha kisebb virtuális gépekkel rendelkezik, javasoljuk, hogy telepítse/helyezze el az Oracle otthoni, színpadi, "saptrace", "saparch", "sapbackup", "sapcheck" vagy "sapreorg" telepítését/helyüket az operációs rendszer lemezébe. Az Oracle DBMS-összetevők ezen részei nem intenzívek az I/O és az I/O átviteli nyomáson. Ez azt jelenti, hogy az operációs rendszer lemeze képes kezelni az I/O-követelményeket. Az operációs rendszer lemezének alapértelmezett mérete 127 GB. 

Ha nincs elég szabad terület, a lemez [átméretezhető](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) 2048 GB-ra. Az Oracle Database és a redo naplófájlokat külön adatlemezeken kell tárolni. Van egy kivétel az Oracle ideiglenes tablespace. Tempfiles hozható létre a D:/ (nem állandó meghajtó). A nem állandó D:\ meghajtó is kínál jobb I/O késleltetés és átviteli (kivéve az A-sorozatú virtuális gépek). 

A hőmérsékletfájlok megfelelő helyének meghatározásához ellenőrizheti a meglévő rendszerek hőmérsékleti fájljainak méretét.

### <a name="storage-configuration"></a>Tároló konfigurálása
Csak az NTFS formátumú lemezeket használó egypéldányos Oracle támogatott. Az összes adatbázisfájlt az NTFS fájlrendszeren felügyelt lemezeken (ajánlott) vagy VHD-ken kell tárolni. Ezek a lemezek az Azure virtuális géphez vannak csatlakoztatva, és az [Azure-lapblob-tárhelyen](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) vagy az [Azure felügyelt lemezeken alapulnak.](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview) 

Javasoljuk az [Azure felügyelt lemezek](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)használatát. Azt is javasoljuk, hogy [prémium szintű SSD-k](../../windows/disks-types.md) az Oracle Database-telepítések.

Az Oracle Database-fájlok nem támogatják a hálózati meghajtókat vagy a távoli megosztásokat, például az Azure fájlszolgáltatásokat. További információkért lásd:

- [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Ha olyan lemezeket használ, amelyek az Azure-lapblob-tárolón vagy a felügyelt lemezeken alapulnak, az [Azure virtual machines DBMS-telepítéssel kapcsolatos szempontok sap-számítási feladatokra](dbms_guide_general.md) az Oracle Database-lel rendelkező központi telepítésekre is vonatkoznak.

Az Azure-lemezek IOPS-átviteli átviteli átviteli kvótái léteznek. Ezt a fogalmat az [Azure virtual machines DBMS sap-munkaterheléshez való üzembe helyezésével kapcsolatos szempontok](dbms_guide_general.md)ismerteti. A pontos kvóták a használt virtuálisgép-típustól függenek. A virtuálisgép-típusok és a kvóták listája megtalálható [az Azure-beli Windows-alapú virtuális gépek méretei ben.][virtual-machines-sizes-windows]

A támogatott Azure virtuálisgép-típusok azonosításáról az SAP [Note 1928533]című témakörben van.

A minimális konfiguráció a következő: 

| Összetevő | Lemez | Gyorsítótárazás | Tárolókészlet |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Prémium | None | Nem szükséges |
| \oracle\<SID>\origlogaB & mirrlogA | Prémium | None | Nem szükséges |
| \oracle\<SID>\sapdata1... N | Prémium | Csak olvasható | Használható |
| \oracle\<SID>\oraarch | Standard | None | Nem szükséges |
| Oracle Home, saptrace, ... | Operációsrendszer-lemez | | Nem szükséges |


Az online ismétlési naplók üzemeltetéséhez kiválasztott lemezeket az I/P-követelményeknek kell vezérelniük. Az összes sapdata1 tárolható... n (tablespaces) egyetlen csatlakoztatott lemezen, amíg a méret, IOPS és átviteli hang megfelel a követelményeknek. 

A teljesítménykonfiguráció a következő:

| Összetevő | Lemez | Gyorsítótárazás | Tárolókészlet |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Prémium | None | Használható  |
| \oracle\<SID>\origlogaB | Prémium | None | Használható |
| \oracle\<SID>\mirrlogAB | Prémium | None | Használható |
| \oracle\<SID>\mirrlogBA | Prémium | None | Használható |
| \oracle\<SID>\sapdata1... N | Prémium | Csak olvasható | Ajánlott  |
| \oracle\SID\sapdata(n+1)* | Prémium | None | Használható |
| \oracle\<SID>\oraarch* | Prémium | None | Nem szükséges |
| Oracle Home, saptrace, ... | Operációsrendszer-lemez | Nem szükséges |

*(n+1): hosting SYSTEM, TEMP és UNDO táblaterek. A rendszer i/o-mintázata és a Tablespaces visszavonása eltér az alkalmazásadatokat tároló többi tablespaces-től. A nincs gyorsítótárazás a legjobb megoldás a rendszer és a Táblaterek visszavonása esetén.

*oraarch: a tárolókészlet teljesítmény szempontjából nem szükséges. Ezt fel lehet használni, hogy minél több helyet.

Ha több IOPS-ra van szükség, javasoljuk, hogy a Windows storage-készletek (csak a Windows Server 2012 és újabb) használatával hozzon létre egy nagy logikai eszközt több csatlakoztatott lemezen. Ez a megközelítés leegyszerűsíti a lemezterület kezeléséhez szükséges felügyeleti többletterhelést, és segít elkerülni a fájlok manuális terjesztését több csatlakoztatott lemez között.


#### <a name="write-accelerator"></a>Írásgyorsító
Az Azure M sorozatú virtuális gépek esetében az online újraműveletek naplóiba történő késés az Azure Premium Storage-hoz képest tényezőkkel csökkenthető. Engedélyezze az Azure Write Accelerator a lemezek (VHDs) alapján az Azure Premium Storage, amelyek az online ismétlési naplófájlok. További információt az [Írásgyorsító című](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)témakörben talál.


### <a name="backuprestore"></a>Biztonsági mentés/visszaállítás
A biztonsági mentési/visszaállítási funkciók esetében az SAP BR*Tools for Oracle ugyanúgy támogatott, mint a szabványos Windows Server operációs rendszereken. Az Oracle Recovery Manager (RMAN) is támogatott a lemezre történő biztonsági mentéshez és a lemezről történő visszaállításhoz.

Az Azure Backup segítségével is futtathat egy alkalmazáskonzisztens virtuális gép biztonsági mentést. A cikk [a virtuális gép biztonsági mentési infrastruktúrájának megtervezése az Azure-ban](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) elmagyarázza, hogy az Azure Biztonsági másolat hogyan használja a Windows VSS-funkciókat az alkalmazáskonzisztens biztonsági mentések végrehajtásához. Az Oracle DBMS-kiadások, amelyeket az SAP támogat az Azure-ban, kihasználhatják a VSS-funkciókat a biztonsági mentések számára. További információt az Oracle dokumentációjában az [adatbázis biztonsági mentésének és helyreállításának alapfogalmai a VSS segítségével.](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701)


### <a name="high-availability"></a>Magas rendelkezésre állás
Az Oracle Data Guard magas rendelkezésre állású és vész-helyreállítási célokra támogatott. Az adatőr automatikus feladatátvételének eléréséhez a Gyors indítás feladatátvétel (FSFA) szükséges. Az Observer (FSFA) elindítja a feladatátvételt. Ha nem használja az FSFA-t, csak manuális feladatátvételi konfigurációt használhat.

Az Azure-beli Oracle-adatbázisok vész-helyreállítási szolgáltatásáról további információt az [Oracle Database 12c adatbázisvész-helyreállítása Azure-környezetben című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)

### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Az Oracle Windows-alapú telepítései esetén erősen javasoljuk a gyorsított hálózatkezelést az Azure gyorsított hálózatkezelés című részben [leírtak szerint.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Vegye figyelembe az Okat is az [Azure virtual machines DBMS üzembe helyezésének az SAP-munkaterheléshez](dbms_guide_general.md)című javaslatokban. 
### <a name="other"></a>Egyéb
[Az Azure Virtual Machines DBMS üzembe helyezésének megfontolásai az SAP-munkaterheléshez](dbms_guide_general.md) az Oracle Database-t tartalmazó virtuális gépek üzembe helyezésével kapcsolatos egyéb fontos fogalmakat ismertetik, beleértve az Azure rendelkezésre állási csoportjait és az SAP figyelést.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Az Oracle Database sajátosságai Az Oracle Linux rendszeren
Az Oracle szoftverét az Oracle támogatja, hogy a Microsoft Azure-on az Oracle Linux lesz a vendég operációs rendszer. A Windows Hyper-V és az Azure általános támogatásáról az Azure és az [Oracle gyakori kérdések című témakörben talál további információt.](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 

Az Oracle adatbázisokat kihasználó SAP-alkalmazások konkrét forgatókönyve is támogatott. A részleteket a dokumentum következő részében tárgyaljuk.

### <a name="oracle-version-support"></a>Oracle verziótámogatása
Ha tudni szeretné, hogy mely Oracle-verziók és a megfelelő operációsrendszer-verziók támogatottak az SAP Azure virtuális gépeken való futtatásához, olvassa el az SAP Note [2039619 című témakört.]

Az SAP Business Suite Oracle-en való futtatásával kapcsolatos általános információk az [SAP-ban](https://www.sap.com/community/topic/oracle.html)találhatók az Oracle közösségi oldalán.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Oracle konfigurációs irányelvek az SAP-telepítésekhez linuxos Azure-beli virtuális gépeken

Az SAP telepítési kézikönyvek szerint az Oracle-hez kapcsolódó fájlokat nem szabad telepíteni vagy elhelyezni a virtuális gép rendszerindító lemezének rendszer-illesztőprogramjaiban. A virtuális gépek különböző méretű méretei különböző számú csatlakoztatott lemezt támogatnak. A kisebb virtuálisgép-típusok kisebb számú csatlakoztatott lemezt is támogathatnak. 

Ebben az esetben azt javasoljuk, telepítése / helytaként Oracle home, stage, saptrace, saparch, sapbackup, sapcheck, vagy sapreorg a rendszerindító lemez. Az Oracle DBMS-összetevők ezen részei nem intenzívek az I/O és az I/O átviteli nyomáson. Ez azt jelenti, hogy az operációs rendszer lemeze képes kezelni az I/O-követelményeket. Az operációs rendszer lemezének alapértelmezett mérete 30 GB. Az Azure Portal, a PowerShell vagy a CLI használatával kibonthatja a rendszerindító lemezt. A rendszerindító lemez kibontása után hozzáadhat egy további partíciót az Oracle bináris fájlokhoz.


### <a name="storage-configuration"></a>Tároló konfigurálása

Az ext4, xfs vagy Oracle ASM fájlrendszerek et az Azure-beli Oracle Database-fájlok támogatják. Az összes adatbázisfájlt ezeken a fájlrendszereken kell tárolni vhd-k vagy felügyelt lemezek alapján. Ezek a lemezek az Azure virtuális géphez vannak csatlakoztatva, és az [Azure-lapblob-tárhelyen](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy az [Azure felügyelt lemezeken alapulnak.](../../windows/managed-disks-overview.md)

Az Oracle Linux UEK kernelek esetében legalább 4-es UEK 4-es verzió szükséges az [Azure prémium szintű SSD-k](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching)támogatásához.

Erősen ajánlott [azure-beli felügyelt lemezek használata.](../../windows/managed-disks-overview.md) Emellett erősen ajánlott [az Azure prémium szintű SSD-k](../../windows/disks-types.md) használata az Oracle Database-telepítésekhez.

Az Oracle Database-fájlok nem támogatják a hálózati meghajtókat vagy a távoli megosztásokat, például az Azure fájlszolgáltatásokat. További információkat a következő cikkekben talál: 

- [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Ha az Azure-lapblob-tárolón vagy a felügyelt lemezeken alapuló lemezeket használ, az [Azure virtual machines DBMS-telepítéssel kapcsolatos szempontok sap-számítási feladatokra](dbms_guide_general.md) vonatkozó utasítások az Oracle Database használatával rendelkező központi telepítésekre is vonatkoznak.

 Az Azure-lemezek IOPS-átviteli átviteli átviteli kvótái léteznek. Ezt a fogalmat az [Azure virtual machines DBMS sap-munkaterheléshez való üzembe helyezésével kapcsolatos szempontok](dbms_guide_general.md)ismerteti. A pontos kvóták a használt virtuálisgép-típustól függenek. A virtuálisgép-típusok kvótákkal rendelkező listáját az [Azure-beli Linux-alapú virtuális gépek méretei (Méretek az Azure-ban) (Méret linuxos virtuális gépekhez) témakörben ismeri el.][virtual-machines-sizes-linux]

A támogatott Azure virtuálisgép-típusok azonosításáról az SAP [Note 1928533]című témakörben van.

Minimális konfiguráció:

| Összetevő | Lemez | Gyorsítótárazás | Sztrippelés* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA & mirrlogB | Prémium | None | Nem szükséges |
| /oracle/\<SID>/origlogaB & mirrlogA | Prémium | None | Nem szükséges |
| /oracle/\<SID>/sapdata1... N | Prémium | Csak olvasható | Használható |
| /oracle/\<SID>/oraarch | Standard | None | Nem szükséges |
| Oracle Home, saptrace, ... | Operációsrendszer-lemez | | Nem szükséges |

*Stripping: LVM csík vagy MDADM segítségével RAID0

Az Oracle online ismétlési naplóinak üzemeltetéséhez a lemezválasztást az IOPS-követelményeknek kell vezérelniük. Az összes sapdata1 tárolható... n (tablespaces) egyetlen csatlakoztatott lemezen, amíg a kötet, IOPS és átviteli hang megfelel a követelményeknek. 

Teljesítménykonfiguráció:

| Összetevő | Lemez | Gyorsítótárazás | Sztrippelés* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Prémium | None | Használható  |
| /oracle/\<SID>/origlogaB | Prémium | None | Használható |
| /oracle/\<SID>/mirrlogAB | Prémium | None | Használható |
| /oracle/\<SID>/mirrlogBA | Prémium | None | Használható |
| /oracle/\<SID>/sapdata1... N | Prémium | Csak olvasható | Ajánlott  |
| /oracle/\<SID>/sapdata(n+1)* | Prémium | None | Használható |
| /oracle/\<SID>/oraarch* | Prémium | None | Nem szükséges |
| Oracle Home, saptrace, ... | Operációsrendszer-lemez | Nem szükséges |

*Stripping: LVM csík vagy MDADM segítségével RAID0

*(n+1):rendszer, temp és undo táblaterek: A rendszer I/O mintázata és a Tablespaces visszavonása eltér az alkalmazásadatokat tároló többi tablespaces-től. A nincs gyorsítótárazás a legjobb megoldás a rendszer és a Táblaterek visszavonása esetén.

*oraarch: a tárolókészlet teljesítmény szempontjából nem szükséges.


Ha több IOPS-ra van szükség, javasoljuk, hogy LVM (Logikai kötetkezelő) vagy MDADM használatával hozzon létre egy nagy logikai kötetet több csatlakoztatott lemezen. További információ: [Szempontok az Azure virtuális gépek DBMS üzembe helyezésaz SAP számítási feladatok](dbms_guide_general.md) kapcsolatos iránymutatásokat és útmutatást, hogyan kell kihasználni LVM vagy MDADM. Ez a megközelítés leegyszerűsíti a lemezterület kezelésének adminisztrációs terhelését, és segít elkerülni a fájlok manuális terjesztését több csatlakoztatott lemezközött.


#### <a name="write-accelerator"></a>Írásgyorsító
Az Azure M sorozatú virtuális gépek, ha az Azure Write Accelerator használata, a késés írása az online ismétlés naplók csökkenthető tényezők az Azure Premium Storage teljesítményéhez képest. Engedélyezze az Azure Write Accelerator a lemezek (VHDs) alapján az Azure Premium Storage, amelyek az online ismétlési naplófájlok. További információt az [Írásgyorsító című](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)témakörben talál.


### <a name="backuprestore"></a>Biztonsági mentés/visszaállítás
A biztonsági mentési/visszaállítási funkciók hoz az SAP BR*Tools for Oracle ugyanúgy támogatott, mint a csupasz fém és a Hyper-V. Az Oracle Recovery Manager (RMAN) is támogatott a lemezre történő biztonsági mentéshez és a lemezről történő visszaállításhoz.

Ha többet szeretne tudni arról, hogy miként használhatja az Azure Biztonsági mentési és helyreállítási szolgáltatásokat az Oracle-adatbázisok biztonsági mentéséhez és helyreállításához, olvassa el [az Oracle Database 12c adatbázis biztonsági mentése és helyreállítása Azure Linux-alapú virtuális gépen című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery)

### <a name="high-availability"></a>Magas rendelkezésre állás
Az Oracle Data Guard magas rendelkezésre állású és vész-helyreállítási célokra támogatott. Az adatőr automatikus feladatátvételéhez gyorsindításos feladatátvételt (FSFA) kell használnia. Az Observer funkció (FSFA) elindítja a feladatátvételt. Ha nem használja az FSFA-t, csak manuális feladatátvételi konfigurációt használhat. További információ: [Implement Oracle Data Guard on a Azure Linux virtual machine](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).


Az Azure-beli Oracle-adatbázisok vész-helyreállítási szempontjait az [Oracle Database 12c adatbázisvész-helyreállításazure-környezetben](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)című cikkismerteti.

### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Az Oracle Linux azure accelerated networking támogatása az Oracle Linux 7 Update 5 (Oracle Linux 7.5) csomaggal érhető el. Ha nem tud frissíteni a legújabb Oracle Linux 7.5 kiadásra, előfordulhat, hogy az Oracle UEK kernel helyett a RedHat Compatible Kernel (RHCK) használatával kerülő megoldás. 

Az RHEL kernel használata az Oracle Linuxon belül az SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Az Azure Accelerated Networking esetében a minimális RHCKL kernel kiadásnak 3.10.0-862.13.1.el7-nek kell lennie. Ha az Oracle Linux uek kernelét használja az [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)szolgáltatással együtt, akkor az Oracle UEK kernel 5-ös verzióját kell használnia.

Ha nem az Azure Marketplace-en alapuló lemezképről telepít virtuális gépeket, akkor a következő kód futtatásával további konfigurációs fájlokat kell másolnia a virtuális gépre: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Egyéb
[Az Azure Virtual Machines DBMS üzembe helyezésének megfontolásai az SAP-munkaterheléshez](dbms_guide_general.md) az Oracle Database-t tartalmazó virtuális gépek üzembe helyezésével kapcsolatos egyéb fontos fogalmakat ismertetik, beleértve az Azure rendelkezésre állási csoportjait és az SAP figyelést.
