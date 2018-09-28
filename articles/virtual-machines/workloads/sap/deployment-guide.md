---
title: Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Linux virtuális gépeken az Azure-beli SAP-szoftvereket.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: sedusch
ms.openlocfilehash: 9208f2cb207daff2b122550fede48a8dda11d1db
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407926"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése
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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Az SAP az Azure Virtual Machines DBMS üzembe helyezése)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Virtuális gépek és VHD-gyorsítótárazás)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Szoftveres RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (A Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Egy RDBMS üzembe helyezési struktúrája)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Az Azure virtuális gépek magas rendelkezésre állás és vészhelyreállítás helyreállítási)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (Az SQL Server 2012 SP1 CU4 vagy újabb)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 vagy korábbi kiadásai)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Az Azure Marketplace-ről az SQL Server-lemezkép használatával)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Az SQL Server általános az SAP az Azure összegzése)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Az SQL Server relációsadatbázis-kezelő rendszer tulajdonságairól)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Tárolási konfiguráció)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Biztonsági mentés és visszaállítás)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Teljesítménnyel kapcsolatos biztonsági mentés és visszaállítás)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Egyéb)
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

[deployment-guide]:deployment-guide.md (Az SAP az Azure virtuális gépek üzembe helyezése)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Az SAP-erőforrások)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Virtuális gép egyéni rendszerkép használatával való központi telepítéséhez)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (1. forgatókönyv: Az SAP üzembe helyezése egy virtuális Gépet az Azure Marketplace-ről)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (2. forgatókönyv: Az SAP üzembe helyezése egy virtuális gép egyéni rendszerképpel)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (3. forgatókönyv: Virtuális gép áthelyezése a helyszínről az SAP az Azure nem általánosított virtuális merevlemez használata)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Az SAP a Microsoft Azure virtuális gépek központi telepítési forgatókönyvei)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell-parancsmagok telepítése)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Töltse le és importálja az SAP-kapcsolódó PowerShell-parancsmagok)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Egy a helyszíni tartomány – csak a Windows virtuális gépek csatlakoztatása)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Letöltése, telepítése és az Azure Virtuálisgép-ügynök engedélyezése)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell-lel)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Az Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Az SAP az Azure fejlett figyelési bővítmény konfigurálása)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Az Azure SAP Enhanced Monitoring készültség-ellenőrzés)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Az Azure figyelési infrastruktúra állapotának ellenőrzése)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Azure monitorozási SAP-hibaelhárítás)

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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Ellenőrzési és hibaelhárítási lehetőségek teljes körű figyelési beállításához)

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

[msdn-set-azurermvmaemextension]:https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmaemextension

[planning-guide]:planning-guide.md (Az Azure virtuális gépek tervezése és megvalósítása SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Erőforrások)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (SAP NetWeaver az Azure Virtual machines szolgáltatásban futó magas rendelkezésre állás és vészhelyreállítás helyreállítása)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Magas rendelkezésre állás az SAP-alkalmazáskiszolgálókhoz)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Az SAP-példányok automatikus indítása használatával)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Kizárólag felhőalapú - függőségek a helyszíni ügyfél hálózat nélkül az Azure-beli virtuális gépek telepítéséhez)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Létesítmények közötti – üzembe egyetlen vagy több SAP virtuális gép az Azure-ban teljesen integrálva van a helyszíni hálózat)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Az Azure-régiók)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Tartalék tartományok)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Frissítési tartományok)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Az Azure rendelkezésre állási csoportok)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Virtuális gépek fogalma a Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Az Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Egy adott ügyfél-rendszerképet a virtuális gép üzembe helyezése)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Felkészülés a virtuális gép áthelyezése a helyszínről az Azure-ba nem általánosított lemezzel)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Virtuális gép adott rendszerképpel ügyfél telepítése az SAP-előkészítése)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Az Azure az SAP virtuális gépek előkészítése)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Egy Azure-lemez és az Azure lemezkép közötti különbség)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Az Azure-bA a helyi VHD feltöltése)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure Storage-fiókok közötti lemezek másolása)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM-/ VHD-szerkezet SAP-környezetekhez)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (A csatlakoztatott lemezek automount beállítása)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (SAP netweaver bemutató/képzési forgatókönyv egyetlen virtuális gép)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Az SAP-példányok csak felhőalapú üzembe helyezés fogalmak)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitorozási megoldás az SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Az Azure Premium Storage)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (A felügyelt lemezek)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (A Microsoft Azure-hálózatkezelés)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: A Microsoft Azure Storage és az adatlemezek)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Az SAP-termék rendelkezésre állási mátrix)
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Üzembe helyezése és kezelése a virtuális gépek az Azure Resource Manager-sablonokkal és az Azure CLI használatával)
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

Azure-beli virtuális gépek a megoldás olyan szervezeteknek, amelyek a szükséges számítási és tárolási erőforrások, minimális és hosszadalmas beszerzési ciklusokat nélkül is. Használhatja az Azure Virtual Machines klasszikus alkalmazás, például az SAP NetWeaver-alapú alkalmazások, az Azure-beli üzembe helyezéséhez. Egy alkalmazás megbízhatósággal és rendelkezésre állással, a helyszíni erőforrások nélkül kiterjesztheti. Azure-beli virtuális gépek közötti kapcsolatot, támogatja, így az Azure virtuális gépek integrálása a szervezet helyszíni tartományok, a magánfelhők és az SAP-rendszer rendszeren.

Ez a cikk a virtuális gépek (VM) az Azure-ban, például az alternatív üzembe helyezési lehetőségeit, és hibaelhárítási SAP-alkalmazások telepítésének lépéseit ismerteti. Ebben a cikkben található információk épül [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára][planning-guide]. Azt is egészíti ki a dokumentáció SAP és SAP-megjegyzések, amelyek az elsődleges erőforrások telepítéséhez és az SAP-szoftverek üzembe helyezésére.

## <a name="prerequisites"></a>Előfeltételek
Az SAP szoftver központi telepítése Azure virtuális gép beállítása magában foglalja a több lépéseket és erőforrások. Mielőtt elkezdené, győződjön meg róla, hogy megfelelnek-e az Azure-beli virtuális gépeken SAP-szoftverek telepítéséhez szükséges előfeltételeket.

### <a name="local-computer"></a>Helyi számítógép
Windows vagy Linux rendszerű virtuális gépek kezelésére, használhatja a PowerShell-szkriptet, és az Azure Portalon. Mindkét eszközök Windows 7 vagy újabb verziója Windows rendszert futtató számítógépről kell. Ha csak a Linux rendszerű virtuális gépek kezeléséhez, és ebben a feladatban egy Linux rendszerű számítógép használni kívánt, Azure CLI-vel is használhatja.

