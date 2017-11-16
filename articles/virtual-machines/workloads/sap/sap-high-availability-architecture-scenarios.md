---
title: "Azure virtuális gépek magas rendelkezésre állású architektúra és SAP NetWeaver forgatókönyvek |} Microsoft Docs"
description: "Magas rendelkezésre állású architektúra és SAP NetWeaver Azure virtuális gépeken forgatókönyvei"
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
ms.openlocfilehash: 31f3765d807882e65a247819a5999c191f9e7ac5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
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


## <a name="terminology-definitions"></a>Terminológiai definíciók

**Magas rendelkezésre állású**: minimálisra csökkentik az informatikai szolgáltatások keresztül redundáns, hibatűrő vagy feladatátvételi védett összetevőinek IT szolgáltatások üzleti folytonosság megadásával technológiák hivatkozik a *azonos*adatközpont. Ebben az esetben az adatközpontban található egy Azure-régiót.

**Vész-helyreállítási**: is hivatkozik a minimalizálja a IT szolgáltatások megszakítása és a helyreállítási, de keresztben *különböző* adatközpontokban, amelyek több száz többet egy másik miles lehetnek. Ebben az esetben az adatközpontok vagy ügyfélként az Ön által létrehozott helyen lévő különböző Azure-régiók geopolitikai régión belül található.


## <a name="overview-of-high-availability"></a>A magas rendelkezésre állás – Áttekintés
SAP a magas rendelkezésre állás, az Azure-ban háromféle sorolhatók:

* **Azure-infrastruktúra magas rendelkezésre állású**: 

    Például a magas rendelkezésre állású (VM) számítási, hálózati, vagy a tároló- és az SAP-alkalmazásokból rendelkezésre állásának növelése előnye tartalmazhatnak.

* **Indítsa újra a virtuális gép Azure-infrastruktúra használatával eléréséhez *magas rendelkezésre állás érdekében* az SAP-alkalmazásokból**: 

    Ha úgy dönt, hogy nem használja a Funkciók, például a Windows Server feladatátvételi fürtszolgáltatási (WSFC) vagy támasztja Linux, Azure virtuális gép újraindítása van szükség. Általa védett SAP rendszerek elleni tervezett és nem tervezett leállások a fizikai kiszolgálók Azure-infrastruktúra és a teljes alapul szolgáló Azure platformon.

* **SAP alkalmazás magas rendelkezésre állású**: 

    Teljes SAP rendszer magas rendelkezésre állás biztosítása érdekében, az összes kritikus SAP rendszerösszetevők kell védeni. Példa:
    * Redundáns SAP alkalmazáskiszolgálók.
    * Egyedi összetevők. Például lehet egyetlen pont, a hiba (SPOF) összetevő, például egy SAP ASC/SCS példányt vagy egy adatbázis-kezelő rendszer (DBMS).

SAP a magas rendelkezésre állás, az Azure-ban eltér az SAP a magas rendelkezésre állás, a helyi fizikai vagy virtuális környezetben. A következő dokumentum [SAP NetWeaver magas rendelkezésre állás és a VMware és a Microsoft Windows Hyper-V virtuális környezetek üzletmenet-folytonosságának] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] szabványos SAP magas rendelkezésre állású ismerteti a konfigurációk Windows rendszeren virtualizált környezetben.

Nincs sapinst integrált SAP magas rendelkezésre állású konfiguráció Linux rendszerhez készült Windows van. Linux SAP magas rendelkezésre állású helyszíni kapcsolatos információkért lásd: [magas rendelkezésre állású partneradatok][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra magas rendelkezésre állás

### <a name="sla-for-single-instance-virtual-machines"></a>SLA-t egypéldányos virtuális gépekhez

Jelenleg egy prémium szintű Storage 99,9 %-os single-VM szolgáltatásiszint-szerződés. Ha meg szeretne ismerkedni kapcsolatos milyen lehet az egyetlen virtuális gép rendelkezésre állását, a különféle érhető el a termék hozhat létre [Azure szolgáltatói szerződések][azure-sla].

A számítás alapja havonta vagy 43 200 perc 30 nap. Például egy 0,05 % állásidő megfelel 21,6 perc. A szokásos módon a különböző szolgáltatások rendelkezésre állásának kiszámítása a következőképpen:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) \*...

