---
title: Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe SAP-szoftvereket Linux rendszerű virtuális gépeken az Azure-ban.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: sedusch
ms.openlocfilehash: b9db5cbb9e65fc7bc8aa306a69a0889f29b61be3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101349"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]: https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
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
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Virtuális gépek és virtuális merevlemezek gyorsítótárazása)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Szoftveres RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS központi telepítésének szerkezete)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Magas rendelkezésre állás és vész-helyreállítás Azure-beli virtuális gépekkel)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 és újabb verziók)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 és korábbi verziók)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (SQL Server rendszerkép használata az Azure Marketplace-en)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Általános SQL Server az SAP-hez az Azure-ban – összefoglalás)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS vonatkozó részletek)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Tárolási konfiguráció)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Biztonsági mentés és visszaállítás)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (A biztonsági mentés és a visszaállítás teljesítményével kapcsolatos szempontok)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Más)
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

[deployment-guide]:deployment-guide.md (Azure Virtual Machines üzembe helyezés az SAP-ban)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-erőforrások)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Virtuális gép üzembe helyezése egyéni rendszerkép használatával)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (1. forgatókönyv: Virtuális gép üzembe helyezése az Azure Marketplace-ről az SAP-hoz)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (2. forgatókönyv: Virtuális gép üzembe helyezése az SAP-hez készült egyéni rendszerképekkel)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (3. forgatókönyv: Virtuális gép áthelyezése a helyszínről egy nem általánosított Azure VHD-vel SAP használatával)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (A virtuális gépek üzembe helyezési forgatókönyvei az SAP-on Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell-parancsmagok telepítése)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Az SAP-hez kapcsolódó PowerShell-parancsmagok letöltése és importálása)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Virtuális gép csatlakoztatása helyszíni tartományhoz – csak Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Az Azure VM-ügynök letöltése, telepítése és engedélyezése)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Az SAP-hez készült Azure Enhanced monitoring bővítmény konfigurálása)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Az Azure bővített monitorozásának készültségi ellenőrzése az SAP esetében)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Az Azure monitoring-infrastruktúra állapotának ellenőrzése)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Az SAP-hez készült Azure monitoring hibaelhárítása)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Figyelés konfigurálása)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (A proxy konfigurálása)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (A végpontok közötti figyelés beállításának ellenőrzése és hibaelhárítása)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Azure Virtual Machines az SAP tervezéséhez és megvalósításához)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Erőforrások)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Magas rendelkezésre állás és vész-helyreállítás az Azure-Virtual Machines futó SAP NetWeaver esetében)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Magas rendelkezésre állás az SAP-alkalmazások kiszolgálói számára)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Az Automatikus indítás használata az SAP-példányok esetében)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Csak felhőalapú virtuális gépek telepítése az Azure-ban a helyszíni ügyfél-hálózat függőségei nélkül)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Létesítmények közötti – egy vagy több SAP virtuális gép üzembe helyezése az Azure-ban teljes mértékben integrálva a helyszíni hálózattal)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-régiók)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Tartalék tartományok)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Frissítési tartományok)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure rendelkezésre állási készletek)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure Virtual Machines koncepciója)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure-Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Virtuális gép áthelyezése a helyszínről az Azure-ba egy nem általánosított lemezzel)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Virtuális gép üzembe helyezése egy ügyfél-specifikus képpel)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (A virtuális gép a helyszínről az Azure-ba való áthelyezésének előkészítése nem általánosított lemezzel)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Virtuális gép üzembe helyezésének előkészítése az SAP-hez készült ügyfél-rendszerképekkel)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Virtuális gépek előkészítése az Azure-beli SAP-vel)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Az Azure-lemez és az Azure-lemezkép közötti különbség)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Virtuális merevlemez feltöltése a helyszínről az Azure-ba)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Lemezek másolása az Azure Storage-fiókok között)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM-/VHD-struktúra SAP-környezetekhez)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (A csatlakoztatott lemezek automatikus csatlakoztatásának beállítása)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Egyetlen virtuális gép SAP NetWeaver bemutatóval/betanítási forgatókönyvvel)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Az SAP-példányok csak felhőalapú üzembe helyezésének fogalmai)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitoring megoldás az SAP-hoz)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure-Premium Storage)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure hálózatkezelés)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage Microsoft Azure Storage és adatlemezek)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]: https://support.sap.com/pam (SAP-termék rendelkezésre állási mátrixa)
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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Virtuális gépek üzembe helyezése és kezelése Azure Resource Manager-sablonok és az Azure CLI használatával)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Virtuális gépek kezelése a Azure Resource Manager és a PowerShell használatával)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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

Az Azure Virtual Machines olyan szervezetek számára készült megoldás, amelyek számítási és tárolási erőforrásokat igényelnek, minimális idő alatt és hosszadalmas beszerzési ciklusok nélkül. Az Azure Virtual Machines használatával klasszikus alkalmazásokat telepíthet az Azure-ban, például az SAP NetWeaver-alapú alkalmazásokat. További helyszíni erőforrások nélkül kiterjesztheti az alkalmazások megbízhatóságát és rendelkezésre állását. Az Azure Virtual Machines támogatja a létesítmények közötti kapcsolatokat, így integrálhatja az Azure Virtual Machinest a szervezet helyi tartományában, a privát felhőkben és az SAP-rendszer környezetében.

Ebben a cikkben az SAP-alkalmazások Azure-beli virtuális gépeken történő üzembe helyezésének lépéseit tárgyaljuk, beleértve az alternatív telepítési lehetőségeket és a hibaelhárítást. Ez a cikk az [Azure Virtual Machines az SAP NetWeaver tervezésével és megvalósításával][planning-guide]kapcsolatos információkra épül. Emellett kiegészíti az SAP telepítési dokumentációját és az SAP-megjegyzéseket is, amelyek az SAP-szoftverek telepítésének és telepítésének elsődleges erőforrásai.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Az Azure-beli virtuális gépek SAP-szoftverek telepítéséhez való beállítása több lépést és erőforrást is magában foglal. Mielőtt elkezdené, győződjön meg arról, hogy megfelel az SAP-szoftverek Azure-beli virtuális gépekre való telepítésének előfeltételeinek.

### <a name="local-computer"></a>Helyi számítógép

Windows-vagy Linux-alapú virtuális gépek kezeléséhez használhatja a PowerShell-parancsfájlt és a Azure Portal. Mindkét eszközhöz szükség van egy Windows 7 vagy újabb Windows-verziót futtató helyi számítógépre. Ha csak Linux rendszerű virtuális gépeket szeretne kezelni, és linuxos számítógépet szeretne használni ehhez a feladathoz, használhatja az Azure CLI-t.

### <a name="internet-connection"></a>Internetkapcsolat

Az SAP-szoftverek telepítéséhez szükséges eszközök és parancsfájlok letöltéséhez és futtatásához csatlakoznia kell az internethez. Az SAP-hez készült Azure Enhanced monitoring bővítményt futtató Azure-beli virtuális gép számára is elérhetőnek kell lennie az internethez. Ha az Azure-beli virtuális gép egy Azure-beli virtuális hálózat vagy egy helyszíni tartomány része, győződjön meg arról, hogy a megfelelő proxybeállítások be vannak állítva a [proxy konfigurálása][deployment-guide-configure-proxy]című részben leírtak szerint.

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-előfizetés

Aktív Azure-fiókra van szüksége.

### <a name="topology-and-networking"></a>Topológia és hálózatkezelés

Meg kell határoznia az SAP üzembe helyezésének topológiáját és architektúráját az Azure-ban:

* Használandó Azure Storage-fiókok
* Virtuális hálózat, amelyen telepíteni szeretné az SAP-rendszerét
* Az az erőforráscsoport, amelybe telepíteni kívánja az SAP-rendszerét
* Az az Azure-régió, ahol telepíteni szeretné az SAP-rendszerét
* SAP-konfiguráció (kétrétegű vagy háromrétegű)
* Virtuálisgép-méretek és a virtuális gépekhez csatlakoztatni kívánt további adatlemezek száma
* SAP-javító és-átviteli rendszerek (CTS) konfigurációja

Hozzon létre és konfiguráljon Azure Storage-fiókokat (ha szükséges) vagy Azure-beli virtuális hálózatokat az SAP szoftver központi telepítési folyamatának megkezdése előtt. További információ az erőforrások létrehozásáról és konfigurálásáról: [Azure Virtual Machines tervezése és implementálása az SAP NetWeaver][planning-guide]-ben.

### <a name="sap-sizing"></a>SAP-méretezés

A következő információk ismerete az SAP-méretezéshez:

* Tervezett SAP-munkaterhelés, például az SAP gyors méretezési eszköz és az SAP Application Performance standard (NEDV) számának használatával
* Az SAP-rendszeren szükséges CPU-erőforrás és memória-felhasználás
* Szükséges bemeneti/kimeneti (I/O) műveletek másodpercenként
* Az Azure-beli virtuális gépek közötti esetleges kommunikációhoz szükséges hálózati sávszélesség
* Szükséges hálózati sávszélesség a helyszíni eszközök és az Azure által telepített SAP-rendszer között

### <a name="resource-groups"></a>Erőforráscsoportok

Az Azure Resource Manager az Azure-előfizetésében lévő összes alkalmazás-erőforrást az erőforráscsoportok használatával kezelheti. További információk: [Azure Resource Manager overview][resource-group-overview] (Az Azure Resource Manager áttekintése).

## <a name="resources"></a>További források

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-erőforrások

