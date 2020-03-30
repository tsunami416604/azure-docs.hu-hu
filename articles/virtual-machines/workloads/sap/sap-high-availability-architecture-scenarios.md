---
title: Azure Virtuális gépek HA architektúrája és forgatókönyvei az SAP NetWeaver számára | Microsoft dokumentumok
description: Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára az Azure virtuális gépeken
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08f770ced6cb1ec1102159788e1583d481436b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279909"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állású konfiguráció)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>Terminológia-meghatározások

**Magas rendelkezésre állás:** Olyan technológiák ra hivatkozik, amelyek minimálisra csökkentik az informatikai zavarokat azáltal, hogy az informatikai szolgáltatások üzletmenet-folytonosságát redundáns, hibatűrő vagy feladatátvételsel ellátott összetevőkön keresztül biztosítják *ugyanazon* adatközponton belül. A mi esetünkben az adatközpont egy Azure-régióban található.

**Katasztrófa utáni helyreállítás:** Az informatikai szolgáltatások megszakadásának és helyreállításának minimalizálására is utal, de *különböző* adatközpontokban, amelyek több száz mérföldre lehetnek egymástól. A mi esetünkben az adatközpontok előfordulhat, hogy különböző Azure-régiókban találhatók ugyanazon a geopolitikai régióban, vagy az Ön által ügyfélként meghatározott helyeken.


## <a name="overview-of-high-availability"></a>A magas rendelkezésre állás áttekintése
Az Azure-ban az SAP magas rendelkezésre állása három típusra bontható:

* **Az Azure-infrastruktúra magas rendelkezésre állása:** 

    Például a magas rendelkezésre állású számítási (VM), hálózati vagy tárolási és előnyeit az SAP-alkalmazások elérhetőségének növelése.

* **Az Azure-infrastruktúra virtuális gépének újraindítása az SAP-alkalmazások *nagyobb rendelkezésre állásának* elérése érdekében:** 

    Ha úgy dönt, hogy nem használ olyan funkciókat, mint a Windows Server feladatátvételi fürtözés (WSFC) vagy pacemaker Linuxon, az Azure vm újraindítása használatos. Megvédi az SAP-rendszereket az Azure fizikai kiszolgálói infrastruktúrájának tervezett és nem tervezett állásidejétől és az alapul szolgáló Azure-platformtól.

* **SAP alkalmazás magas rendelkezésre állású:** 

    A teljes SAP-rendszer magas rendelkezésre állásának eléréséhez meg kell védenie az összes kritikus SAP-rendszerösszetevőt. Példa:
    * Redundáns SAP-alkalmazáskiszolgálók.
    * Egyedi összetevők. Egy példa lehet egy hibapont (SPOF) összetevő, például egy SAP ASCS/SCS-példány vagy egy adatbázis-kezelő rendszer (DBMS).

Az Sap magas rendelkezésre állása az Azure-ban eltér az SAP magas rendelkezésre állása egy helyszíni fizikai vagy virtuális környezetben. A következő papír [SAP NetWeaver magas rendelkezésre állású és üzletmenet-folytonosság virtuális környezetekben a VMware és a Hyper-V Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] ismerteti szabványos SAP magas rendelkezésre állású konfigurációk virtualizált környezetben a Windows rendszeren.

Nincs sapinst integrált SAP magas rendelkezésre állású konfiguráció Linux, mint van a Windows. Az SAP magas rendelkezésre állású Linux-szal kapcsolatos információkért olvassa el a [Magas rendelkezésre állású partneradatok című témakört.][sap-ha-partner-information]

## <a name="azure-infrastructure-high-availability"></a>Az Azure infrastruktúra magas rendelkezésre állása

### <a name="sla-for-single-instance-virtual-machines"></a>Egypéldányos virtuális gépek sla-ja

Jelenleg egy egyvm SLA 99,9%-os prémium szintű tárterülettel rendelkezik. Ha képet szeretne kapni arról, hogy mi lehet egy virtuális gép rendelkezésre állása, létrehozhatja a különböző elérhető [Azure szolgáltatásiszint-szerződések][azure-sla]termékét.

A számítás alapja havi 30 nap, azaz 43 200 perc. A 0,05%-os állásidő például 21,6 percnek felel meg. A különböző szolgáltatások elérhetőségét szokás szerint a következőképpen számítják ki:

(Elérhetőségi szolgáltatás #1/100) * (elérhetőségi szolgáltatás #2/100) * \*(elérhetőségi szolgáltatás #3/100) ...

Példa:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 vagy 99,75%-os teljes rendelkezésre állás.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Több példánynyi virtuális gép ugyanabban a rendelkezésre állási csoportban
Minden olyan virtuális gép esetében, amelynek két vagy több példánya ugyanabban a *rendelkezésre állási csoportban*van telepítve, garantáljuk, hogy az idő legalább 99,95%-ában legalább egy példányhoz virtuálisgép-kapcsolattal fog rendelkezni.

Ha két vagy több virtuális gép ugyanannak a rendelkezésre állási csoportnak a része, a rendelkezésre állási csoport minden virtuális géphez hozzá van rendelve egy *frissítési tartomány* és egy *tartalék tartomány* az alapul szolgáló Azure-platform.

* **A frissítési tartományok** garantálják, hogy az Azure-infrastruktúra tervezett karbantartása során nem újraindul nak egyszerre több virtuális gép. Egyszerre csak egy virtuális gép újraindul.

* **A tartalék tartományok** garantálják, hogy a virtuális gépek olyan hardverösszetevőkön vannak telepítve, amelyek nem osztoznak közös áramforrással és hálózati kapcsolóval. Ha kiszolgálók, hálózati kapcsoló vagy áramforrás nem tervezett állásidő, csak egy virtuális gép érintett.

További információt [a Windows virtuális gépek azure-beli elérhetőségének kezelése című témakörben talál.][azure-virtual-machines-manage-availability]

A rendelkezésre állási csoport a következők magas rendelkezésre állásának elérésére szolgál:

* Redundáns SAP-alkalmazáskiszolgálók.  
* Fürtök két vagy több csomópont (például virtuális gépek), amelyek védik spof-ok, például egy SAP ASCS/SCS-példány vagy egy DBMS.


### <a name="azure-availability-zones"></a>Azure Availability Zones
Az Azure jelenleg az [Azure rendelkezésre állási zónáinak fogalmait](https://docs.microsoft.com/azure/availability-zones/az-overview) vezeti be a különböző [Azure-régiókban.](https://azure.microsoft.com/global-infrastructure/regions/) Az Azure-régiókban, ahol rendelkezésre állási zónák kínálnak, az Azure-régiók több adatközpontok, amelyek függetlenek a tápegység, hűtés és a hálózat. Oka kínál különböző zónák egyetlen Azure-régióban, hogy lehetővé tegye az alkalmazások üzembe helyezését két vagy három rendelkezésre állási zónák kínált. Feltételezve, hogy az energiaforrások és/vagy a hálózat problémái csak egy rendelkezésre állási zóna infrastruktúráját érintik, az alkalmazás üzembe helyezése egy Azure-régióban továbbra is teljes mértékben működőképes. Végül néhány csökkentett kapacitású, mivel néhány virtuális gép egy zónában elveszhet. A másik két zónában lévő virtuális gépek azonban még mindig működnek. A zónákat kínáló Azure-régiók az [Azure rendelkezésre állási zónáiban](https://docs.microsoft.com/azure/availability-zones/az-overview)vannak felsorolva.

A rendelkezésre állási zónák használatával néhány dolgot figyelembe kell venni. A szempontok listája, mint például:

- Az Azure rendelkezésre állási csoportjait nem telepítheti egy rendelkezésre állási zónán belül. Ki kell választania egy rendelkezésre állási zónát vagy egy rendelkezésre állási készletet a virtuális gép központi telepítési kereteként.
- Az [alapszintű terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nem használható a Windows feladatátvevő fürtszolgáltatásokon vagy Linux-pacemakeren alapuló feladatátvevő fürtmegoldások létrehozásához. Ehelyett az Azure [Standard Load Balancer Termékváltozatot](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) kell használnia
- Az Azure rendelkezésre állási zónái nem garantálnak bizonyos távolságot a régión belüli különböző zónák között
- A különböző Azure-régiókon belüli különböző Azure-rendelkezésre állási zónák közötti hálózati késés eltérhet az Azure régiótól a régióig. Lesznek olyan esetek, ahol az ügyfél ésszerűen futtathatja az SAP alkalmazásréteget különböző zónák között üzembe helyezve, mivel a hálózati késés az egyik zónából az aktív DBMS virtuális gépbe továbbra is elfogadható az üzleti folyamat hatásából. Mivel lesznek olyan ügyfélforgatókönyvek, ahol az egyik zónában lévő aktív DBMS virtuális gép és egy SAP-alkalmazáspéldány egy másik zónában lévő késés túl tolakodó lehet, és nem elfogadható az SAP üzleti folyamatok számára. Ennek eredményeképpen a központi telepítési architektúrák nak különbözniük kell az alkalmazás aktív/aktív architektúrájával, vagy aktív/passzív architektúrával, ha a késés túl magas.
- [Az Azure által felügyelt lemezek](https://azure.microsoft.com/services/managed-disks/) használata kötelező az Azure rendelkezésre állási zónáiba való üzembe helyezéshez 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Virtuális gépek tervezett és nem tervezett karbantartása

Az Azure platformesemények két típusa befolyásolhatja a virtuális gépek rendelkezésre állását:

* **A tervezett karbantartási** események a Microsoft által az alapul szolgáló Azure platformra készített rendszeres frissítések. A frissítések javítják a virtuális gépek által futtatott platforminfrastruktúra általános megbízhatóságát, teljesítményét és biztonságát.

* **Nem tervezett karbantartási** események akkor fordulnak elő, ha a virtuális gép alapjául szolgáló hardver vagy fizikai infrastruktúra valamilyen módon meghibásodott. Ez magában foglalhatja a helyi hálózati hibákat, a helyi lemezhibákat vagy más rackszintű hibákat. Ilyen hiba észlelésekor az Azure platform automatikusan áttelepíti a virtuális gépet a nem megfelelő állapotú fizikai kiszolgálóról, amely a virtuális gépet üzemelteti egy kifogástalan állapotú fizikai kiszolgálóra. Az ilyen események ritkák, de a virtuális gép újraindítását is okozhatják.

További információt [a Windows virtuális gépek azure-beli elérhetőségének kezelése című témakörben talál.][azure-virtual-machines-manage-availability]

### <a name="azure-storage-redundancy"></a>Az Azure Storage redundanciája
A tárfiókban lévő adatok mindig replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében, és az Azure Storage SLA-nak még átmeneti hardverhibák esetén is megfelelnek.

Mivel az Azure Storage alapértelmezés szerint három lemezképet tárol az adatokról, a RAID 5 vagy a RAID 1 használata több Azure-lemezen szükségtelen.

További információ: [Azure Storage replication][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
A felügyelt lemezek az Azure Resource Manager egy erőforrástípusa, amely et az Azure storage-fiókokban tárolt virtuális merevlemezek (VHD-k) helyett ajánlott használni. A felügyelt lemezek automatikusan igazodnak a virtuális gép Azure-rendelkezésre állási készletéhez, amelyhez kapcsolódnak. Ezek növelik a virtuális gép és a rajta futó szolgáltatások rendelkezésre állását.

További információt az [Azure felügyelt lemezek – áttekintés című témakörben talál.][azure-storage-managed-disks-overview]

Azt javasoljuk, hogy felügyelt lemezeket használjon, mert azok leegyszerűsítik a virtuális gépek üzembe helyezését és felügyeletét.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Az Azure-infrastruktúra magas rendelkezésre állásának kihasználása az SAP-alkalmazások *nagyobb rendelkezésre állásának* elérése érdekében

Ha úgy dönt, hogy nem használja a funkciókat, például a WSFC vagy a Pacemaker Linux (jelenleg csak a SUSE Linux Enterprise Server [SLES] 12 és újabb), az Azure vm újraindítása használatos. Megvédi az SAP-rendszereket az Azure fizikai kiszolgálói infrastruktúrájának tervezett és nem tervezett állásidejétől és az alapul szolgáló Azure-platformtól.

Erről a megközelítésről további információt az [Azure-infrastruktúra virtuális gép újraindításának kihasználása az SAP-rendszer magasabb rendelkezésre állásának eléréséhez című][sap-higher-availability]témakörben talál.

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Az SAP-alkalmazások magas rendelkezésre állása az Azure IaaS-on

A teljes SAP-rendszer magas rendelkezésre állásának eléréséhez meg kell védenie az összes kritikus SAP-rendszerösszetevőt. Példa:
  * Redundáns SAP-alkalmazáskiszolgálók.
  * Egyedi összetevők. Egy példa lehet egy hibapont (SPOF) összetevő, például egy SAP ASCS/SCS-példány vagy egy adatbázis-kezelő rendszer (DBMS).

A következő szakaszok ismertetik, hogyan érheti el a magas rendelkezésre állást mindhárom kritikus SAP-rendszerösszetevő esetében.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Sap-alkalmazáskiszolgálók magas rendelkezésre állású architektúrája

> Ez a szakasz a következőkre vonatkozik:
>
> ![Windows][Logo_Windows] Windows és ![Linux][Logo_Linux] Linux
>

Általában nincs szükség egy adott magas rendelkezésre állású megoldásra az SAP-alkalmazáskiszolgálóhoz és a párbeszédpéldányokhoz. Redundanciával magas rendelkezésre állást érhet el, és több párbeszédpéldányt konfigurál hat az Azure virtuális gépek különböző példányaiban. Legalább két SAP-alkalmazáspéldánynak kell telepítve az Azure virtuális gépek két példányában.

![1. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**1. ábra:** Magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

Az SAP-alkalmazáskiszolgáló-példányokat tároló összes virtuális gépet ugyanabban az Azure rendelkezésre állási csoportban kell elhelyeznie. Az Azure rendelkezésre állási készlete biztosítja a következőket:

* Minden virtuális gép ugyanannak a frissítési tartománynak a része.  
    A frissítési tartomány biztosítja, hogy a virtuális gépek nem frissülnek egyszerre a tervezett karbantartási állásidő alatt.

    Az alapvető funkciók, amelyek egy Azure-skálán egy egységen belül különböző frissítési és tartalék tartományokra épülnek, már be vezették a [frissítési tartományok][planning-guide-3.2.2] szakaszban.

* Minden virtuális gép ugyanannak a tartalék tartománynak a része.  
    A tartalék tartomány biztosítja, hogy a virtuális gépek telepítése, hogy egyetlen meghibásodási pont nem befolyásolja az összes virtuális gép rendelkezésre állását.

Az Azure-skálán egy egységen belül egy Azure-szintű egységen belül használható frissítési és tartalék tartományok száma véges. Ha továbbra is virtuális gépeket ad hozzá egyetlen rendelkezésre állási csoporthoz, két vagy több virtuális gép végül ugyanabban a hibavagy frissítési tartományban végzi.

Ha néhány SAP-alkalmazáskiszolgáló-példányt telepít a dedikált virtuális gépeken, feltételezve, hogy öt frissítési tartománnyal rendelkezünk, az alábbi kép jelenik meg. A rendelkezésre állási csoporton belüli frissítési és tartalék tartományok tényleges maximális száma a jövőben változhat:

![2. ábra: Az SAP-alkalmazáskiszolgálók][planning-guide-figure-3000]
magas rendelkezésre állása az Azure rendelkezésre állási készletében_**2.**_

További információt [a Windows virtuális gépek azure-beli elérhetőségének kezelése című témakörben talál.][azure-virtual-machines-manage-availability]

További információkért tekintse meg az [Azure rendelkezésre állási készletek][planning-guide-3.2.3] szakasza az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver dokumentum.

**Csak nem felügyelt lemezek:** Mivel az Azure storage-fiók egy potenciális hibapont, fontos, hogy legalább két Azure storage-fiók, amelyben legalább két virtuális gép van elosztva. Ideális beállítás esetén az SAP-párbeszédpanelt futtató virtuális gépek lemezei egy másik tárfiókban lesznek telepítve.

> [!IMPORTANT]
> Azt javasoljuk, hogy az Azure felügyelt lemezek et az SAP magas rendelkezésre állású telepítések használata. Mivel a felügyelt lemezek automatikusan igazodnak a virtuális gép rendelkezésre állási készletéhez, amelyekhez kapcsolódnak, növelik a virtuális gép és a rajta futó szolgáltatások rendelkezésre állását.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Sap ASCS/SCS-példány magas rendelkezésre állású architektúrája Windows rendszeren

> ![Windows][Logo_Windows] Windows
>

Az SAP ASCS/SCS-példány védelméhez wsfc-megoldást is használhat. A megoldásnak két változata van:

* **Fürtözött SAP ASCS/SCS-példány fürtözött megosztott lemezek használatával:** Erről az architektúráról további információt a [Fürt sap ASCS/SCS-példányának fürtözése windowsos feladatátvevő fürtön fürt megosztott lemez használatával talál.][sap-high-availability-guide-wsfc-shared-disk]   

* **Az SAP ASCS/SCS-példány fürtje fájlmegosztás használatával:** Az architektúráról további információt a [Windows feladatátvevő fürtén SAP ASCS/SCS-példány fürtözése fájlmegosztás sal][sap-high-availability-guide-wsfc-file-share]című témakörben talál.

* **Az SAP ASCS/SCS-példány fürtje az ANF SMB-megosztás használatával**: Az architektúráról további információt a [Fürtfürt SAP ASCS/SCS-példánya a Windows feladatátvételi fürtén az ANF SMB fájlmegosztás használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)című témakörben talál.

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Magas rendelkezésre állású architektúra egy SAP ASCS/SCS-példányhoz Linuxon

> ![Linux][Logo_Linux] Linux
> 
> Az SAP ASCS/SCS-példány SLES-fürtkeretrendszer használatával történő fürtözéséről további információt az [SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken az Sap-alkalmazásokhoz készült SUSE Linux Enterprise Server szolgáltatásban][sap-suse-ascs-ha]című témakörben talál. Az SLES alternatív HA architektúráját, amely nem igényel magas rendelkezésre [állású NFS-t, lásd: Magas rendelkezésre állási útmutató az SAP NetWeaver számára a SUSE Linux Enterprise Server en az Azure NetApp-fájlokkal az SAP-alkalmazásokhoz.][sap-suse-ascs-ha-anf]

Az SAP ASCS/SCS-példány Red Hat fürtkeretrendszer használatával történő fürtözéséről további információt az [Azure Virtual Machines sap NetWeaver magas rendelkezésre állása a Red Hat Enterprise Linux rendszeren című](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) témakörben talál.


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver többSID-konfiguráció fürtözött SAP ASCS/SCS-példányhoz

> ![Windows][Logo_Windows] Windows
> 
> A WSFC támogatja a többszörös biztonsági azonosítót a fájlmegosztás és a megosztott lemez használatával.
> 
> A Windows több SID-szintű magas rendelkezésre állású architektúrájáról a következő témakörben talál további információt:

* [Az SAP ASCS/SCS példány többSID-példány magas rendelkezésre állása windows Server feladatátvevő fürtözéshez és fájlmegosztáshoz][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Az SAP ASCS/SCS példány többSID-példány magas rendelkezésre állása windows Server feladatátvevő fürtözéshez és megosztott lemezhez][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux][Logo_Linux] Linux
> 
> A több SID-fürtözés az SAP ASCS/ERS Linux Pacemaker-fürtjein támogatott, amelyek ugyanazon a fürtön **legfeljebb öt** SAP-SID-re korlátozódnak.
> A Linuxon elérhető több SID magas anamnézisről a következő témakörben talál további információt:

* [HA SAP NW az Azure virtuális gépek SLES SAP-alkalmazások több SID útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [HA SAP NW az Azure-beli virtuális gépek RHEL SAP-alkalmazások több SID útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="high-availability-dbms-instance"></a>Magas rendelkezésre állású DBMS-példány

A DBMS is egy kapcsolattartó pont egy SAP-rendszerben. Meg kell védeni egy magas rendelkezésre állású megoldás használatával. Az alábbi ábrán egy SQL Server AlwaysOn magas rendelkezésre állású megoldás látható az Azure-ban, a Windows Server feladatátvételi fürtözésével és az Azure belső terheléselosztójával. Az SQL Server AlwaysOn saját DBMS-replikációval replikálja az ADATBÁZIS-adatokat és a naplófájlokat. Ebben az esetben nincs szükség fürtmegosztott lemezre, ami leegyszerűsíti a teljes telepítést.

![3. ábra: Példa egy magas rendelkezésre állású SAP DBMS-re, az SQL Server AlwaysOn kiszolgálóval][sap-ha-guide-figure-2003]

_**3. ábra:** Példa egy magas rendelkezésre állású SAP DBMS-re, az SQL Server AlwaysOn kiszolgálóval_

Az SQL Server ADATBÁZIS-kezelő azure-beli fürtözéséről az Azure Resource Manager telepítési modelljének használatával az alábbi cikkekben talál további információt:

* [AlwaysOn rendelkezésre állási csoport konfigurálása az Azure virtuális gépein manuálisan az Erőforrás-kezelő használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure belső terheléselosztó konfigurálása egy AlwaysOn rendelkezésre állási csoporthoz az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

Az SAP HANA DBMS azure-beli fürtözéséről az Azure Resource Manager üzembe helyezési modelljének használatával további információt az [SAP HANA Azure virtuális gépeken (VM-ek) című][sap-hana-ha]témakörben talál.