Példa:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 vagy egy 99.75 % rendelkezésre állását.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Az azonos rendelkezésre állási csoport a virtuális gépek több példánya
Legalább két példányt az azonos rendelkező összes virtuális gépekhez *rendelkezésre állási csoport*, de tudjuk garantálni, úgy kell legalább egy példány a virtuális gépek csatlakoztatásához legalább 99,95 %-ában.

Ha két vagy több virtuális gépet az azonos rendelkezésre állási készlet része, a rendelkezésre állási csoport összes virtuális gépén van hozzárendelve egy *frissítési tartomány* és egy *tartalék tartomány* az alapul szolgáló Azure platformon.

* **Tartományok frissítése** garantálja, hogy több virtuális gép nem újraindítása van az Azure-infrastruktúra a tervezett karbantartás alatt egy időben. Egyszerre csak egy virtuális gép újraindítása után.

* **Tartományok fault** garantálja, hogy a virtuális gépek telepítve vannak a hardverösszetevők közül nem ugyanazt a közös power forrás- és a hálózati kapcsoló. Amikor kiszolgálók, a hálózati kapcsoló vagy egy áramforrásról változni egy nem tervezett leállás, csak egy virtuális gép van hatással.

További információkért lásd: [a Windows Azure virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

Egy rendelkezésre állási csoportot a magas rendelkezésre állás elérése érdekében szolgál:

* Redundáns SAP alkalmazáskiszolgálók.  
* Csomópontokkal rendelkező fürtök két vagy több (például VM) például SAP ASC/SCS példánya vagy egy adatbázis-kezelő SPOFs védelmét.

### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>A virtuális gépek tervezett és nem tervezett karbantartás

Kétféle esemény Azure platformon hatással lehet a virtuális gépek rendelkezésre állását:

* **A tervezett karbantartások** események az alapul szolgáló Azure platformra a Microsoft által készített rendszeres frissítések érhetők el. A frissítések javítása átfogó megbízhatóságát, teljesítményét és, amelyek a virtuális gépek futnak a platform-infrastruktúra biztonságát.

* **Nem tervezett karbantartás** események fordulhat elő, ha a hardver- vagy a virtuális gép alapul szolgáló fizikai infrastruktúra valamilyen módon nem sikerült. Helyi hálózati hibák, helyi lemezhiba vagy más állvány szintű hibák tartalmazhat. Ilyen hiba lép fel, az Azure platformon automatikusan áttelepíti a virtuális gépről a nem megfelelő fizikai kiszolgálóra, amelyen a virtuális gépet egy megfelelő fizikai kiszolgálóhoz. Ezek az események ritkán fordul elő, de azok a virtuális gép újraindítására is okozhat.

További információkért lásd: [a Windows Azure virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Az Azure Storage-redundancia
A tárfiókban lévő adatokat mindig replikálódik a tartósság és magas rendelkezésre állás, az Azure Storage SLA még átmeneti hardverhibák esetén állásuk értekezlet biztosításához.

Mivel az Azure Storage tartja az adatok három képek alapértelmezés szerint több Azure lemezre RAID 5 vagy RAID-1 használata nem szükséges.

További információkért lásd: [Azure Storage replikációs][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Felügyelt lemezek olyan új erőforrást az Azure Resource Manager használható helyett virtuális merevlemezeket (VHD), az Azure storage-fiókok vannak tárolva. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva. Azok a virtuális gép és a rajta futó szolgáltatások rendelkezésre állásának javítása.

További információkért lásd: [Azure felügyelt lemezekhez – áttekintés][azure-storage-managed-disks-overview].

Azt javasoljuk, hogy felügyelt lemezek használja, mert egyszerűsítése érdekében a központi telepítés és a virtuális gépek felügyeletét.

SAP jelenleg csak a felügyelt premium lemezek. További információkért lásd: SAP Megjegyzés [1928533].

## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Azure-infrastruktúra magas rendelkezésre állás elérése érdekében okhoz *magas rendelkezésre állás érdekében* az SAP-alkalmazásokból

Ha úgy dönt, hogy nem használja a Funkciók, például a WSFC vagy támasztja Linux (jelenleg támogatott csak SUSE Linux Enterprise Server [SLES] 12 és újabb), Azure virtuális gép újraindítása van szükség. Általa védett SAP rendszerek elleni tervezett és nem tervezett leállások a fizikai kiszolgálók Azure-infrastruktúra és a teljes alapul szolgáló Azure platformon.

Ezzel a módszerrel kapcsolatos további információkért lásd: [használata Azure infrastruktúra virtuális gép újraindítása az SAP rendszer magasabb rendelkezésre állás biztosítása érdekében][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Az Azure infrastruktúra-szolgáltatási SAP alkalmazások magas rendelkezésre állás

Teljes SAP rendszer magas rendelkezésre állás biztosítása érdekében, az összes kritikus SAP rendszerösszetevők kell védeni. Példa:
  * Redundáns SAP alkalmazáskiszolgálók.
  * Egyedi összetevők. Például lehet egyetlen pont, a hiba (SPOF) összetevő, például egy SAP ASC/SCS példányt vagy egy adatbázis-kezelő rendszer (DBMS).

A következő szakaszok ismertetik az összes három kritikus SAP rendszer összetevőinek magas rendelkezésre állás biztosítása érdekében.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Az SAP alkalmazáskiszolgálók magas rendelkezésre állású architektúrája

> Ez a szakasz a következőkre vonatkozik:
>
> ![Windows][Logo_Windows] A Windows és ![Linux][Logo_Linux] Linux
>

Általában nem szükséges egy adott magas rendelkezésre állású megoldás az SAP-alkalmazás kiszolgáló és a párbeszédpanel példányok. Magas rendelkezésre állású redundancia által érhetők el, és több párbeszédpanel példányt konfigurálnia az Azure virtuális gépek különböző példányai. Az Azure virtuális gépek két példánya telepítve legalább két SAP alkalmazáspéldányok kell rendelkeznie.

![1. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**1. ábra:** magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

Az összes virtuális gép gazdagép SAP alkalmazáskiszolgáló-példányok az azonos Azure rendelkezésre állási beállított kell elhelyezni. Az Azure rendelkezésre állási csoportok biztosítja, hogy:

* Az összes virtuális gép ugyanazon frissítési tartomány részét képezik.  
    Egy frissítési tartományt biztosítja, hogy a virtuális gépek tervezett karbantartás leállások során egy időben nem frissíti.

    Az alapvető funkciókat, épít különböző frissítési és a tartalék tartományok belül egy Azure méretezési egység, amely már jelent a a [tartományok frissítése] [ planning-guide-3.2.2] szakasz.

* Összes virtuális gépet az azonos tartalék tartományban részét képezik.  
    A tartalék tartomány biztosítja, hogy telepít virtuális gépeket, hogy nincs hibaérzékeny pontok kialakulását hatással van az összes virtuális gép rendelkezésre állását.

A frissítés és a tartalék tartományok, amelyek segítségével Azure rendelkezésre állási készlet belül egy Azure méretezési egység száma nem véges. Ha virtuális gépeket tartsa hozzáadása egy egyetlen rendelkezésre állási csoport, két vagy több virtuális gépek végül kat, ugyanabban a tartományban hiba vagy a frissítés.

Ha néhány SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépeken, feltéve, hogy rendelkezik-e öt frissítési tartományok, a következő kép jelenik meg. A rendelkezésre állási csoportok frissítés és a tartalék tartományok tényleges maximális száma a jövőben esetleg módosító:

![2. ábra: A magas rendelkezésre állású, SAP alkalmazáskiszolgáló Azure rendelkezésre állási csoportba][planning-guide-figure-3000]
_**2. ábra:** magas rendelkezésre állású Azure rendelkezésre állási csoportba SAP-alkalmazáskiszolgáló_

További információkért lásd: [a Windows Azure virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

További információkért lásd: a [Azure rendelkezésre állási készletek] [ planning-guide-3.2.3] az Azure virtuális gépek tervezési és megvalósítási SAP NetWeaver dokumentum.

**Nem felügyelt csak lemezek:** mivel az Azure storage-fiók egy potenciális hibaérzékeny pontok kialakulását, fontos, hogy legalább két az Azure storage-fiókot, legalább két virtuális gép szétosztva. Az ideális beállítás a lemezeket, az egyes virtuális gépek egy SAP párbeszédpanel példányát futtató volna helyezhető üzembe egy másik tárolási fiókot.

> [!IMPORTANT]
> Határozottan javasoljuk, hogy használja-e Azure által kezelt lemezeken a SAP magas rendelkezésre állású telepítéshez. Felügyelt lemezek automatikusan igazodnak a rendelkezésre állási csoport a virtuális gép vannak csatolva, mert azok a virtuális gép és a rajta futó szolgáltatások rendelkezésre állásának javítása.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>A Windows egy SAP ASC/SCS példányához magas rendelkezésre állású architektúrája

> ![Windows][Logo_Windows] Windows
>

A WSFC-megoldás segítségével az SAP ASC/SCS példány védelmét. A megoldás két rendelkezik:

* **Az SAP ASC/SCS példány fürt fürtözött megosztott lemezek használatával**: Ez az architektúra kapcsolatos további információkért lásd: [SAP ASC/SCS példánya a Windows feladatátvevő fürt a fürt egy fürt megosztott lemez] [ sap-high-availability-guide-wsfc-shared-disk].   

* **Az SAP ASC/SCS példány fájlmegosztást a fürt**: Ez az architektúra kapcsolatos további információkért lásd: [SAP ASC/SCS példány fürtön egy Windows feladatátvevő fürt fájlmegosztás használatával] [ sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Magas rendelkezésre állású architektúra egy SAP ASC/SCS példány Linux rendszeren

> ![Linux][Logo_Linux] Linux
>
Az SAP ASC/SCS példány fürtszolgáltatással a SLES fürt keretrendszer használatával kapcsolatos további információkért lásd: [magas rendelkezésre állás a SAP NetWeaver a SUSE Linux Enterprise Server Azure virtuális gépeken az SAP-alkalmazásokból] [ sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver multi-SID-konfiguráció egy fürtözött SAP ASC/SCS-példány

> ![Windows][Logo_Windows] Windows
>
> Multi-SID jelenleg csak a WSFC használata támogatott. Multi-SID fájlmegosztás és a megosztott lemez használata támogatott.
>
Magas rendelkezésre állású architektúra multi-SID kapcsolatos további információkért lásd:

* [SAP ASC/SCS példányt multi-SID magas rendelkezésre állású Windows Server feladatátvételi fürtszolgáltatás és a fájlmegosztás][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASC/SCS példányt multi-SID magas rendelkezésre állású Windows Server feladatátvételi fürtszolgáltatás és megosztott lemez][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Magas rendelkezésre állású DBMS példány

Az adatbázis-kezelő a rendszer egyetlen pont, forduljon egy SAP rendszerben. Azt a magas rendelkezésre állású megoldás használatával védeni kell. A következő ábrán látható egy SQL Server AlwaysOn magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatási és az Azure belső terheléselosztó. SQL Server AlwaysOn DBMS adatainak és naplókönyvtárainak fájlok replikációja a saját adatbázis-kezelő replikációval. Ebben az esetben a fürt megosztott lemezt, amely egyszerűbbé teszi a teljes telepítés nem szükséges.

![3. ábra: Példa egy magas rendelkezésre állású SAP DBMS, az SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**3. ábra:** egy magas rendelkezésre állású SAP DBMS, az SQL Server AlwaysOn – példa_

Az Azure Resource Manager telepítési modell segítségével az Azure SQL Server adatbázis-kezelő fürtszolgáltatással kapcsolatos további információkért lásd: ezek a cikkek:

* [Az AlwaysOn rendelkezésre állási csoport az Azure virtuális gépeken kézi konfigurálása erőforrás-kezelő használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Egy Azure belső terheléselosztót egy AlwaysOn rendelkezésre állási csoport konfigurálása az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

SAP HANA DBMS az Azure-ban az Azure Resource Manager telepítési modell segítségével fürtszolgáltatással kapcsolatos további információkért lásd: [SAP HANA Azure virtuális gépek (VM) a magas rendelkezésre állású][sap-hana-ha].
