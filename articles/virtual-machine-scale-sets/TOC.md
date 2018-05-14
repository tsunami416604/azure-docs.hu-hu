# [A Virtual Machines Scale Sets dokumentációja](index.yml)

# Áttekintés
## [Mik a virtuálisgép-méretezési csoportok?](overview.md)

# Gyors útmutatók
## [Létrehozás az Azure Portalon](quick-create-portal.md)
## [Létrehozás az Azure CLI 2.0 használatával](quick-create-cli.md)
## [Létrehozás az Azure PowerShell-lel](quick-create-powershell.md)
## Létrehozás sablon alapján
### [Linux méretezési csoport](quick-create-template-linux.md)
### [Windows méretezési csoport](quick-create-template-windows.md)

# Oktatóanyagok
## 1 – Méretezési csoport létrehozása/kezelése
### [Azure CLI 2.0](tutorial-create-and-manage-cli.md)
### [Azure PowerShell](tutorial-create-and-manage-powershell.md)
## 2 – Adatlemezek használata
### [Azure CLI 2.0](tutorial-use-disks-cli.md)
### [Azure PowerShell](tutorial-use-disks-powershell.md)
## 3 – Egyéni virtuálisgép-rendszerkép létrehozása
### [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
### [Azure PowerShell](tutorial-use-custom-image-powershell.md)
## 4 – Alkalmazások üzembe helyezése méretezési csoportba
### [Azure CLI 2.0](tutorial-install-apps-cli.md)
### [Azure PowerShell](tutorial-install-apps-powershell.md)
### [Sablon](tutorial-install-apps-template.md)
## 5 – Méretezési csoport automatikus méretezése
### [Azure CLI 2.0](tutorial-autoscale-cli.md)
### [Azure PowerShell](tutorial-autoscale-powershell.md)
### [Sablon](tutorial-autoscale-template.md)
## Alkalmazások üzembe helyezése Azure-beli virtuálisgép-méretezési csoportokban egyéni Packer-rendszerképpel
### [Azure CLI 2.0](https://docs.microsoft.com/learn/deploy-custom-vmss-app/index)

# Példák
## [Azure CLI 2.0](cli-samples.md)
## [PowerShell](powershell-samples.md)

# Útmutató
## Tervezés és kialakítás
### [Kialakítási szempontok](virtual-machine-scale-sets-design-overview.md)
### [A példányazonosítók ismertetése](virtual-machine-scale-sets-instance-ids.md)

## Sablon létrehozása
### [Információk a méretezési csoportok sablonjairól](virtual-machine-scale-sets-mvss-start.md)
### [Meglévő virtuális hálózat használata](virtual-machine-scale-sets-mvss-existing-vnet.md)
### [Egyéni rendszerkép használata](virtual-machine-scale-sets-mvss-custom-image.md)
### [Vendégalapú automatikus méretezés használata Linux rendszerű méretezésicsoport-sablonnal](virtual-machine-scale-sets-mvss-guest-based-autoscale-linux.md)

## Üzembe helyezés
### [Létrehozás a Visual Studióval](virtual-machine-scale-sets-vs-create.md)
### [Rendelkezésre állási zónák használata](virtual-machine-scale-sets-use-availability-zones.md)
### [Méretezési csoport automatikus méretezése](virtual-machine-scale-sets-autoscale-overview.md)
#### [Az Azure Portal használata](virtual-machine-scale-sets-autoscale-portal.md)
#### [Automatikus skálázás haladó szinten](../monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [Méretezésicsoport-alkalmazások](virtual-machine-scale-sets-deploy-app.md)
### [Adatlemezek használata méretezési csoportokkal](virtual-machine-scale-sets-attached-disks.md)
### Méretezési csoportokban lévő lemezek titkosítása
#### [A PowerShell használata](virtual-machine-scale-sets-encrypt-disks-ps.md)
#### [Az Azure CLI 2.0 használata](virtual-machine-scale-sets-encrypt-disks-cli.md)
### [Nagyméretű méretezési csoportokkal végzett munka](virtual-machine-scale-sets-placement-groups.md)
### [Méretezési csoport sablonjának konvertálása felügyelt lemez használatához](virtual-machine-scale-sets-convert-template-to-md.md)
### [Alacsony prioritás használata](virtual-machine-scale-sets-use-low-priority.md)

## Kezelés
### Általános kezelési feladatok
#### [Az Azure CLI 2.0 használata](virtual-machine-scale-sets-manage-cli.md)
#### [Azure PowerShell használata](virtual-machine-scale-sets-manage-powershell.md)
### [Függőleges méretezés méretezési csoportban](virtual-machine-scale-sets-vertical-scale-reprovision.md)
### [Operációs rendszer automatikus frissítései](virtual-machine-scale-sets-automatic-upgrade.md)
### [Méretezési csoportok módosítása](virtual-machine-scale-sets-upgrade-scale-set.md)
### [DSC és méretezési csoportok használata](virtual-machine-scale-sets-dsc.md)
### [Méretezési csoportok hálózatkezelése](virtual-machine-scale-sets-networking.md)
### [Sablon konvertálása felügyelt lemezhez](virtual-machine-scale-sets-convert-template-to-md.md)

## Hibaelhárítás
### [Automatikus méretezés](virtual-machine-scale-sets-troubleshoot.md)

## GYIK
### [Méretezési csoport – gyakran ismételt kérdések](virtual-machine-scale-sets-faq.md)

# Referencia
## [Azure PowerShell](/powershell/azure/overview)
## [Azure CLI](../virtual-machines/azure-cli-arm-commands.md)
## [REST](/rest/api/virtualmachinescalesets/)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=compute)
## Díjszabás 
### [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)
### [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-vm-scale-set)
