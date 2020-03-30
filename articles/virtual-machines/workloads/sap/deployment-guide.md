---
title: Az Azure virtuális gépek telepítése az SAP NetWeaver számára | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az SAP-szoftvereket Linuxos virtuális gépeken az Azure-ban.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 7fb87380047d046a580d1ad62b1d7107a94bb297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239891"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Az Azure virtuális gépek üzembe helyezése az SAP NetWeaver számára

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
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Gyorsítótárazás virtuális gépekhez és virtuális gépekhez)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (SzoftverES RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure tárhely)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RdBMS-telepítés szerkezete)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Magas rendelkezésre állás és vészhelyreállítás az Azure virtuális gépeivel)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 és újabb verziói)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 és korábbi kiadások)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (SQL Server-lemezkép használata az Azure Piactérről)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Általános SQL Server for SAP az Azure összegzésén)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Az SQL Server RDBMS sajátosságai)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Tároló konfigurációja)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Biztonsági mentés és visszaállítás)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (A biztonsági mentés és visszaállítás teljesítményével kapcsolatos szempontok)
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

[deployment-guide]:deployment-guide.md (Az Azure virtuális gépek üzembe helyezése az SAP-hoz)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-erőforrások)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Virtuális gép telepítése egyéni lemezkép használatával)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (1. forgatókönyv: Virtuális gép üzembe helyezése az Azure Marketplace-ről az SAP-hoz)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (2. forgatókönyv: Virtuális gép üzembe helyezése az SAP egyéni lemezképével)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (3. forgatókönyv: Virtuális gép áthelyezése a helyszíni nem általános Azure-virtuális merevlemez használatával az SAP-val)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Az SAP virtuális gépeinek telepítési forgatókönyvei a Microsoft Azure-ban)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell-parancsmagok üzembe helyezése)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP-szempontjából releváns PowerShell-parancsmagok letöltése és importálása)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Virtuális gép csatlakoztatása helyszíni tartományhoz – csak Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Az Azure VM-ügynök letöltése, telepítése és engedélyezése)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Az Azure-bővítmény konfigurálása az SAP-hoz)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Az SAP-hoz való Azure Extension felkészültségi ellenőrzése)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Állapot-ellenőrzés az Azure-bővítmény sap-konfigurációhoz)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Az Azure-bővítmény SAP-hoz történő hibaelhárítása)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Virtuálisgép-bővítmény konfigurálása)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Az SAP-gazdaügynök végpontok között történő adatgyűjtésének ellenőrzése és hibaelhárítása)

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

[planning-guide]:planning-guide.md (Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Erőforrások)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Magas rendelkezésre állás és vészhelyreállítás az Azure virtuális gépeken futó SAP NetWeaver számára)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Az SAP alkalmazáskiszolgálók magas rendelkezésre állása)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Az Automatikus indítás használata SAP-példányokhoz)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Csak felhőalapú – Virtuálisgép-telepítések az Azure-ban a helyszíni ügyfélhálózattól való függőség nélkül)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Telephelyek közötti – Egy vagy több SAP-virtuális gép üzembe helyezése az Azure-ban teljes mértékben integrálva van a helyszíni hálózattal)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-régiók)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Tartalék tartományok)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Tartományok frissítése)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Az Azure rendelkezésre állási készletei)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (A Microsoft Azure virtuális gépek koncepciója)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure prémium szintű tárhely)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Virtuális gép áthelyezése a helyszíni környezetből az Azure-ba egy nem általános lemezzel)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Virtuális gép üzembe helyezése ügyfélspecifikus lemezképpel)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Felkészülés a virtuális gépek helyszíni és azure-beli, nem általános lemezes áthelyezésére)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Felkészülés egy virtuális gép üzembe helyezéséhez egy ügyfélspecifikus lemezképpel az SAP számára)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Virtuális gépek előkészítése az Sap-val az Azure-hoz)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Az Azure-lemez és az Azure-lemezkép közötti különbség)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Virtuális merevlemez feltöltése a helyszíni azure-ból)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Lemezek másolása az Azure Storage-fiókok között)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Virtuális gép/vhd struktúra az SAP-telepítésekhez)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Csatlakoztatott lemezek automatikus csatlakoztatásának beállítása)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Egyetlen virtuális gép az SAP NetWeaver bemutató/betanítási forgatókönyvvel)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Az SAP-példányok csak felhőalapú telepítésének fogalmai)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure figyelési megoldás az SAP-hoz)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure prémium szintű tárhely)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Felügyelt lemezek)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure-hálózatkezelés)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Tárolás: Microsoft Azure Storage és adatlemezek)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-termék rendelkezésre állási mátrixa)
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
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Virtuális gépek üzembe helyezése és kezelése az Azure Resource Manager-sablonok és az Azure CLI használatával)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Virtuális gépek kezelése az Azure Resource Manager és a PowerShell használatával)
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

Az Azure Virtual Machines olyan szervezetek számára megoldás, amelyeknek minimális idő alatt és hosszadalmas beszerzési ciklusok nélkül van szükségük számítási és tárolási erőforrásokra. Az Azure virtuális gépekkel klasszikus alkalmazásokat, például SAP NetWeaver-alapú alkalmazásokat telepíthet az Azure-ban. Bővítse ki az alkalmazás megbízhatóságát és rendelkezésre állását további helyszíni erőforrások nélkül. Az Azure virtual machines támogatja a létesítmények közötti kapcsolatot, így integrálhatja az Azure virtuális gépeket a szervezet helyszíni tartományaiba, a magánfelhőkbe és az SAP-rendszerkörnyezetbe.

Ebben a cikkben ismerteti az SAP-alkalmazások üzembe helyezésének lépéseit az Azure-beli virtuális gépeken, beleértve az alternatív üzembe helyezési lehetőségeket és a hibaelhárítást. Ez a cikk az [Sap NetWeaver virtuális gépek tervezésének és megvalósításának azure-beli virtuális gépekben][planning-guide]található információkra épül. Kiegészíti az SAP telepítési dokumentációját és az SAP Notes-t is, amelyek az SAP-szoftverek telepítésének és telepítésének elsődleges forrásai.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Az Azure virtuális gép beállítása az SAP-szoftverek üzembe helyezéséhez több lépésés erőforrás is magában foglalja. Mielőtt elkezdené, győződjön meg arról, hogy megfelel az SAP-szoftverek azure-beli virtuális gépeken történő telepítésének előfeltételeinek.

### <a name="local-computer"></a>Helyi számítógép

A Windows vagy linuxos virtuális gépek kezeléséhez használhatja a PowerShell-parancsfájlt és az Azure Portalt. Mindkét eszközhöz Windows 7 vagy a Windows egy újabb verzióját futtató helyi számítógépre van szükség. Ha csak Linux virtuális gépeket szeretne kezelni, és ehhez a feladathoz Linux-számítógépet szeretne használni, használhatja az Azure CLI-t.

### <a name="internet-connection"></a>Internetkapcsolat

Az SAP-szoftverek központi telepítéséhez szükséges eszközök és parancsfájlok letöltéséhez és futtatásához csatlakoznia kell az internethez. Az Azure virtuális gép, amely az Sap-bővítményt futtató is szüksége van az internet-hozzáférésre. Ha az Azure virtuális gép egy Azure virtuális hálózat vagy helyszíni tartomány része, győződjön meg arról, hogy a megfelelő proxybeállítások be vannak állítva, [a proxy konfigurálása][deployment-guide-configure-proxy]című részben leírtak szerint.

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-előfizetés

Aktív Azure-fiókra van szüksége.

### <a name="topology-and-networking"></a>Topológia és hálózatépítés

Meg kell határoznia az SAP-telepítés tantanát és architektúráját az Azure-ban:

* Használandó Azure storage-fiókok
* Virtuális hálózat, ahol telepíteni szeretné az SAP-rendszert
* Erőforráscsoport, amelyre telepíteni szeretné az SAP-rendszert
* Az Azure-régió, ahol telepíteni szeretné az SAP-rendszert
* SAP-konfiguráció (kétrétegű vagy háromrétegű)
* Virtuális gép méreteés a virtuális gépekhez csatlakoztatandó további adatlemezek száma
* SAP javító és közlekedési rendszer (CTS) konfiguráció

Az SAP-szoftvertelepítési folyamat megkezdése előtt hozzon létre és konfiguráljon Azure storage-fiókokat (ha szükséges) vagy az Azure virtuális hálózatokat. Az erőforrások létrehozásáról és konfigurálásáról az [Azure Virtual Machines tervezése és megvalósítása az SAP NetWeaver számára][planning-guide]című témakörben talál tájékoztatást.

### <a name="sap-sizing"></a>SAP méretezése

Ismerje meg a következő információkat az SAP méretezéséhez:

* Tervezett SAP-munkaterhelés, például az SAP Quick Sizer eszköz és az SAP-alkalmazás teljesítményszabvány (SAPS) számával
* Az SAP rendszer szükséges CPU-erőforrás- és memóriafelhasználása
* Szükséges bemeneti/kimeneti (I/O) műveletek másodpercenként
* Az Azure-beli virtuális gépek közötti esetleges kommunikáció szükséges hálózati sávszélessége
* Szükséges hálózati sávszélesség a helyszíni eszközök és az Azure által telepített SAP-rendszer között

### <a name="resource-groups"></a>Erőforráscsoportok

Az Azure Resource Managerben erőforráscsoportok használatával kezelheti az Azure-előfizetésében lévő összes alkalmazás-erőforrást. További információk: [Azure Resource Manager overview][resource-group-overview] (Az Azure Resource Manager áttekintése).

## <a name="resources"></a>Források

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-erőforrások

Az SAP-szoftvertelepítés beállításakor a következő SAP-erőforrásokra van szüksége:

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuális gépméretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban

