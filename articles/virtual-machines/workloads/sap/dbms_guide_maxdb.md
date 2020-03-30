---
title: SAP MaxDB, liveCache és Content Server központi telepítése az Azure virtuális gépein | Microsoft dokumentumok
description: Az SAP MaxDB, a liveCache és a Content Server központi telepítése az Azure-ban
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90de49ae3137735683bae6a18b5f7c8951b021ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645871"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB, liveCache és Content Server központi telepítése az Azure virtuális gépein

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




Ez a dokumentum számos különböző területet fed le, amelyeket figyelembe kell venni a MaxDB, a liveCache és a Content Server Azure IaaS-ben történő telepítésekor. A dokumentum előfeltételeként el kell olvasnia a [dokumentumot az Azure virtual machines DBMS üzembe helyezésével kapcsolatos szempontok sap-számítási feladatokhoz,](dbms_guide_general.md) valamint az Azure dokumentációjában az [SAP-munkaterhelés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)egyéb útmutatóit. 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Az SAP MaxDB windowsos központi telepítéseinek sajátosságai
### <a name="sap-maxdb-version-support-on-azure"></a>SAP MaxDB verziótámogatás az Azure-ban
Az SAP jelenleg támogatja az SAP MaxDB 7.9-es vagy újabb verzióját az SAP NetWeaver-alapú termékekkel való használatra az Azure-ban. Az SAP MaxDB-kiszolgáló, illetve az SAP NetWeaver-alapú termékekkel használható JDBC és ODBC illesztőprogramok összes frissítése kizárólag az SAP Service Marketplace-en keresztül érhető el a rendszerben. <https://support.sap.com/swdc>
Az SAP NetWeaver SAP MaxDB-n való <https://www.sap.com/community/topic/maxdb.html>futtatásával kapcsolatos általános információk a következő címen találhatók: .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Támogatott Microsoft Windows-verziók és Azure virtuálisgép-típusok SAP MaxDB DBMS esetén
Az SAP MaxDB DBMS támogatott Microsoft Windows-verziójának az Azure-ban című témakört a következő témakörben találja:

* [SAP termék rendelkezésre állási mátrixa (PAM)][sap-pam]
* SAP Megjegyzés [1928533]

Erősen ajánlott a Microsoft Windows operációs rendszer legújabb verzióját használni, amely a Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Elérhető SAP MaxDB dokumentáció a MaxDB-hez
Az SAP MaxDB dokumentációjának frissített listáját az alábbi SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Az SAP MaxDB konfigurációs irányelvei az SAP-telepítésekhez az Azure virtuális gépeken
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Tároló konfigurálása
Az Sap MaxDB Azure storage-tal kapcsolatos gyakorlati tanácsok az [RDBMS-telepítésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)való virtuális gép tárolási struktúrájában található általános javaslatokat követik.

> [!IMPORTANT]
> Más adatbázisokhoz hasonlóan az SAP MaxDB is rendelkezik adatokkal és naplófájlokkal. Az SAP MaxDB terminológiában azonban a helyes kifejezés a "kötet" (nem a "fájl"). Például vannak SAP MaxDB adatkötetek és naplókötetek. Ne tévesszük össze ezeket az operációs rendszer lemezköteteivel. 
> 
> 

Röviden meg kell:

* Ha Azure Storage-fiókokat használ, állítsa be az Azure Storage-fiókot, amely az SAP MaxDB-adatokat és naplóköteteket (adat- és naplófájlokat) **helyi redundáns tárolásra (LRS)** állítja az [SAP-munkaterheléshez szükséges Azure Virtual Machines DBMS-telepítés](dbms_guide_general.md)szempontjai című részben megadottak szerint.
* Válassza el az SAP MaxDB adatkötetek (adatfájlok) IO elérési útját a naplókötetek (naplófájlok) IO elérési útjától. Ez azt jelenti, hogy az SAP MaxDB adatköteteket (adatfájlokat) egy logikai meghajtóra kell telepíteni, és az SAP MaxDB naplóköteteket (naplófájlokat) egy másik logikai meghajtóra kell telepíteni.
* Állítsa be a megfelelő gyorsítótárazási típust az egyes lemezekhez, attól függően, hogy az SAP MaxDB-adatokhoz vagy naplókötetekhez (adat- és naplófájlokhoz) használja-e, és hogy az Azure Standard vagy az Azure Premium Storage-ot használja-e, az [Azure virtuális gépek DBMS-üzembe helyezésének megfontolásai sap-munkaterheléshez](dbms_guide_general.md)című részen leírtak szerint.
* Mindaddig, amíg a lemezenkénti aktuális IOPS-kvóta megfelel a követelményeknek, az összes adatkötet egyetlen csatlakoztatott lemezen tárolható, és az összes adatbázisnapló-kötet et egy másik csatlakoztatott lemezen is tárolhatja.
* Ha több IOPS-ra és/vagy területre van szükség, ajánlott microsoft window tárolókészleteket használni (csak microsoft Windows Server 2012-ben és újabb verzióban érhető el) egy nagy logikai eszköz több csatlakoztatott lemezen történő létrehozásához. További részletek: [Az Azure virtual machines DBMS üzembe helyezésének szempontjai az SAP-munkaterheléshez.](dbms_guide_general.md) Ez a megközelítés leegyszerűsíti a lemezterület kezeléséhez szükséges adminisztrációs többletterhelést, és elkerüli a fájlok manuális terjesztését több csatlakoztatott lemez között.
* erősen ajánlott az Azure Premium Storage használata a MaxDB-telepítésekhez. 

