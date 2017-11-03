---
title: "Az Azure virtuális gépek magas rendelkezésre állású architektúra és SAP NetWeaver forgatókönyvek |} Microsoft Docs"
description: "Magas rendelkezésre ÁLLÁS architektúra és az Azure virtuális gépeken SAP NetWeaver forgatókönyvei"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 867fe2835418a48e4e616d8137ba9fa4182c8fb7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Magas rendelkezésre állású architektúra és SAP NetWeaver forgatókönyvei

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="definition-of-terminologies"></a>Terminológiát meghatározása

A kifejezés **magas rendelkezésre ÁLLÁS** technológiák, amely minimálisra csökkenti a IT szolgáltatások üzleti folytonosság keresztül IT szolgáltatások biztosításával kapcsolatos redundáns, hibatűrő, vagy a feladatátvételi védett a összetevőinek**azonos** adatközpont. Ebben az esetben egy Azure-régión belül.

**Vészhelyreállítás (DR)** is célzott IT szolgáltatások megszűnésének minimalizálja a és a helyreállítás azonban keresztben **különböző** adatközpontokban, amelyek található több száz kilométer számítógépnél. A mi esetünkben általában különböző Azure-régiókat geopolitikai régión belül, vagy Ön által létrehozott, az ügyfél között.


## <a name="overview-of-high-availability"></a>A magas rendelkezésre állás – Áttekintés
Azt is külön SAP magas rendelkezésre állás az Azure-ban három részre vonatkozó vitafórum:

* **Azure-infrastruktúra magas rendelkezésre állású**, például magas rendelkezésre ÁLLÁSÚ (VM) számítási, hálózati, tárolási stb és az SAP rendelkezésre állásának növelése előnyeit.

* **Azure-infrastruktúra virtuális gép újraindítása "magasabb rendelkezésre állás biztosítása érdekében" SAP-alkalmazások használata**

  Ha úgy dönt, hogy nem használja a Funkciók, például a Windows Server feladatátvételi fürtszolgáltatási (WSFC) vagy támasztja Linux, Azure virtuális gép újraindítása használata egy SAP rendszer tervezett és nem tervezett leállások a fizikai kiszolgálók Azure-infrastruktúra és a teljes elleni védelme érdekében az alapul szolgáló Azure platformon.


* **SAP alkalmazás magas rendelkezésre állás**

  Teljes SAP rendszer magas rendelkezésre állás biztosítása érdekében, az összes kritikus SAP rendszer összetevők, például védeni kell:
  * Redundáns **SAP alkalmazáskiszolgálók**, és
  * Egyedi összetevők (például **egyetlen pont, hiba (SPOF)**) például
    * **(A) SCS példány SAP** és
    *  **ADATBÁZIS-KEZELŐ**.


SAP a magas rendelkezésre állás, az Azure-ban van néhány különbség az SAP magas rendelkezésre állás a helyi fizikai vagy virtuális környezetben képest. A következő dokumentum [SAP NetWeaver magas rendelkezésre állás és a VMware és a Microsoft Windows Hyper-V virtuális környezetek üzletmenet-folytonosságának] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] szabványos SAP magas rendelkezésre állású ismerteti a konfigurációk Windows rendszeren virtualizált környezetben.

Nincs sapinst integrált SAP-magas rendelkezésre ÁLLÁSÚ konfiguráció Linux például Windows létezik. Vonatkozó SAP magas rendelkezésre ÁLLÁSÚ Linux helyszíni található további információ a [magas rendelkezésre állás partneradatok][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra magas rendelkezésre állás

### <a name="single-instance-virtual-machine-sla"></a>Egypéldányos virtuális gép SLA

Jelenleg egy prémium szintű Storage 99,9 %-os single-VM szolgáltatásiszint-szerződés. Ha meg szeretne ismerkedni hogyan nézhet ki például egy virtuális rendelkezésre állását, a különböző elérhető termék hozhat létre [Azure szolgáltatói szerződések][azure-sla].

A számítás alapja havonta vagy 43 200 perc 30 nap. Ezért 21,6 perc 0,05 % állásidő felel meg. A szokásos módon a különböző szolgáltatások rendelkezésre állásának szorozza meg a következő módon:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) \*...