* Az SAP Note [2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [1409604] rendelkezik a szükséges SAP Host Agent windowsos verzióval az Azure-ban.
* Az SAP Note [2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1984787] általános információkat tartalmaz a SUSE Linux Enterprise Server 12-ről.
* Az SAP Note [2002167] általános információkat tartalmaz a Red Hat Enterprise Linux 7.x-ről.
* Az SAP Note [2069760] általános információkat tartalmaz az Oracle Linux 7.x-ről.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* Az SAP Note [1597355] általános információkat tartalmaz a Linux-csereterületről.
* [Az SAP az Azure SCN-lapon](https://wiki.scn.sap.com/wiki/x/Pia7Gg) híreket és hasznos erőforrások gyűjteményét rendelkezik.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden szükséges SAP-jegyzetet igényel Linuxhoz.
* Az [Azure PowerShell][azure-ps]részét tartalmazó SAP-specifikus PowerShell-parancsmagokkal.
* Az [Azure CLI][azure-cli]részét tartalmazó SAP-specifikus Azure CLI-parancsok.

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-erőforrások

Ezek a Microsoft-cikkek az Azure-beli SAP-telepítéseket fedik le:

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver számára][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP NetWeaver számára (ez a cikk)][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése az SAP NetWeaver számára][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Üzembe helyezési forgatókönyvek az SAP-szoftverekhez az Azure virtuális gépeken

Több lehetőség közül választhat a virtuális gépek és a kapcsolódó lemezek azure-ban való üzembe helyezéséhez. Fontos megérteni a központi telepítési lehetőségek közötti különbségeket, mert előfordulhat, hogy különböző lépéseket tesz a virtuális gépek üzembe helyezésre való előkészítéséhez a választott központi telepítés alapján.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>1. forgatókönyv: Virtuális gép üzembe helyezése az Azure Marketplace-ről az SAP-hoz

Használhatja a Microsoft által biztosított lemezképet, vagy egy harmadik fél az Azure Marketplace-en a virtuális gép üzembe helyezéséhez. A Piactér a Windows Server és a különböző Linux-disztribúciók szabványos operációsrendszer-lemezképeit kínálja. Adatbázis-kezelő rendszer (DBMS) termékkiszolgálást, például Microsoft SQL Server rendszert is tartalmazó lemezképet is telepíthet. A lemezképek DBMS skus-okkal való használatáról az [Azure Virtual Machines DBMS deployment for SAP NetWeaver című][dbms-guide]témakörben talál további információt.

A következő folyamatábra a virtuális gép Azure Piactérről történő üzembe helyezésére vonatkozó LÉPÉSEK SAP-specifikus sorozatát mutatja be:

![Virtuálisgép-üzembe helyezés folyamatábrája az SAP-rendszerekhez az Azure Marketplace-ről származó virtuálisgép-lemezkép használatával][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portal használatával

Az Azure Piactérről egy lemezképpel rendelkező új virtuális gép létrehozásának legegyszerűbb módja az Azure Portal használata.

1.  Nyissa meg a következőt: <https://portal.azure.com/#create/hub>.  Vagy válassza az Azure Portal menüben a **+ Új**lehetőséget.
1.  Válassza **a Számítás**lehetőséget, majd válassza ki a telepíteni kívánt operációs rendszer típusát. Például Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) vagy Oracle Linux 7.2. Az alapértelmezett listanézet nem jeleníti meg az összes támogatott operációs rendszert. A teljes lista megtekintéséhez jelölje be az **összes** megtekintése jelölőnégyzetet. Az SAP-szoftverek telepítéséhez támogatott operációs rendszerekről az SAP [1928533]megjegyzése című témakörben talál további információt.
1.  A következő oldalon tekintse át a felhasználási feltételeket.
1.  A **Telepítési modell kiválasztása** mezőben válassza az **Erőforrás-kezelő**lehetőséget.
1.  Kattintson a **Létrehozás** gombra.

A varázsló végigvezeti a virtuális gép létrehozásához szükséges paraméterek beállításán, az összes szükséges erőforráson, például a hálózati csatolókon és a tárfiókokon kívül. Néhány ezek a paraméterek a következők:

1. **Alapok:**
   * **Név**: Az erőforrás neve (a virtuális gép neve).
   * **Virtuálisgép-lemez típusa**: Válassza ki az operációs rendszer lemezének típusát. Ha prémium szintű tárhelyet szeretne használni az adatlemezekhez, javasoljuk, hogy az operációsrendszer-lemezhez is használja a Prémium szintű storage-ot.
   * **Felhasználónév és jelszó** vagy **SSH nyilvános kulcs**: Adja meg a kiépítés során létrehozott felhasználó felhasználónevét és jelszavát. Linuxos virtuális gép esetén megadhatja a nyilvános Secure Shell (SSH) kulcsot, amelyet a gépre való bejelentkezéshez használ.
   * **Előfizetés**: Válassza ki az új virtuális gép kiépítéséhez használni kívánt előfizetést.
   * **Erőforráscsoport**: A virtuális gép erőforráscsoportjának neve. Megadhat egy új erőforráscsoport nevét vagy egy már létező erőforráscsoport nevét.
   * **Hely**: Az új virtuális gép üzembe helyezése. Ha a virtuális gépet a helyszíni hálózathoz szeretné csatlakoztatni, győződjön meg arról, hogy kiválasztja az Azure-t a helyszíni hálózathoz összekötő virtuális hálózat helyét. További információt a [Microsoft Azure hálózati szolgáltatás][planning-guide-microsoft-azure-networking] az Azure virtuális gépek tervezése és [megvalósítása az SAP NetWeaver alkalmazásban][planning-guide]című témakörben talál.
1. **Size** (Méret):

     A támogatott virtuálisgép-típusok listáját az SAP [Note 1928533]megjegyzéstartalmazza. Győződjön meg arról, hogy válassza ki a megfelelő virtuális gép típusát, ha az Azure Premium Storage-t szeretné használni. Nem minden virtuálisgép-típus támogatja a prémium szintű storage-ot. További információ: [Storage: Microsoft Azure Storage and data disks][planning-guide-storage-microsoft-azure-storage-and-data-disks] and [Azure Premium Storage][planning-guide-azure-premium-storage] in Azure Virtual Machines planning and implementation for SAP [NetWeaver][planning-guide].

1. **Beállítások**:
   * **Tárterület**
     * **Lemeztípusa**: Válassza ki az operációs rendszer lemezének típusát. Ha prémium szintű tárhelyet szeretne használni az adatlemezekhez, javasoljuk, hogy az operációsrendszer-lemezhez is használja a Prémium szintű storage-ot.
     * **Felügyelt lemezek használata:** Ha felügyelt lemezeket szeretne használni, válassza az Igen lehetőséget. A felügyelt lemezekről a tervezési [útmutatóban][planning-guide-managed-disks] található Felügyelt lemezek című témakörben olvashat bővebben.
     * **Tárfiók:** Válasszon ki egy meglévő tárfiókot, vagy hozzon létre egy újat. Nem minden tárolási típus működik az SAP-alkalmazások futtatásához. A tárolótípusokról további információt az [RDBMS-telepítésekhez szükséges virtuális gép tárolási struktúrája című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
   * **Hálózat**
     * **Virtuális hálózat** és **alhálózat:** A virtuális gép intranettel való integrálásához válassza ki a helyszíni hálózathoz csatlakoztatott virtuális hálózatot.
     * **Nyilvános IP-cím:** Válassza ki a használni kívánt nyilvános IP-címet, vagy adjon meg paramétereket egy új nyilvános IP-cím létrehozásához. A virtuális gép interneten keresztül történő eléréséhez nyilvános IP-címet használhat. Győződjön meg arról, hogy hozzon létre egy hálózati biztonsági csoportot a virtuális gép biztonságos eléréséhez.
     * **Hálózati biztonsági csoport**: További információ: [A hálózati forgalom szabályozása hálózati biztonsági csoportokkal.][virtual-networks-nsg]
   * **Bővítmények**: Virtuálisgép-bővítményeket telepíthet, ha hozzáadja őket a központi telepítéshez. Ebben a lépésben nem kell bővítményeket hozzáadnia. Az SAP-támogatáshoz szükséges bővítmények telepítése később lesz telepítve. Lásd: [Az Azure-bővítmény konfigurálása az SAP-hoz][deployment-guide-4.5] ebben az útmutatóban.
   * **Magas rendelkezésre állás:** Válasszon ki egy rendelkezésre állási csoportot, vagy adja meg a paramétereket egy új rendelkezésre állási csoport létrehozásához. További információ: [Azure availability sets.][planning-guide-3.2.3]
   * **Megfigyelő**
     * **Rendszerindítási diagnosztika**: A **letiltás** lehetőséget választhatja a rendszerindítási diagnosztikához.
     * **Vendég operációs rendszer diagnosztikája:** **Kiválaszthatja a letiltása** a diagnosztika figyeléséhez.

1. **Összegzés**:

   Tekintse át a beállításokat, majd kattintson **az OK gombra.**

A virtuális gép a kiválasztott erőforráscsoportban van telepítve.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Virtuális gép létrehozása sablon használatával

Virtuális gépet hozhat létre az [azure-quickstart-templates GitHub-tárházban közzétett SAP-sablonok][azure-quickstart-templates-github]egyikével. Az [Azure Portal][virtual-machines-windows-tutorial], a [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]vagy az [Azure CLI][virtual-machines-linux-tutorial]használatával manuálisan is létrehozhat virtuális gépet.

* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Ha csak egy virtuális gép használatával szeretne kétrétegű rendszert létrehozni, használja ezt a sablont.
* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon – Felügyelt lemezek** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Ha kétrétegű rendszert szeretne létrehozni egyetlen virtuális gép és felügyelt lemezek használatával, használja ezt a sablont.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Ha háromrétegű rendszert szeretne létrehozni több virtuális gép használatával, használja ezt a sablont.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon – Felügyelt lemezek** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Ha háromrétegű rendszert szeretne létrehozni több virtuális gép és felügyelt lemezek használatával, használja ezt a sablont.

Az Azure Portalon adja meg a következő paramétereket a sablonhoz:

1. **Alapok:**
   * **Előfizetés**: A sablon üzembe helyezéséhez használt előfizetés.
   * **Erőforráscsoport**: A sablon telepítéséhez használandó erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévő erőforráscsoportot az előfizetésben.
   * **Hely**: A sablon üzembe helyezése. Ha meglévő erőforráscsoportot választott, a rendszer az adott erőforráscsoport helyét használja.

1. **Beállítások**:
   * **SAP rendszerazonosító:** az SAP rendszerazonosító (SID).
   * **Operációs rendszer típusa**: A telepíteni kívánt operációs rendszer például Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) vagy Oracle Linux 7.2.

     A listanézetben nem található az összes támogatott operációs rendszer. Az SAP-szoftverek telepítéséhez támogatott operációs rendszerekről az SAP [1928533]megjegyzése című témakörben talál további információt.
   * **SAP-rendszer mérete:** Az SAP-rendszer mérete.

     Az új rendszer által biztosított SAPS-ek száma. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
   * **A rendszer rendelkezésre állása** (csak háromszintű sablon): A rendszer rendelkezésre állása.

     Válassza a **HA** lehetőséget egy olyan konfigurációhoz, amely alkalmas a magas rendelkezésre állású telepítéshez. Két adatbázis-kiszolgáló és két kiszolgáló jön létre az ABAP SAP Central Services (ASCS) számára.
   * **Tárolási típus** (csak kétrétegű sablon): A használandó tároló típusa.

     Nagyobb rendszerek esetén erősen javasoljuk az Azure Premium Storage használatát. A tárolótípusokról az alábbi forrásokban talál további információt:
      * [Az Azure Premium SSD storage használata az SAP DBMS-példányhoz][2367194]
      * [Virtuális gép tárolási struktúrája rdbms-telepítésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Prémium szintű tárhely: Nagy teljesítményű tárolás az Azure virtuális gép számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
   * **Rendszergazdai felhasználónév** és **rendszergazdai jelszó**: Felhasználónév és jelszó.
     Új felhasználó jön létre a virtuális gépre való bejelentkezéshez.
   * **Új vagy meglévő alhálózat:** Azt határozza meg, hogy új virtuális hálózat ot és alhálózatot hoznak-e létre, vagy egy meglévő alhálózatot használnak. Ha már rendelkezik olyan virtuális hálózattal, amely a helyszíni hálózathoz csatlakozik, válassza a **Létező**lehetőséget.
   * **Alhálózati azonosító:** Ha a virtuális gép egy meglévő virtuális hálózatba szeretné telepíteni, ahol a virtuális gép hez definiált alhálózat ot kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában így néz ki:&lt;/subscriptions/subscription id>/resourceGroups/&lt;resource group name&lt;>/providers/Microsoft.Network/virtualNetworks/ virtual network name>/subnets/&lt;subnets name>

1. **Általános szerződési feltételek:**  
    Tekintse át és fogadja el a jogi feltételeket.

1. Válassza a **Beszerzés** lehetőséget.

Az Azure virtuálisgép-ügynök alapértelmezés szerint üzembe helyezése, ha az Azure Marketplace-ről egy lemezképet használ.

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Attól függően, hogy a helyszíni hálózat konfigurálva van, előfordulhat, hogy be kell állítania a proxyt a virtuális gépen. Ha a virtuális gép VPN-en vagy ExpressRoute-on keresztül csatlakozik a helyszíni hálózathoz, előfordulhat, hogy a virtuális gép nem tudja elérni az internetet, és nem tudja letölteni a szükséges virtuálisgép-bővítményeket, és nem tudja összegyűjteni az SAP-gazdaügynök Azure-infrastruktúra adatait az SAP-bővítményen keresztül az Azure-hoz. További információt [a Proxy konfigurálása][deployment-guide-configure-proxy]című témakörben talál.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)

Ha az Azure-központi telepítés egy helyszíni Active Directory vagy DNS-példány egy Azure-hely közötti VPN-kapcsolaton vagy ExpressRoute-on keresztül csatlakozik (ez az [SAP NetWeaver azure-beli virtuális gépek tervezésének és megvalósításának][planning-guide] *több helyiségen belüli* szolgáltatása), várható, hogy a virtuális gép csatlakozik egy helyszíni tartományhoz. A feladattal kapcsolatos szempontokról további információt a [Virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)][deployment-guide-4.3]című témakörben talál.

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Virtuálisgép-bővítmény konfigurálása

Győződjön meg arról, hogy az SAP támogatja a környezetet, állítsa be az Azure Extension for SAP az Azure Extension for SAP című részben [leírtak][deployment-guide-4.5]szerint. Ellenőrizze az SAP előfeltételeit, valamint az SAP Kernel és az SAP host agent szükséges minimális verzióit az [SAP-erőforrásokban][deployment-guide-2.2]felsorolt erőforrásokban.

#### <a name="vm-extension-for-sap-check"></a>Virtuálisgép-bővítmény az SAP-ellenőrzéshez

Ellenőrizze, hogy az SAP [virtuálisgép-bővítménye működik-e a Csekkek és az SAP gazdagép-ügynök végpontok között történő adatgyűjtéssel kapcsolatos hibaelhárítás][deployment-guide-troubleshooting-chapter]című témakörben leírtak szerint.

#### <a name="post-deployment-steps"></a>A telepítés utáni lépések

Miután létrehozta a virtuális gép, és a virtuális gép telepítése, telepítenie kell a szükséges szoftverösszetevőket a virtuális gép. Az ilyen típusú virtuálisgép-telepítés központi telepítési/szoftvertelepítési sorrendje miatt a telepítendő szoftvernek már elérhetőnek kell lennie, akár az Azure-ban, akár egy másik virtuális gépen, vagy egy csatolható lemezként. Vagy fontolja meg egy több helyiségből álló forgatókönyv használatát, amelyben a helyszíni eszközökhöz (telepítési megosztásokhoz) való kapcsolódás meg van adva.

Miután üzembe helyezte a virtuális gép az Azure-ban, kövesse ugyanazokat az irányelveket és eszközöket az SAP-szoftver telepítéséhez a virtuális gép, mint egy helyszíni környezetben. Sap-szoftver telepítése egy Azure virtuális gép, mind az SAP és a Microsoft azt javasolja, hogy töltse fel és tárolja az SAP telepítési adathordozót az Azure VDs vagy a felügyelt lemezek, vagy hozzon létre egy Azure virtuális gép, amely működik, mint egy fájlkiszolgáló, amely rendelkezik az összes szükséges SAP telepítési adathordozót.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>2. forgatókönyv: Virtuális gép üzembe helyezése az SAP egyéni lemezképével

Mivel az operációs rendszer vagy az ADATBÁZIS-rendszer különböző verziói eltérő javítási követelményekkel rendelkeznek, előfordulhat, hogy az Azure Marketplace-en található lemezképek nem felelnek meg az igényeinek. Ehelyett érdemes lehet létrehozni egy virtuális gépet a saját OS/DBMS virtuális géplemezkép használatával, amely később újra üzembe helyezhető.
Különböző lépéseket használ, hogy hozzon létre egy privát lemezképet a Linux, mint létrehozni egy Windows.You use different steps to create a private image for Linux than to create one for Windows.

---
> ![Windows][Logo_Windows] Windows
>
> Több virtuális gép központi telepítéséhez használható Windows-lemezkép előkészítéséhez a Windows-beállításokat (például a Windows BIZTONSÁGI azonosítóját és állomásnevét) a helyszíni virtuális gépen kell elalkalmazni vagy általánosítani. Ehhez használhatja a [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) programot.
>
> ![Linux][Logo_Linux] Linux
>
> Egy Linux-lemezkép előkészítése, amely több virtuális gép üzembe helyezéséhez használható, néhány Linux-beállításokat kell absztrakt vagy általánosítani a helyszíni virtuális gépen. Ezt használhatja. `waagent -deprovision` További információ: [Capture a Linux virtuális gép azure-on futó][virtual-machines-linux-capture-image] és az [Azure Linux ügynök felhasználói útmutató.][virtual-machines-linux-agent-user-guide-command-line-options]
>
>

---
Előkészítheti és létrehozhat egy egyéni lemezképet, majd több új virtuális gépet hozhat létre. Ezt az [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása][planning-guide]ismerteti. Állítsa be az adatbázis tartalmát az SAP Software Provisioning Manager használatával egy új SAP-rendszer telepítéséhez (adatbázis-biztonsági mentés visszaállítása a virtuális géphez csatlakoztatott lemezről), vagy közvetlenül visszaállítja az adatbázis biztonsági mentését az Azure storage-ból, ha a DBMS támogatja azt. További információ: [Azure Virtual Machines DBMS deployment for SAP NetWeaver][dbms-guide]. Ha már telepített egy SAP-rendszert a helyszíni virtuális gépen (különösen kétrétegű rendszerek esetében), módosíthatja az SAP rendszerbeállításait az Azure virtuális gép üzembe helyezése után az SAP szoftverkiépítési kezelője által támogatott rendszerátnevezési eljárás (SAP [Note 1619720)]használatával. Ellenkező esetben telepítheti az SAP-szoftvert az Azure virtuális gép üzembe helyezése után.

A következő folyamatábra a virtuális gép egyéni lemezképből történő üzembe helyezésének SAP-specifikus folyamatait mutatja be:

![A virtuális gép üzembe helyezésének folyamatábrája az SAP-rendszerekhez virtuálisgép-lemezkép használatával a privát Piactérben][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portal használatával

A felügyelt lemezről egy új virtuális gép létrehozásának legegyszerűbb módja az Azure Portal használatával. A Lemezlemezlemez-kezelő lemezkép kezeléséről további információt az [Általános gép felügyelt lemezképének rögzítése az Azure-ban című](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) részben talál.

1.  Nyissa meg a következőt: <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Vagy az Azure Portal menüben válassza a **Képek**lehetőséget.
1.  Jelölje ki az telepíteni kívánt felügyelt lemezlemez-lemezlemez-lemezképet, és kattintson a **Virtuálisgép létrehozása elemre.**

A varázsló végigvezeti a virtuális gép létrehozásához szükséges paraméterek beállításán, az összes szükséges erőforráson, például a hálózati csatolókon és a tárfiókokon kívül. Néhány ezek a paraméterek a következők:

1. **Alapok:**
   * **Név**: Az erőforrás neve (a virtuális gép neve).
   * **Virtuálisgép-lemez típusa**: Válassza ki az operációs rendszer lemezének típusát. Ha prémium szintű tárhelyet szeretne használni az adatlemezekhez, javasoljuk, hogy az operációsrendszer-lemezhez is használja a Prémium szintű storage-ot.
   * **Felhasználónév és jelszó** vagy **SSH nyilvános kulcs**: Adja meg a kiépítés során létrehozott felhasználó felhasználónevét és jelszavát. Linuxos virtuális gép esetén megadhatja a nyilvános Secure Shell (SSH) kulcsot, amelyet a gépre való bejelentkezéshez használ.
   * **Előfizetés**: Válassza ki az új virtuális gép kiépítéséhez használni kívánt előfizetést.
   * **Erőforráscsoport**: A virtuális gép erőforráscsoportjának neve. Megadhat egy új erőforráscsoport nevét vagy egy már létező erőforráscsoport nevét.
   * **Hely**: Az új virtuális gép üzembe helyezése. Ha a virtuális gépet a helyszíni hálózathoz szeretné csatlakoztatni, győződjön meg arról, hogy kiválasztja az Azure-t a helyszíni hálózathoz összekötő virtuális hálózat helyét. További információt a [Microsoft Azure hálózati szolgáltatás][planning-guide-microsoft-azure-networking] az Azure virtuális gépek tervezése és [megvalósítása az SAP NetWeaver alkalmazásban][planning-guide]című témakörben talál.
1. **Size** (Méret):

     A támogatott virtuálisgép-típusok listáját az SAP [Note 1928533]megjegyzéstartalmazza. Győződjön meg arról, hogy válassza ki a megfelelő virtuális gép típusát, ha az Azure Premium Storage-t szeretné használni. Nem minden virtuálisgép-típus támogatja a prémium szintű storage-ot. További információ: [Storage: Microsoft Azure Storage and data disks][planning-guide-storage-microsoft-azure-storage-and-data-disks] and [Azure Premium Storage][planning-guide-azure-premium-storage] in Azure Virtual Machines planning and implementation for SAP [NetWeaver][planning-guide].

1. **Beállítások**:
   * **Tárterület**
     * **Lemeztípusa**: Válassza ki az operációs rendszer lemezének típusát. Ha prémium szintű tárhelyet szeretne használni az adatlemezekhez, javasoljuk, hogy az operációsrendszer-lemezhez is használja a Prémium szintű storage-ot.
     * **Felügyelt lemezek használata:** Ha felügyelt lemezeket szeretne használni, válassza az Igen lehetőséget. A felügyelt lemezekről a tervezési [útmutatóban][planning-guide-managed-disks] található Felügyelt lemezek című témakörben olvashat bővebben.
   * **Hálózat**
     * **Virtuális hálózat** és **alhálózat:** A virtuális gép intranettel való integrálásához válassza ki a helyszíni hálózathoz csatlakoztatott virtuális hálózatot.
     * **Nyilvános IP-cím:** Válassza ki a használni kívánt nyilvános IP-címet, vagy adjon meg paramétereket egy új nyilvános IP-cím létrehozásához. A virtuális gép interneten keresztül történő eléréséhez nyilvános IP-címet használhat. Győződjön meg arról, hogy hozzon létre egy hálózati biztonsági csoportot a virtuális gép biztonságos eléréséhez.
     * **Hálózati biztonsági csoport**: További információ: [A hálózati forgalom szabályozása hálózati biztonsági csoportokkal.][virtual-networks-nsg]
   * **Bővítmények**: Virtuálisgép-bővítményeket telepíthet, ha hozzáadja őket a központi telepítéshez. Ebben a lépésben nem kell bővítményt hozzáadnia. Az SAP-támogatáshoz szükséges bővítmények telepítése később lesz telepítve. Lásd: [Az Azure-bővítmény konfigurálása az SAP-hoz][deployment-guide-4.5] ebben az útmutatóban.
   * **Magas rendelkezésre állás:** Válasszon ki egy rendelkezésre állási csoportot, vagy adja meg a paramétereket egy új rendelkezésre állási csoport létrehozásához. További információ: [Azure availability sets.][planning-guide-3.2.3]
   * **Megfigyelő**
     * **Rendszerindítási diagnosztika**: A **letiltás** lehetőséget választhatja a rendszerindítási diagnosztikához.
     * **Vendég operációs rendszer diagnosztikája:** **Kiválaszthatja a letiltása** a diagnosztika figyeléséhez.

1. **Összegzés**:

   Tekintse át a beállításokat, majd kattintson **az OK gombra.**

A virtuális gép a kiválasztott erőforráscsoportban van telepítve.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Virtuális gép létrehozása sablon használatával

Ha az Azure Portalról egy privát operációsrendszer-lemezkép használatával szeretne központi telepítést létrehozni, használja az alábbi SAP-sablonok egyikét. Ezek a sablonok az [azure-quickstart-sablonok GitHub-tárházban vannak közzétéve.][azure-quickstart-templates-github] A [PowerShell][virtual-machines-upload-image-windows-resource-manager]használatával manuálisan is létrehozhat virtuális gépet.

* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Ha csak egy virtuális gép használatával szeretne kétrétegű rendszert létrehozni, használja ezt a sablont.
* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon – Felügyelt lemezlemezlemez-lemezkép** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Ha kétrétegű rendszert szeretne létrehozni egyetlen virtuális gép és egy felügyelt lemezlemez-lemezlemez használatával, használja ezt a sablont.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Ha háromrétegű rendszert szeretne létrehozni több virtuális gép vagy saját operációsrendszer-lemezkép használatával, használja ezt a sablont.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon – Felügyelt lemezlemezlemez-lemezkép** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Ha háromrétegű rendszert szeretne létrehozni több virtuális gép vagy saját operációsrendszer-lemezkép és egy felügyelt lemezlemez-lemezlemez használatával, használja ezt a sablont.

Az Azure Portalon adja meg a következő paramétereket a sablonhoz:

1. **Alapok:**
   * **Előfizetés**: A sablon üzembe helyezéséhez használt előfizetés.
   * **Erőforráscsoport**: A sablon telepítéséhez használandó erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy kijelölhet egy meglévő erőforráscsoportot az előfizetésben.
   * **Hely**: A sablon üzembe helyezése. Ha meglévő erőforráscsoportot választott, a rendszer az adott erőforráscsoport helyét használja.
1. **Beállítások**:
   * **SAP rendszerazonosító:** az SAP rendszerazonosító.
   * **Operációs rendszer típusa**: A telepíteni kívánt operációs rendszer típusa (Windows vagy Linux).
   * **SAP-rendszer mérete:** Az SAP-rendszer mérete.

     Az új rendszer által biztosított SAPS-ek száma. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
   * **A rendszer rendelkezésre állása** (csak háromszintű sablon): A rendszer rendelkezésre állása.

     Válassza a **HA** lehetőséget egy olyan konfigurációhoz, amely alkalmas a magas rendelkezésre állású telepítéshez. Két adatbázis-kiszolgáló és két ASCS-kiszolgáló jön létre.
   * **Tárolási típus** (csak kétrétegű sablon): A használandó tároló típusa.

     Nagyobb rendszerek esetén erősen javasoljuk az Azure Premium Storage használatát. A tárolótípusokról további információt az alábbi forrásokban talál:
      * [Az Azure Premium SSD storage használata az SAP DBMS-példányhoz][2367194]
      * [Virtuális gép tárolási struktúrája rdbms-telepítésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Prémium szintű tárhely: Nagy teljesítményű tárolás az Azure virtuálisgép-számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
   * **Felhasználó kép VHD URI** (nem felügyelt lemezkép sablon csak): A privát operációs&lt;rendszer képének Virtuális merevlemez, például https:// fióknév>.blob.core.windows.net/vhds/userimage.vhds.
   * **Felhasználói lemezkép-tárfiók** (csak nem felügyelt lemezlemez-lemezlemez-sablon): A magánoperációs rendszer &lt;lemezképét tároló&lt;tárfiók neve, például a fióknév> https:// fióknévben>.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (csak felügyelt lemezképsablon): A használni kívánt felügyelt lemezkép azonosítója
   * **Rendszergazdai felhasználónév** és **rendszergazdai jelszó**: A felhasználónév és a jelszó.

     Új felhasználó jön létre a virtuális gépre való bejelentkezéshez.
   * **Új vagy meglévő alhálózat:** Azt határozza meg, hogy új virtuális hálózat és alhálózat jön-e létre, vagy egy meglévő alhálózatot használ. Ha már rendelkezik olyan virtuális hálózattal, amely a helyszíni hálózathoz csatlakozik, válassza a **Létező**lehetőséget.
   * **Alhálózati azonosító:** Ha a virtuális gép egy meglévő virtuális hálózatba szeretné telepíteni, ahol a virtuális gép hez definiált alhálózat ot kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában így néz ki:&lt;/subscriptions/subscription id>/resourceGroups/&lt;resource group name&lt;>/providers/Microsoft.Network/virtualNetworks/ virtual network name>/subnets/&lt;subnets name>

1. **Általános szerződési feltételek:**  
    Tekintse át és fogadja el a jogi feltételeket.

1. Válassza a **Beszerzés** lehetőséget.

#### <a name="install-the-vm-agent-linux-only"></a>A virtuális gépügynök telepítése (csak Linux)

Az előző szakaszban ismertetett sablonok használatához a Linux-ügynöknek már telepítve kell lennie a felhasználói lemezképben, különben a központi telepítés sikertelen lesz. Töltse le és telepítse a virtuálisgép-ügynököt a felhasználói lemezképben a [Letöltés, telepítés és az Azure Virtuálisgép-ügynök engedélyezése][deployment-guide-4.4]című témakörben leírtak szerint. Ha nem használja a sablonokat, később is telepítheti a virtuálisgép-ügynököt.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)

Ha az Azure-telepítés egy helyszíni Active Directory vagy DNS-példány hoz egy Azure-hely közötti VPN-kapcsolaton vagy az Azure *ExpressRoute-on* keresztül (ez az SAP NetWeaver helyszíni tervezése [és megvalósítása][planning-guide]során a létesítmények közötti kapcsolatoknak minősül), várható, hogy a virtuális gép csatlakozik egy helyszíni tartományhoz. A lépéssel kapcsolatos szempontokról további információt a [Virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)][deployment-guide-4.3]című témakörben talál.

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Attól függően, hogy a helyszíni hálózat konfigurálva van, előfordulhat, hogy be kell állítania a proxyt a virtuális gépen. Ha a virtuális gép VPN-en vagy ExpressRoute-on keresztül csatlakozik a helyszíni hálózathoz, előfordulhat, hogy a virtuális gép nem tudja elérni az internetet, és nem tudja letölteni a szükséges virtuálisgép-bővítményeket, és nem tudja összegyűjteni az SAP-gazdaügynök Azure-beli bővítményen keresztül az Azure-beli sap-bővítményen keresztül az Azure-beli infrastruktúra-adatok gyűjtését, [lásd: A proxy konfigurálása.][deployment-guide-configure-proxy]

#### <a name="configure-azure-vm-extension-for-sap"></a>Az Azure VM-bővítmény konfigurálása az SAP-hoz

Győződjön meg arról, hogy az SAP támogatja a környezetet, állítsa be az Azure Extension for SAP az Azure Extension for SAP című részben [leírtak][deployment-guide-4.5]szerint. Ellenőrizze az SAP előfeltételeit, valamint az SAP Kernel és az SAP host agent szükséges minimális verzióit az [SAP-erőforrásokban][deployment-guide-2.2]felsorolt erőforrásokban.

#### <a name="sap-vm-extension-check"></a>SAP virtuális gép bővítmény ellenőrzése

Ellenőrizze, hogy az SAP [virtuálisgép-bővítménye működik-e a Csekkek és az SAP gazdagép-ügynök végpontok között történő adatgyűjtéssel kapcsolatos hibaelhárítás][deployment-guide-troubleshooting-chapter]című témakörben leírtak szerint.


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>3. forgatókönyv: Egy helyszíni virtuális gép áthelyezése egy nem általános azure-alapú virtuális merevlemez használatával az SAP-val

Ebben a forgatókönyvben azt tervezi, hogy egy adott SAP-rendszer áthelyezése egy helyszíni környezetben az Azure-ba. Ezt úgy teheti meg, hogy feltölti az operációs rendszert, az SAP bináris fájlokat és végül a DBMS bináris fájlokat, valamint a virtuális merevlemezeket a DBMS adatait és naplófájljait az Azure-ba. A 2. forgatókönyvben ismertetett [forgatókönyvtől eltérően: Virtuális gép üzembe helyezése az SAP egyéni lemezképével][deployment-guide-3.3]ebben az esetben megtartja a gazdanév, az SAP-azonosító és az SAP felhasználói fiókokat az Azure virtuális gépben, mert a helyszíni környezetben konfigurálva vannak. Nem kell általánosítani az operációs rendszert. Ez a forgatókönyv leggyakrabban a létesítmények közötti forgatókönyvek, ahol az SAP-környezet egy része a helyszínen fut, és egy része fut az Azure-ban.

Ebben a forgatókönyvben a virtuálisgép-ügynök **nem** települ automatikusan a központi telepítés során. Mivel a virtuális gép ügynöke és az Azure-bővítmény az SAP-hoz szükséges az SAP NetWeaver azure-beli futtatásához, a virtuális gép létrehozása után mindkét összetevőt le kell töltenie, telepítenie kell, és engedélyeznie kell mindkét összetevőt.

Az Azure virtuálisgép-ügynökről további információt a következő erőforrásokban talál.

---
> ![Windows][Logo_Windows] Windows
>
> [Az Azure virtuálisgép-ügynök – áttekintés][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Az Azure Linux-ügynök használati útmutatója][virtual-machines-linux-agent-user-guide]
>
>

---

A következő folyamatábra a helyszíni virtuális gép nem általános azure-alapú virtuális merevlemez használatával történő áthelyezésére vonatkozó lépések sorrendjét mutatja be:

![Virtuálisgép-telepítés folyamatábrája AZ SAP-rendszerekhez virtuálisgép-lemez használatával][deployment-guide-figure-400]

Ha a lemez már feltöltve van, és az Azure-ban definiálva van (lásd: [Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver számára),][planning-guide]végezze el a következő néhány szakaszban leírt feladatokat.

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ha egy privát operációsrendszer-lemez használatával szeretne központi telepítést létrehozni az Azure Portalon keresztül, használja az [azure-quickstart-templates GitHub-tárházban közzétett SAP-sablont.][azure-quickstart-templates-github] A PowerShell használatával manuálisan is létrehozhat egy virtuális gépet.

* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Ha csak egy virtuális gép használatával szeretne kétrétegű rendszert létrehozni, használja ezt a sablont.
* [**Kétrétegű konfiguráció (csak egy virtuális gép) sablon – Felügyelt lemez** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Ha kétrétegű rendszert szeretne létrehozni egyetlen virtuális gép és egy felügyelt lemez használatával, használja ezt a sablont.

Az Azure Portalon adja meg a következő paramétereket a sablonhoz:

1. **Alapok:**
   * **Előfizetés**: A sablon üzembe helyezéséhez használt előfizetés.
   * **Erőforráscsoport**: A sablon telepítéséhez használandó erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy kijelölhet egy meglévő erőforráscsoportot az előfizetésben.
   * **Hely**: A sablon üzembe helyezése. Ha meglévő erőforráscsoportot választott, a rendszer az adott erőforráscsoport helyét használja.
1. **Beállítások**:
   * **SAP rendszerazonosító:** az SAP rendszerazonosító.
   * **Operációs rendszer típusa**: A telepíteni kívánt operációs rendszer típusa (Windows vagy Linux).
   * **SAP-rendszer mérete:** Az SAP-rendszer mérete.

     Az új rendszer által biztosított SAPS-ek száma. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
   * **Tárolási típus** (csak kétrétegű sablon): A használandó tároló típusa.

     Nagyobb rendszerek esetén erősen javasoljuk az Azure Premium Storage használatát. A tárolótípusokról további információt az alábbi forrásokban talál:
      * [Az Azure Premium SSD storage használata az SAP DBMS-példányhoz][2367194]
      * [Virtuális gép tárolási struktúrája rdbms-telepítésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Prémium szintű tárhely: Nagy teljesítményű tárolás az Azure virtuális gép számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
   * **VHD URI operációs rendszerlemez** (csak nem felügyelt lemezsablon): A saját&lt;operációsrendszer-lemez URI-ja, például https:// fióknév>.blob.core.windows.net/vhds/osdisk.vhd.
   * **Operációs rendszer felügyelt lemezazonosítója** (csak felügyelt lemezsablon): A Felügyelt lemez operációsrendszer-lemezazonosítója,/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Új vagy meglévő alhálózat:** Azt határozza meg, hogy új virtuális hálózat és alhálózat jön-e létre, vagy egy meglévő alhálózatot használ. Ha már rendelkezik olyan virtuális hálózattal, amely a helyszíni hálózathoz csatlakozik, válassza a **Létező**lehetőséget.
   * **Alhálózati azonosító:** Ha a virtuális gép egy meglévő virtuális hálózatba szeretné telepíteni, ahol a virtuális gép hez definiált alhálózat ot kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában így néz ki:&lt;/subscriptions/subscription id>/resourceGroups/&lt;resource group name&lt;>/providers/Microsoft.Network/virtualNetworks/ virtual network name>/subnets/&lt;subnets name>

1. **Általános szerződési feltételek:**  
    Tekintse át és fogadja el a jogi feltételeket.

1. Válassza a **Beszerzés** lehetőséget.

#### <a name="install-the-vm-agent"></a>A virtuálisgép-ügynök telepítése

Az előző szakaszban ismertetett sablonok használatához a virtuálisgép-ügynököt telepíteni kell az operációs rendszer lemezére, különben a központi telepítés sikertelen lesz. Töltse le és telepítse a virtuális gép ügynökét a virtuális gépben, az [Azure virtuálisgép-ügynök letöltése, telepítése és engedélyezése][deployment-guide-4.4]című témakörben leírtak szerint.

Ha nem használja az előző szakaszban leírt sablonokat, utána is telepítheti a virtuálisgép-ügynököt.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)

Ha az Azure-központi telepítés egy helyszíni Active Directory vagy DNS-példány egy Azure-hely közötti VPN-kapcsolaton vagy ExpressRoute-on keresztül csatlakozik (ez az [SAP NetWeaver azure-beli virtuális gépek tervezésének és megvalósításának][planning-guide] *több helyiségen belüli* szolgáltatása), várható, hogy a virtuális gép csatlakozik egy helyszíni tartományhoz. A feladattal kapcsolatos szempontokról további információt a [Virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)][deployment-guide-4.3]című témakörben talál.

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Attól függően, hogy a helyszíni hálózat konfigurálva van, előfordulhat, hogy be kell állítania a proxyt a virtuális gépen. Ha a virtuális gép VPN-en vagy ExpressRoute-on keresztül csatlakozik a helyszíni hálózathoz, előfordulhat, hogy a virtuális gép nem tudja elérni az internetet, és nem tudja letölteni a szükséges virtuálisgép-bővítményeket, és nem tudja összegyűjteni az SAP-gazdaügynök Azure-beli bővítményen keresztül az Azure-beli sap-bővítményen keresztül az Azure-beli infrastruktúra-adatok gyűjtését, [lásd: A proxy konfigurálása.][deployment-guide-configure-proxy]

#### <a name="configure-azure-vm-extension-for-sap"></a>Az Azure VM-bővítmény konfigurálása az SAP-hoz

Győződjön meg arról, hogy az SAP támogatja a környezetet, állítsa be az Azure Extension for SAP az Azure Extension for SAP című részben [leírtak][deployment-guide-4.5]szerint. Ellenőrizze az SAP előfeltételeit, valamint az SAP Kernel és az SAP host agent szükséges minimális verzióit az [SAP-erőforrásokban][deployment-guide-2.2]felsorolt erőforrásokban.

#### <a name="sap-vm-check"></a>SAP virtuális gép ellenőrzése

Ellenőrizze, hogy az SAP [virtuálisgép-bővítménye működik-e a Csekkek és az SAP gazdagép-ügynök végpontok között történő adatgyűjtéssel kapcsolatos hibaelhárítás][deployment-guide-troubleshooting-chapter]című témakörben leírtak szerint.

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Az Azure Extension for SAP konfigurációjának frissítése

Frissítse az Azure Extension for SAP konfigurációját az alábbi esetekben:
* A közös Microsoft/SAP csapat kibővíti a virtuális gép bővítmény képességeit, és több vagy kevesebb számlálót kér.
* A Microsoft bemutatja az alapul szolgáló Azure-infrastruktúra új verzióját, amely biztosítja az adatokat, és az Azure-bővítményt az SAP-hoz hozzá kell igazítani ezekhez a változásokhoz.
* További adatlemezek csatlakoztatása az Azure virtuális géphez, vagy eltávolít egy adatlemezt. Ebben a forgatókönyvben frissítse a tárolással kapcsolatos adatok gyűjtését. A konfiguráció módosítása végpontok hozzáadásával vagy törlésével, vagy IP-címek hozzárendelésével egy virtuális géphez nincs hatással a bővítmény konfigurációját.
* Módosíthatja az Azure virtuális gép méretét, például Az A5-ös méretről bármely más virtuális gép méretére.
* Új hálózati csatolók hozzáadása az Azure virtuális géphez.

A beállítások frissítéséhez frissítse az Azure Extension for SAP konfigurációját az [Azure Extension for SAP konfigurálása][deployment-guide-4.5]című lépések lépéseit követve.

## <a name="detailed-tasks-for-sap-software-deployment"></a>Részletes feladatok az SAP-szoftverek telepítéséhez

Ez a szakasz részletes lépéseket tartalmaz a konfigurációs és telepítési folyamat egyes feladatainak elvégzéséhez.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell-parancsmagok üzembe helyezése

1. Nyissa meg a [Microsoft Azure letöltéseit.](https://azure.microsoft.com/downloads/)
1. A **Parancssori eszközök csoport**Ban a **PowerShell**csoportban válassza a **Windows telepítése**lehetőséget.
1. A Microsoft Download Manager párbeszédpanel letöltött fájl (például WindowsAzurePowershellGet.3f.3f.3fnew.exe) esetén a **Futtatás**lehetőséget választja.
1. A Microsoft Web Platform Installer (Microsoft Web PI) futtatásához válassza az **Igen**lehetőséget.
1. Az így hasonló oldalak jelennek meg:

   ![Az Azure PowerShell-parancsmagok telepítési lapja][deployment-guide-figure-500]<a name="figure-5"></a>

1. Válassza **a Telepítés**lehetőséget, majd fogadja el a Microsoft szoftverlicenc-feltételeit.
1. A PowerShell telepítve van. A telepítővarázsló bezárásához válassza a **Befejezés** gombra.

Gyakran ellenőrizze a PowerShell-parancsmagok frissítéseit, amelyek általában havonta frissülnek. A frissítések keresésének legegyszerűbb módja az előző telepítési lépések, az 5. A parancsmagok kiadási dátuma és kiadási száma az 5. Ha csak az SAP Note [1928533] vagy az SAP Note [2015553]másként nem rendelkezik, javasoljuk, hogy az Azure PowerShell-parancsmagok legújabb verziójával dolgozzon.

A számítógépre telepített Azure PowerShell-parancsmagok verziójának ellenőrzéséhez futtassa ezt a PowerShell-parancsot:

```powershell
(Get-Module Az.Compute).Version
```

Az eredmény így néz ki:

![Az Azure PowerShell-parancsmag verziójának ellenőrzésének eredménye][deployment-guide-figure-600]
<a name="figure-6"></a>

Ha a számítógépre telepített Azure-parancsmag verziója az aktuális verzió, a telepítővarázsló első oldala azt jelzi, hogy hozzáadja **(telepítette)** a termék címéhez (lásd a következő képernyőképet). A PowerShell Azure-parancsmagok naprakészek. A telepítővarázsló bezárásához válassza a **Kilépés**gombot.

![Az Azure PowerShell-parancsmagok telepítési lapja, amely jelzi, hogy az Azure PowerShell-parancsmagok legújabb verziója telepítve van][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Az Azure CLI üzembe helyezése

1. Nyissa meg a [Microsoft Azure letöltéseit.](https://azure.microsoft.com/downloads/)
1. A **Parancssori eszközök**csoportBan az **Azure parancssori felületén**válassza a **Telepítési** hivatkozást az operációs rendszerhez.
1. A Microsoft Download Manager párbeszédpanel letöltött fájl (például WindowsAzureXPlatCLI.3f.3f.3fnew.exe) esetén a **Futtatás**lehetőséget választja.
1. A Microsoft Web Platform Installer (Microsoft Web PI) futtatásához válassza az **Igen**lehetőséget.
1. Az így hasonló oldalak jelennek meg:

   ![Az Azure PowerShell-parancsmagok telepítési lapja][deployment-guide-figure-500]<a name="figure-5"></a>

1. Válassza **a Telepítés**lehetőséget, majd fogadja el a Microsoft szoftverlicenc-feltételeit.
1. Az Azure CLI telepítve van. A telepítővarázsló bezárásához válassza a **Befejezés** gombra.

Gyakran ellenőrizze az Azure CLI frissítéseit, amelyek általában havonta frissülnek. A frissítések keresésének legegyszerűbb módja az előző telepítési lépések, az 5.

A számítógépre telepített Azure CLI verziójának ellenőrzéséhez futtassa a következő parancsot:

```console
azure --version
```

Az eredmény így néz ki:

![Az Azure CLI verzióellenőrzésének eredménye][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Virtuális gép csatlakoztatása helyszíni tartományhoz (csak Windows)

Ha sap-virtuális gépeket telepít egy telephelyközi forgatókönyvben, ahol a helyszíni Active Directory és a DNS az Azure-ban is ki van bővítve, várható, hogy a virtuális gépek csatlakoznak egy helyszíni tartományhoz. A virtuális gépek helyszíni tartományhoz való csatlakozásának részletes lépései, valamint a helyszíni tartomány hozvalóhoz szükséges további szoftverek ügyféltől függően változnak. Általában ahhoz, hogy egy virtuális gép egy helyszíni tartományhoz csatlakozzon, további szoftvereket kell telepítenie, például kártevőirtó szoftvert, valamint biztonsági mentést vagy figyelési szoftvert.

Ebben az esetben azt is meg kell győződnie arról, hogy ha az internetes proxy beállítások kényszerítve, amikor egy virtuális gép csatlakozik egy tartományhoz a környezetben, a Windows helyi rendszerfiók (S-1-5-18) a vendég virtuális gép ugyanazokat a proxybeállításokat. A legegyszerűbb megoldás a proxy kényszerítése tartományi csoportházirend használatával, amely a tartomány rendszereire vonatkozik.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Az Azure VM-ügynök letöltése, telepítése és engedélyezése

A virtuális gépek, amelyek nem általánosított operációsrendszer-lemezképből vannak telepítve (például egy olyan lemezkép, amely nem a Windows rendszerelőkészítése vagy a sysprep eszközből származik), manuálisan kell letöltenie, telepítenie és engedélyeznie kell az Azure virtuálisgép-ügynököt.

Ha üzembe helyez egy virtuális gép az Azure Marketplace-en, ez a lépés nem szükséges. Az Azure Piactérről származó lemezképek már rendelkeznek az Azure virtuálisgép-ügynökkel.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Töltse le az Azure virtuálisgép-ügynököt:
   1.  Töltse le az [Azure VM Agent telepítőcsomagját.](https://go.microsoft.com/fwlink/?LinkId=394789)
   1.  A virtuálisgép-ügynök MSI-csomagjának helyi tárolása személyi számítógépen vagy kiszolgálón.
1. Az Azure virtuálisgép-ügynök telepítése:
   1.  Csatlakozzon a telepített Azure virtuális gép hez a Távoli asztali protokoll (RDP) használatával.
   1.  Nyisson meg egy Windows Intéző ablakot a virtuális gépen, és válassza ki a virtuális gépügynök MSI-fájljának célkönyvtárát.
   1.  Húzza az Azure VM Agent Installer MSI-fájlt a helyi számítógépről/kiszolgálóról a virtuális gép ügynökének célkönyvtárába.
   1.  Kattintson duplán az MSI-fájlra a virtuális gépen.
1. A helyszíni tartományokhoz csatlakozó virtuális gépek esetében győződjön meg arról, hogy a végleges internetproxy-beállítások a virtuális gép Windows-helyi rendszerfiókjára (S-1-5-18) is vonatkoznak, [a proxy konfigurálása című][deployment-guide-configure-proxy]részen leírtak szerint. A virtuálisgép-ügynök ebben a környezetben fut, és képesnek kell lennie az Azure-hoz való csatlakozásra.

Nincs szükség felhasználói beavatkozásra az Azure virtuálisgép-ügynök frissítéséhez. A virtuálisgép-ügynök automatikusan frissül, és nem igényel virtuális gép újraindítását.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

A következő parancsokkal telepítse a VM Agent for Linux:Use the following commands to install the VM Agent for Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) vagy Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Ha az ügynök már telepítve van, az Azure Linux-ügynök frissítéséhez végezze el [az Azure Linux-ügynök frissítése a virtuális gépen a GitHub legújabb verziójára][virtual-machines-linux-update-agent]leírt lépéseket.

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>A proxy konfigurálása

A proxy Windows rendszerben történő konfigurálásához szükséges lépések eltérnek a proxy Linux on történő konfigurálásától.

#### <a name="windows"></a>Windows

A proxybeállításokat megfelelően kell beállítani ahhoz, hogy a Helyi rendszer fiók hozzáférjen az internethez. Ha a csoportházirend nem adja meg a proxybeállításokat, konfigurálhatja a Helyi rendszer fiók beállításait.

1. Nyissa meg a **Kezdőképernyő**t, írja be **a gpedit.msc**parancsot, és válassza **az Enter lehetőséget.**
1. Válassza **a Számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők** > **Internet Explorer**. Győződjön meg arról, hogy a **Proxybeállítások számítógépenkénti beállítása (nem pedig felhasználónként)** le van tiltva vagy nincs konfigurálva.
1. A **Vezérlőpulton**nyissa meg a **Hálózati és megosztási központ** > **Internetbeállításai című**lehetőséget.
1. A **Kapcsolatok** lapon kattintson a **LAN-beállítások** gombra.
1. Törölje **A beállítások automatikus észlelése** jelölőnégyzet kijelölését.
1. Jelölje be a **Proxykiszolgáló használata a helyi hálózathoz** jelölőnégyzetet, majd adja meg a proxycímet és a portot.
1. Válassza a **Speciális** gombot.
1. A **Kivételek** mezőbe írja be a **168.63.129.16**IP-címet. Válassza **az OK gombot.**

#### <a name="linux"></a>Linux

Konfigurálja a megfelelő proxyt a Microsoft Azure vendégügynök \\konfigurációs fájljában, amely az etc\\waagent.conf fájlban található.

Állítsa be a következő paramétereket:

1. **HTTP proxyállomás**. Állítsa például **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP proxyport**. Például állítsa **80-ra.**

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Indítsa újra az ügynököt.

   ```console
   sudo service waagent restart
   ```

Az \\etc\\waagent.conf proxybeállításai a szükséges virtuálisgép-bővítményekre is vonatkoznak. Ha az Azure-adattárak, győződjön meg arról, hogy a forgalom ezek a tárolók nem megy keresztül a helyszíni intraneten. Ha a kényszerített bújtatás engedélyezéséhez felhasználó által definiált útvonalakat hozott létre, győződjön meg arról, hogy olyan útvonalat ad hozzá, amely a forgalmat közvetlenül az internetre irányítja az adattárakba, és nem a helyek közötti VPN-kapcsolaton keresztül.

* **SLES**

  Azt is meg kell adni útvonalakat \\az\\IP-címek felsorolt etc regionserverclnt.cfg. Az alábbi ábra egy példát mutat be:

  ![Alagúthasználat kényszerítése][deployment-guide-figure-50]


* **RHEL**

  Emellett hozzá kell adnia az útvonalakat az \\etc\\yum.repos.d\\rhui-load-balancers oldalon felsorolt állomások IP-címeihez. Lásd például az előző ábrát.

* **Oracle Linux**

  Az Azure-beli Oracle Linux-adattárak nem találhatók. Konfigurálnia kell a saját adattárak at Oracle Linux vagy használja a nyilvános adattárak.

A felhasználó által definiált útvonalakról a [Felhasználó által definiált útvonalak ról és az IP-továbbítás című témakörben][virtual-networks-udr-overview]talál további információt.

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Az Azure-bővítmény konfigurálása az SAP-hoz

Amikor elkészítette a virtuális gépet az [SAP-beli SAP-alapú virtuális gépek üzembe helyezési forgatókönyveiben leírtak][deployment-guide-3]szerint, az Azure virtuálisgép-ügynök telepítve van a virtuális gépen. A következő lépés az Azure Extension for SAP üzembe helyezése, amely elérhető az Azure Extension Repository a globális Azure-adatközpontokban. További információ: [Azure Virtual Machines planning and implementation for SAP NetWeaver][planning-guide-9.1].

A PowerShell vagy az Azure CLI használatával telepítheti és konfigurálhatja az Azure-bővítményt az SAP-hoz. A bővítmény Windows- vagy Linux-alapú virtuális gépre történő telepítéséről az [Azure PowerShell][deployment-guide-4.5.1]című témakörben van. Ha a bővítményt Linux-alapú virtuális gépre szeretné telepíteni linuxos asztali számítógéphasználatával, olvassa el az Azure CLI című [témakört.][deployment-guide-4.5.2]

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell Linuxra és Windows rendszerre

Az Azure-bővítmény telepítése az SAP-hoz a PowerShell használatával:

1. Győződjön meg arról, hogy telepítette az Azure PowerShell-parancsmag legújabb verzióját. További információ: [Az Azure PowerShell-parancsmagok telepítése.][deployment-guide-4.1]  
1. Futtassa az alábbi PowerShell-parancsmagot.
    Az elérhető környezetek listájáért `commandlet Get-AzEnvironment`futtassa a futtassa a programot. Ha globális Azure-t szeretne használni, környezete az **AzureCloud.** A kínai Azure-hoz válassza az **AzureChinaCloud**lehetőséget.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Miután megadta a fiókadatait, és azonosította az Azure virtuális gépet, a parancsfájl telepíti a szükséges bővítményeket, és engedélyezi a szükséges funkciókat. Ez néhány percig tarthat.
További információ `Set-AzVMAEMExtension`a beállításról: [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Az SAP-specifikus Azure-parancsmag sikeres végrehajtása Set-AzVMAEMExtension][deployment-guide-figure-900]

A `Set-AzVMAEMExtension` konfiguráció minden lépést megtesz az SAP gazdagép-adatgyűjtésének konfigurálásához.

A parancsfájl kimenete a következő információkat tartalmazza:

* Annak megerősítése, hogy az operációs rendszer lemezére és az összes további adatlemezre vonatkozó adatgyűjtés konfigurálva van.
* A következő két üzenet megerősíti a storage metrikák konfigurációját egy adott tárfiókhoz.
* Egy kimeneti sor adja meg a virtuális gép bővítménye az SAP-konfiguráció tényleges frissítésének állapotát.
* Egy másik kimeneti sor megerősíti, hogy a konfiguráció telepítve vagy frissítve lett.
* A kimenet utolsó sora tájékoztató. Bemutatja a virtuális gép bővítményének SAP-konfigurációhoz való tesztelésének lehetőségeit.
* Annak ellenőrzéséhez, hogy az Azure VM Extension for SAP-konfiguráció összes lépése sikeresen megtörtént-e, és hogy az Azure Infrastructure biztosítja-e a szükséges adatokat, folytassa az Azure-bővítmény SAP-hoz készültségi ellenőrzésének készenléti ellenőrzését az [SAP-hoz való készenléti ellenőrzésben][deployment-guide-5.1]leírtak szerint.
* Várjon 15-30 percet az Azure Diagnostics számára a vonatkozó adatok összegyűjtésére.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI Linuxos virtuális gépekhez

Az Azure Extension for SAP telepítése az Azure CLI használatával:

   1. Telepítse az Azure classic CLI-t [az Azure classic CLI telepítése][azure-cli]című témakörben leírtak szerint.
   1. Jelentkezzen be Azure-fiókjával:

      ```console
      azure login
      ```

   1. Váltás az Azure Resource Manager módra:

      ```console
      azure config mode arm
      ```

   1. Az Azure-bővítmény engedélyezése az SAP-hoz:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Telepítés az Azure CLI 2.0 használatával

   1. Telepítse az Azure CLI 2.0-s telepítését az [Azure CLI 2.0 telepítése][azure-cli-2]című témakörben leírtak szerint.
   1. Jelentkezzen be Azure-fiókjával:

      ```azurecli
      az login
      ```

   1. Az Azure CLI AEM bővítmény telepítése
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Telepítse a bővítményt
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Ellenőrizze, hogy az Azure-bővítmény az SAP aktív-e az Azure Linux virtuális gép. Ellenőrizze, hogy \\\\a\\var\\lib AzureEnhancedMonitor PerfCounters fájl létezik-e. Ha létezik, a parancssorban futtassa ezt a parancsot az Sap-bővítmény által gyűjtött információk megjelenítéséhez:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   A kimenet így néz ki:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Az SAP-gazdaügynök végpontok között történő adatgyűjtésének ellenőrzése és hibaelhárítása

Miután üzembe helyezte az Azure virtuális gép, és állítsa be a megfelelő Azure-bővítmény az SAP-hoz, ellenőrizze, hogy a bővítmény összes összetevője a várt módon működik-e.

Futtassa az Azure-bővítmény az SAP-hoz az [Azure-bővítmény az SAP-hoz való készenléti ellenőrzés című][deployment-guide-5.1]részen leírtak szerint. Ha minden készenléti ellenőrzés eredménye pozitív, és az összes releváns teljesítményszámlálók jelennek meg OK, az Azure Extension az SAP sikeresen be van állítva. Folytathatja az SAP host agent telepítését az SAP-erőforrások SAP-jegyzeteiben [leírtak szerint.][deployment-guide-2.2] Ha a készenléti ellenőrzés azt jelzi, hogy a számlálók hiányoznak, futtassa az állapot-ellenőrzést az Azure Extension for SAP, az [Azure-bővítmény SAP-konfigurációhoz állapotfelmérése][deployment-guide-5.2]című részen leírtak szerint. További hibaelhárítási lehetőségekaz [Azure Extension for SAP hibaelhárítása][deployment-guide-5.3]című témakörben található.

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Az SAP-hoz való Azure-bővítmény készenléti ellenőrzése

Ez az ellenőrzés biztosítja, hogy az SAP-alkalmazáson belül megjelenő összes teljesítménymetrikót az alapul szolgáló Sap-bővítmény biztosítja.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>A készenléti ellenőrzés futtatása Windows virtuális gépen

1. Jelentkezzen be az Azure virtuális gépre (rendszergazdai fiók használatával nem szükséges).
1. Nyisson meg egy parancssori ablakot.
1. A parancssorban módosítsa a könyvtárat az SAP-bővítmény telepítési\\mappájára:\\C: Csomagok beépülő\\modul Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verziója>\\csepp

   A bővítmény elérési útjának *verziója* eltérő lehet. Ha a telepítési mappában a bővítmény több verziójához is megjelenik mappák, ellenőrizze az AzureEnhancedMonitoring Windows-szolgáltatás konfigurációját, majd váltson át a *végrehajtható elérési út*ként megjelölt mappára.

   ![Az SAP-hoz az Azure Extension-t futtató szolgáltatás tulajdonságai][deployment-guide-figure-1000]

1. A parancssorban futtassa az **azperflib.exe** parancsot paraméterek nélkül.

   > [!NOTE]
   > Azperflib.exe fut egy hurok, és frissíti az összegyűjtött számlálók 60 másodpercenként. A ciklus befejezéséhez zárja be a parancssorablakot.
   >
   >

Ha az Azure-bővítmény az SAP nincs telepítve, vagy az AzureEnhancedMonitoring szolgáltatás nem fut, a bővítmény nincs megfelelően konfigurálva. A bővítmény üzembe helyezéséről az [Sap-bővítmény hibaelhárítása című][deployment-guide-5.3]témakörben talál részletes információt.

> [!NOTE]
> Az Azperflib.exe olyan összetevő, amely nem használható saját célokra. Ez egy olyan összetevő, amely kizárólag az SAP gazdaügynök virtuális gépéhez kapcsolódó Azure-infrastruktúra-adatokat szolgáltat.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Az azperflib.exe kimenetének ellenőrzése

Azperflib.exe kimenet az SAP összes feltöltött Azure-teljesítményszámlálóját megjeleníti. Az összegyűjtött számlálók listájának alján egy összefoglaló és állapotjelző mutatja az Azure Extension az SAP állapotát.

![Állapot-ellenőrzés kimenete az azperflib.exe végrehajtásával, ami azt jelzi, hogy nem áll fenn probléma][deployment-guide-figure-1100]
<a name="figure-11"></a>

Ellenőrizze a **számlálók teljes** kimenete, amely üresként van jelentve, és az **állapot állapota**, az előző ábrán látható eredmény.

Értelmezze az eredményül kapott értékeket a következőképpen:

| Azperflib.exe eredményértékek | Azure Extension sap-állapothoz |
| --- | --- |
| **API-hívások - nem érhető el** | A nem elérhető számlálók vagy nem alkalmazhatók a virtuális gép konfigurációjára, vagy hibák. Lásd: **Állapot**. |
| **Számlálók összesen - üres** |A következő két Azure storage-számláló üres lehet: <ul><li>Tárolási olvasási késleltetési kiszolgáló msec</li><li>Tárolási olvasási késleltetés E2E msec</li></ul>Minden más számlálónak értékekkel kell rendelkeznie. |
| **Egészségi állapot** |Csak akkor OK, ha a visszatérési állapot **ok**látható. |
| **Diagnosztika** |Részletes információk az egészségi állapotról. |

Ha az **Állapot érték** nem **OK,** kövesse az [Sap-konfigurációhoz][deployment-guide-5.2]az Azure Extension állapotellenőrzése című útmutató utasításait.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>A készenléti ellenőrzés futtatása Linux os virtuális gépen

1. Csatlakozzon az Azure virtuális géphez az SSH használatával.

1. Tekintse meg az Azure Extension az SAP-hoz kimenetét.

   a.  Futtassa a `more /var/lib/AzureEnhancedMonitor/PerfCounters` parancsot.

   **Várt eredmény**: A teljesítményszámlálók listáját adja vissza. A fájl nem lehet üres.

   b. Futtassa a `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` parancsot.

   **Várt eredmény**: Egy olyan sort ad eredményül, ahol a hiba **nincs**, például **3;config; Hiba;;0;0; nincs;0;1456416792;tst-servercs;**

   c. Futtassa a `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` parancsot.

   **Várt eredmény**: Üres vagy nem létező értéket ad vissza.

Ha az előző ellenőrzés nem sikerült, futtassa az alábbi további ellenőrzéseket:

1. Ellenőrizze, hogy a waagent telepítve van-e és engedélyezve van-e.

   a.  Futtassa a `sudo ls -al /var/lib/waagent/` parancsot.

     **Várt eredmény**: A waagent könyvtár tartalma.

   b.  Futtassa a `ps -ax | grep waagent` parancsot.

   **Várt eredmény**: Egy, a következőhöz hasonló bejegyzést jelenít meg:`python /usr/sbin/waagent -daemon`

1. Győződjön meg arról, hogy az Azure Extension for SAP telepítve van és fut.

   a.  Futtassa a `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` parancsot.

   **Várt eredmény:** Az Azure-bővítmény SAP-címtárhoz való tartalom listázása.

   b. Futtassa a `ps -ax | grep AzureEnhanced` parancsot.

   **Várt eredmény**: Egy, a következőhöz hasonló bejegyzést jelenít meg:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Telepítse az SAP Host Agent-et az SAP [Note 1031096]megjegyzésében leírtak szerint, és ellenőrizze a kimenetét. `saposcol`

   a.  Futtassa a `/usr/sap/hostctrl/exe/saposcol -d` parancsot.

   b.  Futtassa a `dump ccm` parancsot.

   c.  Ellenőrizze, hogy a **Virtualization_Configuration\Enhanced Monitoring Access** mérőszám **igaz-e.**

Ha már telepítve van egy SAP NetWeaver ABAP alkalmazáskiszolgáló, nyissa meg az ST06 tranzakciót, és ellenőrizze, hogy engedélyezve van-e a továbbfejlesztett figyelés.

Ha ezek az ellenőrzések bármelyike sikertelen, és a bővítmény újratelepítésével kapcsolatos részletes információkért olvassa el [az Azure Extension for SAP hibaelhárítása című][deployment-guide-5.3]témakört.

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Állapot-ellenőrzés az Azure-bővítmény sap-konfigurációhoz

Ha az infrastruktúra egyes adatai nem megfelelően kézbesítve, ahogy azt a teszt leírt `Test-AzVMAEMExtension` készültségi ellenőrzés az Azure Extension for [SAP,][deployment-guide-5.1]futtassa a parancsmag, hogy ellenőrizze, hogy az Azure-infrastruktúra és az Azure Extension for SAP megfelelően konfigurálva van.

1. Győződjön meg arról, hogy telepítette az Azure PowerShell-parancsmag legújabb verzióját az [Azure PowerShell-parancsmagok telepítése][deployment-guide-4.1]című részben leírtak szerint.
1. Futtassa az alábbi PowerShell-parancsmagot. Az elérhető környezetek listájáért futtassa `Get-AzEnvironment`a parancsmast. A globális Azure használatához **AzureCloud** válassza ki az AzureCloud-környezetet. A kínai Azure-hoz válassza az **AzureChinaCloud**lehetőséget.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Adja meg a fiókadatait, és azonosítsa az Azure virtuális gépet.

   ![Az SAP-specifikus Azure-parancsmag teszt-VMConfigForSAP_GUI bemeneti lapja][deployment-guide-figure-1200]

1. A parancsfájl a kiválasztott virtuális gép konfigurációját teszteli.

   ![Az Azure-bővítmény SAP-hoz sikeres tesztjének kimenete][deployment-guide-figure-1300]

Győződjön meg arról, hogy minden állapotfelmérés eredménye **rendben**van. Ha néhány ellenőrzés nem jelenik meg **az OK felirat,** futtassa a frissítési parancsmamot [az Azure Extension for SAP című][deployment-guide-4.5]részen leírtak szerint. Várjon 15 percet, és ismételje meg az [Azure-bővítmény SAP-konfigurációhoz][deployment-guide-5.1] való készenléti ellenőrzése című szakaszban ismertetett ellenőrzéseket, [és ellenőrizze az Azure Extension t.][deployment-guide-5.2] Ha az ellenőrzések továbbra is problémát jeleznek néhány vagy az összes számlálóval kapcsolatban, olvassa [el az Azure Extension sap-hoz történő hibaelhárítása című témakört.][deployment-guide-5.3]

> [!Note]
> Bizonyos figyelmeztetéseket tapasztalhat olyan esetekben, amikor felügyelt standard Azure-lemezeket használ. Az "OK" szót visszaadó tesztek helyett figyelmeztetések jelennek meg. Ez normális, és az adott lemeztípus esetén készült. Lásd még: [Az Azure-bővítmény SAP-hoz történő hibaelhárítása című][deployment-guide-5.3] témakört.
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Az Azure-bővítmény SAP-hoz történő hibaelhárítása

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Az Azure teljesítményszámlálói egyáltalán nem jelennek meg

Az AzureEnhancedMonitoring Windows-szolgáltatás teljesítménymutatókat gyűjt az Azure-ban. Ha a szolgáltatás nincs megfelelően telepítve, vagy ha nem fut a virtuális gép, nem teljesítmény metrikákgyűjthetők.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Az SAP-hoz való Azure Extension telepítési könyvtára üres

###### <a name="issue"></a>Probléma

A c telepítési\\könyvtár:\\\\A csomagok beépülő modulja Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;>\\drop verziója üres.

###### <a name="solution"></a>Megoldás

A bővítmény nincs telepítve. Határozza meg, hogy proxyprobléma-e (ahogy azt korábban leírtuk). Előfordulhat, hogy újra kell indítania `Set-AzVMAEMExtension` a számítógépet, vagy újra kell futtatnia a konfigurációs parancsfájlt.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Az SAP-hoz való Azure-bővítmény nem létezik

###### <a name="issue"></a>Probléma

Az AzureEnhancedMonitoring Windows-szolgáltatás nem létezik.

Azazperflib.exe kimenet hibát jelez:

![Az azperflib.exe végrehajtása azt jelzi, hogy az Sap-bővítmény szolgáltatása nem fut][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Megoldás

Ha a szolgáltatás nem létezik, az Azure-bővítmény az SAP-hoz nincs megfelelően telepítve. Telepítse újra a bővítményt a telepítési forgatókönyvhöz leírt lépések használatával az [SAP-hoz az Azure-beli virtuális gépek üzembe helyezési forgatókönyveiben.][deployment-guide-3]

A bővítmény üzembe helyezése után egy óra elteltével ellenőrizze újra, hogy az Azure-teljesítményszámlálók az Azure virtuális gép.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Az Sap-hoz az Azure Extension szolgáltatás létezik, de nem indul el

###### <a name="issue"></a>Probléma

Az AzureEnhancedMonitoring Windows-szolgáltatás létezik és engedélyezve van, de nem indul el. További információt az alkalmazás eseménynaplójában talál.

###### <a name="solution"></a>Megoldás

A konfiguráció helytelen. Indítsa újra az Sap-bővítményt a virtuális gépben, [az Azure Extension for SAP konfigurálása][deployment-guide-4.5]című részen leírtak szerint.

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Néhány Azure-teljesítményszámláló hiányzik

Az AzureEnhancedMonitoring Windows-szolgáltatás teljesítménymutatókat gyűjt az Azure-ban. A szolgáltatás több forrásból kap adatokat. Egyes konfigurációs adatok helyileg gyűjtik, és néhány teljesítmény metrikák az Azure Diagnosztika. A tárolószámlálók a tárolási előfizetési szinten történő naplózásból használatosak.

Ha az SAP Note [1999351] használatával történő hibaelhárítás `Set-AzVMAEMExtension` nem oldja meg a problémát, futtassa újra a konfigurációs parancsfájlt. Előfordulhat, hogy várnia kell egy órát, mert előfordulhat, hogy a tárolási elemzési vagy diagnosztikai számlálók nem jönnek létre közvetlenül az engedélyezésük után. Ha a probléma továbbra is fennáll, nyisson meg egy SAP-ügyfélszolgálati üzenetet a BC-OP-NT-AZR windowsos vagy BC-OP-LNX-AZR összetevőn egy Linux-alapú virtuális gépen.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Az Azure teljesítményszámlálói egyáltalán nem jelennek meg

Az Azure-beli teljesítménymutatókat egy démon gyűjti. Ha a démon nem fut, nem lehet teljesítménymutatókat gyűjteni.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Az SAP-hoz való Azure Extension telepítési könyvtára üres

###### <a name="issue"></a>Probléma

A \\\\könyvtárvar\\lib\\ waagent nem rendelkezik alkönyvtárral az SAP-bővítményhez.

###### <a name="solution"></a>Megoldás

A bővítmény nincs telepítve. Határozza meg, hogy proxyprobléma-e (ahogy azt korábban leírtuk). Előfordulhat, hogy újra kell indítania a `Set-AzVMAEMExtension` számítógépet, és/vagy újra kell futtatnia a konfigurációs parancsfájlt.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A Set-AzVMAEMExtension és a Test-AzVMAEMExtension végrehajtása figyelmeztető üzeneteket jelenít meg arról, hogy a szabványos felügyelt lemezek nem támogatottak

###### <a name="issue"></a>Probléma

A Set-AzVMAEMExtension vagy a Test-AzVMAEMExtension extension üzenetek végrehajtásakor az alábbiak a következők:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Az azperfli.exe végrehajtása a korábbi leírtak szerint olyan eredményt kaphat, amely nem kifogástalan állapotot jelez. 

###### <a name="solution"></a>Megoldás

Az üzenetek et az a tény okozza, hogy a standard felügyelt lemezek nem kézbesítik az SAP-bővítmény által használt API-kat a standard Azure Storage-fiókok statisztikáinak ellenőrzéséhez. Ez nem aggodalomra ad okot. A standard lemeztároló-fiókok adatgyűjtési adatainak bevezetésének oka a gyakran előforduló I/O-k szabályozása volt. A felügyelt lemezek elkerülik az ilyen szabályozást azáltal, hogy korlátozzák a tárfiókban lévő lemezek számát. Ezért az ilyen típusú adatok hiánya nem kritikus fontosságú.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Néhány Azure-teljesítményszámláló hiányzik

Az Azure-beli teljesítménymutatókat egy démon gyűjti, amely több forrásból szerzi be az adatokat. Egyes konfigurációs adatok helyileg gyűjtik, és néhány teljesítmény metrikák az Azure Diagnosztika. A tárolószámlálók a tárban való előfizetés naplóiból származnak.

Az ismert problémák teljes és naprakész listáját az SAP [Note 1999351]című témakörben talál, amely további hibaelhárítási információkat tartalmaz az SAP-bővítményhez.

Ha az SAP Note [1999351] használatával történő hibaelhárítás `Set-AzVMAEMExtension` nem oldja meg a problémát, futtassa újra a konfigurációs parancsfájlt [az Azure Extension for SAP című][deployment-guide-4.5]témakörben leírtak szerint. Előfordulhat, hogy várnia kell egy órát, mert előfordulhat, hogy a tárolási elemzési vagy diagnosztikai számlálók nem jönnek létre közvetlenül az engedélyezésük után. Ha a probléma továbbra is fennáll, nyisson meg egy SAP-ügyfélszolgálati üzenetet a BC-OP-NT-AZR windowsos vagy BC-OP-LNX-AZR összetevőn egy Linux-alapú virtuális gépen.
