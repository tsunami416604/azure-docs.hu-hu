# Áttekintés
## [Mi a Site Recovery?](site-recovery-overview.md)
## [Hogyan működik a Site Recovery?](site-recovery-components.md)
## [Milyen számítási feladatokat tud megvédeni?](site-recovery-workload.md)
## [Site Recovery támogatási mátrix](site-recovery-support-matrix-to-azure.md)
## [Gyakori kérdések](site-recovery-faq.md)
## [Bevezetés megtekintése](https://www.youtube.com/watch?v=eOOwMQPBKfM)

# Első lépések
## [VMWare-alapú virtuális gépek replikálása az Azure-ba](site-recovery-vmware-to-azure.md)
## [VMWare-alapú virtuális gépek replikálása az Azure-ba több-bérlős központi telepítésben (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Hyper-V-alapú virtuális gépek replikálása az Azure-ba (VMM-mel)](site-recovery-vmm-to-azure.md)
## [Hyper-V virtuális gépek replikálása az Azure-ba](site-recovery-hyper-v-site-to-azure.md)
## [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](site-recovery-vmware-to-vmware.md)
## [Hyper-V-alapú virtuális gépek replikálása másodlagos helyre (VMM-mel)](site-recovery-vmm-to-vmm.md)

# Útmutató
## Felkészülés
### [Üzembe helyezési előfeltételek](site-recovery-prereq.md)
### [A hálózati infrastruktúrával kapcsolatos szempontok](site-recovery-network-design.md)
### [A Site Recovery Capacity Planner használata](site-recovery-capacity-planner.md)
### [Kapacitás megtervezése és VMware-replikálás méretezése az Azure-hoz](site-recovery-plan-capacity-vmware.md)
## Konfigurálás
### [A forrásreplikálási környezet beállítása](site-recovery-set-up-vmware-to-azure.md)
### [Replikációs beállítások konfigurálása](site-recovery-setup-replication-settings-vmware.md)
### [Mobilitási szolgáltatás üzembe helyezése VMware-replikálásához](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Mobilitási szolgáltatás üzembe helyezése a System Center Configuration Managerrel](site-recovery-install-mobility-service-using-sccm.md)
#### [Mobilitási szolgáltatás üzembe helyezése az Azure Automation DSC-vel](site-recovery-automate-mobility-service-install.md)
## Feladatátvétel és feladat-visszavétel
### [Feladatátvétel a Site Recoveryben](site-recovery-failover.md)
### [Helyreállítási tervek beállítása](site-recovery-create-recovery-plans.md)
#### [Azure Runbookok hozzáadása helyreállítási tervekhez](site-recovery-runbook-automation.md)
### [Azure-ba irányuló feladatátvételi teszt futtatása](site-recovery-test-failover-to-azure.md)
### [Két VMM-hely közötti feladatátvételi teszt futtatása](site-recovery-test-failover-vmm-to-vmm.md)
### [Feladat-visszavétel VMware virtuális gépek és fizikai kiszolgálók esetében](site-recovery-failback-azure-to-vmware.md)

## Migrate (Áttelepítés)
### [Áttérés az Azure-ra](site-recovery-migrate-to-azure.md)
### [Áttelepítés Azure-régiók között](site-recovery-migrate-azure-to-azure.md)
### [AWS Windows-példányok áttelepítése az Azure-ba](site-recovery-migrate-aws-to-azure.md)
## Számítási feladatok
### [Active Directory és DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Egyéb számítási feladatok](site-recovery-workload.md#workload-summary)
## Replikálás automatizálása
### [Hyper-V-replikáció automatizálása az Azure-ba (VMM nélkül)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Hyper-V-replikáció automatizálása az Azure-ba (VMM-mel)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Hyper-V-replikáció automatizálása másodlagos helyre (VMM-mel)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Kezelés
### [Kiszolgálók eltávolítása és a védelem letiltása](site-recovery-manage-registration-and-protection.md)
### [Replikációs beállítások szerkesztése](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy)
## [Figyelés és hibaelhárítás](site-recovery-monitoring-and-troubleshooting.md)

# Referencia
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [Klasszikus PowerShell](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Kapcsolódó
## [Azure Automation](/azure/automation/)

# Erőforrások
## [Képzési terv](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=site-recovery)


<!--HONumber=Feb17_HO4-->


