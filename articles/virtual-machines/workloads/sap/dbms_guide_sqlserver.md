---
title: Az SAP számítási feladatok SQL Server Azure virtuális gépek DBMS üzembe |} A Microsoft Docs
description: Az SQL Server Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3cefecdf0f87483a1fb544d1eb4e3e514e388259
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406921"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SAP NetWeaver az SQL Server Azure virtuális gépek DBMS üzembe helyezése

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




Ez a dokumentum figyelembe kell venni az SQL Server telepítése az Azure iaas SAP munkaterhelés számos különböző területekre terjed ki. Ebben a dokumentumban előfeltételeként rendelkezik olvassa el a dokumentumot [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md) lévő többi útmutató és a [SAP számítási feladatok az Azure-dokumentáció](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> Ez a dokumentum Windows verziója SQL Server-kiszolgálón. Az SAP az SAP-szoftverek bármelyikével SQL Server Linux-verzióját nem támogat. A dokumentum van nem megvizsgálja a Microsoft Azure SQL Database, azaz a Platform szolgáltatási ajánlatunk a Microsoft Azure platform. Ez a tanulmány a hozzászólás van az SQL Server-termék futtatásáról, mint a helyszíni üzemelő példányok az Azure Virtual Machines, az infrastruktúra kihasználva az Azure szolgáltatás szerint ismert. Adatbázis-képességeket és a funkciók között ezeket az ajánlatokat két különböző, és meg nem nem lenne szerencsés egymással. Lásd még: <https://azure.microsoft.com/services/sql-database/>
> 
>

Általában érdemes lehet a legújabb SQL Server használata az SAP számítási feladatok futtatásához az Azure IaaS-kiadások. A legújabb SQL Server-verziókban az egyes Azure-szolgáltatások és funkciók jobb integrációt kínálnak. Vagy optimalizálására az Azure IaaS-infrastruktúrát a módosításokat.

Javasoljuk, hogy tekintse át [ez] [ virtual-machines-sql-server-infrastructure-services] a folytatás előtt.

A következő szakaszokban megtalálhatja a fenti hivatkozást a dokumentáció részét összesítve és említett. SAP összegyűjtjük is szerepelnek, és néhány olyan fogalmat, részletesebben ismerteti. Azonban erősen ajánlott, haladjon végig a dokumentáció első fent az SQL Server-specifikus dokumentáció elolvasása előtt.

Van néhány SQL Server IaaS konkrét információk tudnia kell, mielőtt továbblépne:

* **SQL-Verziótámogatás**: az ügyfelek, az SQL Server 2008 R2 és újabb verziója támogatja a Microsoft Azure virtuális gépen. Korábbi verziók nem támogatottak. Tekintse át az ebben az általános [támogatási nyilatkozattal](https://support.microsoft.com/kb/956893) további részletekért. Általánosságban véve az SQL Server 2008 használata a Microsoft által is támogatott. Azonban jelentős funkciót az SAP, az SQL Server 2008 R2 rendszerben jelent meg, amely miatt az SQL Server 2008 R2 az SAP a minimális kiadásban. Általában érdemes lehet a legújabb SQL Server használata az SAP számítási feladatok futtatásához az Azure IaaS-kiadások. A legújabb SQL Server-verziókban az egyes Azure-szolgáltatások és funkciók jobb integrációt kínálnak. Vagy optimalizálására az Azure IaaS-infrastruktúrát a módosításokat. A tanulmány ezért az SQL Server 2016 és az SQL Server 2017-ben.
* **SQL-teljesítmény**: Microsoft Azure futtatott virtuális gépek végrehajtása is támogatáshoz képest korlátozottabb más nyilvános felhőalapú virtualizációs megoldások, de az egyes eredmények eltérőek lehetnek. Tekintse meg a cikk [teljesítmény az Azure Virtual machines gépeken az SQL Server ajánlott eljárásai](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Az Azure piactéren elérhető lemezképekkel**: új Microsoft Azure virtuális gép üzembe helyezése a leggyorsabb módot kínálni arra, hogy a lemezképek használata az Azure Marketplace-ről. Nincsenek a legújabb SQL Server-kiadások tartalmazó lemezképek az Azure piactéren. A képek, ahol az SQL Server már telepítve van az SAP NetWeaver-alkalmazásaihoz azonnal nem használható. A hiba oka az SQL Server alapértelmezett rendezése ezeket a képeket, és nem az SAP NetWeaver-rendszerek által igényelt rendezés belül van telepítve. Az ilyen rendszerképek használatához ellenőrizze a fejezetben leírt lépéseket [SQL Server-lemezkép használatával a Microsoft Azure Marketplace-en kívül][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>A VM-/ VHD-szerkezet SAP-kapcsolódó SQL Server-telepítéseket javaslatok
Az általános leírása megfelelően SQL Server végrehajtható fájlok található, vagy el kell telepíteni a virtuális gép operációsrendszer-lemez rendszermeghajtóján (C: meghajtó\).  Általában a legtöbb az SQL Server rendszer-adatbázisok nem használhatók magas szinten SAP NetWeaver munkaterhelés szerint. Ennek eredményeképpen a rendszeradatbázisokban (master, msdb, valamint modell) SQL Server is marad, valamint a C:\ meghajtón található. Kivétel a tempdb, amely SAP-munkaterhelések esetén lehet szükség, vagy nagyobb adatmennyiség vagy az i/o-műveletek kötetet kell lennie. I/O munkaterheléstől, amelyre nem vonatkozik a rendszert tartalmazó virtuális Merevlemezt. Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani:


* Minden SAP-minősítéssel rendelkező virtuális gépek típusai (tekintse meg az SAP-Jegyzetnek [1928533]), kivéve A-sorozatú virtuális gépek, a tempdb adat és a naplófájlok helyezhető a D:\ meghajtóra nem megőrzött. 
* Mindazonáltal javasoljuk, hogy több tempdb-adatfájlok használatát. Vegye figyelembe, hogy különböznek a D:\ meghajtóra köteteket a virtuális gép típusa alapján. A pontos méretét a D:\ meghajtóra, a különböző virtuális gépek, ellenőrizze a cikk [méretek a Windows virtuális gépek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Ezek a konfigurációk engedélyezze a tempdb fel több helyet több, mint a rendszermeghajtó tud biztosítani. A nem állandó D:\ meghajtóra a jobb i/o-késés és az átviteli sebesség (kivéve A-sorozatú virtuális gépek) is kínál. Annak érdekében, hogy a megfelelő tempdb mérete határozza meg, ellenőrizheti a meglévő rendszerek tempdb méretet. 

>[!NOTE]
> abban az esetben, ha a tempdb-adatfájlok és naplófájl helyezzen egy mappába a D:\ meghajtóra, amelyet létrehozott, győződjön meg arról, hogy a mappa létezik-e a virtuális gép újraindítását követően kell. Mivel a D:\ meghajtóra frissen inicializálása után indítsa újra a virtuális gép összes fájl és könyvtár struktúrák is törli. Annak a lehetősége, újra létre kell hozni a D:\ meghajtón végleges könyvtárstruktúrák dokumentálva az SQL Server szolgáltatás kezdete előtt [Ez a cikk](http://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Egy Virtuálisgép-konfigurációt, az SAP adatbázis az SQL Server fut, amely, és ahol a tempdb adat- és a tempdb logfile kerülnek a D:\ meghajtóra hasonlóan néz ki:

![Az SQL Server egyszerű virtuális gép lemezkonfigurációja ábrája](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

A fenti ábrán egy egyszerű esetet jeleníti meg. Ahogy a cikk a eluded [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md), number, és a Premium Storage-lemez mérete a különböző tényezőktől függ. Azonban általában azt javasoljuk:

- Tárolóhelyek segítségével egy vagy kis számú kötetek kialakításához, amely tartalmazza az SQL Server-adatfájlok. Ez a konfiguráció mögötti oka az, hogy a valós életben vannak a különböző méretű adatbázisfájlokat a különböző i/o-munkaterhelés számos olyan SAP-adatbázisok.
- A tárolóhelyek használatával adjon meg elegendő IOPS és az SQL Server tranzakciós naplófájlt. A lehetséges IOPS számítási feladatok gyakran az a útmutatás vonal a méretezéshez, a tranzakciós napló kötet és az SQL Server tranzakciós kötet nem lehetséges mennyisége
- Használja a D:\drive tempdb mindaddig teljesítményt elég jó. Ha a teljes számítási feladatnak a teljesítmény korlátozza a D:\ meghajtóra alatt található tmepdb szüksége lehet érdemes lehet külön a Premium Storage-lemez által közölt tempdb áthelyezése [Ez a cikk](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Az M-sorozatú virtuális gépek speciális
Azure M sorozatú virtuális gép a tranzakciós naplóba való írása a késés csökkenthető tényezők befolyásolják, a teljesítmény az Azure Premium Storage, Azure Write Accelerator használatakor képest. Ezért a virtuális, az SQL Server-tranzakciónapló számára a kötetet alkotó Azure Write Accelerator szabad telepíteni. A dokumentumban olvasható részletei [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>A lemezek formázása
Az SQL Server SQL Server adat- és naplófájlok fájljait tartalmazó lemezt NTFS blokkméretének 64 KB-os kell lennie. Hiba esetén nem kell formázni a D:\ meghajtóra. Ez a meghajtó előre formázott származnak.

Annak érdekében, hogy, hogy a visszaállítási vagy -adatbázisok létrehozása nem inicializálása folyamatban van az adatfájlok által feltérképezése valódi élménnyé válik a fájlok tartalmát, akkor gondoskodnia kell arról, hogy a felhasználói környezet az SQL Server-szolgáltatás fut a jogosultsága egy bizonyos. Általában a Windows rendszergazdai csoport rendelkezik ezekkel a jogosultságokkal. Ha a felhasználó nem Windows rendszergazdai felhasználó környezetében fut, az SQL Server szolgáltatást, kell rendelnie, hogy a felhasználó a felhasználói jogosultság **kötet-karbantartási feladatok végrehajtása**.  A Microsoft Tudásbázis megfelelő cikkében. a részleteket lásd: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Adatbázis tömörítése hatása
A konfigurációk, ahol i/o-sávszélesség korlátozó válhat minden mérték, ami csökkenti az IOPS segíthet a munkaterhelés, mint például az Azure IaaS-forgatókönyveknél is futtatható egy többhelyes. Ezért ha még nincs kész, az SQL Server lap tömörítés alkalmazása ajánlott SAP és a Microsoft egy meglévő SAP-adatbázist az Azure-ba való feltöltéséhez.

Azt javasoljuk, hogy az adatbázis tömörítése végrehajtása az Azure-ba való feltöltéséhez kívül két oka van megadva:

* A feltölteni kívánt adatok mennyisége kisebb.
* A tömörítés végrehajtási időtartamának rövidebb, feltéve, hogy az egyik használjon erősebb hardveres további processzorokat vagy magasabb i/o-sávszélességet vagy kisebb I/O várakozási ideje a helyszíni.
* Kisebb adatbázis maximális mérete kisebb költségek lemezfoglaláshoz vezethet

Adatbázis tömörítése működik, valamint egy Azure Virtual Machines szolgáltatásban, mint a helyszíni. További részleteket az tömörítése a meglévő SAP NetWeaver az SQL Server-adatbázisok, ellenőrizze a cikk [SAP továbbfejlesztett tömörítés eszköz MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>Az SQL Server 2014 és újabb - tárolására-adatbázisfájlok közvetlenül az Azure Blob Storage
Az SQL Server 2014 és újabb verziókban nyílt foglalkozik a gondolattal tároljon adatbázisfájlokat közvetlenül az Azure Blob Store "burkolót" őket egy virtuális merevlemez nélkül. A központi telepítési típus különösen a standard szintű Azure Storage vagy kisebb Virtuálisgép-típusok használata lehetővé teszi a forgatókönyvek, ahol szeretné érvénybe lépteti néhány kisebb Virtuálisgép-típusok a csatlakoztatott lemezek csak korlátozott számú iops-érték határain is leküzdeni. Ezzel a módszerrel a központi telepítés működik, azonban nem rendszer-adatbázisokat az SQL Server felhasználói adatbázisokat. Emellett az SQL Server adathoz és naplófájlhoz működik. Ha szeretné-e egy SAP SQL Server-adatbázis üzembe helyezése "alkalmazásburkoló" helyett ezzel a módszerrel azt a virtuális merevlemezek, vegye figyelembe:

* A Tárfiókot igényeinek, az egyik, amellyel a virtuális gép az SQL Server telepítése fut-e az azonos Azure-régióban kell használni.
* Ezt a módszert, valamint a központi telepítések korábban felsorolt a VHD-k elosztásra vonatkozó különböző Azure-Tárfiókok keresztül szempontok érvényesek. Azt jelenti, hogy az Azure Storage-fiók határértékek i/o műveletek száma.
* Helyett a számlázás a virtuális gép tárolási i/o-kvóta ellen, a storage-blobokkal, az SQL Server adathoz és naplófájlhoz jelölő forgalmát, a virtuális gép hálózati sávszélesség adott típusú virtuális gép fog kijelölésére. A hálózati és tárolási sávszélesség-egy adott típusú virtuális Gépet, tekintse meg [méretek a Windows virtuális gépek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Eredményeként a fájl I/O a hálózat kvótát állít keresztül küld, vannak részes főleg a tárolási kvótát, és az adott a teljes sávszélesség használata a virtuális gép csak részben.
* Az IOPS és az i/o átviteli teljesítmény-határértékeit, amely rendelkezik az Azure Premium Storage a különböző lemezméretet többé nem érvényesek. Akkor is, ha a létrehozott blobok az Azure Premium Storage találhatók. A tárolók szerepelnek a cikkben [nagy teljesítményű Premium Storage és a felügyelt lemezek virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage#scalability-and-performance-targets). Következtében az SQL Server-adatfájlok és közvetlenül az Azure Premium Storage tárolt blobok naplófájlokat helyez, a teljesítményjellemzők eltérő lehet az Azure Premium Storage virtuális merevlemezek képest.
* Elérhető az Azure Premium Storage-lemez gyorsítótár-alapú gazdagép nem érhető el, amikor az SQL Server adatfájljainak közvetlenül az Azure-blobokat.
* M sorozatú virtuális gépek, az Azure Írásgyorsító támogatása az SQL Server tranzakciós naplófájl ezredmásodperces írására nem használható. 

Ez a funkció a részletek megtalálhatók a cikkben [SQL Server adatfájljainak a Microsoft Azure-ban](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Éles rendszerek ajánljuk, hogy elkerülése érdekében ez a konfiguráció helyett válassza ki az Elhelyezés a SQL Server-adatok és a naplófájlokat az Azure Premium Storage virtuális merevlemezek helyett közvetlenül az Azure-blobok.


## <a name="sql-server-2014-buffer-pool-extension"></a>Az SQL Server 2014 pufferkészlet-bővítmény
Az SQL Server 2014 bevezetett egy új funkciót, amelyet [pufferkészlet-kiterjesztés](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Ez a funkció, amelyet a memóriát, valamint egy második szintű gyorsítótár-kiszolgáló helyi SSD-k vagy a virtuális gép biztonsági SQL Server a pufferkészletben terjeszti ki. A pufferkészlet-bővítmény lehetővé teszi, hogy tartja az adatokat egy nagyobb munkakészletének "a memóriában". Standard szintű Azure Storage eléréséhez képest a hozzáférést a bővítményt a pufferkészlet, helyi SSD-k az Azure virtuális gép található, a számos tényező gyorsabb. A pufferkészlet-kiterjesztés az Azure Premium Storage olvasási gyorsítótárat összehasonlítása, ajánlott az SQL Server-adatfájlok, nincs jelentős előnyökkel várhatóan a pufferkészlet-bővítményeinek. Oka, hogy mindkét gyorsítótárak (SQL Server pufferkészlet-bővítmény és a prémium szintű Storage olvasási gyorsítótár) használ az Azure-beli számítási csomópont helyi lemezeket.

Addig is az SQL Server a pufferkészlet-kiterjesztés kapcsolatos tapasztalatok SAP számítási feladatok vegyes, és még nem teszi lehetővé a egyértelmű javaslatok-e használni minden esetben a. Az ideális eset az, hogy az SAP-alkalmazás szükséges munkakészletének illeszkedik a fő memóriában. A csapattagok pedig nyugodtabban aludhatnak kínál, amelyek akár 4 TB memóriával rendelkező virtuális gépek Azure-ban elérhető munkakészletének megtartása a memóriában, kell lennie. Ezért a pufferkészlet-bővítmény használatát korlátozva, egyes ritka esetekben, és nem lehet egy alapvető technikai esetet.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Az SQL Server biztonsági mentési/helyreállítási szempontjai
SQL Server üzembe helyezése az Azure-ba, amikor a biztonsági mentési módszerek át kell tekinteni. Még ha a rendszer nem egy éles rendszer, az SAP-adatbázist az SQL-kiszolgáló által üzemeltetett kell készíteni rendszeres időközönként. Mivel az Azure Storage három rendszerkép tartja, a biztonsági mentés már kevésbé fontos tárolási összeomlás kompenzáló tekintetben. Prioritás okát a megfelelő biztonsági mentési és helyreállítási terv karbantartása sokkal többet lehet kompenzálni a logikai vagy manuális hibákat azáltal, hogy az idő helyreállítási lehetőségei a pont. Így az célja, hogy mindkét használata biztonsági mentések, állítsa vissza az adatbázist vissza egy bizonyos ponton az időben, vagy használhatja a biztonsági másolatokat az Azure használatával ültet be egy másik rendszer a meglévő adatbázis másolásával. 

Annak érdekében, hogy különböző SQL Server tekintse meg az Azure-beli biztonsági mentési lehetőségeket olvassa [biztonsági mentése és visszaállítása az SQL Server az Azure Virtual machines gépeken](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). A cikk számos különböző lehetőségeit ismerteti.

### <a name="manual-backups"></a>Manuális biztonsági mentések
"Manual" biztonsági mentések által végrehajtására több lehetőség közül választhat:

1. Közvetlen csatolt Azure lemezeken hagyományos SQL Server biztonsági mentések végrehajtásához. Ez a módszer az előnnyel jár, hogy a rendszer frissíti a gyorsan visszaállítható biztonsági másolatokat és állíthatja össze az új rendszerek szerint példányait a meglévő SAP-rendszerek
2.  Az SQL Server 2012 CU4 és magasabb biztonsági másolatot készíthet adatbázisok egy Azure storage URL-CÍMÉT.
3.  Biztonságimásolat-pillanatkép készítése Adatbázisfájlokról az Azure Blob Storage-ban. Ez a módszer csak akkor működik, ha az SQL Server adathoz és naplófájlhoz találhatók az Azure blob storage-bA

Az első módszer a jól ismert és a helyszíni világ, valamint sok esetben alkalmazott. Mindazonáltal akkor hagyja, a feladat megoldásához a hosszabb távú biztonsági mentési helyre. Mivel nem szeretné megőrizni a biztonsági mentések 30 vagy több napig a helyileg csatlakoztatott Azure Storage-ban, vagy használata az Azure biztonsági mentési szolgáltatások vagy más harmadik felek biztonsági mentési/helyreállítási eszköz, amely tartalmazza a biztonsági mentések hozzáférési és adatmegőrzési-kezelés van. Vagy létrehozhat az Azure-beli nagyméretű fájlkiszolgálókon ki Windows tárolóhelyek segítségével.

A második módszer van közelebb a cikkben ismertetett [URL-címét az SQL Server biztonsági másolat](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). SQL Server különböző kiadásaiban Ez a funkció különböző módszerekkel rendelkezik. Ezért, tekintse meg a dokumentáció az adott SQL Server-kiadás ellenőrzés. Fontos megjegyezni, hogy ez a cikk felsorolja a nagy mennyiségű korlátozások. Vagy a biztonsági mentés elleni lehetősége van:

- Egy egyetlen oldala az Azure blob, amelyet majd korlátozza a biztonsági másolat mérete 1000 GB. Ez is korlátozza az átviteli sebességet érhet el.
- Több (legfeljebb 64) az Azure a blokkblobok használatát támogatják, amelyek lehetővé teszik egy elméleti biztonsági másolatának mérete legfeljebb 12 TB. Azonban az ügyfél adatbázisok tesztek kimutatta, hogy a tartalék megengedettnél lehet kisebb, mint a elméleti korlátja. Ebben az esetben Ön biztonsági és hozzáférési o megőrzési kezeléséért, valamint a biztonsági mentéseket.


### <a name="automated-backup-for-sql-server"></a>Automatikus biztonsági mentés az SQL Serverhez
Automatikus biztonsági mentés a Windows Azure virtuális Gépen futó SQL Server Standard és Enterprise kiadások egy automatikus biztonsági mentési szolgáltatást biztosít. Ez a szolgáltatás által biztosított a [SQL Server IaaS-ügynök bővítmény](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), amely automatikusan települ az SQL Server Windows virtuálisgép-lemezképek az Azure Portalon. Ha a saját operációsrendszer-lemezképek telepít az SQL Server telepítve van, a Virtuálisgép-bővítmények külön-külön telepíteni szeretné. Ezen szükséges lépések szerepelnek [cikk](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Ez a módszer képességeivel kapcsolatos további részletekért ezekben a cikkekben található:

- Az SQL Server 2014: [automatikus biztonsági mentése az SQL Server 2014 virtuális gépeken (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- Az SQL Server 2016/2017: [biztonsági mentési v2 automatikus az Azure virtuális gépeken (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Vajon a dokumentációt, láthatja, hogy az a Funkciók, az a legújabb SQL Server-kiadások továbbfejlesztett. További részletek az SQL Server automatikus biztonsági másolatok jelennek meg a cikk [SQL Server Managed Backup a Microsoft Azure-bA](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). A biztonsági másolat mérete elméleti határértéke 12 TB.  Az automatikus biztonsági másolatok jó módszer a biztonsági mentési legfeljebb 12 TB méretű is lehet. Több blobok írt párhuzamosan, mivel egy nagyobb, mint 100 MB/s átviteli várható. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Az Azure Backup az SQL Server virtuális gépek
Ez az új módszer az SQL Server biztonsági mentések kínálják 2018. június nyilvános előzetes verzióban az Azure Backup szolgáltatás. A biztonsági mentés az SQL Server metódust ugyanúgy történik, mint más külső eszközöket használ, nevezetesen az SQL Server VSS/VDI felületén stream biztonsági mentések célhelyre is. Ebben az esetben a célhelyen az Azure Recovery Services-tárolót.

Ez a biztonsági mentési módszer több mint részletes leírását, amely hozzáadja a központi biztonsági mentési konfigurációban, figyelés, számos előnye és felügyeleti érhető el [Itt](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Külső biztonsági mentési megoldások
Meglehetősen ügyfelek száma nem újrakezdeni és a saját Azure-on futó SAP-rendszeren részét teljes új biztonsági mentési megoldások bevezetése nem fordulhat elő. Ennek eredményeképpen a meglévő biztonsági mentési megoldások használt és az Azure-ra kiterjesztett szükséges. Kiterjeszti a meglévő biztonsági mentési megoldások általában használhatta is ezen a helyen a főbb beszállítók a legtöbb Azure-bA. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>A Microsoft Azure Marketplace-en kívül az SQL Server-lemezkép használatával
A Microsoft kínál a virtuális gépeket az Azure piactéren, az SQL Server verziói már tartalmazzák. Az SAP-vásárlóknak, akik licencek szükségesek az SQL Server és a Windows ezek a lemezképek használatával lehet, hogy biztosítsák a szükséges licencek kell indítani az SQL Server már telepítve van a virtuális gépek lehetőséget. Az ilyen rendszerképek használatához az SAP, az alábbiakat kell tenni:

* SQL Server-verziók nem próbaverzió költsége magasabb, mint az Azure Marketplace-ről üzembe helyezett egy "Csak Windows" virtuális gép beszerezni. Tekintse meg ezeket a cikkeket hasonlítsa össze az árakat: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> és <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Az SAP által támogatott SQL Server kiadások csak használhatja.
* Az SQL Server-példány, amelyre telepítve van a virtuális gépeket az Azure piactéren kínált rendezése nem áll a rendezést, az SAP NetWeaver az SQL Server-példány futtatásához szükséges. A rendezés módosíthatja, ha az az alábbi szakasz utasításait.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>A Microsoft Windows/SQL Server rendszerű virtuális gép az SQL Server-rendezés módosítása
Miután az SQL Server-lemezképek az Azure Marketplace-en nem állította be, amelyek szükségesek az SAP NetWeaver-alkalmazásaihoz, a rendezést használja az üzembe helyezést követően azonnal módosítani kell. Az SQL Server a rendezés módosítása teheti meg az alábbi lépéseket követve, amint a virtuális gép már telepítve van, és a egy rendszergazda a központilag telepített virtuális gép be tud jelentkezni:

* Nyisson meg egy Windows parancssori ablakban rendszergazdaként.
* Lépjen a C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Hajtsa végre a parancsot: Setup.exe/test/művelet = REBUILDDATABASE InstanceName = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> van a fiók, amely a rendszergazdai fiókként lett definiálva, a virtuális gép telepítésekor az első alkalommal a gyűjteményből.

A folyamat csak kell néhány percet igénybe. Annak érdekében, hogy győződjön meg arról, hogy a lépés befejeződött a megfelelő eredménnyel, hajtsa végre az alábbi lépéseket:

* Nyissa meg az SQL Server Management Studiót.
* Egy lekérdezési ablak megnyitásához.
* Az SQL Server főadatbázisában hajtsa végre a parancsot sp_helpsort.

A kívánt eredményt hasonlóan kell kinéznie:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Különböző az eredmény esetén állítsa le az SAP üzembe helyezése, és vizsgálja meg, miért érdemes a setup parancs volt a várt módon működik. Az üzembe helyezés az SAP NetWeaver-alkalmazásaihoz, mint a korábban említett különböző SQL Server megadására az SQL Server-példány az alakzatot **nem** támogatott.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Az SQL Server magas rendelkezésre állás az SAP az Azure-ban
Az Azure IaaS SAP üzemelő példány az SQL Server üzembe helyezése a magas rendelkezésre állású adatbázis-kezelő réteg hozzáadása számos különböző lehetőségeket rendelkezik. Az [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md) már, az Azure révén egyetlen virtuális gép és a egy Azure rendelkezésre állási csoportban üzembe helyezett virtuális gépek két különböző partícióterheléses SLA-kkal. Feltételezi, a partícióterheléses SLA az éles környezetekben üzemelő példányok, amelyhez szükséges az üzembe helyezés az Azure rendelkezésre állási csoportok felé meghajtó. Ebben az esetben is telepíteni kell egy legalább két virtuális gépet, például egy rendelkezésre állási csoportban. Egy virtuális gép aktív SQL Server-példány fog futni. A virtuális gép a passzív példányt fog futni.

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Az SQL Server fürtözési Windows Scale-out File Server használatával
Windows Server 2016, a Microsoft bevezette [a közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). A tárolóhely közvetlen üzembe helyezésének alapján, az SQL Server FCI fürtszolgáltatás támogatott. Részleteket a cikkben található [konfigurálása SQL Server feladatátvevő fürt-példány az Azure Virtual Machinesben](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). A megoldás a virtuális IP-címét a fürt erőforrásainak kezelésére van szükség, valamint egy Azure load balancert. Az SQL Server-adatbázisfájlok a tárolóhelyek vannak tárolva. Ennélfogva a tekintve, hogy akkor építse fel az Azure Premium Storage alapján Windows tárolóhelyek szüksége lesz. Ez a megoldás a túl hosszú ideig még nem támogatott, mivel nincsenek nem ismert SAP ügyfeleink, akik ebben a megoldásban az SAP termelési forgatókönyvekhez.  

### <a name="sql-server-log-shipping"></a>Az SQL Server-Naplóküldés
Magas rendelkezésre ÁLLÁS módszer, az SQL Server Naplóküldést. Ha a magas rendelkezésre ÁLLÁSÚ konfigurációban részt vevő virtuális gépek névfeloldás működik, nem jelent problémát, és a beállítása az Azure-ban térnek el minden olyan beállítás, amely a helyszínen történik. Érdemes beállítása a Naplóküldést és a Naplóküldést alapelveket. A cikkben található SQL Server Naplóküldést részleteit [kapcsolatos Naplóküldést (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Az SQL Server-naplóküldés funkció alig használja az Azure-ban egy Azure-régióban a magas rendelkezésre állás eléréséhez. Azonban a következő esetekben ügyfelek használta a naplóküldés sikeres együtt az Azure-ral:

- Azure-régióból egy másik Azure-régióba történő vész-helyreállítási helyzetekben
- Vészhelyreállítási konfiguráció helyszínről az Azure-régióba
- Kivágási feladatátvételi forgatókönyvek helyszínről az Azure-bA. Ezekben az esetekben naplóküldés szolgál az új DBMS üzembe helyezés az Azure-ban és a folyamatos éles rendszer helyszíni szinkronizálása. Cutting keresztül időpontjában éles leáll, és arról, hogy a legutóbbi és a legújabb tranzakciónapló biztonsági mentései át lett az Azure DBMS üzembe helyezési történik. Ezután az Azure DBMS üzembe helyezési számára van nyitva éles környezetben.  



### <a name="database-mirroring"></a>Az adatbázis-tükrözés
Az SAP által támogatott adatbázis-tükrözés (tekintse meg az SAP-Jegyzetnek [965908]) definiálása az SAP-kapcsolati karakterláncot a feladatátvételi partner támaszkodik. A létesítmények közötti esetek feltételezzük, hogy a két virtuális gép ugyanabban a tartományban vannak, és, hogy a felhasználói környezet a két SQL Server-példányokat, valamint a tartományi felhasználók csoportban futnak, és részt vevő két SQL Server-példányokat a megfelelő jogosultságokkal rendelkezik. Ezért a beállítása az Azure adatbázis-tükrözés nem egy tipikus helyszíni telepítés/konfigurációs eltérnek a.

Kezdődően kizárólag felhőalapú telepítések esetén a legegyszerűbb módszer, hogy egy másik tartomány beállítása az Azure-ban, ezek az adatbázis-kezelő virtuális gépek (és ideális dedikált SAP virtuális gépek) egy tartományon belül.

Ha egy tartomány nem lehetséges, egy is használható tanúsítványok esetében az adatbázis-tükrözési végpont itt leírtak szerint: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Oktatóanyagnak beállítása az adatbázis-tükrözés az Azure-ban, itt található: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Always On támogatott SAP helyszíni, (tekintse meg az SAP-Jegyzetnek [1772688]), együtt az SAP az Azure-ban támogatott. Néhány szempontot, mobilmegoldások üzembe helyezéséhez az SQL Server rendelkezésre állási csoport figyelőjét (nem tévesztendő össze az Azure rendelkezésre állási csoport), mivel az Azure ezen a ponton a egyszerre nem engedélyezi a helyszíni lehetséges, mert az AD/DNS objektum létrehozását. Ezért bizonyos másik telepítési lépések szükségesek, hogy az adott viselkedését az Azure.

Néhány szempontot, egy rendelkezésre állási csoport figyelőjének használatával a következők:

* Egy rendelkezésre állási csoport figyelőjének használata csak akkor lehetséges, a Windows Server 2012 vagy újabb vendég operációs rendszer a virtuális gép. A Windows Server 2012 rendszerhez kell győződjön meg arról, hogy érvényesek-e a javítást: <https://support.microsoft.com/kb/2854082> 
* A Windows Server 2008 R2, a javítás nem létezik, és mindig bekapcsolva kell egy feladatátvételi partner a kapcsolat-karakterlánc megadásával az adatbázis-tükrözés gazdagépprofilokkal megegyező módon használható (keresztül az SAP default.pfl paraméter adatbázisok/mss/kiszolgáló – lásd a SAP-Jegyzetnek [965908]).
* Egy rendelkezésre állási csoport figyelőjének használata esetén az adatbázis-beli virtuális gépek csatlakoznia kell egy dedikált terheléselosztóhoz. Annak érdekében, hogy, hogy az Azure van-e azokban az esetekben, ahol mindkét virtuális gép lehet átváltani állítsa le az új IP-címet rendelni, egy kell hozzárendelése statikus IP-címeket a hálózati adapterek közül virtuális gépek (meghatározása a statikus IP-cím leírtmindigakonfigurációban[ez] [ virtual-networks-reserved-private-ip] cikk)
* Megadása kötelező, ha létrehozásához a WSFC-fürt konfigurációját, ahol a fürt van szüksége egy speciális IP-cím rendelve, mert a jelenlegi működését az Azure rendelne a fürt neve azonos IP-címét, a csomópont a fürthöz a rendszer létrehozza a speciális lépésből áll. Ez azt jelenti, hogy a fürt egy másik IP-cím hozzárendelése egy manuális lépés kell elvégezni.
* A rendelkezésre állási csoport figyelőjének fog létrehozni az Azure-ban TCP/IP-végpontokkal, amely az elsődleges és másodlagos replikák rendelkezésre állási csoport futó, a virtuális gépekhez vannak hozzárendelve.
* Előfordulhat, hogy biztonságos hozzáférés-vezérlési ezeket a végpontokat kell.

Always On Azure virtuális gépeken futó SQL Serverrel történő telepítéséről részletes dokumentációt sorolja fel, például:

- [Bemutatkozik az SQL Server Always On rendelkezésre állási csoportok az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Az Azure-beli virtuális gépek különböző régiókban Always On rendelkezésre állási csoport konfigurálása](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Egy Always On rendelkezésre állási csoport terheléselosztó konfigurálása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Ha az Azure load balancert a virtuális IP-cím a rendelkezésre állási csoport figyelőjének konfigurál, ügyeljen arra, hogy van-e konfigurálva a DirectServerReturn. Ez a beállítás csökkenti a hálózat, a SAP alkalmazás réteget és az adatbázis-kezelő réteg közötti körbejárási késés. 

SQL Server Always On a leggyakrabban használt magas rendelkezésre állású és vészhelyreállítási funkciók az Azure-beli SAP számítási feladatok üzembe helyezéséhez használt. A legtöbb ügyfél mindig a magas rendelkezésre állás egy adott Azure-régión belül használja. Ha a központi telepítés két csomópont csak korlátozott, a hálózati kapcsolatot két lehetősége van:

- A rendelkezésre állási csoport figyelőjének használatával. A rendelkezésre állási csoport figyelőjét, az Ön telepítéséhez szükséges egy Azure load balancert. Ez általában a központi telepítés az alapértelmezett mód. SAP-alkalmazások úgy lesz beállítva, a rendelkezésre állási csoport figyelőjének és nem egyetlen csomópont elleni kapcsolódni
- Az SQL Server adatbázis-tükrözési kapcsolat paraméterek használatával. Ebben az esetben, konfigurálnia kell az SAP-alkalmazások csatlakoztatásához oly módon, ahol mindkét csomópont nevek lesznek elnevezve. Ilyen egy SAP ügyféloldali konfiguráció pontos részleteit a SAP-Jegyzetnek dokumentált [#965908](https://launchpad.support.sap.com/#/notes/965908). Ez a beállítás használata esetén nem szükséges egy rendelkezésre állási csoport kérésfigyelőjének konfigurálása kell. És az adott nem Azure terheléselosztó az SQL Server magas rendelkezésre álláshoz. Ennek eredményeképpen a hálózati késés, a SAP alkalmazás réteget és az adatbázis-kezelő réteg között kisebb, mivel a bejövő forgalmat, az SQL Server-példány nem irányít rá az Azure load balanceren keresztül. Azonban a már ismert, ezt a beállítást csak akkor működik, ha a két példány több rendelkezésre állási csoport korlátozza. 

Több ügyfél között az Azure-régiók további vészhelyreállítási funkciók az SQL Server Always On funkció vannak kihasználva. Ügyfél segítségével is lehetővé teszi egy másodlagos másodpéldány a biztonsági mentéshez. 

## <a name="sql-server-transparent-data-encryption"></a>Az SQL Server transzparens adattitkosítás
Egy SQL Servert használó ügyfelek száma [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) amikor az SAP az SQL Server telepítése adatbázisok az Azure-ban. Az SAP teljes mértékben támogatott az SQL Server TDE funkcióit (lásd az SAP-Jegyzetnek [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>TDE SQL-kiszolgáló alkalmazása
Azokban az esetekben, ahol egy másik adatbázis-kezelő, a helyszínen futó Azure-ban futó Windows vagy SQL Server a heterogén áttelepítés végrehajtása az üres céllal adatbázis az SQL Server előre kell létrehoznia. Következő lépésként, az SQL Server TDE funkció lenne alkalmazhatja. Amíg az éles rendszer helyi továbbra is futnak. Ez a sorozat a végrehajtani kívánt oka, hogy az üres adatbázis titkosító is eltarthat még egy darabig. Az SAP-importálási folyamatokat szeretne majd az adatok importálása a titkosított adatbázis az állásidő fázisban. Gyűjteményértékelést kelljen végezni egy titkosított adatbázis való importálásakor, mint az adatbázis titkosító után az exportálási folyamat fázisai a le módon alacsonyabb idő hatással van ideje fázisban. Negatív teljesen, ha a alkalmazni a TDE az adatbázis felett futó SAP számítási feladat a tett kísérlet során. Ezért javaslat van való kezelése központi telepítését a TDE, egy tevékenységgel, amely az SAP számítási feladatok az adott adatbázis nélkül kell elvégezni.

Azokban az esetekben, ahol áthelyezi az SAP az SQL Server-adatbázisok helyszínről az Azure-bA javasoljuk, hogy mely infrastruktúrát a alkalmazni leggyorsabb titkosítási beszerezheti a teszteléshez. Ezt tartsa szem előtt ezeket a tények:

- Hány szál használható adattitkosítás alkalmazandó az adatbázist nem lehet definiálni. Szálak száma a szolgáltatás az SQL Server adathoz és naplófájlhoz be vannak elosztva kötetek száma majorly függ. Azt jelenti, hogy a különböző kötetek (meghajtó-betűjelek), a több szál kell elvégezni a titkosítást fog párhuzamosan kell bekapcsolni. Ilyen konfiguráció egy kicsit javaslattal korábbi lemez konfigurációja az egy-vagy Azure virtuális gépeken futó SQL Server adatbázis-fájlok a tárolóhelyek kevesebb ellentmond. Egy konfigurációt a kötetek kis számú szál végrehajtása a titkosítás kis számú vezetne. Egy egyetlen szálból titkosítása van olvasása 64 KB-os egységek, titkosítja, és majd írja be a tranzakciós naplófájlt, amely arról tájékoztat, hogy a mértékben titkosítva lett egy rekordot. Ennek eredményeképpen a tranzakciós napló terhelése mérsékelt.
- Az SQL Server régebbi kiadásokban biztonságimásolat-tömörítési funkciók nem tudták beszerezni hatékonyság többé, ha az SQL Server-adatbázis titkosított. Ezt a viselkedést sikerült fejlesztése problémába, amikor a csomag volt, hogy titkosítani az SQL Server adatbázis helyi, és másolja be a biztonsági másolat az Azure-ba, állítsa vissza az adatbázist az Azure-ban. Az SQL Server biztonságimásolat-tömörítési funkciók általában a tömörítési aránytól tényező 4 éri el.
- Az SQL Server 2016 az SQL Server új funkció, amely lehetővé teszi a titkosított adatbázis, valamint tömöríti és hatékonyan vezetett be. Lásd: [a blogok](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) bizonyos részleteket.
 
Az alkalmazás kevés SAP számítási feladatok csak a nem a TDE titkosítási kezelésére, tesztelje az adott konfigurációban meg, hogy-e jobb a alkalmazni a TDE az SAP adatbázis helyi vagy ehhez az Azure-ban. Az Azure-ban, természetesen magasabb fokú rugalmasság szempontjából fölösleges üzembe helyezési infrastruktúra és -csökkentése az infrastruktúra TDE van alkalmazása után.

### <a name="using-azure-key-vault"></a>Az Azure Key Vault használatával
Az Azure szolgáltatás kínál egy [Key Vault](https://azure.microsoft.com/services/key-vault/) tárolja a titkosítási kulcsokat. SQL Server, a másik oldalon egy összekötőt, és kihasználhatja az Azure Key Vault a TDE-tanúsítványok tárolását is kínál.

További részletek az Azure Key Vault használata az SQL Server TDE sorolja fel, például:

- [Bővíthető kulcskezelés az Azure Key Vault (SQL Server) a](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [Az SQL Server TDE bővíthető kulcskezelés a az Azure Key Vault - beállítási lépéseket](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [Az SQL Server-összekötő karbantartást és a hibaelhárítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [További kérdései vannak az ügyfeleinek, az SQL Server transzparens adattitkosításának – TDE + az Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Az SQL Server TDE, különösen az Azure key Vault használ, javasoljuk, hogy használja a legújabb javításokat az SQL Server 2014, SQL Server 2016 és az SQL Server 2017-ben. Oka, hogy az ügyfelek visszajelzései miatt, optimalizálás, és javításokat a kódhoz van alkalmazva. Tegyük fel, ellenőrizze [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Az SQL Server általános az SAP az Azure összegzése
Ez az útmutató számos ajánlást, és olvassa, egynél több alkalommal az Azure-alapú tervezése előtt. Általánosságban véve, ügyeljen arra, hogy a felső általános adatbázis-kezelő kövesse az Azure-ra vonatkozó javaslatok:

1. Használja a legújabb DBMS kiadását, például az SQL Server 2017, amely rendelkezik a legtöbb előnyeit az Azure-ban. 
2. Tervezze meg gondosan az SAP-rendszer környezetét az Azure-ban, az adatok fájl elrendezése és az Azure-korlátozások elosztása érdekében:
   * Nem túl sok lemezzel rendelkezik, de van-e elegendő annak érdekében, hogy a szükséges iops-érték érhető el.
   * Ha nem használ Managed Disks, ne felejtse el iops-t is korlátozott egy Azure Storage-fiókot, hogy, hogy a Storage-fiókok korlátozva, minden egyes Azure-előfizetésen belül ([további részleteket][azure-subscription-service-limits]). 
   * Csak a lemezeken, ha egy nagyobb átviteli sebességet érhet el kell stripe.
3. Ne telepítse a szoftvert, vagy telepítse azokat a fájlokat, a nem állandó és a semmit a meghajtón egy Windows újraindításkor elveszett adatmegőrzés a D:\ meghajtóra szükség van.
4. Ne használja a lemezek gyorsítótárazása az Azure standard szintű tárolást.
5. Ne használja az Azure georeplikált Azure standard szintű Storage-fiókok.  Az adatbázis-kezelő számítási feladatok esetében használja a helyileg redundáns.
6. Adatbázis-adatok replikálása a DBMS gyártója által biztosított magas rendelkezésre ÁLLÁS/Vészhelyreállítás megoldást használni.
7. Mindig névfeloldás használata, ne használjon IP-címeket.
8. Az SQL Server TDE használja, a legújabb SQL Server-javítások alkalmazása.
9. A legmagasabb lehetséges adatbázis-tömörítést használni. Ez az oldal tömörítési SQL Serverhez.
10. Ügyeljen arra, hogy az SQL Server-rendszerképek használata az Azure Marketplace-ről. Ha egy SQL-kiszolgálót használ, módosítania kell a példány rendezési rajta minden olyan SAP NetWeaver rendszer telepítése előtt.
11. Telepítse és konfigurálja az SAP gazdagép figyelése az Azure-ban leírtak szerint [üzembe helyezési útmutató][deployment-guide].