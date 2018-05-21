---
title: SAP NetWeaver Azure virtuális gépek DBMS telepítésének |} Microsoft Docs
description: Az SAP NetWeaver Azure virtuális gépek adatbázis-kezelő telepítése
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c78b764b66e677144186831b6139fd6a0aae7e6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Az SAP NetWeaver Azure virtuális gépek adatbázis-kezelő telepítése
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez az útmutató a megvalósítás és a Microsoft Azure SAP szoftverek telepítését dokumentáció részét képezi. Ez az útmutató elolvasása, előtt olvassa el a [tervezési és megvalósítási útmutató][planning-guide]. Ez a dokumentum ismerteti a központi telepítés különböző relációs adatbázis Management Systems (RDBMS) és az Azure infrastruktúra használja, mint a szolgáltatási (IaaS) képességeket kapcsolódó termékek SAP a Microsoft Azure virtuális gépek (VM) együtt.

A dokumentum kiegészíti az SAP-dokumentáció és az SAP, az elsődleges erőforrások telepítése és a SAP szoftver központi telepítését megfelelnek a megadott platformok.

## <a name="general-considerations"></a>Általános megfontolások
Ez a fejezet az SAP-kapcsolódó adatbázis-kezelő rendszert futtató Azure virtuális gépeken szempontok be. Ez a fejezet az adott adatbázis-kezelő rendszereken néhány hivatkozások találhatók. Ehelyett az adott adatbázis-kezelő rendszerek kezelése a dokumentum után ez a fejezet.

### <a name="definitions-upfront"></a>Definíciók előzetes megfizetése esetén
A dokumentumban a következő kifejezéseket használjuk:

* IaaS: Szolgáltatott infrastruktúra.
* A PaaS: Platform szolgáltatásként.
* SaaS: Szolgáltatott szoftver.
* SAP összetevő: egy egyedi SAP alkalmazás, például ECC, a Sávszélesség, a megoldás Manager vagy a EP  SAP-összetevők a hagyományos ABAP vagy Java technológiák vagy egy nem NetWeaver alapú alkalmazás, például üzleti objektumok is alapulhat.
* SAP-környezetben: egy vagy több SAP összetevők logikailag egy csoportba egy üzleti funkció, például a fejlesztés, QAS, képzési, vész-Helyreállítási vagy éles.
* SAP fekvő: Ez vonatkozik az egész SAP eszközök az ügyfél informatikai fekvő. Az SAP fekvő minden üzemi és nem éles környezetben tartalmaz.
* SAP-verzió: Az adatbázis-kezelő réteg és kombinációja alkalmazás réteget, például egy SAP ERP fejlesztőrendszer, SAP BW gazdagépes tesztrendszer, SAP CRM éles rendszer stb. Azure-környezetekhez, a nem támogatott a két réteg között a helyszíni és az Azure felosztása. Ez azt jelenti, hogy egy SAP rendszer vagy a helyszínen telepített vagy telepítve van az Azure-ban. Azonban telepítheti a másik egy SAP fekvő Azure vagy a helyszíni rendszeren. Például sikerült telepíteni a SAP CRM-fejlesztői és tesztrendszerek Azure, de az SAP CRM éles rendszer helyszíni.
* Csak felhőalapú telepítési: egy központi telepítést, amikor az Azure-előfizetés nem csatlakozik a pont-pont vagy ExpressRoute-kapcsolat a helyszíni hálózati infrastruktúrára. Közös Azure dokumentációja az ilyen típusú központi telepítések egyaránt "Csak felhőalapú" telepítések leírása. Ezzel a módszerrel telepített virtuális gépek az internethez, és a virtuális gépeket az Azure-ban rendelt nyilvános Internet végpontok keresztül érhetők el. A helyszíni Active Directory (AD) és a DNS nem bővítette ki az Azure-ba, az ilyen típusú központi telepítések. Ezért a virtuális gépek nincsenek a helyszíni Active Directory részét. Megjegyzés: Ebben a dokumentumban csak felhő alapú telepítések esetén is meg van adva teljes SAP tájak, amely a helyszíni kizárólag az Azure Active Directory vagy a névfeloldás kiterjesztés nélkül futtatja nyilvános felhőbe. Csak felhőalapú konfigurációk nem támogatottak a termelési SAP rendszerek vagy konfigurációk, ahol az SAP STM vagy más helyszíni erőforrásokat kell használható Azure és a helyszínen található erőforrások üzemeltetett SAP-rendszerek között.
* Létesítmények közötti: Bemutatja egy olyan forgatókönyvet, ahol a virtuális gépek Azure-előfizetéshez, amely rendelkezik pont-pont, többhelyes vagy a helyszíni datacenter(s) és az Azure között ExpressRoute kapcsolat van telepítve. Közös Azure dokumentációja, az ilyen típusú központi telepítések egyaránt létesítmények közötti forgatókönyv leírása. A kapcsolat oka, hogy a helyi tartomány, a helyszíni Active Directory és a helyi DNS-kiterjeszti Azure. A helyszíni fekvő az időtartam, az előfizetéshez tartozó Azure eszközökre. Ha ezt a bővítményt, a virtuális gépek a helyi tartomány része lehet. A helyi tartomány tartományi felhasználók férhetnek hozzá a kiszolgálókat, és szolgáltatásokat futtathatja virtuális gépek (például adatbázis-kezelő szolgáltatás). Virtuális gépek közötti kommunikációt és a névfeloldás telepítése a helyszíni és az Azure-ban telepített virtuális gépek lehetséges. Várhatóan ez lesz a leggyakoribb forgatókönyvet az Azure SAP-eszközök telepítése. További információkért lásd: [Ez a cikk] [ vpn-gateway-cross-premises-options] és [Ez a cikk][vpn-gateway-site-to-site-create].

> [!NOTE]
> Létesítmények közötti telepítések SAP rendszert futtató Azure virtuális gépek esetén a helyszíni-tartománybeli tagsággal SAP rendszerek éles SAP rendszerek támogatottak. Létesítmények közötti konfigurációk támogatottak a kijelzők telepítése, vagy végezze el az SAP tájak az Azure. A teljes SAP fekvő az Azure-ban futó szükség van a helyszíni tartományi és REKLÁMOK részévé virtuális gépek. Hibrid-IT-forgatókönyvekkel kapcsolatos megtartásról korábbi verzióiban a dokumentációt, ha a kifejezés *hibrid* a tényt, hogy van-e a létesítmények közötti kapcsolat a helyszíni és az Azure közötti feltörték. Ebben az esetben *hibrid* azt is jelenti, hogy a virtuális gépeket az Azure-ban a helyszíni Active Directory részét képezik.
> 
> 

Néhány Microsoft dokumentációjában létesítmények közötti forgatókönyv különösen az adatbázis-kezelő magas rendelkezésre ÁLLÁSÚ konfiguráció egy kicsit másképp ismerteti. Az SAP-kapcsolódó dokumentumok esetében a létesítmények közötti forgatókönyv forrni kezd a pont-pont vagy titkos (ExpressRoute) megszakadt a kapcsolat le, és arra, hogy a SAP fekvő elosztása között a helyszíni és az Azure.

### <a name="resources"></a>További források
Azure SAP-telepítéshez az alábbi útmutatók érhetők el:

* [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]
* [SAP NetWeaver Azure virtuális gépek központi telepítését][deployment-guide]
* [Az SAP NetWeaver (Ez a dokumentum) az Azure virtuális gépek DBMS-telepítés][dbms-guide]

Az alábbi SAP megjegyzések Azure SAP kapcsolódnak:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |Az Azure-on SAP-alkalmazásokból: támogatott termékek és az Azure virtuális Gépen |
| [2015553] |A Microsoft Azure SAP: Előfeltételek támogatja |
| [1999351] |Hibaelhárítási továbbfejlesztett Azure megfigyelése az SAP |
| [2178632] |Kulcs figyelési metrikákat az SAP, a Microsoft Azure |
| [1409604] |A Windows virtualizálási: fokozott figyelése |
| [2191498] |Az Azure Linux SAP: fokozott figyelése |
| [2039619] |A Microsoft Azure-ban az Oracle-adatbázishoz SAP-alkalmazásokból: támogatott termékek és termékverziók |
| [2233094] |DB6: Azure-ban IBM DB2 Linux, UNIX és a Windows - további információ az SAP-alkalmazásokból |
| [2243692] |A Microsoft Azure (IaaS) virtuális gép Linux: SAP licenc problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7.x: telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítése és frissítése |
| [1597355] |A Linux rendszerhez használt lapozóterület javaslat |
| [2171857] |Oracle Database 12c - fájlrendszer Linux-támogatás |
| [1114181] |Oracle Database 11g - fájlrendszer Linux-támogatás |


Emellett olvassa el a [Állapotváltozás Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) Linux tartalmazó összes SAP-jegyzetet.

Rendelkeznie kell a Microsoft Azure-architektúra és a Microsoft Azure virtuális gépeken telepített és üzemeltetett hogyan ismeretét. További információt: <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Folyamatban van, **nem** megvitatása a Microsoft Azure Platform, egy a Microsoft Azure Platform (PaaS) szolgáltatás által kínált. A dokumentum tárgya futó egy adatbázis-kezelő rendszer (DBMS) a Microsoft Azure virtuális gépek (IaaS) az adatbázis-kezelő a helyszíni környezetben futna. Adatbázis-képességeket és funkciók között ezek két ajánlatok nagyon eltérőek, és kell nem keverhetők egymás mellett. Lásd még: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Mivel azt hanem IaaS, általában a Windows, Linux és adatbázis-kezelő telepítése és konfigurálása a következők ugyanaz, mint bármely virtuális gép vagy a helyszíni volna telepítése operációs rendszer nélküli számítógép. Van azonban néhány architektúra és a rendszer felügyeleti megvalósítási döntésekhez, különböző IaaS használata során. A jelen dokumentum célja ismertetik a konkrét architekturális és a rendszer felügyeleti módszer közötti különbségeket, elő kell készíteni az infrastruktúra-szolgáltatási használatakor.

A teljes területek különbséggel, hogy a dokumentum ismerteti a általában a következők:

* SAP rendszerek győződjön meg arról, hogy rendelkezik a szükséges adatokat a megfelelő virtuális gép/lemez elrendezésének tervezése elrendezés fájlt, és elég IOPS, a munkaterhelés számára érhető el.
* Hálózat használatának szempontjai IaaS.
* Adott adatbázis-szolgáltatások az adatbázis-Elrendezés optimalizálása érdekében.
* Infrastruktúra-szolgáltatási biztonsági mentési és visszaállítási szempontok.
* Különböző központi telepítési lemezképek használatával.
* Magas rendelkezésre állás érdekében Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS üzembe helyezésének struktúra
Érdekében kövesse az ebben a fejezetben, fontos megismerni, hogy mi a bemutatott [ez] [ deployment-guide-3] fejezete a [telepítési útmutató][deployment-guide]. A másik Virtuálisgép-sorozat és azok és különbségek Azure Standard és prémium szintű Storage ismerete tisztában legyen, és ez a fejezet elolvasása előtt ismert.

