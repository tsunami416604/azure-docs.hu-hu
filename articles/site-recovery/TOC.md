# Áttekintés
## [Mi a Site Recovery?](site-recovery-overview.md)
## Hogy működik a Site Recovery?
### [Azure-ról Azure-ra architektúra](site-recovery-azure-to-azure-architecture.md)
### [VMware-ről Azure-ra architektúra](site-recovery-architecture-vmware-to-azure.md)
### [Hyper-V-ről Azure-ra architektúra](site-recovery-architecture-hyper-v-to-azure.md)
### [Replikálás másodlagos helyre architektúra](site-recovery-architecture-to-secondary-site.md)
## [Milyen számítási feladatokat tud megvédeni?](site-recovery-workload.md)
## Site Recovery támogatási mátrix
### [Azure-ról Azure-ra támogatás](site-recovery-support-matrix-azure-to-azure.md)
### [Helyszíniről Azure-ra támogatás](site-recovery-support-matrix-to-azure.md)
### [Helyszíniről másodlagos helyre támogatás](site-recovery-support-matrix-to-sec-site.md)
## [Gyakori kérdések](site-recovery-faq.md)
## [Bevezetés megtekintése](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Első lépések
## [Azure-beli virtuális gépek replikálása (előzetes verzió)](site-recovery-azure-to-azure.md)
## [Fizikai kiszolgálók replikálása az Azure-ba](site-recovery-physical-servers-to-azure.md)
## [Hyper-V-alapú virtuális gépek replikálása az Azure-ba (VMM-mel)](site-recovery-vmm-to-azure.md)
## [Hyper-V virtuális gépek replikálása az Azure-ba](site-recovery-hyper-v-site-to-azure.md)
## [Hyper-V-alapú virtuális gépek replikálása másodlagos helyre (VMM-mel)](site-recovery-vmm-to-vmm.md)
## [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](site-recovery-vmware-to-vmware.md)
## [VMWare-alapú virtuális gépek replikálása az Azure-ba több-bérlős központi telepítésben (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Útmutató
## Felkészülés
### [Az Azure-replikáció előfeltételei](site-recovery-azure-to-azure-prereq.md)
### Hálózatkezelés megtervezése
#### [Hálózatkezelés megtervezése Azure-ról Azure-ra történő replikációhoz (előzetes verzió)](site-recovery-azure-to-azure-networking-guidance.md)
#### [Hálózatkezelés tervezése a helyszíni gépek replikációjához](site-recovery-network-design.md)
#### [Hálózatleképezés megtervezése Azure-beli virtuális gépek replikációjához](site-recovery-network-mapping-azure-to-azure.md)
#### [Hálózatleképezés megtervezése Hyper-V virtuális gépek replikációjához](site-recovery-network-mapping.md)
### Kapacitás és skálázhatóság megtervezése
#### [Kapacitás megtervezése a VMware Azure-ba történő replikálásához](site-recovery-plan-capacity-vmware.md)
#### [Deployment Planner a VMware Azure-ba történő replikálásához](site-recovery-deployment-planner.md)
#### [Capacity Planner Hyper-V-replikáláshoz](site-recovery-capacity-planner.md)
### [Szerepköralapú hozzáférés tervezése virtuális gépek replikációjához](site-recovery-role-based-linked-access-control.md)
## Üzembe helyezés
### [VMware virtuális gépek replikálása az Azure-ba](vmware-walkthrough-overview.md)
#### [1. lépés: Az architektúra áttekintése](vmware-walkthrough-architecture.md)
#### [2. lépés: Előfeltételek és korlátozások ellenőrzése](vmware-walkthrough-prerequisites.md)
#### [3. lépés: Kapacitás megtervezése](vmware-walkthrough-capacity.md)
#### [4. lépés: Hálózatkezelés megtervezése](vmware-walkthrough-network.md)
#### [5. lépés: Az Azure előkészítése](vmware-walkthrough-prepare-azure.md)
#### [6. lépés: A VMware előkészítése](vmware-walkthrough-prepare-vmware.md)
#### [7. lépés: Tároló létrehozása](vmware-walkthrough-create-vault.md)
#### [8. lépés: A forrás és a cél beállítása](vmware-walkthrough-source-target.md)
#### [9. lépés: Replikációs szabályzat beállítása](vmware-walkthrough-replication.md)
#### [10. lépés: A mobilitási szolgáltatás telepítése](vmware-walkthrough-install-mobility.md)
#### [11. lépés: A replikáció engedélyezése](vmware-walkthrough-enable-replication.md)
#### [12. lépés: Feladatátvételi teszt futtatása](vmware-walkthrough-test-failover.md)
## Konfigurálás
### A forráskörnyezet beállítása
#### [Forráskörnyezet: VMware-ről Azure-ra](site-recovery-set-up-vmware-to-azure.md)
#### [Forráskörnyezet: fizikairól az Azure-ra](site-recovery-set-up-physical-to-azure.md)
### A célkörnyezet beállítása
#### [Célkörnyezet: VMware-ről Azure-ra](site-recovery-prepare-target-vmware-to-azure.md)
#### [Célkörnyezet: fizikairól az Azure-ra](site-recovery-prepare-target-physical-to-azure.md)
### [Replikációs beállítások konfigurálása](site-recovery-setup-replication-settings-vmware.md)
### [Mobilitási szolgáltatás üzembe helyezése VMware-replikálásához](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Mobilitási szolgáltatás üzembe helyezése a System Center Configuration Managerrel](site-recovery-install-mobility-service-using-sccm.md)
#### [Mobilitási szolgáltatás üzembe helyezése az Azure Automation DSC-vel](site-recovery-automate-mobility-service-install.md)
### A replikáció engedélyezése
#### [Azure-ról Azure-ra történő replikáció engedélyezése](site-recovery-replicate-azure-to-azure.md)
#### [VMware-ről Azure-ra történő replikáció engedélyezése](site-recovery-replicate-vmware-to-azure.md)
## Feladatátadás és feladatátvétel
### [Helyreállítási tervek beállítása](site-recovery-create-recovery-plans.md)
#### [Azure Runbookok hozzáadása helyreállítási tervekhez](site-recovery-runbook-automation.md)
### Feladatátvételi teszt futtatása
#### [Azure-ba irányuló feladatátvételi teszt futtatása](site-recovery-test-failover-to-azure.md)
#### [VMM-felhők közötti feladatátvételi teszt futtatása](site-recovery-test-failover-vmm-to-vmm.md)
### [Védett gépek feladatátvétele](site-recovery-failover.md)
### Gépek ismételt védelme feladatátvétel után
#### [Ismételt védelem – Egy másodlagos Azure-régióról egy elsődlegesre](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [Ismételt védelem – Azure-ból helyszíni rendszerbe](site-recovery-how-to-reprotect.md)
### Feladat-visszavétel az Azure-ból
#### [Feladat-visszavétel az Azure-ból a VMware-be](site-recovery-failback-azure-to-vmware.md)
#### [Feladat-visszavétel az Azure-ból a Hyper-V-be](site-recovery-failback-from-azure-to-hyper-v.md)
## Migrate (Áttelepítés)
### [Áttérés az Azure-ra](site-recovery-migrate-to-azure.md)
### [Áttelepítés Azure-régiók között](site-recovery-migrate-azure-to-azure.md)
### [AWS Windows-példányok áttelepítése az Azure-ba](site-recovery-migrate-aws-to-azure.md)
### [Migrált gépek replikálása másik Azure-régióba](site-recovery-azure-to-azure-after-migration.md)
## Számítási feladatok
### [Active Directory és DNS](site-recovery-active-directory.md)
### [SQL Server replikálása](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [IIS-alapú webalkalmazások](site-recovery-iis.md)
### [Citrix XenApp és XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Egyéb számítási feladatok](site-recovery-workload.md#workload-summary)
## Replikálás automatizálása
### [Hyper-V-replikáció automatizálása az Azure-ba (VMM nélkül)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Hyper-V-replikáció automatizálása az Azure-ba (VMM-mel)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Hyper-V-replikáció automatizálása másodlagos helyre (VMM-mel)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Kezelés
### [Folyamatkiszolgálók kezelése az Azure-ban](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [A konfigurációs kiszolgáló kezelése](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Horizontálisan felskálázott folyamatkiszolgálók kezelése](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [vCenter-kiszolgálók kezelése](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Kiszolgálók eltávolítása és a védelem letiltása](site-recovery-manage-registration-and-protection.md)

## Figyelés és hibaelhárítás
### [Az Azure-ból Azure-ba végzett replikáció problémái](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [A helyszínről Azure-ba történő replikáció problémái](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [Naplók gyűjtése és helyszíni problémák hibaelhárítása](site-recovery-monitoring-and-troubleshooting.md)

# Referencia
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [Klasszikus PowerShell](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Kapcsolódó
## [Azure Automation](/azure/automation/)

# Erőforrások
## [Képzési terv](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=site-recovery)
