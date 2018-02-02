---
title: "SAP NetWeaver Azure virtuális gépek telepítésének |} Microsoft Docs"
description: "Útmutató az Azure-ban a Linux virtuális gépeken SAP szoftver központi telepítése."
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.openlocfilehash: 4c06b1c8265a12af6764124e3c753e9456a2be20
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>SAP NetWeaver Azure virtuális gépek központi telepítését
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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS deployment for SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching for VMs and VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure of a RDBMS deployment)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (High availability and disaster recovery with Azure VMs)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 and earlier releases)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Using a SQL Server image from the Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General SQL Server for SAP on Azure summary)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Storage configuration)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup and restore)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Performance considerations for backup and restore)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other)
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

[deployment-guide]:deployment-guide.md (Azure Virtual Machines deployment for SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP resources)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Deploying a VM by using a custom image)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Deploying a VM from the Azure Marketplace for SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Deploying a VM with a custom image for SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Moving a VM from on-premises using a non-generalized Azure VHD with SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Deployment scenarios of VMs for SAP on Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Deploying Azure PowerShell cmdlets)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download and import SAP-relevant PowerShell cmdlets)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Join a VM to an on-premises domain - Windows only)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Download, install, and enable the Azure VM Agent)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure the Azure Enhanced Monitoring Extension for SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Readiness check for Azure Enhanced Monitoring for SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Health check for the Azure monitoring infrastructure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Troubleshooting Azure monitoring for SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure monitoring)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure the proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and troubleshooting for setting up end-to-end monitoring)

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

[planning-guide]:planning-guide.md (Azure Virtual Machines planning and implementation for SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resources)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (High availability and disaster recovery for SAP NetWeaver running on Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (High availability for SAP Application Servers)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Using Autostart for SAP instances)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud-only - Virtual Machine deployments in Azure without dependencies on the on-premises customer network)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises - Deployment of single or multiple SAP VMs in Azure fully integrated with the on-premises network)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regions)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fault domains)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgrade domains)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure availability sets)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure virtual machines concept)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Deploying a VM with a customer specific image)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparation for moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparation for deploying a VM with a customer specific image for SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparing VMs with SAP for Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Difference between an Azure disk and an Azure image)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Uploading a VHD from on-premises to Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copying disks between Azure Storage accounts)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD structure for SAP deployments)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Setting automount for attached disks)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Single VM with SAP NetWeaver demo/training scenario)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts of Cloud-Only deployment of SAP instances)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Managed Disks)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and data disks)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Manage virtual machines by using Azure Resource Manager and PowerShell)
[virtual-machines-windows-agent-user-guide]:../../windows/agent-user-guide.md
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
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
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Az Azure virtuális gépek egy a megoldás olyan szervezeteknek, amelyek a számítási és tárolási erőforrásokat, szükséges minimális időben, és hosszabb beszerzési ciklusok nélkül. Azure virtuális gépek segítségével telepítheti a klasszikus alkalmazások, például SAP NetWeaver-alapú alkalmazások az Azure-ban. Az alkalmazás megbízhatósági és rendelkezésre állás további helyszíni erőforrások nélkül kiterjesztése. Az Azure virtuális gépek közötti kapcsolatot nyújthassanak, támogatja, így a Azure Virtual Machines integrálása a szervezete helyszíni tartományok, magánfelhőket és SAP rendszer fekvő.

Ez a cikk azt lépésének a virtuális gépek (VM) Azure, alternatív telepítési lehetőségeket és hibaelhárítási SAP-alkalmazások központi telepítése. Ebben a cikkben szereplő információk épít [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]. Kiegészíti is SAP dokumentáció és az SAP, az elsődleges erőforrások telepítéséhez és az SAP szoftverek telepítését.

## <a name="prerequisites"></a>Előfeltételek
Az SAP szoftver központi telepítése Azure virtuális gép beállítása magában foglalja a több lépéseket és erőforrásokat. Mielőtt elkezdené, győződjön meg arról, hogy teljesülnek-e az Azure virtuális gépek SAP szoftver telepítéséhez szükséges előfeltételeket.

### <a name="local-computer"></a>Helyi számítógép
Windows vagy Linux rendszerű virtuális gépek kezelésére, egy PowerShell-parancsfájlt, és az Azure-portálon is használhat. Mindkét eszközök van szüksége a Windows 7 vagy újabb verziója a Windows rendszert futtató számítógépről. Ha azt szeretné, csak a Linux virtuális gépek kezeléséhez, és ehhez a feladathoz egy Linux-számítógép használni kívánt, az Azure CLI-t is használhatja.