2015. márciusi, amíg a lemezek, amely tartalmazza az operációs rendszer volt legfeljebb 127 GB-nál. Ez a korlátozás kapott vissza a 2015. márciusi (További információ az ellenőrzéshez <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Az operációs rendszer tartalmazó lemezeken ott lehet akkora, mint bármely más lemezt. Mindazonáltal azt még inkább egy struktúra, ahol az operációs rendszer, az adatbázis-kezelő és a végleges SAP bináris fájlok nem azonosak az adatbázisfájlokat a központi telepítés. Ezért várhatóan SAP rendszereken futó Azure virtuális gépek rendelkezik az operációs rendszer, az adatbázis felügyeleti rendszer futtatható fájljainak és az SAP végrehajtható fájlok telepített alap virtuális gép (vagy lemez). A DBMS adatainak és naplókönyvtárainak fájlok külön lemezeken tárolja az Azure Storage (Standard vagy prémium szintű Storage) és logikai lemez csatolva az eredeti Azure operációs rendszer lemezképének virtuális gép. 

Függő Azure Standard vagy prémium szintű Storage (például használatával a DS-méretek és GS sorozatnak virtuális gépeken) használni nincs más kvótákat, az Azure-ban ismertetett [itt (Linux)] [ virtual-machines-sizes-linux] és [ Itt (Windows)][virtual-machines-sizes-windows]. A lemez elrendezése megtervezésekor kell egyensúlyba a kvóták a következő elemek:

* Az adatfájlok számát.
* A fájlokat tartalmazó lemezek számát.
* Egyetlen lemez IOPS vonatkozó kvótákat.
* Lemezenként adatátvitelt.
* A Virtuálisgép-méretet / lehetséges további adatok lemezek számát.
* A teljes tárolási teljesítményt biztosíthat egy virtuális Gépet.

Azure érvénybe lépteti az IOPS kvóta adatok lemezenként. Ezek a kvóták eltérőek lemezeken Azure standard szintű tárolást és a prémium szintű Storage működtetnek. I/o-késések különböznek a is nagyon tényezők kézbesíti hatékonyabb i/o-késések prémium szintű Storage tárolási kétféle. A virtuális gép különböző típusú mindegyik rendelkezik-e, hogy a program tudni csatolni az adatlemezek korlátozott számú. Egy másik korlátozás, hogy csak bizonyos Virtuálisgép-típusokon kihasználhatják a prémium szintű Azure Storage. Ez azt jelenti, hogy bizonyos VM írja be a következőt a döntést előfordulhat, hogy nem csak alapján vezeti a CPU és memória követelményeit, hanem az IOPS, a lemezek számát vagy a prémium szintű Storage lemezek típusú általában méretezése késés és a lemez átviteli követelmények. Különösen a prémium szintű Storage mérete a lemez is lehet, hogy meghatározni iops-érték és a teljesítményt, amelyet a valósítható meg egyes lemezek számát.

Hogy a teljes IOPS arány, csatlakoztatva a lemezek számát, és a virtuális gép méretét az összes operációs rendszer együtt, egy Azure konfigurálása a helyszíni telepítéshez eltér az SAP rendszer okozhatja. Az IOPS-korlátok vonatkoznak Logikaiegység-számonként esetében általában konfigurálhatók a helyszíni telepítésekkel. Mivel az Azure Storage ezen korlátok rögzített vagy prémium szintű Storage függ a lemez típusát. Ezért a helyszíni telepítésekkel rendelkező látható felhasználói konfigurációk adatbázis-kiszolgálók által használt számos különböző kötetek például SAP és az adatbázis-kezelő vagy speciális kötetet, az ideiglenes adatbázisok vagy tábla szóközöket, különleges végrehajtható fájlok számára. Ha ilyen egy a helyszíni rendszer Azure helyezik át, azt vezethet a pazarlás lehetséges IOPS sávszélesség által végrehajtható fájlok, vagy ne hajtsa végre a bármelyikéhez vagy nem IOPS nagy adatbázisokhoz lemezét jelentős. Ezért az Azure virtuális gépeken azt javasoljuk, hogy az adatbázis-kezelő és az SAP végrehajtható fájlok telepíteni az operációs rendszer lemezének Ha lehetséges.

Az adatbázisfájlok és a naplófájlok és a használt, Azure tárolási típusát elhelyezésének IOPS, a késés és a átviteli követelmények definiálni kell. Ahhoz, hogy rendelkezik a tranzakciós naplónak elegendő IOPS, előfordulhat, hogy kényszerített kihasználja a tranzakciós naplófájlt több lemezt, vagy használjon nagyobb prémium szintű Storage lemezt. Ebben az esetben egy egy szoftveres RAID (a példa a Windows Storage Windows készlet vagy MDADM és LVM (logikai kötetkezelő) Linux), a lemezek, a tranzakciós napló tartalmazó volna létrehozni.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Egy Azure virtuális gép D:\ meghajtón egy nem tárolt meghajtót, amely az Azure számítási csomóponton egyes helyi lemezek alapját. Mivel nem tárolt, ez azt jelenti, hogy a tartalmat a D:\ meghajtóra végzett minden módosítás elvész, amikor a virtuális gép újraindítása után. "Végrehajtott módosítások" azt jelenti, mentett fájlokat, a létrehozott címtárakat, a telepített alkalmazások, stb.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure virtuális gépek automatikusan, amely az Azure számítási csomópont alapját helyi lemezek nem tárolt meghajtó /mnt/resource meghajtót csatlakoztatni. Mivel nem tárolt, ez azt jelenti, hogy /mnt/resource tartalma végrehajtott módosítások elvesznek, ha a virtuális gép újraindítása után. Végrehajtott módosítások azt jelenti,-fájlokat, a létrehozott címtárakat, a telepített alkalmazások, a stb.
> 
> 

- - -
Az Azure Virtuálisgép-sorozat függ, a számítási csomóponton a helyi lemezek megjelenítése más-más teljesítménybeli, például csoportosíthatók:

* A0-A7: Erősen korlátozott teljesítményét. Nem használható a semmit túl a windows lapozófájlja
* A8-A11: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség
* D sorozatú: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség
* DS-méretek: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség
* G-sorozat: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség
* GS sorozatnak: Nagyon jó teljesítményt nyújt az egyes tízezer IOPS és > 1GB/s átviteli sebesség

A fenti utasítások alkalmazzák, a virtuális gép típusú, amelynek az SAP hitelesít. A Virtuálisgép-sorozat kiváló IOPS és átviteli jogosultsághoz használja ki az egyes adatbázis-kezelő funkciók – például a tempdb vagy ideiglenes táblán terület szerint.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>A virtuális gépek és adatlemezek gyorsítótárazás
Adatlemezek a portálon keresztül, vagy azt csatlakoztassa a virtuális gépek feltöltött lemezek létrehozásakor azt kiválaszthatja, hogy az i/o-forgalom a virtuális gép és az Azure storage található lemezek között gyorsítótárba kerüljenek-e. Azure Standard és prémium szintű Storage használata a két technológia az ilyen típusú gyorsítótár. Mindkét esetben a gyorsítótár magát lenne lemez a ideiglenes lemezt (a Windows D:\) vagy /mnt/resource Linux rendszeren a virtuális gép által használt azonos meghajtókat alapját.

Az Azure standard szintű tárolót a lehető gyorsítótár típusok a következők:

* Gyorsítótárazása
* Olvasási gyorsítótárazás lemezre
* Olvasási és írási gyorsítótárazást

Ahhoz, hogy következetes és determinisztikus teljesítményre van szüksége, állítsa be a gyorsítótárazást az Azure standard szintű tárolást tartalmazó lemezein **DBMS kapcsolatos adatokat fájlok, naplófájlok és táblázat a hely a "NONE"**. A virtuális gép gyorsítótárazását az alapértelmezett maradjanak.

A prémium szintű Azure Storage a következő gyorsítótárazási lehetőségek érhetők el:

* Gyorsítótárazása
* Olvasási gyorsítótárazás lemezre

A prémium szintű Azure Storage javasoljuk, hogy kihasználja **olvasási gyorsítótárazás lemezre az adatfájlok** az SAP-adatbázist, és válassza a **naplófájlokhoz lemezek gyorsítótárazása**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Szoftveres RAID
A már fentieknek között a lemezeinek száma miatt is beállíthat az adatbázis-fájlok szükséges IOPS száma egyensúlyba kell, és a maximális IOPS egy Azure virtuális gép / lemez vagy a prémium szintű Storage lemeztípus biztosít. Lemezek keresztül az IOPS terhelés kezelésére legegyszerűbb módja a szoftveres RAID keresztül a különböző lemezek létrehozásához. Majd helyezze el az SAP DBMS adatforrás adatfájljainak számos kívül a szoftveres RAID faragottnak logikaiegység-SZÁMOT. A követelmények, érdemes figyelembe venni a prémium szintű Storage használatát, valamint a három közül kettő óta függ másik prémium szintű Storage-lemezek segítségével magasabb IOPS kvóta, mint a szokásos tároló mérete alapján. Prémium szintű Azure Storage által biztosított jelentős jobb i/o késés mellett. 

Ugyanez vonatkozik a tranzakciós napló, a másik adatbázis-kezelő rendszerek. Ezek további Tlog fájlok hozzáadása a nem működik, mert az adatbázis-kezelő rendszerek írjon be egy fájlt egy időben. Ha magasabb IOPS-díjszabás van szükség, mint egyetlen szabvány-alapú tárolás lemez biztosíthat, akkor is paritásos több szabványos tárolólemezek keresztül, vagy használhatja a nagyobb prémium szintű Storage lemez típusa túl magasabb IOPS-díjszabás is megadja a írásra kisebb késést biztosít az tényezők I / Az operációs rendszer tranzakciós naplóba.

Az Azure-környezetekhez, amely volna alkalmazást egy szoftveres RAID használatával észlelt helyzetek a következők:

* Tranzakciónapló napló/mégis szükség magasabb iops-értéket, mint az Azure biztosít egyetlen lemezen. Fent említett Ez a logikai egység épület több lemezt a szoftveres RAID használatával keresztül megoldhatók.
* I/o munkaterhelés a egyenetlen eloszlását a különböző adatokat a fájlokat az SAP-adatbázist. Ilyen esetekben egy fedezheti gyakran ahelyett, hogy elérte-e a kvóta egy adatfájlt. Mivel más adatfájlok még nem kapják meg megközelíti a IOPS egyetlen lemezt a beállított kvótát. Ebben az esetben a legegyszerűbb megoldás az keresztül egy szoftveres RAID használatával több lemez egy logikai egység létrehozásához. 
* A pontos i/o-munkaterhelés adatok fájlonként és nagyjából csak tudja, hogy mi a teljes IOPS munkaterhelés szemben az adatbázis-kezelő nem tudja. Ehhez legegyszerűbb egy szoftveres RAID segítségével. egy logikai egység létrehozásához. Több lemez a logikai Egységnek mögött kvótáinak összege kell majd teljesítéséhez ismert IOPS sebessége.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Azt javasoljuk, hogy Windows tárolóhelyek használatával, ha a Windows Server 2012 vagy újabb verzióját futtatja. Hatékonyabb, mint a korábbi Windows Windows csíkozást. Szükség lehet hozhat létre a Windows a Tárolókészletek és a tárolóhelyek PowerShell-parancsok használata Windows Server 2012 operációs rendszert. A PowerShell-parancsok itt található <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Csak a MDADM és az LVM (logikai kötetkezelő) hozhat létre egy szoftveres RAID Linux rendszeren támogatott. További információkért olvassa el a következő cikkeket:
> 
> * [Konfigurálja a szoftveres RAID Linux] [ virtual-machines-linux-configure-raid] (a MDADM)
> * [A Linux virtuális gép az Azure-ban LVM konfigurálása][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Tud dolgozni prémium szintű Azure Storage általában Virtuálisgép-sorozat hasznosítására vonatkozó szempontok a következők:

* I/o-késések hamarosan SAN/NAS-eszközök biztosítanak a követelményeinek.
* Igény szerinti tényezők jobb késleltetéséről Azure standard szintű tárolást biztosíthat, mint a.
* Mint mi érhető el az egyes virtuális gép elleni több Standard tárolási lemezzel rendelkező virtuális gépenként magasabb IOPS írja be.

Mivel a mögöttes Azure Storage replikálja az egyes lemezek legalább három tárolócsomóponthoz egyszerű RAID 0 csíkozást használható. Nincs szükség az RAID5 vagy RAID1 végrehajtásához.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>A Microsoft Azure Storage
A Microsoft Azure Storage tárolja az alap virtuális gép (és az operációs rendszer) és a lemezek vagy a Blobok legalább három különálló tárolócsomóponthoz. Egy tárfiókhoz vagy a felügyelt lemezes létrehozásakor választási lehetőség van a védelem itt látható módon:

![Georeplikáció engedélyezve az Azure Storage-fiókban][dbms-guide-figure-100]

Az Azure Storage helyi replikációs (helyileg redundáns) biztosít, amely néhány ügyfél központi telepítése sikerült biztosítanak infrastrukturális hiba miatt adatvesztés elleni védelmi szintek. A fentiek szerint a ötödik alatt az első három egyik változata négy különböző lehetőségek közül. Keresése szorosabb, azokat is különböztetünk meg:

* **Prémium szintű helyileg redundáns tárolás (LRS)**: prémium szintű Azure Storage szolgáltatás nyújt nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása. Három replikákat az adatok egy Azure-régió, azonos Azure-adatközpontban belül van. A példányok szerepelnek a különböző tartalék és frissítési tartományok (fogalmak című [ez] [ planning-guide-3.2] fejezetében a [tervezési útmutatója][planning-guide]). Nem működik egy tárolási Csomóponthiba vagy lemezhiba miatt is az adatok replikáját, esetén egy új replika automatikusan létrejön.
* **Helyileg redundáns tárolás (LRS)**: Ebben az esetben van egy Azure-régió, azonos Azure-adatközpontban belüli adatok három replikáit. A példányok szerepelnek a különböző tartalék és frissítési tartományok (fogalmak című [ez] [ planning-guide-3.2] fejezetében a [tervezési útmutatója][planning-guide]). Nem működik egy tárolási Csomóponthiba vagy lemezhiba miatt is az adatok replikáját, esetén egy új replika automatikusan létrejön. 
* **Földrajzi redundancia tárolás (GRS)**: Ebben az esetben van egy aszinkron replikáció, amely hírcsatornák egy további három replikákat az adatok egy másik Azure régióban, amely a legtöbb esetben ugyanabban a földrajzi régióban (például Észak-Európában és Nyugat-Európa ). Az eredmény három további replikákat, így sum hat replikák szerepelnek. Ez a változata kiegészítése földrajzi replikált Azure-régióban az adatok helyének (írásvédett Georedundáns) olvasás céljából.
* **Redundáns tárolás (ZRS) zónát**: Ebben az esetben az adatok három replikáinak egy Azure-régióban maradnak. A [ez] [ planning-guide-3.1] fejezete a [tervezési útmutatója] [ planning-guide] egy Azure-régió lehet adatközpontok közel. LRS esetén a replikák volna kell elosztani a különböző adatközpontokban, amelyek egy Azure-régiót.

További információ található [Itt][storage-redundancy].

> [!NOTE]
> Az adatbázis-kezelő történő telepítések esetén a redundáns Georedundáns tárolás használata nem ajánlott
> 
> Az Azure Storage Georeplikáció aszinkron. Egyes egy egyetlen virtuális géphez csatlakoztatott lemezek replikációját zárolási lépésben nincsenek szinkronizálva. Ezért nem alkalmas különböző lemezek elosztott vagy szemben a szoftveres RAID több lemez alapján telepített adatbázis-kezelő fájlok replikálásához. Adatbázis-kezelő szoftver szükséges, hogy az állandó lemezegységet más logikai egységek és az alapjául szolgáló lemezek/forgórészek pontosan szinkronizálva. Adatbázis-kezelő szoftver használ a különböző mechanizmusokat I/O írási tevékenységeit, és egy adatbázis-kezelő jelenti, hogy a replikáció által megcélzott lemezegység sérült-e, ha ezek még néhány ezredmásodperc változhat. Ezért ha a konfigurációját egy adatbázis több lemezre georeplikált felhőbe, ilyen replikációs kell végezhető el adatbázis eszközöket és funkciókat. Egy ne hagyatkozzon kizárólag az Azure Storage a Georeplikáció a feladat végrehajtásához. 
> 
> A probléma a legegyszerűbb példa rendszert ismertetik. Tételezzük fel, hogy egy SAP rendszer Azure, amely az adatbázis-kezelő adatforrás adatfájljainak tartalmazó nyolc lemezek kiegészítve egy lemezt, a tranzakciós naplófájlt tartalmazó töltve. A kilenc lemezeken írni őket egy egységes módszer alapján az adatbázis-kezelő adatokkal rendelkeznek, hogy az adatok írása az adatok vagy a tranzakciós naplófájlok.
> 
> Földrajzi-replikálás megfelelően érdekében az adatok és karbantartása a konzisztens adatbázis lemezkép minden kilenc lemezek tartalmának kell a megfelelő sorrendben az i/o-műveletek végrehajtódtak szemben a kilenc különböző lemezek georeplikált kellene. Azure Storage georeplikáció azonban nem engedélyezi deklarálható lemezek közötti függőségek. Ez azt jelenti, hogy a Microsoft Azure Storage a georeplikáció nem ismeri a tényt, hogy a kilenc különböző lemezek tartalmát kapcsolódnak egymáshoz, és, hogy az adatok változásait megegyeznek, csak akkor, ha az i/o-műveletek sorrendben replikálása történt összes a kilenc lemezeket.
> 
> Túl magas, hogy a forgatókönyv a georeplikált lemezképeket nem biztosítanak egy egységes adatbázis kép alatt esélyét is van a teljesítményét, amely mutatja, földrajzi redundáns tárolás súlyosan befolyásolhatja a teljesítményt. Összefoglalva nem az ilyen típusú adattároló redundanciája, amely az adatbázis-kezelő típus munkaterhelésekhez.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Virtuális merevlemezek a virtuális gép az Azure Storage szolgáltatásfiókok leképezése
Ez a fejezet csak az Azure Storage-fiókok vonatkozik. Ha tervezi felügyelt lemezek, a fejezet említett korlátozások nem alkalmazhatók. Felügyelt lemezek kapcsolatos további információkért olvassa el a fejezet [kezelt lemezek] [ dbms-guide-managed-disks] a jelen útmutató.

Egy Azure Storage-fiók nem csak olyan felügyeleti szerkezetet, de a tárgyat korlátozás is. Mivel a korlátozások eltérések lehetnek e döntésről bővebben egy Azure standard szintű Storage-fiókot vagy egy Azure prémium szintű Storage-fiókot. A pontos lehetőségeit és korlátait felsorolt [Itt][storage-scalability-targets]

Ezért az Azure standard szintű tárolást fontos megjegyezni, hogy nincs maximális az iops-értéket a tárfiók (sort tartalmazó **teljes kérelmek gyakorisága** a [a cikk][storage-scalability-targets]). Emellett egy kezdeti korlátozás van (2015. július) az Azure-előfizetés / 100 tárfiókok. Ezért ajánlott IOPS virtuális gépek Azure standard szintű tárolást használ több storage-fiókok közötti elosztása érdekében. Mivel egy virtuális ideális használ egy tárfiókot, ha lehetséges. Ezért ha döntésről bővebben az adatbázis-kezelő központi telepítések, ahol minden Azure standard szintű tárolást a tárolt virtuális merevlemez elérhetik a kvótakorlát, csak telepítsen Azure standard szintű tárolást használó Azure Storage-fiók / 30-40 VHD-k. Másrészről ha kihasználja a prémium szintű Azure Storage és nagy adatbázis kötetek tárolására, valószínűleg finom IOPS értékben. Egy Azure prémium szintű Storage-fiók azonban eltérő módon határértéknél erősebb korlátozást az adatmennyiség egy Azure standard szintű Tárfiók. Ennek eredményeképpen csak telepíthet virtuális merevlemezek belül egy Azure prémium szintű Tárfiók csak korlátozott számú előtt az kötet korlátot elérte-e. A végén gondolja egy Azure Storage-fiókot az "A virtuális SAN" képességei iops-érték és/vagy kapacitása korlátozott. Ennek eredményeképpen a feladat marad, mint a helyszíni központi telepítések, azzal, hogy a virtuális merevlemezek, a másik SAP rendszerek megadhatók a különböző "képzeletbeli SAN-eszközöket" vagy az Azure Storage-fiókok.

Az Azure standard szintű Storage nem ajánlott eltérő tárfiókokból tárhelyhez lehetőleg egyetlen virtuális gép van.

A DS vagy a GS sorozatnak Azure virtuális gépek használatakor is lehet csatlakoztatási VHD-k szabványos Azure Storage-fiókok és a prémium szintű Storage-fiókok. Használati esetekben, például virtuális merevlemezek és adatbázis-kezelő adatok biztonsági mentés írása a szabványos tároló biztonsági és a prémium szintű Storage-naplófájlokat tudomást szem előtt tartva ahol ilyen heterogén tárolás sikerült javítható. 

A felhasználói telepítés és tesztelését körülbelül 30-40 az adatbázis az adatfájlok és a naplófájlokat tartalmazó VHD-k helyezhetők egy egyetlen Azure standard szintű tárfiók elfogadható teljesítménnyel alapján. Amint azt korábban említettük, az Azure prémium szintű Tárfiók korlátai várhatóan tárolható adatok kapacitását és nem az iops-érték.

Mint SAN a helyszíni eszközök, a megosztás néhány figyelést igényel ahhoz, hogy végül észleli a szűk keresztmetszetek egy Azure Storage-fiók. Az Azure Figyelőbővítmény SAP és az Azure-portálon foglalt Azure Storage-fiókok, amelyek lehet, olyan optimálisnál IO teljesítmény észleléséhez használható eszközök.  Ha ez a helyzet észleli, javasoljuk, hogy foglalt virtuális gépek áthelyezése egy másik Azure Storage-fiókot. Tekintse meg a [telepítési útmutató] [ deployment-guide] vonatkozó részletes információt az SAP aktiválásához gazdagép figyelési lehetőségek körét.

Ajánlott eljárások Azure standard szintű tárolást és a szabványos Azure Storage-fiókok összefoglalójához egy másik cikkben található itt <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Felügyelt lemezek
Felügyelt lemezek olyan új erőforrást az Azure Resource Manager az Azure Storage-fiókok tárolt VHD-k helyett használható. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva, és ezért a a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állásának javítása. További tudnivalókért olvassa el a [a cikk áttekintése](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP jelenleg csak prémium felügyelt lemezeit támogatja. Olvasási SAP Megjegyzés [1928533] további részleteket.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Adatbázis-kezelő virtuális gépek Azure standard szintű tárolást a prémium szintű Azure Storage áthelyezése telepítése
Egy forgatókönyvek, ahol ügyfélként szeretné helyezni a központilag telepített virtuális gépek Azure standard szintű Storage-ból a prémium szintű Azure Storage előforduló azt. A lemezek Azure Storage-fiókok vannak tárolva, ha ez nem lehetséges az adatokat fizikailag áthelyezése nélkül. A cél elérése érdekében számos módja van:

* Minden virtuális merevlemezek, virtuális Alaplemez, valamint adatokat VHD-k sikerült másolása, ha az egy új Azure prémium szintű Storage-fiókot. Gyakran kiválasztott VHD-k száma Azure standard szintű tárolást nem azt a tényt, hogy szükséges-e a adatmennyiség miatt. Azonban ennyi virtuális merevlemezek van szüksége az IOPS miatt. Most, hogy helyezze át a prémium szintű Azure Storage sikerült módba módszert kevesebb VHD-k a azonos IOPS-teljesítmény eléréséhez. Mivel a, hogy Azure standard szintű tárolót a használt adatok és a névleges lemez mérete nem kell fizetnie, a virtuális merevlemezek száma volt számít költségei tekintetében. Azonban a prémium szintű Azure Storage volna kell fizetnie a névleges lemez méretét. Ezért az ügyfelek többsége próbálnia úgy elhelyezni az Azure virtuális merevlemezek száma a prémium szintű Storage az IOPS-teljesítmény eléréséhez szükséges számon szükséges. Igen a legtöbb ügyfél döntse el, 1:1 egyszerű módja elleni másolása.
* Ha még nincs csatlakoztatva, az SAP-adatbázis egy adatbázis biztonsági mentése is tartalmazó egyetlen virtuális merevlemez csatlakoztatása. A biztonsági mentés óta válassza le az összes virtuális merevlemez többek között a biztonsági másolatot tartalmazó VHD-t, és átmásolja a virtuális Alaplemez és a VHD-t a biztonsági másolat egy prémium szintű Azure Storage-fiók. Volna ezután telepítse a virtuális Gépet alapján a virtuális Alaplemez és a biztonsági másolat a VHD csatlakoztatására. Most létrehozza a üres prémium szintű Storage lemezt a virtuális gép, amely segítségével állítsa vissza az adatbázist. A parancs feltételezi, hogy az adatbázis-kezelő lehetővé teszi az adatokhoz és a naplófájlhoz fájlok elérési utak módosítsa a visszaállítási folyamat részeként.
* Egy másik lehetőség, a korábbi folyamat, ahol a biztonsági mentés VHD átmásolja a prémium szintű Azure Storage és csatolja a újonnan telepített és telepített virtuális gépek elleni egy változata.
* A negyedik lehetőségét, Ha rászoruló az adatbázis az adatfájlok számának módosításához válassza. Ebben az esetben egy SAP homogén rendszer másolása exportálási/importálási használatával hajtaná végre. Helyezze a virtuális Merevlemezt, amely egy Azure prémium szintű Storage-fiók történő másolása és csatlakoztatása egy virtuális Gépet, amely az importálási folyamat futtatásához használt fájlok exportálása azokat. Az ügyfelek ezt a lehetőséget használja, főleg, ha szeretne az adatfájlok számának csökkentése.

Felügyelt lemezek használatakor telepítheti át a prémium szintű Storage szerint:

1. A virtuális gép felszabadítása
2. Ha szükséges, méretezze át a virtuális gép a megfelelő méretűre, amely támogatja a prémium szintű Storage (például a DS vagy a GS)
3. Prémium (SSD) lemez felügyelt fiók típusának módosítása
4. A virtuális gép elindítása

### <a name="deployment-of-vms-for-sap-in-azure"></a>Virtuális gépek telepítése az SAP, az Azure-ban
A Microsoft Azure virtuális gépek és a kapcsolódó lemezek telepítendő több lehetőséget is kínál. Ezáltal fontos különbségek megismeréséhez, mert a virtuális gépek előkészített eltérőek lehetnek, a központi telepítési módszer függ. Általában azt megismerhetők a forgatókönyvek a következő fejezet ismerteti.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Egy virtuális Gépet az Azure piactérről telepítése
Szeretné, hogy a Microsoft vagy harmadik féltől származó lemezkép az Azure piactérről megadott központi telepítése a virtuális Gépet. Miután telepítette a virtuális Gépet az Azure-ban, az azonos irányelvek és eszközök szoftvertelepítésre az SAP a virtuális Gépen belül, mint a helyszíni környezetben hajtsa végre. Kell telepíteni a SAP szoftvert, az Azure virtuális Gépen belül, SAP és a Microsoft ajánlott feltöltése és tárolja a SAP telepítési adathordozó lemezeken, vagy hozzon létre egy Azure virtuális gép fájlkiszolgálóként"", amely tartalmazza az összes szükséges SAP telepítési adathordozót működik.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Virtuális gép és ügyfél-specifikus általános lemezkép központi telepítése
Az operációs rendszer vagy az adatbázis-kezelő verziójával kapcsolatos követelményeket konkrét javítás, mert a megadott lemezképek az Azure piactéren előfordulhat, hogy nem felelnek meg az igényeinek. Ezért szükség lehet a saját "private" az operációs rendszer vagy adatbázis-kezelő Virtuálisgép-lemezkép, amelyet ezután többször telepítve virtuális gép létrehozása. Ilyen "private" lemezkép előkészítése duplikálva lettek-e, hogy az operációs rendszer a helyszíni virtuális Gépre általánosítva van. Tekintse meg a [telepítési útmutató] [ deployment-guide] talál részletes általánosítja egy virtuális Gépet.

Ha már telepítette a helyszíni virtuális gépre (különösen a 2-réteg rendszerek) SAP tartalom, módosíthatja az SAP rendszerbeállítások után az Azure virtuális Gépen keresztül a példány központi telepítését, nevezze át a SAP szoftver kiépítés Manager által támogatott eljárás (SAP Megjegyzés [1619720]). Ellenkező esetben az Azure virtuális gép központi telepítése után később a SAP szoftvert is telepíthet.

Az adatbázis tartalom az SAP-alkalmazás által használt, vagy létrehozhat a tartalom frissen egy SAP telepítése, illetve importálhatja a tartalom Azure egy adatbázis-kezelő adatbázis biztonsági másolatából egy virtuális Merevlemezt vagy közvetlenül biztonsági mentéséhez be az adatbázis-kezelő képességei kihasználva  A Microsoft Azure Storage. Ebben az esetben sikerült is előkészítése az adatbázis-kezelő adatainak és naplókönyvtárainak fájlok helyszíni virtuális merevlemezek és majd importálja azokat lemezként Azure. De az adatbázis-kezelő adatokat, amelyeket az Azure-bA helyszíni első betöltődnek az átvitel akkor működik olyan nagy távolságú elő kell készíteni a helyi VHD lemezek.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Áthelyezését a virtuális gépek helyszíni Azure olyan nem általánosított tartalmazó
Kívánja áthelyezni a megadott számú SAP-rendszer a helyszíni Azure-ba (a növekedési és a shift). Ezt úgy teheti a lemezt, amely tartalmazza az operációs rendszer, az SAP bináris fájljait, és az esetleges adatbázis-kezelő bináris fájljait és a lemezek feltöltése az Azure-bA DBMS adatainak és naplókönyvtárainak fájlokkal. A leváló #2. a fenti forgatókönyv megőrzi az állomásnév, SAP SID és SAP felhasználói fiókok az Azure virtuális gépen, azok a helyszíni környezetben volt konfigurálva. A kép normalizálása ezért nem szükséges. Ebben az esetben az létesítmények közötti forgatókönyvek, ahol az SAP fekvő egy részét futtatja a helyszíni és a részeket Azure többnyire vonatkozik.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépek
Az Azure kínál a következő magas rendelkezésre állású (HA) és a vészhelyreállítás (DR) helyreállítási funkciók, vonatkozó azt használja az SAP- és adatbázis-kezelő központi telepítések különböző összetevői

### <a name="vms-deployed-on-azure-nodes"></a>Az Azure-csomópontokra telepített virtuális gépek
Az Azure platformra nem biztosít például az élő áttelepítési funkciók telepített virtuális gépekhez. Ez azt jelenti, hogy nincs karbantartási szükséges a kiszolgálófürt, amelyre a virtuális gép telepítve van, a virtuális gép leállítása és újraindítása kell-e. Az Azure-ban karbantartási használatával történik így nevű kiszolgálók fürtök frissítési tartományok. Egyszerre csak egy frissítési tartomány áll rendelkezésre. Egy újraindítás közben nincs szolgáltatás megszakítását közben a virtuális gép le van állítva, a karbantartást végeznek és a virtuális gép újraindul. A legtöbb adatbázis-kezelő beszállító azonban adja meg a magas rendelkezésre állású és vész-helyreállítási funkció, amely gyorsan újraindítja az adatbázis-kezelő szolgáltatások egy másik csomópontjára, ha az elsődleges csomópont nem érhető el. Az Azure platformra virtuális gépeket, tárolási és más Azure-szolgáltatásokkal szét a frissítési tartományok győződjön meg arról, hogy tervezett karbantartás vagy infrastruktúra hibák csak befolyásolná a virtuális gépek vagy szolgáltatások egy szűk részhalmaza funkciót kínál.  Gondos tervezéssel a helyszíni infrastruktúra hasonló rendelkezésre állási szintre eléréséhez.

A Microsoft Azure rendelkezésre állási készletek virtuális gépek logikai csoportosítása vagy, amely biztosítja a virtuális gépek és egyéb szolgáltatásokat továbbítja őket a különböző tartalék és tartományok frissítése a fürtön belüli úgy, hogy nem csak lenne egy csomópont meghibásodásakor egy bármikor ( olvasásiidő[ez (Linux)] [ virtual-machines-manage-availability-linux] vagy [a (Windows)] [ virtual-machines-manage-availability-windows] cikkben olvashat).

Konfigurálható célú virtuális gépeken, ahogy az képen látható bevezetésekor kell:

![Az adatbázis-kezelő magas rendelkezésre ÁLLÁSÚ konfiguráció rendelkezésre állási csoport meghatározását][dbms-guide-figure-200]

A DBMS központi telepítések magas rendelkezésre állású konfigurációk (független egyéni adatbázis-kezelő magas rendelkezésre ÁLLÁSÚ funkcióját használja) létrehozása szeretnénk, ha az adatbázis-kezelő virtuális gépek kell:

* A virtuális gépek felvétele a azonos Azure Virtual Network (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* A virtuális gépeket, a magas rendelkezésre ÁLLÁSÚ konfiguráció is ugyanazon az alhálózaton kell lennie. Névfeloldás a különböző alhálózatok között nincs lehetőség az csak felhőalapú környezetekben csak IP-feloldási működik. Pont-pont vagy ExpressRoute-kapcsolat használatával létesítmények közötti telepítésekhez, egy legalább egy alhálózattal rendelkező hálózatot már létrejött. Névfeloldás történik megfelelően a helyszíni AD-házirendek és a hálózati infrastruktúrát. 



#### <a name="ip-addresses"></a>IP-címek
Ajánlott rugalmas úgy a virtuális gépek magas rendelkezésre ÁLLÁSÚ konfigurációk beállítása. IP-címek és kezelje a magas rendelkezésre ÁLLÁSÚ közös belül a magas rendelkezésre ÁLLÁSÚ konfiguráció hagyatkoznia esetén nem megbízható, az Azure-ban statikus IP-címek használhatók. Az Azure-ban van a két "Leállítás" fogalmak:

* Azure-portálon vagy az Azure PowerShell-parancsmag Stop-AzureRmVM leállítást: Ebben az esetben a virtuális gép leállítási lekérdezi és deszerializálni lefoglalt. Az Azure-fiókjával már nem terheli a program a virtuális gép úgy, hogy a csak költségek, amelyiknél a felhasznált tárterület. Ha a hálózati adapter a magánhálózati IP-cím nem statikus, az IP-cím kiadása történik, és nem garantált, hogy a hálózati illesztő lekérdezi a virtuális gép újraindítása után a régi IP-cím. Deaktiválás foglalási hajt végre a leállítási le az Azure portálon keresztül vagy a Stop-AzureRmVM automatikusan meghívásával okoz. Ha nem szeretné felszabadítani a gép Stop-AzureRmVM - StayProvisioned használata 
* Ha leállítja a virtuális Gépet egy operációs rendszer szintet, a virtuális gép lekérdezi állítsa le és ne vonja lefoglalva. Azonban ebben az esetben az Azure-fiókjával továbbra is fizetnie kell a a virtuális gép, annak ellenére, hogy a rendszer leállítása. Ebben az esetben az IP-cím egy leállított virtuális géphez hozzárendelt helye változatlan marad. A belül a virtuális gép leállítása nem automatikusan kényszerített deaktiválás foglalási.

Még a létesítmények közötti forgatókönyv alapértelmezés szerint egy leállítási és deaktiválás foglalási azt jelenti, hogy deaktiválás az IP-címek hozzárendelése a virtuális gépről, akkor is, ha a helyi házirendek segítségével a DHCP-beállítások különböző. 

* A kivétel egy rendel hozzá egy statikus IP-címet a hálózati adaptert, ha leírt [Itt][virtual-networks-reserved-private-ip].
* Ebben az esetben az IP-cím rögzített marad mindaddig, amíg a hálózati illesztő nem törlődik.

> [!IMPORTANT]
> Annak érdekében, hogy a teljes telepítési egyszerű és kezelhető is legyen, a tiszta ajánljuk, beállítása a virtuális gépeket egy adatbázis-kezelő magas rendelkezésre ÁLLÁSÚ vagy vész-Helyreállítási konfiguráció Azure-ban úgy, hogy van működő névfeloldás a különböző virtuális gépek közötti kapcsolattal.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Host Monitoring központi telepítése
Az SAP-alkalmazásokból Azure virtuális gépek hatékony használatát SAP képes lekérni a figyelési adatok az Azure virtuális gépeket futtató fizikai gazdagépek állomás van szükség. Egy adott SAP a gazdagép ügynöke javítási szintje szükség, amely lehetővé teszi, hogy ezt a lehetőséget az SAPOSCOL és SAP a gazdagép ügynöke. A pontos javítási szintje SAP Megjegyzés ismertetett [1409604].

Az, hogy a gazdagép adatok SAPOSCOL és SAP a gazdagép ügynöke összetevők telepítési és összetevők életciklusának kezelését kapcsolatban további tájékoztatást a [telepítési útmutatója][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Microsoft SQL Server rögzítésen
### <a name="sql-server-iaas"></a>SQL Server IaaS
A Microsoft Azure-től kezdődően egyszerűen áttelepítheti a meglévő SQL Server-alkalmazások Azure virtuális gépek Windows Server platformra épül. SQL Server virtuális gépen lehetővé teszi, hogy a központi telepítési, kezelési és vállalati körének alkalmazások karbantartása tulajdonosi költségek csökkentéséhez könnyen áttelepítéssel ezeket az alkalmazásokat a Microsoft Azure használatával. Az SQL Server egy Azure virtuális gépen, rendszergazdák és fejlesztők számára továbbra is használhatja a azonos fejlesztési és a rendelkezésre álló helyszíni felügyeleti eszközöket. 

> [!IMPORTANT]
> Jelenleg nem hanem Microsoft Azure SQL Database, amely egy platformon, mint egy szolgáltatás-ajánlat a Microsoft Azure platform. A SQL Server terméket futtató, mert a helyszíni telepítéshez az Azure virtuális gépek, az infrastruktúra, egy Azure-szolgáltatás képességét kihasználva ismeri a ismertető dokumentum tárgya. Adatbázis-képességeket és funkciók között ezekről az ajánlatokról két különböző, és kell nem keverhetők egymás mellett. Lásd még: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Javasoljuk, hogy tekintse át [ez] [ virtual-machines-sql-server-infrastructure-services] a folytatás előtt.

Az alábbi szakaszok a fenti hivatkozás a dokumentációs részeinek darabok összesítve, és említett. SAP körül rögzítésen is szerepelnek, és néhány olyan fogalmat, részletesebben ismertetjük. Azonban erősen ajánlott a dokumentáció első felett az SQL Server-specifikus dokumentáció elolvasása előtt keresztül működnek.

Van néhány SQL Server IaaS konkrét információk tudnia kell, mielőtt továbblépne:

* **Virtuális gép SLA**: van szolgáltatásiszint-szerződésben garantált virtuális gépeken futó Azure, amely itt található: <https://azure.microsoft.com/support/legal/sla/>  
* **SQL-verzió támogatja**: számára, SAP, támogatott SQL Server 2008 R2 és újabb rendszer a Microsoft Azure virtuális gépen. Korábbi verziók nem támogatottak. Tekintse át az általános [támogatási nyilatkozattal](https://support.microsoft.com/kb/956893) további részleteket. Vegye figyelembe, hogy általában SQL Server 2008 Microsoft által támogatott is. Azonban az SAP, SQL Server 2008 R2 jelent, amely jelentős működése miatt az SQL Server 2008 R2 a SAP minimális verziójának. Ne feledje, hogy az SQL Server 2012 és a 2014 lett bővítve be (például biztonsági mentése közvetlenül az Azure Storage szemben) IaaS-forgatókönyveknél szorosabb integrációt. Ezért azt korlátozása a dokumentum az SQL Server 2012 és a legújabb javítási szintje a 2014 Azure.
* **Az SQL által nyújtott szolgáltatások**: legtöbb SQL Server szolgáltatást támogat a Microsoft Azure virtuális gépeken néhány kivétellel. **SQL Server feladatátvételi fürtszolgáltatási megosztott lemezek használata nem támogatott**.  Elosztott technológiák, például adatbázis-tükrözés, AlwaysOn rendelkezésre állási csoportok, replikáció, a Naplóküldés és a Service Broker támogatottak egyetlen Azure-régión belül. Az SQL Server AlwaysOn is támogatott különböző Azure-régiók közötti itt leírtak szerint: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Tekintse át a [támogatási nyilatkozattal](https://support.microsoft.com/kb/956893) további részleteket. Az AlwaysOn-konfigurációjának telepítéséről példa a [ez] [ virtual-machines-workload-template-sql-alwayson] cikk. Emellett tekintse meg az ajánlott eljárásokat, dokumentált [Itt][virtual-machines-sql-server-infrastructure-services] 
* **SQL-teljesítmény**: vagyunk abban, hogy a Microsoft Azure futtatott virtuális gépek jól szemben a más nyilvános felhő virtualizálási ajánlatokat, de az egyes eredmények végrehajtani változhat. Tekintse meg [ez] [ virtual-machines-sql-server-performance-best-practices] cikk.
* **Az Azure piactérről lemezképekkel**: A leggyorsabban egy új Microsoft Azure virtuális gép üzembe helyezése, hogy a lemezkép használata az Azure piactérről. Az Azure piactéren, képek, amelyekben megtalálható az SQL Server vannak. A képek, ahol az SQL Server már telepítve van az SAP NetWeaver alkalmazások azonnal nem használható. A hiba oka az alapértelmezett SQL Server-rendezés ezeket a képeket és nem az SAP NetWeaver rendszerek által igényelt rendezés belül van telepítve. Ahhoz, hogy az ilyen képek, ellenőrizze a fejezetben leírt lépéseket [SQL kiszolgálói lemezkép használatával a Microsoft Azure piactérről kívül][dbms-guide-5.6]. 
* Tekintse meg [díjszabás](https://azure.microsoft.com/pricing/) további információt. A [SQL Server 2012 licencelése útmutató](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) és [SQL Server 2014 licencelési útmutató](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) egy fontos erőforrás is vannak.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>SQL Server-beállítási útmutatója SAP-kapcsolódó SQL Server-telepítések az Azure virtuális gépeken
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>VM-/ VHD-szerkezet SAP-kapcsolódó SQL Server-telepítések kapcsolatos javaslatok
Általános ismertetését megfelelően SQL Server végrehajtható fájlok található, vagy el kell telepíteni a virtuális gép operációsrendszer-lemez rendszermeghajtóján (C: meghajtó\).  Általában a legtöbb az SQL Server rendszer-adatbázisokat nem használhatók magas szinten SAP NetWeaver munkaterhelés szerint. Ezért a rendszeradatbázisokban (master, msdb, és a modell) SQL-kiszolgáló, valamint a C:\ meghajtón marad. Kivétel tempdb, amely néhány SAP ERP-szolgáltatásokat és minden BW munkaterhelések esetén előfordulhat, hogy nagyobb adatmennyiség vagy az eredeti virtuális gép nem sorolhatók i/o műveletek kötet lehet. Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani:

* Helyezze át az elsődleges tempdb adatfájllal az SAP-adatbázist az elsődleges adatforrás (oka) t azonos logikai meghajtón.
* Bármely további tempdb adatok fájlok hozzáadása az SAP felhasználói adatbázis adatfájlt tartalmazó az más logikai meghajtókhoz.
* A tempdb logfile hozzáadása a logikai meghajtó, amely tartalmazza a felhasználói adatbázis naplófájlját.
* **Kizárólag a helyi SSD meghajtókat használó Virtuálisgép-típusokon** a számítási csomópont tempdb adatainak és naplókönyvtárainak fájlok előfordulhat, hogy kell helyezni a D:\ meghajtóra. Azonban előfordulhat, hogy lehet célszerű több tempdb adatfájlok használatát. Ne feledje D:\ meghajtót kötetek eltérőek a virtuális gép típusa alapján.

Ezek a konfigurációk felhasználásához nagyobb területet a rendszermeghajtó rendelkezésére áll, mint a tempdb engedélyezése. Ahhoz, hogy a megfelelő tempdb mérete határozza meg, a tempdb méretek meglévő operációs rendszert, a helyi ellenőrizheti egy. Ilyen konfiguráció emellett lehetővé tenné IOPS számok elleni tempdb, amely a rendszermeghajtó nem adható meg. Ebben az esetben a helyi rendszert futtató rendszerek figyeléséhez i/o-munkaterhelés tempdb ellen, hogy a várt a a tempdb adatbázison IOPS számok tudnak származtatni használható.

Virtuálisgép-konfiguráció, egy SAP-adatbázist az SQL Server fut, amely, és ha a tempdb adatok és a tempdb logfile kerülnek a D:\ meghajtóra alábbihoz hasonlóan fog kinézni:

![Az Azure infrastruktúra-szolgáltatási virtuális gép SAP referencia-konfiguráció][dbms-guide-figure-300]

Vegye figyelembe, hogy rendelkezik-e a D:\ meghajtóra függ a virtuális gép mérete eltér. A tempdb vonatkozó méretbeli követelményt függ, előfordulhat, hogy mindenképpen pár tempdb adatainak és naplókönyvtárainak és fájlok az SAP adatbázis adatainak és naplókönyvtárainak azokban az esetekben, ahol D:\ meghajtóra túl kicsi.

#### <a name="formatting-the-disks"></a>A lemezek formázása
Az SQL Server az NTFS blokkolhatja az SQL Server-adatokat tartalmazó lemez mérete, illetve naplófájlok 64K kell lennie. Nincs szükség a D:\ meghajtóra formázásához. A meghajtó rendelkezik előre formázott.

Győződjön meg arról, hogy a visszaállítási vagy -adatbázisok létrehozását nem inicializálja az adatfájlok által a fájlok tartalmának nullázást, hogy egy győződjön meg arról, hogy a felhasználói környezet, az SQL Server-szolgáltatás fut a bizonyos engedéllyel rendelkezik. Általában a Windows felügyeleti csoport rendelkezik ezekkel a jogosultságokkal. Ha az SQL Server szolgáltatás Windows rendszergazdai jogokkal nem rendelkező felhasználó környezetében fut, szeretné-e megadni, hogy a felhasználó a jogot **kötet karbantartási feladatokat végez**.  A részletek a Microsoft Tudásbázis: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Adatbázis-tömörítés hatása
Konfigurációk, ahol a i/o-sávszélesség korlátozó tényezővé válhat minden mértékcsoport, ami csökkenti az IOPS segíthetnek az alkalmazások és szolgáltatások egyik futtathatók az IaaS-forgatókönyveknél, például Azure Nyújtás. Ezért lépést még nem tette meg, ha SQL Server PAGE-tömörítés alkalmazása ajánlott SAP és a Microsoft Azure-bA egy meglévő SAP-adatbázist feltöltés előtt.

A javaslat adatbázis tömörítés végrehajtásához Azure-bA feltöltés előtt kívül két okból kap:

* A feltöltendő adatok mérete kisebb.
* A tömörítési végrehajtásának időtartama rövidebb, feltéve, hogy az egyik használható erősebb hardver, további processzorok vagy magasabb i/o-sávszélesség legfeljebb I/O várakozási ideje a helyszíni.
* Kisebb adatbázis mérete kisebb költségek a lemezfoglaláshoz vezethet

Adatbázis típusú tömörítést, valamint egy Azure virtuális gépek azonban nem helyi. A meglévő SAP SQL Server adatbázis tömörítése módjáról további részleteket tekintse meg itt: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 - közvetlenül az Azure Blob Storage tárolóban tárolni adatbázisfájlok
Az SQL Server 2014 közvetlenül az Azure Blob-tároló nélkül a virtuális merevlemez felhasználókat ezekbe a csoportokba "burkolót" adatbázis fájljainak tárolására szolgáló lehetősége nyílik meg. Különösen a szabványos Azure Storage vagy kisebb Virtuálisgép-típusokon lehetővé teszi forgatókönyvek, ahol de kapcsolatos iops-érték, amely akkor kikényszeríti a néhány kisebb Virtuálisgép-típusokon a csatlakoztatott lemezek korlátozott számú határain. Ez a módszer a felhasználói adatbázisokat azonban nem az SQL Server rendszer-adatbázisokat. Azt is működik, az SQL Server adatainak és naplókönyvtárainak fájlokat. Ha meg szeretne telepíteni egy SAP SQL Server-adatbázis helyett a "alkalmazásburkoló" így azt a virtuális merevlemezeket, tartsa a következő szem előtt:

* A Tárfiók kell lennie egy Azure-régióban, mint az egyik, amellyel a virtuális gép az SQL Server telepítéséhez futtatja a használt.
* Különböző Azure Storage-fiókok keresztül korábbi VHD-k terjesztési felsorolt feltételek vonatkoznak, a központi telepítések, valamint a metódus. Azt jelenti, hogy az i/o műveletek száma a korlátozások az Azure Storage-fiókot.

[comment]: <> (MSSedusch TODO, de ez a beállítás használja hálózati és tárolási sávszélesség-nem, akkor nem?)

A központi telepítési típus adatait az itt felsorolt: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Ahhoz, hogy az SQL Server-adatfájlok közvetlenül a prémium szintű Azure Storage tárolja, kell rendelkeznie a minimális SQL Server 2014 javítás kiadás, amely itt dokumentált: <https://support.microsoft.com/kb/3063054>. Azure standard szintű tárolást SQL Server-adatfájlok tárolására működik az SQL Server 2014 a hivatalosan kiadott verzió. A nagyon azonos javítások azonban másik adatsorozatot tartalmaz, amelyek az SQL Server-adatfájlok és a biztonsági mentést az Azure Blob Storage közvetlen használatát megbízhatóbb tartalmaznak. Ezért azt javasoljuk, ezek a javítások általában.

### <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 pufferkészlet-kiterjesztés
Az SQL Server 2014 bevezetett új szolgáltatása, a pufferkészlet-kiterjesztés nevezzük. Ez a funkció a pufferkészlet az SQL Server, amelyet a helyi SSD-k egy kiszolgáló vagy a virtuális gép által támogatott második szintű gyorsítótárával memória terjeszti ki. Ez lehetővé teszi a nagyobb működő adatkészletet megtartása "a memóriában". Azure standard szintű tárolást elérése képest a helyi SSD-k egy Azure virtuális Gépen tárolt pufferkészlet kiterjesztését be-e számos tényező gyorsabb.  Ezért a Virtuálisgép-típusokon, amelyek kiváló IOPS és átviteli sebesség a helyi D:\ meghajtóra, ami lehet egy Azure Storage IOPS terhelés csökkentésére, és jelentősen javíthatja a lekérdezések válaszidejét nagyon ésszerű módszer. Ez vonatkozik, különösen akkor, ha nem a prémium szintű tároló. Prémium szintű Storage és a prémium szintű Azure olvasási gyorsítótár, a számítási csomóponton használatát az adatfájlok, ajánlott nincs lényeges különbség várható. Ennek oka, hogy mindkét gyorsítótárak (SQL Server a pufferkészlet-kiterjesztés és prémium szintű Storage olvasási gyorsítótár) használ a számítási csomópontok helyi lemezek.
Ez a funkció kapcsolatos további részletekért ellenőrizze a jelen dokumentáció: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server biztonsági mentés/helyreállítás szempontjai
Az Azure SQL-kiszolgáló telepítésekor meg kell vizsgálni a biztonsági mentési módszer. Akkor is, ha a rendszer nem hatékony rendszer, az SAP-adatbázist az SQL-kiszolgáló által üzemeltetett másolatot kell rendszeres időközönként. Azure Storage három képek tartja, mert a biztonsági mentés már kevésbé fontos való tárolási crash tekintetben. A prioritás a megfelelő biztonsági mentési és helyreállítási tervek karbantartása oka, hogy több is kompenzálják a logikai kézi/hibákat idő helyreállítási funkciók pont megadásával. Vagy használjon biztonsági mentések visszaállítani az adatbázist egy bizonyos mértékig időben vagy a biztonsági másolatokat az Azure-ban a meglévő adatbázis másolása egy másik rendszer rendezi a célja. Például akkor sikerült átvitele 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása az adott rendszer biztonsági mentésének visszaállításával.

SQL Server mentésére az Azure Storage három különböző módja van:

1. SQL Server 2012 CU4 és magasabb lehet natív módon adatbázisok biztonsági mentése egy URL-címre. Ez a blogjában részletes [új funkció az SQL Server 2014 - rész 5 – biztonsági mentés/visszaállítás fejlesztések](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Című [SQL Server 2012 SP1 CU4 vagy újabb][dbms-guide-5.5.1].
2. SQL Server-verziókban SQL 2012 CU4 előtt egy átirányítási funkció segítségével virtuális merevlemez való biztonsági mentés és alapvetően helyezze át az írási adatfolyam felé az Azure tárolási helye, amely konfigurálva van. Című [SQL Server 2012 SP1 CU3 és a korábbi kiadásokban][dbms-guide-5.5.2].
3. A végső metódus lemez parancs, a lemez eszköz hagyományos SQL Server biztonsági mentés. Ez a helyszíni telepítési minta azonos, és részletei a jelen dokumentum nem tárgyalja.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 és újabb verziók
Ez a funkció lehetővé teszi számára közvetlenül Azure BLOB Storage tárolóban történő biztonsági mentés. Ezzel a módszerrel nem kell biztonsági mentése el másik lemezre, amely volna felhasználását, lemez és IOPS-kapacitás. A képet alapjában ezt:

 ![SQL Server 2012 biztonsági mentéssel Microsoft Azure Storage-blobba][dbms-guide-figure-400]

Ebben az esetben előnye, hogy egy nem kell egy SQL Server biztonsági másolatokat tárolni kívánt lemezeket. Úgy, hogy kevesebb lefoglalt lemezek és a teljes sávszélesség lemez iops-érték használható az adatok és a naplófájlokat. Ne feledje, hogy a biztonsági mentés maximális mérete legfeljebb 1 TB-os szakaszában leírtak szerint **korlátozások** ebben a cikkben: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Ha a biztonsági másolat mérete, annak ellenére, hogy az SQL Server biztonsági másolat tömörítésével lehetett meghaladná a 1 TB-nál, a funkciók fejezetben ismertetett [SQL Server 2012 SP1 CU3 és a korábbi kiadásokban] [ dbms-guide-5.5.2] ebben a dokumentumban kell lennie használja.

[Kapcsolódó dokumentáció](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) Azure Blob-tárolóban biztonsági másolatból adatbázisok visszaállítása leíró célszerű nem közvetlenül az Azure BLOB-tároló vissza, ha a biztonsági mentés > 25 GB. Ebben a cikkben a javaslat a teljesítménnyel kapcsolatos szempontok és működési korlátozások miatt nem alapul. Ezért különböző feltételeket alkalmazhatnak eseti alapon.

Ilyen típusú biztonsági mentést beállítása és kihasználhatók dokumentációjában található [ez](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) oktatóanyag

A lépések sorrendjét például olvasható [Itt](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Biztonsági mentések automatizálása, van legmagasabb jelentőségű győződjön meg arról, hogy a Blobok minden egyes biztonsági másolathoz vannak más a neve. Máskülönben felül, és a restore-lánc megszakad.

Annak érdekében, hogy többek között a biztonsági mentések három különböző típusú összekeveri tanácsos különböző tárolók alatt a biztonsági mentésekhez használt tárfiók létrehozásához. A tárolók virtuális gép csak, vagy pedig virtuális gép és a biztonsági mentés típusa lehet. A séma nézhet:

 ![SQL Server 2012 backup Microsoft Azure Storage-blobba - külön Tárfiókot a különböző tárolók használatával][dbms-guide-figure-500]

A fenti példában a biztonsági mentések volna nem futtatható, irányítson át hol vannak telepítve a virtuális gépek ugyanazt a tárfiókot. Kifejezetten a biztonsági mentés új tárfiók lenne. A storage-fiókok belül nem lenne a biztonsági mentési és a virtuális gép nevét típusú mátrix létre különböző tárolók. Ilyen Szegmentálás révén egyszerűbben felügyelheti a különböző virtuális gépek biztonsági mentését.

A Blobok egy közvetlenül ír a biztonsági mentések, ne adja hozzá a számnak az adatok lemezt a virtuális gépek. Ezért adatlemez csatlakoztatva az adott virtuális gép metódust az adatok legfeljebb egy sikerült maximalizálása és a tranzakciós naplófájlt, és továbbra is végre hajtani egy biztonsági mentési tárolót ellen. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 és rendszerek régebbi kiadásaiban
Az első lépés, el kell végeznie ahhoz, hogy a biztonsági mentés közvetlenül szemben Azure Storage elérése lenne, töltse le az MSI-fájl, amely csatolva van [ez](https://www.microsoft.com/download/details.aspx?id=40740) KBA cikk.

Letöltés a x64 telepítési fájl- és a dokumentációt. A fájl neve programot telepíti: **Microsoft SQL Server biztonsági másolat, a Microsoft Azure eszköz**. Alaposan olvassa el a termék dokumentációját.  Az eszköz alapvetően az alábbi módon működik:

* Az SQL Server oldalán, a lemez helye az SQL Server biztonsági másolat van megadva (nem használják a D:\ meghajtóra).
* Az eszköz lehetővé teszi a szabályok, amelyek segítségével különböző Azure Storage tárolók a biztonsági mentések különböző típusairól közvetlen adható meg.
* Ha a szabályok helyen, az eszköz átirányítja a biztonsági mentés írható adatfolyamot egy VHD-k/lemezt az Azure Storage helyre, amely korábban meghatározott.
* Az eszköz elhagyja néhány KB méretű kis helyettes fájl a lemezen VHD /, hogy az SQL Server biztonsági másolat. **Ezt a fájlt, mert szükség van az Azure Storage újra szeretné visszaállítani a tárolóhelyen kell hagyni.**
  * Ha elvesztette a helyettes fájlt (például a veszteség a tárolóeszköz, amely a helyettes fájl található), és a biztonsági másolatot a Microsoft Azure Storage-fiók lehetőséget választotta, a Microsoft Azure Storage helyettes fájl is helyreállítása letöltéssel a tároló el helyezni. Helyezze a helyettes fájlt egy mappába a helyi számítógépen, ahol az eszköz észlelésére, és töltse fel a titkosítási jelszót a tárolóhoz, ha a titkosítás volt használva az eredeti szabályt van konfigurálva. 

Ez azt jelenti, hogy a séma, SQL Server újabb kiadásaiban a fent leírt módon helyezhetik helyen, valamint az SQL Server-kiadásokban, amely nem engedélyezi a közvetlen cím az Azure tárolási helyét.

Ez a metódus nem használható az SQL Server újabb kiadásokban, amely támogatja a biztonsági mentését natív Azure Storage ellen. Kivételek, ahol az Azure a natív biztonsági mentéssel korlátozásai blokkolják a natív biztonsági mentés végrehajtása az Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Egyéb lehetőségek az SQL Server-adatbázisok biztonsági mentése
Egyéb lehetőségek adatbázisok biztonsági mentése egy virtuális Gépet, a biztonsági másolatok tárolására használt további adatlemezt csatolni. Ebben az esetben meg kell győződjön meg arról, hogy a lemezek nem futnak teljes. Ha ez a helyzet, akkor kellene a lemezt leválasztani speak úgy a "archiválhatja", és cserélje le új üres lemez. Elérési út leáll, ha meg szeretné tartani ezeket VHD-k a külön Azure Storage-fiókok közül., amely a VHD-k, az adatbázis fájlok.

A második lehetőség, hogy a nagy virtuális gépek, amelyeken sok lemezt csatolni, például egy D14 32VHDs a használja. Használni a tárolóhelyek rugalmas környezet létrehozásához, ahol sikerült build megosztásokat, majd biztonsági mentési célként a használt az adatbázis-kezelő különböző kiszolgálókon.

Néhány ajánlott eljárás a kapott a következő dokumentált [Itt](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) is. 

#### <a name="performance-considerations-for-backupsrestores"></a>A biztonsági mentés/visszaállítás teljesítménnyel kapcsolatos szempontok
Operációs rendszer nélküli telepítések, mint a biztonsági mentés/visszaállítás teljesítménye minden hány kötetek párhuzamosan olvasható, és mi az átviteli sebessége a köteteket lehet függ. Emellett a biztonsági másolatok tömörítését által használt CPU-felhasználás is fontos szerepet játszik a virtuális gépeken legfeljebb nyolc CPU szállal. Ezért akkor feltételezheti:

* A kevesebb a lemezek száma az adatok tárolására használt fájlok, a minél kisebb a teljes teljesítményt való olvasáskor.
* Minél kisebb a virtuális gépen, a súlyosabb szálait a CPU-szám a hatását a biztonsági másolatok tömörítését.
* A kevesebb célok (BLOB, virtuális merevlemezek vagy lemezek) írni a biztonsági mentés, a kisebb a teljesítményt.
* Minél kisebb a virtuális gép mérete, annál kisebb tárolási átviteli kvótát írása és olvasása Azure Storage-ból. Független, hogy a biztonsági mentés közvetlenül tárolódnak az Azure Blob, vagy hogy ezeket tárolja az Azure BLOB újra tárolt VHD-k.

Egy Microsoft Azure Storage-BLOBBA és a biztonsági mentési cél a korábbi kiadásokban használatakor áll minden egyes konkrét biztonsági mentés csak egy URL-cím cél kijelölő korlátozni.

De a régebbi kiadásokban a "Microsoft SQL Server biztonsági másolat a Microsoft Azure eszköz" használva határozhat meg egynél több cél. Az egynél több cél a biztonsági mentés méretezhető, és az átviteli sebessége a biztonsági mentés nagyobb. Ez azt eredményezi majd, valamint az Azure Storage-fiókban található több fájlt. A tesztelés során a több célhoz rendelt fájl használatával egyértelműen érhet el az átviteli sebességben, ami az SQL Server 2012 SP1 CU4 meg végrehajtani a biztonsági mentési kiterjesztésű el lehet érni. Akkor is nem blokkolja a natív biztonsági mentéssel hasonlóan 1 TB-os korlát az Azure.

Tartsa azonban szem előtt, az átviteli sebesség is függ, az Azure Storage-fiókot használ a biztonsági mentési helyét. Keresse meg a tárfiók más régióban, mint a virtuális gépek futnak képet lehet. Például meg szeretné futtatni a Virtuálisgép-konfiguráció Nyugat-Európa, de helyezni a Tárfiók, amelyekkel biztonsági mentési Észak-Európában, ellen. Hogy biztosan hatással van a biztonsági mentési átviteli, és nem valószínű, hogy egy 150MB/s átviteli generálnak, ahogy azt úgy tűnik, hogy lehetséges azokban az esetekben, ahol a céloldali tárfiók és a virtuális gépek futnak regionális ugyanabban az adatközpontban.

#### <a name="managing-backup-blobs"></a>Biztonsági mentési bináris objektumok kezelése
Nincs a követelmény, hogy a saját biztonsági mentéseit. Az elvárás, hogy hány blobok gyakori tranzakciónapló biztonsági mentései végrehajtásával jöttek létre, mert ezek blobok felügyeleti könnyen is működési az Azure-portálon. Ezért fontos recommendable, hogy kihasználja az Azure Tártallózó. Van több jó néhányat a meglévők közül érhető el, így az Azure storage-fiókok kezelése

* Microsoft Visual Studio telepített Azure SDK-val (<https://azure.microsoft.com/downloads/>)
* A Microsoft Azure Tártallózó (<https://azure.microsoft.com/downloads/>)
* Harmadik féltől származó eszközök

Bővebb információt a biztonsági másolat és az SAP Azure, tekintse meg [az SAP biztonsági mentési útmutató](sap-hana-backup-guide.md) további információt.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>SQL-kiszolgálói lemezkép használatával kívül a Microsoft Azure piactéren
Microsoft virtuális gépeinek az Azure piactéren, amely már tartalmazza az SQL Server verzióinak kínál. Az SAP-ügyfelek, akik licencek szükségesek az SQL Server és a Windows a problémát valószínűleg alapvetően fedik le a szükséges licencek által telepített SQL Server virtuális gépeinek forgó lehetőséget. Ahhoz, hogy az ilyen képek SAP, az alábbiakat kell tenni:

* Az SQL Server nem-próbaverziói szerezzen be újabb költségei, mint az Azure piactérről telepítve "Csak Windows" virtuális gép. Ezek a cikkek árak összehasonlításához lásd: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> és <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Csak használhatja az SQL Server-verziókban, SAP, például az SQL Server 2012 által támogatott.
* A rendezés az SQL Server-példányhoz, amelyen telepítve van-e a virtuális gépek érhető el az Azure piactéren nincs az SAP NetWeaver kell futtatni az SQL Server-példány rendezése. A rendezés módosíthatja, ha az a következő című szakasz utasításait.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>A Microsoft Windows vagy SQL Server virtuális gép az SQL Server-rendezés módosítása
Miután az SQL Server-rendszerképeit az Azure piactéren nincs beállítva a rendezés használatára, amely azonban szükséges a SAP NetWeaver alkalmazások, azonnal a telepítés után módosítani kell. Az SQL Server 2012 ehhez a következő lépések, amint a virtuális gép van telepítve, és a rendszergazda tud jelentkezni a központilag telepített virtuális gép:

* Nyisson meg egy Windows parancsablakot rendszergazdaként.
* Módosítsa a könyvtárat a C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* A parancs végrehajtásához: Setup.exe/test/művelet REBUILDDATABASE InstanceName = MSSQLSERVER /SQLSYSADMINACCOUNTS = =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> a fiók, amely van definiálva a rendszergazdai fiók használatával a katalógusban az első alkalommal a virtuális gép telepítésekor.

A folyamat csak kell eltarthat néhány percig. Ahhoz, hogy ellenőrizze, hogy a lépés befejeződött megfelelő eredménnyel, hajtsa végre a következő lépéseket:

* Nyissa meg az SQL Server Management Studiót.
* Nyissa meg a lekérdezési ablakban.
* A parancs sp_helpsort végrehajtani az SQL Server master adatbázisban.

A kívánt eredményt hasonlóan kell kinéznie:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Ha nem ez az eredmény, állítsa le a SAP telepítése, és vizsgálja meg, miért a setup parancs volt a várt módon működik. SAP NetWeaver alkalmazások, a másik SQL Server megadására, mint a fent említett SQL Server-példány telepítése **nem** támogatott.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server magas rendelkezésre állású az SAP, az Azure-ban
Ahogy azt korábban említettük, a dokumentum korábbi, nincs létrehozása a megosztott tárolóról, ami szükséges, hogy a funkció használata a a legrégebbi SQL Server magas rendelkezésre állású lehetőség. Ez a funkció a a Windows Server feladatátvételi fürt (WSFC) egy megosztott lemez használata a felhasználói adatbázisok (és végül az tempdb) telepíti legalább két SQL Server-példányokat. Esetében hosszú ideig a szabványos magas rendelkezésre állású módszert, amelyet SAP is támogat. Azure nem támogatja a megosztott tárolót, mert az SQL Server magas rendelkezésre állású konfiguráció a megosztott lemez fürtözött konfigurációban nem kell megvalósítani. Számos más magas rendelkezésre állású módszer azonban továbbra is lehetségesek, és a következő szakaszok ismertetik.

#### <a name="sql-server-log-shipping"></a>SQL Server-Naplóküldés
SQL Server-Naplóküldés a magas rendelkezésre ÁLLÁS módszerek egyikét. Ha a virtuális gépeket, a magas rendelkezésre ÁLLÁSÚ konfigurációban részt vevő névfeloldás működik, nincs probléma, és a telepítő az Azure-ban nem különböznek a telepítés, amely a helyszínen történik. Nem ajánlott, csak az IP-feloldási támaszkodni. A Naplóküldés és a Naplóküldés körül ezeket az alapelveket beállítása jelenítik ebben a dokumentációban ellenőrizze:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

A magas rendelkezésre állás elérése érdekében egy kell telepíteni a virtuális gépeket, ilyen a Naplóküldés konfiguráció belül az azonos Azure rendelkezésre állási csoporthoz tartozó.

#### <a name="database-mirroring"></a>Az adatbázis-tükrözés
SAP által támogatott adatbázis-tükrözés (lásd az SAP megjegyzést [965908]) meghatározása az SAP-kapcsolati karakterláncot a feladatátvételi partner támaszkodik. A létesítmények közötti esetben feltételezzük, hogy, hogy a két virtuális gépek ugyanabban a tartományban, és, hogy a felhasználói környezet a két SQL Server-példányokat, valamint egy tartományi felhasználói futnak, és a jogosultsága az érintett két SQL Server-példányokat. Ezért a telepítő az adatbázis-tükrözés az Azure-ban nem eltérő egy tipikus helyszíni telepítés vagy a konfigurációs.

Től csak felhőalapú telepítések esetén a legegyszerűbb módja, hogy egy másik tartomány beállítása az Azure-e az adatbázis-kezelő virtuális gépek (és ideális esetben dedikált SAP virtuális gépek) egy tartományon belül.

Ha egy tartomány nem lehetséges, egyik is tanúsítványokat is használhat az adatbázistükrözés végpontok itt leírtak szerint: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Az oktatóanyagnak, amellyel beállítása az Azure-adatbázis-tükrözés itt található: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQL Server Always On
Always On támogatja a helyszíni SAP (lásd az SAP megjegyzést [1772688]), használható együtt az Azure-ban SAP esetén támogatott. A tényt, hogy Ön nem hozhat létre a megosztott lemezt az Azure-ban nem jelenti azt, hogy egy nem hozható létre különböző virtuális gépek közötti mindig a Windows Server feladatátvételi fürt (WSFC) konfigurálása során. Csak azt jelenti, hogy nem rendelkezik arra, hogy egy megosztott lemez használata a kvórum a fürt konfigurálása. Ezért egy WSFC mindig a konfiguráció az Azure-ban, és jelölje be a kvórum típus, amely megosztott lemezt használja. Az Azure környezetbe virtuális gépek vannak telepítve kell hárítsa el a virtuális gépek neve és a virtuális gépek kell ugyanabban a tartományban. Ez igaz csak Azure-vagy létesítmények közötti telepítések esetén. Néhány szempontot körül központi telepítése az SQL Server rendelkezésre állási csoport figyelőjét (nem keverendő össze az Azure rendelkezésre állási csoport) mert Azure ezen a ponton a időben nem teszik lehetővé, mert lehetséges helyi AD/DNS-objektum létrehozásához. Ezért néhány különböző telepítési lépések szükségesek, amelyek az adott Azure viselkedését.

Azt is számításba kell egy rendelkezésre állási csoport figyelőjének segítségével a következők:

* Egy rendelkezésre állási csoport figyelőjének használata csak akkor lehetséges a Windows Server 2012 vagy újabb vendég operációs rendszer a virtuális gép. A Windows Server 2012 kell győződjön meg arról, hogy ez a javítókészlet vonatkozik: <https://support.microsoft.com/kb/2854082> 
* A Windows Server 2008 R2, a javítás nem létezik, és mindig bekapcsolva kell a kapcsolati karakterlánc a feladatátvételi partner megadásával az adatbázis-tükrözés megegyező módon használható (a SAP default.pfl paraméter adatbázisok/mss/kiszolgáló - modullal végzett lásd az SAP megjegyzést [965908]).
* Ha egy rendelkezésre állási csoport figyelőjével, az adatbázis virtuális gépek csatlakoznia kell egy dedikált terheléselosztóhoz. Nevek feloldása csak felhőalapú telepítések esetén, vagy igényel az SAP rendszer (alkalmazás-kiszolgálókat, adatbázis-kezelő kiszolgáló és (A) SCS kiszolgáló) virtuális gépeinek az azonos virtuális hálózatban, vagy egy SAP alkalmazás rétegből lenne szükség a etc\host fájl sorrendben a karbantartás a beolvasandó feloldani az SQL Server virtuális gépek virtuális gép nevét. Elkerülése érdekében, hogy Azure van-e az esetekben, amikor mindkét VM mellékesen leállítási új IP-címek hozzárendelése, egy kell statikus IP-címek kiosztása a hálózati adapterek, virtuális gépek (meghatározása a statikus IP-címnek ismertetettmindigakonfigurációban[ez] [ virtual-networks-reserved-private-ip] cikk)

[comment]: <> (Régi blogok)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Nincsenek speciális lépéseit összeállítása a WSFC fürtkonfiguráció, amikor a fürt kell egy különös IP-cím hozzárendelése, mert az aktuális funkciójú Azure rendelne a fürt neve azonos IP-címét a csomópont a fürt létrehozását a. Ez azt jelenti, hogy a fürt egy másik IP-cím hozzárendelése egy manuális lépés kell végrehajtani.
* A rendelkezésre állási csoport figyelőjének hozható létre az Azure-ban a TCP/IP-végpontok, rendelt a virtuális gép fut az elsődleges és másodlagos replikák rendelkezésre állási csoport lesz.
* Előfordulhat, hogy ezeket a végpontokat a hozzáférés-vezérlési listák biztonságossá kell.

[comment]: <> (Teendők régi blog)
[comment]: <> (A részletes lépéseket és az AlwaysOn-konfigurációjának telepítése Azure szükségleti cikkek vannak legjobb akkor tapasztaltak, amikor útmutató alapján elérhető oktatóanyag [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Előre konfigurált AlwaysOn beállítása az Azure katalógusában keresztül <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Legjobb ismertetett [this][virtual-machines-windows-classic-ps-sql-int-listener] az oktatóanyagban egy rendelkezésre állási csoport figyelőjének létrehozása történik)
[comment]: <> (A hozzáférés-vezérlési listák biztonságossá tétele hálózati végpont magyarázatát legjobb itt:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Központi telepítése egy SQL Server mindig a rendelkezésre állási csoport keresztül különböző Azure-régiókat is lehetőség. Ez a funkció kihasználja az Azure VNet – Vnet-kapcsolatot ([további részleteket][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Teendők régi blog)
[comment]: <> (Ilyen esetben az SQL Server AlwaysOn rendelkezésre állási csoportok beállítása az alábbiakban ismertetjük: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Az SQL Server magas rendelkezésre állás az Azure-ban összegzése
Mivel, hogy Azure Storage védi a tartalmat, egy kisebb ok arra, hogy a készenléti lemezkép ragaszkodjanak nincs. Ez azt jelenti, hogy a magas rendelkezésre állású forgatókönyv kell csak elleni, a következő esetekben:

* A virtuális Gépet a fürt az Azure-ban vagy más meggondolásból karbantartás miatt egész elérhetetlensége
* Az SQL Server-példányban szoftverekkel kapcsolatos problémák
* Ha adatok törlése és időpontban helyreállítási szükséges manuális hiba védelme

Megtekint egy is is, hogy az első két esetben elegendő az adatbázis-tükrözés vagy mindig bekapcsolva, mivel a harmadik esetben csak elegendő Naplóküldés egyező technológiák.

Kiegyenlítheti a mindig bekapcsolva, összehasonlítja az adatbázis-tükrözés, mindig a előnyei összetettebb beállításának kell. Ezek előnyeit is listázva lehet, például:

* Olvasható másodlagos másodpéldányokra.
* Biztonsági másolatok a másodlagos replikákon.
* Jobb méretezhetőséget.
* Több mint egy másodlagos replikákon.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Az SQL Server általános az SAP, az Azure összegzése
Az útmutatóban számos javaslatok és ajánlott elolvasni, több alkalommal az Azure telepítésének tervezése előtt. Általánosságban elmondható azonban ügyeljen arra, hogy kövesse a felső tíz általános adatbázis-kezelő Azure adott pontok:

[comment]: <> (2.3 a nagyobb átviteli sebesség mint mi? Mint egy virtuális merevlemez?)
1. Például az SQL Server 2014, az Azure-ban a legtöbb előnnyel rendelkezik a legújabb DBMS kiadás további használatára. Az SQL Server Ez az SQL Server 2012 SP1 CU4, amely magában foglalja az Azure Storage találkoznak biztonsági szolgáltatása. Azonban SAP együtt ajánlott legalább SQL Server 2014 SP1 CU1 vagy SQL Server 2012 SP2 és a legújabb CU használatát.
2. Tervezze meg gondosan az SAP rendszer fekvő egyensúlyba az adatok fájlelrendezés és az Azure-korlátozások az Azure-ban:
   * Nem rendelkezik túl sok lemezzel, de van-e elegendő érhető el a szükséges IOPS biztosításához.
   * Felügyelt lemezek használatakor nem, ne feledje, hogy IOPS is korlátozottak egy Azure Storage-fiókot és a Storage-fiókok belül minden Azure-előfizetésre korlátozódnak ([további részleteket][azure-subscription-service-limits]). 
   * Csak paritásos helyét a lemezeken, ha egy nagyobb átviteli sebesség eléréséhez van szüksége.
3. Ne telepítse a szoftvert, vagy az adatmegőrzési a D:\ meghajtóra, nem állandó és semmit, a meghajtó kapcsolat megszakad, a Windows újraindítását igénylő fájlokra.
4. Ne használjon Azure standard szintű tárolást gyorsítótárazása lemezen.
5. Ne használjon georeplikált Storage-fiókok Azure.  Adatbázis-kezelő munkaterhelések esetén használja a helyileg redundáns.
6. A DBMS gyártója által biztosított elérhető HA/DR megoldást használni adatbázis adatreplikációhoz.
7. Mindig névfeloldás használata, akkor az nem IP-címeket.
8. A lehetséges legmagasabb adatbázis tömörítés használata. Ez az SQL Server-lap tömörítése.
9. Ügyeljen arra, hogy az SQL Server-lemezképek használatával az Azure piactérről. Ha használja az SQL Server egyik, módosítania kell a példány rendezési bármely SAP NetWeaver rendszer telepítését megelőzően.
10. Telepítse és konfigurálja az SAP állomás figyelésével kapcsolatos Azure leírtak [telepítési útmutató][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>A Windows SAP ASE rögzítésen
A Microsoft Azure-től kezdődően egyszerűen áttelepítheti a meglévő SAP ASE alkalmazások az Azure virtuális gépeken. SAP ASE virtuális gépen lehetővé teszi, hogy a központi telepítési, kezelési és vállalati körének alkalmazások karbantartása tulajdonosi költségek csökkentéséhez könnyen áttelepítéssel ezeket az alkalmazásokat a Microsoft Azure használatával. SAP mértékéig egy Azure virtuális gépen, rendszergazdák és fejlesztők számára továbbra is használhatja a azonos fejlesztési és a rendelkezésre álló helyszíni felügyeleti eszközöket.

Nincs szolgáltatásiszint-szerződésben garantált az az Azure Virtual Machines, amely itt található: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Folyamatban, abban, hogy jól szemben a más nyilvános felhő virtualizálási ajánlatokat, de az egyes eredmények végez futtatott virtuális gépek Microsoft Azure változhat. Méretezése a különböző SAP SAP számú SAP hitelesített VM termékváltozatok megtalálható egy külön SAP Megjegyzés [1928533].

Utasítások és javaslatok a használat tekintetében az Azure Storage, SAP virtuális gépek központi telepítési vagy SAP figyelési alkalmazni SAP-alkalmazásokból együtt SAP hajlamosnak központi telepítések során a jelen dokumentum az első négy fejezetek megadott.

### <a name="sap-ase-version-support"></a>SAP ASE verzióinak támogatása
SAP jelenleg támogatja SAP ASE verzió 16,0 SAP Business Suite való használatára. Az SAP ASE kiszolgáló és JDBC és az ODBC-illesztőprogramok SAP Business Suite termékek használni kívánt összes frissítését, az SAP szolgáltatás piactéren keresztül kizárólag találhatók: <https://support.sap.com/swdc>.

Telepítések esetében a helyszíni, ne töltse le frissítéseket, a SAP ASE kiszolgálón, vagy a JDBC és az ODBC-illesztőprogramok közvetlenül a Sybase-webhelyek. Részletes információt a javítások, amely a SAP Business Suite termékek helyszíni használatát támogatja, és az Azure virtuális gépek, tekintse meg a következő SAP megjegyzéseket.

* [1590719]
* [1973241]

Az SAP Business Suite futó SAP ASE általános információk találhatók a [Állapotváltozás](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE beállítási útmutatója SAP kapcsolatos SAP ASE telepítések az Azure virtuális gépeken
#### <a name="structure-of-the-sap-ase-deployment"></a>Az SAP ASE telepítési szerkezete
Általános ismertetését megfelelően SAP ASE végrehajtható fájlok található, vagy el kell telepíteni a virtuális gép operációsrendszer-lemez rendszermeghajtóján (c: meghajtó\). Általában a SAP ASE rendszer és az eszközök adatbázisok többsége nem merevlemez által használt SAP NetWeaver munkaterhelés. Ezért a rendszer és az eszközök adatbázisok (master, model, saptools, sybmgmtdb, sybsystemdb), valamint a C:\ meghajtón marad. 

Kivétel oka lehet az összes munkaelem táblák és SAP ASE, amely néhány SAP ERP-szolgáltatásokat és minden BW munkaterhelések esetén előfordulhat, hogy nagyobb adatmennyiség vagy az i/o műveletek kötet, amely az eredeti virtuális gép operációs rendszere nem sorolhatók által létrehozott ideiglenes táblák tartalmazó ideiglenes adatbázis lemez (c: meghajtó\).

Attól függően, hogy a rendszer telepítéséhez használt SAPInst/SWPM verziója az adatbázis tartalmazhat:

* Egy egyetlen SAP ASE tempdb, amely SAP ASE telepítésekor jön létre
* Egy SAP ASE tempdb hozta létre SAP ASE és egy további saptempdb hozta létre a SAP telepítési eljárás telepítése
* Egy SAP ASE és egy további tempdb manuálisan lett létrehozva telepítése által létrehozott SAP ASE tempdb (például a következő SAP Megjegyzés [1752266]) ERP/BW adott tempdb követelményeinek megfelelően

Adott ERP-szolgáltatásokat vagy az összes BW munkaterhelések esetén érdemes, teljesítmény, hogy továbbra is a tempdb eszközei a továbbá létrehozott tempdb (SWPM, vagy manuálisan) a nem a C:\ meghajtóra tekintetében. Ha nincsenek további tempdb, javasoljuk, hogy hozzon létre egyet (SAP Megjegyzés [1752266]).

Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani a továbbá létrehozott TempDB:

* Helyezze át az első tempdb eszköz az első eszköz az SAP-adatbázis
* Az egyes a egy eszközhöz az SAP-adatbázist tartalmazó virtuális merevlemezeket tempdb eszközök felvétele

Ez a konfiguráció lehetővé teszi, hogy a tempdb vagy a több helyet kell biztosítania a rendszermeghajtó állónál felhasználását. Hivatkozásként egy ellenőrizheti a meglévő operációs rendszert, a helyi a tempdb eszköz méretét. Vagy ilyen konfiguráció lehetővé tenné IOPS számok elleni tempdb, amely a rendszermeghajtó nem adható meg. A helyi rendszert futtató rendszerek újra i/o-munkaterhelés elleni tempdb figyelésére használható.

Soha ne helyezze el azokat az eszközöket, SAP ASE alakzatot a D:\ meghajtóra, a virtuális gép. Ez vonatkozik a tempdb, akkor is, ha a tempdb tartani az objektumok csak átmeneti.

#### <a name="impact-of-database-compression"></a>Adatbázis-tömörítés hatása
Konfigurációk, ahol a i/o-sávszélesség korlátozó tényezővé válhat minden mértékcsoport, ami csökkenti az IOPS segíthetnek az alkalmazások és szolgáltatások egyik futtathatók az IaaS-forgatókönyveknél, például Azure Nyújtás. Ezért javasoljuk, hogy győződjön meg arról, hogy SAP ASE tömörítési használja-e egy meglévő SAP-adatbázist az Azure-bA feltöltés előtt.

Az ajánlott megoldás a következő tömörítés előtt feltöltése az Azure-ba, ha nincs megvalósítva megadott kívül számos oka van:

* Az Azure-bA feltöltendő adatok mérete alacsonyabb
* A tömörítési végrehajtásának időtartama rövidebb, feltéve, hogy az egyik használható erősebb hardver, további processzorok vagy magasabb i/o-sávszélesség legfeljebb I/O várakozási ideje a helyszíni
* Kisebb adatbázis mérete kisebb költségek a lemezfoglaláshoz vezethet

A LOB - és adattömörítés Azure virtuális gépeken üzemeltetett, mint a helyszíni virtuális gép működik. További részletes annak ellenőrzése, hogy tömörítési már szerepel egy meglévő SAP ASE adatbázis használja, ellenőrizze az SAP Megjegyzés [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit használatával figyelheti az adatbázis-példány
SAP rendszerek, amelyek használják az SAP ASE adatbázis platformként, a DBACockpit érhető el a következő tranzakcióban DBACockpit beágyazott böngészőablakot vagy Webdynpro. Azonban a figyelési és felügyelete az adatbázis összes funkcióját érhető el a DBACockpit csak Webdynpro végrehajtásában.

Mint a helyszíni rendszerekkel több lépések szükségesek a DBACockpit Webdynpro megvalósítása által használt összes SAP NetWeaver funkcióinak engedélyezéséhez. Hajtsa végre az SAP Megjegyzés [1245200] webdynpros használatának engedélyezése, és kötelező megfelelően generálásához. Ha a fenti megjegyzések utasításait követve is konfigurálnia az internetes kommunikáció kezelése (icm) együtt a http és https-kapcsolatok használni kívánt portokat. Alapértelmezés szerint http így néz ki:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

és a hivatkozások tranzakciót hozott létre DBACockpit néz:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően Ha, és hogyan keresztül helyek, a az SAP rendszert futtató Azure virtuális gép csatlakozik többhelyes vagy ExpressRoute (létesítmények közötti telepítési), győződjön meg arról, hogy ICM kell használ egy teljesen minősített állomásnév, amely feloldható a gépen amelyre próbálja megnyitni a DBACockpit. Lásd az SAP megjegyzést [773830] megértése, hogyan ICM határozza meg a teljesen minősített állomásnév attól függően, hogy a profil paraméterek és a set paraméter icm/host_name_full explicit módon ha szükséges.

Ha telepítette a virtuális gép közötti kapcsolatot nyújthassanak a helyszíni és az Azure közötti nélkül csak felhőalapú esetén, egy nyilvános IP-címet és egy domainlabel szeretné. A virtuális gép nyilvános DNS-nevének formátuma így néz ki:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

A DNS-nevével kapcsolatos további részleteket talál [Itt][virtual-machines-azurerm-versus-azuresm].

Az SAP profil paraméter icm/host_name_full beállítása a DNS-nevével, az Azure virtuális gép a hivatkozás hasonlóan néznének ki:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Ebben az esetben meg kell győződnie arról, hogy:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoport az Azure portálon ICM folytatott kommunikációhoz használt portok számára
* Bejövő szabályok hozzáadása a Windows tűzfal konfigurációját a ICM folytatott kommunikációhoz használt portok számára

Az egy automatizált az összes rendelkezésre álló javítások importálása javasoljuk, hogy rendszeres időközönként alkalmazni a javítási gyűjtemény SAP Megjegyzés az SAP verziója alkalmazandó:

* [1558958]
* [1619967]
* [1882376]

SAP ASE DBA Vezérlőpult további információkat talál a következő SAP kiegészítő:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE biztonsági mentés/helyreállítás szempontjai
Az Azure SAP ASE telepítésekor meg kell vizsgálni a biztonsági mentési módszer. Akkor is, ha a rendszer nem hatékony rendszer, az SAP-adatbázist üzemelteti a SAP ASE másolatot kell rendszeres időközönként. Azure Storage három képek tartja, mert a biztonsági mentés már kevésbé fontos való tárolási crash tekintetben. Az elsődleges fenntartása a megfelelő biztonsági mentési és helyreállítási tervek oka, hogy több is kompenzálják a logikai kézi/hibákat idő helyreállítási funkciók pont megadásával. Vagy használjon biztonsági mentések visszaállítani az adatbázist egy bizonyos mértékig időben vagy a biztonsági másolatokat az Azure-ban a meglévő adatbázis másolása egy másik rendszer rendezi a célja. Például akkor sikerült átvitele 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása az adott rendszer biztonsági mentésének visszaállításával.

Biztonsági mentése és visszaállítása az Azure-adatbázis ugyanúgy működik, mint a helyszíni. Tekintse meg az SAP megjegyzéseket:

* [1588316]
* [1585981]

biztonsági másolat létrehozása konfigurációk és ütemezési biztonsági mentések leírását. Attól függően, hogy az stratégia és konfigurálható igények adatbázishoz és naplófájlokhoz listázása, lemez egy meglévő lemezt, vagy a biztonsági mentés további lemezt. Csökkentse a hiba adatvesztést, ahol nincs adatbázis eszköz lemez használata ajánlott.

Adat - és LOB mellett tömörítési SAP ASE is biztosít, a biztonsági másolatok tömörítését. Az adatbázis és naplófájlok kiírt fájlok kevesebb helyet foglalnak a biztonsági másolatok tömörítését használata ajánlott. További információkért lásd: a SAP Megjegyzés [1588316]. A biztonsági másolat tömörítése elengedhetetlen is, ha azt tervezi, hogy töltse le a biztonsági mentések és a biztonsági mentési kiírt fájlok az Azure virtuális gép a helyszíni tartalmazó VHD átvinni kívánt adatok mennyiségének csökkentésére.

Ne használja a D:\ meghajtóra adatbázist vagy naplófájlokat memóriakép célként.

#### <a name="performance-considerations-for-backupsrestores"></a>A biztonsági mentés/visszaállítás teljesítménnyel kapcsolatos szempontok
Operációs rendszer nélküli telepítések, mint a biztonsági mentés/visszaállítás teljesítménye minden hány kötetek párhuzamosan olvasható, és mi az átviteli sebessége a köteteket lehet függ. Emellett a biztonsági másolatok tömörítését által használt CPU-felhasználás is fontos szerepet játszik a virtuális gépeken legfeljebb nyolc CPU szállal. Ezért egy veheti fel:

* A kevesebb a lemezek számát tárolja az adatbázis-eszközöket, annál kisebb a teljes átviteli sebesség mértéke a olvasása
* Minél kisebb a virtuális Gépet, a súlyosabb a hatását a biztonsági másolatok tömörítését a CPU-szám szálak
* A kevesebb célokat (paritásos könyvtárak, lemezek) írni a biztonsági mentés, a kisebb az átviteli sebesség

Két lehetőség, amely igényeitől függően használt/kombinált lehet írni a célok számának növeléséhez:

* A biztonsági mentési célkötet szétosztott több csatlakoztatott lemezek keresztül adott csíkozott köteten a IOPS átviteli sebességének javítása érdekében
* A biztonsági másolat beállításainak létrehozása SAP ASE szinten, használó egynél több célkönyvtár írni a biztonsági másolat

Egy köteten több csatlakoztatott lemezek keresztül szétosztott esik szó című szakaszában talál. További információ az több könyvtárak használatával az SAP ASE memóriakép konfigurációban, tekintse meg a dokumentációt a tárolt eljárás sp_config_dump, amelynek segítségével hozza létre a biztonsági másolat konfigurációt a [Sybase az Adatközpont](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Vészhelyreállítás Azure virtuális gépek
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Adatreplikálás SAP Sybase replikációs kiszolgálóval
Az SAP Sybase replikációs Server SRS-SAP mértékéig adatbázis-tranzakció aszinkron módon át távoli helyre meleg készenléti megoldást kínál. 

A telepítési és SRS működésének működik, valamint funkcionálisan ugyanúgy, mint a helyszíni Azure virtuálisgép-szolgáltatásokban üzemeltetett virtuális gépen.

SAP ASE HADR nincs szükség az Azure belső terheléselosztóhoz, és nem rendelkezik az operációs rendszer szintű fürtszolgáltatás függőségek, és működik-e az Azure Windows és Linux virtuális gépek. Az SAP ASE HADR részleteket olvassa el a [SAP ASE HADR felhasználók útmutató](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>SAP ASE Linux rögzítésen
A Microsoft Azure-től kezdődően egyszerűen áttelepítheti a meglévő SAP ASE alkalmazások az Azure virtuális gépeken. SAP ASE virtuális gépen lehetővé teszi, hogy a központi telepítési, kezelési és vállalati körének alkalmazások karbantartása tulajdonosi költségek csökkentéséhez könnyen áttelepítéssel ezeket az alkalmazásokat a Microsoft Azure használatával. SAP mértékéig egy Azure virtuális gépen, rendszergazdák és fejlesztők számára továbbra is használhatja a azonos fejlesztési és a rendelkezésre álló helyszíni felügyeleti eszközöket.

Azure virtuális gépek telepítése fontos tudni, hogy a hivatalos SLA-k, amely itt található: <https://azure.microsoft.com/support/legal/sla>

SAP méretezési információkat és a virtuális gép termékváltozatok hitelesített SAP listája megtalálható SAP Megjegyzés [1928533]. Méretezéssel dokumentumokat az Azure virtuális gépek itt található további SAP <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> és itt <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Utasítások és javaslatok a használat tekintetében az Azure Storage, SAP virtuális gépek központi telepítési vagy SAP figyelési alkalmazni SAP-alkalmazásokból együtt SAP hajlamosnak központi telepítések során a jelen dokumentum az első négy fejezetek megadott.

Az alábbi két SAP megjegyzések a Linux és ASE ASE általános információkat tartalmaznak, a felhőben:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE verzióinak támogatása
SAP jelenleg támogatja SAP ASE verzió 16,0 SAP Business Suite való használatára. Az SAP ASE kiszolgáló és JDBC és az ODBC-illesztőprogramok SAP Business Suite termékek használni kívánt összes frissítését, az SAP szolgáltatás piactéren keresztül kizárólag találhatók: <https://support.sap.com/swdc>.

Telepítések esetében a helyszíni, ne töltse le frissítéseket, a SAP ASE kiszolgálón, vagy a JDBC és az ODBC-illesztőprogramok közvetlenül a Sybase-webhelyek. Részletes információt a javítások, amely a SAP Business Suite termékek helyszíni használatát támogatja, és az Azure virtuális gépek, tekintse meg a következő SAP megjegyzéseket.

* [1590719]
* [1973241]

Az SAP Business Suite futó SAP ASE általános információk találhatók a [Állapotváltozás](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE beállítási útmutatója SAP kapcsolatos SAP ASE telepítések az Azure virtuális gépeken
#### <a name="structure-of-the-sap-ase-deployment"></a>Az SAP ASE telepítési szerkezete
Általános ismertetését megfelelően SAP ASE végrehajtható fájlokat kell helyét, vagy nem a legfelső szintű fájl rendszerbe a virtuális gép (/sybase) telepítve. Általában a SAP ASE rendszer és az eszközök adatbázisok többsége nem használja merevlemez SAP NetWeaver munkaterhelés szerint. Ezért a rendszer és az eszközök adatbázisok (master, model, saptools, sybmgmtdb, sybsystemdb), valamint a legfelső szintű fájlrendszerben maradjanak. 

Kivétel oka lehet az összes munkaelem táblák és SAP ASE, igénylő néhány SAP ERP-szolgáltatásokat és minden BW munkaterhelések esetén előfordulhat, hogy nagyobb adatmennyiség vagy az i/o-műveletek, kötet, amely az eredeti virtuális gép operációs rendszere nem sorolhatók által létrehozott ideiglenes táblák tartalmazó ideiglenes adatbázis a lemez.

Attól függően, hogy a rendszer telepítéséhez használt SAPInst/SWPM verziója az adatbázis tartalmazhat:

* Egy egyetlen SAP ASE tempdb, amely SAP ASE telepítésekor jön létre
* Egy SAP ASE tempdb hozta létre SAP ASE és egy további saptempdb hozta létre a SAP telepítési eljárás telepítése
* Egy SAP ASE és egy további tempdb manuálisan lett létrehozva telepítése által létrehozott SAP ASE tempdb (például a következő SAP Megjegyzés [1752266]) ERP/BW adott tempdb követelményeinek megfelelően

Adott ERP-szolgáltatásokat vagy az összes BW munkaterhelések esetén érdemes, teljesítményét, a tempdb eszközei a továbbá létrehozott tempdb (SWPM, vagy manuálisan) külön fájlrendszeren, amely egyetlen Azure adatlemezt vagy egy Linux RAID spannin jelölhető tekintetében g több Azure adatlemezek. Ha nincsenek további tempdb, javasoljuk, hogy hozzon létre egyet (SAP Megjegyzés [1752266]).

Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani a továbbá létrehozott TempDB:

* Helyezze át a tempdb első címtárat az SAP-adatbázisokhoz első fájlrendszere
* Az SAP-adatbázist a fájlrendszer tartalmazó mindegyikének tempdb könyvtárak hozzáadása

Ez a konfiguráció lehetővé teszi, hogy a tempdb vagy a több helyet kell biztosítania a rendszermeghajtó állónál felhasználását. Hivatkozásként egy ellenőrizheti a meglévő operációs rendszert, a helyi a tempdb directory méretét. Vagy ilyen konfiguráció lehetővé tenné IOPS számok elleni tempdb, amely a rendszermeghajtó nem adható meg. A helyi rendszert futtató rendszerek újra i/o-munkaterhelés elleni tempdb figyelésére használható.

Soha ne helyezze el SAP ASE könyvtárak /mnt vagy a virtuális gép /mnt/resource. Ez vonatkozik a tempdb, akkor is, ha a tempdb tartani az objektumok csak ideiglenes /mnt vagy /mnt/resource nem az alapértelmezett Azure virtuális gép ideiglenes adhatja, amely nem állandó. További információt az Azure virtuális gép ideiglenes terület található [Ez a cikk][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Adatbázis-tömörítés hatása
Konfigurációk, ahol a i/o-sávszélesség korlátozó tényezővé válhat minden mértékcsoport, ami csökkenti az IOPS segíthetnek az alkalmazások és szolgáltatások egyik futtathatók az IaaS-forgatókönyveknél, például Azure Nyújtás. Ezért javasoljuk, hogy győződjön meg arról, hogy SAP ASE tömörítési használja-e egy meglévő SAP-adatbázist az Azure-bA feltöltés előtt.

Az ajánlott megoldás a következő tömörítés előtt feltöltése az Azure-ba, ha nincs megvalósítva megadott kívül számos oka van:

* Az Azure-bA feltöltendő adatok mérete alacsonyabb
* A tömörítési végrehajtásának időtartama rövidebb, feltéve, hogy az egyik használható erősebb hardver, további processzorok vagy magasabb i/o-sávszélesség legfeljebb I/O várakozási ideje a helyszíni
* Kisebb adatbázis mérete kisebb költségek a lemezfoglaláshoz vezethet

A LOB - és adattömörítés Azure virtuális gépeken üzemeltetett, mint a helyszíni virtuális gép működik. További részletes annak ellenőrzése, hogy tömörítési már szerepel egy meglévő SAP ASE adatbázis használja, ellenőrizze az SAP Megjegyzés [1750510]. Adatbázis tömörítési kapcsolatos további információkért lásd: SAP Megjegyzés [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit használatával figyelheti az adatbázis-példány
SAP rendszerek, amelyek használják az SAP ASE adatbázis platformként, a DBACockpit érhető el a következő tranzakcióban DBACockpit beágyazott böngészőablakot vagy Webdynpro. Azonban a figyelési és felügyelete az adatbázis összes funkcióját érhető el a DBACockpit csak Webdynpro végrehajtásában.

Mint a helyszíni rendszerekkel több lépések szükségesek a DBACockpit Webdynpro megvalósítása által használt összes SAP NetWeaver funkcióinak engedélyezéséhez. Hajtsa végre az SAP Megjegyzés [1245200] webdynpros használatának engedélyezése, és kötelező megfelelően generálásához. Ha a fenti megjegyzések utasításait követve is konfigurálnia az internetes kommunikáció kezelése (icm) együtt a http és https-kapcsolatok használni kívánt portokat. Alapértelmezés szerint http így néz ki:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

és tranzakciót hozott létre hivatkozások DBACockpit hasonló lesz:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően Ha, és hogyan keresztül helyek, a az SAP rendszert futtató Azure virtuális gép csatlakozik többhelyes vagy ExpressRoute (létesítmények közötti telepítési), győződjön meg arról, hogy ICM kell használ egy teljesen minősített állomásnév, amely feloldható a gépen amelyre próbálja megnyitni a DBACockpit. Lásd az SAP megjegyzést [773830] megértése, hogyan ICM határozza meg a teljesen minősített állomásnév attól függően, hogy a profil paraméterek és a set paraméter icm/host_name_full explicit módon ha szükséges.

Ha telepítette a virtuális gép közötti kapcsolatot nyújthassanak a helyszíni és az Azure közötti nélkül csak felhőalapú esetén, egy nyilvános IP-címet és egy domainlabel szeretné. A virtuális gép nyilvános DNS-nevének formátuma így néz ki:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

A DNS-nevével kapcsolatos további részleteket talál [Itt][virtual-machines-azurerm-versus-azuresm].

Az SAP profil paraméter icm/host_name_full beállítása a DNS-nevével, az Azure virtuális gép a hivatkozás hasonlóan néznének ki:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Ebben az esetben meg kell győződnie arról, hogy:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoport az Azure portálon ICM folytatott kommunikációhoz használt portok számára
* Bejövő szabályok hozzáadása a Windows tűzfal konfigurációját a ICM folytatott kommunikációhoz használt portok számára

Az egy automatizált az összes rendelkezésre álló javítások importálása javasoljuk, hogy rendszeres időközönként alkalmazni a javítási gyűjtemény SAP Megjegyzés az SAP verziója alkalmazandó:

* [1558958]
* [1619967]
* [1882376]

SAP ASE DBA Vezérlőpult további információkat talál a következő SAP kiegészítő:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE biztonsági mentés/helyreállítás szempontjai
Az Azure SAP ASE telepítésekor meg kell vizsgálni a biztonsági mentési módszer. Akkor is, ha a rendszer nem hatékony rendszer, az SAP-adatbázist üzemelteti a SAP ASE másolatot kell rendszeres időközönként. Azure Storage három képek tartja, mert a biztonsági mentés már kevésbé fontos való tárolási crash tekintetben. Az elsődleges fenntartása a megfelelő biztonsági mentési és helyreállítási tervek oka, hogy több is kompenzálják a logikai kézi/hibákat idő helyreállítási funkciók pont megadásával. Vagy használjon biztonsági mentések visszaállítani az adatbázist egy bizonyos mértékig időben vagy a biztonsági másolatokat az Azure-ban a meglévő adatbázis másolása egy másik rendszer rendezi a célja. Például akkor sikerült átvitele 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása az adott rendszer biztonsági mentésének visszaállításával.

Biztonsági mentése és visszaállítása az Azure-adatbázis ugyanúgy működik, mint a helyszíni. Tekintse meg az SAP megjegyzéseket:

* [1588316]
* [1585981]

biztonsági másolat létrehozása konfigurációk és ütemezési biztonsági mentések leírását. Attól függően, hogy az stratégia és konfigurálható igények adatbázishoz és naplófájlokhoz listázása, lemez egy meglévő lemezt, vagy a biztonsági mentés további lemezt. Csökkentse a hiba adatvesztést, ahol nincs adatbázis könyvtárfájl lemez használata ajánlott.

Adat - és LOB mellett tömörítési SAP ASE is biztosít, a biztonsági másolatok tömörítését. Az adatbázis és naplófájlok kiírt fájlok kevesebb helyet foglalnak a biztonsági másolatok tömörítését használata ajánlott. További információkért lásd: a SAP Megjegyzés [1588316]. A biztonsági másolat tömörítése elengedhetetlen is, ha azt tervezi, hogy töltse le a biztonsági mentések és a biztonsági mentési kiírt fájlok az Azure virtuális gép a helyszíni tartalmazó VHD átvinni kívánt adatok mennyiségének csökkentésére.

Ne használja az Azure virtuális gép ideiglenes terület /mnt vagy /mnt/resource adatbázist vagy naplófájlokat biztonsági rendeltetési.

#### <a name="performance-considerations-for-backupsrestores"></a>A biztonsági mentés/visszaállítás teljesítménnyel kapcsolatos szempontok
Operációs rendszer nélküli telepítések, mint a biztonsági mentés/visszaállítás teljesítménye minden hány kötetek párhuzamosan olvasható, és mi az átviteli sebessége a köteteket lehet függ. Emellett a biztonsági másolatok tömörítését által használt CPU-felhasználás is fontos szerepet játszik a virtuális gépeken legfeljebb nyolc CPU szállal. Ezért egy veheti fel:

* A kevesebb a lemezek számát tárolja az adatbázis-eszközöket, annál kisebb a teljes átviteli sebesség mértéke a olvasása
* Minél kisebb a virtuális Gépet, a súlyosabb a hatását a biztonsági másolatok tömörítését a CPU-szám szálak
* A kevesebb célokat (Linux szoftveres RAID, lemezek) írni a biztonsági mentés, a kisebb az átviteli sebesség

Két lehetőség, amely igényeitől függően használt/kombinált lehet írni a célok számának növeléséhez:

* A biztonsági mentési célkötet szétosztott több csatlakoztatott lemezek keresztül adott csíkozott köteten a IOPS átviteli sebességének javítása érdekében
* A biztonsági másolat beállításainak létrehozása SAP ASE szinten, használó egynél több célkönyvtár írni a biztonsági másolat

Egy köteten több csatlakoztatott lemezek keresztül szétosztott esik szó című szakaszában talál. További információ az több könyvtárak használatával az SAP ASE memóriakép konfigurációban, tekintse meg a dokumentációt a tárolt eljárás sp_config_dump, amelynek segítségével hozza létre a biztonsági másolat konfigurációt a [Sybase az Adatközpont](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Vészhelyreállítás Azure virtuális gépek
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Adatreplikálás SAP Sybase replikációs kiszolgálóval
Az SAP Sybase replikációs Server SRS-SAP mértékéig adatbázis-tranzakció aszinkron módon át távoli helyre meleg készenléti megoldást kínál. 

A telepítési és SRS működésének működik, valamint funkcionálisan ugyanúgy, mint a helyszíni Azure virtuálisgép-szolgáltatásokban üzemeltetett virtuális gépen.

NEM támogatott ezen a ponton a időben történő ASE HADR SAP replikációs kiszolgálón keresztül. Előfordulhat, hogy ez tesztelése, és a jövőben jelent meg a Microsoft Azure platformon.

## <a name="specifics-to-oracle-database-on-windows"></a>Oracle Database Windows rögzítésen
Oracle szoftver futtatásához a Microsoft Windows Hyper-V és az Azure Oracle támogatja. A részleteket a Windows Hyper-V és az Azure általános támogatásában tekintse meg: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Követően az általános támogatási SAP alkalmazások Oracle-adatbázisok, ami az adott forgatókönyv támogatott is. Részleteket a dokumentum ezen része a neve.

### <a name="oracle-version-support"></a>Oracle által támogatott verzió
Oracle-verziókról és a megfelelő operációsrendszer-verziók, a SAP futó Azure virtuális gépeken Oracle találhatók SAP Megjegyzés: a támogatott [2039619].

Általános információk az SAP Business Suite futó Oracle 1DX találhatók: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Oracle beállítási útmutatója SAP-telepítések az Azure virtuális gépeken
#### <a name="storage-configuration"></a>Tároló konfigurálása
Csak egyetlen példány Oracle NTFS formátumú lemezek használata támogatott. Adatbázis összes fájlja az NTFS fájlrendszerrel, a virtuális merevlemezek vagy kezelt lemezek alapján kell tárolni. Ezeknek a lemezeknek az Azure virtuális géphez csatlakoztatva vannak, és Azure lap BLOB Storage alapuló (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy felügyelt lemezeket (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Valamilyen hálózati meghajtók vagy távoli megosztások, például az Azure file szolgáltatás:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

vannak **nem** Oracle adatbázis-fájlok támogatott!

Azure lap BLOB-tároló vagy kezelt lemezeken, ez a dokumentum fejezetben szereplő lemezekkel alapján [gyorsítótárazást a virtuális gépek és az adatlemezek] [ dbms-guide-2.1] és [Microsoft Azure Storage] [ dbms-guide-2.3] , valamint az Oracle-adatbázishoz megadott központi telepítéseknél vonatkozik.

Ahogy korábban a dokumentum általános része, az Azure-lemezeket a IOPS átviteli kvótái létezik. A pontos kvóták attól függően, hogy a Virtuálisgép-típussá szolgálnak. A kvótával rendelkező VM Típuslista található [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A támogatott Azure Virtuálisgép-típusokon azonosításához tekintse meg a SAP ne feledje [1928533].

Mindaddig, amíg a jelenlegi IOPS kvóta lemezenként megfelelnek a követelményeinek, lehetőség több egyetlen csatlakoztatott lemez DB-fájlok tárolására. 

Ha magasabb iops-értéket szükség, javasoljuk, hogy használatát ablak Tárolókészletek (csak érhető el a Windows Server 2012 és újabb) vagy a Windows 2008 R2 Windows szétosztott hozhat létre egy nagy méretű logikai eszköz több csatlakoztatott lemezek (lásd még: fejezet [ Szoftver RAID] [ dbms-guide-2.2] a jelen dokumentum). Ez a megközelítés leegyszerűsíti az adminisztrációs többletterhelés kezelheti a szabad lemezterület, és ezzel elkerülheti a annak érdekében, hogy a fájlok manuális szét több csatlakoztatott lemez.

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
A biztonsági mentéshez / működőképes állapotba hozni, a SAP BR * Oracle eszközei ugyanúgy a szabványos Windows Server operációs rendszerek és a Hyper-V által támogatott. Oracle-helyreállítás-kezelő (RMAN) a biztonsági mentés lemezre, és állítsa vissza a lemezről is támogatott.

#### <a name="high-availability"></a>Magas rendelkezésre állás
Oracle Data Guard magas rendelkezésre állású és vész-helyreállítási célokra támogatott. A részletek megtalálhatók [ez] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentációját.

#### <a name="other"></a>Egyéb
Más általános területre, például Azure rendelkezésre állási készletek vagy SAP figyelési alkalmazni a jelen dokumentum az Oracle-adatbázishoz, valamint a virtuális gépek központi telepítését az első három fejezetek leírtak szerint.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Oracle-adatbázishoz, Oracle Linux rögzítésen
Oracle szoftver futtatásához a Microsoft Windows Hyper-V és az Azure Oracle támogatja. A részleteket a Windows Hyper-V és az Azure általános támogatásában tekintse meg: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Követően az általános támogatási SAP alkalmazások Oracle-adatbázisok, ami az adott forgatókönyv támogatott is. Részleteket a dokumentum ezen része a neve.

### <a name="oracle-version-support"></a>Oracle által támogatott verzió
Oracle-verziókról és a megfelelő operációsrendszer-verziók, a SAP futó Azure virtuális gépeken Oracle találhatók SAP Megjegyzés: a támogatott [2039619].

Általános információk az SAP Business Suite futó Oracle 1DX találhatók: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Oracle beállítási útmutatója SAP-telepítések az Azure virtuális gépeken
#### <a name="storage-configuration"></a>Tároló konfigurálása
Csak egyetlen példány használatával ext3 Oracle, ext4 és xfs formázott lemezek támogatott. Minden adatbázisfájlt a rendszerek óráit fájlt a VHD-k vagy kezelt lemezek alapján kell tárolni. Ezeknek a lemezeknek az Azure virtuális géphez csatlakoztatva vannak, és Azure lap BLOB Storage alapuló (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy felügyelt lemezeket (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Valamilyen hálózati meghajtók vagy távoli megosztások, például az Azure file szolgáltatás:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

vannak **nem** Oracle adatbázis-fájlok támogatott!

Azure lap BLOB-tároló vagy kezelt lemezeken, ez a dokumentum fejezetben szereplő lemezekkel alapján [gyorsítótárazást a virtuális gépek és az adatlemezek] [ dbms-guide-2.1] és [Microsoft Azure Storage] [ dbms-guide-2.3] , valamint az Oracle-adatbázishoz megadott központi telepítéseknél vonatkozik.

Ahogy korábban a dokumentum általános része, az Azure-lemezeket a IOPS átviteli kvótái létezik. A pontos kvóták attól függően, hogy a Virtuálisgép-típussá szolgálnak. A kvótával rendelkező VM Típuslista található [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A támogatott Azure Virtuálisgép-típusokon azonosításához tekintse meg a SAP ne feledje [1928533]

Mindaddig, amíg a jelenlegi IOPS kvóta lemezenként megfelelnek a követelményeinek, lehetőség több egyetlen csatlakoztatott lemez DB-fájlok tárolására. 

Magasabb iops-értéket szükség, ha LVM (logikai kötetkezelő) vagy MDADM hozhat létre egy logikai sok több csatlakoztatott lemez használata ajánlott. További információ a fejezet [szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban. Ez a megközelítés leegyszerűsíti az adminisztrációs többletterhelés kezelheti a szabad lemezterület, és ezzel elkerülheti a annak érdekében, hogy a fájlok manuális szét több csatlakoztatott lemez.

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
A biztonsági mentéshez / működőképes állapotba hozni, a SAP BR * Oracle eszközei ugyanúgy az operációs rendszer és a Hyper-V által támogatott. Oracle-helyreállítás-kezelő (RMAN) a biztonsági mentés lemezre, és állítsa vissza a lemezről is támogatott.

#### <a name="high-availability"></a>Magas rendelkezésre állás
Oracle Data Guard magas rendelkezésre állású és vész-helyreállítási célokra támogatott. A részletek megtalálhatók [ez] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentációját.

#### <a name="other"></a>Egyéb
Más általános területre, például Azure rendelkezésre állási készletek vagy SAP figyelési alkalmazni a jelen dokumentum az Oracle-adatbázishoz, valamint a virtuális gépek központi telepítését az első három fejezetek leírtak szerint.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Az SAP MaxDB adatbázis Windows rögzítésen
### <a name="sap-maxdb-version-support"></a>SAP MaxDB verzióinak támogatása
SAP jelenleg támogatja SAP MaxDB verzió 7.9 SAP NetWeaver szolgáltatáson alapuló termékek az Azure-ban való használatra. Az SAP MaxDB kiszolgáló és JDBC és az ODBC-illesztőprogramok SAP NetWeaver szolgáltatáson alapuló termékek használni kívánt összes frissítését találhatók, a SAP szolgáltatás piactéren keresztül kizárólag <https://support.sap.com/swdc>.
SAP NetWeaver futó SAP MaxDB általános tájékoztatást található <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Microsoft Windows-verziók és az Azure virtuális gép típusú támogatott SAP MaxDB adatbázis-kezelő
A támogatott Microsoft-Windows-verzió az SAP MaxDB DBMS Azure című témakörben talál:

* [SAP termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP Megjegyzés [1928533]

Erősen ajánlott az operációs rendszer Microsoft Windows, amely Microsoft Windows 2012 R2 legújabb verzióját használja.

### <a name="available-sap-maxdb-documentation"></a>Rendelkezésre álló SAP MaxDB dokumentáció
A SAP MaxDB dokumentáció frissített listáját megtalálja a következő SAP Megjegyzés [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB beállítási útmutatója SAP-telepítések az Azure virtuális gépeken
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Tárolás beállítása
Az Azure storage ajánlott eljárások az SAP MaxDB kövesse az általános javaslatokat fejezetben említett [RDBMS üzembe helyezésének struktúra][dbms-guide-2].

> [!IMPORTANT]
> Más adatbázisokhoz, például SAP MaxDB is rendelkeznek, adatokat és a naplófájlokat. Azonban az SAP MaxDB terminológia megfelelő kifejezés "volume" (nem "file"). Nincsenek például SAP MaxDB adatok és a naplózási kötetek. Ne tévessze össze ezeket az operációs rendszer lemezkötetek használja. 
> 
> 

Rövid kell:

* Ha az Azure Storage-fiókok használatához beállítása az Azure storage-fiók, amely tárolja a SAP MaxDB adatainak és naplókönyvtárainak kötetek (azaz fájlok) **helyi redundáns tárolás (LRS)** fejezet meghatározott [Microsoft Azure Storage][dbms-guide-2.3].
* SAP MaxDB adatköteteken (azaz fájlok) a IO útvonal külön naplózási kötetek (azaz fájlok) a IO elérési úton található. Ez azt jelenti, hogy SAP MaxDB adatköteteken (azaz fájlok) kell telepíteni kell egy logikai meghajtó SAP MaxDB naplózási kötetek (azaz fájlok) kell őket telepíteni egy másik logikai meghajtón.
* Állítsa be a megfelelő gyorsítótárazási típusú lemezek, attól függően, hogy használ-e az SAP MaxDB adat- vagy naplófájl kötetek (azaz fájlok), és hogy használ-e Azure Standard vagy prémium szintű Azure Storage fejezetben leírtak [gyorsítótárazást a virtuális gépek és az adatlemezek] [dbms-guide-2.1].
* Mindaddig, amíg a jelenlegi IOPS kvóta lemezenként megfelelnek a követelményeinek, lehetőség az adatkötetek tárolásához egyetlen csatlakoztatott lemez és is tárolhatja az összes adatbázis naplózási kötetek másik egyetlen csatlakoztatott lemezen.
* Ha további iops-érték és/vagy a hely szükség, a Microsoft ablak Tárolókészletek (csak érhető el a Microsoft Windows Server 2012 és újabb) vagy a Microsoft Windows csíkozást Microsoft Windows 2008 R2 hozhat létre egy nagy méretű logikai eszköz több javasolt csatlakoztatott lemezek. További információ a fejezet [szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban. Ez a megközelítés leegyszerűsíti az adminisztrációs többletterhelés kezelheti a szabad lemezterület, és manuálisan elosztása fájlok több csatlakoztatott lemezre tevékenységi elkerülhető.
* Az IOPS legmagasabb szintű követelményeknek is használhatja prémium szintű Azure Storage elérhető a DS-méretek és GS sorozatnak virtuális gépeket.

![Az Azure infrastruktúra-szolgáltatási virtuális gép SAP MaxDB DBMS referencia-konfiguráció][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Biztonsági mentés és helyreállítás
Az Azure SAP MaxDB telepítésekor kell áttekinteni a biztonsági mentési módszer. Akkor is, ha a rendszer nem hatékony rendszer, az SAP-adatbázist üzemelteti a SAP MaxDB másolatot kell rendszeres időközönként. Azure Storage három képek tartja, mert a biztonsági mentés már kevésbé fontos a rendszer tárolási hiba és a fontosabb felügyeleti vagy működési hibák elleni védelme. Az elsődleges karbantartása, a megfelelő biztonsági mentési és visszaállítási terv oka, hogy is kompenzálják a logikai vagy kézi-e hibákat időpontban helyreállítási képességek biztosításával. Így a cél az, hogy vagy biztonsági másolatok állítsa vissza az adatbázist egy bizonyos mértékig időben vagy a biztonsági másolatokat az Azure-ban egy másik rendszer rendezi a meglévő adatbázis másolásával. Például akkor sikerült átvitele 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása az adott rendszer biztonsági mentésének visszaállításával.

Biztonsági mentése és visszaállítása az Azure-adatbázis ugyanúgy működik, mint a helyszíni rendszerekre, hogy használhassa a szabványos SAP MaxDB biztonsági mentés/visszaállítás eszközök, a SAP MaxDB dokumentáció dokumentumokat SAP megjegyzés szerepel ismertetett [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>A biztonsági mentés és helyreállítás a teljesítménnyel kapcsolatos szempontok
Operációs rendszer nélküli telepítések, mint a biztonsági mentési és visszaállítási teljesítménye minden függ, hogy hány kötetek olvasható, és az átviteli sebessége a köteteket. Emellett a biztonsági másolatok tömörítését által használt CPU-felhasználás is fontos szerepet játszik a virtuális gépek legfeljebb nyolc CPU szállal. Ezért egy veheti fel:

* A kevesebb a lemezek számát tárolja az adatbázis-eszközök, az alsó az általános Olvasás átviteli sebességét
* Minél kisebb a virtuális Gépet, a súlyosabb a hatását a biztonsági másolatok tömörítését a CPU-szám szálak
* A kevesebb célokat (paritásos könyvtárak, lemezek) írni a biztonsági mentés, a kisebb az átviteli sebesség

Célok írni számának növeléséhez, két módon használható, valószínűleg a kombinálva, igényeitől függően:

* Hogy a biztonsági mentéshez különböző köteteken
* A biztonsági mentési célkötet szétosztott több csatlakoztatott lemezek keresztül az IOPS átviteli sebességet csíkozott kötet javítása érdekében
* Dedikált logikai lemezen eszközök rendelkeznek:
  * SAP MaxDB biztonsági mentési kötetek (azaz fájlok)
  * SAP MaxDB adatköteteken (azaz fájlok)
  * SAP MaxDB naplózási kötetek (azaz fájlok)

Egy kötet szétosztott keresztül több csatlakoztatott lemez rendelkezik már tárgyalt, a fejezet [szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Other
Más általános területeken, mint a Azure rendelkezésre állási készletek vagy SAP figyelését is érvényesek a SAP MaxDB adatbázis virtuális gépek központi telepítéseknél a dokumentum az első három fejezetek leírtak szerint.
Más SAP MaxDB-specifikus beállításokat Azure virtuális gépek számára átlátható, és ismerteti a különböző dokumentumok SAP megjegyzés szerepel [767598] és ezek a SAP megjegyzések:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Az SAP liveCache Windows rögzítésen
### <a name="sap-livecache-version-support"></a>SAP liveCache verzióinak támogatása
Azure virtuális gépek támogatott SAP liveCache minimális verziója **SAP LC/LCAPPS 10.0 SP 25** beleértve **liveCache 7.9.08.31** és **LCA-Build 25**, az engedélyezett **EhP 2 SAP SCM 7.0** és magasabb.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Microsoft Windows-verziók és az Azure virtuális gép típusú támogatott SAP liveCache adatbázis-kezelő
A támogatott Microsoft-Windows-verzió az SAP liveCache Azure című témakörben talál:

* [SAP termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP Megjegyzés [1928533]

Erősen ajánlott az operációs rendszer Microsoft Windows Server legújabb verzióját használja. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache beállítási útmutatója SAP-telepítések az Azure virtuális gépeken
#### <a name="recommended-azure-vm-types"></a>Az Azure Virtuálisgép-típusokon ajánlott
SAP liveCache hatalmas számításokat alkalmazás, a mennyiségét és sebességét, a memória és CPU SAP liveCache teljesítményének jelentős hatással van. 

A SAP által támogatott Azure virtuális gép esetében (SAP Megjegyzés [1928533]), minden virtuális Processzor-erőforrások a virtuális gép számára lefoglalt dedikált fizikai Processzor-erőforrások a hipervizor által támogatott. Nincs elhelyezésétől (és a Processzor-erőforrások verseny ezért) történik.

Minden Azure virtuális gép példány által támogatott eszköztípusok SAP, hasonlóan a Virtuálisgép-memória értéke nem használatos a fizikai memória - (túlzott foglaltsága), például elhelyezésétől leképezve 100 %.

Ez szempontjából ajánlott használni az új D sorozatú vagy a DS sorozatnak (prémium szintű Azure Storage kombináltan) Azure virtuális gép típus, A-sorozatú mint 60 % processzorral rendelkeznek. A legnagyobb RAM Memóriát és CPU betöltési használhatja a legújabb Intel G-sorozat és GS sorozatnak (prémium szintű Azure Storage együtt) a virtuális gépek?? Xeon?? processzor E5 v3 termékcsalád, amelyek kétszer a memória és négy alkalommal fordult elő a D/DS sorozatnak tartós állapotú meghajtót tárterülete (SSD).

#### <a name="storage-configuration"></a>Tároló konfigurációja
SAP liveCache SAP MaxDB technológia alapul, mert az az Azure storage ajánlott a fejezet az SAP MaxDB említett ajánlott bevált [tárkonfiguráció] [ dbms-guide-8.4.1] SAP liveCache is érvényesek. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedikált Azure virtuális gép liveCache
SAP liveCache intenzíven használ számítási teljesítménnyel rendelkezik, hatékony használatának ajánlott egy dedikált Azure virtuális gép telepítéséhez. 

![Azure virtuális gép számára a hatékony használati eset liveCache dedikált][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
biztonsági mentés és visszaállítás, beleértve a teljesítménnyel kapcsolatos megfontolások már ismertetett vonatkozó SAP MaxDB fejezeteiben [biztonsági mentése és visszaállítása] [ dbms-guide-8.4.2] és [a biztonsági mentéshez a teljesítménnyel kapcsolatos szempontok Visszaállítás és][dbms-guide-8.4.3]. 

#### <a name="other"></a>Egyéb
Általános területre már ismertetett a megfelelő SAP MaxDB [ez] [ dbms-guide-8.4.4] fejezet. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Az SAP-kiszolgálóhoz Windows rögzítésen
Az SAP tartalom kiszolgáló nem egy különálló, kiszolgálóalapú összetevő például elektronikus dokumentumok tartalmát tároló különböző formátumokban. A SAP-kiszolgálóhoz fejlesztési technológia biztosítja, és a használt alkalmazások közötti SAP alkalmazások legyen. Külön rendszeren települ. Tipikus tartalma oktatási anyagok és a Tudásbázis adatraktár vagy a mySAP PLM dokumentumkezelő rendszer származó műszaki rajzok dokumentációjában talál. 

### <a name="sap-content-server-version-support"></a>SAP kiszolgáló által támogatott verzió
SAP jelenleg támogatja:

* **SAP tartalomkiszolgáló** verziójával **6.50 (vagy újabb)**
* **SAP MaxDB 7.9 verziója**
* **Microsoft IIS (Internet Information Server) 8.0-s (és újabb)**

Kifejezetten ajánljuk, hogy az SAP tartalom a kiszolgálón, amely a jelen dokumentum írása idején legújabb verzióját használja **6.50-es verzió SP4**, és a legújabb verzió **Microsoft IIS 8.5**. 

A legújabb támogatott verziói SAP-kiszolgálóból, valamint a Microsoft IIS ellenőrizze a [SAP termék rendelkezésre állási mátrix (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Támogatott Microsoft-Windows- és Azure virtuális gép SAP-kiszolgálóhoz
Támogatott Windows-verzió SAP-kiszolgálóhoz az Azure-on, című témakör tartalmazza:

* [SAP termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP Megjegyzés [1928533]

Erősen ajánlott a Microsoft Windows Server legújabb verzióját használja.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP tartalomkiszolgáló beállítási útmutatója SAP-telepítések az Azure virtuális gépeken
#### <a name="storage-configuration"></a>Tároló konfigurációja
Ha konfigurálja a fájlok tárolására az SAP MaxDB adatbázisban SAP tartalomkiszolgáló, ajánlott az Azure storage eljárásokat a fejezet az SAP MaxDB említett javaslat [tárolási konfigurációt] [ dbms-guide-8.4.1] a SAP kiszolgáló-forgatókönyv esetében is érvényesek. 

Ha a fájlok tárolásához a fájlrendszer SAP tartalomkiszolgáló konfigurálásához dedikált logikai meghajtó használata ajánlott. Windows tárolóhelyek használatával lehetővé teszi, hogy is növelheti a logikai lemez méretét és IOPS átviteli fejezetben leírtak [szoftver RAID][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>SAP tartalomkiszolgáló helye
Tartalom SAP-kiszolgálónak is telepíthető a azonos Azure-régió, és az Azure virtuális Hálózatot, amelyre a SAP rendszert telepíti. Szabadon döntse el, hogy szeretné-e egy dedikált Azure virtuális Gépet, vagy az SAP rendszert futtató ugyanabból virtuális SAP kiszolgáló-összetevők üzembe. 

![Azure virtuális gép dedikált SAP-kiszolgálóhoz][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>SAP gyorsítótár-kiszolgálón
A SAP-kiszolgáló egy olyan további kiszolgálóalapú összetevő helyileg (gyorsítótárazott) dokumentumokhoz való hozzáférést. A SAP-kiszolgáló gyorsítótárazza a dokumentumok tartalom SAP-kiszolgálók. Ez a hálózati forgalom optimalizálása, ha dokumentumok kell csak egyszer olvasható különböző helyekről. Az általános szabály az, hogy az SAP-kiszolgáló kell fizikailag közel az ügyfél, aki hozzáfér a SAP-kiszolgáló rendelkezik-e. 

Itt két lehetőség közül választhat:

1. **Ügyfél egy olyan háttér SAP rendszer** Ha egy SAP háttérrendszer tartalom SAP-kiszolgálóhoz való hozzáféréshez van konfigurálva, a SAP rendszer ügyfél. SAP rendszer és a SAP-kiszolgálót is telepített ugyanabban a régióban Azure, az azonos Azure adatközpontjában, mivel azok fizikailag egymás közelében. Ezért nincs szükség dedikált SAP gyorsítótár-kiszolgáló. SAP UI ügyfelek (SAP grafikus felhasználói felületének vagy a webes böngésző) való közvetlen hozzáféréshez a SAP rendszer, és az SAP rendszer beolvassa a dokumentumok a SAP-kiszolgálóhoz.
2. **Az ügyfél áll egy helyszíni webböngésző** az SAP kiszolgáló beállítható úgy, hogy közvetlenül a webböngésző számára érhető el. Ebben az esetben egy webes böngésző helyileg futó a SAP-kiszolgálóhoz az ügyfél. A helyszíni adatközpont és az Azure-adatközpontban kerülnek (ideális esetben közel egymáshoz) különböző fizikai helyen találhatóak. A helyszíni adatközpontját Azure Azure telephelyek közötti VPN- vagy ExpressRoute keresztül csatlakozik. Bár mindkét lehetőséget kínálnak a biztonságos VPN-hálózati kapcsolat az Azure-ba, pont-pont hálózati kapcsolat nem biztosít a hálózati sávszélesség és a késleltetés SLA-t a helyszíni adatközpontját és az Azure-adatközpontban között. Gyorsabb hozzáférés a dokumentumokhoz, tegye a következők egyikét:
   1. Helyszíni SAP gyorsítótár-kiszolgálót telepíteni, a Bezárás gombra a helyszíni böngészőben (beállítást [ez] [ dbms-guide-900-sap-cache-server-on-premises] . ábrát)
   2. Azure ExpressRoute, a nagy sebességű és kis késleltetésű dedikált hálózati kapcsolatot a helyszíni adatközpontban és Azure-adatközpontban kínál, amelyek konfigurálásához.

![A beállítással telepítheti a helyszíni SAP gyorsítótár-kiszolgáló][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
Ha a fájlok tárolására az SAP MaxDB adatbázisban a SAP-tartalomkiszolgáló konfigurálásához a biztonsági mentési/visszaállítási művelet és a teljesítménnyel kapcsolatos megfontolásokról már ismertetett SAP MaxDB fejezet [biztonsági mentése és visszaállítása] [ dbms-guide-8.4.2] és fejezet [teljesítménnyel kapcsolatos szempontok a biztonsági mentési és visszaállítási][dbms-guide-8.4.3]. 

Ha a fájlrendszer fájlok tárolására az SAP tartalomkiszolgáló konfigurálásához egy beállítás nem végrehajtása manuális biztonsági mentés/visszaállítás az egész fájl struktúra hol találhatók a dokumentumokat. SAP MaxDB biztonsági mentés/visszaállítás hasonló, javasoljuk, hogy rendelkezik dedikált lemezkötetek biztonsági mentési célra. 

#### <a name="other"></a>Egyéb
Más SAP tartalom kiszolgáló-specifikus beállításokat transzparensek Azure virtuális gépeken, és ismerteti a különböző dokumentumok és SAP megjegyzések:

* <https://service.sap.com/contentserver> 
* SAP Megjegyzés [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>A Windows LUW IBM DB2 rögzítésen
A Microsoft Azure-ban egyszerűen áttelepítheti a meglévő SAP alkalmazást IBM DB2 Linux, UNIX és a Windows (LUW) az Azure virtuális gépekhez. Az IBM DB2 LUW az SAP, rendszergazdák és fejlesztők továbbra is használhatja a azonos fejlesztői és felügyeleti eszközöket, amely elérhető a helyszínen.
Általános információk az SAP Business Suite futó IBM DB2 a LUW találhatók a az SAP közösségi hálózati Állapotváltozás-: <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

További információért és frissítéseire vonatkozó DB2 Azure LUW az SAP, tekintse meg a SAP Megjegyzés [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 Linux, UNIX és a Windows által támogatott verzió
A Microsoft Azure virtuális gép Services LUW IBM DB2 SAP 10.5 DB2 verzió frissítésétől esetén támogatott.

Támogatott SAP-termékek és Azure Virtuálisgép-típusokon kapcsolatos információkért tekintse meg a SAP ne feledje [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 Linux, UNIX és a Windows konfigurációs irányelveket SAP-telepítések az Azure virtuális gépeken
#### <a name="storage-configuration"></a>Tároló konfigurációja
Minden adatbázisfájlt a közvetlenül csatlakoztatott lemezek alapján NTFS fájlrendszerben kell tárolni. Ezeknek a lemezeknek az Azure virtuális géphez csatlakoztatott és a rendszer az Azure lap BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy felügyelt lemezeket (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Valamilyen hálózati meghajtók vagy távoli megosztások, például a következő fájl Azure-szolgáltatások **nem** támogatott adatbázis-fájlok: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

A Azure lap BLOB-tároló vagy kezelt lemezek mérete alapján használja, ha az utasítások ebben a dokumentumban fejezetben végrehajtott [RDBMS üzembe helyezésének struktúra] [ dbms-guide-2] is alkalmazhat az IBM DB2 LUW a megadott központi telepítéseknél Az adatbázis. 

Ahogy korábban a dokumentum általános része, a lemezek IOPS átviteli kvótákat létezik. A pontos kvóták használt Virtuálisgép-típustól függnek. A kvótával rendelkező VM Típuslista található [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Mindaddig, amíg lemezenként az aktuális IOPS-kvóta elegendő, lehetőség az adatbázis összes fájlja egy egyetlen csatlakoztatott lemezen tárolja. 

A teljesítmény szempontok is hivatkoznak fejezet "Adatok biztonsági és teljesítménnyel kapcsolatos szempontok adatbázis címtárak" a SAP telepítési útmutatóban.

Azt is megteheti, használhatja a Windows Tárolókészletek (csak érhető el a Windows Server 2012 és újabb), vagy a Windows 2008 R2, Windows csíkozást fejezetben ismertetett [szoftver RAID] [ dbms-guide-2.2] jelen dokumentum Hozzon létre egy nagy méretű logikai eszköz több lemez keresztül.
A lemezek, a DB2 tárolási útvonalat a sapdata és saptmp könyvtárak tartalmazó meg kell adnia egy fizikai lemez szektormérete 512 KB. Windows Tárolókészletek használata esetén létre kell hoznia manuálisan paraméter használatával parancssori felület használatával a tárolókészleteket `-LogicalSectorSizeDefault`. További információkért lásd: <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Biztonsági mentés / visszaállítás
A biztonsági mentési/visszaállítási funkciót, a LUW az IBM DB2 ugyanúgy, mint a szabványos Windows Server operációs rendszerek és a Hyper-V használata támogatott.

Meg kell győződnie arról, hogy rendelkezik-e egy érvényes adatbázis biztonsági mentési stratégia helyen. 

Operációs rendszer nélküli telepítések, mint biztonsági mentés/visszaállítás függ hány kötetek párhuzamosan olvasható, és mi az átviteli sebessége a köteteket lehet. Emellett a biztonsági másolatok tömörítését által használt CPU-felhasználás is fontos szerepet játszik a virtuális gépeken legfeljebb nyolc CPU szállal. Ezért egy veheti fel:

* A kevesebb a lemezek számát tárolja az adatbázis-eszközöket, annál kisebb a teljes átviteli sebesség mértéke a olvasása
* Minél kisebb a virtuális Gépet, a súlyosabb a hatását a biztonsági másolatok tömörítését a CPU-szám szálak
* A kevesebb célokat (paritásos könyvtárak, lemezek) írni a biztonsági mentés, a kisebb az átviteli sebesség

Célok írni számának növeléséhez, két lehetőség közül választhat lehet igényeitől függően használt/együtt:

* A biztonsági mentési célkötet szétosztott keresztül több lemez adott csíkozott köteten a IOPS átviteli sebességének javítása érdekében
* Egynél több célkönyvtár használatával történő biztonsági mentés írása

#### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás
A Microsoft Fürtkiszolgáló (MSCS) nem támogatott.

DB2 magas rendelkezésre állású vész-helyreállítási (HADR) használata támogatott. Ha a virtuális gépek a magas rendelkezésre ÁLLÁSÚ konfiguráció névfeloldás működik, a telepítő az Azure-ban nem Miben különböznek a telepítés, amely a helyszínen történik. Nem ajánlott, csak az IP-feloldási támaszkodni.

A storage-fiókok, amelyek az adatbázis-lemezek tárolására ne használjon a Georeplikáció. További információkért tekintse meg a fejezet [Microsoft Azure Storage] [ dbms-guide-2.3] és fejezet [magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó] [ dbms-guide-3].

#### <a name="other"></a>Egyéb
Más általános területre, például Azure rendelkezésre állási készletek vagy SAP figyelés alkalmazása az IBM DB2 LUW a virtuális gépek telepítéséhez, valamint a dokumentum az első három fejezetek leírtak szerint. 

Is érdemes felkeresni fejezet [az SAP Azure összegzése az SQL Server általános][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>A Linux LUW IBM DB2 rögzítésen
A Microsoft Azure-ban egyszerűen áttelepítheti a meglévő SAP alkalmazást IBM DB2 Linux, UNIX és a Windows (LUW) az Azure virtuális gépekhez. Az IBM DB2 LUW az SAP, rendszergazdák és fejlesztők továbbra is használhatja a azonos fejlesztői és felügyeleti eszközöket, amely elérhető a helyszínen. Általános információk az SAP Business Suite futó IBM DB2 a LUW találhatók a az SAP közösségi hálózati Állapotváltozás-: <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

További információért és frissítéseire vonatkozó DB2 Azure LUW az SAP, tekintse meg a SAP Megjegyzés [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 Linux, UNIX és a Windows által támogatott verzió
A Microsoft Azure virtuális gép Services LUW IBM DB2 SAP 10.5 DB2 verzió frissítésétől esetén támogatott.

Támogatott SAP-termékek és Azure Virtuálisgép-típusokon kapcsolatos információkért tekintse meg a SAP ne feledje [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 Linux, UNIX és a Windows konfigurációs irányelveket SAP-telepítések az Azure virtuális gépeken
#### <a name="storage-configuration"></a>Tároló konfigurációja
Minden adatbázisfájlt a közvetlenül csatlakoztatott lemezek alapján fájlrendszeren kell tárolni. Ezeknek a lemezeknek az Azure virtuális géphez csatlakoztatott és a rendszer az Azure lap BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy felügyelt lemezeket (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Valamilyen hálózati meghajtók vagy távoli megosztások, például a következő fájl Azure-szolgáltatások **nem** támogatott adatbázis-fájlok:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Ha az Azure lap BLOB-tároló, ez a dokumentum fejezetben szereplő alapján lemezek [RDBMS üzembe helyezésének struktúra] [ dbms-guide-2] az IBM DB2 LUW adatbázis telepítések esetén is vonatkozik.

Ahogy korábban a dokumentum általános része, a lemezek IOPS átviteli kvótákat létezik. A pontos kvóták használt Virtuálisgép-típustól függnek. A kvótával rendelkező VM Típuslista található [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Mindaddig, amíg lemezenként az aktuális IOPS-kvóta elegendő, lehetőség az adatbázis összes fájlja egy egyetlen lemezen tárolja.

A teljesítmény szempontok is hivatkoznak fejezet "Adatok biztonsági és teljesítménnyel kapcsolatos szempontok adatbázis címtárak" a SAP telepítési útmutatóban.

Másik megoldásként használhatja LVM (logikai kötetkezelő) vagy MDADM fejezetben leírtak [szoftver RAID] [ dbms-guide-2.2] hozhat létre egy nagy méretű logikai eszköz több lemezt a dokumentum.
A lemezek, a DB2 tárolási útvonalat a sapdata és saptmp könyvtárak tartalmazó meg kell adnia egy fizikai lemez szektormérete 512 KB.

#### <a name="backuprestore"></a>Biztonsági mentés / visszaállítás
Ahogy a normál Linux telepítési helyszíni IBM DB2 LUW a biztonsági mentés/visszaállítás funkcionalitása használata támogatott.

Meg kell győződnie arról, hogy rendelkezik-e egy érvényes adatbázis biztonsági mentési stratégia helyen.

Operációs rendszer nélküli telepítések, mint biztonsági mentés/visszaállítás függ hány kötetek párhuzamosan olvasható, és mi az átviteli sebessége a köteteket lehet. Emellett a biztonsági másolatok tömörítését által használt CPU-felhasználás is fontos szerepet játszik a virtuális gépeken legfeljebb nyolc CPU szállal. Ezért egy veheti fel:

* A kevesebb a lemezek számát tárolja az adatbázis-eszközöket, annál kisebb a teljes átviteli sebesség mértéke a olvasása
* Minél kisebb a virtuális Gépet, a súlyosabb a hatását a biztonsági másolatok tömörítését a CPU-szám szálak
* A kevesebb célokat (paritásos könyvtárak, lemezek) írni a biztonsági mentés, a kisebb az átviteli sebesség

Célok írni számának növeléséhez, két lehetőség közül választhat lehet igényeitől függően használt/együtt:

* A biztonsági mentési célkötet szétosztott keresztül több lemez adott csíkozott köteten a IOPS átviteli sebességének javítása érdekében
* Egynél több célkönyvtár használatával történő biztonsági mentés írása

#### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás
DB2 magas rendelkezésre állású vész-helyreállítási (HADR) használata támogatott. Ha a virtuális gépek a magas rendelkezésre ÁLLÁSÚ konfiguráció névfeloldás működik, a telepítő az Azure-ban nem Miben különböznek a telepítés, amely a helyszínen történik. Nem ajánlott, csak az IP-feloldási támaszkodni.

A storage-fiókok, amelyek az adatbázis-lemezek tárolására ne használjon a Georeplikáció. További információkért tekintse meg a fejezet [Microsoft Azure Storage] [ dbms-guide-2.3] és fejezet [magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó] [ dbms-guide-3].

#### <a name="other"></a>Egyéb
Minden más általános például Azure rendelkezésre állási készletek vagy SAP figyelés témakörei az IBM DB2 LUW a virtuális gépek telepítéséhez, valamint a dokumentum az első három fejezetek leírtak szerint.

Is érdemes felkeresni fejezet [az SAP Azure összegzése az SQL Server általános][dbms-guide-5.8].

