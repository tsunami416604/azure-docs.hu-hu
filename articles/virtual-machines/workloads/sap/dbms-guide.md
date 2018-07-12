---
title: Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése |} A Microsoft Docs
description: Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
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
ms.openlocfilehash: 2caa9a5137edd4e012adf704c01dc5c470e1bb51
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972444"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez az útmutató a végrehajtási és a Microsoft Azure az SAP-szoftverek üzembe helyezésére dokumentáció részét képezi. Ez az útmutató elolvasása, előtt olvassa el a [tervezési és megvalósítási útmutató][planning-guide]. Ez a dokumentum ismerteti a különböző relációs adatbázis felügyeleti rendszerek (RDBMS) és a kapcsolódó termékek együtt az SAP a Microsoft Azure-beli virtuális gépeken (VM) használatával az Azure infrastruktúra-szolgáltatás (IaaS) képességeket telepítését.

A tanulmány az SAP-telepítési dokumentációt és SAP-megjegyzések, amelyek tartalmazzák a telepítések és SAP-szoftverek központi telepítései az elsődleges erőforrásokat a megadott platformok egészíti ki.

## <a name="general-considerations"></a>Általános megfontolások
Ebben a fejezetben megfontolásokkal kapcsolatos SAP DBMS-rendszerek Azure-beli virtuális gépeken futó jelennek meg. Ez a fejezet az adott DBMS-rendszerekre való néhány hivatkozások találhatók. Ehelyett az adott DBMS-rendszerek kezelése a tanulmány után ez a fejezet.

### <a name="definitions-upfront"></a>Előre definíciók
A dokumentumban a következő kifejezéseket használjuk:

* IaaS: Infrastruktúra-szolgáltatás.
* PaaS: Platformszolgáltatás.
* SaaS: Szolgáltatott szoftver.
* Az SAP-összetevőt: egyedi SAP alkalmazások például az ECC, a BW, a megoldás Manager vagy a EP  Az SAP-összetevők hagyományos ABAP és Java-technológiák vagy egy nem NetWeaver-alapú alkalmazás, például az üzleti objektumok alapulhat.
* Az SAP-környezet: egy vagy több SAP összetevő logikusan például fejlesztési, QAS, képzés, DR vagy éles üzleti függvény végrehajtásához.
* SAP-rendszeren: Ez vonatkozik a teljes SAP-eszközök az ügyfél informatikai környezetét. Az SAP-rendszeren, tartalmazza az összes éles környezetben, és nem éles környezetekben.
* SAP-rendszerhez: Adatbázis-kezelő réteg és kombinációja alkalmazásréteg, például egy fejlesztőrendszerrel SAP ERP, SAP BW tesztgépen, SAP CRM-előállítási rendszerek stb. Az Azure-környezetek nem támogatott ezen két réteg között a helyszíni és az Azure osztani. Ez azt jelenti, hogy az SAP-rendszer vagy az üzembe helyezett helyszíni, vagy azt az Azure-ban üzemel. Azonban telepíthet egy Azure-ban vagy a helyszíni SAP-rendszeren, a különböző rendszerek. Például sikerült telepíteni az SAP CRM fejlesztési és rendszerek tesztelése az Azure azonban az SAP CRM rendszert a helyi környezetben.
* Kizárólag felhőalapú üzembe helyezés: egy központi telepítést, ahol az Azure-előfizetés nem kapcsolódik site-to-site vagy a helyszíni hálózati infrastruktúrát ExpressRoute-kapcsolaton keresztül. A gyakori Azure-dokumentáció az ilyen típusú központi telepítések is rendelkezésre állnak, "csak Felhőbeli" című szakasz ismertet. Ezzel a módszerrel telepített virtuális gépek az interneten és az Azure-ban a virtuális gépekhez rendelt nyilvános internetes végpontok keresztül érhetők el. A helyszíni Active Directory (AD) és a DNS nem lett kiterjesztve, az Azure-telepítések az ilyen típusú. Ezért a virtuális gépek nem részei a helyszíni Active Directoryban. Megjegyzés: Ebben a dokumentumban csak felhőalapú telepítések futtató kizárólag az Azure-ban anélkül, hogy az Active Directory és a névfeloldás kiterjesztése a helyszíni nyilvános felhőbe teljes SAP környezetünk vannak meghatározva. Kizárólag felhőalapú konfigurációk nem támogatottak az éles SAP-rendszereit vagy konfigurációk, ahol az SAP STM vagy más helyszíni erőforrásokat kell használható üzemeltetett Azure-ra és a hozzá tartozó a helyszíni erőforrások az SAP-rendszerek között.
* Létesítmények közötti: Egy forgatókönyvet, ahol a virtuális gépek Azure-előfizetéssel, amely rendelkezik a site-to-site, többhelyes vagy az ExpressRoute-kapcsolat a helyszíni kommunikálhassanak és az Azure közötti üzembe ismerteti. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések is rendelkezésre állnak, létesítmények közötti forgatókönyvek leírtak szerint. A kapcsolat az az oka, hogy kiterjesztése a helyszíni tartományokra, a helyszíni Active Directory és a helyszíni DNS az Azure-bA. Az Azure-előfizetés objektumok kiterjed a helyszíni környezet változásaihoz. Ez a bővítmény kapcsolatban, a virtuális gépek a helyszíni tartomány része lehet. Tartományi felhasználókat a helyszíni tartomány férhet hozzá a kiszolgálókat, és futtathatja a szolgáltatások a virtuális gépeken (például adatbázis-kezelő szolgáltatások). Virtuális gépek közötti kommunikációt és a névfeloldás üzembe helyezte a helyszínen és az Azure-ban üzembe helyezett virtuális gépek lehetséges. Várhatóan ez lesz a leggyakoribb forgatókönyv üzembe helyezéséhez SAP-eszközök az Azure-ban. További információkért lásd: [Ez a cikk] [ vpn-gateway-cross-premises-options] és [Ez a cikk][vpn-gateway-site-to-site-create].

> [!NOTE]
> Létesítmények közötti SAP-rendszereit, amelyeknél a helyszíni tartomány tagjai az Azure Virtual machines gépeken futó SAP-rendszerek központi telepítései éles SAP-rendszerek támogatottak. Létesítmények közötti konfigurációkat üzembe helyezésének részek támogatottak, vagy hajtsa végre az SAP-környezetünk az Azure-bA. A teljes SAP-rendszeren az Azure-ban futó szükséges ezeket a virtuális gépek is tagja a helyszíni tartomány és a reklámok érdekében. A-dokumentáció korábbi verziói a beszéltünk hibrid-informatikai alkalmazási helyzetek, ahol az előfizetési időszak *hibrid* rootolva van, az a tény, hogy nincs-e a létesítmények közötti kapcsolatok a helyszíni és az Azure között. Ebben az esetben *hibrid* azt is jelenti, hogy a virtuális gépek az Azure-ban a helyszíni Active Directory részét képezik.
> 
> 

Bizonyos Microsoft-dokumentációt létesítmények közötti forgatókönyv különösen az adatbázis-kezelő magas rendelkezésre ÁLLÁS konfigurációk esetén egy kicsit másképp ismerteti. Az SAP-kapcsolatos dokumentumok a létesítmények közötti forgatókönyv forrni kezd a site-to-site vagy privát (ExpressRoute) megszakadt a kapcsolat le, és arra, hogy az SAP fekvő oszlik el a helyszíni és az Azure között.

### <a name="resources"></a>További források
A következő útmutatók érhetők el Azure-beli SAP-környezetekhez:

* [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver][planning-guide]
* [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Azure virtuális gépek DBMS üzembe helyezési SAP NetWeaver (Ez a dokumentum)][dbms-guide]

Az alábbi SAP-megjegyzések kapcsolódó Azure-beli SAP:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok |
| [2015553] |A Microsoft Azure-beli SAP: támogatás előfeltételei |
| [1999351] |Továbbfejlesztett Azure Monitoring for SAP hibaelhárítási |
| [2178632] |Metrikák figyelése a Microsoft Azure-beli SAP-kulcs |
| [1409604] |A Windows virtualizálási: Enhanced Monitoring |
| [2191498] |SAP használata Linux az Azure-ral: Enhanced Monitoring |
| [2039619] |A Microsoft Azure-ban, az Oracle-adatbázishoz az SAP-alkalmazások: támogatott termékek és termékverziók |
| [2233094] |DB6: Azure-ban, IBM DB2-höz használatával Linux, UNIX és a Windows - további információ az SAP-alkalmazások |
| [2243692] |A Microsoft Azure (IaaS) virtuális gép Linux: SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési jegyzetek |
| [2002167] |Red Hat Enterprise Linux 7.x: telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítése és frissítése |
| [1597355] |Linux-lapozóterület javaslat |
| [2171857] |Oracle Database 12c - fájlrendszer Linux-támogatás |
| [1114181] |Oracle Database 11g - fájlrendszer Linux-támogatás |