### <a name="internet-connection"></a>Internetkapcsolat
Töltse le és futtassa az eszközök és parancsfájlok, amelyek szükségesek a SAP szoftver központi telepítése, akkor kapcsolódnia kell az internethez. Az Azure virtuális Gépen, hogy fut az Azure fokozott Figyelőbővítmény az SAP is kell elérnie az interneten. Ha az Azure virtuális gép egy Azure-beli virtuális hálózat vagy a helyi tartományhoz tartozik, győződjön meg arról, hogy a megfelelő proxybeállítások vannak beállítva, a [a proxybeállítások][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-előfizetés
Egy aktív Azure-fiókra van szüksége.

### <a name="topology-and-networking"></a>Topológia és a hálózatkezelés
Meg kell határoznia a topológia és az SAP üzemelő példány architektúrájának az Azure-ban:

* Használható az Azure storage-fiókok
* Virtuális hálózaton az SAP rendszer központi telepítéséhez
* Erőforráscsoport kívánt az SAP rendszer központi telepítéséhez
* Azure-régió, ahol szeretné központilag telepíteni az SAP rendszer
* SAP-konfiguráció (kétrétegű és háromrétegű)
* Virtuálisgép-méretek és adatlemeznek csatlakoztatni kell a virtuális gépek száma
* SAP javítása és a szállítási rendszer (CTS) konfigurálása

Hozzon létre, és konfigurálja az Azure storage-fiókok (ha szükséges) vagy egy Azure virtuális hálózatot, a SAP szoftvertelepítési folyamat megkezdése előtt. Hozza létre és konfigurálja az ezekkel az erőforrásokkal kapcsolatos információkért lásd: [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>SAP méretezése
Az alábbi információkra, az SAP méretezési:

* Tervezett SAP-munkaterhelések, például a SAP gyors Szimbólumméretező eszköz és az SAP alkalmazás teljesítmény szabványos (SAP) használatával száma
* Szükséges CPU erőforrás és a memória-felhasználás az SAP rendszer
* Kötelező bemeneti/kimeneti (I/O) műveletek másodpercenkénti száma
* A végleges kommunikáció az Azure-ban futó virtuális gépek közötti hálózati sávszélesség szükséges.
* A helyszíni eszközök és az Azure rendszerbe SAP rendszer közötti szükséges hálózati sávszélesség

### <a name="resource-groups"></a>Erőforráscsoportok
Az Azure Resource Manager erőforráscsoportokat használhatja az alkalmazás-erőforrásokat az Azure-előfizetése kezeléséhez. További információkért lásd: [Azure Resource Manager áttekintése][resource-group-overview].

## <a name="resources"></a>További források

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-erőforrások
A SAP szoftvertelepítést állít be, amikor a következő SAP-erőforrások szüksége:

* SAP Megjegyzés [1928533], amelynek van:
  * Az SAP szoftver központi telepítése támogatott Azure Virtuálisgép-méretek listáját
  * Az Azure Virtuálisgép-méretek fontos készletkapacitás információival
  * Támogatott SAP szoftver, és az operációs rendszer és az adatbázis kombinációját
  * A Windows és a Microsoft Azure Linux szükséges SAP kernel verziója

* SAP Megjegyzés [2015553] a SAP-támogatott SAP szoftverek központi telepítése az Azure-ban szükséges előfeltételeket ismerteti.
* SAP Megjegyzés [2178632] tartalmaz részletes információkat az Azure-ban SAP jelentett összes figyelési metrikákat.
* SAP Megjegyzés [1409604] szükséges SAP gazdagép-ügynök verziója vannak a Windows Azure-ban.
* SAP Megjegyzés [2191498] vannak a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP Megjegyzés [2243692] SAP Azure Linux licenceléssel kapcsolatos információt tartalmaz.
* SAP Megjegyzés [1984787] SUSE Linux Enterprise Server 12 vonatkozó általános információkat tartalmaz.
* SAP Megjegyzés [2002167] Red Hat Enterprise Linux vonatkozó általános információkat tartalmaz 7.x.
* SAP Megjegyzés [2069760] Oracle Linux vonatkozó általános információkat tartalmaz 7.x.
* SAP Megjegyzés [1999351] további információkat talál az Azure fokozott Figyelőbővítmény az SAP rendelkezik.
* SAP Megjegyzés [1597355] Linux lapozóterület vonatkozó általános információkat tartalmaz.
* [SAP Azure Állapotváltozás oldalon](https://wiki.scn.sap.com/wiki/x/Pia7Gg) hírek és hasznos erőforrások gyűjteményét.
* [SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP megjegyzések Linux.
* SAP-specifikus PowerShell-parancsmagok részét képező [Azure PowerShell][azure-ps].
* SAP-specifikus Azure parancssori felület parancsait részét képező [Azure CLI][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-erőforrások
A Microsoft cikkeiben SAP üzemelő példányok Azure terjed ki:

* [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]
* [Az Azure virtuális gépek központi telepítésének SAP NetWeaver (Ez a cikk)][deployment-guide]
* [Az SAP NetWeaver Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure virtuális gépeken SAP szoftver központi telepítési forgatókönyvei
Lehetősége van több virtuális gépek és a kapcsolódó lemezt az Azure-ban való telepítéséhez. Fontos a központi telepítési lehetőségek közötti különbségek megértése mert előfordulhat, hogy a virtuális gépek előkészítése telepítéséhez választott központi telepítési típusa alapján különböző lépések végrehajtása.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>1. forgatókönyv: Központi telepítése egy virtuális Gépet az Azure piactérről SAP
A Microsoft által vagy az Azure piactéren harmadik fél által biztosított rendszerképet segítségével telepítse a virtuális Gépet. A piactér kínál néhány szabványos operációs rendszer képet, a Windows Server és a különböző Linux terjesztéseket. Is telepíthet egy olyanra, amely magában foglalja az adatbázis-kezelő rendszer (DBMS) SKU, például a Microsoft SQL Server. Az adatbázis-kezelő termékváltozatok lemezképek használatával kapcsolatos további információkért lásd: [SAP NetWeaver Azure virtuális gépek DBMS telepítésének][dbms-guide].

A következő folyamatábra azt mutatja be, az SAP-specifikus egy virtuális Gépet az Azure piactérről telepítésének lépései:

![Virtuális gép Virtuálisgép-lemezkép használatával az Azure piactérről SAP rendszerek központi telepítésének folyamatábrája][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása az Azure portál használatával
Az Azure portál segítségével új virtuális gép létrehozása az Azure piactérről képpel legegyszerűbben el.

1.  Ugrás a <https://portal.azure.com/#create/hub>.  Vagy válassza ki az Azure-portál menüjében, **+ új**.
2.  Válassza ki **számítási**, majd válassza ki a telepíteni kívánt operációs rendszer típusa. Például Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) vagy Oracle Linux 7.2. Az alapértelmezett listanézet nem szerepelnek az összes támogatott operációs rendszerek. Válassza ki **láthatja az összes** teljes listáját. A SAP szoftverek központi telepítéséhez támogatott operációs rendszerekkel kapcsolatos további információkért lásd: a SAP Megjegyzés [1928533].
3.  A következő oldalon tekintse át a feltételeket és kikötéseket.
4.  Az a **telepítési modell kiválasztása** jelölje ki **erőforrás-kezelő**.
5.  Kattintson a **Létrehozás** gombra.

A varázsló végigvezeti Önt a virtuális gép, minden szükséges erőforrásokat, például a hálózati adapterek és a storage-fiókok létrehozásához szükséges paraméterek beállítása. Ezek a paraméterek a következők:

1. **Alapvető**:
 * **Név**: az erőforrás (a virtuális gép neve) nevét.
 * **Virtuális gép lemeztípus**: válassza ki az operációsrendszer-lemezképet a lemez típusát. Ha azt szeretné, a prémium szintű Storage az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemezképet használatát javasoljuk.
 * **Felhasználónév és jelszó** vagy **nyilvános SSH-kulcs**: Adja meg a felhasználónevet és jelszót a kiépítés során létrehozott felhasználó. Linux virtuális gép a Secure Shell (SSH) kulccsal, amelyekkel jelentkezzen be a gépet is megadhatja.
 * **Előfizetés**: válassza ki az előfizetést, amely az új virtuális gép kiépítéséhez használni kívánt.
 * **Erőforráscsoport**: a virtuális gép az erőforráscsoport nevét. Új erőforráscsoport neve vagy a már meglévő erőforráscsoport nevét is megadhatja.
 * **Hely**: központi telepítése az új virtuális gép helyét. Ha azt szeretné, a virtuális gép csatlakozni a helyszíni hálózat, mindenképpen jelölje ki az Azure a helyszíni hálózathoz csatlakozó virtuális hálózati helyét. További információkért lásd: [Microsoft Azure hálózatkezelés] [ planning-guide-microsoft-azure-networking] a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide].
2. **Méret**:

     A támogatott virtuális gép típusainak listáját lásd: SAP Megjegyzés [1928533]. Győződjön meg arról, hogy ha a prémium szintű Azure Storage használni kívánt válassza ki a megfelelő Virtuálisgép-típussá. Nem minden virtuális gép esetében támogatja a prémium szintű Storage. További információkért lásd: [tárolási: Microsoft Azure Storage- és adatlemezek] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] és [prémium szintű Azure Storage] [ planning-guide-azure-premium-storage] a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide].

3. **Beállítások**:
  * **Storage**
    * **Lemez típusa**: válassza ki az operációsrendszer-lemezképet a lemez típusát. Ha azt szeretné, a prémium szintű Storage az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemezképet használatát javasoljuk.
    * **Felügyelt lemezeket használó**: ha felügyelt lemezeket használni szeretne, válassza az Igen lehetőséget. Felügyelt lemezek kapcsolatos további információkért lásd: fejezet [kezelt lemezek] [ planning-guide-managed-disks] a tervezési útmutatóban.
    * **A tárfiók**: Válasszon egy meglévő tárfiókot, vagy hozzon létre egy újat. Nem minden tárolási használható SAP-alkalmazások futtatására. További információ a tárolási típusok: [Microsoft Azure Storage] [ dbms-guide-2.3] a [SAP NetWeaver Azure virtuális gépek DBMS telepítésének][dbms-guide].
  * **Hálózat**
    * **Virtuális hálózati** és **alhálózati**: a virtuális gép integrálása az intraneten, válassza ki a virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
    * **Nyilvános IP-cím**: válassza ki a használni kívánt nyilvános IP-cím, vagy adjon meg egy új nyilvános IP-cím létrehozásához paramétereket. A nyilvános IP-cím segítségével fér hozzá a virtuális géphez az interneten keresztül. Győződjön meg arról, hogy a hálózati biztonsági csoportok segítségével biztonságossá tehető a virtuális gép létrehozása.
    * **Hálózati biztonsági csoport**: további információkért lásd: [hálózati biztonsági csoportokkal hálózati adatforgalom szabályozásához][virtual-networks-nsg].
  * **Bővítmények**: telepítése virtuálisgép-bővítmények központi hozzáadásával. Nem kell hozzáadnia a bővítmények ebben a lépésben. A SAP támogatásához szükséges extensions telepítve később. Című [konfigurálása az Azure fokozott Figyelőbővítmény az SAP] [ deployment-guide-4.5] az útmutatóban.
  * **Magas rendelkezésre állású**: Válasszon ki egy rendelkezésre állási csoportot, vagy adja meg a paraméter egy új rendelkezésre állási készlet létrehozásához. További információkért lásd: [Azure rendelkezésre állási készletek][planning-guide-3.2.3].
  * **Figyelés**
    * **Rendszerindítási diagnosztika**: kiválaszthatja **letiltása** vonatkozó rendszerindítási diagnosztika.
    * **Vendég operációs rendszer diagnosztika**: kiválaszthatja **letiltása** diagnosztika figyelésre.

4. **Összefoglalás**:

  Ellenőrizze a beállításokat, majd válassza ki **OK**.

A virtuális gép telepítve van a kijelölt erőforráscsoportban.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Hozzon létre egy virtuális gépet egy sablon használatával
A közzétett SAP-sablonok segítségével hozhat létre egy virtuális gépet a [azure-gyors üzembe helyezés-sablonok GitHub-tárházban][azure-quickstart-templates-github]. Emellett manuálisan is létrehozhat egy virtuális gép használatával a [Azure-portálon][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], vagy [Azure CLI][virtual-machines-linux-tutorial].

* [**A két-réteg konfigurációjához (csak egy virtuális gép) sablon** (sap-2-réteg-Piactéri-lemezképhez)][sap-templates-2-tier-marketplace-image]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép használatával hozhat létre.
* [**A két-réteg konfigurációjához (csak egy virtuális gép) sablon - lemezek felügyelt** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép és a felügyelt lemezek használatával hozhat létre.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon** (sap-3-réteg-Piactéri-lemezképhez)][sap-templates-3-tier-marketplace-image]

  Háromrétegű rendszer használatával több virtuális gép létrehozása, a sablon használatához.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon - lemezek felügyelt** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Ez a sablon segítségével több virtuális gépek és a felügyelt lemezek használatával a háromrétegű rendszer hozhat létre.

Az Azure portálon adja meg a sablon a következő paraméterekkel:

1. **Alapvető**:
  * **Előfizetés**: az előfizetése használja a sablon telepítéséhez.
  * **Erőforráscsoport**: az erőforráscsoport a sablon telepítéséhez használandó. Létrehozhat egy új erőforráscsoportot, vagy egy meglévő erőforráscsoportot kiválaszthatja az előfizetést.
  * **Hely**: Ha a sablon telepítéséhez. Ha egy meglévő erőforráscsoportot jelölt ki, az erőforráscsoport helye szolgál.

2. **Beállítások**:
  * **SAP Rendszerazonosító**: az SAP azonosító (SID).
  * **Operációs rendszer típusa**: A szeretné központilag telepíteni, például operációs rendszert, a Windows Server 2012 R2, a SUSE Linux Enterprise Server 12 (SLES 12), a Red Hat Enterprise Linux 7.2 (RHEL 7.2) vagy az Oracle Linux 7.2.

    A lista nézet nem szerepelnek az összes támogatott operációs rendszerek. A SAP szoftverek központi telepítéséhez támogatott operációs rendszerekkel kapcsolatos további információkért lásd: a SAP Megjegyzés [1928533].
  * **SAP mérete**: az SAP rendszer méretét.

    Az új rendszer biztosít SAP száma. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  * **Rendelkezésre állására** (csak a sablon háromrétegű): A rendszer rendelkezésre állását.

    Válassza ki **magas rendelkezésre ÁLLÁSÚ** konfiguráció esetén, amely lehetővé teszi egy magas rendelkezésre állású telepítéshez. Két és két kiszolgálói a ABAP SAP központi szolgáltatások (ASC) jönnek létre.
  * **Tárolási típus** (csak a sablon kétrétegű): A tároló típusa szerinti használatára.

    Nagyobb méretű rendszerekhez erősen ajánlott prémium szintű Azure Storage használatával. További információ a tárolási típusok a cikkekben találhat:
      * [A prémium szintű Azure SSD-tárolón SAP DBMS példány használata][2367194]
      * [A Microsoft Azure tárolás] [ dbms-guide-2.3] a [SAP NetWeaver Azure virtuális gépek DBMS központi telepítése][dbms-guide]
      * [Prémium szintű Storage: Nagy teljesítményű tárolást az Azure virtuális gépek terheléséhez][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
  * **Rendszergazda felhasználóneve** és **rendszergazdai jelszó**: felhasználónévvel és jelszóval.
    Új felhasználó jön létre, a virtuális géphez való bejelentkezéskor.
  * **Új vagy meglévő alhálózati**: meghatározza, hogy egy új virtuális hálózat és alhálózat jönnek létre, vagy egy létező alhálózatot használt. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válassza ki a **meglévő**.
  * **Alhálózati azonosító**: az azonosító az alhálózat a virtuális gépek csatlakozni fognak. Jelölje ki az alhálózatot, a virtuális magánhálózati (VPN) vagy a virtuális hálózati Azure ExpressRoute segítségével csatlakozzon a virtuális gép a helyszíni hálózathoz. Az azonosító általában néz ki: /subscriptions/&lt;előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;virtuálishálózat-név > /subnets/&lt;alhálózat neve >

3. **Feltételek és kikötések**:  
    Tekintse át és fogadja el a jogi feltételeket.

4.  Válassza ki **beszerzési**.

Az Azure Virtuálisgép-ügynök van telepítve alapértelmezés szerint az Azure Piactérről származó lemezkép használatakor.

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
Attól függően, hogy a helyszíni hálózat van konfigurálva szükség lehet a virtuális gépen a proxy beállítása. Ha a virtuális gép csatlakozik-e a helyi hálózaton keresztül VPN vagy ExpressRoute, a virtuális gép lehet, hogy nem fogja tudni érni az internetet, és nem fogja tudni letölteni a szükséges kiterjesztéseket vagy figyelési adatainak gyűjtéséről. További információkért lásd: [a proxybeállítások][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Csatlakozás egy tartományhoz (csak Windows)
Ha a helyszíni Active Directory vagy a DNS-példány egy Azure-webhelyek VPN-kapcsolat vagy ExpressRoute keresztül kapcsolódik az Azure-telepítés (Ez a lehetőség *létesítmények közötti* a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]), várható, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ez a feladat szempontjai kapcsolatos további információkért lásd: [egy virtuális gép csatlakozik egy helyszíni tartományhoz (csak Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Figyelés konfigurálása
Hogy az SAP támogatja a környezetben, állítsa be az Azure Figyelőbővítmény az SAP, a [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5]. Ellenőrizze az SAP-figyelési előfeltételeit, és szükséges minimális verziója a SAP Kernel és a gazdagép ügynöke SAP, az erőforrások szerepel [SAP erőforrások][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Figyelésének ellenőrzése
Ellenőrizze, hogy figyelési működik-e, a [ellenőrzések és végpont figyelő beállításával kapcsolatos hibaelhárítás][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Telepítés utáni lépéseket
Miután a virtuális gép létrehozása és a virtuális gép van telepítve, szükséges összetevők telepítését a virtuális gép kell. A központi telepítés/szoftverfrissítési telepítés során az ilyen típusú Virtuálisgép-telepítéshez, mert a szoftver telepítése már elérhetőnek kell lennie, vagy egy másik virtuális gépen, vagy csatolt lemeznek, az Azure-ban. Vagy, érdemes lehet egy létesítmények közötti forgatókönyv esetén a mely kapcsolat a helyszíni eszközök (telepítési megosztások) kap.

Miután telepítette a virtuális Gépet az Azure-ban, hajtsa végre az azonos irányelvek és eszközök SAP szoftver telepítése a virtuális gépen, mint a helyszíni környezetben. SAP telepítését egy Azure virtuális gépen, SAP és a Microsoft ajánlott, hogy töltse fel, és az SAP telepítési adathordozóján Azure virtuális merevlemezek vagy kezelt lemezeken tárolja, vagy egy fájlkiszolgálón, amely rendelkezik az összes szükséges SAP telepítési adathordozót, hogy hozzon létre egy Azure virtuális Gépen, amely működik.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>2. forgatókönyv: Egyéni lemezképet a virtuális gép telepítése az SAP
Az operációs rendszer vagy az adatbázis-kezelő különböző verziói különböző javítás igényekkel rendelkeznek, mert a lemezképek az Azure piactéren talált előfordulhat, hogy nem az igényeinek. Előfordulhat, hogy inkább kívánt virtuális gép létrehozása a saját operációs rendszer vagy adatbázis-kezelő Virtuálisgép-lemezkép, amely központilag telepíthető újra később.
Hozzon létre egy Linux titkos lemezképet létrehozni egy, a Windows, mint a különböző lépések segítségével.

- - -
> ![Windows][Logo_Windows] Windows
>
> Segítségével több virtuális gépet telepítsen egy Windows-lemezkép előkészítése, a Windows-beállítások (például a Windows biztonsági AZONOSÍTÓK és állomásnév) azért vagy a helyszíni virtuális Gépre általánosítva van. Használhat [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) ehhez.
>
> ![Linux][Logo_Linux] Linux
>
> Készítse elő a Linux-lemezkép segítségével több virtuális gépet telepítsen, egyes Linux beállítások azért vagy általánosítva van a helyszíni virtuális Gépre. Használhat `waagent -deprovision` ehhez. További információkért lásd: [Azure-on futó Linux virtuális gép rögzítése] [ virtual-machines-linux-capture-image] és a [Azure Linux ügynök felhasználói útmutató][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Készítse elő és hozzon létre egy egyéni lemezképet, és több új virtuális gépek létrehozásához kövesse. Erről a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]. Állítsa be az adatbázis-tartalom kezelővel SAP szoftver kiépítés egy új SAP rendszer telepítéséhez (visszaállítja egy adatbázis biztonsági mentése egy lemezt, amely a virtuális géphez csatolt) vagy közvetlenül egy adatbázis biztonsági mentését állítja vissza az Azure storage, ha az adatbázis-kezelő lehetővé teszi. További információkért lásd: [SAP NetWeaver Azure virtuális gépek DBMS telepítésének][dbms-guide]. Ha már telepítette a helyszíni virtuális Gépre (különösen a kétrétegű rendszerek) egy SAP rendszer, módosíthatja az SAP rendszerbeállítások az Azure virtuális gép központi telepítése után a rendszer átnevezése eljárással SAP szoftver kiépítés Manager által támogatott (SAP Megjegyzés [1619720]). Ellenkező esetben a SAP szoftver telepítéséhez, az Azure VM telepítése után.

A következő folyamatábra azt mutatja be, az SAP-specifikus egy virtuális Gépet egy egyéni lemezképről való telepítésének lépései:

![A Virtuálisgép-telepítéshez SAP rendszerekhez titkos piactéren Virtuálisgép-lemezkép használatával folyamatábrája][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása az Azure portál használatával
A legegyszerűbb egy új virtuális gép létrehozása egy felügyelt lemezképet módja az Azure portál használatával. Kezelése lemezképet létrehozásával kapcsolatos további információkért olvassa el a [egy általánosított virtuális gép az Azure-ban kezelt lemezképének elkészítése](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Ugrás a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Vagy válassza ki az Azure-portál menüjében, **képek**.
2.  Válassza ki a felügyelt lemezképét szeretné telepíteni, majd kattintson a **virtuális gép létrehozása**

A varázsló végigvezeti Önt a virtuális gép, minden szükséges erőforrásokat, például a hálózati adapterek és a storage-fiókok létrehozásához szükséges paraméterek beállítása. Ezek a paraméterek a következők:

1. **Alapvető**:
 * **Név**: az erőforrás (a virtuális gép neve) nevét.
 * **Virtuális gép lemeztípus**: válassza ki az operációsrendszer-lemezképet a lemez típusát. Ha azt szeretné, a prémium szintű Storage az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemezképet használatát javasoljuk.
 * **Felhasználónév és jelszó** vagy **nyilvános SSH-kulcs**: Adja meg a felhasználónevet és jelszót a kiépítés során létrehozott felhasználó. Linux virtuális gép a Secure Shell (SSH) kulccsal, amelyekkel jelentkezzen be a gépet is megadhatja.
 * **Előfizetés**: válassza ki az előfizetést, amely az új virtuális gép kiépítéséhez használni kívánt.
 * **Erőforráscsoport**: a virtuális gép az erőforráscsoport nevét. Új erőforráscsoport neve vagy a már meglévő erőforráscsoport nevét is megadhatja.
 * **Hely**: központi telepítése az új virtuális gép helyét. Ha azt szeretné, a virtuális gép csatlakozni a helyszíni hálózat, mindenképpen jelölje ki az Azure a helyszíni hálózathoz csatlakozó virtuális hálózati helyét. További információkért lásd: [Microsoft Azure hálózatkezelés] [ planning-guide-microsoft-azure-networking] a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide].
2. **Méret**:

     A támogatott virtuális gép típusainak listáját lásd: SAP Megjegyzés [1928533]. Győződjön meg arról, hogy ha a prémium szintű Azure Storage használni kívánt válassza ki a megfelelő Virtuálisgép-típussá. Nem minden virtuális gép esetében támogatja a prémium szintű Storage. További információkért lásd: [tárolási: Microsoft Azure Storage- és adatlemezek] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] és [prémium szintű Azure Storage] [ planning-guide-azure-premium-storage] a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide].

3. **Beállítások**:
  * **Storage**
    * **Lemez típusa**: válassza ki az operációsrendszer-lemezképet a lemez típusát. Ha azt szeretné, a prémium szintű Storage az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemezképet használatát javasoljuk.
    * **Felügyelt lemezeket használó**: ha felügyelt lemezeket használni szeretne, válassza az Igen lehetőséget. Felügyelt lemezek kapcsolatos további információkért lásd: fejezet [kezelt lemezek] [ planning-guide-managed-disks] a tervezési útmutatóban.
  * **Hálózat**
    * **Virtuális hálózati** és **alhálózati**: a virtuális gép integrálása az intraneten, válassza ki a virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
    * **Nyilvános IP-cím**: válassza ki a használni kívánt nyilvános IP-cím, vagy adjon meg egy új nyilvános IP-cím létrehozásához paramétereket. A nyilvános IP-cím segítségével fér hozzá a virtuális géphez az interneten keresztül. Győződjön meg arról, hogy a hálózati biztonsági csoportok segítségével biztonságossá tehető a virtuális gép létrehozása.
    * **Hálózati biztonsági csoport**: további információkért lásd: [hálózati biztonsági csoportokkal hálózati adatforgalom szabályozásához][virtual-networks-nsg].
  * **Bővítmények**: telepítése virtuálisgép-bővítmények központi hozzáadásával. Nem kell felvenni a bővítményt ebben a lépésben. A SAP támogatásához szükséges extensions telepítve később. Című [konfigurálása az Azure fokozott Figyelőbővítmény az SAP] [ deployment-guide-4.5] az útmutatóban.
  * **Magas rendelkezésre állású**: Válasszon ki egy rendelkezésre állási csoportot, vagy adja meg a paraméter egy új rendelkezésre állási készlet létrehozásához. További információkért lásd: [Azure rendelkezésre állási készletek][planning-guide-3.2.3].
  * **Figyelés**
    * **Rendszerindítási diagnosztika**: kiválaszthatja **letiltása** vonatkozó rendszerindítási diagnosztika.
    * **Vendég operációs rendszer diagnosztika**: kiválaszthatja **letiltása** diagnosztika figyelésre.

4. **Összefoglalás**:

  Ellenőrizze a beállításokat, majd válassza ki **OK**.

A virtuális gép telepítve van a kijelölt erőforráscsoportban.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Hozzon létre egy virtuális gépet egy sablon használatával
A központi telepítés létrehozásához Azure-portálról egy titkos operációsrendszer-lemezképek használatával, a következő SAP sablonok valamelyikét használja. Ezek a sablonok teszik közzé a [azure-gyors üzembe helyezés-sablonok GitHub-tárházban][azure-quickstart-templates-github]. Emellett manuálisan is létrehozhat egy virtuális gép használatával [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**A két-réteg konfigurációjához (csak egy virtuális gép) sablon** (sap-2-réteg-felhasználó-kép)][sap-templates-2-tier-user-image]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép használatával hozhat létre.
* [**A két-réteg konfigurációjához (csak egy virtuális gép) sablon - lemezképe felügyelt** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Hozzon létre egy kétszintű rendszer csak egy virtuális gép és egy felügyelt lemezképet, használja ezt a sablont.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon** (sap-3-réteg-felhasználó-kép)][sap-templates-3-tier-user-image]

  Ez a sablon segítségével egy háromrétegű rendszer több virtuális gép vagy a saját operációsrendszer-lemezképek használatával hozhat létre.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon - lemezképe felügyelt** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Hozzon létre egy háromrétegű rendszer több virtuális gép vagy a saját operációsrendszer-lemezképek és felügyelt lemezképét, használja ezt a sablont.

Az Azure portálon adja meg a sablon a következő paraméterekkel:

1. **Alapvető**:
  * **Előfizetés**: az előfizetése használja a sablon telepítéséhez.
  * **Erőforráscsoport**: az erőforráscsoport a sablon telepítéséhez használandó. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az előfizetést.
  * **Hely**: Ha a sablon telepítéséhez. Ha egy meglévő erőforráscsoportot jelölt ki, az erőforráscsoport helye szolgál.
2. **Beállítások**:
  * **SAP Rendszerazonosító**: A SAP rendszer azonosítóját.
  * **Operációs rendszer típusa**: az operációs rendszer típusa (Windows vagy Linux) telepíteni szeretné.
  * **SAP mérete**: az SAP rendszer méretét.

    Az új rendszer biztosít SAP száma. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  * **Rendelkezésre állására** (csak a sablon háromrétegű): A rendszer rendelkezésre állását.

    Válassza ki **magas rendelkezésre ÁLLÁSÚ** konfiguráció esetén, amely lehetővé teszi egy magas rendelkezésre állású telepítéshez. Két adatbázis-kiszolgálók és a két kiszolgáló ASC jönnek létre.
  * **Tárolási típus** (csak a sablon kétrétegű): A tároló típusa szerinti használatára.

    Nagyobb méretű rendszerekhez erősen ajánlott prémium szintű Azure Storage használatával. A tárolási típusok kapcsolatos további információkért lásd a következőket:
      * [A prémium szintű Azure SSD-tárolón SAP DBMS példány használata][2367194]
      * [A Microsoft Azure tárolás] [ dbms-guide-2.3] a [SAP NetWeaver Azure virtuális gépek DBMS központi telepítése][dbms-guide]
      * [Prémium szintű Storage: Nagy teljesítményű tárolást az Azure virtuális gépek terheléséhez][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
  * **Virtuális merevlemez URI felhasználói lemezkép** (csak a nem felügyelt lemez kép sablon esetében): az URI a saját operációs rendszerkép Indulni, például https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **Felhasználói lemezkép tárfiókja** (csak a nem felügyelt lemez kép sablon esetében): a saját operációsrendszer-lemezképek tárolására, például a tárfiók neve &lt;accountname > a https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (csak felügyelt lemezes kép sablon): a használni kívánt lemez felügyelt kép azonosítója
  * **Rendszergazda felhasználóneve** és **rendszergazdai jelszó**: A felhasználónevet és jelszót.

    Új felhasználó jön létre, a virtuális géphez való bejelentkezéskor.
  * **Új vagy meglévő alhálózati**: meghatározza, hogy egy új virtuális hálózat és alhálózat jön létre, vagy egy létező alhálózatot használt. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válassza ki a **meglévő**.
  * **Alhálózati azonosító**: az azonosító az alhálózat, amelyhez a virtuális gépek csatlakoznak. Jelölje ki az alhálózatot, a VPN- vagy ExpressRoute virtuális hálózat a virtuális gép a helyszíni hálózathoz való kapcsolódáshoz használandó. Az azonosító általában néz ki:

    következő&lt;előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;virtuálishálózat-név > /subnets/&lt;alhálózat neve >

3. **Feltételek és kikötések**:  
    Tekintse át és fogadja el a jogi feltételeket.

4.  Válassza ki **beszerzési**.

#### <a name="install-the-vm-agent-linux-only"></a>Telepítse a Virtuálisgép-ügynök (csak Linux)
A sablonokkal az előző szakaszban leírt, a Linux-ügynök már telepíteni kell a felhasználói kép, vagy a telepítés meghiúsul. Töltse le és telepítse az ügynököt a felhasználó kép [letöltése, telepítése és engedélyezése az Azure Virtuálisgép-ügynök][deployment-guide-4.4]. Ha nem adja meg a sablonok, is telepítheti a Virtuálisgép-ügynök újabb.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás egy tartományhoz (csak Windows)
Ha a helyszíni Active Directory vagy a DNS-példány egy Azure-webhelyek VPN-kapcsolat vagy Azure ExpressRoute keresztül kapcsolódik az Azure-telepítés (Ez a lehetőség *létesítmények közötti* a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]), várható, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ez a lépés szempontjai kapcsolatos további információkért lásd: [egy virtuális gép csatlakozik egy helyszíni tartományhoz (csak Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
Attól függően, hogy a helyszíni hálózat van konfigurálva szükség lehet a virtuális gépen a proxy beállítása. Ha a virtuális gép csatlakozik-e a helyi hálózaton keresztül VPN vagy ExpressRoute, a virtuális gép lehet, hogy nem fogja tudni érni az internetet, és nem fogja tudni letölteni a szükséges kiterjesztéseket vagy figyelési adatainak gyűjtéséről. További információkért lásd: [a proxybeállítások][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Figyelés konfigurálása
Hogy az SAP támogatja a környezetben, állítsa be az Azure Figyelőbővítmény az SAP, a [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5]. Ellenőrizze az SAP-figyelési előfeltételeit, és szükséges minimális verziója a SAP Kernel és a gazdagép ügynöke SAP, az erőforrások szerepel [SAP erőforrások][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Figyelésének ellenőrzése
Ellenőrizze, hogy figyelési működik-e, a [ellenőrzések és végpont figyelő beállításával kapcsolatos hibaelhárítás][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>3. forgatókönyv: Egy a helyszíni virtuális gép áthelyezése a SAP Azure nem általánosított virtuális merevlemez használatával
Ebben a forgatókönyvben kívánja áthelyezni a megadott SAP-rendszer egy a helyszíni környezetből Azure-bA. Ehhez a virtuális Merevlemezt, amely rendelkezik az operációs rendszer, az SAP bináris fájljainak és végül az adatbázis-kezelő bináris fájlokat, valamint a virtuális merevlemezek feltöltését az adatbázis-kezelő, az Azure-bA adatainak és naplókönyvtárainak fájlokkal. Eltérően ismertetett forgatókönyv [2. forgatókönyv: központi telepítése a virtuális gép és egy egyéni lemezképet az SAP][deployment-guide-3.3], ebben az esetben az állomásnevet, SAP SID mindig és SAP felhasználói fiókot az Azure virtuális gépen, mert a azok a helyszíni környezetben volt konfigurálva. Nem kell általánosítja az operációs rendszer. Ebben a forgatókönyvben leggyakrabban létesítmények közötti forgatókönyvek, ahol az SAP fekvő része a helyszíni fut, és részét fut az Azure vonatkozik.

Ebben a forgatókönyvben a VM-ügynök nem **nem** automatikusan telepített központi telepítése során. Mivel a Virtuálisgép-ügynök és az Azure fokozott Figyelőbővítmény az SAP Azure SAP NetWeaver futtatásához szükséges, meg kell letöltése, telepítése és a virtuális gép létrehozása után manuálisan engedélyezze a összetevőket egyaránt.

Az Azure Virtuálisgép-ügynök kapcsolatos további információkért lásd a következőket.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Az Azure virtuális gép ügynökének áttekintése][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux ügynök felhasználói útmutatója][virtual-machines-linux-agent-user-guide]
>
>

- - -

Az alábbi folyamatábra bemutatja a feladatütemezési lépések az Azure nem általánosított virtuális merevlemez használatával egy a helyszíni virtuális gép áthelyezése:

![Virtuális gép üzembe helyezéséhez egy Virtuálisgép-lemez SAP rendszerekhez folyamatábrája][deployment-guide-figure-400]

Ha a lemez már feltöltött és az Azure által meghatározott (lásd: [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]), tegye a feladatok leírása a következő szakaszok néhány.

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A központi telepítés létrehozása az Azure portálon keresztül titkos operációsrendszer-lemez használatával, a közzétett SAP-sablont használ a [azure-gyors üzembe helyezés-sablonok GitHub-tárházban][azure-quickstart-templates-github]. Emellett manuálisan is létrehozhat egy virtuális gép PowerShell használatával.

* [**A két-réteg konfigurációjához (csak egy virtuális gép) sablon** (sap-2-réteg-felhasználó-lemez)][sap-templates-2-tier-os-disk]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép használatával hozhat létre.
* [**A két-réteg konfigurációjához (csak egy virtuális gép) sablon - lemez felügyelt** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Hozzon létre egy kétszintű rendszer csak egy virtuális gép és egy felügyelt lemezt, használja ezt a sablont.

Az Azure portálon adja meg a sablon a következő paraméterekkel:

1. **Alapvető**:
  * **Előfizetés**: az előfizetése használja a sablon telepítéséhez.
  * **Erőforráscsoport**: az erőforráscsoport a sablon telepítéséhez használandó. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az előfizetést.
  * **Hely**: Ha a sablon telepítéséhez. Ha egy meglévő erőforráscsoportot jelölt ki, az erőforráscsoport helye szolgál.
2. **Beállítások**:
  * **SAP Rendszerazonosító**: A SAP rendszer azonosítóját.
  * **Operációs rendszer típusa**: az operációs rendszer típusa (Windows vagy Linux) telepíteni szeretné.
  * **SAP mérete**: az SAP rendszer méretét.

    Az új rendszer biztosít SAP száma. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  * **Tárolási típus** (csak a sablon kétrétegű): A tároló típusa szerinti használatára.

    Nagyobb méretű rendszerekhez erősen ajánlott prémium szintű Azure Storage használatával. A tárolási típusok kapcsolatos további információkért lásd a következőket:
      * [A prémium szintű Azure SSD-tárolón SAP DBMS példány használata][2367194]
      * [A Microsoft Azure tárolás] [ dbms-guide-2.3] a [SAP NetWeaver Azure virtuális gép DBMS központi telepítése][dbms-guide]
      * [Prémium szintű Storage: Nagy teljesítményű tárolást az Azure virtuális gépek terheléséhez][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
  * **Az operációs rendszer lemez VHD URI** (csak a sablon nem felügyelt lemezt): az URI-azonosítója a saját operációsrendszer-lemez, például https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Az operációs rendszer lemezén lemezazonosítót felügyelt** (csak felügyelt lemezes sablon): a kezelt lemez operációsrendszer-lemez, a azonosítója /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Új vagy meglévő alhálózati**: meghatározza, hogy egy új virtuális hálózat és alhálózat jönnek létre, vagy egy létező alhálózatot használt. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válassza ki a **meglévő**.
  * **Alhálózati azonosító**: az azonosító az alhálózat, amelyhez a virtuális gépek csatlakoznak. Jelölje ki az alhálózatot, a VPN- vagy Azure ExpressRoute virtuális hálózat a virtuális gép a helyszíni hálózathoz való kapcsolódáshoz használandó. Az azonosító általában néz ki:

    következő&lt;előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;virtuálishálózat-név > /subnets/&lt;alhálózat neve >

3. **Feltételek és kikötések**:  
    Tekintse át és fogadja el a jogi feltételeket.

4.  Válassza ki **beszerzési**.

#### <a name="install-the-vm-agent"></a>A Virtuálisgép-ügynök telepítése
A sablonok az előző szakaszban leírt használatához ügynököt telepíteni kell az operációsrendszer-lemezképet, vagy a telepítés meghiúsul. Töltse le és telepítse az ügynököt a virtuális gép, [letöltése, telepítése és engedélyezése az Azure Virtuálisgép-ügynök][deployment-guide-4.4].

Ha nem adja meg a sablonok az előző szakaszban leírt, később is telepítheti a Virtuálisgép-ügynök.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás egy tartományhoz (csak Windows)
Ha a helyszíni Active Directory vagy a DNS-példány egy Azure-webhelyek VPN-kapcsolat vagy ExpressRoute keresztül kapcsolódik az Azure-telepítés (Ez a lehetőség *létesítmények közötti* a [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide]), várható, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ez a feladat szempontjai kapcsolatos további információkért lásd: [egy virtuális gép csatlakozik egy helyszíni tartományhoz (csak Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
Attól függően, hogy a helyszíni hálózat van konfigurálva szükség lehet a virtuális gépen a proxy beállítása. Ha a virtuális gép csatlakozik-e a helyi hálózaton keresztül VPN vagy ExpressRoute, a virtuális gép lehet, hogy nem fogja tudni érni az internetet, és nem fogja tudni letölteni a szükséges kiterjesztéseket vagy figyelési adatainak gyűjtéséről. További információkért lásd: [a proxybeállítások][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Figyelés konfigurálása
Hogy az SAP támogatja a környezetben, állítsa be az Azure Figyelőbővítmény az SAP, a [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5]. Ellenőrizze az SAP-figyelési előfeltételeit, és szükséges minimális verziója a SAP Kernel és a gazdagép ügynöke SAP, az erőforrások szerepel [SAP erőforrások][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Figyelésének ellenőrzése
Ellenőrizze, hogy figyelési működik-e, a [ellenőrzések és végpont figyelő beállításával kapcsolatos hibaelhárítás][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Az SAP figyelési konfiguráció frissítése
Az alábbi esetekben az SAP figyelési konfigurációjának frissítése:
* A közös Microsoft/SAP munkacsoport kibővíti a figyelési lehetőségek körét, és több vagy kevesebb számlálók kéri.
* Microsoft bemutatkozik az alapul szolgáló Azure-infrastruktúra, amely biztosítja a figyelési adatok új verziója, és az Azure fokozott Figyelőbővítmény az SAP kell ezeket a módosításokat kell igazítani.
* További adatlemezt csatlakoztatása az Azure virtuális géphez, vagy egy adatlemez eltávolítása. Ebben a forgatókönyvben frissítése tárolással kapcsolatos adatok gyűjtését. A konfiguráció módosítása felvételét vagy törlését végpontok vagy IP-címek hozzárendelése a virtuális gép a figyelési konfiguráció nincs hatással.
* Az Azure virtuális gép méretét például vált mérete A5 más Virtuálisgép-méretet.
* Új hálózati adapterek hozzáadása az Azure virtuális gép.

A figyelési beállításokat frissítéséhez frissítse a felügyeleti infrastruktúra lépéseit követve [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Feladatok részletesen SAP szoftver központi telepítése
Ez a szakasz részletes van arról, hogy adott feladatokat a konfigurációs és központi telepítési folyamat lépéseit.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell-parancsmagok telepítése
1.  Ugrás a [letölti a Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  A **parancssori eszközök**a **PowerShell**, jelölje be **Windows telepítése**.
3.  A Microsoft letöltése Manager párbeszédpanelen a letöltött fájl (például WindowsAzurePowershellGet.3f.3f.3fnew.exe), válassza ki a **futtatása**.
4.  Microsoft Webplatform-telepítővel (a Microsoft Web-PI) futtatásához válasszon **Igen**.
5.  Egy oldal, amely a jelek szerint ez jelenik meg:

  ![Telepítés lap az Azure PowerShell-parancsmagok][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Válassza ki **telepítése**, és fogadja el a Microsoft szoftverlicenc-szerződést.
7.  A PowerShell telepítve van. Válassza ki **Befejezés** a telepítővarázsló bezárásához.

Gyakran elérhető frissítések ellenőrzése a PowerShell-parancsmag, amely általában frissítése havi. A frissítések keresését legkönnyebben az előző telepítési lépéseket, akár a telepítés oldal megjelenik az 5. lépés elvégzéséhez. A kiadási dátum és a kiadás száma a parancsmagok részét képezik a lapon látható az 5. Amennyiben másképp SAP Megjegyzés [1928533] vagy SAP Megjegyzés [2015553], azt javasoljuk, hogy a legújabb Azure PowerShell-parancsmagok használata.

Az Azure PowerShell-parancsmagokat a számítógépre telepített verzió ellenőrzéséhez futtassa a PowerShell-parancsot:
```powershell
(Get-Module AzureRm.Compute).Version
```
Az eredmény így néz ki:

![Azure PowerShell parancsmag-verziójának ellenőrzése eredménye][deployment-guide-figure-600]
<a name="figure-6"></a>

Az aktuális verzióját a számítógépen telepített Azure parancsmag verzió esetén a telepítési varázsló első oldalán azt mutatja, hogy hozzáadásával **(telepített)** számára a termék nevét (lásd az alábbi képernyőfelvételen). Az PowerShell Azure-parancsmagok naprakészek legyenek. Zárja be a telepítővarázslót, jelölje be **kilépési**.

![Jelzi, hogy a legfrissebb Azure PowerShell-parancsmagjai telepítve vannak az Azure PowerShell-parancsmagok telepítési lapját][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Az Azure parancssori felület telepítése
1.  Ugrás a [letölti a Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  A **parancssori eszközök**a **Azure parancssori felület**, jelölje be a **telepítése** hivatkozást az operációs rendszerhez.
3.  A Microsoft letöltése Manager párbeszédpanelen a letöltött fájl (például WindowsAzureXPlatCLI.3f.3f.3fnew.exe), válassza ki a **futtatása**.
4.  Microsoft Webplatform-telepítővel (a Microsoft Web-PI) futtatásához válasszon **Igen**.
5.  Egy oldal, amely a jelek szerint ez jelenik meg:

  ![Telepítés lap az Azure PowerShell-parancsmagok][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Válassza ki **telepítése**, és fogadja el a Microsoft szoftverlicenc-szerződést.
7.  Az Azure parancssori felület telepítve van. Válassza ki **Befejezés** a telepítővarázsló bezárásához.

Ellenőrizze gyakran az Azure parancssori felület, amely általában frissül havi frissítések. A frissítések keresését legkönnyebben az előző telepítési lépéseket, akár a telepítés oldal megjelenik az 5. lépés elvégzéséhez.

Azure CLI-t a számítógépen telepített verziójának ellenőrzéséhez futtassa ezt a parancsot:
```
azure --version
```

Az eredmény így néz ki:

![Az Azure parancssori felület verziójának ellenőrzése eredménye][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>A virtuális gép csatlakoztatása egy helyszíni tartományban (csak Windows)
SAP virtuális gépek központi telepítését a létesítmények közötti forgatókönyv, ahol a helyszíni Active Directory és a DNS-bővítve lettek az Azure-ban, ha szükséges, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ügyfél függ a részletes lépései a virtuális gép csatlakoztatása egy helyszíni tartományban, és a szükséges egy helyszíni tartomány tagja lehet további szoftvereket. Általában a virtuális gép csatlakoztatása egy helyszíni tartományban, kell további szoftvereket, például a kártevőirtó szoftver, és a figyelési vagy biztonsági mentési szoftver telepítése.

Ebben a forgatókönyvben szükség is győződjön meg arról, hogy ha az internetes proxybeállításokat kényszerítve vannak, amikor egy virtuális gép csatlakozik egy tartományhoz a környezetben, a Windows helyi rendszerfiók (S-1-5-18) a Vendég virtuális gépen a proxybeállításokat. A legegyszerűbb lehetőség a proxy kényszerítheti a tartomány csoportházirend, amely a tartomány rendszerekre vonatkozik.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Letöltése, telepítése és engedélyezése az Azure Virtuálisgép-ügynök
(Például egy olyanra, amely nem származnak, a Windows rendszer-előkészítő vagy, a sysprep eszköz) nem általánosított OS lemezképből telepített virtuális gépek esetében meg kell manuálisan letöltése, telepítése és engedélyezése az Azure Virtuálisgép-ügynök.

Ha egy virtuális Gépet az Azure piactérről, ebben a lépésben nincs szükség. Az Azure piactérről képek már rendelkezik az Azure Virtuálisgép-ügynök.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Töltse le az Azure virtuális gép ügynököt:
  1.  Töltse le a [Azure Virtuálisgép-ügynök a telepítőcsomag](https://go.microsoft.com/fwlink/?LinkId=394789).
  2.  A virtuális gép ügynök MSI-csomag helyileg tárolja egy számítógépre vagy a kiszolgáló.
2.  Az Azure Virtuálisgép-ügynök telepítése:
  1.  A telepített Azure virtuális gép kapcsolódni a távoli asztal protokoll (RDP) használatával.
  2.  Nyissa meg a Windows Intéző ablakot a virtuális Gépre, és válassza ki a cél könyvtárat az MSI-fájl, a Virtuálisgép-ügynök.
  3.  Húzza az Azure virtuális gép ügynök telepítő MSI-fájl a helyi számítógép kiszolgáló a célkönyvtár a Virtuálisgép-ügynök a virtuális Gépen.
  4.  Kattintson duplán a virtuális Gépen az MSI-fájl.
3.  Virtuális gépekhez, amely tagja a helyi tartomány, győződjön meg arról, hogy végleges internetes proxybeállításokat is vonatkozik a Windows helyi rendszerfiók (S-1-5-18) a virtuális gép, lásd: [a proxybeállítások][deployment-guide-configure-proxy]. A Virtuálisgép-ügynök ebben a környezetben fut, és csatlakozni Azure képesnek kell lennie.

Az Azure virtuális gép ügynökének frissítése nincs felhasználói beavatkozás szükséges. A Virtuálisgép-ügynök automatikusan frissül, és a virtuális gép újraindítása nem szükséges.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Az alábbi parancsokkal a Virtuálisgép-ügynök telepítése Linux rendszerhez:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) vagy Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Ha az ügynök már telepítve van, az Azure Linux ügynök frissítése a lépések ismertetett [a virtuális gép az Azure Linux ügynök frissítése a legújabb verzióra a Githubról][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>A proxy konfigurálása
Konfigurálja a proxyt a Windows elvégzendő műveleteket a proxy konfigurálását a Linux eltérnek.

#### <a name="windows"></a>Windows
Proxybeállítások kell állítható be megfelelően a helyi rendszer fiók az Internet eléréséhez. Ha a proxybeállítások nem csoportházirend-beállításokat, konfigurálhatja a helyi rendszer fiók beállításait.

1. Lépjen **Start**, adja meg **gpedit.msc**, majd válassza ki **Enter**.
2. Válassza ki **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők** > **Internet Explorer**. Győződjön meg arról, hogy a beállítás **proxybeállítások beállítások számítógépenkénti (nem felhasználónként)** le van tiltva vagy nincs konfigurálva.
3. A **Vezérlőpult**, és **hálózati és megosztási központ** > **Internetbeállítások**.
4. Az a **kapcsolatok** lapon jelölje be a **LAN-beállítások** gombra.
5. Törölje a jelet a **beállítások automatikus észlelése** jelölőnégyzetet.
6. Válassza ki a **proxykiszolgálót használni a helyi hálózaton** jelölőnégyzetet, majd írja be a proxy címét és portját.
7. Válassza ki a **speciális** gombra.
8. Az a **kivételek** mezőbe írja be az IP-cím **168.63.129.16**. Kattintson az **OK** gombra.

#### <a name="linux"></a>Linux
A proxykiszolgáló konfigurálása a Microsoft Azure Vendég ügynöke, amely itt található: a konfigurációs fájlban \\stb\\waagent.conf.

Állítsa be a következő paraméterekkel:

1.  **HTTP-proxy állomás**. Például állítsa az értékét **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **HTTP-proxy port**. Például állítsa az értékét **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  Indítsa újra az ügynököt.

  ```
  sudo service waagent restart
  ```

A proxybeállításai \\stb\\waagent.conf is vonatkoznak a szükséges Virtuálisgép-bővítmények. Ha szeretné használni az Azure tárházak találhatók, győződjön meg arról, hogy a forgalmat a tárolóhelyekkel nem lesz a helyi intraneten keresztül. Ha létrehozta a felhasználó által definiált útvonalak kényszerített bújtatás engedélyezése, győződjön meg arról, hogy adjon hozzá egy útvonalat, amely irányítja a forgalmat a tárolóhelyekkel közvetlenül az internethez, és nem az a pont-pont VPN-kapcsolaton keresztül történő.

* **SLES**

  Is kell beállítani az útvonalakat az IP-címek szerepelnek \\stb\\regionserverclnt.cfg. Az alábbi ábra egy példát mutat be:

  ![Alagúthasználat kényszerítése][deployment-guide-figure-50]


* **RHEL**

  Sikerült beállítani az útvonalakat a gazdagépek szereplő IP-címek is kell \\stb\\yum.repos.d\\rhui terheléselosztók. Tegyük fel tekintse meg az előző ábrán.

* **Oracle Linux**

  Nincsenek nem tárolóhelyekkel Oracle Linux Azure-on. A saját adattárak konfigurálása az Oracle Linux vagy a nyilvános tárházak használni kell.

Felhasználó által definiált útvonalakra vonatkozó további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Az Azure bővített Monitorozási bővítményt az SAP konfigurálása
Ha előkészítése után a virtuális Gépet a [szituációkban az SAP Azure virtuális gépek][deployment-guide-3], az Azure Virtuálisgép-ügynök telepítve van a virtuális gépen. A következő lépés a központi telepítése az Azure fokozott Figyelőbővítmény az SAP, amely letölthető az Azure-bővítmény tárház a globális Azure adatközpontjaiban. További információkért lásd: [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver][planning-guide-9.1].

PowerShell vagy Azure CLI telepítése és konfigurálása az Azure fokozott Figyelőbővítmény az SAP használhatja. A bővítmény telepítéséhez Windows vagy Linux virtuális Gépet egy Windows-számítógép segítségével, tekintse át [Azure PowerShell][deployment-guide-4.5.1]. A bővítmény telepítéséhez a Linux virtuális gép Linux asztali használatával, lásd: [Azure CLI][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>A Linux és a Windows virtuális gépek az Azure PowerShell
Az Azure fokozott Figyelőbővítmény az SAP telepítése a PowerShell használatával:

1. Győződjön meg arról, hogy telepítette-e az Azure PowerShell-parancsmag a legújabb verzióra. További információkért lásd: [telepítése Azure PowerShell-parancsmagok][deployment-guide-4.1].  
2. Futtassa az alábbi PowerShell-parancsmagot.
    A rendelkezésre álló környezeteket listája, futtassa a `commandlet Get-AzureRmEnvironment`. Globális Azure használni kívánt, ha a környezet olyan **AzureCloud**. Az Azure Kínában, válassza ki a **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Után adja meg a fiók adatait, és az Azure virtuális gépének azonosítását, a parancsfájl telepíti a szükséges kiterjesztéseket, és lehetővé teszi, hogy a szükséges funkciókat. Ez több percig is eltarthat.
További információ `Set-AzureRmVMAEMExtension`, lásd: [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![SAP-specifikus sikeres végrehajtását az Azure parancsmag Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

A `Set-AzureRmVMAEMExtension` konfigurációs does az SAP figyelési állomás konfigurálása a lépéseket.

A parancsfájl kimenete a következő információkat tartalmazza:

* A megerősítő, hogy az operációsrendszer-lemezképet, és minden további adatlemezt megfigyelését van konfigurálva.
* A következő két üzenetek erősítse meg Storage Metrics egy bizonyos tárolási fiókot.
* A kimenet egy sor biztosít a tényleges frissítés a figyelési konfiguráció állapota.
* A kimenet egy másik sor megerősíti, hogy a konfigurációs telepíteni vagy frissíteni.
* A kimenet utolsó sora tájékoztató. Azt ismerteti, hogy a lehetőségeket a figyelési konfiguráció tesztelése.
* Ellenőrizze, hogy minden lépést Azure fokozott figyelési sikeresen végrehajtotta-e, és, hogy az Azure infrastruktúra adja meg a szükséges adatait, folytatásához a készültség-ellenőrzés az Azure fokozott Figyelőbővítmény az SAP, az ismertetett módon [készültség-ellenőrzés Azure fokozott figyelés az SAP][deployment-guide-5.1].
* Várja meg a 15-30 perces az Azure Diagnostics a megfelelő adatok gyűjtéséért felelős ügyfélfeladatot.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux virtuális gépek az Azure parancssori felület
Az Azure fokozott Figyelőbővítmény az SAP telepítése az Azure parancssori felület használatával:

1. Telepítse az Azure CLI 1.0-s, leírtak szerint [telepítse az Azure CLI 1.0][azure-cli].
2. Jelentkezzen be az Azure-fiókjával:

  ```
  azure login
  ```

3. Váltson Azure Resource Manager módra:

  ```
  azure config mode arm
  ```

4. Az Azure bővített figyelés engedélyezése:

  ```
  azure vm enable-aem <resource-group-name> <vm-name>
  ```

5. Győződjön meg arról, hogy-e az Azure Linux virtuális gép aktív az Azure fokozott Figyelőbővítmény. Ellenőrizze, hogy a fájl \\var\\lib\\AzureEnhancedMonitor\\PerfCounters létezik-e. Ha létezik, a parancssorban futtassa ezt a parancsot az Azure fokozott figyelő által gyűjtött információk megjelenítése:
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

A kimeneti így néz ki:
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
???
???
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Ellenőrzések és a hibaelhárítás a végpont figyelő
Után az Azure virtuális gép telepítését és a kapcsolódó Azure-figyelési infrastruktúra beállítása, ellenőrizze, hogy az Azure fokozott Figyelőbővítmény összetevőit van a várt módon működik.

A készenléti-ellenőrzés futtatása a az Azure fokozott Figyelőbővítmény az SAP leírtak [az Azure fokozott Figyelőbővítmény az SAP készültség-ellenőrzés][deployment-guide-5.1]. Ha az összes készenléti ellenőrzésének az eredménye pozitív, és megfelelő teljesítményszámlálók jelennek meg az OK gombra, Azure figyelés beállítása sikeresen. Nyugodtan folytathatja a SAP állomás ügynök telepítése, a SAP jegyzetének [SAP erőforrások][deployment-guide-2.2]. A készültség-ellenőrzés azt jelzi, hogy a számlálók hiányoznak, ha a rendszerállapot-ellenőrzés futtatása az Azure felügyeleti infrastruktúra leírtak [Azure figyelési infrastruktúra konfigurálása az állapot-ellenőrzéssel][deployment-guide-5.2]. További hibaelhárítási lehetőségekkel, lásd: [az SAP figyelése Azure hibaelhárítási][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Az Azure fokozott Figyelőbővítmény az SAP készültség-ellenőrzés
Ez az ellenőrzés gondoskodik arról, hogy az SAP-alkalmazás belsejében szerepelhet a metrikák a mögöttes Azure felügyeleti infrastruktúra által biztosított.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>A Windows virtuális gép készültség-ellenőrzés futtatása

1.  Jelentkezzen be az Azure virtuális géphez (rendszergazdai fiók használata nem szükséges).
2.  Nyisson meg egy parancssori ablakot.
3.  A parancssorban módosítsa a könyvtárat a telepítési mappájába másolja az Azure fokozott Figyelőbővítmény az SAP: C:\\csomagok\\beépülő modulok\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verzió >\\dobja el

  A *verzió* elérési útját a monitorozási bővítményt függően változhat. Ha a mappák különböző verzióinak a monitorozási bővítményt az telepítési mappájában, ellenőrizze a AzureEnhancedMonitoring Windows-szolgáltatás konfigurációját, és majd átváltása tüntetve ennek az a mappa *végrehajtható fájl elérési útja*.

  ![Az Azure fokozott Figyelőbővítmény az SAP futó szolgáltatás tulajdonságai][deployment-guide-figure-1000]

4.  A parancssorban futtassa **azperflib.exe** paraméter nélkül.

  > [!NOTE]
  > Azperflib.exe hurok fut, és frissíti a gyűjtött teljesítményszámlálók 60 másodpercenként. A hurok befejezéséhez zárja be a parancssori ablakot.
  >
  >

Ha az Azure fokozott Figyelőbővítmény nincs telepítve, vagy a AzureEnhancedMonitoring szolgáltatás nem fut, a bővítmény konfigurációja nem megfelelő. A bővítmény telepítésével kapcsolatos részletes információkért lásd: [hibaelhárítása az Azure felügyeleti infrastruktúra az SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Ellenőrizze a azperflib.exe kimenete
Azperflib.exe az alábbiakat mutatja be, minden Azure teljesítményszámlálók feltöltve az olyan SAP. Gyűjtött teljesítményszámlálók listája alján összegzéseket és az állapotát jelző Azure figyelő állapotának megjelenítése.

![Az állapot-ellenőrzéssel azperflib.exe, amely azt jelzi, hogy létezik-e nem jelez problémákat végrehajtásával kimenete][deployment-guide-figure-1100]
<a name="figure-11"></a>

Ellenőrizze az eredményt adott vissza a **számlálók teljes** kimenete, amely üres, és a jelentett **állapot**, az előző ábrán látható.

Az eredményül kapott érték az alábbiak szerint értelmezni:

| Azperflib.exe eredmény értékek | Azure figyelési állapot |
| --- | --- |
| **Az API-hívásokban - nem érhető el** | Előfordulhat, hogy mutat be, melyek nem érhető el vagy nem alkalmazható a virtuális gép konfigurációjából, vagy hibák. Lásd: **állapot**. |
| **Számlálók összesen – üres** |Lehet, hogy üres a következő két az Azure storage-számlálókat: <ul><li>Tárolási olvasási Op késés Server MS</li><li>Tárolási olvasási Op késés E2E MS</li></ul>Az összes számláló értéknek kell lennie. |
| **Állapot ellenőrzése** |Ha csak OK vissza állapotát jeleníti meg **OK**. |
| **Diagnosztika** |Állapotfigyelő állapotával kapcsolatos részletes információkért. |

Ha a **állapot** értéke nem **OK**, kövesse az utasításokat a [Azure figyelési infrastruktúra konfigurálása az állapot-ellenőrzéssel][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>A Linux virtuális gép készültség-ellenőrzés futtatása

1.  Csatlakozás az Azure virtuális gép SSH használatával.

2.  Ellenőrizze az Azure fokozott Figyelőbővítmény kimenetét.

  a.  Futtassa a `more /var/lib/AzureEnhancedMonitor/PerfCounters` parancsot.

   **A várt eredmény**: teljesítményszámlálók listája értéket ad vissza. A fájl nem lehet üres.

 b. Futtassa a `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` parancsot.

   **A várt eredmény**: értéket ad vissza egy sorban, ahol a hiba a következő **nincs**, például **3; config; Hiba történt; 0, 0; Nincs; 0; 1456416792; teszt-servercs;**

  c. Futtassa a `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` parancsot.

    **A várt eredmény**: ad vissza, üres vagy nem létezik.

Ha az előző ellenőrzése sikertelen volt, futtassa a további ellenőrzést:

1.  Győződjön meg arról, hogy a waagent telepítve és engedélyezve van.

  a.  Futtassa a `sudo ls -al /var/lib/waagent/` parancsot.

      **A várt eredmény**: a tartalom a waagent könyvtár jeleníti meg.

  b.  Futtassa a `ps -ax | grep waagent` parancsot.

   **A várt eredmény**: egy bejegyzést, jeleníti meg:`python /usr/sbin/waagent -daemon`

3.   Győződjön meg arról, hogy az Azure fokozott Figyelőbővítmény telepítve és fut-e.

  a.  Futtassa a `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` parancsot.

    **A várt eredmény**: a tartalom az Azure fokozott Figyelőbővítmény könyvtár jeleníti meg.

  b. Futtassa a `ps -ax | grep AzureEnhanced` parancsot.

     **A várt eredmény**: egy bejegyzést, jeleníti meg:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

3. SAP Megjegyzés SAP Állomásügynök telepítse [1031096], és ellenőrizze a kimenetét `saposcol`.

  a.  Futtassa a `/usr/sap/hostctrl/exe/saposcol -d` parancsot.

  b.  Futtassa a `dump ccm` parancsot.

  c.  Ellenőrizze, hogy a **Virtualization_Configuration\Enhanced figyelési hozzáférés** metrika **igaz**.

Ha már van telepítve egy SAP NetWeaver ABAP alkalmazáskiszolgálót, nyissa meg a tranzakció ST06, és ellenőrizze a fokozott ellenőrzés engedélyezve van-e.

Ha minden ellenőrzés nem sikerül, és telepítse újra a bővítmény kapcsolatos részletes információkért lásd: [hibaelhárítása az Azure felügyeleti infrastruktúra az SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>A figyelési Azure infrastruktúra-konfiguráció állapotának ellenőrzése
Ha néhány a figyelés nem kerülnek az adatok a vizsgálat leírtak szerint megfelelően [készültség-ellenőrzés Azure fokozott figyelés az SAP][deployment-guide-5.1]- ben futtassa a `Test-AzureRmVMAEMExtension` parancsmag futtatásával ellenőrizze, hogy az Azure felügyeleti infrastruktúra és a figyelés az SAP bővítmény helyesen vannak konfigurálva.

1.  Győződjön meg arról, hogy telepítette-e a legújabb verzióját a következő Azure PowerShell-parancsmag, a [telepítése Azure PowerShell-parancsmagok][deployment-guide-4.1].
2.  Futtassa az alábbi PowerShell-parancsmagot. Rendelkezésre álló környezeteket listájának megtekintéséhez futtassa a parancsmagot `Get-AzureRmEnvironment`. Globális Azure használatához válassza a **AzureCloud** környezetben. Az Azure Kínában, válassza ki a **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Login-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  Adja meg a fiók adatait, és az Azure virtuális gépének azonosítását.

  ![SAP-specifikus bemeneti oldalán Azure parancsmag teszt-VMConfigForSAP_GUI][deployment-guide-figure-1200]

4. A parancsfájl ellenőrzi a kiválasztott virtuális gép konfigurációját.

  ![Az SAP az Azure felügyeleti infrastruktúra sikeres vizsgálat kimeneti][deployment-guide-figure-1300]

Győződjön meg arról, hogy minden rendszerállapot-ellenőrzés eredménye **OK**. Ha nem jelennek meg az egyes ellenőrzések **OK**, a frissítés parancsmagot futtathatja a [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5]. Várjon 15 percig, és a leírt ellenőrzésekként [készültség-ellenőrzés Azure fokozott figyelés az SAP] [ deployment-guide-5.1] és [Azure figyelési infrastruktúra konfiguráció állapot-ellenőrzéssel][deployment-guide-5.2]. Ha az ellenőrzések még néhány vagy az összes számlálók problémát jelez, lásd: [hibaelhárítása az Azure felügyeleti infrastruktúra az SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Az Azure felügyeleti infrastruktúra SAP-hibaelhárítás

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Az Azure teljesítményszámlálók nem jelennek meg minden
A AzureEnhancedMonitoring Windows-szolgáltatás az Azure-ban teljesítménymutatók gyűjti. Ha a szolgáltatás nem lett megfelelően telepítve, vagy ha a virtuális gép nem fut, nincs teljesítménymutatók gyűjthetők össze.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>A telepítési könyvtár az Azure fokozott ellenőrzésére bővítmény nem üres

###### <a name="issue"></a>Probléma
A telepítési könyvtár C:\\csomagok\\beépülő modulok\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verzió >\\eldobási értéke üres.

###### <a name="solution"></a>Megoldás
A bővítmény nincs telepítve. Határozza meg, hogy ez-e a proxy problémát (a korábban ismertetett). Szükség lehet, hogy indítsa újra a gépet, vagy futtassa újra a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájl.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Azure fokozott ellenőrzésére szolgáltatás nem létezik.

###### <a name="issue"></a>Probléma
A AzureEnhancedMonitoring Windows szolgáltatás nem létezik.

Azperflib.exe kimenet hibát jelez:

![Azperflib.exe végrehajtásának azt jelzi, hogy az SAP, az Azure fokozott Figyelőbővítmény a szolgáltatás nem fut.][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Megoldás
Ha a szolgáltatás nem létezik, az Azure fokozott Figyelőbővítmény az SAP nem lett megfelelően telepítve. A bővítmény újratelepíteni az üzembe helyezési forgatókönyvnek a ismertetett lépések segítségével [szituációkban az SAP, az Azure virtuális gépek][deployment-guide-3].

Miután telepítette a kiterjesztés egy óra múlva, ellenőrizze újra az Azure teljesítményszámlálók igénybe az Azure virtuális Géphez.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Azure fokozott ellenőrzésére szolgáltatás létezik, de nem indul el

###### <a name="issue"></a>Probléma
A AzureEnhancedMonitoring Windows szolgáltatás létezik-e és engedélyezve van, de nem indul el. További információért tekintse meg az alkalmazások eseménynaplójában.

###### <a name="solution"></a>Megoldás
A konfiguráció nem megfelelő. Indítsa újra a monitorozási bővítményt a virtuális gép számára, lásd: [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Egyes Azure teljesítményszámlálók hiányoznak.
A AzureEnhancedMonitoring Windows-szolgáltatás az Azure-ban teljesítménymutatók gyűjti. A szolgáltatás több forrásból származó adatokat lekérdezi. Bizonyos konfigurációs adatok gyűjtése helyileg, és az Azure Diagnostics olvasható néhány teljesítménymutatók. A tárolási előfizetési szintről a naplózás tárolási számlálók használtak.

Ha hibaelhárítása SAP Megjegyzés használatával [1999351] nem oldja meg a problémát, futtassa újra a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájl. Előfordulhat, hogy Várjon egy órát, mert a storage analytics vagy a diagnosztika számlálók előfordulhat, hogy nem jönnek létre, azonnal követően engedélyezve vannak. Ha a probléma továbbra is fennáll, a Windows BC-OP-NT-AZR összetevő vagy BC-OP-LNX-AZR Linux virtuális gép SAP felhasználói támogatási üzenet megnyitása.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Az Azure teljesítményszámlálók nem jelennek meg minden
Az Azure-ban teljesítménymutatók démon által gyűjtött. Ha a démon nem fut, nincs teljesítménymutatók gyűjthetők össze.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>A telepítési könyvtár az Azure figyelési fokozott kiterjesztés nem üres

###### <a name="issue"></a>Probléma
A könyvtár \\var\\lib\\waagent\\ nem rendelkezik az Azure fokozott figyelőbővítmény alkönyvtárat.

###### <a name="solution"></a>Megoldás
A bővítmény nincs telepítve. Határozza meg, hogy ez-e a proxy problémát (a korábban ismertetett). Szükség lehet, indítsa újra a gépet, vagy futtassa újra a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájl.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Egyes Azure teljesítményszámlálók hiányoznak.
Adatok beolvasása számos forrásból származó démon által gyűjtött teljesítménymutatók az Azure-ban. Bizonyos konfigurációs adatok gyűjtése helyileg, és az Azure Diagnostics olvasható néhány teljesítménymutatók. Tárolási számlálók a naplófájlok tárolási előfizetését az határozza meg.

Teljesebb és ismert problémák listáját, lásd: SAP Megjegyzés [1999351], amely tartalmaz további hibaelhárítási információért Azure figyelésének továbbfejlesztett SAP.

Ha hibaelhárítása SAP Megjegyzés használatával [1999351] nem oldja meg a problémát, futtassa újra a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájl a [konfigurálása az Azure fokozott Figyelőbővítmény az SAP][deployment-guide-4.5]. Lehetséges, hogy egy óráig várja meg, mert storage analytics vagy a diagnosztika számlálók előfordulhat, hogy nem jönnek létre, azonnal követően engedélyezve vannak. Ha a probléma továbbra is fennáll, a Windows BC-OP-NT-AZR összetevő vagy BC-OP-LNX-AZR Linux virtuális gép SAP felhasználói támogatási üzenet megnyitása.
