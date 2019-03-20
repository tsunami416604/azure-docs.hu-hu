---
title: Az Azure virtuális gépek magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára |} A Microsoft Docs
description: Magas rendelkezésre állású architektúra és forgatókönyvek esetében az SAP NetWeaver az Azure Virtual machines szolgáltatásban
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37f5040585681a53743fb3426b7f7ffac36de51c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008691"
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

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP több biztonsági AZONOSÍTÓVAL magas rendelkezésre állású konfiguráció)


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

**Magas rendelkezésre állású**: Informatikai fennakadások minimálisra azáltal, hogy az informatikai szolgáltatások összetevőit redundáns, hibatűrő vagy feladatátvételi védett keresztül üzletmenet technológiák csoportja hivatkozik a *ugyanazon* adatközpontban. Ebben az esetben, az adatközpontban található, egy Azure-régióban.

**Vész-helyreállítási**: Is hivatkozik a minimálisra csökkentik az informatikai szolgáltatások megszakítás és a helyreállítási de keresztben *különböző* , lehet, hogy több száz mérföld forrásadatok egy másik adatközpontban. A mi esetünkben az adatközpontok különböző Azure-régióban egy geopolitikai régión belül, vagy a Microsoft az Ön által létrehozott helyeken előfordulhat, hogy találhatók.


## <a name="overview-of-high-availability"></a>A magas rendelkezésre állás – Áttekintés
Az SAP az Azure-ban magas rendelkezésre állású három tartalmaznak:

* **Azure-infrastruktúra magas rendelkezésre állású**: 

    Például a magas rendelkezésre állás (VM) számítási, hálózati, vagy tárolási és annak előnyeit SAP-alkalmazások rendelkezésre állásának növelése tartalmazhatnak.

* **Indítsa újra a virtuális gép Azure-infrastruktúra használatával eléréséhez *magasabb rendelkezésre állás* SAP alkalmazások**: 

    Ha úgy dönt, hogy nem használja a funkciókat, például a Windows Server feladatátvételi fürtszolgáltatási (WSFC) vagy támasztja Linux rendszeren, akkor az Azure virtuális gép újraindítása használt fel. Megvédi SAP-rendszereinket tervezett és nem tervezett leállások ellen a fizikai kiszolgáló Azure-infrastruktúra és a mögöttes Azure platform általános.

* **Az SAP alkalmazások magas rendelkezésre állású**: 

    Teljes SAP rendszer magas rendelkezésre állás, az összes kritikus fontosságú SAP rendszerösszetevők kell levédenie. Példa:
    * Redundáns SAP-alkalmazáskiszolgálók.
    * Egyedi összetevők. Például lehet a hiba (SPOF) összetevő, például az SAP ASCS/SCS példányhoz vagy egy adatbázis-kezelő rendszer (DBMS) hibaérzékeny pont.

Az SAP az Azure-ban magas rendelkezésre állású SAP magas rendelkezésre állású helyszíni fizikai vagy virtuális környezetben eltér. A következő tanulmány [SAP NetWeaver magas rendelkezésre állás és a VMware és a Microsoft Windows Hyper-V virtuális környezetek üzletmenet-folytonossági] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] standard SAP magas rendelkezésre állású ismerteti konfigurációk a Windows virtualizált környezetekben.

Nem tartozik sapinst integrált SAP magas rendelkezésre állású konfiguráció Linux, a Windows van. Linux SAP magas rendelkezésre állású helyszíni kapcsolatos információkért lásd: [magas rendelkezésre állású partneradatok][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra magas rendelkezésre állás

### <a name="sla-for-single-instance-virtual-machines"></a>Egypéldányos virtuális gépekre vonatkozó SLA

Jelenleg egy egyetlen virtuális garantált 99,9 %-a premium storage segítségével. Hogy mi lehet az egyetlen virtuális gép rendelkezésre állásának kapcsolatos képet kapjon, hozhat létre a különböző elérhető a termék [Azure szolgáltatásiszint-szerződései][azure-sla].

A számítási alapja 30 nap / hó, vagy 43 200 perc. Ha például 21,6 perc 0,05 % üzemkimaradást felel meg. A különböző szolgáltatások rendelkezésre állását a szokásos módon, a következő módon számítható ki:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) \*...