![Az Azure IaaS virtuális gép referenciakonfigurációja az SAP MaxDB DBMS-hez](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Biztonsági mentés és visszaállítás
Az SAP MaxDB Azure-ba való üzembe helyezésekor át kell tekintenie a biztonsági mentési módszertant. Még akkor is, ha a rendszer nem produktív rendszer, az SAP MaxDB által üzemeltetett SAP-adatbázisról rendszeres időközönként biztonsági másolatot kell kapnia. Mivel az Azure Storage három lemezképet tárol, a biztonsági mentés most már kevésbé fontos a rendszer tárolási hibák és a fontosabb működési vagy felügyeleti hibák elleni védelme szempontjából. A megfelelő biztonsági mentési és visszaállítási terv fenntartásának elsődleges oka a logikai vagy manuális hibák kompenzálása a pont-in-time helyreállítási képességek biztosításával. Így a cél az, hogy vagy biztonsági mentések segítségével visszaállítsa az adatbázist egy adott időpontban, vagy használja a biztonsági mentések az Azure-ban egy másik rendszer a meglévő adatbázis másolásával. 

Az Azure-beli adatbázisok biztonsági mentése és visszaállítása ugyanúgy működik, mint a helyszíni rendszerek esetében, így használhatja a szabványos SAP MaxDB biztonsági mentési/visszaállítási eszközöket, amelyeket az SAP [767598.] 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>A biztonsági mentés és visszaállítás teljesítményével kapcsolatos szempontok
A csupasz fémből készült központi telepítésekhez, a biztonsági mentés és visszaállítás teljesítménye attól függ, hogy hány kötet olvasható párhuzamosan, és a kötetek átviteli. Ezért feltételezhető:

* Minél kevesebb enkeresztül tárolják az adatbázis-eszközöket, annál kisebb a teljes olvasási átviteli
* Minél kevesebb cél (csíkos könyvtárak, lemezek) írhatja a biztonsági mentést, annál kisebb az átviteli

Az íráshoz használt célok számának növeléséhez két lehetőség közül választhat, esetleg kombinálva, az igényeitől függően:

* Külön kötetek felszentelése biztonsági mentéshez
* A biztonsági mentési célkötet csíkozása több csatlakoztatott lemezre a csíkozott lemezkötet IOPS-átviteli átviteli teljesítményének javítása érdekében
* Külön dedikált logikai lemezeszközök:
  * SAP MaxDB biztonsági mentési kötetek (pl. fájlok)
  * SAP MaxDB adatkötetek (pl. fájlok)
  * SAP MaxDB naplókötetek (pl. fájlok)

A kötet több csatlakoztatott lemezen való csíkozását korábban az [Azure virtual machines DBMS-telepítéssel kapcsolatos sap-számítási feladatok](dbms_guide_general.md)ban tárgyaltuk. 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Egyéb szempontok
Az összes többi általános terület, például az Azure availability sets vagy az SAP monitoring is érvényesek az [Azure virtuális gépek ADATBÁZIS-üzembe helyezésének megfontolásai sap számítási feladatokhoz](dbms_guide_general.md)című részen leírtak szerint.  Az SAP MaxDB adatbázissal rendelkező virtuális gépek központi telepítéseihez.
Az Sap MaxDB-specifikus egyéb beállításai az Azure virtuális gépei számára transzparensek, és az SAP [767598] megjegyzésében és az SAP-megjegyzésekben felsorolt különböző dokumentumokban találhatók:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Az SAP liveCache windowsos központi telepítésének sajátosságai
### <a name="sap-livecache-version-support"></a>SAP liveCache verziótámogatás
Az SAP liveCache minimális, az Azure virtuális gépekben támogatott verziója az **SAP LC/LCAPPS 10.0 SP 25,** beleértve **a liveCache 7.9.08.31** és **lca-build 25-öt,** amely az EhP 2 számára érhető el az SAP **SCM 7.0** és újabb verziókhoz.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Támogatott Microsoft Windows-verziók és Azure virtuálisgép-típusok az SAP liveCache DBMS-hez
A támogatott Microsoft Windows-verzió az SAP liveCache azure-ban, lásd:

* [SAP termék rendelkezésre állási mátrixa (PAM)][sap-pam]
* SAP Megjegyzés [1928533]

Erősen ajánlott a Microsoft Windows Server operációs rendszer legújabb verzióját használni. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Az SAP liveCache konfigurációs irányelvei az SAP-telepítésekhez az Azure virtuális gépeken
#### <a name="recommended-azure-vm-types-for-livecache"></a>Ajánlott Azure virtuálisgép-típusok az élőgyorsítótárhoz
Mivel az SAP liveCache egy olyan alkalmazás, amely hatalmas számításokat végez, a RAM és a CPU mennyisége és sebessége jelentős hatással van az SAP liveCache teljesítményére. 

Az SAP által támogatott Azure virtuálisgép-típusok (SAP Note [1928533)]esetében a virtuális gépszámára lefoglalt összes virtuális CPU-erőforrást a hipervizor dedikált fizikai CPU-erőforrásai támogatják. Nincs túlterhelés (és ezért nem verseny a CPU-erőforrások) kerül sor.

Hasonlóképpen az SAP által támogatott összes Azure virtuálisgép-példánytípushoz a virtuális gép memóriája 100%-ban a fizikai memóriához van rendelve – például a túl-kiépítés (túlzott kötelezettségvállalás) nem használatos.

Ebből a szempontból erősen ajánlott a legújabb Dv2, Dv3, Ev3 és M sorozatú virtuális gépek használata. A különböző virtuálisgép-típusok kiválasztása a liveCache-hez szükséges memóriától és a szükséges CPU-erőforrásoktól függ. Mint minden más DBMS-telepítéshez, célszerű az Azure Premium Storage-t a teljesítmény szempontjából kritikus kötetekhez használni.

#### <a name="storage-configuration-for-livecache-in-azure"></a>A liveCache tárolási konfigurációja az Azure-ban
Mivel az SAP liveCache az SAP MaxDB technológián alapul, az SAP MaxDB-hez ezen a dokumentumban ismertetett összes ajánlott Azure-tárolási gyakorlati javaslat az SAP liveCache-re is érvényes. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedikált Azure virtuális gép liveCache-forgatókönyvhöz
Mivel az SAP liveCache intenzíven használja a számítási teljesítményt, a produktív használathoz erősen ajánlott egy dedikált Azure virtuális gépen telepíteni. 

![Dedikált Azure virtuális gép az élőgyorsítótárhoz a produktív használati esethez](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Biztonsági mentés és visszaállítás az azure-beli liveCache-hez
a biztonsági mentés és visszaállítás, beleértve a teljesítménnyel kapcsolatos szempontokat is, már szerepel a jelen dokumentum megfelelő SAP MaxDB fejezeteiben. 

#### <a name="other-considerations"></a>Egyéb szempontok
Az összes többi általános terület már ismert et a vonatkozó SAP MaxDB fejezetben. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Az SAP Content Server Azure-beli windowsos telepítésének sajátosságai
Az SAP Content Server egy különálló, kiszolgálóalapú összetevő, amely különböző formátumokban tárolja a tartalmakat, például az elektronikus dokumentumokat. Az SAP Content Server a technológia fejlesztése, és kell használni cross-application minden SAP-alkalmazások. Ez telepítve van egy külön rendszer. A tipikus tartalom a Tudásraktár ból származó képzési anyag és dokumentáció, vagy a mySAP PLM dokumentumkezelő rendszerből származó műszaki rajzok. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Az SAP-tartalomkiszolgáló verziótámogatása az Azure virtuális gépeihez
Az SAP jelenleg a következőket támogatja:

* **SAP Content Server** **6.50-es verzióval (vagy újabbverzióval)**
* **SAP MaxDB 7.9-es verzió**
* **Microsoft IIS (Internet Information Server) 8.0-s verzió (és újabb)**

Erősen ajánlott az SAP Content Server legújabb és a **Microsoft IIS**legújabb verzióját használni. 

Ellenőrizze az SAP Content Server és a Microsoft IIS legújabb támogatott verzióit az [SAP termékelérhetőségi mátrixában (PAM).][sap-pam]

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Támogatott Microsoft Windows és Azure virtuálisgép-típusok az SAP Content Server szolgáltatáshoz
Ha meg szeretné tudni, hogy az SAP Content Server támogatott Windows-verziója az Azure-ban, olvassa el a következő témakört:

* [SAP termék rendelkezésre állási mátrixa (PAM)][sap-pam]
* SAP Megjegyzés [1928533]

Erősen ajánlott a Microsoft Windows Server legújabb verzióját használni.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Az SAP-tartalomkiszolgáló konfigurációs irányelvei az SAP-telepítésekhez az Azure virtuális gépein
#### <a name="storage-configuration-for-content-server-in-azure"></a>A tartalomkiszolgáló tárolási konfigurációja az Azure-ban
Ha úgy konfigurálja az SAP Content Server rendszert, hogy fájlokat tároljon az SAP MaxDB adatbázisban, az SAP MaxDB-hez ebben a dokumentumban említett összes Azure storage-ajánlott eljárás is érvényes az SAP Content Server forgatókönyvben. 

Ha úgy állítja be az SAP Content Server kiszolgálót, hogy fájlokat tároljon a fájlrendszerben, ajánlott dedikált logikai meghajtót használni. A Windows storage Spaces használatával is növelheti a logikai lemez méretét és az IOPS-átviteli kapacitást, ahogy azt az [Azure virtuális gépek ADATBÁZIS-telepítési szempontjai az SAP-munkaterheléshez című részen leírtak](dbms_guide_general.md)szerint. 

#### <a name="sap-content-server-location"></a>SAP-tartalomkiszolgáló helye
Az SAP Content Server t ugyanabban az Azure-régióban és az Azure-virtuális hálózatban kell telepíteni, ahol az SAP-rendszer telepítve van. Szabadon eldöntheti, hogy az SAP Content Server-összetevőket egy dedikált Azure virtuális gépre vagy ugyanazon a virtuális gépre szeretné-e telepíteni, ahol az SAP-rendszer fut. 

![Dedikált Azure virtuális gép az SAP-tartalomkiszolgálóhoz](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP gyorsítótárazási kiszolgáló helye
Az SAP cache-kiszolgáló egy további kiszolgálóalapú összetevő, amely helyileg biztosítja a (gyorsítótárazott) dokumentumok elérését. Az SAP-gyorsítótárazási kiszolgáló gyorsítótárazza az SAP-tartalomkiszolgáló dokumentumait. Ez a hálózati forgalom optimalizálása, ha a dokumentumokat többször is le kell kérni különböző helyekről. Az általános szabály az, hogy az SAP cache-kiszolgálónak fizikailag közel kell lennie ahhoz az ügyfélhez, amely hozzáfér az SAP gyorsítótár-kiszolgálóhoz. 

Itt két lehetőség közül választhat:

1. **Az ügyfél egy háttérSAP-rendszer** Ha egy háttérSAP-rendszer sap content server elérésére van konfigurálva, akkor az SAP-rendszer egy ügyfél. Mivel az SAP-rendszer és az SAP Content Server ugyanabban az Azure-régióban, ugyanabban az Azure-adatközpontban van telepítve, fizikailag közel vannak egymáshoz. Ezért nincs szükség dedikált SAP-gyorsítótár-kiszolgálóra. Az SAP felhasználói felületi ügyfelei (SAP GUI vagy webböngésző) közvetlenül érik el az SAP-rendszert, és az SAP-rendszer lekéri a dokumentumokat az SAP Content Serverből.
2. **Az ügyfél egy helyszíni webböngésző** Az SAP Content Server beállítható úgy, hogy közvetlenül a webböngészővel férjen hozzá. Ebben az esetben a helyszíni webböngésző az SAP Content Server ügyfele. A helyszíni adatközpont és az Azure-adatközpont különböző fizikai helyeken (ideális esetben közel vannak egymáshoz). A helyszíni adatközpont az Azure-ból az Azure-ból helyek közötti VPN-en vagy ExpressRoute-on keresztül csatlakozik az Azure-hoz. Bár mindkét lehetőség biztonságos VPN-hálózati kapcsolatot biztosít az Azure-ral, a helyek közötti hálózati kapcsolat nem biztosít hálózati sávszélességet és késésű SLA-t a helyszíni adatközpont és az Azure-adatközpont között. A dokumentumokhoz való hozzáférés felgyorsítása érdekében az alábbi elkövetésre lehet a következő t:
   1. Telepítse az SAP Cache Server-t a helyszínen, közel a helyszíni webböngészőhöz (az alábbi ábrán látható lehetőség)
   2. Konfigurálja az Azure ExpressRoute szolgáltatást, amely nagy sebességű és alacsony késleltetésű dedikált hálózati kapcsolatot kínál a helyszíni adatközpont és az Azure-adatközpont között.

![Az SAP Cache Server helyszíni telepítésének lehetősége](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Biztonsági mentés / visszaállítás
Ha úgy konfigurálja az SAP Content Server kiszolgálót, hogy fájlokat tároljon az SAP MaxDB adatbázisban, a biztonsági mentési/visszaállítási eljárás és a teljesítményre vonatkozó szempontok a dokumentum SAP MaxDB fejezeteiben már szerepelnek. 

Ha úgy állítja be az SAP Content Server kiszolgálót, hogy fájlokat tároljon a fájlrendszerben, az egyik lehetőség a dokumentumok helye szerinti teljes fájlstruktúra manuális biztonsági mentésének/visszaállításának végrehajtása. Az SAP MaxDB biztonsági mentéshez/visszaállításhoz hasonlóan ajánlott egy dedikált lemezkötet biztonsági mentési célra. 

#### <a name="other"></a>Egyéb
Az SAP-tartalomkiszolgáló-specifikus egyéb beállítások az Azure virtuális gépei számára transzparensek, és különböző dokumentumokban és SAP-megjegyzésekben vannak leírva:

* <https://service.sap.com/contentserver> 
* SAP Megjegyzés [1619726]  