Például:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 vagy egy 99.75 % rendelkezésre állását.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Több példány a virtuális gép rendelkezésre állási csoportban
Az összes virtuális gépeken, amelyek legalább két példányt az azonos **rendelkezésre állási csoport**, garantáljuk, hogy a virtuális gépek csatlakoztatásához legalább egy példány legalább 99,95 %-ában.

Ha két vagy több virtuális gépek rendelkezésre állási csoport része, a rendelkezésre állási csoport összes virtuális gépén hozzá van rendelve egy **frissítési tartomány** és egy **tartalék tartomány** az alapul szolgáló Azure platformon.

**Tartományok fault** garantálja, hogy a virtuális gépek telepítve vannak, amelyek nem ugyanazt a közös forrás- és hálózati kikapcsolás másik hardveren. Ha a kiszolgálók, a hálózati kapcsoló vagy áramforrásról, csak egy virtuális gép nem tervezett leállás hatással.

**Tartományok frissítése** biztosítja, hogy a különböző virtuális gépek nem során az Azure-infrastruktúra tervezett karbantartása egy időben újraindítása után, de egyszerre csak egy virtuális gép újraindítása után.

További információkért lásd: [a Windows Azure virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

A magas rendelkezésre állás elérése érdekében a rendelkezésre állási csoport szolgál:

* Redundáns SAP alkalmazáskiszolgálók  

* Csomópontokkal rendelkező fürtök két vagy több (pl. a virtuális gépek) SPOFs védelmét, például SAP (A) SCS-példány és az adatbázis-kezelő

### <a name="virtual-machine-vm-planned-and-unplanned-maintenance"></a>A virtuális gép (VM) tervezett és nem tervezett karbantartás

Az Azure platform események, amelyek hatással lehetnek a virtuális gépek rendelkezésre állásának két típusa van: a tervezett karbantartást és a nem tervezett karbantartás.

* **A tervezett karbantartások** események a Microsoft által készített az alapul szolgáló Azure platform átfogó megbízhatóságát, teljesítményét és a platform infrastruktúra, amely a virtuális gépek futnak a biztonsági javítása érdekében rendszeres frissítések érhetők el.

* **Nem tervezett karbantartás** események fordulhat elő, ha a hardver- vagy a virtuális gép alapul szolgáló fizikai infrastruktúra valamilyen módon hibát jelzett. Ez lehet helyi hálózati hiba, a helyi lemezek meghibásodása, vagy egyéb állványszintű meghibásodások. Ilyen hiba lép fel, ha az Azure platformon éppen automatikusan áttelepíti a virtuális gép a virtuális gépet egy megfelelő fizikai kiszolgálóhoz szolgáltató a nem megfelelő fizikai kiszolgálóról. Ilyen esetek ritkán lépnek fel, de ezek is okozhatják a virtuális gép újraindítását.

További információkért lásd: [a Windows Azure virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Az Azure Storage redundancia
A Microsoft Azure Storage-fiók adatainak mindig replikálódik a tartósság és magas rendelkezésre állás, az Azure Storage SLA még átmeneti hardverhibák esetén állásuk értekezlet biztosításához.

Mivel az Azure Storage alapértelmezés szerint megakadályozza az adatok három képek, RAID5 vagy több Azure lemezre RAID1 nem szükségesek.

További információkért lásd: [Azure Storage replikációs][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Felügyelt lemezek olyan új erőforrást az Azure Resource Manager az Azure Storage-fiókok tárolt VHD-k helyett használható. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva, és ezért a a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állásának javítása.
További információkért lásd: [Azure felügyelt lemezekhez – áttekintés][azure-storage-managed-disks-overview].

Azt javasoljuk, hogy felügyelt lemezt használja, mivel azok egyszerűsítése érdekében a központi telepítés és a virtuális gépek felügyeletét.
**SAP jelenleg csak prémium felügyelt lemezeit támogatja**. További információkért olvassa el az SAP Megjegyzés [1928533].

## <a name="utilizing-azure-infrastructure-ha-to-achieve-sap-application-higher-availability"></a>Magas rendelkezésre ÁLLÁSÚ SAP "Felső" rendelkezésre állásának eléréséhez Azure-infrastruktúra használatával

Ha úgy dönt, hogy nem használja a Funkciók, például a Windows Server feladatátvételi fürtszolgáltatási (WSFC) vagy támasztja Linux (jelenleg csak a SLES 12 rendszert és az annál magasabb támogatott), az SAP rendszer az Azure-beli tervezett és nem tervezett leállások elleni védelméhez első Azure virtuális gép újraindítása fizikai kiszolgálói infrastruktúra és a teljes alapul szolgáló Azure platformon.

Ez a megközelítés több leírása a következő dokumentum [használata Azure infrastruktúra VM újraindítás Achieve "Magas rendelkezésre állás érdekében" SAP rendszer][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>SAP alkalmazás magas rendelkezésre állásra Azure IaaS

Teljes SAP rendszer magas rendelkezésre állás biztosítása érdekében, az összes kritikus SAP rendszer összetevők, például védeni kell:

* Redundáns **SAP alkalmazáskiszolgálók**, és

* Egyedi összetevők (például **egyetlen pont, hiba (SPOF)**) például
  * **(A) SCS példány SAP** és
  *  **ADATBÁZIS-KEZELŐ**.

Tárgyaljuk részletesen az alábbiakban a magas rendelkezésre állás három kritikus SAP rendszer összetevők hogyan kell.

### <a name="high-availability-for-sap-application-servers"></a>Az SAP alkalmazáskiszolgálók magas rendelkezésre állás

> Ez a fejezet esetén is alkalmazható:
>
> ![Windows][Logo_Windows] A Windows és ![Linux][Logo_Linux] Linux
>

Általában nem szükséges egy adott magas rendelkezésre állású megoldás az SAP-kiszolgáló és a párbeszédpanel-példányok. Redundancia által a magas rendelkezésre állás elérése érdekében, valamint a különböző példányát az Azure virtuális gépek több párbeszédpanel példánya konfigurálni. Két példányban Azure virtuális gépek telepítése legalább két SAP alkalmazáspéldányok kell rendelkeznie.

![1. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**1. ábra:** magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

Az azonos Azure rendelkezésre állási állomás SAP Application Server-példány beállítása minden virtuális gépnek kell elhelyezni. Az Azure rendelkezésre állási csoportok biztosítja, hogy:

* Az összes virtuális gép azonos részét képező **frissítési tartomány**. Frissítési tartományokhoz, például gondoskodik arról, hogy a virtuális gépek tervezett karbantartás leállások során egy időben nem frissíti.
Alapvető funkcióit, épít, különböző frissítési és tartalék tartományok belül egy Azure méretezési egység, amely már jelent fejezetben [frissítési tartományok][planning-guide-3.2.2].

* Az összes virtuális gép azonos részét képező **tartalék tartomány**. A tartalék tartomány például gondoskodik arról, hogy telepít virtuális gépeket, hogy nincs hibaérzékeny pontok kialakulását hatással van az összes virtuális gép rendelkezésre állását.

Nincs hiba, és a frissítési tartományok, amelyek segítségével Azure rendelkezésre állási csoport egy Azure méretezési egység belül korlátlan számú van. Ez azt jelenti, hogy a virtuális gépek száma üzembe egy rendelkezésre állási csoportban, előbb vagy később egynél több virtuális gép ugyanazon hiba vagy frissítéséhez tartományi fejeződik be.

Néhány SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépeken telepíti, és feltéve, hogy azt a kapott a következő öt frissítési tartományok, a következő kép jelenik meg a végén. Rendelkezésre állási csoportok hiba és a frissítési tartományok maximális száma a jövőben esetleg módosító:

![2. ábra: Magas rendelkezésre ÁLLÁSÚ SAP alkalmazáskiszolgáló Azure rendelkezésre állási csoportban][planning-guide-figure-3000]
_**2. ábra:** magas rendelkezésre ÁLLÁSÚ az SAP alkalmazáskiszolgálók Azure rendelkezésre állási csoportban_ további részleteket a jelen dokumentációban található: [virtuális gépek rendelkezésre állásának kezelése][virtual-machines-manage-availability].


Az Azure rendelkezésre állási készletek fejezetben bemutatott [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] Azure virtuális gépek tervezési és megvalósítási SAP NetWeaver dokumentum.


**Csak a nem felügyelt lemezt:** mivel az Azure storage-fiók egy potenciális hibaérzékeny pontok kialakulását, fontos, hogy legalább két az Azure storage-fiókot, legalább két virtuális gép szétosztva. Az ideális beállítás a lemezeket, az egyes virtuális gépek egy SAP párbeszédpanel példányát futtató volna helyezhető üzembe egy másik tárolási fiókot.

> [!IMPORTANT]
>
> Határozottan javasoljuk, hogy az Azure Managed lemez használata a SAP magas rendelkezésre ÁLLÁSÚ telepítés, mivel ezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép azok vannak csatolva, és ezért a virtuális gép rendelkezésre állásának javítása és a a virtuális gépen futó szolgáltatásokat.  
>


### <a name="high-availability-architecture-for-the-sap-ascs-instance"></a>Az SAP (A) SCS példány magas rendelkezésre állású architektúrája

### <a name="high-availability-architecture-for-the-sap-ascs-instance-on-windows"></a>Az SAP (A) SCS példányához a Windows a magas rendelkezésre állású architektúrája


> ![Windows][Logo_Windows] Windows
>

Használhat **Windows Server feladatátvételi fürtszolgáltatási** (**WSFC**) SAP (A) SCS példány védelmét biztosító megoldás. Megoldás két változata van:

* SAP (A) SCS példány használt fürtözését **fürtözött megosztott lemezeket**

   Ebben a dokumentumban található további információ a fürtözött megosztott lemezek magas rendelkezésre ÁLLÁSÚ-architektúra: [fürtszolgáltatás SAP (A) SCS példányához a Windows feladatátvevő fürt használatával megosztott fürtlemez][sap-high-availability-guide-wsfc-shared-disk].   

* SAP (A) SCS példány használt fürtözését **fájlmegosztás**

  Ebben a dokumentumban található további információ a magas rendelkezésre ÁLLÁSÚ architektúra fájlmegosztáshoz való: [fürtszolgáltatás SAP (A) SCS példányához a Windows feladatátvevő fürt használatával fájlmegosztás][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Az SAP (A) SCS példány Linux magas rendelkezésre állás


> ![Linux][Logo_Linux] Linux
>

További információ a fürtözött SAP (A) SCS példányhoz SUSE Linux Enterprise Server fürt keretrendszer használatával ebben a dokumentumban található: [magas rendelkezésre állás a SAP NetWeaver a SUSE Linux Enterprise Server Azure virtuális gépeken az SAP-alkalmazásokból][sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-clustered-sap-ascs-instance"></a>SAP NetWeaver Multi-SID-konfigurációt a fürtözött SAP (A) SCS példányra

> ![Windows][Logo_Windows] Windows
>
>Jelenleg Multi-SID csak a támogatott **Windows Server feladatátvételi fürt (WSFC)**. Multi-SID támogatott használatával **fájlmegosztás** és **megosztott lemez**.
>

Ezen architektúra-dokumentumokban található Multi-SID magas rendelkezésre ÁLLÁSÚ architektúra további információk:

* [SAP (A) SCS példány Multi-SID magas rendelkezésre állás a Windows Server Feladatátvételi fürtszolgáltatással és a fájlmegosztás][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP (A) SCS példány Multi-SID magas rendelkezésre állás a Windows Server Feladatátvételi fürtszolgáltatással és megosztott lemez][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Magas rendelkezésre állású DBMS példány

Az adatbázis-kezelő a rendszer egyetlen pont, forduljon egy SAP rendszerben. Azt a magas rendelkezésre állású megoldás használatával védeni kell. Következő ábrán látható egy SQL Server Always On magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatási és az Azure belső terheléselosztó. SQL Server Always On DBMS adatainak és naplókönyvtárainak fájlokat replikálja a saját adatbázis-kezelő replikáció használatával. Ebben az esetben a fürt megosztott lemezt, amely egyszerűbbé teszi a teljes telepítés nem szükséges.

![3. ábra: Példa egy magas rendelkezésre állású SAP DBMS, az SQL Server Always On][sap-ha-guide-figure-2003]

_**3. ábra:** egy magas rendelkezésre állású SAP DBMS, az SQL Server Always On – példa_

További információ a Fürtszolgáltatás **SQL Server adatbázis-kezelő** Azure az Azure Resource Manager telepítési modell segítségével, lásd: ezek a cikkek:

* [Always On rendelkezésre állási csoport konfigurálásához Azure virtuális gépek manuálisan erőforrás-kezelő használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Egy Azure belső terheléselosztót egy Always On rendelkezésre állási csoport konfigurálása az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

További információ a Fürtszolgáltatás **SAP HANA DBMS** Azure az Azure Resource Manager telepítési modell segítségével, lásd: Ez a cikk:

* [Magas rendelkezésre állású SAP HANA az Azure virtuális gépek (VM)][sap-hana-ha]