### <a name="internet-connection"></a>Internetkapcsolat
Töltse le és futtassa az eszközök és az SAP-szoftverek központi telepítéséhez szükséges parancsprogramok, akkor kapcsolódnia kell az internethez. Az Azure virtuális Gépen, amelyen fut az Azure Enhanced Monitoring bővítményt az SAP is el kell érnie az interneten. Ha az Azure virtuális Gépen egy Azure virtuális hálózat vagy a helyszíni tartomány része, ellenőrizze, hogy, hogy a megfelelő proxybeállítások, leírtak szerint [konfigurálni a proxyt][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-előfizetés
Egy aktív Azure-fiókra van szüksége.

### <a name="topology-and-networking"></a>Topológia és a hálózatkezelés
Meg kell határoznia a topológia és az SAP üzemelő példány architektúra az Azure-ban:

* Azure storage-fiókot kell használni.
* Virtuális hálózat, ahol az SAP-rendszer központi telepítését szeretné
* Az erőforráscsoportot, amelyhez hozzá szeretné az SAP-rendszer központi telepítéséhez
* Azure-régió, ahol szeretné telepíteni az SAP-rendszerhez
* SAP-konfiguráció (kétrétegű és háromrétegű)
* Virtuálisgép-méretek és a további adatlemezek csatlakoztatása a virtuális gépek száma
* SAP-javítási és átviteli System (CTS) konfigurálása

Létrehozása és konfigurálása az Azure storage-fiókok (ha szükséges) vagy Azure virtuális hálózatok, az SAP szoftvertelepítési folyamat megkezdése előtt. Hozzon létre, és ezeket az erőforrásokat konfigurálása kapcsolatos információkért lásd: [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára][planning-guide].

### <a name="sap-sizing"></a>SAP méretezése
A következő adatokat, az SAP méretezéshez tudnia:

* Tervezett SAP számítási feladatok, például az SAP gyors Szimbólumméretező eszközt, és a SAP alkalmazás teljesítményének Standard (SAP) száma
* Szükséges erőforrás- és memória processzorfelhasználását az SAP-rendszerhez
* Kötelező bemeneti/kimeneti (I/O) műveletek száma másodpercenként
* Szükséges hálózati sávszélesség végleges, az Azure-beli virtuális gépek közötti kommunikáció
* Szükséges hálózati sávszélesség között a helyszíni eszközök és az Azure által üzembe helyezett SAP-rendszerhez

### <a name="resource-groups"></a>Erőforráscsoportok
Az Azure Resource Manager erőforráscsoportokat használhatja az alkalmazás-erőforrások kezelése az Azure-előfizetésében. További információkért lásd: [Azure Resource Manager áttekintése][resource-group-overview].

## <a name="resources"></a>További források

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Az SAP-erőforrások
Amikor állítja be az SAP szoftver központi telepítése, az alábbi SAP-erőforrások szüksége:

* SAP-Jegyzetnek [1928533], amely rendelkezik:
  * SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek
  * Azure-beli Virtuálisgép-méretek esetében fontos kapacitásadatok
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure

* SAP-Jegyzetnek [2015553] az Azure-beli SAP az SAP által támogatott szoftverek központi telepítéséhez szükséges előfeltételeket ismerteti.
* SAP-Jegyzetnek [2178632] részletes jelentett az Azure-beli SAP-figyelési metrikákat kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1409604] rendelkezik a szükséges SAP gazdagép-ügynök verziója a Windows Azure-ban.
* SAP-Jegyzetnek [2191498] rendelkezik a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP-Jegyzetnek [2243692] SAP linuxon az Azure-beli licenceléssel kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1984787] SUSE Linux Enterprise Server 12 vonatkozó általános információkat tartalmaz.
* SAP-Jegyzetnek [2002167] általános információkat tartalmaz arról Red Hat Enterprise Linux 7.x.
* SAP-Jegyzetnek [2069760] általános információkat tartalmaz arról Oracle Linux 7.x.
* SAP-Jegyzetnek [1999351] további információkat talál az Azure Enhanced Monitoring bővítményt az SAP rendelkezik.
* SAP-Jegyzetnek [1597355] Linux lapozóterület vonatkozó általános információkat tartalmaz.
* [Az SAP az Azure Állapotváltozás oldalon](https://wiki.scn.sap.com/wiki/x/Pia7Gg) hírek és hasznos segédanyagok gyűjteménye van.
* [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzések Linux rendszeren.
* Az SAP-specifikus PowerShell-parancsmagok részét képező [Azure PowerShell-lel][azure-ps].
* Az SAP-specifikus Azure CLI-parancsokat, amelyek részei [Azure CLI-vel][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-erőforrások
Ezek a cikkek a Microsoft Azure-beli SAP-környezetekhez terjed ki:

* [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése, az SAP NetWeaver (Ez a cikk)][deployment-guide]
* [Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure virtuális gépeken SAP-szoftverek központi telepítési forgatókönyvei
Több lehetőség áll rendelkezésére az Azure-ban társított lemezek és virtuális gépek üzembe helyezéséhez. Fontos központi telepítési lehetőségek közötti különbségek megértéséhez, mert előfordulhat, hogy a virtuális gépek előkészítése a választott központi telepítési típus alapján különböző lépések végrehajtása.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>1. forgatókönyv: Az SAP üzembe helyezése egy virtuális Gépet az Azure Marketplace-ről
A virtuális gép üzembe helyezéséhez használhatja a Microsoft vagy az Azure piactér a harmadik fél által biztosított rendszerképet. A piactér egyes szabványos operációsrendszer-lemezképek a Windows Server és a különböző Linux-disztribúciót kínál. Is telepítheti egy lemezképet, amely tartalmazza az adatbázis-kezelő rendszer (DBMS) SKU-k, például a Microsoft SQL Server. Képek DBMS termékváltozatok való használatával kapcsolatos további információkért lásd: [SAP NetWeaver az Azure Virtual Machines DBMS üzembe][dbms-guide].

Az alábbi folyamatábra bemutatja egy virtuális Gépet az Azure Marketplace-ről telepítésének lépéseit a SAP-specifikus folyamata:

![Az SAP-rendszereit az Azure Marketplace-ről egy Virtuálisgép-lemezkép használatával a virtuális gép üzembe helyezésének folyamatábrája][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása az Azure portal használatával
Az új virtuális gép létrehozása az Azure Piactérről származó lemezkép legegyszerűbben az Azure portal használatával.

1.  Nyissa meg a következőt: <https://portal.azure.com/#create/hub>.  Vagy az Azure portál menüjében válassza **+ új**.
1.  Válassza ki **számítási**, majd válassza ki a telepíteni kívánt operációs rendszer típusát. Ha például a Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) vagy Oracle Linux 7.2. Az alapértelmezett listanézetben nem jeleníti meg az összes támogatott operációs rendszereket. Válassza ki **összes** teljes listáját. SAP-szoftverek központi telepítéséhez támogatott operációs rendszerekkel kapcsolatos további információkért tekintse meg az SAP-Jegyzetnek [1928533].
1.  A következő oldalon tekintse át a használati feltételeket.
1.  Az a **telepítési modell kiválasztása** jelölje ki **Resource Manager**.
1.  Kattintson a **Létrehozás** gombra.

A varázsló végigvezeti Önt az összes szükséges erőforrást, például a hálózati adapterek és a storage-fiókok mellett a virtuális gép létrehozásához szükséges paramétereket. Ezek a paraméterek a következők:

1. **Alapvető beállítások**:
 * **Név**: az erőforrás (a virtuális gép neve) nevét.
 * **Virtuális merevlemez típusa**: válassza ki az operációsrendszer-lemez a lemez típusát. Ha azt szeretné, prémium szintű Storage használata az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemez használatát javasoljuk.
 * **Felhasználónév és jelszó** vagy **nyilvános SSH-kulcs**: Adja meg a felhasználónevet és jelszót a kiépítés során létrehozott felhasználó. Egy Linux rendszerű virtuális gépek nyilvános Secure Shell (SSH) kulcs segítségével jelentkezzen be a gép is megadhatja.
 * **Előfizetés**: az új virtuális gép kiépítéséhez használni kívánt előfizetés kiválasztásához.
 * **Erőforráscsoport**: a virtuális gép az erőforráscsoport nevét. Egy új erőforráscsoport nevére és a egy már létező erőforráscsoport nevét is megadhatja.
 * **Hely**: az üzembe helyezés az új virtuális gépet. Ha azt szeretné, a helyszíni hálózat a virtuális géphez csatlakozni, ellenőrizze, hogy a hely, amely kapcsolódik a helyszíni hálózat az Azure virtuális hálózat kiválasztása. További információkért lásd: [Microsoft Azure-hálózatkezelés] [ planning-guide-microsoft-azure-networking] a [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára] [ planning-guide].
1. **Méret**:

     Támogatott Virtuálisgép-típusok listáját, tekintse meg az SAP-Jegyzetnek [1928533]. Győződjön meg arról, hogy a helyes VM-típust választja, ha azt szeretné használni az Azure Premium Storage. Nem minden Virtuálisgép-típusokat támogatja a Premium Storage. További információkért lásd: [Storage: a Microsoft Azure Storage és az adatlemezek] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] és [Azure Premium Storage] [ planning-guide-azure-premium-storage] a [ Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver][planning-guide].

1. **Beállítások**:
  * **Storage**
    * **Lemez típusa**: válassza ki az operációsrendszer-lemez a lemez típusát. Ha azt szeretné, prémium szintű Storage használata az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemez használatát javasoljuk.
    * **Felügyelt lemezek használata**: Ha azt szeretné, felügyelt lemezeket használni, válassza az Igen lehetőséget. Felügyelt lemezekkel kapcsolatos további információkért lásd: fejezet [Managed Disks] [ planning-guide-managed-disks] a tervezési útmutatóban.
    * **Storage-fiók**: Válasszon ki egy meglévő tárfiókot vagy hozzon létre egy újat. Nem minden tárolási típusok futó SAP-alkalmazások esetében működik. További információ a tárolási típusok: [a Microsoft Azure Storage] [ dbms-guide-2.3] a [SAP NetWeaver az Azure Virtual Machines DBMS üzembe] [ dbms-guide].
  * **Hálózat**
    * **Virtuális hálózat** és **alhálózati**: a virtuális gép integrálhatja az intraneten, válassza ki a virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
    * **Nyilvános IP-cím**: válassza ki a használni kívánt nyilvános IP-cím, vagy adjon meg egy új nyilvános IP-cím létrehozása a paramétereket. Használhatja a nyilvános IP-címet a virtuális gép eléréséhez az interneten keresztül. Ellenőrizze, hogy biztonságos hozzáférést könnyíti meg a virtuális gép hálózati biztonsági csoport létrehozása.
    * **Hálózati biztonsági csoport**: további információkért lásd: [irányíthatja a hálózati forgalmat hálózati biztonsági csoportokkal][virtual-networks-nsg].
  * **Bővítmények**: telepítése virtuálisgép-bővítmények a központi telepítési hozzáadásával. Nem kell ebben a lépésben-kiterjesztések hozzáadása. A bővítmények SAP támogatási szükséges később telepítve. Tekintse meg a fejezet [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP] [ deployment-guide-4.5] ebben az útmutatóban.
  * **Magas rendelkezésre állású**: Válasszon ki egy rendelkezésre állási csoportot, vagy adja meg a paramétereket egy új rendelkezésre állási csoport létrehozása. További információkért lásd: [Azure rendelkezésre állási csoportok][planning-guide-3.2.3].
  * **Monitorozás**
    * **Rendszerindítási diagnosztika**: választhat **letiltása** a rendszerindítási diagnosztika.
    * **Vendég operációs rendszer diagnosztikája**: választhat **letiltása** figyelés, diagnosztika.

1. **Összefoglalás**:

  Tekintse át a beállításokat, és válassza ki **OK**.

A virtuális gép telepítve van a kiválasztott erőforráscsoportban.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Virtuális gép létrehozása sablon használatával
Létrehozhat egy virtuális gépet a közzétett SAP-sablonok használatával a [azure-gyorssablonok GitHub-adattár][azure-quickstart-templates-github]. Emellett manuálisan létrehozhat egy virtuális gép használatával a [az Azure portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], vagy [Azure CLI] [virtual-machines-linux-tutorial].

* [**Kétrétegű konfigurációja (csak egy virtuális gép) sablon** (sap-2-szint-marketplace-lemezkép)][sap-templates-2-tier-marketplace-image]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép használatával hozhat létre.
* [**Kétrétegű konfigurációja (csak egy virtuális gép) sablon - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép és a Managed Disks használatával hozhat létre.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon** (sap-3-szint-marketplace-lemezkép)][sap-templates-3-tier-marketplace-image]

  Egy három rétegű rendszer használatával több virtuális gép létrehozásához használja ezt a sablont.
* [**Három-réteg konfigurációjához (több virtuális gép) sablon - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Virtuális gépek és a Managed Disks használatával hozzon létre egy három rétegű system, használja ezt a sablont.

Az Azure Portalon adja meg a sablon a következő paraméterekkel:

1. **Alapvető beállítások**:
  * **Előfizetés**: az a sablon üzembe helyezéséhez használni kívánt előfizetést.
  * **Erőforráscsoport**: az erőforráscsoport a sablon üzembe helyezéséhez. Létrehozhat egy új erőforráscsoportot, vagy egy meglévő erőforráscsoportot kiválaszthatja az előfizetésben.
  * **Hely**: az üzembe helyezés a sablont. Ha egy meglévő erőforráscsoportot, szolgál az erőforráscsoport helyét.

1. **Beállítások**:
  * **Az SAP-rendszer-azonosító**: az SAP-rendszer-azonosító (SID).
  * **Operációs rendszer típusa**: A telepítendő, például operációs rendszert, a Windows Server 2012 R2, a SUSE Linux Enterprise Server 12 (SLES 12), a Red Hat Enterprise Linux 7.2 (RHEL 7.2) vagy a Oracle Linux 7.2.

    A lista nézet nem jeleníti meg az összes támogatott operációs rendszereket. SAP-szoftverek központi telepítéséhez támogatott operációs rendszerekkel kapcsolatos további információkért tekintse meg az SAP-Jegyzetnek [1928533].
  * **Az SAP-rendszer mérete**: az SAP-rendszer méretét.

    Az új rendszer biztosít SAP száma. Ha nem, hogy a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  * **Rendszer rendelkezésre állását** (csak a háromrétegű sablon): A rendszer rendelkezésre állását.

    Válassza ki **magas rendelkezésre ÁLLÁSÚ** konfiguráció, amely lehetővé teszi magas rendelkezésre állású telepítéshez. Két adatbázis-kiszolgáló és a két kiszolgáló a ABAP SAP Central Services (ASCS) jönnek létre.
  * **Tárolási típus** (csak a sablon kétrétegű): használandó tárolási típus.

    Olyan nagyobb rendszerekhez erősen ajánlott az Azure Premium Storage tárolást használ. További információ a tárolási típusokat az alábbi forrásokban talál:
      * [SAP DBMS-példány használatát az Azure prémium szintű SSD-tárolóval][2367194]
      * [A Microsoft Azure Storage] [ dbms-guide-2.3] a [SAP NetWeaver az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
      * [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
  * **Rendszergazdai felhasználónév** és **rendszergazdai jelszó**: felhasználónévvel és jelszóval.
    Új felhasználó létrehozása a virtuális géphez való bejelentkezéshez.
  * **Új vagy meglévő alhálózaton**: azt határozza meg, hogy létrehozott egy új virtuális hálózatot és alhálózatot, vagy egy létező alhálózatot. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
  * **Alhálózati azonosító**: Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/&lt;előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;virtuális hálózat neve > /subnets/&lt;alhálózat neve >

1. **Feltételek és kikötések**:  
    Tekintse át és fogadja el a jogi feltételeket.

1.  Válassza a **Beszerzés** lehetőséget.

Az Azure Virtuálisgép-ügynök alapértelmezés szerint telepítve van, az Azure Piactérről származó lemezkép használatakor.

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
A helyszíni hálózat konfigurációjától függően szükség lehet a virtuális Gépen a proxy beállítása. Ha a virtuális gép csatlakoztatva van a helyszíni hálózat, a virtuális gép VPN vagy ExpressRoute-n keresztül nem lehet csatlakozni az internethez, és nem tudják letölteni a szükséges bővítmények vagy figyelési adatokat gyűjteni. További információkért lásd: [konfigurálni a proxyt][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)
Ha egy helyszíni Active Directory vagy a DNS-példányt az Azure site-to-site VPN-kapcsolat vagy ExpressRoute-n keresztül csatlakozik az Azure-alapú (ezt nevezik *létesítmények közötti* a [Azure virtuális gépek tervezése és az SAP NetWeaver megvalósítása][planning-guide]), valószínű, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ez a feladat szempontjai kapcsolatos további információkért lásd: [csatlakoztatni egy virtuális Gépet egy helyszíni tartományban (csak Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Figyelés konfigurálása
SAP, hogy támogatja-e a környezet beállítása az SAP az Azure Monitoring bővítmény leírtak szerint [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-4.5]. SAP-figyelés az Előfeltételek ellenőrzése és a szükséges minimális verziója a SAP Kernel és SAP gazdagép-ügynök, az erőforrások szereplő [SAP erőforrások][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Figyelésének ellenőrzése
Ellenőrizze, hogy figyelési működik, leírtak szerint [ellenőrzési és hibaelhárítási lehetőségek teljes körű figyelési beállításának][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések
Miután létrehozta a virtuális gép és a virtuális gép üzemel, a szükséges összetevők telepítését a virtuális gépen kell. Az ilyen típusú virtuális gépek üzembe helyezése az üzembe helyezés/szoftverfrissítési telepítés feladatütemezési miatt a szoftver telepítése már elérhetőnek kell lennie, vagy az Azure-ban, egy másik virtuális Géphez, vagy csatolt lemez. Másik lehetőségként fontolja meg egy létesítmények közötti forgatókönyv, mely a helyszíni kapcsolat az eszközök (telepítési megosztások) van megadva.

Miután telepítette a virtuális gép az Azure-ban, hajtsa végre az irányelvek és az eszközöket, az SAP-szoftverek telepítése a virtuális Gépen, mint a helyszíni környezetben. SAP-szoftverek telepítése egy Azure-beli virtuális gépen, SAP és a Microsoft javasolja, hogy töltse fel, és az Azure virtuális merevlemezek vagy a Managed Disks tárolnia az SAP telepítési adathordozóján, vagy létrehozhat egy Azure virtuális Gépen, amely működik, mint egy fájlkiszolgálóval, amely rendelkezik az összes szükséges SAP telepítési adathordozót.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>2. forgatókönyv: Az SAP üzembe helyezése egy virtuális gép egyéni rendszerképpel
Mert az operációs rendszer vagy az adatbázis-kezelő különböző verziói különböző patch-követelményekkel rendelkeznek, a képek, az Azure Marketplace-en található előfordulhat, hogy nem az igényeinek. Ehelyett érdemes a saját operációs rendszer/adatbázis-kezelő Virtuálisgép-rendszerképet, amelyeket később újra üzembe a virtuális gép létrehozásához.
Különböző lépések segítségével hozzon létre egy a Windows mint Linux-privát rendszerkép létrehozása.

- - -
> ![Windows][Logo_Windows] Windows
>
> Több virtuális gépek telepítéséhez használhatja a Windows-lemezkép előkészítése, a Windows-beállítások (például a Windows biztonsági AZONOSÍTÓK és állomásnév) kell emeli ki, vagy a helyszíni virtuális gép általánosítva. Használhat [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) ehhez.
>
> ![Linux][Logo_Linux] Linux
>
> Előkészítése, amelyek segítségével több virtuális gép üzembe helyezése Linux-rendszerképen, egyes Linux-beállításokat kell emeli ki, vagy a helyszíni virtuális gép általánosítva. Használhat `waagent -deprovision` ehhez. További információkért lásd: [az Azure-ban futó Linux rendszerű virtuális gép rögzítése] [ virtual-machines-linux-capture-image] és a [Azure Linux-ügynök használati útmutatója][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Készítse elő és hozzon létre egy egyéni rendszerképet, és, amellyel több új virtuális gép létrehozása. Erről a [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára][planning-guide]. Állíthatók az adatbázisok tartalmát egy új SAP-rendszerhez (adatbázis biztonsági mentésének visszaállítja egy lemezről, amely a virtuális géphez csatolt) telepítése a SAP Software kiépítés Manager segítségével, vagy közvetlenül az Azure storage-ból az adatbázis biztonsági mentésének visszaállítása esetén az adatbázis-kezelő támogatja azt. További információkért lásd: [SAP NetWeaver az Azure Virtual Machines DBMS üzembe][dbms-guide]. Ha már telepítette a helyszíni virtuális gép (különösen a kétrétegű rendszerekhez) egy SAP-rendszerrel, módosíthatja a SAP-rendszer beállításait az Azure virtuális gépek üzembe helyezése után is eljárással nevezze át a rendszer által SAP Software kiépítés Manager (SAP támogatott Vegye figyelembe [1619720]). Ellenkező esetben az SAP-szoftvereket telepíteni, az Azure virtuális gép üzembe helyezése után.

Az alábbi folyamatábra bemutatja a SAP-specifikus lépéssorozattal egyéni rendszerképből egy virtuális gép üzembe helyezéséhez:

![Az SAP-rendszereinket privát piactéren Virtuálisgép-rendszerkép használatával a virtuális gép üzembe helyezésének folyamatábrája][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Virtuális gép létrehozása az Azure portal használatával
Az új virtuális gép létrehozása felügyelt lemez rendszerképének a legegyszerűbben az Azure portal használatával. Kezelése lemezképét létrehozásával kapcsolatos további információkért olvassa el [az Azure-beli általánosított virtuális gép felügyelt rendszerképének rögzítése](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Nyissa meg a következőt: <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Vagy az Azure portál menüjében válassza **lemezképek**.
1.  Válassza ki a felügyelt lemez rendszerképének üzembe helyezéséhez, és kattintson a kívánt **virtuális gép létrehozása**

A varázsló végigvezeti Önt az összes szükséges erőforrást, például a hálózati adapterek és a storage-fiókok mellett a virtuális gép létrehozásához szükséges paramétereket. Ezek a paraméterek a következők:

1. **Alapvető beállítások**:
 * **Név**: az erőforrás (a virtuális gép neve) nevét.
 * **Virtuális merevlemez típusa**: válassza ki az operációsrendszer-lemez a lemez típusát. Ha azt szeretné, prémium szintű Storage használata az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemez használatát javasoljuk.
 * **Felhasználónév és jelszó** vagy **nyilvános SSH-kulcs**: Adja meg a felhasználónevet és jelszót a kiépítés során létrehozott felhasználó. Egy Linux rendszerű virtuális gépek nyilvános Secure Shell (SSH) kulcs segítségével jelentkezzen be a gép is megadhatja.
 * **Előfizetés**: az új virtuális gép kiépítéséhez használni kívánt előfizetés kiválasztásához.
 * **Erőforráscsoport**: a virtuális gép az erőforráscsoport nevét. Egy új erőforráscsoport nevére és a egy már létező erőforráscsoport nevét is megadhatja.
 * **Hely**: az üzembe helyezés az új virtuális gépet. Ha azt szeretné, a helyszíni hálózat a virtuális géphez csatlakozni, ellenőrizze, hogy a hely, amely kapcsolódik a helyszíni hálózat az Azure virtuális hálózat kiválasztása. További információkért lásd: [Microsoft Azure-hálózatkezelés] [ planning-guide-microsoft-azure-networking] a [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára] [ planning-guide].
1. **Méret**:

     Támogatott Virtuálisgép-típusok listáját, tekintse meg az SAP-Jegyzetnek [1928533]. Győződjön meg arról, hogy a helyes VM-típust választja, ha azt szeretné használni az Azure Premium Storage. Nem minden Virtuálisgép-típusokat támogatja a Premium Storage. További információkért lásd: [Storage: a Microsoft Azure Storage és az adatlemezek] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] és [Azure Premium Storage] [ planning-guide-azure-premium-storage] a [ Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver][planning-guide].

1. **Beállítások**:
  * **Storage**
    * **Lemez típusa**: válassza ki az operációsrendszer-lemez a lemez típusát. Ha azt szeretné, prémium szintű Storage használata az adatlemezek, prémium szintű Storage, valamint az operációsrendszer-lemez használatát javasoljuk.
    * **Felügyelt lemezek használata**: Ha azt szeretné, felügyelt lemezeket használni, válassza az Igen lehetőséget. Felügyelt lemezekkel kapcsolatos további információkért lásd: fejezet [Managed Disks] [ planning-guide-managed-disks] a tervezési útmutatóban.
  * **Hálózat**
    * **Virtuális hálózat** és **alhálózati**: a virtuális gép integrálhatja az intraneten, válassza ki a virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
    * **Nyilvános IP-cím**: válassza ki a használni kívánt nyilvános IP-cím, vagy adjon meg egy új nyilvános IP-cím létrehozása a paramétereket. Használhatja a nyilvános IP-címet a virtuális gép eléréséhez az interneten keresztül. Ellenőrizze, hogy biztonságos hozzáférést könnyíti meg a virtuális gép hálózati biztonsági csoport létrehozása.
    * **Hálózati biztonsági csoport**: további információkért lásd: [irányíthatja a hálózati forgalmat hálózati biztonsági csoportokkal][virtual-networks-nsg].
  * **Bővítmények**: telepítése virtuálisgép-bővítmények a központi telepítési hozzáadásával. Nem kell ebben a lépésben adja hozzá a bővítményt. A bővítmények SAP támogatási szükséges később telepítve. Tekintse meg a fejezet [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP] [ deployment-guide-4.5] ebben az útmutatóban.
  * **Magas rendelkezésre állású**: Válasszon ki egy rendelkezésre állási csoportot, vagy adja meg a paramétereket egy új rendelkezésre állási csoport létrehozása. További információkért lásd: [Azure rendelkezésre állási csoportok][planning-guide-3.2.3].
  * **Monitorozás**
    * **Rendszerindítási diagnosztika**: választhat **letiltása** a rendszerindítási diagnosztika.
    * **Vendég operációs rendszer diagnosztikája**: választhat **letiltása** figyelés, diagnosztika.

1. **Összefoglalás**:

  Tekintse át a beállításokat, és válassza ki **OK**.

A virtuális gép telepítve van a kiválasztott erőforráscsoportban.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Virtuális gép létrehozása sablon használatával
Üzembe helyezés létrehozása egy privát operációsrendszer-lemezkép az Azure Portal használatával, használja az alábbi SAP-sablonok egyikét. Ezek a sablonok teszik közzé a [azure-gyorssablonok GitHub-adattár][azure-quickstart-templates-github]. Emellett manuálisan létrehozhat egy virtuális gép használatával [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Kétrétegű konfigurációja (csak egy virtuális gép) sablon** (sap-2-szint – felhasználói-lemezkép)][sap-templates-2-tier-user-image]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép használatával hozhat létre.
* [**Kétrétegű konfigurációja (csak egy virtuális gép) sablon – felügyelt lemez rendszerképének** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Ez a sablon segítségével csak egy virtuális gép és a egy felügyelt lemez rendszerképének használatával egy kétszintű rendszer hozhat létre.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon** (sap-3 – szint – felhasználói-lemezkép)][sap-templates-3-tier-user-image]

  Ez a sablon segítségével egy háromszintű rendszer hozhat létre több virtuális gép vagy a saját operációsrendszer-lemezkép használatával.
* [**Háromrétegű konfigurációs (több virtuális gép) sablon – felügyelt lemez rendszerképének** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Hozzon létre egy három rétegű rendszer több virtuális gép vagy a saját operációsrendszer-lemezkép és a egy felügyelt lemez rendszerképének használatával, használja ezt a sablont.

Az Azure Portalon adja meg a sablon a következő paraméterekkel:

1. **Alapvető beállítások**:
  * **Előfizetés**: az a sablon üzembe helyezéséhez használni kívánt előfizetést.
  * **Erőforráscsoport**: az erőforráscsoport a sablon üzembe helyezéséhez. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az előfizetésben.
  * **Hely**: az üzembe helyezés a sablont. Ha egy meglévő erőforráscsoportot, szolgál az erőforráscsoport helyét.
1. **Beállítások**:
  * **Az SAP-rendszer-azonosító**: az SAP-rendszer azonosítója.
  * **Operációs rendszer típusa**: az operációs rendszer típusa (Windows vagy Linux) telepíteni kívánja.
  * **Az SAP-rendszer mérete**: az SAP-rendszer méretét.

    Az új rendszer biztosít SAP száma. Ha nem, hogy a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  * **Rendszer rendelkezésre állását** (csak a háromrétegű sablon): A rendszer rendelkezésre állását.

    Válassza ki **magas rendelkezésre ÁLLÁSÚ** konfiguráció, amely lehetővé teszi magas rendelkezésre állású telepítéshez. Két adatbázis-kiszolgáló és -ASCS két kiszolgálói jönnek létre.
  * **Tárolási típus** (csak a sablon kétrétegű): használandó tárolási típus.

    Olyan nagyobb rendszerekhez erősen ajánlott az Azure Premium Storage tárolást használ. Tárolási típusaival kapcsolatos további információkért lásd a következőket:
      * [SAP DBMS-példány használatát az Azure prémium szintű SSD-tárolóval][2367194]
      * [A Microsoft Azure Storage] [ dbms-guide-2.3] a [SAP NetWeaver az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
      * [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
  * **Felhasználói lemezkép VHD URI** (csak a nem felügyelt lemez kép sablon esetében): az URI-ját a saját operációs rendszerkép VHD-t, például a https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **Felhasználói lemezkép tárfiókja** (csak a nem felügyelt lemez kép sablon esetében): a saját operációsrendszer-lemezkép tárolására, például a tárfiók nevére &lt;accountname > a https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (csak felügyelt lemez kép sablon): a használni kívánt felügyelt lemez rendszerképének azonosítója
  * **Rendszergazdai felhasználónév** és **rendszergazdai jelszó**: A felhasználónevet és jelszót.

    Új felhasználó létrehozása a virtuális géphez való bejelentkezéshez.
  * **Új vagy meglévő alhálózaton**: azt határozza meg, hogy létrejön egy új virtuális hálózatot és alhálózatot, vagy egy létező alhálózatot. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
  * **Alhálózati azonosító**: Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/&lt;előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;virtuális hálózat neve > /subnets/&lt;alhálózat neve >

1. **Feltételek és kikötések**:  
    Tekintse át és fogadja el a jogi feltételeket.

1.  Válassza a **Beszerzés** lehetőséget.

#### <a name="install-the-vm-agent-linux-only"></a>Telepítse a Virtuálisgép-ügynök (csak Linux)
Az előző szakaszban leírt a sablonok használatához a Linux-ügynök már telepítve kell lennie a felhasználói lemezkép, vagy a telepítés meghiúsul. Töltse le és telepítse a Virtuálisgép-ügynök a felhasználói lemezkép leírtak szerint [letöltése, telepítése és engedélyezése az Azure Virtuálisgép-ügynök][deployment-guide-4.4]. Ha nem használja a sablonokat, is telepítheti a Virtuálisgép-ügynök később.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)
Ha egy helyszíni Active Directory vagy a DNS-példányt az Azure site-to-site VPN-kapcsolat vagy Azure ExpressRoute használatával csatlakozik az Azure-alapú (ezt nevezik *létesítmények közötti* a [Azure Virtual machines szolgáltatásban tervezés és megvalósítás az SAP NetWeaver számára][planning-guide]), valószínű, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ebben a lépésben szempontjai kapcsolatos további információkért lásd: [csatlakoztatni egy virtuális Gépet egy helyszíni tartományban (csak Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
A helyszíni hálózat konfigurációjától függően szükség lehet a virtuális Gépen a proxy beállítása. Ha a virtuális gép csatlakoztatva van a helyszíni hálózat, a virtuális gép VPN vagy ExpressRoute-n keresztül nem lehet csatlakozni az internethez, és nem tudják letölteni a szükséges bővítmények vagy figyelési adatokat gyűjteni. További információkért lásd: [konfigurálni a proxyt][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Figyelés konfigurálása
SAP, hogy támogatja-e a környezet beállítása az SAP az Azure Monitoring bővítmény leírtak szerint [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-4.5]. SAP-figyelés az Előfeltételek ellenőrzése és a szükséges minimális verziója a SAP Kernel és SAP gazdagép-ügynök, az erőforrások szereplő [SAP erőforrások][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Figyelésének ellenőrzése
Ellenőrizze, hogy figyelési működik, leírtak szerint [ellenőrzési és hibaelhárítási lehetőségek teljes körű figyelési beállításának][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>3. forgatókönyv: Egy a helyszíni virtuális gép áthelyezése az SAP az Azure nem általánosított virtuális merevlemez használatával
Ebben a forgatókönyvben áthelyezését tervezi egy megadott SAP-rendszer egy a helyszíni környezetből az Azure-bA. Ehhez a virtuális Merevlemezt, amely rendelkezik az operációs rendszer, az SAP bináris fájljait és végül az adatbázis-kezelő bináris fájljainak, valamint a virtuális merevlemezek feltöltését a adathoz és naplófájlhoz az adatbázis-kezelő, az Azure-ba, a. Ismertetett forgatókönyv eltérően [2. forgatókönyv: egyéni rendszerkép rendelkező virtuális gép telepítése az SAP][deployment-guide-3.3], ebben az esetben az állomásnevet, SAP SID, maradjon, és SAP-felhasználói fiókok az Azure-beli virtuális gépen, mert a megadott konfiguráció a helyszíni környezetben. Nem kell az operációs rendszert általánosítani. Ebben a forgatókönyvben általában létesítmények közötti forgatókönyvek, ahol részei az SAP-rendszeren fut, a helyszíni és a egy része futtatható Azure-on vonatkozik.

Ebben a forgatókönyvben a Virtuálisgép-ügynök nem **nem** automatikusan telepített üzembe helyezés során. Mivel a Virtuálisgép-ügynök és az Azure Enhanced Monitoring bővítményt az SAP az SAP NetWeaver futtatása az Azure-ban van szükség, akkor kell letöltése, telepítése és a virtuális gép létrehozása után manuálisan engedélyezze a mindkét összetevő.

További információ az Azure Virtuálisgép-ügynök a következő forrásanyagokban talál.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Az Azure virtuálisgép-ügynök – áttekintés][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux-ügynök használati útmutatója][virtual-machines-linux-agent-user-guide]
>
>

- - -

Az alábbi folyamatábra bemutatja a feladatütemezési lépéseket egy helyszíni virtuális gép áthelyezése az Azure nem általánosított virtuális merevlemez használatával:

![Az SAP-rendszereinket Virtuálisgép-lemez a virtuális gép üzembe helyezésének folyamatábrája][deployment-guide-figure-400]

Ha a lemez már feltöltött és az Azure-ban definiált (lásd: [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára][planning-guide]), hajtsa végre a tevékenységek leírása a következő néhány szakaszban.

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Üzembe helyezés létrehozása az Azure Portalon keresztül egy privát operációsrendszer-lemez használatával, a SAP-sablont, a közzétett használja a [azure-gyorssablonok GitHub-adattár][azure-quickstart-templates-github]. Emellett manuálisan létrehozhat egy virtuális gépet PowerShell-lel.

* [**Kétrétegű konfigurációja (csak egy virtuális gép) sablon** (sap-2-szint – felhasználó-lemez)][sap-templates-2-tier-os-disk]

  Ez a sablon segítségével egy kétszintű rendszer csak egy virtuális gép használatával hozhat létre.
* [**Kétrétegű konfigurációja (csak egy virtuális gép) sablon – felügyelt lemez** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Csak egy virtuális gép és a egy felügyelt lemezt egy kétszintű rendszer létrehozásához használja ezt a sablont.

Az Azure Portalon adja meg a sablon a következő paraméterekkel:

1. **Alapvető beállítások**:
  * **Előfizetés**: az a sablon üzembe helyezéséhez használni kívánt előfizetést.
  * **Erőforráscsoport**: az erőforráscsoport a sablon üzembe helyezéséhez. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az előfizetésben.
  * **Hely**: az üzembe helyezés a sablont. Ha egy meglévő erőforráscsoportot, szolgál az erőforráscsoport helyét.
1. **Beállítások**:
  * **Az SAP-rendszer-azonosító**: az SAP-rendszer azonosítója.
  * **Operációs rendszer típusa**: az operációs rendszer típusa (Windows vagy Linux) telepíteni kívánja.
  * **Az SAP-rendszer mérete**: az SAP-rendszer méretét.

    Az új rendszer biztosít SAP száma. Ha nem, hogy a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  * **Tárolási típus** (csak a sablon kétrétegű): használandó tárolási típus.

    Olyan nagyobb rendszerekhez erősen ajánlott az Azure Premium Storage tárolást használ. Tárolási típusaival kapcsolatos további információkért lásd a következőket:
      * [SAP DBMS-példány használatát az Azure prémium szintű SSD-tárolóval][2367194]
      * [A Microsoft Azure Storage] [ dbms-guide-2.3] a [SAP NetWeaver az Azure virtuális gép DBMS üzembe helyezése][dbms-guide]
      * [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz][storage-premium-storage-preview-portal]
      * [A Microsoft Azure Storage bemutatása][storage-introduction]
  * **Az operációs rendszer lemez VHD URI** (csak a sablon nem felügyelt lemez): az URI a saját operációsrendszer-lemezről, például a https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Felügyelt lemez azonosítója az operációs rendszer lemezén** (csak felügyelt lemez sablon): a felügyelt lemez operációsrendszer-lemezről, az azonosító /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Új vagy meglévő alhálózaton**: azt határozza meg, hogy létrehozott egy új virtuális hálózatot és alhálózatot, vagy egy létező alhálózatot. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
  * **Alhálózati azonosító**: Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/&lt;előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;virtuális hálózat neve > /subnets/&lt;alhálózat neve >

1. **Feltételek és kikötések**:  
    Tekintse át és fogadja el a jogi feltételeket.

1.  Válassza a **Beszerzés** lehetőséget.

#### <a name="install-the-vm-agent"></a>Virtuálisgép-ügynök telepítése
Az előző szakaszban leírt a sablonok használatához a virtuális gép ügynököt telepíteni kell az operációsrendszer-lemez a, vagy a telepítés meghiúsul. Töltse le és telepítse a Virtuálisgép-ügynököt a virtuális gép leírtak szerint [letöltése, telepítése és engedélyezése az Azure Virtuálisgép-ügynök][deployment-guide-4.4].

Ha nem használja a sablonokat az előző szakaszban leírt, később is telepítheti a Virtuálisgép-ügynök.

#### <a name="join-a-domain-windows-only"></a>Csatlakozás tartományhoz (csak Windows)
Ha egy helyszíni Active Directory vagy a DNS-példányt az Azure site-to-site VPN-kapcsolat vagy ExpressRoute-n keresztül csatlakozik az Azure-alapú (ezt nevezik *létesítmények közötti* a [Azure virtuális gépek tervezése és az SAP NetWeaver megvalósítása][planning-guide]), valószínű, hogy a virtuális gép csatlakozik egy helyszíni tartományban. Ez a feladat szempontjai kapcsolatos további információkért lásd: [csatlakoztatni egy virtuális Gépet egy helyszíni tartományban (csak Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
A helyszíni hálózat konfigurációjától függően szükség lehet a virtuális Gépen a proxy beállítása. Ha a virtuális gép csatlakoztatva van a helyszíni hálózat, a virtuális gép VPN vagy ExpressRoute-n keresztül nem lehet csatlakozni az internethez, és nem tudják letölteni a szükséges bővítmények vagy figyelési adatokat gyűjteni. További információkért lásd: [konfigurálni a proxyt][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Figyelés konfigurálása
SAP, hogy támogatja-e a környezet beállítása az SAP az Azure Monitoring bővítmény leírtak szerint [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-4.5]. SAP-figyelés az Előfeltételek ellenőrzése és a szükséges minimális verziója a SAP Kernel és SAP gazdagép-ügynök, az erőforrások szereplő [SAP erőforrások][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Figyelésének ellenőrzése
Ellenőrizze, hogy figyelési működik, leírtak szerint [ellenőrzési és hibaelhárítási lehetőségek teljes körű figyelési beállításának][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Az SAP-figyelési konfigurációjának frissítése
Az alábbi esetek bármelyikében SAP figyelési konfigurációjának frissítése:
* A közös Microsoft/SAP csapata figyelési bővíti ki, és több vagy kevesebb számlálók kér.
* A Microsoft elérhetővé tette az alapul szolgáló Azure-infrastruktúra, amely a monitorozási adatokat biztosít egy új verziója, és az Azure Enhanced Monitoring bővítményt az SAP kell ezeket a módosításokat kell igazítani.
* További adatlemezek csatlakoztatása az Azure virtuális géphez, vagy egy adatlemez eltávolítása. Ebben a forgatókönyvben frissítse a storage szolgáltatással kapcsolatos adatok gyűjtését. Módosítja a konfigurációt, felvételét vagy törlését a végpontok vagy IP-címet rendelni egy virtuális géphez nem befolyásolja a figyelési konfigurációt.
* Az Azure virtuális gép méretét például módosítja bármely más Virtuálisgép-méret A5 méretű.
* Új hálózati adapter hozzáadása az Azure virtuális gép.

Monitoringbeállítások frissítése, frissítse a figyelési infrastruktúra a lépéseket követve [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>SAP szoftver központi telepítése tartozó feladatok részletesen
Ez a szakasz részletesen a konfigurációs és központi telepítési folyamata konkrét feladatok végrehajtására vonatkozó lépések.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell-parancsmagok telepítése
1.  Lépjen a [letölti a Microsoft Azure](https://azure.microsoft.com/downloads/).
1.  Alatt **parancssori eszközök**alatt **PowerShell**válassza **Windows telepítése**.
1.  A letöltött fájl (például WindowsAzurePowershellGet.3f.3f.3fnew.exe), a Microsoft letöltési Manager párbeszédpanelen jelölje ki **futtatása**.
1.  Microsoft Webplatform-telepítővel (a Microsoft Web-PI) futtatásához válassza **Igen**.
1.  Egy oldal, amely úgy tűnik, ez jelenik meg:

  ![Telepítési lapja az Azure PowerShell-parancsmagok][deployment-guide-figure-500]<a name="figure-5"></a>

1.  Válassza ki **telepítése**, majd fogadja el a Microsoft szoftverlicenc-feltételeket.
1.  PowerShell telepítve van. Válassza ki **Befejezés** a telepítővarázsló bezárásához.

Gyakori frissítések keresése a PowerShell-parancsmagokról, amely általában frissülnek havonta. Frissítések keresése a legegyszerűbb módja, hogy hajtsa végre a fenti telepítési lépéseket, akár a telepítés oldal jelenik meg az 5. lépésben. A parancsmagok kiadási dátum- és kiadási száma lap jelenik meg az 5. lépésben szerepelnek. Amennyiben másként nincs feltüntetve a SAP-Jegyzetnek [1928533] vagy az SAP-Jegyzetnek [2015553], javasoljuk, hogy az Azure PowerShell-parancsmagok legújabb verzióját.

Az Azure PowerShell-parancsmagot a számítógépen telepített verziójának ellenőrzéséhez futtassa a PowerShell-parancsot:
```powershell
(Get-Module AzureRm.Compute).Version
```
Az eredmény a következőhöz hasonló:

![Azure PowerShell-parancsmag verzió-ellenőrzés eredménye][deployment-guide-figure-600]
<a name="figure-6"></a>

Ha a számítógépen telepített Azure parancsmag verzió aktuális verzióját, a telepítővarázsló első lapján azt mutatja, hogy hozzáadásával **(telepítve)** , a termék nevét (lásd az alábbi képernyőfelvételen látható). Az PowerShell Azure-parancsmagok naprakészek legyenek. A telepítővarázsló bezárásához kattintson **kilépési**.

![Telepítési lapja az Azure PowerShell-parancsmagok, amely azt jelzi, hogy telepítve vannak-e az Azure PowerShell-parancsmagok legújabb verzióját][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Az Azure CLI telepítése
1.  Lépjen a [letölti a Microsoft Azure](https://azure.microsoft.com/downloads/).
1.  A **parancssori eszközök**alatt **Azure parancssori felület**, jelölje be a **telepítése** hivatkozást az operációs rendszer.
1.  A letöltött fájl (például WindowsAzureXPlatCLI.3f.3f.3fnew.exe), a Microsoft letöltési Manager párbeszédpanelen jelölje ki **futtatása**.
1.  Microsoft Webplatform-telepítővel (a Microsoft Web-PI) futtatásához válassza **Igen**.
1.  Egy oldal, amely úgy tűnik, ez jelenik meg:

  ![Telepítési lapja az Azure PowerShell-parancsmagok][deployment-guide-figure-500]<a name="figure-5"></a>

1.  Válassza ki **telepítése**, majd fogadja el a Microsoft szoftverlicenc-feltételeket.
1.  Az Azure CLI telepítve van. Válassza ki **Befejezés** a telepítővarázsló bezárásához.

Gyakori frissítések keresése az Azure CLI-t, amely rendszerint havonta frissül. Frissítések keresése a legegyszerűbb módja, hogy hajtsa végre a fenti telepítési lépéseket, akár a telepítés oldal jelenik meg az 5. lépésben.

A számítógépen telepített Azure CLI verziójának ellenőrzéséhez a következő parancs futtatásával:
```
azure --version
```

Az eredmény a következőhöz hasonló:

![Azure CLI verziójának ellenőrzése eredménye][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Virtuális gépek csatlakoztatása egy helyszíni tartományban (csak Windows)
Ha Ön egy létesítmények közötti forgatókönyv, ahol a helyszíni Active Directory és DNS az Azure-ban vannak bővítve, SAP virtuális gépek üzembe helyezése várható, hogy a virtuális gépek csatlakozik egy helyszíni tartományban. A részletes lépéseket, virtuális gépek csatlakoztatása egy helyszíni tartományban, és a egy a helyszíni tartomány tagja lehet szükséges további szoftvereket ügyfél változik. Általában egy virtuális gép csatlakoztatása egy helyszíni tartományban, szeretne további szoftverek, például a kártevőirtó szoftver, és a biztonsági mentési vagy figyelési szoftver telepítése.

Ebben a forgatókönyvben, emellett győződjön meg arról, hogy ha az internetes proxybeállítások kényszerített vannak, amikor egy virtuális gép csatlakozik egy tartományhoz, a környezetében, a Windows helyi rendszerfiók (S-1-5 – 18) a Vendég virtuális gépen a proxybeállításokat. A legegyszerűbb lehetőség, hogy a proxy kényszerítheti a tartomány csoportházirend, amely a tartomány rendszerekre vonatkozik.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Letöltése, telepítése és az Azure Virtuálisgép-ügynök engedélyezése
Nincs általánosítva (például képet, amely nem származnak, a Windows rendszer-előkészítés, vagy a sysprep eszközzel) operációs rendszer lemezképből üzembe helyezett virtuális gépek esetén kell manuálisan letöltése, telepítése és az Azure Virtuálisgép-ügynök engedélyezése.

Ha egy virtuális Gépet az Azure Marketplace-ről telepíti, ezt a lépést, nem szükséges. Az Azure Piactérről származó rendszerképek az Azure Virtuálisgép-ügynök már rendelkezik.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Az Azure Virtuálisgép-ügynök letöltése:
  1.  Töltse le a [Azure Virtuálisgép-ügynököt a telepítőcsomag](https://go.microsoft.com/fwlink/?LinkId=394789).
  1.  A Virtuálisgép-ügynök MSI-csomag Store helyileg egy személyi számítógép vagy a kiszolgálón.
1.  Az Azure Virtuálisgép-ügynök telepítése:
  1.  Az üzembe helyezett Azure virtuális Géphez kapcsolódni a távoli asztal protokoll (RDP) használatával.
  1.  Nyissa meg a Windows Intéző ablakot, a virtuális gépen, és válassza ki a célkönyvtárat a Virtuálisgép-ügynök MSI-fájljának.
  1.  Húzza az Azure VM Agent Telepítő MSI-fájl a helyi számítógép kiszolgáló célkönyvtára a Virtuálisgép-ügynököt a virtuális gépen.
  1.  Kattintson duplán a virtuális gépen az MSI-fájlt.
1.  A virtuális gépek, a helyszíni tartományokra csatlakoztatott, győződjön meg arról, végleges internetes proxybeállítások is alkalmazni a Windows helyi rendszer fiók (S-1-5 – 18) a virtuális gép leírtak szerint [konfigurálni a proxyt][deployment-guide-configure-proxy]. A Virtuálisgép-ügynök ebben a környezetben fut, és csatlakozás az Azure-bA képesnek kell lennie.

Az Azure Virtuálisgép-ügynök frissítéséhez szükséges nincs szükség felhasználói beavatkozásra. A Virtuálisgép-ügynök automatikusan frissül, és nem igényel a virtuális gép újraindítása.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
A következő parancsokat használja a Virtuálisgép-ügynök telepítése Linux rendszeren:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) vagy Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Ha az ügynök már telepítve van, az Azure Linux-ügynök frissítéséhez tegye leírt lépéseket követve [egy virtuális gépen az Azure Linux-ügynök frissítése a legújabb verzióra a Githubról][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>A proxy konfigurálása
A lépéseket, a proxy konfigurálása a Windows nem azonosak a úgy kell konfigurálni a proxyt a Linux.

#### <a name="windows"></a>Windows
Proxybeállítások megfelelően a helyi rendszerfiók interneteléréssel kell állítani. Ha a proxybeállításokat nem a csoportházirend-beállításokat, konfigurálhatja a helyi rendszer fiók beállításait.

1. Lépjen a **Start**, adja meg **gpedit.msc**, majd válassza ki **Enter**.
1. Válassza ki **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**  >   **Az Internet Explorer**. Győződjön meg arról, hogy a beállítás **győződjön meg arról, proxy beállításainak a gépenkénti (helyett felhasználónkénti)** le van tiltva vagy nincs konfigurálva.
1. A **Vezérlőpult**, lépjen a **hálózati és megosztási központ** > **Internetbeállítások**.
1. Az a **kapcsolatok** lapon jelölje be a **LAN-beállítások** gombra.
1. Törölje a **beállítások automatikus észlelése** jelölőnégyzetet.
1. Válassza ki a **proxykiszolgáló használata a helyi hálózaton** jelölje be a jelölőnégyzetet, és adja meg a proxykiszolgáló címét és portját.
1. Válassza ki a **speciális** gombra.
1. Az a **kivételek** adja meg az IP-cím **168.63.129.16**. Kattintson az **OK** gombra.

#### <a name="linux"></a>Linux
Konfigurálja a megfelelő proxyt a konfigurációs fájlban, a Microsoft Azure-Vendégügynök, amely a következő helyen található \\stb\\waagent.conf.

Állítsa be a következő paraméterekkel:

1.  **HTTP-proxy állomás**. Például állítsa be **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
1.  **HTTP-proxy portja**. Például állítsa be **80-as**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
1.  Indítsa újra az ügynököt.

  ```
  sudo service waagent restart
  ```

A proxybeállítások \\stb\\waagent.conf is alkalmazni kell a szükséges Virtuálisgép-bővítmények. Ha szeretné használni az Azure-tárházak, győződjön meg arról, hogy az ezen tárházakba nem történik, a helyi intraneten keresztül. Létrehozta a felhasználó által megadott útvonalak a kényszerített bújtatás engedélyezéséhez győződjön meg arról, hogy adjon hozzá egy útvonalat, amely átirányítja a forgalmat a közvetlenül az internethez, és nem a site-to-site VPN-kapcsolaton keresztül a tárházban.

* **SLES**

  Is kell beállítani az útvonalakat az IP-címek szerepelnek \\stb\\regionserverclnt.cfg. A következő ábrán egy példa látható:

  ![Alagúthasználat kényszerítése][deployment-guide-figure-50]


* **RHEL**

  Is kell beállítani az útvonalakat az IP-cím szerepel a gazdagépek \\stb\\yum.repos.d\\rhui terheléselosztók. Egy vonatkozó példáért lásd az előző ábrán.

* **Oracle Linux**

  Nincsenek nem tárházak Oracle Linux rendszeren az Azure-ban. Konfigurálja a saját tárház Oracle Linux rendszeren, vagy használjon két nyilvános adattár kell.

Felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [felhasználó által megadott útvonalak és IP-továbbítás][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Az SAP az Azure fejlett figyelési bővítmény konfigurálása
Amikor előkészítése után a virtuális gép leírtak szerint [virtuális gépek az Azure-beli SAP telepítési forgatókönyvek][deployment-guide-3], az Azure Virtuálisgép-ügynök telepítve van a virtuális gépen. A következő lépés, hogy üzembe helyezése az Azure Enhanced Monitoring bővítményt az SAP, amely a globális Azure-adatközpontok Azure-bővítmény tárházban érhető el. További információkért lásd: [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára][planning-guide-9.1].

PowerShell vagy az Azure CLI segítségével telepítheti és konfigurálhatja az Azure Enhanced Monitoring bővítményt az SAP. A bővítmény telepítése a Windows vagy Linux rendszerű virtuális gépek által egy Windows-gépen: [Azure PowerShell-lel][deployment-guide-4.5.1]. A bővítmény telepítése egy Linux rendszerű virtuális gépen a Linux rendszerű asztali: [Azure CLI-vel][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>A Linux és Windows virtuális gépek Azure PowerShell-lel
Az Azure Enhanced Monitoring bővítményt az SAP telepítése a PowerShell használatával:

1. Győződjön meg arról, hogy telepítette-e a legújabb Azure PowerShell-parancsmag. További információkért lásd: [üzembe helyezése az Azure PowerShell-parancsmagok][deployment-guide-4.1].  
1. Futtassa az alábbi PowerShell-parancsmagot.
    Rendelkezésre álló környezetek listáját futtatása `commandlet Get-AzureRmEnvironment`. Ha szeretné használni az Azure globális, a környezet a **AzureCloud**. Válassza ki az Azure Kínában, **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Miután adja meg a fiókja adatait, és azonosítsa az Azure virtuális gépen, a parancsfájl telepíti a szükséges bővítményeit, és lehetővé teszi, hogy a szükséges funkciókat. Ez több percet is igénybe vehet.
További információ `Set-AzureRmVMAEMExtension`, lásd: [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![Az SAP-specifikus sikeres végrehajtása az Azure a Set-AzureRmVMAEMExtension parancsmag][deployment-guide-figure-900]

A `Set-AzureRmVMAEMExtension` konfigurációs does SAP-figyelési állomás konfigurálása a lépéseket.

A parancsfájl kimenete a következő információkat tartalmazza:

* A megerősítő, hogy az operációsrendszer-lemez és az összes további adatlemezek megfigyelését lett konfigurálva.
* A következő két üzenetek erősítse meg, hogy adott tárfiókhoz a Storage Metrics konfigurációját.
* Kimeneti egy üzletági biztosítja a figyelési konfiguráció a tényleges frissítés állapota.
* Kimenet egy másik üzletági megerősíti, hogy a konfiguráció telepíteni vagy frissíteni.
* Tájékoztató a kimenet utolsó sora. A beállítások teszteléséhez a figyelési konfigurációtól jeleníti meg.
* Ellenőrizze, hogy Azure Enhanced Monitoring az összes lépés sikeres végrehajtása megtörtént, és, hogy az Azure-infrastruktúrát biztosít a szükséges adatok, folytassa az Azure Enhanced Monitoring bővítményt az SAP, az készültség-ellenőrzés leírtakszerint[Készültség-ellenőrzés az Azure SAP Enhanced Monitoring][deployment-guide-5.1].
* Várjon 15 – 30 percet, a vonatkozó adatok összegyűjtése az Azure diagnosztikai.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux rendszerű virtuális gépekhez az Azure CLI
Az Azure Enhanced Monitoring bővítményt az SAP telepítése az Azure CLI-vel:

   1. Az Azure klasszikus parancssori felület telepítése leírtak szerint [a klasszikus Azure CLI telepítése][azure-cli].
   1. Jelentkezzen be az Azure-fiókjával:

      ```
      azure login
      ```

   1. Váltson Azure Resource Manager módra:

      ```
      azure config mode arm
      ```

   1. Engedélyezze az Azure Enhanced Monitoring:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Telepítse az Azure CLI 2.0 használatával

   1. Azure CLI 2.0 telepítése a leírtak szerint [Azure CLI 2.0 telepítése][azure-cli-2].
   1. Jelentkezzen be az Azure-fiókjával:

      ```
      az login
      ```

   1. Az Azure CLI az AEM-bővítményének telepítése
  
      ```
      az extension add --name aem
      ```
  
   1. A bővítmény telepítése
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Ellenőrizze, hogy aktív az Azure Linux rendszerű virtuális gépen az Azure Enhanced Monitoring bővítményt. Ellenőrizze, hogy a fájl \\var\\lib\\AzureEnhancedMonitor\\PerfCounters létezik. Ha létezik, egy parancssorban futtassa ezt a parancsot, a továbbfejlesztett Azure figyelő által gyűjtött információk megjelenítéséhez:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   A kimenet a következőhöz hasonló:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Ellenőrzési és hibaelhárítási lehetőségek teljes körű figyelés
Után üzembe helyezve az Azure virtuális gép, és állítsa be a megfelelő Azure figyelési infrastruktúra, ellenőrizze, hogy az Azure Enhanced Monitoring bővítményt minden összetevőjének várt módon működnek-e.

A készültség-ellenőrzés futtatása a az Azure Enhanced Monitoring bővítményt az SAP leírtak szerint [készültség-ellenőrzés a az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-5.1]. Ha az összes készenléti ellenőrzésének az eredménye pozitív, és minden kapcsolódó teljesítményszámlálókat OK jelennek meg, az Azure monitoring be lett állítva sikeresen. Folytathatja az SAP-gazdagép-ügynök telepítése a SAP-megjegyzések leírtak szerint [SAP erőforrások][deployment-guide-2.2]. Ha a készültség-ellenőrzés azt jelzi, hogy a számlálók hiányoznak, futtassa az állapot-ellenőrzés az Azure figyelési infrastruktúra leírtak szerint [Azure figyelési infrastruktúra konfigurálása-állapotellenőrzés] [ deployment-guide-5.2]. A további hibaelhárítási lehetőségekről [hibaelhárítása Azure monitoring for SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Az Azure Enhanced Monitoring bővítményt az SAP készültség ellenőrzése
Ez az ellenőrzés gondoskodik arról, hogy a metrikák jelennek meg az SAP alkalmazáson belül, az alapul szolgáló Azure figyelési infrastruktúra által biztosított.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>A Windows virtuális gép készültség-ellenőrzés futtatása

1.  Jelentkezzen be az Azure virtuális gépen (egy rendszergazdai fiók használata nem szükséges).
1.  Nyisson meg egy parancssori ablakot.
1.  A parancssorban módosítsa a könyvtárat, az Azure Enhanced Monitoring bővítményt az SAP telepítési mappájában: C:\\csomagok\\beépülő modulok\\ Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verzió >\\drop

  A *verzió* elérési útját a figyelés típusú kiterjesztés változhat. Ha több verziója a monitorozási bővítményt az telepítési mappájában, mappák, ellenőrizze a AzureEnhancedMonitoring Windows-szolgáltatás konfigurációját, és ezután váltson arra a mappára jelzett *végrehajtható fájl elérési útja* .

  ![Az Azure Enhanced Monitoring bővítményt az SAP futó szolgáltatás tulajdonságai][deployment-guide-figure-1000]

1.  A parancssorban futtassa **azperflib.exe** paraméterek nélkül.

  > [!NOTE]
  > Azperflib.exe hurkot futtat, és frissíti a gyűjtött teljesítményszámlálók minden 60 másodpercben. A hurok befejezéséhez zárja be a parancssori ablakot.
  >
  >

Ha nincs telepítve az Azure Enhanced Monitoring bővítményt, vagy a AzureEnhancedMonitoring szolgáltatás nem fut, a bővítmény konfigurációja nem megfelelő. A bővítmény telepítésével kapcsolatos részletes információkért lásd: [az SAP az Azure figyelési infrastruktúra hibaelhárítási][deployment-guide-5.3].

> [!NOTE]
> A Azperflib.exe egy összetevő, amely saját célokra nem használhatók. A program egy összetevő, amelyek Azure monitorozási adatok, az SAP gazdagép-ügynök a virtuális géphez kapcsolódó.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Ellenőrizze a azperflib.exe kimenete
Azperflib.exe megjelenítheti az összes Azure teljesítményszámlálók feltöltve az SAP. Gyűjtött teljesítményszámlálók listája alján összegzéseket és az egészségügyi mutató megjelenítése az Azure figyelési állapotát.

![Állapot-ellenőrzés végrehajtásával azperflib.exe, ami azt jelzi, hogy nincsenek-e problémák létezik kimenete][deployment-guide-figure-1100]
<a name="figure-11"></a>

Ellenőrizze az eredményt adott vissza a **számlálók teljes** kimenete, amely üres, és a jelentett **állapot**, az előző ábrán látható módon.

A következő értelmezni az eredményül kapott értékeket:

| Azperflib.exe eredmény érték | Az Azure figyelési állapot |
| --- | --- |
| **API-hívások – nem érhető el** | Amelyek nem érhetők számlálói lehet, vagy nem alkalmazható a virtuális gép konfigurációját, vagy hibák. Lásd: **állapot**. |
| **Számlálók összesen – üres** |A következő két az Azure storage-számlálók üres is lehet: <ul><li>Storage által végrehajtott olvasási késés Op kiszolgáló MS</li><li>Storage által végrehajtott olvasási késés Op E2E MS</li></ul>Minden egyéb számlálók értékeit kell rendelkeznie. |
| **Állapotadatok** |Csak OK if vissza állapotú **OK**. |
| **Diagnosztika** |Állapot részletes adatai. |

Ha a **állapot** hodnota není **OK**, kövesse a [Azure figyelési infrastruktúra konfigurálása-állapotellenőrzés] [ deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>A Linux rendszerű virtuális gép készültség-ellenőrzés futtatása

1.  Csatlakozás az Azure virtuális gép SSH használatával.

1.  Ellenőrizze az Azure Enhanced Monitoring bővítményt kimenetét.

  a.  Futtassa a `more /var/lib/AzureEnhancedMonitor/PerfCounters` parancsot.

   **Várt eredmény**: teljesítményszámlálók listájának visszaadása. A fájl nem lehet üres.

 b. Futtassa a `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` parancsot.

   **Várt eredmény**: értéket ad vissza egy sorral, ahol a hiba: **nincs**, például **3; konfigurációs; Hiba történt; 0, 0; Nincs; 0; 1456416792; teszt-servercs;**

  c. Futtassa a `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` parancsot.

    **Várt eredmény**: ad vissza, üres vagy nem létezik.

Ha az előző ellenőrzése nem volt sikeres, a további ellenőrzések futtatásához:

1.  Győződjön meg arról, hogy a waagent telepítve és engedélyezve van.

  a.  Futtassa a `sudo ls -al /var/lib/waagent/` parancsot.

      **Várt eredmény**: a waagent könyvtár tartalmát listázza.

  b.  Futtassa a `ps -ax | grep waagent` parancsot.

   **Várt eredmény**: hasonló egy bejegyzés jeleníti meg: `python /usr/sbin/waagent -daemon`

1.   Győződjön meg arról, hogy az Azure Enhanced Monitoring bővítményt telepítve és fut-e.

  a.  Futtassa a `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` parancsot.

    **Várt eredmény**: az Azure Enhanced Monitoring bővítményt könyvtár tartalmát listázza.

  b. Futtassa a `ps -ax | grep AzureEnhanced` parancsot.

     **Várt eredmény**: hasonló egy bejegyzés jeleníti meg: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. SAP-gazdagép-ügynök telepítése a SAP-Jegyzetnek leírtak szerint [1031096], és ellenőrizze a kimenetét `saposcol`.

  a.  Futtassa a `/usr/sap/hostctrl/exe/saposcol -d` parancsot.

  b.  Futtassa a `dump ccm` parancsot.

  c.  Ellenőrizze-e a **Virtualization_Configuration\Enhanced figyelési hozzáférés** metrika **igaz**.

Ha már van telepítve az SAP NetWeaver ABAP-alkalmazáskiszolgáló, nyissa meg a tranzakciós ST06, és ellenőrizze, hogy engedélyezve van-e a fokozott ellenőrzés.

Ha minden ellenőrzés nem sikerül, és ismételt üzembe helyezése a bővítmény kapcsolatos részletes információkért lásd: [az SAP az Azure figyelési infrastruktúra hibaelhárítási][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Az Azure figyelési infrastruktúra konfigurálásának állapotának ellenőrzése
Ha egyes figyelési adatokat nem kézbesíti a rendszer a teszt leírt aszinkronitást megfelelően [készültség-ellenőrzés az Azure SAP Enhanced Monitoring][deployment-guide-5.1]futtassa a `Test-AzureRmVMAEMExtension` parancsmaggal ellenőrizheti-e a Az Azure SAP-infrastruktúra és a figyelés típusú kiterjesztés figyelése megfelelően vannak konfigurálva.

1.  Győződjön meg arról, hogy telepítette-e a legújabb Azure PowerShell-parancsmag leírtak szerint [üzembe helyezése az Azure PowerShell-parancsmagok][deployment-guide-4.1].
1.  Futtassa az alábbi PowerShell-parancsmagot. Rendelkezésre álló környezetek listáját, futtassa a parancsmagot `Get-AzureRmEnvironment`. Globális Azure használatához válassza ki a **AzureCloud** környezetben. Válassza ki az Azure Kínában, **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Connect-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

1.  Adja meg a fiókja adatait, és azonosítja az Azure virtuális gépen.

  ![Az SAP-specifikus bemeneti lapján Azure parancsmag: Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. A parancsfájl teszteli a konfigurációt a virtuális gép választja.

  ![Az SAP az Azure figyelési infrastruktúra sikeres tesztet kimenete][deployment-guide-figure-1300]

Győződjön meg arról, hogy minden állapot-ellenőrzés eredménye **OK**. Ha nem jelennek meg néhány ellenőrzést **OK**, a frissítés parancsmag futtatása leírtak szerint [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-4.5]. Várjon 15 percig, majd ismételje meg a leírt ellenőrzések [készültség-ellenőrzés az Azure SAP Enhanced Monitoring] [ deployment-guide-5.1] és [Azure figyelési infrastruktúra konfiguráció-állapotellenőrzés] [deployment-guide-5.2]. Ha az ellenőrzések még néhány vagy minden számláló problémára utalhat, [az SAP az Azure figyelési infrastruktúra hibaelhárítási][deployment-guide-5.3].

> [!Note]
> Bizonyos esetekben, ahol a standard szintű Azure Managed Disks szolgáltatást figyelmeztetések tapasztalhatnak. Figyelmeztetések helyett a tesztek visszaadó "OK" jelenik meg. Ez a normál és a tervezett esetén a lemez típusát. Lásd még: lásd: [az SAP az Azure figyelési infrastruktúra hibaelhárítása][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Az SAP az Azure figyelési infrastruktúra hibaelhárítása

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Az Azure teljesítményszámlálók nem minden jelennek meg
A AzureEnhancedMonitoring Windows szolgáltatás adatokat gyűjt a teljesítmény-mérőszámok az Azure-ban. Ha a szolgáltatás nem lett megfelelően telepítve, vagy ha a virtuális gép nem fut, nincs teljesítmény-mérőszámok lehessen gyűjteni.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>A telepítési könyvtár, az Azure Enhanced Monitoring bővítményt nem üres

###### <a name="issue"></a>Probléma
A telepítési könyvtár: C:\\csomagok\\beépülő modulok\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verzió >\\dobja el az üres.

###### <a name="solution"></a>Megoldás
A bővítmény nincs telepítve. Határozza meg, hogy ez-e a proxyval kapcsolatos probléma (a korábban ismertetett). Indítsa újra a gépet, vagy futtassa újra a szüksége lehet a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájlt.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Az Azure Enhanced Monitoring szolgáltatás nem létezik.

###### <a name="issue"></a>Probléma
A AzureEnhancedMonitoring Windows szolgáltatás nem létezik.

Azperflib.exe kimenet hibát jelez:

![Azperflib.exe végrehajtását jelzi, hogy az SAP az Azure Enhanced Monitoring bővítményt, a szolgáltatás nem fut][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Megoldás
Ha a szolgáltatás nem létezik, az Azure Enhanced Monitoring bővítményt az SAP nem lett megfelelően telepítve. Telepítse újra a bővítmény az üzembe helyezési forgatókönyvnek leírt lépéseket követve [központi telepítési forgatókönyvei az SAP az Azure-beli virtuális gépek][deployment-guide-3].

A bővítmény telepítése egy óra múlva, után ellenőrizze újra-e az Azure teljesítményszámlálók találhatók-e az Azure-beli virtuális gépen.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Az Azure Enhanced Monitoring szolgáltatás létezik, de nem indul el

###### <a name="issue"></a>Probléma
A AzureEnhancedMonitoring Windows szolgáltatás létezik, és engedélyezve van, de nem indul el. További információkért tekintse meg az alkalmazások eseménynaplójában.

###### <a name="solution"></a>Megoldás
A konfiguráció helytelen. Indítsa újra a monitorozási bővítményt a virtuális gép leírtak szerint [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Hiányoznak egyes Azure-teljesítményszámlálók
A AzureEnhancedMonitoring Windows szolgáltatás adatokat gyűjt a teljesítmény-mérőszámok az Azure-ban. A szolgáltatás több forrásból származó adatok beolvasása. Helyileg összegyűjtött konfigurációs adatokat, és néhány teljesítmény-mérőszámok az Azure Diagnostics olvasható. A tároló előfizetési szinten a naplózási tároló számlálói használtak.

Ha az SAP-Jegyzetnek hibaelhárítási [1999351] nem oldja meg a problémát, futtassa újra a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájl. Várjon egy órát, mert storage analytics vagy a diagnosztika számlálók előfordulhat, hogy nem hozható létre, után azonnal engedélyezve vannak, előfordulhat, hogy rendelkezik. Ha a probléma tartósan fennáll, az összetevő BC-OP-NT-AZR Windows vagy BC-OP-LNX-AZR Linuxos virtuális gépek SAP customer támogatási üzenet megnyitása.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Az Azure teljesítményszámlálók nem minden jelennek meg
A démon által gyűjtött teljesítmény-mérőszámok az Azure-ban. Ha a démon nem fut, nincs teljesítmény-mérőszámok lehessen gyűjteni.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>A telepítési könyvtár, az Azure Enhanced Monitoring bővítményt nem üres

###### <a name="issue"></a>Probléma
A könyvtár \\var\\lib\\waagent\\ nem rendelkezik az Azure Enhanced Monitoring bővítményt alkönyvtárat.

###### <a name="solution"></a>Megoldás
A bővítmény nincs telepítve. Határozza meg, hogy ez-e a proxyval kapcsolatos probléma (a korábban ismertetett). Indítsa újra a gépet, és futtassa újra a szüksége lehet a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájlt.

##### <a name="the-execution-of-set-azurermvmaemextension-and-test-azurermvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A Set-AzureRmVMAEMExtension és Test-AzureRmVMAEMExtension végrehajtásának arról, hogy az standard szintű felügyelt lemezek használata nem támogatott figyelmeztető üzenetek megjelenítése

###### <a name="issue"></a>Probléma
Ha az alábbiakhoz hasonló végrehajtó Set-AzureRmVMAEMExtension és Test-AzureRmVMAEMExtension üzenetek jelennek meg:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Azperfli.exe végrehajtása a fentebb leírt módon kérheti le, amely jelzi a nem kifogástalan állapot eredményt. 

###### <a name="solution"></a>Megoldás
Az üzenetek a tény, hogy standard szintű Managed Disks nem kézbesítéséhez a monitorozási bővítményt használják, a standard szintű Azure-Tárfiókok statistics ellenőrizheti az API-k által okozott. Ez a nem aggodalmat. Bemutatkozik a Standard Disk Storage-fiókok figyelési okát gyakran előforduló i/o volt szabályozás. A felügyelt lemezek ilyen szabályozás korlátozza a tárfiókban lévő lemezek számát a rendszer működésében. Nem rendelkezik az adott típusú figyelési adatok ezért nem kritikus fontosságú.


#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Hiányoznak egyes Azure-teljesítményszámlálók
Adatok beolvasása számos forrásból származó démon által gyűjtött teljesítmény-mérőszámok az Azure-ban. Helyileg összegyűjtött konfigurációs adatokat, és néhány teljesítmény-mérőszámok az Azure Diagnostics olvasható. Storage-számlálók a naplókat tároló előfizetését származnak.

Ismert problémák teljes és naprakész listája, tekintse meg az SAP-Jegyzetnek [1999351], amely további információkat talál a továbbfejlesztett Azure Monitoring for SAP rendelkezik.

Ha az SAP-Jegyzetnek hibaelhárítási [1999351] nem oldja meg a problémát, futtassa újra a `Set-AzureRmVMAEMExtension` konfigurációs parancsfájl leírtak szerint [konfigurálása az Azure Enhanced Monitoring bővítményt az SAP] [deployment-guide-4.5]. Előfordulhat, hogy egy óránál várakoznia, mivel a storage analytics vagy a diagnosztika számlálók előfordulhat, hogy nem jönnek létre után azonnal engedélyezve vannak. Ha a probléma tartósan fennáll, az összetevő BC-OP-NT-AZR Windows vagy BC-OP-LNX-AZR Linuxos virtuális gépek SAP customer támogatási üzenet megnyitása.
