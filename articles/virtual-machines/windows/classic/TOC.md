# Áttekintés

## [Információ a virtuális gépekről](../../virtual-machines-windows-about.md)

## [Lemezek és virtuális merevlemezek](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## [Virtuális hálózatok](../../../virtual-network/virtual-networks-overview.md)

## [Gyakori kérdések](faq.md)

## [Virtuális gépek, webhelyek és felhőszolgáltatások összehasonlítása](../../../app-service-web/choose-web-site-cloud-service-vm.md)

## [Tárolók](../../virtual-machines-windows-containers.md)


# Bevezetés

## [Virtuális gép létrehozása a portálról](tutorial.md)

## [Bejelentkezés virtuális gépre](connect-logon.md)

## [Az Azure PowerShell telepítése](/powershell/azure/overview)

## [Az Azure parancssori felület telepítése](../../../cli-install-nodejs.md)


# Útmutató


## A Storage használata

### [Adatlemez csatolása](attach-disk.md)

### [Adatlemez leválasztása](detach-disk.md)

### [A „D:” meghajtó használata adatlemezként](../../virtual-machines-windows-change-drive-letter.md)


## Network (Hálózat)

### [Végpontok beállítása](setup-endpoints.md)

### [Virtuális gépek csatlakoztatása virtuális hálózathoz vagy felhőszolgáltatáshoz](connect-vms.md)

### [Klasszikus virtuális hálózatok csatlakoztatása Resource Manager-alapú virtuális hálózatokhoz](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)

### [Terheléselosztó létrehozása](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)

### [NSG-k kezelése az Azure PowerShell-lel](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)


## Üzembe helyezés

### [Egyéni virtuális gép létrehozása](createportal.md)

### [Virtuális gép létrehozása és konfigurálása az Azure PowerShell-lel](create-powershell.md)

### [Windows rendszerű virtuális gép rögzítése](capture-image.md)

### [Virtuális merevlemez létrehozása és feltöltése a PowerShell-lel](createupload-vhd.md)

### [Az Azure-beli virtuális gépek üzembe helyezésének automatizálása a Chef szolgáltatással](../../virtual-machines-windows-chef-automation.md)

### [Virtuális gépek létrehozása és kezelése a Visual Studióban](manage-visual-studio.md)

### [Webalkalmazáshoz tartozó virtuális gép létrehozása a Visual Studióban](web-app-visual-studio.md)

### [Számításigényes feladat futtatása Java környezetben](java-run-compute-intensive-task.md)

### [Django Hello World webalkalmazás](python-django-web-app.md)


## Konfigurálás

### [Jelszó vagy távoli asztali szolgáltatás visszaállítása](../../virtual-machines-windows-reset-rdp.md)

### [A Symantec Endpoint Protection telepítése és konfigurálása](install-symantec.md)

### [A Trend Micro Deep Security telepítése és konfigurálása szolgáltatásként](install-trend.md)

### [Rendelkezésre állási csoport konfigurálása](configure-availability.md)

### [A klasszikus üzemi modellben létrehozott Windows-alapú virtuális gépek átméretezése](resize-vm.md)


## Kezelés

### [Áttelepítés klasszikusról Resource Manager-alapú környezetbe](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

### [Virtuális gépek kezelése az Azure PowerShell-lel](manage-psh.md)

### [További információk a virtuálisgép-ügynökről és -bővítményekről](agents-and-extensions.md)

### [Virtuálisgép-bővítmények kezelése](manage-extensions.md)

### [Egyéni parancsfájl-bővítmény virtuális gépek számára](extensions-customscript.md)

### [Egyéni adatok betöltése Azure virtuális gépbe](inject-custom-data.md)


## Felkészülés

### [További információk a rendszerképekről](about-images.md)

### [A virtuális gépek mérete](../../virtual-machines-windows-sizes.md)

### [Az Azure-beli virtuális gépek tervezett karbantartása](../../virtual-machines-windows-planned-maintenance.md)

### [Az Azure infrastruktúra-szolgáltatások megvalósítási irányelvei](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)


## Számítási feladatok kezelése

### [Nagy teljesítményű feldolgozás (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)

#### [Erőforrások automatikus méretezése](hpcpack-cluster-node-autogrowshrink.md)

#### [Számítási csomópontok kezelése](hpcpack-cluster-node-manage.md)

#### [Fürt létrehozása](hpcpack-cluster-powershell-script.md)

#### [Fürt beállítása MPI-alkalmazások futtatására](hpcpack-rdma-cluster.md)

#### [Excel és SOA típusú számítási feladatok futtatása](../../virtual-machines-windows-excel-cluster-hpcpack.md)

#### [Piactéri rendszerképet használó átjárócsomópont létrehozása](../../virtual-machines-windows-hpcpack-cluster-headnode.md)

#### [Helyszíni feladatok küldése az Azure-ba](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

### [MongoDB](install-mongodb.md)

### [MySQL](mysql-2008r2.md)

### [Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)

####[Az Azure-hoz készült Oracle Data Guard konfigurálása](configure-oracle-data-guard.md)

####[Az Azure-hoz készült Oracle GoldenGate konfigurálása](configure-oracle-goldengate.md)

####[A rendszerképek megfontolandó szempontjai](oracle-considerations.md)

####[Oracle virtuálisgép-rendszerképek listája](oracle-images.md)

### [SAP](sap-get-started.md)

### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

### [Tomcat](java-run-tomcat-app-server.md)


## Hibaelhárítás

### [Távoli asztali kapcsolatok](../../virtual-machines-windows-troubleshoot-rdp-connection.md)

####[Távoli asztali kapcsolatok problémáinak részletes hibaelhárítási lépései](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)

### [Hozzáférés alkalmazáshoz](../../virtual-machines-windows-troubleshoot-app-connection.md)

### [Klasszikus üzembe helyezési hibák új virtuális gép létrehozásakor](troubleshoot-deployment-new-vm.md)

### [Klasszikus üzembe helyezési hibák meglévő virtuális gép újraindításakor vagy átméretezésekor](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)

## [RDP-jelszó visszaállítása](reset-rdp.md)


# Referencia

## [PowerShell](/powershell/azure/overview)

## [Azure CLI](/cli/azure/vm)

## [Java](/java/api)

## [.NET](/dotnet/api/microsoft.azure.management.compute)

## [Resource Manager-sablonok készítése](../../../resource-group-authoring-templates.md)

## [Közösségi sablonok](https://azure.microsoft.com/documentation/templates)

## [Számítási REST](/rest/api/compute)

## [Hálózati REST](/rest/api)

## [Tárolási REST](/rest/api/storageservices)


# Erőforrások

## [Azure-ütemterv](https://azure.microsoft.com/roadmap/)

## [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)

## [Régiónkénti rendelkezésre állás](https://azure.microsoft.com/regions/services/)

## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)

## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)