Emellett olvassa el a [Állapotváltozás Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden SAP-megjegyzések, amely tartalmazza a Linuxhoz készült.

Rendelkeznie kell a Microsoft Azure-architektúra és a Microsoft Azure Virtual Machines telepített és üzemeltetett hogyan ismeretét. További információkat: <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Tudjuk **nem** megvizsgálja a Microsoft Azure Platform, a Platformszolgáltatás (PaaS) ajánlatok, a Microsoft Azure platform. Ez a tanulmány tárgya egy adatbázis-kezelő rendszer (DBMS) a Microsoft Azure Virtual Machines szolgáltatásban futó (IaaS), az adatbázis-kezelő futtatná a helyszíni környezetben. Adatbázis-képességeket és funkciók ezen két ajánlatok között amelyek nagyon eltérőek, és meg nem nem lenne szerencsés egymással. Lásd még: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Mivel azt a rendszer megvizsgálja az IaaS, általában a Windows, Linux- és adatbázis-kezelő rendszer telepítési és konfigurációs lényegében ugyanaz, mint a virtuális gépekhez vagy telepítenie a helyi operációs rendszer nélküli gépen. Azonban néhány architektúra és a rendszer felügyeleti megvalósítási döntést kell meghoznia, amelyek különböző IaaS használatakor. A jelen dokumentum célja, hogy ismertesse specifikus architekturális és fel kell készülnie az IaaS használatakor system management különbségek.

Általában a teljes különbséggel, hogy ez a tanulmány bemutatja a következő területekre:

* SAP-rendszereit, ellenőrizze, hogy a megfelelő adatokat a megfelelő Virtuálisgép-lemez elrendezése tervezési elrendezés fájlt, és a számítási feladatok számára elegendő IOPS érhető el.
* Hálózati megfontolások IaaS használata esetén.
* Fogja optimalizálhatja az adatbázis elrendezése az adott adatbázis-szolgáltatása.
* Az IaaS biztonsági mentési és helyreállítási szempontjai.
* Különböző típusú központi telepítési lemezképek használatával.
* Az Azure iaas-beli magas rendelkezésre állás.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Egy RDBMS üzembe helyezési struktúrája
Annak érdekében, hogy kövesse az ebben a fejezetben, érdekében fontos megérteni, milyen a bemutatott [ez] [ deployment-guide-3] fejezete az [üzembe helyezési útmutató][deployment-guide]. Kapcsolatos tudnivalók a különböző Virtuálisgép-sorozat, és azok és különbségek az Azure Standard és prémium szintű Storage kell értelmezni, és ez a fejezet elolvasása előtt ismert.

2015. március, amíg olyan lemezeket, az operációs rendszer mérete 127 GB korlátozva lett. Ez a korlátozás van szüntetni a 2015. március (további információkért tekintse <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Az operációs rendszert tartalmazó lemezeket az onnan akkora, mint bármely más lemez is rendelkezhet. Azonban hogy továbbra is inkább egy üzembe helyezési, ahol az operációs rendszer, az adatbázis-kezelő és végleges SAP bináris fájlokat nem azonosak az adatbázisfájlok szerkezete. Így várhatóan az Azure Virtual Machines szolgáltatásban futó SAP-rendszerek van telepítve az operációs rendszer, adatbázis-kezelési rendszer futtatható fájljainak és SAP végrehajtható fájlok alap virtuális gép (vagy lemez). Az adatbázis-kezelő adathoz és naplófájlhoz a különálló lemezek (Standard vagy prémium szintű tároló) Azure Storage-ban tárolt és az eredeti Azure operációs rendszer lemezképének virtuális gép logikai lemezként csatolt. 

Az Azure Standard vagy prémium szintű Storage (például használatával a DS vagy GS sorozatú virtuális gépek) kihasználva van függő egyéb kvóták az Azure-ban, amely vannak dokumentálva [itt (Linux)] [ virtual-machines-sizes-linux] és [ Itt (Windows)][virtual-machines-sizes-windows]. A lemez elrendezése tervezésekor meg kell keresnie a legjobb egyensúlyt a kvóták a következő elemek:

* Az adatfájlok számát.
* A lemezek, amelyek tartalmazzák a fájlok számát.
* Az egyetlen lemez iops-érték kvóták.
* Lemezenként fájlmegosztásra.
* A lehetséges Virtuálisgép-méret szerint további adatlemezek száma.
* A teljes tárterületek átviteli sebességének egy virtuális Gépet biztosít.

Azure-adatlemez IOPS kvóta kényszeríti. Az Azure Standard Storage és a Premium Storage üzemeltetett lemezeket kvóta eltérőek. I/o-késések is jelentős mértékben eltérnek a Premium Storage tényezők továbbítása a jobb i/o-késések a két tárolási típusok közötti. A virtuális gépek különböző típusairól mindegyike rendelkezik egy korlátozott számú adatlemezeket, amelynek a csatolni. Egy másik korlátozás, hogy csak bizonyos virtuális gépek típusai kihasználhatják az Azure Premium Storage. Ez azt jelenti, hogy egy bizonyos Virtuálisgép-típusra vonatkozó döntést előfordulhat, hogy nem csak alapján vezeti a Processzor- és követelményeket, hanem által az IOPS, késés és a lemez, amely általában vannak méretezve, a lemezek számát vagy a prémium szintű tárolólemezeket típusát az átviteli sebességet megkövetelő. Kifejezetten a Premium Storage a lemez mérete is valószínűleg szabja IOPS és átviteli sebesség érhető el, hogy az egyes lemezek szükséges számát.

Az a tény, hogy a teljes IOPS arány csatlakoztatva a lemezek számát, és a virtuális gép méretét az összes kapcsolódnak egymáshoz, előfordulhat, hogy kell lennie, mint a helyszíni telepítéshez az SAP a rendszer egy Azure konfigurálása. Az IOPS-korlátok Logikaiegység-számai szerinti a helyszíni telepítéseknél általában konfigurálható. Mivel az Azure Storage ezek korlátai a rögzített vagy a lemez típusát prémium szintű Storage függ. Így a helyszíni üzemelő láthatjuk, speciális végrehajtható fájlok, például SAP, és az adatbázis-kezelő vagy a speciális kötetet a ideiglenes adatbázisokhoz vagy olyan táblázat tárolóhelyek számos különböző kötetet használ adatbázis-kiszolgálók olyan felhasználói konfigurációk. Ha például egy helyszíni rendszer az Azure-ba kerül, azt vezethet lehetséges IOPS sávszélesség feleslegesen használna ami pazarolja a végrehajtható fájlok vagy adatbázisok, amely bármelyik vagy iops-érték nem egy sok ne végezze el a lemez által. Ezért az Azure-beli virtuális gépeken azt javasoljuk, hogy az adatbázis-kezelő és az SAP végrehajtható fájlok telepítését az operációsrendszer-lemez Ha lehetséges.

Az elhelyezési az adatbázisfájlokat és a naplófájlok és a használt, az Azure Storage IOPS, késés és átviteli sebességet megkövetelő lehet definiálni. Annak érdekében, hogy elegendő IOPS, a tranzakciós napló rendelkezik, előfordulhat, hogy kényszerített több lemez esetében a tranzakciós naplófájlt használhatja, vagy használjon nagyobb prémium szintű Storage-lemez. Ebben az esetben egy lenne hozhat létre egy szoftveres RAID (a példában a Windows Storage készlet for Windows vagy MDADM és LVM (a Logical Volume Manager) Linux rendszeren) a lemezek, a tranzakciós napló tartalmaznak.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Egy Azure virtuális Gépen a D:\ meghajtón egy nem megőrzött meghajtót, az Azure-beli számítási csomópont által az egyes helyi lemezek biztonsági mentése. Mivel a szolgáltatás nem megőrzött, akkor ez azt jelenti, hogy a tartalom a D:\ meghajtóra végzett módosítások elvész, amikor a virtuális gép újraindul. "Végrehajtott módosítások" alatt azt értjük mentett fájlokat, a létrehozott címtárakat, a telepített alkalmazásokat, stb.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux rendszerű Azure virtuális gépek automatikusan, amely az Azure számítási csomópont helyi lemezek által támogatott nem megőrzött meghajtó /mnt/resource meghajtót csatlakoztatni. Mivel a szolgáltatás nem megőrzött, akkor ez azt jelenti, hogy /mnt/resource tartalom végzett módosítások elvesznek, ha a virtuális gép újraindul. Végrehajtott módosítások értjük, mentett fájlokat, a létrehozott címtárakat, a telepített alkalmazásokat, stb.
> 
> 

- - -
Függ az Azure Virtuálisgép-sorozat, a számítási csomóponton a helyi lemezek megjelenítése a különböző teljesítményére, ami például kategóriába sorolhatók:

* A0-A7: Nagyon korlátozott teljesítményét. Nem használható a windows lapozófájlja túl az összes adat
* A8 – A11: Nagyon jó teljesítményt nyújt, és néhány tízezer IOPS és a > 1GB/s átviteli sebesség
* A D-sorozat: Nagyon jó teljesítményt nyújt, és néhány tízezer IOPS és a > 1GB/s átviteli sebesség
* DS-sorozat: Nagyon jó teljesítményt nyújt, és néhány tízezer IOPS és a > 1GB/s átviteli sebesség
* A G-sorozat: Nagyon jó teljesítményt nyújt, és néhány tízezer IOPS és a > 1GB/s átviteli sebesség
* A GS-sorozat: Nagyon jó teljesítményt nyújt, és néhány tízezer IOPS és a > 1GB/s átviteli sebesség

A fenti utasítások a virtuális gépek típusai, az SAP által minősített lépnek életbe. Egyes adatbázis-kezelő funkciók – például a tempdb vagy ideiglenes táblán terület szerint használja ki a Virtuálisgép-sorozat kiváló IOPS és átviteli sebesség jogosultak.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Virtuális gépek és az adatlemezek gyorsítótárazás
Ha hozunk létre, az adatlemezeket a portálon keresztül, vagy hogy csatlakoztatási feltöltött lemezek virtuális gépekhez, azt kiválaszthatja a i/o-forgalom a virtuális gép és az Azure storage-ban található lemezek közötti gyorsítótárba kerüljenek-e. Az Azure Standard és prémium szintű Storage két különböző technológiák használata az ilyen típusú gyorsítótár. Mindkét esetben maga a gyorsítótár lenne biztonsági az ideiglenes lemezt (Windows a D:\) vagy /mnt/resource Linux rendszeren a virtuális gép által használt azonos meghajtókat a lemezt.

Az Azure standard szintű Storage a lehetséges gyorsítótár-típusok a következők:

* Nincs gyorsítótárazás
* Olvasási gyorsítótárazás
* Olvasási és írási gyorsítótárazás

Annak érdekében, hogy egységes és determinisztikus teljesítmény eléréséhez, állítsa be a gyorsítótárazás az Azure standard szintű tárolást tartalmazó összes lemezek **DBMS kapcsolódó adatok fájlok, a naplófájlok és hely a tábla "NONE"**. A virtuális gép gyorsítótárazását az alapértelmezett marad.

Az Azure Premium Storage a következő gyorsítótárazási lehetőségek érhetők el:

* Nincs gyorsítótárazás
* Olvasási gyorsítótárazás

Az Azure Premium Storage esetében javasoljuk, hogy kihasználhatja **olvasási gyorsítótárazás az adatfájlok** az SAP adatbázis és a kiválasztott **naplófájlokhoz, a lemezek gyorsítótárazása**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Szoftveres RAID
Már a fent meghatározott iops-érték között konfigurálhatja lemezek száma az adatbázisfájlok számára szükséges számát egyensúlyba hozni kell, és a maximális iops-t egy Azure virtuális gép / lemez vagy a prémium szintű tárolólemez-típusba biztosít. Lemezek keresztül az IOPS-terhelés kezelésére legegyszerűbben hozhat létre szoftveres RAID különböző lemezek keresztül. Ezután elhelyezheti egy, az SAP DBMS adatok fájlok száma a LUN-okra faragottnak ki a szoftveres RAID. A követelményeknek, akkor érdemes megfontolni a Premium Storage használatát, valamint két három óta függ másik Premium Storage-lemez biztosítanak, mint a lemezek a standard szintű Storage-alapú magasabb IOPS-kvóta. Az Azure Premium Storage által biztosított jelentős jobb i/o késés mellett. 

Ugyanez vonatkozik a különböző DBMS-rendszerek a tranzakciós naplóban. A legtöbb Tlog további fájlok felvételével, nem segít, mert egyszerre csak egy, a fájlok írási DBMS-rendszerekre. Ha magasabb iops-érték érvényes van szükség, mint az egyetlen standard szintű Storage-alapú lemez biztosíthat, akkor is stripe-több standard szintű tárolólemezek keresztül, vagy használhatja a egy nagyobb prémium szintű tárolólemez-típusba, amely magasabb iops-érték érvényes túl is biztosít tényezők alacsonyabb késést az írási I / Az operációs rendszer, a tranzakciós naplóban.

Az Azure-környezetek, amelyek lenne alkalmazást egy szoftveres RAID használatával észlelt helyzetek a következők:

* Napló vagy visszaállíthatja a tranzakciónapló további iops-t, mint az Azure biztosít egyetlen lemezre van szükség. Ahogy korábban említettük a logikai egység több lemez használatával egy szoftveres RAID keresztül létrehozásával megoldhatók.
* I/o számítási feladatok eloszlása egyenletlen a különböző adatokat a fájlokat az SAP-adatbázist. Ebben az esetben egy tapasztalhatnak a kvóta lenyomásával meglehetősen gyakran egy-egy adatfájlt. Mivel a többi adatfájlok még nem kapják meg közeli egyetlen lemez iops-érték kvótáját. Ebben az esetben a legegyszerűbb megoldás az, ha egy logikai egység több lemez egy szoftveres RAID használatával a build. 
* Mi a pontos i/o-munkaterhelés adatok fájlonként, és csak nagyjából tudja, hogy mi a teljes IOPS számítási feladatnak, szemben az adatbázis-kezelő nem tudja. Legegyszerűbb tennie, hogy hozhat létre egy LUN-t egy szoftveres RAID segítségével. Ezen a logikai Egységen mögött több lemezt a kvóták összege kell majd teljesítése ismert IOPS sebessége.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Azt javasoljuk, hogy a Windows tárolóhelyek segítségével, ha futtatja a Windows Server 2012 vagy újabb. Legyen hatékonyabb, mint a Windows korábbi verzióiban a Windows szétosztottsága befolyásolhatja. Szüksége lehet hozhat létre a Tárolókészletek Windows és a tárolóhelyek PowerShell-parancsok használata a Windows Server 2012 operációs rendszer esetén. A PowerShell-parancsok itt található <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Csak a MDADM és az LVM (a Logical Volume Manager) hozhat létre egy szoftveres RAID Linux rendszeren támogatott. További információkért olvassa el a következő cikkeket:
> 
> * [Szoftveres RAID linuxon konfigurálása] [ virtual-machines-linux-configure-raid] (a MDADM)
> * [LVM konfigurálása az Azure-beli Linuxos virtuális gépre][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Virtuálisgép-sorozat, amelyek képesek együttműködni az Azure Premium Storage általában kihasználva szempontjai a következők:

* A növekvő igények szerint az i/o-késés érdekében hamarosan TÁROLÓHÁLÓZATI és NAS-eszközökön kézbesítéséhez.
* Igény szerint a tényezők jobb i/o várakozási ideje, mint az Azure standard szintű tárolást biztosít alkalmazhatók.
* Adja meg, mi érhető el az egyes virtuális gépek elleni több standard szintű Storage-lemezzel rendelkező virtuális gépenként magasabb IOPS.

Mivel az alapul szolgáló Azure Storage replikálja mindegyik lemez legalább három tárolási csomópontok, egyszerű RAID 0 használható szétosztottsága befolyásolhatja. Hiba esetén nem kell RAID5 vagy RAID1 megvalósításához.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>A Microsoft Azure Storage
A Microsoft Azure Storage tárolja az alap VM (az operációs rendszer) és a lemezek vagy a Blobok legalább három külön tárolócsomópontok való. A storage-fiók vagy a felügyelt lemez létrehozásakor választási lehetőség van védelmi itt látható módon:

![Az Azure Storage-fiók engedélyezve van a Georeplikáció][dbms-guide-figure-100]

Az Azure Storage helyi replikációs (helyileg redundáns), amely néhány ügyfelek központi telepítése sikerült elfogadható infrastrukturális hiba miatt az adatvesztéssel szembeni adatvédelmi szinteket biztosít. Ahogy fent látható, az ötödik folyamatban van egy változata, az első három egyik négy különböző lehetőség van. Szeretne részletesebben azokat is különböztetünk meg:

* **Premium helyileg redundáns tárolás (LRS)**: az Azure Premium Storage lemeztámogatást biztosít nagy teljesítményű, kis késleltetésű I/O-igényes számítási feladatokat futtató virtuális gépekhez. Nincsenek az adatokat az Azure-régióba az Azure adatközpontokon belül három replikával. A példányok különböző hibatűrési és frissítési tartományokban vannak (további fogalmak: [ez] [ planning-guide-3.2] című fejezetében a [tervezési útmutatója][planning-guide]). Esetén a tárolócsomópont-hiba vagy a lemezhiba miatt nem működik az adatok replikáját új replikát automatikusan létrejön.
* **Helyileg redundáns tárolás (LRS)**: nincsenek ebben az esetben az adatok az Azure-régióba az Azure adatközpontokon belül három replikával. A példányok különböző hibatűrési és frissítési tartományokban vannak (további fogalmak: [ez] [ planning-guide-3.2] című fejezetében a [tervezési útmutatója][planning-guide]). Esetén a tárolócsomópont-hiba vagy a lemezhiba miatt nem működik az adatok replikáját új replikát automatikusan létrejön. 
* **Georedundáns tárolás (GRS)**: Ebben az esetben van egy aszinkron replikáció, amely egy másik Azure-régió, amely a legtöbb esetben az azonos földrajzi régióban (például Észak-Európában és Nyugat-Európában lévő adatok további három replika-csatornák ). Az eredmény további három replika, így nincsenek hat összeg. Ez egy változata további, ahol az adatok földrajzi replikált Azure-régióban (az írásvédett Georedundáns) olvasási célokra is használhatók.
* **Zónaredundáns tárolás (ZRS) zóna**: Ebben az esetben az adatok három replika továbbra is az azonos Azure-régióban. A [ez] [ planning-guide-3.1] fejezete az [tervezési útmutatója] [ planning-guide] egy Azure-régió lehet adatközpontok számos hálózatbővítési. LRS esetén a replikákat a különböző adatközpontokban, amelyek egy Azure-régiót kellene szétoszthatók.

További információ található [Itt][storage-redundancy].

> [!NOTE]
> A DBMS üzembe helyezési Georedundáns tárolás használata nem ajánlott
> 
> Az Azure Storage Georeplikációs aszinkron. Csatlakoztatva egyetlen virtuális gép egyes lemezeinek replikációja zárolási lépésben nincsenek szinkronizálva. Ezért nem alkalmas elosztott eltérő lemezeken vagy ellen a szoftveres RAID több lemez alapján üzembe helyezett adatbázis-kezelő fájlok replikálására. Az adatbázis-kezelő szoftver szükséges, hogy a állandó lemezegységet pontosan szinkronizálása más logikai egységek és a mögöttes lemezek/forgórészek között. Az adatbázis-kezelő szoftvert használ feladatütemezési i/o-írási tevékenységek különböző mechanizmusokat, és adatbázis-kezelő jelenti, hogy a lemezes tárolás, a replikáció által megcélzott sérült-e, ha ezek még néhány ezredmásodperc alatt változhat. Ezért ha egy adatbázis-konfiguráció egy georeplikált több lemezre kiterjedő nyújtva adatbázissal, például egy replikációs kell végezhető el adatbázis azt jelenti, és funkciókat. Az egyik nem használhatók az Azure Storage Georeplikációs a feladat végrehajtásához. 
> 
> A probléma a legegyszerűbb példa rendszerrel ismertetik. Tegyük fel, az Azure-ba, az adatbázis-kezelő az adatfájlok tartalmazó nyolc lemezek és a egy lemezt a tranzakciós naplófájlt tartalmazó rendelkező feltöltött egy SAP-rendszerrel rendelkezik. A következő kilenc lemezek mindegyike rendelkezik egy egységes módszer alapján az adatbázis-kezelő az írt adatok,-e az adatok akkor ír adatokat vagy a tranzakciós naplófájlok.
> 
> Az adatok megfelelően georeplikációját érdekében és -rendszerkép konzisztens adatbázisok, a tartalom minden kilenc lemezek kell a megfelelő sorrendben az i/o-műveletek végrehajtódtak szemben a következő kilenc eltérő lemezeken georeplikálás készíthető. Azure Storage georeplikációs azonban nem engedi deklarálnia lemezek közötti függőségek. Ez azt jelenti, hogy a Microsoft Azure Storage georeplikációs az a tény, hogy a tartalmát a következő kilenc különböző lemezek egymáshoz kapcsolódó és, hogy az adatok változásait konzisztens csak akkor, ha a sorrendben replikálja az i/o-műveletek történt összes nem ismert a következő kilenc lemezek.
> 
> Magas, hogy a forgatókönyv a georeplikált lemezképeket nem ad meg egy konzisztens adatbázisok kép alatt esélyét, mellett is nincs rendszer teljesítményét, hogy megjelenik-e a geo redundant storage szolgáltatás, amely jelentősen befolyásolhatja a teljesítményt. Összefoglalva ne használja a tárhely-redundancia az ilyen típusú adatbázis-kezelő típus számítási feladatokhoz.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>VHD-k hozzárendelése az Azure virtuális gépek szolgáltatás Storage-fiókok
Ez a fejezet csak az Azure Storage-fiókokra vonatkozik. Ha azt tervezi, felügyelt lemezeket használni, az ebben a fejezetben említett korlátozások nem érvényesek. Felügyelt lemezekkel kapcsolatos további információkért olvassa el a fejezet [Managed Disks] [ dbms-guide-managed-disks] ebben az útmutatóban.

Egy Azure Storage-fiók nem, nem csak egy felügyeleti szerkezet, de korlátozások tárgya is. Mivel a korlátozások eltérő e tárgyaljuk az Azure standard szintű Storage-fiókot, vagy az Azure Premium Storage-fiók. A pontos és korlátozásaikat felsorolt [Itt][storage-scalability-targets]

Az Azure standard szintű Storage, ezért fontos megjegyezni, hogy korlátozva van az iops-értékkel tárfiókonként (sor **teljes kérelmek gyakorisága** a [a cikk][storage-scalability-targets]). Ezenkívül csak egy kezdeti 100 Storage-fiókok (től 2015 július) Azure-előfizetésenként. Ezért ajánlott között több tárfiók használata az Azure standard szintű tárolást iops-t, virtuális gépek elosztása érdekében. Mivel egyetlen virtuális gép ideális esetben egy tárfiókot használja, ha lehetséges. Tehát ha az adatbázis-kezelő központi telepítések, ahol minden egyes virtuális Merevlemezhez, amely az Azure standard szintű tárolást tudta elérni a kvótakorlát tárgyaljuk, kell csak telepít 30 – 40 virtuális merevlemezek egy Azure Storage-fiókot, amely az Azure standard szintű tárolást használ. Másrészről Ha kihasználhatja az Azure Premium Storage, és nagy méretű adatbázisok köteteken tárolni kívánt, valószínűleg iops okoz gondot. Azonban az Azure Premium Storage-fiók módon határértéknél erősebb korlátozást jelentő adatok mennyisége az Azure standard szintű Storage-fiókban. Ennek eredményeképpen csak akkor telepíthet egy VHD-k Azure Premium Storage-fiókban lévő korlátozott számú előtt szerezze meg az mennyiségi korlát. A végén úgy Azure Storage-fiókban, mint "A virtuális SAN" képességek az IOPS és/vagy kapacitása korlátozott. Ennek eredményeképpen a feladat marad, mint a helyszíni üzemelő példányok, a virtuális merevlemezeket, a másik SAP-rendszerek elrendezésének definiálásához a különböző "képzeletbeli SAN-eszközöket" vagy az Azure Storage-fiókok.

Az Azure standard szintű Storage esetében nem ajánlott különböző tárfiókokban, ha lehetséges egyetlen virtuális gép tárterületének nyújtjuk.

A DS vagy GS sorozatú Azure virtuális gépek használatakor az is lehet csatlakoztatási VHD-k Azure-Tárfiókok Standard és prémium szintű Storage-fiókok. Használati esetek, például a biztonsági másolatok írására Standard Storage-bA a VHD-k és az adatbázis-kezelő rendszer adateltéréssel biztonsági és a naplófájlok a Premium Storage jár szem előtt, például a heterogén tárolási sikerült javítható. 

Rendszereit és tesztelési körülbelül 30 – 40 adatbázis adatfájlokat és naplófájlokat tartalmazó VHD-k bővítheti a egyetlen Azure standard szintű Storage-fiók az elfogadható teljesítmény alapján. Ahogy korábban említettük, a korlátozás az Azure Premium Storage-fiókban valószínűleg tudja kezelni a tárolt adatok kapacitása és nem az iops-érték.

SAN a helyszíni eszközök, a megosztáshoz szükséges ahhoz, hogy idővel észleli az Azure Storage-fiókban a szűk keresztmetszetek figyeli. SAP és az Azure Portalon az Azure Monitoring bővítmény olyan eszközökkel, amelyek segítségével észlelheti az elfoglalt Azure Storage-fiókokat, előfordulhat, hogy lehet optimálisnál i/o-teljesítmény biztosítása.  Ez a helyzet észlelése esetén ajánlott foglalt virtuális gépek áthelyezése egy másik Azure-tárfiókba. Tekintse meg a [üzembe helyezési útmutató] [ deployment-guide] az aktiválja az SAP való üzemeltetéséhez figyelési funkciókat.

Ajánlott eljárások az Azure Standard Storage és a standard szintű Azure Storage-fiókok összefoglalójához egy másik cikk itt található <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>A felügyelt lemezek
A felügyelt lemezek az Azure Resource Managerben, amely használható az Azure Storage-fiókokban tárolt VHD-k helyett új erőforrástípust. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva, és ezért a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állásának növelése. További tudnivalókért olvassa el a [áttekintő cikkben](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP jelenleg csak támogatja prémium szintű Managed Disks. Olvasási SAP-Jegyzetnek [1928533] további részletekért.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Áthelyezés telepített adatbázis-kezelő virtuális gépek az Azure standard szintű Storage-ból az Azure Premium Storage
Hogy találkozik, még néhány forgatókönyvet, ahol ügyfélként szeretné üzembe helyezett virtuális gép áthelyezése az Azure standard szintű Storage-ból az Azure Premium Storage-bA. A lemezek az Azure Storage-fiókok vannak tárolva, ha ez nem lehetséges az adatok fizikai áthelyezésével nélkül. A cél elérése érdekében számos módja van:

* Az új Azure Premium Storage-fiókot minden virtuális merevlemezek, virtuális, valamint adatokat VHD-k sikerült másolni. Gyakran azt választotta, a VHD-k száma standard szintű Azure Storage-ban a tény, hogy szükséges-e az adatmennyiség miatt nem. Azonban számú virtuális merevlemezek az IOPS miatt volt szükséges. Most, hogy áthelyezi az Azure Premium Storage módszert sikerült módba kevesebb VHD-k, az IOPS azonos átviteli sebességet érhet el. Tekintve, hogy az Azure standard szintű tárolást a felhasznált adatok és a névleges lemez mérete nem fizet, a VHD-k száma fejeződött nem számít, hogy költségei tekintetében. Azonban az Azure Premium Storage, akkor kell fizetnie névleges méretét. Ezért az ügyfelek többsége próbálja meg Azure VHD-k száma a Premium Storage: a szám, az IOPS-teljesítmény eléréséhez szükséges szükséges. Így a legtöbb ügyfél döntse el, szemben a módszer egy egyszerű 1:1 példány.
* Ha még nincs csatlakoztatva, akkor egy, az SAP adatbázis egy adatbázis biztonsági mentése is tartalmazhat egyetlen virtuális Merevlemezt csatlakoztathatja. A biztonsági mentést követően válassza le az összes virtuális merevlemez, beleértve a biztonsági mentést tartalmazó VHD-t, és a virtuális alaplemez és a virtuális Merevlemezt másolja a biztonsági mentés az Azure Premium Storage-fiók. Szeretné ezután telepítse a virtuális Gépet, az alap virtuális merevlemez alapján és a VHD csatlakoztatására a biztonsági mentést. Most hozza létre további üres prémium szintű Tárolólemezeket a virtuális Gépet, azokat az adatbázis visszaállításához használt. A parancs feltételezi, hogy az adatbázis-kezelő lehetővé teszi az adathoz és naplófájlhoz elérési utak módosítsa a visszaállítási folyamat részeként.
* Egy másik lehetőség, a korábbi folyamat, ahol a biztonsági mentés VHD másolása az Azure Premium Storage-ba, és mellékelje egy újonnan üzembe helyezett és telepített virtuális gépek elleni kapcsolat egy változata.
* A negyedik lehetőséget, ha az adatbázis adatfájlok számának módosításához rászoruló áll válassza. Ebben az esetben egy SAP homogén rendszer másolása exportálási/importálási használatával elvégeznie. A PUT, amely átmásolja az Azure Premium Storage-fiókban, és mellékelje egy virtuális Gépet, amely az importálási folyamat futtatásához használt virtuális merevlemez fájlok exportálása azokat. Ügyfelek akkor használja ezt a lehetőséget, főleg, ha kívánják elérni az adatfájlok számának csökkentéséhez.

A Managed Disks szolgáltatást, áttelepíthető-e a prémium szintű Storage:

1. A virtuális gép felszabadítása
1. Szükség esetén méretezze át a virtuális gép, amely támogatja a Premium Storage (például a DS vagy GS)
1. Módosítsa a felügyelt lemez típusa Premium (SSD)
1. A gyorsítótárazás adatlemezek fejezetben ajánlott módosítani [virtuális gépek és az adatlemezek gyorsítótárazás][dbms-guide-2.1]
1. A virtuális gép elindítása

### <a name="deployment-of-vms-for-sap-in-azure"></a>Az SAP az Azure-beli virtuális gépek üzembe helyezés
A Microsoft Azure virtuális gépek és a kapcsolódó lemezek üzembe helyezése több lehetőséget is kínál. Ezáltal fontos különbségek megértéséhez, mivel a virtuális gépek előkészített függ a központi telepítési módszer eltérőek lehetnek. Általánosságban elmondható hogy megvizsgáljuk az alábbi fejezetek leírt forgatókönyveket.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Az Azure Marketplace-ről egy virtuális gép üzembe helyezése
Öntől, hogy a virtuális gép üzembe helyezéséhez a Microsoft vagy harmadik féltől származó biztosított rendszerképet az Azure Marketplace-ről. Az Azure-ban a virtuális gép központi telepítése után, kövesse az irányelvek és az eszközök telepítéséhez az SAP-szoftvereket, a virtuális Gépen belül, mint a helyszíni környezetben. Az SAP-szoftvereket az Azure virtuális gépen telepíthető, SAP és a Microsoft javasolja feltöltésével, és tárolja az SAP telepítési adathordozó-lemezeken lévő vagy egy Azure virtuális gép létrehozása fájlkiszolgálóként' ", amely tartalmazza az összes szükséges SAP telepítési adathordozót működik.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Egy virtuális Gépet a specifikus általános lemezkép telepítése
Adott javítás követelményeket támaszt az operációs rendszer vagy az adatbázis-kezelő verzióját, mert a megadott lemezképek az Azure Marketplace-en előfordulhat, hogy nem az igényeinek. Emiatt előfordulhat, hogy kell hozzon létre egy virtuális Gépet, a saját "private" az operációs rendszer/adatbázis-kezelő virtuális gép rendszerkép használatával, amely ezután többször is telepíthető. Az ilyen "private" lemezkép előkészítése a másolásra, az operációs rendszer a helyszíni virtuális gép általánosítva van. Tekintse meg a [üzembe helyezési útmutató] [ deployment-guide] egy virtuális gép általánosításához részleteiért.

Ha már telepítette az SAP-tartalom a helyszíni virtuális gépen (különösen a 2 szintű rendszerekhez), központi telepítését az Azure VM-példány az eljárás szerint a SAP Software kiépítés Manager (SAP támogatott átnevezése után módosíthatja úgy a SAP-rendszer beállításai Vegye figyelembe [1619720]). Ellenkező esetben az SAP-szoftvereket telepítheti később az Azure virtuális gépek üzembe helyezése után is.

Az SAP-alkalmazás által használt adatbázis tartalom, létrehozhat a tartalom frissen SAP-telepítéssel, vagy importálhatja a tartalom az Azure-bA vagy az közvetlenül biztonsági mentése az adatbázis-kezelő funkcióit kihasználva az adatbázis-kezelő adatbázis biztonsági másolatát a virtuális merevlemez használatával  A Microsoft Azure Storage. Ebben az esetben sikerült is készítse elő a VHD-k az adatbázis-kezelő adat- és naplófájlok fájlok egy helyi- és azokat a lemezeket, majd importálja az Azure-bA. De az adatbázis-kezelő adatokat, amelyeket az Azure-bA a helyszíni első betöltődnek az átvitel akkor működik, elő kell készíteni a helyi VHD lemezek keresztül.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel
Azt tervezi, hogy egy adott SAP-rendszer áthelyezése a helyszínről az Azure-ba (átemeléses). Ezt megteheti a lemezt, amely tartalmazza az operációs rendszer, az SAP bináris fájljait, és végleges DBMS bináris fájljainak, valamint a lemezek a DBMS Azure-ba, az adat- és naplófájlok fájlok feltöltésével. A #2. a fenti forgatókönyv leváló, ne az állomásnév, SAP SID és SAP felhasználói fiókok az Azure virtuális gép, azok a helyszíni környezetben lettek konfigurálva. Ezért a kép általánosítása már nem szükséges. Ebben az esetben a leginkább érvényes létesítmények közötti forgatókönyvek esetén, ahol egy részét az SAP-rendszeren fut a helyszíni és a részek az Azure-ban.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Magas rendelkezésre állás és vészhelyreállítás az Azure virtuális gépek
Az Azure kínál a következő magas rendelkezésre állású (HA) és a vészhelyreállítás (DR) helyreállítási funkciók, amelyek a alkalmazni a különböző összetevőket, a SAP- és adatbázis-kezelő központi telepítések akkor használjuk

### <a name="vms-deployed-on-azure-nodes"></a>Az Azure-csomópontok üzembe helyezett virtuális gépek
Az Azure Platform nem kínál üzembe helyezett virtuális gépek például az élő áttelepítési funkciók. Ez azt jelenti, hogy ha karbantartási szükséges a server-fürt, amelyre a virtuális gép telepítve van, a virtuális Gépet kell beolvasása az leáll és újraindul. Az Azure-ban karbantartási használatával történik így nevű fürtökben a kiszolgálók frissítési tartományok. Egyszerre csak egyetlen frissítési tartomány változatlan. Egy újraindítás közben nincs a szolgáltatás megszakadásának közben a virtuális gép leállt, a karbantartásra azért és újraindítja a virtuális Gépet. A legtöbb DBMS-szállítók azonban az adatbázis-kezelő szolgáltatások egy másik csomóponton gyorsan újraindul, ha az elsődleges csomópont nem érhető el magas rendelkezésre állású és vész-helyreállítási funkciókat biztosítanak. Az Azure Platform virtuális gépek, tárolási és más Azure-szolgáltatások szét a frissítési tartományok győződjön meg arról, hogy a tervezett karbantartás, illetve az infrastruktúra hibák csak hatással virtuális gépek és szolgáltatások kisebb részhalmazát funkciókat kínál.  Gondos tervezés rendelkezésre állási szintek a helyszíni infrastruktúra hasonlítható elérése érdekében lehetősége.

A Microsoft Azure rendelkezésre állási készletek virtuális gépek logikus vagy szolgáltatás, amely biztosítja a virtuális gépek és más szolgáltatások oszlanak meg különböző hibatűrési és frissítési tartományokba, fürtön belül, hogy csak lenne egy csomópont meghibásodásakor egy pontot (olvasás időben[ez (Linux)] [ virtual-machines-manage-availability-linux] vagy [a (Windows)] [ virtual-machines-manage-availability-windows] további részleteivel).

Virtuális gépek, ahogy az képen látható bevezetésekor célú úgy kell konfigurálni kell:

![Az adatbázis-kezelő magas rendelkezésre ÁLLÁS konfiguráció a rendelkezésre állási csoport definíciója][dbms-guide-figure-200]

Szeretnénk létrehozni az adatbázis-kezelő központi telepítések magas rendelkezésre állású konfigurációk (amely független az egyes adatbázis-kezelő magas rendelkezésre ÁLLÁS funkciók használt), ha az adatbázis-kezelő virtuális gépeket kellene:

* A virtuális gépeket ad hozzá az azonos Azure virtuális hálózatban (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* A virtuális gépek, a magas rendelkezésre ÁLLÁSÚ konfiguráció is ugyanazon az alhálózaton kell lennie. A különböző alhálózatok közötti névfeloldás esetén nem lehet a csak felhőalapú telepítések esetén csak akkor IP feloldási működik. Használja a helyek közötti és ExpressRoute-kapcsolat a létesítmények közötti központi telepítések, egy legalább egy alhálózattal rendelkező hálózatot már létrejött. Névfeloldás történik megfelelően a helyszíni AD-házirendek és a hálózati infrastruktúrát. 



#### <a name="ip-addresses"></a>IP-címek
Erősen ajánlott rugalmas módon állíthatja be a virtuális gépek magas rendelkezésre ÁLLÁSÚ konfigurációkhoz. Oldja meg a magas rendelkezésre ÁLLÁSÚ alkotnának belül a magas rendelkezésre ÁLLÁSÚ konfiguráció IP-címek a függő entitások nem megbízható, az Azure-ban, ha statikus IP-címeket használja. Nincsenek két "Shutdown" fogalmak az Azure-ban:

* Az Azure Portalon vagy Azure PowerShell-parancsmag Stop-AzureRmVM leállítást: Ebben az esetben a virtuális gép leállítási lekérdezi, és fel lesznek szabadítva. Az Azure-fiókja már nem díjat számítunk fel a virtuális gép ezért, amelyiknél díjak csak a felhasznált tárhelyért. Ha a hálózati adapter magánhálózati IP-cím nem statikus, az IP-cím akkor szabadul fel, és nem garantált, hogy a hálózati adapter lekérdezi a régi IP-cím a virtuális gép az újraindítás után újra. Felszabadítási hajt végre a leállítási le az Azure Portalon keresztül, vagy hívja a Stop-AzureRmVM automatikusan okoz. Ha nem szeretné felszabadítani a Stop-AzureRmVM - StayProvisioned gép használata 
* Ha leállítja a virtuális gépről egy operációs rendszer szintjén, a virtuális gép lekérdezi leállítása és nem fel lesznek szabadítva. Azonban ebben az esetben az Azure-fiókkal van is díjat számítunk fel a virtuális gép, annak ellenére, hogy-e állítva. Ebben az esetben a leállított virtuális gép IP-cím-hozzárendelés helye változatlan marad. A belül a virtuális gép leállítása nem kényszeríti automatikusan felszabadítási.

Még a létesítmények közötti forgatókönyvek esetén alapértelmezés szerint egy leállítási és felszabadítási azt jelenti, hogy az IP-címek való hozzárendelését a virtuális gépről, akkor is, ha a DHCP-beállításokat a helyi házirendek abban különböznek a. 

* A kivétel egy statikus IP-címet rendel egy hálózati adapter, mint ha leírt [Itt][virtual-networks-reserved-private-ip].
* Ebben az esetben az IP-cím rögzített marad mindaddig, amíg nem törlik a hálózati adaptert.

> [!IMPORTANT]
> Annak érdekében, hogy egyszerű és kezelhető a teljes üzembe helyezési megőrzéséhez a tiszta ajánlás az, hogy a virtuális gépek partneri együttműködés egy adatbázis-kezelő magas rendelkezésre ÁLLÁS vagy úgy, hogy van egy működő névfeloldás között a különböző virtuális gépek Azure-ban DR-konfiguráció beállítása.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Host Monitoring telepítése
Hatékony használati SAP-alkalmazások az Azure Virtual machines szolgáltatásban az SAP képes monitorozási adatai az Azure Virtual machines szolgáltatásban futó fizikai gazdagép-gazda lekérése használatához. Egy adott gazdagép-ügynök SAP javítási szintje szükség, amely lehetővé teszi, hogy ez a funkció a SAPOSCOL és SAP-gazdagép-ügynök. A pontos javítási szintet leírása itt található SAP-Jegyzetnek [1409604].

Összetevő-gazdagép adatok SAPOSCOL és SAP-gazdagép-ügynök telepítési és összetevők életciklus-kezelését kapcsolatos részletekért tekintse meg a [üzembe helyezési útmutató][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>A Microsoft SQL Server rögzítésen
### <a name="sql-server-iaas"></a>Az SQL Server IaaS
Kezdve a Microsoft Azure, könnyedén áttelepítheti a meglévő SQL Server-alkalmazások az Azure Virtual Machines a Windows Server-platformra épül. Az SQL Server virtuális gép segítségével csökkentheti a teljes tulajdonosi költség, a központi telepítési, felügyeleti és karbantartási vállalati szánt alkalmazások egyszerűen telepítse át ezeket az alkalmazásokat a Microsoft Azure-bA. Az SQL Server egy Azure virtuális gépet a rendszergazdák és fejlesztők számára továbbra is használhatják az azonos fejlesztési és felügyeleti eszközöket, amelyek a rendelkezésre álló helyi. 

> [!IMPORTANT]
> A Microsoft nem hozzászólást a Microsoft Azure SQL Database, vagyis a Platform szolgáltatási ajánlatunk a Microsoft Azure platform. Ez a tanulmány a hozzászólás van az SQL Server-termék futtatásáról, mint a helyszíni üzemelő példányok az Azure Virtual Machines, az infrastruktúra kihasználva az Azure szolgáltatás szerint ismert. Adatbázis-képességeket és a funkciók között ezeket az ajánlatokat két különböző, és meg nem nem lenne szerencsés egymással. Lásd még: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Javasoljuk, hogy tekintse át [ez] [ virtual-machines-sql-server-infrastructure-services] a folytatás előtt.

A következő szakaszokban megtalálhatja a fenti hivatkozást a dokumentáció részét összesítve és említett. SAP összegyűjtjük is szerepelnek, és néhány olyan fogalmat, részletesebben ismerteti. Azonban erősen ajánlott, haladjon végig a dokumentáció első fent az SQL Server-specifikus dokumentáció elolvasása előtt.

Van néhány SQL Server IaaS konkrét információk tudnia kell, mielőtt továbblépne:

* **Virtual machines garantált szolgáltatási SZINTJEI**: nincs SLA-t a virtuális gépek futtatása az Azure-ban, amely itt található: <https://azure.microsoft.com/support/legal/sla/>  
* **SQL-verzió támogatja**: az ügyfelek, támogatja az SQL Server 2008 R2 vagy újabb Microsoft Azure virtuális gépen. Korábbi verziók nem támogatottak. Tekintse át az ebben az általános [támogatási nyilatkozattal](https://support.microsoft.com/kb/956893) további részletekért. Vegye figyelembe, hogy általában az SQL Server 2008 használata a Microsoft által is támogatott. Azonban jelentős funkciót az SAP, az SQL Server 2008 R2 rendszerben jelent meg, amely miatt az SQL Server 2008 R2 az SAP a minimális kiadásban. Ne feledje, hogy az SQL Server 2012 és a 2014-es az IaaS-forgatókönyveknél (például biztonsági mentésével közvetlenül az Azure Storage szemben), ugyanakkor szorosabb integrációt lett kiterjesztve. Ezért hogy korlátozni a Ez a tanulmány az SQL Server 2012 és a legutóbbi javítási szintet 2014 az Azure-hoz.
* **SQL-funkciók támogatása**: leginkább az SQL Server funkciói a Microsoft Azure-beli virtuális gépeken támogatott néhány kivétellel. **Az SQL Server feladatátvételi fürtszolgáltatási megosztott lemezek használata nem támogatott**.  Elosztott technológiák, például adatbázis-tükrözés, AlwaysOn rendelkezésre állási csoportok, replikáció, Naplóküldést és a Service Broker támogatottak egy adott Azure-régión belül. Az SQL Server AlwaysOn is támogatott különböző Azure-régiók között, itt dokumentált: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Tekintse át a [támogatási nyilatkozattal](https://support.microsoft.com/kb/956893) további részletekért. AlwaysOn-konfigurációjának telepítése a példa a [ez] [ virtual-machines-workload-template-sql-alwayson] cikk. Emellett tekintse meg az ajánlott eljárásokat, dokumentált [Itt][virtual-machines-sql-server-infrastructure-services] 
* **SQL-teljesítmény**: biztosak vagyunk abban, hogy a Microsoft Azure futtatott virtuális gépek rendkívül jól végrehajtani más nyilvános felhőalapú virtualizációs megoldások, de az egyes eredmények támogatáshoz képest korlátozottabb eltérőek lehetnek. Tekintse meg [ez] [ virtual-machines-sql-server-performance-best-practices] cikk.
* **Az Azure piactéren elérhető lemezképekkel**: új Microsoft Azure virtuális gép üzembe helyezése a leggyorsabb módot kínálni arra, hogy a lemezképek használata az Azure Marketplace-ről. Az Azure Marketplace-en, képek, az SQL Server tartalmazó vannak. A képek, ahol az SQL Server már telepítve van az SAP NetWeaver-alkalmazásaihoz azonnal nem használható. A hiba oka az SQL Server alapértelmezett rendezése ezeket a képeket, és nem az SAP NetWeaver-rendszerek által igényelt rendezés belül van telepítve. Az ilyen rendszerképek használatához ellenőrizze a fejezetben leírt lépéseket [SQL Server-lemezkép használatával a Microsoft Azure Marketplace-en kívül][dbms-guide-5.6]. 
* Tekintse meg [díjszabása](https://azure.microsoft.com/pricing/) további információt. A [SQL Server 2012 licencelése útmutató](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) és [SQL Server 2014 licencelési útmutató](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) fontos tényezőként is vannak.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Beállítási útmutatója az SQL Server Azure virtuális gépeken futó SAP-kapcsolódó SQL Server-telepítések
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>A VM-/ VHD-szerkezet SAP-kapcsolódó SQL Server-telepítéseket javaslatok
Az általános leírása megfelelően SQL Server végrehajtható fájlok található, vagy el kell telepíteni a virtuális gép operációsrendszer-lemez rendszermeghajtóján (C: meghajtó\).  Általában a legtöbb az SQL Server rendszer-adatbázisok nem használhatók magas szinten SAP NetWeaver munkaterhelés szerint. Ezért a rendszeradatbázisokban (master, msdb, valamint modell) SQL Server is marad, valamint a C:\ meghajtón található. Kivétel a tempdb, igénylő néhány SAP ERP és az összes BW számítási feladatokhoz, előfordulhat, hogy nagyobb adatmennyiség vagy az eredeti virtuális gép nem illik i/o műveletek kötet lehet. Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani:

* Helyezze át a tempdb elsődleges adatfájl az SAP adatbázis elsődleges adatfájl azonos logikai meghajtón.
* További tempdb adatok fájlokat hozzáadni egy adatfájlt az SAP-felhasználó adatbázist tartalmazó más logikai meghajtókhoz.
* A tempdb logfile adja hozzá a logikai meghajtó, amely tartalmazza a felhasználói adatbázis naplófájlját.
* **Kizárólag a helyi SSD-meghajtókat használó virtuális gépek típusai** a számítási csomópont tempdb adatait, és jelentkezzen be a fájlokat a D:\ meghajtóra elhelyezhetők. Mindazonáltal ajánlatos lenne több tempdb-adatfájlok használatát. Vegye figyelembe, hogy különböznek a D:\ meghajtóra köteteket a virtuális gép típusa alapján.

Ezek a konfigurációk engedélyezze a tempdb fel több helyet több, mint a rendszermeghajtó tud biztosítani. Annak érdekében, hogy a megfelelő tempdb mérete határozza meg, egyet a meglévő rendszerek esetében a helyszíni futtatásához, amelyek a tempdb méretek ellenőrizheti. Ilyen konfiguráció emellett lehetővé teszik a tempdb, amely a rendszermeghajtó nem található, fogyasztóalapú ellen IOPS-számokkal. Újra a helyszíni rendszerben használható i/o-munkaterhelés ellen a tempdb figyelése, hogy a TempDB adatbázis a látja a keresett IOPS-számokkal is ezekből származik.

Egy Virtuálisgép-konfigurációt, az SAP adatbázis az SQL Server fut, amely, és ahol a tempdb adat- és a tempdb logfile kerülnek a D:\ meghajtóra hasonlóan néz ki:

![Az SAP az Azure IaaS virtuális gép konfigurációjának referencia][dbms-guide-figure-300]

Vegye figyelembe, hogy rendelkezik-e a D:\ meghajtóra függ, a virtuális gép típusát különböző méretekre. A TempDB méretkövetelményt függ, előfordulhat, hogy mindenképpen pár tempdb adat- és naplófájlok az SAP adatbázis adathoz és naplófájlhoz azokban az esetekben, ahol a D:\ meghajtón túl kicsi a fájlokat.

#### <a name="formatting-the-disks"></a>A lemezek formázása
Az SQL Server az NTFS blokkolja az SQL Server-adatokat tartalmazó lemezeinek méretét, és a naplófájlok kell lennie 64K. Hiba esetén nem kell formázni a D:\ meghajtóra. Ez a meghajtó előre formázott származnak.

Annak érdekében, hogy, hogy a visszaállítási vagy -adatbázisok létrehozása nem inicializálása folyamatban van az adatfájlok által feltérképezése valódi élménnyé válik a fájlok tartalmát, az egyik gondoskodnia kell arról, hogy a felhasználói környezet az SQL Server-szolgáltatás fut a jogosultsága egy bizonyos. Általában a Windows rendszergazdai csoport rendelkezik ezekkel a jogosultságokkal. Ha a felhasználó nem Windows rendszergazdai felhasználó környezetében fut, az SQL Server szolgáltatást, kell rendelnie, hogy a felhasználó a felhasználói jogosultság **kötet-karbantartási feladatok végrehajtása**.  A Microsoft Tudásbázis megfelelő cikkében. a részleteket lásd: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Adatbázis tömörítése hatása
A konfigurációk, ahol i/o-sávszélesség korlátozó válhat minden mérték, ami csökkenti az IOPS segíthet a munkaterhelés, mint például az Azure IaaS-forgatókönyveknél is futtatható egy többhelyes. Ezért ha még nincs kész, az SQL Server lap tömörítés alkalmazása ajánlott SAP és a Microsoft egy meglévő SAP-adatbázist az Azure-ba való feltöltéséhez.

Azt javasoljuk, hogy az adatbázis tömörítése végrehajtása az Azure-ba való feltöltéséhez kívül két oka van megadva:

* A feltölteni kívánt adatok mennyisége kisebb.
* A tömörítés végrehajtási időtartamának rövidebb, feltéve, hogy az egyik használjon erősebb hardveres további processzorokat vagy magasabb i/o-sávszélességet vagy kisebb I/O várakozási ideje a helyszíni.
* Kisebb adatbázis maximális mérete kisebb költségek lemezfoglaláshoz vezethet

Adatbázis tömörítése működik, valamint egy Azure Virtual Machines szolgáltatásban, mint a helyszíni. Meglévő SAP SQL Server-adatbázis tömörítése módjáról további részletekért keresse fel itt: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>Az SQL Server 2014 - tárolását adatbázisfájlok közvetlenül az Azure Blob Storage
Az SQL Server 2014 tároljon adatbázisfájlokat közvetlenül az Azure Blob Store "burkolót" őket egy virtuális merevlemez nélkül lehetőségük nyílik meg. Különösen a standard szintű Azure Storage vagy kisebb Virtuálisgép-típusok használata lehetővé teszi forgatókönyvek, ahol szeretné érvénybe lépteti néhány kisebb Virtuálisgép-típusok a csatlakoztatott lemezek csak korlátozott számú iops-érték határain is leküzdeni. Ez azonban nem rendszer-adatbázisokat az SQL Server felhasználói adatbázisokat esetében működik. Emellett az SQL Server adathoz és naplófájlhoz működik. Ha szeretné-e egy SAP SQL Server-adatbázis üzembe helyezése "alkalmazásburkoló" helyett ezzel a módszerrel azt a virtuális merevlemezek, szem előtt tartani a következőket:

* A Tárfiókot igényeinek, az egyik, amellyel a virtuális gép az SQL Server telepítése fut-e az azonos Azure-régióban kell használni.
* Ezt a módszert, valamint a központi telepítések korábban felsorolt a VHD-k elosztásra vonatkozó különböző Azure-Tárfiókok keresztül szempontok érvényesek. Azt jelenti, hogy az Azure Storage-fiók határértékek i/o műveletek száma.

[comment]: <> (MSSedusch teendőlista-kezelő, de ez a beállítás használja hálózati sávszélességet, és nem a tárolási sávszélességet, így?)

A központi telepítési típus részleteit az alábbiakban: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Annak érdekében, hogy közvetlenül az Azure Premium Storage az SQL Server adatfájljainak tárolására, szüksége lesz egy legalább SQL Server 2014 javítás szoftver-és itt dokumentált: <https://support.microsoft.com/kb/3063054>. Az Azure standard szintű tárolóban működő SQL Server adatfájljainak tárolására működik az SQL Server 2014 kiadott verzióját. Azonban nagyon ugyanazokat a javításokat tartalmaz másik adatsorozatot tartalmaz, amelyek a közvetlen Azure Blob Storage és használatát az SQL Server adatfájljainak biztonsági mentések megbízhatóbbá tétele. Ezért ajánlott javításokról általában használata.

### <a name="sql-server-2014-buffer-pool-extension"></a>Az SQL Server 2014 pufferkészlet-bővítmény
Az SQL Server 2014 bevezetett egy új szolgáltatást, amely a pufferkészlet-kiterjesztés nevezzük. Ez a funkció, amelyet a memóriát, valamint egy második szintű gyorsítótár-kiszolgáló helyi SSD-k vagy a virtuális gép biztonsági SQL Server a pufferkészletben terjeszti ki. Ez lehetővé teszi, hogy az adatokat egy nagyobb munkakészletének "a memóriában". Standard szintű Azure Storage eléréséhez képest a hozzáférést a bővítményt a pufferkészlet, helyi SSD-k az Azure virtuális gép található, a számos tényező gyorsabb.  Ezért kihasználva a helyi D:\ meghajtóra, a virtuális gép közül, amelyek kiváló IOPS és átviteli sebesség lehet olyan nagyon ésszerű módon az Azure Storage IOPS terhelés csökkentésére, és jelentősen javíthatja a lekérdezések válaszidejét. Ez vonatkozik, különösen akkor, ha nem a Premium Storage tárolást használ. Prémium szintű tárolás és a számítási csomóponton a prémium szintű Azure olvasási gyorsítótár használata esetén ajánlott adatfájlokat, nincs lényeges különbség várhatóan. Oka, hogy mindkét gyorsítótárak (SQL Server pufferkészlet-bővítmény és a prémium szintű Storage olvasási gyorsítótár) használ a számítási csomópontok helyi lemezeket.
Ez a funkció kapcsolatos további részletekért tekintse meg ezt a dokumentációt: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Az SQL Server biztonsági mentési/helyreállítási szempontjai
Ha az SQL Server üzembe helyezése az Azure-bA a biztonsági mentési módszerek át kell tekinteni. Még ha a rendszer nem egy hatékony rendszer, az SAP-adatbázist az SQL-kiszolgáló által üzemeltetett kell készíteni rendszeres időközönként. Mivel az Azure Storage három rendszerkép tartja, a biztonsági mentés már kevésbé fontos tárolási összeomlás kompenzáló tekintetben. Prioritás okát a megfelelő biztonsági mentési és helyreállítási terv karbantartása sokkal többet lehet kompenzálni a logikai vagy manuális hibákat azáltal, hogy az idő helyreállítási lehetőségei a pont. Így az célja, hogy mindkét használata biztonsági mentések, állítsa vissza az adatbázist vissza egy bizonyos ponton az időben, vagy használhatja a biztonsági másolatokat az Azure használatával ültet be egy másik rendszer a meglévő adatbázis másolásával. Például meg tudta átvinni 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása egy adott rendszer biztonsági mentésének visszaállításával.

Az SQL Server biztonsági mentése az Azure Storage három különböző módja van:

1. Az SQL Server 2012 CU4 és magasabb lehet natív módon adatbázisai biztonsági mentését egy URL-címet. Ez a blog részletes [új funkciók az SQL Server 2014 – 5. rész – biztonsági mentési és visszaállítási fejlesztések](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Tekintse meg a fejezet [SQL Server 2012 SP1 CU4 vagy újabb][dbms-guide-5.5.1].
2. SQL Server-verziókban az SQL 2012 CU4 előtt egy átirányítási funkció segítségével biztonsági mentést készíteni a VHD-t, és alapvetően az írási adatfolyam a digitalizáció felé egy Azure tárolási helye, amely konfigurálva van. Tekintse meg a fejezet [SQL Server 2012 SP1 CU3 vagy korábbi kiadásai][dbms-guide-5.5.2].
3. Az utoljára létrehozandó metódust, hogy a hagyományos SQL Server biztonsági mentését, hogy a lemez parancs egy lemezt eszközre. Ez megegyezik a helyszíni központi telepítési típus, és nem a jelen dokumentum részletesen tárgyalja.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>Az SQL Server 2012 SP1 CU4 vagy újabb
Ez a funkció lehetővé teszi közvetlenül az Azure BLOB storage-biztonsági mentés. Ezzel a módszerrel nem kell biztonsági másik lemezre, amely szeretne felhasználni, lemez- és IOPS. A cél alapvetően ezt:

 ![A Microsoft Azure Storage-BLOBBÓL az SQL Server 2012 backup használatával][dbms-guide-figure-400]

Ebben az esetben előnye az, hogy egy nem kell az SQL Server biztonsági másolatok tárolására a lemezeket. Ezért kell lefoglalt kevesebb lemezek és a lemez iops-érték használható az adatok és a naplófájlok a teljes sávszélesség. Ne feledje, hogy a biztonsági másolat maximális mérete egy legfeljebb 1 TB a szakaszban leírt **korlátozások** ebben a cikkben: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Ha a biztonsági másolat mérete, annak ellenére, hogy az SQL Server biztonságimásolat-tömörítési funkciók használatával túllépné 1 TB-os méretig, a funkciót az fejezetben leírt [SQL Server 2012 SP1 CU3 vagy korábbi kiadásai] [ dbms-guide-5.5.2] ebben a dokumentumban kell lennie használja.

[Kapcsolódó dokumentációt](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) ellen az Azure Blob-Store másolatokból adatbázisok visszaállítása leíró javasoljuk, nem közvetlenül az Azure BLOB tárolóból vissza, ha a biztonsági mentés > 25 GB. Ebben a cikkben a javaslat a teljesítménnyel kapcsolatos megfontolások és nem funkcionális korlátozások miatt alapul. Ezért különböző feltételeket alkalmazhatnak eseti alapon.

Ilyen típusú biztonsági mentés állítsa be és ki dokumentációjában található [ez](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) oktatóanyag

A lépések sorrendjét példát is olvashatók legyenek [Itt](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Fontos győződjön meg arról, hogy a Blobok minden egyes biztonsági vannak más a neve, a legmagasabb jelentőségű automatizálása a biztonsági mentések. Máskülönben felül, és a restore-lánc megszakad.

Annak érdekében, hogy összekeveri a három különböző típusú biztonsági mentések közötti dolog célszerű alá a biztonsági mentésekhez használt tárfiók különböző tárolót hoz létre. A tárolók lehet virtuális gép által csak vagy a virtuális gép és a biztonsági mentés típusát. A séma nézhet:

 ![A Microsoft Azure Storage-BLOB - biztonsági mentés az SQL Server 2012 külön Tárfiókot a különböző tárolók használatával][dbms-guide-figure-500]

A fenti példában a biztonsági mentések lenne nem futtatható, irányítson át, a virtuális gépek telepítve vannak-e ugyanabban a tárfiókban. Egy új storage-fiókot kifejezetten a biztonsági másolatok számára lenne. A storage-fiókok belül létrehozott biztonsági másolat és a virtuális gép neve össze a különböző tárolók lenne. Az ilyen Szegmentálás megkönnyíti a felügyelheti a különböző virtuális gépek biztonsági mentését.

A Blobok, az egyik közvetlenül ír a biztonsági másolatok, ne adja hozzá a száma, az adatok egy virtuális gép lemezének. Ezért egy sikerült maximalizálhatja a maximális számát az adott virtuális gép termékváltozatának az adatokat a csatlakoztatott adatlemezeket és tranzakciós naplófájlt, és továbbra is hajtsa végre a egy backup tárolót ellen. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 vagy korábbi kiadásai
Az első lépés, végezze el a biztonsági mentés közvetlenül szemben az Azure Storage elérése érdekében lenne, töltse le az msi, amely csatolva van [ez](https://www.microsoft.com/download/details.aspx?id=40740) KBA cikk.

Letöltés a x64 telepítési fájlt, és a dokumentációt. A fájl nevű programot telepíti: **Microsoft SQL Server biztonsági másolat, a Microsoft Azure eszközzel**. Alaposan olvassa el a termék dokumentációját.  Az eszköz alapvetően a következőképpen működik:

* Az SQL Server oldalán, az SQL Server biztonsági másolat egy lemez helye van definiálva (ne használja a D:\ meghajtóra helyként).
* Az eszköz lehetővé teszi, hogy meghatározza a szabályok, amelyek segítségével különböző Azure Storage-tárolók, biztonsági mentések különböző típusairól közvetlen.
* A szabályok vannak érvényben, ha az eszköz átirányítja az írási adatfolyam a biztonsági mentés egy VHD-k/lemezt az Azure Storage helyre, amely korábban definiálva lett.
* Az eszköz kikerül egy kis helyettes fájlt néhány KB méretű VHD/lemezre, amely meg van adva az az SQL Server biztonsági mentési. **Mivel erre szükség van az Azure Storage-ból újra visszaállítása a tárolóhelyen kell hagyni ezt a fájlt.**
  * Ha elvesztette a helyettes fájlt (például keresztül a tárolást kínál, amelyek a helyettes fájl elvesztését), és úgy döntött, hogy a készíthető biztonsági másolat a Microsoft Azure Storage-fiókba, akkor előfordulhat, hogy állítsa helyre a helyettes fájlt a Microsoft Azure Storage szolgáltatáson keresztül töltse le a a storage-tárolóból, amelyben el helyezni. Helyezze a helyettes fájlt egy mappába a helyi számítógépen, ahol az eszköz van beállítva, és ugyanazt a titkosítási jelszót a tárolóhoz tölthet fel, ha a titkosítás együttes az eredeti szabályt. 

Ez azt jelenti, hogy az SQL Server újabb kiadásaiban a fentieknek megfelelően a séma helye, valamint az SQL Server különböző kiadásaiban, amely egy Azure Storage-helyre nem engedélyezi a közvetlen cím kerülhetnek.

Ez a módszer nem használható az SQL Server újabb kiadásokban, amely támogatja a biztonsági mentést natív módon ellen az Azure Storage. Kivétel, ahol az Azure-bA a natív biztonsági mentésével korlátozásai forgalomszűrők blokkolják a natív biztonsági mentési végrehajtása az Azure-bA.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Egyéb lehetőségek az SQL Server-adatbázisok biztonsági mentése
Egyéb lehetőségek az adatbázisok biztonsági mentését, hogy további adatlemezeket csatlakoztathat a biztonsági másolatok tárolására használt virtuális gép. Ebben az esetben kell győződjön meg arról, hogy a lemezek nem futnak teljes. Ha ez a helyzet, a lemezek leválasztása a speak úgy az "archív", és cserélje le egy új üres lemez kell. Az elérési út leáll, ha meg szeretné tartani ezeket VHD-k a külön Azure Storage-fiókok közül, amelyek a VHD-k, az adatbázis fájljai.

A második lehetőség, hogy egy nagy méretű virtuális Gépet, amely számos lemez csatolva, például 32VHDs D14 csomag. A tárolóhelyek használata hozhat létre olyan rugalmas környezetben, építhető megosztásokkal, majd biztonsági mentési célként a különböző DBMS-kiszolgálókhoz.

Néhány ajánlott eljárást itt van dokumentálva [Itt](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) is. 

#### <a name="performance-considerations-for-backupsrestores"></a>Teljesítménnyel kapcsolatos megfontolások a biztonsági mentés/visszaállítás
Operációs rendszer nélküli telepítések, mint a biztonsági mentési/visszaállítási teljesítménye szolgáltatás hány kötetek párhuzamosan olvasható, és az átviteli sebességet, a köteteket lehet függ. Emellett a biztonságimásolat-tömörítési funkciók által használt CPU-felhasználás előfordulhat, hogy fontos szerepet játszik a virtuális gépek nyolc CPU szállal. Ezért akkor feltételezheti, hogy:

* A kevesebb az adatok tárolására használt lemezek fájlok száma, minél kisebb általános való olvasáskor.
* Minél kisebb a virtuális gépen, a jogosultságeszkalálást szálak száma a biztonságimásolat-tömörítési funkciók hatását.
* A kevesebb céllal (Blobok, VHD-k vagy lemezek) írni a biztonsági mentés, a kisebb az átviteli sebességet.
* Minél kisebb a virtuális gép mérete, annál kisebb tárolási teljesítmény kvótája írása és olvasása az Azure Storage-ból. Független, hogy a biztonsági másolatokat közvetlenül tárolja az Azure Blob vagy e újra az Azure-Blobokban tárolt VHD-k találhatók.

A Microsoft Azure Storage-BLOBBÓL az újabb verziókban a biztonsági mentési célként használatakor, csak egy URL-cím cél, minden egyes konkrét biztonsági kijelölésének korlátozva.

Azonban a régebbi kiadásokban a "Microsoft SQL Server backup a Microsoft Azure eszközzel való" használatakor megadhatja több fájl cél. Az egynél több cél a biztonsági mentés méretezhetők, és a biztonsági mentés az átviteli sebesség magasabb. Ez azt eredményezi majd több fájlt, valamint az Azure Storage-fiókban. A tesztelés használatával több fájl célhelyre mindenképp érheti el az átviteli sebességet, amely az SQL Server 2012 SP1 CU4 meg végrehajtani a biztonsági mentési kiterjesztésű el lehet érni. Akkor is nem blokkolja a 1 TB-os korlátot, mint a natív biztonsági mentéssel az Azure-bA.

Azonban tartsa szem előtt, az átviteli sebességet is függ, az Azure Storage-fiókot használja a biztonsági mentési helyét. Ötlet lehet keresse meg a tárfiók más régióban, mint a virtuális gépek futnak. Például, akkor a Virtuálisgép-konfiguráció futtatása a Nyugat-Európa, de helyezze a Storage-fiók, amellyel biztonsági mentési elleni Észak-Európában. Amely természetesen hatással van a biztonsági mentési átviteli sebességet, és valószínűleg nem fog létrehozni egy 150MB/s átviteli, azt úgy tűnik, hogy lehetséges azokban az esetekben, ahol a céloldali tárfiók és a virtuális gépek futnak regionális ugyanabban az adatközpontban.

#### <a name="managing-backup-blobs"></a>Biztonsági mentési Blobok kezelése
Nincs a követelmény, hogy a saját biztonsági másolatainak kezelése. Az elvárás, hogy sok blobok jönnek létre a gyakori tranzakciónaplók biztonsági mentését hajtja végre, mert ezek a blobok felügyelete könnyen is túlterhelni az Azure Portalon. Ezért fontos recommendable kihasználhatja az Azure storage Explorerben. Nincsenek több helyes méretprofilokat érhető el, amelyek segíthetnek az Azure storage-fiók kezelése

* A Microsoft Visual Studio és az Azure SDK-t (<https://azure.microsoft.com/downloads/>)
* A Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* Harmadik felektől származó eszközök

Teljes leírását a biztonsági másolat és az SAP az Azure-on, tekintse meg [biztonsági mentési útmutató a SAP](sap-hana-backup-guide.md) további információt.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>A Microsoft Azure Marketplace-en kívül az SQL Server-lemezkép használatával
A Microsoft kínál a virtuális gépeket az Azure piactéren, az SQL Server verziói már tartalmazzák. Az SAP-vásárlóknak, akik licencek szükségesek az SQL Server és a Windows Ez lehet alapvetően terjed ki a szükséges licencek kell indítani az SQL Server már telepítve van a virtuális gépek lehetőséget. Az ilyen rendszerképek használatához az SAP, az alábbiakat kell tenni:

* SQL Server-verziók nem próbaverzió költsége magasabb, mint az Azure Marketplace-ről üzembe helyezett egy "Csak Windows" virtuális gép beszerezni. Tekintse meg ezeket a cikkeket hasonlítsa össze az árakat: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> és <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Csak használhatja az SQL Server-kiadásokat, SAP, például az SQL Server 2012 által támogatott.
* Az SQL Server-példány, amelyre telepítve van a virtuális gépeket az Azure piactéren kínált rendezése nem áll a rendezést, az SAP NetWeaver az SQL Server-példány futtatásához szükséges. A rendezés módosíthatja, ha az az alábbi szakasz utasításait.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>A Microsoft Windows/SQL Server rendszerű virtuális gép az SQL Server-rendezés módosítása
Miután az SQL Server-lemezképek az Azure Marketplace-en nem állította be, amelyek szükségesek az SAP NetWeaver-alkalmazásaihoz, a rendezést használja az üzembe helyezést követően azonnal módosítani kell. Az SQL Server 2012 ezt megteheti az alábbi lépéseket követve, amint a virtuális gép már telepítve van, és a egy rendszergazda a központilag telepített virtuális gép be tud jelentkezni:

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

Ha nem ez az eredmény, állítsa le az SAP üzembe helyezése, és vizsgálja meg, miért érdemes a setup parancs volt a várt módon működik. Az üzembe helyezés az SAP NetWeaver-alkalmazásaihoz, mint a korábban említett különböző SQL Server megadására az SQL Server-példány az alakzatot **nem** támogatott.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>Az SQL Server magas rendelkezésre állás az SAP az Azure-ban
A tanulmány korábbi szakaszában leírtaknak nincs hozhat létre a megosztott tárolóról, ami szükséges a legrégebbi SQL Server magas rendelkezésre állású funkció használatára vonatkozó lehetőség. Ez a funkció telepíti legalább két SQL Server-példányokat a egy Windows Server feladatátvételi fürt (WSFC) megosztott lemezt használ a felhasználói adatbázisok (és végül a tempdb). Ez az a hosszú ideig standard magas rendelkezésre állású metódussal, amely az SAP által is támogatott. Az Azure nem támogatja a megosztott tárolót, mert az SQL Server magas rendelkezésre állású konfigurációkhoz a megosztott lemez fürtözött konfigurációban nem kell megvalósítani. Magas rendelkezésre állású számos egyéb módszerek azonban továbbra is lehetséges, és a következő szakaszok ismertetik.

#### <a name="sql-server-log-shipping"></a>Az SQL Server-Naplóküldés
Magas rendelkezésre ÁLLÁS módszer, az SQL Server Naplóküldést. Ha a magas rendelkezésre ÁLLÁSÚ konfigurációban részt vevő virtuális gépek névfeloldás működik, nem jelent problémát, és a beállítása az Azure-ban térnek el minden olyan beállítás, amely a helyszínen történik. Nem ajánlott, csak az IP-feloldási támaszkodik. Tartományállapot Naplóküldést és a Naplóküldést alapelveket beállítása, tekintse meg ezt a dokumentációt:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

Annak érdekében, hogy minden magas rendelkezésre állás, egyet kell Naplóküldést sebességgel belül az azonos Azure rendelkezésre állási csoport belüli virtuális gépek üzembe helyezése.

#### <a name="database-mirroring"></a>Az adatbázis-tükrözés
Az SAP által támogatott adatbázis-tükrözés (tekintse meg az SAP-Jegyzetnek [965908]) definiálása az SAP-kapcsolati karakterláncot a feladatátvételi partner támaszkodik. A létesítmények közötti esetek feltételezzük, hogy a két virtuális gép ugyanabban a tartományban vannak, és, hogy a felhasználói környezet a két SQL Server-példányokat, valamint a tartományi felhasználók csoportban futnak, és részt vevő két SQL Server-példányokat a megfelelő jogosultságokkal rendelkezik. Ezért a beállítása az Azure adatbázis-tükrözés nem egy tipikus helyszíni telepítés/konfigurációs eltérnek a.

Kezdődően kizárólag felhőalapú telepítések esetén a legegyszerűbb módszer, hogy egy másik tartomány beállítása az Azure-ban, ezek az adatbázis-kezelő virtuális gépek (és ideális dedikált SAP virtuális gépek) egy tartományon belül.

Ha egy tartomány nem lehetséges, egy is használható tanúsítványok esetében az adatbázis-tükrözési végpont itt leírtak szerint: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Oktatóanyagnak beállítása az adatbázis-tükrözés az Azure-ban, itt található: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQL Server Always On
Always On támogatott SAP helyszíni, (tekintse meg az SAP-Jegyzetnek [1772688]), együtt kell használni az SAP az Azure-ban támogatott. A tény, hogy Ön nem képes a megosztott lemezek létrehozásához az Azure-ban nem jelenti azt, hogy az egyik nem hozható létre egy mindig a Windows Server feladatátvételi fürt (WSFC) konfiguráció eltérő virtuális gépek között. Csak azt jelenti, hogy nem kell egy megosztott lemez adatokként a fürt konfigurációját a kvórum lehetőséget. Ezért hozhat létre egy mindig WSFC-konfigurációt az Azure-ban, és jelölje be a kvórum írja be a megosztott lemez. Azure-beli virtuális gépek vannak üzembe helyezve kell hárítsa el a virtuális gépek neve és a virtuális gépeket kell ugyanabban a tartományban. Ez a csak az Azure-ban és a helyszínek közötti központi telepítések esetében. Néhány szempontot, mobilmegoldások üzembe helyezéséhez az SQL Server rendelkezésre állási csoport figyelőjét (nem tévesztendő össze az Azure rendelkezésre állási csoport) Azure ezen a ponton a egyszerre nem engedélyezi a helyszíni lehetséges, mert az AD/DNS-objektum létrehozása óta. Ezért bizonyos másik telepítési lépések szükségesek, hogy az adott viselkedését az Azure.

Néhány szempontot, egy rendelkezésre állási csoport figyelőjének használatával a következők:

* Egy rendelkezésre állási csoport figyelőjének használata csak akkor lehetséges, a Windows Server 2012 vagy újabb vendég operációs rendszer a virtuális gép. A Windows Server 2012 rendszerhez kell győződjön meg arról, hogy érvényesek-e a javítást: <https://support.microsoft.com/kb/2854082> 
* A Windows Server 2008 R2, a javítás nem létezik, és mindig bekapcsolva kell egy feladatátvételi partner a kapcsolat-karakterlánc megadásával az adatbázis-tükrözés gazdagépprofilokkal megegyező módon használható (keresztül az SAP default.pfl paraméter adatbázisok/mss/kiszolgáló – lásd a SAP-Jegyzetnek [965908]).
* Egy rendelkezésre állási csoport figyelőjének használata esetén az adatbázis-beli virtuális gépek csatlakoznia kell egy dedikált terheléselosztóhoz. Nevezze el a csak Felhőbeli megoldás kellene vagy az SAP-rendszer (alkalmazás-kiszolgálók, az adatbázis-kezelő kiszolgáló és (A) SCS-kiszolgálón) minden virtuális gép ugyanazon a virtuális hálózaton, vagy egy SAP-alkalmazás rétegből kellene a etc\host fájl sorrendben a karbantartás a virtuális gép nevének feloldása az SQL Serveres virtuális gépek beolvasása. Annak érdekében, hogy, hogy az Azure van-e olyan esetekben, ahol mindkét virtuális gép lehet átváltani leállítási új IP-címet rendelni, egy kell hozzárendelése statikus IP-címeket a hálózati adapterek közül virtuális gépek (meghatározása a statikus IP-cím leírtmindigakonfigurációban[ez] [ virtual-networks-reserved-private-ip] cikk)

[comment]: <> (Régi blogok)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Megadása kötelező, ha létrehozásához a WSFC-fürt konfigurációját, ahol a fürt van szüksége egy speciális IP-cím rendelve, mert a jelenlegi működését az Azure rendelne a fürt neve azonos IP-címét, a csomópont a fürthöz a rendszer létrehozza a speciális lépésből áll. Ez azt jelenti, hogy a fürt egy másik IP-cím hozzárendelése egy manuális lépés kell elvégezni.
* A rendelkezésre állási csoport figyelőjének fog létrehozni az Azure-ban TCP/IP-végpontokkal, amely az elsődleges és másodlagos replikák rendelkezésre állási csoport futó, a virtuális gépekhez vannak hozzárendelve.
* Előfordulhat, hogy biztonságos hozzáférés-vezérlési ezeket a végpontokat kell.

[comment]: <> (Teendőlista-kezelő régi blog)
[comment]: <> (Részletes lépéseit és a egy AlwaysOn-konfigurációjának telepítése az Azure-ban szükségleti cikkek észlelt leginkább a amikor, ajánljuk figyelmébe az elérhető oktatóanyag [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (AlwaysOn beállításai keresztül az Azure-katalógus előre konfigurált <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Legjobb folyamata [this][virtual-machines-windows-classic-ps-sql-int-listener] az oktatóanyagban egy rendelkezésre állási csoport figyelőjének létrehozásáról az)
[comment]: <> (Hozzáférés-vezérlési biztonságossá tétele hálózati végpont vannak írva legjobb itt:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Keresztül történő központi telepítéséhez egy SQL Server Always On rendelkezésre állási csoport különböző Azure-régióban, valamint lehetőség. Ez a funkció kihasználja az Azure VNet – Vnet kapcsolat ([további részleteket][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Teendőlista-kezelő régi blog)
[comment]: <> (Ilyen esetben az SQL Server AlwaysOn rendelkezésre állási csoportok beállítása az itt leírtak szerint: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Az SQL Server magas rendelkezésre állás az Azure-ban összegzése
Adja meg azt a tényt, hogy az Azure Storage a tartalom védelmére, egy kisebb indok van arra a készenléti lemezképen mereven. Ez azt jelenti, hogy a magas rendelkezésre állású forgatókönyv csak a következő esetekben ellen védelmet biztosító van szüksége:

* A virtuális gép elérhetetlensége miatt a fürt az Azure-ban vagy más okból karbantartását teljes
* Az SQL Server-példány szoftverekkel kapcsolatos problémák
* Manuális hiba, ahol adatokat törli, és szükség időponthoz helyreállítási védelme

Megfelelő technológiák egy is is, hogy az első két esetben is fedezi az adatbázis-tükrözés vagy mindig bekapcsolva, mivel a harmadik eset csak fedezhető Naplóküldés Hibaoldal.

Always On, az adatbázis-tükrözés, összehasonlítva az előnyei az Always On összetettebb beállítása elosztása érdekében szüksége. Ezeket az előnyöket is listázva lehet hasonló:

* Olvasható másodlagos replikával.
* Biztonsági másolatok a másodlagos replikákon.
* Jobb méretezhetőséget.
* Több mint egy másodlagos replikára.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Az SQL Server általános az SAP az Azure összegzése
Ez az útmutató számos ajánlást, és olvassa, egynél több alkalommal az Azure-alapú tervezése előtt. Általánosságban véve, ügyeljen arra, hogy hajtsa végre az első tíz általános DBMS Azure adott pontokon:

[comment]: <> (2.3 a nagyobb átviteli sebességet, mint? Mint egy virtuális merevlemez?)
1. Használja a legújabb DBMS kiadását, például az SQL Server 2014 esetében, amely rendelkezik a legtöbb előnyeit az Azure-ban. Az SQL Server esetében ez az SQL Server 2012 SP1 CU4, amelynél az Azure Storage együtt biztonsági funkcióját. Azonban az SAP együtt ajánlott legalább SQL Server 2014 SP1 CU1 vagy SQL Server 2012 SP2 és a legújabb CU használatára.
2. Tervezze meg gondosan az SAP-rendszer környezetét az Azure-ban, az adatok fájl elrendezése és az Azure-korlátozások elosztása érdekében:
   * Nem túl sok lemezzel rendelkezik, de van-e elegendő annak érdekében, hogy a szükséges iops-érték érhető el.
   * Ha nem használ Managed Disks, ne felejtse el iops-t is korlátozott egy Azure Storage-fiókot, hogy, hogy a Storage-fiókok korlátozva, minden egyes Azure-előfizetésen belül ([további részleteket][azure-subscription-service-limits]). 
   * Csak a lemezeken, ha egy nagyobb átviteli sebességet érhet el kell stripe.
3. Ne telepítse a szoftvert, vagy telepítse azokat a fájlokat, a nem állandó és a semmit a meghajtón egy Windows újraindításkor elveszett adatmegőrzés a D:\ meghajtóra szükség van.
4. Ne használja a lemezek gyorsítótárazása az Azure standard szintű tárolást.
5. Ne használja az Azure-Tárfiókok georeplikált.  Az adatbázis-kezelő számítási feladatok esetében használja a helyileg redundáns.
6. Adatbázis-adatok replikálása a DBMS gyártója által biztosított magas rendelkezésre ÁLLÁS/Vészhelyreállítás megoldást használni.
7. Mindig névfeloldás használata, ne használjon IP-címeket.
8. A legmagasabb lehetséges adatbázis-tömörítést használni. Ez az oldal tömörítési SQL Serverhez.
9. Ügyeljen arra, hogy az SQL Server-rendszerképek használata az Azure Marketplace-ről. Ha egy SQL-kiszolgálót használ, módosítania kell a példány rendezési rajta minden olyan SAP NetWeaver rendszer telepítése előtt.
10. Telepítse és konfigurálja az SAP gazdagép figyelése az Azure-ban leírtak szerint [üzembe helyezési útmutató][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>A Windows SAP ASE rögzítésen
Kezdve a Microsoft Azure, könnyedén áttelepítheti a meglévő SAP ASE-alkalmazások az Azure Virtual Machines. A virtuális gépeken SAP ASE segítségével csökkentheti a teljes tulajdonosi költség, a központi telepítési, felügyeleti és karbantartási vállalati szánt alkalmazások egyszerűen telepítse át ezeket az alkalmazásokat a Microsoft Azure-bA. Az SAP ASE egy Azure virtuális gépet a rendszergazdák és fejlesztők számára továbbra is használhatják az azonos fejlesztési és felügyeleti eszközöket, amelyek a rendelkezésre álló helyi.

Nincs SLA-t az Azure Virtual Machines, amely itt található: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Vagyunk abban, hogy a Microsoft Azure futtatott virtuális gépek hajt végre, valamint támogatáshoz képest korlátozottabb más nyilvános felhőalapú virtualizációs megoldások, de az egyes eredmények eltérőek lehetnek. A különböző SAP SAP számú méretezési SAP tanúsítvánnyal rendelkező, virtuális gépek Termékváltozatait megtalálható egy különálló SAP-Jegyzetnek [1928533].

Utasításokat és javaslatokat a használat tekintetében az Azure Storage, SAP virtuális gépek üzembe helyezési vagy az SAP-figyelés során ez a dokumentum az első négy fejezetek leírtaknak alkalmazni, SAP ASE üzemelő példányok, SAP-alkalmazások együtt.

### <a name="sap-ase-version-support"></a>SAP ASE verzió támogatása
SAP jelenleg támogatja az SAP ASE verzió 16,0 SAP Business Suite való használatára. Kizárólag az SAP Service Marketplace-en keresztül, SAP ASE kiszolgáló, vagy a JDBC, és az ODBC illesztőprogramjai SAP Business Suite termékekkel használt minden frissítés biztosított: <https://support.sap.com/swdc>.

Telepítések meghajtóbetűjeleket a helyszínen, ne töltse le a SAP ASE-kiszolgáló, vagy a JDBC és az ODBC-illesztőprogramok frissítéseit közvetlenül a Sybase-webhelyeket. További információk a javítások, amely az SAP Business Suite termékek helyi használatát támogatja, és az Azure Virtual machines szolgáltatásban, tekintse meg az alábbi SAP-megjegyzések:

* [1590719]
* [1973241]

Általános információk a futó SAP Business Suite az SAP ASE megtalálható a [Állapotváltozás](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE beállítási útmutatója az Azure-beli virtuális gépeken SAP kapcsolatos SAP ASE telepítések
#### <a name="structure-of-the-sap-ase-deployment"></a>Az SAP ASE üzembe helyezés szerkezete
Az általános leírása megfelelően SAP ASE végrehajtható fájlok található, vagy el kell telepíteni a virtuális gép operációsrendszer-lemez rendszermeghajtóján (c: meghajtó\). Általában az SAP ASE rendszer és eszközök adatbázisok többsége nem használ keményen SAP NetWeaver munkaterhelés szerint. Ezért a rendszer és eszközök adatbázisok (master, model, saptools, sybmgmtdb, sybsystemdb) továbbra is használhatja, valamint a C:\ meghajtóra. 

Kivétel oka az lehet az összes munkahelyi táblákat és SAP ASE, amely bizonyos SAP ERP, és minden BW számítási feladatok esetén lehet szükség, vagy nagyobb adatmennyiség, vagy nem fér el az eredeti virtuális gép operációs Rendszeréhez, i/o műveletek kötet által létrehozott ideiglenes táblák tartalmazó ideiglenes adatbázis lemez (c: meghajtó\).

A rendszer telepítéséhez használt SAPInst/SWPM verziójától függően az adatbázis tartalmazhatja:

* Egyetlen SAP ASE TempDB-adatbázisa, amely SAP ASE telepítésekor jött létre
* Egy SAP ASE tempdb, SAP ASE és a egy további, a SAP telepítési eljárás által létrehozott saptempdb telepítése által létrehozott
* Egy SAP ASE tempdb, SAP ASE és a egy további tempdb manuálisan létrehozott telepítése által létrehozott (például az SAP-Jegyzetnek következő [1752266]) ERP/BW adott tempdb követelményeinek

Adott ERP vagy az összes BW számítási feladatokhoz logikus, teljesítmény érdekében, hogy a tempdb-eszközök a emellett létrehozott TempDB (SWPM vagy manuálisan) egy másik C:\ meghajtóra tekintetében. Ha nincsenek további tempdb létezik, akkor javasoljuk, hogy hozzon létre egyet (SAP-Jegyzetnek [1752266]).

Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani a emellett létrehozott TempDB:

* Az első tempdb eszköz az első eszköz, az SAP adatbázis áthelyezése
* A tempdb eszközök hozzáadása az egyes egy eszközt, az SAP-adatbázist tartalmazó VHD-k

Ez a konfiguráció lehetővé teszi, hogy a tempdb vagy fel több helyet több, mint a rendszermeghajtó tud biztosítani. Referenciaként egy ellenőrizheti a meglévő rendszerek esetében a helyszíni futtatásához, amelyek a tempdb eszköz méretek. Vagy ilyen konfiguráció lehetővé tenné az IOPS-számokkal tempdb, amely a rendszermeghajtó nem található, fogyasztóalapú ellen. Újra a helyszíni rendszerben használható i/o-számítási feladatok tempdb ellen.

Soha ne helyezzen SAP ASE eszközök alakzatot a D:\ meghajtóra, a virtuális gép. Ez vonatkozik a TempDB adatbázist, akkor is, ha a TempDB adatbázisban tárolt objektumok csak átmeneti.

#### <a name="impact-of-database-compression"></a>Adatbázis tömörítése hatása
A konfigurációk, ahol i/o-sávszélesség korlátozó válhat minden mérték, ami csökkenti az IOPS segíthet a munkaterhelés, mint például az Azure IaaS-forgatókönyveknél is futtatható egy többhelyes. Ezért ajánlott annak biztosításához, hogy egy meglévő SAP-adatbázist az Azure-ba való feltöltéséhez használ-e a SAP ASE tömörítést.

Azt javasoljuk, hogy végre tömörítést feltöltése az Azure-ba, ha azt nem érhető előtt kívül számos oka van megadva:

* Az Azure-bA feltölteni kívánt adatok mennyisége kisebb
* A tömörítés végrehajtás időtartama rövidebb, feltéve, hogy az egyik használjon erősebb hardveres további processzorokat vagy magasabb i/o-sávszélességet vagy kisebb I/O várakozási ideje a helyszíni
* Kisebb adatbázis maximális mérete kisebb költségek lemezfoglaláshoz vezethet

Adatok és a LOB-tömörítés az üzemeltetett Azure Virtual Machines szolgáltatásban, mint a helyszíni virtuális gépek működnek. További részleteket a hogyan ellenőrizheti, ha a tömörítés elem már szerepel egy létező adatbázisban az SAP ASE használja, ellenőrizze az SAP-Jegyzetnek [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Adatbázis-példányok figyelése céljából DBACockpit használatával
Az SAP-rendszereit, amelyek használ adatbázis-platformot, SAP ASE, a DBACockpit érhető el tranzakcióban DBACockpit beágyazott böngészőablakot vagy Webdynpro. Figyelése és felügyelete az adatbázis összes funkcióját azonban csak a DBACockpit Webdynpro végrehajtásának érhető el.

Mint a helyszíni rendszerekkel való több lépésre van szükség a DBACockpit Webdynpro végrehajtásának által használt minden SAP NetWeaver funkciók engedélyezéséhez. Hajtsa végre az SAP-Jegyzetnek [1245200] webdynpros használatának engedélyezése, és a szükséges kapcsolatok létrehozásához. Ha a fenti megjegyzések utasításait követve konfigurálnia is az internetes kommunikáció kezelése (icm) együtt használható a http és https-kapcsolatok a portokat. A http alapértelmezés szerint a következőhöz hasonló:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

és a tranzakció jön létre hivatkozások DBACockpit ehhez hasonló:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően, és ha igen, az SAP-rendszert futtató Azure virtuális gép csatlakoztatva van a site-to-site, keresztül többhelyes vagy ExpressRoute (a létesítmények közötti üzembe helyezés), győződjön meg arról, hogy ICM kell használ egy teljesen minősített állomásnév megoldható a gépen, Nyissa meg a DBACockpit a kívánt. Tekintse meg az SAP-Jegyzetnek [773830] megérteni, hogyan ICM határozza meg a profil paramétereinek és a set paraméter icm/host_name_full függően teljesen minősített állomásnév explicit módon ha szükséges.

Ha a virtuális gép egy kizárólag felhőalapú a forgatókönyvben a helyszíni és az Azure közötti létesítmények közötti kapcsolat nélkül telepítette, egy nyilvános IP-cím és a egy domainlabel megadása szeretne. A virtuális gép nyilvános DNS-név formátuma a következőhöz hasonló:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

A DNS-név kapcsolatos további információk találhatók [Itt][virtual-machines-azurerm-versus-azuresm].

Az Azure virtuális Gépen a kapcsolat DNS-nevét állítja az SAP profil paraméter icm/host_name_full hasonlóan néznének ki:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Ebben az esetben kell ügyeljen arra, hogy:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoport ICM folytatott kommunikációhoz használt portok számára az Azure Portalon
* Bejövő szabályok a ICM folytatott kommunikációhoz használt portok számára a Windows tűzfal-konfiguráció hozzáadása

Az egy automatizált importálja az összes rendelkezésre álló javítások javasoljuk, hogy rendszeres időközönként a alkalmazni a javítás gyűjtemény SAP az SAP-verzióra vonatkozó Megjegyzés::

* [1558958]
* [1619967]
* [1882376]

SAP ASE DBA vezérlőpultja további információ található a következő SAP-megjegyzések:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE biztonsági mentési/helyreállítási szempontjai
Üzembe helyezésekor SAP ASE az Azure-ba, a biztonsági mentési módszerek át kell tekinteni. Még ha a rendszer nem egy hatékony rendszer, az SAP-adatbázist, SAP ASE által üzemeltetett kell készíteni rendszeres időközönként. Mivel az Azure Storage három rendszerkép tartja, a biztonsági mentés már kevésbé fontos tárolási összeomlás kompenzáló tekintetben. A legfőbb oka egy megfelelő biztonsági mentési és helyreállítási terv karbantartása sokkal többet lehet kompenzálni a logikai vagy manuális hibákat azáltal, hogy az idő helyreállítási lehetőségei a pont. Így az célja, hogy mindkét használata biztonsági mentések, állítsa vissza az adatbázist vissza egy bizonyos ponton az időben, vagy használhatja a biztonsági másolatokat az Azure használatával ültet be egy másik rendszer a meglévő adatbázis másolásával. Például meg tudta átvinni 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása egy adott rendszer biztonsági mentésének visszaállításával.

Biztonsági mentése és visszaállítása egy adatbázist az Azure-ban ugyanúgy működik, mint a helyszíni. Tekintse meg az SAP-megjegyzések:

* [1588316]
* [1585981]

További információ memóriakép létrehozásakor konfigurációk és ütemezési biztonsági mentéseket. A stratégia és konfigurálható követelményektől függően adatbázis és naplófájlok listázása, lemez alakzatot a meglévő lemezek közül, vagy adja hozzá a biztonsági másolat egy további lemezt. Csökkentse a hiba az adatvesztést, ahol nincs adatbázis-eszköz nem található lemez használata ajánlott.

Adat - és ÜZLETÁGI mellett tömörítési SAP ASE biztonságimásolat-tömörítési funkciók is kínál. Az adatbázis és naplófájlok memóriaképek a kevesebb helyet foglalnak biztonságimásolat-tömörítési funkciók használata ajánlott. További információkért tekintse meg az SAP-Jegyzetnek [1588316]. A biztonsági másolat tömörítése elengedhetetlen is át lehet adni, ha azt tervezi, hogy töltse le a biztonsági mentések és a VHD-fájlokat tartalmazó helyszíni az Azure virtuális gép biztonsági mentési memóriaképek adatmennyiség csökkentése érdekében.

Ne használja a D:\ meghajtóra adatbázist vagy naplófájlokat memóriakép célként.

#### <a name="performance-considerations-for-backupsrestores"></a>Teljesítménnyel kapcsolatos megfontolások a biztonsági mentés/visszaállítás
Operációs rendszer nélküli telepítések, mint a biztonsági mentési/visszaállítási teljesítménye szolgáltatás hány kötetek párhuzamosan olvasható, és az átviteli sebességet, a köteteket lehet függ. Emellett a biztonságimásolat-tömörítési funkciók által használt CPU-felhasználás előfordulhat, hogy fontos szerepet játszik a virtuális gépek nyolc CPU szállal. Ezért egy feltételezheti, hogy:

* A kevesebb, a lemezek számát fogja tárolni, az adatbázis-eszközökre, annál kisebb a teljes átviteli sebesség beolvasás
* Minél kisebb a virtuális gépen, a jogosultságeszkalálást szálak száma a biztonságimásolat-tömörítési funkciók hatását
* A kevesebb céllal (lemezek Stripe könyvtárak) írni a biztonsági mentés, a kisebb az átviteli sebesség

Növelje a tárolók két módon, amely lehet igényeitől függően használt/kombinált írni:

* A biztonsági mentési célkötet szétosztott több csatlakoztatott lemez keresztül az IOPS átviteli sebességet a köteten lévő csíkozott javítása érdekében
* Memóriakép konfigurációt SAP ASE szintjén hoz létre, amely használatával egynél több céloldali könyvtár a memóriaképet való írása

A kötet szétosztott keresztül több csatlakoztatott lemez szakasz tárgyalja című szakaszában talál. További információ a több címtár segítségével SAP ASE memóriakép konfigurációjában tárolt eljárás sp_config_dump, amelyet hozza létre a memóriakép konfigurációt a dokumentációban tájékozódhat a [Sybase az Adatközpont](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Vészhelyreállítás az Azure-alapú virtuális gépekhez
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Az Adatreplikációt a Sybase-replikálás SAP-kiszolgáló
Az SAP Sybase replikációs Server SRS-SAP ASE-megoldást kínál, amellyel meleg készenléti adatbázis-tranzakciók aszinkron átvitelét távoli helyre. 

A telepítés és a művelet az SRS működik, valamint funkcionálisan üzemeltetett virtuális gépek Azure-szolgáltatások, mint a helyszíni virtuális gépen.

SAP ASE HADR nem igényel Azure Internal Load Balancer, és nem rendelkezik az operációs rendszer szintű fürtszolgáltatás függőségek, és az Azure Windows és Linux rendszerű virtuális gépek is működik. Az SAP ASE HADR részleteiért olvassa el a [SAP ASE HADR felhasználói útmutató](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>A linuxon futó SAP ASE rögzítésen
Kezdve a Microsoft Azure, könnyedén áttelepítheti a meglévő SAP ASE-alkalmazások az Azure Virtual Machines. A virtuális gépeken SAP ASE segítségével csökkentheti a teljes tulajdonosi költség, a központi telepítési, felügyeleti és karbantartási vállalati szánt alkalmazások egyszerűen telepítse át ezeket az alkalmazásokat a Microsoft Azure-bA. Az SAP ASE egy Azure virtuális gépet a rendszergazdák és fejlesztők számára továbbra is használhatják az azonos fejlesztési és felügyeleti eszközöket, amelyek a rendelkezésre álló helyi.

Azure virtuális gépek üzembe helyezéséhez fontos tudni, hogy a hivatalos SLA-k, amely itt található: <https://azure.microsoft.com/support/legal/sla>

SAP méretezése információk és az SAP-minősítéssel rendelkező virtuális gépek Termékváltozatait listáját van megadva, a SAP-Jegyzetnek [1928533]. Dokumentumok méretezése az Azure-beli virtuális gépek itt található további SAP <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> és itt <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Utasításokat és javaslatokat a használat tekintetében az Azure Storage, SAP virtuális gépek üzembe helyezési vagy az SAP-figyelés során ez a dokumentum az első négy fejezetek leírtaknak alkalmazni, SAP ASE üzemelő példányok, SAP-alkalmazások együtt.

A következő két SAP-megjegyzések közé tartozik a Linux-és ASE ASE kapcsolatos általános adatok a felhőben:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE verzió támogatása
SAP jelenleg támogatja az SAP ASE verzió 16,0 SAP Business Suite való használatára. Kizárólag az SAP Service Marketplace-en keresztül, SAP ASE kiszolgáló, vagy a JDBC, és az ODBC illesztőprogramjai SAP Business Suite termékekkel használt minden frissítés biztosított: <https://support.sap.com/swdc>.

Telepítések meghajtóbetűjeleket a helyszínen, ne töltse le a SAP ASE-kiszolgáló, vagy a JDBC és az ODBC-illesztőprogramok frissítéseit közvetlenül a Sybase-webhelyeket. További információk a javítások, amely az SAP Business Suite termékek helyi használatát támogatja, és az Azure Virtual machines szolgáltatásban, tekintse meg az alábbi SAP-megjegyzések:

* [1590719]
* [1973241]

Általános információk a futó SAP Business Suite az SAP ASE megtalálható a [Állapotváltozás](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE beállítási útmutatója az Azure-beli virtuális gépeken SAP kapcsolatos SAP ASE telepítések
#### <a name="structure-of-the-sap-ase-deployment"></a>Az SAP ASE üzembe helyezés szerkezete
Az általános leírása megfelelően SAP ASE végrehajtható fájlok legyen található vagy a legfelső szintű fájl rendszerbe a virtuális gép (/sybase) telepítve van. Általában az SAP ASE rendszer és eszközök adatbázisok többsége nem integritással keményen SAP NetWeaver számítási feladatot. Ezért a rendszer és eszközök adatbázisok (master, model, saptools, sybmgmtdb, sybsystemdb) továbbra is használhatja, valamint a legfelső szintű fájlrendszer. 

Kivétel oka az lehet az összes munkahelyi táblákat és SAP ASE vagy igénylő néhány SAP ERP, és minden BW számítási feladatok esetén előfordulhat, hogy nagyobb adatmennyiség vagy az i/o-műveletek, kötet, amely nem illik az eredeti virtuális gép operációs rendszer által létrehozott ideiglenes táblák tartalmazó ideiglenes adatbázis a lemez.

A rendszer telepítéséhez használt SAPInst/SWPM verziójától függően az adatbázis tartalmazhatja:

* Egyetlen SAP ASE TempDB-adatbázisa, amely SAP ASE telepítésekor jött létre
* Egy SAP ASE tempdb, SAP ASE és a egy további, a SAP telepítési eljárás által létrehozott saptempdb telepítése által létrehozott
* Egy SAP ASE tempdb, SAP ASE és a egy további tempdb manuálisan létrehozott telepítése által létrehozott (például az SAP-Jegyzetnek következő [1752266]) ERP/BW adott tempdb követelményeinek

Adott ERP vagy az összes BW számítási feladatokhoz logikus, teljesítmény érdekében, hogy a tempdb-eszközök a emellett létrehozott TempDB (SWPM vagy manuálisan) egy különálló fájlrendszerben, amely egységes Azure adatlemez vagy egy Linux RAID spannin jelölhető tekintetében g több Azure-adatlemezek. Ha nincsenek további tempdb létezik, akkor javasoljuk, hogy hozzon létre egyet (SAP-Jegyzetnek [1752266]).

Az ilyen rendszerek esetében a következő lépéseket kell végrehajtani a emellett létrehozott TempDB:

* Az első tempdb könyvtár az első fájlrendszer az SAP adatbázis áthelyezése
* Minden, az SAP-adatbázist egy fájlrendszer tartalmazó lemez hozzáadása a tempdb-könyvtárak

Ez a konfiguráció lehetővé teszi, hogy a tempdb vagy fel több helyet több, mint a rendszermeghajtó tud biztosítani. Referenciaként egy ellenőrizheti a meglévő rendszerek esetében a helyszíni futtatásához, amelyek a tempdb directory méretek. Vagy ilyen konfiguráció lehetővé tenné az IOPS-számokkal tempdb, amely a rendszermeghajtó nem található, fogyasztóalapú ellen. Újra a helyszíni rendszerben használható i/o-számítási feladatok tempdb ellen.

Soha ne helyezze az SAP ASE könyvtárak /mnt vagy a virtuális gép /mnt/resource. Ez vonatkozik a TempDB adatbázist, akkor is, ha a TempDB adatbázisban tárolt objektumok csak ideiglenes /mnt vagy /mnt/resource nem az alapértelmezett Azure virtuális gép ideiglenes adhatja, amely nem állandó. További információt az Azure virtuális gép ideiglenes terület található [Ez a cikk][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Adatbázis tömörítése hatása
A konfigurációk, ahol i/o-sávszélesség korlátozó válhat minden mérték, ami csökkenti az IOPS segíthet a munkaterhelés, mint például az Azure IaaS-forgatókönyveknél is futtatható egy többhelyes. Ezért ajánlott annak biztosításához, hogy egy meglévő SAP-adatbázist az Azure-ba való feltöltéséhez használ-e a SAP ASE tömörítést.

Azt javasoljuk, hogy végre tömörítést feltöltése az Azure-ba, ha azt nem érhető előtt kívül számos oka van megadva:

* Az Azure-bA feltölteni kívánt adatok mennyisége kisebb
* A tömörítés végrehajtás időtartama rövidebb, feltéve, hogy az egyik használjon erősebb hardveres további processzorokat vagy magasabb i/o-sávszélességet vagy kisebb I/O várakozási ideje a helyszíni
* Kisebb adatbázis maximális mérete kisebb költségek lemezfoglaláshoz vezethet

Adatok és a LOB-tömörítés az üzemeltetett Azure Virtual Machines szolgáltatásban, mint a helyszíni virtuális gépek működnek. További részleteket a hogyan ellenőrizheti, ha a tömörítés elem már szerepel egy létező adatbázisban az SAP ASE használja, ellenőrizze az SAP-Jegyzetnek [1750510]. Adatbázis tömörítése kapcsolatos további információkért tekintse meg az SAP-Jegyzetnek [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Adatbázis-példányok figyelése céljából DBACockpit használatával
Az SAP-rendszereit, amelyek használ adatbázis-platformot, SAP ASE, a DBACockpit érhető el tranzakcióban DBACockpit beágyazott böngészőablakot vagy Webdynpro. Figyelése és felügyelete az adatbázis összes funkcióját azonban csak a DBACockpit Webdynpro végrehajtásának érhető el.

Mint a helyszíni rendszerekkel való több lépésre van szükség a DBACockpit Webdynpro végrehajtásának által használt minden SAP NetWeaver funkciók engedélyezéséhez. Hajtsa végre az SAP-Jegyzetnek [1245200] webdynpros használatának engedélyezése, és a szükséges kapcsolatok létrehozásához. Ha a fenti megjegyzések utasításait követve konfigurálnia is az internetes kommunikáció kezelése (icm) együtt használható a http és https-kapcsolatok a portokat. A http alapértelmezés szerint a következőhöz hasonló:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

és a tranzakció jön létre hivatkozások DBACockpit hasonlóan néz ki ez:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően, és ha igen, az SAP-rendszert futtató Azure virtuális gép csatlakoztatva van a site-to-site, keresztül többhelyes vagy ExpressRoute (a létesítmények közötti üzembe helyezés), győződjön meg arról, hogy ICM kell használ egy teljesen minősített állomásnév megoldható a gépen, Nyissa meg a DBACockpit a kívánt. Tekintse meg az SAP-Jegyzetnek [773830] megérteni, hogyan ICM határozza meg a profil paramétereinek és a set paraméter icm/host_name_full függően teljesen minősített állomásnév explicit módon ha szükséges.

Ha a virtuális gép egy kizárólag felhőalapú a forgatókönyvben a helyszíni és az Azure közötti létesítmények közötti kapcsolat nélkül telepítette, egy nyilvános IP-cím és a egy domainlabel megadása szeretne. A virtuális gép nyilvános DNS-név formátuma a következőhöz hasonló:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

A DNS-név kapcsolatos további információk találhatók [Itt][virtual-machines-azurerm-versus-azuresm].

Az Azure virtuális Gépen a kapcsolat DNS-nevét állítja az SAP profil paraméter icm/host_name_full hasonlóan néznének ki:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Ebben az esetben kell ügyeljen arra, hogy:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoport ICM folytatott kommunikációhoz használt portok számára az Azure Portalon
* Bejövő szabályok a ICM folytatott kommunikációhoz használt portok számára a Windows tűzfal-konfiguráció hozzáadása

Az egy automatizált importálja az összes rendelkezésre álló javítások javasoljuk, hogy rendszeres időközönként a alkalmazni a javítás gyűjtemény SAP az SAP-verzióra vonatkozó Megjegyzés::

* [1558958]
* [1619967]
* [1882376]

SAP ASE DBA vezérlőpultja további információ található a következő SAP-megjegyzések:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE biztonsági mentési/helyreállítási szempontjai
SAP ASE Azure-ba való telepítésekor a biztonsági mentési módszerek át kell tekinteni. Még ha a rendszer nem egy hatékony rendszer, az SAP-adatbázist, SAP ASE által üzemeltetett kell készíteni rendszeres időközönként. Mivel az Azure Storage három rendszerkép tartja, a biztonsági mentés már kevésbé fontos tárolási összeomlás kompenzáló tekintetben. A legfőbb oka egy megfelelő biztonsági mentési és helyreállítási terv karbantartása sokkal többet lehet kompenzálni a logikai vagy manuális hibákat azáltal, hogy az idő helyreállítási lehetőségei a pont. Így az célja, hogy mindkét használata biztonsági mentések, állítsa vissza az adatbázist vissza egy bizonyos ponton az időben, vagy használhatja a biztonsági másolatokat az Azure használatával ültet be egy másik rendszer a meglévő adatbázis másolásával. Például meg tudta átvinni 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása egy adott rendszer biztonsági mentésének visszaállításával.

Biztonsági mentése és visszaállítása egy adatbázist az Azure-ban ugyanúgy működik, mint a helyszíni. Tekintse meg az SAP-megjegyzések:

* [1588316]
* [1585981]

További információ memóriakép létrehozásakor konfigurációk és ütemezési biztonsági mentéseket. A stratégia és konfigurálható követelményektől függően adatbázis és naplófájlok listázása, lemez alakzatot a meglévő lemezek közül, vagy adja hozzá a biztonsági másolat egy további lemezt. Adatvesztés hiba esetén a veszély csökkentése érdekében ajánlott, ahol nincs adatbázis-directory/fájl nem található olyan lemez használatára.

Adat - és ÜZLETÁGI mellett tömörítési SAP ASE biztonságimásolat-tömörítési funkciók is kínál. Az adatbázis és naplófájlok memóriaképek a kevesebb helyet foglalnak biztonságimásolat-tömörítési funkciók használata ajánlott. További információkért tekintse meg az SAP-Jegyzetnek [1588316]. A biztonsági másolat tömörítése elengedhetetlen is át lehet adni, ha azt tervezi, hogy töltse le a biztonsági mentések és a VHD-fájlokat tartalmazó helyszíni az Azure virtuális gép biztonsági mentési memóriaképek adatmennyiség csökkentése érdekében.

Ne használja az Azure virtuális gép ideiglenes terület /mnt vagy /mnt/resource adatbázist vagy naplófájlokat memóriakép célként.

#### <a name="performance-considerations-for-backupsrestores"></a>Teljesítménnyel kapcsolatos megfontolások a biztonsági mentés/visszaállítás
Operációs rendszer nélküli telepítések, mint a biztonsági mentési/visszaállítási teljesítménye szolgáltatás hány kötetek párhuzamosan olvasható, és az átviteli sebességet, a köteteket lehet függ. Emellett a biztonságimásolat-tömörítési funkciók által használt CPU-felhasználás előfordulhat, hogy fontos szerepet játszik a virtuális gépek nyolc CPU szállal. Ezért egy feltételezheti, hogy:

* A kevesebb, a lemezek számát fogja tárolni, az adatbázis-eszközökre, annál kisebb a teljes átviteli sebesség beolvasás
* Minél kisebb a virtuális gépen, a jogosultságeszkalálást szálak száma a biztonságimásolat-tömörítési funkciók hatását
* A kevesebb céllal (Linux szoftveres RAID, a lemezek) írni a biztonsági mentés, a kisebb az átviteli sebesség

Növelje a tárolók két módon, amely lehet igényeitől függően használt/kombinált írni:

* A biztonsági mentési célkötet szétosztott több csatlakoztatott lemez keresztül az IOPS átviteli sebességet a köteten lévő csíkozott javítása érdekében
* Memóriakép konfigurációt SAP ASE szintjén hoz létre, amely használatával egynél több céloldali könyvtár a memóriaképet való írása

A kötet szétosztott keresztül több csatlakoztatott lemez szakasz tárgyalja című szakaszában talál. További információ a több címtár segítségével SAP ASE memóriakép konfigurációjában tárolt eljárás sp_config_dump, amelyet hozza létre a memóriakép konfigurációt a dokumentációban tájékozódhat a [Sybase az Adatközpont](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Vészhelyreállítás az Azure-alapú virtuális gépekhez
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Az Adatreplikációt a Sybase-replikálás SAP-kiszolgáló
Az SAP Sybase replikációs Server SRS-SAP ASE-megoldást kínál, amellyel meleg készenléti adatbázis-tranzakciók aszinkron átvitelét távoli helyre. 

A telepítés és a művelet az SRS működik, valamint funkcionálisan üzemeltetett virtuális gépek Azure-szolgáltatások, mint a helyszíni virtuális gépen.

ASE HADR SAP replikációs kiszolgálón keresztül nem támogatott ezen a ponton az időben. Előfordulhat, hogy ez teszteltük, és a jövőben jelent meg a Microsoft Azure platformon.

## <a name="specifics-to-oracle-database-on-windows"></a>Oracle-adatbázishoz a Windows tulajdonságairól
Oracle-szoftverek futtatásához a Microsoft Windows Hyper-V és az Azure-on Oracle támogatja. A részleteket a Windows Hyper-V és az Azure általános támogatási tekintse meg: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Az általános támogatási követően az adott forgatókönyvtől az Oracle-adatbázisok felhasználásával SAP-alkalmazások használata is támogatott. A dokumentum ezen részében a részletek neve.

### <a name="oracle-version-support"></a>Oracle-verzió támogatása
Oracle-verziókról és a megfelelő operációs rendszer verziója, az SAP-Jegyzetnek találja az SAP futtatása az Oracle Azure virtuális gépeken támogatott [2039619].

Általános információk az SAP Business Suite futó Oracle 1DX található: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Oracle-beállítási útmutatója az Azure-beli virtuális gépeken SAP-telepítések
#### <a name="storage-configuration"></a>Tároló konfigurálása
Csak egyetlen példány Oracle NTFS Fájlrendszerrel formázott lemezek használata támogatott. Adatbázis összes fájlja az NTFS fájlrendszer, a VHD-k vagy a Managed Disks alapján kell tárolni. Ezeket a lemezeket az Azure virtuális géphez csatlakoztatott és a rendszer az Azure BLOB Storage-lap (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy a Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Valamilyen hálózati meghajtók vagy az Azure-szolgáltatásokhoz hasonlóan a távoli megosztások:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

vannak **nem** támogatott Oracle-adatbázis fájlok!

Lemezek használata Azure lap BLOB Storage- vagy Managed Disks, ez a fejezet a dokumentum nyilatkozatok alapján [virtuális gépek és az adatlemezek gyorsítótárazás] [ dbms-guide-2.1] és [a Microsoft Azure Storage] [ dbms-guide-2.3] központi telepítések, az Oracle-adatbázishoz, valamint a alkalmazni.

Amint azt korábban a dokumentum általános része, az Azure-lemezek IOPS átviteli kvótái létezik. A pontos kvóták a virtuális gép típusától függően használhatók. Saját kvótával rendelkező virtuális gépek típusainak listáját találja [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A támogatott Azure-beli Virtuálisgép-típusok azonosítását, tekintse meg az SAP-jegyzetnek [1928533].

Mindaddig, amíg a jelenlegi IOPS-kvóta lemezenként megfelel a követelményeknek, lehetőség több egyetlen csatlakoztatott lemez DB-fájlok tárolására. 

Ha további IOPS szükség, javasoljuk, hogy használatát ablak Tárolókészletek (csak elérhető a Windows Server 2012 és újabb) vagy a Windows rendszerben, használjon csíkozást a Windows 2008 R2 hozhat létre egy nagy méretű logikai eszköz több csatlakoztatott lemezek (lásd még: fejezet [ Szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban). Ez a megközelítés leegyszerűsíti az adminisztratív terhelés kezeléséhez a lemezterületet, és elkerülhető az részéről az erőfeszítés, manuálisan több csatlakoztatott lemezek között oszthatja el a fájlokat.

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
Biztonsági mentés / visszaállítás működéséhez, az SAP-BR * Oracle eszközöket támogat a ugyanúgy, mint a standard szintű Windows Server operációs rendszerek és a Hyper-V. Oracle-helyreállítás-kezelő (RMAN) használata is támogatott a lemezre, és a lemezről visszaállítani a biztonsági mentésekhez.

#### <a name="high-availability"></a>Magas rendelkezésre állás
Oracle Data Guard támogatott magas rendelkezésre állású és vész-helyreállítási céllal. A részletek megtalálhatók [ez] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentációját.

#### <a name="other"></a>Egyéb
Egyéb általános területeken, például az Azure rendelkezésre állási csoportok vagy az SAP-figyelés a alkalmazni: az első három fejezetek a jelen dokumentum az Oracle-adatbázishoz, valamint a virtuális gépek központi telepítései leírtak szerint.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Oracle Database, Oracle Linux rendszeren rögzítésen
Oracle-szoftverek futtatásához a Microsoft Windows Hyper-V és az Azure-on Oracle támogatja. A részleteket a Windows Hyper-V és az Azure általános támogatási tekintse meg: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Az általános támogatási követően az adott forgatókönyvtől az Oracle-adatbázisok felhasználásával SAP-alkalmazások használata is támogatott. A dokumentum ezen részében a részletek neve.

### <a name="oracle-version-support"></a>Oracle-verzió támogatása
Oracle-verziókról és a megfelelő operációs rendszer verziója, az SAP-Jegyzetnek találja az SAP futtatása az Oracle Azure virtuális gépeken támogatott [2039619].

Általános információk az SAP Business Suite futó Oracle 1DX található: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Oracle-beállítási útmutatója az Azure-beli virtuális gépeken SAP-telepítések
#### <a name="storage-configuration"></a>Tároló konfigurálása
Csak az Oracle ext3 használatával, ext4 és xfs formázott lemezek egyetlen példányt támogat. Ezekhez a fájlrendszerekhez VHD-k vagy a Managed Disks-alapú adatbázis összes fájlja kell tárolni. Ezeket a lemezeket az Azure virtuális géphez csatlakoztatott és a rendszer az Azure BLOB Storage-lap (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy a Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Valamilyen hálózati meghajtók vagy az Azure-szolgáltatásokhoz hasonlóan a távoli megosztások:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

vannak **nem** támogatott Oracle-adatbázis fájlok!

Lemezek használata Azure lap BLOB Storage- vagy Managed Disks, ez a fejezet a dokumentum nyilatkozatok alapján [virtuális gépek és az adatlemezek gyorsítótárazás] [ dbms-guide-2.1] és [a Microsoft Azure Storage] [ dbms-guide-2.3] központi telepítések, az Oracle-adatbázishoz, valamint a alkalmazni.

Amint azt korábban a dokumentum általános része, az Azure-lemezek IOPS átviteli kvótái létezik. A pontos kvóták a virtuális gép típusától függően használhatók. Saját kvótával rendelkező virtuális gépek típusainak listáját találja [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

A támogatott Azure-beli Virtuálisgép-típusok azonosítását, tekintse meg az SAP-jegyzetnek [1928533]

Mindaddig, amíg a jelenlegi IOPS-kvóta lemezenként megfelel a követelményeknek, lehetőség több egyetlen csatlakoztatott lemez DB-fájlok tárolására. 

Ha további IOPS szükség, ajánlott LVM (a Logical Volume Manager) vagy MDADM segítségével hozzon létre egy logikai nagy több csatlakoztatott lemez keresztül. Lásd még: fejezet [szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban. Ez a megközelítés leegyszerűsíti az adminisztratív terhelés kezeléséhez a lemezterületet, és elkerülhető az részéről az erőfeszítés, manuálisan több csatlakoztatott lemezek között oszthatja el a fájlokat.

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
Biztonsági mentés / visszaállítás működéséhez, az SAP-BR * Oracle eszközöket támogat a ugyanúgy, mint az operációs rendszer nélküli és a Hyper-V. Oracle-helyreállítás-kezelő (RMAN) használata is támogatott a lemezre, és a lemezről visszaállítani a biztonsági mentésekhez.

#### <a name="high-availability"></a>Magas rendelkezésre állás
Oracle Data Guard támogatott magas rendelkezésre állású és vész-helyreállítási céllal. A részletek megtalálhatók [ez] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentációját.

#### <a name="other"></a>Egyéb
Egyéb általános területeken, például az Azure rendelkezésre állási csoportok vagy az SAP-figyelés a alkalmazni: az első három fejezetek a jelen dokumentum az Oracle-adatbázishoz, valamint a virtuális gépek központi telepítései leírtak szerint.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Az SAP-MaxDB adatbázist, a Windows-adatait
### <a name="sap-maxdb-version-support"></a>SAP MaxDB verzió támogatása
SAP jelenleg támogatja az SAP MaxDB verzió 7.9 SAP NetWeaver-alapú termékeknél az Azure-ban való használatra. Kizárólag az SAP Service Marketplace-en keresztül, minden frissítés SAP MaxDB kiszolgáló, vagy az SAP NetWeaver-alapú termékeknél használandó JDBC és az ODBC illesztőprogramjai biztosított <https://support.sap.com/swdc>.
Általános információk a SAP NetWeaver futó SAP MaxDB található <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>A Microsoft Windows-verziók és az Azure-beli Virtuálisgép-típusok támogatott SAP MaxDB adatbázis-kezelő
A Microsoft Windows támogatott verzióját az Azure-beli SAP MaxDB DBMS című témakörben talál:

* [SAP-termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP-Jegyzetnek [1928533]

Erősen ajánlott az operációs rendszer a Microsoft Windows, amely Microsoft Windows 2012 R2 legújabb verzióját használja.

### <a name="available-sap-maxdb-documentation"></a>Rendelkezésre álló SAP MaxDB dokumentációja
A frissített listáját SAP MaxDB dokumentációjában találhatja az alábbi SAP-Jegyzetnek [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB beállítási útmutatója az Azure-beli virtuális gépeken SAP-telepítések
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Tárolási konfiguráció
Az Azure storage ajánlott eljárások az SAP MaxDB hajtsa végre a fejezet az említett általános javaslatok [struktúra egy relációsadatbázis-kezelő rendszer központi telepítésének][dbms-guide-2].

> [!IMPORTANT]
> Más adatbázisok, például SAP MaxDB adat és naplófájl is van. Azonban az SAP MaxDB terminológiája megfelelő kifejezés "kötete" (nem "file"). Például nincsenek SAP MaxDB adatköteteket és naplózási köteteket. Ne keverje össze ezeket az operációsrendszer-lemez kötetek. 
> 
> 

Röviden kell:

* Ha Azure Storage-fiókokat használ, állítsa be az Azure storage-fiókot, amely az SAP MaxDB adat- és naplózási köteteket (azaz fájlok) tárol **helyi redundáns tárolást (LRS)** fejezet meghatározott [a Microsoft Azure Storage][dbms-guide-2.3].
* Az IO-útvonalához SAP MaxDB adatkötetnél (azaz fájlok) elkülönítése az IO-útvonalához, a naplózási köteteknek (azaz fájlok). Ez azt jelenti, hogy SAP MaxDB adatkötetek (azaz fájlok) kell telepíteni kell egy logikai meghajtó, és telepíteni kell egy másik logikai meghajtó rendelkeznek SAP MaxDB naplózási kötetek (azaz fájlok).
* Állítsa be az egyes lemezek, attól függően, hogy használat SAP MaxDB adat- vagy naplófájl kötetekhez (vagyis fájlok) és protokollt használja az Azure standard szintű vagy az Azure Premium Storage, a megfelelő gyorsítótárazási típus fejezetben leírtak szerint [virtuális gépek és az adatlemezek gyorsítótárazás] [dbms-guide-2.1].
* Mindaddig, amíg a jelenlegi IOPS-kvóta lemezenként megfelel a követelményeknek, lehetőség az adatkötetek tárolni egyetlen csatlakoztatott lemez és is tárolhatja az összes adatbázis naplózási kötetek másik egyetlen csatlakoztatott lemezen.
* Ha további iops-érték és/vagy lemezterület szükség, javasoljuk, hogy a Microsoft ablak Tárolókészletek (csak elérhető a Microsoft Windows Server 2012 és újabb) vagy a Microsoft Windows szétosztottsága befolyásolhatja a Microsoft Windows 2008 R2 segítségével hozzon létre egy nagy méretű logikai eszköz több keresztül csatlakoztatott lemezek. Lásd még: fejezet [szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban. Ez a megközelítés leegyszerűsíti az adminisztratív terhelés kezeléséhez a lemezterület, és elkerülhető az a munka, amelyet manuálisan elosztása több csatlakoztatott lemezek között fájlokat.
* IOPS legmagasabb szintű követelményeknek használhatja az Azure Premium Storage a DS sorozatú és a GS-sorozat virtuális gépei elérhető.

![Azure IaaS virtuális Gépeken SAP MaxDB DBMS referencia konfigurációja][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Biztonsági mentés és visszaállítás
Amikor SAP MaxDB üzembe az Azure-ba, nézze át a biztonsági mentési módszer. Még ha a rendszer nem egy hatékony rendszer, az SAP MaxDB futó SAP-adatbázist kell készíteni rendszeres időközönként. Mivel az Azure Storage három rendszerkép tartja, a biztonsági mentés már kevésbé fontos a rendszer tárolási hiba és a fontosabb operatív vagy felügyeleti meghibásodása elleni védelme. Elsődleges fenntartása a megfelelő biztonsági mentési és visszaállítási terv oka, hogy lehet kompenzálni logikai vagy kézi-e hibák időponthoz helyreállítási képességek biztosításával. Így az a célja, hogy az adatbázis visszaállítása egy bizonyos ponton, vagy használhatja a biztonsági másolatokat az Azure használatával ültet be egy másik rendszer a meglévő adatbázis másolásával vagy használja a biztonsági mentéseket. Például meg tudta átvinni 2 szintű SAP konfigurációról egy 3 szintű rendszer beállítása egy adott rendszer biztonsági mentésének visszaállításával.

Biztonsági mentése és visszaállítása egy adatbázist az Azure-ban működik ugyanúgy, mint a helyszíni rendszerek, így használhatja a szokásos SAP MaxDB biztonsági mentési és visszaállítási eszközöket, amelyek a SAP MaxDB dokumentációja a dokumentumokat, az SAP-Jegyzetnek felsorolt ismertetett [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Teljesítménnyel kapcsolatos biztonsági mentés és visszaállítás
Operációs rendszer nélküli telepítések, mint biztonsági mentési és helyreállítási teljesítmény az adott nyelvtől függ, hogy hány köteteket tudja olvasni a párhuzamos és az átviteli sebességet, a köteteket. Emellett a biztonságimásolat-tömörítési funkciók által használt CPU-használat is fontos szerepet játszik a virtuális gépek nyolc CPU szállal. Ezért egy feltételezheti, hogy:

* A kevesebb, a lemezek számát az adatbázis-eszközökre, az alacsonyabb tárolja az összesített olvasás átviteli sebességét
* Minél kisebb a virtuális gépen, a jogosultságeszkalálást szálak száma a biztonságimásolat-tömörítési funkciók hatását
* A kevesebb céllal (lemezek Stripe könyvtárak) írni a biztonsági mentés, az alacsonyabb az átviteli sebesség

Növelje az írási tárolók, két lehetőség van használható, valószínűleg kombináció, az Ön szükségleteinek:

* Dedikálni különböző kötetet a biztonsági mentéshez
* A biztonsági mentési célkötet szétosztott keresztül több csatlakoztatott lemez annak érdekében, hogy a köteten lévő csíkozott lemez iops-érték javítani
* Különálló dedikált logikai lemez eszközök rendelkeznek:
  * SAP MaxDB biztonsági másolatokat tároló köteteken (azaz fájlok)
  * SAP MaxDB adatkötetek (azaz fájlok)
  * SAP MaxDB naplózási kötetek (azaz fájlok)

Kötet szétosztott keresztül több csatlakoztatott lemezzel rendelkezik lett korábban ismertetett fejezet [szoftver RAID] [ dbms-guide-2.2] ebben a dokumentumban. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Other
Egyéb általános területre, például az Azure rendelkezésre állási csoportok vagy az SAP figyelése is érvényesek, az első három fejezetek a dokumentum az SAP MaxDB adatbázis virtuális gépek központi telepítéseknél leírtak szerint.
Egyéb SAP MaxDB-specifikus beállításokat átlátható az Azure virtuális gépeire, és az SAP-Jegyzetnek felsorolt különböző dokumentumokon ismertetett [767598] és az alábbi SAP-megjegyzések:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>SAP liveCache a Windows-adatait
### <a name="sap-livecache-version-support"></a>SAP liveCache verzió támogatása
Minimális verziója támogatja az Azure Virtual machines gépeken SAP liveCache **SAP LC/LCAPPS 10.0 SP 25** beleértve **liveCache 7.9.08.31** és **LCA-Build 25**, az engedélyezett **EhP 2 az SAP SCM 7.0** és újabb verzióit.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>A Microsoft Windows-verziók és az Azure-beli Virtuálisgép-típusok támogatott SAP liveCache adatbázis-kezelő
A Microsoft Windows támogatott verzióját az Azure-beli SAP liveCache című témakörben talál:

* [SAP-termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP-Jegyzetnek [1928533]

Erősen ajánlott az operációs rendszer a Microsoft Windows Server legújabb verzióját használja. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache beállítási útmutatója az Azure virtuális gépeken futó SAP-telepítések
#### <a name="recommended-azure-vm-types"></a>Ajánlott az Azure virtuális gépek típusai
Az SAP liveCache az olyan alkalmazás, amely hatalmas számításokat végez, a mennyiségét és sebességét, a RAM-MAL és CPU SAP liveCache teljesítményének jelentős hatással van. 

Az SAP által támogatott Azure-beli Virtuálisgép-típusok (SAP-Jegyzetnek [1928533]), minden virtuális Processzor-erőforrások a virtuális gép számára lefoglalt dedikált fizikai Processzor-erőforrások a hipervizor élvezik. Nincs túlzott (és a Processzor-erőforrások ezért nincsenek verseny) történik.

Minden Azure virtuális gép példány esetében az SAP által támogatott, ehhez hasonlóan a Virtuálisgép-memória hozzárendelve a fizikai memória – például túlzott (túlzott kötelezettségvállalás), 100 %-os nem használatos.

A szempontból azt javasoljuk az új D-sorozat vagy a DS sorozat (prémium szintű Azure Storage együttes) az Azure virtuális gép típusú 60 %-kal gyorsabb processzorokkal rendelkeznek, az a sorozat rendelkeznek. Legnagyobb RAM-MAL és a CPU terhelésének használhatja az G és GS-sorozat (az Azure Premium Storage együtt) virtuális gépei a legújabb Intel?? Xeon?? E5 v3 processzorcsalád, amelyek kétszer a memória és négy alkalommal a D/DS-sorozat szemben – tartós állapotú lemezes tárolást (SSD).

#### <a name="storage-configuration"></a>Tároló konfigurációja
SAP liveCache SAP MaxDB technológia alapul, ahogy az Azure storage ajánlott a fejezet az SAP MaxDB említett ajánlásokat és tanácsokat [tárolási konfigurációt] [ dbms-guide-8.4.1] SAP liveCache esetében is érvényesek. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedikált Azure virtuális gép liveCache
SAP liveCache intenzíven használ a számítási teljesítménnyel, hatékony használatot javasoljuk egy dedikált Azure virtuális gép üzembe helyezése. 

![Azure virtuális gépek hatékony használati esetekhez liveCache számára fenntartva][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
biztonsági mentés és visszaállítás, beleértve a teljesítménnyel kapcsolatos megfontolások már ismertetett vonatkozó SAP MaxDB fejezeteiben [biztonsági mentését és visszaállítását] [ dbms-guide-8.4.2] és [biztonsági mentés teljesítményével kapcsolatos megfontolások és visszaállítási][dbms-guide-8.4.3]. 

#### <a name="other"></a>Egyéb
A megfelelő SAP MaxDB már ismertetett egyéb általános területek [ez] [ dbms-guide-8.4.4] fejezet. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>A SAP-kiszolgáló a Windows-adatait
Az SAP-tartalom kiszolgáló összetevő külön, server-alapú különböző formátumokban tartalmat, például elektronikus dokumentumok tárolására. A SAP-kiszolgáló technológia biztosítja, és minden olyan SAP-alkalmazások használt cross-alkalmazás lesz. Ez egy külön rendszeren telepítve van. Tipikus tartalma képzési anyagokat és az ismeretek adatraktár vagy a mySAP PLM dokumentumkezelő rendszer származó műszaki rajzok dokumentáció. 

### <a name="sap-content-server-version-support"></a>SAP webtartalom-kiszolgáló verzió támogatása
SAP jelenleg támogatja:

* **SAP-kiszolgáló** verziójával **6.50 (vagy újabb)**
* **SAP MaxDB 7.9 verzió**
* **Microsoft IIS (Internet Information Server) 8.0-s (és újabb)**

Erősen javasoljuk, hogy a legújabb verzióját használja, az SAP-kiszolgálóhoz, amely a jelen dokumentum írása idején **6.50-es verzió SP4**, és a legújabb verziója **Microsoft IIS 8.5**. 

A legújabb támogatott verzióit SAP-kiszolgálóból, valamint a Microsoft IIS ellenőrizze a [SAP termék rendelkezésre állási mátrix (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Támogatott a Microsoft Windows- és Azure virtuális Gépen az SAP-kiszolgálóhoz
Ismerje meg a támogatott Windows-verziót, SAP-kiszolgálóhoz az Azure-ban, lásd:

* [SAP-termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP-Jegyzetnek [1928533]

Erősen ajánlott a Microsoft Windows Server legújabb verzióját használja.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP webtartalom-kiszolgáló beállítási útmutatója az Azure-beli virtuális gépeken SAP-telepítések
#### <a name="storage-configuration"></a>Tároló konfigurációja
Ha konfigurálja az SAP az SAP MaxDB adatbázis fájlok tárolására kiszolgáló, az összes Azure storage ajánlott eljárásokat fejezet SAP MaxDB az említett javaslat [tárolási konfigurációt] [ dbms-guide-8.4.1] is érvényesek az SAP-kiszolgáló a forgatókönyvhöz. 

Ha konfigurálja az SAP webtartalom-kiszolgáló tárolja a fájlokat a fájlrendszer, dedikált logikai meghajtó használata ajánlott. A Windows tárolóhelyek használata lehetővé teszi, hogy is növelheti a logikai lemez méretét és IOPS-teljesítmény, fejezetben leírtak szerint [szoftver RAID][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>SAP webtartalom-kiszolgáló helye
SAP-kiszolgáló rendelkezik az ugyanazon Azure-régióban és az Azure virtuális hálózat, ahol telepíti az SAP-rendszerhez üzembe helyezni. Szabadon eldöntheti, hogy szeretné-e egy dedikált Azure virtuális gépen, vagy ugyanazon a virtuális Gépen a SAP-rendszer fut, ahol az SAP-kiszolgáló-összetevők üzembe helyezése. 

![Az Azure virtuális gép dedikált SAP-kiszolgálóhoz][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>SAP gyorsítótár-kiszolgáló helye
Az SAP gyorsítótár-kiszolgáló helyi (gyorsítótárazott) dokumentumokhoz való hozzáférés biztosításához új server-alapú összetevőt. Az SAP gyorsítótár-kiszolgáló gyorsítótárazza a dokumentumok tartalom SAP-kiszolgáló. Ez a hálózati forgalom optimalizálása, ha dokumentumok rendelkezik a különböző helyeken lévő egynél többször lekérni. Az általános szabály, hogy az SAP gyorsítótár-kiszolgálót kell fizikailag közel az ügyfél, amely hozzáfér az SAP gyorsítótár-kiszolgáló rendelkezik-e. 

Itt két lehetősége van:

1. **Ügyfél egy olyan háttérrendszer SAP-rendszer** egy háttérrendszer SAP az SAP webtartalom-kiszolgáló eléréséhez van konfigurálva, a SAP-rendszer-e egy ügyfél. SAP-rendszerhez és a webtartalom-kiszolgáló SAP vannak üzembe helyezve az azonos Azure-régióban, az azonos Azure-adatközpontban, azok fizikailag közel egymással. Így hiba esetén nem kell egy dedikált SAP gyorsítótár-kiszolgáló. SAP felhasználói felület ügyfelek (SAP grafikus felhasználói felületének vagy a webes böngészőben) a közvetlen elérés az SAP-rendszerhez, és az SAP-rendszer beolvassa a dokumentumok a SAP-kiszolgálóhoz.
2. **Ügyfél egy helyszíni webböngésző** az SAP-tartalom kiszolgáló beállítható úgy, hogy közvetlenül a böngésző számára legyen elérhető. Ebben az esetben egy webböngészőben a helyszínen futó egy ügyfél, a SAP-kiszolgálóhoz. Helyszíni adatközpont és az Azure-adatközpont (ideális esetben közeli egymással) különböző fizikai helyen vannak elhelyezve. A helyszíni adatközpont Azure-t az Azure Site-to-Site VPN vagy az ExpressRoute segítségével csatlakozik. Bár mindkét lehetőséget kínálnak biztonságos VPN-kapcsolattal az Azure-ba, helyek közötti hálózati kapcsolat nem vonatkozik a hálózati sávszélesség és késés SLA-t a helyszíni adatközpont és az Azure-adatközpont között. Gyorsabb dokumentumokhoz való hozzáférést, a következők egyikét teheti:
   1. Helyszíni SAP-gyorsítótár-kiszolgáló telepíthető, zárja be a webböngészőt a helyszíni (beállítást [ez] [ dbms-guide-900-sap-cache-server-on-premises] . ábra)
   2. Azure ExpressRoute lehetőséget kínál a nagy sebességű és kis késleltetésű dedikált hálózati kapcsolat a helyszíni adatközpont és az Azure-adatközpont között, amelyek konfigurálásához.

![Helyszíni SAP-gyorsítótár-kiszolgáló telepítését.][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
Ha a SAP-kiszolgáló fájlok tárolására az SAP MaxDB adatbázisban konfigurálja, a biztonsági mentési és visszaállítási eljárást és teljesítménnyel kapcsolatos már ismertetett SAP MaxDB fejezet [biztonsági mentését és visszaállítását] [ dbms-guide-8.4.2]és fejezet [teljesítménnyel kapcsolatos biztonsági mentési és visszaállítási][dbms-guide-8.4.3]. 

Ha konfigurálja a SAP-kiszolgáló tárolja a fájlokat a fájlrendszer, egy lehetőség, hogy hajtsa végre a manuális biztonsági mentés/visszaállítás az egész fájlra struktúra hol találhatók a dokumentumokat. SAP MaxDB biztonsági mentési és visszaállítási hasonlóan, azt javasoljuk, hogy a dedikált lemezkötet biztonsági mentési célból. 

#### <a name="other"></a>Egyéb
Egyéb SAP tartalom-kiszolgálóra jellemző beállításokat az Azure virtuális gépek transzparens, és különböző dokumentumok és az SAP-megjegyzések ismerteti:

* <https://service.sap.com/contentserver> 
* SAP-Jegyzetnek [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>LUW a Windows-, IBM DB2-höz adatait
A Microsoft Azure-ral egyszerűen áttelepítheti a meglévő SAP on futó alkalmazáshoz, IBM DB2-höz Linux, UNIX és a Windows (LUW) az Azure virtuális gépekre. SAP-LUW az IBM DB2-höz a rendszergazdák és fejlesztők számára továbbra is használhatják az azonos fejlesztési és felügyeleti eszközöket, amely elérhető a helyszínen.
Általános információk az SAP Business Suite futó LUW találja a az SAP közösségi hálózati Állapotváltozás-, IBM DB2-höz <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

SAP az Azure-beli LUW DB2-kapcsolatos frissítésekről és további információkat lásd: SAP-Jegyzetnek [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2, Linux, UNIX és a Windows verzió támogatása
SAP a Microsoft Azure Virtual Machine Services LUW az IBM DB2-tól 10,5 DB2-verzió támogatott.

Támogatott SAP-termékek és Azure-beli Virtuálisgép-típusok kapcsolatos információkért tekintse meg az SAP-Jegyzetnek [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2, Linux, UNIX és a beállítási útmutatója Windows Azure virtuális gépeken futó SAP-telepítések
#### <a name="storage-configuration"></a>Tároló konfigurációja
Adatbázis összes fájlja az NTFS fájlrendszerrel, közvetlenül csatlakoztatott lemezek alapján kell tárolni. Ezeket a lemezeket az Azure virtuális géphez csatlakoztatva vannak, és az Azure BLOB Storage-oldal alapulnak (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy a Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Hálózati meghajtók vagy távoli megosztások a következő fájl Azure-szolgáltatásokat, mint bármilyen típusú **nem** támogatott adatbázis-fájlok: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Azure lap BLOB Storage vagy a Managed Disks-alapú lemezeket használ, ha a utasításokat ebben a dokumentumban a fejezet végzett [struktúra egy relációsadatbázis-kezelő rendszer központi telepítésének] [ dbms-guide-2] is vonatkoznak az IBM DB2-höz tartozó LUW központi telepítéseknél Az adatbázis. 

Amint azt korábban a dokumentum általános része, a lemez iops-érték átviteli kvótákat létezik. A pontos kvóták használt Virtuálisgép-típustól függnek. Saját kvótával rendelkező virtuális gépek típusainak listáját találja [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Mindaddig, amíg a jelenlegi IOPS lemezenként nem elegendő, lehetőség az adatbázis összes fájlja egy egyetlen csatlakoztatott lemezen tárolja. 

A teljesítmény szempontok is hivatkozni fejezet "Az Adatbiztonság és adatbázis-könyvtárak a teljesítménnyel kapcsolatos szempontok" SAP telepítési útmutatóit.

Azt is megteheti, Windows Tárolókészletek (csak elérhető a Windows Server 2012 és újabb) is használhatja, vagy a Windows 2008 R2, Windows csíkozást fejezetben leírt [szoftver RAID] [ dbms-guide-2.2] dokumentum Hozzon létre egy nagy méretű logikai eszköz több lemez keresztül.
A lemezek, a DB2 tárolási útvonalat a sapdata és saptmp címtárak tartalmazó adjon meg egy 512 KB-os fizikai lemez szektormérete. A Windows Tárolókészletek használata esetén a tárolókészletek a parancssori felület használatával a paraméter használatával manuálisan létre kell hoznia `-LogicalSectorSizeDefault`. További információkért lásd: <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Biztonsági mentés / visszaállítás
A biztonsági mentési és visszaállítási funkció az IBM DB2-höz tartozó LUW ugyanúgy, mint a standard szintű Windows Server operációs rendszerek és a Hyper-V támogatott.

Győződjön meg arról, hogy már működik a adatbázis érvényes biztonsági mentési stratégiájának. 

Operációs rendszer nélküli telepítések, mint biztonsági mentési/visszaállítási teljesítménye attól függ, hány kötetek párhuzamosan olvasható, és az átviteli sebességet, ezek a kötetek lehetnek. Emellett a biztonságimásolat-tömörítési funkciók által használt CPU-felhasználás előfordulhat, hogy fontos szerepet játszik a virtuális gépek nyolc CPU szállal. Ezért egy feltételezheti, hogy:

* A kevesebb, a lemezek számát fogja tárolni, az adatbázis-eszközökre, annál kisebb a teljes átviteli sebesség beolvasás
* Minél kisebb a virtuális gépen, a jogosultságeszkalálást szálak száma a biztonságimásolat-tömörítési funkciók hatását
* A kevesebb céllal (lemezek Stripe könyvtárak) írni a biztonsági mentés, az alacsonyabb az átviteli sebesség

Növelje az írási tárolók, a két lehetőség lehet igényeitől függően használt/kombinált:

* A biztonsági mentési célkötet szétosztott több lemez keresztül az IOPS átviteli sebességet a köteten lévő csíkozott javítása érdekében
* Egynél több céloldali könyvtár használatával történő biztonsági mentés írása

#### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás
A Microsoft Fürtkiszolgáló (MSCS) nem támogatott.

DB2 magas rendelkezésre állású vész-helyreállítási (HADR) használata támogatott. Ha a virtuális gépek, a magas rendelkezésre ÁLLÁSÚ konfiguráció névfeloldás működik, a nem különböznek a beállítása az Azure-ban minden olyan beállítás, amely a helyszínen történik. Nem ajánlott, csak az IP-feloldási támaszkodik.

Ne használja a Georeplikáció a storage-fiókok, amelyek az adatbázis lemezeinek tárolására. További információkért tekintse meg fejezet [a Microsoft Azure Storage] [ dbms-guide-2.3] és fejezet [magas rendelkezésre állás és vészhelyreállítás az Azure virtuális gépek] [ dbms-guide-3].

#### <a name="other"></a>Egyéb
Egyéb általános területeken, például az Azure rendelkezésre állási csoportok vagy az SAP-figyelés a alkalmazni: az első három fejezetek a központi telepítések LUW az IBM DB2-höz rendelkező virtuális gépek, valamint a jelen dokumentum leírtak szerint. 

Is hivatkoznak. fejezet [az SAP az Azure-összefoglalás az SQL Server általános][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>IBM DB2 for Linux LUW rögzítésen
A Microsoft Azure-ral egyszerűen áttelepítheti a meglévő SAP on futó alkalmazáshoz, IBM DB2-höz Linux, UNIX és a Windows (LUW) az Azure virtuális gépekre. SAP-LUW az IBM DB2-höz a rendszergazdák és fejlesztők számára továbbra is használhatják az azonos fejlesztési és felügyeleti eszközöket, amely elérhető a helyszínen. Általános információk az SAP Business Suite futó LUW találja a az SAP közösségi hálózati Állapotváltozás-, IBM DB2-höz <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

SAP az Azure-beli LUW DB2-kapcsolatos frissítésekről és további információkat lásd: SAP-Jegyzetnek [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2, Linux, UNIX és a Windows verzió támogatása
SAP a Microsoft Azure Virtual Machine Services LUW az IBM DB2-tól 10,5 DB2-verzió támogatott.

Támogatott SAP-termékek és Azure-beli Virtuálisgép-típusok kapcsolatos információkért tekintse meg az SAP-Jegyzetnek [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2, Linux, UNIX és a beállítási útmutatója Windows Azure virtuális gépeken futó SAP-telepítések
#### <a name="storage-configuration"></a>Tároló konfigurációja
Adatbázis összes fájlja a fájlrendszerben, közvetlenül csatlakoztatott lemezek alapján kell tárolni. Ezeket a lemezeket az Azure virtuális géphez csatlakoztatva vannak, és az Azure BLOB Storage-oldal alapulnak (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) vagy a Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Hálózati meghajtók vagy távoli megosztások a következő fájl Azure-szolgáltatásokat, mint bármilyen típusú **nem** támogatott adatbázis-fájlok:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Ha az Azure lap BLOB Storage, ez a fejezet a dokumentum nyilatkozatok alapján lemezek [struktúra egy relációsadatbázis-kezelő rendszer központi telepítésének] [ dbms-guide-2] az IBM DB2-höz LUW adatbázis telepítések esetén is vonatkoznak.

Amint azt korábban a dokumentum általános része, a lemez iops-érték átviteli kvótákat létezik. A pontos kvóták használt Virtuálisgép-típustól függnek. Saját kvótával rendelkező virtuális gépek típusainak listáját találja [itt (Linux)] [ virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Mindaddig, amíg a jelenlegi IOPS lemezenként nem elegendő, lehetőség az adatbázis összes fájlja egy egyetlen lemezen tárolja.

A teljesítmény szempontok is hivatkozni fejezet "Az Adatbiztonság és adatbázis-könyvtárak a teljesítménnyel kapcsolatos szempontok" SAP telepítési útmutatóit.

Másik lehetőségként használhatja LVM (a Logical Volume Manager) vagy MDADM fejezetben leírtak szerint [szoftver RAID] [ dbms-guide-2.2] keresztül több lemez egy nagy méretű logikai eszköz létrehozásához ebben a dokumentumban.
A lemezek, a DB2 tárolási útvonalat a sapdata és saptmp címtárak tartalmazó adjon meg egy 512 KB-os fizikai lemez szektormérete.

#### <a name="backuprestore"></a>Biztonsági mentés / visszaállítás
A biztonsági mentési és visszaállítási funkció LUW az IBM DB2-höz tartozó ugyanúgy, mint a standard szintű Linux telepítése a helyszíni támogatott.

Győződjön meg arról, hogy már működik a adatbázis érvényes biztonsági mentési stratégiájának.

Operációs rendszer nélküli telepítések, mint biztonsági mentési/visszaállítási teljesítménye attól függ, hány kötetek párhuzamosan olvasható, és az átviteli sebességet, ezek a kötetek lehetnek. Emellett a biztonságimásolat-tömörítési funkciók által használt CPU-felhasználás előfordulhat, hogy fontos szerepet játszik a virtuális gépek nyolc CPU szállal. Ezért egy feltételezheti, hogy:

* A kevesebb, a lemezek számát fogja tárolni, az adatbázis-eszközökre, annál kisebb a teljes átviteli sebesség beolvasás
* Minél kisebb a virtuális gépen, a jogosultságeszkalálást szálak száma a biztonságimásolat-tömörítési funkciók hatását
* A kevesebb céllal (lemezek Stripe könyvtárak) írni a biztonsági mentés, az alacsonyabb az átviteli sebesség

Növelje az írási tárolók, a két lehetőség lehet igényeitől függően használt/kombinált:

* A biztonsági mentési célkötet szétosztott több lemez keresztül az IOPS átviteli sebességet a köteten lévő csíkozott javítása érdekében
* Egynél több céloldali könyvtár használatával történő biztonsági mentés írása

#### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás
DB2 magas rendelkezésre állású vész-helyreállítási (HADR) használata támogatott. Ha a virtuális gépek, a magas rendelkezésre ÁLLÁSÚ konfiguráció névfeloldás működik, a nem különböznek a beállítása az Azure-ban minden olyan beállítás, amely a helyszínen történik. Nem ajánlott, csak az IP-feloldási támaszkodik.

Ne használja a Georeplikáció a storage-fiókok, amelyek az adatbázis lemezeinek tárolására. További információkért tekintse meg fejezet [a Microsoft Azure Storage] [ dbms-guide-2.3] és fejezet [magas rendelkezésre állás és vészhelyreállítás az Azure virtuális gépek] [ dbms-guide-3].

#### <a name="other"></a>Egyéb
Egyéb általános témaköröket, mint például a figyelés Azure rendelkezésre állási csoportok vagy az SAP üzemelő példányainak LUW az IBM DB2-höz rendelkező virtuális gépeket, valamint a jelen dokumentum az első három fejezeteiben leírtak szerint alkalmazni.

Is hivatkoznak. fejezet [az SAP az Azure-összefoglalás az SQL Server általános][dbms-guide-5.8].

