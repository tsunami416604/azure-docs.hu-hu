# Áttekintés
## [Mi a Site Recovery?](site-recovery-overview.md)
## [Hogyan működik a Site Recovery?](site-recovery-azure-to-azure-architecture.md)
## [Hogyan működik a Hyper-V-replikáció az Azure-ba?](site-recovery-hyper-v-azure-architecture.md)
## [Milyen számítási feladatokat tud megvédeni?](site-recovery-workload.md)
## [Site Recovery támogatási mátrix](site-recovery-support-matrix-azure-to-azure.md)
## [Gyakori kérdések](site-recovery-faq.md)
## [Bevezetés megtekintése](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Első lépések
## [Azure-alapú virtuális gépek replikálása (előnézet)](site-recovery-azure-to-azure.md)
## [VMware virtuális gépek replikálása az Azure-ba](site-recovery-vmware-to-azure.md)
## [Fizikai kiszolgálók replikálása az Azure-ba](site-recovery-physical-servers-to-azure.md)
## [Hyper-V-alapú virtuális gépek replikálása az Azure-ba (VMM-mel)](site-recovery-vmm-to-azure.md)
## [Hyper-V virtuális gépek replikálása az Azure-ba](site-recovery-hyper-v-site-to-azure.md)
## [Hyper-V-alapú virtuális gépek replikálása másodlagos helyre (VMM-mel)](site-recovery-vmm-to-vmm.md)
## [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](site-recovery-vmware-to-vmware.md)
## [VMWare-alapú virtuális gépek replikálása az Azure-ba több-bérlős központi telepítésben (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Útmutató
## Felkészülés
### [Az Azure-replikáció előfeltételei](site-recovery-azure-to-azure-prereq.md)
### [Kimenő hálózati kapcsolatok tervezése az Azure-alapú virtuális gépekhez (előnézet)](site-recovery-azure-to-azure-networking-guidance.md)
### [Hálózati infrastruktúra tervezése a helyszíni gépekhez](site-recovery-network-design.md)
### [Hálózatleképezés megtervezése](site-recovery-network-mapping-azure-to-azure.md)
### [Kapacitás megtervezése és VMware-replikálás méretezése az Azure-hoz](site-recovery-plan-capacity-vmware.md)
### [Deployment Planner a VMware Azure-ba történő replikálásához](site-recovery-deployment-planner.md)
### [Capacity Planner Hyper-V-replikáláshoz](site-recovery-capacity-planner.md)
### [VM-replikáció szabályozása szerepköralapú hozzáféréssel](site-recovery-role-based-linked-access-control.md)

## Konfigurálás
### [A forráskörnyezet beállítása](site-recovery-set-up-vmware-to-azure.md)
### [A célkörnyezet beállítása](site-recovery-prepare-target-vmware-to-azure.md)
### [Replikációs beállítások konfigurálása](site-recovery-setup-replication-settings-vmware.md)
### [Mobilitási szolgáltatás üzembe helyezése VMware-replikálásához](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Mobilitási szolgáltatás üzembe helyezése a System Center Configuration Managerrel](site-recovery-install-mobility-service-using-sccm.md)
#### [Mobilitási szolgáltatás üzembe helyezése az Azure Automation DSC-vel](site-recovery-automate-mobility-service-install.md)
### [A replikáció engedélyezése](site-recovery-replicate-azure-to-azure.md)
## Feladatátvétel és feladat-visszavétel
### [Helyreállítási tervek beállítása](site-recovery-create-recovery-plans.md)
#### [Azure Runbookok hozzáadása helyreállítási tervekhez](site-recovery-runbook-automation.md)
### [Feladatátvételi teszt futtatása](site-recovery-test-failover-to-azure.md)
### [Védett gépek feladatátvétele](site-recovery-failover.md)
### [Gépek ismételt védelme feladatátvétel után](site-recovery-how-to-reprotect-azure-to-azure.md)
### [Feladat-visszavétel az Azure-ból](site-recovery-failback-azure-to-vmware.md)

## Migrate (Áttelepítés)
### [Áttérés az Azure-ra](site-recovery-migrate-to-azure.md)
### [Áttelepítés Azure-régiók között](site-recovery-migrate-azure-to-azure.md)
### [AWS Windows-példányok áttelepítése az Azure-ba](site-recovery-migrate-aws-to-azure.md)
### [Migrált gépek replikálása másik Azure-régióba](site-recovery-azure-to-azure-after-migration.md)

## Számítási feladatok
### [Active Directory és DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
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
### [Replikációs beállítások szerkesztése](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Folyamatkiszolgálók kezelése az Azure-ban](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [A konfigurációs kiszolgáló kezelése](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Horizontálisan felskálázott folyamatkiszolgálók kezelése](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [vCenter-kiszolgálók kezelése](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Kiszolgálók eltávolítása és a védelem letiltása](site-recovery-manage-registration-and-protection.md)
## Figyelés és hibaelhárítás
### [Azure-alapú virtuális gépek replikációs problémái](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [A helyszínről Azure-ba történő replikáció problémái](site-recovery-monitoring-and-troubleshooting.md)

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