Az SAP-szoftverek központi telepítésének beállításakor a következő SAP-erőforrásokra van szükség:

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [1409604] -es SAP-Megjegyzés rendelkezik a szükséges SAP-gazdagép ügynökének verziójával az Azure-ban.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az [2002167] -es SAP-Megjegyzés általános információkat tartalmaz az Red Hat Enterprise Linux 7. x-ről.
* Az [2069760] -es SAP-Megjegyzés általános információkat tartalmaz az Oracle Linux 7. x-ről.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [1597355] -es SAP-Megjegyzés általános információkat tartalmaz a Linux-alapú swap-területről.
* Az [SAP az Azure-beli állapotváltozás oldalán](https://wiki.scn.sap.com/wiki/x/Pia7Gg) Hírek és hasznos erőforrások gyűjteménye szerepel.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* A [Azure PowerShell][azure-ps]részét képező SAP-specifikus PowerShell-parancsmagok.
* Az [Azure CLI][azure-cli]részét képező SAP-specifikus Azure CLI-parancsok.

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-erőforrások

Ezek a Microsoft-cikkek a SAP üzembe helyezéseit fedik le az Azure-ban:

* [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben (ez a cikk)][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP NetWeaver számára][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Üzembe helyezési forgatókönyvek az Azure virtuális gépeken futó SAP-szoftverekhez

Több lehetőség is rendelkezésre áll a virtuális gépek és a hozzájuk kapcsolódó lemezek Azure-beli üzembe helyezésére. Fontos megérteni az üzembe helyezési lehetőségek közötti különbségeket, mivel előfordulhat, hogy a választott központi telepítési típus alapján a virtuális gépek központi telepítésének előkészítéséhez különböző lépések szükségesek.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>1. forgatókönyv: Virtuális gép üzembe helyezése az Azure Marketplace-ről az SAP-hoz

A virtuális gép üzembe helyezéséhez a Microsoft vagy egy harmadik fél által biztosított lemezképet is használhat az Azure piactéren. A piactér a Windows Server és a különböző Linux-disztribúciók néhány szabványos operációsrendszer-lemezképét kínálja. Olyan rendszerképet is telepíthet, amely tartalmazza az adatbázis-kezelő rendszer (adatbázisok kezelése) SKU-t (például Microsoft SQL Server). További információ az adatbázis-kezelői készletekkel rendelkező lemezképek használatáról: [Azure Virtual Machines adatbázis-kezelő rendszerbe állítás az SAP NetWeaver számára][dbms-guide].

A következő folyamatábra egy virtuális gép Azure piactéren való üzembe helyezéséhez szükséges SAP-specifikus lépések listáját mutatja be:

![Az SAP-rendszerek virtuálisgép-telepítésének folyamatábrája az Azure Marketplace-en található virtuálisgép-rendszerkép használatával][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portal használatával

Az Azure Marketplace-ről rendszerképpel rendelkező új virtuális gép létrehozásának legegyszerűbb módja a Azure Portal használata.

1.  Nyissa meg a következőt: <https://portal.azure.com/#create/hub>.  Vagy a Azure Portal menüben válassza az **+ új**lehetőséget.
1.  Válassza a **számítás**lehetőséget, majd válassza ki a telepíteni kívánt operációs rendszer típusát. Például: Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) vagy Oracle Linux 7,2. Az alapértelmezett listanézet nem jeleníti meg az összes támogatott operációs rendszert. Válassza az összes megjelenítése lehetőséget a teljes lista **megtekintéséhez** . További információ az SAP-szoftverek központi telepítéséhez támogatott operációs rendszerekről: SAP-Megjegyzés [1928533].
1.  A következő oldalon tekintse át a használati feltételeket.
1.  A **telepítési modell kiválasztása** mezőben válassza a **Resource Manager**lehetőséget.
1.  Kattintson a **Létrehozás** gombra.

A varázsló végigvezeti a virtuális gép létrehozásához szükséges paraméterek beállításán, az összes szükséges erőforráson, például a hálózati adaptereken és a Storage-fiókokon kívül. A paraméterek némelyike a következő:

1. **Alapvető beállítások**:
   * **Név**: Az erőforrás neve (a virtuális gép neve).
   * **VM-lemez típusa**: Válassza ki az operációsrendszer-lemez lemezének típusát. Ha Premium Storaget szeretne használni az adatlemezekhez, javasoljuk, hogy az operációs rendszer lemezének Premium Storage használatát is használja.
   * **Felhasználónév és jelszó** vagy **nyilvános SSH-kulcs**: Adja meg a kiépítés során létrehozott felhasználó felhasználónevét és jelszavát. Linux rendszerű virtuális gépek esetében megadhatja a számítógépbe való bejelentkezéshez használt nyilvános Secure Shell-(SSH-) kulcsot.
   * **Előfizetés**: Válassza ki az új virtuális gép kiépítéséhez használni kívánt előfizetést.
   * **Erőforráscsoport**: A virtuális gép erőforráscsoport neve. Megadhat egy új erőforráscsoport nevét, vagy egy már létező erőforráscsoport nevét.
   * **Hely**: Hová kell telepíteni az új virtuális gépet. Ha a virtuális gépet a helyszíni hálózathoz szeretné csatlakoztatni, győződjön meg arról, hogy a virtuális hálózat azon helyét választja, amely az Azure-t csatlakoztatja a helyszíni hálózathoz. További információ: [Microsoft Azure hálózatkezelés][planning-guide-microsoft-azure-networking] az Azure-ban [Virtual Machines az SAP NetWeaver tervezése és megvalósítása][planning-guide].
1. **Méret**:

     A támogatott virtuálisgép-típusok listáját lásd: SAP-Megjegyzés [1928533]. Ügyeljen arra, hogy a megfelelő virtuálisgép-típust válassza, ha az Azure Premium Storaget szeretné használni. Nem minden VM-típus támogatja a Premium Storage. További információ [: Storage: Microsoft Azure Storage és adatlemezek][planning-guide-storage-microsoft-azure-storage-and-data-disks] és [Azure Premium Storage][planning-guide-azure-premium-storage] az Azure-ban Virtual Machines az [SAP NetWeaver tervezése és megvalósítása][planning-guide].

1. **Beállítások**:
   * **Storage**
     * **Lemez típusa**: Válassza ki az operációsrendszer-lemez lemezének típusát. Ha Premium Storaget szeretne használni az adatlemezekhez, javasoljuk, hogy az operációs rendszer lemezének Premium Storage használatát is használja.
     * **Felügyelt lemezek használata**: Ha Managed Diskst szeretne használni, válassza az Igen lehetőséget. További információ a Managed Disksről: fejezet [Managed Disks][planning-guide-managed-disks] a tervezési útmutatóban.
     * **Storage-fiók**: Válasszon egy meglévő Storage-fiókot, vagy hozzon létre egy újat. Nem minden tárolási típus működik az SAP-alkalmazások futtatásához. További információ a tárolási típusokról: [virtuális gép tárolási szerkezete RDBMS üzemelő példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Hálózat**
     * **Virtuális hálózat** és **alhálózat**: A virtuális gép intranetes integrálásához válassza ki a helyszíni hálózathoz csatlakozó virtuális hálózatot.
     * **Nyilvános IP-cím**: Válassza ki a használni kívánt nyilvános IP-címet, vagy adja meg a paramétereket egy új nyilvános IP-cím létrehozásához. Nyilvános IP-címet is használhat a virtuális gép interneten keresztüli eléréséhez. Győződjön meg arról, hogy létrehoz egy hálózati biztonsági csoportot is a virtuális géphez való hozzáférés biztonságossá tételéhez.
     * **Hálózati biztonsági csoport**: További információ: [a hálózati forgalom forgalmának szabályozása hálózati biztonsági csoportokkal][virtual-networks-nsg].
   * **Bővítmények**: A virtuálisgép-bővítmények a telepítéshez való hozzáadásával is telepíthetők. Ebben a lépésben nem kell bővítményeket felvennie. Az SAP-támogatáshoz szükséges bővítmények később települnek. Lásd a jelen útmutató [az SAP-hez készült Azure Enhanced monitoring bővítményének konfigurálása][deployment-guide-4.5] című szakaszát.
   * **Magas rendelkezésre állás**: Válasszon ki egy rendelkezésre állási készletet, vagy adja meg a paramétereket egy új rendelkezésre állási csoport létrehozásához. További információ: [Azure rendelkezésre állási készletek][planning-guide-3.2.3].
   * **Monitorozás**
     * **Rendszerindítási diagnosztika**: A rendszerindítási diagnosztika **letiltása** lehetőség kiválasztásával.
     * **Vendég operációs rendszer diagnosztikája**: A diagnosztika figyelése lehetőség kiválasztásával engedélyezheti a **tiltást** .

1. **Összefoglalás**:

   Tekintse át a beállításokat, majd kattintson **az OK gombra**.

A virtuális gép üzembe helyezése a kiválasztott erőforráscsoporthoz történik.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Virtuális gép létrehozása sablon használatával

Létrehozhat egy virtuális gépet az [Azure-Gyorsindítás-templates GitHub][azure-quickstart-templates-github]-tárházban közzétett SAP-sablonok egyikével. A virtuális gépet manuálisan is létrehozhatja a [Azure Portal][virtual-machines-windows-tutorial], a [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]vagy az [Azure CLI][virtual-machines-linux-tutorial]használatával.

* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon** (SAP-2-réteg-piactér-rendszerkép)][sap-templates-2-tier-marketplace-image]

  Ha csak egy virtuális géppel szeretné létrehozni a kétrétegű rendszereket, használja ezt a sablont.
* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon – Managed Disks** (SAP-2-réteg-piactér-rendszerkép-MD)][sap-templates-2-tier-marketplace-image-md]

  Kétrétegű rendszer létrehozásához csak egy virtuális gép és Managed Disks használatával használja ezt a sablont.
* [**Háromrétegű konfiguráció (több virtuális gép) sablon** (SAP-3-réteg-piactér-rendszerkép)][sap-templates-3-tier-marketplace-image]

  Ha egy háromrétegű rendszerhez több virtuális gép használatával szeretne létrehozni, használja ezt a sablont.
* [**Háromrétegű konfiguráció (több virtuális gép) sablon – Managed Disks** (SAP-3-réteg-piactér-rendszerkép-MD)][sap-templates-3-tier-marketplace-image-md]

  Ha egy háromrétegű rendszer több virtuális géppel és Managed Disks használatával szeretne létrehozni, használja ezt a sablont.

A Azure Portal adja meg a következő paramétereket a sablonhoz:

1. **Alapvető beállítások**:
   * **Előfizetés**: A sablon üzembe helyezéséhez használt előfizetés.
   * **Erőforráscsoport**: A sablon üzembe helyezéséhez használt erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévő erőforráscsoportot is az előfizetésben.
   * **Hely**: A sablon üzembe helyezésének helye. Ha kiválasztott egy meglévő erőforráscsoportot, az adott erőforráscsoport helyét fogja használni.

1. **Beállítások**:
   * **SAP**-rendszerazonosító: Az SAP rendszerazonosító (SID).
   * **Operációs rendszer típusa**: A telepíteni kívánt operációs rendszer, például Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) vagy Oracle Linux 7,2.

     A listanézet nem jeleníti meg az összes támogatott operációs rendszert. További információ az SAP-szoftverek központi telepítéséhez támogatott operációs rendszerekről: SAP-Megjegyzés [1928533].
   * **SAP**-rendszerméret: Az SAP-szolgáltatás mérete.

     Az új rendszerek SAP-száma. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
   * A **rendszerek rendelkezésre állása** (csak háromrétegű sablon): A rendszerek rendelkezésre állása.

     Válassza a **Ha** lehetőséget egy magas rendelkezésre állású telepítéshez megfelelő konfigurációhoz. A rendszer két adatbázis-kiszolgálót és két kiszolgálót is létrehoz a ABAP SAP Central Services (ASCS) szolgáltatásokhoz.
   * **Tárolási típus** (csak kétrétegű sablon): A használandó tároló típusa.

     Nagyobb rendszerek esetében ajánlott az Azure Premium Storage használata. A tárolási típusokkal kapcsolatos további információkért tekintse meg a következő forrásokat:
      * [Az Azure prémium SSD Storage használata az SAP adatbázis-kezelő példányához][2367194]
      * [Egy virtuális gép tárolási szerkezete RDBMS üzemelő példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Nagy teljesítményű tárterület az Azure-beli virtuális gépek számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
   * **Rendszergazdai Felhasználónév** és **rendszergazdai jelszó**: Felhasználónév és jelszó.
     A rendszer létrehoz egy új felhasználót a virtuális gépre való bejelentkezéshez.
   * **Új vagy meglévő alhálózat**: Meghatározza, hogy a rendszer létrehoz-e új virtuális hálózatot és alhálózatot, vagy meglévő alhálózatot használ-e. Ha már van olyan virtuális hálózata, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő**lehetőséget.
   * **ALHÁLÓZAT azonosítója**: Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában a következőképpen néz ki:&lt;/Subscriptions/előfizetés azonosítója >&lt;/resourceGroups/erőforráscsoport neve >/Providers/Microsoft.Network/virtualNetworks/&lt;virtuális hálózat neve >/Subnets/&lt;alhálózat neve >

1. **Feltételek és**kikötések:  
    Tekintse át és fogadja el a jogi feltételeket.

1. Válassza a **Beszerzés** lehetőséget.

Az Azure-beli virtuálisgép-ügynök alapértelmezés szerint telepítve van, ha az Azure Marketplace-en lemezképet használ.

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

A helyszíni hálózat konfigurációjától függően előfordulhat, hogy a proxyt be kell állítania a virtuális gépen. Ha a virtuális gép VPN-vagy ExpressRoute-kapcsolaton keresztül csatlakozik a helyszíni hálózathoz, előfordulhat, hogy a virtuális gép nem fér hozzá az internethez, és nem fogja tudni letölteni a szükséges kiterjesztéseket, illetve nem gyűjti a figyelési adatokat. További információ: [configure The proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)

Ha az Azure-beli üzembe helyezése egy helyszíni Active Directory vagy DNS-példányhoz egy Azure-helyek közötti VPN-kapcsolaton vagy [Virtual Machines ExpressRoute keresztül történik NetWeaver][planning-guide]) a rendszer azt várta, hogy a virtuális gép egy helyszíni tartományhoz csatlakozik. További információ a feladattal kapcsolatos megfontolásokról: [virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Figyelés konfigurálása

Ahhoz, hogy az SAP támogassa a környezetet, állítsa be az Azure monitoring bővítményt az SAP-hez az SAP-hez készült [Azure Enhanced monitoring bővítmény konfigurálása][deployment-guide-4.5]című cikkben leírtak szerint. Győződjön meg az SAP-figyelés előfeltételeiről és az SAP kernel és az SAP Host Agent szükséges minimális verzióiról az SAP- [erőforrások][deployment-guide-2.2]listájában felsorolt erőforrásokban.

#### <a name="monitoring-check"></a>Figyelés ellenőrzése

Ellenőrizze, hogy működik-e a figyelés, lásd: [ellenőrzések és hibaelhárítás a végpontok közötti figyelés beállításához][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

A virtuális gép létrehozása és a virtuális gép üzembe helyezése után telepítenie kell a szükséges szoftver-összetevőket a virtuális gépen. Az ilyen típusú virtuálisgép-telepítés központi telepítési/szoftvertelepítési folyamata miatt a telepítendő szoftvernek már elérhetőnek kell lennie az Azure-ban, egy másik virtuális gépen vagy egy csatolható lemezként. Vagy használjon olyan létesítmények közötti helyzetet, amelyekben a helyszíni eszközökhöz (telepítési megosztásokhoz) való kapcsolódás van megadva.

Miután üzembe helyezte a virtuális gépet az Azure-ban, kövesse ugyanezeket az irányelveket és eszközöket, hogy az SAP-szoftvert a virtuális gépre telepítse, mint a helyszíni környezetben. Ha az SAP-szoftvert egy Azure-beli virtuális gépen szeretné telepíteni, az SAP és a Microsoft is azt javasolja, hogy az SAP telepítési adathordozóját töltse fel és tárolja az Azure VHD-k vagy a Managed Disks számára, vagy hozzon létre egy olyan Azure-beli virtuális gépet, amely az összes szükséges SAP telepítési adathordozóval rendelkezik.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>2. forgatókönyv: Virtuális gép üzembe helyezése az SAP-hez készült egyéni rendszerképekkel

Mivel az operációs rendszer vagy az adatbázis különböző verziói eltérő javítási követelményekkel rendelkeznek, előfordulhat, hogy az Azure Marketplace-en található rendszerképek nem felelnek meg az igényeinek. Ehelyett érdemes lehet egy virtuális gépet létrehozni a saját operációs rendszer/adatbázis-kezelő virtuális gép rendszerkép használatával, amelyet később is üzembe helyezhet.
A Linux rendszerhez készült privát rendszerképeket különböző lépésekkel hozhatja létre, mint a Windows számára.

---
> ![Windows][Logo_Windows] Windows
>
> Ha olyan Windows-lemezképet szeretne előkészíteni, amelyet több virtuális gép telepítéséhez használhat, a Windows-beállításokat (például a Windows SID-t és az állomásnévt) a helyszíni virtuális gépen kell absztrakt vagy általánosítani. Ezt a [Sysprep](https://msdn.microsoft.com/library/hh825084.aspx) használatával végezheti el.
>
> ![Linux][Logo_Linux] Linux
>
> A több virtuális gép üzembe helyezéséhez használható linuxos lemezkép előkészítéséhez egyes Linux-beállításokat el kell vetni vagy általánosítani kell a helyszíni virtuális gépen. `waagent -deprovision` Ezt használhatja. További információ: az Azure-ban [futó linuxos virtuális gép rögzítése][virtual-machines-linux-capture-image] és az [Azure Linux-ügynök felhasználói útmutatója][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Előkészítheti és létrehozhatja az egyéni rendszerképeket, majd a használatával több új virtuális gépet is létrehozhat. Ez az [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása][planning-guide]című témakörben található. Állítsa be az adatbázis tartalmát az SAP szoftver-kiépítési kezelővel egy új SAP-rendszer telepítéséhez (visszaállítja egy adatbázis biztonsági mentését a virtuális géphez csatlakoztatott lemezről), vagy az adatbázis biztonsági másolatának közvetlen visszaállítását az Azure Storage-ból, ha az adatbázis-KEZELŐje támogatja azt. További információkért lásd: [SAP NetWeaver az Azure Virtual Machines DBMS üzembe][dbms-guide]. Ha már telepített egy SAP-rendszert a helyszíni virtuális gépen (különösen a kétrétegű rendszerek esetében), az SAP-rendszerbeállítások az Azure-beli virtuális gép üzembe helyezése után az SAP-szoftver kiépítési kezelője által támogatott rendszer-átnevezési eljárással módosíthatók (SAP [1619720]. megjegyzés). Ellenkező esetben az Azure-beli virtuális gép üzembe helyezése után is telepítheti az SAP szoftvert.

A következő folyamatábra a virtuális gép egyéni rendszerképből történő üzembe helyezéséhez szükséges SAP-specifikus lépések listáját mutatja be:

![Az SAP-rendszerek virtuálisgép-telepítésének folyamatábrája virtuálisgép-rendszerkép használatával a privát piactéren][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portal használatával

Az új virtuális gép felügyelt lemezképből való létrehozásának legegyszerűbb módja a Azure Portal használata. A lemezképek kezelésével kapcsolatos további információkért olvassa el az általánosított [virtuális gép felügyelt rendszerképének rögzítése az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) című témakört.

1.  Nyissa meg a következőt: <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Vagy a Azure Portal menüben válassza a **képek**elemet.
1.  Válassza ki a felügyelt lemezképet, amelyet központilag telepíteni szeretne, és kattintson a **virtuális gép létrehozása** elemre.

A varázsló végigvezeti a virtuális gép létrehozásához szükséges paraméterek beállításán, az összes szükséges erőforráson, például a hálózati adaptereken és a Storage-fiókokon kívül. A paraméterek némelyike a következő:

1. **Alapvető beállítások**:
   * **Név**: Az erőforrás neve (a virtuális gép neve).
   * **VM-lemez típusa**: Válassza ki az operációsrendszer-lemez lemezének típusát. Ha Premium Storaget szeretne használni az adatlemezekhez, javasoljuk, hogy az operációs rendszer lemezének Premium Storage használatát is használja.
   * **Felhasználónév és jelszó** vagy **nyilvános SSH-kulcs**: Adja meg a kiépítés során létrehozott felhasználó felhasználónevét és jelszavát. Linux rendszerű virtuális gépek esetében megadhatja a számítógépbe való bejelentkezéshez használt nyilvános Secure Shell-(SSH-) kulcsot.
   * **Előfizetés**: Válassza ki az új virtuális gép kiépítéséhez használni kívánt előfizetést.
   * **Erőforráscsoport**: A virtuális gép erőforráscsoport neve. Megadhat egy új erőforráscsoport nevét, vagy egy már létező erőforráscsoport nevét.
   * **Hely**: Hová kell telepíteni az új virtuális gépet. Ha a virtuális gépet a helyszíni hálózathoz szeretné csatlakoztatni, győződjön meg arról, hogy a virtuális hálózat azon helyét választja, amely az Azure-t csatlakoztatja a helyszíni hálózathoz. További információ: [Microsoft Azure hálózatkezelés][planning-guide-microsoft-azure-networking] az Azure-ban [Virtual Machines az SAP NetWeaver tervezése és megvalósítása][planning-guide].
1. **Méret**:

     A támogatott virtuálisgép-típusok listáját lásd: SAP-Megjegyzés [1928533]. Ügyeljen arra, hogy a megfelelő virtuálisgép-típust válassza, ha az Azure Premium Storaget szeretné használni. Nem minden VM-típus támogatja a Premium Storage. További információ [: Storage: Microsoft Azure Storage és adatlemezek][planning-guide-storage-microsoft-azure-storage-and-data-disks] és [Azure Premium Storage][planning-guide-azure-premium-storage] az Azure-ban Virtual Machines az [SAP NetWeaver tervezése és megvalósítása][planning-guide].

1. **Beállítások**:
   * **Storage**
     * **Lemez típusa**: Válassza ki az operációsrendszer-lemez lemezének típusát. Ha Premium Storaget szeretne használni az adatlemezekhez, javasoljuk, hogy az operációs rendszer lemezének Premium Storage használatát is használja.
     * **Felügyelt lemezek használata**: Ha Managed Diskst szeretne használni, válassza az Igen lehetőséget. További információ a Managed Disksről: fejezet [Managed Disks][planning-guide-managed-disks] a tervezési útmutatóban.
   * **Hálózat**
     * **Virtuális hálózat** és **alhálózat**: A virtuális gép intranetes integrálásához válassza ki a helyszíni hálózathoz csatlakozó virtuális hálózatot.
     * **Nyilvános IP-cím**: Válassza ki a használni kívánt nyilvános IP-címet, vagy adja meg a paramétereket egy új nyilvános IP-cím létrehozásához. Nyilvános IP-címet is használhat a virtuális gép interneten keresztüli eléréséhez. Győződjön meg arról, hogy létrehoz egy hálózati biztonsági csoportot is a virtuális géphez való hozzáférés biztonságossá tételéhez.
     * **Hálózati biztonsági csoport**: További információ: [a hálózati forgalom forgalmának szabályozása hálózati biztonsági csoportokkal][virtual-networks-nsg].
   * **Bővítmények**: A virtuálisgép-bővítmények a telepítéshez való hozzáadásával is telepíthetők. Ebben a lépésben nem kell hozzáadnia a bővítményt. Az SAP-támogatáshoz szükséges bővítmények később települnek. Lásd a jelen útmutató [az SAP-hez készült Azure Enhanced monitoring bővítményének konfigurálása][deployment-guide-4.5] című szakaszát.
   * **Magas rendelkezésre állás**: Válasszon ki egy rendelkezésre állási készletet, vagy adja meg a paramétereket egy új rendelkezésre állási csoport létrehozásához. További információ: [Azure rendelkezésre állási készletek][planning-guide-3.2.3].
   * **Monitorozás**
     * **Rendszerindítási diagnosztika**: A rendszerindítási diagnosztika **letiltása** lehetőség kiválasztásával.
     * **Vendég operációs rendszer diagnosztikája**: A diagnosztika figyelése lehetőség kiválasztásával engedélyezheti a **tiltást** .

1. **Összefoglalás**:

   Tekintse át a beállításokat, majd kattintson **az OK gombra**.

A virtuális gép üzembe helyezése a kiválasztott erőforráscsoporthoz történik.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Virtuális gép létrehozása sablon használatával

A következő SAP-sablonok egyikével hozzon létre egy központi telepítést a Azure Portal privát operációsrendszer-rendszerképének használatával. Ezek a sablonok az [Azure-Gyorsindítás-templates GitHub][azure-quickstart-templates-github]-tárházban jelennek meg. A [PowerShell][virtual-machines-upload-image-windows-resource-manager]használatával manuálisan is létrehozhat virtuális gépeket.

* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon** (SAP-2-réteg-felhasználó-rendszerkép)][sap-templates-2-tier-user-image]

  Ha csak egy virtuális géppel szeretné létrehozni a kétrétegű rendszereket, használja ezt a sablont.
* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon – felügyelt lemezkép** (SAP-2-réteg-felhasználó-rendszerkép-MD)][sap-templates-2-tier-user-image-md]

  Ha csak egy virtuális géppel és egy felügyelt lemezkép használatával szeretne létrehozni egy kétrétegű rendszer-rendszerképet, használja ezt a sablont.
* [**Háromrétegű konfiguráció (több virtuális gép) sablon** (SAP-3-réteg-felhasználó-rendszerkép)][sap-templates-3-tier-user-image]

  Ha egy háromrétegű rendszert több virtuális géppel vagy saját operációsrendszer-lemezképpel szeretne létrehozni, használja ezt a sablont.
* [**Háromrétegű konfiguráció (több virtuális gép) sablon – felügyelt lemezkép** (SAP-3-réteg-felhasználó-rendszerkép-MD)][sap-templates-3-tier-user-image-md]

  Ha egy háromrétegű rendszert több virtuális gép vagy a saját operációsrendszer-lemezképe és egy felügyelt lemezkép használatával szeretne létrehozni, használja ezt a sablont.

A Azure Portal adja meg a következő paramétereket a sablonhoz:

1. **Alapvető beállítások**:
   * **Előfizetés**: A sablon üzembe helyezéséhez használt előfizetés.
   * **Erőforráscsoport**: A sablon üzembe helyezéséhez használt erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévő erőforráscsoportot az előfizetésben.
   * **Hely**: A sablon üzembe helyezésének helye. Ha kiválasztott egy meglévő erőforráscsoportot, az adott erőforráscsoport helyét fogja használni.
1. **Beállítások**:
   * **SAP**-rendszerazonosító: Az SAP rendszerazonosító.
   * **Operációs rendszer típusa**: A telepíteni kívánt operációs rendszer típusa (Windows vagy Linux).
   * **SAP**-rendszerméret: Az SAP-szolgáltatás mérete.

     Az új rendszerek SAP-száma. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
   * A **rendszerek rendelkezésre állása** (csak háromrétegű sablon): A rendszerek rendelkezésre állása.

     Válassza a **Ha** lehetőséget egy magas rendelkezésre állású telepítéshez megfelelő konfigurációhoz. A rendszer két adatbázis-kiszolgálót és két kiszolgálót hoz létre a ASCS.
   * **Tárolási típus** (csak kétrétegű sablon): A használandó tároló típusa.

     Nagyobb rendszerek esetében ajánlott az Azure Premium Storage használata. A tárolási típusokkal kapcsolatos további információkért tekintse meg a következő forrásokat:
      * [Az Azure prémium SSD Storage használata az SAP adatbázis-kezelő példányához][2367194]
      * [Egy virtuális gép tárolási szerkezete RDBMS üzemelő példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Nagy teljesítményű tárterület az Azure-beli virtuális gépek számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
   * **Felhasználói RENDSZERKÉP VHD URI-ja** (csak nem felügyelt lemezkép sablonja): A magánhálózati operációs rendszer lemezképének virtuális merevlemezének URI-ja,&lt;például https://accountname >. blob. Core. Windows. net/VHD/userimage. vhd.
   * **Felhasználói rendszerkép Storage** -fiókja (csak nem felügyelt lemezkép sablonja): Annak a Storage-fióknak a neve, ahol a magánhálózati operációs rendszer lemezképe tárolva van ( &lt;például&lt;accountname > a https://accountname >. blob. Core. Windows. net/VHD/userimage. vhd).
   * **userImageId** (csak felügyelt lemezes lemezkép sablonja): A használni kívánt felügyelt lemezkép azonosítója
   * **Rendszergazdai Felhasználónév** és **rendszergazdai jelszó**: A Felhasználónév és a jelszó.

     A rendszer létrehoz egy új felhasználót a virtuális gépre való bejelentkezéshez.
   * **Új vagy meglévő alhálózat**: Meghatározza, hogy a rendszer létrehoz-e új virtuális hálózatot és alhálózatot, vagy meglévő alhálózatot használ-e. Ha már van olyan virtuális hálózata, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő**lehetőséget.
   * **ALHÁLÓZAT azonosítója**: Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában a következőképpen néz ki:&lt;/Subscriptions/előfizetés azonosítója >&lt;/resourceGroups/erőforráscsoport neve >/Providers/Microsoft.Network/virtualNetworks/&lt;virtuális hálózat neve >/Subnets/&lt;alhálózat neve >

1. **Feltételek és**kikötések:  
    Tekintse át és fogadja el a jogi feltételeket.

1. Válassza a **Beszerzés** lehetőséget.

#### <a name="install-the-vm-agent-linux-only"></a>A VM-ügynök telepítése (csak Linux)

Az előző szakaszban ismertetett sablonok használatához a Linux-ügynöknek már telepítve kell lennie a felhasználói rendszerképben, vagy a telepítés sikertelen lesz. Töltse le és telepítse a virtuálisgép-ügynököt a felhasználói rendszerképbe az [Azure VM-ügynök letöltése, telepítése és engedélyezése][deployment-guide-4.4]című témakörben leírtak szerint. Ha nem használja a sablonokat, később is telepítheti a virtuálisgép-ügynököt.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)

Ha az Azure-beli üzembe helyezése egy helyszíni Active Directory vagy DNS-példányhoz egy Azure-helyek közötti VPN-kapcsolaton vagy Azure [Virtual Machines ExpressRoute keresztül történik NetWeaver][planning-guide]) a rendszer azt várta, hogy a virtuális gép egy helyszíni tartományhoz csatlakozik. További információ a lépésekkel kapcsolatos megfontolásokról: a [virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

A helyszíni hálózat konfigurációjától függően előfordulhat, hogy a proxyt be kell állítania a virtuális gépen. Ha a virtuális gép VPN-vagy ExpressRoute-kapcsolaton keresztül csatlakozik a helyszíni hálózathoz, előfordulhat, hogy a virtuális gép nem fér hozzá az internethez, és nem fogja tudni letölteni a szükséges kiterjesztéseket, illetve nem gyűjti a figyelési adatokat. További információ: [configure The proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Figyelés konfigurálása

Ahhoz, hogy az SAP támogassa a környezetet, állítsa be az Azure monitoring bővítményt az SAP-hez az SAP-hez készült [Azure Enhanced monitoring bővítmény konfigurálása][deployment-guide-4.5]című cikkben leírtak szerint. Győződjön meg az SAP-figyelés előfeltételeiről és az SAP kernel és az SAP Host Agent szükséges minimális verzióiról az SAP- [erőforrások][deployment-guide-2.2]listájában felsorolt erőforrásokban.

#### <a name="monitoring-check"></a>Figyelés ellenőrzése

Ellenőrizze, hogy működik-e a figyelés, lásd: [ellenőrzések és hibaelhárítás a végpontok közötti figyelés beállításához][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>3. forgatókönyv: Helyszíni virtuális gép áthelyezése egy nem általánosított Azure VHD-vel az SAP használatával

Ebben a forgatókönyvben egy adott SAP-rendszer áthelyezését tervezi egy helyszíni környezetből az Azure-ba. Ezt úgy teheti meg, hogy feltölti a virtuális merevlemezt, amely az operációs rendszer, az SAP bináris fájljai, és végül az adatbázis-kezelő bináris fájljai, valamint a virtuális merevlemezek és az adatbázis-kezelő naplófájlja az Azure-ba kerül. A 2. [forgatókönyvben ismertetett forgatókönyvtől eltérően: Virtuális gép üzembe helyezése az SAP][deployment-guide-3.3]-hez készült egyéni rendszerképekkel, ebben az esetben az állomásnév, az SAP SID és az SAP felhasználói fiókok megmaradnak az Azure-beli virtuális gépen, mivel azok a helyszíni környezetben lettek konfigurálva. Nem kell általánosítani az operációs rendszert. Ez a forgatókönyv leggyakrabban a létesítmények közötti forgatókönyvekre vonatkozik, ahol az SAP-környezet egy része a helyszínen fut, és egy része az Azure-ban fut.

Ebben az esetben a virtuálisgép-ügynököt **nem** telepíti automatikusan a rendszer az üzembe helyezés során. Mivel a VM-ügynök és az SAP-hez készült Azure Enhanced monitoring bővítmény szükséges az SAP NetWeaver Azure-beli futtatásához, a virtuális gép létrehozása után manuálisan kell letöltenie, telepítenie és engedélyeznie mindkét összetevőt.

Az Azure-beli virtuálisgép-ügynökkel kapcsolatos további információkért tekintse meg a következő forrásokat.

---
> ![Windows][Logo_Windows] Windows
>
> [Az Azure Virtual Machine Agent áttekintése][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Az Azure Linux-ügynök felhasználói útmutatója][virtual-machines-linux-agent-user-guide]
>
>

---

A következő folyamatábra a helyszíni virtuális gépek nem általánosított Azure VHD használatával történő áthelyezésének lépéseit mutatja be:

![Virtuálisgép-telepítés folyamatábrája SAP-rendszerekhez virtuálisgép-lemez használatával][deployment-guide-figure-400]

Ha a lemez már fel van töltve és definiálva van az Azure-ban (lásd: az [azure Virtual Machines tervezése és implementálása az SAP NetWeaver][planning-guide]-ben), végezze el a következő néhány szakaszban leírt feladatokat.

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ha a Azure Portal használatával szeretne létrehozni egy központi telepítést, használja az [Azure-Gyorsindítás-templates GitHub][azure-quickstart-templates-github]-tárházban közzétett SAP-sablont. A PowerShell használatával manuálisan is létrehozhat virtuális gépeket.

* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon** (SAP-2-réteg-felhasználó-lemez)][sap-templates-2-tier-os-disk]

  Ha csak egy virtuális géppel szeretné létrehozni a kétrétegű rendszereket, használja ezt a sablont.
* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon által felügyelt lemez** (SAP-2-réteg-felhasználó-lemez-MD)][sap-templates-2-tier-os-disk-md]

  Ha csak egy virtuális géppel és felügyelt lemezzel szeretne létrehozni egy kétrétegű rendszerrendszert, használja ezt a sablont.

A Azure Portal adja meg a következő paramétereket a sablonhoz:

1. **Alapvető beállítások**:
   * **Előfizetés**: A sablon üzembe helyezéséhez használt előfizetés.
   * **Erőforráscsoport**: A sablon üzembe helyezéséhez használt erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévő erőforráscsoportot az előfizetésben.
   * **Hely**: A sablon üzembe helyezésének helye. Ha kiválasztott egy meglévő erőforráscsoportot, az adott erőforráscsoport helyét fogja használni.
1. **Beállítások**:
   * **SAP**-rendszerazonosító: Az SAP rendszerazonosító.
   * **Operációs rendszer típusa**: A telepíteni kívánt operációs rendszer típusa (Windows vagy Linux).
   * **SAP**-rendszerméret: Az SAP-szolgáltatás mérete.

     Az új rendszerek SAP-száma. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
   * **Tárolási típus** (csak kétrétegű sablon): A használandó tároló típusa.

     Nagyobb rendszerek esetében ajánlott az Azure Premium Storage használata. A tárolási típusokkal kapcsolatos további információkért tekintse meg a következő forrásokat:
      * [Az Azure prémium SSD Storage használata az SAP adatbázis-kezelő példányához][2367194]
      * [Egy virtuális gép tárolási szerkezete RDBMS üzemelő példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Nagy teljesítményű tárterület az Azure-beli virtuális gépek számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
   * **Operációsrendszer-lemez VHD URI-ja** (csak nem felügyelt sablon): A magánhálózati operációsrendszer-lemez URI-ja, például https://&lt;accountname >. blob. Core. Windows. net/VHD/osdisk. vhd.
   * **Operációsrendszer-lemez felügyelt lemezének azonosítója** (csak felügyelt lemez sablonja): A felügyelt lemez operációsrendszer-lemezének azonosítója,/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Új vagy meglévő alhálózat**: Meghatározza, hogy az új virtuális hálózat és alhálózat létrehozása megtörtént-e, vagy egy meglévő alhálózat van-e használatban. Ha már van olyan virtuális hálózata, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő**lehetőséget.
   * **ALHÁLÓZAT azonosítója**: Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában a következőképpen néz ki:&lt;/Subscriptions/előfizetés azonosítója >&lt;/resourceGroups/erőforráscsoport neve >/Providers/Microsoft.Network/virtualNetworks/&lt;virtuális hálózat neve >/Subnets/&lt;alhálózat neve >

1. **Feltételek és**kikötések:  
    Tekintse át és fogadja el a jogi feltételeket.

1. Válassza a **Beszerzés** lehetőséget.

#### <a name="install-the-vm-agent"></a>A virtuálisgép-ügynök telepítése

Az előző szakaszban ismertetett sablonok használatához a virtuálisgép-ügynököt az operációsrendszer-lemezen kell telepíteni, vagy a telepítés sikertelen lesz. Töltse le és telepítse a virtuálisgép-ügynököt a virtuális gépen az [Azure VM-ügynök letöltése, telepítése és engedélyezése][deployment-guide-4.4]című témakörben leírtak szerint.

Ha nem az előző szakaszban leírt sablonokat használja, akkor a virtuálisgép-ügynököt később is telepítheti.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)

Ha az Azure-beli üzembe helyezése egy helyszíni Active Directory vagy DNS-példányhoz egy Azure-helyek közötti VPN-kapcsolaton vagy [Virtual Machines ExpressRoute keresztül történik NetWeaver][planning-guide]) a rendszer azt várta, hogy a virtuális gép egy helyszíni tartományhoz csatlakozik. További információ a feladattal kapcsolatos megfontolásokról: [virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

A helyszíni hálózat konfigurációjától függően előfordulhat, hogy a proxyt be kell állítania a virtuális gépen. Ha a virtuális gép VPN-vagy ExpressRoute-kapcsolaton keresztül csatlakozik a helyszíni hálózathoz, előfordulhat, hogy a virtuális gép nem fér hozzá az internethez, és nem fogja tudni letölteni a szükséges kiterjesztéseket, illetve nem gyűjti a figyelési adatokat. További információ: [configure The proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Figyelés konfigurálása

Ahhoz, hogy az SAP támogassa a környezetet, állítsa be az Azure monitoring bővítményt az SAP-hez az SAP-hez készült [Azure Enhanced monitoring bővítmény konfigurálása][deployment-guide-4.5]című cikkben leírtak szerint. Győződjön meg az SAP-figyelés előfeltételeiről és az SAP kernel és az SAP Host Agent szükséges minimális verzióiról az SAP- [erőforrások][deployment-guide-2.2]listájában felsorolt erőforrásokban.

#### <a name="monitoring-check"></a>Figyelés ellenőrzése

Ellenőrizze, hogy működik-e a figyelés, lásd: [ellenőrzések és hibaelhárítás a végpontok közötti figyelés beállításához][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Az SAP figyelési konfigurációjának frissítése

Frissítse az SAP-figyelés konfigurációját a következő esetekben:
* A közös Microsoft/SAP-csapat bővíti a figyelési képességeket, és több vagy kevesebb számlálót kér.
* A Microsoft bevezeti a mögöttes Azure-infrastruktúra új verzióját, amely a figyelési adatokat tartalmazza, az SAP-hez készült Azure Enhanced monitoring bővítmény pedig alkalmazkodik a változásokhoz.
* További adatlemezeket csatlakoztathat az Azure-beli virtuális géphez, vagy eltávolít egy adatlemezt. Ebben az esetben frissítse a Storage-hoz kapcsolódó adat gyűjteményét. A konfiguráció a végpontok hozzáadásával vagy törlésével, illetve az IP-címek virtuális géphez való hozzárendelésével történő módosítása nem befolyásolja a figyelési konfigurációt.
* Megváltoztathatja az Azure-beli virtuális gép méretét, például az A5 méretet bármely más VM-méretre.
* Új hálózati adaptereket adhat hozzá az Azure-beli virtuális géphez.

A figyelési beállítások frissítéséhez frissítse a figyelési infrastruktúrát az [Azure bővített figyelési bővítmény konfigurálása SAP][deployment-guide-4.5]-hoz című témakör lépéseit követve.

## <a name="detailed-tasks-for-sap-software-deployment"></a>Az SAP-szoftverek központi telepítésének részletes feladatai

Ez a szakasz részletesen ismerteti a konfigurációs és központi telepítési folyamat egyes feladatainak végrehajtását.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell-parancsmagok üzembe helyezése

1. Ugrás [Microsoft Azure letöltésekre](https://azure.microsoft.com/downloads/).
1. A **parancssori eszközök**alatt, a **PowerShell**alatt válassza a **Windows telepítés**lehetőséget.
1. A letöltött fájlhoz (például WindowsAzurePowershellGet. 3F. 3F. 3fnew. exe) a Microsoft Download Manager párbeszédpanelen válassza a **Futtatás**lehetőséget.
1. A Microsoft webplatform-telepítő (Microsoft Web PI) futtatásához válassza az **Igen**lehetőséget.
1. Az alábbihoz hasonló oldal jelenik meg:

   ![Azure PowerShell-parancsmagok telepítési lapja][deployment-guide-figure-500]<a name="figure-5"></a>

1. Válassza a **telepítés**lehetőséget, majd fogadja el a Microsoft szoftverlicenc-feltételeit.
1. A PowerShell telepítve van. A telepítővarázsló bezárásához kattintson a **Befejezés** gombra.

Rendszeresen keressen a PowerShell-parancsmagok frissítéseire, amelyek általában havonta frissülnek. A frissítések keresésének legegyszerűbb módja az előző telepítési lépések végrehajtása, amely az 5. lépésben látható telepítési lapra mutat. A parancsmagok kiadási és kiadási száma az 5. lépésben látható oldalon található. Ha nincs megadva a [1928533] -es SAP-Megjegyzés vagy a [2015553]-es SAP-Megjegyzés, javasoljuk, hogy működjön az Azure PowerShell-parancsmagok legújabb verziójával.

A számítógépen telepített Azure PowerShell-parancsmagok verziójának vizsgálatához futtassa a következő PowerShell-parancsot:
```powershell
(Get-Module Az.Compute).Version
```
Az eredmény így néz ki:

![Azure PowerShell parancsmag verziójának ellenőrzésének eredménye][deployment-guide-figure-600]
<a name="figure-6"></a>

Ha a számítógépre telepített Azure-parancsmag verziója a jelenlegi verzió, a telepítővarázsló első lapja azt jelzi, hogy hozzáadja **(telepített)** a termék címéhez (lásd a következő képernyőképet). A PowerShell Azure-parancsmagjai naprakészek. A telepítővarázsló bezárásához kattintson a **Kilépés**elemre.

![Azure PowerShell-parancsmagok telepítési lapja, amely azt jelzi, hogy a Azure PowerShell-parancsmagok legújabb verziója van telepítve.][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Az Azure CLI üzembe helyezése

1. Ugrás [Microsoft Azure letöltésekre](https://azure.microsoft.com/downloads/).
1. Az **Azure parancssori felület**alatti **parancssori eszközök**területen válassza ki az operációs rendszerének **telepítési** hivatkozását.
1. A letöltött fájlhoz (például WindowsAzureXPlatCLI. 3F. 3F. 3fnew. exe) a Microsoft Download Manager párbeszédpanelen válassza a **Futtatás**lehetőséget.
1. A Microsoft webplatform-telepítő (Microsoft Web PI) futtatásához válassza az **Igen**lehetőséget.
1. Az alábbihoz hasonló oldal jelenik meg:

   ![Azure PowerShell-parancsmagok telepítési lapja][deployment-guide-figure-500]<a name="figure-5"></a>

1. Válassza a **telepítés**lehetőséget, majd fogadja el a Microsoft szoftverlicenc-feltételeit.
1. Az Azure CLI telepítve van. A telepítővarázsló bezárásához kattintson a **Befejezés** gombra.

Rendszeresen keressen frissítéseket az Azure CLI-hez, amely általában havonta frissül. A frissítések keresésének legegyszerűbb módja az előző telepítési lépések végrehajtása, amely az 5. lépésben látható telepítési lapra mutat.

A számítógépen telepített Azure CLI verziójának vizsgálatához futtassa a következő parancsot:
```
azure --version
```

Az eredmény így néz ki:

![Az Azure CLI-verzió ellenőrzésének eredménye][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)

Ha olyan helyszíni környezetben helyez üzembe SAP virtuális gépeket, ahol a helyszíni Active Directory és a DNS ki van bővítve az Azure-ban, akkor a virtuális gépeknek a helyszíni tartományhoz kell csatlakozniuk. A virtuális gépek helyszíni tartományhoz való csatlakoztatásának részletes lépései, valamint a helyszíni tartomány tagjaként szükséges további szoftverek az ügyfelektől függően változnak. A virtuális gépek helyszíni tartományhoz való csatlakoztatása általában további szoftvereket, például kártevő szoftvereket és biztonsági mentési vagy figyelési szoftvereket kell telepítenie.

Ebben az esetben meg kell győződnie arról, hogy ha az internetes proxy beállításai kényszerítve vannak, amikor egy virtuális gép egy tartományhoz csatlakozik a környezetben, akkor a vendég virtuális gépen található Windows helyi rendszerfióknak (S-1-5-18) ugyanaz a proxybeállításokat kell megadnia. A legegyszerűbb lehetőség, ha a proxyt egy tartományi Csoportházirend használatával kényszeríti, amely a tartományban lévő rendszerekre vonatkozik.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Az Azure VM-ügynök letöltése, telepítése és engedélyezése

Olyan virtuális gépek esetén, amelyek nem általánosított operációsrendszer-lemezképből vannak telepítve (például egy olyan rendszerkép, amely nem a Windows rendszer-előkészítés vagy a Sysprep eszközből származik), manuálisan kell letöltenie, telepítenie és engedélyeznie az Azure VM-ügynököt.

Ha az Azure Marketplace-en helyez üzembe egy virtuális gépet, ez a lépés nem szükséges. Az Azure Marketplace-ről származó rendszerképek már rendelkeznek Azure VM-ügynökkel.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Töltse le az Azure VM-ügynököt:
   1.  Töltse le az Azure virtuálisgép- [ügynök telepítőcsomagot](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  A virtuálisgép-ügynök MSI-csomagját helyileg tárolja egy személyes számítógépen vagy kiszolgálón.
1. Az Azure VM-ügynök telepítése:
   1.  Kapcsolódjon az üzembe helyezett Azure-beli virtuális géphez RDP protokoll (RDP) használatával.
   1.  Nyisson meg egy Windows Intéző ablakot a virtuális gépen, és válassza ki a virtuálisgép-ügynök MSI-fájljához tartozó cél könyvtárat.
   1.  Húzza az Azure virtuálisgép-ügynök telepítőjének MSI-fájlját a helyi számítógépről/kiszolgálóról a virtuálisgép-ügynök cél könyvtárába a virtuális gépen.
   1.  Kattintson duplán az MSI-fájlra a virtuális gépen.
1. A helyszíni tartományokhoz csatlakoztatott virtuális gépek esetén győződjön meg arról, hogy a végleges internetes proxybeállítások a virtuális gépen a Windows helyi rendszerfiókra (S-1-5-18) is érvényesek, a [proxy konfigurálása][deployment-guide-configure-proxy]című cikkben leírtak szerint. A virtuálisgép-ügynök ebben a kontextusban fut, és képesnek kell lennie az Azure-hoz való kapcsolódásra.

Az Azure VM-ügynök frissítéséhez nincs szükség felhasználói beavatkozásra. A virtuálisgép-ügynök automatikusan frissül, és nem igényli a virtuális gép újraindítását.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

A Linux rendszerhez készült virtuálisgép-ügynök telepítéséhez használja az alábbi parancsokat:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) vagy Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Ha az ügynök már telepítve van, az Azure Linux-ügynök frissítéséhez hajtsa végre a következő témakörben ismertetett lépéseket: az [Azure Linux-ügynök frissítése a virtuális gépen a legújabb verzióra][virtual-machines-linux-update-agent]a githubról.

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>A proxy konfigurálása

A proxy Windowson történő konfigurálásának lépései eltérnek a proxy Linux rendszerben történő konfigurálásának módjától.

#### <a name="windows"></a>Windows

A proxybeállításokat helyesen kell beállítani ahhoz, hogy a helyi rendszerfiók hozzáférjen az internethez. Ha a proxybeállításokat nem a Csoportházirend állítja be, beállíthatja a helyi rendszerfiók beállításait.

1. Lépjen a **Start menüre**, írja be a **gpedit. msc parancsot**, majd kattintson az **ENTER**gombra.
1. Válassza a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **Windows-összetevők** > **Internet Explorer**lehetőséget. Győződjön meg arról, hogy a **Proxybeállítások beállítása számítógépenkénti (nem felhasználónként)** beállítás le van tiltva vagy nincs konfigurálva.
1. A **Vezérlőpulton**lépjen a **hálózati és megosztási központ** > **internetes beállítások**elemre.
1. A **kapcsolatok** lapon kattintson a LAN- **Beállítások** gombra.
1. Törölje a **beállítások automatikus észlelése** jelölőnégyzet jelölését.
1. Jelölje be a **proxykiszolgáló használata a helyi hálózaton** jelölőnégyzetet, majd adja meg a proxy címe és a port mezőt.
1. Kattintson a **speciális** gombra.
1. A **kivételek** mezőben adja meg az IP- **168.63.129.16**. Kattintson az **OK** gombra.

#### <a name="linux"></a>Linux

Konfigurálja a helyes proxyt a Microsoft Azure vendég ügynök konfigurációs fájljába, amely a (z) \\etc\\waagent. conf fájlban található.

Állítsa be a következő paramétereket:

1. **Http-proxy gazdagépe**. Állítsa be például a következőt: **proxy. Corp. local**.
   ```
   HttpProxy.Host=<proxy host>

   ```
1. **Http-proxy portja**. Állítsa be például a **80**értékre.
   ```
   HttpProxy.Port=<port of the proxy host>

   ```
1. Indítsa újra az ügynököt.

   ```
   sudo service waagent restart
   ```

A\\waagent. conf \\fájlban megadott proxybeállítások a szükséges virtuálisgép-bővítményekre is érvényesek. Ha az Azure-adattárakat szeretné használni, győződjön meg arról, hogy az ezekre a tárházokra irányuló forgalom nem halad át a helyszíni intraneten. Ha a kényszerített bújtatás engedélyezéséhez felhasználó által megadott útvonalakat hozott létre, akkor ügyeljen arra, hogy olyan útvonalat adjon hozzá, amely közvetlenül az internetre irányítja át a forgalmat, és nem a két hálózat közötti pont-pont típusú VPN-kapcsolaton keresztül.

* **SLES**

  Emellett útvonalakat kell hozzáadnia az IP-címekhez, \\az\\etc regionserverclnt. cfg használatával. Az alábbi ábrán egy példa látható:

  ![Alagúthasználat kényszerítése][deployment-guide-figure-50]


* **RHEL**

  Emellett útvonalakat is hozzá kell adnia a következő cikkben felsorolt gazdagépek IP- \\címeihez:\\yum. Repos\\. d rhui-Load-Balancer. Példaként tekintse meg az előző ábrát.

* **Oracle Linux**

  Az Azure-ban nincsenek Oracle Linux adattárak. Saját adattárakat kell konfigurálnia a Oracle Linuxhoz, vagy a nyilvános adattárakat kell használnia.

A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Az SAP-hez készült Azure Enhanced monitoring bővítmény konfigurálása

Ha előkészítette a virtuális gépet az Azure-beli virtuális [gépek üzembe helyezési forgatókönyvei][deployment-guide-3]című témakörben leírtak szerint, az Azure-beli virtuálisgép-ügynök telepítve van a virtuális gépen. A következő lépés az SAP-hez készült Azure Enhanced monitoring bővítmény üzembe helyezése, amely a globális Azure-adatközpontokban érhető el az Azure Extension adattárában. További információ: [Azure Virtual Machines tervezése és implementálása az SAP NetWeaver-][planning-guide-9.1]ban.

A PowerShell vagy az Azure CLI használatával telepítheti és konfigurálhatja az SAP-hez készült Azure Enhanced monitoring bővítményt. Ha Windows-vagy Linux-alapú virtuális gépen szeretné telepíteni a bővítményt, tekintse meg a következőt: [Azure PowerShell][deployment-guide-4.5.1]. Ha Linux rendszerű asztali gépen szeretné telepíteni a bővítményt Linux rendszerű virtuális gépre, tekintse meg az [Azure CLI][deployment-guide-4.5.2]-t.

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell Linux és Windows rendszerű virtuális gépekhez

Az Azure Enhanced monitoring bővítmény telepítése az SAP-hez a PowerShell használatával:

1. Győződjön meg arról, hogy telepítette a Azure PowerShell parancsmag legújabb verzióját. További információ: Azure PowerShell- [parancsmagok telepítése][deployment-guide-4.1].  
1. Futtassa az alábbi PowerShell-parancsmagot.
    Az elérhető környezetek listájáért futtassa a `commandlet Get-AzEnvironment`parancsot. Ha globális Azure-t szeretne használni, a környezet **AzureCloud**. Az Azure-ban Kínában válassza a **AzureChinaCloud**lehetőséget.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

A fiókadatok megadása és az Azure-beli virtuális gép azonosítása után a parancsfájl telepíti a szükséges bővítményeket, és engedélyezi a szükséges szolgáltatásokat. Ez több percet is igénybe vehet.
További információ `Set-AzVMAEMExtension`: [set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Az SAP-specifikus Azure-parancsmag-AzVMAEMExtension sikeres végrehajtása][deployment-guide-figure-900]

A `Set-AzVMAEMExtension` konfiguráció a gazdagép figyelésének SAP-hez való konfigurálásának lépéseit is végrehajtja.

A parancsfájl kimenete a következő információkat tartalmazza:

* Erősítse meg, hogy az operációs rendszer lemezének és az összes további adatlemeznek van beállítva a figyelése.
* A következő két üzenet megerősíti egy adott Storage-fiók tárolási metrikáinak konfigurációját.
* Az egyik kimeneti sor a figyelési konfiguráció tényleges frissítésének állapotát adja meg.
* A kimenet egy másik sora megerősíti, hogy a konfiguráció telepítve van vagy frissült.
* A kimenet utolsó sora tájékoztató. Megjeleníti a figyelési konfiguráció tesztelésének lehetőségeit.
* Annak ellenőrzéséhez, hogy az Azure bővített monitorozásának összes lépése sikeresen befejeződött-e, és hogy az Azure-infrastruktúra biztosítja a szükséges adatmennyiséget, folytassa az SAP-hez készült Azure Enhanced monitoring bővítmény készültségi ellenőrzésével, a következő témakörben leírtak szerint: [ A készültségi ellenőrzés az SAP-hez készült Azure][deployment-guide-5.1]-beli továbbfejlesztett figyeléssel.
* Várjon 15-30 percet, amíg Azure Diagnostics a kapcsolódó adatok gyűjtésére.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI Linux rendszerű virtuális gépekhez

Az Azure Enhanced monitoring bővítmény telepítése az SAP-hez az Azure CLI használatával:

   1. Telepítse a klasszikus Azure CLI-t a [klasszikus Azure CLI telepítése][azure-cli]című témakörben leírtak szerint.
   1. Jelentkezzen be az Azure-fiókjával:

      ```
      azure login
      ```

   1. Váltson Azure Resource Manager módra:

      ```
      azure config mode arm
      ```

   1. Az Azure Enhanced monitoring engedélyezése:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Telepítés az Azure CLI 2,0-vel

   1. Telepítse az Azure CLI 2,0-et az [Azure cli 2,0 telepítése][azure-cli-2]című témakörben leírtak szerint.
   1. Jelentkezzen be az Azure-fiókjával:

      ```
      az login
      ```

   1. Az Azure CLI AEM bővítmény telepítése
  
      ```
      az extension add --name aem
      ```
  
   1. A bővítmény telepítése a
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Ellenőrizze, hogy az Azure Enhanced monitoring bővítmény aktív-e az Azure Linux rendszerű virtuális gépen. Győződjön meg arról, \\hogy\\a fájl\\var lib\\AzureEnhancedMonitor PerfCounters létezik. Ha létezik, a parancssorban futtassa ezt a parancsot az Azure Enhanced monitor által gyűjtött információk megjelenítéséhez:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   A kimenet így néz ki:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>A végpontok közötti figyelés ellenőrzése és hibaelhárítása

Miután üzembe helyezte az Azure-beli virtuális gépet, és beállította a megfelelő Azure monitoring-infrastruktúrát, győződjön meg arról, hogy az Azure Enhanced monitoring bővítmény összes összetevője a várt módon működik-e.

Futtassa a készültségi ellenőrzést az SAP-hez készült Azure Enhanced monitoring bővítményhez, az SAP-hez készült [Azure Enhanced monitoring bővítmény készültségi ellenőrzése][deployment-guide-5.1]című témakörben leírtak szerint. Ha az összes készültségi ellenőrzés eredménye pozitív, és az összes kapcsolódó teljesítményszámláló rendben jelenik meg, az Azure-figyelés sikeresen be lett állítva. Az SAP-gazdagép ügynökének telepítését az SAP- [erőforrások][deployment-guide-2.2]SAP-megjegyzések című részében leírtak szerint folytathatja. Ha a készültségi ellenőrzés azt jelzi, hogy a számlálók hiányoznak, futtassa az Azure monitoring-infrastruktúra állapot-ellenőrzését az [Azure figyelési infrastruktúra konfigurációjának állapot-ellenőrzése][deployment-guide-5.2]című témakörben leírtak szerint. További hibaelhárítási lehetőségek: az [Azure Monitoring for SAP hibaelhárítása][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Az SAP-hez készült Azure Enhanced monitoring bővítmény készültségének ellenőrzése

Ez az ellenőrzés biztosítja, hogy az SAP-alkalmazáson belül megjelenő összes teljesítménymutatót a mögöttes Azure monitoring-infrastruktúra biztosítja.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>A készültség-ellenőrzési szolgáltatás futtatása Windows rendszerű virtuális gépen

1. Jelentkezzen be az Azure-beli virtuális gépre (a rendszergazdai fiók használata nem szükséges).
1. Nyisson meg egy parancssori ablakot.
1. A parancssorban módosítsa a könyvtárat az SAP bővített Azure-figyelési bővítményének telepítési mappájába: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop

   A figyelési bővítmény elérési útjának *verziója* eltérő lehet. Ha a figyelési bővítmény több verziójának mappáit látja a telepítési mappában, ellenőrizze a AzureEnhancedMonitoring Windows-szolgáltatás konfigurációját, majd váltson a *végrehajtható fájl elérési útjaként*megjelölt mappára.

   ![Az SAP-hez készült Azure Enhanced monitoring bővítményt futtató szolgáltatás tulajdonságai][deployment-guide-figure-1000]

1. A parancssorban a **azperflib. exe** parancsot paraméterek nélkül futtassa.

   > [!NOTE]
   > A Azperflib. exe ciklusban fut, és 60 másodpercenként frissíti az összegyűjtött számlálókat. A hurok befejezéséhez zárjuk be a parancssorablakot.
   >
   >

Ha az Azure Enhanced monitoring bővítmény nincs telepítve, vagy a AzureEnhancedMonitoring szolgáltatás nem fut, a bővítmény nincs megfelelően konfigurálva. A bővítmény üzembe helyezésével kapcsolatos részletes információkért lásd: [Az Azure figyelési infrastruktúrájának hibaelhárítása az SAP-hez][deployment-guide-5.3].

> [!NOTE]
> A Azperflib. exe olyan összetevő, amely nem használható saját célra. Ez egy olyan összetevő, amely az SAP-gazdagép ügynökének virtuális géphez kapcsolódó Azure-figyelési adatait biztosítja.
> 

##### <a name="check-the-output-of-azperflibexe"></a>A azperflib. exe kimenetének keresése

A Azperflib. exe kimenetben az összes kitöltött Azure-teljesítményszámláló megjelenik az SAP számára. Az összegyűjtött számlálók listájának alján az összefoglalás és az állapotjelző az Azure-figyelés állapotát jeleníti meg.

![Az állapot-ellenőrzés kimenete a azperflib. exe futtatásával, amely azt jelzi, hogy nem léteznek problémák][deployment-guide-figure-1100]
<a name="figure-11"></a>

Az előző ábrán látható módon tekintse meg a **számlálók összes** kimenetének eredményét, amely üresként van jelezve, valamint az **állapot állapotaként**.

Az eredményül kapott értékeket a következőképpen értelmezheti:

| Azperflib. exe eredmény értékei | Az Azure monitorozási állapotának állapota |
| --- | --- |
| **API-hívások – nem érhető el** | Előfordulhat, hogy a nem elérhető számlálók nem alkalmazhatók a virtuális gép konfigurációjához, vagy hibásak. **Állapot**megtekintése. |
| **Összes számláló – üres** |A következő két Azure Storage-számláló lehet üres: <ul><li>Tárolás olvasási op késési kiszolgáló MS</li><li>Tárolási olvasási op késés E2E MS</li></ul>Minden más számlálónak értékkel kell rendelkeznie. |
| **Állapot** |Csak az OK, ha a visszatérési állapot **az OK gombra**mutat. |
| **Diagnosztika** |Az állapottal kapcsolatos részletes információk. |

Ha az állapot értéke nem megfelelő,kövesse az állapot-ellenőrzés az [Azure-figyelési infrastruktúra konfigurációjának][deployment-guide-5.2]utasításait.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>A készültségi vizsgálat futtatása Linux rendszerű virtuális gépen

1. Csatlakozzon az Azure-beli virtuális géphez az SSH használatával.

1. Az Azure Enhanced monitoring bővítmény kimenetének ellenőrzése.

   a.  Futtassa a `more /var/lib/AzureEnhancedMonitor/PerfCounters` parancsot.

   **Várt eredmény**: A teljesítményszámlálók listáját adja vissza. A fájl nem lehet üres.

   b. Futtassa a `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` parancsot.

   **Várt eredmény**: Egy olyan sort ad vissza, ahola hiba nem, például: **3; config; Hiba;; 0; 0; nincs; 0; 1456416792; TST-servercs;**

   c. Futtassa a `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` parancsot.

   **Várt eredmény**: Üresként tér vissza, vagy nem létezik.

Ha az előző ellenőrzés sikertelen volt, futtassa a következő további ellenőrzéseket:

1. Győződjön meg arról, hogy a waagent telepítve és engedélyezve van.

   a.  Futtassa a `sudo ls -al /var/lib/waagent/` parancsot.

     **Várt eredmény**: A waagent könyvtár tartalmának felsorolása.

   b.  Futtassa a `ps -ax | grep waagent` parancsot.

   **Várt eredmény**: Egy, a következőhöz hasonló bejegyzést jelenít meg:`python /usr/sbin/waagent -daemon`

1. Győződjön meg arról, hogy az Azure Enhanced monitoring bővítmény telepítve van és fut.

   a.  Futtassa a `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` parancsot.

   **Várt eredmény**: Felsorolja az Azure Enhanced monitoring bővítmény könyvtárának tartalmát.

   b. Futtassa a `ps -ax | grep AzureEnhanced` parancsot.

   **Várt eredmény**: Egy, a következőhöz hasonló bejegyzést jelenít meg:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Telepítse az SAP-gazdagép ügynököt az SAP Note [1031096]-as verziójában `saposcol`leírtak szerint, és ellenőrizze a kimenetét.

   a.  Futtassa a `/usr/sap/hostctrl/exe/saposcol -d` parancsot.

   b.  Futtassa a `dump ccm` parancsot.

   c.  Győződjön meg arról, hogy a **Virtualization_Configuration\Enhanced-figyelési hozzáférési** metrika értéke **true (igaz**).

Ha már telepítve van egy SAP NetWeaver ABAP-alkalmazáskiszolgáló, nyissa meg a tranzakció ST06, és ellenőrizze, hogy engedélyezve van-e a fokozott figyelés.

Ha ezek közül bármelyik ellenőrzés meghiúsul, és részletes információkat talál a bővítmény újratelepítéséről, olvassa el [Az Azure figyelési infrastruktúrájának hibaelhárítása az SAP-hez][deployment-guide-5.3]című témakört.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Az Azure figyelési infrastruktúra konfigurációjának állapot-ellenőrzése

Ha a figyelési információk némelyike nem megfelelően jelenik meg, ahogy az az [Azure bővített monitorozásának készültség-ellenőrzése][deployment-guide-5.1]című témakörben leírtak szerint `Test-AzVMAEMExtension` , futtassa a parancsmagot annak ellenőrzéséhez, hogy az Azure monitoring-infrastruktúra és a figyelés az SAP-bővítmény helyesen van konfigurálva.

1. Győződjön meg arról, hogy telepítette a Azure PowerShell parancsmag legújabb verzióját a [Azure PowerShell parancsmagok telepítése][deployment-guide-4.1]című cikkben leírtak szerint.
1. Futtassa az alábbi PowerShell-parancsmagot. Az elérhető környezetek listájához futtassa a parancsmagot `Get-AzEnvironment`. A globális Azure használatához válassza ki a **AzureCloud** -környezetet. Az Azure-ban Kínában válassza a **AzureChinaCloud**lehetőséget.
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Adja meg a fiókadatok adatait, és azonosítsa az Azure-beli virtuális gépet.

   ![Az SAP-specifikus Azure cmdlet test-VMConfigForSAP_GUI bemeneti lapja][deployment-guide-figure-1200]

1. A parancsfájl ellenőrzi a kiválasztott virtuális gép konfigurációját.

   ![Az SAP-hez készült Azure monitoring-infrastruktúra sikeres tesztelésének kimenete][deployment-guide-figure-1300]

Győződjön meg arról, hogy minden állapot-ellenőrzési eredmény **rendben**van. Ha egyes ellenőrzések nem jelenítik meg az **OK gombot**, futtassa a frissítési parancsmagot az SAP-hoz készült [Azure Enhanced monitoring bővítmény konfigurálása][deployment-guide-4.5]című részben leírtak szerint. Várjon 15 percet, és ismételje meg az Azure- [figyelési infrastruktúra konfigurálására][deployment-guide-5.2]vonatkozó készültségi ellenőrzés az Azure-ban [bővített figyelése az SAP][deployment-guide-5.1] -hez és az állapot-ellenőrzéshez című témakört. Ha az ellenőrzések továbbra is problémát jeleznek egy vagy több számlálóval kapcsolatban, tekintse [meg az SAP-hez készült Azure monitoring Infrastructure hibaelhárítása][deployment-guide-5.3]című témakört.

> [!Note]
> Bizonyos figyelmeztetések olyan esetekben is előfordulhatnak, amikor felügyelt szabványos Azure-lemezeket használ. A figyelmeztetések az "OK" kifejezés helyett a tesztek után jelennek meg. Ez normális, és a lemez típusa esetén szükséges. Lásd még: [Az Azure figyelési infrastruktúrájának hibaelhárítása az SAP][deployment-guide-5.3] -hoz
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Az SAP-hez készült Azure monitoring-infrastruktúra hibaelhárítása

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Az Azure-teljesítményszámlálók egyáltalán nem jelennek meg

A AzureEnhancedMonitoring Windows-szolgáltatás a teljesítmény-mérőszámokat gyűjti az Azure-ban. Ha a szolgáltatás nem lett megfelelően telepítve, vagy ha nem fut a virtuális gépen, akkor nem gyűjthetők teljesítmény mérőszámok.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Az Azure Enhanced monitoring bővítmény telepítési könyvtára üres

###### <a name="issue"></a>Probléma

A telepítési könyvtár C:\\Packages\\\\plugins Microsoft. AzureCAT. AzureEnhancedMonitoring.\\AzureCATExtensionHandler&lt;Version\\> drop üres.

###### <a name="solution"></a>Megoldás

A bővítmény nincs telepítve. Állapítsa meg, hogy ez egy proxy-probléma (a korábban leírtak szerint). Előfordulhat, hogy újra kell indítania a számítógépet, `Set-AzVMAEMExtension` vagy újra kell futtatnia a konfigurációs parancsfájlt.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Nem létezik az Azure bővített figyelésére szolgáló szolgáltatás.

###### <a name="issue"></a>Probléma

A AzureEnhancedMonitoring Windows-szolgáltatás nem létezik.

A Azperflib. exe kimenete hibát jelez:

![A azperflib. exe futtatása azt jelzi, hogy az SAP-hez készült Azure Enhanced monitoring Extension szolgáltatás nem fut][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Megoldás

Ha a szolgáltatás nem létezik, az SAP-hez készült Azure Enhanced monitoring bővítmény nem lett megfelelően telepítve. Telepítse újra a bővítményt a központi telepítési forgatókönyvben ismertetett lépések segítségével az [Azure-ban az SAP-hez készült virtuális gépek üzembe helyezési forgatókönyvei][deployment-guide-3]között.

A bővítmény üzembe helyezése után egy óra elteltével újra ellenőriznie kell, hogy az Azure-teljesítményszámlálók az Azure-beli virtuális gépen vannak-e megadva.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Az Azure bővített monitorozási szolgáltatása létezik, de nem indul el

###### <a name="issue"></a>Probléma

A AzureEnhancedMonitoring Windows-szolgáltatás létezik, és engedélyezve van, de nem indul el. További információért olvassa el az alkalmazás eseménynaplóját.

###### <a name="solution"></a>Megoldás

A konfiguráció helytelen. Indítsa újra a virtuális gép figyelési bővítményét a következő témakörben leírtak szerint: [Az Azure Enhanced monitoring bővítmény konfigurálása az SAP][deployment-guide-4.5]-hoz.

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Egyes Azure-teljesítményszámlálók hiányoznak

A AzureEnhancedMonitoring Windows-szolgáltatás a teljesítmény-mérőszámokat gyűjti az Azure-ban. A szolgáltatás különböző forrásokból származó adatokkal rendelkezik. Bizonyos konfigurációs adatokat a rendszer helyileg gyűjt, és egyes teljesítmény-metrikák beolvasása Azure Diagnostics. A tárolási számlálókat a rendszer a tárolási előfizetés szintjén használja a naplózásban.

Ha a [1999351] -es SAP-Megjegyzés használatával végzett hibaelhárítás nem oldja meg `Set-AzVMAEMExtension` a problémát, futtassa újra a konfigurációs parancsfájlt. Előfordulhat, hogy várnia kell egy órát, mert a Storage Analytics vagy a diagnosztikai számlálók nem hozhatók létre azonnal, miután engedélyezve lettek. Ha a probléma továbbra is fennáll, nyisson meg egy SAP-ügyfélszolgálati üzenetet a BC-OP-NT-AZR for Windows vagy BC-OP-LNX-AZR összetevővel Linux rendszerű virtuális gépen.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Az Azure-teljesítményszámlálók egyáltalán nem jelennek meg

Az Azure-ban a teljesítmény-mérőszámokat egy démon gyűjti. Ha a démon nem fut, a teljesítmény-mérőszámok nem gyűjthetők össze.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Az Azure Enhanced monitoring bővítmény telepítési könyvtára üres

###### <a name="issue"></a>Probléma

A \ \\\\\\\\waagent\\ könyvtár nem rendelkezik alkönyvtárral az Azure Enhanced monitoring bővítményhez.

###### <a name="solution"></a>Megoldás

A bővítmény nincs telepítve. Állapítsa meg, hogy ez egy proxy-probléma (a korábban leírtak szerint). Előfordulhat, hogy újra kell indítania a számítógépet, és/ `Set-AzVMAEMExtension` vagy újra kell futtatnia a konfigurációs parancsfájlt.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A set-AzVMAEMExtension és a test-AzVMAEMExtension végrehajtásával figyelmeztető üzenetek jelennek meg, amelyek szerint a standard Managed Disks nem támogatottak

###### <a name="issue"></a>Probléma

A set-AzVMAEMExtension vagy a test-AzVMAEMExtension üzenetek a következőhöz hasonló módon történő végrehajtásakor láthatók:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

A azperfli. exe futtatása a korábban leírtak szerint a nem kifogástalan állapotot jelző eredményt kaphat. 

###### <a name="solution"></a>Megoldás

Az üzeneteket az a tény okozza, hogy a standard Managed Disks nem biztosítanak a figyelési bővítmény által használt API-kat a standard Azure Storage-fiókok statisztikáinak ellenőrzéséhez. Ez nem jelent problémát. A standard Disk Storage fiókok figyelésének bevezetésének oka a gyakran előforduló I/o-szabályozás volt. A felügyelt lemezek a Storage-fiókban lévő lemezek számának korlátozásával elkerülhetők az ilyen szabályozások. Ezért az ilyen típusú megfigyelési adattípusok nem kritikus fontosságúak.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Egyes Azure-teljesítményszámlálók hiányoznak

Az Azure-beli teljesítmény-mérőszámokat egy démon gyűjti, amely több forrásból származó adatokat kap. Bizonyos konfigurációs adatokat a rendszer helyileg gyűjt, és egyes teljesítmény-metrikák beolvasása Azure Diagnostics. A tárolási számlálók a tároló-előfizetés naplóiból származnak.

Az ismert problémák teljes és naprakész listáját a [1999351]-es SAP-Megjegyzés tartalmazza, amely további hibaelhárítási információkat tartalmaz az SAP-hez készült továbbfejlesztett Azure-figyeléshez.

Ha a [1999351] -es SAP-Megjegyzés használatával végzett hibaelhárítás nem oldja meg a `Set-AzVMAEMExtension` problémát, futtassa újra a konfigurációs parancsfájlt az [SAP-hez készült Azure Enhanced monitoring bővítmény konfigurálása][deployment-guide-4.5]című részben leírtak szerint. Előfordulhat, hogy várnia kell egy órát, mert a Storage Analytics vagy a diagnosztikai számlálók nem hozhatók létre azonnal, miután engedélyezve lettek. Ha a probléma továbbra is fennáll, nyisson meg egy SAP-ügyfélszolgálati üzenetet a BC-OP-NT-AZR for Windows vagy BC-OP-LNX-AZR összetevővel Linux rendszerű virtuális gépen.
