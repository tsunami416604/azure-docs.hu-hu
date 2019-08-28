---
title: SAP bevezetési Azure Virtual Machines adatbázis-kezelő üzembe helyezése SAP-munkaterheléshez | Microsoft Docs
description: SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101330"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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



Ebben a dokumentumban több különböző területre is kiterjed, amelyeket figyelembe kell venni az SAP bevezetésének az Azure IaaS történő telepítésekor. Ennek a dokumentumnak az előfeltétele, hogy olvassa el az [azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP](dbms_guide_general.md) -munkaterheléshez és egyéb útmutatók az Azure-beli SAP-munkaterheléshez című dokumentumban ismertetett szempontokat. [](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 

## <a name="specifics-to-sap-ase-on-windows"></a>A Windowson futó SAP-központhoz kapcsolódó részletek
A Microsoft Azure-től kezdve a meglévő SAP-elősegítő alkalmazásai áttelepíthetők az Azure-Virtual Machinesba. Az Azure-beli virtuális gépeken futó SAP-bevezetéssel csökkentheti a vállalati szélességű alkalmazások üzembe helyezésének, kezelésének és karbantartásának teljes tulajdonlási költségeit azáltal, hogy egyszerűen áttelepíti ezeket az alkalmazásokat a Microsoft Azure. Az Azure-beli virtuális gépeken futó SAP bevezetéssel a rendszergazdák és a fejlesztők továbbra is használhatják a helyszínen elérhető fejlesztési és felügyeleti eszközöket.

Az Azure Virtual Machines SLA-kat itt találja:<https://azure.microsoft.com/support/legal/sla/virtual-machines>

A Microsoft Azure számos különböző virtuálisgép-típust kínál, amelyek lehetővé teszik a legkisebb SAP-rendszerek és-tájak használatát a nagy SAP-rendszerek és tájak több ezer felhasználóval való futtatásával. A különböző SAP-tanúsítvánnyal rendelkező virtuális gépek SAP-méretezési SAP-száma az [1928533]-as SAP-megjegyzésben található.

Az Azure Storage, az SAP virtuális gépek vagy az SAP-figyelés használatára vonatkozó nyilatkozatok és javaslatok az [azure Virtual Machines adatbázis-kezelő rendszerekben történő üzembe helyezésével kapcsolatos megfontolások](dbms_guide_general.md) az SAP bevezetésére is vonatkoznak.

### <a name="sap-ase-version-support"></a>SAP benyújtó verziójának támogatása
Az SAP jelenleg a 16,0-es SAP-es verzióban támogatja az SAP Business Suite-termékekkel való használatot. Az SAP Business Suite-termékekkel való használatra szolgáló SAP bevezető kiszolgáló, illetve JDBC és ODBC illesztőprogramok összes frissítése kizárólag az SAP szolgáltatási Piactéren keresztül érhető <https://support.sap.com/swdc>el a következő címen:.

Ne töltsön le frissítéseket az SAP bevezető kiszolgáló, illetve a JDBC-és ODBC-illesztőprogramok számára közvetlenül a Sybase-webhelyekről. A helyszíni és az Virtual Machines Azure-beli SAP-termékekkel használható javításokkal kapcsolatos részletes információkat a következő SAP-megjegyzésekben talál:

* [1590719]
* [1973241]

Az SAP-t használó SAP Business Suite futtatásával kapcsolatos általános információk az [állapotváltozás](https://www.sap.com/community/topic/ase.html) szolgáltatásban találhatók

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP-vel kapcsolatos konfigurációs irányelvek SAP-hez kapcsolódó SAP-alapú Azure-beli virtuális gépeken való telepítéséhez
#### <a name="structure-of-the-sap-ase-deployment"></a>Az SAP bevezetési környezet szerkezete
Az SAP-bevezetési végrehajtható fájlnak a virtuális gép operációsrendszer-lemezének rendszermeghajtóján kell lennie\), vagy telepítve kell lennie (c meghajtó:. Általában a legtöbb SAP beszállítói rendszer és eszköz adatbázisa nem tapasztal nagy terhelést. Ezért a rendszer és az eszközök adatbázisai (Master, Model, saptools, sybmgmtdb, sybsystemdb) továbbra is megmaradhatnak a C:\ meghajtó. 

Kivétel lehet az ideiglenes adatbázis, amely bizonyos SAP ERP-ben és az összes BW-munkaterhelésnél nagyobb adatmennyiséget vagy I/O-műveletek mennyiségét igényli. A virtuális gép operációsrendszer-lemeze által nem biztosított kötetek vagy IOPS (C meghajtó:\).

A telepítéshez használt SAPInst/SWPM-verziótól függ, hogy az SAP-beli betekintő példány konfigurációja így néz ki:

* Egyetlen SAP-tempdb, amely az SAP beadásának telepítésekor jön létre
* Az SAP beépítési folyamatának telepítésével létrehozott SAP-tempdb, valamint az SAP-telepítési rutin által létrehozott további saptempdb
* Az SAP-bevezetés és a manuálisan létrehozott további tempdb (például az [1752266]-es SAP-megjegyzéssel) létrehozásával létrehozott SAP-TEMPDB az ERP/BW-specifikus tempdb-követelmények teljesítéséhez

Az adott ERP-vagy az összes BW-számítási feladat teljesítményének kihasználása miatt érdemes lehet a tempdb-eszközöket a C:\) rendelkező-től eltérő meghajtón tárolni a további tempdb Ha nem létezik további tempdb, ajánlott létrehozni egy (SAP-Megjegyzés [1752266]).

Ilyen rendszerek esetén a következő lépéseket kell végrehajtani a további létrehozott tempdb:

* Az első tempdb-eszköz áthelyezése az SAP-adatbázis első eszközére
* Tempdb-eszközök hozzáadása az SAP-adatbázis eszközét tartalmazó virtuális merevlemezekhez

Ez a konfiguráció lehetővé teszi, hogy a tempdb több helyet fogyasszon el, mint a rendszermeghajtó. Az egyik hivatkozásként megtekintheti a tempdb-eszközök méretét a meglévő rendszereken, amelyek a helyszínen futnak. Vagy egy ilyen konfiguráció lehetővé teszi a IOPS-számok tempdb való használatát, amely nem adható meg a rendszermeghajtón. A helyszínen futó rendszerek az I/O-munkaterhelések figyelésére használhatók a tempdb.

Soha ne helyezzen semmilyen SAP-előállító eszközt a D:\ a virtuális gép meghajtója. Az SAP-elősegítő szolgáltatás esetében ez a Tanács a tempdb is vonatkozik, még akkor is, ha a tempdb tárolt objektumok csak átmenetiek.

Az adatok és a tranzakciós naplófájlok központi telepítése esetén az [Azure Virtual Machines adatbázis-kezelői szolgáltatás az SAP-munkaterheléshez való üzembe helyezésével kapcsolatos megfontolások](dbms_guide_general.md)és javaslatok. Windows-alapú központi telepítések esetén a Windows tárolóhelyek használatát javasoljuk, hogy a megfelelő IOPS, átviteli sebességgel és kötettel rendelkező csíkkészleteket hozzon létre.  

#### <a name="impact-of-database-compression"></a>Az adatbázis-tömörítés hatása
Azon konfigurációk esetében, amelyekben az I/O-sávszélesség korlátozási tényezővé válhat, minden mérték, amely csökkenti a IOPS, segíthet a számítási feladatok egy IaaS-forgatókönyvben, például az Azure-ban való futtatásában. Ezért azt javasoljuk, hogy a meglévő SAP-adatbázisok Azure-ba való feltöltése előtt győződjön meg arról, hogy az SAP-vel történő adattömörítés használatban van.

Az Azure-ba való feltöltés előtt a tömörítés alkalmazására vonatkozó javaslat több okból is fennáll:

* Az Azure-ba feltölteni kívánt adatmennyiség alacsonyabb
* A tömörítés végrehajtásának időtartama rövidebb, feltételezve, hogy az egyik nagyobb CPU-igényű vagy magasabb I/O-sávszélességgel vagy kisebb I/O-késéssel rendelkező erősebb hardvert használhat.
* A kisebb adatbázis-méretek kevesebb költséget okozhatnak a lemez kiosztása során

Az adatés LOB-tömörítés az Azure Virtual Machines üzemeltetett virtuális gépeken működik, mivel a helyszínen működik. Ha további információt szeretne arról, hogyan ellenőrizze, hogy a tömörítés már használatban van-e egy meglévő SAP-adatbázison, tekintse meg a [1750510]-es SAP-megjegyzést.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Az DBACockpit használata az adatbázis-példányok figyelésére
Az SAP-t adatbázis-platformként használó SAP-rendszerek esetén a DBACockpit a tranzakciós DBACockpit vagy WebDynpro a beágyazott böngészőablakként érhető el. Az adatbázis figyelésének és felügyeletének teljes funkcionalitása azonban csak a DBACockpit WebDynpro implementációjában érhető el.

Csakúgy, mint a helyszíni rendszerek esetében, több lépésre van szükség ahhoz, hogy a DBACockpit WebDynpro-implementációja által használt összes SAP NetWeaver funkció engedélyezve legyen. A webdynpros használatának engedélyezéséhez és a szükségesek létrehozásához kövesse az [1245200] -es SAP-megjegyzést. A fenti megjegyzésekben szereplő utasítások követése esetén az Internet Communication Manager (ICM) és a http-és HTTPS-kapcsolatokhoz használt portok is megadhatók. A http alapértelmezett beállítása a következőképpen néz ki:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a tranzakciós DBACockpit létrehozott hivatkozások a következőhöz hasonlóan néz ki:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően, hogy az SAP-szolgáltatást üzemeltető Azure-beli virtuális gép hogyan kapcsolódik az AD-hez és a DNS-hez, meg kell győződnie arról, hogy az ICM teljes állomásnevet használ, amely feloldható azon a gépen, amelyen a DBACockpit megnyitja. Tekintse meg az [773830] -es SAP-megjegyzést, hogy megtudja, hogyan határozza meg a teljes állomásnév a profil paramétereinek alapján, valamint az ICM/host_name_full megadása explicit módon, ha szükséges.

Ha a virtuális gépet csak felhőalapú, a helyszíni és az Azure közötti kapcsolat nélküli környezetben telepítette, meg kell adnia egy nyilvános IP-címet és egy domainlabel. A virtuális gép nyilvános DNS-nevének formátuma így néz ki:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

A DNS-névvel kapcsolatos további részletek [itt][virtual-machines-azurerm-versus-azuresm]találhatók.

Ha az ICM/host_name_full SAP-profil paraméterét az Azure-beli virtuális gép DNS-nevére állítja be, a hivatkozás a következőhöz hasonló lehet:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Ebben az esetben a következőket kell tennie:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoporthoz az ICM-sel való kommunikációhoz használt TCP/IP-portok Azure Portaljában
* Bejövő szabályok hozzáadása az ICM-sel való kommunikációhoz használt TCP/IP-portok Windows tűzfal konfigurációjához

Az összes rendelkezésre álló javítás automatikus importálása esetén ajánlott rendszeresen alkalmazni az SAP-verzióra vonatkozó korrekciós gyűjtemény SAP-megjegyzését:

* [1558958]
* [1619967]
* [1882376]

A következő SAP-megjegyzésekben talál további információt az SAP-vel kapcsolatos DBA Pilótafülkéről:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Az SAP-t érintő biztonsági mentési/helyreállítási megfontolások
Az SAP bevezetésének az Azure-ba történő telepítésekor a biztonsági mentési módszert felül kell vizsgálni. A nem éles rendszerek esetében is az SAP-adatbázis (ok) ról rendszeresen biztonsági mentést kell készíteni. Mivel az Azure Storage három lemezképet tart fenn, előfordulhat, hogy a biztonsági mentés kevésbé fontos a tárolási összeomlás kompenzálása tekintetében. A megfelelő biztonsági mentési és visszaállítási terv fenntartásának elsődleges oka, hogy kompenzálni tudja a logikai/manuális hibákat az időponthoz kapcsolódó helyreállítási képességek biztosításával. A cél az, hogy a biztonsági mentések használatával visszaállítsa az adatbázist egy adott időpontra, vagy az Azure-beli biztonsági mentések használatával egy másik rendszert a meglévő adatbázis másolásával. 

Az Azure-beli adatbázisok biztonsági mentése és visszaállítása ugyanúgy történik, mint a helyszínen. Lásd: SAP-megjegyzések:

* [1588316]
* [1585981]

a memóriakép-konfigurációk létrehozásával és a biztonsági mentések ütemezésével kapcsolatos részletekért. A stratégiától és az igényektől függően konfigurálhatja az adatbázis-és a naplózási memóriaképeket az egyik meglévő lemezre, vagy hozzáadhat egy további lemezt a biztonsági mentéshez. A hiba esetén az adatvesztés veszélyének csökkentése érdekében ajánlott olyan lemezt használni, ahol nem találhatók adatbázisfájlok.

Az adatkezelés és a LOB-tömörítés mellett az SAP-előkészítő is a biztonsági másolatok tömörítését is biztosítja. Ha kevesebb helyet kíván használni az adatbázissal, és a naplózási memóriaképeket használja, ajánlott a biztonsági másolatok tömörítése. További információ: SAP Note [1588316]. A biztonsági mentés tömörítése szintén elengedhetetlen az átvinni kívánt adatok mennyiségének csökkentéséhez, ha az Azure-beli virtuális gépről a helyszíni rendszerre biztonsági másolatokat tartalmazó biztonsági másolatok vagy virtuális merevlemezek letöltését tervezi.

Ne használja a meghajtó D:\ az adatbázis vagy a napló memóriaképének célhelye.

#### <a name="performance-considerations-for-backupsrestores"></a>A biztonsági másolatok/visszaállítások teljesítményével kapcsolatos megfontolások
Az operációs rendszer nélküli telepítésekhez hasonlóan a biztonsági mentési/visszaállítási teljesítmény attól függ, hogy hány kötet olvasható párhuzamosan, és milyen átviteli sebesség lehet a köteten. Ne feledje, hogy a biztonsági másolatok tömörítése CPU-erőforrásokat használ. A biztonsági másolatok tömörítésének ezen CPU-használata jelentős szerepet játszhat a virtuális gépeken kis számú CPU-szál használatával. Ezért feltételezheti a következőket:

* Minél kevesebb az adatbázis-eszközök tárolására használt lemezek száma, annál kisebb a teljes teljesítmény az olvasás során
* Minél kevesebb CPU-szál van a virtuális gépen, annál súlyosabb a biztonsági másolatok tömörítésének hatása
* A kevesebb célpont (könyvtárak, lemezek) a biztonsági mentés megírásához, annál kisebb az átviteli sebesség

Ha szeretné megnövelni, hogy hány célpontot kell írnia, a rendszer két lehetőséget kínál, amelyek az igényeitől függően használhatók/kombinálhatók:

* A biztonsági mentési cél kötetének több csatlakoztatott lemezre történő csíkozása a IOPS átviteli sebességének növeléséhez a csíkozott köteten
* A memóriakép konfigurációjának létrehozása az SAP-alapú bekapcsolási szinten, amely több célhelyet használ a memóriakép írásához

A lemez kötetének több csatlakoztatott lemezre történő csíkozása az [Azure Virtual Machines adatbázis-kezelői szolgáltatás SAP](dbms_guide_general.md)-munkaterheléshez való üzembe helyezésével kapcsolatos megfontolásokban található. Ha többet szeretne megtudni arról, hogyan lehet több címtárat használni az SAP betekintő memóriaképének konfigurációjában, tekintse meg a tárolt eljárás sp_config_dump kapcsolatos dokumentációt, amely a memóriakép konfigurációjának létrehozásához használatos a [Sybase-Infoközpont](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Vész-helyreállítás Azure-beli virtuális gépekkel
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Adatreplikáció az SAP Sybase replikációs kiszolgálójával
Az SAP Sybase replikációs kiszolgálójának (SRS) köszönhetően az SAP-technológia meleg készenléti megoldást kínál az adatbázis-tranzakciók távoli helyre történő átviteléhez aszinkron módon. 

Az SRS telepítése és működése az Azure-beli virtuálisgép-szolgáltatásokban üzemeltetett virtuális gépeken is működik.

Az SAP-beli HADR nem igényel Azure belső Load Balancer, és nincs függősége az operációs rendszer szintjének fürtözéséhez. Az Azure Windows-és Linux-alapú virtuális gépeken működik. Az SAP bevezető HADR kapcsolatos részletekért olvassa el az [SAP-HADR felhasználói útmutatóját](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>A Linuxon futó SAP-központhoz kapcsolódó részletek
A Microsoft Azuretól kezdve könnyedén áttelepítheti a meglévő SAP-előállítók alkalmazásait az Azure Virtual Machinesba. A virtuális gépeken futó SAP-bevezetéssel csökkentheti a vállalati szélességű alkalmazások üzembe helyezésének, kezelésének és karbantartásának teljes tulajdonlási költségeit azáltal, hogy egyszerűen áttelepíti ezeket az alkalmazásokat a Microsoft Azure. Az Azure-beli virtuális gépeken futó SAP bevezetéssel a rendszergazdák és a fejlesztők továbbra is használhatják a helyszínen elérhető fejlesztési és felügyeleti eszközöket.

Az Azure-beli virtuális gépek üzembe helyezéséhez fontos, hogy megismerje a hivatalos SLA-kat, amelyek itt találhatók:<https://azure.microsoft.com/support/legal/sla>

Az SAP-méretezési információk és az SAP-tanúsítvánnyal rendelkező virtuális gépek listája a [1928533]-es SAP-megjegyzésben található. Az Azure Virtual Machines szolgáltatáshoz elérhető további SAP-méretezési dokumentumok itt <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> és itt találhatók<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Az Azure Storage, az SAP virtuális gépek üzembe helyezése és az SAP-figyelés használatára vonatkozó utasítások és javaslatok az SAP bevezetésének az SAP-alkalmazásokkal együtt történő üzembe helyezéséhez a jelen dokumentum első négy fejezetében leírtak szerint.

A következő két SAP-Megjegyzés általános információt tartalmaz a Linuxon és a-ben a felhőben található központról:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP benyújtó verziójának támogatása
Az SAP jelenleg a 16,0-es SAP-es verzióban támogatja az SAP Business Suite-termékekkel való használatot. Az SAP Business Suite-termékekkel való használatra szolgáló SAP bevezető kiszolgáló, illetve JDBC és ODBC illesztőprogramok összes frissítése kizárólag az SAP szolgáltatási Piactéren keresztül érhető <https://support.sap.com/swdc>el a következő címen:.

A helyszíni telepítésekhez hasonlóan az SAP bevezetési kiszolgáló frissítései nem tölthetők le, vagy közvetlenül a Sybase-webhelyekről származó JDBC-és ODBC-illesztőprogramok esetén. Az SAP Business Suite-termékekkel a helyszínen és az Virtual Machines Azure-ban használható javításokkal kapcsolatos részletes információkért lásd a következő SAP-megjegyzéseket:

* [1590719]
* [1973241]

Az SAP-t használó SAP Business Suite futtatásával kapcsolatos általános információk az [állapotváltozás](https://www.sap.com/community/topic/ase.html) szolgáltatásban találhatók

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP-vel kapcsolatos konfigurációs irányelvek SAP-hez kapcsolódó SAP-alapú Azure-beli virtuális gépeken való telepítéséhez
#### <a name="structure-of-the-sap-ase-deployment"></a>Az SAP bevezetési környezet szerkezete
Az SAP-beépítés végrehajtható fájlját a virtuális gép (/Sybase) gyökérkönyvtárában kell elhelyezni vagy telepíteni. Általában a legtöbb SAP beszállítói rendszer és eszköz adatbázisa nem tapasztal nagy terhelést. Ezért a rendszer-és eszköz-adatbázisok (Master, Model, saptools, sybmgmtdb, sybsystemdb) a gyökérszintű fájlrendszerben tárolhatók. 

Kivétel lehet az ideiglenes adatbázis, amely bizonyos SAP ERP-ben és az összes BW-munkaterhelésnél nagyobb adatmennyiséget vagy I/O-műveletek mennyiségét igényli. A virtuális gép operációsrendszer-lemeze által nem biztosított kötetek vagy IOPS 

A rendszer telepítéséhez használt SAPInst/SWPM-verziótól függően az adatbázis a következőket tartalmazhatja:

* Az SAP-beadási szolgáltatás telepítésekor létrehozott egyszeri SAP-tempdb
* Az SAP beépítési folyamatának telepítésével létrehozott SAP-tempdb, valamint az SAP-telepítési rutin által létrehozott további saptempdb
* Az SAP-bevezetés és a manuálisan létrehozott további tempdb (például az [1752266]-es SAP-megjegyzéssel) létrehozásával létrehozott SAP-TEMPDB az ERP/BW-specifikus tempdb-követelmények teljesítéséhez

Az adott ERP-vagy az összes BW-számítási feladat teljesítményének hiányában ésszerű lehet a további létrehozott tempdb (SWPM vagy manuálisan) tempdb-eszközeinek tárolása egy különálló fájlrendszeren, amelyet egyetlen Azure-adatlemez vagy egy Linux RAID-átívelő m képvisel. webhely az Azure-adatlemezeket. Ha nem létezik további tempdb, ajánlott létrehozni egy (SAP-Megjegyzés [1752266]).

Ilyen rendszerek esetén a következő lépéseket kell végrehajtani a további létrehozott tempdb:

* Helyezze át az első tempdb könyvtárat az SAP-adatbázis első fájlrendszerére
* Tempdb-címtárak hozzáadása az SAP-adatbázis fájlrendszerét tartalmazó összes lemezhez

Ez a konfiguráció lehetővé teszi, hogy a tempdb több helyet fogyasszon el, mint a rendszermeghajtó. Az egyik hivatkozásként megtekintheti a tempdb-eszközök méretét a meglévő rendszereken, amelyek a helyszínen futnak. Vagy egy ilyen konfiguráció lehetővé teszi a IOPS-számok tempdb való használatát, amely nem adható meg a rendszermeghajtón. A helyszínen futó rendszerek az I/O-munkaterhelések figyelésére használhatók a tempdb.

Soha ne helyezzen semmilyen SAP-előfizetői könyvtárat a virtuális gép/mnt vagy/mnt/Resource. Az SAP-elősegítő szolgáltatás esetében ez a Tanács a tempdb is vonatkozik, még akkor is, ha a tempdb tárolt objektumok csak átmenetiek. Mivel a/mnt vagy a/mnt/Resource egy alapértelmezett Azure-beli virtuális gép ideiglenes területe, amely nem állandó. Az Azure-beli virtuális gépek ideiglenes területével kapcsolatos további részletek [ebben a cikkben találhatók.][virtual-machines-linux-how-to-attach-disk]

Az adatok és a tranzakciós naplófájlok központi telepítése esetén az [Azure Virtual Machines adatbázis-kezelői szolgáltatás az SAP-munkaterheléshez való üzembe helyezésével kapcsolatos megfontolások](dbms_guide_general.md)és javaslatok. Linux-alapú telepítések esetén az LVM vagy a MDADM használatát javasoljuk, hogy a megfelelő IOPS, átviteli sebességgel és kötettel rendelkező csíkkészleteket hozza létre. 

#### <a name="impact-of-database-compression"></a>Az adatbázis-tömörítés hatása
Azon konfigurációk esetében, amelyekben az I/O-sávszélesség korlátozási tényezővé válhat, minden mérték, amely csökkenti a IOPS, segíthet a számítási feladatok egy IaaS-forgatókönyvben, például az Azure-ban való futtatásában. Ezért azt javasoljuk, hogy a meglévő SAP-adatbázisok Azure-ba való feltöltése előtt győződjön meg arról, hogy az SAP-vel történő adattömörítés használatban van.

Az Azure-ba való feltöltés előtt a tömörítés alkalmazására vonatkozó javaslat több okból is fennáll:

* Az Azure-ba feltölteni kívánt adatmennyiség alacsonyabb
* A tömörítés végrehajtásának időtartama rövidebb, feltételezve, hogy az egyik nagyobb CPU-igényű vagy magasabb I/O-sávszélességgel vagy kisebb I/O-késéssel rendelkező erősebb hardvert használhat.
* A kisebb adatbázis-méretek kevesebb költséget okozhatnak a lemez kiosztása során

Az adatés LOB-tömörítés az Azure Virtual Machines üzemeltetett virtuális gépeken működik, mivel a helyszínen működik. Ha további információt szeretne arról, hogyan ellenőrizze, hogy a tömörítés már használatban van-e egy meglévő SAP-adatbázison, tekintse meg a [1750510]-es SAP-megjegyzést. Az adatbázis-tömörítéssel kapcsolatos további információkért lásd: SAP-Megjegyzés [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Az DBACockpit használata az adatbázis-példányok figyelésére
Az SAP-t adatbázis-platformként használó SAP-rendszerek esetén a DBACockpit a tranzakciós DBACockpit vagy WebDynpro a beágyazott böngészőablakként érhető el. Az adatbázis figyelésének és felügyeletének teljes funkcionalitása azonban csak a DBACockpit WebDynpro implementációjában érhető el.

Csakúgy, mint a helyszíni rendszerek esetében, több lépésre van szükség ahhoz, hogy a DBACockpit WebDynpro-implementációja által használt összes SAP NetWeaver funkció engedélyezve legyen. A webdynpros használatának engedélyezéséhez és a szükségesek létrehozásához kövesse az [1245200] -es SAP-megjegyzést. A fenti megjegyzésekben szereplő utasítások követése esetén az Internet Communication Manager (ICM) és a http-és HTTPS-kapcsolatokhoz használt portok is megadhatók. A http alapértelmezett beállítása a következőképpen néz ki:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a tranzakció DBACockpit létrehozott hivatkozások a következőhöz hasonlóan fognak kinézni:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően, hogy az SAP-szolgáltatást üzemeltető Azure-beli virtuális gép hogyan kapcsolódik az AD-hez és a DNS-hez, meg kell győződnie arról, hogy az ICM teljes állomásnevet használ, amely feloldható azon a gépen, amelyen a DBACockpit megnyitja. Tekintse meg az [773830] -es SAP-megjegyzést, hogy megtudja, hogyan határozza meg az ICM a teljes állomásnevet a profil paramétereinek függvényében, és az ICM/host_name_full explicit módon adja meg, ha szükséges.

Ha a virtuális gépet csak felhőalapú, a helyszíni és az Azure közötti kapcsolat nélküli környezetben telepítette, meg kell adnia egy nyilvános IP-címet és egy domainlabel. A virtuális gép nyilvános DNS-nevének formátuma így néz ki:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

A DNS-névvel kapcsolatos további részletek [itt][virtual-machines-azurerm-versus-azuresm]találhatók.

Ha az ICM/host_name_full SAP-profil paraméterét az Azure-beli virtuális gép DNS-nevére állítja be, a hivatkozás a következőhöz hasonló lehet:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Ebben az esetben a következőket kell tennie:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoporthoz az ICM-sel való kommunikációhoz használt TCP/IP-portok Azure Portaljában
* Bejövő szabályok hozzáadása az ICM-sel való kommunikációhoz használt TCP/IP-portok Windows tűzfal konfigurációjához

Az összes rendelkezésre álló javítás automatikus importálása esetén ajánlott rendszeresen alkalmazni az SAP-verzióra vonatkozó korrekciós gyűjtemény SAP-megjegyzését:

* [1558958]
* [1619967]
* [1882376]

A következő SAP-megjegyzésekben talál további információt az SAP-vel kapcsolatos DBA Pilótafülkéről:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Az SAP-t érintő biztonsági mentési/helyreállítási megfontolások
Az SAP bevezetésének az Azure-ba történő telepítésekor a biztonsági mentési módszert felül kell vizsgálni. A nem éles rendszerek esetében is az SAP-adatbázis (ok) ról rendszeresen biztonsági mentést kell készíteni. Mivel az Azure Storage három lemezképet tart fenn, előfordulhat, hogy a biztonsági mentés kevésbé fontos a tárolási összeomlás kompenzálása tekintetében. A megfelelő biztonsági mentési és visszaállítási terv fenntartásának elsődleges oka, hogy kompenzálni tudja a logikai/manuális hibákat az időponthoz kapcsolódó helyreállítási képességek biztosításával. A cél az, hogy a biztonsági mentések használatával visszaállítsa az adatbázist egy adott időpontra, vagy az Azure-beli biztonsági mentések használatával egy másik rendszert a meglévő adatbázis másolásával. 

Az Azure-beli adatbázisok biztonsági mentése és visszaállítása ugyanúgy történik, mint a helyszínen. Lásd: SAP-megjegyzések:

* [1588316]
* [1585981]

a memóriakép-konfigurációk létrehozásával és a biztonsági mentések ütemezésével kapcsolatos részletekért. A stratégiától és az igényektől függően konfigurálhatja az adatbázis-és a naplózási memóriaképeket a lemezre az egyik meglévő lemezre, vagy hozzáadhat egy további lemezt a biztonsági mentéshez. A hiba esetén az adatvesztés veszélyének csökkentése érdekében ajánlott olyan lemezt használni, amelyben nincs adatbázis-könyvtár vagy-fájl.

Az adatkezelés és a LOB-tömörítés mellett az SAP-előkészítő is a biztonsági másolatok tömörítését is biztosítja. Ha kevesebb helyet kíván használni az adatbázissal, és a naplózási memóriaképeket használja, ajánlott a biztonsági másolatok tömörítése. További információ: SAP Note [1588316]. A biztonsági mentés tömörítése szintén elengedhetetlen az átvinni kívánt adatok mennyiségének csökkentéséhez, ha az Azure-beli virtuális gépről a helyszíni rendszerre biztonsági másolatokat tartalmazó biztonsági másolatok vagy virtuális merevlemezek letöltését tervezi.

Ne használja az Azure VM Temp Space/mnt vagy a/mnt/Resource adatbázist vagy a napló memóriaképének célhelyét.

#### <a name="performance-considerations-for-backupsrestores"></a>A biztonsági másolatok/visszaállítások teljesítményével kapcsolatos megfontolások
Az operációs rendszer nélküli telepítésekhez hasonlóan a biztonsági mentési/visszaállítási teljesítmény attól függ, hogy hány kötet olvasható párhuzamosan, és milyen átviteli sebesség lehet a köteten. Ne feledje, hogy a biztonsági másolatok tömörítése CPU-erőforrásokat használ. A biztonsági másolatok tömörítésének ezen CPU-használata jelentős szerepet játszhat a virtuális gépeken kis számú CPU-szál használatával.  Ezért feltételezheti a következőket:

* Minél kevesebb az adatbázis-eszközök tárolására használt lemezek száma, annál kisebb a teljes teljesítmény az olvasás során
* Minél kevesebb CPU-szál van a virtuális gépen, annál súlyosabb a biztonsági másolatok tömörítésének hatása
* A kevesebb cél (Linux szoftveres RAID, lemez) a biztonsági mentés megírásához, annál kisebb az átviteli sebesség

Ha szeretné megnövelni, hogy hány célpontot kell írnia, a rendszer két lehetőséget kínál, amelyek az igényeitől függően használhatók/kombinálhatók:

* A biztonsági mentési cél kötetének több csatlakoztatott lemezre történő csíkozása a IOPS átviteli sebességének növeléséhez a csíkozott köteten
* A memóriakép konfigurációjának létrehozása az SAP-alapú bekapcsolási szinten, amely több célhelyet használ a memóriakép írásához

A lemez kötetének több csatlakoztatott lemezre történő csíkozása az [Azure Virtual Machines adatbázis-kezelői szolgáltatás SAP](dbms_guide_general.md)-munkaterheléshez való üzembe helyezésével kapcsolatos megfontolásokban található. Ha többet szeretne megtudni arról, hogyan lehet több címtárat használni az SAP betekintő memóriaképének konfigurációjában, tekintse meg a tárolt eljárás sp_config_dump kapcsolatos dokumentációt, amely a memóriakép konfigurációjának létrehozásához használatos a [Sybase-Infoközpont](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Vész-helyreállítás Azure-beli virtuális gépekkel
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Adatreplikáció az SAP Sybase replikációs kiszolgálójával
Az SAP Sybase replikációs kiszolgálójának (SRS) köszönhetően az SAP-technológia meleg készenléti megoldást kínál az adatbázis-tranzakciók távoli helyre történő átviteléhez aszinkron módon. 

Az SRS telepítése és működése az Azure-beli virtuálisgép-szolgáltatásokban üzemeltetett virtuális gépeken is működik.

Az SAP-replikációs kiszolgálón keresztüli HADR támogatott. Javasoljuk, hogy az SAP-t 16,03-et használja a konfiguráció kipróbálásához. Az ilyen konfigurációk telepítéséhez részletesebb utasítások találhatók ebben a [blogban](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