Példa:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 vagy 99.75 %-os általános rendelkezésre állási.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Az ugyanazon rendelkezésre állási csoportban lévő virtuális gépek több példánya
Minden virtuális gép, amelynek két vagy több példánya azonos *rendelkezésre állási csoport*, garantáljuk, hogy elérhetőséget virtuálisgép-példányhoz legalább egy legalább 99,95 %-ában.

Ha két vagy több virtuális gépet az azonos rendelkezésre állási csoport része, minden virtuális gép rendelkezésre állási csoportban van hozzárendelve egy *frissítési tartományt* és a egy *tartalék tartomány* a mögöttes Azure platform.

* **Frissítési tartományok** garantálja, hogy több virtuális gép nem indulnak újra, amely az Azure-infrastruktúra a tervezett karbantartás során egy időben. Egyszerre csak egy virtuális gép újraindul.

* **Tartalék tartományok** garantálja, hogy a virtuális gépek telepítve vannak a hardverösszetevők, amelyek nem rendelkeznek közös áramforrással és hálózati kapcsolóval. Kiszolgálók, a hálózati kapcsoló vagy egy áramforráshoz mennek keresztül egy nem tervezett üzemkimaradások, csak egy virtuális Géphez van hatással.

További információkért lásd: [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

Rendelkezésre állási magas rendelkezésre állásának eléréséhez használatos:

* Redundáns SAP-alkalmazáskiszolgálók.  
* Legalább két csomóponttal (virtuális gépek, a példában), amely védeni SPOFs például az SAP ASCS/SCS példányhoz vagy adatbázis-kezelő-fürtöket.


### <a name="azure-availability-zones"></a>Azure-beli rendelkezésre állási zónák
Azure éppen egy fogalmait bevezetéséről [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview) során különböző [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/). Hol érhetők el rendelkezésre állási zónák Azure-régióban az Azure-régiók rendelkezik független áramforrásról, hűtéssel és hálózati ellátás több adatközpontban. Egy adott Azure-régión belül különböző zónák ajánlat oka, hogy az alkalmazások üzembe helyezése két vagy három rendelkezésre állási zónák érhető el. Feltételezve, hogy áramforrásokhoz és/vagy hálózati problémák hatással lenne a csak egy rendelkezésre állási zónában infrastruktúráját, az alkalmazás központi telepítésének egy Azure-régióban továbbra is teljes körűen működik. Végül néhány kisebb kapacitással óta egy zónában lévő virtuális gépek elveszhetnek. Azonban a két zónában lévő virtuális gépek továbbra is helyezheti üzembe. Az Azure-régiók zónák kínáló szerepelnek [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview).

A rendelkezésre állási zónák használja, néhány dolgot figyelembe venni. A szempontok lista, például:

- Az Azure rendelkezésre állási csoportok egy rendelkezésre állási zónán belül nem lehet telepíteni. Meg kell választania egy rendelkezésre állási zónák vagy rendelkezésre állási csoport üzembe helyezési keretet másként egy virtuális gép.
- Nem használhatja a [alapszintű Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) megoldásokat hozhat létre feladatátvevő fürt feladatátvételi fürtszolgáltatások Windows vagy Linux rendszerű támasztja alapján. Ehelyett kell használnia a [Azure Standard Load Balancer Termékváltozat](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- Azure-beli rendelkezésre állási zónák nem biztosít semmilyen garanciát bizonyos egy régión belül a különböző zónák közötti távolság
- A hálózati késést a különböző Azure-régiókhoz tartozó különböző Azure rendelkezésre állási zónák között az Azure régiók eltérő lehet. Előfordulhatnak olyan esetek, akkor egy ügyfél ésszerű módon futtatható az üzembe helyezett SAP alkalmazásrétegre különböző zónák között óta a hálózati késés egyik zónából a aktív az adatbázis-kezelő virtuális gép továbbra is elfogadható az egy folyamatot az üzletmenetre. Mivel az ügyfél forgatókönyvek, ahol a késés a aktív az adatbázis-kezelő virtuális gép egy zóna és a egy virtuális Gépet egy másik zónában az SAP alkalmazáspéldány között lehet-e túl zavaró, és nem fogadható el, az SAP business folyamatok lesz. Ennek eredményeképpen az üzembe helyezési architektúra kell lenniük az aktív/aktív architektúra az alkalmazás vagy az aktív/passzív architektúra különböző, ha túl nagy a késés.
- Használatával [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) Azure rendelkezésre állási zónában történő üzembe helyezéséhez megadása kötelező 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>A virtuális gépek tervezett és nem tervezett karbantartás

Kétféle esemény Azure-platform hatással lehet a virtuális gépek rendelkezésre állásának:

* **Tervezett karbantartás** események pedig a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések. A frissítések általános megbízhatóságának, teljesítményének és a virtuális gépeket futtató platforminfrastruktúra biztonsági javítása.

* **Nem tervezett karbantartás** események történnek, ha valamilyen módon a mögöttes hardveres vagy fizikai infrastruktúrája a virtuális gép nem sikerült. Helyi hálózati hiba, a helyi lemezek meghibásodása vagy egyéb állványszintű meghibásodások is foglalhat magában. Ilyen hibát észlel, az Azure platform automatikusan áttelepíti a virtuális gép a nem megfelelő állapotú fizikai kiszolgálóra, amelyen a virtuális gép kifogástalan állapotú fizikai kiszolgáló. Ilyen esetek ritkák, de ezek is okozhatják a virtuális gép újraindítását.

További információkért lásd: [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Az Azure Storage-redundancia
A tárfiókban lévő adatok mindig replikálódik a tartósság és magas rendelkezésre állás érdekében az Azure Storage SLA átmeneti hardverhibák esetén is teljesíti.

Mivel az Azure Storage úgy biztosítja az adatok három rendszerkép alapértelmezés szerint több Azure-lemezek különböző RAID 5 vagy RAID 1 használata nem szükséges.

További információkért lásd: [Azure Storage replikáció][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
A Managed Disks szolgáltatás az Azure Resource Manager helyett virtuális merevlemezeket (VHD) az Azure storage-fiókokban tárolt ajánlott erőforrástípust. A felügyelt lemezek funkcióval automatikusan összehangolhatja, az Azure rendelkezésre állási csoport a virtuális gép vannak csatolva. Ezek a virtuális gép és a rajta futó szolgáltatások rendelkezésre állásának növelése.

További információkért lásd: [Azure Managed Disks – áttekintés][azure-storage-managed-disks-overview].

Azt javasoljuk, hogy felügyelt lemezek használata, mert azzal az üzembe helyezés és a virtuális gépek felügyeletét.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Azure-infrastruktúra magas rendelkezésre állás elérése érdekében használó *magasabb rendelkezésre állás* az SAP-alkalmazások

Ha úgy dönt, hogy nem használja a funkciókat, például a WSFC vagy támasztja (jelenleg csak a SUSE Linux Enterprise Server [SLES] 12 és annál újabb támogatott) Linux rendszeren, akkor az Azure virtuális gép újraindítása használt fel. Megvédi SAP-rendszereinket tervezett és nem tervezett leállások ellen a fizikai kiszolgáló Azure-infrastruktúra és a mögöttes Azure platform általános.

Ezzel a módszerrel kapcsolatos további információkért lásd: [igénybevételéhez Azure-infrastruktúra virtuális gép újraindítása a magas rendelkezésre állás az SAP-rendszer eléréséhez][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Magas rendelkezésre állás az SAP-alkalmazások Azure IaaS-on

Teljes SAP rendszer magas rendelkezésre állás, az összes kritikus fontosságú SAP rendszerösszetevők kell levédenie. Példa:
  * Redundáns SAP-alkalmazáskiszolgálók.
  * Egyedi összetevők. Például lehet a hiba (SPOF) összetevő, például az SAP ASCS/SCS példányhoz vagy egy adatbázis-kezelő rendszer (DBMS) hibaérzékeny pont.

A következő szakaszok ismertetik az összes három kritikus fontosságú SAP rendszer összetevőinek magas rendelkezésre állás eléréséhez.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Az SAP-alkalmazáskiszolgálókhoz magas rendelkezésre állású architektúrával

> Ebben a szakaszban a következőkre vonatkozik:
>
> ![Windows][Logo_Windows] Windows és a ![Linux][Logo_Linux] Linux
>

Általában nincs szükség egy adott magas rendelkezésre állású megoldás esetében az SAP-kiszolgáló és a párbeszédpanel alkalmazáspéldányok. Magas rendelkezésre állás, a redundancia, és Azure-beli virtuális gépek különböző példányainak alkalmazáspéldányok párbeszédpanelen konfigurálja. Az SAP-alkalmazás legalább két példányt az Azure-beli virtuális gépek két példánya telepítve kell lennie.

![1. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**1. ábra:** Magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

Gazdagép SAP alkalmazáskiszolgáló-példányok Azure egyazon rendelkezésre állási beállítása minden virtuális gépnek kell elhelyeznie. Az Azure rendelkezésre állási csoport biztosítja, hogy:

* Az összes virtuális gép az azonos frissítési tartományhoz tartoznak.  
    Frissítési tartomány biztosítja, hogy a virtuális gépek tervezett karbantartás leállások során egy időben nem frissítenek.

    Az alapszintű funkcióval rendelkezik, amely a különböző frissítési és tartalék tartományok az Azure skálázási egység épül, a már jelent meg a [frissítési tartományok] [ planning-guide-3.2.2] szakaszban.

* Az összes virtuális gép az azonos tartalék tartomány részét képezik.  
    Tartalék tartomány biztosítja, hogy a virtuális gépek, hogy nem rendszerkritikus meghibásodási pontot hatással van az összes virtuális gépek rendelkezésre állásának vannak-e telepítve.

A frissítési és tartalék tartományok, amelyek segítségével az Azure rendelkezésre állási csoport egy Azure-beli skálázás egységben lévő szám véges. Virtuális gépek folyamatosan hozzá egy egyetlen rendelkezésre állási csoportban, ha két vagy több virtuális gép lesz végül végül ugyanabban a tartalék és frissítési tartományban.

Ha telepít néhány SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépeken, feltéve, hogy van-e öt frissítési tartománnyal, a következő képen látható jelenik meg. Rendelkezésre állási csoporton belül frissítési és tartalék tartományok tényleges maximális száma a későbbiekben változhatnak:

![2. ábra: Magas rendelkezésre állás az SAP-alkalmazáskiszolgálók egy Azure rendelkezésre állási csoportban][planning-guide-figure-3000]
_**2. ábra:** Magas rendelkezésre állás az SAP-alkalmazáskiszolgálók egy Azure rendelkezésre állási beállítása_

További információkért lásd: [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése][azure-virtual-machines-manage-availability].

További információkért lásd: a [Azure rendelkezésre állási csoportok] [ planning-guide-3.2.3] a tervezése az Azure virtual machines és a megvalósítási SAP NetWeaver dokumentumra vonatkozó szakaszában.

**Csak a nem felügyelt lemezek:** Mivel az Azure storage-fiók egy potenciálisan hibaérzékeny pont, fontos legalább két Azure storage-fiókok, amelyben oszlanak meg legalább két virtuális gép rendelkezik. Egy ideális beállítás minden SAP párbeszédpanel példányát futtató virtuális gép lemezei a egy másik tárfiók üzembe kívánja helyezni.

> [!IMPORTANT]
> Javasoljuk, hogy az Azure használt felügyelt lemezek esetében az SAP magas rendelkezésre állású telepítéseit. A felügyelt lemezek funkcióval automatikusan összehangolhatja, a kapcsolódó virtuális gép rendelkezésre állási, mert azok a virtuális gép és a rajta futó szolgáltatások rendelkezésre állásának növelése.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Magas rendelkezésre állású architektúra a Windows-SAP ASCS/SCS példányhoz

> ![Windows][Logo_Windows] Windows
>

A WSFC-megoldás segítségével megvédheti az SAP ASCS/SCS példányhoz. A megoldás két változatban rendelkezik:

* **Az SAP ASCS/SCS példányhoz fürt fürtözött megosztott lemezek használatával**: Ez az architektúra kapcsolatos további információkért lásd: [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával][sap-high-availability-guide-wsfc-shared-disk].   

* **Az SAP ASCS/SCS példányhoz a fájlmegosztást a fürt**: Ez az architektúra kapcsolatos további információkért lásd: [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fájlmegosztás használatával][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>-A linuxon futó SAP ASCS/SCS-példányok esetében a magas rendelkezésre állású architektúrával

> ![Linux][Logo_Linux] Linux
> 
> A SLES fürt keretrendszer használatával az SAP ASCS/SCS példányhoz fürtszolgáltatással kapcsolatos további információkért lásd: [magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure virtuális gépeken SAP alkalmazások] [ sap-suse-ascs-ha]. Magas rendelkezésre ÁLLÁSÚ architektúra helyettesítő SLES rendszeren, amely nem igényel magas rendelkezésre álló NFS lásd [magas rendelkezésre állású útmutató az SAP NetWeaver SUSE Linux Enterprise Server, SAP-alkalmazások Azure NetApp fájlokkal futó] [ sap-suse-ascs-ha-anf].

A Red Hat-fürt keretrendszer használatával az SAP ASCS/SCS példányhoz fürtszolgáltatással kapcsolatos további információkért lásd: [Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver több biztonsági AZONOSÍTÓVAL konfigurációs a fürtözött SAP ASCS/SCS példányhoz

> ![Windows][Logo_Windows] Windows
> 
> Több SID-vel jelenleg csak a WSFC használata támogatott. Több SID-vel fájlmegosztás és a megosztott lemez használata támogatott.
> 
> Több SID-vel magas rendelkezésre állású architektúrával kapcsolatos további információkért lásd:

* [Az SAP ASCS/SCS példányt több biztonsági AZONOSÍTÓVAL magas rendelkezésre állású Windows Server feladatátvételi fürtszolgáltatás és a megosztott fájl][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Az SAP ASCS/SCS példány több biztonsági AZONOSÍTÓVAL magas rendelkezésre állású Windows Server feladatátvételi fürtszolgáltatás és megosztott lemez][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Magas rendelkezésre állású DBMS-példány

Az adatbázis-kezelő egy SAP-rendszerrel is szerepel egy egyetlen kapcsolattartási pontja. Kell egy magas rendelkezésre állású megoldás megvédheti azokat. A következő ábrán látható egy SQL Server AlwaysOn magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatási és az Azure belső terheléselosztó. Az SQL Server AlwaysOn adathoz és naplófájlhoz az adatbázis-kezelő replikálja a saját DBMS-replikációval történik. Ebben az esetben a fürt megosztott lemezt, amely egyszerűbbé teszi a teljes telepítés nem szükséges.

![3. ábra: Az SQL Server AlwaysOn egy magas rendelkezésre állású SAP DBMS – példa][sap-ha-guide-figure-2003]

_**3. ábra:** Az SQL Server AlwaysOn egy magas rendelkezésre állású SAP DBMS – példa_

Az Azure Resource Manager-alapú üzemi modell használatával az Azure-beli SQL Server adatbázis-kezelő fürtszolgáltatással kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Egy AlwaysOn rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépeken manuálisan Resource Manager használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [AlwaysOn rendelkezésre állási csoporthoz az Azure belső terheléselosztó konfigurálása az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

Az Azure Resource Manager-alapú üzemi modell használatával az Azure-beli SAP HANA DBMS fürtszolgáltatással kapcsolatos további információkért lásd: [magas rendelkezésre állás az SAP Hana az Azure-beli virtuális gépek (VM)][sap-hana-ha].
